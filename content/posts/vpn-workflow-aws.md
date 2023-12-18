---
title: "How to actually setup VPN in AWS?"
date: 2023-12-12T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "Setting VPN in AWS is not straightforward!"
tags: ["AWS", "VPN"]
categories: ["VPN"]
---


## What is a VPN in the first place?


VPN is what it says: Virtual Private Network.
**but what does that exactly mean?**


Suppose you are using an internet connection available at your university and connecting to the internet.
In this case, the university has a private network setup that allows you to access the internet, but here,
You can definitely get traced because, well, ultimately, the traffic will be coming and going to your
university's main router, and of course, the ISP has records for that.
and your university might as well keep records of what you are visiting through a firewall, which is a common scenario.


But with the VPN, you can get into a private network, but in this case, it is virtual in some
other geographical region, which keeps your identity private (mostly!)
Here, it is very hard to trace your identity and what you have visited when you are visiting a VPN.


If you are accessing a VPN at your university, the university will find a normal valid IP address that you are visiting, but mostly it doesn't know it is a VPN and has no control over what you are accessing or how you are accessing it.


It is like you are virtually in that private network, i.e., your PC or laptop is in the private network, which is on some server that you don't handle.


Fine, now that you understand how VPN works, let's understand how AWS allows you to create a VPN and let you connect to it.


---


When creating a VPN in AWS, you can do the following:
- access private resources not routable on the internet. e.g., not having public IP.
- access internet securely to access some content that is restricted to your geographical location.
- have controlled connectivity for VPN clients to be able to access the internet.
- Only allow connectivity from your VPN for a particular resource and not from the internet, though the resources are publicly available. e.g., having administrative privileges work only when admins are connected via VPN and not exposing the admin API over the internet.
- etc.


There are more use cases, of course, than this. But you get the point.


---


### Well, let's dive deep into how everything fits together.



When you need to create a VPN, what you are basically doing is creating a VPN service, which will
let users connect to it.
you can,
- restrict which users can access the VPN using mutual authentication.
- restrict users over subnets and internet connectivity.


VPN is, of course, over HTTPS, so who is going to provide the secure connection?


VPN is a kind of lightweight service that listens to client connection requests and, based on the required authentication, lets users connect to it.


But to provide a secure connection, you need an SSL certificate, right?


You can request a certificate from the AWS certificate manager, but wait, should you do it?



Requesting a certificate from the AWS Cert Manager will authorize a certificate for a domain.
Supposing you have bought the ```example.com``` domain, setup static hosting on the S3 bucket, and want to secure your client connections, you request a certificate from Cert Manager.
where you **prove** your domain ownership.
So then you can use that certificate in CloudFront to secure your domain.


But here is the catch: your clients can get the secure connection when they connect to your domain, which you have validated when requesting the certificate, right?



But when you create a VPN client in AWS that has a different endpoint, something like,


```text
cvpn-endpoint-06cfc1ca6ccea985c.prod.clientvpn.us-east-1.amazonaws.com
```


This is the endpoint that I got when I created a client VPN.


So, how do you handle this domain?
Is there any point in requesting a certificate from AWS for this domain?
Of course not!


What you simply need to do is create a custom certificate and sign it with a certificate authority of your own!


### Create client and server certificates on your Linux machine.


To create client and server certificates, you must have a certificate authority that signs those certificates, right?



> Here understand that we need to have **mutual authentication**, meaning we also need to authorize the clients that are connecting to the VPN and allow only them to connect.


> steps are taken from the [AWS VPN Guide](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/mutual.html)


1. Clone the OpenVPN easy-rsa repo to your local computer and navigate to the easy-rsa/easyrsa3 folder.


```shell
git clone https://github.com/OpenVPN/easy-rsa.git
```
> this provides an easy workflow for creating and managing certificates.


2. Initialize a new PKI environment.


```shell
./easyrsa init-pki
```


3. To build a new **certificate authority (CA)**, run this command and follow the prompts.


```shell
./easyrsa build-ca nopass
```
> no pass means you don't require a passphrase to use that ca-certificate.
Basically, you are creating 'ca-cert', which will act as the root certificate and 'private key' for that certificate.



4. Generate the server certificate and key.


```shell
./easyrsa build-server-full server nopass
```
>This creates a server certificate and its private key. Here, a certificate gets created, which means it is also signed by the certificate authority by using its private key.



Now you can optionally generate client certificates.
This means that you can generate a separate certificate for each client or use the same for everyone.
However, it is recommended that you use a separate certificate for each client, so in the case of that employee
leaves your organization, you can simply revoke that certificate, rather than revoking the common one and giving everyone a new cert.


But for our usecase one cert is enough. so let's go ahead and create it.


5. Generate the client certificate and key.


```
./easyrsa build-client-full client1.domain.tld nopass
```


6. Copy the server certificate and key and the client certificate and key to a custom folder, and then navigate into the custom folder.


```shell
mkdir ~/custom_folder/
cp pki/ca.crt ~/custom_folder/
cp pki/issued/server.crt ~/custom_folder/
cp pki/private/server.key ~/custom_folder/
cp pki/issued/client1.domain.tld.crt ~/custom_folder
cp pki/private/client1.domain.tld.key ~/custom_folder/
cd ~/custom_folder/
```


This is to simplify the workflow for uploading these certs to AWS.


---


### Uploading certificates to the AWS Certificate Manager


Here, you can either use ```aws-cli``` or aws-console to upload the certificates.


But we will follow a console-based method for simplicity.


Head towards **AWS Certificate Manager** and click **import Certificate**.


{{< figure src="/images/VPN-AWS/certimport.png#center" link="/images/VPN-AWS/certimport.png" target="_blank" class="align-center" >}}


Here, give the details, like **server certificate**, **Private Key** and optionally the certificate chain.
But here, you must provide the certificate of CA.


If you don't provide a CA certificate, even in normal cases of simply attaching to a domain, your client browser will request a certificate, and when it gets this, it will try to authorize it with the built-in CA certificates that it trusts. If it doesn't find any CA certificates that can authorize this server certificate,
It fails to authorize it and doesn't set up TLS.


But when you also provide the CA certificate along with the server certificate, the client can authorize it and establish the secure connection, but of course, it will try to back out as the certificate is custom and not globally accredited.


But in our case for VPN connections, this is fine and the recommended way of managing certificates.


So when the client connects, it will be able to authorize the server using the CA cert and successfully establish a connection to the server via **TLS**.


Now, when you provide the CA cert in the certificate chain, of course you have only two hierarchies in the certificate chain: one is the server cert, and the other is the root cert, or CA cert, which actually signs the server cert.


So you simply need to give the CA certificate in the certificate chain.


Now your certificate is imported! Congrats!


---


Now you can optionally import the client certificate here.
But in our case, it is **not required**.
Why?


Because when our VPN service requests that the client bring its own certificate and the client sends it,
The VPN service authorizes it on the basis of a certificate chain, right?
But if the client doesn't send the CA cert, then how does this service authorize it?
Well, it falls back on using the certificate chain provided by us for the server.


And as we have created the client certificate and signed it using the same **CA authority**
The service will successfully be able to authorize the client certificate.


But if you have used different **CA**, you need to import exactly the same you imported the server cert.


This also proves security. why?


Because **no one** can use any certificate other than signed by your **CA authority**, which is residing in your linux machine!


So even if a person has globally accredited certificate for his server, his of-course won't be able to access our VPN service as the server would fail to authorize it as its certificate is not signed by our **CA Authority**


---


### Creating a Client VPN Endpoint


This step actually creates a VPN where users can connect.


Here, go to the **Client VPN Endpoints** section in the VPC.


{{< figure src="/images/VPN-AWS/vpnendpoint.png#center" link="/images/VPN-AWS/vpnendpoint.png" target="_blank" class="align-center" >}}


Create an Endpoint.


Here, give,
- name for the endpoint (optional)
- Give the CIDR block from which your clients will get private IP addresses.
Don't give a CIDR block, which can conflict with the subnet that you want to work with.
- Give server certificate that will be used for a secure connection.
- Check the mutual authentication box to authorize clients as well.
Here for ```Client Certificate ARN```, choose the same server certificate.
This makes sure that the VPN service will authorize the clients based on the CA cert of the server itself. i.e it will use the certificate chain of server certificate to authorize the client certificate. If you have used different ```CA``` for client certificate, make sure to import that first and choose it.
- You can optionally set the DNS server to use here. remember there is no default DNS server configured for your VPN, so if you plan to access internet from this, make sure to add DNS server here.


Leave everything blank, as we don't require other things for this demonstration.


Now you have set up the client VPN.
Its now time to give clients access to a particular **subnet** or **subnets**.


To configure this, simply go to the **target network associations**


and then choose **Associate target network**


{{< figure src="/images/VPN-AWS/network-association.png#center" link="/images/VPN-AWS/network-association.png" target="_blank" class="align-center" >}}


Here, choose the subnet that you want to access from your VPN.


There is a **route table** associated with this VPN, and when you associate the subnet,
It automatically creates an entry in the route table.


{{< figure src="/images/VPN-AWS/vpn-route-table.png#center" link="/images/VPN-AWS/vpn-route-table.png" target="_blank" class="align-center" >}}


Here, you can also add custom route entries, like for accessing the internet.



But remember, you can only access the internet with the help of a subnet.


So if you have configured the Internet Gateway for that VPC, you can go on the internet from the VPN.
when you add ```0.0.0.0/0``` route in the route table and select the ```target network``` as the subnet that has internet access.


Here, remember that, **by-default**, VPN configures NAT by which you can reach the internet, so even if the subnet doesn't have access to the internet, if an internet gateway is there, you can access the internet from the VPN without any extra configuration. That's great!


But small things are remaining that you need to do in order to fully set up your VPN.


First, head towards **Authorization Rules** and create the rule where all or some of your clients are able to access what they want.


If you want to allow all the clients to go wherever they need to go according to the route table, simply
Allow them for the ```0.0.0.0/0``` network.


These rules allow you to give fine-grained access to your VPN clients, so even if the route table of the VPN has multiple routes, it is your responsibility to give each client specific access or give all of them full access to all the networks (subnets) that you have associated.


---


### Testing


Now that everything is setup, try creating an **EC2 instance** in the same region as your VPN and making it private purposefully.
Also, make sure to create it in the subnet that you have associated with the VPN.


Now you can't access this instance from the internet, right?
And before this, there was no way with which you could gain access to this instance, right?


Meaning if you headed towards Connect and tried connecting, AWS would error out that the instance is private.


In order to connect to it, there was only one way there: **bastion host**, which is again not that secure considering you still require a public endpoint from which you will access private resources.


But now, with the **Client VPN**, you can successfully access the instance as the subnet in which it lies is associated with the VPN.


So download the client configuration from the top bar.


You get an ```.ovpn``` file.


Before you start using it, a few changes are required.


The changes are because, if you didn't setup **mutual authentication** for VPN, you can use this file directly to connect to the VPN.


But as you have **mutual authentication** enabled, you need to provide the **certificate path** and **private key** path in this ```.ovpn``` file.


So before changes, the file looks like this:


```text
client
dev tun
proto udp
remote cvpn-endpoint-00e16cdbf058cc151.prod.clientvpn.us-east-1.amazonaws.com 443
remote-random-hostname
resolv-retry infinite
nobind
remote-cert-tls server
cipher AES-256-GCM
verb 3
<ca>
-----BEGIN CERTIFICATE-----
<the certificate here>
-----END CERTIFICATE-----


</ca>



reneg-sec 0


verify-x509-name server name
```
Here, you don't see any configuration for the client certificate or its private key.


Now, as I previously told you, store all the files in ```custom_folder```, including the client certificate and its private key.
Grab their fully qualified path and paste it like this, so the end result is like,


```text
client
dev tun
proto udp
remote cvpn-endpoint-00e16cdbf058cc151.prod.clientvpn.us-east-1.amazonaws.com 443
remote-random-hostname
resolv-retry infinite
nobind
remote-cert-tls server
cipher AES-256-GCM
--cert "/home/varad/custom_folder/client.domain.tld.crt"
--key "/home/varad/custom_folder/client.domain.tld.key"
verb 3
<ca>
-----BEGIN CERTIFICATE-----
<the certificate here>
-----END CERTIFICATE-----


</ca>



reneg-sec 0


verify-x509-name server name


```
See those ```--cert``` and ```--key```, and paste your paths in front of them.


Now when that is done, it's done!


You **cannot** use the aws-cli for connecting to this VPN.


So now head over to the [aws vpn client download]()
to download the appropriate client for your system with which you can connect to the VPN you just set up.


I prefer the **windows/macos client** as for Linux, you require outdated Linux machines (18.04 & 20.04) and I bet most of you people won't even have it.
And don't even try to download this client in a suitable Docker container or VM, as this might cause unnecessary troubles. (I tried a lot.)


So if you have a Windows client, make sure to change the path in the certificate accordingly and have double backslashes instead of singles in the path.


Now, after all this, you can now successfully connect to the VPN!






---
title: "The DNS deep dive"
date: 2023-10-16T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "Learn in depth how DNS System works."
tags: ["DNS", "deep-dives"]
categories: ["deep-dives","DNS"]
---

## Topics Covered
You will find lots of blogs talking about DNS.
But most of them just explain the DNS resolution concept, and not how everything is setup.
Like how domain is registered in the first place that it becomes available to customers to resolve.
 
So this blog post will cover everything that I came accross to with my learning, and I hope you like it.

Following are the topics being covered in this 
- How DNS Resolution works?
- How Domain Registration works?

--- 

## How DNS Resolution works?

{{< figure src="/images/dns-deep-dive/dns-resolution.png#center" title="DNS Resolution" link="/images/dns-deep-dive/dns-resolution.png" target="_blank" class="align-center" >}}
(Image credit: AWS)

Domain Name System (DNS) resolution is a critical process that translates human-readable domain names into IP addresses, allowing computers to locate and connect to each other over the Internet. The DNS resolution process involves several steps, and I'll explain them in depth:

#### User Input or Application Request:

When a user types a URL into a web browser or any application initiates a network connection using a domain name, the DNS resolution process begins.

#### Local DNS Cache Lookup:

The operating system checks its local DNS cache to see if it already has the IP address corresponding to the requested domain name. If the information is found and hasn't expired, the system can skip the rest of the DNS resolution process.

#### Hosts File Check:

If the local DNS cache doesn't have the required information, the operating system checks the local "hosts" file. This file contains manual mappings of IP addresses to domain names. If there is a match, the resolution process stops here.

#### Recursive DNS Query (if necessary):

If the local cache and hosts file don't provide the required information, the operating system forwards the DNS query to a recursive DNS resolver. This resolver may be provided by your Internet Service Provider (ISP) or a third-party DNS service like Google DNS or OpenDNS.

#### Root DNS Server:

If the recursive resolver doesn't have the necessary information, it starts the resolution process from the root DNS servers. There are 13 sets of root DNS servers worldwide, identified by letters (A to M).

#### Top-Level Domain (TLD) DNS Server:

The root DNS server responds with a referral to the authoritative DNS server for the top-level domain (TLD) of the requested domain. TLDs include familiar ones like .com, .org, .net, and country code TLDs like .us, .uk, etc.
 
#### Authoritative DNS Server:

The recursive resolver queries the authoritative DNS server for the specific domain. The authoritative DNS server is responsible for holding information about a particular domain, including the IP address associated with it.

#### Resource Record (RR) Lookup:

The authoritative DNS server responds to the recursive resolver with the requested resource record (RR), containing the IP address associated with the domain name.

#### Caching and Response:

The recursive resolver caches the obtained information for a specified time, known as the Time-to-Live (TTL). Subsequent requests for the same domain can be answered from the cache until the TTL expires.

#### Return to the User/Application:

The resolved IP address is sent back to the operating system, and the application can use this information to establish a connection with the desired server.

---

## How Domain Registration works?

This section is the most important part where you will learn how a domain actually gets registered and all the servers involved in the process update the domain information.

Lets start from very first...

Consider you want domain ```example.com```.

So first you need to understand that what you want to do with that domain.
So lets consider you want to host a simple website on it, a **static website**.

When you explore on the google, you will find bunch of domain registrars, but most common thing you will notice is that they also provide you sample hosting with the domain you buy.
For example, lets consider you found **hostinger** as your domain registrar, again, this can be godaddy or any other company, but you got the point.


When you go to hostinger website, you search in the box for your required domains, and if that domain is available, you get to see plans according to either you want `.com`, `.in` `.io` or something else.

Suppose you select as usual `.com` domain, so you select the `example.com` domain and lets say you are paying $10 for it.

When you setup your hostinger account and pay for the domain. you now own the domain.

When you verify your email-id, you are free to **configure** your domain.

Wait...?
Configuration?
What does that even mean?

When you are on hostinger dashboard, it will look something like this,

{{< figure src="/images/dns-deep-dive/hostinger-dashboard.png#center" title="Hostinger Dashboard" link="/images/dns-deep-dive/hostinger-dashboard.png" target="_blank" class="align-center" >}}

When you buy the domain, after some time, when you search for it on the browser, you will be landed on your website, `example.com`.
Which is set up by default by hostinger.

So when you buy the domain, `example.com` it actually creates a zone file in the **dns servers** managed by hostinger.

OK!
Now what is a zone file?
a zone file is the complete information about a domain and its subdomains.


meaning when I say for `example.com`, a zone file is created, it means the example.com itself, and other subdomains under it will be listed in this zone file itself.

Here, we can explicitly have different zone file for suppose `shopping.example.com`, and under this, we can have multiple subdomains under this, like, `payment.shopping.example.com` etc.

Inside the zone file, there are what called as **records**.

This records actually resolve the DNS query to either **IP address** called as **A-Record** or to another domain name which is called as a **CNAME Record**. (CNAME :- Canonical Name).

Here there is another important Record named as **SOA Record** which actually contains the primary nameserver for your domain.
(SOA :- State Of Authority).

For example, consider following example zone file:-

{{< figure src="/images/dns-deep-dive/aws-hosted-zone-details.png#center" title="Route53 Hosted Zone" link="/images/dns-deep-dive/aws-hosted-zone-details.png" target="_blank" class="align-center" >}}
(These records are taken from my AWS route53.
This is a zone file for my `belwalkarvarad.tech` domain.
)


Here, as you can see, there are multiple records as can be seen, and I can have as many records as possible for my sub-domains.

like in this zone file for `belwalkarvarad.tech`, I can have `info.belwalkarvarad.tech`, `cloud.belwalkarvarad.tech`, etc.

Then setting up appropriate values for them and you are ready to go.

---

So now for simplicity suppose we have bought domain `example.com` so a **zone file** for `example.com` will get created on the **DNS Servers** that hostinger manages.

Now most important thing, these **DNS Servers** are nothing but **Authoratitive DNS Servers**
which hold the zone file for your domain.

Here is the catch.

For your domain to get resolved, it will first go to the root domain, from which it will of course go to the `.com` domain server.

But from there, where will it go?

You will say to hostinger dns server, but, why?

How that `.com` domain server come to know that hostinger authoratitive server is the server who holds information about that domain i.e in our case `example.com`?


### Zone transfer and updation at TLD Servers.

Here, the only valid zone files are updated to the **Secondary DNS Servers**.
So from now that, **Seondary DNS Servers** can then resolve the DNS queries coming to them.

Now, the most important step is to configure the **TLD Servers** for pointing to the right **Authoritative DNS Servers**.

Here the most important thing you need to understand is,

Configuring `Zone file` is different from configuring the `TLD Server`.

Meaning when your provider say hostinger creates **zone file** in its primary DNS server, it runs separate authorized process to update the information on the **TLD Servers**.

So the only work of Authoritative DNS Servers is to serve the requests coming in, and if it is secondary DNS server, then to do zone transfer with the primary DNS servers and also serve requests.

The process of sending the **NS record** to the **TLD server** is managed by the hostinger through entirely separate workflow.

How does **NS Record** look like?
Following is the **NS Record** from my AWS Route53 zone file:

{{< figure src="/images/dns-deep-dive/aws-ns-record-details.png#center" title="NS Record" link="/images/dns-deep-dive/aws-ns-record-details.png" target="_blank" class="align-center" >}}


So when you log in into your hostinger dashboard, you have authority to change the nameservers of your domain, so when you change those, an updated NS record is then sent to the **TLD Server** for updation, and from there, the **TLD Server** now points to those updated nameservers.

{{< figure src="/images/dns-deep-dive/hostinger-change-ns.png#center" title="Changing NameServers for your Domain" link="/images/dns-deep-dive/hostinger-change-ns.png" target="_blank" class="align-center" >}}


And this takes some time for propagating into network of these **TLD Servers**.

So when request comes to the **TLD Server**, it looks into the NS record for your domain, and sends those Nameservers list to the query maker, from there, the qeury maker, in our case, the browser randomly choose any one of the nameserver and makes a query again to that server.

When that query comes to that Authoritative DNS Server, it then looks for the Zone file, and from there, it resolves the Request.


If you have worked with AWS Route53 you probably have given nameservers of AWS Route53 there.

When you create hosted zones in the **Route53** you are actually creating zone files for you domain.

{{< figure src="/images/dns-deep-dive/aws-hosted-zone.png#center" title="Hosted Zone" link="/images/dns-deep-dive/aws-hosted-zone.png" target="_blank" class="align-center" >}}

{{< figure src="/images/dns-deep-dive/aws-hosted-zone-details.png#center" title="Zone file Details" link="/images/dns-deep-dive/aws-hosted-zone-details.png" target="_blank" class="align-center" >}}


and that zone file gets reflected in all **secondary servers of AWS**.

So, in that zone file of Route53, one record i.e **NS Record** is there which you need to take and copy those nameservers, and then go to hostinger's dashboard, and replace those original namservers with these ones.

With this, hostinger uses TLD Server API and  updates the reocord for your domain with the given nameservers,
remember, previously, **TLD Server** had NS Record for your domain, pointing to hostinger nameservers, and Now, with this updation, it now points to the nameservers of Route53.

So by the time now, you know that the TLD Servers only contain NS record for that domain, i.e which authoritative DNS servers are there for the domain.

And to update this record, you need to log in into your registrar account, and make the changes, as this is highly authenticated process.

In the Route53 console, you can only change the records in the hosted zone, you obviously cannot control the pointing of TLDs to the nameservers that are listed in the NS Record from there.






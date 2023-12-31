---
title: "An overview of AWS Route53"
date: 2023-11-22T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "Route53 overview"
tags: ["AWS", "Networking"]
categories: ["AWS","Networking"]
---

First and Foremost, if you didn't read my [DNS deep dive](https://belwalkarvarad.tech/posts/dns-deep-dive/),
I highly recommend you read that before coming here.I am going to refer lots of things from there, but if you know how everything in DNS works right from Registration to updation of NS Records to TLD servers, then you are good to go :).

**AWS Route53** is a service provided by AWS to manage zone files for our registered domains.

When you go on creating a hosted zone, you are presented with this type of dashboard:


{{< figure src="/images/aws-route53-overview/create-zone-dashboard.png#center" title="Create a Zone" link="/images/aws-route53-overview/create-zone-dashboard.png" target="_blank" class="align-center" >}}

Here, domain name is what your domain name is.

The **Type** option is whether you want the hosted zone to be publicly available i.e to serve as public Authoritative DNS server for your domain OR,
To use it as a **Private** zone for your internal AWS infrastructure that you manage.

And that's it!


Here is what the dashboard of Route53 looks like when you create a hosted zone:

{{< figure src="/images/aws-route53-overview/route53-dashboard.png#center" title="Route-53 Dashboard" link="/images/aws-route53-overview/route53-dashboard.png" target="_blank" class="align-center" >}}

Here, as you can see there, I have three hosted zones,
- google.com
- limotimilimo.com
- belwalkarvarad.tech

Wait!

`google.com` ?

yup, I own the `google.com`!

Just kidding, the thing is you can create zone file for any domain!

But how? and why?

We will cover that later.

AWS provides what is called delegation sets for each **hosted zone** that we create in Route53.

A **delegation Set** is the set of authoritative DNS servers that keep your zone file i.e this file itself.

When you go into that domain, you see some default records listed, those are **NS Record** and **SOA Record**.

Again, if you don't know records or don't know what these records mean, [visit](https://belwalkarvarad.tech/posts/dns-deep-dive/) the post and then resume here.

Then you can create the records that you require.

Here is my example zone details:

{{< figure src="/images/aws-route53-overview/hosted-zone-details.png#center" title="Zone Details" link="/images/aws-route53-overview/hosted-zone-details.png" target="_blank" class="align-center" >}}

Here, you can see in the NS Record, there are the nameservers listed for my domain.

You can use these NameServers and use them to point to your zone file, by configuring your nameservers settings for your domain in the registrar dashboard.

___

Now some most important questions:

- Why am I able to create the `google.com` zone?
- Why AWS lets me create this zone?

The answer lies in the AWS DNS Architecture it manages. 

The nameservers you are seeing in the NS record are **Exclusive** to you.

Yes.

Most small scale providers host your **zone** in a **single pool**.
Meaning,
your and other customers' zone files will reside on the **same nameservers**.
That time, if there is an already a zone for suppose `example.com` you can't create your own one there.

But in case of **AWS Route53**, not even per account, but you get exclusive nameservers per hosted zone you create!

That means lets say you got `ns-3420.awsdns-20.org` as a nameserver for your domain,

This nameserver is exclusive for your domain, that means this server will only contain zone file of your domain!

Of course these nameservers are **virtual**, and allows you to have fine-grained access to your domains.

And that is the reason why you can create `google.com` zone in your account, because,
it doesn't really matter!

Because the nameservers your `google.com` getting are exclusive to it.

The thing is how you will route the google.com resolution requests to your zone file?

Of course you need to update TLDs for that.
Are you able to do that with just creating this zone in your Route53 dashboard?
Of course not!

Lets say your friend owns a domain `friend.com` that is registered by godaddy.

He does following:
- Creates zone for `friend.com` in Route53
- Updates nameservers din godaddy dashboard to point to Route53 nameservers that he got.

Then you create the same `friend.com` zone in your Route53 dashboard.
You get Different nameservers as usual.
You want to update the TLDs.
How?
You need to change nameserver settings for that domain, and for changing that, you need to login to your friend's godaddy account, change settings there,

and from there, the godaddy will then authorize itself that it can change the nameserver settings for this domain, and then it communicates with the TLD server to update the nameserver information.

But you don't have acccess to the account!





---
title: "An overview of S3 bucket in AWS"
date: 2023-12-02T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "An overview of S3 bucket in AWS"
tags: ["s3", "cloud-storage", "AWS"]
categories: ["S3","AWS"]
---


S3 buckets are somewhat like google drive storages.

Here, S3 is an object storage, meaning simply you store files and folders under a bucket, and that is all, hence it is also called as flat storage, as it doesn't involve any file system.

Here is an overview of S3 storage given by network kings:


{{< figure src="/images/s3-bucket-overview/nkingsflow.png#center" link="/images/virtual_interfaces/first.png" target="_blank" class="align-center" >}}


So when you are going to create an S3 bucket, 

the S3 bucket name you create need to be globally unique, yes you heard it right, else you cannot create it. Also it needs to be in lowercase.

Next, you choose the region in which to create the bucket, an defaultely, the bucket as shown in above diagram, get backed up as per AWS rules, into 3 or more availability zones.
You don't manage this replication, as amazon does this internally.

Next, By default `Block all Public Access` is selected, meaning that no one can access the objects stored inside this bucket unless you configure manually.

Here, there are two things as per the S3 bucket object, the bucket URI and URL, URI simply identifies it, while teh URL actually locates it so that you can view or download it.

But, as mentioned above, if the bucket ACL is to block all public access, no one can view it using hte URL right, then how does it look like when we try to view it?

First this is a typical object overview:

{{< figure src="/images/s3-bucket-overview/s3overview.png#center" link="/images/s3-bucket-overview/s3overview.png" target="_blank" class="align-center" >}}

And this is shown when we try to access this particular object, i.e access denied.

{{< figure src="/images/s3-bucket-overview/objectaccess.png#center" link="/images/s3-bucket-overview/objectaccess.png" target="_blank" class="align-center" >}}


Again before deleting the bucket, you need to empty it first, and then delete it, delete the versions as well if you have enabled versioning















---
title: "Understand LXC commands "
date: 2023-05-05T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "LXC Commands list with explaination"
tags: ["Linux", "LXD"]
categories: ["LXC","LXD"]
---

First in case you messed up with the lxd by creating cluster,and now you are unable to access the lxd daemon,  
because your IP has changed,  
If you don't have any critical information stored in any of the lxd instances, you can simply remove all the files, under,    
```shell
/var/snap/lxd/common/lxd/database/
```
by running,  
```shell
sudo rm -rf /var/snap/lxd/common/lxd/database/*
```
And thereafter running `lxd init` again to configure new workflow.

---

## Commands

List remote repositories that make images available:  
```shell
lxc remote list
```
And to see which images are available on a particular remote repository:
```shell
lxc image list <remote_name>:
e.g
lxc image list images:
```

Copy the image from the remote repository to local  
The only way to pull images to local repo is to copy from remote end.
```shell
lxc image copy images:ubuntu/22.04/amd64 local: --alias ubuntu
```
Launch a container from image, pull image if not exists:  
```shell
lxc launch ubuntu container-name
```

Creating a New Container:
```shell
lxc init ubuntu my-container
```

Starting a Container:
```shell
lxc start my-container
```

Stopping a Container:
```shell
lxc stop my-container
```

Delete a container:  
```shell
lxc delete container-name
```

List storage pools(You can't run anything if there is no storage pool, as it doesn't create automatically like docker does)
```shell
lxc storage list
```


Create a storage pool to be used by containers/vms:  
Here, the preferred storage driver in my case is dir, others are btrfs etc.
```shell
lxc storage create storage-name dir source=/var/snap/lxd/common/lxc/storage-pools/storage-name
```

To launch or init a container in a particular storage pool:  
```shell
lxc launch ubuntu container-name -s mystoragepool

lxc init ubuntu container-name -s mystoragepool
```

Make sure that you have freshly created pool, otherwise this won't work, or the pool which is empty.
Before you make any storage pool default for a profile(default is default), the profile looks like: 
```yml
config: {}
description: Default LXD profile
devices: {}
name: default
used_by: []
```
and after change it will look like this: 
```yml
config: {}
description: Default LXD profile
devices:
  root:
    path: /
    pool: local
    type: disk
name: default
used_by: []

```
 
So to configure the default storage for this profile, i.e to change the pool, you can either do it manually, by looking at the end result as above: 
```shell
lxc profile edit default
```
and then change the pool name to the name that you want as default pool, 
while the path mentioned there is nothing but, relative path against the pool configured,
so if storage pool you are using here is `/var/snap/lxd/common/lxd/storage-pools/local`
then the path is considered as root i.e /, meaning that if you configure path to be /containers, 
then the containers that you create using this profile, in term, using this default storage pool,
the actual container directory will be created at `/var/snap/lxd/common/lxd/storage-pools/local/containers/<container-name>` location.


As you can see above, there is no entry in the device section, so first we need to add a device called root so that it will used by default, and set it params,
this is recommended way rather than editing the file manually,

```shell
lxc profile device add default root disk pool=local path=/
```
Here we are addding the device called root to the default profile and setting the `pool` to `local`, and `path` to `/` here.
Here the type of device(root) is disk.


But if you have already the root device over there, simply edit its configuration if not suitable.  
The recommended way to change this config is,

```shell
lxc profile device set default root pool=<pool_name>
```
Here, we are editing profile, so the `lxc profile` command,  
next, we want to edit the devices section, so the  `device set`,  
next, the profile `default` one,  
pool 
next, `root` means the device we want to configure
and pool=<pool_name> means we are changing/adding that property.


Accessing a Container's Console:
```shell
lxc console my-container
```
This is similar to getting a shell in the container with the `exec` command, but requires password for the 
user already set up.


Executing a Command Inside a Container:
```shell
lxc exec my-container ls -l
```


Adding a repository to the remote list: 
```shell
lxc remote add --protocol simplestreams ubuntu https://cloud-images.ubuntu.com/releases/
```

configure a vm/container: 
```shell
lxc config edit vm/container
```

Creating Snapshots:
```shell
lxc snapshot my-container snapshot-1
```

Restoring from a Snapshot:
```shell
lxc restore my-container snapshot-1
```
Copying and Moving Containers:
```shell
lxc copy my-container my-container-copy
lxc move my-container my-container-renamed
```


Listing Containers:
```shell
lxc list
```

Displaying Container Information:
```shell
lxc info my-container
```


Configuring Container Properties:
```shell
lxc config set my-container limits.cpu 2
```


Creating a Bridge Network:
```shell
lxc network create my-network
```

Adding a Container to a Network:
```shell
lxc network attach my-network my-container
```

Listing LXD Networks:
```shell
lxc network list
```

Deleting a Network:
```shell
lxc network delete my-network
```


Cloning a Container:
```shell
lxc copy my-container my-container-clone
```

Exporting a Container:
```shell
lxc publish my-container --alias my-container-image
```

Importing an Exported Container:
```shell
lxc image import my-container-image.tar.gz --alias my-container-image
```


Uploading Files to a Container:
```shell
lxc file push /path/to/local/file my-container/root/path/in/container/
```

Downloading Files from a Container:
```shell
lxc file pull my-container/root/path/in/container/file /path/to/local/destination/
```

Simply add a recursive flag `-r` in case it is a directory.


Listing Snapshots:
```shell
lxc info my-container
```

Deleting Snapshots:
```shell
lxc delete my-container/snapshot-name
```



Renaming a Container:
```shell
lxc rename my-container new-container-name
```


Connecting to a Remote LXD Host:
```shell
lxc remote add remote-name remote-host
```

Managing Containers on a Remote Host:
```shell
lxc remote list
lxc remote show remote-name
lxc remote remove remote-name
```

Monitoring Container Resource Usage:
```shell
lxc monitor --type=container my-container
```

Creating a Container from an Image:
```shell
lxc init my-custom-image my-new-container
```

Exporting a Custom Image:
```shell
lxc image export my-custom-image my-custom-image.tar.gz
```

Moving Containers to Different Storage Pools:
```shell
lxc move my-container my-new-container --target new-storage-pool
```

Container Configuration Overrides:
```shell
lxc config set my-container limits.memory 2GB
lxc config unset my-container limits.memory
```

Creating a Custom Bridge Network:
```shell
lxc network create my-custom-bridge ipv4.address=192.168.1.1/24 ipv4.nat=true
```

Assign a specific IP address to a container.
```shell
lxc config device add my-container eth0 nic nictype=bridged parent=my-custom-bridge name=eth0 ipv4.address=192.168.1.2
```


Running a Container in Privileged Mode:

```shell
lxc config set my-container security.privileged true
lxc start my-container
```

Creating a Container Backup:
```shellshell
lxc export my-container /path/to/backup-directory
```

Restoring a Container from Backup:
```shell
lxc image import /path/to/backup-file --alias my-restored-container
lxc init my-restored-container my-restored-container-name
```

Listing Available Images on a Remote Server:
```shell
lxc remote list my-remote-server
lxc image list my-remote-server:
```


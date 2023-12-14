---
title: "netsh in windows"
date: 2023-04-26T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "learn netsh"
tags: ["windows", "Networking"]
categories: ["windows","Networking"]
---

## netsh is primarily used for configuring network and network interfaces

Show available interface

```shell
netsh interface show interface
```

Disable Network interfaces

```shell
netsh interface set interface "Ethernet" admin=disable
```

Enable Network interfaces

```shell
netsh interface set interface "Ethernet" admin=enable
```


Manually assign ip address to interface

```shell
netsh interface ipv4 set address "Ethernet" static 192.168.1.100 255.255.255.0
```


Default Gatway configuration

```shell
netsh interface ipv4 set address "InterfaceName" static IPAddress SubnetMask DefaultGateway
```


To set DNS servers for the interface, use the following command. Replace "DNSserver1" and "DNSserver2" with the actual IP addresses of the DNS servers:

```shell
netsh interface ipv4 set dns "InterfaceName" static DNSserver1 primary
netsh interface ipv4 add dns "InterfaceName" DNSserver2 index=2
```

To show available WIFI Access Points

```shell
netsh wlan show networks
```











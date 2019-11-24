---
title: Setting FQDN
layout: post
categories:
- devops
---
- [FQDN](#fqdn)
  - [Finding FQDN](#finding-fqdn)
- [Hostname](#hostname)
  - [Finding Hostname](#finding-hostname)
- [Setting hostname and FQDN](#setting-hostname-and-fqdn)

## FQDN

**FQDN** stands for Fully Qualified Domain Name. It is a domain name that specifies its exact location in the tree hierarhcy of the Domain Name System (DNS). It specifies all domain levels, including the top-level domain and the root zone.

Example,
`somehost.example.com`

### Finding FQDN

```shell
hostname -f
```

## Hostname

A hostname is a label that is assigned to a device connected to a computer network and that is used to identify the device. Example, `kafka-dev`

### Finding Hostname

```
 $ hostname
AayushTuladhar-Mac.local
```

## Setting hostname and FQDN

* Update `/etc/hosts`

```
127.0.0.1  packer-20140710152503.aayushtuladhar.com packer-20140710152503
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
<ip-addr> kafka-dev.aayushtuladhar.com kafka-dev
```

* Update `/etc/sysconfig/network`

```
[vagrant@kafka-dev ~]$ cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=kafka-dev.aayushtuladhar.com
NOZEROCONF=yes
```

* Restart the Hostname Service

```
/etc/init.d/network restart
```

* Verify Hostname and FQDN

```
hostname
hostname -f
```

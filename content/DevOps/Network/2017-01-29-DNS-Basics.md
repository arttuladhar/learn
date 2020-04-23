---
title: DNS Basics
layout: post
categories:
- devops
---

DNS (Domain Name System) is essential component of modern internet communication. It allows us to reference computers by human friendly names instead of IP Addresses.

## Terminologies

* Domain Name
* IP Address

## DNS Lookup using Dig

Dig is a flexible tool for interrogating DNS name servers. It performs DNS lookup and is very helpful to troubleshoot DNS problems.

```bash
dig <serverName> +nostats +nocomments +nocmd
```

```bash
$ dig google.com +nostats

; <<>> DiG 9.10.6 <<>> google.com +nostats
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 9995
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		142	IN	A	172.217.8.206
```

* The first line tell us the version of dig (9.10.6) command
* Next, dig shows the header of the response it received from the DNS server
* The question section tells us about the A Record and `IN` means this is an internet lookup
* The answer section tells us that `google.com` has `IP` address of `172.217.8.206`

## References

* [DNS Concepts](https://www.digitalocean.com/community/tutorials/an-introduction-to-dns-terminology-components-and-concepts)

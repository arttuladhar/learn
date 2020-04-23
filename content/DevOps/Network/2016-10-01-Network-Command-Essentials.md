---
title: Network Tools / Command Essentials
---

## Must Know Network Tools / Commands

### NetStat

netstat stands for network statistics. This command displays incoming and outgoing network connections as well as other network information. The netstat utility can show you the open connections on your computer, which programs are making which connections, how much data is being transmitted, and other information.

```
## List all connections
netstat -a

## List TCP or UDP Connections
netstat -at   //TCP Connections
netstat -au   //UDP Connections

## List all Ports being Listened to
netstat -an | grep "LISTEN "
```

## IpTables

```
## Open 9001 Port
sudo iptables -A INPUT -p tcp --dport 9001 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp --sport 9001 -m conntrack --ctstate ESTABLISHED -j ACCEPT

sudo iptables -A INPUT -p tcp --dport 3306 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp --sport 3306 -m conntrack --ctstate ESTABLISHED -j ACCEPT
```

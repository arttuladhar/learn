---
title: Installing Docker CE on Centos 7
categories:
- devops
---
- [Install Docker Community Edition](#install-docker-community-edition)
- [Verify](#verify)

### Install Docker Community Edition

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum makecache fast
sudo yum install docker-ce
sudo systemctl start docker
```

### Verify

```shell
sudo docker run hello-world
```

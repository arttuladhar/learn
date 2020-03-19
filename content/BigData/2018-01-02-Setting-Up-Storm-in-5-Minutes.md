---
title: Setting up Apache Storm
layout: post
---

Apache Storm is free and open source distributed real-time computation system. Storm provides reliable real-time data processing what Hadoop did for batch processing. It provides real-time, robust, user friendly, reliable data processing capability with operational Intelligence.

This post is more about setting up your Storm Environment from ground up in less than 5 Minutes. Yes, you heard it right less than 5 Minutes. Without any delay, let's get it running.


```bash
# Installing Zookeeper and Storm

brew install zookeeper
brew install storm
```

```bash
# Starting Zookeeper

brew services start zookeeper

# Starting Storm
cd /usr/local/bin/

./storm nimbus
./storm supervisor
./storm ui
```

Here you go, Storm Cluster is up and you are ready to deploy your Storm Topology

```
http://localhost:8080/index.html
```
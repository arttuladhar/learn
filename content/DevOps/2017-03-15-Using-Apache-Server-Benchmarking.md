---
title: Using Apache Server Benchmarking
layout: post
categories:
- devops
---

Apache Benchmark  is a single-threaded command line tool for measuring the performance of a HTTP web server. It gives you an impression of how many requests per second your server is capable of serving.

### Installation

```
sudo apt-get install apache2-utils.
```

### Usage

```
-p  POST Message
-H  Message Header
-T  Content Type
-c  Concurrent Clients
-n  Number of Requests to Run in the Test
```


#### GET REQUEST

```
$ ab -n200 -c100 -H "APP-TOKEN: Q977quNeXjFsNjLNlmC9MK1HuRP+fFKmwDX9KSD6Y=" \
http://test-api.aayushtuladhar.com:8080/test/start_page=0
```

### POST REQUEST

```
$ ab -p body.txt -n200 -c100 -T application/json \
  https://test-api.aayushtuladhar.com:8080/anotherTest
```
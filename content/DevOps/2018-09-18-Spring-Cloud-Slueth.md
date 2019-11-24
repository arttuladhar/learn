---
title: Spring Cloud Slueth
layout: post
categories:
- devops
---

A powerful tool for enhancing logs in any application, but especially in a system built up of multiple services. This is where `spring-cloud-starter-sleuth` comes into play to help you enhance your logging and traceability across multiple systems. Just including the `spring-cloud-starter-sluth` in your project.

Few concepts you need to be familiar with when using Spring Cloud Slueth are concepts of **Trace** and **Spans**. Trace can though as single request or job that is triggered in an application. All the various steps in the request, even across application and thread boundaries will have the same traceId. Whereas, Spans can be though of a section of a job request. A single trace can be composed of multiple spans each correlating to a specific step or section of the request.

[application name, traceId, spanId, export]

|   |   |
|---|---|
| Application Name  | Name of the Application, we set in the properties file  |
| traceId | Request Id to Single Request |
| spanId | Track Unit of Work |
| export | Indicates whether or not the log was exported to an aggregator like `Zipkin` |


-----

## Resources
[Spring Cloud Sleuth - Baeldung](https://www.baeldung.com/spring-cloud-sleuth-single-application)

---
title: 01 - AWS Fundamentals
---

- [Architecture 101](#architecture-101)
  - [Access Management Basics](#access-management-basics)
  - [AWS Account](#aws-account)
      - [Authentication](#authentication)
      - [Authorization](#authorization)
      - [Billing](#billing)
  - [AWS Physical and Networking Layer](#aws-physical-and-networking-layer)
  - [Well-Architected Framework](#well-architected-framework)
  - [Elasticity](#elasticity)
  - [Introduction to S3 - (Simple Storage Service)](#introduction-to-s3---simple-storage-service)

# Architecture 101

## Access Management Basics


## AWS Account

AWS accounts are more than just a way to log in and access AWS services — they are a crucial AWS feature that AWS solutions architects can use to implement secure and high-performance systems. 

AWS Account is used to perform following functionalities:

* Authentication
* Authorization
* Billing

#### Authentication
AWS account are *isolated*. they are created initially with a single root user. This user, via it's username / password / API Keys, is the **only identity** that can use the account. If account credentials are leaked, the impact is limited to that account.

#### Authorization
**Authorization** is controlled on a **per-account** basis. The *root* user starts with full control of the account and its resources. Additional identities can be created or external identities can be granted access. Unless defined otherwise, no identity except the account root user has access to resources.

#### Billing
Every AWS account has it's own isolation billing information. This is initially in the form of an attached credit card, but established acccounts can be converted to user traditional, term-based invoicing. By default, you are only billed for resoure in your account. Billing or security exploits are limited to a single account.

---

##  AWS Physical and Networking Layer

Regions contain multiple Availability Zones (AZs), which are separated and isolated networks. AZs in the same region are connected with **reduandant high-speed, low-latency** network connections.

{{% notice info %}}
A failure in one AZ generally won't impact another AZ.
{{% /notice %}}

Most AWS services run within AZs. Some series operate from one AZ, while other replicate between AZs. Some services allow you to choose the AZ to use, and some don't.

{{% notice info %}}
Edge locations are small pockets of AWS compute, storage, and networking close to major populations and are generally used for edge computing and content delivery.
{{% /notice %}}


## Well-Architected Framework

Well-Architected Framework is a set of Best Practices, Principles, Concepts to help you build effective AWS solutions. It introduces general design principles to help you build efficient systems.

Pillars of Well Architected Framework

* Security
* Operational Excellence
* Reliability
* Performance Efficiency
* Cost Optimization

[AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/)

[AWS-Well Architected Framework - PDF](https://d1.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf)

## Elasticity

Traditional legacy system use **vertial scalling**. An attempt is made to **forecast** demand and purchase servers ideally before the demand passes current capacity. Purchase too early and capacity is wasted. Purchase too **late** and performance is **impacted**.

When horizontal scalling is used (more smaller servers), capacity can be maintained closer to demand. There is less waste because servers are smaller and there's less risk of performance impact as each increase is less expensive, so it generally require less approval.

Elasticity, or Elastic Scalling is where automation and horizontal scalling are used in conjunction to match capacity with demand. Demand is rarely so linear - it can increase or decrease, often in a rapid and sudden way. An efficient platform should scale **OUT** and **IN**, matching demand on that system.

## Introduction to S3 - (Simple Storage Service)

Simple Storage Service (S3) is a global object storage platfrom that can be used to store objects in the form of text files, photos, audio, movies, large binaries or other object types.
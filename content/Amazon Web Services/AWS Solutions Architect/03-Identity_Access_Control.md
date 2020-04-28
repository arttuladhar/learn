---
title: 03 - Identity and Access Control
---

- [Identity and Access Control](#identity-and-access-control)
  - [IAM Essentials](#iam-essentials)
  - [IAM Policies](#iam-policies)
  - [IAM Users](#iam-users)
  - [IAM Groups](#iam-groups)
  - [IAM Access Keys](#iam-access-keys)
- [Multi-Account Management and Organizations](#multi-account-management-and-organizations)
  - [AWS Organizations](#aws-organizations)
  - [Role Switching Between Accounts](#role-switching-between-accounts)

---

## Identity and Access Control

### IAM Essentials
Identity and Access Management, known as IAM, is one of the key services within AWS. It controls access to the AWS API endpoints that are used by the console UI, command line tools, and any applications wanting to utilize AWS. Identity and Access Management (IAM) is the primary service that handles authentication and authorization within AWS environments.

IAM controls access to AWS service via **policies** that can be attached to **users**, **groups** and **roles**. Users are given long-term credentials to access AWS resource (username and password or access keys).

Roles allow for short-term access to resources when assumed, using temporary access credentials.

![Identity And Access Management](/images/AWS_Certified_Solutions_Architect/IAM.jpg)

### IAM Policies

IAM policies are JSON documents that either allow or deny access to combinations of actions and resources. An IAM policy (policy document) is known as an identity policy when attached to an identity or a resource policy when attached to a resource. They have no effect until they are attached to something.

A policy document is a list of statements.

Each statement matches a request to AWS. Requests are matched based on their *Action*(or actions), which are the API calls or operations being attempted and the *Resource* (or resources) the request is against. A given statement results in an **Allow** or **Deny** for the request.

**IAM Policy - Exam Tips**

* If a request isn't explicitly allowed, it's implicity (**default**) denied.
* If a request is explicitly denied, *it overrides everything else*.
* If a request is explicitly allowed, it's allowed unless denied by an explicit deny.
* Remember: **DENY** -> **ALLOW** -> **DENY**
* Only attached policies have any impact
* When evaluating policies, all applicable policies are merged:
  * All identity (user, group, role) and any resource policies
* Managed policies allow the same policy to impact many identities.
* Inline policies allow exceptions to be applied to identities.
* AWS-managed policies are low overhead but lack flexibility.
* Customer-managed policies are flexible but require administration
* Inline and managed policies can apply to users, groups and roles.

{{% notice info %}}
Use Managed Policies to control the base level permissions and for customization use in-line permissions as needed.
{{% /notice %}}

### IAM Users

![Identity And Access Management - Users](/images/AWS_Certified_Solutions_Architect/IAM_Users.jpg)

IAM users are a type of IAM identity suitable for **long-term** access for a **known entity** (human, service, application)

Principals authenticate to IAM users either with a **username** and **password** or using **access keys**.

**Exam Facts and Figures:**

* Hard limit of 5,000 IAM users per account
* 10 group membership per IAM user
* Default maximum of 10 managed policies per user
* No inline limit, but you cannot exceed 2048 characters for all inline policies on an IAM user
* 1 MFA per user
* 2 Access Keys per user

### IAM Groups

IAM groups allow for large-scale management of IAM users. This way, policies can be applied to groups and impact collections of similar users.

**Exam Facts and Figures:**

* Groups are an admin feature to group IAM users.
* Groups can contain many IAM users, and users can be in many groups.
* IAM inline policies can be added to IAM groups - and these flow onto IAM users who are memebers
* Managed IAM policies can be attached and flow on to IAM users who are members
* Groups are not **true** identities, and they can't be referenced from resource policies
* Groups have no credentials

### IAM Access Keys

Access keys consist of access key IDs and secret access keys. The access key ID is the public part of the key and is stored by AWS once generated. The secret access key is the sensitive and private part of the access key available only once when the access key is initally generated. It is stored only the owner of the key and should never be revealed.

{{% notice note %}}
Access keys are the long-term credentials used to authenticate to AWS for anything but the console UI.
{{% /notice %}}

## Multi-Account Management and Organizations

### AWS Organizations

AWS Organizations is useful for businesses that need to manage multiple accounts. It provides the following features:

* Consolidated billing
* Service control policies (SCPs)
* Account creation
* Simplified role switching

### Role Switching Between Accounts


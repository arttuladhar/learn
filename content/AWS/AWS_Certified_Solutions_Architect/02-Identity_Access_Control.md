---
title: 02 - Identity and Access Control
---

- [Identity and Access Control](#identity-and-access-control)
  - [IAM Policy](#iam-policy)
    - [IAM Policy Notes](#iam-policy-notes)
  - [IAM Users](#iam-users)
  - [IAM Groups](#iam-groups)
  - [IAM Access Keys](#iam-access-keys)
- [Multi-Account Management and Organizations](#multi-account-management-and-organizations)
  - [AWS Organizations](#aws-organizations)
  - [Role Switching Between Accounts](#role-switching-between-accounts)

## Identity and Access Control

Identity and Access Management, known as IAM, is one of the key services within AWS. It controls access to the AWS API endpoints that are used by the console UI, command line tools, and any applications wanting to utilize AWS. Identity and Access Management (IAM) is the primary service that handles authentication and authorization within AWS environments.

IAM controls access to AWS service via **policies** that can be attached to **users**, **groups** and **roles**. Users are given long-term credentials to access AWS resource (username and password or access keys).

Roles allow for short-term access to resources when assumed, using temporary access credentials.

![Identity And Access Management](/images/AWS_Certified_Solutions_Architect/IAM.jpg)

### IAM Policy

IAM policies are JSON documents that either allow or deny access to combinations of actions and resources. An IAM policy (policy document) is known as an identity policy when attached to an identity or a resource policy when attached to a resource. They have no effect until they are attached to something.

A policy document is a list of statements.

Each statement matches a request to AWS. Requests are matched based on their *Action*(or actions), which are the API calls or operations being attempted and the *Resource* (or resources) the request is against. A given statement results in an **Allow** or **Deny** for the request.

#### IAM Policy Notes

* If a request isn't explicitly allowed, it's implicity (**default**) denied.
* If a request is explicitly denied, it overrides everything else.
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

![Identity And Access Management - Users](/images/AWS_Certified_Solutions_Architect/IAM_Users.jpg)

### IAM Users

IAM users are a type of IAM identity suitable for **long-term** access for a **known entity** (human, service, application)

Principals authenticate to IAM users either with a username and password or using access keys.

* Hard limit of 5,000


### IAM Groups

IAM groups allow for large-scale management of IAM users. This way, policies can be applied to groups and impact collections of similar users.

{{% notice info %}}
Group is not truly an **identity** in IAM because it cannot be identified as a Principal in a permission policy. It is simply a way to attach policies to multiple users at one time.
{{% /notice %}}

### IAM Access Keys

Access keys consist of access key IDs and secret access keys. Access keys are the long-term credentials used to authenticate to AWS for anything but the console UI. This lesson walks through the 
architecture and discusses some key exam-relevant points.

## Multi-Account Management and Organizations

### AWS Organizations

AWS Organizations is useful for businesses that need to manage multiple accounts. It provides the following features:

* Consolidated billing
* Service control policies (SCPs)
* Account creation
* Simplified role switching

### Role Switching Between Accounts


---
title: 07 - Hybrid and Scaling
---

## Load Balancing and Auto Scalling

Load balancing is a method used to distribute incoming connections across a group of servers or services. Incoming connections are made to the load balancer, which distributes them to associated services.

### Elastic Load Balancing (ELB)

* ELB is a service that provides a set of highly available and scalable load balancers in one of three versions: Classic (CLB), Application (ALB) and Network (NLB).
* ELBs can be paired with Auto Scaling groups to enhance high availability and fault tolerance - automating scaling / elasticity.
* An elastic load balancer has a DNS record, which allows access to the external side.
* An elastic load balancer can be public facing, meaning it accepts traffic from the public internet or internal, which is only accessible from inside a VPC and is often used betwen application tiers.
* An elastic load balancer accepts traffic via listerers using protocol and ports. It can stirp HTTPS at this point, meaning it handles encryption/decryption, reducing CPU usage on instances.


**Classic Load Balancers**

* CLB are the oldest type of load balancers and genrally should be avoided for new projects.
* Support L3 and L4 (TCP and SSL) and some HTTP/S features
* It isn't L7 device, so no real HTTP/S
* One SSL certificate per CLB - can get expensive for complex projects
* Can offload SSL connections - HTTPS to the load balancer and HTTP to the instance (lower CPU and admin overhead on instances)
* Can be associated with Auto Scaling groups
* DNS A Record is used to connect to the CLB

**Application Load Balancers**

![Application Load Balancers](/images/AWS_Certified_Solutions_Architect/Hybrid-Scaling-ALB.jpg)

* ALB operates on L7 of the OSI model. They understand HTTP and HTTPS and can load balance based on this protocol layer.
* ALBs are now recommend as the default LB for VPCs. They perform better than CLBs and are most always cheaper.
* Content rules can direct certain traffic to specific target groups.
  * Host-based rules: Route traffic based on the host used
  * Path-based rules: Route traffic based on URL path
* ALBs support EC2, ECS, EKS, Lambda, HTTPS, HTTP/2 and WebSockets, and they can be integrated with AWS Web Application Firewall (WAF)
* Use an ALB if you need to use containers or microservices.

**Network Load Balancers**

Network Load Balancers (NLB) are the newest type of load balancers and operate at Layer 4 of the OSI network model.

* Best load balancing performance within AWS





### Launch Templates and Configurations

Launch templates and launch configuration allow you to configure various configuration attributes that can be used to launch EC2 instances. Typical configuration that can be set include:

* AMI to use for EC2 launch
* Instance type
* Storage
* Key pair
* IAM role
* User data
* Purchase options
* Network configuration
* Security group(s)

Launch template address some of the weaknesses of the legacy launch configurations and add the following features:

* Versioning and inheritance
* Tagging
* More advanced purchasing options


{{% notice info %}}
Launch templates should be used over launch configuration where possible. **Neither can be edited after creation**
{{% /notice %}}


### Auto Scalling

Auto Scaling groups use launch configuration or launch template and allow automatic scale-out or scale-in based on configurable metrics. Auto Scaling groups are often paired with elastic load balancers.

Metrics such as CPU utilization or network transfer can be used either to scale out or scale in using scaling policies. Scaling can be manual, scheduled, or dynamic. Cooldowns can be defined to ensure rapid in/out events don't occur.

Scaling policies can be simple, step scaling, or target tracking.




---
title: 04 - Compute
---
- [Server-Based Compute](#server-based-compute)
  - [Elastic Cloud Compute (EC2)](#elastic-cloud-compute-ec2)
  - [Elastic Block Storage (EBS)](#elastic-block-storage-ebs)
    - [Exam Facts](#exam-facts)
  - [EBS Snapshots](#ebs-snapshots)
  - [Security Groups](#security-groups)
  - [Instance Metadata](#instance-metadata)
  - [AMI](#ami)
  - [Bootstrap](#bootstrap)
  - [Private Instance and Public Instance](#private-instance-and-public-instance)
  - [EC2 Instance Roles](#ec2-instance-roles)
  - [EBS Volume and Snapshot Encryption](#ebs-volume-and-snapshot-encryption)
  - [EBS Optimized, Enhanced Networking, and Placement Group](#ebs-optimized-enhanced-networking-and-placement-group)
    - [EBS optimization](#ebs-optimization)
    - [Enhanced networking](#enhanced-networking)
    - [Cluster, partition, and spread placement groups](#cluster-partition-and-spread-placement-groups)
  - [EC2 Billing Models](#ec2-billing-models)
  - [Dedicated Hosts](#dedicated-hosts)
- [Serverless Compute](#serverless-compute)
  - [Serverless Compute](#serverless-compute-1)
  - [Lambda Essentials](#lambda-essentials)
  - [API Gateway](#api-gateway)
  - [Step Functions](#step-functions)
- [Container Based Compute](#container-based-compute)
  - [ECS](#ecs)

## Server-Based Compute

### Elastic Cloud Compute (EC2)

EC2 is one of the most widely used services within AWS. As an Infrastructure as a Service (IaaS) product, it's responsible for providing long-running compute as a service.

EC2 instances are grouped into families, which are designed for a specific broad type workload. The type determines a certain set of features, and size decide the level of workload they can cope with.

The current EC2 families are:

1. Genral Purpose
2. Compute Optimized
3. Memory Optimized
4. Storage Optimized
5. Accelerated Computing

Instance Types include:

* T2 and T3: Low cost instance types that provide burst capability
* M5: for general workoads
* C4: Provides more capable CPU
* X1 and R4: Optimize large amounts of Fast Memory
* I3: Delivers fast IO
* P2, G3 and F1: Deliver GPU and FPGA

Instance sizes include Nano, Small, Medium, Large, X.Large, 2X.Large and Larger

Special Cases

* "a": Use AMD CPU
* "A": Arm based
* "n": Higher speed networking
* "d": NVMe storage

![Identity And Access Management - Users](/images/AWS_Certified_Solutions_Architect/EC2.jpg)

---
### Elastic Block Storage (EBS)

Elastic Block Storage is a storage service that **creates and manages volumes** based on four underlying storage types. Volumes are persistent, can be attached and removed from EC2 instances, and are replicated within a single AZ.

{{% notice info %}}
To protect against AZ failure, EBS snapshots (to S3) can be used. Data is replicated across AZs in the region and (optionally) internationally.
{{% /notice %}}

#### Exam Facts

* EBS supports a maximum per-instance throughput of 1750 MiB/s and 80,000 IOPS.

**General Purpose (gp2): (SSD)**

* Default for most workloads
* 3 IOPS/GiB  (100 IOPS - 16,000 IOPS)
* Burst up to 3,000 IOPS (credit based)
* 1 GiB - 16 TiB size, max throughput p/vol of 250 MiB/s

---
### EBS Snapshots

EBS Snapshots are a point-in-time backup of an EBS volume stored in S3. The initial snapshot is a full copy of the volume. Future snapshots only store the data changed since the last snapshot.

Snapshots can be used to create new volumes and are a great way to move or copy instances between AZs. When creating a snapshot of the root/boot volume of an instance of busy volume, it's recommended the instance is powered off, or disks are "flushed"

Snapshots can be copied between regions, shared and automated using Data Lifecycle Manager (DLM).

---
### Security Groups

![Identity And Access Management - Users](/images/AWS_Certified_Solutions_Architect/Security_Group.jpg)

Security Groups are software firewalls that can be attached to network interface and (by association) products in AWS. Security groups each have **inbound rules** and **outbound rules**. A rule allows traffic to and from a source (IP, network, named AWS entity) and protocol. Security Group belongs to a VPC

{{% notice note %}}
Each Elastic Network Interface (ENI) can have upto 5 security groups.
{{% /notice %}}

{{% notice info %}}
Security group have a hidden implicit/default deny rule but cannot explicitly deny traffic.
{{% /notice %}}

They are stateful - meaning for any traffic allowed in/out, the return traffic is automatically allowed. Security groups can reference AWS resource, other security groups, and even themselves.

---
### Instance Metadata

Instance metadata can be used to access information about an instance from the instance. It allows applications running within EC2 to have visibility into their environment. Instance metadata is data relating to the instance that can be accessed from within the instance itself using a utility capable of accessing HTTP and using the URL

```
http://169.254.169.254/latest/meta-data
```

Instance metadata is a way that scripts and application running on EC2 can get visibility of data they would normally need API calls for.

The metadata can provide the current external IPv4 address for the instance, which isn't configured on the instance itself but provided by the internet gateway in the VPC. It provides the Availability Zone the instance was lanched in and the security group applied to the instance. IN the case of spot instances, it also provides the approximate time the instance will terminate.

{{% notice tip %}}
Remember the IP address to access metadata
{{% /notice %}}

---
### AMI

AMIs (Amazon Machine Images) are used to build instance. They store snapshots of EBS volumes, permissions, and a block device mapping, which configures how the instance OS see the attached volumes. AMIs can be shared, free or paid and can be copied to other AWS regions.

1. **Configure Instance** - Source instance and attached EBS volumes are configured with any required software and configuration.
   
2. **Create Image** - Snapshots are created from volumes. AMI references snapshots, permission, and block device mapping.

3. **Launch Instance** - With approriate launch permissions, instances can be created from an AMI. EBS volumes are created using snapshots as the source, and an EC2 instance is created uinsg the block device mapping to reference its new volumes.

{{% notice warning %}}
Downside of using AMI is you can't do dynamic configuration
{{% /notice %}}

---
### Bootstrap

Bootstrapping is a process where instructions are executed on an instance during its launch process. Bootstraping is used to configure the instance, perform software installation, and add application configuraiton.

In EC2, user data can be used to run **shell scripts** or run **cloud-init** directives.
### Private Instance and Public Instance

* **Private Instance** - Private IP allocated when launching instances. Unchanged during stop/start. Released when terminated.
* **Public Instance** - Same private address as private instance. A public IPv4 address is allocated when the machine starts and deallocated when it stops. By default all public IPv4 addresses are dynamic
* **Elastic IP** - Elastic IPs are static. When allocated, they replace the normal public IP, which is deallocated.

---
### EC2 Instance Roles

EC2 **instance roles** are IAM roles that can be "assumed" by EC2 using an itermediary called an **instance profile**. An instance profile is either created automatically when using the console UI or manually when using the CLI. It's a container for the role that is associated with an EC2 instance.

The instance profile allows application on the EC2 instance to access the credentials fromt he role using the **instance metadata**.

---
### EBS Volume and Snapshot Encryption

![Identity And Access Management - Users](/images/AWS_Certified_Solutions_Architect/EBS-Encryption.jpg)

Volume encryption uses EC2 host hardware to encrypt data at rest and in transit between EBS and EC2 instances. Encryption generates a data encryption key (DEK) from a customer master key (CMK) in each region. A unique DEK encrypts each volume. Snapshots of that volume are encrypted with the same DEK, as are any volumes created from that snapshot.

Encrypted DEKs stored with volume are decrypted by KMS using a CMK and given to the EC2 host.

Plaintext DEKs stored in EC2 memory and used to encrypt and decrypt data. The EC2 instance and OS see plaintext data as normal - no performance impact.

### EBS Optimized, Enhanced Networking, and Placement Group

#### EBS optimization
EBS-optimized mode, which was historically optional is now the default, adds optimizations and dedicated communcation paths for storage and traditional data networking. This allows consistent utilization of both - and is one required feature to support higher performance storage.

#### Enhanced networking
Traditionally, virtual networking meant a virtual host (EC2 host) arranging access for n virtual machines to access one physical network card - this multitasking is done in software and is typically slow.

Enhanced networking uses **SR-IOV**, which allows a single physical network card to appear as multiple physical devices. Each instance can be given one of these (fake) physical devices. This results in **faster transfer rate, lower CPU usage**, and **lower consistent latency**. EC2 delivers this via the Elastic Network Adapter (ENA) or Intel 82599 Virtual Function (VF) interface.

#### Cluster, partition, and spread placement groups

* **Cluster Placement Group**
Cluster placement groups place instances physically near each other in a single **AZ**. Every instance can talk to each other instance at the same time at full speed. Works with enhanced networking for peak performance.

Use Cluster Placement Group for maximum performance

* **Partition Placement Group**

Use Partition Placement Group if you have large infrastructe platform and provides visibility on placement.

* **Spread Placement Group**

Use Spread Placement Group for maximum availability.

### EC2 Billing Models

On-Demand Billing (Default)

**Spot Instances**

Spot instance allow consumption of spare AWS capacity for a given instance type and size in a specific AZ. Instances are provided for as long as your bid price is above the spot price, and you ony ever pay the spot price. If your bid is exceeded, instances are terminated with a two-minute warning.

Spot fleets are a container for "capacity needs". You can specify pools of instances of certain types/sizes aiming for a given "capacity". A minimum percentage of on-demand can be set to ensure the fleet is always active.

Spot instances are perfect for non-critical workloads, burst workloads or consistent non-critical jobs that can tolerate interruptions without impacting functionality.

Spot is not suitable for long-running workloads that require stability and cannot tolerate interruptions.

{{% notice tip %}}
Spot Pricing can save you more than 90% than on-demand application.
{{% /notice %}}

* **Reserved Instances**

Reserved instance lock in a reduced rate for one or three years. Zonal reserved instance include a capacity reservation. Your commitment incurs costs even if instance aren't launched. Reserved purchases are used for long-running, understood, and consistent workloads.

**When to Use Reserved Purchases**

* Base / Consistent Load
* Known and Understood Growth
* Critical Systems / Components

**When to Use Spot Instances / Fleets**

* Burst-y workloads
* Cost-critial, when can cope with interruptions

**When to Use On-Demand**

* Default or unknown demand
* Anything in between reserved/spot
* Short-term workloads that cannot tolerate interruptions

### Dedicated Hosts

EC2 dedicated hosts are a feature of EC2, giving you complete control over physical instance placement and dedicated hardware free from other customer interaction.


## Serverless Compute

### Serverless Compute

![Serverless](/images/AWS_Certified_Solutions_Architect/serverless.png)

Serverless architecture consists of two main principles, including **BaaS** (Backend as a Service), which means using third party services where possible rather than running your own. Example include Auth0 or Cognito for authentication and Firebase or DynamoDb for data storage. Servless architecture uses event driven architecture using **FaaS** (Function as a Service) products to provide application logic. These functions are only actively invoked when they are needed.

### Lambda Essentials

Lambda is a **FaaS** product. Function are code, which run in a runtime. Functions are invoked by events, perform actions for **upto 15 minutes** and terminate. Functions are also stateless - each run is clean.

![Serverless Compute Lambda](/images/AWS_Certified_Solutions_Architect/Serverless_Compute_Lambda.jpg)

### API Gateway

API Gateway is a managed API endpoint service. It can be used to create, publish, monitor and secure APIs **"As a Service"**. API Gateway can use other AWS services for compute (FaaS/IaaS) as well as to store and recall data.

![Api Gateway](/images/AWS_Certified_Solutions_Architect/ApiGateway.jpg)

{{% notice info %}}
Pricing is based on the number of API calls, the data transferred and any caching required to improve performance.
{{% /notice %}}

### Step Functions

Step Functions is a **serverless visual workflow** servcie that provides state machines. A state machine can orchestrate other AWS services with simple logic, branching, and parallel execution, and it maintains a state. Workflow steps are known as **states**, and they can perform work via **tasks**. Step Functions allows for long-running serverless workflows. A state machine can be defined by using Amazon State Language (ASL).

Without Step Functions, Lambda functions could only run for 15 minutes. Lambda functions are stateless. State machines maintain state and allow longer running processess.

## Container Based Compute

### ECS 
ECS is a managed container engine. It allows **Docker containers** to be deployed and managed within AWS environments. ECS can use infrastructure clsutes based on EC2 or Fargate where AWS manages the backing infrastructure.

![Api Gateway](/images/AWS_Certified_Solutions_Architect/Containers_ECS.jpg)

With EC2 launch type utilizes your own EC2 instances. AWS Fargate is a managed service, so tasks are auto placed.

**Cluster** - A logical collection of ECS resources - either ECS EC2 instances or a logical representation of managed Fargate infrastructure

**Task Definition** - Defines your application. Similar to Dockerfile but for running containers in ECS. Task definition can contain multiple containers.

**Container Definition** - Inside a task definition, a container definition defines the individual containers a task uses. It controls the CPU and memory each container has, in addition to port mappings for the container

**Task** - A single running copy of any containers defined by a task definition. One working copy of an application

**Services** - Allows task definitions to be scaled by adding additional tasks. Define minimum and maximum values.

**Registry** - Storage for container images. (eg. ECS Container Registry or Dockerhub). Used to download image to create containers.
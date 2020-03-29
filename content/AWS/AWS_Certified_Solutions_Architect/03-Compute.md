---
title: 03 - Compute
---

- [Elastic Cloud Compute (EC2)](#elastic-cloud-compute-ec2)
- [Elastic Block Storage (EBS)](#elastic-block-storage-ebs)
  - [EBS Types](#ebs-types)
- [EBS Snapshots](#ebs-snapshots)
- [Security Groups](#security-groups)
- [Instance Metadata](#instance-metadata)
- [AMI](#ami)
- [Bootstrap](#bootstrap)
- [Private Instance and Public Instance](#private-instance-and-public-instance)
- [EC2 Instance Roles](#ec2-instance-roles)

![Identity And Access Management - Users](/images/AWS_Certified_Solutions_Architect/EC2.jpg)

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

---
### Elastic Block Storage (EBS)

Elastic Block Storage is a storage service that creates and managed volumes based on four underlying storage types. Volumes are persistent, can be attached and removed from EC2 instances, and are replicated within a single AZ.


{{% notice info %}}
To protect against AZ failure, EBS snapshots (to S3) can be used. Data is replicated across AZs in the region and (optionally) internationally.
{{% /notice %}}

* EBS supports a maximum per-instance throughput of 1750 MiB/s and 80,000 IOPS.

#### EBS Types

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

Security Groups are software firewalls that can be attached to network interface and (by association) products in AWS. Security groups each have inbound rules and outbound rules. A rule allows traffic to and from a source (IP, network, named AWS entity) and protocol. Security Group belongs to a VPC

{{% notice info %}}
Each Elastic Network Interface (ENI) can have upto 5 security groups.
{{% /notice %}}

Security group have a hidden implicit/default deny rule but cannot explicitly deny traffic.

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

1. Configure Instance - Source instance and attached EBS volumes are configured with any required software and configuration.
   
2. Create Image - Snapshots are created from volumes. AMI references snapshots, permission, and block device mapping.

3. Launch Instance - With approriate launch permissions, instances can be created from an AMI. EBS volumes are created using snapshots as the source, and an EC2 instance is created uinsg the block device mapping to reference its new volumes.

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

EC2 instance roles are IAM roles that can be "assumed" by EC2 using an itermediary called an **instance profile**. An instance profile is either created automatically when using the console UI or manually when using the CLI. It's a container for the role that is associated with an EC2 instance.

The instance profile allows application on the EC2 instance to access the credentials fromt he role using the **instance metadata**.
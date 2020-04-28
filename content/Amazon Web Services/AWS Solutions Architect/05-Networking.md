---
title: 05 - Networking
---
- [Networking Fundamentals](#networking-fundamentals)
  - [IP Addressing](#ip-addressing)
  - [Subnetting](#subnetting)
  - [Firewall](#firewall)
  - [Proxy Servers](#proxy-servers)
- [Virtual Private Cloud (VPC)](#virtual-private-cloud-vpc)
  - [Virtual Private Cloud (VPC):](#virtual-private-cloud-vpc-1)
  - [VPC Routing](#vpc-routing)
  - [Routes](#routes)
  - [VPC Peering](#vpc-peering)
  - [VPC Endpoints](#vpc-endpoints)
- [Global DNS (Route 53) Fundamentals](#global-dns-route-53-fundamentals)
- [Global DNS (Route 53) Advanced](#global-dns-route-53-advanced)

## Networking Fundamentals

![OSI Model](/images/AWS_Certified_Solutions_Architect/OSI_Model.jpeg)

The Open Syste Interconnection (OSI) Model is a standard used by networking manufacturers globally. It was created and published in 1984; it splits all network communications into seven layers. Each layer servers the layer that's above it plus the layer beneath it which adds additional capabilities. Data between two devices travels down the stack on the device's A-side (wrapped at each layer) and gets transmitted before moving up the stack at the device B-side (where the wrapping gets stripped aways at every stage). The data wrapping process is called encapsulation.

* **At Layer 1 (Physical)**, networks use a shared medium where devices can transmit signals and listen. Layer 1 showcases how data gets received and transmitted while taking into consideration the medium, voltages, and RF details.

* **Layer 2 (Data Link)** adds MAC addresses that can be used for named communication between two devices on a local network. Aditionally, layer 2 adds control over the media, avoiding cross-talk, this allows a back-off time and retransmission. L2 communications use L1 to broadcast and listen. L2 runs on top of L1.

* **The Network Layer (L3)** allows for unique device-to-device communcation over interconnected networks. L3 devices can pass packets over tens or even hundreds of L2 networks. The packets remain largely unchanged during this journey - travelling within different L2 frames as they pass over various networks.

* **L4 (Transport)** adds TCP and UDP. TCP is designated for reliable transport, and UDP is aimed at speed. TCP uses segments to ensure data is received in the consistent order and adds error checking and "ports" allowing different stream of communications to the same host.

* **L5 (Session)** adds the concept of sessions, so that request and reply communication streams are viewed as a single "session" of communication between client and server.

* **L6 (Presentation)** adds data converstion, encryption, compression,and standard that L7 can use. L7(Application) is where protocols (such as HTTP, SSH, and FTP) are added.

---

### IP Addressing

IPv4 addresses are how two devices can communicate at layer 4 and above of the OSI seven-layer model. IP address (IPs) are actually 32-bit binary values but are represented in dotted-decimal notation to make them easier for humans to read and understand.

IPs are split into a network part and host part. The netmask (eg. 255.255.255.0) or prefix (e.g. /24) shows where this split occurs.

IP | 10 | 0 | 0 | 0
---|---|--- |---|---
Binary | 10000000 | 00000000 | 00000000 | 00000000
Subnet Mask | 255 | 255 | 255 | 0
Prefix /24 | 11111111 | 11111111 | 11111111 | X

---

### Subnetting

![OSI Model](/images/AWS_Certified_Solutions_Architect/Subnets.jpg)

Subnetting is a process of breaking a network down into smaller subnetworks. You might be allocated a public range for your business or decide on a private range for a VPC. Subnetting allows you to break it into smaller allocations for use in smaller network.

If you pick 10.0.0.0/16 for your VPC, it's a single network from 10.0.0.0 to 10.0.255.255 and offers 65,536 addresses. With a certain size of VPC, increasing the prefix creates two smaller networks. Increasing agian creates four even smaller networks. Increasing again creates eight smaller and so on.

### Firewall

A firewall is a device that historically sits at the border between different networks and monitors traffic flow between them. A firewall is capable of reading packet data and either allowing or denying traffic based on that data.

Firewall establish a barrier between networks of different security levels and historically have been the first line of defense against perimeter attacks.

### Proxy Servers

A proxy server acts as a gateway between you and the internet. It's an intermediary server separating end users from the websites they browse. Proxy servers provide varying levels of functionality, security, and privacy depending on your use case, needs, or company policy.

A proxy server is a type of gateway that sits between a private and public network. Proxy servers can also choose to pass on traffic or not based on network layer appliances eg, username or element of corporate identity.

## Virtual Private Cloud (VPC)

### Virtual Private Cloud (VPC):

* A private network within AWS. It's your private data center inside the AWS platform.
* Can be configured to be public/private or a mixture
* Regional (can't span regions), highly available, and can be connected to your data center and corporate networks
* Isolated from other VPCs by *default*
* VPC and subnet: Max /16 (65,536 IPs) and minimum /28 (16 IPs)
* VPC subnet can't span AZs (1:1 Mapping)
* Certain IPs are reserved in subnets


**Regional Default VPC:**

* Required for some services, used as a default for most
* Pre-configured with all required network/security
* Configured using /16 CIDR Block (172.31.0.0/16)
* A /20 Public subnet in each AZ, allocating a public P by default
* Attached internet gateway with a "main" route table sending all IPv4 traffic to the internet gateway using a 0.0.0.0/0 route
* A default DHCP option set attached
* SG: Default - all from itself, all outbound
* NACL: Default - allow all inbound and outbound

**Custom VPC:**

* Can be designed and configured in any valid way
* You need to allocate IP ranges, create subnets, and provision gateways and networking, as well as design and implement security
* When you need multiple tiers of a more complex set of networking
* Best practise is to **not** use default for most production things

### VPC Routing

* Every VPC has a virtual routing device called the VPC router.
* It has an interface in any VPC subnet known as the "subnet+1" address for 10.0.1.0/24, this would be 10.0.1.1/32
* The router is highly available, scalable, and controls data entring and leaving the VPC and its subnets.
* Each VPC has a "main" route table, which is allocated to all subnets in the VPC by default. A subnet must have one route table.
* Addditional "custom" route tables can be created and associated with subnets - but only one route table (RT) per subnet.
* A route table controls what the VPC router does with traffic leaving a subnet.
* An internet gateway is created and attached to a VPC (1:1). It can route traffic for public IPs to and from internet.

### Routes

* A RT is a collection of routes that are used when traffic from a subnet arrives at the VPC router.
* Every route table has a local route, which matches the CIDR of the VPC and lets traffic be routed between subnets.
* A route contains a destination and a target. Traffic is forwarded to the target if its destination matches the route destination.
* If multiple routes apply, the most specific is chosen. A/32 is choen before a /24, before a /16.
* Default routes (0.0.0.0/0 v4 and ::/0 v6) can be added that match any traffic not already matched.
* Targets can be IPs or AWS networking gateway objects.
* A subnet is a public subnet if it is (1) configured to allocate public IPs, (2) if the VPC has an associated **internet gateway**, and (3) if that subnet has a **default** route to that internet gateway.

![OSI Model](/images/AWS_Certified_Solutions_Architect/VPC-Route-IG.jpg)

---

### VPC Peering

* Allows direct communication between VPCs
* Services can communicate using private IPs from VPC to VPC
* VPC peers can span AWS accounts and even regions (with some limitations)
* Data is encrypted and transits via the AWS global backbone.
* VPC peers are used to link two VPCs at layer 3: company mergers, shared services, company and vendor, auditing.

**Important Limits and Considerations:**

* VPC CIDR blocks cannot overlap
* VPC peers connect two VPCs - routing is not transitive
* Routes are required at both sites (remote CIDR -> peer connection)
* NACLs and SGs can be used to control access
* SGs can be referenced but **not** cross-region
* IPv6 support is not available cross-region
* DNS reoslution to *private* IPs can be enabled, but it's a setting needed at both sides.

**Steps to Enable VPC Peering**

1. Create VPC Peering Object
2. Add Route **FROM** VPC Subnet and **TO** VPC Peering Object.
3. Update Security Group to Unblock the Inbound and Outbound Rules 
4. Ensure there aren't any connection Blocking Rules in Network ACLs

{{% notice warning %}}
VPCs doesn't support Transitive Routing
{{% /notice %}}

### VPC Endpoints

VPC Endpoints are gateway objects created within a VPC. They can be used to connect to AWS public servers without the need for the VPC to have an attached internet gateway and be public.

**VPC Endpoint Types:**

* Gateway endpoints: Can be used for DynamoDB and S3
* Interface endpoints: Can be used for everything else (e.g. SNS, SQS)

**When to Use a VPC Endpoint:**

* If the entire VPC is private with no IGW
* If a specific instance has no public IP/NATGW and needs to access public services
* To access resources restricted to specific VPCs or endpoints (private S3 bucket)
  
Limitations and Considerations:
* Gateway endpoints are used via route table entries - they are gateway devices. Prefix lists for a service are used in the destintation field with the gateway as the target.
* Gateway endpoints can be restricted via policies
* Gateway endpoints are HA across AZs in a region
* Interface endpoints are interfaces in a specific subnet. For HA, you need to add multiple interfaces - one per AZ
* Interface endpoints are controlled via SGs on that interface. NACLs also impact traffic.
* Interface endpoints add or replace the DNS for the service - no route table updates are required.
* Code changes to use the endpoint DNS, or enable private DNS to override the default service DNS.

Egress-only internet gateways provide IPv6 instances with outgoing access to the public internet using IPv6 but prevent the instances from being accessed from the internet.

{{% notice notice %}}
NAT isn't required with IPv6, and so NATGW's are compatible with IPv6. Egress-only gateways provide the outgoing-only access of a NATGW but do so without adjusting any IP addresses.
{{% /notice %}}

## Global DNS (Route 53) Fundamentals

## Global DNS (Route 53) Advanced


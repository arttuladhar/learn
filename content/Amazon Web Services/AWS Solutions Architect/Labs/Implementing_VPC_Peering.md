---
title: Implementing VPC Peering 
---

## Create a VPC Peer

Ensure you are logged in to the AWS account, INSTANCE1, and INSTANCE2 using the cloud_user credentials provided.

* Change the NACL for Public2 Subnet - change ICMP from `0.0.0.0/0` to `10.0.0.0/13`
* Create a VPC peer from VPC1 to VPC2
* Accept the VPC peer between VPC1 and VPC2

## Configure Routing

Ensure the VPC peer is created and active from Task 1:

* Locate the route tables associated with PublicSubnet1 and PrivateSubnet1
* In each - Add a route for the CIDR of VPC2 and the target of the VPC Peer created in Task 1
* Locate the route tables associated with PublicSubnet2 and PrivateSubnet2
* In each - Add a route for the CIDR of VPC1 and the target of the VPC Peer created in Task 1
* Obtain the privateIP for Instance2 and ping it from Instance1

### Create VPC Peer Mesh
Ensure the VPC Peer from Task 1 is created and active:

* Create and Accept a VPC peer from VPC2 to VPC3
* Locate the route tables associated with PublicSubnet2 and PrivateSubnet2
* In each - Add a route for the CIDR of VPC3 and the target of the VPC Peer created in Task 1
* Locate the route tables associated with PublicSubnet3 and PrivateSubnet3
* In each - Add a route for the CIDR of VPC2 and the target of the VPC Peer created in Task 1
* Edit the NACL associated with the subnet Instance3 is in. Add a INGRESS rule allowing ICMP IPv4 from 10.0.0.0/13
* Edit the NACL associated with the subnet Instance3 is in. Add a EGRESS rule allowing ICMP IPv4 to 10.0.0.0/13
* Ping the privateIP of Instance3 from Instance2 - does it work? Why?

{{% notice note %}}
VPC peering isn't transitive. A pair of peers from VPC1 <-> VPC2 and from VPC2 <-> VPC3 does not mean VPC1 and VPC3 can communicate.
{{% /notice %}}

* Create and accept a VPC peer from VPC1 to VPC3
* Locate the route tables associated with PublicSubnet1 and PrivateSubnet1
* In each - Add a route for the CIDR of VPC3 and the target of the VPC Peer created in Task 1
* Locate the route tables associated with PublicSubnet3 and PrivateSubnet3
* In each - Add a route for the CIDR of VPC1 and the target of the VPC Peer created in Task 1
* From Instance1 ping the privateIP of Instance3

## DNS Over VPC Peer
Ensure that the VPC peer created in Task 1, the routing from Task 2, and the VPC peer mesh and routing from Task 3 are all active:

* From the EC2 console locate the public DNS name and private DNS name for Instance2
* From Instance1 ping the public hostname of Instance2, and it should return a public IP
* From the VPC peer options between VPC1 and VPC2 enable both DNS resolution check boxes
* If you wait a few minutes and ping the public DNS name of Instance2 from Instance1, what happens?

Enabling DNS support for VPC peers allows the private IP usage to be forced, if applications always use the instance DNS name.

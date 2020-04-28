---
title: Designing and Building a Custom VPC from Scratch
---

This hands-on lab provides you with some experience building and connecting the following services inside AWS: VPC, subnets, internet gateway, NAT gateways, Bastion host, route tables, security groups, and network access control lists (NACLs). These services are the foundation of networking architecture inside of AWS and cover concepts such as infrastructure, design, routing, and security.

### Create a VPC

1.  Select Your VPCs.
2.  Click Create VPC, and set the following values:
    -   labVPC
    -   10.0.0.0/16
    -   Amazon Provided IPv6 block
    -   Default Tenancy
3.  Click Create.

### Create Subnets

Create a three-AZ, three-app tier subnet layout (leaving spaces for a fourth AZ and fourth tier).

1.  Select Subnets.
2.  Click Create subnet.
3.  Enter the following values in order for *Name*, *VPC*, *Availability Zone*, and *IPv4 CIDR Block*. Don't assign IPv6 block.
    -   publicA, labVPC, `us-east-1a`, 10.0.0.0/24
    -   publicB, labVPC, `us-east-1b`, 10.0.1.0/24
    -   publicC, labVPC, `us-east-1c`, 10.0.2.0/24
    -   Skip 10.0.3.0/24 as the reserved space for a fourth AZ public subnet
    -   privateA, labVPC, `us-east-1a`, 10.0.4.0/24
    -   privateB, labVPC, `us-east-1b`, 10.0.5.0/24
    -   privateC, labVPC, `us-east-1c`, 10.0.6.0/24
    -   Skip 10.0.7.0/24 as the reserved space for a fourth AZ private subnet
    -   dbA, labVPC, `us-east-1a`, 10.0.8.0/24
    -   dbB, labVPC, `us-east-1b`, 10.0.9.0/24
    -   dbC, labVPC, `us-east-1c`, 10.0.10.0/24
    -   Skip 10.0.11.0/24 as the reserved space for a fourth AZ db subnet

10.0.12.0/24, 10.0.13.0/24, 10.0.14.0/24, and 10.0.15.0/24 can be used for the fourth tier in four AZs, but we won't create them for now.

check_circleCreate Internet Gateway, Public Routing, and Bastion Host

From the VPC console:

1.  Select Subnets.
2.  Select publicA, Actions, and Modify auto-assign IP settings.
3.  Enable Enable auto-assign public IPv4 address.
4.  Repeat for `publicB` and `publicC`.

### Configure Internet Gateway

1.  Select Internet Gateways, and click Create internet gateway.
2.  Set the name tag as labVPCIGW, and click Create.
3.  Select the newly created IGW, click Actions and then Attach to VPC.
4.  Select labVPC and click Attach.

### Configure Routing

1.  Click Route Tables.
2.  Click Create route table.
3.  Set the name tag as publicRT and the VPC as labVPC.
4.  Click Create.

### Add Default Public Route

1.  Select publicRT, click Routes, Edit routes, and Add route.
2.  Set the destination as 0.0.0.0/0, target as Internet Gateway, and select labVPCIGW.
3.  Click Add route again, set the destination as ::/0 , Internet Gateway, and select labVPCIGW.
4.  Click Save routes.
5.  Click Close.

### Associate with Subnets

1.  Select publicRT, and click the Subnet Associations tab.
2.  Click Edit subnet associations.
3.  Select publicA, publicB, and publicC.
4.  Click Save.

### Create a Bastion Host

From the EC2 console:

1.  Click Launch Instance.
2.  Choose Amazon Linux 2, check 64-bit (x86), and click Select.
3.  Choose t3.micro, and click Next: Configure Instance Details.
4.  Leave all as defaults, except set the subnet to publicB and make sure *Auto-assign Public IP* is Use subnet setting (Enable).
5.  Click Next: Add Storage.
6.  Click Next: Add Tags.
7.  Add a tag, and set the *Key* to Name and *Value* to BastionHost.
8.  Click Next: Configure Security Group.
9.  For security group, create a new one with the name and description bastionSG.
10. Click Review and Launch.
11. Click Launch, select to Create a new key pair, call it vpclab, and click Download Key Pair.
12. Click Launch Instances and then View Instances.

### Verify Bastion Host Is Working

From the EC2 console:

1.  When the bastion host has 2/2 status checks, right-click, click Connect, and copy the connection command.
    -   Linux/macOS users will need to run a `chmod 400 vpclab.pem` command first to avoid errors.
    -   Windows users can connect using [this as a guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html).
2.  Run the connection command to connect to your bastion host.

check_circleConfigure Private Internet Connectivity Using NAT Gateway

From the VPC Console:

### Create the NAT Gateways

1.  Click NAT Gateways and then Create NAT Gateway.
2.  Set the subnet to publicA.
3.  Click Create New EIP and then Create a NAT Gateway.
4.  Click Close.
5.  Repeat the process for `publicB` and `publicC` for a total of three NAT gateways.
6.  Select each NAT gateway in turn, and make a note of the `NAT Gateway ID` and which public subnet it's in.

### Create Three Private Route Tables

1.  Click Route Tables.
2.  Click Create route table.
3.  Set the name as privateA-RT and VPC as labVPC.
4.  Click Create and then Close.
5.  Repeat for `privateB-RT` and `privateC-RT`.

### Route Table Associations

Do the following for each route table in `privateA-RT`, `privateB-RT`, and `privateC-RT`.

1.  Select the Subnet Associations tab, click Edit subnet associations, select the db and private subnets in the same AZ.
    -   `privateA-RT` = privateA and dbA
2.  Click Save.
3.  On the same route table, click Routes, Edit routes, and Add route.
4.  Set the destination as 0.0.0.0/0, target as NAT Gateway, and select the NAT Gateway ID in the same AZ (in the list you made earlier).
5.  Click Close.
6.  Repeat these steps for each route table.

check_circleConfigure and Test VPC Security

1.  Create an App Server privateA using the same bastion `vpclab` key.
2.  Configure security group, only allowing incoming SSH from the bastion security group.
3.  Log in via SSH to the App Server.
4.  In the AWS console, navigate to VPC > Network ACLs.
5.  Select the default NACL, and click Edit inbound rules.
6.  Click Add Rule, and set the following values:
    -   *Rule #*: 50
    -   *Type*: ALL Traffic
    -   *Source*: Your IP address (which you can get by googling "what is my IP" in a new browser tab), and append /32 at the end
    -   *Allow / Deny*: DENY
7.  In the terminal, try to log in to the bastion host.
8.  In the AWS console, remove the NACL's rule #50 to remove the explicit DENY.
9.  In the terminal, try connecting to the bastion host again.
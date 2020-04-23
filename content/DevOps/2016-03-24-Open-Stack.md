---
title: OpenStack
layout: post
categories:
- devops
---

## Getting Started with OpenStack

### What is OpenStack

OpenStack is elastic cloud software that provides software developers with the ability to control the virtual infrastructure on which to deploy their applications. It is a set of software tools for building and managing cloud computing platforms for public and private clouds.

It accelerates time-to-market by dramatically reducing application provisioning times, giving companies full control of their software development lifecycle and ultimately giving them a significant competitive advantage. OpenStack also provides application portability by allowing enterprises to freely move between OpenStack clouds without vendor lock-in.

### Introduction to OpenStack

OpenStack lets users deploy virtual machines and other instances that handles different tasks for managing a cloud environment on the fly.

### Key Components of OpenStack

* Nova - Primary Computing Engine behind OpenStack. It is used for deploying and managing large numbers of virtual machines and other instances to handle computing tasks.

* Swift - Storage System for Objects and Files.

<iframe width="560" height="315" src="https://www.youtube.com/embed/bCsw2kkIWyw" frameborder="0" allowfullscreen></iframe>

### Network Commands

```
### List floating IP Pools
openstack ip floating pool list

### Get FloatingIP from the external Network
openstack ip floating create <poolname>
openstack ip floating create ext_vlan1767_net

### Create a Neutron Network
neutron net-create ART-Land

#### View list of Networks
neutron net-list

#### Create network for project
neutron net-create <NetworkName>

#### Creating Subnet
neutron subnet-create --name ART-Subnet ART-Land 192.168.10.0/24

#### View Subnet
neutron subnet show <subnetName>
neutron subnet-show ART-Subnet

### Create Router
neutron router-create <routername>
neutron router-create ART-router

### Add Interface to router
neutron router-interface-add <router> <subnet>
neutron router-interface-add ART-router ART-Subnet

### Attach Router to External Network
neuter router-gateway-set ART-router ext_vlan1767_net

# Security Groups and Rules

### Creating Security Group
openstack security group <groupname> --description 'Allow SSH and pings'
openstack security group create BasicSG --description 'Allow SSH and Pings'

### Adding Rules to Security Groups

#### Create Rule to allow SSH
openstack security group rule create BasicSG --proto tcp --dst-port 22:22

#### Create Rule to allow Ping from any Source IP
openstack security group rule create BasicSG --proto icmp --dst-port -1

### Create Security Group to Open all TCP internal
openstack security group create OpenSG --description 'All TCP internal'

#### Add a new security rule to our OpenSG group that will allow all traffic from the private subnet in
openstack security group rule create OpenSG --proto tcp --dst-port 1:65535 --src-ip 192.168.10.0/24
```

### Creating Instances

```
openstack server create --flavor smem-4vcpu --image ubuntu-latest --security-group BasicSG --nic net-id=ART-Land --key-name ArtKey ARTBastionHost
```

### Working with Stacks

```
### Launching a Stacks
openstack stack create --template <templateFile> --environment <envFile> <stackName>

### Listing Stacks
openstack stack list
openstack flavor list
openstack image list
```

-----

## Neutron

* Provides API to allow your users to create networks, subnets, routers etc.

**Network** - An isolated L2 segment, analogous to a VLAN in physical networking.

**Subnet** - A block of v4 of v6 IP address and associated configuration state.

**Port** - A connection point for attaching a single device (NIC) to Neutron network.


```bash
openstack network list

# Create Network
openstack network create mynetwork

# Create Subnet
openstack subnet create --network mynetwork \
--subnet-range 10.0.0.0/29 --dns-nameserver 8.8.8.8 mynetwork-subnet
```

---

## Nova

Nova sits on top of Hypervisor
Nova Flavors

```bash
# List images
openstack image list

# List flavors
openstack flavor list

# List networks
openstack network list

# Boot an instance using flavor and image names (if names are unique)
openstack server create --flavor m1.tiny --image cirros --nic net-id=private MyFirstInstance

# Boot another instance
openstack server create --flavor m1.tiny --image cirros --nic net-id=private MySecondInstance

# List instances, notice status of instance
openstack server list

# Show details of instance
openstack server show MyFirstInstance

# View console log of instance
openstack console log show MyFirstInstance

# Get the console url of the instance
openstack console url show MyFirstInstance
```

---

## Cinder

Creates a volume on a Hypervisor. Allowing instances to attaching and detaching the volume without losing it's persistence.

Cinder is powered by LVM and iSCSI.
Cinder is Inspired by Elastic Block Storage.

```bash
openstack volume create --size 1 --type sata MyFirstVolume

openstack server create --flavor m1.tiny --image cirros --nic net-id=private MyVolumeInstance

openstack server add volume $MYVOLUMEINSTANCE_ID $MYFIRSTVOLUME_ID

openstack server remove volume $MYVOLUMEINSTANCE_ID $MYFIRSTVOLUME_ID
```

---

# Use 'myadmin' credentials
source ~/credentials/myadmin

# Verify Cinder services are functioning and checking in :-)
```bash
openstack volume service list
```

# Use 'myuser' credentials
```
source ~/credentials/myuser
```

# Create a new volume
```
openstack volume create --size 1 --type sata MyFirstVolume
```

# Boot an instance to attach volume to
```
openstack server create --flavor m1.tiny --image cirros --nic net-id=private MyVolumeInstance
```

# List instances, notice status of instance
```
openstack server list
```

# List volumes, notice status of volume
```
openstack volume list
```

# Get volume and instance IDs
```bash
MYFIRSTVOLUME_ID=`openstack volume show MyFirstVolume | awk '/ id / { print $4 }'`
MYVOLUMEINSTANCE_ID=`openstack server show MyVolumeInstance | awk '/ id / { print $4 }'`

# Attach volume to instance after instance is active, and volume is available
openstack server add volume $MYVOLUMEINSTANCE_ID $MYFIRSTVOLUME_ID

# Confirm the volume has been attached
openstack volume list

# Get the console url of the instance
openstack console url show MyVolumeInstance

# Login to the instance
# username: cirros
# password: cubswin:)

# From inside the instance
# List storage devices
sudo fdisk -l

# Make filesystem on volume
sudo mkfs.ext3 /dev/vdb

# Create a mountpoint
sudo mkdir /extraspace

# Mount volume at mountpoint
sudo mount /dev/vdb /extraspace

# Create a file on volume
sudo touch /extraspace/helloworld.txt
sudo ls /extraspace

# Unmount volume
sudo umount /extraspace

# Log out of instance
exit

# Detach volume from instance
openstack server remove volume $MYVOLUMEINSTANCE_ID $MYFIRSTVOLUME_ID

# List volumes, notice status of volume
openstack volume list

# Delete instance
openstack server delete MyVolumeInstance
```

---

```bash
# Use 'myuser' credentials
source ~/credentials/myuser

# Create a new instance
openstack server create --flavor m1.tiny --image cirros --nic net-id=private MyLastInstance --wait

# Get the console url of the instance
openstack console url show MyLastInstance

# Login to the instance
# username: cirros
# password: cubswin:)

# Ping openstack.org ( should fail, as we have nothing routing between the tenant network (private) and provider network (public) )
ping openstack.org

# Create a router
openstack router create MyRouter

# Connect the private-subnet to the router
openstack router add subnet MyRouter private-subnet

# List interfaces attached to router
openstack port list --router MyRouter

# Connect router to public network
neutron router-gateway-set MyRouter public

# Examine details of router
openstack router show MyRouter

# Get instance ID for MyLastInstance
MYLASTINSTANCE_ID=`openstack server show MyLastInstance | awk '/ id / { print $4 }'`

# Find port id for instance
MYLASTINSTANCE_IP=`openstack server show MyLastInstance | awk '/ addresses / { print $4 }' | cut -d '=' -f 2`
MYLASTINSTANCE_PORT_ID=`openstack port list --device-owner compute:None | awk ' /'$MYLASTINSTANCE_IP'/{print $2}'`

# Create a floating IP and attach it to instance
openstack floating ip create --port $MYLASTINSTANCE_PORT_ID public

# Create a new security group
openstack security group create remote

# Add rules to security group to allow SSH and ping
openstack security group rule create --proto icmp --src-ip 0.0.0.0/0 remote
openstack security group rule create --proto tcp --dst-port 22 --src-ip 0.0.0.0/0 remote

# Apply security group to instance with floating IP
openstack server add security group MyLastInstance remote

# Ping instance with floating IP
ping 172.16.0.12

# Delete all your servers
openstack server delete MyLastInstance
```

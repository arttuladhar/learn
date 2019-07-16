---
title: Network Concepts
---

## Projects

Projects are the outermost container and are used to group resources that share the same trust boundary. Many developers map Projects to teams since each Project has its own access policy (IAM) and member list. Projects also serve as a collector of billing and quota details reflecting resource consumption. Projects contain Networks which contain Subnetworks, Firewall rules, and Routes (see below architecture diagrams for illustration).

![GCP Projects](/gcp-projects.png?pc=75)

---

## Networks

Networks directly connect your resources to each other and to the outside world. Networks, using Firewalls, also house the access policies for incoming and outgoing connections. Networks can be Global (offering horizontal scalability across multiple Regions) or Regional (offering low-latency within a single Region).

## Subnetworks

Subnetworks allow you to group related resources (Compute Engine instances) into RFC1918 private address spaces. Subnetworks can only be Regional. A subnetwork can be in auto mode or custom mode.

An auto mode network has one subnet per region, each with a predetermined IP range and gateway. These subnets are created automatically when you create the auto mode network, and each subnet has the same name as the overall network.

A custom mode network has no subnets at creation. In order to create an instance in a custom mode network, you must first create a subnetwork in that region and specify its IP range. A custom mode network can have zero, one, or many subnets per region.

---

## Create Virtual Private Cloud (VPC) Network and Instances

```bash
# Creating VPC Network with auto subnet
gcloud compute networks create mynetwork --subnet-mode=auto

# Creating VPC Network with Custom Subnet
gcloud compute networks create privatenet --subnet-mode=custom

# Creating Custom Subnet - privatesubnet
gcloud compute networks subnets create privatesubnet --network=privatenet \
    --region=us-central1 --range=10.0.0.0/24 --enable-private-ip-google-access

```

```bash
gcloud compute instances create default-us-vm --zone=us-central1-a --network=default

gcloud compute instances create mynet-us-vm --zone=us-central1-a --network=mynetwork

gcloud compute instances create mynet-eu-vm --zone=europe-west1-b --network=mynetwork

gcloud compute instances create privatenet-bastion --zone=us-central1-c \
    --subnet=privatesubnet --can-ip-forward

gcloud compute instances create privatenet-us-vm --zone=us-central1-f \
    --subnet=privatesubnet
```

{{% notice tip %}}
When any Project is created, a single Network named default is created for you. The default Network has the following 2 firewall rules defined for network traffic:
{{% /notice %}}

`default-deny-all-ingress` - Deny all incoming traffic

`default-allow-all-egress` - Allow all outbound traffic

## Firewall Rules

{{% notice info %}}
The privilege of creating, modifying, and deleting firewall rules is reserved for the **compute.securityAdmin** role by IAM.
{{% /notice %}}

```bash
gcloud beta compute firewall-rules create mynetwork-allow-icmp --network mynetwork \
    --action ALLOW --direction INGRESS --rules icmp

gcloud beta compute firewall-rules create mynetwork-allow-ssh --network mynetwork \
    --action ALLOW --direction INGRESS --rules tcp:22

gcloud beta compute firewall-rules create mynetwork-allow-internal --network \
    mynetwork --action ALLOW --direction INGRESS --rules all \
    --source-ranges 10.128.0.0/9

gcloud beta compute firewall-rules list \
    --filter="network:mynetwork"
```

## Cloud Routes

If you want traffic from specific instances to specific ranges to be routed in a specific way, you can use Google Cloud Routes to set up the destination for this traffic.

You can route traffic based on instance tags and destination range, and you can set the next hop to either:

* A specific instance (by instance name or IP)
* A VPN Tunnel
* The default internet gateway

If multiple routes exist, the more specific route will be used. If there are multiple of those, the lowest priority value is used. Between Subnetworks, routes are automatically created implicitly at lowest priority value. Those routes cannot be changed or deleted.

### Convert to a NAT gateway

```bash
# Tag Instance
gcloud compute instances add-tags privatenet-us-vm --zone us-central1-f --tags nat-me

# Create NAT Route
gcloud compute routes create nat-route --network privatenet \
--destination-range 0.0.0.0/0 --next-hop-instance privatenet-bastion \
--next-hop-instance-zone us-central1-c --tags nat-me --priority 800
```

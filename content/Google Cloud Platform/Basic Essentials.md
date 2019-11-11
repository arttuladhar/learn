---
title: Basic Essentials
---

## Creting Instance (Directly)

```bash
# Create Compute Instance
gcloud compute instances create gcelab2 --machine-type n1-standard-2 \
    --zone us-central1-c
```

---

## Using Instance Templates / Instance Groups

```bash
# Create Instance Template
gcloud compute instance-templates create nginx-template \
         --metadata-from-file startup-script=startup.sh

# Create Target Pool
gcloud compute target-pools create nginx-pool

# Create Instance Group
gcloud compute instance-groups managed create nginx-group \
         --base-instance-name nginx \
         --size 2 \
         --template nginx-template \
         --target-pool nginx-pool

# List Instances
gcloud compute instances list

```

## Create Filewall

```bash
# Create Filewall rule to Allow 80
gcloud compute firewall-rules create www-firewall --allow tcp:80
```

---

## SSH Instance

```bash
gcloud compute ssh gcelab2 --zone us-central1-c
```

> `gcloud` is a command-line tool for Google Cloud Platform

---

> `gsutil` is a command-line tool to mange Cloud Storage resources

---

## Config

```bash
# List Config
gcloud config list

# Sets Default Zone to us-central1-a
gcloud config set compute/zone us-central1-a
```

---

### Kubernetes Cluster Engine

A cluster consists of at least one cluster master machine and multiple worker machines called nodes. Nodes are Compute Engine virtual machine (VM) instances that run the Kubernetes processes necessary to make them part of the cluster.

```bash
# Create Cluster
gcloud container clusters create my-precious-cluster

# Updates a kubeconfig file with appropriate credentials to point kubectl at a specific cluster in GKE
gcloud container cluster get-credentials my-precious-cluster

# Delete Cluster
gcloud container clusters delete my-precious-cluster
```

---

## Network Load Balancer

Network load balancing allows you to balance the load of your systems based on incoming IP protocol data, such as address, port, and protocol type. You also get some options that are not available, with HTTP(S) load balancing. For example, you can load balance additional TCP/UDP-based protocols such as SMTP traffic. And if your application is interested in TCP-connection-related characteristics, network load balancing allows your app to inspect the packets, where HTTP(S) load balancing does not.

```bash
# Create Forwarding Rules
gcloud compute forwarding-rules create nginx-lab \
    --region us-central1 --port=80 --target-pool nginx-pool

# List Forwarding Rules
gcloud compute forwarding-rules list
```

### Creating HTTP(s) Load Balancer

```bash
# Create Health Check
gcloud compute http-health-checks create http-basic-check

# Defining a HTTP service and map a port name to the relevant port
gcloud compute instance-groups manged set-named-ports nginx-group --named-ports http:80

# Creating Backend Service
gcloud compute backend-services create nginx-backend \
    --protocol HTTP \
    --http-health-checks http-basic-check \
    --global

# Adding instance group to the backend services
gcloud compute backend-services add-backend nginx-backend \
    --instance-group nginx-group \
    --instance-group-zone us-central1-a \
    --global

# Create a default URL map that directs all incoming requests to all your instances
gcloud compute url-maps create web-map --default-service nginx-backend

# Create a target HTTP proxy to route requests to URL map
gcloud compute target-http-proxies create http-lb-proxy --url-map web-map

# Create global forwarding rule to handle and route incoming requests
gcloud compute forwarding-rules create http-content-rule \
    --global --target-http-proxy http-lb-proxy --ports 80
```

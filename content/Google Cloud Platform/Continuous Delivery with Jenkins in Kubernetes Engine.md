---
title: Continuous Delivery with Jenkins in Kubernetes Engine
---

![Jenkins](/jenkins-kubernetes-engine.png?width=50pc)

```bash

# Create Kubernetes Cluster
gcloud container clusters create jenkins-cd \
--num-nodes 2 \
--machine-type n1-standard-2 \
--scopes "https://www.googleapis.com/auth/projecthosting,cloud-platform"



# Update KubeConfig with Cluster credentials
gcloud container clusters get-credentials jenkins-cd

# Verify Kubernetes can connect to GCP Kubernetes Cluster
kubectl cluster-info
```

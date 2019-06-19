---
title: Orchestrating Cloud with Kubernetes
---

```bash
# Creating Kubernetes Cluster
gcloud container clusters create io
```

### Quick Demo

```bash

# Create Deployment
kubectl create deployment nginx --image=nginx:1.10.0

# List Pods
kubectl get pods

# Expose Deployment via a Service using LoadBalancer
kubectl expose deployment nginx --port 80 --type LoadBalancer

# List Service
kubectl get services
```

### Pods

Pods are the smallest deployable units of computing that can be created and managed in Kubernetes. Pods represent and hold a collection of one or more containers. Generally, if you have multiple containers with a hard dependency on each other, you package the containers inside a single pod.

[Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/)

```bash

# Create Pod
kubectl create -f pods/monolith.yaml

# List Pods
kubectl get pods

# Describe Pod
kubectl describe pods monolith

# Port Forward Pod
kubectl port-forward monolith 10080:80
```

### Services

An abstract way to expose an application running on a set of Pods as a network service.

No need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives pods their own IP addresses and a single DNS name for a set of pods, and can load-balance across them.

{{% notice info %}}
Pods aren't meant to be persistent. They can be stopped or started for many reasons - like failed liveness or readiness checks - and this leads to a problem
{{% /notice %}}

-----

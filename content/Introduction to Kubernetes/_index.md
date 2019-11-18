---
title: Kubernetes
---

![Kubernetes](https://prod-edxapp.edx-cdn.org/assets/courseware/v1/69f90ad5d78474992e3772219f3bb32a/asset-v1:LinuxFoundationX+LFS158x+2T2019+type@asset+block/kubernetes_logo.png?width=20pc)

Kubernetes is a container management technology developed in Google lab to manage containerized applications in different kind of environments such as physical, virtual, and cloud infrastructure. It is an open source system which helps in creating and managing containerization of application. This tutorial provides an overview of different kind of features and functionalities of Kubernetes and teaches how to manage the containerized infrastructure and application deployment.

## Features

**Automatic bin packing**

Kubernetes automatically schedules containers based on resource needs and constraints, to maximize utilization without sacrificing availability.

**Self-healing**

Kubernetes automatically replaces and reschedules containers from failed nodes. It kills and restarts containers unresponsive to health checks, based on existing rules/policy. It also prevents traffic from being routed to unresponsive containers.

**Horizontal scaling**

With Kubernetes applications are scaled manually or automatically based on CPU or custom metrics utilization.

**Service discovery and Load balancing**

Containers receive their own IP addresses from Kubernetes, white it assigns a single Domain Name System (DNS) name to a set of containers to aid in load-balancing requests across the containers of the set.

**Automated rollouts and rollbacks**

Kubernetes seamlessly rolls out and rolls back application updates and configuration changes, constantly monitoring the application's health to prevent any downtime.

**Secret and configuration management**

Kubernetes manages secrets and configuration details for an application separately from the container image, in order to avoid a re-build of the respective image. Secrets consist of confidential information passed to the application without revealing the sensitive content to the stack configuration, like on GitHub.

**Storage orchestration**

Kubernetes automatically mounts software-defined storage (SDS) solutions to containers from local storage, external cloud providers, or network storage systems.

**Batch execution**

Kubernetes supports batch execution, long-running jobs, and replaces failed containers.
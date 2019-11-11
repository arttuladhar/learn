---
title: 02 - Installing Kubernetes
---

- [Local Installation](#local-installation)
- [On-Premise Installation](#on-premise-installation)
- [Cloud Installation](#cloud-installation)

#### All-in-One Single-Node Installation

In this setup, all the master and worker components are installed and running on a single-node. While it is useful for learning, development, and testing, and it should not be used in production. Minikube is one such example, and we are going to explore it in future chapters.

#### Single-Node etcd, Single-Master and Multi-Worker Installation

In this setup, we have a single-master node, which also runs a single-node etcd instance. Multiple worker nodes are connected to the master node.

#### Single-Node etcd, Multi-Master and Multi-Worker Installation

In this setup, we have multiple-master nodes configured in HA mode, but we have a single-node etcd instance. Multiple worker nodes are connected to the master nodes.

#### Multi-Node etcd, Multi-Master and Multi-Worker Installation

In this mode, etcd is configured in clustered HA mode, the master nodes are all configured in HA mode, connecting to multiple worker nodes. This is the most advanced and recommended production setup.

---

### Local Installation

* Minikube - single-node local Kubernetes cluster
* Docker Desktop - single-node local Kubernetes cluster for Windows and Mac
* CDK on LXD - multi-node local cluster with LXD containers.

### On-Premise Installation

* On-Premise VMs
Kubernetes can be installed on VMs created via Vagrant, VMware vSphere, KVM, or another Configuration Management (CM) tool in conjunction with a hypervisor software. There are different tools available to automate the installation, such as Ansible or kubeadm.

* On-Premise Bare Metal
Kubernetes can be installed on on-premise bare metal, on top of different operating systems, like RHEL, CoreOS, CentOS, Fedora, Ubuntu, etc. Most of the tools used to install Kubernetes on VMs can be used with bare metal installations as well.

### Cloud Installation

* Hosted Solutions
  * Google Kubernetes Engine (GKE)
  * Azure Kubernetes Service (AKS)
  * Amazon Elastic Container Service for Kubernetes (EKS)
  * DigitalOcean Kubernetes
  * OpenShift Dedicated


[Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

---
title: 04 - Kubernetes Building Blocks
---
- [Kubernetes Object Model](#kubernetes-object-model)
- [Pods](#pods)
  - [Labels](#labels)
- [Replication Controller](#replication-controller)
- [Replica Set](#replica-set)
- [Deployments](#deployments)
- [Namespaces](#namespaces)

## Kubernetes Object Model

With each object, we declare our intent or the desired state under the *spec* section. When creating an object, the object's configuration data section from below the *spec* field has to be submitted to the Kubernetes API server.

Example of *Deployment* object configuration in YAML format.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.11
        ports:
        - containerPort: 80
```

## Pods

A Pod is the smallest and simplest Kubernetes object. It is the unit of deployment in Kubernetes, which represents a single instance of the application. A Pod is a logical collection of one or more containers, which:

* Are scheduled together on the same host with the Pod
* Share the same network namespace
* Have access to mount the same external storage (volumes).

Below is an example of a Pod object's configuration in YAML format:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.15.11
    ports:
    - containerPort: 80
```

### Labels

Labels are key-value pairs attached to Kubernetes objects (e.g. Pods, ReplicaSets). Labels are used to organize and select a subset of objects, based on the requirements in place. Many objects can have the same Label(s). Labels do not provide uniqueness to objects. Controllers use Labels to logically group together decoupled objects, rather than using objects' names or IDs.

## Replication Controller

Although no longer a recommended method, a ReplicationController is a controller that ensures a specified number of replicas of a Pod is running at any given time. If there are more Pods than the desired count, a replication controller would terminate the extra Pods, and, if there are fewer Pods, then the replication controller would create more Pods to match the desired count. Generally, we don't deploy a Pod independently, as it would not be able to re-start itself if terminated in error. The recommended method is to use some type of replication controllers to create and manage Pods.

The default controller is a Deployment which configures a ReplicaSet to manage Pods' lifecycle.

## Replica Set

A ReplicaSet is the next-generation ReplicationController. ReplicaSets support both equality- and set-based selectors, whereas ReplicationControllers only support equality-based Selectors. Currently, this is the only difference.

With the help of the ReplicaSet, we can scale the number of Pods running a specific container application image. Scaling can be accomplished manually or through the use of an autoscaler.

## Deployments

Deployment objects provide declarative updates to Pods and ReplicaSets. The DeploymentController is part of the master node's controller manager, and it ensures that the current state always matches the desired state. It allows for seamless application updates and downgrades through rollouts and rollbacks, and it directly manages its ReplicaSets for application scaling.

## Namespaces

If multiple users and teams use the same Kubernetes cluster we can partition the cluster into virtual sub-clusters using Namespaces. The names of the resources/objects created inside a Namespace are unique, but not across Namespaces in the cluster.

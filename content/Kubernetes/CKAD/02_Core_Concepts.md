---
title: 02 - Core Concepts
---

- [Kubernetes API Primitives](#kubernetes-api-primitives)
- [Pods](#pods)
- [Namespaces](#namespaces)
- [Basic Container Configuration](#basic-container-configuration)

## Kubernetes API Primitives

Kubernetes API Primitives are also called Kubernetes Objects. These are data objects that represent the state of the cluster. Example of Kubernetes Objects:

* Pod
* Node
* Service
* Service Account

The `kubectl api-resource` command will list the object types currently available to the cluster.

Every object has a spec and status:

* **Spec** - You provide the spec. This defines the desired state of the object
* **Status** - This is provided by the Kubernetes cluster and contains information about the current state of the object.

Kubernetes objects are often represented in the `yaml` format, so you can create an object by providing the cluster with yaml, defining the object and it's spec.

You can get information about an object's spec and status using the `kubectl describe` command:

```
kubectl describe $object_type $object_name
```

## Pods

**Pods** are the basic building blocks of any application running in Kubernetes.

A **Pod** consists of one or more containers and a set of resources shared by those containers. All containers managed by Kubenernetes cluster are part of a pod.

A basic pod in yaml format:

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
    labels:
        app: myapp
spec:
    containers:
    - name: myapp-container
      image: busybox
      command: ['sh', '-c', 'echo Hello K8s! && sleep 3600'] 
```

Save the pod's yaml definition to a file `my-pod.yml`, then create the pod using

```
kubectl create -f my-pod.yml
```

Similarly, you can change existing objects using:

```
kubectl apply -f my-pod.yaml
# Or
kubectl edit pod my-pod
```

```
# Delete Pod
kubectl delete pod my-pod
```

## Namespaces

Namespaces provide a way to keep your objects organized within the cluster. Every object belongs to a namespace. When no namespace is specified, the cluster will assume the `default` namespace.

When creating an object, you can assign it to a namespace by specifying a namespace in the metadata:

*mypod-ckad-ns.yml*

```yml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
    namespace: my-ckad
    labels:
        app: myapp
spec:
    containers:
    - name: myapp-container
      image: busybox
      command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 60s']
```

When working with objects using kubectl use the `-n` flag to specify the namespace. For example,

```
kubectl get pods -n my-ckad
```

## Basic Container Configuration

You can specify the `command` that will be used to run a container in the Pod spec. Thiw will override any built-in default command specified by the container image.

```yml
apiVersion: v1
kind: Pod
metadata:
    name: my-command-pod
    labels:
        app: myapp
spec:
    containers:
    - name: myapp-container
      image: busybox
      command: ['echo']
      args: ['Hello World']
    restartPolicy: Never
```

Ports are another important part of container configuration. If you need a port that the container is listening on to be exposed to the cluster, you can specify a `containerPort`

*my-containerport-pod.yml*

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-containerport-pod
    labels:
        app: myapp
spec:
    containers:
        - name: myapp-container
          image: nginx
          ports:
            - containerPort: 80
```
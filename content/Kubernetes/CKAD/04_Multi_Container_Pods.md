---
title: 04 - Multi Container Pods
---

Multi-container pods are simply pods with more than one container that all work together as a single unit.

It is often a good idea to keep containers separate by keeping them in their own separate pods, but there are several cases where multi-container pods can be beneficial.

You can create multi-container pods by listing multiple containers in the pod definition.

```yml
apiVersion: v1
kind: Pod
metadata:
    name: multi-container-pod
spec:
    containers:
    - name: nginx
      image: nginx:1.15.8
      ports: 
      - containerPort: 80
    - name: busybox-sidecar
      image: busybox
      command: ["sh", "-c", "while true; do echo "Hello From SideCar"; sleep 3600s; done;"]   
```

How can containers interact with one another in a pod ?

* **Shared Network** - All listening ports are accessible to other containers in the pod, even if they are not exposed outside the pod.
* **Shared Storage Volumes** - Containers can intereact with each other by reading and modifying files in a shared storage volume that is mounted with both containers.
* **Shared Process Namespace** - With process namespace sharing enabled, containers in the same pod can interact with and signal one another's process.


## Design Patterns for Multi-Container Pods

* **Sidecar Pod** - The sidecar pattern uses a sidecar container that enhances or adds functionality of the main container in some way. This could be something like, Eg, a sidecar that syncs files from a Git repository to the file system in a web server container.
* **Ambassador Pod** - The ambassador pattern uses an ambassador container to accept network traffic and pass it on to the main controller. One example might be an ambassador that listens on a custom port, and forwards traffic to the main container on its hard-coded port.
* **Adapter Pod** - The adapter pattern uses an adapter container to change the output of the main container in some way. An example could be an adapter that formats and decorates log output from the main container.



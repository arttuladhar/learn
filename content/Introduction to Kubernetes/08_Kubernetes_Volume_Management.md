---
title: 08 - Kubernetes Volume Management
---
- [Volumes](#volumes)
- [Volume Types](#volume-types)
- [PersistentVolumeClaims](#persistentvolumeclaims)
- [Using a Shared hostPath Volume Type](#using-a-shared-hostpath-volume-type)

## Volumes

As we know, containers running in Pods are ephemeral in nature. All data stored inside a container is deleted if the container crashes. However, the kubelet will restart it with a clean slate, which means that it will not have any of the old data.

To overcome this problem, Kubernetes uses **Volumes**. A Volume is essentially a directory backed by a storage medium. The storage medium, content and access mode are determined by the Volume Type. In Kubernetes, a Volume is attached to a Pod and can be shared among the containers of that Pod. The Volume has the same life span as the Pod, and it outlives the containers of the Pod - this allows data to be preserved across container restarts.

![Volume attached to Pod](/introduction-to-kubernetes/podvolume.png?width=25pc)

{{% notice info %}}
In Kubernetes, a Volume is attached to a Pod and can be shared among the containers of that Pod. The Volume has the same life span as the Pod, and it outlives the containers of the Pod - this allows data to be preserved across container restarts.
{{% /notice %}}

## Volume Types

* *emptyDir* - An empty Volume is created for the Pod as soon as it is scheduled on the worker node. The Volume's life is tightly coupled with the Pod. If the Pod is terminated, the content of emptyDir is deleted forever.  
* hostPath - With the hostPath Volume Type, we can share a directory from the host to the Pod. If the Pod is terminated, the content of the Volume is still available on the host.
* gcePersistentDisk - With the gcePersistentDisk Volume Type, we can mount a Google Compute Engine (GCE) persistent disk into a Pod.
* awsElasticBlockStore - With the awsElasticBlockStore Volume Type, we can mount an AWS EBS Volume into a Pod. 
* azureDisk - With azureDisk we can mount a Microsoft Azure Data Disk into a Pod.
* azureFile - With azureFile we can mount a Microsoft Azure File Volume into a Pod.
* cephfs - With cephfs, an existing CephFS volume can be mounted into a Pod. When a Pod terminates, the volume is unmounted and the contents of the volume are preserved.
* nfs - With nfs, we can mount an NFS share into a Pod.
* iscsi - With iscsi, we can mount an iSCSI share into a Pod.
* secret - With the secret Volume Type, we can pass sensitive information, such as passwords, to Pods. We will take a look at an example in a later chapter.
* configMap - With configMap objects, we can provide configuration data, or shell commands and arguments into a Pod.
* persistentVolumeClaim - We can attach a PersistentVolume to a Pod using a persistentVolumeClaim. We will cover this in our next section.

## PersistentVolumeClaims

A PersistentVolumeClaim (PVC) is a request for storage by a user. Users request for PersistentVolume resources based on type, access mode, and size. There are three access modes: ReadWriteOnce (read-write by a single node), ReadOnlyMany (read-only by many nodes), and ReadWriteMany (read-write by many nodes). Once a suitable PersistentVolume is found, it is bound to a PersistentVolumeClaim.

![PVC](/introduction-to-kubernetes/pvc2.png)

## Using a Shared hostPath Volume Type

```bash
apiVersion: v1
kind: Pod
metadata:
  name: share-pod
  labels:
    app: share-pod
spec:
  volumes:
    - name: "host-volume"
      hostPath:
        path: "/home/docker/pod-volume"
  containers:
    - image: nginx
      name: nginx
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: "host-volume"
      ports:
        - containerPort: 80
    - image: debian
      name: debian
      volumeMounts:
        - mountPath: /host-vol
          name: "host-volume"
      command: ["/bin/sh", "-c", "echo Hello Kubernetes > /host-vol/index.html; sleep 3600"]
```

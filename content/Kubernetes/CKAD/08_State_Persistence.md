---
title: 08 - State and Persistence
---

## Volumes

The internal storage of a container is ephemeral, meaning that it is designed to be highly temporary. Volumes allow you to provide more permanent storage to a pod that exists beyond the life of a container.

Kubernetes volumes allow us to mount storage to a container that isn't in the container itself.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: volume-pod
spec:
  containers:
  - image: busybox
    name: busybox
    command: ["/bin/sh", "-c", "while true; do sleep 3600; done"]
    volumeMounts:
    - mountPath: /tmp/storage
      name: my-volume
  volumes:
  - name: my-volume
    emptyDir: {}
```

---

## Persistent Volumes (PVs) and Persistent Volume Claims (PVCs)

Kubernetes is designed to manage stateless containers. Pods and containers can be easily deleted and/or replaced. When a container is removed, data stored inside the container's internal disk is lost.

State persistence refers to maintaining data outside and potentially beyond the life of a container. This usually means storing data in some kind of persistent data store that can accessed by containers.

PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs) provide a way to easily consume storage resources, especially in the context of a complex production environment that uses multiple storage solutions.

**PersistentVolume (PV)** represents a storage resource, whereas **PersistentVolumeClaim (PVC)** represents an Abstraction layer between user (pod) and the PV. PVC will automatically bind themselves to a PV that has compatible **StorageClass** and **accessMode**


### Creating Persistent Volume

```yml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: my-pv
spec:
  storageClassName: local-storage
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```


```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  storageClassName: local-storage
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 512Mi
```

```shell
# Get Persistent Volume
kubectl get pv

# Get Persistent Volume Claims
kubectl get pvc
```

## Attaching Persistent Volume Claims to a Pod

```yml
kind: Pod
apiVersion: v1
metadata:
  name: my-pvc-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["/bin/sh", "-c", "while true; do sleep 3600; done"]
    volumeMounts:
    - mountPath: "/mnt/storage"
      name: my-storage
  volumes:
  - name: my-storage
    persistentVolumeClaim:
      claimName: my-pvc
```

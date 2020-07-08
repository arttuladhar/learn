---
title: 03 - Configuration
---

- [ConfigMaps](#configmaps)
- [SecurityContexts](#securitycontexts)
- [Resource Requirements](#resource-requirements)
- [Secrets](#secrets)
- [Service Accounts](#service-accounts)
  
### ConfigMaps

A ConfigMap is a Kubernetes Object that stores configuration data in a key-value format. This configuration data can then be used to configure software running in a container, by referencing the ConfigMap in the Pod spec.

*myconfigmap.yml*

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: my-config-map
data:
    myKey: myValue
    anotherKey: anotherValue
```

Passing ConfigMap data to a pod's container as an environment variable:

*myconfigmap-pod.yml*

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-configmap-pod
spec:
    containers:
    - name: myapp-container
      image: busybox
      command: ['sh', '-c', "echo $(MY_VAR) && SLEEP 3600"]
      env:
        - name: MY_VAR
          valueFrom:
            configMapKeyRef:
                name: my-config-map
                key: myKey
```

It's also possible to pass ConfigMap data to container, in the form of file using a mount volume.


```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-configmap-volume-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo $(cat /etc/config/myKey) && sleep 3600"]
    volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: my-config-map
```


### SecurityContexts

A Pod's **Security Context** defines privilege and access control settings for a pod. If a container needs special operating system-level permissions, we can provide them using the securityContext.

The securityContext is defined as part of a Pod's spec.

This spec will cause the container to run as the OS user with an ID of 1000, and the containers will run as the group with and ID of 2000.

```yml
apiVersion: v1
kind: Pod
metadata:
    name: my-securitycontext-pod
spec:
    securityContext:
        runAsUser: 2000
        fsGroup: 3000
    containers:
    - name: myapp-container
      image: busybox
      command: ['sh', '-c', "cat /message/message.txt && sleep 3600s"]
```


### Resource Requirements

Kubernetes allows us to specify the resource requirements of a container in the pod spec. A container's memory and CPU requirements are defined in terms of **resource requests** and **limits**.

**Resoruce request**: The amount of resource necessary to run a container. A pod will only be a run on a node that has enough available resources to run the pod's containeers.

**Resource limit**: A maximum value for the resource usage of a container

Resource requests and limits are defined as part of a pod's spec.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-resource-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

Memory is measured in bytes. 128Mi means 128 Mebibytes
CPU is measured in "cores". 500m means 500 milliCPU or 0.5 CPU cores

### Secrets

**Secrets** are pieces of sensitive information store in the Kubernetes cluster, such as passwords, tokens and keys. If a container needs a sensitive piece of information, such as password, it is more secure to store it as a *secret* than storing it in a pod spec or in the container itself.

*my-secret.yml*
```yml
apiVersion: v1
kind: Secret
metadata:
    name: my-secret
stringData:
    myKey: myPassword    
```

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-secret-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! $(MY_PASSWORD) && sleep 3600"]
    env:
    - name: MY_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: myKey
```
### Service Accounts

**ServiceAccounts** allow containers running in pods to access the Kubernetes API. Some applications may need to interact with the cluster itself, and service accounts provide a way to let them to do it securely, with properly limited permissions.

You can determine the ServiceAccount that a pod will use by specifying a `serviceAccountName` in the pod spec:

```
# Createing Service Account
kubectl create serviceaccount my-serviceaccount
```

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-serviceaccount-pod
spec:
  serviceAccountName: my-serviceaccount
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
```

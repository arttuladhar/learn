---
title: 05 - Observability
---

- [Liveness and Readiness Probes](#liveness-and-readiness-probes)
    - [Liveness probe](#liveness-probe)
    - [Readiness Probe](#readiness-probe)
- [Container Logging](#container-logging)
- [Installing Metrics Server](#installing-metrics-server)
- [Monitoring Applications](#monitoring-applications)
- [Debugging](#debugging)

## Liveness and Readiness Probes

**Probes** - Allow you to customize how Kubernetes determines the status of your containers

#### Liveness probe

Indicates whether the container is running properly, and governs whether the cluster will automatically stop or restart the container. Liveness probes can be created by including them in the container spec. This probe runs a command to test the container's liveness.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-liveness-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
    livenessProbe:
      exec:
        command:
        - echo
        - testing
      initialDelaySeconds: 5
      periodSeconds: 5
```

#### Readiness Probe

Indicates whether the container is ready to service requests, and governs whether request will be forwarded to the pod.

Liveness and readiness probes can determine container status by doing things like **running a command** or **making an http request**


```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-readiness-pod
spec:
  containers:
  - name: myapp-container
    image: nginx
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

## Container Logging

A container's normal console output goes into the container log. You can access container logs using the `kubectl logs` command. For example, create a pod that generates some output every second:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args: [/bin/sh, -c, 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
```

If a pod has more than one container, you must specify which container to get logs from using the `-c` flag.

```yml
kubectl logs my-pod -c my-container
```

## Installing Metrics Server

The Kubernetes metrics server provides an API which allows you to access data about your pods, and nodes, such as CPU and memory usage.

```
cd ~/
git clone https://github.com/linuxacademy/metrics-server
kubectl apply -f ~/metrics-server/deploy/1.8+/
```

Verify
```
kubectl get --raw /apis/metrics.k8s.io/
```

## Monitoring Applications

With a working metrics server, you can use the `kubectl top` to gather information about resource usage within the cluster.

```
kubectl top pods
kubectl top pod resource-consumer-big
kubectl top pods -n kube-system
kubectl top nodes
```

---

## Debugging

Problems will occur in any system, and Kubernetes provides some great tools to help locate and fix problems when they occur within a cluster. Helpful resources:

* https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/
* https://kubernetes.io/docs/tasks/debug-application-cluster/debug-pod-replication-controller/
* https://kubernetes.io/docs/tasks/debug-application-cluster/debug-service/


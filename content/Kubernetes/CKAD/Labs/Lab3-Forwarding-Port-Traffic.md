---
title: Lab03 - Forwarding Port Traffic with an Ambassador Container
---

*fruit-service-ambassador-config.yml*

```yml
# Config Map for HA Proxy
apiVersion: v1
kind: ConfigMap
metadata:
    name: fruit-service-ambassador-config
data:
    haproxy.cfg: |-
        global
            daemon
            maxconn 256

        defaults
            mode http
            timeout connect 5000ms
            timeout client 50000ms
        timeout server 50000ms

        listen http-in
            bind *:80
            server server1 127.0.0.1:8775 maxconn 32
```

*fruit-service-pod.yml*

```yml
# Pod Definition for fruit-service
apiVersion: v1
kind: Pod
metadata:
  name: fruit-service
spec:
  containers:
    - name: fruit-service-container
      image: linuxacademycontent/legacy-fruit-service:1
    - name: fruit-service-ambassador
      image: haproxy:1.7
      ports:
        - containerPort: 80
      volumeMounts:
        - name: config-volume
          mountPath: /usr/local/etc/haproxy
  volumes:
    - name: config-volume
      configMap:
        name: fruit-service-ambassador-config
```

---

*busybox.yml*
```yml
# BusyBox Pod for Testing
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: myapp-container
    image: radial/busyboxplus:curl
    command: ['sh', '-c', 'while true; do sleep 3600; done']
```

Running a Curl command from the BusyBox

```bash
kubectl exec busybox -- curl $(kubectl get pod fruit-service -o=custom-columns=IP:.status.podIP --no-headers):80
```

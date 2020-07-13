---
title: 07 - Services and Networking
---

## Services

Services create an abstraction layer which provides network access to a dynamic set of pods.

Most services use a *selector* to determine which pods will receive traffic though the service. As pods included in the service are created and removed dynamically, clients can receive uninterrupted access by using the service.

Services are Kubernetes objects which means that they can be created using yaml descriptors. Here is an example of a simple service:

```yaml
kind: Service
apiVersion: v1
metadata:
    name: my-service
spec:
    type: ClusterIP
    selector:
        app: nginx
    ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
```

There are four service types in Kubernetes.

* **ClusterIP** - The service is exposed within the cluster using an internal IP address. The service is also accessible using the cluster DNS
* **NodePort** - The service is exposed externally via a port which listens on each node in the cluster.
* **LoadBalancer** - This only works if your cluster is set up to work with a cloud provider. The service is exposed though a load balancer that is created on the cloud platform.
* **ExternalName** - This maps the service to an external address. It is used to allow resources within the cluster to access things outside the cluster though a service

---

## Network Policy

By default, all pods in the cluster can communicate with any other pod, and reach out to any available IP.

NetworkPolicies allow you to limit what network traffic is allowed to and from pods in your cluster.

* **podSelector** - Determines which pods the NetworkPolicy applies to
* **policyTypes** - Sets whether the policy governs incoming traffic (ingress), outgoing traffic (egress), or both
* **ingress** - Rules for incoming traffic
* **egres** - Rules for outgoing traffic
* **rules** - Both ingress and egress rules are whitelist-based, meaning that any traffic that does not match at least one rule will be blocked

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: 
  name: my-network-policy
spec: 
  podSelector: 
    matchLabels: 
      app: MyApp
  policyTypes: 
    - Ingress
  ingress: 
    - from: 
        - podSelector:
            matchLabels:
              app: MyApp
          ports: 
            - protocol: TCP
              port: 6379
              

```

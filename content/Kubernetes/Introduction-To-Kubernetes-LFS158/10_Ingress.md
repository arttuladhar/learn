---
title: 10 - Ingress
---

![Ingress](/introduction-to-kubernetes/ingress_updated.png?width=25pc)

With Services, routing rules are associated with a given Service. They exist for as long as the Service exists, and there are many rules because there are many Services in the cluster. If we can somehow decouple the routing rules from the application and centralize the rules management, we can then update our application without worrying about its external access. This can be done using the **Ingress** resource.

> An Ingress is a collection of rules that allow inbound connections to reach the cluster Services.

To allow the inbound connection to reach the cluster Services, Ingress configures a Layer 7 HTTP/HTTPS load balancer for Services and provides the following:

* TLS (Transport Layer Security)
* Name-based virtual hosting
* Fanout routing
* Loadbalancing
* Custom rules

### Name-Based Virtual Hosting

*virtual-host-ingress.yaml*

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: virtual-host-ingress
  namespace: default
spec:
  rules:
  - host: blue.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-blue-svc
          servicePort: 80
  - host: green.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-green-svc
          servicePort: 80
```

*fan-out-ingress.yaml*
```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: fan-out-ingress
  namespace: default
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /blue
        backend:
          serviceName: webserver-blue-svc
          servicePort: 80
      - path: /green
        backend:
          serviceName: webserver-green-svc
          servicePort: 80
```
![Ingress-Example](/introduction-to-kubernetes/Ingress_URL_mapping.png?width=50pc)

{{% notice info %}}
The Ingress resource does not do any request forwarding by itself, it merely accepts the definitions of traffic routing rules. The ingress is fulfilled by an Ingress Controller, which we will discuss next.
{{% /notice %}}

```shell
# Start the Ingress Controller with Minikube
minikube addons enable ingress

# Deploy an Ingress Resource
kubectl create -f virtual-host-ingress.yaml
```

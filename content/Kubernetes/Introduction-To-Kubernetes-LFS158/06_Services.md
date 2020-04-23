---
title: 06 - Services
---
- [Services](#services)
- [Service Object Example](#service-object-example)
- [kube-proxy](#kube-proxy)
- [Service Discovery](#service-discovery)
- [Servie Type](#servie-type)
  - [Cluster IP](#cluster-ip)
  - [NodePort](#nodeport)
  - [LoadBalancer](#loadbalancer)
  - [ExternalIP](#externalip)
  - [ExternalName](#externalname)

## Services

> An abstract way to expose an application running on a set of Pods as a network service. With Kubernetes you don’t need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their own IP addresses and a single DNS name for a set of Pods, and can load-balance across them.

![Service](/introduction-to-kubernetes/services1.png?width=50pc)

Using the selectors `app==frontend` and `app==db`, we group Pods into two logical sets: one with 3 Pods, and one with a single Pod.

We assign a name to the logical grouping, referred to as a Service. In our example, we create two Services, frontend-svc, and db-svc, and they have the `app==frontend` and the `app==db` Selectors, respectively.

Services can expose single Pods, ReplicaSets, Deployments, DaemonSets, and StatefulSets.

## Service Object Example

```yaml
kind: Service
apiVersion: v1
metadata:
  name: frontend-svc
spec:
  selector:
    app: frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
```

The user/client now connects to a Service via its ClusterIP, which forwards traffic to one of the Pods attached to it. A Service provides load balancing by default while selecting the Pods for traffic forwarding.

{{% notice tip %}}
While the Service forwards traffic to Pods, we can select the targetPort on the Pod which receives the traffic. If the targetPort is not defined explicitly, then traffic will be forwarded to Pods on the port on which the Service receives traffic.
{{% /notice %}}

## kube-proxy

All worker nodes run a daemon called kube-proxy, which watches the API server on the master node for the addition and removal of Services and endpoints.

## Service Discovery

As Services are the primary mode of communication in Kubernetes, we need a way to discover them at runtime. Kubernetes supports two methods for discovering Services:

* Environment Variables
* DNS

Kubernetes has an add-on for DNS, which creates a DNS record for each Service and its format is `my-svc.my-namespace.svc.cluster.local.`

## Servie Type

While defining a Service, we can also choose its access scope. We can decide whether the Service:

* Is only accessible within the cluster
* Is accessible from within the cluster and the external world
* Maps to an entity which resides either inside or outside the cluster.

{{% notice tip %}}
Access scope is decided by ServiceType, which can be configured when creating the Service.
{{% /notice %}}

### Cluster IP

ClusterIP is the default ServiceType. A Service receives a Virtual IP address, known as its ClusterIP. This Virtual IP address is used for communicating with the Service and is accessible only within the cluster.

### NodePort

The NodePort ServiceType is useful when we want to make our Services accessible from the external world. The end-user connects to any worker node on the specified high-port, which proxies the request internally to the ClusterIP of the Service, then the request is forwarded to the applications running inside the cluster. To access multiple applications from the external world, administrators can configure a reverse proxy - an ingress, and define rules that target Services within the cluster.

```shell
kubectl expose pod <podname> --type=NodePort --port=80
```

### LoadBalancer

With the LoadBalancer ServiceType:

* NodePort and ClusterIP are automatically created, and the external load balancer will route to them
* The Service is exposed at a static port on each worker node
* The Service is exposed externally using the underlying cloud provider's load balancer feature.

{{% notice info %}}
The LoadBalancer ServiceType will only work if the underlying infrastructure supports the automatic creation of Load Balancers and have the respective support in Kubernetes, as is the case with the Google Cloud Platform and AWS. If no such feature is configured, the LoadBalancer IP address field is not populated, and the Service will work the same way as a NodePort type Service.
{{% /notice %}}

### ExternalIP

A Service can be mapped to an ExternalIP address if it can route to one or more of the worker nodes. Traffic that is ingressed into the cluster with the ExternalIP (as destination IP) on the Service port, gets routed to one of the Service endpoints. This type of service requires an external cloud provider such as Google Cloud Platform or AWS.

### ExternalName

ExternalName is a special ServiceType, that has no Selectors and does not define any endpoints. When accessed within the cluster, it returns a CNAME record of an externally configured Service.

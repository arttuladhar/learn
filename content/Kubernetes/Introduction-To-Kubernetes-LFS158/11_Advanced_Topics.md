---
title: 11 - Advanced Topics
---

## Annotations

With Annotations, we can attach arbitrary non-identifying metadata to any objects, in a key-value format:

```json
"annotations": {
  "key1" : "value1",
  "key2" : "value2"
}
```

Unlike Labels, annotations are not used to identify and select objects. Annotations can be used to:

* Store build/release IDs, PR numbers, git branch, etc.
* Phone/pager numbers of people responsible, or directory entries specifying where such information can be found
* Pointers to logging, monitoring, analytics, audit repositories, debugging tools, etc.

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: webserver
  annotations:
    description: Deployment based PoC dates 2nd May'2019
```

## Jobs and CronJobs

A Job creates one or more Pods to perform a given task. The Job object takes the responsibility of Pod failures. It makes sure that the given task is completed successfully. Once the task is complete, all the Pods have terminated automatically. Job configuration options include

* `parallelism` - to set the number of pods allowed to run in parallel;
* `completions` - to set the number of expected completions;
* `activeDeadlineSeconds` - to set the duration of the Job;
* `backoffLimit` - to set the number of retries before Job is marked as failed;
* `ttlSecondsAfterFinished` - to delay the clean up of the finished Jobs.

## Quota Management

## Autoscalling

Autoscaling can be implemented in a Kubernetes cluster via controllers which periodically adjust the number of running objects based on single, multiple, or custom metrics. There are various types of autoscalers available in Kubernetes which can be implemented individually or combined for a more robust autoscaling solution:

### Horizontal Pod Autoscaler (HPA)

HPA is an algorithm based controller API resource which automatically adjusts the number of replicas in a ReplicaSet, Deployment or Replication Controller based on CPU utilization.

### Vertical Pod Autoscaler (VPA)

VPA automatically sets Container resource requirements (CPU and memory) in a Pod and dynamically adjusts them in runtime, based on historical utilization data, current resource availability and real-time events.

### Cluster Autoscaler

Cluster Autoscaler automatically re-sizes the Kubernetes cluster when there are insufficient resources available for new Pods expecting to be scheduled or when there are underutilized nodes in the cluster.

## DaemonSets

A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.

Some typical uses of a DaemonSet are:

* running a cluster storage daemon, such as glusterd, ceph, on each node.
* running a logs collection daemon on every node, such as fluentd or logstash.
* running a node monitoring daemon on every node, such as Prometheus Node Exporter, Sysdig Agent, collectd, Dynatrace OneAgent, AppDynamics Agent, Datadog agent, New Relic agent, Ganglia gmond or Instana Agent.

## StatefulSets

The StatefulSet controller is used for stateful applications which require a unique identity, such as name, network identifications, strict ordering, etc. For example, MySQL cluster, etcd cluster.

The StatefulSet controller provides identity and guaranteed ordering of deployment and scaling to Pods. Similar to Deployments, StatefulSets use ReplicaSets as intermediary Pod controllers and support rolling updates and rollbacks.

## Kubernetes Federation

With Kubernetes Cluster Federation we can manage multiple Kubernetes clusters from a single control plane. We can sync resources across the federated clusters and have cross-cluster discovery. This allows us to perform Deployments across regions, access them using a global DNS record, and achieve High Availability.

## Helm Chart

To deploy an application, we use different Kubernetes manifests, such as Deployments, Services, Volume Claims, Ingress, etc. Sometimes, it can be tiresome to deploy them one by one. We can bundle all those manifests after templatizing them into a well-defined format, along with other metadata. Such a bundle is referred to as Chart. These Charts can then be served via repositories, such as those that we have for rpm and deb packages.

Helm is a package manager (analogous to yum and apt for Linux) for Kubernetes, which can install/update/delete those Charts in the Kubernetes cluster.

Helm has two components:

* A client called **helm**, which runs on your user's workstation
* A server called **tiller**, which runs inside your Kubernetes cluster.

The client helm connects to the server tiller to manage Charts. 

## Security Contexts and Pod Security Policies

At times we need to define specific privileges and access control settings for Pods and Containers. Security Contexts allow us to set Discretionary Access Control for object access permissions, privileged running, capabilities, security labels, etc. However, their effect is limited to the individual Pods and Containers where such context configuration settings are incorporated in the spec section.

In order to apply security settings to multiple Pods and Containers cluster-wide, we can define Pod Security Policies. They allow more fine-grained security settings to control the usage of the host namespace, host networking and ports, file system groups, usage of volume types, enforce Container user and group ID, root privilege escalation, etc.

## Network Policies

Kubernetes was designed to allow all Pods to communicate freely, without restrictions, with all other Pods in cluster Namespaces. In time it became clear that it was not an ideal design, and mechanisms needed to be put in place in order to restrict communication between certain Pods and applications in the cluster Namespace. **[Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** are sets of rules which define how Pods are allowed to talk to other Pods and resources inside and outside the cluster. Pods not covered by any **Network Policy** will continue to receive unrestricted traffic from any endpoint. 

**Network Policies** are very similar to typical Firewalls. They are designed to protect mostly assets located inside the Firewall but can restrict outgoing traffic as well based on sets of rules and policies.

The **Network Policy** API resource specifies **podSelectors**, *Ingress* and/or *Egress* **policyTypes**, and rules based on source and destination **ipBlocks** and **ports**. Very simplistic default allow or default deny policies can be defined as well. As a good practice, it is recommended to define a default deny policy to block all traffic to and from the Namespace, and then define sets of rules for specific traffic to be allowed in and out of the Namespace. 

Let's keep in mind that not all the networking solutions available for Kubernetes support Network Policies. Review the Pod-to-Pod Communication section from the Kubernetes Architecture chapter if needed. By default, **Network Policies** are namespaced API resources, but certain network plugins provide additional features so that Network Policies can be applied cluster-wide.

## Monitoring and Logging

In Kubernetes, we have to collect resource usage data by Pods, Services, nodes, etc., to understand the overall resource consumption and to make decisions for scaling a given application. Two popular Kubernetes monitoring solutions are the Kubernetes Metrics Server and Prometheus.

-   **Metrics Server**\
    [Metrics Server](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server) is a cluster-wide aggregator of resource usage data - a relatively new feature in Kubernetes. 
-   **Prometheus[\
    ](https://prometheus.io/)**[Prometheus](https://prometheus.io/), now part of [CNCF](https://www.cncf.io/) (Cloud Native Computing Foundation), can also be used to scrape the resource usage from different Kubernetes components and objects. Using its client libraries, we can also instrument the code of our application.

Another important aspect for troubleshooting and debugging is Logging, in which we collect the logs from different components of a given system. In Kubernetes, we can collect logs from different cluster components, objects, nodes, etc. Unfortunately, however, Kubernetes does not provide cluster-wide logging by default, therefore third party tools are required to centralize and aggregate cluster logs. The most common way to collect the logs is using [Elasticsearch](https://kubernetes.io/docs/tasks/debug-application-cluster/logging-elasticsearch-kibana/), which uses [fluentd](http://www.fluentd.org/) with custom configuration as an agent on the nodes. **fluentd** is an open source data collector, which is also part of CNCF.


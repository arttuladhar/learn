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

A Job creates one or more Pods to perform a given task. The Job object takes the responsibility of Pod failures. It makes sure that the given task is completed successfully. Once the task is complete, all the Pods have terminated automatically. Job configuration options include parallelism - to set the number of pods allowed to run in parallel; completions - to set the number of expected completions; activeDeadlineSeconds - to set the duration of the Job; backoffLimit - to set the number of retries before Job is marked as failed; ttlSecondsAfterFinished - to delay the clean up of the finished Jobs.

## Quota Management

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

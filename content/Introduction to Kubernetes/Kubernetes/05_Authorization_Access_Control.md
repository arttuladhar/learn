---
title: 05 - Authentication, Authorization, and Admission Control
---

To access and manage any Kubernetes resource or object in the cluster, we need to access a specific API endpoint on the API server. Each access request goes through the following three stages:

**Authentication** - Logs in a user
**Authorization** - Authorizes the API requests added by the logged-in user.
**Admission Control** - Software modules that can modify or reject the requests based on some additional checks, like a pre-set Quota.

### Role-Based Access Control (RBAC) Authorizer

**Role** - With Role, we can grant access to resources within a specific Namespace.
**ClusterRole** - The ClusterRole can be used to grant the same permissions as Role does, but its scope is cluster-wide.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: lfs158
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

#### Types of RoleBindings:

**RoleBinding**
It allows us to bind users to the same namespace as a Role. We could also refer a ClusterRole in RoleBinding, which would grant permissions to Namespace resources defined in the ClusterRole within the RoleBinding’s Namespace.

**ClusterRoleBinding**
It allows us to grant access to resources at a cluster-level and to all Namespaces.

{{% notice info %}}
To enable the RBAC authorizer, we would need to start the API server with the **--authorization-mode=RBAC** option
{{% /notice%}}

### Admission Control

Admission control is used to specify granular access control policies, which include allowing privileged containers, checking on resource quota, etc. To use admission controls, we must start the Kubernetes API server with the `--enable-admission-plugins`, which takes a comma-delimited, ordered list of controller names:

```
--enable-admission-plugins=NamespaceLifecycle,ResourceQuota,PodSecurityPolicy,DefaultStorageClass
```

---
title: 05 - Authentication, Authorization, and Admission Control
---

- [Authentication](#authentication)
- [Authorization](#authorization)
    - [Types of RoleBindings](#types-of-rolebindings)
  - [Admission Control](#admission-control)
- [Demo - Authentication and Authorization](#demo---authentication-and-authorization)

To access and manage any Kubernetes resource or object in the cluster, we need to access a specific API endpoint on the API server. Each access request goes through the following three stages:

* **Authentication** - Logs in a user
* **Authorization** - Authorizes the API requests added by the logged-in user.
* **Admission Control** - Software modules that can modify or reject the requests based on some additional checks, like a pre-set Quota.

## Authentication

Kubernetes does not have an object called user, nor does it store usernames or other related details in its object store. However, even without that, Kubernetes can use usernames for access control and request logging.

Kubernetes has two kinds of users:

* **Normal Users**
They are managed outside of the Kubernetes cluster via independent services like User/Client Certificates, a file listing usernames/passwords, Google accounts, etc.

* **Service Accounts**
With Service Account users, in-cluster processes communicate with the API server to perform different operations. Most of the Service Account users are created automatically via the API server, but they can also be created manually. The Service Account users are tied to a given Namespace and mount the respective credentials to communicate with the API server as Secrets.

For authentication, Kubernetes uses different authentication modules:

* **Client Certificates**
    To enable client certificate authentication, we need to reference a file containing one or more certificate authorities by passing the `--client-ca-file=SOMEFILE` option to the API server. The certificate authorities mentioned in the file would validate the client certificates presented to the API server. A demonstration video covering this topic is also available at the end of this chapter.

* **Static Token File**
    We can pass a file containing pre-defined bearer tokens with the `--token-auth-file=SOMEFILE` option to the API server. Currently, these tokens would last indefinitely, and they cannot be changed without restarting the API server.

* **Bootstrap Tokens**
    This feature is currently in beta status and is mostly used for bootstrapping a new Kubernetes cluster.

* **Static Password File**
    It is similar to Static Token File. We can pass a file containing basic authentication details with the `--basic-auth-file=SOMEFILE` option. These credentials would last indefinitely, and passwords cannot be changed without restarting the API server.

* **Service Account Tokens**
    This is an automatically enabled authenticator that uses signed bearer tokens to verify the requests. These tokens get attached to Pods using the ServiceAccount Admission Controller, which allows in-cluster processes to talk to the API server.

* **OpenID Connect Tokens**
    OpenID Connect helps us connect with OAuth2 providers, such as Azure Active Directory, Salesforce, Google, etc., to offload the authentication to external services.

* **Webhook Token Authentication**
    With Webhook-based authentication, verification of bearer tokens can be offloaded to a remote service.

* **Authenticating Proxy**
    If we want to program additional authentication logic, we can use an authenticating proxy.

## Authorization

After a successful authentication, users can send the API requests to perform different operations. Then, those API requests get authorized by Kubernetes using various authorization modules.

* Node Authorizer
* Attribute-Based Access Control (ABAC) Authorizer
* Webhook Authorizer
* Role-Based Access Control (RBAC) Authorizer

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

#### Types of RoleBindings

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

---

## Demo - Authentication and Authorization

* Configuring User by assigning key and certificate
* Create Context with newly created User
* Add RBAC Role and rolebindings to namespace

```bash
minikube start

kubectl config view

# Create New Namespace for Demo
kubectl create namespace lfs158

mkdir rbac && cd rbac

# Create a private key for the student user with openssl tool, then create a certificate signing request for the student user with openssl tool
openssl genrsa -out student.key 2048
openssl req -new -key student.key -out student.csr -subj "/CN=student/O=learner"

cat student.csr | base64 | tr -d '\n'

touch signing-request.yaml

apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: student-csr
spec:
  groups:
  - system:authenticated
  request: <assign encoded value from cat command>
  usages:
  - digital signature
  - key encipherment
  - client auth


kubectl create -f signing-request.yaml
kubectl get csr
kubectl certificate approve student-csr
kubectl get csr

# Generating User Certificate
kubectl get csr student-csr -o jsonpath='{.status.certificate}' | base64 --decode > student.crt

cat student.crt

# Configuring Student User by assigning the key and certificate
kubectl config set-credentials student --client-certificate=student.crt --client-key=student.key

# Create Student Context with Selected User
kubectl config set-context student-context --cluster=minikube --namespace=lfs158 --user=student

kubectl config view

# Creating a Deployment with Nginx Image
kubectl -n lfs158 create deployment nginx --image=nginx:alpine
```

From the new context student-context try to list pods. The attempt fails because the student user has no permissions configured for the student-context:

```
kubectl --context=student-context get pods
```

Error from server (Forbidden): pods is forbidden: User "student" cannot list resource "pods" in API group "" in the namespace "lfs158"


```bash
# Create RBAC Role to allow only get, watch, list actions in lfs158 namespace
~/rbac$ vim role.yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: lfs158
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

~/rbac$ kubectl create -f role.yaml
~/rbac$ kubectl -n lfs158 get roles

NAME         AGE
pod-reader   57s
```

Create a YAML configuration file for a rolebinding object, which assigns the permissions of the pod-reader role to the student user. Then create the rolebinding object and list it from the default minikube context, but from the lfs158 namespace:

```bash
# Create RBAC Role Binding to the User
~/rbac$ vim rolebinding.yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-read-access
  namespace: lfs158
subjects:
- kind: User
  name: student
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io

~/rbac$ kubectl create -f rolebinding.yaml

rolebinding.rbac.authorization.k8s.io/pod-read-access created

~/rbac$ kubectl -n lfs158 get rolebindings

NAME              AGE
pod-read-access   23s
```

Now that we have assigned permissions to the student user, we can successfully list pods from the new context student-context.

```bash
~/rbac$ kubectl --context=student-context get pods

NAME                    READY   STATUS    RESTARTS   AGE
nginx-77595c695-f2xmd   1/1     Running   0          7m41s
```

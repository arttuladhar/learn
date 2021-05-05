---
title: 06 - Pod Design
---

- [Labels, Selectors, and Annotations](#labels-selectors-and-annotations)
- [Deployments](#deployments)
- [Rolling Updates and Rollbacks](#rolling-updates-and-rollbacks)
  - [Rolling Updates and Rollbacks](#rolling-updates-and-rollbacks-1)
- [Jobs and CronJobs](#jobs-and-cronjobs)

## Labels, Selectors, and Annotations

**Labels** are key-value pairs attached to Kubernetes objects. They are used for identifying various attributes of objects which can in turn be used to select and group various subsets of those objects.

We can attach labels to objects by listing them in the `metadata.labels` section of an object descriptor.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-production-label-pod
  labels:
    app: my-app
    environment: production
spec:
  containers:
  - name: nginx
    image: nginx
```

```shell
kubectl get pods --show-labels
```

Selectors are used for identifying and selecting a specific group of objects using their labels. One way to use selectors is to use them with `kubectl get` to retrieve a specific list of objects. We can specify a selector using the `-l` flag.

```shell
# Equality Based Selector
kubectl get pods -l app=my-app

# InEquality Based Selector
kubectl get pods -l env!=prod

# Set Based Selector
kubectl get pods -l 'env in (dev, qa)'

# Chain Multiple Selectors
kubectl get pods -l app=myapp, env=prod
```

Annotations are similar to labels in that they can be used to store custom metadata about objects.

{{% notice warning %}}
However, unlike labels, annotations cannot be used to select or group objects in Kubernetes. External tools can read, write and interact with annotations
{{% /notice %}}

We can attach annotations to objects using the `metadata.annotations` section of the object descriptors:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-annotation-pod
  annotations:
    owner: bob@homeoffice.com
    git-commit: bd7833kjgll20xj3
spec:
  containers:
  - name: nginx
    image: nginx
```

Like labels, existing annotations can also be viewed using `kubectl describe`

## Deployments

**Deployments** provide a way to declaratively manage a dynamic set of replica pods. They provide powerful functionality such as scaling and rolling updates.

A deployment define a desired state for the replica pods. The cluster will constantly work to maintain the desired state, creating, removing, and modifying the replica pods accordingly.

A deployment is a Kubernetes object that can be created using a descriptor:

*nginx-deployment.yml*
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 3
    selector:
        matchLabels:
            app: nginx
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
            - name: nginx
              image: nginx:1.7.9
              ports:
              - containerPort: 80
```

Note the following:

* **spec.replicas** - The number of replica pods
  
* **spec.template** - A template pod descriptor which defines the pods which will be created

* **spec.selector** - The deployment will manage all pods whose label match this selector

```shell
kubectl get deployments

kubectl get deployment <deployment_name>

kubectl describe deployment <deployment_name>

kubectl edit deployment <deployment_name>

kubectl delete deployment <deployment_name>
```

## Rolling Updates and Rollbacks

Rolling updates provide a way to update a deployment to a new container version by gradually updating replicas so that there is no downtime.

```yml
# rolling-deployment-nginx.yml
--
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.1
        ports:
        - containerPort: 80
```

```shell
# Set New Image for the Deployment
kubectl set image deployment/rolling-deployment nginx=nginx:1.7.9 --record

# Perform Rollout
kubectl rollout history deployment/rolling-deployment
```

{{% notice info %}}
The --record flag records information about the update so that it can be rolled back later.
{{% /notice %}}

### Rolling Updates and Rollbacks

Rollbacks allow us to revert to a previous state. For example, if a rolling update breaks something, we can quickly recover by using a rollback.

```shell
# Get Rollout History for Deployment
kubectl rollout history deployment/rolling-deployment
```

{{% notice info %}}
The --revision flag will give more information on a specific revision number
{{% /notice %}}

```shell
# Perform Rollback to previous revision
kubectl rollout undo deployment/rolling-deployment
kubectl rollout undo deployment/rolling-deployment --to-revision=1
```

You can also control how rolling update are performed by setting `maxSurge` and `maxUnavailable` in the deployment spec:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-deployment
spec:
  strategy:
    rollingUpdate:
      maxSurge: 3
      maxUnavailable: 2
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.1
        ports:
        - containerPort: 80
```

## Jobs and CronJobs

Jobs can be used to reliably execute a workload until it completes. The job will create one or more pods. When the job is finished, the container(s) will exit and pods(s) will enter the Completed state.

*batch-job.yml*
```yml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

*hello-cron-job.yml*
```yml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

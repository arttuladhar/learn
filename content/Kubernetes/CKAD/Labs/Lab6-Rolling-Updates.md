---
title: Lab06 - Rolling Updates with Kubernetes Deployments
---

#### `candy-deployment` Deployment descriptor

```yml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"candy-deployment","namespace":"default"},"spec":{"replicas":2,"selector":{"matchLabels":{"app":"candy-ws"}},"template":{"metadata":{"labels":{"app":"candy-ws"}},"spec":{"containers":[{"image":"linuxacademycontent/candy-service:2","name":"candy-ws"}]}}}}
  creationTimestamp: null
  generation: 1
  name: candy-deployment
  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/candy-deployment
spec:
  progressDeadlineSeconds: 600
  replicas: 2
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: candy-ws
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: candy-ws
    spec:
      containers:
      - image: linuxacademycontent/candy-service:2
        imagePullPolicy: IfNotPresent
        name: candy-ws
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

#### Updating to New Image
```shell
# Setting New Image for Deployment
kubectl set image deployment/candy-deployment candy-ws=linuxacademycontent/candy-service:3 --record

# Performing Rollout
kubectl rollout status deployment/candy-deployment
```

## Performing RollBack

```shell
kubectl rollout history deployment/candy-deployment

kubectl rollout undo deployment/candy-deployment
```

---
title: Lab08 - Exploring Services in Kubernetes
---

```yml
# auth-svc.yml
apiVersion: v1
kind: Service
metadata:
  name: auth-svc
spec:
  type: NodePort
  selector:
    app: auth
  ports:
  - port: 8080
    targetPort: 80
```

```yml
# data-svc.yml
apiVersion: v1
kind: Service
metadata:
  name: data-svc
spec:
  selector:
    app: data
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
```

```shell

# Listing Services
kubectl get svc

# Listing Endpoints
kubectl get ep
```

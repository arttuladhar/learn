---
title: Lab01 - Creating Kubernetes Pod
---

*nginx.yml*
```yml
apiVersion: v1
kind: Pod
metadata:
   name: nginx
   namespace: web
spec:
   containers:
   - name: nginx
     image: nginx
     command: ['nginx']
     args: ['-g', 'daemon off;', '-q']
     ports:
     - containerPort: 80
```


```bash
## Applying Spec Definition for Creating Pod
kubectl -n web apply -f nginx.yml

## Verifying Pod running in web namespace
kubectl get pods -n web
```
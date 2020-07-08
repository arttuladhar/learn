---
title: Lab2 - Configuring Kubernetes Pod
---

#### Create a ConfigMap called `candy-service-config` to store the container's configuration data.

*candy-service-config.yml*
```yml
apiVersion: v1
kind: ConfigMap
metadata:
    name: candy-service-config
data:
    candy.cfg: |-
        candy.peppermint.power: 100000000
        candy.nougat-armor.strength: 10
```

#### Create a Kubernetes secret called `db-password` to store the database password.

*candy-service-secret.yml*
```yml
apiVersion: v1
kind: Secret
metadata:
   name: db-password
stringData:
   db-password: Kub3rn3t3sRul3s!
```

#### Create the pod for the candy-service application according to the provided specification.

*candy-service-pod.yml*
```yml
apiVersion: v1
kind: Pod
metadata:
   name: candy-service
spec:
  securityContext:
    fsGroup: 2000
  containers:
  - name: candy-service
    image: linuxacademycontent/candy-service:1
    volumeMounts:
      - name: candy-service-volume
        mountPath: /etc/candy-service
    env:
      - name: DB_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-password
            key: db-password
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  volumes:
     - name: candy-service-volume
       configMap:
         name: candy-service-config          
```


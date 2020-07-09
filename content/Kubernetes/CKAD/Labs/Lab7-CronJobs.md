---
title: Lab 7 - Configuring Cron Jobs in Kubernetes
---

*cleanup-cronjob.yml*

```yml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: cleanup-cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: hello
              image: linuxacademycontent/data-cleanup:1
          restartPolicy: OnFailure
```

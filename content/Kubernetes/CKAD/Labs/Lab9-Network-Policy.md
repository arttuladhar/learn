---
title: Lab09 - Network Policy
---

```yml
# customer-data-policy.yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
spec:
  egress:
  - ports:
    - port: 80
      protocol: TCP
    to:
    - podSelector:
        matchLabels:
          customer-data-access: "true"
  ingress:
  - from:
    - podSelector:
        matchLabels:
          customer-data-access: "true"
    ports:
    - port: 80
      protocol: TCP
  podSelector:
    matchLabels:
      app: customer-data
  policyTypes:
  - Ingress
  - Egress
```

```yml
# inventory-policy.yml
apiVersion: extensions/v1beta1
kind: NetworkPolicy
spec:
  egress:
  - ports:
    - port: 80
      protocol: TCP
    to:
    - podSelector:
        matchLabels:
          inventory-access: "true"
  ingress:
  - from:
    - podSelector:
        matchLabels:
          inventory-access: "true"
    ports:
    - port: 80
      protocol: TCP
  podSelector:
    matchLabels:
      app: inventory
  policyTypes:
  - Ingress
  - Egress
```
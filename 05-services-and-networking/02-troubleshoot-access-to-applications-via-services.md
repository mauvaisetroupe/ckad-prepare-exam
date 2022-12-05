# Services & Networking - Provide and troubleshoot access to applications via services

## What is the DNS for s service in a namespace
[//]: # (source 01 / Namespaces)

```
k get services -A
NAMESPACE       NAME              TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
dev-namespace   db-service        ClusterIP      10.43.54.161    <none>        6379/TCP                     16m
```

```
db-service.dev-namespace.svc.cluster.local
```
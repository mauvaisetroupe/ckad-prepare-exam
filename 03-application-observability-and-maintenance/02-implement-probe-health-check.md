# Application observability and maintenance - Implement probes and health checks

```
apiVersion: v1
kind: Pod
spec:
  containers:
  - image: 
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
    livenessProbe:
      httpGet:
        path: /live
        port: 8080
      periodSeconds: 1
      initialDelaySeconds: 80
```
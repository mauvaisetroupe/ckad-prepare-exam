# Application observability and maintenance - Implement probes and health checks


## Liveness and readiness probes in pod yaml

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

## Create a pod with a liveness probe that just runs the command 'ls'

```
apiVersion: v1
kind: Pod
spec:
  containers:
  - image: nginx
    livenessProbe: 
      exec: 
        command: 
        - ls 
```
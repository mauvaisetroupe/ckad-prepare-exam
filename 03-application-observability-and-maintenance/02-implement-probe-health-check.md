# Application observability and maintenance - Implement probes and health checks


## Liveness and readiness probes in pod yaml
[//]: # (source 03 / Readiness Probes)


>**Warning**
>
> host is not mandatory

```
   host	<string>
     Host name to connect to, defaults to the pod IP. You probably want to set
     "Host" in httpHeaders instead.
```

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

[next](./03-use-provided-tools-to-monitor.md)
# Application observability and maintenance - Utilize container logs

## check log containers
[//]: # (source 03 / Logging)

```
kubectl logs my-busybox-pod -f
```

## check log of a specific container in a multi-containers pod

```
kubectl logs my-pod -c my-container-in-podm
```

[next](./05-debugging-in-kubernetes.md)
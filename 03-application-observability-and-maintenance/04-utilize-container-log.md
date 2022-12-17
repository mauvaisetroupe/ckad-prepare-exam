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

## Check logs of a Job

```
$ kubernetes logs jobs/my-job
```


## The application stores logs at location /log/app.log. View the logs.
[//]: # (source 06 / Persistent Volume)

```
kubectl exec webapp -- cat /log/app.log
```

[next](./05-debugging-in-kubernetes.md)
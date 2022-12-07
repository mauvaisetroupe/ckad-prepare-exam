# Application Environment, Configuration and Security - Understanding and defining resource requirements, limits and quotas

## Add CPU and memory requirements and limits
[//]: # (source 02 / Resource limits)


>**Warning**
>
> A container CANNOT use more CPU resources than its limit
> 
> A container CAN use more memory resources that its limit but POD will be terminated

<pre>
apiVersion: v1
kind: Pod
spec:
  containers:
  - image: polinux/stress
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
</pre>


## Check pod state

<pre>
$ kubectl describe pod nginx | <b>grep -A5</b> -i state
    State:          Running
      Started:      Mon, 05 Dec 2022 22:01:39 +0100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
</pre>


```
$ kubernetes get pods -o wide
NAME       READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
nginx      1/1     Running   0          54s   10.42.0.11   controlplane   <none>           <none>
```
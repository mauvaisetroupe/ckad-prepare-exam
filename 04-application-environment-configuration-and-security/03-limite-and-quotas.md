# Application Environment, Configuration and Security - Understanding and defining resource requirements, limits and quotas

## Add CPU and memory lomit on pods
[//]: # (source 02 / Resource limits)

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

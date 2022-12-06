# Application Environment, Configuration and Security - Understand SecurityContexts

## Find used to execute the process within the  pod?

```
$ kubectl exec ubuntu-sleeper -- whoami
```

## Edit the pod to run the conainer with user ID 1000, and NET_ADMIN capability


```
$ kubernetes get pod my-pod -o yaml > mypod.yaml
apiVersion: v1
kind: Pod
spec:
  containers:
  - command:
    - sleep
    - "4800"
    securityContext:
      runAsUser: 1000
      capabilities:
        add: ["SYS_TIME"]

```

>**warning**
>
> Could add securityContext at pod or containers level

```
$ kubectl explain pod.spec.securityContext
$ kubectl explain pod.spec.containers.securityContext
```

# Application Design and Build - Understand multi-container Pod design patterns

## create pod with 2 containers
[//]: # (source 03 / Multi-Container PODs)

```
apiVersion: v1
kind: Pod
spec:
  containers:
  - image: 
    name: app
  - image: 
    name: sidecar
```

## Create a pod with a initContainer
[//]: # (source 03 / Init container)


```
apiVersion: v1
kind: Pod
metadata:
  name: red
  namespace: default
spec:
  containers:
    image: busybox:1.28
    name: mycontainer
    ...
  initContainers:
  - image: busybox
    name: myinitcontainer
    command: 
      - "sleep"
      - "30"
```      

[next](./04-utilize-persistent-and-ephemeral-volumes.md)
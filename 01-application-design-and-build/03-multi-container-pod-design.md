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

# Application observability and maintenance - Debugging in Kubernetes


## find error in initContainer 
[//]: # (source 03 / Init container)

```
kubectl logs my-pod -c my-init-container
```


## Get logs about the previous instance of a POD (after crash()

```
kubectl logs my-pod --previous
```


[next](../04-application-environment-configuration-and-security/01-discover-and-use-resources-that-extend-ubernetes-CRD.md)
# Application Deployment - Use the Helm package manager to deploy existing packages

Helm is a package manager for Kubernetes


## List Help releases

```
$ helm list -A 
```

## Delete a release

```
$ helm delete -n my-namespace release-name
```

## install a release 

```
$ helm install -n my-namespace release-name nginx-stable/nginx-ingress       
```
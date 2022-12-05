# Application Deployment -  Understand Deployments and how to perform rolling updates

## create a new deployment

[//]: # (source 01 / Deployments)

```
$ kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3
```

# Application Deployment - Use the Helm package manager to deploy existing packages

Helm is a package manager for Kubernetes

## To install Helm, identify the name of the OS
[//]: # (source 07/Labs – Install Helm)

```
$ cat /etc/*release*
```

## find a chart on the hub
[//]: # (source 07/Labs – Helm Concepts)
```
$ helm search hub wordpress
URL                             CHART VERSION	APP VERSION        	DESCRIPTION                                       
https://artifacthub.io/pac...	0.1.0        	1.1                	this is my wordpress package    
```

## List repositories and add a bitnami helm chart repository

```
$ helm repo list
NAME            URL                                                 
puppet          https://puppetlabs.github.io/puppetserver-helm-chart
```

```
$ helm repo add bitnami https://charts.bitnami.com/bitnami
```

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

## Download a chart, modify values.yaml then install

```
$ helm pull bitnami/apache
$ ls
apache-9.2.7.tgz
$ tar xvf apache-9.2.7
$ ls
apache  apache-9.2.7.tgz 
$ helm install mywebapp ./apache 
```
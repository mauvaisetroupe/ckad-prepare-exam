# Application observability and maintenance - Understand API deprecations


## Find information about shortnames, api groups and version
[//]: # (source 07/Lab – API Versions/Deprecations)

<pre>
$ <b>kubectl api-resources </b>
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
bindings                                       v1                                     true         Binding
componentstatuses                 cs           v1                                     false        ComponentStatus
configmaps                        cm           v1                                     true         ConfigMap
endpoints                         ep           v1                                     true         Endpoints
</pre>

## What is the preferred version for job
<pre>
$ <b>kubectl explain job</b>
KIND:     Job
VERSION:  <b>batch/v1</b>
</pre>


## What is the preferred version for authorization.k8s.io api group?
<pre>
$ <b>kubectl proxy 8001&</b>
$ <b>curl localhost:8001/apis/authorization.k8s.io</b>
{
  "kind": "APIGroup",
  "apiVersion": "v1",
  "name": "authorization.k8s.io",
  "versions": [
    {
      "groupVersion": "authorization.k8s.io/v1",
      "version": "v1"
    }
  ],
  "preferredVersion": {
    "groupVersion": "authorization.k8s.io/v1",
    </b>"version": "v1"</b>
  }
}
</pre>

## Enable the v1alpha1 version for rbac.authorization.k8s.io API group on the controlplane node.

Add the --runtime-config flag in the command field
<pre>
$ <b>vi /etc/kubernetes/manifests/kube-apiserver.yaml</b>
 - command:
    - kube-apiserver
...
    <b>- --runtime-config=rbac.authorization.k8s.io/v1alpha1</b>
</pre>



## change the deprecated API version to the networking.k8s.io/v1 

First, install kubctl convert plugin (use documentation)

<pre>
$ <b>kubectl convert -f ingress-old.yaml --output-version networking.k8s.io/v1 > ingress-new.yaml</b>
</pre>

[next](./02-implement-probe-health-check.md)



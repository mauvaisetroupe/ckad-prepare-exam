# Application Environment, Configuration and Security - Discover and use resources that extend Kubernetes (CRD)

## Create a CustomResourceDefinition manifest file 

Go to kubernetes documentation and write yaml file 
https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/

``` yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  # name must match the spec fields below, and be in the form: <plural>.<group>
  name: crontabs.stable.example.com
spec:
  # group name to use for REST API: /apis/<group>/<version>
  group: stable.example.com
  # list of versions supported by this CustomResourceDefinition
  versions:
    - name: v1
      # Each version can be enabled/disabled by Served flag.
      served: true
      # One and only one version must be marked as the storage version.
...
```

## Create the CRD resource in the K8S API


```
$ kubectl apply -f resourcedefinition.yaml
```
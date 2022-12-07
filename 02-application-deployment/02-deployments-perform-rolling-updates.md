# Application Deployment -  Understand Deployments and how to perform rolling updates

[Create a deployment](#create-a-new-deployment)

[Taint and toleration](#taint-and-toleration)

[Label and node selector](#label-and-node-selector)

---

## Create a new deployment

### Create a deployment with a specific image

[//]: # (source 01 / Deployments)

```
$ kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3
```

### Find error in ReplicatSet selector
[//]: # (source 04/Label and Selectors)

<pre>
apiVersion: apps/v1
kind: ReplicaSet
metadata:
   name: replicaset-1
spec:
   replicas: 2
   selector:
      matchLabels:
        <b>tier: front-end</b>
   template:
     metadata:
       labels:
        <b>tier: nginx</b>
     spec:
       containers:
       - name: nginx
         image: nginx
</pre>

**`selector` does not match template `labels`**

---
## Taint and toleration
[//]: # (source 02 / Taints and Tolerations)

Node affinity is a property of Pods that attracts them to a set of nodes (either as a preference or a hard requirement). 
Taints are the opposite -- they allow a node to repel a set of pods.

A toleration "matches" a taint if the keys are the same and the effects are the same, and:
- the operator is Exists (in which case no value should be specified), or
- the operator is Equal and the values are equal.

### Find taint on a Node

```
$ kubectl describe node node01 | grep -A5 -i taint
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  node01
  AcquireTime:     <unset>
  RenewTime:       Tue, 06 Dec 2022 11:17:38 +0000
```

### Create a taint on node01 with key of spray, value of frontend and effect of NoSchedule

```
$ kubectl taint node node01 spray=frontend:NoSchedule
```

### Remove the taint on a node which currently has the taint effect of NoSchedule.

<pre>
$ kubernetes describe nodes node01 | grep i taint
Taints:             spray=frontend:NoSchedule
$ kubectl taint node node01 spray=frontend:NoSchedule-
</pre>

### Create a pod named  with the nginx image, which has a toleration set to the taint frontend.

<pre>
$ kubernetes run nging --image=nginx --dry-run=client -o yaml > nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nging
spec:
  containers:
  - image: nginx
    name: nginx
  <b>tolerations:
    - effect: NoSchedule
        key: app
        operator: Equal
        value: frontend</b>
</pre>

---
## Label and node selector

### Apply a label color=blue to node node01

```
$ kubernetes label nodes node01 color=blue
```

### Create a new deployment named blue with the nginx image and 3 replicas, with node affinitty to node 1

<pre>
$ kuberetes create deploy blue --image=nginx --replicas=3

$ kuberetes edit deploy blue
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      <b>affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: color
                operator: In
                values:
                - blue</b>

</pre>

---



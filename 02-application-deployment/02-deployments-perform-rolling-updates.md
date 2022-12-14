# Application Deployment -  Understand Deployments and how to perform rolling updates

[Create a deployment](#create-a-new-deployment)

[Label and node selector](#label-and-node-selector)

[Taint and toleration](#taint-and-toleration)

[Rollout](#how-many-pods-could-be-stop-during-a-rollingupdate)


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
        <b>tier: front-end <-- selector does not match template labels </b>
   template:
     metadata:
       labels:
        <b>tier: nginx  <-- selector does not match template labels</b>
     spec:
       containers:
       - name: nginx
         image: nginx
</pre>

---

## Label and node selector


### Method-1, use nodeName

<pre>
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: nginx
    image: nginx
  <b>nodeName: kube-01</b>
 </pre>

### Method-2, use nodeSelector

#### Apply a label disktype=ssd to node node01

```
$ kubernetes label nodes node01 disktype=ssd
```

#### Use nodeSelector in POD yaml

<pre>
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  <b>nodeSelector:
    disktype: ssd</b>
</pre>


### Method-2, use nodeAffinity (only for complex requests)

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
                operator: NotIn
                values:
                - red
                - blue</b>
</pre>


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

## How many pods could be stop during a RollingUpdate

```
$ kubectl describe deployments.apps frontend 
...
StrategyType:           RollingUpdate
RollingUpdateStrategy:  25% max unavailable, 25% max surge```
```

## Create deployment, update image and show rollout history

```
$ kubectl create deployment nginx --image=nginx:1.16
$ kubectl set image deployment nginx nginx=nginx:1.17 --record
$ kubectl set image deployment nginx nginx=nginx:latest --record
```

<pre>
<b>$ kubectl rollout history deployment nginx</b>
REVISION  CHANGE-CAUSE
1         None
2         kubectl set image deployment nginx nginx=nginx:1.17 --record=true
3         kubectl set image deployment nginx nginx=nginx:latest --record=true
</pre>

<pre>
$ <b>kubectl rollout history deployment nginx --revision=1</b>
deployment.apps/nginx with revision #1
Pod Template:
  Labels:	app=nginx
	pod-template-hash=6c4b465475
  Containers:
   nginx:
    Image:	nginx:1.16
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
</pre>

## Rollback to previous version

```
$ kubectl rollout undo deployment nginx
```

```
$ kubectl rollout history deployment nginx
REVISION  CHANGE-CAUSE
1         <none>
3         kubectl set image deployment nginx nginx=nginx:latest --record=true
4         kubectl set image deployment nginx nginx=nginx:1.17 --record=true
```

>**Info**
>
>Revision 2 disapear, because rollbacking from rev=3 to previous revision, rev=2 promoted to rev=4 



## Rollback to a specific revision in history

```
$ kubectl rollout undo deployment nginx --to-revision=1
```

[next](./03-helm-package-manager-to-deploy-existing-packages.md)
# Application Environment, Configuration and Security

## Identify ServiceAccount mounted in a pod
[//]: # (source 02/Service Account)
<pre>
$ <b>kubectl get pod web-dashboard-68f98dc77c-22nbn -o yaml</b>
apiVersion: v1
kind: Pod
metadata:
  serviceAccount: default
  serviceAccountName: default
</pre>

## Create a ServiceAccount, and create a token for this sa
[//]: # (source 02/Service Account)
<pre>
$ <b>kubectl create serviceaccount dashboard-sa</b>
</pre>

[//]: # (source 02/Service Account)
<pre>
$ <b>kubectl create token dashboard-sa</b>
eyJhbGciOiJSUzI1NiIsImtpZCI6ImxlenRpQ3JGNVQ0Qjg2YnlNS1FVUGpsS0t3cDdXd3NiUHhlTzkzaHNPSncifQ....
</pre>

## Edit the deployment to change ServiceAccount from default to dashboard-sa
[//]: # (source 02/Service Account)

### Methode 1
<pre>
$ <b>kubectl edit deployments web-dashboard</b>
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-dashboard
spec:
  template:
    spec:
      <b>serviceAccountName: dashboard-sa</b>
      conatiner:
</pre>

### Method 2
<pre>
$ <b>kubectl set serviceaccount deployment web-dashboard dashboard-sa</b>
</pre>

## Identify authorization modes
[//]: # (source 07/Practice Test Role Based Access Controls)

### Method 1 
<pre>
$ <b>ps aux | grep apiserver</b>
root        8994  1.6  1.9 1044792 316884 ?      Ssl  déc.03  14:15 kube-apiserver 
--advertise-address=192.168.49.2 --allow-privileged=true <b>--authorization-mode=Node,RBAC</b>
</pre>

### Method 2
<pre>
$ <b>kubectl get pods -A</b>
$ <b>kubectl get pods -n kube-system  kube-apiserver-minikube -o yaml</b>
...
  - command:
    - kube-apiserver
    - --advertise-address=192.168.49.2
    - --allow-privileged=true
    - <b>--authorization-mode=Node,RBAC</b>
</pre>

## Find resources to which a role give access
[//]: # (source 07/Practice Test Role Based Access Controls)
<pre>
$ <b>kubectl get roles -A</b>
$ <b>kubectl describe role -n kubernetes-dashboard kubernetes-dashboard</b>
Name:         kubernetes-dashboard
PolicyRule:
  Resources       Non-Resource URLs  Resource Names                     Verbs
  ---------       -----------------  --------------                     -----
  secrets         []                 [kubernetes-dashboard-certs]       [get update delete]
</pre>

## Find accounts binded to a role
[//]: # (source 07/Practice Test Role Based Access Controls)
<pre>
$ <b>kubectl get rolebindings -A</b>
NAMESPACE              NAME                           ROLE                             AGE
kubernetes-dashboard   kubernetes-dashboard           Role/kubernetes-dashboard        15h


$ <b>kubectl describe -n kubernetes-dashboard rolebindings kubernetes-dashboard</b>
Name:         kubernetes-dashboard
Role:
  Name:  kubernetes-dashboard
Subjects:
  Kind            Name                  Namespace
  ----            ----                  ---------
  ServiceAccount  kubernetes-dashboard  kubernetes-dashboard
</pre>

## Inspect permission granted to a user
[//]: # (source 07/Practice Test Role Based Access Controls)
<pre>
$ <b>kubectl config view</b>
apiVersion: v1
kind: Config
clusters:
- cluster:
    ...
  name: minikube
contexts:
- context:
    cluster: minikube
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
users:
- name: minikube
  user:
    client-certificate: /home/lionel/.minikube/profiles/minikube/client.crt
    client-key: /home/lionel/.minikube/profiles/minikube/client.key
</pre>

## create a role with access to create, get and list pods, bind the role to a serviceAccount (or user) and check operation is allowed
[//]: # (source 07/Practice Test Role Based Access Controls)
<pre>
$ <b>kubectl create role developer-role --verb=create,get,list --resource=pods</b>
</pre>

<pre>
$ <b>kubectl create rolebinding developer-role-binding --role=developer-role --serviceaccount=default:my-service-account</b>
$ <b>kubectl describe rolebindings developer-role-binding</b>
Name:         developer-role-binding
Role:
  Name:  developer-role
Subjects:
  Kind            Name                Namespace
  ----            ----                ---------
  ServiceAccount  my-service-account  default
</pre>

<pre>
$ <b>kubectl get pods --as user1</b>
Error from server (Forbidden): pods is forbidden: User "user1" cannot list resource "pods" ...
$ <b>kubectl get pods --as=system:serviceaccount:default:my-service-account</b>
</pre>

## Add a resource or resourceName to an existing Role
[//]: # (source 07/Practice Test Role Based Access Controls)

<pre>
$ <b>kubectl edit role developer-role</b>
</pre>


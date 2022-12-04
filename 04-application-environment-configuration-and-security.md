# Application Environment, Configuration and Security

## Identify authorization nmodes
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

## Find resources a role give access
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
NAMESPACE              NAME                                                ROLE                                                  AGE
kubernetes-dashboard   kubernetes-dashboard                                Role/kubernetes-dashboard                             15h


$ kubectl describe -n kubernetes-dashboard rolebindings kubernetes-dashboard 
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

## check if a user get list pods
[//]: # (source 07/Practice Test Role Based Access Controls)
<pre>
$ <b>kubectl get pods --as user1</b>
Error from server (Forbidden): pods is forbidden: User "user1" cannot list resource "pods" in API group "" in the namespace "default"
</pre>

## create a role with access to create, get and list pods
[//]: # (source 07/Practice Test Role Based Access Controls)
<pre>
$ <b>kubectl create role developer-role --verb=create,get,list --resource=pods</b>
</pre>
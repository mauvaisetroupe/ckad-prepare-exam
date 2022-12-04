# Application Environment, Configuration and Security

## Identify authorization nmodes
[//]: # (source 07/Practice Test Role Based Access Controls)

### Method 1 
```bash
$ ps aux | grep apiserver
root        8994  1.6  1.9 1044792 316884 ?      Ssl  déc.03  14:15 kube-apiserver --advertise-address=192.168.49.2 --allow-privileged=true --authorization-mode=Node,RBAC
```

### Method 2
```
$ kubectl get pods -A
$ kubectl get pods -n kube-system  kube-apiserver-minikube -o yaml
...
  - command:
    - kube-apiserver
    - --advertise-address=192.168.49.2
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
```

## Find resources a role give access
[//]: # (source 07/Practice Test Role Based Access Controls)
```
$ kubectl get roles -A
$ kubectl describe role -n kubernetes-dashboard kubernetes-dashboard
Name:         kubernetes-dashboard
PolicyRule:
  Resources       Non-Resource URLs  Resource Names                     Verbs
  ---------       -----------------  --------------                     -----
  secrets         []                 [kubernetes-dashboard-certs]       [get update delete]

```

## Find accounts binded to a role
[//]: # (source 07/Practice Test Role Based Access Controls)
```
$ kubectl get rolebindings -A
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

```

## Inspect permission granted to a user
[//]: # (source 07/Practice Test Role Based Access Controls)
<pre>
<b>$ kubectl config view</b>
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
```
$ kubectl get pods --as user1
Error from server (Forbidden): pods is forbidden: User "user1" cannot list resource "pods" in API group "" in the namespace "default"
```

## create a role with access to create, get and list pods
[//]: # (source 07/Practice Test Role Based Access Controls)

$ kubectl create role developer-role --verb=create,get,list --resource=pods
# Application Environment, Configuration and Security - Understand authentication, authorization and admission control

[Create a user](#create-a-user)

[Create role and bind role](#create-or-modify-a-role-and-rolebinding)

[Create and change context](#create-and-change-context)

[Authorization mode](#identify-authorization-modes)

[Create CluserRole and ClusterBindings](#create-clusterrole-and-clusterrolebinding)

[Admission Contollers](#find-default-admission-controllers-enable-and-disable-some)

---
## Create a user 

### Generate a private key (.key) - not part of CKAD
```
openssl genrsa -out user1.key 2048
```

### Generate a Client Sign Request (.csr)  - not part of CKAD
CSR is a request to a certificate authority (CA) in order to obtain a certificate
```
openssl req -new -key user1.key -out user1.csr -subj “/CN=user1/O=group1”
```
A certificate is the combination of a public key and identity of key issuer:
 - Common Name (CN)
 - Organization (O)

### Generate the certificate (.crt) - not part of CKAD
```
openssl x509 -req -in user1.csr -CA ~/.minikube/ca.crt -CAkey ~/.minikube/ca.key -CAcreateserial 
-out user1.crt -days 100
```

### Create user in default kubeconfig file (.kube/config) or in a different file

```
kubectl config set-credentials user1 --client-certificate=user1.crt --client-key=user1.key 
```

Can use following option to generate user in a specific file
```
--kubeconfig=config-demo 
```


<pre>
more ~/.kube/config 
apiVersion: v1
clusters:
...
contexts:
...
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/lionel/.minikube/profiles/minikube/client.crt
    client-key: /home/lionel/.minikube/profiles/minikube/client.key
<b>- name: user1
  user:
    client-certificate: /media/data/development/CKAD/users/cert/user1.crt
    client-key: /media/data/development/CKAD/users/cert/user1.key</b>
</pre>


---

## Create or Modify a role and roleBinding

### Create a role with access to create, get and list pods
[//]: # (source 07/Practice Test Role Based Access Controls)

```
$ kubectl create role developer-role --verb=create,get,list --resource=pods
```

### Find resources to which a role give access
<pre>
$ <b>kubectl get roles -A</b>
NAME             CREATED AT
developer-role   2022-12-25T08:13:15Z

$ <b>kubectl describe role developer-role</b>
Name:         developer-role
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  pods       []                 []              [create get list]
</pre>


### Bind the role to a user (or serviceAccount) and check operation is allowed


```
$ kubectl create rolebinding developer-role-binding --role developer-role --user user1
```

<pre>
$ <b>kubectl get rolebindings -A</b>
NAME                     ROLE                  AGE
developer-role-binding   Role/developer-role   2m45s

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
$ <b>kubectl get pods --as user-does-not-exist</b>
Error from server (Forbidden): pods is forbidden: User "user1" cannot list resource "pods" ...
$ <b>kubectl get pods --as user1</b>
NAME                     READY   STATUS    RESTARTS      AGE
nginx-75d9bb457d-9jr4t   1/1     Running   1 (24h ago)   5d11h
</pre>



Can also bind to a service account
<pre>
$ kubectl create rolebinding developer-role-binding --role=developer-role <b>--serviceaccount=default:my-service-account</b>
</pre>



### Add authorization to create Deployment to existing Role
[//]: # (source 07/Practice Test Role Based Access Controls)


> **Warning**
> 
> Need to edit apiGroups. 
> Use command kubectl api-resources to know apiGroups (below for deployment)

<pre>
$ <b>kubectl api-resources</b>
NAME           SHORTNAMES   APIVERSION   NAMESPACED   KIND
deployments    deploy       <b>apps/v1</b>      true         Deployment
</pre>

<pre>
$ <b>kubectl edit role developer-role</b>
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: "2022-12-04T16:12:42Z"
  name: developer-role
  namespace: default
  resourceVersion: "42405"
  uid: 36c8f0dd-02b2-4d40-b272-35ade876ae2e
rules:
- <b>apiGroups:
  - "apps"</b>
  resources:
  - deployments
  verbs:
  - list
  - get
  - create
  - watch
</pre>

---

## Create and change context
[//]: # (source 07/Practice Test Role Based Access Controls)
[//]: # (source 07/Practice Test KubeConfig)

### View Config file with User, Clusters and Context (link between Clusters & Users)
Default file is in ~/.kube/config

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
...
- name: user1
...
</pre>

### Create new Context for user1 on cluster minikube

```
kubectl config set-context user1-context --cluster=minikube --user=user1
```

<pre>
$ k config view 
apiVersion: v1
clusters:
- cluster:
  name: minikube
contexts:
...
<b>- context:
    cluster: minikube
    user: user1
  name: user1-context</b>
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
- name: user1
</pre>

### Switch to new create context

```
$ kubectl config use-context user1-context
```

<pre>
kubectl config view 
apiVersion: v1
clusters:
contexts:
- context:
- context:
    cluster: minikube
    user: user1
  name: user1-context
<b>current-context: user1-context</b>
users:
- name: minikube
- name: user1
</pre>


---

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

---

## Create clusterRole and clusterRoleBinding
[//]: # (source 07/Practice Test Cluster Roles)

### Find resources that are cluster scoped (vs. namespace scoped)

<pre>
$ <b>kubectl api-resources --namespaced=false</b>
</pre>


### Create ClusterRole

<pre>
$ <b>kubectl create clusterrole storage-admin --resource=persistentvolumes,storageclasses --verb=* -o yaml</b>
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  creationTimestamp: null
  name: storage-admin
rules:
- apiGroups:
  - ""
  resources:
  - persistentvolumes
  verbs:
  - '*'
- apiGroups:
  - storage.k8s.io
  resources:
  - storageclasses
  verbs:
  - '*'
</pre>

### Create ClusterRoleBinding

<pre>
$ <b>kubectl create clusterrolebinding storage-admin-binding --clusterrole=storage-admin --user=michelle -o yaml</b>
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  creationTimestamp: null
  name: storage-admin-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: storage-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: michelle
</pre>


---

## Find default admission controllers, enable and disable some
[//]: # (source 07/Labs – Admission Controllers)

Default admission controllers are in kube-apiserver help
<pre>
$ <b>kubectl exec -n kube-system kube-apiserver-minikube -- kube-apiserver -h</b>
$ <b>kube-apiserver -h |  grep enable-admission-plugins</b>
--enable-admission-plugins strings  admission plugins that should be enabled in addition to default enabled ones
(NamespaceLifecycle, LimitRanger, ServiceAccount, TaintNodesByCondition, PodSecurity, Priority, 
DefaultTolerationSeconds, DefaultStorageClass, StorageObjectInUseProtection, PersistentVolumeClaimResize, 
RuntimeClass, CertificateApproval, CertificateSigning, CertificateSubjectRestriction, DefaultIngressClass,
MutatingAdmissionWebhook, ValidatingAdmissionWebhook, ResourceQuota)
</pre>

Enable an Admission Controller not enabled by default:
<pre>
$ <b>vi /etc/kubernetes/manifests/kube-apiserver.yaml</b>
   - --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision
</pre>

Disable a Admission Controller enabled by default:
<pre>
$ <b>vi /etc/kubernetes/manifests/kube-apiserver.yaml</b>
   - --disable-admission-plugins=DefaultStorageClass
</pre>

[next](./03-limite-and-quotas.md)
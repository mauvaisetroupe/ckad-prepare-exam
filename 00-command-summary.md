# Create resources with imperative command

## Pod

```
$ kubectl run nginx --image=nginx --labels="app=myapp"
```

### Editing yaml
- multi-container
- initContainer
- livenessProbe
- readinessProbe
- resources limits and requests

## Job

```
$ kubectl create job throw-dice-job --image=throw-dice
$ kubectl create job hello --image=busybox:1.28 -- echo "Hello World"
```

### Editing yaml
 - backoffLimit: 50
 - completions: 3
 - parallelism: 3

## Cronjob

```
$ kubectl create cronjob throw-dice-cron-job --image=throw-dice --schedule="30 21 * * * "
```

## Deployment

```
$ kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3
```

## Taint & toleration

```
$ kubectl taint node node01 spray=frontend:NoSchedule
$ kubectl taint node node01 spray=frontend:NoSchedule- #remove
```

### Editing yaml
- Toleration on pod

## Label

```
$ kubernetes label nodes node01 color=blue
```

### Editing yaml
- affinity on deployment


## Role and RoleBinding, clusterrole and ClusterRoleBinding

```
$ kubectl create role developer-role --verb=create,get,list --resource=pods
$ kubectl create rolebinding developer-role-binding --role=developer-role --serviceaccount=default:my-service-account
$ $ kubectl create clusterrole storage-admin --resource=persistentvolumes,storageclasses --verb=* -o yaml
$ kubectl create clusterrolebinding storage-admin-binding --clusterrole=storage-admin --user=michelle -o yaml   
```

## configMap and Secret

```
$ kubectl create configmap webapp-config-map \
 --from-literal=APP_COLOR=darkblue \
 --from-literal=toto=titi  --dry-run=client -o yaml
$ kubectl create configmap webapp-config-map --from-file=configure-pod-container/configmap/game.properties
$ kubectl create configmap webapp-config-map --from-env-file=configure-pod-container/configmap/game-env-file.properties
```

```
kubectl create secret tls my-server-tls --cert=./my-server-tls.crt --key=./my-server-tls.key -n webhook-demo
```

```
$ kubectl create secret generic db-secret 
    --from-literal="DB_Host=sql01" 
    --from-literal="DB_User=root" 
    --from-literal="DB_Password=password123"
```

## Service

### Expose a pod 

```
$ kubectl run nginx --image=nginx --port=80 --expose
```

```
kubectl expose pod nginx --type=ClusterIP --port=80 --target-port=80 --name=nginx-service
```

### Expose a Deployment via a NodePort service

```
kubectl expose deployment ingress-controller --type=NodePort --port=80 --name=ingress --dry-run=client -o yaml > ingress.yaml 
```
>**Warning** 
>
> Option '-nodePort' doesn't exist, need to generate yaml file and add 'nodePort: 30080' inside
> Option '- namespace my-namespace' is ignored 



## ServiceAccount

```
$ kubectl create serviceaccount dashboard-sa
$ kubectl create token dashboard-sa
```

## Ingress

kubectl create ingress test-ingress -n critical-space \
    --rule="/pay*=pay-service:8282" \
    --annotation="nginx.ingress.kubernetes.io/rewrite-target=/" \
    --annotation="nginx.ingress.kubernetes.io/ssl-redirect=fasle"




---

# Create resources by editing yaml

## CRD

## NetworkingPolicy

## Toleration

---

# Others

```
kubectl logs my-pod -c my-container-in-podm
```


```
$ kubectl rollout history deployment nginx
$ kubectl rollout history deployment nginx --revision=1 # get detail
```

```
$ kubectl rollout undo deployment nginx
$ kubectl rollout undo deployment nginx --to-revision=1
```

```
$ kubectl proxy 8001&
$ curl localhost:8001/apis/authorization.k8s.io
```

```
$ vi /etc/kubernetes/manifests/kube-apiserver.yaml
- --runtime-config=rbac.authorization.k8s.io/v1alpha1
```

```
$ kubectl convert -f ingress-old.yaml --output-version networking.k8s.io/v1 > ingress-new.yaml
```

```
$ git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
$ cd kubernetes-metrics-server/
$ kubectl create -f .
```

```
$ kubectl config view
$ more .kube/config 
```

```
$ ps -ef | grep -i apiserver
$ kubectl get -n kube-system pod kube-apiserver-minikube -o yaml
```

```
$ kubectl get pods --as user1
$ kubectl get pods --as=system:serviceaccount:default:my-service-account
```

```
$ vi /etc/kubernetes/manifests/kube-apiserver.yaml
   - --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision
```

```
$ kubectl exec ubuntu-sleeper -- whoami
```


```
$ kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- 10.105.25.61:80
```
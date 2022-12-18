# Services & Networking - Provide and troubleshoot access to applications via services

[create a service](#create-a-new-service-to-access-the-web-application)

[find info on services](#find-nodeport-service-exposing-deployment)

---
## Create a pod and expose it in one command. Check pod is reachable via it's CluserIP

<pre>
$ kubectl run nginx --image=nginx --port=80 <b>--expose</b>
</pre>

**A ClusterIP has been automatically created**

Check Cluster IP
<pre>
$ kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
nginx-svc    ClusterIP   <b>10.105.25.61</b>   <none>        80/TCP    10m
</pre>

Check Endpoints
```
$ kubectl get endpoints
NAME         ENDPOINTS           AGE
nginx        172.17.0.3:80       25s
```

**Use wget to hit the pod via it's clusterIP**

With prompt:
<pre>
$ kubectl run busybox --image=busybox --rm -it <b>--restart=Never</b> -- sh 
/ # wget -O- <b>10.105.25.61:80</b>
/ # wget -O- <b>nginx-svc:80</b>
</pre>

Inline (note that node service name is a valid DNS):
<pre>
$ kubectl run busybox --image=busybox --rm -it <b>--restart=Never</b> -- wget -O- <b>10.105.25.61:80</b>
$ kubectl run busybox --image=busybox --rm -it <b>--restart=Never</b> -- wget -O- <b>nginx-svc:80</b>
</pre>

## Expose a POD via a ClusterIP service
[//]: # (source 01 / Imperative Commands)

```
$ kubectl expose pod redis <b>--name=redis-service</b> --port=6379
```

[//]: # (source 05 / Ingress Networking – 1)
```
kubectl expose pod nginx --type=ClusterIP --port=80 --target-port=80 --name=nginx-service
```


## Expose a Deployment via a NodePort service
[//]: # (source 05 / Ingress Networking – 1)

```
kubectl expose deployment ingress-controller --type=NodePort --port=80 --name=ingress --dry-run=client -o yaml > ingress.yaml 
```
>**Warning** 
>
> Option '-nodePort' doesn't exist, need to generate yaml file and add 'nodePort: 30080' inside
> Option '- namespace my-namespace' is ignored 


```
apiVersion: v1
kind: Service
metadata:
  namespace: ingress-space
  name: ingress
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    name: nginx-ingress
  type: NodePort
```                     


## Create a new service to access the web application 
[//]: # (source 05 / Kubernetes Services)

```
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 8080
      port: 8080
      nodePort: 30080
  selector:
    name: simple-webapp
```

---

## Find NodePort service exposing deployment

**Check that service selector match deployment label**

<pre>
$ <b>kubernetes get deployments.apps --show-labels </b>
NAME              READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
mydeployment      5/5     5            5           3m29s   <b>app=myapp,tier=frontend</b>
mydeployment-v2   2/2     2            2           105s    <b>app=myapp</b>
</pre> 


<pre>
$ <b>kubernetes get service -o wide</b>
NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE     SELECTOR
mydeployment       NodePort    10.97.165.71   <none>        8080:30080/TCP   2m29s   <b>tier=frontend</b>
</pre>

## What is the DNS for s service in a namespace
[//]: # (source 01 / Namespaces)

```
k get services -A
NAMESPACE       NAME              TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
dev-namespace   db-service        ClusterIP      10.43.54.161    <none>        6379/TCP                     16m
```

```
db-service.dev-namespace.svc.cluster.local
```

[next](./03-use-ingress-rules-to-expose-applications.md)
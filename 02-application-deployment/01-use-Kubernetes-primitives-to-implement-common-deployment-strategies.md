
# Application Deployment - Use Kubernetes primitives to implement common deployment strategies 
[//]: # (source 07/Lab – Practice Test – Deployment strategies)

## Find deployment strategy used for a deployment

<pre>
$ <b>kubernetes get deployments.apps --show-labels </b>
NAME              READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
mydeployment      5/5     5            5           3m29s   <b>app=myapp,tier=frontend</b>
mydeployment-v2   2/2     2            2           105s    <b>app=myapp</b>
</pre> 

<pre>
$ <b>kubernetes describe deployments mydeployment</b>
Name:                   frontend
Namespace:              default
Labels:                 app=myapp
                        tier=frontend
StrategyType:           RollingUpdate
</pre>


## Find NodePort service exposing deployment

<pre>
$ <b>kubernetes get service -o wide</b>
NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE     SELECTOR
mydeployment       NodePort    10.97.165.71   <none>        8080:30080/TCP   2m29s   app=frontend
</pre>


## decrease replicas of a deployment

<pre>
$ kubectl scale deployment mydeployment-v2 --replicas=1
</pre>

## change label selector in deployment

<pre>
$ <b>kubectl edit service frontend-service </b>
apiVersion: v1
kind: Service
...
spec:
...
  selector:
    app: myapp
</pre>

## Find pods with specific label
[//]: # (source 04/Label and Selectors)

```
kubectl get pods --show-labels 
```

```
kubectl get pods --selector=foo=bar 
```

```
kubectl get pods --selector="env=prod,bu=finance,tier=frontend" --show-labels 
NAME          READY   STATUS    RESTARTS   AGE   LABELS
app-1-zzxdf   1/1     Running   0          11m   bu=finance,env=prod,tier=frontend
```
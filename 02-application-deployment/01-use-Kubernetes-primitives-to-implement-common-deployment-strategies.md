
# Application Deployment - Use Kubernetes primitives to implement common deployment strategies 
[//]: # (source 07/Lab – Practice Test – Deployment strategies)

[Find info](#find-deployment-strategy-used-for-a-deployment)

[Implement Canary or blue/green](#implement-canary-or-bluegreen)

---
## Find deployment strategy used for a deployment

<pre>
$ <b>kubernetes describe deployments mydeployment</b>
Name:                   frontend
Namespace:              default
Labels:                 app=myapp
                        tier=frontend
<b>StrategyType:           RollingUpdate</b>
</pre>


## Find NodePort service exposing deployment

See [here](../05-services-and-networking/02-troubleshoot-access-to-applications-via-services.md#find-nodeport-service-exposing-deployment)

## Find pods with specific label
[//]: # (source 04/Label and Selectors)

```
kubectl get pods --show-labels 
```

```
kubectl get pods --selector env=prod,bu=finance,tier=frontend --show-labels 
NAME          READY   STATUS    RESTARTS   AGE   LABELS
app-1-zzxdf   1/1     Running   0          11m   bu=finance,env=prod,tier=frontend
```

## Find service with specific selector

>**Warning**
>
> --selector is used to find services with specific label, not filtering on selector
> 

```
kubectl get svc --selector bu=finance --show-labels -o wide
NAME    TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE     SELECTOR     LABELS
app-1   ClusterIP   10.43.90.86   <none>        3306/TCP   7m43s   name=app-2   bu=finance,env=prod
```

---

## Implement Canary or Blue/Green

### decrease replicas of a deployment

<pre>
$ kubectl scale deployment mydeployment-v2 --replicas=1
</pre>

### change label selector in service to expose another deployment

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

[next](./02-deployments-perform-rolling-updates.md)
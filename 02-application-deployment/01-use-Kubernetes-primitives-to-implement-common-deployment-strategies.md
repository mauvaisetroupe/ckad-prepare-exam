
# Application Deployment - Use Kubernetes primitives to implement common deployment strategies 
[//]: # (source 07/Lab – Practice Test – Deployment strategies)

[Find info](#find-deployment-strategy-used-for-a-deployment)

[Implement Blue/Green](#implement-bluegreen)

[Implement Canary](#implement-canary)

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

## Implement Blue/Green

### Scenario

1. Initial 
    - Deployment-01 with label app=my-application, version=v1
    - ClusterIP with selector app=my-application, version=v1
2. Create Deployment-02 with label app=my-application, version=v2
3. Modify ClusterIP selector app=my-application, version=v2
4. Scale Deployment-01 to replicas=0

### How To

#### Change ClusterIP selector in service to expose another deployment

<pre>
$ <b>kubectl edit service frontend-service </b>
apiVersion: v1
kind: Service
...
spec:
...
  selector:
    matchLabels:
      app: myapp

</pre>

#### Decrease replicas of a deployment

<pre>
$ kubectl scale deployment mydeployment-v2 --replicas=0
</pre>

---
## Implement Canary

### Scenario
1. Initial 
    - Deployment-01 with label app=my-application and image=httpd, 10 replicas
    - ClusterIP with selector app=my-application
2. Create Deployment-02 with label app=my-application, image=nginx, 2 replicas
4. Scale Deployment-01 to replicas=8


>**Warning**
>
>Version 1 and version 2 can have the same selector.
>
>The pod-template-hash label is added by the Deployment controller to every ReplicaSet that a Deployment creates or adopts.
>This label ensures that child ReplicaSets of a Deployment do not overlap. 

### How To

#### Decrease replicas of a deployment

<pre>
$ kubectl scale deployment mydeployment-v1 --replicas=8
</pre>

[next](./02-deployments-perform-rolling-updates.md)
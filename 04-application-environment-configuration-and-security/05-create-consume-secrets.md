# Application Environment, Configuration and Security - Create & consume Secrets

## Create TLS secret my-server-tls for secure webhook communication
[//]: # (source 07/Labs – Validating and Mutating Admission Controllers)

```
kubectl create secret tls my-server-tls --cert=./my-server-tls.crt --key=./my-server-tls.key -n webhook-demo
```
## create secrte for database password and URL
[//]: # (source 02 / Secrets)

<pre>
$ kubectl create secret <b>generic</b> db-secret 
    --from-literal="DB_Host=sql01" 
    --from-literal="DB_User=root" 
    --from-literal="DB_Password=password123"
</pre>

## use all keys from a secret in a pod

```
$ kubectl explain pod.spec.containers.env.valueFrom
$ kubectl explain pod.spec.containers.envFrom
```

> **Warning**
> 
> secretRef and not secretMapRef 

<pre>
apiVersion: v1 
kind: Pod 
spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    env:
      - name: SECRET_USERNAME
        <b>valueFrom</b>:
          secretKeyRef:
            name: mysecret
            key: username    
    <b>envFrom</b>:
    - secretRef:
        name: db-secret
</pre>

[next](./06-understand-serviceaccounts.md)
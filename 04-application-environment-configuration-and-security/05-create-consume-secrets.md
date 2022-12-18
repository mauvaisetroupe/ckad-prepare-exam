# Application Environment, Configuration and Security - Create & consume Secrets

## Create TLS secret my-server-tls for secure webhook communication
[//]: # (source 07/Labs – Validating and Mutating Admission Controllers)

```
kubectl create secret tls my-server-tls --cert=./my-server-tls.crt --key=./my-server-tls.key -n webhook-demo
```
## create secret for database password and URL
[//]: # (source 02 / Secrets)

<pre>
$ kubectl create secret <b>generic</b> db-secret 
    --from-literal="DB_Host=sql01" 
    --from-literal="DB_User=root" 
    --from-literal="DB_Password=password123"
</pre>

## Use secret to inject env variables in POD

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

## Comparison between ConfigMap and Secret

<pre>
  env:
    - name: SECRET_USERNAME
      valueFrom:
        configMapKeyRef / <b>secretKeyRef</b>:
          name: my-name
          key: my-key
</pre>

<pre>
  envFrom:
    - configMapRef / <b>secretRef</b>:
        name: my-name 
</pre>


>**Warning**
>
> For volumes, 'name' attribute for configMap, but 'secretName' for Secret 
> 

<pre>
  volumes:
    - name: config-volume
      configMap / <b>secret</b>:
        name / <b>secretName</b>: my-name
</pre>

## Create secret with a file content as value and file name as key (useful to be mounted as volume)

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-configmap
spec:
  containers:
  - image: nginx
    name: pod-with-configmap
    resources: {}
    volumeMounts:
      - mountPath: /my-config-from-litteral
        name: my-config-from-litteral-volume
      - mountPath: /my-config-from-file
        name: my-config-from-file-volume
      - mountPath: /my-config-from-env-file
        name: my-config-from-env-file-volume
      - mountPath: /my-secret-from-litteral
        name: my-secret-from-litteral-volume
      - mountPath: /my-secret-from-file
        name: my-secret-from-file-volume
      - mountPath: /my-secret-from-env-file
        name: my-secret-from-env-file-volume
  volumes:
  - name: my-config-from-litteral-volume
    configMap:
      name: my-config-from-litteral
  - name: my-config-from-file-volume
    configMap:
      name: my-config-from-file
  - name: my-config-from-env-file-volume
    configMap:
      name: my-config-from-env-file
  - name: my-secret-from-litteral-volume
    secret:
      secretName: my-secret-from-litteral
  - name: my-secret-from-file-volume
    secret:
      secretName: my-secret-from-file
  - name: my-secret-from-env-file-volume
    secret:
      secretName: my-secret-from-env-file
```


```
$ kubectl create secret generic my-secret-from-file --from-file ./my-config.propertie
```

```
$ kubectl describe secrets my-secret-from-file

Data
====
my-config.propertie:  24 bytes
```

```
$ kubectl exec pod-with-configmap -it -- /bin/bash
root@pod-with-configmap:/# ls -l
drwxrwxrwt   3 root root  120 Dec 11 08:12 my-secret-from-env-file
drwxrwxrwt   3 root root  100 Dec 11 08:12 my-secret-from-file
drwxrwxrwt   3 root root  120 Dec 11 08:12 my-secret-from-litteral
```

```
root@pod-with-configmap:/# more my-secret-from-file/my-config.propertie 
key3=value3
key4=value4
```

```
root@pod-with-configmap:/# more my-secret-from-litteral/key1
value1
```

```
root@pod-with-configmap:/# more my-secret-from-env-file/key3
value3
```

## Get the value of mysecret

```
kubectl get secret mysecret -o yaml
echo -n YWXeaW4= | base64 -d 
```




[next](./06-understand-serviceaccounts.md)
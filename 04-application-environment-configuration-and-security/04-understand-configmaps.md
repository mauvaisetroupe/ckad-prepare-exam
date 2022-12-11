# Application Environment, Configuration and Security - Understand ConfigMaps

[configMap to inject env variable](#create-map-from-literal-in-command-line)

[configMap to be mounted as config file](#create-map-with-a-file-content-as-value-and-file-name-as-key-useful-to-be-mounted-as-volume)
---

## create map from literal in command line

<pre>
$ kubectl create cm my-config-from-litteral --from-literal="key1=value1" --from-literal="key2=value2"
</pre>

<pre>
$ kubectl describe cm my-config-from-litteral 

Data
====
key1:
----
value1
key2:
----
value2
</pre>

## create map with a file containing key, values

<pre>
$ kubectl create cm my-config-from-env-file --from-env-file "./my-config.propertie"
</pre>

<pre>
$ kubectl describe cm my-config-from-env-file 

Data
====
key3:
----
value3
key4:
----
value4
</pre>


## Use configMap to inject env variables in POD

```
$ kubectl explain pods.spec.containers.env.valueFrom
$ kubectl explain pods.spec.containers.envFrom
```

<pre>
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: test-container
      env:
        - name: DEMO_GREETING
          value: "Hello from the environment"
        - name: SPECIAL_LEVEL_KEY
          <b>valueFrom:</b>
            configMapKeyRef:
              name: special-config
              key: special.how
      <b>envFrom:</b>
        - secretRef:
            name: app-secret
        - confiMapRef:
            name: app-config              
</pre>


---

## create map with a file content as value and file name as key (useful to be mounted as volume)

<pre>
$ kubectl create cm my-config-from-file --from-file "./my-config.propertie"
</pre>

<pre>
$ kubectl describe cm my-config-from-file 

Data
====
my-config.propertie:
----
key3=value3
key4=value4
</pre>


## use configMAp in a POD mounted as file
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
```



```
$ kubectl exec pod-with-configmap -it -- /bin/bash
root@pod-with-configmap:/# ls -l
total 92
drwxrwxrwx   3 root root 4096 Dec 11 07:15 my-config-from-env-file
drwxrwxrwx   3 root root 4096 Dec 11 07:15 my-config-from-file
drwxrwxrwx   3 root root 4096 Dec 11 07:15 my-config-from-litteral
root@pod-with-configmap:/# 


root@pod-with-configmap:/# ls -l my-config-from-*

my-config-from-env-file:
lrwxrwxrwx 1 root root 11 Dec 11 07:15 key3 -> ..data/key3
lrwxrwxrwx 1 root root 11 Dec 11 07:15 key4 -> ..data/key4

my-config-from-file:
lrwxrwxrwx 1 root root 26 Dec 11 07:15 my-config.propertie -> ..data/my-config.propertie

my-config-from-litteral:
lrwxrwxrwx 1 root root 11 Dec 11 07:15 key1 -> ..data/key1
lrwxrwxrwx 1 root root 11 Dec 11 07:15 key2 -> ..data/key2


root@pod-with-configmap:/# more my-config-from-litteral/key1
value1

root@pod-with-configmap:/# more my-config-from-env-file/key3
value3

root@pod-with-configmap:/# more my-config-from-file/my-config.propertie 
key3=value3
key4=value4


```







[next](./05-create-consume-secrets.md)

## Configure a volume to store logs at /var/log/webapp on the host

<pre>
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: xxx
    volumeMounts:
    - mountPath: /log
      name: <b>log-volume</b>
  volumes:
  - name: <b>log-volume</b>
    hostPath:
      path: /var/log/webapp
</pre>


## Create a PersistentVolume, PersistemtVolumeClaim, use in POD

>**Information**
>
>Search 'pvc' in kubernetes dcumentation
>
>Kubernetes › docs › tasks › configure-persistent-volume-storage

### Persistent Volume (namespaced=false)

<pre>
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  persistentVolumeReclaimPolicy: Retain
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 100Mi
  hostPath:
    path: /pv/log
</pre>

### PersistemtVolumeClaim

<pre>
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  resources:
    requests:
      storage: 50Mi
  accessModes:
    - ReadWriteOnce
</pre>                        

### Change Pod to use PVC instead of declaring volume inside POD

<pre>
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: xxx
    volumeMounts:
    - mountPath: /log
      name: log-volume
  volumes:
  - name: log-volume
    persistentVolumeClaim:
      claimName: claim-log-1
</pre>


[next](../02-application-deployment/01-use-Kubernetes-primitives-to-implement-common-deployment-strategies.md)
# Application Environment, Configuration and Security - Understand ConfigMaps

## create map from literal in command line

<pre>
$ <b>kubectl create configmap webapp-config-map \
 --from-literal=APP_COLOR=darkblue \
 --from-literal=toto=titi  --dry-run=client -o yaml</b>
apiVersion: v1
data:
  APP_COLOR: darkblue
  toto: titi
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: webapp-config-map
</pre>

## create map with a file content as un unique value

<pre>
$ kubectl create configmap webapp-config-map <b>--from-file=configure-pod-container/configmap/game.properties</b>
</pre>

## create map with a file containing key, values

<pre>
$ kubectl create configmap webapp-config-map <b>--from-env-file=configure-pod-container/configmap/game-env-file.properties</b>
</pre>

## use a value of configMap in a POD

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
      image: registry.k8s.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
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

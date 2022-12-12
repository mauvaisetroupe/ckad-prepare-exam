


q5

Create a redis deployment with the following parameters:
Name of the deployment should be redis using the redis:alpine image. It should have exactly 1 replica.
The container should request for .2 CPU. It should use the label app=redis.
It should mount exactly 2 volumes.

a. An Empty directory volume called data at path /redis-master-data.
b. A configmap volume called redis-config at path /redis-master.
c. The container should expose the port 6379.
The configmap has already been created.


controlplane ~ ➜  k create deploy redis --image=redis:alpine --replicas=1 --port=6379  --dry-run=client -o yaml > redi-deploy.yaml  

controlplane ~ ➜  more redi-deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  strategy: {}
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - image: redis:alpine
        name: redis
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "0.2"
        volumeMounts:
          - name: data
            mountPath: /redis-master-data
          - name: redis-config  
            mountPath: /redis-master
      volumes:
        - name: data
          emptyDir: {}
        - name: redis-config
          configMap:
            name: redis-config
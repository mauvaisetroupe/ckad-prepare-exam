# Application observability and maintenance - Use provided tools to monitor Kubernetes applications

## Install Metrics Server
[//]: # (source 03 / Monitoring)

```
$ git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
$ cd kubernetes-metrics-server/
$ kubectl create -f .
```


```
$ kubectl top node
NAME       CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
minikube   535m         6%     1371Mi          8%        
```

```
$  kubectl top pods  --sort-by=memory
NAME         CPU(cores)   MEMORY(bytes)   
shell-demo   0m           23Mi            
nginx        0m           7Mi             
```

[next](./04-utilize-container-log.md)
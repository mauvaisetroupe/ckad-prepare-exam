# Services & Networking - Use Ingress rules to expose applications Ingress rules to expose applications

## Which namespace is the Ingress Resource deployed in?
[//]: # (source 05 / Ingress Networking – 1)

>**Warning**
>
> 'kubectl get all -A' do not return ingress resources

```
$ kubernetes get ingress -A
NAMESPACE   NAME                 CLASS    HOSTS   ADDRESS        PORTS   AGE
app-space   ingress-wear-watch   <none>   *       10.102.81.66   80      4m42s
```

```
$kubectl get ingress ingress-wear-watch -o yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wear-watch
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port:
              number: 8080
        path: /wear
        pathType: Prefix
      - backend:
          service:
            name: video-service
            port:
              number: 8080
        path: /watch
        pathType: Prefix
```

## create an ingress

```
kubectl create ingress test-ingress -n critical-space \
    --rule="/pay*=pay-service:8282" \
    --annotation="nginx.ingress.kubernetes.io/rewrite-target=/" \
    --annotation="nginx.ingress.kubernetes.io/ssl-redirect=fasle"
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: fasle
  name: test-ingress
  namespace: critical-space
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: pay-service
            port:
              number: 8282
        path: /pay
        pathType: Prefix

```
# Services & Networking - Demonstrate basic understanding of NetworkPolicies

(source 06 / network policy)
## Which pod is the Network Policy applied on?

```
kubernetes describe networkpolicies.networking.k8s.io payroll-policy 
Name:         payroll-policy
Spec:
  PodSelector:     name=payroll

$ kubernetes get pods -o wide
NAME       READY   STATUS    RESTARTS   AGE     IP          NODE           NOMINATED NODE   READINESS GATES
payroll    1/1     Running   0          2m36s   10.50.0.5   controlplane   <none>           <none>
```
## Which traffic is allowed

```
$ kubernetes describe networkpolicies.networking.k8s.io payroll-policy 
Name:         payroll-policy
Spec:
  Allowing ingress traffic:
    To Port: 8080/TCP
    From:
      PodSelector: name=internal
  Not affecting egress traffic
```
## Create a network policy 

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
        - podSelector:
            matchLabels:
              role: db
      ports:
        - protocol: TCP
          port: 5978
```
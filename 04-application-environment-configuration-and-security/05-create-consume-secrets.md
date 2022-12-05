# Application Environment, Configuration and Security - Create & consume Secrets

## Create TLS secret webhook-server-tls for secure webhook communication
[//]: # (source 07/Labs – Validating and Mutating Admission Controllers)

```
kubectl create secret tls webhook-server-tls --cert=/root/keys/webhook-server-tls.crt --key=/root/keys/webhook-server-tls.key -n webhook-demo
```


# Application Environment, Configuration and Security - Understand ServiceAccounts

## Identify ServiceAccount mounted in a pod
[//]: # (source 02/Service Account)
<pre>
$ <b>kubectl get pod web-dashboard-68f98dc77c-22nbn -o yaml</b>
apiVersion: v1
kind: Pod
metadata:
  serviceAccount: default
  serviceAccountName: default
</pre>

## Create a ServiceAccount, and create a token for this sa
[//]: # (source 02/Service Account)
<pre>
$ <b>kubectl create serviceaccount dashboard-sa</b>
</pre>

[//]: # (source 02/Service Account)
<pre>
$ <b>kubectl create token dashboard-sa</b>
eyJhbGciOiJSUzI1NiIsImtpZCI6ImxlenRpQ3JGNVQ0Qjg2YnlNS1FVUGpsS0t3cDdXd3NiUHhlTzkzaHNPSncifQ....
</pre>

## Edit the deployment to change ServiceAccount from default to dashboard-sa
[//]: # (source 02/Service Account)

### Methode 1

edit value in yaml @ spec > template > spec > serviceAccountName

<pre>
$ <b>kubectl edit deployments web-dashboard</b>
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-dashboard
spec:
  template:
    spec:
      <b>serviceAccountName: dashboard-sa</b>
      conatiner:
</pre>

### Method 2
<pre>
$ <b>kubectl set serviceaccount deployment web-dashboard dashboard-sa</b>
</pre>


[next](./07-securitycontexts.md)
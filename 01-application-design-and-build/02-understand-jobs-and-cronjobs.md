# Application Design and Build - Understand Jobs and CronJobs

[//]: # (source 04/Jobs and CronJobs)

## Create a Job which prints "Hello World"

>**Warning**
>
>When command includes a sleep, job creation is waiting end of sleep
>
>Prefer /bin/sh -c

```
kubectl create job busybox --image busybox -- echo hello;sleep 30;echo world
kubectl create job busybox --image busybox -- /bin/sh -c "echo hello;sleep 30;echo world"
```


## List jobs and associated Pods

```
$ kubectl get jobs throw-dice-job 
NAME             COMPLETIONS   DURATION   AGE
throw-dice-job   1/3           60s        60s
```

```
$ kubectl get pods
NAME                   READY   STATUS      RESTARTS   AGE
throw-dice-job-4f86x   0/1     Completed   0          24s
throw-dice-job-b2mj2   0/1     Error       0          106s
throw-dice-job-gc564   0/1     Error       0          110s
throw-dice-job-kns7t   0/1     Completed   0          19s
throw-dice-job-pqpnw   0/1     Error       0          92s
throw-dice-job-r6jxz   0/1     Error       0          68s
throw-dice-job-rq8zw   0/1     Completed   0          115s
```

## change completions, backoffLimit, parallelism, activeDeadlineSeconds

```
$ kubectl explain jobs.spec.completions
$ kubectl explain jobs.spec.parallelism
$ kubectl explain jobs.spec.activeDeadlineSeconds
```

<pre>
$ <b>kubectl create job throw-dice-job --image=throw-dice --dry-run=client -o yaml > job.yaml</b>
$ vi job.yaml
apiVersion: batch/v1
kind: Job
spec:
  template:
    metadata:
    spec:
      containers:
      - image: throw-dice
        name: throw-dice-job
      restartPolicy: Never
  <b>backoffLimit: 50
  completions: 3
  parallelism: 3
  activeDeadlineSeconds: 20</b>
</pre>

## How many attempts did it take to complete the job this time?

```
$ kubectl describe jobs.batch throw-dice-job 
Pods Statuses:    0 Active (0 Ready) / 3 Succeeded / 4 Failed
```

## Create Cronjob - Schedule that job to run at 21:30 hours every day

```
$ kubectl create cronjob throw-dice-cron-job --image=throw-dice --schedule="30 21 * * *"
```

Modify editing YAML:
```
$ kubectl explain cronjob.spec.startingDeadlineSeconds
```

```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: throw-dice-cron-job 
spec:
  schedule: "30 21 * * *"
  jobTemplate:
    spec:
      startingDeadlineSeconds: 50 # should be > 10s
      template:
      # see job yaml file
```

[next](./03-multi-container-pod-design.md)


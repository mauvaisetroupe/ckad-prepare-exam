## Define, build and modify container images

### Build container image from Dockerfile

Example of Dockefile:

```dockerfile
FROM python:3.8-alpine
COPY ./requirements.txt /app/requirements.txt
WORKDIR /app
RUN pip install -r requirements.txt
COPY . /app
ENTRYPOINT [ "python" ]
```

In the **folder** where Dockerfile is located, run the following command

```
$ docker build -t my-image-name .
$ docker build -t my-image-name:v1 .
```


### Push imaage to repository


## Understand Jobs and CronJobs

## Understand multi-container Pod design patterns (e.g. sidecar, init and others)

## Utilize persistent and ephemeral volumes
# Define, build and modify container images

## Docker Image
### List docker images
```
$ docker image ls
$ docker images ## equivalent
```

### Build container image from Dockerfile

Example of Dockerfile:

```dockerfile
FROM python:3.6
RUN pip install flask
COPY . /opt/
EXPOSE 8080
WORKDIR /opt
ENTRYPOINT ["python", "app.py"]
```

In the **folder** where Dockerfile is located, run the following command

```
$ docker build -t my-image-name .
$ docker build -t my-image-name:v1 .
```

### Run a instance of the image

Run an instance of the image my-image-name and publish port 8080 on the container to 9090 on the host.
```
$ docker run -p 9090:8080 my-image-name
```

### Push image to repository
TODO

# Understand Jobs and CronJobs

# Understand multi-container Pod design patterns (e.g. sidecar, init and others)

# Utilize persistent and ephemeral volumes
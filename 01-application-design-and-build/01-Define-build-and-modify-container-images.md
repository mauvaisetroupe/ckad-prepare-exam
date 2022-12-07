# Application Design and Build - Define, build and modify container images

[Pods](#list-and-create-pod-with-specific-label)

[Docker](#docker-image)

[Entrypoint and command](#entrypoint-and-command)

---
## List and Create pod, with specific label
[//]: # (source 01 / PODS)
```
kubectl get pods -A
kubectl run nginx --image=nginx --labels="app=myapp"
```

## Get pods image, Node deployed, ...
```
kubectl describe pod mypod-tbtb2
```

---

## Docker Image
[//]: # (source 07 / Practice test Docker Images)

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

---

## Entrypoint and command


| Dockerfile | Pod.yaml | Result |
 -- |--|--
| ENTRYPOINT ["python", "app.py"] | command: ["--color","green"] | --color green |
|CMD ["--color", "red"] |||


| Dockerfile | Pod.yaml | Result |
 -- |--|--
| ENTRYPOINT ["python", "app.py"] | command: ["python", "app.py"] | python app.py --color pink|
|CMD ["--color", "red"] | args: ["--color", "pink"] | |

| Dockerfile | Pod.yaml | Result |
 -- |--|--
| ENTRYPOINT ["python", "app.py"] |  | python app.py --color pink|
|CMD ["--color", "red"] | args: ["--color", "pink"] | |



# Docker Registry playground

The stack:

- [registry](https://github.com/docker/distribution)
- [minio](https://github.com/minio/)

```
$ docker-compose up -d
```

```
$ docker pull alpine:latest
$ docker tag alpine:latest 127.0.0.1:5000/alpine:latest
$ docker push 127.0.0.1:5000/alpine
```
# Docker Registry playground

The stack:

- [registry](https://github.com/docker/distribution)
- [minio](https://github.com/minio/)

```
$ docker-compose up -d
```

```
$ docker pull alpine:latest
$ docker tag alpine:latest localhost:5000/alpine:latest
$ docker push localhost:5000/alpine
```
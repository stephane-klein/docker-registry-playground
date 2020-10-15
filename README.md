# Docker Registry playground

The stack:

- [registry](https://github.com/docker/distribution)
- [minio](https://github.com/minio/)

```
$ docker-compose up -d
```

Push `alpine` Docker Image to repository:

```
$ docker pull ubuntu:latest
$ docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
$ docker push 127.0.0.1:5000/ubuntu:latest
```

```
$ du volumes -h -d0
31M	volumes
```

List registry images:

```
$ docker run --network docker-registry-playground --rm anoxis/registry-cli -r http://registry:5000
---------------------------------
Image: ubuntu
  tag: latest
```

Delete `127.0.0.1:5000/ubuntu:latest` image:

```
$ docker run --network docker-registry-playground --rm anoxis/registry-cli -r http://registry:5000 -i ubuntu --delete-all
```

```
$ docker run --network docker-registry-playground --rm anoxis/registry-cli -r http://registry:5000
---------------------------------
Image: ubuntu
  no tags!
```

```
$ du volumes -h -d0
31M	volumes
```

```
$ ./execute-garbase-collector.py
```

```
$ du volumes -h -d0
2,9M	volumes
```

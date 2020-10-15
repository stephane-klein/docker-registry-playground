# Docker Registry playground

The stack:

- [registry](https://github.com/docker/distribution)
- [minio](https://github.com/minio/)
- [registry-cli](https://github.com/andrey-pohilko/registry-cli)

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

## Test « How can we delete one manifest by tag » [issue](https://github.com/docker/distribution/issues/1566)

```
$ docker build . -f Dockerfile1 -t "127.0.0.1:5000/myimage:tag1"
```

```
$ docker build . -f Dockerfile2 -t "127.0.0.1:5000/myimage:tag2"
```

```
$ docker build . -f Dockerfile1 -t "127.0.0.1:5000/myimage:tag3"
```

```
$ docker history 127.0.0.1:5000/myimage:tag1
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
abae6647b8d0        5 minutes ago       /bin/sh -c #(nop) ADD ea4304b8f317fc0549a414…   110MB
9140108b62dc        2 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           2 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           2 weeks ago         /bin/sh -c [ -z "$(apt-get indextargets)" ]     0B
<missing>           2 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   811B
<missing>           2 weeks ago         /bin/sh -c #(nop) ADD file:da80f59399481ffc3…   72.9MB
```

```
$ docker history 127.0.0.1:5000/myimage:tag2
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
2ee7b319b0dc        2 minutes ago       /bin/sh -c #(nop) ADD 8674f8a421ec2e19043a42…   109MB
abae6647b8d0        5 minutes ago       /bin/sh -c #(nop) ADD ea4304b8f317fc0549a414…   110MB
9140108b62dc        2 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           2 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           2 weeks ago         /bin/sh -c [ -z "$(apt-get indextargets)" ]     0B
<missing>           2 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   811B
<missing>           2 weeks ago         /bin/sh -c #(nop) ADD file:da80f59399481ffc3…   72.9MB
```

```
$ docker push 127.0.0.1:5000/myimage:tag1
$ docker push 127.0.0.1:5000/myimage:tag2
$ docker push 127.0.0.1:5000/myimage:tag3
```

```
$ docker run --network docker-registry-playground --rm anoxis/registry-cli -r http://registry:5000 --layers
---------------------------------
Image: alpine
  tag: latest
    layer: sha256:df20fa9351a15782c64e6dddb2d4a6f50bf6d3688060a34c4014b0d9a752eb4c, size: 2797541
---------------------------------
Image: myimage
  tag: tag1
    layer: sha256:d72e567cc804d0b637182ba23f8b9ffe101e753a39bf52cd4db6b89eb089f13b, size: 28558050
    layer: sha256:0f3630e5ff08d73b6ec0e22736a5c8d2d666e7b568c16f6a4ffadf8c21b9b1ad, size: 848
    layer: sha256:b6a83d81d1f4f942d37e1f17195d9c519969ed3040fc3e444740b884e44dec33, size: 162
    layer: sha256:ed7525632079d5dbd650a45680db4ed05e1829eb71c4cc959f554e7b78bfa7de, size: 109613890
  tag: tag2
    layer: sha256:d72e567cc804d0b637182ba23f8b9ffe101e753a39bf52cd4db6b89eb089f13b, size: 28558050
    layer: sha256:0f3630e5ff08d73b6ec0e22736a5c8d2d666e7b568c16f6a4ffadf8c21b9b1ad, size: 848
    layer: sha256:b6a83d81d1f4f942d37e1f17195d9c519969ed3040fc3e444740b884e44dec33, size: 162
    layer: sha256:ed7525632079d5dbd650a45680db4ed05e1829eb71c4cc959f554e7b78bfa7de, size: 109613890
    layer: sha256:5502033c4e43af91e47582acfee52dc0e4902d5365a4b31659c006f736765407, size: 109490178
  tag: tag3
    layer: sha256:d72e567cc804d0b637182ba23f8b9ffe101e753a39bf52cd4db6b89eb089f13b, size: 28558050
    layer: sha256:0f3630e5ff08d73b6ec0e22736a5c8d2d666e7b568c16f6a4ffadf8c21b9b1ad, size: 848
    layer: sha256:b6a83d81d1f4f942d37e1f17195d9c519969ed3040fc3e444740b884e44dec33, size: 162
    layer: sha256:ed7525632079d5dbd650a45680db4ed05e1829eb71c4cc959f554e7b78bfa7de, size: 109613890
---------------------------------
Image: ubuntu
  no tags!
```


```
$ docker run --network docker-registry-playground --rm anoxis/registry-cli -r http://registry:5000 -i myimage --delete --tags-like "tag2"
$ ./execute-garbase-collector.py
```

```
$ du ./volumes -h -d0
237M	./volumes
```


```
$ docker build -f Dockerfile.scratch -t 127.0.0.1:5000/myimage:tag2 --build-arg tag="myimage:tag2"
$ docker push 127.0.0.1:5000/myimage:tag2
$ ./execute-garbase-collector.py
```

```
$ du ./volumes -h -d0
237M	./volumes
```

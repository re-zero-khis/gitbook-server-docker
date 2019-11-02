# gitbook-server-docker
使用 Docker 构建的 GitBook 服务器


构建 Docker 镜像

`docker build . -t exp/gitbook-server:latest`

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook init`

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook build`

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook serve`


`docker run --rm -v "$PWD/gitbook:/gitbook" -it exp/gitbook-server /bin/sh`


`docker login`

`docker push exp/gitbook-server:latest`


`docker tag fdc060ba7253 expm02/gitbook-server:latest`


`docker pull expm02/gitbook-server:latest`
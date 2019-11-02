# GitBook-Server-Docker

> 使用 Docker 构建的 GitBook 服务器

------

## 简介

使用 [GitBook](http://app.gitbook.com/) 的初衷主要是为了可以利用 Github 为数据载体，在线上和线下同时搭建个人博客。

但是在线下搭建 GitBook 服务器最大的问题是环境部署复杂，因此本文利用 Docker 实现一键部署服务器。

> 在线上部署的方法则比较多：
<br/>　利用 [GitBook](http://app.gitbook.com/) 官方提供的免费空间
<br/>　利用 GitHub Pages （有 300M 免费空间）
<br/>　租用个人云服务器，同样可以用 Docker 搭建

------
## 运行环境

　![](https://img.shields.io/badge/Platform-Windows%2010%20x64-brightgreen.svg) ![](https://img.shields.io/badge/Platform-Linux%20x64-brightgreen.svg) ![](https://img.shields.io/badge/Platform-Mac%20x64-brightgreen.svg) 


> 注：
<br/>　因为 gitbook 服务是运行在 Docker 中，所以不论使用哪个平台，都要预装好 Docker 环境
<br/>　但是本文所使用的基础镜像是基于 Linux 的，因此 Docker in Windows 是无法直接安装的
<br/>　所以针对 Windows 10 ，推荐使用 WSL ( Windows Subsystem for Linux )
<br/>　通过 WSL 安装 Ubuntu 系统，然后再[在 Ubuntu 里面安装 Docker Deamon](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
<br/>　最后 Docker in Windows 做端口映射，就可以实现 Windows 到 Linux 的无缝对接
<br/>　具体的 Windows Docker 环境部署方法可参考 《[简书： Win10 内置 Ubuntu 完美使用 Docker in Windows](https://www.jianshu.com/p/97d16b68045f)》
<br/>　至于 Linux 和 Mac 则简单得多，直接安装 Docker Deamon 即可使用，具体方法自行谷歌


<details>
<summary>展开查看图片</summary>
<br/>

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/00.png)

</details>


------
## 使用方法

### 构建 GitBook 镜像

首先安装 `git` 命令行工具，然后 `clone` 本仓库到本地：

`git clone https://github.com/lyy289065406/gitbook-server-docker`


在命令行环境下 **打开本地仓库目录** 。 Docker 脚本已经编排好在 [`./Dockerfile`](https://github.com/lyy289065406/gitbook-server-docker/blob/master/Dockerfile) 中，可以不修改直接使用。

构建 Docker 镜像（镜像名称 `exp/gitbook-server` 可根据 Docker 规范自定义修改）：

`docker build . -t exp/gitbook-server:latest`

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/01.png)


至此镜像已经安装完毕，下文主要是测试 GitBook 镜像是否可用。



### 初始化 GitBook 项目

在 Docker 镜像中执行命令 `gitbook init`：

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook init`

>　该命令会自动创建用于 **示例** 的 GitBook 文件 。
<br/>　实际效果就是在工作目录 `./gitbook` 下创建两个符合 GitBook 语法的文件 `README.md` 和 `SUMMARY.md` 。

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/02.png)




### 构建 GitBook 项目

在 Docker 镜像中执行命令 `gitbook build`：

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook build`

>　该命令会根据 GitBook 文件 `README.md` 和 `SUMMARY.md` 构建 html 项目 。
<br/>　实际效果就是在工作目录 `./gitbook` 下构建目录名为 `_book` 的静态网页文件 。
<br/>　本地可以通过 `./gitbook/_book/index.html` 测试访问 。

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/03.png)




### 启动 GitBook 服务


在 Docker 镜像中执行命令 `gitbook serve`：

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook serve`

> 该命令效果就是构建一个可以访问 `./gitbook/_book/index.html` 的 Web 服务。

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/04.png)



------
## FAQ

<details>
<summary>展开查看详细</summary>
<br/>

### 0x01 前文中 Docker 命令的参数是什么含义？

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server <Command>`

- `docker run`：运行镜像
- `--rm`：退出镜像后自动删除运行时产生的数据（此镜像目的是提供 GitBook 服务的运行环境，因此没必要保留数据）
- `-v "$PWD/gitbook:/gitbook"`：把本地工作目录 `$PWD/gitbook` 挂载到镜像的工作目录 `/gitbook` （这样运行 GitBook 期间的工作数据就会从本地映射到镜像内，即使镜像退出运行，数据依旧会保留在本地）
- `-p 4000:4000`：把镜像内 GitBook 的 4000 服务端口暴露到本地物理机的 4000 端口
- `exp/gitbook-server`：目标镜像名称
- `<Command>`：要在镜像内执行的命令，如 `gitbook serve` 等，更多命令可见 [gitbook-cli](https://github.com/GitbookIO/gitbook-cli)



### 0x02 怎样以后台运行方式启动 GitBook 服务？

增加 `-d` 参数即可：

`docker run -d --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook serve`



### 0x04 怎样停止 GitBook 服务？

先用 `docker ps` 命令查看正在运行的 GitBook 容器，然后执行命令 `docker stop <CONTAINER ID>` 即可 。

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/05.png)




### 0x05 怎样进入这个 Docker 镜像？

执行下面命令即可：

`docker run --rm -v "$PWD/gitbook:/gitbook" -it exp/gitbook-server /bin/sh`

- `-it`：表示以交互方式运行
- `/bin/sh`：此镜像的基础镜像是 `node:8.5-alpine` ，shell 只支持 `/bin/sh`



### 0x06 怎样共享这个 Docker 镜像？

先执行 `docker login` 命令登陆，然后提交到个人的 Docker Hub 仓库：

`docker push exp/gitbook-server:latest`


若提示 *denied: requested access to the resource is denied* 提交失败，是因为镜像 tag 名称 `/` 前面部分的空间名不是个人的用户名，先修改 tag 名称即可（例如我的用户名是 *expm02* ）：

`docker tag fdc060ba7253 expm02/gitbook-server:latest`

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/06.png)



### 0x06 怎样获取共享的 Docker 镜像？

直接执行以下命令即可（这样就不用执行前文的安装步骤了）：

`docker pull expm02/gitbook-server:latest`

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/07.png)

</details>

------

## 版权声明

　[![Copyright (C) 2016-2020 By EXP](https://img.shields.io/badge/Copyright%20(C)-2016~2019%20By%20EXP-blue.svg)](http://exp-blog.com)　[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
  

- Site: [http://exp-blog.com](http://exp-blog.com) 
- Mail: <a href="mailto:289065406@qq.com?subject=[EXP's Github]%20Your%20Question%20（请写下您的疑问）&amp;body=What%20can%20I%20help%20you?%20（需要我提供什么帮助吗？）">289065406@qq.com</a>


------
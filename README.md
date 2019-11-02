# gitbook-server-docker

> 使用 Docker 构建的 GitBook 服务器

------

## 运行环境

　![](https://img.shields.io/badge/Platform-Windows%2010%20x64-brightgreen.svg) ![](https://img.shields.io/badge/Platform-Linux%20x64-brightgreen.svg) ![](https://img.shields.io/badge/Platform-Mac%20x64-brightgreen.svg) 


> 注：
<br/>　因为 gitbook 服务是运行在 Docker 中，所以不论使用哪个平台，都要预装好 Docker 环境
<br/>　但是本文所使用的基础镜像是基于 Linux 的，因此 Docker in Windows 是无法直接安装的
<br/>　所以针对 Windows 10 ，推荐使用 WSL (Windows Subsystem for Linux)
<br/>　通过 WSL 安装 Ubuntu 系统，然后再在 Ubuntu 里面安装 Docker Deamon
<br/>　最后 Docker in Windows 做端口映射，就可以实现 Windows 到 Linux 的无缝对接
<br/>　具体的 Windows 环境部署方法略，可参考 《[简书： Win10 内置 Ubuntu 完美使用 Docker in Windows](https://www.jianshu.com/p/97d16b68045f)》
<br/>　至于 Linux 和 Mac 则简单得多，直接安装 Docker Deamon 即可使用，具体方法自行谷歌


<details>
<summary>展开查看图片</summary>
<br/>

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/00.png)

</details>



## 使用方法

### 构建 GitBook 镜像

首先安装 `git` 命令行工具，然后 `clone` 本仓库到本地：

`git clone https://github.com/lyy289065406/gitbook-server-docker`


在命令行环境下 **打开本地仓库目录** 。


Docker 脚本已经编排好在 [`./Dockerfile`](https://github.com/lyy289065406/gitbook-server-docker/blob/master/Dockerfile) 中，可以不修改直接使用。


构建 Docker 镜像（镜像名称 `exp/gitbook-server` 可根据 Docker 规范自定义修改）：

`docker build . -t exp/gitbook-server:latest`

<details>
<summary>展开查看图片</summary>
<br/>

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/01.png)

</details>


------
### 初始化 GitBook 项目

在 Docker 镜像中执行命令 `gitbook init`：

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook init`

该命令会自动创建用于 **示例** 的 GitBook 文件 。

实际效果就是在工作目录 `./gitbook` 下创建两个符合 GitBook 语法的文件 `README.md` 和 `SUMMARY.md` 。

<details>
<summary>展开查看图片</summary>
<br/>

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/02.png)

</details>


------
### 构建 GitBook 项目

在 Docker 镜像中执行命令 `gitbook build`：

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook build`

该命令会根据 GitBook 文件 `README.md` 和 `SUMMARY.md` 构建 html 项目 。

实际效果就是在工作目录 `./gitbook` 下构建目录名为 `_book` 的静态网页文件 。

通过 `./gitbook/_book/index.html` 可以测试访问 。

<details>
<summary>展开查看图片</summary>
<br/>

![](https://github.com/lyy289065406/gitbook-server-docker/blob/master/img/03.png)

</details>


------
### 启动 GitBook 服务

`docker run --rm -v "$PWD/gitbook:/gitbook" -p 4000:4000 exp/gitbook-server gitbook serve`


`docker run --rm -v "$PWD/gitbook:/gitbook" -it exp/gitbook-server /bin/sh`

同时把 `./gitbook` 目录挂载到镜像中的 `/gitbook` 目录、 并把 4000 端口暴露出来


`docker login`

`docker push exp/gitbook-server:latest`


`docker tag fdc060ba7253 expm02/gitbook-server:latest`


`docker pull expm02/gitbook-server:latest`


------
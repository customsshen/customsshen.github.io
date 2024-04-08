持续创作，加速成长！这是我参与「掘金日新计划 · 6 月更文挑战」的第 4 天，点击查看活动详情

大家好，我是山月，这是我最近新开的专栏： **前端部署系列** 。包括 Docker、CICD 等内容，大纲图示如下：

![](photo/202206091636424.png)

示例代码开源，置于 Github 中，演示如何对真实项目进行部署上线。

前端部署系列正在更新: _16_ /18

`docker` 使应用部署更加轻量，可移植，可扩展，更好的环境隔离也更大程度地避免了生产环境与测试环境不 一致的巨大尴尬。

由于 `docker` 轻便可移植的特点也极大促进了 `CI/CD` 的发展。

## 术语

`docker` 的架构图如下

![](photo/2022060916364218.png)

从图中可以看出几个组成部分

*   `docker client` : 即 `docker` 命令行工具
*   `docker host` : 宿主机， `docker daemon` 的运行环境服务器
*   `docker daemon` : `docker` 的守护进程， `docker client` 通过命令行与 `docker daemon` 交互
*   `image` : 镜像，可以理解为一个容器的模板，通过一个镜像可以创建多个容器
*   `container` : 最小型的一个操作系统环境，可以对各种服务以及应用容器化，是镜像的运行实例
*   `registry` : 镜像仓库，存储大量镜像，可以从镜像仓库拉取和推送镜像

## 安装 docker

在本地安装 docker/docker-compose，通过 Docker Desktop 下载 docker 后，双击安装即可。

![](photo/2022060916364233.png)

如果是个人服务器且为 centos，可参考安装 docker。

## 底层原理

`docker` 底层使用了一些 `linux` 内核的特性，大概有 `namespace` ， `cgroups` 和 `ufs` 。

### namespace

`docker` 使用 `linux namespace` 构建隔离的环境，它由以下 `namespace` 组成

```
pid
net
ipc
mnt
uts
user


```

### control groups

也叫 `cgroups` ，限制资源配额，比如某个容器只能使用 `100M` 内存

### Union file systems

`UnionFS` 是一种分层、轻量级并且高性能的文件系统，支持对文件系统的修改作为一次提交来一层层的叠加。 `docker` 的镜像与容器就是分层存储，可用的存储引擎有 `aufs` ， `overlay` 等。

关于分层存储的详细内容可以查看官方文档 [docker: About storage drivers](https://link.juejin.cn?target=https%3A%2F%2Fdocs.docker.com%2Fstorage%2Fstoragedriver%2F)

## 镜像

镜像是一份用来创造容器的配置文件，而容器可以视作最小型的一个操作系统。

`docker` 的镜像和容器都使用了 `unionFS` 做分层存储，镜像作为只读层是共享的，而容器在镜像之上附加了一层可写层，最大程度地减少了空间的浪费，详见下图

![](photo/2022060916364372.png)

### 镜像仓库与拉取

大部分时候，我们不需要自己构建镜像，我们可以在官方镜像仓库拉取镜像

可以简单使用命令 `docker pull` 拉取镜像。拉取镜像后可以使用 `docker inspect` 查看镜像信息

```
# 加入拉取一个 node:alpine 的镜像
$ docker pull node:alpine

# 查看镜像信息
$ docker inspect node:alpine

# 列出所有镜像
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
node                alpine              f20a6d8b6721        13 days ago         105MB
mongo               latest              965553e202a4        2 weeks ago         363MB
centos              latest              9f38484d220f        8 months ago        202MB
复制代码

```

### 构建镜像与发布

但并不是所有的镜像都可以在镜像仓库中找到，另外我们也需要为我们自己的业务应用去构建镜像。

使用 `docker build` 构建镜像， **`docker build` 会使用当前目录的 `dockerfile` 构建镜像** ，至于 `dockerfile` 的配置，参考下节。

`-t` 指定标签

```
# -t node-base:10: 镜像以及版本号
# .: 指当前路径
$ docker build -t node-base:10 .
复制代码

```

当构建镜像成功后可以使用 `docker push` 推送到镜像仓库

## Dockerfile

在使用 `docker` 部署自己应用时，往往需要自己构建镜像。 `docker` 使用 `Dockerfile` 作为配置文件构建镜像，简单看一个 `node` 应用构建的 `dockerfile`

```
FROM node:alpine

ADD package.json package-lock.json /code/
WORKDIR /code

RUN npm install --production

ADD . /code

CMD npm start
复制代码

```

### FROM

基于一个旧有的镜像，格式如下

```
FROM <image> [AS <name>]

# 在多阶段构建时会用到
FROM <image>[:<tag>] [AS <name>]
复制代码

```

### ADD

把目录，或者 url 地址文件加入到镜像的文件系统中

```
ADD [--chown=<user>:<group>] <src>... <dest>
复制代码

```

### RUN

执行命令，由于 `ufs` 的文件系统，它会在当前镜像的顶层新增一层

```
RUN <command>
复制代码

```

### CMD

指定容器如何启动

一个 `Dockerfile` 中只允许有一个 CMD

```
# exec form, this is the preferred form
CMD ["executable","param1","param2"] 

# as default parameters to ENTRYPOINT
CMD ["param1","param2"]

# shell form
CMD command param1 param2
复制代码

```

## 容器

镜像与容器的关系，类似于代码与进程的关系。

```
docker run
docker stop
docker rm


```

### 创建容器

基于 `nginx` 镜像创建一个最简单的容器：启动一个最简单的 http 服务

使用 `docker run` 来启动容器， `docker ps` 查看容器启动状态

```
$ docker run -d --name nginx -p 8888:80 nginx:alpine

$ docker ps -l
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                    NAMES
404e88f0d90c        nginx:alpine         "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes        0.0.0.0:8888->80/tcp     nginx
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                    NAMES
复制代码

```

其中:

*   `-d` : 启动一个 `daemon` 进程
*   `--name` : 为容器指定名称
*   `-p host-port:container-port` : 宿主机与容器端口映射，方便容器对外提供服务
*   `nginx:alpine` : 基于该镜像创建容器

此时在宿主机使用 `curl` 测试容器提供的服务是否正常

```
$ curl localhost:8888
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
复制代码

```

那如果要进入容器环境中呢？使用 `docker exec -it container-name` 命令

```
$ docker exec -it nginx sh
/ #
/ #
/ #
复制代码

```

### 容器管理

`docker ps` 列出所有容器

```
$ docker ps
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                    NAMES
404e88f0d90c        nginx:alpine         "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes        0.0.0.0:8888->80/tcp     nginx
498e7d74fb4f        nginx:alpine         "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes        80/tcp                   lucid_mirzakhani
2ce10556dc8f        redis:4.0.6-alpine   "docker-entrypoint.s…"   2 months ago        Up 2 months         0.0.0.0:6379->6379/tcp   apolloserverstarter_redis_1
复制代码

```

`docker port` 查看容器端口映射

```
$ docker port nginx
80/tcp -> 0.0.0.0:8888
复制代码

```

`docker stats` 查看容器资源占用

```
$ docker stats nginx
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
404e88f0d90c        nginx               0.00%               1.395MiB / 1.796GiB   0.08%               632B / 1.27kB       0B / 0B             2
复制代码

```

## 作业

*   初阶: 了解 docker 常见操作，如构建镜像、运行容器、进入容器执行命令
*   高阶: docker 原理，如何模拟 docker 隔离环境及限制资源
*   面试: Dockerfile、Image、Container 的区别
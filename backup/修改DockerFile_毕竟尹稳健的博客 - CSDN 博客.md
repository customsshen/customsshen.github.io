## DockerFile

#### DockerFile 构建过程

Dockerfile 是用来构建 [Docker](https://so.csdn.net/so/search?q=Docker&spm=1001.2101.3001.7020) 镜像的文本文件，也可以说是命令参数脚本。`docker build`命令用于从 Dockerfile 构建镜像。可以在`docker build`命令中使用 - f 标志指向文件系统中任何位置的 Dockerfile。

Docker 镜像发布的步骤：  
1、编写一个 dockerfile 文件

2、docker build 构建成为一个镜像

3、docker run 镜像

4、docker push 镜像（发布镜像到 DockerHub、阿里云镜像仓库）

#### Dockerfile 指令说明

<table><thead><tr><th>指令</th><th>说明</th></tr></thead><tbody><tr><td>FROM</td><td>指定基础镜像</td></tr><tr><td>MAINTAINER</td><td>镜像是谁写的，姓名 + 邮箱</td></tr><tr><td>RUN</td><td>镜像构建的时候需要运行的命令</td></tr><tr><td>ADD</td><td>将本地文件添加到容器中，tar 类型文件会自动解压 (网络压缩资源不会被解压)，可以访问网络资源，类似 wget</td></tr><tr><td>WORKDIR</td><td>镜像的工作目录</td></tr><tr><td>VOLUME</td><td>挂载的目录</td></tr><tr><td>EXPOSE</td><td>保留端口配置</td></tr><tr><td>CMD</td><td>指定这个容器启动的时候要运行的命令（只有最后一个会生效）</td></tr><tr><td>ENTRYPOINT</td><td>指定这个容器启动的时候要运行的命令，可以追加命令</td></tr><tr><td>ONBUILD</td><td>当构建一个被继承 DockerFile，这个时候就会运行 ONBUILD 的指令，触发指令</td></tr><tr><td>COPY</td><td>功能类似 ADD，但是是不会自动解压文件，也不能访问网络资源</td></tr><tr><td>ENV</td><td>构建的时候设置环境变量</td></tr></tbody></table>

![](photo/2022060916364819.png)

Dockerfile 一般分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令，’#’ 为 Dockerfile 中的注释。

关于 DockerFile 文件的脚本注意点有：

1、每个保留关键字（指令）都必须是大写字母

2、文件中的指令从上到下顺序执行，第一个指令必须是 FROM

3、# 号表示注释

4、每一个指令都会创建提交一个新的镜像层，并提交！

dockerfile 是面向开发的，我们以后要发布项目，做镜像，就需要编写 dockerfile 文件，这个文件十分简单!

Docker 镜像逐渐成为企业交付的标准，必须要掌握!  
步骤: 开发，部署，运维。。。缺一不可!  
DockerFile : 构建文件，定义了一切的步骤，源代码  
Dockerlmages: 通过 DockerFile 构建生成的镜像，最终发布和运行的产品!

Docker 容器︰容器就是镜像运行起来提供服务器

#### 实战测试

创建一个自己的 centos

构建镜像命令：docker build -f dockerfile 文件路径 -t 镜像名 [: 版本号] .

```
# 1、编写dockerFile文件
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# cat dockerfile-centos 
FROM centos
MAINTAINER sky<1459543337@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

# 2、构建镜像
# 构建镜像命令：docker build -f dockerfile文件路径 -t 镜像名[:版本号] .
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# docker build -f dockerfile-centos -t mycentos:0.1 .
Successfully built b0ac2a90ff44
Successfully tagged mycentos:0.1


```

#### CMD 和 ENTRYPOINT 的区别

测试 CMD

```
# 编写dockerfile文件
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# cat dockerfile-cmd-test 
FROM centos
CMD ["ls","-a"]

# 构建dockerfile镜像
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# docker build -f dockerfile-cmd-test -t cmdtest .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM centos
 ---> 5d0da3dc9764
Step 2/2 : CMD ["ls","-a"]
 ---> Running in a16f0c53534c
Removing intermediate container a16f0c53534c
 ---> e47937b047ce
Successfully built e47937b047ce
Successfully tagged cmdtest:latest

# 运行镜像容器
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# docker run e47937b047ce
.
..
.dockerenv
bin
dev

# 想追加一个命令 -l ls -al
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# docker run e47937b047ce -l
docker: Error response from daemon: failed to create shim: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.

# #由于使用的是 CMD指令，命令无追加，-l取代了原本的ls -a，而-l命令不存在所以报错。

```

测试 ENTRYPOINT

```
# 编写dockerfile文件
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# cat dockerfile-cmd-test 
FROM centos
ENTRYPOINT ["ls","-a"]

# 构建镜像
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# docker build -f dockerfile-cmd-entrypoint -t entrypoint-test .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos
 ---> 5d0da3dc9764
Step 2/2 : ENTRYPOINT ["ls","-a"]
 ---> Running in 0bc29180d00a
Removing intermediate container 0bc29180d00a
 ---> b59230bfa931
Successfully built b59230bfa931
Successfully tagged entrypoint-test:latest

# 正常运行
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# docker run b59230bfa931
.
..
.dockerenv
bin
dev

# 追加镜像再次运行
[root@iZwz93j24hpsj03xcor6ieZ dockerfile]# docker run b59230bfa931 -l
total 56
drwxr-xr-x   1 root root 4096 May  3 08:33 .
drwxr-xr-x   1 root root 4096 May  3 08:33 ..
-rwxr-xr-x   1 root root    0 May  3 08:33 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root  340 May  3 08:33 dev


```

#### 实战：Tomcat 镜像

1、准备镜像文件 ： tomcat 压缩包 、jdk 压缩包；

```
[root@iZwz93j24hpsj03xcor6ieZ tomcat]# ls
apache-tomcat-9.0.60.tar.gz  jdk-8u191-linux-x64.tar.gz

```

2、编写 dockefile 文件

```
[root@iZwz93j24hpsj03xcor6ieZ tomcat]# cat Dockerfile 
FROM centos:7
MAINTAINER yinwenjian<1459543337@qq.com>

ADD jdk-8u191-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.60.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_191
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.60
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.60
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.60/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.60/bin/logs/catalina.out


```

3、构建镜像 注意这里没有指定 dockerfile 名称是因为我的 dockerfile 名称为 Dockerfile 会默认识别

```
docker build -t diytomcat .

```

4、启动镜像

```
[root@iZwz93j24hpsj03xcor6ieZ tomcat]# docker run -d -p 8080:8080 --name skytomcat -v /home/sky/build/tomcat/test:/usr/local/apache-tomcat-9.0.60/webapps/test -v /home/sky/build/tomcat/logs:/usr/local/apache-tomcat-9.0.60/logs diytomcat

```

在 test 下面新建一个 html 文件 ;

和一个 WEB-INF 目录下建一个 web.xml

5、访问测试

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

</web-app>

```

index.html

```
<!DOCTYPE html>
<html>
    <head>
         <meta charset="UTF-8"/>
        <title>这是个标题</title>
    </head>
    <body>
        <h1>这是一个一个简单的HTML</h1>
        <p>Hello World！</p>
    </body>
</html>

```

6、发布项目

http:/ip:8080/test/

![](photo/20220609163648243.png)

#### 发布镜像到 DockerHub

退出是 docker logout

```
[root@iZwz93j24hpsj03xcor6ieZ ~]# docker login -u yinwenjian
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

# 提交镜像
[root@iZwz93j24hpsj03xcor6ieZ ~]# docker tag 256aec61645c yinwenjian/diytomcat:1.0
[root@iZwz93j24hpsj03xcor6ieZ ~]# docker images
REPOSITORY             TAG          IMAGE ID       CREATED         SIZE
diytomcat              latest       256aec61645c   14 hours ago    838MB
yinwenjian/diytomcat   1.0          256aec61645c   14 hours ago    838MB
entrypoint-test        latest       b59230bfa931   17 hours ago    231MB
cmdtest                latest       e47937b047ce   17 hours ago    231MB
mycentos               0.1          b0ac2a90ff44   18 hours ago    591MB
mytomcat               1.0          f267763101fa   40 hours ago    684MB
elasticsearch          8.1.3        b543e34fe3ca   2 weeks ago     1.2GB
nginx                  latest       605c77e624dd   4 months ago    141MB
tomcat                 9.0          b8e65a4d736d   4 months ago    680MB
tomcat                 latest       fb5657adc892   4 months ago    680MB
mysql                  8.0          3218b38490ce   4 months ago    516MB
rabbitmq               management   6c3c2a225947   4 months ago    253MB
hello-world            latest       feb5d9fea6a5   7 months ago    13.3kB
centos                 7            eeb6ee3f44bd   7 months ago    204MB
centos                 latest       5d0da3dc9764   7 months ago    231MB
portainer/portainer    latest       580c0e4e98b0   13 months ago   79.1MB
[root@iZwz93j24hpsj03xcor6ieZ ~]# docker push yinwenjian/diytomcat:1.0
The push refers to repository [docker.io/yinwenjian/diytomcat]

```

#### 发布镜像到阿里云镜像服务

1、登录阿里云

2、找到容器镜像服务

3、创建命名空间

![](photo/20220609163649291.png)

4、创建容器镜像

![](photo/20220609163649295.png)

```
docker login --username=毕竟尹稳健 registry.cn-shenzhen.aliyuncs.com
docker tag [ImageId] registry.cn-shenzhen.aliyuncs.com/yinwenjian/skytest:1.0
docker push registry.cn-shenzhen.aliyuncs.com/yinwenjian/skytest:1.0

```

```
docker login --username=毕竟尹稳健 registry.cn-shenzhen.aliyuncs.com
docker tag 5e04fd3ea63b registry.cn-shenzhen.aliyuncs.com/yinwenjian/skytest:1.0
docker push registry.cn-shenzhen.aliyuncs.com/yinwenjian/skytest:1.0

```

```
The push refers to repository [registry.cn-shenzhen.aliyuncs.com/yinwenjian/skytest]
92b51dd75c08: Pushing [=======>                                           ]  31.63MB/221.1MB
ea69da1ec832: Pushing [=================================>                 ]  10.77MB/16.02MB
ee4998ffb352: Pushing [=>                                                 ]   13.1MB/396.5MB
174f56854903: Pushing [====>                                              ]  19.58MB/203.9MB

```
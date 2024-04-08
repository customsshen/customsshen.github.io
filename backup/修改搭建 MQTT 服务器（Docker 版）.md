# 总述

## 文档介绍

MQTT（Message Queuing Telemetry Transport，消息队列遥测传输协议），是一种基于发布 / 订阅（publish/subscribe）模式的 "轻量级" 通讯协议。

在软件开发中，常使用 MQTT 协议进行消息广播，因为 MQTT 是一个协议，所以我们需要搭建一个支持 MQTT 协议的服务器，使服务端和客户端能够通过这个 MQTT 服务器（broker）进行消息转发、通信。

## 部署架构概述

MQTT 单节点：192.168.244.84:1883

# 部署

## 安装 docker

### 查看内核版本（需要 CentOS7 或以上版本）

```
uname -r 

```

### 把 yum 包更新到最新

```
sudo yum update

```

### 安装需要的软件包, yum-util 提供 yum-config-manager 功能，另外两个是 devicemapper 驱动依赖的。

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

```

### 设置 yum 源

```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

```

### 查看仓库中 docker 版本

```
yum list docker-ce --showduplicates | sort -r

```

### 安装 docker

```
sudo yum install docker-ce

```

### 命令测试：

### 启动 Docker、设置开机启动、停止 Docker

```
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl stop docker

```

### 查看版本

```
docker version

```

### 使用一下确认是否启动成功, 使用 search 命令

```
docker search mysql

```

### 查看日志状态成功日志

```
systemctl status docker.service

```

## 下载 MQTT 服务器 emqx

_EMQ X_ (Erlang/Enterprise/Elastic MQTT Broker) 是基于 Erlang/OTP 平台开发的开源物联网 MQTT 消息服务器。官方有提供 Docker 版本，可以直接使用 docker pull 进行下载。

它是目前 MQTT 服务器中，最优秀的产品之一，其优点有：

1.  稳定承载大规模的 MQTT 客户端连接，单服务器节点支持 50 万到 100 万连接。
2.  分布式节点集群，快速低延时的消息路由，单集群支持 1000 万规模的路由。
3.  消息服务器内扩展，支持定制多种认证方式、高效存储消息到后端数据库。
4.  完整物联网协议支持，MQTT、MQTT-SN、CoAP、LwM2M、WebSocket 或私有协议支持。

下载命令：

```docker
docker pull emqx

```

启动容器

```docker
docker run  --name=mqtt  --net=host --restart=always -d emqx

```

**启动后，登录 mqtt 管理页面：http://{ip}:18083，确认部署成功。**  
默认账号：admin  
默认密码：public

![](photo/20220609163644119.png)

接下来我们可以通过编写 client 端和 server 端的脚本，来模拟订阅和发布。

client 端测试脚本：

```
var mqtt = require('mqtt');
var client = mqtt.connect('mqtt://192.168.244.84:1883', {
    username: "admin",
    password: "public",
    clientId: 'client9'
});

function getYYYYMMDDhhmmssByDate() {
    let date = new Date();
    let value = date.getFullYear() * 10000000000 +
        (date.getMonth() + 1) * 100000000 +
        date.getDate() * 1000000 +
        date.getHours() * 10000 +
        date.getMinutes() * 100 +
        date.getSeconds();
    return value;
};


client.on('connect', function() {
    console.log("connect success");
    client.subscribe('/server/task/roleId/1/update_task_data');
});

client.on('message', function(topic, message, packet) {
    console.log(" ");
    console.log("time: ", getYYYYMMDDhhmmssByDate());
    var jsonStr = message.toString()
    console.log("jsonStr: " + jsonStr);
    console.log(" ");
});

```

server 端脚本：

```
var mqtt = require('mqtt');
var client = mqtt.connect('mqtt://192.168.244.84:1883', {
    username: 'admin',
    password: 'public',
    clientId: 'server1'
});


var jsonStr = JSON.stringify({ "event": "update_task_data", "data": { "arrTaskId": [], "arrTaskInst": [{ "id": 1, "roleId": 1, "moduleId": "0", "userId": 0, "taskId": 999, "finish": 0, "taskNodeId": 15, "taskNodeStatus": 0, "taskNodeStartTime": 20210811, "beforeNodeId": 14 }] } });
// 推送的频道和数据
//client.publish("/server/task/roleId/1/update_task_data", jsonStr, { qos: 2, retain: false });



```

运行结果：

![](photo/20220609163644181.png)
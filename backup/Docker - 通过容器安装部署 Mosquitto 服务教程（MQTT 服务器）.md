### 1，MQTT 介绍

MQTT 是一个基于客户端 - 服务器的消息发布 / 订阅传输协议。MQTT 协议是轻量、简单、开放和易于实现的，这些特点使它适用范围非常广泛：

*   在很多情况下，包括受限的环境中，如：机器与机器（M2M）通信和物联网（IoT）。
*   其在，通过卫星链路通信传感器、偶尔拨号的医疗设备、智能家居、及一些小型化设备中已广泛使用。

### 2，Mosquitto 介绍

Mosquitto 是一款实现了消息推送协议 MQTT v3.1 的开源消息代理软件，提供轻量级的，支持可发布 / 可订阅的的消息推送模式，使设备对设备之间的短消息通信变得简单，比如现在应用广泛的低功耗传感器，手机、嵌入式计算机、微型控制器等移动设备。

### 3，安装步骤

（1）首先执行如下命令将镜像下载到本地：

<table><tbody><tr><td><p>1</p></td><td><div><p><code>docker pull eclipse-mosquitto</code></p></div></td></tr></tbody></table>

（2）接着执行如下命令创建目录：

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p></td><td><div><p><code>mkdir</code> <code>-p /mosquitto/config</code></p><p><code>mkdir</code> <code>-p /mosquitto/data</code></p><p><code>mkdir</code> <code>-p /mosquitto/log</code></p></div></td></tr></tbody></table>

（3）然后执行如下命令创建初始化配置文件：

<table><tbody><tr><td><p>1</p></td><td><div><p><code>vi /mosquitto/config/mosquitto.conf</code></p></div></td></tr></tbody></table>

（4）在配置文件中添加如下内容，然后保存退出。

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p></td><td><div><p><code>persistence true</code></p><p><code>persistence_location /mosquitto/data</code></p><p><code>log_dest file /mosquitto/log/mosquitto.log</code></p></div></td></tr></tbody></table>

（5）接着执行如下命令为目录授权（其中日志目录要最大权限）：

<table><tbody><tr><td><p>1</p><p>2</p></td><td><div><p><code>chmod</code> <code>-R 755 /mosquitto</code></p><p><code>chmod</code> <code>-R 777 /mosquitto/log</code></p></div></td></tr></tbody></table>

（6）最后执行如下命令即可启动 mosquitto 容器：

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p></td><td><div><p><code>docker run -d --name=mosquitto --privileged \</code></p><p><code>-p 1883:1883 -p 9001:9001 \</code></p><p><code>-v /mosquitto/config/mosquitto.conf:/mosquitto/config/mosquitto.conf \</code></p><p><code>-v /mosquitto/data:/mosquitto/data \</code></p><p><code>-v /mosquitto/log:/mosquitto/log \</code></p><p><code>eclipse-mosquitto</code></p></div></td></tr></tbody></table>

*   注意：如果使用最新的 2.x 版本 Mosquitto，可能会出现启动后客户端工具无法连接上服务器的情况，我们可以改用 1.x 版本即可。

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p></td><td><div><p><code>docker run -d --name=mosquitto --privileged \</code></p><p><code>-p 1883:1883 -p 9001:9001 \</code></p><p><code>-v /mosquitto/config/mosquitto.conf:/mosquitto/config/mosquitto.conf \</code></p><p><code>-v /mosquitto/data:/mosquitto/data \</code></p><p><code>-v /mosquitto/log:/mosquitto/log \</code></p><p><code>eclipse-mosquitto:1.6.14</code></p></div></td></tr></tbody></table>

### 4，连接测试

![](https://www.hangge.com/blog_uploads/201903/2019032710393640294.png)

## 附：配置权限

（1）有时为了安全我们希望连接 mosquitto 服务时需要用户名密码，首先修改配置文件（/mosquitto/config/mosquitto.conf），添加以下配置：

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p><p>4</p></td><td><div><p><code># 关闭匿名模式</code></p><p><code>allow_anonymous false</code></p><p><code># 指定密码文件</code></p><p><code>password_file /mosquitto/config/pwfile.conf</code></p></div></td></tr></tbody></table>

（2）接着执行如下命令进入容器：

<table><tbody><tr><td><p>1</p></td><td><div><p><code>docker </code><code>exec</code> <code>-it mosquitto sh</code></p></div></td></tr></tbody></table>

（3）执行如下命令建立 pwfile.conf 文件，并设置权限：

<table><tbody><tr><td><p>1</p><p>2</p></td><td><div><p><code>touch /mosquitto/config/pwfile.conf</code></p><p><code>chmod</code> <code>-R 755 /mosquitto/config/pwfile.conf</code></p></div></td></tr></tbody></table>

（4）然后使用 mosquitto_passwd 命令创建用户（比如下面我们创建了一个名为 hangge 的用户，密码为 123），添加完毕后执行 exit 退出容器。

<table><tbody><tr><td><p>1</p></td><td><div><p><code>mosquitto_passwd -b /mosquitto/config/pwfile.conf hangge 123</code></p></div></td></tr></tbody></table>

（5）最后执行如下命令启动容器，这样就为 mosquitto 服务增加了权限验证功能，需要使用我们前面创建的用户密码才能连接。
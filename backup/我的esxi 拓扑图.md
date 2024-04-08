# alpine 
修改网关指向旁路由IP
```bash
vim /etc/network/interface
```
## docker
安装代码
``` bash
apk update
apk add docker
rc-update add docker boot   #把 docker 添加到开机自启
service docker start        #启动docker
```

###  emqx
 docker run -d --name emqx -p 18083:18083 -p 1883:1883 -p 11883:11883 emqx:latest
 
 需要设置默认admin密码 
 The `admins` command can be used to create/update/delete administrative users
emqx ctl admins username password
``` bash
emqx ctl admins
emqx ctl admins passwd  admin password
```

###  qinglong
```bash
docker run -dit \
  -v $PWD/ql:/ql/data \
  -p 5700:5700 \
  --name qinglong \
  --hostname qinglong \
  --restart unless-stopped \
  whyour/qinglong:latest

```
#### PThome签到
#### hdarea签到
#### 渡口签到
#### IKUUU签到
#### RSS推送
#### 公网IP变化推送

``` docker
ql repo [https://github.com/customsshen/ql.git](https://github.com/customsshen/ql.git) "main"

customsshen:keys
```

### portainer
``` docker
docker run -d --restart=always --name="portainer" -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock 6053537/portainer-ce
```

# win10-test
 ## EDGE:tampermonkey 学习强国 js
 ## selenium 渡口签到
 ## WinSCP.exe
 ## openssh
 ## airtest 钉钉学习


# istoreOS
## 添加软件源
> **系统---软件包---配置OPKG**
> 两个核心步骤： 
- 1注释掉检查签名**etc/opkg.conf**     # option check_signature 
- 2添加软件源地址**etc/opkg/customfeeds.conf**，如x86的
~~~ vim
src/gz dllkids https://op.dllkids.xyz/packages/x86_64/
~~~

## openclash
下载IPK [Releases · vernesong/OpenClash (github.com)](https://github.com/vernesong/OpenClash/releases)
## 打印机共享
## Homebox内网测速
http://192.168.1.250:3300

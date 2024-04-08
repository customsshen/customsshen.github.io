# [工控机](https://so.csdn.net/so/search?q=%E5%B7%A5%E6%8E%A7%E6%9C%BA&spm=1001.2101.3001.7020)上安装服务器级虚拟机 vmware ESXi6.7

## 1、工具

硬件：

        主机  ：下载资料，制作启动盘

        工控机：配置好点，安装 [esxi](https://so.csdn.net/so/search?q=esxi&spm=1001.2101.3001.7020) 虚拟机

        U 盘：至少 8G

 软件：  

        U 盘引导工具：ventoy-1.0.82

        PE 工具：WePE64_V2.2.iso

        esxi 镜像：ESXi-6.7.0.iso

## 2、安装

### 2.1 用 ventoy 工具制作启动盘

（1）U 盘插入主机（非工控机）

![](https://img-blog.csdnimg.cn/298efbe179054aaa8b3b08356dae7bbc.png)

制作成功后，U 盘名称变成 Ventoy

![](https://img-blog.csdnimg.cn/30e823c84328450a9e07b604acddc1d5.png)

 注： 

写盘不成功：换 U 盘、用 [NTFS](https://so.csdn.net/so/search?q=NTFS&spm=1001.2101.3001.7020) 格式化 U 盘

![](https://img-blog.csdnimg.cn/607dc928df434e44b0dad8cc06ad7167.png)

 （2）esxi 及 WinPE 的 iso 文件拷贝到制作好的启动盘里

![](https://img-blog.csdnimg.cn/9f0df1bbd8df4f0ca871ceea1605cc26.png)

### 2.2 WePE 下格式化硬盘（硬盘是新盘可以不考虑）

（1）U 盘插入工控机

（2）U 盘启动

启动工控机，按【Del】键进入 Bios（不同的机器可能不一样），选择 U 盘启动

![](https://img-blog.csdnimg.cn/0a04860fc9094707ab429c4df9ddfd60.png)

（3）进入 WePE

到 iso 镜像选择页面，选 WePE 启动 PE 系统（如果启动异常，选第三种 vmedia 的方式）

注：启动不了 WEPE：换最新版本的 ventoy，最开始用的 ventoy-1.0.64，后来换成 ventoy-1.0.82 的可以了

（4）格式化工控机硬盘

分区工具 - 选中硬盘 -【右键】-【删除所有分区】-【新建分区】-【保存更改】

![](https://img-blog.csdnimg.cn/3f74c30ae0af4b619bd3e15065a53524.png)

（5）重启重新进 U 盘启动盘

### 2.3 安装 ESXi

（1）启动 Esxi 镜像

U 盘启动到选择 iso 页面，选择 esxi 镜像（选第一种，normal 的启动方式），跑码

![](https://img-blog.csdnimg.cn/21c803b3bf564d598df56d757b0fda04.png)

（2）设置 esxi 安装信息

![](https://img-blog.csdnimg.cn/5d7da7c34ba64e3e9727a5561435d8e2.png)

 欢迎界面 -【Enter】继续

![](https://img-blog.csdnimg.cn/db47120c068d4ea5af87e20a2a4e2a8c.png)

 许可声明界面 -【F11】接受并继续

![](https://img-blog.csdnimg.cn/14da82f8eb8c4fc29b66cc91525d2dd1.png)

 选择安装到硬盘（黄色表示选中） - 【Enter】继续

![](https://img-blog.csdnimg.cn/e0f59909fbd446c18a7ded4623c48fd0.png)

 再次确认 - 【（Enter）OK】继续

![](https://img-blog.csdnimg.cn/217a18e6005344928e8400b4a5b99d03.png)

 选择键盘布局，默认的【US Default】即可，【Enter】继续

![](https://img-blog.csdnimg.cn/0626905b94434aa59b6ed95d5e464069.png)

 设置密码 - 【Enter】继续

需要复杂密码（字符、数字、字符且不能出现经常出现的密码组合） ，密码示例 Abc12345.

记住密码，后面要用！！！
## 密码无法保存 ，可以强制ALT+TAB

![](https://img-blog.csdnimg.cn/e156640d90e548d8b1d3879f3c2723c9.png)

确认安装信息 - 【（F11）Install】 

![](https://img-blog.csdnimg.cn/cf106cdc54e34c7b90f52d79ae5bf05b.png)

（3）开始安装 esxi

![](https://img-blog.csdnimg.cn/00fb65460e094f74a9bf58ba7c26103c.png)

 安装完成 - 拔掉 U 盘 - 【（Enter）Reboot】 -  重启

![](https://img-blog.csdnimg.cn/2b99e44e2fe248a6b695b97069470283.png)

 esxi 系统启动成功

![](https://img-blog.csdnimg.cn/4ab54648c3384cae8feadd70ebf4c94b.png)

## 3、系统设置

### 3.1 进入设置页面

【F2】键 - 输入设置的密码 -【Enter】继续

![](https://img-blog.csdnimg.cn/b38c36715ef6442eaae6dd9856f3fbfc.png)

 进入配置页面进行配置管理

![](https://img-blog.csdnimg.cn/3a38ecf815344a04bb5b4e6278e1e6d0.png)

###  3.2 设置网络

选中【Configure Management Network】-【Enter】

![](https://img-blog.csdnimg.cn/f3b67afd6118454f857dc3951571c4be.png)

 设置 IPv4：选中【IPv4 Configuration】 -【Enter】

![](https://img-blog.csdnimg.cn/2251ed6ed698408e8821a643481a1d96.png)

 设置静态 IP 地址：下移到第三项 - 【空格】选中 - 设置信息 - 【Enter】保存

        根据实际情况进行设置：比如设备都在 192.168.100.xxx 网段，则可以进行如下设置：

IPv4 Address：192.168.100.2（自定义，esxi 的后台管理地址，浏览器登录用）

Subnet Mask：255.255.255.0

Default Gateway：192.168.100.1（后面要安装的 openwrt 的地址，openwrt 作为主路由）

![](https://img-blog.csdnimg.cn/8f5ad34c547640a280af96ea4a79792f.png)

### 3.3 设置 esxi 管理网口

选中【Network Adapters】-【Enter】进入

![](https://img-blog.csdnimg.cn/de464b1d26df4c799b8d35ff10f27580.png)

 默认选择第一个网口，可以根据实际情况设置，设置完【Enter】保存

![](https://img-blog.csdnimg.cn/fa41c02d97e141c393a3a18d1d845fcd.png)

 确认更改信息：【Esc】后退 - 【Y】确认

![](https://img-blog.csdnimg.cn/18fe23fee32b406f8aaa4d471960fbad.png)

### 3.4 清除密码（此步非必须，不想设置密码用）    

【Reset System Configuration】-【F11】- 确认 -【Enter】

### 3.5 退到 esxi 信息界面

 【esc】退到 esxi 信息界面

![](https://img-blog.csdnimg.cn/83d925bcee2c4d9ba5c5de57046aa039.png)

##  4、设置主机

将主机的 IP 地址设置为跟工控机安装的 esxi 一个网段（比如：192.168.100.5）

![](https://img-blog.csdnimg.cn/5a41e6d1666944c49fc0c3362d87ead4.png)

## 5、登录 esxi 后台

### 5.1 连接网线

一段在工控机的第一个网口上，另一端连接主机

### 5.2 登录后台

 （1）浏览器输入 esxi 设置的 IP 地址（192.168.100.1）到 esxi 的登录界面

![](https://img-blog.csdnimg.cn/c2e5a32f8c7048009914d471dfbedb98.png)

 （2）用户名、密码进入

![](https://img-blog.csdnimg.cn/8b9f85f37cc6477c9b602d94828a5c1a.png)

 （3）esxxi 安装完成
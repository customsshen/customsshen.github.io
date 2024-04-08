# 利用 NutClient 使 ESXi 配合群晖 UPS 实现停电自动关机

2022-03-28 11:26:55 24 点赞 218 收藏 19 评论

自去年入手山特 UPS 后（[有了 UPS 加持，再也不怕突然断电了](https://post.smzdm.com/p/a0dw82w0/)），后来又遇到过几次临时停电，虽然黑裙有了 UPS 的加持，是不用担心[硬盘](https://www.smzdm.com/fenlei/yingpan/)损毁了，但软路由里也有硬盘，虽然也连上了 UPS，但一直没去折腾停电自动关机，软路由功耗虽低，但 UPS 毕竟电量有限，遇到停电时间长点的，软路由也撑不住，正好前几天又突然停了一会儿电，于是决心要折腾折腾了

软路由底层用的是 ESXi（6.7.0 Update3），最开始装了 iKuai 跟 op，玩了一段时间后，发现 op 对我来说用处不大，于是现在只剩下了 iKuai，期间打算去掉 ESXi，直接物理机装 iKuai，由于懒，一直也就没去动它

![](https://res.smzdm.com/images/emotions/23.png)

，这两天在度娘上看了半天，大部分都是写脚本，去 ping 一个接市电的地址，当停电 ping 不通时，实现 ESXi 关机，或者是 APC 的 UPS，安装 apcupsd 守护进程，奈何一不懂脚本，二不是 apc 家的 ups，正准备放弃的时候，看到有人在一个写脚本的教程下的留言提到了 NutClient，进一步了解后，发现这玩意儿挺简单的，蛮适合我这种啥也不懂但又忍不住想要折腾的人，于是就有了今天这篇经（liu）验（shui）帖（zhang）

![](https://res.smzdm.com/images/emotions/44.png)

### 1、准备工作

NutClient 工具下载：下载第一个 NutClient-ESXi-2.7.4-2.2.2.i386.tar.gz（链接[Le journal de René - Client NUT pour ESXi 5, 6, 7 et 8Le journal de René (margar.fr)](http://rene.margar.fr/2012/05/client-nut-pour-esxi-5-0/)）

[![](https://qnam.smzdm.com/202203/28/62408acda6a0c1175.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_2/)

### 2、[群晖](https://pinpai.smzdm.com/2315/)

[](https://pinpai.smzdm.com/2315/)

[

关注

](https://pinpai.smzdm.com/2315/)[](javascript:;)

品牌

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)

粉丝：

*   商品百科
    
*   好价
    
*   社区文章
    

端设置

打开**控制面板** - **硬件与电源** - **不断电系统**

[![](https://qnam.smzdm.com/202203/28/62408bb67574d721.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_3/)

首先要确定群晖是否支持此 UPS 的信息采集，点击**设备信息**，我用的山特 TG-BOX 850，OK 的

[![](https://qnam.smzdm.com/202203/28/62408bc05432d8464.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_4/)

勾上**启用网络 UPS [服务器](https://www.smzdm.com/fenlei/fuwuqi/)**

[![](https://qnam.smzdm.com/202203/28/62408bc6e73059041.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_5/)

在**允许的 DiskStation 设备**里填上 ESXi 的 ip 地址

[![](https://qnam.smzdm.com/202203/28/62408c58169bd2116.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_6/)

### 3、ESXi 端设置

ESXi 默认是关闭 SSH 功能的，所以我们首先要打开 ESXi 的 SSH 功能  

进入后台后，打开**管理** - **服务**，找到 TSM 和 TSM-SSH 服务，并将其启动

[![](https://qnam.smzdm.com/202203/28/62408da4ed6b37036.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_7/)

[![](https://qnam.smzdm.com/202203/28/62408dd991c692322.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_8/)

这样 SSH 功能就能临时使用了，重启后会关闭，如果需要继续使用的，再重新开启就好了

接下来还要修改一下软件的安装策略，打开**管理** - **安全与用户**，点击**接受级别**，将其修改为**社区**

[![](https://qnam.smzdm.com/202203/28/62408de3190414696.png_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_9/)

**4、安装 NutClient**

使用工具 SSH 连接到 ESXi，我这里使用的是 winscp

[![](https://qnam.smzdm.com/202203/28/62408ea64aa902090.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_10/)

将刚刚下载的 NutClient 文件上传到 / tmp [文件夹](https://www.smzdm.com/fenlei/wenjianjia/)下

[![](https://qnam.smzdm.com/202203/28/62408f3acb1538657.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_11/)

然后解压刚刚上传到 / tmp 目录里的 NutClient 文件，命令：

tar -xzvf NutClient-ESXi-2.7.4-2.2.2.i386.tar.gz

[![](https://qnam.smzdm.com/202203/28/62409129f3d519647.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_12/)

安装 NutClient，命令：

./upsmon-install.sh  

[![](https://qnam.smzdm.com/202203/28/6240913e02ef96250.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_13/)

看到如上回显信息后，表示安装完成了  

### 5、配置 NutClient

在 ESXi 后台，打开**管理** - **系统** - **高级设置**，在列表中找到 Nut 的相关设置项，可以直接在右上角的搜索框里输入 nut 进行快速的查找，一共有 7 项，如果没有的话，可以尝试重启一下 ESXi

[![](https://qnam.smzdm.com/202203/28/624091d0d6b3e7387.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_14/)

需要修改的是

UserVars.NutFinalDelay：60（关机时间，单位为秒，我这设置为 1 分钟后关闭 ESXi）  
UserVars.NutPassword：secret  
UserVars.NutUpsName：ups@192.168.1.1（这里为群晖地址）  
UserVars.NutUser：monuser  

配置完成后，打开**管理** - **服务**，找到 NutClient，如果服务已经启动的话，点击**操作** - **重新启动**，如果服务没有启动，点击**操作** - **启动**，每次修改配置参数后，都需要重启该服务才能生效，另外需要将该服务的策略设置为**随主机启动和停止**

[![](https://qnam.smzdm.com/202203/28/624093908135d1955.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_15/)

到这里，ESXi 就可以配合群晖 UPS 实现停电自动关机了，但是 ESXi 里的虚拟机并不能安全的关闭，还需要对虚拟机的开关机进行设置，打开**管理** - **系统**，点击**自动启动**，在**编辑设置**里，配置自动启动，因为目前我只有一个虚拟机，因此只需要一个总的设置就可以了，如果有安装好几个虚拟机的话，还需要对每个虚拟机进行**启用**设置

[![](https://qnam.smzdm.com/202203/28/6240948b3a4f51820.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_16/)

**6、连接测试**

通过 SSH 连接 ESXi 后，输入命令：

/opt/nut/bin/upsc ups@192.168.1.1(此为群晖地址，即 ups 服务器 ip)

出现下面这些信息，则表明连接成功  

[![](https://qnam.smzdm.com/202203/28/624097378f86e9162.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_17/)

验证防火墙设置，输入命令：

esxcli network firewall ruleset list

当看到 NutServer 为 true 时，表示正常  

[![](https://qnam.smzdm.com/202203/28/624097a2d68d27624.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_18/)

### 7、断电测试

拔掉 UPS 的电源插头，看看群晖跟软路由是否会先后关机

[![](https://qnam.smzdm.com/202203/28/62409881cc48a7144.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_19/)

[![](https://qnam.smzdm.com/202203/28/6240988c159927198.jpg_e1080.jpg)](https://post.smzdm.com/p/az6p6ern/pic_20/)

拔掉 UPS 的电源插头后，UPS 开始报警，提示市电断开，群晖提示进入[电池](https://www.smzdm.com/fenlei/dianchi/)供电模式，3 分钟后，自动关机，软路由也自动关机了

插上 UPS 的电源插头，群晖自动开机，软路由也能自动开机

### 总语

通过以上的配置与测试，以 ESXi 为底层的软路由能配合群晖的 UPS 实现停电后自动关机并在来电后自动开机了，完美的通过一台 UPS 实现了两台设备的自动开关机

![](https://res.smzdm.com/images/emotions/34.png)

最后，如果觉得这个文章对您有一点点帮助的话，也希望您能为我的文章点赞、收藏、评论一下下。感谢大家的支持，欢迎一起讨论

![](https://res.smzdm.com/images/emotions/33.png)

![](https://res.smzdm.com/images/emotions/33.png)

![](https://res.smzdm.com/images/emotions/33.png)

作者声明本文无利益相关，欢迎值友理性交流，和谐讨论～

![](https://res.smzdm.com/pc/pc_shequ/dist/img/the-end.png)
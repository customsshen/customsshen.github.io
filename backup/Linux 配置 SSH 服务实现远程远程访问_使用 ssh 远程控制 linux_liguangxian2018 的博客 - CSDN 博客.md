1. 查询是否安装 SSH.

```
rpm -pa |grep ssh

```

2. 如果没有安装 rmp:

```
sudo apt-get install rpm          #ubuntu,debian
yum -y instal rpm                 #centos,redhat

```

3. 安装 SSH

```
sudo apt-get install ssh
or
yum -y install openssh

```

4. 启动服务:

```
service sshd start
or
/bin/systemctl restart sshd.service
or
/etc/init.d/sshd start

```

5. 配置端口:

```
vim /etc/ssh/sshd_config

```

6. 将 port 前面的 #删除, 也可以更改其它端口.

![](https://img-blog.csdn.net/20180330165807123?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpZ3Vhbmd4aWFuYmlu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

7. 允许 root 用户远程登录.

![](https://img-blog.csdn.net/20180330165726332?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpZ3Vhbmd4aWFuYmlu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

8. 查看服务是否启动:

```
ps -e | grep ssh

```

![](https://img-blog.csdn.net/20180330180326133?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpZ3Vhbmd4aWFuYmlu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

9. 远程登录:

```
ssh name@192.168.0.128

```

![](https://img-blog.csdn.net/20180330180046753?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpZ3Vhbmd4aWFuYmlu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

10. 服务器与主机之间的文件的上传与下载:

scp 服务器主机之间文件互传:[https://blog.csdn.net/liguangxianbin/article/details/79544182](https://blog.csdn.net/liguangxianbin/article/details/79544182)
参考 [wake_on_lan + shell_command实现ESXi Server开关机 - 『HomeAssistant』综合讨论区 - 『瀚思彼岸』» 智能家居技术论坛 - Powered by Discuz! (hassbian.com)](https://bbs.hassbian.com/thread-11160-1-72.html)

# 安装ha主机需要sshpass
docker 下安装sshpass
~~~ bash
$ docker exec -it ha bash
 apk add --update --no-cache sshpass
~~~

# 安装成功后 添加configuration文件
~~~ json
switch:

  - platform: command_line

    switches:

      centos:

        command_on: "sshpass -p ssSS2326250. ssh -o stricthostkeychecking=no -p22 root@192.168.1.76 'vim-cmd vmsvc/power.on 4'"

        command_off: "sshpass -p ssSS2326250. ssh -o stricthostkeychecking=no -p22 root@192.168.1.76 'vim-cmd vmsvc/power.off 4'"

        command_state: "sshpass -p ssSS2326250. ssh -o stricthostkeychecking=no -p22 root@192.168.1.76'vim-cmd vmsvc/power.getstate 4|grep on|wc -l'"

        value_template: '{{ value == "1" }}'

        friendly_name: 虚拟机WIN7开关
~~~


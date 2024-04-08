通常
我们常用的添加crontab定时任务方式是，手动执行命令：
``` linux
crontab -e
```
然后将任务脚本手动添加到文件中

实际
实际中我们常常想在执行程序启动脚本时，自动添加crontab定时任务，例如通过crontab任务周期性删除程序产生的日志等等，我们可以使用如下脚本，通过脚本自动添加我们的crontab定时任务。将如下脚本修改后，跟随程序启动时执行即可。
```linux
#!/bin/sh

#日志清理脚本 delete_log.sh替换成你的日志清理脚本，或者参考另一篇博客《Linux中用定时任务清理日志》
delete_log_sh_path=/data/delete_log.sh

# crontab脚本 每天凌晨1点执行任务
cron_command="0 1 * * * /bin/bash ${delete_log_sh_path} >/dev/null 2>&1"

# 判断定时任务是否存在
existing_job=$(crontab -l | grep "${delete_log_sh_path}")
if [ -n "${existing_job}" ]; then
    echo "cpp日志定时清理任务已存在,请勿重复执行!!!"
    exit 1
fi

# 添加任务
# 将已有任务输出到临时文件
crontab -l > cron.txt
# 将新任务追加到文件结尾
echo "${cron_command}" >> cron.txt
# 加载定时任务
crontab cron.txt
# 删除临时文件
rm -f cron.txt

crontab -l
echo "cpp日志定时清理任务已创建"

exit 0
```
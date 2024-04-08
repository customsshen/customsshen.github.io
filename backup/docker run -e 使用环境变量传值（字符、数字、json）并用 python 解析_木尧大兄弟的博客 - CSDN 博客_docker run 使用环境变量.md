# 第一步，代码编写

#### Dockerfile

Dockerfile 的后半部分（设置[环境变量](https://so.csdn.net/so/search?q=%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F&spm=1001.2101.3001.7020)默认值，以及传到 run.sh 中）  
一个坑：ENTRYPOINT 后面不能用 [] 的形式 否则无法读取环境变量值。

```
# 设置环境默认值
ENV task_id=123
ENV task_param=1

# entrypoint是容器入口 
# 不能用下面[]这种形式，否则读取不到环境变量!!
# ENTRYPOINT  ["/bin/bash", "/code/run.sh"]
ENTRYPOINT  /bin/bash /code/run.sh $task_id $task_param
```

#### run.sh

用来输出传过来的环境变量，用来验证环境变量是否生效；并执行 python 脚本

```
echo "task_id = ${task_id}"
echo "task_param = ${task_param}"
python /code/main.py
```

#### main.py

python 脚本，用`os.environ`读取环境变量。

```
import os

task_id = os.environ['task_id']
task_param = os.environ['task_param']
```

# 第二步，运行 [docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020)

构建镜像：`docker build -t image_name:v1.211020 -f ./Dockerfile .`  
生成容器并运行：`docker run -e task_id="ua084d" -e task_param={"tag": "BERT"} --name MO_CLS_ua084d 9c6ad6da --rm`

这里踩的一个坑：  
注意：docker run 的 - e 参数要紧跟 run，否则不生效!!  
注意：docker run 的 - e 参数要紧跟 run，否则不生效!!  
注意：docker run 的 - e 参数要紧跟 run，否则不生效!!
> 本文由 [[简悦 SimpRead](http://ksria.com/simpread/)](http://ksria.com/simpread/) 转码， 原文地址 [[blog.csdn.net](https://blog.csdn.net/misterrenwoxing/article/details/128972443)](https://blog.csdn.net/misterrenwoxing/article/details/128972443)

目录  
1 在开发服务器上[[创建虚拟环境](https://so.csdn.net/so/search?q=%E5%88%9B%E5%BB%BA%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83&spm=1001.2101.3001.7020)](https://so.csdn.net/so/search?q=%E5%88%9B%E5%BB%BA%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83&spm=1001.2101.3001.7020)  
1.1 [[查看虚拟环境](https://so.csdn.net/so/search?q=%E6%9F%A5%E7%9C%8B%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83&spm=1001.2101.3001.7020)](https://so.csdn.net/so/search?q=%E6%9F%A5%E7%9C%8B%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83&spm=1001.2101.3001.7020)  
1.2 创建[虚拟环境](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83&spm=1001.2101.3001.7020)  
1.3 安装依赖包  
2 打包压缩所要迁移的[虚拟环境](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83&spm=1001.2101.3001.7020)  
2.1 安装 conda pack  
2.2 打包压缩虚拟环境  
3 在生产服务器上安装虚拟环境  
3.1 将压缩文件上传至生产服务器  
3.2 解压虚拟环境压缩包  
3.3 测试虚拟环境  
4 参考文献

1 在外网服务器上创建虚拟环境
---------------

### 1.1 查看虚拟环境

在 base 环境下，键入 conda env list，可以查看当前已经安装的 python 环境，base 环境为 anaconda 默认环境，torch1.8.1 为我已安装的虚拟环境  
![](https://img-blog.csdnimg.cn/0ca1f7cfa1f14a82aa5ba15e212fd43a.png)

### 1.2 创建虚拟环境

在 base 环境下，键入 conda create --name torch1.8.1 python=3.7，创建名为 torch1.8.1 的虚拟环境，其 python 版本为 3.7，可按需要更改。  
![](https://img-blog.csdnimg.cn/2b11d4abb07a4f558e3fd022def171de.png)

键入 y 回车以确定  
![](https://img-blog.csdnimg.cn/19293e57a31046e8bbabc0f26e7028d5.png)

创建名为 torch1.8.1 的虚拟环境成功  
![](https://img-blog.csdnimg.cn/81144968cd824ece94354ffe77bcd628.png)

### 1.3 安装依赖包

键入 conda activate torch1.8.1 激活虚拟环境，此时用户名前面出现 (torch1.8.1) 表明激活了虚拟环境 torch1.8.1，使用 conda install library_name 安装需要的依赖包，library_name 为相应的依赖包，图中示例为 pandas。  
![](https://img-blog.csdnimg.cn/b524bf3bec0140d199b83f56eba76f4e.png)

键入 y 回车以确定  
![](https://img-blog.csdnimg.cn/fe2321bbb77247b58271455598ef620e.png)

键入 conda deactivate 关闭虚拟环境

2 打包压缩所要迁移的虚拟环境
---------------

### 2.1 安装 conda pack

打包虚拟环境需要安装 conda pack，在 conda 的 base 环境下输入以下命令安装 conda pack

```
conda install -c conda-forge conda -pack

```

### 2.2 打包压缩虚拟环境

键入 conda pack -n torch1.8.1 -o torch1.8.1.tar.gz，torch1.8.1 为将要打包的虚拟环境名，torch1.8.1.tar.gz 为打包压缩保存的文件名。  
![](https://img-blog.csdnimg.cn/eb5c9a087257403c9025fd8ebb34e025.png)

等待 conda pack 检索虚拟环境的依赖包，开始打包压缩，依赖包过大会需要更久的时间。  
![](https://img-blog.csdnimg.cn/d9c923a15d5b4a2c91da58b183582417.png)

打包完成。  
![](https://img-blog.csdnimg.cn/64542da9f28b40699aa5f93445cf254a.png)

3 在生产服务器上安装虚拟环境
---------------

### 3.1 将压缩文件上传至生产服务器

通过 FileZilla、WinSCP、SSH 或者其他工具将虚拟环境压缩包传输至生产服务器。  
![](https://img-blog.csdnimg.cn/ef6713ac4b6c45dc90f955c5bc17a1c3.png)

### 3.2 解压虚拟环境压缩包

在生产服务器上找到 anaconda 的环境安装路径

```
conda info --env # 查看anaconda安装路径

cd /usr/local/tools/anaconda3/envs # cd到anaconda的envs目录

mkdir torch1.8.1 # 创建虚拟环境迁移文件夹

cd /home/user/4 # cd到虚拟环境压缩包所在文件夹
 tar -xzvf torch1.8.1.tar.gz -C /usr/local/tools/anaconda3/envs/torch1.8.1# 解压环境压缩包至目标文件夹


```

等待解压完成  
![](https://img-blog.csdnimg.cn/2b0a161c81604719a72ce98074e2ed19.png)

### 3.3 测试虚拟环境

键入 conda env list 查看已安装的虚拟环境，torch1.8.1 已安装。  
![](https://img-blog.csdnimg.cn/a5b1ed31a3f94b22ac5f6e106ac53cf1.png)
**ESXi** **虚拟机安装群辉 7.1**

**一、准备工作：**

**ESXi 虚拟机一台，版本任意**

**群辉 VMDk 引导文件 （文章末尾有提供下载）**

**群辉系统安装（pat）文件**

**支持兼容情况：ESXi5.5-ESXi8.0 均可正常安装使用。**

**这里我使用的 ESXi8.0 进行安装群辉，其他版本的 ESXi 安装方法差不多。**

![](https://img-blog.csdnimg.cn/595d91136afa4b4c974b289e6123f012.png)

**二、安装部署：**

**1.** **首先我们点击创建 / 注册虚拟机**

![](https://img-blog.csdnimg.cn/ad55634c168b499cbae9d55038a2b2eb.png)

**2.** **选择创建新虚拟机，下一页**

![](https://img-blog.csdnimg.cn/f03dc507247f442eb8b197b67ffeeafb.png)

**3.** **名称可随意填写，兼容性一般选择默认即可，客户机操作系统系列: 选择 Linux,** **客户机操作系统版本: 其他 4.xliunx（64 位）配置完成后，下一页。**

![](https://img-blog.csdnimg.cn/80251807b42b4294b294b35bfb312340.png)

**4.** **存储这里随便选即可，下一页**

![](https://img-blog.csdnimg.cn/2ea97bbea5cb4d6f8414c615830a0244.png)

**5.** **删除硬盘后，在删除 SCSI 控制器、以及没有用的 CD/DVD 驱动器**

![](https://img-blog.csdnimg.cn/492a16a5aec64301b4d3648de5460131.png)

**6.** **删除完成后是这样子的，内存和 CPU 可以根据自己的需求进行随便分配，然后点击下一页**

![](https://img-blog.csdnimg.cn/6d2d91ec53b6407790b552439d9efcc2.png)

**7. 到这里我们点击完成，虚拟机配置和创建工作就已经做的差不多了，**

![](https://img-blog.csdnimg.cn/1c6138e3e2e343d8b9b7a7db988509c0.png)

**8.** **点击存储，找到我们创建的群辉配置文件的目录，点击上载，上传我们群辉的引导文件。**

![](https://img-blog.csdnimg.cn/d593329cb93a4b8e8471060df00ea626.png)

**注意：这里要说明一下，ESXi 的引导文件一共有两个，只上传一个是不会显示的，两个文件上传完毕后会合成一个 VMDK 文件，如果上传完两个文件后，发现只有一个文件说明是正常的。**

**上传完成后的显示状态**

![](https://img-blog.csdnimg.cn/e99607352c704f34b01d28f39e8e92fb.png)

**9.** **我们点击刚刚创建好的群辉虚拟机，右键，点击编辑设置**

![](https://img-blog.csdnimg.cn/518b47e715314a168ad1dfd98acc178d.png)

**10.** **点击添加硬盘，选择现有硬盘，选择刚刚上传的 VMDK 文件**

![](https://img-blog.csdnimg.cn/7cdb8741739f497985803936f178e0cc.png)

![](https://img-blog.csdnimg.cn/d73730d7165b42748b9297f729ce03e9.png)

**11.** **然后大家可以在根据自己的需求可以再添加一块数据盘作为安装系统使用，当然也可以添加直通硬盘，这里教学我就添加一块 50G 的系统盘**

![](https://img-blog.csdnimg.cn/e6d3b5cd894b41a987259f6bffebd957.png)

**12.** **点击虚拟机选项，引导选项 - 选择应当用来引导虚拟机的固件: 选择 BIOS，然后点击保存，**

![](https://img-blog.csdnimg.cn/4ad87f8abfc749629ce7fd3bbc4db575.png)

**13.** **到这里我们的虚拟机就已经全部配置好了**

![](https://img-blog.csdnimg.cn/5983f1335f744a0582dca811e9fa83c0.png)

**14.** **这里面有一个很重要的配置修改一定要做，否则是无法搜到群晖系统的，就是需要修改 “标准虚拟交换机” 的安全选项，把默认的 “拒绝” 都修改成“接受”**

![](https://img-blog.csdnimg.cn/d3d5facb048d463b90cc6e4ffc34af0d.png)

![](https://img-blog.csdnimg.cn/737b8400498c44b39a2b61174ce26dc9.png)

**15.** **配置完成后即可开启电源，到这里出现以下黑色 Dos 窗口，说明已经正常启动成功，使用群辉助手搜索群辉设备安装系统即可使用，群辉系统的安装教程不在做赘述。**

![](https://img-blog.csdnimg.cn/fa4db9692d7046fbade6a46f9956ff5d.png)

![](https://img-blog.csdnimg.cn/83b70fdd12284e1d9e0ad626235ed3a1.png)

![](https://img-blog.csdnimg.cn/2a18901ed6da48d3b76b669fc9c93437.png)

**OFV** **模板导入教程**

**1. 点击创建虚拟机，**

![](https://img-blog.csdnimg.cn/dbf33c2d3dfe4843ba44d8c67e5717ea.png)

**2.** **选择从 OVF 或者 OVA 文件部署虚拟机，我这里 OVF 使用的是 6.7 版本创建的，支持 6.7-8.0 版本的 ESXi，6.7 以下的可能会导入失败无法使用，建议使用上面的手动创建的方式安装**

![](https://img-blog.csdnimg.cn/0595856f383945acb8366dbedadc3c5c.png)

**3.** **输入虚拟机的名称可以随意输入，然后把下载的 OVF 模板文件导入进去**

![](https://img-blog.csdnimg.cn/48f6e36769ec497babeed1b578116149.png)

**4.** **选择自己的存储路径**

![](https://img-blog.csdnimg.cn/b91de043faba495792895d860eb18b4a.png)

![](https://img-blog.csdnimg.cn/a99cef3352fa4f0a9adde169cfd01b27.png)

**5.** **这里提示缺少所需的磁盘映像可以忽略，不影响导入，点击完成**

![](https://img-blog.csdnimg.cn/fd80cd230b234443944a97d7239698a2.png)

**6.** **我们点击虚拟机，编辑，添加一块硬盘，也可以添加直通盘，进行系统安装**

![](https://img-blog.csdnimg.cn/dc93bdedf2aa436b8532f153d3867463.png)

**7.** **配置完成后即可开启电源，到这里出现以下黑色 Dos 界面**

**就说明已经正常启动成功，使用群辉助手搜索群辉设备安装系统即可使用，群辉系统的安装教程不在做赘述。**

![](https://img-blog.csdnimg.cn/eb27f13a8ce34bc8933510733a9d291d.png)

**如果出现创建或者导入成功启动后无法搜到到设备的情况请按照以下说明自查：**  
**1. 检查自己的 CPU 版本，918 不支持 4 代以下的 CPU 建议使用 DS3622 版本**  
**2. 虚拟机** **“标准虚拟交换机”的安全选项，把默认的 “拒绝” 都修改成“接受”**  
**3. 修改网卡为 E1000e**  
**4. 还没想到仔细看以上三条！**

**DSM 虚拟机配置文件打包，包含 918 + 和 3622 两个 7.X 版本的配置文件、引导、vof、系统 pat 包：**  
**123 网盘： [https://www.123pan.com/s/HEl8Vv-pbeHh](https://www.123pan.com/s/HEl8Vv-pbeHh "https://www.123pan.com/s/HEl8Vv-pbeHh")  
百度网盘：[https://pan.baidu.com/s/1ocef29XNg8D9NApkVmpETg?pwd=6sii](https://pan.baidu.com/s/1ocef29XNg8D9NApkVmpETg?pwd=6sii "https://pan.baidu.com/s/1ocef29XNg8D9NApkVmpETg?pwd=6sii") 提取码:（6sii）  
阿里网盘：[https://www.aliyundrive.com/s/JYAuB2hirRF](https://www.aliyundrive.com/s/JYAuB2hirRF "https://www.aliyundrive.com/s/JYAuB2hirRF")  
微云网盘：[https://share.weiyun.com/tzS9wWMs](https://share.weiyun.com/tzS9wWMs "https://share.weiyun.com/tzS9wWMs")  
微信网盘：[https://drive.weixin.qq.com/s?k=AIIA1QdUAAsvD4rZmO](https://drive.weixin.qq.com/s?k=AIIA1QdUAAsvD4rZmO "https://drive.weixin.qq.com/s?k=AIIA1QdUAAsvD4rZmO")  
天翼网盘：[https://cloud.189.cn/t/UVrANb6Jr6by](https://cloud.189.cn/t/UVrANb6Jr6by "https://cloud.189.cn/t/UVrANb6Jr6by") 访问码: (fc9b)  
和彩云盘：[https://caiyun.139.com/m/i?0W5Cs0FB0DHMy](https://caiyun.139.com/m/i?0W5Cs0FB0DHMy "https://caiyun.139.com/m/i?0W5Cs0FB0DHMy")  提取码:（vyCQ）  
迅雷网盘：[https://pan.xunlei.com/s/VNANCm87GsNXFB2odd_F0Ex4A1?pwd=t6dx](https://pan.xunlei.com/s/VNANCm87GsNXFB2odd_F0Ex4A1?pwd=t6dx "https://pan.xunlei.com/s/VNANCm87GsNXFB2odd_F0Ex4A1?pwd=t6dx") 提取码：(t6dx)**  
**ESXi8.0 镜像下载地址：**  
**[https://pan.baidu.com/s/1tDXJ5zf17cDY0llwMhLq1A?pwd=3z8d](https://pan.baidu.com/s/1tDXJ5zf17cDY0llwMhLq1A?pwd=3z8d "https://pan.baidu.com/s/1tDXJ5zf17cDY0llwMhLq1A?pwd=3z8d") 提取码：3z8d**  
[https://www.123pan.com/s/HEl8Vv-MieHh](https://www.123pan.com/s/HEl8Vv-MieHh "https://www.123pan.com/s/HEl8Vv-MieHh")
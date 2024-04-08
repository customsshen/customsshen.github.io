文章来自：https://rmbz.net/archives/vmware-esxi-passthrough

最近再搞 ESXI，把原来的 “黑群晖” 改成 ESXI；因为群晖里有数据，为了不想迁移数据所以需要对硬盘做直通

### 0x01 RDM 直通

通过 RDM（Raw Device Mapping）方式，将磁盘应设为本地 VMDK  
优点：不需要增加硬件和修改任何配置文件，配置起来很易用  
缺点：如果需要对硬盘调整，需要重新映射；群晖无法获取到 SMART 信息

1. 通过命令`ls -l /dev/disks`查看当前挂载的所有磁盘信息。

1.  `[root@HOME-ESXI:~] ls -l /dev/disks`
2.  `total 1948588424`
3.  `-rw------- 1 root root 500107862016 Jan 24 13:18 t10.ATA_____ST3500413AS_________________________________________5VMQDMFJ`
4.  `-rw------- 1 root root 2550005760 Jan 24 13:18 t10.ATA_____ST3500413AS_________________________________________5VMQDMFJ:1`
5.  `-rw------- 1 root root 2147483648 Jan 24 13:18 t10.ATA_____ST3500413AS_________________________________________5VMQDMFJ:2`
6.  `-rw------- 1 root root 495268773888 Jan 24 13:18 t10.ATA_____ST3500413AS_________________________________________5VMQDMFJ:3`
7.  `-rw------- 1 root root 495161835520 Jan 24 13:18 t10.ATA_____ST3500413AS_________________________________________5VMQDMFJ:5`
8.  `-rw------- 1 root root 250059350016 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001`
9.  `-rw------- 1 root root 4161536 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:1`
10.  `-rw------- 1 root root 4293918720 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:2`
11.  `-rw------- 1 root root 242137341440 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:3`
12.  `-rw------- 1 root root 262127616 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:5`
13.  `-rw------- 1 root root 262127616 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:6`
14.  `-rw------- 1 root root 115326976 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:7`
15.  `-rw------- 1 root root 299876352 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:8`
16.  `-rw------- 1 root root 2684354560 Jan 24 13:18 t10.NVMe____HP_SSD_EX900_250GB______________________0000000000000001:9`

2. 通过命令`vmkfstools -z /vmfs/devices/disks/<直通硬盘的标识符> /vmfs/volumes/<保存vmdk的硬盘标识符>/<VMDK名字>.vmdk`，进行挂载；其中 <直通硬盘的标识符> 为上一个命令中的类似这样的值`t10.ATA_____ST3500413AS_________________________________________5VMQDMFJ`；<保存 vmdk 的硬盘标识符> 可在`/vmfs/volumes/`查看的到；<VMDK 名字> 名字随意。

1.  `[root@HOME-ESXI:~] cd /vmfs/volumes/`
2.  `[root@HOME-ESXI:/vmfs/volumes] ls`
3.  `1eeffc73-bcd27263-1e02-52d4b71eff6e 5e2966c6-05e642a6-2fa3-7085c2860b45 6d530177-2222d68b-7c96-8146fdca8e09`
4.  `5e2966c0-51442ba6-628a-7085c2860b45 5e2966c6-ee8383fe-dd4c-7085c2860b45 local.vm.store`
5.  `[root@HOME-ESXI:/vmfs/volumes] cd local.vm.store/`
6.  `[root@HOME-ESXI:/vmfs/volumes/5e2966c6-ee8383fe-dd4c-7085c2860b45] mkdir DMS.store`
7.  `[root@HOME-ESXI:/vmfs/volumes/5e2966c6-ee8383fe-dd4c-7085c2860b45/DMS.store] vmkfstools -z /vmfs/devices/disks/t10.ATA_____ST3500413AS_________________________`
8.  `________________5VMQDMFJ /vmfs/volumes/local.vm.store/DMS.store/disk4.vmdk`

3. 设置群晖磁盘  

![](https://img2022.cnblogs.com/blog/2605882/202204/2605882-20220401223942975-1496386234.png)

至此，RDM 直通设置完成；多磁盘与此类似。

### 0x02 PCIE 直通 (适用所有不能直通的 PCIE 设备)

通过修改`/etc/vmware/passthru.map`配置文件实现  
优点：可以对几乎任何所有的 PCIE 设备直通；不需要担心磁盘顺序等  
缺点：如果对 AHCI 设备配置，会对整个设备映射，无法单独对某块硬盘；需要修改 ESXI 系统配置文件；部分不支持 SMART 信息；需要预留内存空间

1. 打开直通映射文件`vi /etc/vmware/passthru.map`  
2. 在 esxi 上查看设备供应商 ID(VID)、设备 ID(PID) 等信息  

![](https://img2022.cnblogs.com/blog/2605882/202204/2605882-20220401224014963-1214079040.png)

3. 在`passthru.map`中增加对应配置： vi /etc/vmware/passthru.map

1.  `# Intel Corporation Union Point-H AHCI Controller`
2.  `# <供应商ID> <设备ID> d3d0 default`
3.  `8086 a282 d3d0 default`

你还可以用 Xftp 去登录到设备中，并且可以直接右键以记事本打开，来修改文件，还可以备份一份

![](https://img2022.cnblogs.com/blog/2605882/202204/2605882-20220401224513626-774757600.png)

4. 重启 ESXI 服务  
5. 开启直通  

![](https://img2022.cnblogs.com/blog/2605882/202204/2605882-20220401224607750-1203847540.png)

6. 配置群晖，并预留内存空间  

![](https://img2022.cnblogs.com/blog/2605882/202204/2605882-20220401224630889-2117407227.png)

7. 群晖对硬盘识别正常

![](https://img2022.cnblogs.com/blog/2605882/202204/2605882-20220401224651975-1966169911.png)

### 0x03 参考

[ESXI 硬盘 RDM 直通给群晖使用](https://forum.51nb.com/thread-1731181-1-1.html)  
[ESXi 强制开启主板 USB3.0 芯片组](https://koolshare.cn/thread-171040-1-1.html)
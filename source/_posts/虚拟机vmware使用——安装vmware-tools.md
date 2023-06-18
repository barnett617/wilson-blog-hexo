---
title: 虚拟机vmware使用——安装vmware-tools
date: 2018-01-25 09:59:50
update: 2018-1-25 14:14:50
categories: 操作系统
tags: [操作系统]
---

每次安装虚拟机都忘记怎么安装vmware tools，而不安装这个东西，用起来总是各种蹩脚，故做此整理。

<!--more-->

### 背景

虚拟机屏幕不能自适应大小，虽然vmware有相关设置，但貌似不能符合使用要求，因此仍需要安装vmware tools（感觉甚至像一个补丁）来完善vmware的使用，比如与宿主机的文件传输等。

### 安装前提

VMware Tools使用Perl编写，所以需要装有Perl

### 操作步骤（以Ubuntu为例）

1. 从VMware菜单栏中选择“安装VMware Tools”

2. 找到VMware Tools安装文件（后缀为tar.gz的压缩文件）

3. 打开终端，切换至root用户

```
sudo su
```

4. 检查是否自动装载VMware Tools虚拟CD-ROM映像

若已装载CD-ROM设备，则列出CD-ROM设备及其装载点

```
mount
```

![](http://trigolds.com/vm1.png)

```
df
```

![](http://trigolds.com/vm2.png)

5. 若未装载，则需安装CD-ROM驱动器

	1. 检查装载点目录

	可能在/mnt/cdrom或/media/VMware Tools(例如本例)

	2. 若不存在装载点目录则创建

	```
	mkdir /mnt/cdrom
	```

	3. 装载CD-ROM驱动器

	```
	mount /dev/cdrom /mnt/cdrom
	```

	> 某些Linux发行版使用不同的设备名称，或以不同的方式组织/dev目录，若CD-ROM驱动器不是/dev/cdrom或CD-ROM装载点不是/mnt/cdrom，则需根据实际情况进行装载

6. 转到工作目录，例如/tmp

7. 安装VMware Tools前，应删除以前的vmware-tools-distrib目录

8. 解压安装包（若直接解压可能会失败，因为这里解压会创建vmware-tools-distrib目录，而当前路径为只读，不允许mkdir）

9. 所以需要将VMware Tools拷贝到其他目录再解压

可看到成功解压

10. 进入解压后的软件目录并安装

11. 安装完成

Bingo! Well done！

接下来就可以使用VMware一系列拓展功能了（可能需要重新启动虚拟机以使设置生效）

12. 如需要安装后卸载CD-ROM镜像，则执行以下命令（若Linux自动装载CD-ROM则无需卸载，在安装成功后会自动卸载）

```
umount /dev/cdrom
```

参考链接：

<a href="https://docs.vmware.com/cn/VMware-Workstation-Pro/12.0/com.vmware.ws.using.doc/GUID-08BB9465-D40A-4E16-9E15-8C016CC8166F.html">https://docs.vmware.com/cn/VMware-Workstation-Pro/12.0/com.vmware.ws.using.doc/GUID-08BB9465-D40A-4E16-9E15-8C016CC8166F.html</a>

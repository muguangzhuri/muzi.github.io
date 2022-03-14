---
layout: post
title: "VMwar ESXi7 安装"
date: 2021-12-14
toc: true
tags: [VMwar ESXi]
comments: true
author: 暮光烛日
music-id: 505446396
---

# 安装VMwareESXi7

大多数服务器厂商都随着VMware发布新版的VSphere，从而发布集成自家硬件驱动的ISO镜像，但是还是有少数厂商没有，或者服务器使用偏门阵列卡和网卡以及有最新驱动的需求，这就需要手动集成驱动到ESXi镜像中。集成驱动主要以集成阵列卡和网卡驱动为主，其他驱动则可通过ssh安装。镜像中没有阵列卡驱动没法安装，没有网络驱动，没法使用虚拟化平台。网上关于集成 ESXI6.x 驱动的文章其实比较多，大部分都是通过VMware PowerShell在线下载镜像集成驱动的教程，由于网络原因，成功的次数少之又少，就这样，网络门槛劝退了不少人。而使用本地bundle来集成驱动的教程也有，但是关于如何下载缺少的驱动都是一笔带过，我今天主要以如何下载驱动为主。

## 一、环境

### Mware-PowerCLI

​	下载 VMware-PowerCLI-11.5.0-14912921.zip，并解压，把解压出来的模块文件夹放到`C:\Program Files\WindowsPowerShell\Modules`

![image-20210622134711941](https://raw.githubusercontent.com/muguangzhuri/muzi.github.io/main/images/%E5%AE%89%E8%A3%85VMwareESXi7.assets/image-20210622134711941.png)

### ESXi-Customizer-PS

  下载 ESXi-Customizer-PS-v2.6.0.ps1，这是集成驱动用到的脚本，由国外的社区团队维护。

 [ESXi-Customizer-PS-v2.6.0.ps1](f:\Users\Administrator\Downloads\VMwareESXi\ESXi-Customizer-PS-v2.6.0.ps1) 

## 二、下载离线包

VMware vSphere Hypervisor (ESXi) Offline Bundle

* VMware vSphere Hypervisor (ESXi 7 ISO) image

https://pan.baidu.com/s/1EhkFbVpUonMarpMV4AvOIQ 提取码: itsc

* **VMware vSphere Hypervisor (ESXi 7) Offline Bundle**

链接：https://pan.baidu.com/s/18Vv57b88kndS1-03x_ssKg 提取码：9FN5

## 三、下载驱动

我的网卡**Ethernet Connection (11) I219-LM**

下面是新的社区版 **ne1000 VIB** （离线包）:

[https://download3.vmware.com/software/vmw-tools/Intel-NUC-ne1000_0.8.4-3vmw.670.0.0.8169922-offline_bundle-16654787.zip](https://links.jianshu.com/go?to=https%3A%2F%2Fdownload3.vmware.com%2Fsoftware%2Fvmw-tools%2FIntel-NUC-ne1000_0.8.4-3vmw.670.0.0.8169922-offline_bundle-16654787.zip)

```
8086:0d4e - Ethernet Connection (10) I219-LM
8086:0d4f - Ethernet Connection (10) I219-V
8086:0d4c - Ethernet Connection (11) I219-LM
8086:0d4d - Ethernet Connection (11) I219-V
8086:0d53 - Ethernet Connection (12) I219-LM
8086:0d55 - Ethernet Connection (12) I219-V
8086:15fb - Ethernet Connection (13) I219-LM
8086:15fc - Ethernet Connection (13) I219-V
8086:15f9 - Ethernet Connection (14) I219-LM
8086:15fa - Ethernet Connection (14) I219-V
8086:15f4 - Ethernet Connection (15) I219-LM
8086:15f5 - Ethernet Connection (15) I219-V
8086:1a1e - Ethernet Connection (16) I219-LM
8086:1a1f - Ethernet Connection (16) I219-V
8086:1a1c - Ethernet Connection (17) I219-LM
8086:1a1d - Ethernet Connection (17) I219-V

```

关于查找驱动的说明：一般在VMware官网可以下载到大部分驱动，除非硬件很特别，则需联系厂商。 驱动说明：集成的驱动可以是vib格式文件，也可以是offline_bundle.zip文件。我们下载的驱动是压缩包，里面包含vib文件和offline_bundle.zip文件，需要提取二者之一来使用，不能直接使用下载的zip文件。vib文件很好辨认，如何确认下载的zip是offline_bundle.zip 还是二者的一个压缩包。首先，离线bundle驱动包，一般会以offline_bundle命名，其次，离线包包含下面内容

![image-20210622135911173](https://raw.githubusercontent.com/muguangzhuri/muzi.github.io/main/images/%E5%AE%89%E8%A3%85VMwareESXi7.assets/image-20210622135911173.png)

## 四、集成驱动

把ESXi-Customizer-PS-v2.6.0.ps1 和下载好的VSphere离线bundle包放一起，然后新建驱动文件夹drivers/*.vib

 [VMW_bootbank_ne1000-intelnuc_0.8.4-3vmw.670.0.0.8169922.vib](f:\Users\Administrator\Downloads\VMwareESXi\drivers\VMW_bootbank_ne1000-intelnuc_0.8.4-3vmw.670.0.0.8169922.vib) 

![image-20210622134446148](https://raw.githubusercontent.com/muguangzhuri/muzi.github.io/main/images/%E5%AE%89%E8%A3%85VMwareESXi7.assets/image-20210622134446148.png)

PowerShell 执行

.\ESXi-Customizer-PS-v2.6.0.ps1 -izip .\VMware-ESXi-7.0.0-14896552-depot.zip -pkgDir .\drivers

```powershell
PS F:\Users\Administrator\Downloads\VMwareESXi> .\ESXi-Customizer-PS-v2.6.0.ps1 -izip .\VMware-ESXi-7.0.0-14896552-depot.zip -pkgDir .\drivers

This is ESXi-Customizer-PS Version 2.6.0 (visit https://ESXi-Customizer-PS.v-front.de for more information!)
(Call with -help for instructions)

Logging to C:\Users\ADMINI~1\AppData\Local\Temp\ESXi-Customizer-PS-17556.log ...

Running with PowerShell version 5.1 and VMware PowerCLI version 11.5.0.14899560

Adding base Offline bundle .\VMware-ESXi-7.0.0-14896552-depot.zip ... [OK]

Getting Imageprofiles, please wait ... [OK]

Using Imageprofile ESXi-7.0.0-14896552-standard ...
(dated 10/18/2019 10:31:37, AcceptanceLevel: PartnerSupported,
The general availability release of VMware ESXi Server 7.0.0 brings whole new levels of virtualization performance to datacenters and enterprises.)

Loading Offline bundles and VIB files from .\drivers ...
   Loading F:\Users\Administrator\Downloads\VMwareESXi\drivers\VMW_bootbank_ne1000-intelnuc_0.8.4-3vmw.670.0.0.8169922.vib ... [OK]
      Add VIB ne1000-intelnuc 0.8.4-3vmw.670.0.0.8169922 [OK, added]

Exporting the Imageprofile to 'F:\Users\Administrator\Downloads\VMwareESXi\ESXi-7.0.0-14896552-standard-customized.iso'. Please be patient ...


All done.
```

![image-20210622120203708](https://raw.githubusercontent.com/muguangzhuri/muzi.github.io/main/images/%E5%AE%89%E8%A3%85VMwareESXi7.assets/image-20210622120203708.png)



新安装

发现问题

未对文件 D:\ATT&C
K\VMwareESXi7-x\ESXi-Customizer-PS-v2.6.0.ps1 进行数字签名。无法在当前系统上运行该脚本。

Get-ExecutionPolicy -List

Unblock-File  D:\ATT&CK\VMwareESXi7-x\ESXi-Customizer-PS-v2.6.0.ps1

![image-20211214110120075](https://raw.githubusercontent.com/muguangzhuri/muzi.github.io/main/images/%E5%AE%89%E8%A3%85VMwareESXi7.assets/image-20211214110120075.png)

```
 .\ESXi-Customizer-PS-v2.6.0.ps1 -izip .\VMware-ESXi-7.0.0-14896552-depot.zip -pkgDir .\drivers
```

![image-20211214110050377](https://raw.githubusercontent.com/muguangzhuri/muzi.github.io/main/images/%E5%AE%89%E8%A3%85VMwareESXi7.assets/image-20211214110050377.png)

## 五、修改BIOS

解决无法识别SSD、硬盘

RAID修改为 AHCI模式



启用客户机操作系统和远程控制台之间的复制和粘贴操作 要在客户机操作系统和远程控制台之间进行复制和粘贴，必须使用 vSphere Client 启用复制和粘贴操作。 步骤 1 使用 vSphere Client 登录到 vCenter Server 系统并选择虚拟机。 2 在摘要选项卡中，单击编辑设置。 3 选择选项 > 高级 > 常规，然后单击配置参数。 4 单击添加行，并在“名称”和“值”列中键入以下值。 名称 值 isolation.tools.copy.disable false 

isolation.tools.paste.disable false

参考链接

[VMware之离线集成ESXi6.x驱动 - 猫先生的日常点滴 (mr-mao.cn)](https://www.mr-mao.cn/archives/vmware-integrated-local-drivers-for-esxi.html)

[vmware esxi 找不到网卡驱动，硬盘的解决方法 - mofy - 博客园 (cnblogs.com)](https://www.cnblogs.com/z-books/articles/5610848.html)

[VMware ESXi 7.0 （vSphere7.0）官方原版ISO和离线定制包附加vcsa-小北的自留地 (y4f.net)](https://www.y4f.net/71787.html)

[ESXi社区版ne1000 VIB驱动的更新](https://www.jianshu.com/p/b008665db716)

[Enhancements to the community ne1000 VIB for Intel NUC 10 (williamlam.com)](https://williamlam.com/2020/08/enhancements-to-the-community-ne1000-vib-for-intel-nuc-10.html)

[ESXi-6.7.0U3b nvme固态硬盘无法识别解决方法_dnpao的专栏-CSDN博客_esxi nvme](https://blog.csdn.net/dnpao/article/details/109288760)

---
title: 惠普r221tx 安装high sierra 10.13.4
date: 2018-04-19 07:39:14
tags: [Hackintosh,黑苹果]
categories: 黑苹果
top: 2
---
### 一、下载镜像
- 这里使用黑果小兵镜像[10.13.4](https://blog.daliansky.net/macOS-High-Sierra-10.13.4-17E199-Release-Version-and-Clover-4418-Original-Image.html#more) 

### 二、镜像写入U盘
- 在win下使用[transmac](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1768303&highlight=transmac)写入镜像，先格式化为hfs格式，在选择镜像写入（注意transmac要以管理员身份运行）

### 三、修改BIOS
- **关闭安全启动**
- **选择U盘启动方式**
- **进入安装界面**
	- 选择-V启动，如果卡++++，更换OsxAptioFixDrv-64.efi
	- 卡Service only ran for 0 seconds. Pushing respawn out by 10 second，10.13后笔记本需要屏蔽独显安装，这里采用hotpatch屏蔽独显，将SSDT-DDGPU.ssdt放入clover/acpi/pached
	- **惠普电脑**clover需勾选Kernel Lapic
- 选择磁盘工具，进行抹盘，固态硬盘可选择APFS格式，要求硬盘的EFI分区不小于**200M**否则会抹盘失败
- 根据提示进行安装，安装过程中需重启数次，每次重启要在clover界面选择之前**安装的盘符**启动
- 不出意外的安装步骤已经结束了，接下来就是对驱动的完善以及变频的优化

### 四、逐步完善
- **显卡驱动**，一开始安装的时候采用的是注入ig-platform-id的方法驱动，我的HD5500采用的id为0x16260006，安装以后用的SSDT-IGPU.ssdt，新版本的IGPU的hotpatch可自动注入id。安装时直接采用hotpatch暂未尝试
- **声卡驱动**，clover注入id为3，加入AppleALC.kext以及Lilu.kext，然后发现并不能驱动，后来尝试在DSDT中打了IRQ的补丁，重启驱动成功
- **网卡驱动**，根据自己型号选择，本机采用的RTL8100的kext
- **亮度调节**，使用SSDT-PLNF.ssdt和RMCF.ssdt配合AppleBacklightInjector.kext。参考帖： [移动版Intel核显使用hotpatch实现亮度调节的方法](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1774672&highlight=%D2%C6%B6%AF)
	- 在使用本方法遇到一个问题，最大亮度不是硬件上最亮的问题。在尝试中发现移除了SLE的AppleBacklightInjector.kext后亮度依然可以调节（中途误删了AppleBacklightExpert.kext），并解决了最大亮度的问题，但亮度调节档位不均匀。-----后采用0x16260006的ig-platform-id解决，无需删除ABI驱动。
- **电池驱动**，在10.12.x的时候采用的是DSDT打相似电池补丁以及配合AcpiBattery.kext的方法。在10.13.4的时候发现直接就驱动了，可能是新版本的电池驱动原生支持了本电脑。
- 开机**唤醒**登录界面面**卡顿**，更换了许多SMBIOS的机型都没有成功，最后在搞变频的时候看到了这样一篇帖子：[一条命令教你如何确认自己的机型及如何开启HWP](https://blog.daliansky.net/A-command-to-teach-you-how-to-confirm-their-own-models-and-how-to-open-the-HWP.html)在把机型设置为符合条件的机型MacBook9.1后开机卡顿消失。
- **修改系统分配的核显到2048M**，采用远景大神开发的工具：[[原创工具]自动生成核显补丁,提升显存至2G,还可以提取FB数据](http://bbs.pcbeta.com/viewthread-1784050-1-1.html)
- **睡眠唤醒无声**，更新最新版的ALC驱动，并删除CC和EAPD驱动
- **完善变频**，通过CpuFriend和CpuDateProvider动态注入变频数据，目前采用MBA7.2的变频数据并修改最低频率为800MHz。具体用法：[CPUFriend - 动态注入 CPU 电源管理数据](http://bbs.pcbeta.com/viewthread-1752935-1-1.html)。

---
title: Hackintosh遇到的问题以及解决方案
date: 2018-05-03 13:23:59
tags: 黑苹果
categories: 黑苹果
---
> 安装好黑苹果后有许多要完善的地方以及使用中会遇到各种问题，这里记录一些偶然解决的问题的方案以及过程。

### 【2018.5.2】USB问题以及睡眠自动唤醒

起因是插了U盘后发现提示需要为USB配件提供电源而导致USB设备无法使用，在远景搜索后有提示说吧USBInjectAll这个驱动删掉，删掉后重启发现问题解决但USB3.0达不到5G而且摄像头也不能加载，于是这并不能当做解决方案，后来就在想是不是因为DSDT的原因（因为用的是论坛大神帮忙修改的解决自动唤醒的DSDT），可是发现换回原来的DSDT也是不行，于是就回想在USB正常之前做了哪些改动：

- 修改了CPUfriend和CPUprovider。
- 升级了Clover版本。
- clover勾选了`change EC0 to EC`、`change H_EC to EC`两个补丁

于是一步一步复原，复原第一步时，发现删除了两个驱动后变频并没有受到改变，和删之前是一样的，之前误认为修改生效，<del>事实证明这两个驱动对本电脑是有负面影响的。</del>复原第二步发现依然提示需要为USB配件提供电源，于是复原第三步，重启发现问题解决了。<del>而令人意外的是，晚上睡觉之前睡眠第二天早上也没有自动唤醒，说明自动唤醒的问题也解决了。</del>

总结：

- 变频是原生加载了的，虽然原因未知
- <del>自动唤醒是受两个CPU驱动的影响</del>（**后测试是因为电源管理的唤醒以供以太网访问选项引起的**）
- USB配件需要电源时因为误勾选了那两个clover补丁

### 【2018.5.3】睡眠唤醒无声

睡眠唤醒无声，看到了这样一篇帖子：[Realtek ALC282使用applealc applehda原生声卡并解决睡眠唤醒无声问题](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1783287&highlight=%CB%AF%C3%DF%BB%BD%D0%D1%CE%DE%C9%F9)，于是从AppleALC的github项目主页上下载了最新版本（1.2.6），而当前用的是1.2.3版本，看了更新日志，在1.2.5就已经在ALC驱动当中加入了解决睡眠唤醒无声的CC以及EAPD的驱动，于是把clover的ALC驱动更新到了最新版，经测试，不删除原有的CC会导致唤醒依旧无声，在帖子当中也看到了，要想利用CC解决唤醒无声的问题还需要修改CC的参数。

总结：

- AppleALC驱动在1.2.5后更新了CC驱动，可以直接解决睡眠唤醒无声的问题而不需要也不可以同时存在CC驱动。
- CC驱动需要配合自身声卡的参数才可以生效。

### 【2018.5.5】变频问题

- 由于原来的X86PlatformPlugin签名验证失败，所以更换了原版的IOPlatformPluginFamily.kext，修复权限后解决。
- 先更换了7.2的机型使用ssdtPrGen.sh生成了变频ssdt，然后换回了9.1的机型（只有9.1的机型开机不卡顿）
- ssdtPrGen生成的ssdt和XCPM.ssdt（设置PluginType=1）的效果相同，都是为了加载两个x86以及完善电源管理面板。
- 测试中发现只有配合CPUFriend和CpuDateProvider才可以正确加载x86，而变频效果受后者驱动的影响。
- 目前采用的变频方法为
	- SSDT-XCPM.ssdt和CpuFriend.kext以及CpuDateprovider.kext的配合。
	- 通过使用不同的机型plist生成不同变频效果的CpuDateprovider.kext。
	- 目前采用修改过最低频率为800MHz的12.1的变频数据。
	- 更新：测试MBA7.2并修改最低频率为800的变频数据更合适。

### 【2018.5.15】亮度最大值问题
之前在搞10.13的亮度调节时参照了这个帖子[移动版Intel核显使用hotpatch实现亮度调节的方法](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1774672&highlight=%D2%C6%B6%AF)，后更新了[第二版](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1783713)。再详细看过教程以后，发现1.大部分笔记本无需注入EDID于是删掉，2.之前放入AppleBacklightInjector亮度不能够最大的原因和ig-platform-id有关，于是想到当前显卡驱动的方式是SSDT-IGPU自动注入的，查了FB数据发现用的是0x16260004，于是删掉了这个ssdt，并在注入了clover推荐的id：0x16260006.重启，亮度达到了最大值，之前采用的方法是删除AppleBacklightInjector驱动，但亮度调节档位不均匀。
总结：使用ABI驱动来调节亮度的话一定要使用正确的ig-platform-id，否则会影响最大亮度值。驱动要安装到S/L/E或者L/E，安装在clover需要将inject kexts设置为Yes。

### 【2019.4.4】更新10.14.4
在10.13.6下稳定体验了将近一年的时间了，今天忍不住更新了10.14.4（MacOS Mojave）。虽说更新内容并没有什么吸引我的地方，但是作为强迫症的我一向习惯保持软件为最新状态。
在U盘都没有的情况下，我真不知道我哪来的勇气去直接更新。但是令我没想到是，整个过程竟是如此顺利：

- 更新clover到最新版本（直接下载了driver64.efi替换旧版本即可）。
- 更新驱动到最新版。
- 到应用商店下载macOS Mojave，点击安装，一路next。
- 很幸运一路没有五国。
- 开机显卡正常，WiFi正常，修复权限重建缓存后声卡正常。
- 变频无法最低800，后提取了新的MacBook7.2的变频文件，更新了CPUFriend.kext，并按照以前的帖子重新制作了CPUDateProvider.kext，开机后恢复最低800频率。

### 【2019.4.8】更换显卡驱动方式以及亮度调节方式
目前各种显卡大多都依赖者WhatEverGreen这个驱动，今天也想通过这种方式来驱动我的HD5500。于是就仔细浏览了[WEG的官方使用说明](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.cn.md)，结合[黑果小兵关于Hackintool的教程](http://bbs.pcbeta.com/viewthread-1794948-1-1.html)成功驱动了显卡，并应用了最新的亮度调节方法。
> 以下只是概述，具体操作还要按照上边两个帖子一步一步来。

**以前的驱动方法：**

- 注入合适的ig-plateform-id，对于HD5500就是0x16260006（HD6000的id）
- 使用ssdt-IGPU自动注入（后测试此方法不完美，影响亮度调节的档位及最大值）

**现在的驱动方法：**

- 配合WEG驱动，使用Hackintool在clover的Device中打补丁，实际上就是注入显卡的各种数据，目前用到的有：ig-plateform-id、修改显存2048M、修改DVMT值、禁用eGPU。

> 对于BIOS的DVMT Pre-Allocated值大小32M而又无法调节的机型， 需要采用一些方法：最早是通过clover的kexttopatch打补丁，每次更新需要换补丁；后来通过InterGraphicDVMTfixup驱动来防止内核崩溃；现在采用的方式是Hackintool在clover的Device中打补丁解决。

**以前的亮度调节方法：**

1. 安装AppleBacklightInjector.kext驱动。
2. kexttopatch打补丁。
3. 放入SSDT-PNLF。

**现在的亮度调节方法：**

1. WhatEverGreen.kext。
2. 勾选AddPNLF的DSDT补丁与SetIntelBacklight, SetIntelMaxBacklight两项，无需为其赋值，Clover 会根据相应的处理器型号自动适配。要想亮度调节完美，同样需要选择好最合适的ig-Plateform-id。
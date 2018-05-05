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

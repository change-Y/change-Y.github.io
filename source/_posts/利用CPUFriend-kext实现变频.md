---
title: 利用CPUFriend.kext实现变频
date: 2018-04-30 18:08:14
tags: 变频
categories: 黑苹果
---
> 黑苹果CPU的变频受多重因素的影响，如SMBIOS机型、电源管理、x86驱动是否加载等，本机在安装10.13.4后，电源管理正常，x86正常加载，为解决开机卡顿的问题改了MP9.1的机型，但是变频不正常，最低档位1000，最高档位2700，导致温度有时较高，在采用了CPUFried后变频有所改进，最低档800，最高2200，但这种变频变频也有所牺牲，档位只有四个，并且电源管理面板也不完善。

原贴地址：[CPUFriend - 动态注入 CPU 电源管理数据](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1752935&page=6#pid47433245)

1. 下载[CPUFriernd.kext](https://github.com/PMheart/CPUFriend)
2. 下载[ResourceConverter.sh](https://github.com/PMheart/CPUFriend/tree/master/ResourceConverter)
3. 在ResourceConverter.sh所在目录运行以下命令`./ResourceConverter.sh --kext /System/Library/Extensions/IOPlatformPluginFamily.kext/Contents/PlugIns/X86PlatformPlugin.kext/Contents/Resources/Mac-F60DEB81FF30ACF6.plist `其中后边的文件按目录找到对应SMBIOS机型board-ID对应的plist拖入终端即可，其中board-ID可以用clover查看
4. 完成后在ResourceConverter.sh同级目录下会生成一个CPUFriendDataProvider.kext文件，将其连同CPUFriend.kext放入clover的kext下即可
5. 重启查看变频效果
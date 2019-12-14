---
title: 树莓派入门
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Linux
comments: true
date: 2019-07-07 13:59:25
tags:
- Linux
- Raspberry Pi
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/posts/pi.jpg
---

#  树莓派入门

##  外设

1. 之前某活动白嫖的`Raspberry Pi 3B`一个
2. 32G Sandisk micro SD卡一张
3. 读卡器一个
4. USB接口5V、2A充电器一个
5. HDMI线一条
6. HDMI显示器一个（我直接用的客厅的电视机）
7. 键盘、鼠标
8.  wifi模块一个（3B+及以上的不需要）

##  安装Raspbian系统

`Raspberry Pi`官方推荐的系统是`Raspbian`，是`Debian`的定制版。  
推荐使用[NOOBS（New Out Of the Box Software）](https://www.raspberrypi.org/downloads/noobs/)安装系统。

1. 下载NOOBS

> 注意选择NOOBS，而不是NOOBS LITE。  
NOOBS LITE不含系统，在安装时需要网络支持。

2. 将SD卡格式化为FAT格式，我用的是[DiskGenius](http://www.diskgenius.cn/download.php)

![sd](https://tva1.sinaimg.cn/large/007DFXDhly1g4wbgyt7xoj315e0rqqbw.jpg)

3. 将NOOBS.zip解压到SD卡根目录

> 注意根目录需要的是如下图的详细内容，而不是一个NOOBS_v3_1_1的子文件夹。

![noobs](https://tva2.sinaimg.cn/large/007DFXDhgy1g4rb5r19t3j30o00k1q54.jpg)

4. 插入SD卡，接通电源，打开树莓派

5. 一路回车，就可以安装好系统。

##  SSH登录

1. 点击左上角树莓图标，`Preference -> Raspberry Pi Configuration -> Interfaces -> SSH`设置为`Enabled`，之后就可以通过`SSH`登录树莓派。我是用的是`XShell`。  
树莓派默认用户为`pi`，密码`raspberrypi`，登录后可以通过`passwd`修改密码。
2. `ifconfig`查看IP
3. 连接

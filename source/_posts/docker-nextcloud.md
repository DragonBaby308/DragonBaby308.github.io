---
title: Docker应用：使用NextCloud搭建私有云盘
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Docker
comments: true
date: 2019-07-06 20:13:06
tags:
- Docker
- NextCloud
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/posts/nextcloud.jpg
---

#  Docker应用：使用NextCloud搭建私有云盘

##  连接服务器，安装Docker

1. 首先你要有自己的服务器，并且要有连接服务器的工具。在[《阿里云ECS上搭建私人Git仓库》](http://www.dragonbaby308.com/ECS-Git/)一文中介绍了`XShell`的安装。  
2. 同时你需要在服务器上安装`Docker`，参考[《Docker基础》](http://www.dragonbaby308.com/docker/)。
3. 如果你是用的是`阿里云ECS`，那么你还需要打开对应端口的权限，参考[《阿里云ECS上安装Nginx》](http://www.dragonbaby308.com/ECS-Nginx/)。

##  拉取镜像，创建NextCloud容器

`docker run -d -p yourHostPort:yourContainerPort nextcloud`，其中`yourHostPort`是你想要打开的服务器端口，`yourContainerPort`是你想打开的容器端口，一般默认是`80`，这里我选择了`8081:80`。  

> docker run命令参数回顾：  
-d  后台运行
-p  指定端口   hostPort:containerPort

如果没有`pull`镜像的话，`docker run`的时候会自动去`pull`最新的`nextcloud`镜像。  

![nc](https://tva3.sinaimg.cn/large/007DFXDhgy1g4qe5uv01zj30pr0f5q35.jpg)  

通过`docker images`命令可以查看下载好的镜像；通过`docker ps -a`命令可以查看创建的容器。

##  使用NextCloud

使用`URL:8081`或`IP:8081`的方式登录，输入管理员账户、密码后，你的`NextCloud`云盘就创建成功了。  

![安装成功](https://tva3.sinaimg.cn/large/007DFXDhgy1g4qf57naztj31k40t91kx.jpg)
![云盘](https://tva4.sinaimg.cn/large/007DFXDhgy1g4qfb2dmm7j31k40rkadr.jpg)

你可以[下载](https://nextcloud.com/install/#install-clients)对应的客户端对它进行操作。

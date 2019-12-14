---
title: 使用jsDeliver和GitHub白嫖CDN
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout: 
authorDesc: 
categories: GitHub
comments: true
date: 2019-12-14 10:53:13
tags:
- CDN
- jsDeliver
- GitHub
- Git
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/gakki/9.jpeg
---

#  使用`jsDeliver`和`GitHub`白嫖`CDN`

查看[文档](https://www.jsdelivr.com/?docs=gh)可知，`jsDeliver CDN`主要使用了`GitHub`的`release`版本特性：

```text
// load any GitHub release, commit, or branch
// note: we recommend using npm for projects that support it
https://cdn.jsdelivr.net/gh/user/repo@version/file
```

1. 新建`GitHub public`仓库，名称就叫`cdn`。
> ![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/git/cdn/newRepository.png)

2. 在本地电脑克隆远程仓库，将需要的静态资源（比如图片、不超过`20M`的视频……）加入本地仓库，然后提交到远程仓库。
> 默认你已经配置好了`GitHub`的`SSH`，并且本地`Git`也已经配置用户和邮箱。  
参考[《Pro Git》](http://www.dragonbaby308.com/progit/#%E9%A6%96%E6%AC%A1%E7%94%9F%E6%88%90SSH%E5%AF%86%E9%92%A5)

```bash
git clone git@github.com:DragonBaby308/cdn.git
cd cdn
//添加静态资源，建议创建不同目录存储，防止太乱
mkdir js
mkdir img
mkdir movie
git add .
git commit -m "release 1.0"
git push
```

3. 在`GitHub`远程仓库中点击`release`发布。
> ![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/git/cdn/release.png)  
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/git/cdn/release10.png)

4. 通过`jsDeliver`引用资源

```text
//加载js
https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/js/jquery.js
//加载图片
https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/db3.png
```
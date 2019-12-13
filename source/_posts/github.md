---
title: GitHub小技巧
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/header/db3Avatar.jpeg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: GitHub
comments: true
date: 2019-11-29 00:01:55
tags:
- Git
- GitHub
- Go
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/gakki/7.gif
---

#  `GitHub`小技巧

---

##  （一）快捷键查看：`shift + ?`

在任意界面输入`shift + ?`，可以显示快捷键。

---

##  （二）分享某段代码：`#Lstart-Lend`

如果你想分享你的`Github`上某段代码，可以在URL后加`#L开始行号-L结束行号`，如：`https://github.com/DragonBaby308/python_diy_crawlers/blob/master/xiumeim_crawler/xiumeim.py#L26-L32`。其实也不需要特别去记，点中某行代码前的行号，URL后就会加`#L行号`，再按住`shift`，选择结束行号，URL就会加上`#L开始行号-L结束行号`。

---

##  （三）查看自己项目的访问数据：Insights - Traffic

在自己的项目下，点击`Insights`-`Traffic`，里面有`Referring sites`（代表大家从什么网页来到你的项目）和`Popular content`（代表大家经常看你项目的什么文件）。

![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/git/github/shortcut.png)

---

##  （四）编辑代码

在查看任意文件时，右上角会有一个小铅笔的标识。

![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/git/github/pen.png)

点击小铅笔就可以直接编辑文件，编辑完成后，直接在页面上点击`Propose File Change`，`Github`就会为你`fork`这个仓库，创建一个`pull request`，节省了在本地`fork -> pull -> 修改 -> push -> 创建PR`的过程。

---

##  （五）趋势图——https://octoverse.github.com/

[Github语言趋势图](https://octoverse.github.com/)：`https://octoverse.github.com/`

---

##  （六）精确搜索项目

1. `in:name keyWord`：项目名中含有`keyWord`
2. `in:readme keyWord`：项目`README.md`中含有`keyWord`
3. `in:description keyWord`：项目描述中含有`keyWord`
4. `stars:>N`：星星数量大于`N`
5. `language:Golang`：语言限定为`Golang语言`
6. `pushed:>2019-06-06`：**最后一次提交时间**晚于`2019-06-06`

> 比如我想搜索`Golang`语言微服务项目，最后一次提交时间要晚于`2019-06-06`，星星数量大于`1000`个：  
`in:description 微服务 language:go pushed:>2019-06-06 stars:>1000`

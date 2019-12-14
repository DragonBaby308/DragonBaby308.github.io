---
title: GayHub上一些有趣的项目【持续更新~】
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: GitHub
comments: true
date: 2019-08-04 17:47:11
tags:
- GitHub
- Git
- Linux
- Shell
- Vim
- 前端
- Markdown
- Node.js
- Vue.js
- Fun!!!!!!!
- JS
- Homebrew
- Mac
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/ow.jpg
---

#  GayHub上一些有趣的项目

---

##  （一）`reveal-md`：使用`.md`来做PPT  

详细信息见[reveal-md项目地址](https://github.com/webpro/reveal-md)。  
相似的还有用HTML做PPT展示的[reveal.js](https://github.com/hakimel/reveal.js)。  

###  安装

`npm install -g reveal-md`

###  使用

`reveal-md path/demo.md`，其中，`path/demo.md`是你的Markdown文件地址，可以使用本地地址，也可以使用URL。  
使用如下的Markdown文件：

```Markdown
# Title

* Point 1
* Point 2

---

## Second slide

> Best quote ever.

Note: speaker notes FTW!
```

效果如图：  
![reveal-md](https://tva1.sinaimg.cn/large/007rAy9hgy1g3id3iiwqmg31gv0ojq8z.jpg)

###  通过`solarized`主题使用

`reveal-md path/demo.md --theme solarized`  
效果如图：  
![reveal-md theme](https://tva1.sinaimg.cn/large/007rAy9hgy1g3id43ocxkg31gv0ojq9l.jpg)

---

##  （二）`Logoly.Pro`：不正经的Logo生成器  

该项目可以根据你输入的文字创建PornHub风格的Logo（~~知道PornHub是什么的坏孩子请面壁思过~~）。  
[GitHub仓库地址](https://github.com/bestony/logoly)  
[Logoly.Pro在线版](https://logoly.pro/#/)  

比如，我输入DragonBaby，然后点击Export：
![Logoly.Pro](https://tva2.sinaimg.cn/large/007DFXDhgy1g5nspomkv7j31k40rk42d.jpg)
修改`theme/_config.yaml`的`avatar`属性，就可以将导出的图片作为我个人博客的头像啦~
![头像](https://tva4.sinaimg.cn/large/007DFXDhly1g5nszcphxvj31k40rk43c.jpg)

---

##  （三）`emoji-mosaic`：将图片转成emoji标签马赛克

[GitHub仓库地址](https://github.com/ericandrewlewis/emoji-mosaic)  
[emoji-mosaic在线版](http://ericandrewlewis.github.io/emoji-mosaic/)（有一说一，这个在线版属实简约嗷……）  

效果图如下：
![when i was young](https://tva4.sinaimg.cn/large/007DFXDhly1g5nt2rci0vj30u012ob29.jpg)

---

##  （四）`gifi`：在你执行`npm install`时放gif图

* [gifi](https://github.com/vadimdemedes/gifi)：注意它不是在你的终端播放，而是浏览器打开gif图播放，所以酌情安装。

---

##  （五）`bash-insulter`：在输入错误`bash`命令时进行羞辱的脚本

[Github项目地址](https://github.com/hkbakke/bash-insulter)。

###  git clone

首先你服务器需要安装了`Git`。  
通过`git clone https://github.com/hkbakke/bash-insulter bash-insulter`命令克隆项目到本地。

###  将下载内容移动到/etc目录下

`sudo cp bash-insulter/src/bash.command-not-found /etc/`

###  修改/etc/bash.bashrc文件

`vim /etc/bash.bashrc`打开文件，在末尾添加：

```bash
#Bash Insulter
if [ -f /etc/bash.command-not-found ]; then
 . /etc/bash.command-not-found
fi
```

###  source /etc/bash.bashrc 使修改生效

`source /etc/bash.bashrc`  

生效后，输入错误的`bash`命令就会被狠狠羞辱：  

![](https://tva4.sinaimg.cn/large/007DFXDhgy1g4r1xhe10yj30np0eyq3p.jpg)

---

##  （六）`the-fuck`：输入错误`bash`命令后通过`fuck`进行修正

> [the-fuck项目地址](https://github.com/nvbn/thefuck)

* `Mac`下通过`brew install thefuck`安装
* `vim ~/.bashrc`，修改：

```
eval "$(thefuck --alias)"
```

* `source ~/.bashrc`使之生效，输入错误`bash`命令后通过`fuck`命令即可进行修正，如：

```bash
(base) bogon:~ dragonbaby308$ git brach
git: 'brach' is not a git command. See 'git --help'.

The most similar command is
	branch
(base) bogon:~ dragonbaby308$ fuck
git branch [enter/↑/↓/ctrl+c]
```

---

##  （七）12306抢票

* [12306抢票](https://github.com/pjialin/py12306)
> 未测试。

---

##  （八）变声器

* [变声器](https://github.com/CorentinJ/Real-Time-Voice-Cloning)
> 未测试。

---

##  （九）狗屁不通文章生成器

* [狗屁不通文章生成器-`GitHub`地址](https://github.com/menzi11/BullshitGenerator)
* [狗屁不通文章生成器-网页版](https://suulnnka.github.io/BullshitGenerator/index.html)
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/git/github/BullshitGenerator.png)

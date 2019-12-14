---
title: iTerm2和oh-my-zsh联合打造Mac终端
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Linux
comments: true
date: 2019-12-10 22:21:27
tags:
- Linux  
- Shell  
- Fun!!!!!!!
- Mac
- oh-my-zsh
- iTerm2
- python3
- pip
- HomeBrew
- Git
- GitHub
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/mac/iterm2_oh-my-zsh/FujikoIterm2.png
---

#  `iTerm2`和`oh-my-zsh`联合打造`Mac`终端

##  （一）`iTerm2`

###  1.安装并设置为默认终端

1. [下载](https://www.iterm2.com/)并安装`iTerm2`
2. 将`iTerm2`设置为默认终端：`iTerm2 -> Make iTerm2 Default Term`
> ![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/mac/iterm2_oh-my-zsh/iterm2.png)

###  2.背景图片设置

1. 选择背景图片：`iTerm2 -> Preferences -> Profiles -> Window -> Background Image -> Enabled`
> ![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/mac/iterm2_oh-my-zsh/bgiterm2.png)

2. 设置透明度：通过`Blending`调节壁纸的透明度，透明度越高越透明

> 效果图：  
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/mac/iterm2_oh-my-zsh/iterm2mine.png)

###  3.字体设置

1. 通过`pip`安装`PowerLine`：

```shell
# 通过HomeBrew安装python3，获取easy-install
brew install python3
# 通过pip获取PowerLine
pip install powerline-status --user
```

2. 在合适位置创建空目录，执行如下命令安装字体库：

```shell
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts
./install.sh
# Powerline fonts installed to /Users/dragonbaby308/Library/Fonts
cd /Users/dragonbaby308/Library/Fonts
```

3. 设置字体：`iTerm2 -> Preferences -> Profiles -> Text -> Font -> 选择字体`

###  4.配色设置

1. 在合适位置创建空目录，执行如下命令下载配色：

```shell
git clone https://github.com/altercation/solarized
cd solarized/iterm2-colors-solarized/
# 打开Finder
open .
```

2. 在打开的窗口中，双击`Solarized Dark.itermcolors`和`Solarized Light.itermcolors`即可安装明暗两种配色：
> ![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/mac/iterm2_oh-my-zsh/ps.png)

3. 设置配色：`iTerm2 -> Preferences -> Profiles -> Colors -> Color Presets`

---

##  （二）`oh-my-zsh`

###  1.安装

1. `Mac`默认使用`dash`作为终端，通过`chsh -s /bin/zsh`将默认终端修改为`zsh`
2. 安装`oh-my-zsh`：

```shell
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

###  2.主题下载与配置

1. 创建空目录，执行如下命令：

```shell
git clone https://github.com/fcamblor/oh-my-zsh-agnoster-fcamblor.git
cd oh-my-zsh-agnoster-fcamblor/
# 执行该命令会将主题拷贝到oh-my-zsh的themes目录下，当然你也可以手动进行
./install
```

2. 修改配置文件：`vi ~/.zshrc`

```shell
# 将主题配置为ys
ZSH_THEME="ys"
```

3. `source ~/.zshrc`使配置生效

###  3.插件安装

1. 插件安装

```shell
# 语法高亮插件
sudo git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
# 命令补全插件
sudo git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

2. 修改配置文件：`vi ~/.zshrc`

```shell
# zsh-syntax-highlighting必须是最后一个！
plugins=(git, zsh-autosuggestions, zsh-syntax-highlighting)
```

3. `source ~/.zshrc`使配置生效

> 如果觉得自动补全颜色与背景色难以区分，可以在`iTerm2 -> Preferences -> Profiles -> Colors -> 右侧ANSI Colors -> Bright第一行`中配置自动补全的字体颜色。  
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/mac/iterm2_oh-my-zsh/auto.png)

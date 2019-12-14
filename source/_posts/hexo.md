---
title: 静态博客Hexo搭建教程（本地、服务器两开花）
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Hexo
comments: true
date: 2019-05-22 20:31:42
tags:
- Hexo框架      
- Nginx      
- Git      
- 阿里云ECS      
- Linux      
- Shell    
- Vim
- Windows
- Mac
- JS
- Node.js
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/gakki/6.gif
---

#  静态博客Hexo搭建 —— 本地、服务器两开花

---

##  静态博客 —— Hexo

静态博客：是指**没有后端数据库**的博客，所有文章都是以**文件**的形式存储，是一种**纯前端**的博客框架。  

---

###  本地安装

####  1.  安装Node.js    

因为Hexo是通过Node.js生成的，所以首先需要根据自己的操作系统版本[下载](https://nodejs.org/en/download/)并安装Node.js。  
根据步骤提示进行操作后，在item（Windows是cmd）中输入命令：  
`node -v`  
如果能正确显示Node.js版本，则说明安装成功（本人使用的版本为**v10.15.3**）。  

####  2.  通过npm安装Hexo框架

Node.js安装成功后，同时会自动安装一个包管理器，称为npm（Node Package Manager）。  
> 什么是npm？  
  npm是一个包管理器，发布者将自己的代码发布（npm publish）到代码仓库（registry），使用者将自己需要的包写入**package.json**文件中，到代码仓库中把代码下载（npm install）下来。

安装：`npm install -g hexo-cli`  
查看版本：`hexo -v`（本人使用的Hexo版本为**1.1.0**）

> 由于npm代码仓库的服务器在国外，由于Great Firewall的缘故，下载速度可能不尽如人意，这时你可以使用淘宝的npm代码仓库，通过npm安装cnpm：  
`npm install -g cnpm --registry=https://registry.npm.taobao.org`  
安装成功后，可以通过以下命令查看cnpm版本：  
`cnpm -v`  
通过cnpm来操作下载速度会得到很大提升，但包的版本不一定是最新的。  

---

###  本地使用

####  1.  创建目录  

Hexo的所有文章都是以文件的形式存储在目录下的，所以首先需要新建一个目录，我的是`Hexo`，在此目录下操作。

####  2.  初始化博客  

`hexo init`

####  3.  新建文章  

`hexo new "BlogName"`（BlogName是你的文章名称）  
会在`source/_posts`目录下生成BlogName.md文件，可以vim或其他Markdown编辑器进行修改。  

####  4.  生成博客静态文件

`hexo g`（g代表generate），生成博客**静态文件**  
每次修改文章后，都需要通过`hexo clean`清理一下，  
然后通过`hexo g`重新生成，  
最后也不要忘了通过`hexo s`重新启动Hexo。

####  5.  启动博客  

`hexo s`（s代表server，默认端口号是**4000**）  

####  6.  更换主题  

将需要更换的主题下载并解压缩到`theme/yourThemeName`目录下，yourThemeName是你给主题取的名字，  
如我用的主题是[next](https://github.com/theme-next/hexo-theme-next)，只需要解压缩到`Hexo/themes/next`目录下即可,  
当然你也可以直接在`Hexo`目录下执行`git clone https://github.com/theme-next/hexo-theme-next themes/next`，    
解压完成后，修改_config.yaml文件中的`theme`属性，默认是`landscape`，修改为`next`：

```yaml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next                          #注意：“theme:”后要带一个空格
```

每次修改主题后，都需要通过`hexo clean`清理一下，  
然后通过`hexo g`重新生成，  
最后也不要忘了通过`hexo s`重新启动Hexo。  

[《Hexo主题 —— NexT优化》](http://www.dragonbaby308.com/hexo-theme-next/)

####  7.  其他_config.yaml配置

\_config.yaml文件是Hexo项目的全局配置文件，常用的属性有：
```yaml
# Site
# 站点标题
title: DragonBaby308's Blog
# 站点副标题
subtitle: Fighting for BAT!
# 站点描述
description: 记住迄今为止每一次失败时有心无力的感受，“为了更美好的明天而战”。
# 作者
author: DragonBaby308
# 语言——  请注意，官方语言包不再支持简体中文zh-Hans，而是统一采用了zh-CN
language: zh-CN
# 时区——默认是本地时区
timezone:

# URL
# 站点地址
url: http://106.14.153.30
# 站点root，如果网站存在子目录中，url设置为'http://yoursite.com/child'，root设置为'/child/'
root: /
# 生成固定链接的格式
permalink: :title/
permalink_defaults:

# Pagination
# 每页文章数——关闭分页：0
per_page: 10

# Extensions
# 主题
theme: next

# Deployment
# 部署配置
deploy:
  type: git
  repo: git@106.14.153.30:/usr/DragonBaby308/repo/blog.git
  branch: master
```

---

###  服务器部署  

####  （一）  本地 + github.io 白嫖部署  

#####  1.  生成github.io仓库  

首先注册并登录[GitHub](https://github.com)，创建新public仓库，仓库名称**一定要是**：  
`YourGitHubName.github.io`（YourGitHubName是**你的**GitHub昵称，**大小写敏感！**）  

#####  2.  本地安装Hexo的git部署插件  

在Hexo的目录下，输入`npm install --save hexo-deployer-git`，会报一个peerDependencies WARNING，可以忽略。

#####  3.  本地修改_config.yaml文件  

在Hexo目录下，找到_config.yaml文件，在#Deployment做如下修改：  
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/DragonBaby308/DragonBaby308.github.io.git        #你的github.io的网址
  branch: master                                                            #“type:”、“repo:”和“branch:”后都要带一个空格
```

#####  4.  部署  

`hexo d`  
部署成功后，浏览器输入`YourGitHubName.github.io`即可访问，其中YourGitHubName是**你的**GitHub昵称，且**大小写敏感**  

#####  5.  写文章 & 发布

和本地一致，都是`hexo new "yourArticle"`后`hexo clean && hexo d - g`，不再赘述。

---

####  （二）  阿里云ECS服务器部署

#####  1.  思路

本地部署Hexo，通过`hexo new ""`命令新建Markdown文件，存储在`source/_posts`目录下，可以通过Markdown编辑器进行修改，执行`hexo g`命令后，Markdown文件会被渲染为静态的HTML文件，存储在`public/`目录下，这些HTML文件就是博客展示页；  
所以，我们首先要在服务器端建立一个目录，用于存储这些HTML文件，我的是`/usr/DragonBaby308/hexo`，同时这个目录也要是Nginx配置文件中的网站根目录。    
而我们要如何将这些文件发送到服务器呢？一个简单的方式就是通过git发送，所以我们需要在服务器搭建一个git仓库，我的是`/usr/DragonBaby308/repo/blog.git`。  
现在，我们的HTML文件被发送到了`blog.git`中，我们通过git的钩子函数（hook）同步到`hexo`目录中，就能够被Nginx识别，展示给用户了。        
![Hexo服务器部署流程](https://tva1.sinaimg.cn/large/007rAy9hgy1g3a6qdefv9j30rs0go3zo.jpg)

#####  2.  服务器上搭建私人git仓库  

搭建自己的私人git仓库，并与本地建立SSH信任，详见[《阿里云ECCS上搭建私人Git仓库》](http://www.dragonbaby308.com/ECS-Git/)。  
完成此步后，可以得到一个repo目录下的私人仓库，和一个hexo目录，作为Hexo应用部署目录。  

#####  3.  服务器上搭建Nginx

> 什么是Nginx？  
Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，可以提供诸如负载均衡、动静分离的功能。  
【代理服务器】：代理服务器相当于一个中介，介于客户端和服务器之间，客户端与服务器的通信通过代理服务器进行。  
【正向代理】：代理服务器代理客户端，服务器直接和代理服务器通信，不关心客户端是谁，比如**科学上网**。    
【反向代理】：代理服务器代理服务器，客户端不知道访问的具体是哪台服务器，可以对客户端屏蔽服务器端的细节，也能做到负载均衡。  
【负载均衡】：使用服务器集群代替单台服务器的工作时，将访问的压力分摊到不同的服务器，防止某台服务器压力过大。  
【动静分离】：将网站静态资源（HTML，JavaScript，CSS，img等文件）与后台应用分开部署，提高用户访问静态代码的速度，降低对后台应用访问。

我们暂时只使用Nginx的反响代理功能，配置网页根目录为Hexo应用部署目录`/usr/DragonBaby308/hexo`，详见[《阿里云ECS服务器上安装Nginx》](http://www.dragonbaby308.com/ECS-Nginx/)。

#####  4.  服务器安装node.js环境

```Shell
yum install -y nodejs
node -v #查看node.js版本
npm -v  #查看npm版本
```

#####  5.  本地Hexo部署  

本地的Hexo安装在上上节有详细介绍，不再介绍。  
我们直接在本地Hexo目录下，找到_config.yaml文件，在#Deployment做如下修改：

```yaml
# Deployment
# Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git                                                       #用户名
  repo: git@106.14.153.30:/usr/DragonBaby308/repo/blog.git        #私人git仓库
  branch: master                                                  #“type:”、“repo:”和“branch:”后都要带一个空格
```

修改完成后，我们通过`hexo clean`和`hexo d -g`即可部署。   

![Bravo!](https://tva1.sinaimg.cn/large/007rAy9hgy1g3ac9u5dcmj31k40rkmzg.jpg)

---

###  Windows换Mac部署

> 2019年9月23日，我从Windows换成了Mac，为了不丢失进度，参考了[https://feijunjie.github.io/2018/10/10/20181010-如何在另一台电脑上继续hexo写博客/](https://feijunjie.github.io/2018/10/10/20181010-如何在另一台电脑上继续hexo写博客/)

* 首先Mac安装`Git`：在终端下输入`git`，会提示你通过`Xcode`安装`Git`
* 配置`Git`用户名、邮箱

```bash
git config --global user.name="DragonBaby308's MacBook Pro"
git config --global user.emai="xxx@yyy.com"
```

* 生成ssh密钥：`ssh-keygen -t rsa`，将公钥复制到服务器上，参考[http://www.dragonbaby308.com/ECS-Git/](http://www.dragonbaby308.com/ECS-Git/)，通过`ssh git@yourIP`可以测试连接
* [下载](https://nodejs.org/en/)`Node.js`并安装，如果你没有vpn，这一步会巨他妈慢
* 从Windows的博客目录下复制如下文件或文件夹，我是上传到我的[NextCloud私有云盘](http://www.dragonbaby308.com/docker-nextcloud/)：

```xml
_config.yml
package.json
scaffolds/
source/
themes/
```

* 执行如下命令:

```bash
# 修改root密码
sudo bash
sudo passed root
su
npm install hero-cli -g --unsafe-perm
npm install hero -g --unsafe-perm
npm install
npm install hero-deployer-git --save
```

> 期间如果报错，哪个目录没有访问权限，就通过`chmod`授权，如`sudo chmod -R 777 /var/root`

* 一切顺利的话，就可以在Mac通过`hex clean && hero d -g`进行部署了

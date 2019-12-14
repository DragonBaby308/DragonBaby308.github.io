---
title: Hexo主题 —— Sakura 入门
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Hexo
comments: true
date: 2019-12-13 17:20:00
tags:
- Hexo框架
- Hexo主题
- GitHub
- Git
- CDN
- jsDeliver
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/hexo/sakura.jpg
---

#  Hexo主题 —— Sakura 入门

使用`NexT`主题有一段时间了，做了很多魔改，舍不得放弃。但是看别人的`Sakura`主题好香，又觉得眼馋
> 呵，男人o(*￣▽￣*)o

所以我决定再白嫖一个`.github.io`的站点，这次使用`Sakura`主题：[https://dragonbaby308.github.io/](https://dragonbaby308.github.io/)

---

##  （一）安装`Hexo`与`hexo-theme-sakura`

> 参考[《静态博客Hexo搭建教程（本地、服务器两开花）》](http://www.dragonbaby308.com/hexo/)和[《唯美主题hexo-sakura使用教程》](https://www.jianshu.com/p/06f7814d815b)

```bash
mkdir HexoSakura
//默认你已经安装好了npm和hexo-cli
hexo init
```

根据作者的建议，直接访问[https://github.com/honjun/hexo-theme-sakura](https://github.com/honjun/hexo-theme-sakura)获取压缩包，解压到博客**根目录**（注意，不是主题目录！），替换原有文件，然后运行`npm i`安装依赖。  
到这一步，使用`hexo clean && hexo g && hexo s`就已经可以看到站点效果了，不过我们还需要进行一系列自定义的配置。

---

##  （二）配置

###  1.站点配置文件`_config.yaml`

####  （1）简介

```yaml
# Site
# 主标题：这个是你鼠标放标签页显示的名称，主页的名称要在主题配置文件中设置
title: DragonBaby308
# 副标题：不展示
subtitle:
# 描述：不展示
description:
keywords:
# 作者名
author: DragonBaby308
# 语言
language: zh-CN       # 简体中文
timezone:
```

####  （2）部署

> 默认你已经配置好了`GitHub`的`SSH`，并且本地`Git`也已经配置用户和邮箱。  
参考[《Pro Git》](http://www.dragonbaby308.com/progit/#%E9%A6%96%E6%AC%A1%E7%94%9F%E6%88%90SSH%E5%AF%86%E9%92%A5)

```yaml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/DragonBaby308/DragonBaby308.github.io.git        #你的github.io的网址
  branch: master
```

####  （3）备份

> 首先需要在你远程`GitHub`仓库新建一个`backup`分支。

```yaml
# backup
backup:
  type: git
  message: https://DragonBaby308.github.io/  备份
  repository:
    github: https://github.com/DragonBaby308/DragonBaby308.github.io.git,backup
```

> 配置完成后每次通过`hexo clean && hexo b && hexo d -g`就可以完成部署和备份。

---

###  2.主题配置文件`theme/Sakura/_config.yaml`

```yaml
# site name
# 站点名前缀，可以省略
prefixName: 想进阿里の
# 站点名，显示为：前缀+站点名
siteName: DB3

# favicon and site master avatar
# 标签页小图标：
favicon: XXXXX
# 头像：
avatar: XXXXX

# 站点URL
url: https://DragonBaby308.github.io/

# 描述
description: 2019年只剩下尾巴了，希望2020年春招可以进ATM！

# 站点CDN，可以为空，图片需要填完整路径
cdn: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0

# 开启pjax
pjax: 1

# 公告
notice: 我不玩游戏，我定规则。

# 懒加载的加载中图片
lazyloadImg: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg

# 站点菜单配置【暂时保持，以后慢慢改】
menus:
  首页: { path: /, fa: fa-fort-awesome faa-shake }
  归档: { path: /archives, fa: fa-archive faa-shake, submenus: {
    技术: {path: /categories/技术/, fa: fa-code },
    生活: {path: /categories/生活/, fa: fa-file-text-o },
    资源: {path: /categories/资源/, fa: fa-cloud-download },
    随想: {path: /categories/随想/, fa: fa-commenting-o },
    转载: {path: /categories/转载/, fa: fa-book }
  } }
  清单: { path: javascript:;, fa: fa-list-ul faa-vertical, submenus: {
    书单: {path: /tags/悦读/, fa: fa-th-list faa-bounce },
    番组: {path: /bangumi/, fa: fa-film faa-vertical },
    # 歌单: {path: /music/, fa: fa-headphones },
    图集: {path: /tags/图集/, fa: fa-photo }
  } }
  留言板: { path: /comment/, fa: fa-pencil-square-o faa-tada }
  友人帐: { path: /links/, fa: fa-link faa-shake }
  赞赏: { path: /donate/, fa: fa-heart faa-pulse }
  关于: { path: /, fa: fa-leaf faa-wrench , submenus: {
    我: {path: /about/, fa: fa-meetup},
    # 主题: {path: /theme-sakura/, fa: iconfont icon-sakura },
    # Lab: {path: /lab/, fa: fa-cogs },
  } }
  # 客户端: { path: /client/, fa: fa-android faa-vertical }
  RSS: { path: /atom.xml, fa: fa-rss faa-pulse }

# 背景图片
bg:
  - XXXXX
  - XXXXX
  - XXXXX
  - https://cdn.jsdelivr.net/gh/yremp/cdn@2.1.5/img/cover/(2).jpg.webp
  - https://cdn.jsdelivr.net/gh/yremp/cdn@2.1.5/img/cover/(3).jpg.webp
  - https://cdn.jsdelivr.net/gh/yremp/cdn@2.1.5/img/cover/(4).jpg.webp
  - https://cdn.jsdelivr.net/gh/yremp/cdn@2.1.5/img/cover/(5).jpg.webp
  - https://cdn.jsdelivr.net/gh/yremp/cdn@2.1.5/img/cover/(7).jpg.webp

# 背景图片样式：
# 空：原图
# filter-dim：阴影
# filter-grid：横条
# filter-dot：点点
bgclass: filter-dot

# 快速导航面板
# url：链接，如果是本站链接，可以简写
# title：大标题
# desc：描述
# img：背景图
startdash:
  # - {url: /theme-sakura/, title: Sakura, desc: 本站 hexo 主题, img: /img/startdash/sakura.md.png}
  - {url: https://space.bilibili.com/24837083, title: Bilibili, desc: 乾杯！！！, img: /img/startdash/bilibili.jpg}


# your site build time or founded date
# 建站时间
siteBuildingTime: 12/12/2019


# 社交按钮PC端
# url：链接
# qrcode：二维码
# img：图标
social:
  GitHub: {url: http://github.com/DragonBaby308, img: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/social/github.png}
  掘金: {url: https://juejin.im/user/5cef386351882551b47a32f7, img: XXXXX}
  BiliBili: {url: https://space.bilibili.com/24837083, img: XXXXX}
  WeChat: {url: /#, qrcode: XXXXX, img: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/social/wechat.png}
  QQ: {url: /#, qrcode: XXXXX, img: https://cdn.jsdelivr.net/gh/yremp/cdn@2.1.5/img/social/qq.png}

# 社交按钮移动端
# url：链接
# fontawesome：图标
# color：颜色
msocial:
  github: {url: http://github.com/DragonBaby308, fa: fa-github, color: 333}
  # weibo: {url: http://weibo.com/mashirozx?is_all=1, fa: fa-weibo, color: dd4b39}
  # qq: {url: https://wpa.qq.com/msgrd?v=3&uin=954655431&site=qq&menu=yes, fa: fa-qq, color: 25c6fe}

# 赞赏二维码
donate:
  alipay: XXXXX
  wechat: XXXXX

# 首页视频
movies:
  # url前缀
  url: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/movie/
  # 多个视频用逗号隔开，随机获取。支持的格式目前已知MP4,Flv
  name: fujiko.mp4

# 左下角aplayer播放器配置
# id:歌单ID   ——    在歌单的URL中会有体现，自己观察
# server:音乐服务器    ——  tencent：QQ音乐
# autoplay：是否自动播放
aplayer:
  id: 3090329008
  server: tecent
  type: playlist
  fixed: true
  mini: false
  autoplay: false
  loop: all
  order: random
  preload: auto
  volume: 0.7
  mutex: true

# Valine评论
# 记得要在LeanCloud -> 设置 -> 安全中心 -> web安全域名 中，将新域名加进去
valine: true
v_appId: XXXXX
v_appKey: XXXXX
```

---

###  3.分类/标签页配置`theme/Sakura/languages/zh-cn.yaml`

```yaml
# category   
# 分类名称：需要和theme/Sakura/_config.yaml的menus属性对应
# 对应链接为：/categories
技术:
    #中文标题
    zh: 野生技术协会
    # 英文标题
    en: Geek – Only for Love
    # 封面图片
    img: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/banner/coding.jpg
算法:
    zh: 程序 = 数据结构 + 算法
    en: Algorithm
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/Algorithm.jpg
Java基础:
    zh: Java是世界上最好的语言！来战！
    en: Java
    img: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/banner/coding.jpg
Linux:
    zh: Talk is cheap, show me the code.
    en: Linux
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/Linux.jpg
随笔:
    zh: 我有一个埋在心里的梦想，就是成为一个畅销小说家，没有和几个人说过。
    en: Code & Poetry.
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/Novel.jpg
设计模式:
    zh: 设计模式
    en: Design Pattern
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/DesignPattern.jpg
分布式:
    zh: 分布式
    en: Distributed System
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/DistributedSystem.jpg
Docker:
    zh: 容器比虚拟机更加轻量级。	——	沃兹.基硕德
    en: Docker
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/Docker.jpg
Hexo:
    zh: Hexo是非常好用的静态博客。	——	海氏.沃兹.基硕德
    en: Hexo
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/Hexo.jpg
GitHub:
    zh: 全世界最大同性交友平台 ——
    en: GayHub
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/GitHub.jpg
Go:
    zh: Go语言设计得非常人性化，我愿意把它作为第二语言。
    en: GoLang
    img: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/categories/Go.jpg

# tag       
# 标签名称
# 对应链接为：/tags
悦读:
    # 封面图片
    img: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/banner/reading.jpg
```

---

###  4.单页面封面配置

比如“留言板”，修改`/source/comment/index.md`中的`photos`属性即可。

---

###  5.番组计划页`source/bangumi/index.md`

**注释只是为了描述每个属性的作用，自己配置时需要将所有注释删除**！

```yaml
---
layout: bangumi
title: bangumi
comments: false
date: 2019-02-10 21:32:48
keywords:
description:
bangumis:
  # 番组图片
  - img: https://lain.bgm.tv/pic/cover/l/0e/1e/218971_2y351.jpg
  # 番组名
    title: 朝花夕誓——于离别之朝束起约定之花
  # 追番状态 （追番ing/已追完）
    status: 已追完
  # 追番进度
    progress: 100
  # 番剧日文名称
    jp: さよならの朝に約束の花をかざろう
  # 放送时间
    time: 放送时间: 2018-02-24 SUN.
  # 番剧介绍
    desc:  住在远离尘嚣的土地，一边将每天的事情编织成名为希比欧的布，一边静静生活的伊欧夫人民。在15岁左右外表就停止成长，拥有数百年寿命的他们，被称为“离别的一族”，并被视为活着的传说。没有双亲的伊欧夫少女玛奇亚，过着被伙伴包围的平稳日子，却总感觉“孤身一人”。他们的这种日常，一瞬间就崩溃消失。追求伊欧夫的长寿之血，梅萨蒂军乘坐着名为雷纳特的古代兽发动了进攻。在绝望与混乱之中，伊欧夫的第一美女蕾莉亚被梅萨蒂带走，而玛奇亚暗恋的少年克里姆也失踪了。玛奇亚虽然总算逃脱了，却失去了伙伴和归去之地……。
  - img: http://lain.bgm.tv/pic/cover/l/e9/15/179949_c2j50.jpg
    title: 小林家的龙女仆
    status: 已追完
    progress: 100
    jp: 小林さんちのメイドラゴン
    time: null
    desc: 在单身的辛苦OL小林身边突然出现的女仆装束的美少女托尔。长着角和尾巴的她的身姿正是所谓的龙娘。在醉酒的小林邀请下说要到家里去的托尔，鬼使神差地开始以小林家女仆的身份工作……！？“女仆”+“龙”=“女仆龙”有着笨手笨脚的可爱之处！龙娘与人类之间基本上很温暖、偶尔有些黑暗的异种族间交流喜剧！！
  - img: http://lain.bgm.tv/pic/cover/l/42/54/219164_TLkl2.jpg
    title: 鲁邦三世 PART5
    status: 已追完
    progress: 100
    jp: ルパン三世 PART5
    time: null
    desc: 鲁邦三世与次元大介身处法国。故事在某处乡间小镇的公寓一室开始。包括毒品枪械等非法物资在内，任何物品都能购买的地下网站，“马可波罗”。为了盗取虚拟货币，鲁邦等人侵入了戒备森严的巨大服务器设施。他们在那里遇到了神秘的天才黑客少女，阿米。鲁邦和阿米一起正面应对马可波罗的陷阱，但由于敌人设置的“鲁邦游戏”，而落得被全世界监视的下场……阿米身上接连揭晓的谜团，以及知晓鲁邦三世过去的男人出现——鲁邦正陷入前所未有的绝境！
---
```

---

###  5.友链页`source/links/index.md`

**注释只是为了描述每个属性的作用，自己配置时需要将所有注释删除**！

```yaml
---
layout: links
title: links
# 创建日期，可以改下
date: 2018-12-19 23:11:06
# 图片上的标题，自定义修改
keywords: 友人帐
description:
# true/false 开启/关闭评论
comments: true
# 页面头部图片，自定义修改
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.4/img/banner/links.jpg
# 友链配置
links:
  # 类型分组
  - group: 个人项目
    # 类型简介
    desc: 充分说明这家伙是条咸鱼 < (￣︶￣)>
    items:
    # 友链链接
    - url: https://shino.cc/fgvf
    # 友链头像
      img: https://cloud.moezx.cc/Picture/svg/landscape/fields.svg
    # 友链站点名
      name: Google
    # 友链介绍
      desc: Google 镜像
  # 类型分组...
  - group: 小伙伴们
    desc: 欢迎交换友链 ꉂ(ˊᗜˋ)
    items:
    - url: https://shino.cc/fgvf
      img: https://cloud.moezx.cc/Picture/svg/landscape/fields.svg
      name: Google
      desc: Google 镜像
---
```

---

##  （三）其他魔改

###  1.`startdash`名称更改

将`/themes/Sakura/layout/_partial/startdash.ejs`中的`<h1 ...>startdash</h1>`改为`<h1 ...>快速导航</h1>`。

###  2.`Discovery`名称更改

将`/themes/Sakura/layout/index.ejs`中的`<h1 ...>Discovery</h1>`改为`<h1 ...>正文</h1>`。

###  3.文章链接更改

将`_config.yaml`中的`permalink`属性修改为`permalink: :title/`。

###  4.新建帖子头部格式

修改`/scaffolds/post.md`，可以将`hexo n`生成的博客头改为自定义格式：

```md
---
title: {{ title }}
date: {{ date }}
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout: 气是清风肉是泥。
authorDesc: 佯狂难免假成真。
categories: xxx
comments: true
tags: 
keywords: 
description: 
photos: xxx
---
```



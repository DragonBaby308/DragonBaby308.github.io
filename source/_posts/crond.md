---
title: Crond定时任务
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/header/db3Avatar.jpeg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Linux
comments: true
date: 2019-10-28 22:37:32
tags:
- Linux
- ECS
- Crond
- 定时任务
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/posts/crond.jpg
---

#  `Crond`定时任务

* `Crond`是`Linux`下用来**周期性执行某种任务**或者**等待处理某些事件**的一个守护进程。
* 当安装了`OS`后默认会自动启动`Crond`进程，每分钟定期检查是否有需要执行的任务，如果有则自动执行。

> 参考[https://blog.csdn.net/qq_22172133/article/details/81263736](https://blog.csdn.net/qq_22172133/article/details/81263736)

---

##  （一）`Linux`下的任务调度

###  1.系统任务调度：`/etc/crontab`

`/etc`目录下的`crontab`文件是系统任务调度的配置文件，记录了系统周期性所执行的工作，比如写缓存数据到硬盘、日志清理等。如：

```bash
# 【Crond任务运行的环境变量】
# 指定使用哪个shell，比如bash
SHELL=/bin/bash
# 指定了系统执行命令的路径
PATH=/sbin:/bin:/usr/sbin:/usr/bin
# Crond任务执行信息将通过电子邮件发送给root用户
MAILTO="root"
# 执行命令或脚本时使用的主目录
HOME=/

# 【此部分是crontab文件，第二节介绍】
51 * * * * root run-parts /etc/cron.hourly
24 7 * * * root run-parts /etc/cron.daily
22 4 * * 0 root run-parts /etc/cron.weekly
42 4 1 * * root run-parts /etc/cron.monthly
```

###  2.用户任务调度

* `/etc/cron.deny`：**不允许使用`crontab`命令的用户**
* `/etc/cron.allow`：**允许使用`crontab`命令的用户**
* `/var/spool/cron/`：**所有用户`crontab`文件存放的目录,以用户名命名**

---

##  （二）`crontab`

用户所建立的`crontab`文件中，每一行都代表一个任务，每行的每个字段代表一项设置。

###  1.文件格式

```bash
minute hour day month week command
```

1. **`minute`：分钟**，`0 ~ 59`之间的整数
2. **`hour`：小时**，`0 ~ 23`之间的整数
3. **`day`：日期**，`1 ~ 31`之间的整数
4. **`month`：月份**，`1 ~ 12`之间的整数
5. **`week`：星期几**，`0 ~ 7`之间的整数，`0`/`7`表示星期日
6. **`command`：要执行的命令，可以是系统命令，也可以是自己写的脚本**

####  特殊字符

在上述字段中，可以使用特殊字符：
1. **`*`：星号 —— 表示所有可能的值**，比如`month`字段为`*`表示每个月；
2. **`,`：逗号 —— 用来指定一个范围列表**，比如`1,2,3,4,5,6,7`；
3. **`-`：中杠 —— 用在整数中间表示一个整数范围**，比如`2-6`表示`2,3,4,5,6`；
4. **`/`：正斜杠 —— 指定时间的间隔频率**，比如`hour`字段为`0-23/2`表示每隔`2`小时执行一次；`minute`字段为`*/10`表示`10`分钟执行一次。

![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/Linux/crond/crontab_01.jpg)

###  2.命令格式

```bash
crontab [-u user] fileName
crontab [-u user] [-e|-l|-r]
```

1. `-u user`：设定某个用户的`crontab`服务
2. `fileName`：`crontab`文件名
3. **`-e`：编辑某个用户的`crontab`文件内容，如果不指定用户，则编辑当前用户的**
> e for edit

4. **`-l`：显示某个用户的`crontab`文件内容，如果不指定用户，则显示当前用户的**
> l for list

5. **`-r`：删除某个用户的`crontab`文件内容，如果不指定用户，则删除当前用户的**
> r for remove

---

##  （三）`Crond`服务

1. 安装（一般不需要）

```bash
yum install crontabs
```

2. 启动/关闭/重启/重载服务

```bash
/sbin/service crond start   #启动
/sbin/service crond stop    #关闭
/sbin/service crond restart #重启
/sbin/service crond reload  #重载配置
```

3. 查看服务状态

```bash
service crond status
```

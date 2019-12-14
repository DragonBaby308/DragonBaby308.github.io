---
title: 分布式全局唯一ID
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: 分布式
comments: true
date: 2019-09-10 22:35:30
tags:
- 分布式
- MySQL
- Redis
- 雪花算法
- CAS
- JVM
- MyCat
- Java Base
- HTTP
- 高并发
- 分布式
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/posts/uid.jpg
---

#  分布式全局唯一ID

---

##  （一）UUID

缺点：
1. `UUID`太长
2. `UUID`无序

---

##  （二）`MySQL`自增ID

1. 需要单独一个`MySQL`实例，在这个实例新建一个单独的表

```sql
CREATE DATABASE `SEQID`;

CREATE TABLE SEQID.SEQUENCE_ID (
    id bigint(20) unsigned NOT NULL auto_increment,
    stub char(10) NOT NULL default '',
    PRIMARY KEY (id),
    UNIQUE KEY stub (stub)
) ENGINE=MyISAM;
```

> `stub`没有实际意义，只是为了**方便插入数据**，只有插入数据才能产生自增ID。

2. 可以使用下列语句生成并获取到一个自增ID

```sql
bigin;
replace into SEQUENCE_ID(stub) VALUES ('anyword');
select last_insert_id();
commit;
```

> `replace into`优于`insert into`的地方：  
**`replace into`会先看是否存在和`stub`指定值一样的数据，如果存在则先`delete`后`insert`，如果不存在则直接`insert`**。

###  缺点

1. **业务系统每需要一个ID，都会请求同一个数据库，单数据库压力大、性能低**；
2. **单点故障，一旦数据库宕机，整个系统都受影响**

###  优化

####  1.`MySQL`双主模式

1. 使用两台`MySQL`实例，做如下配置：

```bash
# 第一台MySQL实例
set @@auto_increment_offset = 1;    # 初始值
set @@auto_increment_increment = 2; # 步长

# 第二台MySQL实例
set @@auto_increment_offset = 2;    # 初始值
set @@auto_increment_increment = 2; # 步长
```

2. 分布式应用随机去两个`MySQL`实例获取ID

#####  优点

* **单`MySQL`实例宕机，另一台`MySQL`实例可以继续工作，不会影响整个系统，避免了单点故障**

#####  缺点

* **不利于扩容**

####  2.号段模式

1. 创建`MySQL`表，用于存储当前最大ID

```sql
CREATE TABLE id_generator (
    id int(10) NOT NULL,
    current_max_id bigint(20) NOT NULL COMMENT '当前最大ID',
    increment_step int(10) NOT NULL COMMENT '号段长度',
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

2. **应用程序从`MySQL`获取ID时，批量获取多个ID（称为一个“号段”）并进行缓存（可以是：①`JVM`缓存在本地 / ②`Redis`缓存）/③`MyCat`缓存。这样应用程序只需要在本地自增并返回，不需要每次请求数据库，只有缓存号段用完才去数据库获取下一号段。**
3. **可以在表中增加一个`version`字段，保证获取ID号段时的线程安全性**：

```sql
update id_generator set current_max_id=#{newMaxId},
version=version + 1 where version=#{version}
```

> **双主模式**和**号段模式**可以结合使用。

---

##  （三）雪花算法（`snow-flake`）

思路：
1. **保证生成分布式ID的每台机器在每`ms`内生成不一样的ID**。
2. 分布式ID固定是一个`long`类型的数字，一个`long`占`64bit`。由于`Long.MAX_VALUE = 2 ^ 63 - 1 = 9223372036854775807`，也就是说`long`类型最多`19`位，用一个`19`位的`String`就可以保存。
> `64 bit - snowflake id` = `1 bit - 标志位` + `41 bit - 时间戳` + `10 bit - 工作机器ID` + `12 bit - 序列号`  
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/DS/snowflake.jpg)

* 第一个`1 bit`是**标识部分**，在Java中由于`long`最高位是符号位，正数0，负数1，一般生成的ID都是正数，所以固定是0；
* **时间戳**部分占`41 bit`，是一个`ms`级的时间，一般不会存储当前时间，而是**时间戳差值（当前时间 - 固定开始时间）**，41位的时间戳可以使用69年（`(1 << 41) / (365 * 24 * 60 * 60 * 1000) = 69 年`）；
> 当前时间戳应该使用**应用启动时间对应的`ms`数原子递增**，不能使用`System.currentTimeMillis()`，因为后者如果机器时间回调，可能出现重复ID。

* **工作机器ID**占据`10 bit`，这部分比较灵活，比如**前5位作为数据中心机房标识，后5位作为单机房机器标识，可以部署1024个节点**
* **序列号部分占`12 bit`，支持同一ms内同一节点可以生成4096个ID。序列号可以从`MySQL`获取自增ID，可以参考上述的“`MySQL`序列号 + `JVM`/`Redis`/`MyCat`缓存”方案。**

---

##  （四）`Redis`

* 利用`Redis`的`incr`原子命令实现ID的自增和返回，但是**需要持久化**

```bash
# 设置ID初始值
set seq_id 1
# 自增 & 返回，是原子操作
incr seq_id
```

###  缺点

1. 如果使用的`RDB`持久化方式：**`RDB`持久化会丢失宕机期间到上一次快照存储期间的数据，所以可能出现重复ID**
2. 如果使用的`AOF`持久化方式：**不会出现重复ID，但是会由于`incr`命令太多导致重启数据恢复时间过长**

---

##  （五）应用：短URL（短网址）服务

> [百度短网址 —— https://dwz.cn/](https://dwz.cn/)：  
比如输入[`http://www.dragonbaby308.com/`](http://www.dragonbaby308.com/)，生成[`https://dwz.cn/SyQc17kp`](https://dwz.cn/SyQc17kp)，二者指向地址都是一样的。

###  优点

1. 短信或其他各类社交平台（比如微博）都会有字数限制，使用短链接可以**减少字数**
2. **比起一堆不明所以的参数，使用短链接更加简洁友好，同时不暴露参数会更安全**

###  原理

**`HTTP 301/302`重定向**：  
* 将长URL转为短URL
* 用户访问短URL，会通过`HTTP 301/302`重定向，访问到对应的长URL

###  长URL如何转短URL？

* 建立一个**发号器**，每次有一个新的长URL进来，就将短网址 + 1然后返回，**同时需要记录下长URL和短URL的映射关系（可以通过`MySQL`进行持久化存储）**。
> 比如第一个URL返回`www.dwz.cn/1`，  
第二个URL则返回`www.dwz.cn/2`

* 使用这种方式，**同一个长URL多次请求返回的短URL是不同的**
> 即便是[百度短网址](https://dwz.cn/)，也没有实现同一个长URL多次请求返回相同的短URL，因为这样的代价太大，并且没有什么意义。  
[百度短网址](https://dwz.cn/)会保存1年的映射关系。

####  存在的问题：

> 其实**发号器**究根结底，也就是一种 分布式唯一ID，分布式唯一ID中`MySQL`发号的方案存在的高并发、单点故障问题，短URL发号器也同样会有。

#####  1.如果直接存储在`MySQL`，高并发时库压力大怎么办？

1. **加一层`Redis`热点缓存**
2. 采用`MySQL`号段 + 缓存（`JVM`缓存/`Redis`缓存）的形式，**每次从数据库获取一批号段，在缓存中进行分配，缓存号段用完才去数据库请求新的号段。可以有效减少库压力**

#####  2.采用单一`MySQL`，出现单点故障怎么办？

1. 采用`MySQL`双主模式，比如一个`MySQL`发号器发单号，另一个发双号
2. 采用**雪花算法**

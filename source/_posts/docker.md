---
title: Docker 、 k8s 基础
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Docker
comments: true
date: 2019-07-01 13:21:30
tags:
- Docker
- MongoDB
- Redis
- Spring
- Maven
- k8s
- 架构
- 高可用
- 分布式
- REST
- 微服务
- 高可用
- etcd
- MySQL
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/docker.jpg
---

#  Docker 、 k8s 基础

---

##  （一）镜像（创建容器） & 容器（封装为镜像）

`镜像`：目的内容的一个封装，不可以改变内容。  
`容器`：可以改变内容，相当于虚拟机，默认情况下彼此封闭。  

一般我们做的，就是**通过镜像创建容器**；当我们改变了容器的内容之后，也可以重新**将容器封装为镜像**。

###  容器(Container) vs 虚拟机(VM)

`Docker`项目的目标是实现轻量级的操作系统虚拟化解决方案。  

####  区别

1. VM：VM是硬件层面上的虚拟化。一个VM包含了应用、必须的bin/lib库，同时**每个VM都必须要有Guest OS**，每个都高达数十MB。
2. 容器：容器是OS层面上的虚拟化。一个容器只包含了应用和必须的bin/lib库，**所有容器共享宿主机的Host OS，但是在独立的进程上运行，可以隔离性地使用系统资源**。
> 底层原理：LXC(Linux Container)。  
namespace：每个容器有各自的命名空间，用于逻辑隔离。  
cgroup：每个容器在Linux中有自己的分组和权限，可以用于互不干扰地访问系统资源。

####  容器的优点

1. 单个容器使用的是宿主机的OS，不包含自己的OS，所以容器要比VM更加**轻量级，开销更小，开发、测试、部署都更快，迁移也更方便**；
2. 容器的启动可以在秒级实现，比VM**快**得多；
3. 一台主机上可以运行数千个容器，对**系统资源的利用率**比VM高得多

---

##  （二）安装

`CentOS`下，`yum -y install docker`即可。  
我的版本是`Docker version 1.13.1, build b2f74b2/1.13.1`。

---

##  （三）使用

###  Docker服务的启动 & 停止：systemctl start/stop docker

启动：`systemctl start docker`  
停止：`systemctl stop docker`

###  Docker的镜像搜索：search

`docker search keyword`：根据关键字，在互联网搜索镜像。  
如：`docker search ubuntu`

###  Docker的镜像下载：pull

`docker pull imageName`：根据镜像名下载镜像。  
如：`docker pull docker.io/ubuntu`。  

###  Docker本地镜像查看：images

`docker images`：查看所有本地镜像

###  Docker容器的创建（基于镜像创建虚拟机） & 命名：run --name

`docker run -tid --name containerName imageID `：可以通过`docker images`查看`imageID`，一般取前4位即可。  
如：`docker run -tid --name DistributedCrawlerUbuntu 4c10`。
> docker run参数及其含义：
-p    指定端口号   
-ti   以交互的方式创建  
-d    后台创建  
-tid  创建一个容器并运行，但不进入容器里面  
-e    设置环境变量  
-p  宿主端口：容器端口  
-v          宿主目录：容器目录，挂载容器数据卷

###  Docker容器启动

`docker start containerID/containerName`：通过ID或者名称启动某个停止的容器

###  Docker容器的查看：ps -a

`docker ps -a`：查看当前所有运行的容器

###  Docker容器的进入：attach

`docker attach containerID`：可以通过`docker ps -a`查看`containerID`，一般取前4位即可。

###  在运行的容器中执行命令：docker exec

`docker exec -it ContainerName` + 命令，比如`docker exec -it MongoDB bash`。

###  Docker容器的不停止退出：ctrl + p + q + Enter

`ctrl + p + q`，按下回车，即可不停止容器但是退出。

###  基于容器封装为Docker镜像：commit

`docker commit containerID imageName:version`：将已有的容器封装为镜像，`containerID`可以通过`docker ps -a`获得。  
如：`docker commit e4cc distributed_crawler:v1`

---

##  （四）网络配置：run --link linkContainerName

`docker run -tid --name containerName --link linkContainerName iamgeID/Name`：在创建容器时，使其与某个容器保持网络通畅。  
如：

```shell
docker run -tid --name host1 distributed_crawler:v1                   # 创建host1
docker run -tid --name host2 --link host1 distributed_crawler:v1      # 创建host2时，link host1
```

---

##  （五）DockerFile

`DockerFile`是对`镜像`的源码级别的描述文件。

关键字如下：

```docker
FROM        基础镜像：当前镜像是基于哪个镜像的
MAINTAINER  镜像维护者的姓名和邮箱
RUN         容器构建时需要的命令
EXPOSE      当前容器对外暴露的端口
WORKDIR     创建容器后，终端默认登录进来的工作目录，一个落脚点
ENV         构建容器的过程中设置环境变量，如Java、MySQL……
ADD         将host目录下的文件拷贝进镜像，且ADD命令会自动处理URL和解压tar包
COPY        类似ADD，拷贝文件和目录到镜像
VOLUME      容器数据卷，用于数据的持久化
CMD         指定一个容器启动时需要运行的命令；DockerFile中可以有多个CMD命令，但只有最后一个会生效，且CMD会被docker run之后的参数替换
ENTRYPOINT  指定一个容器启动时需要运行的命令
ONBUILD     当构建一个被继承的DockerFile是运行命令，子镜像继承父镜像后，父镜像中的ONBUILD被触发
```

---

##  （六）容器数据卷(volume)

容器关闭后，数据需要`共享`和`持久化`，`容器数据卷`(volume)就是完成此功能的，它类似于`Docker`的移动硬盘。  

###  特点

1. 数据卷可以在容器之间共享或重用数据
2. 卷中的更改直接生效。不管是在host还是container中数据卷中修改了内容，另一方都能同步收到更新，**即使container已经退出也可以更新**。
3. 卷中的更改不会包含在镜像的更新中
4. 卷的生命周期一直持续到没有容器使用它为止

###  如何添加容器数据卷

1. 在`DockerFile`中的`VOLUME`属性下添加；
2. 直接命令添加：`docker run -it -v /hostPath:/containerPath imageName`。其中`hostPath`是宿主机绝对路径目录；`containerPath`是容器内目录；`imageName`是镜像名。
> 挂载Read-Only的容器数据卷：      docker run -it -v /hostPath:/containerPath:ro imageName  
如何查看已经挂载了哪些容器数据卷？   docker inspect containerID命令，查看Volumes属性下的对应关系

---

##  （七）Docker-Compose

> 参考[https://blog.51cto.com/9291927/2310444](https://blog.51cto.com/9291927/2310444)

###  简介

* `Docker-Compose`是`Docker`官方的开源项目，负责实现**对`Docker`容器集群的快速编排。**
* `Docker-Compose`的默认配置文件为`docker-compose.yml`
* **`Docker-Compose`允许用户通过一个`docker-compose.yml`文件来定义一组相关联的应用容器作为一个项目。**

###  安装

```bash
# 下载
sudo curl -L https://github.com/docker/compose/releases/download/1.23.0-rc3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# 更改权限
sudo chmod +x /usr/local/bin/docker-compose

# 查看版本
docker-compose version
```

###  `docker-compose.yml`文件格式

**包含`version`、`services`、`networks`三大部分：**  

`services`：
* `image`：**指定服务的镜像名称/ID，如果本地不存在，会尝试拉取**
* `command`：**覆盖容器启动后默认执行的命令**
* `ports`：**以`HOST:CONTAINER`的格式进行端口映射**
* `volumes`：**挂载数据卷容器**

```yml
version: '3'
services:
    elasticsearch:
        image: elasticsearch:7.3.1
        environment:
            discovery.type: single-node
        ports:
            - "9200:9200"
            - "9300:9300"
    logstash:
        image: logstash:7.3.1
        command: logstash -f /etc/logstash/conf.d/logstash.conf
        volumes:
            # 挂载logstash配置文件
            - ./config:/etc/logstash/conf.d
            - /user/elk/logs/:/opt/build/
        ports:
            - "5000:5000"
    kibana:
        image: kibana:7.3.1
        environment:
            -ELASTICSEARCH_URL=http://elasticsearch:9200
        ports:
            - "5601:5601"
```

###  `Docker-Compose`命令

* 启动：`docker-compose up`

---

##  （八）Demo

###  1.`MongoDB`

```bash
docker pull mongo
# 运行 ：-p 进行端口映射；-v 容器数据卷挂载目录；-e 配置环境变量；-d 后台运行
docker run --name MongoDB -p 27017:27017 -v ~/docker-data/mongo:/data/db -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=****** -d mongo
# 查看
docker ps -a
# 登录MongoDB
docker exec -it MongoDB bash
mongo -u root -p ******
# help
# show dbs
```

####  启动web监控

进入`MongoDB`容器后运行`db.enableFreeMonitoring()`会得到一个`url`，通过该`url`可以web健康你的`MongoDB`容器（不过贼鸡儿慢就是了……）：  
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/docker/mongo%20free%20monitor.png)

###  2.`Redis`

```bash
docker pull redis
docker run --name Redis -d -p 6379:6379 redis
```

###  3.`MySQL`主从

```bash
# 端口映射：master 3307；slave01 3308
docker run --name master -p 127.0.0.1:3307:3306 -e MYSQL\_ROOT\_PASSWORD=xxxxxx -d  mysql:5.6
docker run --name slave01 -p 127.0.0.1:3308:3306 -e MYSQL\_ROOT\_PASSWORD=xxxxxx -d --link master mysql:5.6
```

###  4.`RedisSearch`：高性能`Redis`全文搜索模块

```bash
docker pull redislabs/redisearch:latest
```

---

##  （九）Maven依赖

包括：`dockerfile-maven-plugin`、`maven-dependency-plugin`

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>dockerfile-maven-plugin</artifactId>
    <version>1.3.4</version>
    <configuration>
        <repository>${docker.image.prefix}/${project.artifactId}</repository>
    </configuration>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>unpack</id>
            <phase>package</phase>
            <goals>
                <goal>unpack</goal>
            </goals>
            <configuration>
                <artifactItems>
                    <artifactItem>
                        <groupId>${project.groupId}</groupId>
                        <artifactId>${project.artifactId}</artifactId>
                        <version>${project.version}</version>
                    </artifactItem>
                </artifactItems>
            </configuration>
        </execution>
    </executions>
</plugin>
```


---

##  （十）`Kubernetes`（`k8s`）

`Kubernetes`（简称`k8s`）是谷歌开源的**容器集群管理系统**，在`Docker`技术的基础上，为容器的应用提供**部署运行、资源调度（`Kube-proxy`）、服务发现（`Kublet`）和动态伸缩**等一系列完整功能，**提高了大规模容器集群管理的便捷性**。

###  `k8s`架构

![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/%E6%9E%B6%E6%9E%84/k8s.png)

* `Master Node`：**主节点，提供管理整个`k8s`集群的控制面板**；
* `API server`：**所有`REST`命令的入口，是主节点内唯一的用户可以访问的组件**；
* `Datastore`：**持续、高可用的K/V数据存储中心**；
* `Scheduler`：**监控新创建的容器组（`pod`），并将其加入节点中**；
* `Contoller manager`：**管理`k8s`集群内处理例行任务的控制器**；
* `Worker Node`：**运行容器组（`pod`），管理容器之间的网络，和`Master Node`交流**；
* `Docker`：**节点内的单独容器**；
* `Kublet`：**监控容器组（`pod`）状态，保证所有容器正常启动、运行，并且负责和`Datastore`交互**；
* `Kube-proxy`：**单个节点内的路由网关和负载均衡器**；
* `Kubectl`：**提供给用户的命令行工具，用于与`k8s` API服务器通信**
* `etcd`：**高可用、强一致性的服务发现存储仓库，一般是集群，采用的协议是[`Raft`](http://www.dragonbaby308.com/Transaction-Consistency/)，通过全局锁保证强一致性**

> [`k8s`集群规模限制 & 优化](http://www.dragonbaby308.com/artsk20190929/)

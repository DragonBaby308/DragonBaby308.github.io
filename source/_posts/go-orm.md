---
title: go-orm
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout: 气是清风肉是泥。
authorDesc: 佯狂难免假成真。
categories: Go
comments: true
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.4.5/img/posts/db3/go/go-orm.jpg
date: 2020-01-02 20:16:50
tags:
- Go
- ORM
- MySQL
- Beego
- bee
keywords:
description:
---

#  通过`Beego ORM`操作数据库

> [《Beego ORM官方文档》](https://beego.me/docs/mvc/model/overview.md)

---

##  （一）安装

* `go get github.com/astaxie/beego/orm`
* 如果你本来就需要使用`Beego`框架，那么直接`go get github.com/astaxie/beego`就可以了

---

##  （二）初始化

```go
package main

import (
    "fmt"
    "github.com/astaxie/beego/orm"
    _ "github.com/go-sql-driver/mysql" //导入MySQL驱动，并执行初始化函数
)

//结构体模型
type User struct {
    Id   int
    Name string `orm:"size(100)"`
}

//初始化
func init() {
    maxIdle := 30 //最大空闲连接
    maxConn := 30 //最大连接数
    //连接数据库
    orm.RegisterDataBase("default", "mysql", "root:password@/kynodesme?charset=utf8", maxIdle, maxConn)

    //注册模型
    orm.RegisterModel(new(User))

    //根据注册模型，创建表
    orm.RunSyncdb("default", false, true)
}
```

---

###  1.注册数据库驱动：`RegisterDriver()`

> 导入`DB Driver`时如果执行了`init()`，这一步就可以省略。

* 参数1：`driverName`
* 参数2：`driverName`对应的数据库类型，支持`MySQL`/`PostgreSQL`/`Sqlite3`

```go
orm.RegisterDriver("mysql", orm.DBMySQL)
```

---

###  2.连接数据库：`RegisterDataBase()`

* 参数1：数据库别名，**必须注册一个别名`default`的数据库，作为默认使用**
* 参数2：`driverName`
* 参数3：`user:passwd@/dbname?charset`
* 参数4：最大空闲连接数（可省略）
* 参数5：最大连接数（可省略）

```go
maxIdle := 30 //最大空闲连接
maxConn := 30 //最大连接数
orm.RegisterDataBase("default", "mysql", "root:password@/kynodesme?charset=utf8", maxIdle, maxConn)
```

---

###  3.注册模型：`RegisterModel()`

* 参数：结构体对象

```go
//结构体模型
// type User struct{
//   Id int
//   Name string
// }
orm.RegisterModel(new(User))
```

---

###  4.根据注册模型，创建表：`RunSyncdb()`

* 参数1：数据库别名
* 参数2：如果表已存在，是否删除后重建，一般建议`false`
* 参数3：是否显示编译好的`SQL`语句

```go
orm.RunSyncdb("default", false, true)
```

---

##  （三）`CRUD`

```go
func main() {
    o := orm.NewOrm()

    user := User{Name: "slene"}

    // insert，插入
    id, err := o.Insert(&user)
    fmt.Printf("ID: %d, ERR: %v\n", id, err)

    // update，更新
    user.Name = "astaxie"
    num, err := o.Update(&user)
    fmt.Printf("NUM: %d, ERR: %v\n", num, err)

    // read one，查询
    u := User{Id: user.Id}
    err = o.Read(&u)
    fmt.Printf("ERR: %v\n", err)

    // delete，删除
    num, err = o.Delete(&u)
    fmt.Printf("NUM: %d, ERR: %v\n", num, err)
}
```

---

##  （四）原生`SQL`：`Raw()`

```go
var maps []orm.Params //存储查询结果的map
num, err := o.Raw("SELECT * FROM user").Values(&maps)
for _,term := range maps{
    fmt.Println(term["id"],":",term["name"])
}
```

---

##  （五）事务

* `Begin()`：开启事务
* `Commit()`：提交事务
* `Rollback()`：回滚事务

```go
o.Begin()
...
user := User{Name: "slene"}
id, err := o.Insert(&user)
if err == nil {
    o.Commit()
} else {
    o.Rollback()
}
```
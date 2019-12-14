---
title: Go语言网络编程（TCP、HTTP、Beego框架）
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Go
comments: true
date: 2019-12-04 23:37:01
tags:
- Go
- TCP
- HTTP
- Goroutine
- 高并发
- 多线程
- Beego
- bee
- GitHub
- JSON
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/beego.png
---

#  `Go`语言网络编程（`TCP`、`HTTP`、`Beego`框架）

##  （一）`TCP`编程

涉及的包：`net`

###  1.服务端

1. `net.Listen()`监听某个端口
2. 循环获取连接，为每个连接开启一个`Goroutine`
3. 在单独的`Goroutine`中读取每个连接的数据
4. **关闭连接**

```go
package main

import (
	"fmt"
	"net"
	"strings"
)

func main() {
	fmt.Println("TCP服务端启动……")

	//监听50000端口
	listen, err := net.Listen("tcp", "0.0.0.0:50000")

	//错误处理
	if err != nil {
		fmt.Println("监听50000端口错误：", err)
		return
	}

	//开启循环，获取连接
	for {
		conn, err := listen.Accept()
		if err != nil {
			fmt.Println("获取连接错误：", err)
			return
		}
		//为每个连接开启一个Goroutine
		go process(conn)
	}
}

//连接处理函数
func process(conn net.Conn)  {
	//执行完关闭连接
	defer conn.Close()

	//开启循环，处理连接
	for {
		buf := make([]byte, 512)
		n, err := conn.Read(buf)
		if err != nil {
			fmt.Println("读取数据失败：", err)
			return
		}
		//打印接收到的数据
		fmt.Printf(strings.TrimSpace(string(buf[0:n])))
	}
}
```

###  2.客户端

1. `net.Dail()`连接服务端的某个端口
2. 收发数据
3. **关闭连接**

```go
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"strings"
)

func main() {
	//拨号，连接50000端口
	conn, err := net.Dial("tcp", "localhost:50000")
	if err != nil {
		fmt.Println("连接服务端失败：", err)
		return
	}

	//读取键盘输入
	inputReader := bufio.NewReader(os.Stdin)
	for {
		input, _ := inputReader.ReadString('\n')
		trimedInput := strings.Trim(input, "\r\n")
		//输入Q退出
		if trimedInput == "Q" {
			return
		}

		//将数据写入连接，发送给服务端
		_, err = conn.Write([]byte(trimedInput))
		if err != nil {
			return
		}
	}

	//执行结束后关闭连接
	defer conn.Close()
}
```

---

##  （二）`HTTP`编程

涉及的包：`net/http`

###  1.服务端

1. `http.HandleFunc()`：**指定某个函数处理对应路径下的请求**
> 对应的处理函数接收`w http.ResponseWriter, r *http.Request`两个参数。

2. `http.ListenAndServe()`：**监听某个端口的请求**

```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", Hello)
	if err := http.ListenAndServe("0.0.0.0:8080", nil); err != nil {
		fmt.Println("HTTP开启失败！")
	}
}

func Hello(w http.ResponseWriter, r *http.Request)  {
	fmt.Println("处理“localhost:8080/”路径下的请求")
	fmt.Fprintf(w, "Hello World")
}
```

###  2.客户端

1. `http.Get(url string) (resp *Response, err error)`
2. `http.Post(url, contentType string, body io.Reader) (resp *Response, err error)`
3. `http.Head(url string) (resp *Response, err error)`

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {
	response, err := http.Get("http://www.dragonbaby308.com/")
	if err != nil {
		fmt.Println("http.Get()错误：", err)
		return
	}

	data, err := ioutil.ReadAll(response.Body)
	if err != nil {
		fmt.Println("读取响应数据错误：", err)
		return
	}

	fmt.Println(string(data))
}
```

---

##  （三）`Beego` MVC框架

> 参考[《`Beego`开发文档》](https://beego.me/docs/intro/)

1. `go get github.com/astaxie/beego`下载`Beego`框架
2. 创建如下目录结构：

```text
beego
├─controller					：控制器层
	 ├─IndexController			：【/index控制器】
	 			├─index.go				
├─conf							：配置
	 ├─app.conf					：【默认配置文件】
├─main							：入口
	 ├─main.go					：【入口程序】
├─model							：模型层
├─router						：路由
	 ├─router.go				：【路由规则】
└─view							：视图层
	 ├─index
	 		├─index.html		：【/index模板】
```

3. `main/main.go`中导入`Beego`框架，通过`beego.Run()`运行：

```go
package main

import (
	"github.com/astaxie/beego"
	_ "router"		//调用router的init()
)

func main() {
	//运行Beego框架
	beego.Run()
}
```

4. `router/router.go`中创建路由规则：

```go
package router

import "github.com/astaxie/beego"

func init() {
	//路由规则：
	//1.URL路径
	//2.对应的控制器
	//3.对应处理函数
	beego.Router("/index", &IndexController.IndexController{}, "*:Index")
}
```

5. 在`controller/IndexController/index.go`中继承`Beego`的控制器，配置模板：

```go
package IndexController

import (
		"github.com/astaxie/beego"
		"github.com/astaxie/beego/logs"
)

//继承Beego的控制器
type IndexController struct {
	beego.Controller
}

//处理函数
func (p *IndexController) Index() {
	//打日志
	logs.Debug("进入IndexController")

	//1.【页面形式】
	//配置模板
	p.TplName = "index/index.html"

	//2.【API形式】
	//autorender=false，不再渲染页面，可以返回JSON类型的结果
	// m := make(map[string]interface{})
	// m["code"] = 200
	// m["msg"] = "success"
	// p.Data["json"] = m
	// p.ServeJSON(true)
}
```

6. `view/index/index.html`模板：

```html
<html>
	<body>
		<p>Hello World!</p>
	</body>
</html>
```

7. `Beego`**默认会解析**`conf/app.conf`配置文件：

```conf
# IP地址
httpaddr = "127.0.0.1"

# 应用名
appname = beego_demo

# 是否自动渲染模板，默认true，返回渲染后的页面；
# 如果想要返回API类型的JSON数据，将其改为false
autorender = true

# 运行时环境
runmode = "dev"

# 开发环境
[dev]
# 端口，默认9090
httpport = 9090

# 生产环境
[prod]
# 端口
httpport = 9091
```

---

###  `bee`工具

参考[《`bee` 工具简介》](https://beego.me/docs/install/bee.md)

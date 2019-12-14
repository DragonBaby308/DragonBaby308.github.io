---
title: Go语言入门
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Go
comments: true
date: 2019-11-20 23:03:51
tags:
- Go
- GC
- Homebrew
- Mac
- GitHub
- Git
- OS
- HTTP
- JSON
- 高并发
- Goroutine
- OS
- 协程
- 多线程
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/gakki/1.jpeg
---

#  Go语言入门

多学一门语言多条路吧，`C/C++`易学难精，`Go`语言**原生支持并发编程**，现在在**微服务服务端编程**中也越来越常用了，或许能给只会`Java`的我提供一个加入鹅厂的机会也说不准呢~  
PS：`PornHub`后端也是用的`Go`，你懂我的意思吧。
> 参考：[https://howistart.org/posts/go/1/](https://howistart.org/posts/go/1/)

`Go`语言应用：
1. [`Docker`](http://www.dragonbaby308.com/docker/)
2. 静态博客框架：`Hugo`

---

##  （〇）`Go`语言特点

1. **强类型语言，静态编译，不需要运行时进行类型推导，所以速度比解释型语言（如`python`、`php`...）快**
2. 存在`GC`，不需要程序员进行内存回收
3. 存在未引用变量和未引用包时会报错
4. `Go`语言所有编码都是`UTF-8`，不需要额外指定编码类型

---

##  （一）环境搭建

* [下载](https://studygolang.com/dl)并安装对应版本的`Go`语言包
* 查看版本：`go version`
* 查看环境：`go env`
> 其中`GOROOT="/usr/local/go"`熟悉就是安装目录。  
而`GOPATH`属性对应的就是`Go`的项目工作目录，该目录下会有`bin`、`pkg`、`src`三个子目录。  
`bin`：`Go`编译好的可运行程序存放地址；  
`pkg`：`Go`库源码文件；  
`src`：`Go`**命令源码文件、第三方类库存放地址，相当于根目录，建议将代码放在`src`目录下**

* 在用户目录下除了`GOROOT`文件夹外的地方新建一个文件夹，作为`GOPATH`的值，比如我是`/Users/dragonbaby308/go`，并创建`bin`、`pkg`、`src`三个子目录
* `vim ~/.bash_profile`，加：

```bash
GOROOT=/usr/local/go
export GOROOT
export GOPATH=/Users/dragonbaby308/go
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOBIN:$GOROOT/bin
```

* `source ~/.bash_profile`使之生效

###  编辑器

* `GoLand`：`JetBrains`旗下产品，体验自然没得说
* `Sublime Text`安装`GoSublime`插件
* 你要是够牛逼，直接用`vim`很炫酷，10个看了9个都会佩服

---

##  （二）源码文件

`Go`语言源码文件名称都是以`.go`为后缀，内容以`Go`语言代码组织的文件。

###  分类

1. 命令源码文件：声明自己属于`main`代码包，包含无参数声明和结果声明的`main`函数，是`Go`语言程序的**入口**；
> 每个程序只能有一个`main`包，只有声明了`package main`的才是命令源码文件，一个程序只允许一个`main()`。

2. 库源码文件
> 除了`main`的其他`package`下的都是库源码文件，编译后会在`pkg`目录下生成`.a`静态库文件。

3. 测试源码文件：名称以`_test.go`为后缀，其中至少有一个函数名称以`Test`/`Benchmark`为前缀，且该函数接受一个类型为`*testing.T`/`*testing.B`的参数。
> `Test`：功能测试；  
`Benchmark`：性能（基准）测试

---

###  单元测试小Demo

1. 新建`test`包，创建`calc.go`：

```go
package main

func add(a, b int) int {
  return a + b
}
```

2. 同目录下创建`calc_test.go`：
> 名称以`_test.go`为后缀，其中至少有一个函数名称以`Test/Benchmark`为前缀，且该函数接受一个类型为`*testing.T/*testing.B`的参数

```go
package main

func TestAdd(t *testing.T)  {
  r := add(2, 4)
  if r != 6 {
    t.Fatalf("add(2, 4) error, expect: %d, actual: %d", 6, r)
  }
  t.Logf("test add() success!")
}
```

3. 运行`calc_test.go`，或者在`test`目录下执行`go test -v`：

![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/GoLang/go%20test.png)

---

##  （三）基础命令

> `Go`语言的命令和`NPM`很相似。

###  1.`go run`：运行

* `go run`用于运行源码文件
* 只能接受**一个**命令源码文件以及若干个库源码文件作为文件参数
* 执行`go run`会先**编译**源码文件，存放在一个临时文件夹，然后再**运行**命令源码文件编译得到的**可执行文件**、库源码文件编译得到的**归档文件**
> **如果`go run`不加`-work`参数，会在命令执行完之前删除临时文件夹**

####  参数

* `-a`：**强制编译**相关代码，不论其编译结果是否已经是最新；
* `-n`：打印编译过程中所需要运行的命令，但是**不**真正执行；
* `-x`：打印编译过程中所需要运行的命令，并且真正执行；
* `-p n`：**并行编译**，其中`n`为并行的数量；
* `-v`：列出被编译的代码包的名称；
> `-a -v`：列出所有（不含标准库）被编译的代码包的名称

* `-work`：**显示编译时创建的临时工作目录的路径，并且在命令执行完后不删除临时工作目录**；

---

###  2.`go build`：编译

* 编译`.go`命令源码文件，在相同目录下得到一个可执行文件。
* `go build`不加参数，会将当前目录当作代码包进行编译。

####  参数

* `-race`：**如果存在资源竞争，运行时会打印在控制台**

---

###  3.`go install`：编译并安装

* `go install`不加参数，会将当前目录当作代码包进行编译安装。

---

###  4.`go get`：从远程仓库下载并安装

从远程仓库（`GitHub`/`GitLab`/`Gogs`）下载并安装代码包

####  参数

* `-d`：只执行下载动作，而不执行安装操作；
* `-fix`：在下载代码包后先执行**修正**操作，而后再进行编译和安装；
* `-n`：利用网络来**更新**已有的代码包及其依赖包
* `-x`：打印编译过程中所需要运行的命令，并且真正执行；

---

###  5.`ds`：查看目录结构

> `ds` for directory structure

---

###  6.`go test`：单元测试

####  参数

* `-v`：**打印详细信息**

---

##  （四）数据类型

###  1.关键字

* `package`：声明代码包
* `import`：导入代码包
> **通过`import _ "packagePath"`可以在导包时调用对应包的`init()`方法**！  
每个包都可以有自己的`init()`方法，在其中可以写一些**初始化资源**的默认方法。

* `func`：声明函数
* `var`：声明变量
* `const`：声明常量
* `map`：声明K/V对字典
* `chan`：声明通道
* `type`：定义数据类型
* `struct`：定义结构体
* `interface`：定义接口
* `go`：通过`go`关键字可以开启一个`Goroutine`
> `Goroutine`的本质是**协程**，你可以理解为轻量级的线程，很多个`Goroutine`可以映射到一个物理线程上。

* 程序流控：`select/break/case/continue/default/defer/else/fallthrough/for/goto/if/range/return/switch`

```go
package main

import (
  "fmt"
)

func main()  {
  fmt.Println("Hello World!")
}
```

---

###  2.变量`var` & 常量`const`

> 变量可以只声明不赋值，常量声明了就一定要赋值  
`Go`语言非常严格：**声明了变量就一定要使用，否则就会报错**

1. 单行赋值：`关键字 名称 类型 ( = 值)`/ `名称 := (值)`

```go
var var1 int = 1
var var0 //变量可以只声明不赋值
shortVar := (577)  //短变量声明，可以进行类型推断

const cosnt1 int = 1
```

2. 平行赋值

```go
var var2, var3 int = 2, 3
const const2, const3 int = 2, 3
```

3. 多行赋值

```go
var (
  var4 int = 4
  var5 int = 5
)

const (
  const4 int = 4
  const5 int = 5
)
```

---

###  3.数组

####  （1）数组的声明

```go
//声明长度为3的数组
type Number1 [3]int
var numbers2 [3]int //不赋值时数组初始值默认为0，即：[3]int{0, 0, 0}
```

####  （2）数组的赋值

```go
//声明定长数组时赋值
var numbers3 [3]int{1, 2, 3}
//声明不定长数组时赋值
var numbers4 [...]int{1, 2, 3}
```

####  （3）获取数组元素 & 数组长度（`len()`）

```go
//通过下标获取数组元素，从0开始
numbers3[0] = 4

//数组长度
var length = len(numbers3)
```

---

###  4.切片（不含长度的数组）

####  （1）切片的声明

* 声明：`type MySlice []int`
* 开辟空间：`make([]T, length)`，其中`length`为切片长度

```go
//定义长度为100的int类型的切片
type nums []int = make([]int, 100)

//可以简化为
nums := make([]int, 100)
```

> `slice := []int{}`定义的是**一个空切片，并不是为切片开辟空间**！！！

> `Golang`二维切片赋值是真TMD反人类(￣_￣|||)

####  （2）从数组获取切片

`var sliceName = arrayName[sIndex, eIndex]`：获得的切片是对应数组的一个`[sIndex, eIndex)`的子数组（**不包含结束位置元素**）

```go
//数组
var numbers = [5]int{1, 2, 3, 4, 5}
//通过切片表达式获取切片
var slice1 = numbers[1:4] //相当于[3]int{2, 3, 4}
//获取切片的切片
var slice2 = slice1[1:3]  //相当于[2]int{3, 4}
```

####  （3）从切片获取子切片

```go
sub1 := slice[i:j]  //i到j，不包括j
sub2 := slice[i:]   //i到尾部
sub3 := slice[:j]   //头部到j，不包括j
sub4 := slice[:]    //相当于复制整个切片
```

####  （4）切片的长度 & 容量（`cap()`）

* 切片的长度：`eIndex - sIndex`
* 切片的容量：`len - sIndex`，其中`len`是**切片来源数组的长度**
* 未赋值的切片默认容量为`nil`

```go
//capacity = len - 1
//len为来源数组长度，对于切片的子切片来说，是其父切片的容量，即cap(slice1)
//cap(slice1) = len(numbers) - 1 = 4
//capacity = cap(slice1) - 1 = 3
var capacity int = cap(slice2)  //值为3
```

####  （5）添加元素：`append()`

* `append(slice []Type, elems ...Type) []Type`

```go
var numbers2 []int
numbers2 = append(numbers2, 0)
```

---

###  5.字典

> `Go`语言的`map`是**无序**的！业务如果需要有序需要自己实现

格式：`map[K]V`

```go
mm := map[int]string{1:"a", 2:"b", 3:"c"}
b := mm[2]
//删除：有则删除，没有则无视
delete(mm, 4)
//对于字典值来说，如果其中不存在索引表达式欲取出的键值对，那么就以它的值类型的空值（或称默认值）作为该索引表达式的求值结果
e := mm[5]  
//e的值一定是空字符串""，那么我们怎么知道它是本来就为空，还是不存在该索引值呢？
//字典的索引表达式可以有两个结果，第二个结果是bool类型的，表明该键是否存在
e, ok := mm[5]

//map的value声明成空接口类型，说明value可以是任何类型
var m map[string]interface{}
//通过make为map初始化
m = make(map[string]interface{})
//赋值
m["username"] = "user2"
```

---

###  6.通道【`Go`并发编程的基础】

> 参考[《`Go`语言并发编程》](http://www.dragonbaby308.com/goroutine/)

* 通道（Channel）的一种特殊的数据结构，**用于在不同`Goroutine`之间传递类型化的数据，并且是并发安全的**。
> `Goroutine`（也称为`Go`程序，本质上是[协程](http://www.dragonbaby308.com/multithread/)）可以被看成是承载可被**并发执行**的代码块的载体，它们**由`Go`语言的运行时系统调度，并且依托于操作系统线程来并发执行其中的代码**。  
![Go运行时调度机制](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/GoLang/goroutine.jpg)

* 声明通道：`chan T`，如`chan int`
* 赋值：`make(chan T, length)`，其中`length`是通道的长度，如`make(chan int, 5)`
> `length`可以省略，即`make(chan T)`

* 接收/发送数据：`<-`
* 关闭通道：`close()`
> **重复关闭通道/向已关闭的通道发送数据，都会造成异常**！

```go
//赋值
channel := make(chan string, 5)
ch := make(chan int)
//发送数据到通道
channel <- "value1"
//从通道接收数据，第二个值是bool类型的，true代表通道值有效
value, ok := <- channel
//关闭通道
close(channel)
```

---

###  7.函数

* `Go`语言中函数是一等公民，可以作为值来传递和使用，参考[`Java8`](http://www.dragonbaby308.com/java-8-in-action/)。
* `Go`语言中的函数可以返回**多个**结果。
* 函数类型声明：`type MyFunc func(入参列表) (出参列表)`，如：

```go
//如果出参只有一个，可以省略括号
//出参中的参数名称也是可以省略的，只保留参数类型
type EmployeeIdGenerator func(company string, department string, number uint32) string
```

* 函数声明：`func functionName(入参列表) (出参列表){}`，如：

```go
//默认公司名称
var company = "Tencent or PornHub"
//序列号
var number uint32

func generateId(generator EmployeeIdGenerator, department string) (string, bool){
  if generator == nil {
    return "", false
  }
  //使用代码包sync/atomic中提供的原子操作，保证并发操作安全性
  newNumber := atomic.AddUint32(&number, 1)
  return generator(company, department, newNumber), true
}
```

* **可以简单地认为：函数名大写是`public`函数，可以在包外被访问到；函数名小写是`private`函数，不能在包外被访问到**

---

###  8.结构体

* 定义结构体：`tpye StructName struct{若干字段声明}`；

```go
//定义结构体
type Person struct{
  Name string
  Gender string
  Age uint8
}

//创建结构体
Person{Name:"Robert", Gender:"Male", Age:30}
//如果键值对的顺序与其类型中的字段声明完全相同的话，我们还可以统一省略掉所有字段的名称
p := Person{"Lee Tang", "Male", 20}
```

> 每个字段声明后可以带**标签**，参考第六节的Demo

####  结构体的方法

```go
func (person *Person) Grow() {
  person.Age++
}

func (person *Person) Move(newAddress string) string {
    old := person.Address
    person.Address = newAddress
    return old
}

//调用结构体的方法
p.Grow()

//通过指针创建结构体
person := &Person{
  Name: "阿里",
  Gender: "男",
  Age: 20,
}
```

---

###  9.接口

* 定义接口：`tpye InterfaceName interface{若干方法声明}`；

```go
type Animal interface{
  Grow()
  Move(string) string
}
```

* 如果一个数据类型所拥有的方法集合中包含了某一个接口类型中的所有方法声明的实现，那么就可以说这个数据类型实现了那个接口类型。

```go
//builtin.go中的error接口
type error interface {
  Error() string
}

//定义一个结构体
type PathError struct{
  path string
  op string
  time string
  msg string
}

//使用该结构体的指针，实现某个接口的全部方法，就可以认为是实现了该接口
func (p *PathError) Error() string {
  return fmt.Sprintf("自定义异常：\n path=%s\n op=%s\n time=%s\n msg=%s\n", p.path, p.op, p.time, p.msg)
}
```

* **如果想让一个方法能够接收/返回任何类型的参数，就将参数声明为空接口**：`v interface{}` —— 因为**任何类型都可以是空接口**

---

###  10.指针

* `&`：取址，当`&`应用到一个值上会取出指向该值的指针值
> **凡是需要改变一个对象的内存地址的，都需要使用`&`取址**。  
比如说，改变一个`map`中某个`key`的`value`不需要`&`，但是**反序列化时为一个新的`map`分配内存空间就需要用到`&`取址**，见（七）-3-（2）-①~③

* `*`：取值，当`*`应用到一个指针值上会取出该指针指向的值

---

###  11.错误

####  （1）`err`错误

一般来说可能返回错误的函数，通过一个`err`对象接收后直接打印或使用即可。

####  （2）简单字符串错误

首先看下`Go`语言中简单字符串错误的类库`errors.go`源码：

```go
package errors

func New(text string) error {
  //返回一个错误信息结构体，使用的是传入的字符串
  return &errorString{text}
}

//错误信息结构体，只包含一个字符串
type errorString struct{
  s string
}
```

所以说想要自定义简单字符串错误，使用`errors.New(text string)`即可：

```go
err1 := errors.New("简单字符串错误")
fmt.Println(err1)
//输出：
//简单字符串错误
```

####  （3）接口错误

`builtin.go`类库下存在`error`接口，源码如下：

```go
package builtin

type error interface{
  Error() string
}
```

通过实现`error`接口即可实现自定义接口错误，在`Go`中，实现了一个接口的全部方法就可以认为是实现了该接口：

```go
//定义结构体
type PathError struct {
  path string
  op string
  time string
  msg string
}

//实现error接口
func (p *PathError) Error() string {
  return fmt.Sprintf("自定义错误：\n path=%s\n op=%s\n time=%s\n msg=%s\n", p.path, p.op, p.time, p.msg)
}

//OpenFile函数，异常时返回一个PathError结构体，实现了error接口
func OpenFile(filePath string) error {
  file, err := os.Open(filePath)
  if err != nil {
    return &PathError{
      path: filePath,
      op:   "read",
      time: time.Now().String(),
      msg:  err.Error(),
    }
  }
  defer file.Close()
  return nil
}

func main() {
  err2 := OpenFile("C:/faaaaaa/gaaaaa/donotexistfile.txt")
  if err2 != nil {
    fmt.Println(err2)
  }
}
//输出：
// 自定义错误：
//  path=C:/faaaaaa/gaaaaa/donotexistfile.txt
//  op=read
//  time=2019-12-03 15:28:14.7397259 +0800 CST m=+0.001979701
//  msg=open C:/faaaaaa/gaaaaa/donotexistfile.txt: The system cannot find the path specified.
```

#####  `switch`判断错误类型

```go
switch v := err.(type) {
case *PathError:
  fmt.Println("PathError：", v)
default:
  fmt.Println(v)
}
```

---

###  12.异常`panic()` & 捕获`recover()`

```go
package main

import "fmt"

func main() {
  fmt.Println("发生异常前")

  //声明异常捕获方法
  defer func() {
    if err := recover(); err != nil {
      fmt.Println("异常捕获：", err)
    }
  }()

  //可能抛出异常的方法
  Panic()

  fmt.Println("发生异常后")
}

//抛出异常的方法
func Panic() {
  var x = 30
  var y = 0
  //自定义异常
  panic("抛出自定义异常！")
  var c = x / y
  fmt.Println(c)
}
//输出：
// 发生异常前
// 异常捕获： 抛出自定义异常！
```

---

##  （五）流程控制

###  1.`if`

```go
//else if 和 else可以没有
if 100 > number {
  number += 3
} else if 100 < number {
  number -= 2
} else {
  fmt.Println("100 hun!")
}
```

####  `if`语句的初始化子句

* 我们可以**在`if`子句中为变量赋值**，不需要单独赋值
* 初始化子句要**放在`if `和`; 条件表达式`中间**
* **初始化子句中定义的变量在`if`语句外是不可见的，除非在`if`语句外进行了声明**

```go
//number的作用域只在if内
if number := 4; 100 > number {
  //...
}
```

---

###  2.`switch`

```go
names := []string{"Golang", "Java", "Rust", "C"}

//switch也可以有初始化子句
switch name := names[0]; name {
case "Golang":
  fmt.Println("Tencent/PornHub")
case "Java":
  fmt.Println("Alibaba")
default:
  fmt.Println("回家喂猪是一样的")
}
```

---

###  3.`for`

```go
for i := 0; i < 10; i++ {
  fmt.Print(i, " ")
}

//for-range语句
for i, v := range "fxxk" {
  fmt.Printf("%d: %c\n", i, v)
}
// 0: f
// 1: x
// 2: x
// 3: k

//Go语言中声明了i就一定要使用，如果不想打印，可以使用_代替
for _, v := range "fxxk" {
  fmt.Printf("%c\n", v)
}
//f
//x
//x
//k

//死循环
for {
  fmt.Println("死循环")
}
```

---

###  4.`select`：用于选择不同通道接收数据

```go
ch1 := make(chan int, 1)
ch2 := make(chan int, 1)
select {
case e1 := <-ch1:
    fmt.Printf("1th case is selected. e1=%v.\n", e1)
case e2 := <-ch2:
    fmt.Printf("2th case is selected. e2=%v.\n", e2)
default:
    fmt.Println("No data!")
}
```

---

###  5.`defer`：方法结束前执行

```go
func readFile(path string) ([]byte, error) {
    file, err := os.Open(path)
    if err != nil {
        return nil, err
    }
    //关闭文件
    defer file.Close()
    return ioutil.ReadAll(file)
}
```

---

##  （六）网络应用Demo

> 参考[《`Go`语言网络编程》](http://www.dragonbaby308.com/go-web/)

1. 创建`main.go`

```go
package main

import (
  "net/http"      //生产级别的HTTP服务器，具备抵御常见攻击的能力
  "encoding/json" //JSON格式解析
  "strings"       //处理字符串的库
)

func main()  {
  //让hello函数处理来自"/hello"的请求
  //每当一个新请求进入到与路径匹配的HTTP服务器时，服务器将生成一个执行hello()的新goroutine
  http.HandleFunc("/hello", hello)
  //让weather函数处理来自"/weather"的请求
  //每当一个新请求进入到与路径匹配的HTTP服务器时，服务器将生成一个执行weather()的新goroutine
  //使用匿名函数
  http.HandleFunc("/weather/", func(w http.ResponseWriter, r *http.Request)  {
    city := strings.SplitN(r.URL.Path, "/", 3)[2]
    //Go中的函数可以接收两个返回值，一般都会通过err判断是否发生异常
    data, err := query(city)
    //异常处理
    if err != nil {
      http.Error(w, err.Error(), http.StatusInternalServerError)
      return
    }
    //设置HTTP响应头
    w.Header().Set("Content-Type", "application/json; charset=utf-8")
    //JSON格式化
    json.NewEncoder(w).Encode(data)
  })

  //在8080端口启动服务器
  http.ListenAndServe(":8080", nil)
}

//hello函数，用于处理来自"/"的请求
//http.ResponseWriter：用于发送响应报文到客户端，参数为byte类型的切片
func hello(w http.ResponseWriter, r *http.Request) {
  w.Write([]byte("hello!"))
  //如果觉得使用byte数组不直观，可以通过fmt.Fprintf()进行输出
  //fmt.Fprintf(w, "hello!")
}

//query函数
//回参可以省略参数名，只保留类型，weatherData是一个结构体类型
func query(city string) (weatherData, error) {
  //API_KEY需要申请
  resp, err := http.Get("http://api.openweathermap.org/data/2.5/weather?APPID=YOUR_API_KEY&q=" + city)
  //异常处理
  if err != nil {
    return weatherData{}, err
  }
  //延迟处理函数，用于关闭资源
  defer resp.Body.Close()
  var d weatherData
  //if初始化子句，通过&d指针给结构体塞值，err只有发生了异常才会有值
  if err := json.NewDecoder(resp.Body).Decode(&d); err != nil {
    return weatherData{}, err
  }
  return d, nil
}

//天气数据结构体
type weatherData struct {
  Name string `json:"name"` //在字段后加标签，方便进行JSON解析
  Main struct {
    Kelvin float64 `json:"temp"`
  } `json:"main"`
}
```

2. 运行：`go run`
3. 测试：

```go
curl http://localhost:8080/hello
//输出：hello!

curl http://localhost:8080/weather/tokyo
//输出：{"name":"Tokyo","main":{"temp":295.9}}
```

---

##  （七）常用的库

> 已经使用过的`fmt`、`net/http`、`strings`不再记录。

###  1.`sort`：排序

1. `sort.go`：

```go
package main

import (
  "sort"
  "fmt"
)

func main()  {
  a := []int{3, 6, 2, 1, 9, 10, 8}
  //sort.Ints()，排序int类型切片/数组
  sort.Ints(a)

  for _, v := range a {
    fmt.Println(i, v)
  }
}
```

2. 输出：

```text
1
2
3
6
8
9
10
```

---

###  2.`time`：时间

####  （1）停顿：`time.Sleep()`

```go
import "time"

func main()  {
  time.Sleep(1*time.Millisecond)  //停顿1ms
  time.Sleep(5*time.Second)       //停顿5s
}
```

####  （2）一次性计时器：`time.After()`

* 一次性计时器触发后会被`GC`，但是不保证何时回收，如果对性能要求高的话，**不推荐使用`time.After`，应该用`time.NewTicker()`取代，手动关闭**；
* 一次性计时器可以用于**超时控制**：

```go
import "time"

func main()  {
  //1s后触发一次
  select {
  case v := <- ch: fmt.Println("正常情况")
  //不推荐使用！
  case <- time.After(time.Second) : fmt.Println("超时1s")
  }
}

```

####  （3）定时器：`time.NewTicker()`

定时器也可以用于**超时控制**，但是**一定要关闭定时器，否则可能造成内存泄漏**：

```go
import "time"

func main()  {
  //每过1s触发一次
  t := time.NewTicker(time.Second)

  //定时器也可以用于超时控制
  select{
  case v := <- ch: fmt.Println("正常情况")
  //t.C是一个时间的通道
  case <- t.C : fmt.Println("超时1s")
  }

  //每过1s打印一次
  for v := range t.C {
    fmt.Println("hello", v)
  }

  //关闭定时器
  t.Stop()
}
//输出：
// 超时1s
// hello 2019-12-04 14:39:27.2520634 +0800 CST m=+2.005214701
// hello 2019-12-04 14:39:28.251665 +0800 CST m=+3.004824301
// hello 2019-12-04 14:39:29.2532157 +0800 CST m=+4.006383001
//……
```

---

###  3.`encoding/json`

####  （1）序列化

1. `NewEncoder(w io.Writer).Encode(v interface{})`
> 参考[（六）网络应用Demo](http://www.dragonbaby308.com/go/#%EF%BC%88%E5%85%AD%EF%BC%89%E7%BD%91%E7%BB%9C%E5%BA%94%E7%94%A8Demo)

2. `json.Marshal(v interface{}) ([]byte, error)`：传入任何类型的参数，转换为`byte[]`类型的`JSON`数组

#####  ①结构体序列化

```go
package main

import (
  "encoding/json"
  "fmt"
)

//定义用户结构体
type User struct {
  UserName string `json:"user_name"`
  NickName string `json:"nick_name"`
  Age int //留一个不加标签，方便看标签的作用
  Birthday string `json:"birthday"`
  Sex string `json:"sex"`
  Email string `json:"e-mail"`
  Phone string `json:"phone"`
}

func main() {
  //创建结构体对象
  user := &User{
    UserName: "user001",
    NickName: "你弟含王",
    Age:      14,
    Birthday: "1988-08-08",
    Sex:      "男",
    Email:    "741@qq.com",
    Phone:    "741741741",
  }
  jsonBytes, err := json.Marshal(user)
  if err != nil {
    fmt.Printf("json.Marshal err: ", err)
  }
  fmt.Printf("%s\n", jsonBytes)
}
//输出：
//{"user_name":"user001","nick_name":"你弟含王","Age":14,"birthday":"1988-08-08","sex":"男","e-mail":"741@qq.com","phone":"741741741"}
//可以看到没有打标签的，JSON序列化时key就是字段名，打了标签则key是标签名
```

---

#####  ②字典序列化

```go
package main

import (
  "encoding/json"
  "fmt"
)

func main() {
  //定义[string, interface{}]类型的map
  var m map[string]interface{}
  //初始化
  m = make(map[string]interface{})
  m["username"] = "user002"
  m["age"] = 18

  jsonBytes, err := json.Marshal(m)
  if err != nil {
    fmt.Printf("json.Marshal err: ", err)
  }
  fmt.Printf("%s\n", jsonBytes)
}
//输出：
//{"age":18,"username":"user002"}
```

---

#####  ③切片序列化

```go
package main

import (
  "encoding/json"
  "fmt"
)

func main() {
  //切片由[string, interface{}]的map组成
  var s []map[string]interface{}

  //定义并给map赋值
  var m map[string]interface{}
  m = make(map[string]interface{})
  m["nickname"] = "小老弟"
  m["address"] = "聚宝山庄"

  //将map添加到切片
  s = append(s, m)

  jsonByteArrays, err := json.Marshal(s)
  if err != nil {
    fmt.Printf("json.Marshal() err: ", err)
    return
  }
  fmt.Printf("%s\n", string(jsonByteArrays))

}
//输出：JSON数组
//[{"address":"聚宝山庄","nickname":"小老弟"}]
```

---

####  （2）反序列化

1. `NewDecoder(r io.Reader).Decode(v interface{})`
> 参考[（六）网络应用Demo](http://www.dragonbaby308.com/go/#%EF%BC%88%E5%85%AD%EF%BC%89%E7%BD%91%E7%BB%9C%E5%BA%94%E7%94%A8Demo)

2. `json.Unmarshal(data []byte, v interface{}) error`：**第二个参数传入的要是`&`指针引用**

#####  ①结构体反序列化

```go
package main

import (
  "encoding/json"
  "fmt"
)

//定义用户结构体
type User struct {
  UserName string `json:"user_name"`
  NickName string `json:"nick_name"`
  Age int //留一个不加标签，方便看标签的作用
  Birthday string `json:"birthday"`
  Sex string `json:"sex"`
  Email string `json:"e-mail"`
  Phone string `json:"phone"`
}

func main() {
  //创建结构体对象
  user := &User{
    UserName: "user001",
    NickName: "你弟含王",
    Age:      14,
    Birthday: "1988-08-08",
    Sex:      "男",
    Email:    "741@qq.com",
    Phone:    "741741741",
  }
  //获取转换后的JSON字节数组
  jsonBytes, err := json.Marshal(user)
  if err != nil {
    fmt.Printf("json.Marshal err: ", err)
  }

  //反序列化
  var userResult User
  //结构体不是引用类型，所以此处传入的必须是指针引用，否则会报错
  err = json.Unmarshal(jsonBytes, &userResult)
  if err != nil {
    fmt.Println("json.Unmarshal err： ", err)
  }else {
    fmt.Println(userResult)
    fmt.Println(userResult.Age)
  }
}
//输出：
//{user001 你弟含王 14 1988-08-08 男 741@qq.com 741741741}
//14
```

---

#####  ②字典反序列化

```go
package main

import (
  "encoding/json"
  "fmt"
)

func main() {
  //序列化
  var m map[string]interface{}
  m = make(map[string]interface{})
  m["username"] = "user002"
  m["age"] = 18
  jsonBytes, err := json.Marshal(m)
  if err != nil {
    fmt.Println("json.Marshal err: ", err)
  }

  //反序列化
  var mapResult map[string]interface{}
  mapResult = make(map[string]interface{})
  //传入的需要是&指针引用
  err = json.Unmarshal(jsonBytes, &mapResult)
  if err != nil {
    fmt.Println("json.UnMarshal err: ", err)
  }
  fmt.Println(mapResult)
}
//输出：
//map[age:18 username:user002]
```

---

#####  ③切片序列化

```go
package main

import (
  "encoding/json"
  "fmt"
)

func main() {
  //序列化
  var s []map[string]interface{}
  var m map[string]interface{}
  m = make(map[string]interface{})
  m["nickname"] = "小老弟"
  m["address"] = "聚宝山庄"
  s = append(s, m)
  jsonByteArrays, err := json.Marshal(s)
  if err != nil {
    fmt.Printf("json.Marshal() err: ", err)
    return
  }

  //反序列化
  var sliceResult []map[string]interface{}
  err = json.Unmarshal(jsonByteArrays, &sliceResult)
  if err != nil {
    fmt.Printf("json.UnMarshal() err: ", err)
  }
  fmt.Println(sliceResult)

}
//输出：
//[map[address:聚宝山庄 nickname:小老弟]]
```

---

###  4.`runtime`：获取`Go`运行时环境

```go
import runtime

//获取运行时CPU核心数
cpus := runtime.NumCPU()
fmt.Println(cpus)

//获取运行中的Goroutine数
gos := runtime.NumGoroutine()
fmt.Println(gos)
```

---

###  5.`sync`：同步

####  （1）`sync.Mutex`：互斥锁

```go
//定义互斥锁
var lock sync.Mutex

//获取锁
lock.Lock()

//同步代码块……

//释放锁
lock.Unlock()
```

####  （2）`sync.RWMutex`：读写锁

**适合读多写少的情况**：

```go
//定义读写锁
var lock sync.RWMutex

//获取写锁
lock.Lock()

//同步代码块……

//释放写锁
lock.Unlock()

//获取读锁
lock.RLock()
//释放读锁
lock.RUnlock()
```

---

###  6.`ioutil`：I/O工具类

* `ioutil.ReadAll(r io.Reader) ([]byte, error)`：传入一个`io.Reader`，以字节数组的形式返回其中内容

```go
response, err := http.Get("http://www.dragonbaby308.com/")
if err != nil {
  fmt.Println("http.Get()错误：", err)
  return
}

//Body io.ReadCloser
data, err := ioutil.ReadAll(response.Body)
```

---

###  7.`math`：数学库

####  比较`float64`类型的大小  

> 由于`float64`类型的数据具备**精度**的概念，开发者不具备精确比较两个`float64`类型数据的能力，所以`Golang`提供了库函数；  
但是对于其他的`int`类型数据，由于大小比较实现起来并不难，`Golang`为了保证语言的尽可能精炼，**不提供比较两个`int`类型数据大小的库函数，需要开发人员自己实现**（而且`Golang`还不支持`?:`的三元运算符，反正我觉得是挺没道理的━┳━　━┳━

* `math.Min(x, y float64) float64`：两数小者
* `math.Max(x, y float64) float64`：两数大者

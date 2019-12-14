---
title: Go语言并发编程
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Go
comments: true
date: 2019-11-22 22:48:10
tags:
- Go
- 高并发
- Goroutine
- OS
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/gakki/3.gif
---

#  `Go`语言并发编程

* 采用`CSP`（Communication Sequential Process）模型
* 编程时不需要锁，不需要`callback`
* 适合分布式

---

##  （〇）`Go`运行时调度机制

![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/GoLang/goroutine.jpg)

---

##  （一）并发版Hello World

> `Go`并发编程的核心就是`go`和[`chan`](http://www.dragonbaby308.com/go/#6-%E9%80%9A%E9%81%93%E3%80%90Go%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E7%9A%84%E5%9F%BA%E7%A1%80%E3%80%91)，也即`Goroutine`和通道。

1. 创建`helloworldconc`目录，创建`conc.go`：

```go
package main
//Go语言中package后的目录名可以和实际目录不一致，main表明该文件是个可执行文件

import(
  "fmt"
)

func main()  {
  //:=变量声明，并赋初值
  ch := make(chan string)   //定义管道

  for i := 0; i < 5; i++ {
    //通过go关键字可以开启一个Goroutine
    go printHelloWorld(i, ch)
  }

  //从通道获取消息
  for {
    msg := <- ch
    fmt.Println(msg)
  }

}

func printHelloWorld(i int, ch chan string)  {
  for {
    //如果使用了像%d一样的format参数，就需要用到Printf，不能用Println
    ch <- fmt.Sprintf("Hello World from Goroutine %d\n", i) //将字符串送入管道
  }
}
```

2. `go run conc.go`，不断循环输出：

```text
Hello World from Goroutine 3
Hello World from Goroutine 4
Hello World from Goroutine 1
Hello World from Goroutine 2
Hello World from Goroutine 0
```

---

##  （二）并行计算

1. 新建目录`pipeline`
2. 创建`nodes.go`，不包括`main`函数：

```go
package pipeline

import "sort"

///...表示可变长参数
//<-表明这个返回的通道是只出不进的
func ArraySource(a ...int) <-chan int {
  //创建通道
  out := make(chan int)

  //创建Goroutine，将数组中的元素输送到通道
  go func() {
    for _, v := range a {
      out <- v
    }
    //关闭通道
    close(out)
  }()

  return out
}

//排序单个通道
func InMemSort(in <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    a := []int{}
    //将输入通道中的数据加入切片中
    for v := range in {
      a = append(a, v)
    }
    //排序
    sort.Ints(a)
    //输出
    for _, v := range a {
      out <- v
    }
    //关闭通道
    close(out)
  }()
  return out
}

//归并
func Merge(in1, in2 <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    v1, ok1 := <-in1
    v2, ok2 := <-in2
    for ok1 || ok2 {
      if !ok2 || (ok1 && v1 <= v2) {
        out <- v1
        v1, ok1 =  <-in1
      } else {
        out <- v2
        v2, ok2 =  <-in2
      }
    }
    close(out)
  }()
  return out
}
```

3. 创建可执行文件`main.go`：

```go
package main

func main() {
  p := pipeline.Merge(
    pipeline.InMemSort(pipeline.ArraySource(3, 2, 6, 7, 4)),
    pipeline.InMemSort(pipeline.ArraySource(7, 4, 0, 3, 2, 8, 13)))
  for {
    //通过if子句，从通道获取数组元素
    if num, ok := <- p; ok {
      fmt.Println(num)
    } else {
      break
    }
    //等价于：
    // for v := range p {
    //   fmt.Println(v)
    // }

  }
}
```

4. 输出：

```text
0
2
2
3
3
4
4
6
7
7
8
13
```

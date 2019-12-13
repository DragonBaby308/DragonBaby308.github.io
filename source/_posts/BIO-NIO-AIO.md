---
title: BIO / NIO / AIO
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/header/db3Avatar.jpeg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: Java基础
comments: true
date: 2019-08-16 19:50:20
tags:
- BIO
- NIO
- Java Base
- 多线程
- 线程池
- Future
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/posts/nio.jpg
---

#  BIO & NIO & AIO

##  （〇）同步vs异步、阻塞vs非阻塞

###  同步vs异步

1. 同步：**发起一个调用后，被调用者未处理完请求之前，调用不返回**
2. 异步：**发起一个调用后，立即收到被调用者的回应表示已经接收到请求，但是被调用者没有返回结果，会通过事件/回调等机制通知调用者结果**

###  阻塞vs非阻塞

1. 阻塞：调用方必须等待请求返回结果（**即被挂起**），不能从事其他任务
> 阻塞I/O线程处于线程的哪个状态呢？  
答案：**`RUNNABLE`。因为它既没有等待`notify()/notifyAll()`，也不是由于`synchronized`被阻塞**

> **线程池实现了伪异步I/O，但是底层仍然是`BIO`。**

2. 非阻塞：调用方在等待请求返回结果时，不会被挂起，可以从事其他任务
> 在网上看到了一个很贴切的比喻——烧开水：  
1.你妈妈叫你烧开水，你小时候比较笨，坐在开水壶前等水开，这是**同步阻塞**；  
2.等你大一点了，知道等水烧开的这段时间可以忙别的事情，你只需要时不时来看看水烧开了没有，这是**同步非阻塞**；  
3.后来你们家用上了水烧开会发出声音的壶，这样你就可以去干别的事情了，等到水开了它自己会发出声音提醒你，这就是**异步非阻塞**

##  （一）BIO

**同步阻塞I/O，数据的读取写入必须阻塞在一个线程内完成，不能应付高并发**。  

BIO设计的类有40多个，看似杂乱，但是其实都是从4个抽象类中派生来的。
1. `Reader`：字符输入流
2. `InputStream`：字节输入流
3. `Writer`：字符输出流
4. `OutputStream`：字节输出流

###  分类

####  1.字符读取 - `Reader`

1. 节点流：
* 文件操作：`FileReader`
* 管道操作：`PipedReader`
* 数组操作：`CharArrayReader`

2. 处理流：
* 缓冲操作：`BufferedReader`
* 转化控制：`InputStreamReader`

####  2.字节读取 - `InputStream`

1. 节点流：
* 文件操作：`FileInputStream`
* 管道操作：`PipedInputStream`
* 数组操作：`ByteArrayInputStream`

2. 处理流：
* 缓冲操作：`BufferedInputStream`
* 基本数据类型操作：`DataInputStream`
* 对象序列化操作：`ObjectInputStream`
* `SequenceInputStream`

####  3.字符写出 - `Writer`

1. 节点流：
* 文件操作：`FileWriter`
* 管道操作：`PipedWriter`
* 数组操作：`CharArrayWriter`

2. 处理流：
* 缓冲操作：`BufferedWriter`
* 转化控制：`OutputStreamWriter`
* 打印控制：`PrintWriter`

####  4.字节写出 - `OutputStream`

1. 节点流：
* 文件操作：`FileOutputStream`
* 管道操作:`PipedOutputStream`
* 数组操作：`ByteArrayOutputStream`

2. 处理流：
* 缓冲操作：`BufferedOutputStream`
* 基本数据类型操作：`DataOutputStream`
* 对象序列化操作：`ObjectOutputStream`
* 打印操作：`PrintStream`

---

###  demo

```java
//BIO：try-catch-finally
BufferedReader br = null;
String sCurrentLine = null;
try{
    br = new BufferedReader(new FileReader("C:\\Users\\19047535\\IdeaProjects\\batal\\src\\io\\text.txt"));
    while ((sCurrentLine = br.readLine()) != null) {
        System.out.println(sCurrentLine);
    }
}catch (IOException e){
    e.printStackTrace();
}finally {
    try{
        if (br != null)
            br.close();
    }catch (IOException e){
        e.printStackTrace();
    }
}
```

使用[`try-with-resources`](http://www.dragonbaby308.com/effective-java/)取代`try-catch-finally`：
```java
//BIO：try-with-resource
try(BufferedReader br2 = new BufferedReader(
    new FileReader("C:\\Users\\19047535\\IdeaProjects\\batal\\src\\io\\text.txt"))) {
    while ((sCurrentLine = br2.readLine()) != null) {
        System.out.println(sCurrentLine);
    }
}catch (IOException e){
    e.printStackTrace();
}
```

---

###  缺点 & 解决方案：线程池

每一个请求需要一个I/O线程进行处理，同时线程的创建、销毁需要消耗系统资源，加重了系统的负担。  

解决方法：通过**线程池**复用线程
> **线程池本质上仍然是`BIO`**

---

##  （二）NIO

**同步非阻塞I/O，支持高并发、高负载**。  
**应用操作之后直接返回，不会堵塞在那里**。

###  `NIO` 与 `BIO` 的区别

1. `BIO`是面向**流**的，`NIO`是面向**缓冲区**的
2. `BIO`是**阻塞**的，`NIO`是**非阻塞**的
3. `NIO`有**选择器**而`BIO`没有

###  读 & 写

1. 读：创建一个**缓冲区**，然后请求**通道**读取数据
2. 写：创建一个**缓冲区，填充数据**，然后要求**通道**写入数据

###  核心组件

####  1.缓冲区（`Buffer`）

`Buffer`的本质是一块内存区域，用于和`Channel`进行交互——将`Buffer`中的数据写入`Channel`，或是从`Channel`读取数据到`Buffer`。

#####  （1）属性

1. `capacity`：**`buffer`容量，一旦设置就不能更改**
2. `position`：**下一个读/写元素的位置，通过`get`/`put`更新**
3. `limit`：`buffer`中不能读/写的第一个元素，换句话来说**就是`buffer`中存活元素的个数**
4. `mark`：`buffer`中一个记录的位置，调用`mark()`可以让`mark = position`，调用`reset()`可以让`position = mark`
> 0 <= `mark` <= `position` <= `limit` <= `capacity`  
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/NIO/Newly-created-buffer-attributes.png)

#####  （2）常见方法

1. `Buffer clear()`
2. `Buffer flip()`
3. `Buffer rewind()`
4. `Buffer position(int newPosition)`

#####  （3）使用缓冲区

1. 分配缓冲区：`Buffer.allocate(capacity)`
```java
//字节缓冲区
ByteBuffer buffer = ByteBuffer.allocate(28);

//字符缓冲区
CharBuffer charBuffer = CharBuffer.allocate(100);
//从原有字符数组中分配缓冲区
char[] charArr = new char[100];
CharBuffer charBuffer2 = CharBuffer.wrap(charArr);
CharBuffer charBuffer2 = CharBuffer.wrap(charArr, 12, 43);    //分配一部分
```

2. 写入数据到缓冲区：`Channel.read(Buffer)`/`Buffer.put()`

* 从`Channel`中读数据到`Buffer`
```java
int bytesRead = inChannel.read(buffer);
```

* 通过`put`写数据
```java
buffer.put(127);
```

3. 从缓冲区读取数据：`Buffer.get()`
4. 读写反转：**调用`Buffer.flip()`可以让`limit = position; position = 0`，从读模式切换到写模式**
5. 查询剩余元素：`Buffer.hasRemaining()`，如果`buffer`非空，返回`true`
6. 清空`buffer`：`Buffer.clear()`
> 等价于：

```java
for(int i = 0; buffer.hasRemaining(), i++){
    buffer.get();
}
```

---

####  2.通道（`Channel`）

* `FileChannel`
* `SocketChannel`
* `ServerSocketChannel`
* `DatagramChannel`

```java
RandomAccessFile raf = new RandomAccessFile("file", "r");
FileChannel fc = raf.getChannel();

SocketChannel sc = SocketChannel.open();
sc.connect(new InetSocketAddress("host", port));

ServerSocketChannel ssc = ServerSocketChannel.open();
ssc.socket().bind(new InetSocketAddress(localport));

DatagramChannel dc = DatagramChannel.open();

//channel.read()
//channel.write()
//channel.close()
```

#####  （1）多缓冲区操作

* `Scatter`：将1个`Channel`中的数据分散到N个`Buffer`中去

```java
public interface ScatteringByteChannel extends ReadableByteChannel{
    public long read(ByteBuffer[] dsts) throws IOException;
    public long read(ByteBuffer[] dsts, int offset, int length) throws IOException;
}
```

* `Gather`：将N个`Buffer`中的数据**按照顺序**发送到一个`Channel`

```java
public interface GatheringByteChannel extends ReadableByteChannel{
    public long write(ByteBuffer[] srcs) throws IOException;
    public long write(ByteBuffer[] srcs, int offset, int length) throws IOException;
}
```

#####  （2）多通道之间数据传输

1. `transferFrom()`：把数据从通道源传输到`FileChannel`
2. `transferTo()`：将`FileChannel`数据传输到其他通道

```java
public abstract class FileChannel
        extends AbstractChannel
        implements ByteChannel, GatheringByteChannel, ScatteringByteChannel
{
        // There are more other methods
        public abstract long transferTo (long position, long count, WritableByteChannel target);
        public abstract long transferFrom (ReadableByteChannel src, long position, long count);
}
```

---

####  3.选择器/多路复用器（`Selector`）

**通过单线程的`Selector`检查多个`Channel`是否处于可读/可写，从而实现单线程管理多个通道。**

#####  （1）优点

`Selector`**通过多路复用避免了多线程，从而减少了线程上下文切换的开销**。

#####  （2）使用选择器

1. 创建：`Selector.open()`
```java
Selector selector = Selector.open();
```

2. 注册`Channel`到`Selector`：`Channel.register(...)`
```java
channel.configureBlocking(false);   //通道必须是非阻塞的
SelectionKey key = channel.register(selector, SelectionKey.OP_READ);
//一个SelectionKey表示了一个Selector和一个Channel之间的绑定关系
```

---

###  demo

```java
public static void main(String[] args) throws IOException{
    //NIO
    System.out.println("============NIO============");
    RandomAccessFile file = new RandomAccessFile("C:\\Users\\19047535\\IdeaProjects\\batal\\src\\io\\text.txt", "r");
    //通过文件打开channel
    FileChannel channel = file.getChannel();
    //分配buffer
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    while (channel.read(buffer) > 0){
        buffer.flip();
        for (int i = 0; i < buffer.limit(); i++) {
            System.out.println((char)buffer.get());
        }
        buffer.clear();
    }
    channel.close();
    file.close();
}
```

---

###  内存映射文件

内存映射文件（memory-mapped file）能让你创建/修改那些大到无法读入内存的文件。有了内存映射文件，你就可以**认为文件已经全部读入内存，然后把它当作一个非常大的数组来访问**。  
内存映射文件虽然最后还是需要从磁盘读取文件，但是**它不需要将数据读取到OS的内核缓冲区，而是直接将进程的用户私有空间和文件磁盘地址进行映射，就好像是从内存中读/写文件一样，所以才快**。

---

##  （三）AIO

**异步非阻塞I/O：应用操作之后直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作**。  

`AIO`包括`Sockets`和`Files`两部分的异步通道接口及实现，并**尽量使用OS提供的原生本地I/O功能进行实现**。

###  `AIO` 和 `NIO` 的区别

1. `NIO`是OS有了I/O资源后，**通知调用方（I/O线程）执行I/O操作**；
2. `AIO`是**直接将I/O操作以函数的形式传给OS，有资源的时候由OS代替执行，只以`Future`或`Callback`的形式返回一个异步的结果给调用方（I/O线程）**。

###  `AIO`的I/O操作

####  1.`Future`

**提交一个I/O操作请求，返回一个`Future`。然后调用方可以通过`Future.get()`进行检查，确认它是否完成**，或者阻塞I/O操作直到正常完成后超时异常。
```java
//异步通道
AsynchronousSocketChannel ch = AsynchronousSocketChannel.open();
//分配缓冲区
ByteBuffer buffer = ByteBuffer.allocateDirect(8192);
//异步 读
Future<Integer> result = ch.read(buffer);
try{
    //通过Future.get()异步获取执行结果
    int bytesRead = result.get();  
    //Success
    if(bytesRead == -1) return;
}catch(ExecutionException e) {
    //Fail
}
```

注意：**`Future.get()`是同步的**，谨慎使用。

---

####  2.`Callback`

**提交一个I/O操作请求，并且指定一个`CompletionHandler`。当异步I/O完成后，发送一个请求，此时`CompletionHandler`对象的`completed`/`failed`方法将会被回调**。
```java
public interface CompletionHandler<V, A> {
    //当操作完成后被调用
    //V result：操作结果
    //A attachment：提交操作请求时的参数
    void completed(V value, A attachment);

    //当操作失败后被调用
    //Throwable exc：失败原因
    void failed(Throwable exc, A attachment);
}
```

####  3.`Future` 和 `Callback` 的区别

1. **`Future`是调用方主动异步查询操作成功/失败，然后调用方再根据操作结果实现不同逻辑**
2. **`Callback`是调用方直接将成功（`completed`）/失败（`failed`）时对应的操作以函数的方式传递给操作方，操作成功/失败后，操作方异步回调对应的函数**

---

### `AsynchronousFileChannel`：异步文件通道

`AsynchronousFileChannel`使数据可以**异步**读写。

---
title: 设计模式
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/header/db3Avatar.jpeg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: 设计模式
comments: true
date: 2019-05-31 14:34:05
tags:
- Java 8  
- 函数式编程  
- 行为参数化  
- Lambda  
- Spring
- 设计模式
- Java Base
- Reactor
- Redis
- 响应式编程
keywords:
description:
photos: https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/summary_img/MiloManara/5.jpg
---

#  设计模式

---

##  （一）策略模式（Strategy Pattern）

策略模式要求使用者定义一族算法，把它们封装起来，称为“策略”，然后在运行时选择一个算法。  

如：Java 8函数式编程中，使用`行为参数化`完成筛选苹果的功能，其中`AppleHeavyWeightPredicate`和`AppleGreenColorPredicate`就是两种策略，运行时通过代码选择策略——

```java
//定义一个谓词接口，包含test()函数，传入一个Apple对象，返回true或false
public interface ApplePredicate{
  boolean test(Apple apple) ;
}

//ApplePredicate具体实现类1，传入Apple对象，返回重量是否超过150
public class AppleHeavyWeightPredicate implements ApplePredicate{
  public boolean test(Apple apple) {
    return apple.getWeight() > 150;
  }
}

//ApplePredicate具体实现类2，传入Apple对象，返回是否是绿苹果
public class AppleGreenColorPredicate implements ApplePredicate{
  public boolean test(Apple apple) {
    return "green".equals(apple.getColor() ) ;
  }
}

//筛选苹果类
public class FilteringApples{

  public static void main(String[] args) {
    List<Apple> inventory = Arrays.asList(new Apple(80,"green"), new Apple(155,"green"), new Apple(120,"red") );
    List<Apple> heavyApples = filterApples(inventory, new AppleHeavyWeightPredicate() );
    List<Apple> greenApples = filterApples(inventory, new AppleGreenColorPredicate() );
  }

  //筛选苹果函数
  public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>() ;
    for (Apple a : inventory) {
      if (p.test(a) ) {
        result.add(a) ;
      }
    }
    return result;
  }
}
```

---

##  （二）门面模式（外观模式）（Facade Pattern）

将多个子系统的一系列对外接口根据调用方的需求封装成单独的一层，称为一个“门面”(facade)，提供给调用方，调用方不需要知道所有内部子系统的实现细节，只需要与“门面”进行交互即可。  
比如：电脑开机关机时涉及到CPU的启动与关闭、内存的启动与关闭、磁盘的启动与关闭等，但用户不需要知道实现细节，只需要提供给用户一个“门面”——电脑的开机关机即可。

```java
//CPU子系统
public class CPU{
    public void start() {}
    public void shutdown() {}
}

//Memory子系统
public class Memory{
    public void start() {}
    public void shutdown() {}
}

//Disk子系统
public class Memory{
    public void start() {}
    public void shutdown() {}
}

//Facade 门面
//客户端直接调用代表电脑的门店，不需要知道内部子系统细节
public class ComputerFacade{
    private CPU cpu;
    private Memory m;
    private Disk d;

    public void start() {
        cpu.start();
        m.start();
        d.start();
    }

    public void shutdown() {
        cpu.start();
        m.start();
        d.start();
    }
}
```

优点：
1. 将客户端与子系统**解耦**，子系统的内部变更不影响客户端
2. **隐藏系统实现细节**，保障安全性

---

##  （三）构造器模式（Builder Pattern）

一般来说我们设计的`Bean`对象，都是由私有字段和公有的getter/setter/构造函数组成，有的字段是非必须的，有的字段是必须的，必须的字段要在构造函数中体现。如：

```java
public class User{
    private final Integer id;   //必须
    private final String name;

    public User() {
    }

    //getter & setter
}
```

构建对象的过程：

```java
User user = new User();
user.setId(1);
user.setName("police"); //一直到全部set完，对象才可用
```

但是这种模式存在很大的弊端:
1. **类的成员变量过多时难以处理**：当参数少时，列出它的所有构造函数很方便，但是如果参数很多，排列组合的可能性就很多了，难道我们要把所有的构造函数都列出来吗？
2. **当一个对象被创建，没有set完所有成员变量，就处于不可用状态**。

解决方式就是`构造器模式`：**只提供getter，不提供setter，同时将构造函数私有化，只提供一个公有的构造器内部类**。将一个复杂对象的构造与它的表示分离，使得同样的构造过程可以创造不同的对象。

```java
public class User{
    //id name
    //getter

    //私有化构造方法
    private User(UserBuilder builder) {
        this.id = builder.id;
        this.name = builder.name;
    }

    //========================

    //静态公有构造器内部类
    public static class UserBuilder{
        private Integer id;
        private String name;

        //公有构造方法
        public UserBuilder() {
        }

        public UserBuilder id(Integer id) {
            this.id = id;
            return this;
        }

        public UserBuilder name(String name) {
            this.name = name;
            return this;
        }

        //公有构造器方法
        public User build() {
            return new User(this);
        }
    }
}
```

调用构造器来创建对象了：

```java
//只要是return this的方法都能通过类连续调用
//比如new StringBuilder().append("1").append("00");
User user = new User.UserBuilder()
.id(1)
.name("fxxk")
.build();
```

---

##  （四）单例模式

同一时刻，系统内只允许一个对象的实例存在。  

实现方法：

###  1.饿汉式(直接实例化)

```java
public class Singleton{
    //饿汉式，非延迟实例化
    private static final Singleton instance = new Singleton();

    //不提供public构造方法
    private Singleton() {}

    //静态工厂方法
    public static Singleton getInstance() {
        return instance;
    }
}
```

优点：**线程安全**，调用效率高；  
缺点：**不能延迟实例化**

###  2.懒汉式(延迟实例化)

```java
public class Singleton{
    //延迟实例化
    private static final Singleton instance;

    private Singleton() {}

    //synchronized锁，线程安全
    public static synchronized Singleton getInstance() {
        if(instance == null) instance = new Singleton();
        return instance;
    }
}
```

优点：**线程安全**、**可以延迟实例化**；  
缺点：调用效率低

> 注意：使用这种方法，**每次获取单例对象都会尝试加锁**，带来很大的性能损失，可以优化为**双重判断的加锁模式**—— **获取单例对象时不加锁，只有在尝试创建单例对象时才加锁**

####  优化：双重判断 + `volatile static` + `synchronized`  

**获取单例对象时不加锁，只有在尝试创建单例对象时才加锁**：

```java
public class Singleton{
    //volatile static单例实例，保证实例的可见性和唯一性
    private volatile static Singleton instance;

    //私有化构造函数
    private Singletion(){}

    public static Singleton getInstance() {
        if(instance == null) {
            synchronized(Singleton.class) {
                //双重判断：可以防止在synchronized加锁时，
                    //有别的线程创建了对象，导致单例被破坏
                if(instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

###  3.静态内部类！

```java
public class Singleton {
    //静态内部类
    private static class SingletonInstance {
        private static final Singleton instance = new Singleton();
    }

    //私有化构造函数
    private Singleton() {}

    public static Singleton getInstance() {
        return SingletonInstance.instance;
    }
}
```

优点：**线程安全、延迟实例化、调用效率高**

###  4.枚举

```java
public enum Singleton{
    INSTANCE;

    public void operation(){...}
}
```

优点：**线程安全**，调用效率高；  
缺点：**不能延迟实例化**

---

##  （五）原型模式（Prototype Pattern）

简单来说就是**通过复制生成新实例** —— 将一个类注册成为原型类，每次创建对象就是拷贝这个类的实例对象。
> [深拷贝 & 浅拷贝](http://www.dragonbaby308.com/artsk20190826/)

###  应用

* `Spring`框架中`bean scope`可以是`prototype` —— **它采用的就是原型模式，每次请求这个`bean`就进行一次深拷贝，克隆原型`bean`生成一个新`bean`实例，对新`bean`实例进行的修改不会影响原型`bean`**

---

##  （六）工厂模式（Factory Pattern）

简单来说，工厂模式就是**定义一个创建一系列对象的接口，由它的子类决定生成哪种具体对象，可以将对象的创建和使用解耦。**

创建相关接口及其具体实现类：
```java
//枪
public interface Gun{
    public void shoot();
}

//子弹
public interface Bullet{
    public void load();
}

//========================M4A1========================
public class M4A1Gun implements Gun {
    @Override
    public void shoot() {
        System.out.println("Shooting with M4A1");
    }
}

public class M4A1Bullet implements Bullet {
    @Override
    public void load() {
        System.out.println("Shooting with M4A1 ");
    }
}

//========================AK47========================
public class AKGun implements Gun {
    @Override
    public void shoot() {
        System.out.println("Shooting with AK47");
    }
}

public class AKBullet implements Bullet {
    @Override
    public void load() {
        System.out.println("Loading with AK47 bullets");
    }
}
```

创建工厂接口，以及具体工厂实现类：
```java
public interface Factory {
    public Gun productGun();
    public Bullet productBullet();
}

//========================M4A1========================
public class M4A1Factory implements Factory {
    @Override
    public Gun productGun() {
        return new M4A1Gun();
    }
    @Override
    public Bullet productBullet() {
        return new M4A1Bullet();
    }
}

//========================AK47========================
public class AKFactory implements Factory {
    @Override
    public Gun productGun() {
        return new AKGun();
    }
    @Override
    public Bullet productBullet() {
        return new AKBullet();
    }
}
```

测试工厂方法（**创建工厂接口，用其具体实现类创建不同对象**）：
```java
//工厂模式：将对象的创建和使用解耦
Factory akFactory = new AKFactory();
akFactory.productBullet().load();
akFactory.productGun().shoot();
Factory m4a1Factory = new AKFactory();
m4a1Factory.productBullet().load();
m4a1Factory.productGun().shoot();
//输出：
//Loading with AK47 bullets
//Shooting with AK47
//Loading with AK47 bullets
//Shooting with AK47
```

###  优点

1. 解耦：**将对象的创建和使用分开**
2. 降低代码重复率：如果创建某个对象的过程很复杂，需要一定代码量，并且很多地方要用，那么就会有很多重复代码
3. 降低维护成本：**对象的创建过程由工厂统一管理，如果业务发生变化不必在每个创建对象的地方逐个修正，只需要在工厂中进行修改，可以降低维护成本，便于管理**

###  应用

* `Spring`中通过`getBean("xxx")`获取`bean`实例
* `MyBatis`的`SessionFactory`
* `ActiveMQ`的`ConnectionFactory`

---

##  （七）适配器模式（Adaptor Pattern）

**把一个类的接口变成客户端所期待的另一种接口，从而使原本接口不匹配而无法一起工作的两个类能够在一起工作。**


---

##  （八）观察者模式（Observer Pattern）

**将观察者注册到被观察者的“观察者列表”中，如果被观察者状态变化，要能够通过某种渠道通知所有它的观察者**。  

###  Demo

1. 被观察者：`Subject`

```java
//被观察者
public class Subject {
    //状态
    private int state;

    //观察者列表
    private List<Observer> observers = new ArrayList<Observer>();

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
        //更新后，需要通知所有观察者
        notifyAllObservers();
    }

    //将观察者注册到观察者列表
    public void attach(Observer observer) {
        observers.add(observer);
    }

    //通知所有观察者更新
    public void notifyAllObservers() {
        for (Observer observer : observers) {
            observer.update();
        }
    }
}
```

2. 观察者抽象类：`Observer`

```java
public abstract class Observer {
    protected Subject subject;  //被观察者
    public abstract void update();  //更新
}
```

3. 具体观察者：`ObserverA`、`ObserverB`

```java
public class ObserverA extends Observer {
    //构造
    public ObserverA(Subject s) {
        this.subject = s;
        this.subject.attach(this);
    }

    //观察到被观察者更新后，执行
    @Override
    public void update() {
        System.out.println("ObserverA 拿到更新：" + subject.getState());
    }
}

public class ObserverB extends Observer {

    //构造
    public ObserverB(Subject s) {
        this.subject = s;
        this.subject.attach(this);
    }

    //观察到被观察者更新后，执行
    @Override
    public void update() {
        System.out.println("ObserverB 拿到更新：" + subject.getState());
    }
}
```

4. 测试类：`ObserverPatternTest`

```java
public class ObserverPatternTest {
    public static void main(String[] args) {
        Subject s = new Subject();
        new ObserverA(s);
        new ObserverB(s);

        System.out.println("Subject的state变量更新为:15");
        s.setState(15);
        System.out.println("------------------------------");
        System.out.println("Subject的state变量更新为:-50");
        s.setState(-50);
    }
}

//输出：
//Subject的state变量更新为:15
//ObserverA 拿到更新：15
//ObserverB 拿到更新：15
//------------------------------
//Subject的state变量更新为:-50
//ObserverA 拿到更新：-50
//ObserverB 拿到更新：-50
```

###  应用

1. Java GUI `Listener`
2. [`Redis`事务`watch`命令](http://www.dragonbaby308.com/redis/)
3. [`Reactor`框架](http://www.dragonbaby308.com/Reactor-Spring-WebFlux/)

---

##  （九）责任链模式（Chain of Responsibility Pattern）

责任链模式 主要是用于取代 一长串的`if-else`。

> 见[《设计模式 | 责任链模式及典型应用》](https://blog.csdn.net/wwwdc1012/article/details/83592323)，写得很通俗易懂。

###  优点

1. 对象只需要知道请求会被处理即可，责任链中的对象不需要知道链的结构，降低了系统的耦合度；
2. 请求处理对象只需要维护一个指向后继者的引用，不需要维护它对所有候选处理者的引用，可以简化对象的相互连接；
3. **如果只改变处理的顺序，只需要对原有责任链进行动态修改，不需要大规模改动代码；同样的，对某个处理者的小规模修改，不会影响到整个责任链**；
4. **新增一个新的具体请求处理者时无须修改原有代码，只需要在客户端重新建链即可，符合 "开闭原则"**

###  缺点

1. 一个请求可能因为责任链没有被正确配置而得不到处理；
2. 对于很长的责任链，请求的处理涉及到多个处理者，系统性能将会收到影响，且不方便调试；
3. **如果责任链创建不当，造成循环调用，会导致系统陷入死循环**。

###  Demo

1. 请求类

```java
public class Request{
  //...
}
```

2. 处理者抽象类`Handler`

```java
public abstract class Handler{
  //下一个处理者
  protected Handler nextHandler;

  //处理请求
  public abstract boolean process(Request request);
}
```

3. 实际处理者类：`HandlerA`/`HandlerB`/`HandlerC`

```java
//处理者A
public class HandlerA extends Handler{
  @Override
  public boolean process(Request request){
    boolean result = serviceA.doSomething(request);
    //A直接返回失败
    if(result == false) return false;
    //A直接返回成功
    else if(canReturnTrueAlready) return true;
    //A返回成功，但是还需要交给下一个处理者处理
    return nextHandler.process(request);
  }
}

//处理者B
public class HandlerB extends Handler{
  @Override
  public boolean process(Request request){
    boolean result = serviceB.doSomething(request);
    //B直接返回失败
    if(result == false) return false;
    //B直接返回成功
    else if(canReturnTrueAlready) return true;
    //B返回成功，但是还需要交给下一个处理者处理
    return nextHandler.process(request);
  }
}

//处理者C
public class HandlerC extends Handler{
  @Override
  public boolean process(Request request){
    boolean result = serviceC.doSomething(request);
    //C直接返回失败
    if(result == false) return false;
    //C直接返回成功
    else if(canReturnTrueAlready) return true;
    //C返回成功，但是还需要交给下一个处理者处理
    return nextHandler.process(request);
  }
}
```

4. 调用类

```java
public class Client{
  public static void main(String[] args){
    HandlerA a = new HandlerA();
    HandlerB b = new HandlerB();
    HandlerC c = new HandlerC();
    //创建责任链
    a.setNextHandler(b);
    b.setNextHandler(c);
    //处理请求
    Request req = new Request();
    boolean rst = a.process(req);
  }
}
```

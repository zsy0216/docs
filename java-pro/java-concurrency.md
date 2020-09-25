# Java 并发编程

> https://www.bilibili.com/video/BV16J411h7Rd
>

## 1. 进程与线程

### 进程与线程 

#### 进程

- 程序由指令和数据组成，但这些指令要运行，数据要读些，就必须将指令加载至 CPU，数据加载至内存。在指令运行过程中还需要用到磁盘、网络等设备。进程就是用来加载指令、管理内存、管理 IO 的；
- 当一个程序被运行，从磁盘加载这个程序的代码至内存，这时就开启了一个进程；
- 进程可以视为程序的一个实例。大部分程序可以同时运行多个实例进程（浏览器），也有的程序只能启动一个实例进程。

#### 线程

- 一个进程之内可以分为一到多个线程；
- 一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给 CPU 执行；
- Java 中，线程作为最小的调度单位，进程作为资源分配的最小单位。在 Windows 中进程是不活动的，只是作为线程的容器。

#### 二者对比

- 进程基本上相互独立，而线程存在于进程内，是进程的一个子集；
- 进程拥有共享的资源，如内存空间等，供其内部的线程共享；
- 进程间通信较为复杂
  - 同一台计算机的进程通信成为 IPC（Inter-process communication）
  - 不同计算机之间的进程通信，需要通过网络，并遵守共同的协议，例如 HTTP
- 线程通信相对简单，因为它们共享进程内的内存，一个例子是多个线程可以访问同一个共享变量；
- 线程更轻量，线程上下文切换成本一般上要比进程上下文切换低。

### 并行与并发

单核 cpu 下，线程实际还是`串行执行`的。操作系统中有一个组件叫做任务调度器，将 cpu 的时间片（Windows 下时间片最小约为 15 毫秒）分给不同的线程使用，只是由于 cpu 在线程间（时间片很短）的切换非常快，人类感觉是同时运行的。总结为一句话就是：微观穿行，宏观并行。

一般会将这种线程轮流使用 CPU 的做法称为并发，concurrent.

多核 cpu 下，每个核(core) 都可以调度运行线程，这时线程可以是并行的。

> 引用 Rob Pike 的一段描述：
>
> - 并发（concurrent）是同一时间应对（dealing with）多件事情的能力；
> - 并行（parallel）是同一时间动手做（doing）多件事情的能力；

### 应用

1. 单核 cpu 下，多线程不能实际提高程序运行效率，只是为了能够在不同的任务之间切换，不同线程轮流使用cpu ，不至于一个线程总占用 cpu，别的线程没法干活

2. 多核 cpu 可以并行跑多个线程，但能否提高程序运行效率还是要分情况的
- 有些任务，经过精心设计，将任务拆分，并行执行，当然可以提高程序的运行效率。但不是所有计算任务都能拆分（参考后文的【阿姆达尔定律】）
- 也不是所有任务都需要拆分，任务的目的如果不同，谈拆分和效率没啥意义
3. IO 操作不占用 cpu，只是我们一般拷贝文件使用的是【阻塞 IO】，这时相当于线程虽然不用 cpu，但需要一直等待 IO 结束，没能充分利用线程。所以才有后面的【非阻塞 IO】和【异步 IO】优化

## 2. Java 线程

### 线程的创建与启动

#### 方法1：直接使用Thread

原理，重写Thread类中的run()方法，在方法实现要执行的任务

```java
// 创建线程对象
Thread t = new Thread() {
    @Override
    public void run() {
        // task
    }
};
t.start(); //启动线程
```

#### 方法2：使用Runnable配合Thread

将 Runnable 传递给 Thread，原理上是通过 Thread 调用了 Runnable 实现的run()方法

```java
// 创建任务对象
Runnable task = new Runnable() {
    @Override
    public void run() {
        log.debug("hello");
    }
};
// 参数1是任务对象，参数2是线程名，推荐创建方法
Thread t = new Thread(task, "my-thread");
t.start();// 运行线程
```

使用 java8 lambda 表达式简化线程创建：

```java
Thread t = new Thread(() -> log.debug("hello"), "mythread"); 
t.start();
```

#### 方法3：FutureTask 配合 Thread

FutureTask能够接收Callable类型的参数，用来处理有**返回结果**的情况；

FutureTask 继承了 Runnable 接口，能够获取线程处理中的返回结果；

```java
FutureTask<Integer> task = new FutureTask<>(() -> {
    log.debug("running");
    return 100;
});
Thread t = new Thread(task, "mythread");
t.start();
task.get(); // 100
```


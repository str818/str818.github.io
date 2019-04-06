---
layout: article
title: Java 并发
tags: Java

lang: zh-Hans
key: Java_Concurrency
pageview: true
toc: true
show_subscribe: false
---

## 一、基础知识

### 1. 概述

#### Ⅰ. 进程和线程

可以简单的认为一个应用程序就是一个 **进程**，不过有些应用程序会启动多个进程，即一个应用程序至少会启动一个进程，在 Windows 系统上面通过任务管理器可以查看计算机上正在启动的进程。

进程和进程之间的内存是独立的，这样可以提高应用程序的稳定性和安全性。

**线程** 是进程中的一个执行场景，一个进程可以启动多个线程。使用多线程的可以提高 CPU 的使用率，不是提高执行速度。

线程和线程之间共享堆内存和方法区，栈内存是独立的，每个线程有自己独立的栈。

多进程的应用场景：QQ 多人视频、迅雷同时下载多个文件。

#### Ⅱ. 并行和并发

**并行** 指两个或两个以上的任务同时运行，在甲任务进行的同时，乙任务也在进行(需要多核 CPU)。

**并发** 是指两个或两个以上的任务都请求运行，而CPU只能接受一个任务，就把这两个任务安排轮流进行，由于时间间隔较短，使人感觉两个任务都在运行。

#### Ⅲ. 优缺点

优点：提高 CPU 的使用率。

缺点：

- 设计复杂。多线程中共享堆内存和方法区，因此里面的一些数据是可以共享的，在设计时要确保数据的准确性。

- 资源消耗增多。栈内存是不共享的，如果启用多个线程的话会占用更多的内存。

#### Ⅳ. 使用场景

- 通过并行计算提高程序执行性能

- 需要等待网络、I/O 响应导致耗费大量的执行时间，可以采用异步线程的方式来减少阻塞


### 2. 创建线程

#### Ⅰ. 继承 Tread 类

Thread 类本质上是实现了 Runnable 接口的一个实例，代表一个线程实例。启动线程的唯一方法就是通过 Thread 类的 start() 实例方法，start() 方法是一个 native 方法，它会启动一个新线程，并执行 run() 方法。

```java
public class MyThread extends Thread{
    @Override
    public void run(){
        //...
    }
}

MyThread myThread1 = new MyThread();
MyThread myThread2 = new MyThread();
myThread1.start();
myThread2.start();
```

#### Ⅱ. 实现 Runnable 接口

如果自己的类已经继承的其它的类，就无法直接继承 Thread，此时可以实现 Runnable 接口。

```java
public class MyThread implements Runnable{
    @Override
    public void run(){
        //...
    }
}

MyThread myThread1 = new MyThread();
MyThread myThread2 = new MyThread();
myThread1.start();
myThread2.start();
```

#### Ⅲ. 实现 Callable 接口

该接口位于 `java.util.concurrent` 包下面，使用 Callable 接口创建的线程能够获得返回值并且可以声明异常。

```java
public class CallableDemo implements Callable<String>{
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 创建 ExecutorService 线程池
        ExecutorService executorService = Executors.newFixedThreadPool(1);

        CallableDemo callableDemo = new CallableDemo();
        Future<String> future = executorService.submit(callableDemo);
        System.out.println(future.get());

        // 关闭线程池，不再接收新的线程，未执行完的线程不会被关闭
        executorService.shutdown();
    }
    @Override
    public String call() throws Exception {
        int a=1;
        int b=2;
        System.out.println(a+b);
        return "执行结果:"+(a+b);
    }
}
```

### 3. 线程状态

Java 线程在运行的声明周期中可能有 6 种不同的状态。

<div align="center">  <img src="/img/java_concurrency_state.png" width="80%"/> </div><br>

#### 1. 初始状态 (NEW)

线程被创建，但是还没有调用 start 方法。

#### 2. 运行状态 (RUNNABLE)

Java 线程将操作系统中的就绪和运行两种状态统一称为「运行中」。

#### 3. 阻塞状态 (BLOCKED)

线程阻塞于锁。

- 等待阻塞：运行的线程执行 wait 方法，JVM 会把当前线程放入到等待队列。

- 同步阻塞：运行的线程在获取对象的同步锁时，若该同步锁被其它线程锁占用，那么 JVM 会把当前的线程放入到锁池中。

- 其它阻塞：运行的线程执行 Thread.sleep 或者 t.join 方法 ，或者发出 I/O 请求时，JVM 会把当前线程设置为阻塞状态，当 sleep 结束、join 线程终止、IO 处理完毕后线程恢复。

#### 4. 等待状态 (WAITING)

当前线程需要等待其它线程做出一些特定的动作(通知或中断)。

#### 5. 超时等待状态 (TIME_WAITING)

无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。

#### 6. 终止状态 (TERMINATED)

当前线程已经执行完毕。


### 4. 基本操作

#### 线程优先级

在 Java 线程中，通过一个整型成员变量 priority 来控制优先级，优先级的范围从1-10，在线程构建的时候可以通过 setPriority(int) 方法修改优先级，默认优先级是 5，优先级高的线程分配时间片的数量要多余优先级低的线程。

线程优先级不能作为程序正确性的依赖，因为操作系统可以完全不理会 Java 线程对于优先级的设定。

#### Daemon 线程

守护线程时一种支持型线程，主要被用作程序中后台调度以及支持型工作。

当所有非守护线程结束时，程序结束并杀死所有守护线程。守护线程在退出时并不会执行 finally 中的代码，所以将释放资源等操作不要放在 finally 块中执行。

使用 setDaemon(true) 将一个线程设置为守护线程，要在线程启动之前声明。

#### 中断

Ⅰ. interrupt()

中断该线程对象，如果该线程处于阻塞或等待状态，会抛出 InterruptedException 异常，从而提前结束该线程。

Ⅱ. isInterrupted()

测试该线程对象是否被中断，中断标志位不会被清除。

Ⅲ. interrupted()

静态方法，测试当前线程是否被中断，中断标志位会被清除。


#### yield()

放弃当前的 CPU 资源，将它让给其它的任务去占用 CPU 执行时间。

放弃时间不确定，有可能刚刚放弃，马上又获得 CPU 时间片。

```java
Thread.yield();
```

#### sleep()

Thread.sleep(millisec) 方法会休眠当前正在执行的线程，millisec 单位为毫秒。

```java
public void run() {
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

#### join()

当前线程 A 等待 thread 线程终止之后才从 thread.join() 返回。也就是当线程中调用另一个线程的 join() 方法，会将当前线程挂起，直到目标线程结束。


## 二、并发理论 (JMM)

### 1. 重排序

重排序是指编译器和处理器为了优化程序而对指令序列进行重新排序的一种手段。重排序分为三种类型：

<div align="center">  <img src="/img/java_concurrency_resort.png" width="70%"/> </div><br>

- 编译器优化的重排序。编译器在不改变单线程语义的前提下，可以重新安排语句的执行顺序。

- 指令级级并行的重排序。现代处理器采用了指令级并行技术来将多条指令重叠执行。如果不存在数据依赖性，处理器可以改变语句对应的执行顺序。

- 内存系统的重排序。由于处理器使用缓存和读/写缓冲区，这使得加载和存储操作看上去可能是乱序执行。

对于处理器重排序，为了保存内存可见性，Java 编译器在生成指令序列的适当位置会插入内存屏障指令来禁止特定类型的处理器重排序。JMM 把内存屏障指令分为 4 类：

|      屏障类型       |         指令示例         |                             说明                             |
| :-----------------: | :----------------------: | :----------------------------------------------------------: |
|  LoadLoad Barriers  |   Load1;LoadLoad;Load2   |   确保 Load1 数据的装载先于 Load2 及所有后续装载指令的装载   |
| StoreStore Barriers | Store1;StoreStore;Store2 | 确保 Store1 数据对其他处理器可见(刷新到内存)先于Store2及所有后续存储指令的存储 |
| LoadStore Barriers  |  Load1;LoadStore;Store2  | 确保 Load1 数据装载先于 Store2 及所有后续的存储指令刷新到内存 |
| StoreLoad Barriers  |  Store1;StoreLoad;Load2  | 确保 Store1 数据对其他处理器变得可见(刷新到内存)先于Load2 及所有后续装载指令的装载。 该指令相当于一个全能型的屏障，会使该屏障之前的所有内存访问指令完成之后，才执行该屏障之后的内存访问指令 |


## 三、关键字

## Lock


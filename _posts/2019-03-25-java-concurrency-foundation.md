---
layout: article
title: Java 并发编程基础
tags: Java

lang: zh-Hans
key: Java_Concurrency_Foundation
pageview: true
toc: true
show_subscribe: false
---

## 一、易混淆概念

### 1. 进程和线程

进程是操作系统分配资源的最小单元，线程是操作系统调度的最小单元。

一个程序下至少有一个进程，一个进程至少有一个线程，一个进程下也可以有多个线程来增加程序的执行速度。

### 2. 并行和并发

**并行：** 多个处理器或多核处理器同时处理多个任务。

**并发：** 多个任务在同一个 CPU核上，按细分的时间片轮流执行，由于时间间隔较短，使人感觉两个任务都在运行。

## 二、初识线程

### 1. 什么是线程？

线程时操作系统的最小调度单元，在一个进程里可以创建多个线程，这些线程都拥有各自的计数器、堆栈和局部变量等属性，并且能够访问共享的内存变量。处理器在这些线程上高速切换，让使用者感觉到这些线程在同时执行。

### 2. 优缺点与使用场景

优点：提高 CPU 的使用率。

缺点：

- 设计复杂。多线程中共享堆内存和方法区，因此里面的一些数据是可以共享的，在设计时要确保数据的准确性。
- 资源消耗增多。栈内存是不共享的，如果启用多个线程的话会占用更多的内存。

使用场景：

- 通过并行计算提高程序执行性能。
- 需要等待网络、I/O 响应导致耗费大量的执行时间，可以采用异步线程的方式来减少阻塞。

### 3. 线程状态

Java 线程在运行的声明周期中可能有 6 种不同的状态。

<div align="center">  <img src="/img/java_concurrency_state.png" width="80%"/> </div><br>

#### Ⅰ. 初始状态 (NEW)

线程被创建，但是还没有调用 start 方法。

#### Ⅱ. 运行状态 (RUNNABLE)

Java 线程将操作系统中的就绪和运行两种状态统一称为「运行中」。

#### Ⅲ. 阻塞状态 (BLOCKED)

线程阻塞于锁。

- 等待阻塞：运行的线程执行 wait 方法，JVM 会把当前线程放入到等待队列。
- 同步阻塞：运行的线程在获取对象的同步锁时，若该同步锁被其它线程锁占用，那么 JVM 会把当前的线程放入到锁池中。
- 其它阻塞：运行的线程执行 Thread.sleep 或者 t.join 方法 ，或者发出 I/O 请求时，JVM 会把当前线程设置为阻塞状态，当 sleep 结束、join 线程终止、IO 处理完毕后线程恢复。

#### Ⅳ. 等待状态 (WAITING)

当前线程需要等待其它线程做出一些特定的动作(通知或中断)。

#### Ⅴ. 超时等待状态 (TIME_WAITING)

无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。

#### Ⅵ. 终止状态 (TERMINATED)

当前线程已经执行完毕。

### 4. 上下文切换

CPU 通过时间片分配算法来循环执行任务，当前任务执行一个时间片后会切换到下一个任务。但是，在切换前会保存上一个任务的状态，以便下次切换回这个任务时，可以再加载这个任务的状态。任务从保存到加载的过程就是一次上下文切换。

### 5. 线程优先级

在 Java 线程中，通过一个整型成员变量 priority 来控制优先级，优先级的范围从1-10，在线程构建的时候可以通过 `setPriority(int)` 方法修改优先级，默认优先级是 5，优先级高的线程分配时间片的数量要多余优先级低的线程。

线程优先级不能作为程序正确性的依赖，因为操作系统可以完全不理会 Java 线程对于优先级的设定。

### 6. Daemon 线程

守护线程时一种支持型线程，主要被用作程序中后台调度以及支持型工作。

当所有非守护线程结束时，程序结束并杀死所有守护线程。守护线程在退出时并不会执行 finally 中的代码，所以将释放资源等操作不要放在 finally 块中执行。

使用 `setDaemon(true)` 将一个线程设置为守护线程，要在线程启动之前声明。

### 7. 死锁

死锁是指两个或两个以上的线程在执行过程中，因争夺资源而造成的互相等待的现象，在无外力作用的情况下，这些线程会一直相互等待而无法继续运行下去。

## 三、启动与终止线程

### 1. 创建线程

Java 中有三种创建线程的方式。

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

该接口位于 `java.util.concurrent` 包下面，使用 Callable 接口创建的线程能够获得返回值并且可以声明异常。使用 Callable 可以拿到返回值，而 Runnable 没有返回值，Callable 可以看作是 Runnable 的补充。

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

### 2. 启动线程

创建线程对象后，调用 start() 方法启动线程。

run() 方法用于执行线程的运行时代码。run() 可以重复调用，而 start() 只能调用一次。

### 3. 中断

中断可以理解为线程的一个标识位属性，它表示一个运行中的线程是否被其它线程进行了中断操作。

#### Ⅰ. interrupt()

中断该线程对象，如果该线程处于阻塞或等待状态，会抛出 InterruptedException 异常，从而提前结束该线程。

#### Ⅱ. isInterrupted()

测试该线程对象是否被中断，中断标志位不会被清除。

#### Ⅲ. interrupted()

静态方法，测试当前线程是否被中断，中断标志位会被清除。

## 四、基本操作

### 1. yield()

放弃当前的 CPU 资源，将它让给其它的任务去占用 CPU 执行时间。

放弃时间不确定，有可能刚刚放弃，马上又获得 CPU 时间片。

```java
Thread.yield();
```

### 2. sleep()

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

### 3. join()

当前线程 A 等待 thread 线程终止之后才从 thread.join() 返回。也就是当线程中调用另一个线程的 join() 方法，会将当前线程挂起，直到目标线程结束。

### 4. 过期方法

suspend()、resume()、stop() 分别表示对线程的暂停、恢复与停止操作，这些 API 是过期的，不推荐使用。

suspend() 方法在调用后，线程不会释放已经占有的资源，而是占有资源进入睡眠状态，这样容易发生死锁的问题。

stop() 方法在终结一个线程时不会保证线程的资源正常释放，通常是没有给予线程完成资源释放工作的机会，因此会导致程序可能工作在不确定的状态下。

## 四、线程通信

<div align="center">  <img src="/img/java_concurrency_thread_communation.png" width="100%"/> </div><br>


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


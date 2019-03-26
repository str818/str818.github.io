---
layout: article
title: Java 并发
tags: Java

lang: zh-Hans
key: Java_Multi_Thread
pageview: true
toc: true
show_subscribe: false
---

## 一、概念

### 1. 进程和线程

可以简单的认为一个应用程序就是一个 **进程**，不过有些应用程序会启动多个进程，即一个应用程序至少会启动一个进程，在 Windows 系统上面通过任务管理器可以查看计算机上正在启动的进程。

进程和进程之间的内存是独立的，这样可以提高应用程序的稳定性和安全性。

**线程** 是进程中的一个执行场景，一个进程可以启动多个线程。使用多线程的可以提高 CPU 的使用率，不是提高执行速度。

线程和线程之间共享堆内存和方法区，栈内存是独立的，每个线程有自己独立的栈。

多进程的应用场景：QQ 多人视频、迅雷同时下载多个文件。

### 2. 并行和并发

**并行** 指两个或两个以上的任务同时运行，在甲任务进行的同时，乙任务也在进行(需要多核 CPU)。

**并发** 是指两个或两个以上的任务都请求运行，而CPU只能接受一个任务，就把这两个任务安排轮流进行，由于时间间隔较短，使人感觉两个任务都在运行。

### 3. 优缺点

优点：提高 CPU 的使用率。

缺点：

- 设计复杂。多线程中共享堆内存和方法区，因此里面的一些数据是可以共享的，在设计时要确保数据的准确性。

- 资源消耗增多。栈内存是不共享的，如果启用多个线程的话会占用更多的内存。

### 4. 使用场景

- 通过并行计算提高程序执行性能

- 需要等待网络、I/O 响应导致耗费大量的执行时间，可以采用异步线程的方式来减少阻塞


## 二、使用线程

### 1. 继承 Tread 类

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

### 2. 实现 Runnable 接口

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

### 3. 实现 Callable 接口

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

## 三、线程状态

Java 线程在运行的声明周期中可能有 6 种不同的状态。

#### 1. 初始状态 (NEW)

线程被创建，但是还没有调用 start 方法。

#### 2. 运行状态 (RUNNABLE)

Java 线程将操作系统中的就绪和运行两种状态统一称为「运行中」。

#### 3. 阻塞状态 (BLOCKED)

表示线程进入等待状态，线程因为某种原因放弃了 CPU 的使用权。

- 等待阻塞：运行的线程执行 wait 方法，JVM 会把当前线程放入到等待队列。

- 同步阻塞：运行的线程在获取对象的同步锁时，若该同步锁被其它线程锁占用，那么 JVM 会把当前的线程放入到锁池中。

- 其它阻塞：




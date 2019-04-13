---
layout: article
title: Java 并发-线程池
tags: Java

lang: zh-Hans
key: Java_Concurrency_Thread_Pool
pageview: true
toc: true
show_subscribe: false
---

## 一、线程池

### 1. 为什么要使用线程池？

1. 降低资源消耗。重复利用已创建的线程降低线程创建和销毁造成的消耗。
2. 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
3. 提高线程的可管理性。线程时稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一分配、调优和监控。

### 2. 工作原理

当提交一个新任务到线程池时，线程池的处理流程如下：

1. 线程池判断核心线程池里的线程是否都在执行任务。如果不是，则创建一个新的工作线程来执行任务。如果核心线程池里的线程都在执行任务，则进入下个流程。

2. 线程池判断工作队列是否已经满了。如果工作队列没有满，则将新提交的任务存储在这个工作队列里。如果工作队列满了，则进入下个流程。

3. 线程池判断线程池的线程是否都处于工作状态。如果没有，则创建一个新的工作线程来执行任务。如果已经满了，则交给饱和策略来处理这个任务。

<div align="center">  <img src="/img/java_concurrency_thread_pool.png" width="80%"/> </div><br>

### 3. 线程池的创建

```java
ThreadPoolExecutor(int corePoolSize,
                   int maximumPoolSize,
                   long keepAliveTime,
                   TimeUnit unit,
                   BlockingQueue<Runnable> workQueue,
                   ThreadFactory threadFactory,
                   RejectedExecutionHandler handler);
```

参数说明：

1. corePoolSize：线程池的基本大小。
2. runnableTaskQueue：任务队列，用于保存等待执行的任务的阻塞队列。
  - ArrayBlockingQueue：基于数组的有界阻塞队列，FIFO。
  - LinkedBlockingQueue：基于链表的阻塞队列，FIFO，吞度量高于 ArrayBlockingQueue。
  - SynchronousQueue：不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态。
  - PriorityBlockingQueue：优先级无限阻塞队列。
3. keepAliveTime：空闲线程存活时间。
4. unit：keepAliveTime 指定的时间单位。
5. maximumPoolSize：线程池最大数量，如果队列满了，并且已创建的线程数小于最大线程数，则线程池会再创建新的线程执行任务。若使用无界的任务队列这个参数无效。
6. ThreadFactory：用于创建线程的工厂，可以通过线程工厂给每个创建出来的线程设置更有意义的名字。
7. RejectedExecutionHandler：饱和策略，当队列和线程池都满了，说明线程池处于饱和状态，需要采取策略处理提交的新任务，默认时 AbortPolicy。
  - AbortPolicy：直接抛出异常。
  - CallerRunsPolicy：只用调用者所在的线程来执行任务。
  - DiscardPolicy：不处理直接丢弃掉任务。
  - DiscardOldestPolicy：丢弃掉阻塞队列中存放时间最久的任务，执行当前任务。
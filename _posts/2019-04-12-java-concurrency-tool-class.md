---
layout: article
title: Java 并发-工具类
tags: Java

lang: zh-Hans
key: Java_Concurrency_Tool_Class
pageview: true
toc: true
show_subscribe: false
---

## 一、CountDownLatch

CountDownLatch 能够使一个或多个线程等待其他线程完成各自的工作后再执行。

### 1. 主要方法

```java
public CountDownLatch(int count); //指定计数的次数，只能被设置1次
public void countDown();          //调用此方法则计数减1
public void await() throws InterruptedException   //调用此方法会一直阻塞当前线程，直到计时器的值为0，除非线程被中断
Public Long getCount();           //得到当前的计数
Public boolean await(long timeout, TimeUnit unit) //调用此方法会一直阻塞当前线程，直到计时器的值为0，除非线程被中断或者计数器超时，返回false代表计数器超时
```

### 2. 使用

```java
public static void main(String[] args) throws InterruptedException{

    CountDownLatch countDownLatch = new CountDownLatch(3);

    new Thread(()->{
        countDownLatch.countDown();
    },"t1").start();

    new Thread(()->{
        countDownLatch.countDown();
    },"t2").start();

    new Thread(()->{
        countDownLatch.countDown();
    },"t3").start();

    countDownLatch.await();
    System.out.println("Over");
}
```

## 二、 CyclicBarrier

当一组线程都达到了「临界点」时，所有的线程才能继续往前执行，否则线程阻塞。

### 1. 主要方法

```java
// 等到所有的线程都到达指定的临界点
await() throws InterruptedException, BrokenBarrierException 

// 与上面的await方法功能基本一致，只不过这里有超时限制，阻塞等待直至到达超时时间为止
await(long timeout, TimeUnit unit) throws InterruptedException, 
BrokenBarrierException, TimeoutException 

// 获取当前有多少个线程阻塞等待在临界点上
int getNumberWaiting()

// 用于查询阻塞等待的线程是否被中断
boolean isBroken()

// 将屏障重置为初始状态。如果当前有线程正在临界点等待的话，将抛出BrokenBarrierException。
void reset()
```

### 2. 使用

```java
public static void main(String[] args) {
    final int totalThread = 10;
    CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < totalThread; i++) {
        executorService.execute(() -> {
            System.out.print("before..");
            try {
                cyclicBarrier.await();
            } catch (InterruptedException | BrokenBarrierException e) {
                e.printStackTrace();
            }
            System.out.print("after..");
        });
    }
    executorService.shutdown();
}
```

```
before..before..before..before..before..before..before..before..before..before..after..after..after..after..after..after..after..after..after..after..
```

## 三. Samaphore

Semaphore 能够用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源。

例如某商场只有 5 个停车位，每个停车位只能停一辆车，如果这来了 10 辆车，必须等前面有空的车位才能进入。

```java
public static void main(String[] args) throws InterruptedException {
    Semaphore semaphore = new Semaphore(5);
    for (int i = 0; i < 10; i++) {
        new Car(i,semaphore).start();
    }
}
static class Car extends Thread{
    private int num;
    private Semaphore semaphore;
    public Car(int num, Semaphore semaphore) {
        this.num = num;
        this.semaphore = semaphore;
    }
    @Override
    public void run() {
        try{
            semaphore.acquire();
            System.out.println("第" + num + "占用一个停车位");
            TimeUnit.SECONDS.sleep(2);
            System.out.println("第" + num + "辆车走了");
            semaphore.release();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

```
第0占用一个停车位
第2占用一个停车位
第1占用一个停车位
第3占用一个停车位
第4占用一个停车位
第1辆车走了
第4辆车走了
第7占用一个停车位
第3辆车走了
第2辆车走了
第0辆车走了
第6占用一个停车位
第5占用一个停车位
第8占用一个停车位
第9占用一个停车位
第7辆车走了
第5辆车走了
第8辆车走了
第9辆车走了
第6辆车走了
```

## 四、Exchanger

Exchanger 用于进行线程间的数据交换，它提供一个同步点，在这个同步点两个线程可以交换彼此的数据。

```java
private static final Exchanger<String> exgr = new Exchanger<String>();
private static ExecutorService threadPool = Executors.newFixedThreadPool(2);
public static void main(String[] args) throws InterruptedException {

    threadPool.execute(new Runnable() {
        @Override
        public void run() {
            String A = "A 的信息";
            try {
                String B = exgr.exchange(A);
                System.out.println("A 接收的信息：" + B);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });

    threadPool.execute(new Runnable() {
        @Override
        public void run() {
            String B = "B 的信息";
            try {
                String A = exgr.exchange(B);
                System.out.println("B 接收的信息：" + A);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    threadPool.shutdown();
}
```

```
A 接收的信息：B 的信息
B 接收的信息：A 的信息
```
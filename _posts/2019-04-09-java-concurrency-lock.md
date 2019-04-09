---
layout: article
title: Java 并发-Lock
tags: Java

lang: zh-Hans
key: Java_Concurrency_Lock
pageview: true
toc: true
show_subscribe: false
---

## 一、Lock 简介

锁是用来控制多个线程访问共享资源的方式，在 Lock 接口出现之前，Java 程序是靠 synchronized 关键字时间锁的功能，而在 JDK 5 之后，并发包中添加了 Lock 接口来实现锁的功能，其提供了和 synchronized 关键字类似的同步功能。

```java
Lock lock = new ReentrantLock();
lock.lock();
try{
} finally{
    lock.unlock();
}
```

在 finally 块中释放锁，以保证锁最终能够被释放。

### Lock 与 synchronized 的区别？

- synchronized 是关键字；Lock 是接口。
- synchronized 锁的释放是被动的，当出现异常或者同步代码块执行完之后，才会释放锁；Lock 具备更强大的灵活性，可以控制锁的释放和获取。
- synchronized 不能判断锁的状态；Lock 可以判断锁的状态。
- synchronized 只有非公平锁；Lock 可以实现公平锁与非公平锁。
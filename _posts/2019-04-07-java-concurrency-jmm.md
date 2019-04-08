---
layout: article
title: Java 并发内存模型
tags: Java

lang: zh-Hans
key: Java_Concurrency_JMM
pageview: true
toc: true
show_subscribe: false
---

## 一、JMM 抽象结构

Java 线程之间的通信由 Java 内存模型（JMM）控制，JMM 决定一个线程对共享变量的写入何时对另一个线程可见。从抽象的角度来看，JMM 定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存中，每个线程都有一个私有的本地内存，本地内存存储了该线程以读/写共享变量的副本。本地内存是 JMM 的一个抽象概念，并不真实存在，它涵盖了缓存、写缓冲区、寄存器以及其它的硬件和编译器优化。

<div align="center">  <img src="/img/java_concurrency_jmm_abstruct_structure.png" width="60%"/> </div><br>

并发编程中主要需要解决两个问题：「线程之间如何通信」和「线程之间如何完成同步」。

## 二、重排序

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

### as-if-serial 语义

不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不能被改变。

编译器、runtime 和处理器都必须遵循 as-if-serial 语义。

## 三、happens-before 原则

JSR-133 使用 happens-before 的概念常数操作之间的内存可见性。在 JMM 中，如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须存在 happens-before 关系。

JMM 可以通过 happens-before 关系向程序员提供跨线程的内存可见性保证。

与程序员相关的 happens-before 规则如下：

- 程序顺序规则：一个线程中的每个操作，happens-before 于该线程中的任意后续操作。
- 监视器锁规则：对于一个锁的解锁，happens-before 于随后对这个锁的加锁。
- volatile 变量规则：对一个 volatile 域的写，happens-before 于任意后续对这个 volatile 域的读。
- 传递性：如果 A happens-before B，且 B happens-before C，那么 A happens-before C。


---
layout: article
title: Java 并发-Lock
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

happens-before 规则如下：

- 程序顺序规则：一个线程中的每个操作，happens-before 于该线程中的任意后续操作。
- 监视器锁规则：对于一个锁的解锁，happens-before 于随后对这个锁的加锁。
- volatile 变量规则：对一个 volatile 域的写，happens-before 于任意后续对这个 volatile 域的读。
- 传递性：如果 A happens-before B，且 B happens-before C，那么 A happens-before C。
- start() 规则：如果线程 A  执行操作 ThreadB.start()，那么 A 线程的 ThreadB.start() 操作 happens-before 于线程 B 中的任意操作。
- join() 规则：如果线程 A 执行操作 ThreadB.join() 并成功返回，那么线程 B 中的任意操作 happens-before 于线程 A 从 ThreadB.join() 操作成功返回。

## 四、内存间交互操作

关于主内存与工作内存之间具体的交互协议，即一个变量如何从主内存拷贝到工作内存、如何从工作内存同步回主内存之类的实现细节，Java 内存模型中定义了以下 8 中操作来完成，虚拟机实现时必须保证下面每一种操作都是原子的、不可再分的。

- read：把一个变量的值从主内存传输到工作内存中
- load：在 read 之后执行，把 read 得到的值放入工作内存的变量副本中
- use：把工作内存中一个变量的值传递给执行引擎
- assign：把一个从执行引擎接收到的值赋给工作内存的变量
- store：把工作内存的一个变量的值传送到主内存中
- write：在 store 之后执行，把 store 得到的值放入主内存的变量中
- lock：作用于主内存的变量，标识为线程独占状态
- unlock：释放处于锁定状态的主内存变量

## 五、三大特性

### 1. 原子性

Java 内存模型保证了 read、load、use、assign、store、write、lock 和 unlock 操作具有原子性，例如对一个 int 类型的变量执行 assign 赋值操作，这个操作就是原子性的。但是 Java 内存模型允许虚拟机将没有被 volatile 修饰的 64 位数据（long，double）的读写操作划分为两次 32 位的操作来进行，即 load、store、read 和 write 操作可以不具备原子性。

JVM 开放更高层次的 monitorenter 与 monitorexit 指令，反应到 Java 代码中就是 synchronized 关键字。 

### 2. 可见性

可见性是指当一个线程修改了共享变量的值，其它线程能够立即得知这个修改。

主要有三种实现可见性的方式：

- volatile，保证新值能立即同步到主内存，以及每次使用前立即从主内存刷新。
- synchronized，对一个变量执行 unlock 操作之前，必须先把此变量同步回主内存中。
- final，被 final 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸（其它线程通过 this 引用访问到初始化了一半的对象），那么其它线程就能看见 final 字段的值。

### 3. 有序性

有序性是指：在本线程内观察，所有操作都是有序的。在一个线程观察另一个线程，所有操作都是无序的，无序是因为发生了指令重排序。在 Java 内存模型中，允许编译器和处理器对指令进行重排序，重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。

volatile 关键字通过添加内存屏障的方式来禁止指令重排，即重排序时不能把后面的指令放到内存屏障之前。

也可以通过 synchronized 来保证有序性，它保证每个时刻只有一个线程执行同步代码，相当于是让线程顺序执行同步代码。
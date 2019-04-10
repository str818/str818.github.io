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

### 1. API

<div align="center">  <img src="/img/java_concurrency_lock_api.png" width="100%"/> </div><br>

### 2. Lock 与 synchronized 的区别？

- synchronized 是关键字；Lock 是接口。
- synchronized 锁的释放是被动的，当出现异常或者同步代码块执行完之后，才会释放锁；Lock 具备更强大的灵活性，可以控制锁的释放和获取。
- synchronized 不能判断锁的状态；Lock 可以判断锁的状态。
- synchronized 只有非公平锁；Lock 可以实现公平锁与非公平锁。

## 二、LockSupport 工具类

LockSupport 是个工具类，它的主要作用是挂起和唤醒线程，该工具类是创建锁和其他同步类的基础。

LockSupport 是使用 Unsafe 类实现的。

<div align="center">  <img src="/img/java_concurrency_lockSupport.png" width="100%"/> </div><br>

在 Java 6 中，LockSupport 增加了 pack(Object blocker)、parkNanos(Object blocker, long nanos) 和 parkUtil(Object blocker, long deadline) 3 个方法，用于实现阻塞当前线程的功能，其中参数 blocker 是用来标识当前线程在等待的对象（阻塞对象），该对象主要用于问题排查和系统监控。

## 三、AQS

AbstractQueuedSynchronizer 抽象同步队列简称 AQS，它是实现同步器的基础组件，并发包中锁的底层就是使用 AQS 实现的。

AQS 提供了一个 FIFO 队列，它的使用依靠继承来完成，子类通过继承 AQS 并实现所需的方法来管理同步状态。例如常见的 ReentrantLock、CountDownLatch 等。

从使用上，AQS 可以分为两种锁：

- 独占锁：每次只能有一个线程持有锁，例如 ReentrantLock。
- 共享锁：允许多个线程同时获取锁，并发访问共享资源，例如 ReentrantReadWriteLock。

### 1. 同步队列

AQS 依赖内部的同步队列（一个 FIFO 双向队列）来完成同步状态的管理，当前线程获取同步状态失败时，同步器会将当前线程以及等待状态等信息构造成为一个节点（Node）并将其加入同步队列，同时会阻塞当前线程，当同步状态释放时，会把首节点中的线程唤醒，使其再次尝试获取同步状态。 

Node 的主要属性如下：

```java
static final class Node {
    int waitStatus;   // 等待状态。CANCELLED、SIGNAL、CONDITION、PROPAGATE、INITIAL。
    Node prev;        // 前继节点
    Node next;        // 后继节点
    Node nextWaiter;  // 存储在等待队列中的后继节点
    Thread thread;    // 当前线程
}
```

节点是构成同步队列的基础，AQS 类底层的数据结构使用双向链表，是队列的一种实现，包括首节点和尾节点，没有成功获取同步状态的线程将会成为节点加入该队列的尾部。

<div align="center">  <img src="/img/java_concurrency_AQS_1.png" width="80%"/> </div><br>

当一个线程成功地获取了同步状态（或者锁），其他线程将无法获取到同步状态，转而被构造成为节点并加入到同
步队列中，而这个加入队列的过程必须要保证线程安全，因此同步器提供了一个基于 CAS 的设置尾节点的方法：compareAndSetTail(Node expect,Nodeupdate)，它需要传递当前线程「认为」的尾节点和当前节点，只有设置成功后，当前节点才正式与之前的尾节点建立关联。 

同步队列遵循 FIFO，首节点是获取同步状态成功的节点，首节点的线程在释放同步状态时，将会唤醒后继节点，而后继节点将会在获取同步状态成功时将自己设置为首节点。 

设置首节点是通过获取同步状态成功的线程来完成的，由于只有一个线程能够成功获取到同步状态，因此设置头节
点的方法并不需要使用 CAS 来保证，它只需要将首节点设置成为原首节点的后继节点并断开原首节点的 next 引用即可。 

### 2. 独占锁

在获取同步状态时，AQS 维护一个同步队列，获取同步状态失败的线程会加入到队列中进行自旋；移除队列（或停止自旋）的条件是前驱节点是头结点并且成功获得了同步状态。在释放同步状态时，同步器会调用 unparkSuccessor() 方法唤醒后继节点。

#### Ⅰ. 获取 - acqurie()

调用 lock() 方法获取独占锁，获取失败就将当前线程加入同步队列。lock() 方法实际上会调用 AQS 的 acquire() 方法。

```java
public final void acquire(int arg) {
	if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
		selfInterrupt();
}
```

首先调用自定义同步器实现的 tryAcquire(int arg) 方法，该方法保证线程安全的获取同步状态，如果同步状态获取失败，则构造同步节点（独占式 Node.EXCLUSIVE）并通过 addWaiter(Node node) 方法将该节点加入到同步队列的尾部，最后调用 acquireQueued(Node node, int arg) 方法，使得该节点以「死循环」的方法获取同步状态。如果获取不到则阻塞节点中的线程，而被阻塞线程的唤醒主要依靠前驱节点的出队或阻塞线程被中断来实现。

#### Ⅱ. 释放 - release()

调用 release(int arg) 方法释放同步状态，该方法在释放了同步状态后，会唤醒其后继节点。

```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```

unparkSuccessor(Node node) 方法使用 LockSupport 来唤醒处于等待状态的线程。

### 3. 共享锁

#### Ⅰ. 获取 - acquireShared()

在共享式获取的自旋过程中，成功获取到同步状态并退出自旋的条件是 tryAcquireShared(int arg) 方法返回值大于等于 0。

```java
public final void acquireShared(int arg) {
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}
```

#### Ⅱ. 释放 - releaseShared()

对于能够支持多个线程同时访问的并发组件，它和独占式主要区别在于 tryRealeaseShared(int arg) 方法必须确保同步状态线程安全释放，一般是通过循环和 CAS 来保证的，因为释放同步状态的操作会同时来自多个线程。

```java
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```

## 四、重入锁 - ReentrantLock

重入锁表示该锁能够支持一个线程对资源的重复加锁，同时，该锁还支持获取锁时的公平和非公平性的选择。

### 1. 实现原理

重进入是指任意线程在获取到锁之后能够再次获取该锁而不会被锁所阻塞，该特性的实现需要解决以下两个问题。

- 线程再次获取锁。锁需要去识别获取锁的线程是否为当前占据锁的线程，如果是，则再次成功获取。
- 锁的最终释放。线程重复 n 次获取了锁，随后在第 n 次释放该锁后，其他线程能够获取到该锁。锁的最终释放要求锁对于获取进行计数自增，计数表示当前锁被重复获取的次数，而锁被释放时，计数自减，当计数等于 0 时表示锁已经成功释放。

### 2. 公平锁与非公平锁

ReentrantLock 支持两种锁：公平锁与非公平锁。公平锁是指锁的获取顺序应该符合请求的绝对时间顺序，也就是 FIFO。

ReentrantLock 的构造方法无参时是构造非公平锁。

```java
public ReentrantLock() {
    sync = new NonfairSync();
}
```

另外还能通过有参方法控制，true 代表公平锁，false 代表非公平锁。

```java
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

#### 区别

公平锁每次获取到锁为同步队列中的第一个节点，保证请求资源时间上的绝对顺序，而非公平锁有可能刚释放锁的线程下次继续获取该锁，则有可能导致其他线程永远无法获取到锁，造成「饥饿」现象。

公平锁为了保证时间上的绝对顺序，需要频繁的上下文切换，而非公平锁会降低一定的上下文切换，降低性能开销。因此，ReentrantLock 默认选择的是非公平锁，则是为了减少一部分上下文切换，保证了系统更大的吞吐量。

## 五、读写锁 - ReentrantReadWriteLock

读写锁允许同一时刻被多个读线程访问，但是在写线程访问时，所有的读线程和其他的写线程都会被阻塞。读写锁的特性如下：

1. **公平性选择**：支持非公平性（默认）和公平的锁获取方式，吞吐量还是非公平优于公平。
2. **重入性**：支持重入，读锁获取后能再次获取，写锁获取之后能够再次获取写锁，同时也能够获取读锁。
3. **锁降级**：遵循获取写锁，获取读锁再释放写锁的次序，写锁能够降级成为读。

### 1. 写锁

#### Ⅰ. 获取

当读锁已经被读线程获取或者已经被其他写线程获取，则写锁获取失败；否则成功并支持重入，增加写状态。

同步状态的低 16 位表示写锁的获取次数，高 16 位表示读锁的获取次数。

#### Ⅱ. 释放

写锁的释放与 ReentrantLock 的释放过程基本类似，每次释放均减少写状态，当写状态为 0 时表示写锁已被释放，从而等待的读写线程能够继续访问读写锁，同时前次写线程的修改对后续写线程可见。

### 2. 读锁

读锁是一个支持重进入的共享锁，它能够被多个线程同时获取，在没有其他写线程访问时，读锁总会被成功地获取，而所做的也只是增加读状态。

如果当前线程已经获取了读锁，则增加读状态；如果当前线程在获取读锁时，写锁已被其他线程获取，则进入等待状态。

### 3. 锁降级

锁降级是指写锁降级成为读锁，是把持住当前拥有的写锁，再获取到读锁，然后释放写锁的过程。

如果当前线程拥有写锁，然后将其释放，最后再获取读锁，这种分段完成的过程不能成为锁降级。

## 五、Condition

Object 的 wait 和 notify/notifyAll 是与对象监视器配合完成线程间的等待通知机制，而 Condition 是与 Lock 配合完成等待通知机制，前者是 Java 底层级别的，后者是语言级别的，具有更高的可控制性和拓展性。

### 1. 使用方法

<div align="center">  <img src="/img/java_concurrency_condition.png" width="100%"/> </div><br>

### 2. 实现原理

ConditionObject 是 AQS 的内部类，每个 Condition 对象都包含着一个等待队列，该队列是实现等待通知功能的关键。

#### Ⅰ. 等待队列

等待队列是一个 FIFO 的队列，在队列中的每个节点都包含了一个线程引用，该线程就是在 Condition 对象上等待的线程，如果一个线程调用了 Condition.await() 方法，那么该线程将会释放锁、构造成节点加入等待队列并进入等待状态。

<div align="center">  <img src="/img/java_concurrency_wait_queue.png" width="80%"/> </div><br>

在 Object 监视器模型上，一个对象拥有一个同步队列和等待队列，而并发包中的 Lock 拥有一个同步队列和多个等待队列。

#### Ⅱ. await()

调用 Condition 的 await() 方法，会使当前线程进入等待队列并释放锁，同时线程状态变为等待状态。

从队列的角度来看，调用 await() 方法相当于将同步队列中的首节点移动到 Condition 的等待队列中。这一过程中，会通过 addConditionWaiter() 方法把当前线程构造成一个新的节点并将其加入等待队列，而不会直接将同步队列中的节点移动到等待队列中。

#### Ⅲ. signal()

调用 Condition 的 signal() 方法，将会唤醒在等待队列中等待时间最长的节点（首节点），在唤醒节点之前，会将节点移到同步队列中。

signalAll() 方法相当于对等待队列中的每个节点均执行一次 signal() 方法，效果就是将等待队列中所有节点全部移动到同步队列中，并唤醒每个节点的线程。
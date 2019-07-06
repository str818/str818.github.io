---
layout: article
title: 面试 — 每日六问 「1」
tags: 面试

lang: zh-Hans
key: Interview_Six_Question_Per_Day_1
pageview: true
toc: true
show_subscribe: false
---

## 一、注册中心你了解的有哪些？

注册中心能够给客户端提供可供调用的服务列表，客户端在进行远程调用时，根据服务列表然后选择服务提供方的服务地址进行服务调用。

关于注册中心的解决方案，dubbo 支持了 Zookeeper、Redis、Multicast 和 Simple，官方推荐 Zookeeper。Spring Cloud 支持了 Zookeeper、Consul 和 Eureka，官方推荐 Eureka。

- ZK 的设计原则是 CP，即强一致性和分区容错性。他保证数据的强一致性，但舍弃了可用性，如果出现网络问题可能会影响 ZK 的选举，导致 ZK 注册中心的不可用。

- Eureka 的设计原则是 AP，即可用性和分区容错性。他保证了注册中心的可用性，但舍弃了数据一致性，各节点上的数据有可能是不一致的（会最终一致）。

## 二、JDK 中有哪几个线程池？

线程池的基本思想是一种对象池，在程序启动时就开辟一块内存空间，里面存放了众多(未死亡)的线程，池中线程执行调度由池管理器来处理。当有线程任务时，从池中取一个，执行完成后线程对象归池，这样可以避免反复创建线程对象所带来的性能开销，节省了系统的资源。

JDK 通过 Executors 提供四种线程池：

1. CachedThreadPool() 可缓存线程池

  - 线程数无限制
  - 有空闲线程则复用空闲线程，若无空闲线程则新建线程，一定程序减少频繁创建/销毁线程，减少系统开销

2. FixedThreadPool() 定长线程池

  - 可控制线程最大并发数（同时执行的线程数）
  - 超出的线程会在队列中等待

3. ScheduledThreadPool() 定时线程池

  - 支持定时及周期性任务执行

4. SingleThreadExecutor() 单线程化的线程池

  - 有且仅有一个工作线程执行任务
  - 所有任务按照指定顺序执行，即遵循队列的入队出队规则

但是，阿里 Java 开发手册中强制 线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式创建，这样的处理方式能够让开发人员更加明确线程池的运行规则，规避资源耗尽的风险。

ThreadPoolExecutor 是线程中最核心的类，可以通过该类创建线程池。

```java
new ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, 
                      TimUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory, 
                      RejectedExecutionHandler handler);
```

- corePoolSize：核心线程池的大小
- maximumPoolSize：线程池的最大容量
- keepAliveTime：当线程数大于 corePoolSize 时，终止前多余的空闲线程等待新任务的最长时间
- unit：时间单位
- workQueue：保存任务的阻塞队列
- threadFactory：创建线程的工厂
- handler：拒绝策略，线程数和队列都满的情况下，对新添加的任务的处理方式。


## 三、Object 的 hashcode 方法重写了，equals 方法要不要改？

不需要，如果两个对象相等（equals），hashcode 的值也必须相等，但是反过来，hashcode 值相等，两个对象不一定相等（equals）。

## 四、SQL 优化的常见方法有哪些？

1. 禁止使用 select *，值获取必要的字段，需要显示说明列属性。

  读取不需要的列会增加 CPU、IO、NET 消耗，且不能有效的利用覆盖索引。

2. 禁止使用属性隐式转换。
  
  `SELECT uid FROM t_user WHERE phone=13812345678` 会导致全表扫描，而不能命中 phone 索引。

4. 禁止在 Where 条件的属性上使用函数或者表达式。

5. 禁止负向查询、以及 % 开头的模糊查询。

  - 负向查询条件：NOT、!=、<>、!<、!>、NOT in、NOT LIKE 等，会导致全表扫描
  - % 开头的模糊查询，会导致全表扫描

6. 禁止大表使用 Join 查询，禁止大表使用子查询。

  会产生临时表，消耗较多的内存与 CPU，极大影响数据库新性能。

7. 对查询进行优化，要尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

## 五、常见的项目监控指标有哪些？

- 系统：CPU、内存、IO等，nmon 工具可以进行监控。

- 数据库：缓存命中、索引、单条 SQL 性能、数据库线程数、数据池连接数。

- 中间件：消息、负载均衡、缓存（线程数、连接数、日志）。

- 网络：吞吐量、吞吐率。

- 应用：JVM 内存、日志、Full GC 频率。

## 六、Spring Boot 除了自动配置，相比传统的 Spring 有什么其他的区别？

1. Spring Boot 可以建立独立的 Spring 应用程序。

2. 内嵌了如 Tomcat、Jetty 等容器，能够直接跑起来，不需要再进行部署工作了。

3. 整合了常用依赖，提供的 pom 可以简化 mavan 配置。



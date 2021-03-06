---
layout: article
title: Redis - 概述
tags: Redis
 
lang: zh-Hans
key: Redis_Overview
pageview: true
toc: true
show_subscribe: false
---

## 一、什么是 Redis？

Redis（Remote Dictionary Server）是一个使用 C 语言编写、开源的高性能非关系型（NoSQL）的键值对数据库。

Redis 可以存储键和五中不同类型的值之间的映射。键的类型只能为字符串，值支持物种数据类型：字符串、列表、集合、散列表、有序集合。

与传统数据库不同的是 Redis 的数据是存储在内存中的，所以读写速度非常快，因此 Redis 被广泛应用于缓存方向，每秒可以处理超过 10 万次读写操作，是已性能最快的 Key-Value 数据库。另外，Redis 也经常用来做分布式锁，同时也支持事务、持久化、LUA 脚本、LRU 驱动事件、多种集群方案。

## 二、Redis 的优缺点

### 1. 优点

- 读写性能优异。读的速度是 11w 次/s，写的速度是 8.1w 次/s
- 支持数据持久化。支持 AOP 和 RDB 两种持久化方式
- 支持事务。Redis 的所有操作都是原子性的，同时还支持对几个操作合并后原子性执行
- 数据结构丰富。除了 string 类型的 value 外，还支持 hash、set、zset、list 等数据结构
- 支持主从复制。「主机」会自动将数据同步到「从机」，可以进行读写分离

### 2. 缺点

- 数据库容量受到物理内存的限制。不能用作海量数据的高性能读写，因此 Redis适 合的场景主要局限在较小数据量的高性能操作和运算上
- 不具备自动容错和恢复功能。主机从机的宕机都会导致前端部分读写请求失败，需要等待机器重启或者手动切换前端的 IP 才能恢复
- 可用性相对较低。主机宕机，宕机前有部分数据未能及时同步到从机，切换 IP 后还会引入数据不一致的问题，降低了系统的可用性
- 较难支持在线扩容。在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费

## 三、为什么要用 Redis？

### 1. 高性能

用户第一次访问数据库中的某些数据，这个过程会比较慢，因为是从硬盘上读取的。如果将该用户访问的数据存在数缓存中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可。

### 2. 高并发

直接操作缓存能够承受的请求是远远大于直接访问数据库的，所以可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存这里而不用经过数据库。

## 四、为什么要用 Redis 而不是 map/guava 做缓存？

缓存分为本地缓存和分布式缓存。以 Java 为例，使用自带的 map 或者 guava 实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着 JVM 的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，缓存不具有一致性。

使用 Redis 或 Memcached 之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持 Redis 或 Memcached服务的高可用，整个程序架构上较为复杂。

## 五、Reids 为什么这么快？

1. 完全基于内存。数据存在内存中，类似于 HashMap，HashMap 的优势就是查找和操作的时间复杂度都是O(1)；
2. 采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或多线程导致的切换而消耗 CPU，不用取考虑锁的问题，不存在加锁和释放锁操作，没有因为可能出现死锁而导致的性能消耗；
3. 高效的数据结构；
4. 使用多路 I/O 复用模型，非阻塞 IO。
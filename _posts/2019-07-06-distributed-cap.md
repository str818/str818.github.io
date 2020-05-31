---
layout: article
title: 分布式 — CAP 理论
tags: 分布式

lang: zh-Hans
key: Distributed_CAP
pageview: true
toc: true
show_subscribe: false
---

## 一、概述

CAP 理论是指：一个分布式系统最多只能同时满足一致性（Consistency）、可用性（Availability）和分区容错性（Partition tolerance）这三项中的两项。

<div align="center">  <img src="https://s1.ax1x.com/2020/06/01/t3X4UO.png" width="60%"/> </div><br>

## 二、性质

### 1. Consistency 一致性

一致性指 `all nodes see the same data at the same time`，即更新操作成功并返回客户端完成后，所有节点在同一时间的数据完全一致，所以，一致性，说的就是数据一致性。

#### 三种一致性策略

对于关系型数据库，要求更新过的数据能被后续的访问都能看到，这是强一致性。

如果能容忍后续的访问或者完全访问不到，则是弱一致性。

如果经过一段时间后要求能访问到更新后的数据，则是最终一致性。

CAP 中说，不可能同时满足的这个一致性就是强一致性。

### 2. Availability 可用性

可用性指 `Reads and writes always succeed`，即服务一直可用，而且是正常响应时间。

可以用系统可用时间占总时间的比值来衡量，4 个 9 的可用性表示系统 99.99% 的时间是可用的。

### 3. Partition Tolerance 分区容错性

网络分区指分布式系统中的节点被划分为多个区域，每个区域内部可以通信，但是区域之间无法通信。

分区容错性指 `the system continues to operate despite arbitrary message loss or failure of part of the system`，即分布式系统在遇到某节点或网络分区故障的时候，仍然能够对外提供满足一致性和可用性的服务。

## 三、权衡

CAP 不可能三角，对于上述三个指标不可能兼得，只能在 3 个指标中选择 2 个。

在分布式系统中，分区容忍性必不可少，因为需要总是假设网络是不可靠的。因此，CAP 理论实际上是要在可用性和一致性之间做权衡。

可用性和一致性往往是冲突的，很难使它们同时满足。在多个节点之间进行数据同步时，

- 为了保证一致性（CP），不能访问未同步完成的节点，也就失去了部分可用性；
- 为了保证可用性（AP），允许读取所有节点的数据，但是数据可能不一致。

## 参考

- [分布式系统的 CAP 理论](https://www.hollischuang.com/archives/666)

- [CyC2018 - 分布式](https://github.com/CyC2018/CS-Notes/blob/master/notes/分布式.md)

- [分布式协议与算法实战](https://time.geekbang.org/column/intro/100046101)

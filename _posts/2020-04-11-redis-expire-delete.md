---
layout: article
title: Redis - 过期删除机制
tags: Redis

lang: zh-Hans
key: Redis_Expire_Delete
pageview: true
toc: true
---

## 一、过期时间

### 1. 设置命令

```
EXPIRE <KEY> <TTL> : 将键的生存时间设为 ttl 秒
PEXPIRE <KEY> <TTL> :将键的生存时间设为 ttl 毫秒
EXPIREAT <KEY> <timestamp> :将键的过期时间设为 timestamp 所指定的秒数时间戳
PEXPIREAT <KEY> <timestamp>: 将键的过期时间设为 timestamp 所指定的毫秒数时间戳
```

### 2. 数据结构

`redisDb` 结构中，`expires` 字典保存了所有键的过期时间：

```c
typedef struct redisDb {
    ...
    dict *dict;     // 数据库键空间,保存着数据库中所有键值对
    dict *expires   // 过期字典,保存着键的过期时间
    ...
} redisDb;
```

## 二、过期键删除策略

Redis 中有三种过期键删除策略：

- **定时删除**：在设置键的过期时间的同时，创建一个定时器，让定时器在到达过期时间时，立即执行删除操作（CPU不友好、内存友好）
- **惰性删除**：每次获取键时，才判断是否过期，如果过期就删除；没有过期则返回（CPU友好、内存不友好）。
- **定期删除**：每隔一段时间，就对 Redis 进行一遍检查，删除里面过期的键（折中）。

Redis 默认使用惰性删除与定期删除相结合，配合使用。

## 三、淘汰策略

**思考**：如果所有的 Key 都没有设置过期时间，但内存满了怎么办？

`maxmemory` 可以设置最大使用内存，64 位系统中如果不设置可以使用所有的内存。

此外，Redis 提供了几种淘汰策略：

- volatile-lru：从已设置过期时间的数据集中挑选最近最少使用的数据淘汰
- volatile-ttl：从已设置过期时间的数据集中挑选将要过期的数据淘汰
- volatile-random：从已设置过期时间的数据集中任意选择数据淘汰
- allkeys-lru：从数据集中挑选最近最少使用的数据淘汰
- allkeys-random：从数据集中任意选择数据淘汰
- no-enviction：禁止淘汰数据，如果内存满了就只能接受读请求

#### 近似 LRU 算法

LRU 算法需要一个双向链表来记录数据的最近被访问顺序，但是出于节省内存的考虑，Redis 的 LRU 算法并非完整实现。Redis 的 LRU 算法并非完整的实现。Redis 并不会选择最久未被访问的键进行回收，相反它会尝试运行一个近似 LRU 的算法，通过对少量键进行取样，然后回收其中的最久未被访问的键。通过调整每次回收时的采样数量 maxmemory-samples，可以实现调整算法的精度。


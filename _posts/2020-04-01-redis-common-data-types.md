---
layout: article
title: Redis - 五种常用数据类型
tags: Redis
 
lang: zh-Hans
key: Redis_Common_Data_Types
pageview: true
toc: true
show_subscribe: false
---

## 一、String

String 表示的是一个可变的字节数组，可以存储字符串、整数或者浮点数，能够对整个字符串或者子串执行操作，也能够对整数和浮点数执行自增或自减操作。

<div align="center"> <img src="https://s1.ax1x.com/2020/04/01/G82ZUP.png" width="70%"/> </div>

Redis 的字符串是动态字符串，是可以修改的字符串，内部结构实现上类似于 Java 的 ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配，如图中所示，内部为当前字符串实际分配的空间 Capacity 一般要高于实际字符串长度 Len。当字符串长度小于 1M 时，扩容都是加倍现有的空间，如果超过 1M，扩容时一次只会多扩 1M 的空间。需要注意的是字符串最大长度为 512M。

### 1. 应用场景

String 是最常用的一种数据类型，普通的 Key/Value 存储都可以归为此类。Value 不仅是 String，也可以是数字，比如用于计数器，`INCRBY` 命令可以通过原子递增保持计数。

### 2. 编码

字符串的编码可以是 `int`、`raw` 或者 `embstr`：

- **int 编码**：保存 long 型的 64 位有符号整数
- **embstr 编码**：保存长度小于 44 字节的字符串
- **raw 编码**：保存长度大于 44 字节的字符串

### . 常用命令

#### I. 基本

| 命令   | 描述                                                         | CASE                          |
| ------ | ------------------------------------------------------------ | ----------------------------- |
| SET    | 设置指定 `key` 的值                                          | SET key value                 |
| SETNX  | 只有在 `key` 不存在时设置 `key` 的值                         | SETNX key value               |
| SETEX  | 设置指定 `key` 的值，并将 `key` 的生存时间设置为 seconds 秒钟 | SETEX key seconds value       |
| PSETEX | 与 SETEX 类似，但将生存时间的单位变为毫秒                    | PSETEX key milliseconds value |
| GET    | 获取指定 `key` 的值                                          | GET key                       |
| GETSET | 将键 `key` 的值设为 `value` ， 并返回键 `key` 在被设置之前的旧值 | GETSET key value              |

#### II. 批量

| 命令   | 描述                                                        | CASE                           |
| ------ | ----------------------------------------------------------- | ------------------------------ |
| MSET   | 同时为多个键设置值，原子操作                                | MSET key value [key value …]   |
| MSETNX | 当且仅当所有给定键都不存在时， 为所有给定键设置值，原子操作 | MSETNX key value [key value …] |
| MGET   | 返回给定的一个或多个字符串键的值                            | MGET key [key …]               |

#### III. 字符串

| 命令     | 描述                                                         | CASE                      |
| -------- | ------------------------------------------------------------ | ------------------------- |
| STRLEN   | 返回键 `key` 储存的字符串值的长度                            | STRLEN key                |
| APPEND   | 追加到原有字符串的末尾                                       | APPEND key value          |
| SETRANGE | 从 `offset` 开始， 用 `value` 参数覆写(overwrite)键 `key` 储存的字符串值 | SETRANGE key offset value |
| GETRANGE | 截取 `start` 到 `end` 的字符串，包括 `start` 和 `end`        | GETRANGE key start end    |

#### IV. 计数

| 命令        | 描述                                          | CASE                      |
| ----------- | --------------------------------------------- | ------------------------- |
| INCR        | 为键 `key` 储存的数字值加上一                 | INCR key                  |
| INCRBY      | 为键 `key` 储存的数字值加上增量 `increment`   | INCRBY key increment      |
| INCRBYFLOAT | 为键 `key` 储存的值加上浮点数增量 `increment` | INCRBYFLOAT key increment |
| DECR        | 为键 `key` 储存的数字值减去一                 | DECR key                  |
| DECRBY      | 将键 `key` 储存的整数值减去减量 `decrement`   | DECRBY key decrement      |

## 二、List

Redis 将列表数据结构命名为 List 而不是 Array，是因为列表的存储结构用的是链表而不是数组，而且链表还是双向链表。因为它是链表，所以随机定位性能较弱，首尾插入删除性能较优。如果 List 的列表长度很长，使用时一定要关注链表相关操作的时间复杂度。

### 1. 使用场景

TimeLine 或者消息队列。

### 2. 编码

列表的编码可以是 `ziplist` 或者 `linkedlist`，当列表对象可以同时满足以下两个条件时，使用 `ziplist` 编码：

- 列表中保存的所有字符串的长度都小于 64 字节；
- 列表中元素数量小于 512 个

若不能满足上述两个条件，则需要使用 `linkedlist` 编码。

### . 常用命令

RPUSH、RPUSHX、RPOP 与 Lxxx 对应，只不过是在 List 尾部操作。

| 命令       | 描述                                                         | CASE                                  |
| ---------- | ------------------------------------------------------------ | ------------------------------------- |
| LPUSH      | 将一个或多个值 `value` 插入到列表 `key` 的表头               | LPUSH key value [value …]             |
| LPUSHX     | 将值 `value` 插入到列表 `key` 的表头，当且仅当 `key` 存在并且是一个列表 | LPUSHX key value                      |
| LPOP       | 移除并返回列表 `key` 的头元素                                | LPOP key                              |
| RPOPLPUSH  | 将 `source` 中的最后一个元素弹出，插入到 `destination` 的头部 | RPOPLPUSH source destination          |
| LREM       | 根据参数 `count` 的值，移除列表中与参数 `value` 相等的元素。`count > 0` 从表头开始搜索；`count < 0` 从表尾开始搜索；`count = 0` 移除所有的值 | LREM key count value                  |
| LLEN       | 列表 `key` 的长度                                            | LLEN key                              |
| LINDEX     | 返回列表 `key` 中，下标为 `index` 的元素                     | LINDEX key index                      |
| LINSERT    | 将值 `value` 插入到列表 `key` 当中，位于值 `pivot` 之前或之后 | LINSERT key BEFORE\|AFTER pivot value |
| LSET       | 将列表 `key` 下标为 `index` 的元素的值设置为 `value`         | LSET key index value                  |
| LRANGE     | 返回列表 `key` 中指定区间内的元素，区间以偏移量 `start` 和 `stop` 指定 | LRANGE key start stop                 |
| LTRIM      | 对一个列表进行修剪(trim)，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除 | LTRIM key start stop                  |
| BLPOP      | 它是 LPOP 命令的阻塞版本，当给定列表内没有任何元素可供弹出的时候，连接将被 BLPOP 命令阻塞，直到等待超时或发现可弹出元素为止。当给定多个 `key` 参数时，按参数 `key` 的先后顺序依次检查各个列表，弹出第一个非空列表的头元素 | BLPOP key [key …] timeout             |
| BRPOPLPUSH | RPOPLPUSH 的阻塞版本                                         | BRPOPLPUSH source destination timeout |

## 三、Set

Set 是通过哈希表实现的，所以添加、删除、查找的时间复杂度都是 O(1)，Set 中存储的字符串都是各不相同的。

### 1. 使用场景

Set 提供的功能与 List 类似是一个列表的功能，特殊之处在于 Set 是可以自动排重的。共同好友、共同关注、粉丝集合。

### 2. 编码

集合的编码可以是 `intset` 或者 `hashtable`，当集合对象中保存的**所有元素都是整数值**并且**元素数量不超过 512** 个时，使用 `intset` 编码，否则使用 `hashtable` 编码。

### 3. 常用命令

#### I. 基本

| 命令        | 描述                                                         | CASE                                |
| ----------- | ------------------------------------------------------------ | ----------------------------------- |
| SADD        | 将一个或多个 `member` 元素加入到集合 `key` 当中              | SADD key member [member …]          |
| SISMEMBER   | 判断 `member` 元素是否集合 `key` 的成员                      | SISMEMBER key member                |
| SPOP        | 移除并返回集合中的一个随机元素                               | SPOP key                            |
| SRANDMEMBER | 如果命令执行时，只提供了 `key` 参数，那么返回集合中的一个随机元素。如果 `count` 为正数，返回包含 `count` 个元素的数组；`count` 为负数，则返回的数组中的元素可能重复 | SRANDMEMBER key [count]             |
| SREM        | 移除集合 `key` 中的一个或多个 `member` 元素，不存在的 `member` 元素会被忽略 | SREM key member [member …]          |
| SMOVE       | 将 `member` 元素从 `source` 集合移动到 `destination` 集合    | SMOVE source destination member     |
| SCARD       | 返回集合 `key` 的基数(集合中元素的数量)                      | SCARD key                           |
| SMEMBERS    | 返回集合 `key` 中的所有成员                                  | SMEMBERS key                        |

#### II. 集合操作

| 命令        | 描述                                                   | CASE                                |
| ----------- | ------------------------------------------------------ | ----------------------------------- |
| SINTER      | 返回一个集合的全部成员，该集合是所有给定集合的交集     | SINTER key [key …]                  |
| SINTERSTORE | 将交集结果保存到 `destination` 集合                    | SINTERSTORE destination key [key …] |
| SUNION      | 返回一个集合的全部成员，该集合是所有给定集合的并集     | SUNION key [key …]                  |
| SUNIONSTORE | 将并集结果保存到 `destination` 集合                    | SUNIONSTORE destination key [key …] |
| SDIFF       | 返回一个集合的全部成员，该集合是所有给定集合之间的差集 | SDIFF key [key …]                   |
| SDIFFSTORE  | 将差集结果保存到 `destination` 集合                    | SDIFFSTORE destination key [key …]  |

## 四、Hash

Hash 是一个键值对集合，String 类型 key 与 value 的映射表，非常适合用于存储对象。

### 1. 使用场景

假设有多个用户及对应的用户信息，可以用来存储以用户 ID 为 key，将用户信息序列化为 json 格式的 value 进行存储。

### 2. 编码

哈希对象的编码可以是 `ziplist` 或者 `hashtable`。当哈希对象所有键值字符串长度小于 64 字节并且键值对数量小于 512 个时，使用 `ziplist` 编码。

### 3. 常用命令

| 命令    | 描述                                                         | CASE                     |
| ------- | ------------------------------------------------------------ | ------------------------ |
| HSET    | 将哈希表 `hash` 中域 `field` 的值设置为 `value`              | HSET hash field value    |
| HSETNX  | 当且仅当域 `field` 尚未存在于哈希表的情况下， 将它的值设置为 `value` | HSETNX hash field value  |
| HGET    | 返回哈希表中给定域的值                                       | HGET hash field          |
| HEXISTS | 查看哈希表 key 中，指定的字段是否存在                        | HEXISTS key field        |
| HDEL    | 删除哈希表 `key` 中的一个或多个指定域                        | HDEL key field [field …] |
| HGETALL | 获取在哈希表中指定 key 的所有字段和值                        | HGETALL key              |
| HKEYS   | 获取所有哈希表中的字段                                       | HKEYS key                |
| HLEN    | 获取哈希表中字段的数量                                       | HLEN key                 |
| HVALS   | 获取哈希表中所有值                                           | HVALS key                |

## 五、ZSet

相比于 Set，ZSet 增加了一个功能，即集合是有序的，ZSet 的每个成员都带有分数，用于进行排序。

### 1. 使用场景

带有权重的元素，例如用户得分的排行榜。

### 2. 编码

有序集合的编码可以是 `ziplist` 或者 `skiplist`。当有序集合保存的元素数量小于 128 个并且所有元素的长度都小于 64 个字节时，使用 `ziplist` 编码。

### 3. 常用命令

| 命令             | 描述                                                   | CASE                                      |
| ---------------- | ------------------------------------------------------ | ----------------------------------------- |
| ZADD             | 向有序集合添加一个或多个成员，或者更新已存在成员的分数 | ZADD key score1 member1 [score2 member2]  |
| ZCARD            | 获取有序集合的成员数                                   | ZCARD key                                 |
| ZCOUNT           | 返计算在有序集合中指定区间分数的成员数                 | ZCOUNT key min max                        |
| ZREM             | 移除有序集合中的一个或多个成员                         | ZREM key member [member ...]              |
| ZRANK            | 返回有序集合中指定成员的索引                           | ZRANK key member                          |
| ZREVRANGEBYSCORE | 返回有序集中指定分数区间内的成员，分数从高到低排序     | ZREVRANGEBYSCORE key max min [WITHSCORES] |
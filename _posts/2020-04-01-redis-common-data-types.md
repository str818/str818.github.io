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

String 表示的是一个可变的字节数组，我们初始化字符串的内容、可以拿到字符串的长度，可以获取 String 的子串，可以覆盖 String 的子串内容，可以追加子串。

<div align="center"> <img src="https://s1.ax1x.com/2020/04/01/G82ZUP.png" width="100%"/> </div>

Redis 的字符串是动态字符串，是可以修改的字符串，内部结构实现上类似于 Java 的 ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配，如图中所示，内部为当前字符串实际分配的空间 Capacity 一般要高于实际字符串长度 Len。当字符串长度小于 1M 时，扩容都是加倍现有的空间，如果超过 1M，扩容时一次只会多扩 1M 的空间。需要注意的是字符串最大长度为 512M。

### 1. 设置值 O(1)

```
set key value [ex] [px] [nx|xx]
ex 为键值设置秒级过期时间
px 为键值设置毫秒级过期时间
nx 键必须不存在，才可以设置成功，用于添加
xx 键必须存在，才可以设置成功，用于更新
```

```
setnx、setex 与上面的nx、ex作用相同
```

### 2. 获取值 O(1)

```
get key // 不存在则返回 nil
```

### 3. 批量设置/获取

```
mset key value [key value ......]
mget key [key ......]
```

### 4. 计数

若值不是整数，返回错误；若值是整数，返回自增后的结果；若键不存在，按照值为 0 自增，返回结果为 1。

```
// 加一
incr key
decr key

// 加 increment
inceby key increment
decrby key increment
```

### 4. 子串

```
append key value          // 追加字串
getrange key start end    // 返回字串
setrnge key offeset value // 设置字串
```

### 5. 过期时间

```
expire key time   // 设置过期时间(s)
ttl key           // 获取过期时间
```

## 二、List

Redis 将列表数据结构命名为 List 而不是 Array，是因为列表的存储结构用的是链表而不是数组，而且链表还是双向链表。因为它是链表，所以随机定位性能较弱，首尾插入删除性能较优。如果 List 的列表长度很长，使用时我们一定要关注链表相关操作的时间复杂度。

**负下标** 链表元素的位置使用自然数 `0,1,2,....n-1` 表示，还可以使用负数 `-1,-2,...-n` 来表示，`-1` 表示「倒数第一」，`-2` 表示「倒数第二」，那么 `-n` 就表示第一个元素，对应的下标为 `0`。

队列／堆栈 链表可以从表头和表尾追加和移除元素，结合使用 `rpush/rpop/lpush/lpop` 四条指令，可以将链表作为队列或堆栈使用，左向右向进行都可以。

```
lpush key value [value ......]
lpop key value [value ......]

llen key                // List 长度
lindex key index        // 访问制定位置
lrange key start end    // 返回区间
lset key index value    // 修改指定位置的元素
```

**插入元素** 使用 `linsert` 指令在列表的中间位置插入元素，有经验的程序员都知道在插入元素时，我们经常搞不清楚是在指定位置的前面插入还是后面插入，所以在 `linsert` 指令里增加了方向参数 `before/after` 来显示指示前置和后置插入。不过让人意想不到的是 `linsert` 指令并不是通过指定位置来插入，而是通过指定具体的值。这是因为在分布式环境下，列表的元素总是频繁变动的，意味着上一时刻计算的元素下标在下一时刻可能就不是你所期望的下标了。

```
> rpush ireader go java python
(integer) 3
> linsert ireader before java ruby
(integer) 4
> lrange ireader 0 -1
1) "go"
2) "ruby"
3) "java"
4) "python"
```
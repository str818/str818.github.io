---
layout: article
title: Redis - 底层数据结构
tags: Redis
 
lang: zh-Hans
key: Redis_Deep_Data_Struct
pageview: true
toc: true
show_subscribe: false
---

## 一、简单动态字符串（SDS）

Redis 没有直接使用 C 语言传统的字符串表示，而是自己构建了一种明为简单动态字符串（Simple Dynamic String，SDS）的抽象类型，并将 SDS 用作 Redis 的默认字符串表示。

### 1. 数据结构

SDS 字符串可以看做是对 C 字符串的进一步封装，但是内部实现十分巧妙，有效避免了内存溢出、申请销毁开销过大等问题。

<div align="center"> <img src="https://s1.ax1x.com/2020/04/05/GDYxER.png" width="70%"/> </div> 

```c
struct __attribute__ ((__packed__)) sdshdrX { // X 代表 bit 长度
    uintX_t len; 					// 字符串的实际长度
    uintX_t alloc;				// buf[] 分配的内存空间大小
    unsigned char flags;	// 用于标记 sdshdr 的结构体类型
    char buf[];						// 字符串的实际存储区域
};

//  Redis 为节约内存占用，分别定义了不同长度 buf 下的数据结构：
struct __attribute__ ((__packed__)) sdshdr5 {...}
struct __attribute__ ((__packed__)) sdshdr8 {...}
struct __attribute__ ((__packed__)) sdshdr16 {...}
struct __attribute__ ((__packed__)) sdshdr32 {...}
struct __attribute__ ((__packed__)) sdshdr64 {...}
```

### 2. 获取长度 O(1)

C 字符串并不记录自身的长度信息，所以为了获取一个 C 字符串的长度需要遍历整个字符串，这个操作的复杂度为 O(N)。而 SDS 在 len 属性中存储了 SDS 本身的长度，所以获取一个 SDS 长度的复杂度仅为 O(1)。

### 3. 杜绝缓冲区溢出

C 字符串不记录自身的长度，所以在进行字符串更新操作时，假设已经分配了足够多的内存，如果假设不成立就会产生缓冲区溢出。而 SDS 的空间分配策略完全杜绝了发生缓冲区溢出的可能性，当对 SDS 进行修改时，会先检查 SDS 的空间是否满足修改的需求，如果不满足的话，会自动将 SDS 的空间扩展至执行修改所需的大小。

### 4. 内存分配策略

Redis 作为数据库，经常被用于速度要求严苛、数据被品牌修改的场合，如果每次修改字符串的长度都需要执行一次内存重分配的话，那么光是执行内存重分配的时间就会占去修改字符所用时间的一大部分，如果这种修改频繁发生，可能还会对性能造成影响。

通过未使用空间，SDS 实现了「空间预分配」和「惰性空间释放」两种优化策略。

#### I. 空间预分配

空间预分配用于**优化 SDS 字符串增长**操作：当对 SDS 进行修改，并且需要对 SDS 进行空间扩展的时候，程序不仅会为 SDS 分配修改所必须的空间，还会分配额外的未使用空间。分配策略如下：

- 如果原字符串中的空余空间足够使用（alloc - len >= addlen），那么它并不会进行内存分配

- 如果需要分配空间，且追加后字符串总长度小于定义的`SDS_MAX_PREALLOC(1MB)`，其分配的实际内存大小为所需的两倍，以防备继续追加
- 如果追加后字符串总长度大于 1MB，那么分配的空余内存大小为 1MB

通过这种预分配策略，SDS 将连续增长 N 次字符串所需的内存分配次数从必定 N 次降低为最多 N 次。

#### II. 惰性空间释放

惰性空间释放用于优化 SDS 字符串缩短操作：当缩短 SDS 保存的字符串时，程序并不立即使用内存重分配来回收缩短后多出来的字节，而是使用 free 属性将这些字节的数量记录起来，并等待将来使用。

通过惰性空间释放策略，SDS 避免了缩短字符串时所需的内存重分配操作，并为将来可能有的增长操作提供了优化。

### 5. 二进制安全

C 字符串中的字符必须符合某种编码，并且除了字符串的末尾之外，字符串里面不能包含空字符，这些限制使得 C 字符串只能保存文本数据，而不能保存像图片、音频、视频、压缩文件这样的二进制数据。而 SDS 会以处理二进制的方式来处理 SDS 存放在 buf 数组里的数据，程序不会对其中的数据做任何限制或过滤。

## 二、链表

链表是一种常见的数据结构，在 Redis 中使用非常广泛，列表对象的底层实现之一就是链表。Redis 链表使用双向无环链表，提供了高效的节点重排能力和节点访问方式，并且可以通过左/右增删来灵活的调整链表的长度。

### 1. 数据结构

链表节点使用 `listNode` 数据结构表示：

```C
typedef struct listNode {
    struct listNode *prev; // 前置节点指针
    struct listNode *next; // 后置节点指针
    void *value;           // 该节点值指针
} listNode;
```

Redis 为了方便的操作链表，提供了一个 list 结构体来持有链表节点:

```c
typedef struct list {
    listNode *head; // 表头节点指针
    listNode *tail; // 表尾节点指针
    void *(*dup)(void *ptr); // 节点值复制函数
    void (*free)(void *ptr); // 节点值释放函数
    int (*match)(void *ptr, void *key); // 节点值比较函数
    unsigned long len; // 链表包含的节点数量
} list;
```

### 2. 特点

从上面链表的数据结构可以看出，Redis 中的链表有如下特点：

- **双端**：链表节点带有 prev 和 next 指针，获取某个节点的前置和后置节点的复杂度都是 $O(1)$
- **无环**：表头节点的 prev 指针和表尾节点的 next 指针都指向 NULL，对链表的访问以 NULL 尾终点
- **表头和表尾指针**：通过 list 结构的 head 和 tail 指针，获取表头和表尾节点的复杂度均为 $O(1)$

- **链表长度**：通过 list 中的 len，获取链表长度复杂度 $O(1)$

- **多态**：链表节点使用 `void*` 指针保存节点值，可以用于保存各种不同类型的值

## 三、压缩列表

压缩列表（ziplist）是列表和哈希对象的底层实现之一，它是为了尽可能地节约内存而设计的特殊编码双端链表，可以储存字符串值和整数值。

### 1. 数据结构

<div align="center"> <img src="https://s1.ax1x.com/2020/04/06/GyRMPx.png" width="80%"/> </div>

- zlbytes：记录整个压缩列表的内存字节数
- zltail：记录压缩列表表尾节点距离压缩列表的起始地址有多少字节
- zllen：压缩列表包含节点的数量
- entry：压缩列表包含的各个节点
- zlend：用户标记压缩列表的末端

每个压缩列表节点可以保存一个字节数组或者一个整数值，该节点由 `previous_entry_length`、`encoding`、`content` 三个部分组成。

- previous_entry_length：保存着前置节点长度，可以利用指针运算计算出前置节点的起始位置
- encoding：保存者当前节点值的数据类型与长度，编码方式取决于存储的值
- content：保存着该节点的实际值

### 2. 内存重分配

压缩链表的实质是将数据按照一定规则存储在一块连续的内存区域中，以达到节省内存的目的。**但这种数据结构并不擅长做修改操作，一旦数据大小发生变化，就会触发`realloc`进行内存分配**，可能发生的情况有三种：

- 由于内存区域是连续的，如果后面的空余内存空间足够大，那么直接在后面扩展内存空间；
- 如果剩余空间不足，将会从堆中寻找一块新的内存区域，将旧数据拷贝过去并写入新数据；
- 由于节点中存在 `previous_entry_length` 结构，保存着前置节点的长度，如果该值由小于 254 增加至大于 254，那么 `previous_entry_length` 所占据的内存空间也将发生变化，从而引发连锁更新。

## 四、跳跃表

跳跃表（skiplist）是一种有序数据结构，它通过在每个节点中维持多个指向其他节点的指针，从而达到快速访问节点的目的。

在一个链表中，如果要查找某个数据，需要从头开始逐个进行比较，知道找到数据的那个节点，也就是说，时间复杂度 $O(n)$。假如，我们在一些节点中增加额外的指针，指向更后面的节点，这样在查找的过程中就不需要逐个与每个节点比较了。

例如，我们想在下图中找到 22 这个节点，首先从最上层开始查找，找到了值为 19 的节点，19 比 22 要小，这样只需要在 19 节点之后查找就可以了，直接跳过了 19 前面的所有节点；之后从第二层查找，发现 19 在第二层的下一个节点为 26，比 22 要大，这时就需要再往下走一层，第三层找到了值为 22 的节点。

<div align="center"> <img src="https://s1.ax1x.com/2020/04/06/GyHfHK.png" width="90%"/> </div>

理想的情况是上一层链表的个数是下一层链表个数的一半，这样查找过程就相当于二分查找，使得查找的时间复杂度可以降低到 $O(logn)$，但是如果在插入节点的过程中，严格的保证这种 2:1 的关系，就需要把新插入节点后面的所有节点都进行调整，显然成本太大了。

那么 Redis 是怎么处理这一问题的呢？skiplist 为每个节点随机出一个层数，也就是说新插入一个节点不会影响其它节点的层数。这让它在插入性能上明显优于平衡数的方案。

### 1. 数据结构

- 跳跃表节点

```c
typedef struct zskiplistNode {
    // 成员对象
    sds ele;
    // 成员分值
    double score;
    // 后退指针
    struct zskiplistNode *backward;
    // 层结构体
    struct zskiplistLevel {
        // 前进指针，表示该节点第 x 层的下一个节点
        struct zskiplistNode *forward;
        // 跨度，表示从该节点到第 x 层的下一个节点跳跃了多少节点
        unsigned long span;
    } level[];
} zskiplistNode;
```

- 跳跃表

```c
typedef struct zskiplist {
    // 指向表头节点和表尾节点
    struct zskiplistNode *header, *tail;
    // 节点的数量，不包含表头节点
    unsigned long length;
    // 节点的最大层数，不包含表头节点
    int level;
} zskiplist;
```

### 2. 随机层数算法

跳跃表节点的层高由随机数函数 `zslRandomLevel()` 决定：

```c
int zslRandomLevel(void) {
    int level = 1;
    while ((random()&0xFFFF) < (ZSKIPLIST_P * 0xFFFF))
        level += 1;
    return (level<ZSKIPLIST_MAXLEVEL) ? level : ZSKIPLIST_MAXLEVEL;
}
```

初始层高均为 1，`random()`函数返回一个[0…1)的随机数，如果该随机数小于`ZSKIPLIST_P = 0.25`，那么将 level 值加一，并一直循环执行，直到不满足该条件或达到最大层级数`ZSKIPLIST_MAXLEVEL = 32`。可以看出，节点层数的计算不依赖于其它节点，而且生成越高的节点层数的概率越低。

### 3. 为什么使用跳跃表

首先，因为 zset 要支持随机的插入和删除，所以不适合使用数组来实现。关于排序的问题，我们也很容易想到 **红黑树/平衡树** 这样的树形结构，为什么 Redis 不适用这样的一些数据结构呢？

- **性能考虑**：在高并发的情况下，树形结构需要执行一些类似于 rebalance 这样可能涉及整棵树的操作，相对来说，跳跃表的变化只涉及局部。

- **实现考虑**：在复杂度与红黑树相同的情况下，跳跃表实现起来更简单，看起来也更直观。

## 五、字典

字典（dict）是哈希表的底层实现之一，当一个哈希表包含的键值对比较多，或者键值对中的元素都是比较长的字符串时，Redis 将会使用字典作为哈希键的底层实现。

### 1. 数据结构

Redis 的字典使用哈希表（hashtable）作为底层实现：

```c
typedef struct dictht {
    // 哈希表数组
    dictEntry **table;
    // 哈希表大小
    unsigned long size;
    // 哈希表大小掩码，用于计算索引值，总是等于 size - 1
    unsigned long sizemask;
    // 该哈希表已有节点的数量
    unsigned long used;
} dictht;
```

二级指针 table 指向一个`dictEntry`结构体数组，每个 `dictEntry` 结构都保存着一个键值对：

```c
typedef struct dictEntry {
    // 键
    void *key;
    // 值可以是：
    union {
        // 一个指针
        void *val;
        // uint64
        uint64_t u64;
        // int64
        int64_t s64;
        // 浮点数
        double d;
    } v;
    // 指向下个哈希表节点，形成链表,用来解决哈希冲突（链地址法）
    struct dictEntry *next;
} dictEntry;
```

所以一个大小为 4 个哈希表结构如下所示：

<div align="center"> <img src="https://s1.ax1x.com/2020/04/06/G6MDat.png" width="90%"/> </div>

而一个字典的数据结构如下：

```c
typedef struct dict {
    // 类型特定函数
    dictType *type;
    // 私有数据
    void *privdata;
    // 哈希表
    dictht ht[2];
    // rehash 索引，当 rehash 不在进行时，值为 -1
    long rehashidx;
    // 字典标记，目前正在运行的安全迭代器的数量
    unsigned long iterators;
} dict;
```

`ht` 属性是一个两元数组，数组中的每个项都是一个 `dictht` 哈希表，一般情况下，字典中的数都存储在 `ht[0]` 哈希表，`ht[1]` 哈希表只由在对 `ht[0]` 哈希表进行 rehash 时使用。

### 2. 键冲突

当有两个或以上数量的键被分配到了哈希表数组的同一个索引上时，称这些键发生了冲突。

Redis 的哈希表使用链地址法来解决键冲突，每个哈希表节点都有一个 next 指针，多个哈希表节点可以用 next 指针构成一个单向链表，被分配到同一个索引上的多个节点可以用这个单向链表链接起来，通过这种方式就解决了键冲突的问题。

### 3. rehash

随着操作的不断执行，哈希表保存的键值对会逐渐地增多或者减少，为了让哈希表的负载因子维持在一个合理的范围之内，当哈希表保存的键值对数量太多或者太少时，会对哈希表进行扩展或收缩，步骤如下：

1. 为字典的 `ht[1]` 哈希表分配空间，这个哈希表的空间大小取决于要执行的操作，以及 `ht[0]` 当前包含的键值对数量
   - 扩展操作：`ht[1]` 的大小为第一个大于等于 `ht[0].used * 2` 的 $2^n$
   - 收缩操作：`ht[1]` 的大小为第一个大于等于 `ht[0].used` 的 $2^n$
2. 将保存在 `ht[0]` 中的所有键值对 rehash 到 `ht[1]` 上
3. 当 `ht[0]` 包含的所有键值对都迁移到了 `ht[1]` 之后，释放 `ht[0]`，将 `ht[1]` 设置为 `ht[0]`，并在 `ht[1]` 新创建一个空白哈希表，为下一次 rehash 做准备

### 4. 渐进式 rehash

为了避免 rehash 对服务器性能造成影响，哈希表的扩展和收缩 rehash 并不是一次性、集中式完成的，而是分多次、渐进式完成的，步骤如下：

1. 为 `ht[1]` 分配空间，让字典同时持有 `ht[0]` 和 `ht[1]` 两个哈希表
2. 在字典中维持一个索引计数器变量 `rehashidx`，并将它的值设置为 0，表示 rehash 工作正式开始
3. 在 rehash 进行期间，每次对字典执行添加、删除、查找或者更新操作时，程序除了执行指定的操作外，还会顺带将 `ht[0]` 哈希表在 `rehashidx` 索引上的所有键值对 rehash 到 `ht[1]`，当 rehash 工作完成之后，程序将 `rehashidx` 属性的值加一
4. 随着字典操作的不断执行，最终在某个时间点上，`ht[0]` 的所有键值对都会被 rehash 至 `ht[1]`，这时程序将 `rehashidx` 属性的值设为 -1，表示 rehash 操作已完成

## 六、整数集合

整数集合（intset）是集合键的底层数据结构之一，当一个集合只包含整数值且元素数量不超过配置文件中的`set-max-intset-entries`时，集合键就会采用 intset 做为底层实现。

### 1. 数据结构

```c
typedef struct intset {
    // 编码方式 (int16、int32、int64)
    uint32_t encoding;
    // 集合包含的元素数量
    uint32_t length;
    // 保存元素的数组
    int8_t contents[];
} intset;
```

### 2. 类型升级

如果给定的值超过了当前数组的编码，那么 Redis 会将 intset 升级为更大的编码然后再插入给定整数。升级整数集合并添加新元素分为三步进行：

1. 根据新元素的类型，扩展整数集合底层数组的空间大小，并为新元素分配空间
2. 将底层数组现有的所有元素都转换成与新元素相同的类型，并将类型转换后的元素放置到正确的位上，而且在放置元素的过程中，需要继续维持底层数组的有序性质不变
3. 将新元素添加到底层数组

类型升级既能提升灵活性，也能够节约内存，不过整数集合不支持降级操作，一旦对数组进行了升级，编码就会一直保持升级后 的状态。

## 参考

- 《Redis 设计与实现》
- [Redis 数据结构的设计与实现](https://www.wingsxdu.com/post/database/redis/struct/)

- [Redis(2)——跳跃表](https://www.javazhiyin.com/60006.html)
---
layout: article
title: Java - equals() 和 hashCode()
tags: Spring

lang: zh-Hans
key: Java_Equals_And_Hashcode
pageview: true
toc: true
show_subscribe: false
---

## 一、equals() 的作用

equals() 的作用是 **用来判断两个对象是否相等**。

equals() 定义在 JDK 的 Object.java 中。通过判断两个对象的地址是否相等(即，是否是同一个对象)来区分它们是否相等。

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

既然 Object.java 中定义了 equals() 方法，这就意味着所有的 Java 类都实现了 equals() 方法，所有的类都可以通过 equals() 去比较两个对象是否相等。 但是，我们已经说过，使用默认的 “equals()” 方法，等价于 “==” 方法。因此，我们通常会重写 equals() 方法：若两个对象的内容相等，则 equals() 方法返回 true；否则，返回 fasle。 

根据 “类是否覆盖equals()方法”，将它分为 2 类。

1. 若某个类没有覆盖 equals() 方法，当它的通过 equals() 比较两个对象时，实际上是比较两个对象是不是同一个对象。这时，等价于通过 “==” 去比较这两个对象。
2. 我们可以覆盖类的 equals() 方法，来让 equals() 通过其它方式比较两个对象是否相等。通常的做法是：若两个对象的内容相等，则 equals() 方法返回 true；否则，返回 fasle。

Java 对 equals() 有如下要求：

- 自反性：x.equals(x) 必须返回是"true"。
- 对称性：如果 x.equals(y) 返回是"true"，那么 y.equals(x) 也应该返回是"true"。
- 传递性：如果 x.equals(y) 返回是"true"，而且 y.equals(z) 返回是"true"，那么 z.equals(x) 也应该返回是"true"。
- 一致性：如果 x.equals(y) 返回是"true"，只要 x 和 y 内容一直不变，不管你重复 x.equals(y) 多少次，返回都是"true"。
- 非空性：对于任意不为 null 的引用值 x，x.equals(null) 返回 false。

## 二、equals() 与 == 的区别是什么？

**==**：它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同一个对象。

**equals()**：它的作用也是判断两个对象是否相等。但它一般有两种使用情况：

- 类没有覆盖 equals() 方法。则通过 equals() 比较该类的两个对象时，等价于通过“==”比较这两个对象。
- 类覆盖了 equals() 方法。一般，我们都覆盖 equals() 方法来两个对象的内容相等；若它们的内容相等，则返回 true(即，认为这两个对象相等)。

## 三、hashCode() 的作用

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个 int 整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。

hashCode() 定义在 JDK 的 Object.java 中，这就意味着 Java 中的任何类都包含有 hashCode() 函数。

虽然，每个 Java 类都包含 hashCode() 函数。但是，仅仅当创建并某个“类的散列表”时，该类的 hashCode() 才有用 (作用是：确定该类的每一个对象在散列表中的位置；其它情况下 (例如，创建类的单个对象，或者创建类的对象数组等等)，类的 hashCode() 没有作用。

上面的散列表，指的是：Java 集合中本质是散列表的类，如HashMap，Hashtable，HashSet。

**hashCode() 在散列表中才有用，在其它情况下没用。**在散列表中hashCode() 的作用是获取对象的散列码，进而确定该对象在散列表中的位置。

若两个元素相等，它们的散列码一定相等；但反过来确不一定。在散列表中，
1、如果两个对象相等，那么它们的 hashCode() 值一定要相同；
2、如果两个对象 hashCode() 相等，它们并不一定相等，可能哈希冲突。


## 四、hashCode() 和 equals() 的关系

### 1. 不会创建「类对应的散列表」

不会在 HashSet, Hashtable, HashMap 等等这些本质是散列表的数据结构中用到该类时，hashCode() 没有任何作用！该类的 hashCode() 和 equals() 没有半毛钱关系！

### 2. 会创建「类对应的散列表」

1. 如果两个对象相等，那么它们的 hashCode() 值一定相同，这里的相等是指，通过 equals() 比较两个对象时返回 true。
2. 如果两个对象 hashCode() 相等，它们并不一定相等。因为在散列表中，hashCode() 相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。补充说一句：“两个不同的键值对，哈希值相等”，这就是哈希冲突。

在这种情况下。若要判断两个对象是否相等，除了要覆盖 equals() 之外，也要覆盖 hashCode() 函数。否则，equals() 无效。

以 HashSet 为例，判断两个对象是否相等的规则是：先判断 hashCode 是否相等，如果相等才继续判断 equals。


## 参考

- [Java提高篇——equals()与hashCode()方法详解](https://www.cnblogs.com/Qian123/p/5703507.html)
- [Java hashCode() 和 equals()的若干问题解答](https://www.cnblogs.com/skywang12345/p/3324958.html)
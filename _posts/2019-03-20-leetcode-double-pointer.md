---
layout: article
title: 【Leetcode】双指针系列题解
tags: Java

lang: zh-Hans
key: Leetcode_Double_Pointer
pageview: true
toc: true
show_subscribe: false
---

## 两个链表的交集

|   一刷   | 二刷 | 三刷 | 四刷 |
| :------: | :--: | :--: | :--: |
| 19.03.20 |      |      |      |

[Leetcode - 160 Intersection of Two Linked Lists (Easy)](https://leetcode.com/problems/intersection-of-two-linked-lists/)

题目描述：给定两个链表，求出两个链表相交的结点。

```
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
Output: Reference of the node with value = 8
```

<div align="center">  <img src="/img/leetcode-160.png" width="50%"/> </div><br>

解题思路：两个指针分别在两个链表上向前移动，当其中一个指针移动到了链表的末尾，将该指针指向另一个链表的头结点，另一个指针同理，当两个指针指向同一个结点时，该结点即为链表的相交结点。

我在这里绕了一会，其实很简单，交换指针指向的链表就是为了计算出两个链表私有链的长度，比如上图中，当指针 a 从 链表 A 的头结点移动到了最后，将其指向链表 B 的头结点，此时指针 b 指向链表 B 的结点 5，再向前走一步，a 指针就指向了链表 B 的 结点 0，b 指针指向了链表 A 的头结点 4，最后两个指针同时向后移动，就能同时到达相交结点 8。

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    ListNode a = headA;
    ListNode b = headB;
    while(a != b){
        a = a == null? headB : a.next;
        b = b == null? headA : b.next;
    }
    return a;
}
```
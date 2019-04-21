---
layout: article
title: 算法 — 详解课程笔记
tags: 算法

lang: zh-Hans
key: Algorithm_Detailed_Course_Notes
pageview: true
toc: true
show_subscribe: false
---

[课程链接](https://www.bilibili.com/video/av46292575/)

## 一、数组和链表

### 1. 反转链表

[Leetcode - 206 Reverse Linked List (Easy)](https://leetcode.com/problems/reverse-linked-list/)

解法一：遍历

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode cur = head;
        head = head.next;
        cur.next = prev;
        prev = cur;
    }
    return prev;
}
```

解法二：递归

```java
public ListNode reverseList(ListNode head) {
    if(head == null || head.next == null) return head;
    ListNode p = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    return p;
}
```

### 2. 成对交换节点

[Leetcode - 24 Swap Nodes in Pairs (Medium)](https://leetcode.com/problems/swap-nodes-in-pairs/)

解法一：遍历

```java
public ListNode swapPairs(ListNode head) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;
    while(prev.next != null && prev.next.next != null){
        ListNode first = prev.next;
        ListNode second = prev.next.next;
        first.next = second.next;
        second.next = first;
        prev.next = second;
        prev = first;
    }
    return dummy.next;
}
```

解法二：递归

```java
public ListNode swapPairs(ListNode head) {
    if(head == null || head.next == null) return head;
    ListNode n = head.next;
    head.next = swapPairs(head.next.next);
    n.next = head;
    return n;
}
```

### 3. 单链表中的环

[Leetcode - 141 Linked List Cycle (Easy)](https://leetcode.com/problems/linked-list-cycle/)

```java
public boolean hasCycle(ListNode head) {
    if(head == null) return false;
    ListNode slow = head, fast = head;
    while(fast.next != null && fast.next.next != null){
        slow = slow.next;
        fast = fast.next.next;
        if(slow.val == fast.val) return true;
    }
    return false;
}
```




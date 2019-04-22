---
layout: article
title: 算法 — 经典例题
tags: 算法

lang: zh-Hans
key: Algorithm_Classic_Examples
pageview: true
toc: true
show_subscribe: false
---

[课程链接](https://www.bilibili.com/video/av46292575/)

## 一、复杂度

[http://www.bigocheatsheet.com/](http://www.bigocheatsheet.com/)

<div align="center">  <img src="/img/algorithm_common_data_structure_operations.png" width="100%"/> </div><br>

<div align="center">  <img src="/img/algorithm_array_sorting_algorithms.png" width="100%"/> </div><br>


## 二、数组和链表

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

### 4. 单链表中的环 II

[Leetcode - 142 Linked List Cycle II (Medium)](https://leetcode.com/problems/linked-list-cycle-ii/)

题目描述：找到环的入口位置。

```java
public ListNode detectCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null){
        fast = fast.next.next;
        slow = slow.next;
        if (fast == slow){
            ListNode slow2 = head; 
            while (slow2 != slow){
                slow = slow.next;
                slow2 = slow2.next;
            }
            return slow;
        }
    }
    return null;
}
```

### 5. 每 K 个一组反转链表

[Leetcode - 25 Reverse Nodes in k-Group (Hard)](https://leetcode.com/problems/reverse-nodes-in-k-group/)

解法一：遍历

```java
public ListNode reverseKGroup(ListNode head, int k) {
    if(head == null || head.next == null || k < 2) return head;
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy, tail = dummy, temp;
    while(true){
        int count = k;
        while(count > 0 && tail != null){
            count--;
            tail = tail.next;
        }
        if(tail == null) break;
        head = prev.next;
        while(prev.next != tail){
            temp = prev.next;
            prev.next = temp.next;
            temp.next = tail.next;
            tail.next = temp;
        }
        prev = head;
        tail = head;
    }
    return dummy.next;
}
```

解法二：递归

```java
public ListNode reverseKGroup(ListNode head, int k) {
    ListNode curr = head;
    int count = 0;
    while (curr != null && count != k) {
        curr = curr.next;
        count++;
    }
    if (count == k) {
        curr = reverseKGroup(curr, k);
        while (count-- > 0) {
            ListNode tmp = head.next;
            head.next = curr;
            curr = head;
            head = tmp;
        }
        head = curr;
    }
    return head;
}
```





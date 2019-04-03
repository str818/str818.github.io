---
layout: article
title: Leetcode —「双指针」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Double_Pointer
pageview: true
toc: true
show_subscribe: false
---



## 寻找重复数

[Leetcode - 287 Find the Duplicate Number (Medium)](https://leetcode.com/problems/find-the-duplicate-number/)

题目描述：给定一个包含 n + 1 个整数的数组，其中每一个整数均介于 [1,n] 之间，证明其至少有一个重复元素存在。假设只有一个数字出现重复，找出这个重复的数字。

要求：
1. 不能修改原数组
2. 空间复杂度为 O(1)
3. 时间复杂度小于 $O(n^2)$
4. 数组中只存在一个重复数，但是可能重复多次

解题思路：这道题加上要求之后就有很多限制了，可以把这道题巧妙的转化成求环的入口问题，类似[Leetcode - 142 Linked List Cycle II (Medium)](https://leetcode.com/problems/linked-list-cycle-ii/) 。把数组中的 index 看成链表结点的值，数组中 index 对应的 value 看成结点的 next，由于一定存在重复数字，所以一定会有两个结点的 next 指向相同的位置，所以一定会存在环。从数组的 0 位置开始，由于整数介于 [1,n] 之间，不同有结点指向数组的 0 位置。

```java
public int findDuplicate(int[] nums) { 
    int slow = nums[0];
    int fast = nums[0];
    do{
        slow = nums[slow];
        fast = nums[nums[fast]];
    }while(slow != fast);
    
    fast = nums[0];
    while(slow != fast){
        slow = nums[slow];
        fast = nums[fast];
    }
    return slow;
}
```

## 找到链表环的第一个节点

[Leetcode - 142 Linked List Cycle II (Medium)](https://leetcode.com/problems/linked-list-cycle-ii/) 

解题思路：假设链表头是 X，环的第一个节点是 Y，slow 和 fast 第一次的交点是 Z。各段的长度分别是 a,b,c。slow 每次向前走一个节点，fast 每次向前走两个结点，第一次相遇时 slow 走过的距离为 a+b，fast 走过的距离为 a+b+c+b，因为 fast 的速度是 slow 的两倍，所以 fast 走的距离是 slow 的两倍，由 2(a+b) = a+b+c+b 可得 a = c。这时，让两个指针分别从 X 和 Z 开始走，每次都走一步，那么正好会在 Y 相遇，也就是环的第一个节点。

<div align="center">  <img src="/img/leetcode-142.png" width="70%"/> </div><br>

```java
public ListNode detectCycle(ListNode head) {
    if(head == null || head.next == null) return null;
    ListNode slow = head.next;
    ListNode fast = head.next.next;
    while(slow != fast){
        if(fast == null || fast.next == null || fast.next.next == null) return null;
        slow = slow.next;
        fast = fast.next.next;
    }
    slow = head;
    while(slow != fast){
        fast = fast.next;
        slow = slow.next;
    }
    return slow;
}
```
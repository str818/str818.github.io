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

## 两个链表的交集

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

## 判断单链表是否有环

[Leetcode - 141 Linked List Cycle (Medium)](https://leetcode.com/problems/linked-list-cycle/)

```java
public boolean hasCycle(ListNode head) {
    if(head==null) return false;
    ListNode slow = head;
    ListNode fast = head;
    while(fast.next!=null && fast.next.next!=null) {
        slow = slow.next;
        fast = fast.next.next;
        if(slow==fast) return true;
    }
    return false;
}
```
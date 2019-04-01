---
layout: article
title: Leetcode —「链表」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Linked_List
pageview: true
toc: true
show_subscribe: false
---

## 反转链表

[Leetcode - 206 Reverse Linked List (Easy)](https://leetcode.com/problems/reverse-linked-list/)

```
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
```

解法一：递归

```java
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) return head;
    ListNode p = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    return p;
}
```

解法二：遍历

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode nextTemp = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nextTemp;
    }
    return prev;
}
```

## 判断单链表是否有环

[Leetcode - 141 Linked List Cycle (Medium)](https://leetcode.com/problems/linked-list-cycle/)

```java
public boolean hasCycle(ListNode head) {
    ListNode slow = head, fast = head;
    while(fast != null && fast.next != null){
        slow = slow.next;
        fast = fast.next.next;
        if(slow == fast){
            return true;
        }
    }
    return false;
}
```

## 成对交换结点

[Leetcode - 24 Swap Nodes in Pair (Medium)](https://leetcode.com/problems/swap-nodes-in-pairs/)

题目描述：不能更改链表本身的值。

```
Given 1->2->3->4, you should return the list as 2->1->4->3.
```

```java
public ListNode swapPairs(ListNode head) {
    if ((head == null) || (head.next == null))
        return head;
    ListNode n = head.next;
    head.next = swapPairs(head.next.next);
    n.next = head;
    return n;
}
```

## 奇偶链表

[Leetcode - 328 Odd Even Linked List (Medium)](https://leetcode.com/problems/odd-even-linked-list/)

题目描述：将所有的奇数序号节点都放到偶数序号节点前面。

```
Input: 2->1->3->5->6->4->7->NULL
Output: 2->3->6->7->1->5->4->NULL
```

```java
public ListNode oddEvenList(ListNode head) {
    if(head == null) return null;
    ListNode odd = head, even = head.next, evenHead = even;
    while(even != null && even.next != null){
        odd.next = odd.next.next;
        even.next = even.next.next;
        odd = odd.next;
        even = even.next;
    }
    odd.next = evenHead;
    return head;
}
```

## 反转链表 II

[Leetcode - 92 Reverse Linked List II (Medium)](https://leetcode.com/problems/reverse-linked-list-ii/)

题目描述：反转 m 到 n。

```
Input: 1->2->3->4->5->NULL, m = 2, n = 4
Output: 1->4->3->2->5->NULL
```

```java
//TODO
```

## 回文链表

[Leetcode - 234 Palindrome Linked List (Easy)](https://leetcode.com/problems/palindrome-linked-list/)

题目描述：判断链表表示的内容是否是回文数，时间复杂度 O(n)，空间复杂度 O(1)。

```
Input: 1->2->2->1
Output: true
```

解题思路：使用快慢指针找到链表的中点，将中点后面的链反转，之后比较前面的链和反转后的链是否相同。

```java
public boolean isPalindrome(ListNode head) {
    ListNode slow = head, fast = head;
    while(fast != null && fast.next != null){
        slow = slow.next;
        fast = fast.next.next;
    }
    // 奇数
    if(fast != null){
        slow = slow.next;
    }
    slow = reverse(slow);
    while(slow != null){
        if(slow.val != head.val){
            return false;
        }
        slow = slow.next;
        head = head.next;
    }
    return true;
}
// 反转链表
public ListNode reverse(ListNode head){
    ListNode pre = null;
    while(head != null){
        ListNode next = head.next;
        head.next = pre;
        pre = head;
        head = next;
    }
    return pre;
}
```


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

题目描述：反转 m 到 n，只通过一次遍历完成。

```
Input: 1->2->3->4->5->NULL, m = 2, n = 4
Output: 1->4->3->2->5->NULL
```

```java
public ListNode reverseBetween(ListNode head, int m, int n) {
    if(head == null) return null;
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode pre = dummy;
    for(int i = 0; i<m-1; i++) pre = pre.next;

    ListNode start = pre.next; 
    ListNode then = start.next;
    for(int i=0; i<n-m; i++){
        start.next = then.next;
        then.next = pre.next;
        pre.next = then;
        then = start.next;
    }
    return dummy.next;
}
```

## 删除链表的节点

[Leetcode - 237 Delete Node in a Linked List (Easy)](https://leetcode.com/problems/delete-node-in-a-linked-list/)

题目描述：只给出需要删除的节点引用。

```
Input: head = [4,5,1,9], node = 5
Output: [4,1,9]
Explanation: You are given the second node with value 5, the linked list should become 4 -> 1 -> 9 after calling your function.
```

```java
public void deleteNode(ListNode node) {
    node.val = node.next.val;
    node.next = node.next.next;
}
```

## 删除链表的倒数第 n 个节点

[Leetcode - 19 Remove Nth Node From End of List (Medium)](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

```
Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.
```

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummyHead = new ListNode(0);
    dummyHead.next = head;
    ListNode cur = dummyHead;
    ListNode first = cur;
    while(n-- >= 0){
        first = first.next;
    }
    while(first != null){
        first = first.next;
        cur = cur.next;
    }
    cur.next = cur.next.next;
    return dummyHead.next;
}
```

## 删除有序链表的重复节点

[Leetcode - 83 Remove Duplicates from Sorted List (Easy)](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)

```
Input: 1->1->2->3->3
Output: 1->2->3
```

```java
public ListNode deleteDuplicates(ListNode head) {
    ListNode current = head;
    while (current != null && current.next != null) {
        if (current.next.val == current.val) {
            current.next = current.next.next;
        } else {
            current = current.next;
        }
    }
    return head;
}
```

## 删除链表中的元素

[Leetcode - 203 Remove Linked List Elements (Easy)](https://leetcode.com/problems/remove-linked-list-elements/)

题目描述：删除链表中值为 val 的节点。

```
Input:  1->2->6->3->4->5->6, val = 6
Output: 1->2->3->4->5
```

```java
public ListNode removeElements(ListNode head, int val) {
    ListNode dummyHead = new ListNode(0);
    dummyHead.next = head;
    ListNode cur = head, prev = dummyHead;
    while(cur != null){
        if(cur.val == val){
            prev.next = cur.next;
        }else{
            prev = prev.next;
        }
        cur = cur.next;
    }
    return dummyHead.next;
}
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


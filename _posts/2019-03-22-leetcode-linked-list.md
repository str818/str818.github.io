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

## 成对交换节点

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

解题思路：可能会删除第 0 个节点，所以要使用尾节点。

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

题目描述：保留一个重复的节点。

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

## 删除有序链表的重复节点 II

[Leetcode - 82 Remove Duplicates from Sorted List II (Medium)](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

题目描述：删除所有的重复节点。

```
Input: 1->2->3->3->4->4->5
Output: 1->2->5
```

解法一：递归

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null) return null;

    if (head.next != null && head.val == head.next.val) {
        while (head.next != null && head.val == head.next.val) {
            head = head.next;
        }
        return deleteDuplicates(head.next);
    } else {
        head.next = deleteDuplicates(head.next);
    }
    return head;
}
```

解题二：迭代

```java
public ListNode deleteDuplicates(ListNode head) {
    ListNode dummyHead = new ListNode(0);
    dummyHead.next = head;
    ListNode cur = head, prev = dummyHead;
    while(cur != null){
        ListNode next = cur.next;
        while(next != null && next.val == cur.val){
            cur = cur.next;
            next = next.next;
        }
        if(prev.next != cur){
            prev.next = next;
        }else{
            prev = cur;
        }
        cur = next;
    }
    return dummyHead.next;
}
```

## 两数相加

[Leetcode - 2 Add Two Numbers (Medium)](https://leetcode.com/problems/add-two-numbers/)

题目描述：给定两个链表，每个链表表示一个整数，链表的每个节点表示一位数，将两个链表表示的整数相加得到新的链表。

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```
解题思路：同时遍历两个链表，将相同位置的节点相加，注意加法的进位。

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummyHead = new ListNode(0);
    ListNode cur = dummyHead;
    int carry = 0;
    while(l1 != null || l2 != null){
        int x = (l1 != null) ? l1.val : 0;
        int y = (l2 != null) ? l2.val : 0;
        int sum = carry + x + y;
        carry = sum / 10;
        cur.next = new ListNode(sum % 10);
        cur = cur.next;
        if(l1 != null) l1 = l1.next;
        if(l2 != null) l2 = l2.next;
    }
    if(carry > 0){
        cur.next = new ListNode(carry);
    }
    return dummyHead.next;
}
```

## 两个链表的交点

[Leetcode - 160 Intersection of Two Linked Lists (Easy)](https://leetcode.com/problems/intersection-of-two-linked-lists/)

题目描述：给定两个链表，求出两个链表相交的节点。

```
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
Output: Reference of the node with value = 8
```

<div align="center">  <img src="/img/leetcode-160.png" width="50%"/> </div><br>

解题思路：两个指针分别在两个链表上向前移动，当其中一个指针移动到了链表的末尾，将该指针指向另一个链表的头节点，另一个指针同理，当两个指针指向同一个节点时，该节点即为链表的相交节点。

我在这里绕了一会，其实很简单，交换指针指向的链表就是为了计算出两个链表私有链的长度，比如上图中，当指针 a 从 链表 A 的头节点移动到了最后，将其指向链表 B 的头节点，此时指针 b 指向链表 B 的节点 5，再向前走一步，a 指针就指向了链表 B 的 节点 0，b 指针指向了链表 A 的头节点 4，最后两个指针同时向后移动，就能同时到达相交节点 8。

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

## 合并两个有序链表

[Leetcode - 21 Merge Two Sorted Lists (Easy)](https://leetcode.com/problems/merge-two-sorted-lists/)

```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```

解法一：迭代

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    ListNode dummyHead = new ListNode(0);
    ListNode cur = dummyHead;
    while(l1 != null && l2 != null){
        if(l1.val > l2.val){
            cur.next = l2;
            l2 = l2.next;
        }else{
            cur.next = l1;
            l1 = l1.next;
        }
        cur = cur.next;
    }
    if(l1 != null){
        cur.next = l1;
    }
    if(l2 != null){
        cur.next = l2;
    }
    return dummyHead.next;
}
```

解法二：递归

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2){
    if(l1 == null) return l2;
    if(l2 == null) return l1;
    if(l1.val < l2.val){
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    } else{
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
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

## 重排序链表

[Leetcode - 143 Reorder List (Medium)](https://leetcode.com/problems/reorder-list/)

题目描述：将链表 `L0 -> L1 -> ... -> Ln-1 -> Ln` 重排序成 `L0 -> Ln -> L1 -> Ln-1 -> L2 -> Ln-2 -> ...`。

```
Given 1->2->3->4, reorder it to 1->4->2->3.
```

```java
public void reorderList(ListNode head) {
    if(head == null || head.next == null) return;
    
    // 找到链表的中间节点
    ListNode p1 = head;
    ListNode p2 = head;
    while(p2.next != null && p2.next.next != null){
        p1 = p1.next;
        p2 = p2.next.next;
    }
    
    // 反转后半部分链表
    ListNode preMiddle = p1;
    ListNode preCurrent = p1.next;
    while(preCurrent.next != null){
        ListNode cur = preCurrent.next;
        preCurrent.next = cur.next;
        cur.next = preMiddle.next;
        preMiddle.next = cur;
    }
    
    // 重排序
    p1=head;
    p2=preMiddle.next;
    while(p1!=preMiddle){
        preMiddle.next=p2.next;
        p2.next=p1.next;
        p1.next=p2;
        p1=p2.next;
        p2=preMiddle.next;
    }
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

## 链表排序

[Leetcode - 148 Sort List (Medium)](https://leetcode.com/problems/sort-list/)

题目描述：对给定的链表进行排序，时间复杂度 O(longn)，并且空间复杂度为常数。

```
Input: 4->2->1->3
Output: 1->2->3->4
```

解题思路：归并排序。

```java
public ListNode sortList(ListNode head) {
    if(head == null || head.next == null) return head;
    ListNode pre = head, slow = head, fast = head;
    while(fast != null && fast.next != null){
        pre = slow;
        slow = slow.next;
        fast = fast.next.next;
    }
    pre.next = null;
    ListNode l1 = sortList(head);
    ListNode l2 = sortList(slow);
    return merge(l1, l2);
}

public ListNode merge(ListNode l1, ListNode l2){
    ListNode head = new ListNode(0), cur = head;
    while(l1 != null && l2 != null){
        if(l1.val < l2.val){
            cur.next = l1;
            l1 = l1.next;
        }else{
            cur.next = l2;
            l2 = l2.next;
        }
        cur = cur.next;
    }
    if(l1 != null){
        cur.next = l1;
    }
    if(l2 != null){
        cur.next = l2;
    }
    return head.next;
}
```

## 每 K 个一组反转链表

[Leetcode - 25 Reverse Nodes in k-Group (Hard)](https://leetcode.com/problems/reverse-nodes-in-k-group/)

```
Given this linked list: 1->2->3->4->5

For k = 2, you should return: 2->1->4->3->5

For k = 3, you should return: 3->2->1->4->5
```

```java
public ListNode reverseKGroup(ListNode head, int k) {
    if(head == null || head.next == null) return head;
    ListNode dummyHead = new ListNode(0);
    dummyHead.next = head;
    ListNode pre = dummyHead, cur = head;
    while(cur != null){
        ListNode temp = pre;
        for(int i = 0; i < k; i++){
            if(temp.next == null){
                return dummyHead.next;
            }
            temp = temp.next;
        }
        for(int i = 0; i < k - 1; i++){
            temp = pre.next;
            pre.next = cur.next;
            cur.next = pre.next.next;
            pre.next.next = temp;
        }
        pre = cur;
        cur = cur.next;
    }
    return dummyHead.next;
}
```

## 旋转链表

[Leetcode - 61 Rotate List (Medium)](https://leetcode.com/problems/rotate-list/)

```
nput: 0->1->2->NULL, k = 4
Output: 2->0->1->NULL
Explanation:
rotate 1 steps to the right: 2->0->1->NULL
rotate 2 steps to the right: 1->2->0->NULL
rotate 3 steps to the right: 0->1->2->NULL
rotate 4 steps to the right: 2->0->1->NULL
```

```java
public ListNode rotateRight(ListNode head, int n) {
    if (head==null||head.next==null) return head;
    ListNode dummy=new ListNode(0);
    dummy.next=head;
    ListNode fast=dummy,slow=dummy;

    int i;
    for (i=0;fast.next!=null;i++)// 获取链表长度
    	fast=fast.next;
    
    for (int j=i-n%i;j>0;j--) // 找到第 i-n%i 个节点
    	slow=slow.next;
    
    fast.next=dummy.next; // 进行旋转
    dummy.next=slow.next;
    slow.next=null;
    
    return dummy.next;
}
```

## 划分链表

[Leetcode - 86 Partition List (Medium)](https://leetcode.com/problems/partition-list/)

题目描述：将链表中小于给定值的节点都移动到前面，大于等于该值的节点顺序不变。

```
Input: head = 1->4->3->2->5->2, x = 3
Output: 1->2->2->4->3->5
```

解题思路：将小于给定值的节点组成一个新的链表，大于等于给定值的节点也组成一个新的链表，之后将两个链表拼接。

```java
public ListNode partition(ListNode head, int x) {
    ListNode dummyHead1 = new ListNode(0);
    ListNode dummyHead2 = new ListNode(0);
    ListNode cur1 = dummyHead1, cur2 = dummyHead2;
    while(head != null){
        if(head.val < x){
            cur1.next = head;
            cur1 = cur1.next;
        }else{
            cur2.next = head;
            cur2 = cur2.next;
        }
        head = head.next;
    }
    cur2.next = null; // 不能省略，否则可能会形成环
    cur1.next = dummyHead2.next;
    return dummyHead1.next;
}
```

## 合并 k 个有序链表

[Leetcode - 23 Merge k Sorted Lists (Hard)](https://leetcode.com/problems/merge-k-sorted-lists/)

```
Input:
[
  1->4->5,
  1->3->4,
  2->6
]
Output: 1->1->2->3->4->4->5->6
```

解题思路：这道题类似之前的合并两个有序链表，可以逐个两两合并，也可以每次取出链表头部的最小节点，我也是首先想到这种方法，但是效率有点低。能够通过优先级队列和分治进行优化。

解法一：优先级队列

```java
public ListNode mergeKLists(ListNode[] lists) { 
    if (lists == null || lists.length == 0) return null;
    
    PriorityQueue<ListNode> queue= new PriorityQueue<ListNode>(lists.length,new Comparator<ListNode>(){
        @Override
        public int compare(ListNode o1,ListNode o2){
            if (o1.val<o2.val)
                return -1;
            else if (o1.val==o2.val)
                return 0;
            else 
                return 1;
        }
    });
    
    ListNode dummy = new ListNode(0);
    ListNode tail=dummy;
    
    for (ListNode node:lists)
        if (node!=null)
            queue.add(node);
        
    while (!queue.isEmpty()){
        tail.next=queue.poll();
        tail=tail.next;
        
        if (tail.next!=null)
            queue.add(tail.next);
    }
    return dummy.next;
}
```

解法二：分治

```java
public static ListNode mergeKLists(ListNode[] lists){
    return partion(lists,0,lists.length-1);
}

public static ListNode partion(ListNode[] lists,int s,int e){
    if(s==e)  return lists[s];
    if(s<e){
        int q=(s+e)/2;
        ListNode l1=partion(lists,s,q);
        ListNode l2=partion(lists,q+1,e);
        return merge(l1,l2);
    }else
        return null;
}

// 合并两个链表
public static ListNode merge(ListNode l1,ListNode l2){
    if(l1==null) return l2;
    if(l2==null) return l1;
    if(l1.val<l2.val){
        l1.next=merge(l1.next,l2);
        return l1;
    }else{
        l2.next=merge(l1,l2.next);
        return l2;
    }
}
```

## 链表插入排序

[Leetcode - 147 Insertion Sort List (Medium)](https://leetcode.com/problems/insertion-sort-list/)

题目描述：将链表按照插入排序的方式进行排序。

```
Input: -1->5->3->4->0
Output: -1->0->3->4->5
```

```java
public ListNode insertionSortList(ListNode head) {
    if( head == null ) return null;
    ListNode dummyHead = new ListNode(0);
    ListNode cur = head;
    ListNode pre = dummyHead;
    ListNode next = null;
    while( cur != null ){
        next = cur.next;
        while( pre.next != null && pre.next.val < cur.val ){
            pre = pre.next;
        }
        cur.next = pre.next;
        pre.next = cur;
        pre = dummyHead;
        cur = next;
    }
    return dummyHead.next;
}
```
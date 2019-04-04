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


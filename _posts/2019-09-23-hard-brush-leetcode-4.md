---
layout: article
title: 4 - 寻找两个有序数组的中位数 (Median of Two Sorted Arrays)
tags: 刷穿-数组 刷穿-二分查找

lang: zh-Hans
key: Hard_Brush_Leetcode_4
pageview: true
toc: true
show_subscribe: false
---

## 一、题目描述

[Leetcode 4 - Median of Two Sorted Arrays (Hard)](https://leetcode.com/problems/median-of-two-sorted-arrays/)

给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。

```
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
```

## 二、解题思路

https://www.cnblogs.com/grandyang/p/4465932.html

## 三、题解

### 1. Java 题解

```java
public double findMedianSortedArrays(int[] A, int[] B) {
    int m = A.length, n = B.length;
    int l = (m + n + 1) / 2;
    int r = (m + n + 2) / 2;
    return (getkth(A, 0, B, 0, l) + getkth(A, 0, B, 0, r)) / 2.0;
}
public double getkth(int[] A, int aStart, int[] B, int bStart, int k) {
    if (aStart > A.length - 1) return B[bStart + k - 1];
    if (bStart > B.length - 1) return A[aStart + k - 1];
    if (k == 1) return Math.min(A[aStart], B[bStart]);
    
    int aMid = Integer.MAX_VALUE, bMid = Integer.MAX_VALUE;
    if (aStart + k/2 - 1 < A.length) aMid = A[aStart + k/2 - 1];
    if (bStart + k/2 - 1 < B.length) bMid = B[bStart + k/2 - 1];
    
    if (aMid < bMid) {
        return getkth(A, aStart + k/2, B, bStart, k - k/2);
    } else {
        return getkth(A, aStart, B, bStart + k/2, k - k/2);
    }
}
```

### 2. Go 题解

```go
func twoSum(nums []int, target int) []int {
    mp := make(map[int]int)
    for i, num := range(nums) {
        if idx, ok := mp[target - num]; ok {
            return []int{idx, i}
        }
        mp[num] = i
    }
    return nil
}
```

### 3. Python 题解

```python
class Solution(object):
    def twoSum(self, nums, target):
        d = {}
        for i, n in enumerate(nums):
            m = target - n
            if m in d:
                return [d[m], i]
            else:
                d[n] = i
```
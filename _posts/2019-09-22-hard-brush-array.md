---
layout: article
title: 1 - 两数之和 (Tow Sum)
tags: 刷穿-数组

lang: zh-Hans
key: Hard_Brush_Array_1
pageview: true
toc: true
show_subscribe: false
---

## 一、题目描述

[Leetcode 1 - Tow Sum (Easy)](https://leetcode.com/problems/two-sum/)

给定一个整数数组 nums 和一个目标值 target，在该数组中找出和为目标值的那两个整数，并返回它们的数组下标。

假设每种输入只会对应一个答案，不能重复使用数组种的元素。

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

## 二、解题思路

使用哈希表存储访问过的元素，比较 target 与当前元素之差是否在哈希表中，如果在则返回这两个元素在数组中的下标。

只需遍历一遍数组即可求得最终结果。

## 三、题解

### 1. Java 题解

```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] {map.get(complement), i};
        }
        map.put(nums[i], i);
    }
    throw new IllegalArgumentException("No two num solution");
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
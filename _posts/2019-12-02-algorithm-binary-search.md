---
layout: article
title: 算法 — 二分查找
tags: 算法

lang: zh-Hans
key: Algorithm_Binary_Search
pageview: true
toc: true
show_subscribe: false
---

二分查找对具有指定左索引和右索引的连续序列进行操作。

二分查找维护查找空间的左、右和中间指示符，并比较查找目标或将查找条件应用于集合的中间值；如果条件不满足或值不相等，则清除目标不可能存在的那一半，并在剩下的一半上继续查找，直到成功为止。如果查以空的一半结束，则无法满足条件，并且无法找到目标。

## 标准二分查找

给定一个 `n` 个元素升序整型数组 `nums` 和一个目标值 `target`，写一个函数搜索 `nums` 中的 `target`，如果目标值存在则返回下标，否则返回 `-1`。

示例 1：

```
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
```

示例 2：

```
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

```java
public int search(int[] nums, int target) {

    if (nums == null || nums.length == 0) {
        return -1;
    }

    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (target == nums[mid]) {
            return mid;
        } else if (target < nums[mid]) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    return -1;
}
```
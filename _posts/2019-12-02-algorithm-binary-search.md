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

## 1. 标准二分查找

### 模板

[Leetcode 704 - Sqrt(x) (Easy)](https://leetcode.com/problems/sqrtx/)

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

### x 的平方根

[Leetcode 69 - Sqrt(x) (Easy)](https://leetcode.com/problems/sqrtx/)

实现 `int sqrt(int x)` 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

示例 1:

```
输入: 4
输出: 2
```

示例 2:

```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
```

```java
public int mySqrt(int x) {
    // 考虑到 1， 把又边界设为 x / 2 + 1
    long left = 0, right = x / 2 + 1;
    while (left < right) {
        // 一定取右中位数，否则会进入死循环
        long mid = left + (right - left + 1) / 2;
        long square = mid * mid;
        if (square > x) {
            right = mid - 1;
        } else {
            left = mid;
        }
    }
    return (int)left;
}
```

### 猜数字大小

[Leetcode 374 - Guess Number Higher or Lower (Easy)](https://leetcode.com/problems/guess-number-higher-or-lower/)

我们正在玩一个猜数字游戏。 游戏规则如下：
我从 1 到 n 选择一个数字。 你需要猜我选择了哪个数字。
每次你猜错了，我会告诉你这个数字是大了还是小了。
你调用一个预先定义好的接口 `guess(int num)`，它会返回 3 个可能的结果（-1，1 或 0）：

```
-1 : 我的数字比较小
 1 : 我的数字比较大
 0 : 恭喜！你猜对了！
```

示例 :

```
输入: n = 10, pick = 6
输出: 6
```

```java
public int guessNumber(int n) {
    int i = 1, j = n;
    while (i <= j) {
        int mid = i + (j - i) / 2;
        int ans = guess(mid);
        if (ans == 0) {
            return mid;
        } else if (ans == -1) {
            j = mid - 1;
        } else {
            i = mid + 1;
        }
    }
    return -1;
}
```

### 搜索旋转排序数组

[Leetcode 33 - Search in Rotated Sorted Array (Medium)](https://leetcode.com/problems/search-in-rotated-sorted-array/)

假设按照升序排序的数组在预先未知的某个点上进行了旋转。( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 O(log n) 级别。

示例 1:

```
输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
```

示例 2:

```
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1
```

```java
public int search(int[] nums, int target) {
        
    if (nums == null || nums.length == 0) {
        return -1;
    }
    
    int i = 0, j = nums.length - 1;
    while (i <= j) {
        int mid = i + (j - i) / 2;
        if (nums[mid] == target) {
            return mid;
        }
        // 一定有一边是有序的
        if (nums[mid] < nums[j]) {
            // 如果不再有序的那一边，就一定在另外一边
            if (target > nums[mid] && target <= nums[j]) {
                i = mid + 1;
            } else {
                j = mid - 1;
            }
        } else {
            if (target < nums[mid] && target >= nums[i]) {
                j = mid - 1;
            } else {
                i = mid + 1;
            }
        }
    }
    return -1;
}
```


## 2. 高级二分查找

### 模板

用于查找需要访问数组中农当前索引及其右邻居索引的元素或条件。

```java
int binarySearch(int[] nums, int target) {
    if(nums == null || nums.length == 0) {
        return -1;
    }
        
    int left = 0, right = nums.length;
    while(left < right){
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }

    if (left != nums.length && nums[left] == target) return left;
    return -1;
}
```

### 第一个错误版本

[Leetcode 278 - First Bad Version (Easy)](https://leetcode.com/problems/first-bad-version/)

你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 `n` 个版本 `[1, 2, ..., n]`，你想找出导致之后所有版本出错的第一个错误的版本。

你可以通过调用 `bool isBadVersion(version)` 接口来判断版本号 `version` 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

示例:

```
给定 n = 5，并且 version = 4 是第一个错误的版本。

调用 isBadVersion(3) -> false
调用 isBadVersion(5) -> true
调用 isBadVersion(4) -> true

所以，4 是第一个错误的版本。 
```

```java
public int firstBadVersion(int n) {
    int i = 1, j = n;
    while (i < j) {
        int mid = i + (j - i) / 2;
        if (isBadVersion(mid)) {
            j = mid;
        } else {
            i = mid + 1;
        }
    }
    return i;
}
```

### 寻找峰值

[Leetcode 162 - Find Peak Element (Medium)](https://leetcode.com/problems/find-peak-element/)

峰值元素是指其值大于左右相邻值的元素。

给定一个输入数组 `nums`，其中 `nums[i] ≠ nums[i+1]`，找到峰值元素并返回其索引。

数组可能包含多个峰值，在这种情况下，返回任何一个峰值所在位置即可。

你可以假设 `nums[-1] = nums[n] = -∞`。

要求时间复杂度为 `O(logN)`。

示例 1:

```
输入: nums = [1,2,3,1]
输出: 2
解释: 3 是峰值元素，你的函数应该返回其索引 2。
示例 2:
```

```
输入: nums = [1,2,1,3,5,6,4]
输出: 1 或 5 
解释: 你的函数可以返回索引 1，其峰值元素为 2；
     或者返回索引 5， 其峰值元素为 6。
```

思路： 由 `nums[-1] = nums[n] = -∞` 可知，只要数组中存在一个元素比相邻元素大，那么沿着它一定可以找到一个峰值。利用二分法，根据左右指针计算中间位置 m，并比较 m 与 m+1 的值，如果 m 较大，则左侧存在峰值，r = m，如果 m + 1 较大，则右侧存在峰值，l = m + 1

```java
public int findPeakElement(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (nums[mid] > nums[mid + 1]) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```

### 寻找旋转排序数组中的最小值

[Leetcode - 153 Find Minimum in Rotated Sorted Array (Medium)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 `[0,1,2,4,5,6,7]` 可能变为 `[4,5,6,7,0,1,2]` )。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

示例 1:

```
输入: [3,4,5,1,2]
输出: 1
```

```java
public int findMin(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (nums[h] > nums[mid]) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return nums[l];
}
```
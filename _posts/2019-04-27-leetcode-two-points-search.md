---
layout: article
title: Leetcode —「二分查找」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Two_Points_Search
pageview: true
toc: true
show_subscribe: false
---

## Pow(x, n)

[Leetcode - 50 Pow(x, n) (Medium)](https://leetcode.com/problems/powx-n/)

题目描述：实现 `pow(x, n)` ，即计算 x 的 n 次幂函数。

```
Input: 2.00000, 10
Output: 1024.00000
```

```java
public double myPow(double x, int n) {
    double res = 1.0;
    for (int i = n; i != 0; i /= 2) {
        if (i % 2 != 0) res *= x;
        x *= x;
    }
    return n < 0 ? 1 / res : res;
}
```

## 求开方

[Leetcode - 69 Sqrt(x) (Easy)](https://leetcode.com/problems/sqrtx/)

题目描述：输入一个整数，求该整数的开方，去除结果的小数位，返回整数。

```
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since the decimal part is truncated, 2 is returned.
```

解题思路：测试用例中含有较大的整数，所以要用 x / m 进行比较。对于 x = 8，它的开方是 2.82842...，最后应该返回 2 而不是 3。在循环条件为 l <= r 并且循环退出时，R 总是比 l 小 1，也就是说 R = 2，l = 3，因此最后的返回值应该为 R 而不是 l。

```java
public int mySqrt(int x) {
    if(x <= 1) return x;
    int l = 1, r = x;
    while(l <= r){
        int m = l + (r - l) / 2;
        if(m == x / m){
            return m;
        }else if(m < x / m){
            l = m + 1;
        }else{
            r = m - 1;
        }
    }
    return r;
}
```

## 寻找比目标字母大的最小字母

[Leetcode - 744 Find Smallest Letter Greater Than Target (Easy)](https://leetcode.com/problems/find-smallest-letter-greater-than-target)

```
Input:
letters = ["c", "f", "j"]
target = "d"
Output: "f"
```

```java
public char nextGreatestLetter(char[] letters, char target) {
    int n = letters.length;
    int l = 0, h = n - 1;
    while (l <= h) {
        int m = l + (h - l) / 2;
        if (letters[m] <= target) {
            l = m + 1;
        } else {
            h = m - 1;
        }
    }
    return l < n ? letters[l] : letters[0];
}
```

## 有序数组中的单一元素

[Leetcode - 540 Single Element in a Sorted Array (Medium)](https://leetcode.com/problems/single-element-in-a-sorted-array/)

题目描述：给定一个只包含整数的有序数组，每个元素都会出现两次，唯有一个数只会出现一次，找出这个数。

```
Input: [1,1,2,3,3,4,4,8,8]
Output: 2
```

解题思路：假设这个数的序号是 index，那么所有小于 index 的奇数位与偶数位一定相等；而大于 index 的奇数位于偶数位一定不相等，根据这个规律进行二分查找。

```java
public int singleNonDuplicate(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (mid % 2 == 1) mid--;
        if (nums[mid] != nums[mid + 1]) {
            h = mid;
        } else {
            l = mid + 2;
        }
    }
    return nums[l];
}
```

## 第一个错误的版本

[Leetcode - 278 First Bad Version (Easy)](https://leetcode.com/problems/first-bad-version/)

题目描述：给定一个数组 [1, 2 ... n] 表示 n 个版本，如果一个版本出现错误，那本这个版本之后的版本都将出现错误，调用 `isBadVersion(n)` 方法能够判断某个版本是否出现错误，在调用该函数次数最少的情况下，找出第一个出现错误的版本。

```java
public int firstBadVersion(int n) {
    int l = 1, h = n;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (isBadVersion(mid)) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```

## 寻找旋转排序数组中的最小值

[Leetcode - 153 Find Minimum in Rotated Sorted Array (Medium)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

题目描述：给定升序数组在某个点上进行的旋转，找出最小元素。

```
Input: [4,5,6,7,0,1,2]
Output: 0
```

```java
public int findMin(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (nums[h] >= nums[mid]) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return nums[l];
}
```

## 在排序数组中查找元素的第一个和最后一个位置

[Leetcode - 34 Find First and Last Position of Element in Sorted Array (Medium)](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```java
public int[] searchRange(int[] nums, int target) {
    int first = binarySearch(nums, target);
    int last = binarySearch(nums, target + 1) - 1;
    if (first == nums.length || nums[first] != target) {
        return new int[]{-1, -1};
    } else {
        return new int[]{first, Math.max(first, last)};
    }
}

public int binarySearch(int[] nums, int target) {
    int l = 0, h = nums.length; // 注意 h 初始值
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (nums[mid] >= target) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```
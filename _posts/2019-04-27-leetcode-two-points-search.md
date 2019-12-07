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

## 二分查找

[Leetcode - 704 Binary Search (Easy)](https://leetcode.com/problems/binary-search/)

给定一个 `n` 个元素升序整型数组 `nums` 和一个目标值 `target`，写一个函数搜索 `nums` 中的 `target`，如果目标值存在则返回下标，否则返回 `-1`。


```
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
```

```
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
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

## x 的平方根

[Leetcode - 69 Sqrt(x) (Easy)](https://leetcode.com/problems/sqrtx/)

题目描述：输入一个整数，求该整数的开方，去除结果的小数位，返回整数。

```
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since the decimal part is truncated, 2 is returned.
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

## 猜数字大小

[Leetcode - 374 Guess Number Higher or Lower (Easy)](https://leetcode.com/problems/guess-number-higher-or-lower/)

我们正在玩一个猜数字游戏。 游戏规则如下：
我从 1 到 n 选择一个数字。 你需要猜我选择了哪个数字。
每次你猜错了，我会告诉你这个数字是大了还是小了。
你调用一个预先定义好的接口 `guess(int num)`，它会返回 3 个可能的结果（-1，1 或 0）：

```
-1 : 我的数字比较小
 1 : 我的数字比较大
 0 : 恭喜！你猜对了！
```

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

## 搜索旋转排序数组

[Leetcode - 33 Search in Rotated Sorted Array (Medium)](https://leetcode.com/problems/search-in-rotated-sorted-array/)

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

## 寻找峰值

[Leetcode - 162 Find Peak Element (Medium)](https://leetcode.com/problems/find-peak-element/)

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

## 寻找旋转排序数组中的最小值

[Leetcode - 153 Find Minimum in Rotated Sorted Array (Medium)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

给定升序数组在某个点上进行的旋转，找出最小元素，假设不存在重复元素。

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

## 寻找旋转排序数组中的最小值 II

[Leetcode - 154. Find Minimum in Rotated Sorted Array II (Medium)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)

给定升序数组在某个点上进行的旋转，找出最小元素，可能出现重复元素。

```
Input: [2,2,2,0,1]
Output: 0
```

```java
public int findMin(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (nums[h] > nums[mid]) {
            h = mid;
        } else if (nums[h] < nums[mid]){
            l = mid + 1;
        } else {
            // 相等就 h--
            h--;
        }
    }
    return nums[l];
}
```


## 寻找比目标字母大的最小字母

[Leetcode - 744 Find Smallest Letter Greater Than Target (Easy)](https://leetcode.com/problems/find-smallest-letter-greater-than-target)

给定一个只包含小写字母的有序数组 letters 和一个目标字母 target，寻找有序数组里面比目标字母大的最小字母。

数组里字母的顺序是循环的。举个例子，如果目标字母 `target = 'z'` 并且有序数组为 `letters = ['a', 'b']`，则答案返回 `a`。

```
Input:
letters = ["c", "f", "j"]
target = "d"
Output: "f"
```

```java
public char nextGreatestLetter(char[] letters, char target) {
    int l = 0, r = letters.length;
    while (l < r) {
        int mid = l + (r - l) / 2;
        if (letters[mid] <= target) {
            l = mid + 1;
        } else {
            r = mid;
        }
    }
    // 循环
    return letters[l % letters.length];
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

## 在排序数组中查找元素的第一个和最后一个位置

[Leetcode - 34 Find First and Last Position of Element in Sorted Array (Medium)](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

题目描述：给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。算法时间复杂度必须是 `O(log n)` 级别。

如果数组中不存在目标值，返回 `[-1, -1]`。

示例 1:

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
```

示例 2:

```
输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]
```

```java
public int[] searchRange(int[] nums, int target) {
    int first = binarySearch(nums, target);
    int last = binarySearch(nums, target + 1) - 1;
    if (first == nums.length || nums[first] != target) {
        return new int[]{-1, -1};
    } else {
        return new int[]{first, last};
    }
}

// 寻找最左面的值
public int binarySearch(int[] nums, int target) {
    // 注意 h 初始值，表示有多少个数比当前值小
    int l = 0, h = nums.length; 
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

## 找到 K 个最接近的元素

[Leetcode - 658 Find K Closest Elements (Medium)](https://leetcode.com/problems/find-k-closest-elements/)

给定一个排序好的数组，两个整数 `k` 和 `x`，从数组中找到最靠近 `x`（两数之差最小）的 `k` 个数。返回的结果必须要是按升序排好的。如果有两个数与 `x` 的差值一样，优先选择数值较小的那个数。

示例 1:

```
输入: [1,2,3,4,5], k=4, x=3
输出: [1,2,3,4]
```

示例 2:

```
输入: [1,2,3,4,5], k=4, x=-1
输出: [1,2,3,4]
```

```java
public List<Integer> findClosestElements(int[] arr, int k, int x) {
    int l = 0, r = arr.length - 1;
    while (r - l >= k) {
        // 相等一定是取小的数，注意顺序
        if (Math.abs(arr[r] - x) < Math.abs(arr[l] - x)) {
            l++;
        } else {
            r--;
        }
    }
    
    List<Integer> ans = new ArrayList<Integer>();
    for (int i = l; i <= r; i++) {
        ans.add(arr[i]);
    }
    return ans;
}
```

## 有效的完全平方数

[Leetcode - 367 Valid Perfect Square (Easy)](https://leetcode.com/problems/valid-perfect-square/)

给定一个正整数 num，编写一个函数，如果 num 是一个完全平方数，则返回 True，否则返回 False。

```
Input: 16
Output: true
```

```java
public boolean isPerfectSquare(int num) {
    int l = 1, r = num;
    while (l <= r) {
        long mid = l + (r - l) / 2;
        if (mid * mid == num) {
            return true;
        } else if (mid * mid > num) {
            r = (int) mid - 1;
        } else {
            l = (int) mid + 1;
        }
    }
    return false;
    
}
```

## 两个数组的交集

[Leetcode - 349 Intersection of Two Arrays (Easy)](https://leetcode.com/problems/intersection-of-two-arrays/)

给定两个数组，编写一个函数来计算它们的交集。

```
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [9,4]
```

解题思路：可以使用 Set 求解，下面解法使用二分查找。

```java
public int[] intersection(int[] nums1, int[] nums2) {
    Set<Integer> set = new HashSet<>();
    Arrays.sort(nums2);
    for (Integer num : nums1) {
        if (binarySearch(nums2, num)) {
            set.add(num);
        }
    }
    int i = 0;
    int[] result = new int[set.size()];
    for (Integer num : set) {
        result[i++] = num;
    }
    return result;
}

public boolean binarySearch(int[] nums, int target) {
    int low = 0;
    int high = nums.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            return true;
        }
        if (nums[mid] > target) {
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    return false;
}
```
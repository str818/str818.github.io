---
layout: article
title: Leetcode —「数组」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Array
pageview: true
toc: true
show_subscribe: false
---

## 多数元素

[Leetcode - 169 Majority Element (Easy)](https://leetcode.com/problems/majority-element/)

题目描述：找出数组中出现次数超过 `⌊ n/2 ⌋` 次的元素，假设这个元素一定存在。

```
Input: [3,2,3]
Output: 3
```

解题思路：利用了摩尔投票算法的思想，每次从序列里选择两个不相同的数字删除掉（抵消），最后剩下一个数字或几个相同的数字，就是出现次数大于总数一半的那个。

```java
public int majorityElement(int[] nums) {
    int major = nums[0], count = 1;
    for(int i = 1; i < nums.length; i++){
        if(count == 0){
            major = nums[i];
            count++;
        }else if(major == nums[i]){
            count++;
        }else{
            count--;
        }
    }
    return major;
}
```

## 除本身外的数组乘积

[Leetcode - 238 Product of Array Except Self (Medium)](https://leetcode.com/problems/product-of-array-except-self/)

题目描述：不能用分治并且时间复杂度为 O(n)。

```
Input:  [1,2,3,4]
Output: [24,12,8,6]
```

```java
public int[] productExceptSelf(int[] nums) {
    int[] result = new int[nums.length];
    result[0] = 1;
    for(int i = 1; i < nums.length; i++){
        result[i] = result[i - 1] * nums[i - 1];
    }
    int right = 1;
    for(int i = nums.length - 1; i >= 0; i--){
        result[i] *= right;
        right *= nums[i];
    }
    return result;
}
```
---
layout: article
title: Leetcode —「位运算」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Bit_Operation
pageview: true
toc: true
show_subscribe: false
---

## 单一数字

[Leetcode - 136 Single Number (Easy)](https://leetcode.com/problems/single-number/)

题目描述：给定一个非空整数数组，数组中除了一个数字只出现一次外，其它的都出现两次，找到这个单一数字。

```
Input: [2,2,1]
Output: 1
```

解题思路：异或运算，同 0 异 1。

```java
public int singleNumber(int[] nums) {
    int result = 0;
    for(int i : nums){
        result ^= i;
    }
    return result;
}
```
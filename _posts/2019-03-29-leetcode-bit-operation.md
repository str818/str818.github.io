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


## 汉明距离

[Leetcode - 461 Hamming Distance (Easy)](https://leetcode.com/problems/hamming-distance/)

题目描述：汉明距离是指两个数字二进制数对应位不同的个数，计算两个数字的汉明距离。

```
Input: x = 1, y = 4

Output: 2

Explanation:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑

The above arrows point to positions where the corresponding bits are different.
```

解法一：

```java
public int hammingDistance(int x, int y) {
    int xor = x ^ y, count = 0;
    for(int i = 0; i < 32; i++){
        count += (xor >> i) & 1; 
    }
    return count;
}
```

解法二：

```java
public int hammingDistance(int x, int y) {
    return Integer.bitCount(x ^ y);
}
```
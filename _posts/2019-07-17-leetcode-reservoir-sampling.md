---
layout: article
title: Leetcode —「蓄水池采样」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Reservoir_Sampling
pageview: true
toc: true
show_subscribe: false
---

给定一个无限的数据流，要求随机取出 k 个数。也就是说当数据流有 N 个数据时，不论 N 为多少，每个数被取出的概率都为 k / N。

- 先取出前 k 个数；
- 从第 k+1 开始，以 k / i 的概率取出这个数，并随机替换掉之前已经取出的 k 个数中的一个。

## 随机数索引

[Leetcode - 398 Random Pick Index (Medium)](https://leetcode.com/problems/random-pick-index/)

题目描述：给定一个可能含有重复元素的整数数组，要求随机输出给定的数字的索引。

```java
private int[] nums;
private Random rand;
public Solution(int[] nums) {
    this.nums = nums;
    this.rand = new Random();
}
public int pick(int target) {
    int total = 0;
    int res = -1;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] == target) {
            int x = rand.nextInt(++total);
            res = x == 0 ? i : res;
        }
    }
    return res;
}
```
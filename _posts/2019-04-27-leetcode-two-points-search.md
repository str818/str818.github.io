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


## 求开方

[Leetcode - 69 Sqrt(x) (Easy)](https://leetcode.com/problems/sqrtx/)

题目描述：输入一个整数，求该整数的开方，去除结果的小数位，返回整数。

解题思路：测试用例中含有较大的整数，所以要用 x / m 进行比较。

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
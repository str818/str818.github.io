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
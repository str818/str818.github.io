---
layout: article
title: Leetcode —「数学」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Math
pageview: true
toc: true
show_subscribe: false
---

## 回文数

[Leetcode - 9 Palindrome Number (Easy)](https://leetcode.com/problems/palindrome-number/)

题目描述：判断一个整数是否为回文数。

```
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

```java
public boolean isPalindrome(int x) {
    int rev = 0;
    int inputX = x;
    while(x > 0){
        rev = rev * 10 + x % 10;
        x = x / 10;
    }
    return inputX == rev;
}
```
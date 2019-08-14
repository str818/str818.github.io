---
layout: article
title: Leetcode —「分治」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Divide_And_Conquer
pageview: true
toc: true
show_subscribe: false
---


## 为运算表达式设计优先级

[Leetcode - 241 Different Ways to Add Parentheses (Medium)](https://leetcode.com/problems/different-ways-to-add-parentheses/)

题目描述：列出所有加括号的可能结果。

```
Input: "2-1-1"
Output: [0, 2]
Explanation: 
((2-1)-1) = 0 
(2-(1-1)) = 2
```

```java
public List<Integer> diffWaysToCompute(String input) {
    List<Integer> res = new ArrayList<Integer>();
    for (int i = 0; i < input.length(); i++) {
        char c = input.charAt(i);
        if (c == '-' || c == '+' || c == '*') {
            List<Integer> left = diffWaysToCompute(input.substring(0, i));
            List<Integer> right = diffWaysToCompute(input.substring(i + 1));
            for (int l : left) {
                for (int r : right) {
                    switch (c) {
                        case '+':
                            res.add(l + r);
                            break;
                        case '-':
                            res.add(l - r);
                            break;
                        case '*':
                            res.add(l * r);
                            break;
                    }
                }
            }
        }
    }
    if (res.size() == 0) {
        res.add(Integer.valueOf(input));
    }
    return res;
}
```
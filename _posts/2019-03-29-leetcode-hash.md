---
layout: article
title: Leetcode —「哈希」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Hash
pageview: true
toc: true
show_subscribe: false
---

## 珠宝和石头

[Leetcode - 771 Jewels and Stones (Easy)](https://leetcode.com/problems/jewels-and-stones/)

题目描述：字符串 J 表示珠宝的种类，字符串 S 表示石头的种类，判断字符串 S 中的石头是珠宝的有多少块。

```
Input: J = "aA", S = "aAAbbbb"
Output: 3
```

```java
public int numJewelsInStones(String J, String S) {
    HashSet<Character> set = new HashSet<>();
    for(int i = 0; i < J.length(); i++){
        set.add(J.charAt(i));
    }
    int count = 0; 
    for(int i = 0; i < S.length(); i++){
        if(set.contains(S.charAt(i))){
            count++;
        }
    }
    return count;
}
```
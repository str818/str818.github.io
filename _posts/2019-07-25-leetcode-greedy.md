---
layout: article
title: Leetcode —「贪心」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Greedy
pageview: true
toc: true
show_subscribe: false
---

## 分发饼干

[Leetcode - 455 Assign Cookies (Easy)](https://leetcode.com/problems/assign-cookies/)

题目描述：每个孩子都有一个满足度，每个饼干都有一个大小，只有饼干的大小大于等于一个孩子的满足度，该孩子才会获得满足。求解最多可以获得满足的孩子数量。

```java
public int findContentChildren(int[] g, int[] s) {
    Arrays.sort(g);
    Arrays.sort(s);
    int gi = 0, si = 0;
    while (gi < g.length && si < s.length) {
        if (s[si] >= g[gi]) {
            gi++;
        }
        si++;
    }
    return gi;
}
```
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

## 无重叠区间

[Leetcode - 435 Non-overlapping Intervals (Medium)](https://leetcode.com/problems/non-overlapping-intervals/)

题目描述：给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

解释思路：根据右边界进行排序，选择的区间结尾越小，留给后面的区间空间越大，依次判断当前左区间是否大于等于上一个的右区间。

```java
public int eraseOverlapIntervals(int[][] intervals) {
    if (intervals.length == 0) return 0;
    
    Arrays.sort(intervals, (i1, i2) -> Integer.compare(i1[1], i2[1]));
    
    int end = Integer.MIN_VALUE, count = 0;
    for (int i = 0; i < intervals.length; i++) {
        if (intervals[i][0] >= end) end = intervals[i][1];
        else count++;
    }
    return count;
}
```
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

## 用最少数量的箭引爆气球

[Leetcode - 452 Minimum Number of Arrows to Burst Balloons (Medium)](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons)

题目描述：气球在一个水平数轴上摆放，可以重叠，飞镖垂直投向坐标轴，使得路径上的气球都被刺破。求解最小的投飞镖次数使所有气球都被刺破。

解题思路：计算不重叠的区间个数。

```java
public int findMinArrowShots(int[][] points) {
    if (points.length == 0) {
        return 0;
    }
    Arrays.sort(points, Comparator.comparingInt(o -> o[1]));
    int cnt = 1, end = points[0][1];
    for (int i = 1; i < points.length; i++) {
        if (points[i][0] <= end) {
            continue;
        }
        cnt++;
        end = points[i][1];
    }
    return cnt;
}
```

## 根据身高重建队列

[Leetcode - 406 Queue Reconstruction by Height (Medium)](https://leetcode.com/problems/queue-reconstruction-by-height/)


题目描述：假设有打乱顺序的一群人站成一个队列。 每个人由一个整数对(h, k)表示，其中h是这个人的身高，k是排在这个人前面且身高大于或等于h的人数。 编写一个算法来重建这个队列。

```
Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]
Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
```

```java
public int[][] reconstructQueue(int[][] people) {
    if (people == null || people.length == 0 || people[0].length == 0) {
        return new int[0][0];
    }
    Arrays.sort(people, (a, b) -> (a[0] == b[0] ? a[1] - b[1] : b[0] - a[0]));
    List<int[]> queue = new ArrayList<>();
    for (int[] p : people) {
        queue.add(p[1], p);
    }
    return queue.toArray(new int[queue.size()][]);
}
```

## 买股票的最佳时机

[Leetcode - 121 Best Time to Buy and Sell Stock (Easy)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

题目描述：如果你最多只允许完成一笔交易，计算能获取的最大利润。

```java
public int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;
    for (int i = 0; i < prices.length; i++) {
        if (i != 0) {
            maxProfit = Math.max(maxProfit, prices[i] - minPrice);
        }
        minPrice = Math.min(minPrice, prices[i]);
    }
    return maxProfit;
}
```

## 买股票的最佳时机 II

[Leetcode - 122 Best Time to Buy and Sell Stock II (Easy)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

题目描述：能够进行多次交易，计算能获取的最大利润。

```java
public int maxProfit(int[] prices) {
    int max = 0;
    for (int i = 1; i < prices.length; i++) {
        if (prices[i] > prices[i - 1]) {
            max += prices[i] - prices[i - 1];
        }
    }
    return max;
}
```

## 种花问题

[Leetcode - 605 Can Place Flowers (Easy)](https://leetcode.com/problems/can-place-flowers/)

题目描述：有一个花坛，不能连续种花，1 表示种了花， 0 表示没种花，给定 n 朵花，判断是否能种入花坛。

```java
public boolean canPlaceFlowers(int[] flowerbed, int n) {
    int cnt = 0;
    for (int i = 0; i < flowerbed.length && cnt < n; i++) {
        if (flowerbed[i] == 1) continue;
        int pre = i == 0 ? 0 : flowerbed[i - 1];
        int next = i == flowerbed.length - 1 ? 0 : flowerbed[i + 1];
        if (pre == 0 && next == 0) {
            cnt++;
            flowerbed[i] = 1;
        }
    }
    return cnt >= n;
}
```
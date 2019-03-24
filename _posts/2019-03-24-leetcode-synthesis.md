---
layout: article
title: Leetcode —「综合问题」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Synthesis
pageview: true
toc: true
show_subscribe: false
---

## 跳台阶

[Leetcode - 70 Climbing Stairs (Easy)](https://leetcode.com/problems/climbing-stairs/)

假设到达台阶顶部有 n 个台阶，每次只能跳 1 个或者 2 个台阶，计算总共有多少跳到台阶顶部的方案。

```
Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

#### 暴力递归

这种方式存在大量的重复计算，时间复杂度 $O(2^n)$。

```java
public int climbStairs(int n) {        
    if(n == 1 || n == 2) return n;    
    return climbStairs(n - 1) + climbStairs(n - 2);
}
```

#### 记录的递归

每种情况第一次出现是就存储下来，所以只需计算一次，时间复杂度 $O(n)$。

```java
public int climbStairs(int n) {
    int[] memo = new int[n + 1];
    Arrays.fill(memo, -1);
    return recursion(n, memo);
}
public int recursion(int n, int[] memo){
    if(n == 1 || n == 2) return n;
    if(memo[n] != -1) return memo[n];
    memo[n] = recursion(n - 1, memo) + recursion(n - 2, memo);
    return memo[n];
}
```

#### 动态规划

第 i 步是根据前两步 i - 1 和 i - 2 计算的，时间复杂度 $O(n)$。

$dp[i]=dp[i-1] + dp[i-2]$

```java
public int climbStairs(int n) {
    if(n == 1) return 1;
    int[] dp = new int[n+1];
    dp[1] = 1;
    dp[2] = 2;
    for(int i = 3; i <=n; i++){
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n];
}
```

#### 斐波那契数列

仔细一点看这道题，就是一个斐波那契数列呀，和 DP 类似，时间复杂度 $O(n)$。

$Fib(n) = Fib(n-1) + Fib(n-2)$

```java
public int climbStairs(int n) {
    if(n == 1) return 1;
    int first = 1;
    int second = 2;
    for(int i = 3; i <=n; i++){
        int third = first + second;
        first = second;
        second = third;
    }
    return second;
}
```

## 不同的路径

[Leetcode - 62 Unique Paths](https://leetcode.com/problems/unique-paths/)

机器人只能向右或向下移动，计算共有多少条路径到达最终结点。

<div align="center">  <img src="/img/algorithm_code_classical_synthesis_1.png" width="70%"/> </div><br>

#### 动态规划

$dp[i][j]=dp[i-1][j] + dp[i][j-1]$

```java
public int uniquePaths(int m, int n) { 
    int[][] dp = new int[m][n];
    for(int i = 0; i < m; i++){
        dp[i][0] = 1;
    }
    for(int j = 0; j < n; j++){
        dp[0][j] = 1;
    }
    for(int i = 1; i < m; i++){
        for(int j = 1; j < n; j++){
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    return dp[m-1][n-1];
}
```

由于每次只计算一行，所以可以只用一维数组统计。

```java
public int uniquePaths(int m, int n) { 
    int[] dp = new int[n];
    for(int i = 0; i < n; i++){
        dp[i] = 1;
    }
    for(int i = 1; i < m; i++){
        for(int j = 1; j < n; j++){
            dp[j] += dp[j-1];
        }
    }
    return dp[n-1];
}
```

#### 排列组合

- 排列

从 n 个数的元素中取出指定 r 个数的元素，进行排序。

$A_n^r=\frac{n!}{(n-r)!}$

例如从 10 人中分别选出冠军、亚军和季军，公式为$A_{10}^3$。

- 组合

从 n 个数的元素中仅仅取出指定 r 个数的元素，不考虑排序。

$C_n^r=\frac{n!}{r!(n-r)!}$

可以看出比排列少了排序的次数$r!$，例如从 10 个人中选出三个中奖者送出同样的奖品，公式为$C_{10}^3$。


本题可以看成是一个数学问题，机器人总共走了 m + n - 2 步，其中 n - 1 步向右走，m - 1 步向下走，那么总共不同的方法个数就相当于在总步数中一种走法的取法，实际上是一道组合数的问题。

```java
public int uniquePaths(int m, int n) { 
    int N = n + m - 2;  // 走的总步数
    int k = m - 1;      // 向下走的步数
    // C(N,k) = n! / (k!(n-k)!)
    // C = ((n-k+1) * (n-k+2) * ... * n)/k!
    double res = 1;
    for(int i = 1; i <= k; i++){
        res = res * (N - k + i) / i;
    }
    return (int)res;
}
```

## 数组中第 k 个最大元素

[Leetcode - 215 Kth Largest Element in an Array (Medium)](https://leetcode.com/problems/kth-largest-element-in-an-array/)

```
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

#### 最小堆

```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    for(int val : nums){
        pq.offer(val);
        if(pq.size() > k){
            pq.poll();
        }
    }
    return pq.peek();
}
```

#### 快排思想

```java
public int findKthLargest(int[] nums, int k) {
    k = nums.length - k;// 第 k 大元素的索引是 n - k
    int lo = 0;
    int hi = nums.length - 1;
    while (lo < hi) {
        final int j = partition(nums, lo, hi);
        if(j < k) {
            lo = j + 1;
        } else if (j > k) {
            hi = j - 1;
        } else {
            break;
        }
    }
    return nums[k];
}

private int partition(int[] a, int lo, int hi) {

    int i = lo;
    int j = hi + 1;
    while(true) {
        while(i < hi && less(a[++i], a[lo]));
        while(j > lo && less(a[lo], a[--j]));
        if(i >= j) {
            break;
        }
        exch(a, i, j);
    }
    exch(a, lo, j);
    return j;
}

private void exch(int[] a, int i, int j) {
    final int tmp = a[i];
    a[i] = a[j];
    a[j] = tmp;
}

private boolean less(int v, int w) {
    return v < w;
}
```
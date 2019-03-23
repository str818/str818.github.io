---
layout: article
title: Leetcode —「动态规划」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Dynamic_Programming
pageview: true
toc: true
show_subscribe: false
---

## 唯一的二叉搜索树(BST)

[Leetcode - 96 Unique Binary Search Trees (Medium)](https://leetcode.com/problems/unique-binary-search-trees/)

题目描述：给定整数 n,计算由 1...n 能组成多少个不同的二叉搜索树。

```
Input: 3
Output: 5
Explanation:
Given n = 3, there are a total of 5 unique BST's:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

解题思路：这道题非常棒，从上述的输入示例中可以看出，如果 n = 3，最后输出的可能 BST 是分别由 1、2 和 3 作为根节点的 BST 数目之和。设 G(n) 为节点数为 n 时 BST 的数量，这也就是本题需要计算的结果。那么 G(n) 是怎么计算的呢？ 设 F(i,n) 为当根节点是 i 时 BST 的数量，那么 G(n) 就可以分解成 G(n) = F(1,n) + F(2,n) +...+ F(n,n)。 

问题又来了，F(i,n) 是怎么计算的？ 假设给定序列 [1 2 3 4 5 6 7]，F(3,7) 表示以 3 为根节点 BST 的数量，这时可以划分左子树与右子树，左子树由 [1 2] 组成，为 G(2)；右子树由 [4 5 6 7] 组成，为 G(4)，那么组合结果为 F(3,7) = G(2) * G(4)，即 F(i,n) = G(i - 1) * G(n - i)。可以定义初始状态，当 n = 0 时，空树，G(0) = 1；当 n = 1 时，树中只有一个节点，G(1) = 1。

这样就可以用动态规划计算最终结果：G(n) = G(0) * G(n-1) + G(1) * G(n-2) +...+ G(n-1) * G(0)。

```java
public int numTrees(int n) {
    int [] G = new int[n+1];
    G[0] = G[1] = 1;
    for(int i=2; i<=n; ++i) {
    	for(int j=1; j<=i; ++j) {
    		G[i] += G[j-1] * G[i-j];
    	}
    }
    return G[n];
}
```
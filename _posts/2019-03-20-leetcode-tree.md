---
layout: article
title: Leetcode —「树」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Tree
pageview: true
toc: true
show_subscribe: false
---

## 遍历

### 二叉树的中序遍历

[Leetcode - 94 Binary Tree Inorder Traversal (Medium)](https://leetcode.com/problems/binary-tree-inorder-traversal/)

#### 非递归
```java
public List<Integer> inorderTraversal(TreeNode root) {
  List<Integer> ans = new ArrayList<Integer>();
  Stack<TreeNode> stack = new Stack<TreeNode>();
  TreeNode cur = root;
  while(!stack.isEmpty() || cur != null){
      while(cur != null){
          stack.push(cur);
          cur = cur.left;
      }
      cur = stack.pop();
      ans.add(cur.val);
      cur = cur.right;
  }
  return ans;
}
```

#### 递归

```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> ans = new ArrayList<Integer>();
    backtrack(ans, root);
    return ans;
}
public void backtrack(List<Integer> ans, TreeNode root){
    if(root == null) return;
    backtrack(ans, root.left);
    ans.add(root.val);
    backtrack(ans, root.right);
}
```
### 验证二叉搜索树

[Leetcode - 98 Validate Binary Search Tree (Medium)](https://leetcode.com/problems/validate-binary-search-tree/)

题目描述：给出一个二叉树，判断是否是二叉搜索树。

```
Input:
    2
   / \
  1   3
Output: true
```

解题思路：二叉搜索树需要左子树比根节点小，右子树比根节点大，注意是子树，而不是结点，所以需要子树中的每一个结点都比根节点小/大。

```java
public boolean isValidBST(TreeNode root) {
    return backtrack(root, Long.MAX_VALUE, Long.MIN_VALUE);
}
public boolean backtrack(TreeNode root, long maxValue, long minValue){
    if(root == null) return true;
    if(root.val >= maxValue || root.val <= minValue) return false;
    return backtrack(root.left, root.val, minValue) && backtrack(root.right, maxValue, root.val);
}
```




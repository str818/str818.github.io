---
layout: article
title: 「树」系列题解
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





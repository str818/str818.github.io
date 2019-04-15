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

## 二叉树的遍历

### 先序遍历

[Leetcode - 144 Binary Tree Preorder Traversal (Medium)](https://leetcode.com/problems/binary-tree-preorder-traversal/)

解法一：递归
```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> ansList = new ArrayList<Integer>();
    backtrack(ansList, root);
    return ansList;
}

public void backtrack(List<Integer> ansList, TreeNode root){
    if(root == null) return;
    ansList.add(root.val);
    backtrack(ansList, root.left);
    backtrack(ansList, root.right);
}
```

解法二：遍历
```java
public List<Integer> preorderTraversal(TreeNode node) {
    List<Integer> list = new LinkedList<Integer>();
    Stack<TreeNode> rights = new Stack<TreeNode>();
    while(node != null) {
        list.add(node.val);
        if (node.right != null) {
            rights.push(node.right);
        }
        node = node.left;
        if (node == null && !rights.isEmpty()) {
            node = rights.pop();
        }
    }
    return list;
}
```

### 中序遍历

[Leetcode - 94 Binary Tree Inorder Traversal (Medium)](https://leetcode.com/problems/binary-tree-inorder-traversal/)

解法一：递归
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

解法二：遍历
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

### 后序遍历

[Leetcode - 145 Binary Tree Postorder Traversal (Hard)](https://leetcode.com/problems/binary-tree-postorder-traversal/)

解法一：递归
```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> ansList = new ArrayList<Integer>();
    backtrack(ansList, root);
    return ansList;
}

public void backtrack(List<Integer> ansList, TreeNode root){
    if(root == null) return;
    backtrack(ansList, root.left);
    backtrack(ansList, root.right);
    ansList.add(root.val);
}
```

解法二：遍历
```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> ans = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<>();
    if (root == null) return ans;
    stack.push(root);
    while (!stack.isEmpty()) {
        TreeNode cur = stack.pop();
        ans.addFirst(cur.val);
        if (cur.left != null) {
            stack.push(cur.left);
        }
        if (cur.right != null) {
            stack.push(cur.right);
        } 
    }
    return ans;
}
```

### 层次遍历

[Leetcode - 102 Binary Tree Level Order Traversal (Medium)](https://leetcode.com/problems/binary-tree-level-order-traversal/)

```
Input: [3,9,20,null,null,15,7]
    3
   / \
  9  20
    /  \
   15   7
Output:
[
  [3],
  [9,20],
  [15,7]
]
```

解法一：递归

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> ans = new ArrayList<>();
    backtrack(ans, root, 0);
    return ans;
}
public void backtrack(List<List<Integer>> ans, TreeNode curNode, int level){ 
    if(curNode == null) return;
    if(ans.size() - 1 < level){
        ans.add(new ArrayList<Integer>());
    }
    ans.get(level).add(curNode.val);
    backtrack(ans, curNode.left, level + 1);
    backtrack(ans, curNode.right, level + 1);
}
```

解法二：遍历

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
    if(root == null) return wrapList;
    queue.offer(root);
    while(!queue.isEmpty()){
        int levelNum = queue.size();
        List<Integer> subList = new LinkedList<Integer>();
        for(int i=0; i<levelNum; i++) {
            if(queue.peek().left != null) queue.offer(queue.peek().left);
            if(queue.peek().right != null) queue.offer(queue.peek().right);
            subList.add(queue.poll().val);
        }
        wrapList.add(subList);
    }
    return wrapList;
}
```

## 先序遍历系列

### 判断相同树

[Leetcode 100 - Same Tree (Easy)](https://leetcode.com/problems/same-tree/)

```
Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true
```

解法一：递归
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if(p == null && q == null) return true;
    if(p == null || q == null) return false;
    if(p.val != q.val) return false;
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}
```

解法二：遍历
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    Stack<TreeNode> stack_p = new Stack <> ();       
    Stack<TreeNode> stack_q = new Stack <> ();
    if (p != null) stack_p.push( p ) ;
    if (q != null) stack_q.push( q ) ;
    while (!stack_p.isEmpty() && !stack_q.isEmpty()) {
        TreeNode pn = stack_p.pop() ;
        TreeNode qn = stack_q.pop() ;	    	
        if (pn.val != qn.val) return false ;
        if (pn.right != null) stack_p.push(pn.right) ;
        if (qn.right != null) stack_q.push(qn.right) ;
        if (stack_p.size() != stack_q.size()) return false ;
        if (pn.left != null) stack_p.push(pn.left) ;	    	 	    	 
        if (qn.left != null) stack_q.push(qn.left) ;
        if (stack_p.size() != stack_q.size()) return false ;
    }		     
    return stack_p.size() == stack_q.size() ;	 
}
```

### 对称树

[Leetcode - 101 Symmetric Tree (Easy)](https://leetcode.com/problems/symmetric-tree/)

题目描述：判断一个二叉树是不是对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

解法一：递归

```java
public boolean isSymmetric(TreeNode root) {
    return isMirror(root, root);
}

public boolean isMirror(TreeNode t1, TreeNode t2) {
    if (t1 == null && t2 == null) return true;
    if (t1 == null || t2 == null) return false;
    return (t1.val == t2.val)
        && isMirror(t1.right, t2.left)
        && isMirror(t1.left, t2.right);
}
```

解法二：遍历

```java
public boolean isSymmetric(TreeNode root) {
    Queue<TreeNode> q = new LinkedList<>();
    q.add(root);
    q.add(root);
    while (!q.isEmpty()) {
        TreeNode t1 = q.poll();
        TreeNode t2 = q.poll();
        if (t1 == null && t2 == null) continue;
        if (t1 == null || t2 == null) return false;
        if (t1.val != t2.val) return false;
        q.add(t1.left);
        q.add(t2.right);
        q.add(t1.right);
        q.add(t2.left);
    }
    return true;
}
```

### 反转二叉树

[Leetcode - 226 Invert Binary Tree (Easy)](https://leetcode.com/problems/invert-binary-tree/)

```
Input:
     4
   /   \
  2     7
 / \   / \
1   3 6   9
Output:
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

解法一：递归

```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    TreeNode right = invertTree(root.right);
    TreeNode left = invertTree(root.left);
    root.left = right;
    root.right = left;
    return root;
}
```

解法二：非递归

```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    queue.add(root);
    while (!queue.isEmpty()) {
        TreeNode current = queue.poll();
        TreeNode temp = current.left;
        current.left = current.right;
        current.right = temp;
        if (current.left != null) queue.add(current.left);
        if (current.right != null) queue.add(current.right);
    }
    return root;
}
```

### 二叉树路径

[Leetcode - 257 Binary Tree Paths (Easy)](https://leetcode.com/problems/binary-tree-paths/)

```
Input:

   1
 /   \
2     3
 \
  5

Output: ["1->2->5", "1->3"]

Explanation: All root-to-leaf paths are: 1->2->5, 1->3
```

解法一：递归

```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> answer = new ArrayList<String>();
    if (root != null) searchBT(root, "", answer);
    return answer;
}
private void searchBT(TreeNode root, String path, List<String> answer) {
    if (root.left == null && root.right == null) answer.add(path + root.val);
    if (root.left != null) searchBT(root.left, path + root.val + "->", answer);
    if (root.right != null) searchBT(root.right, path + root.val + "->", answer);
}
```

解法二：遍历

```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> list=new ArrayList<String>();
    Queue<TreeNode> qNode=new LinkedList<TreeNode>();
    Queue<String> qStr=new LinkedList<String>();
    
    if (root==null) return list;
    qNode.add(root);
    qStr.add("");
    while(!qNode.isEmpty()) {
        TreeNode curNode=qNode.remove();
        String curStr=qStr.remove();
        
        if (curNode.left==null && curNode.right==null) list.add(curStr+curNode.val);
        if (curNode.left!=null) {
            qNode.add(curNode.left);
            qStr.add(curStr+curNode.val+"->");
        }
        if (curNode.right!=null) {
            qNode.add(curNode.right);
            qStr.add(curStr+curNode.val+"->");
        }
    }
    return list;
}
```

### 路径和 I

[Leetcode - 112 Path Sum (Easy)](https://leetcode.com/problems/path-sum/)

```
Given the below binary tree and sum = 22,

      5
     / \
    4   8
   /   / \
  11  13  4
 /  \      \
7    2      1

return true, as there exist a root-to-leaf path 5->4->11->2 which sum is 22.
```

解法一：递归

```java
public boolean hasPathSum(TreeNode root, int sum) {
    if(root == null) return false;
    if(root.left == null && root.right == null && sum == root.val) return true;
    return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
}
```

解法二：遍历

```java
public boolean hasPathSum(TreeNode root, int sum) {
    Stack <TreeNode> stack = new Stack<> ();	    
    stack.push(root) ;	    
    while (!stack.isEmpty() && root != null){
        TreeNode cur = stack.pop() ;	
        if (cur.left == null && cur.right == null){	    		
            if (cur.val == sum ) return true ;
        }
        if (cur.right != null) {
            cur.right.val = cur.val + cur.right.val ;
            stack.push(cur.right) ;
        }
        if (cur.left != null) {
            cur.left.val = cur.val + cur.left.val;
            stack.push(cur.left);
        }
    }	    
    return false ;
}
```

### 路径和 II

[Leetcode - 113 Path Sum II (Medium)](https://leetcode.com/problems/path-sum-ii/)

题目描述：返回路径和为给定值的路径。

```
Given the below binary tree and sum = 22,

      5
     / \
    4   8
   /   / \
  11  13  4
 /  \    / \
7    2  5   1
Return:
[
   [5,4,11,2],
   [5,8,4,5]
]
```

解法一：递归
```java
public List<List<Integer>> pathSum(TreeNode root, int sum) {
    List<List<Integer>> ansList = new ArrayList<>();
    pathSum(root, sum, new ArrayList<Integer>(), ansList);
    return ansList;
}

public void pathSum(TreeNode root, int sum, List<Integer> curList, List<List<Integer>> ansList){
    if(root == null) return;
    
    if(root.left == null && root.right == null && sum - root.val == 0){
        List<Integer> cur = new ArrayList(curList);
        cur.add(root.val);
        ansList.add(cur);
        return;
    }
    curList.add(root.val);
    pathSum(root.left, sum - root.val, curList, ansList);
    pathSum(root.right, sum - root.val, curList, ansList);
    curList.remove(curList.size() - 1);
    return;
}
```

解法二：遍历
```java
public List<List<Integer>> pathSum(TreeNode root, int sum) {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<TreeNode>();
    int SUM = 0;
    TreeNode cur = root;
    TreeNode pre = null;
    while(cur!=null || !stack.isEmpty()){
        while(cur!=null){
            stack.push(cur);
            path.add(cur.val);
            SUM+=cur.val;
            cur=cur.left;
        }
        cur = stack.peek();
        if(cur.right!=null && cur.right!=pre){
            cur = cur.right;
            continue;
        } 
        if(cur.left==null && cur.right==null && SUM==sum) 
            res.add(new ArrayList<Integer>(path));

        pre = cur;
        stack.pop();
        path.remove(path.size()-1);
        SUM-=cur.val;
        cur = null;
    }
    return res;
}
```

### 根到叶节点数字之和

[Leetcode - 129 Sum Root to Leaf Numbers (Medium)](https://leetcode.com/problems/sum-root-to-leaf-numbers/)

```
Input: [4,9,0,5,1]
    4
   / \
  9   0
 / \
5   1
Output: 1026
Explanation:
The root-to-leaf path 4->9->5 represents the number 495.
The root-to-leaf path 4->9->1 represents the number 491.
The root-to-leaf path 4->0 represents the number 40.
Therefore, sum = 495 + 491 + 40 = 1026.
```

```java
public int sumNumbers(TreeNode root) {
    return sum(root, 0); 
}
public int sum(TreeNode root, int s){
    if(root == null) return 0;
    if(root.left == null && root.right == null){
        return s * 10 + root.val;
    }
    return sum(root.left, s * 10 + root.val) + sum(root.right, s * 10 + root.val);
}
```

### 二叉树的最小深度

[Leetcode - 111 Minimum Depth of Binary Tree (Easy)](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

```
Given binary tree [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7
return its minimum depth = 2.
```

```java
public int minDepth(TreeNode root) {
    if(root == null) return 0;
    int left = minDepth(root.left);
    int right = minDepth(root.right);
    return (left == 0 || right == 0) ? left + right + 1: Math.min(left,right) + 1;
}
```

## 后续遍历系列

### 二叉树的最大深度

[Leetcode - 104 Maximum Depth of Binary Tree (Easy)](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

```java
public int maxDepth(TreeNode root) {
    if(root == null) return 0;
    return Math.max(maxDepth(root.left),maxDepth(root.right)) + 1;
}
```

### 判断平衡二叉树

[Leetcode - 110 Balanced Binary Tree (Easy)](https://leetcode.com/problems/balanced-binary-tree/)

题目描述：平衡二叉树左右子树高度相差不能超过 1。

```
Given the following tree [3,9,20,null,null,15,7]:

    3
   / \
  9  20
    /  \
   15   7
Return true.
```

```java
public boolean isBalanced(TreeNode root) {
    return depth(root) != -1;
}

public int depth(TreeNode root){
    if(root == null) return 0;
    int left = depth(root.left);
    int right = depth(root.right);
    
    if(left == -1 || right == -1){
        return -1;
    }
    
    if(Math.abs(left - right) > 1){
        return -1;
    }
    return Math.max(left, right) + 1;
}
```

### 二叉树最大路径和

[Leetcode - 124 Binary Tree Maximum Path Sum (Hard)](https://leetcode.com/problems/binary-tree-maximum-path-sum/)

题目描述：在二叉树中找到一个路径，使得路径和最大，不限制路径的起点与终点。

```
Input: [21,9,20,null,null,15,7]

   21
   / \
  9  20
    /  \
   15   7

Output: 56
```

题目描述：先递归到叶子节点，如果以 20 为根节点，最大路径和为 20 + 15 + 7，如果再向上回溯到 20 时，就不能同时取两条路径了，15 和 7 中只能选择一个最大的，记录所有路径中的最大路径和。

```java
int maxValue;
public int maxPathSum(TreeNode root) {
    maxValue = Integer.MIN_VALUE;
    maxPathDown(root);
    return maxValue;
}
public int maxPathDown(TreeNode root){
    if(root == null) return 0;
    int left = Math.max(0, maxPathDown(root.left));
    int right = Math.max(0, maxPathDown(root.right));
    maxValue = Math.max(maxValue, left + right + root.val);
    return Math.max(left, right) + root.val;
}
```

### 打家劫舍 III

[Leetcode - 337 House Robber III (Medium)](https://leetcode.com/problems/house-robber-iii/)

题目描述：连续偷窃相连的节点会触发警报，要求在不触发警报的情况下计算偷到的最大金额。

```
Input: [3,4,5,1,3,null,1]

     3
    / \
   4   5
  / \   \ 
 1   3   1

Output: 9
Explanation: Maximum amount of money the thief can rob = 4 + 5 = 9.
```

```java
public int rob(TreeNode root) {
    int[] res = robSub(root);
    return Math.max(res[0], res[1]);
}
public int[] robSub(TreeNode root){
    if(root == null) return new int[2];
    
    int[] left = robSub(root.left);
    int[] right = robSub(root.right);
    
    int[] res = new int[2];
    // 不包含当前节点的最大值
    res[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
    // 包含当前节点的最大值
    res[1] = root.val + left[0] + right[0];
    
    return res;
}
```

## 层次遍历系列

### 二叉树的层次遍历 II

[Leetcode - 107 Binary Tree Level Order Traversal II (Easy)](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/)

题目描述：将二叉树从下到上存储。

```
Given binary tree [3,9,20,null,null,15,7],
    3
   / \
  9  20
    /  \
   15   7
return its bottom-up level order traversal as:
[
  [15,7],
  [9,20],
  [3]
]
```

解法一：递归 BFS

```java
public List<List<Integer>> levelOrderBottom(TreeNode root) {
    List<List<Integer>> ansList = new ArrayList<>();
    backtrack(ansList, root, 0);
    return ansList;
}
public void backtrack(List<List<Integer>> ansList, TreeNode root, int level){
    if(root == null) return;
    if(ansList.size() <= level){
        ansList.add(0, new ArrayList<>());
    }
    backtrack(ansList, root.left, level + 1);
    backtrack(ansList, root.right, level + 1);
    ansList.get(ansList.size() - level - 1).add(root.val);
}
```

解法二：遍历
```java
public List<List<Integer>> levelOrderBottom(TreeNode root) {
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
    
    if(root == null) return wrapList;
    
    queue.offer(root);
    while(!queue.isEmpty()){
        int levelNum = queue.size();
        List<Integer> subList = new LinkedList<Integer>();
        for(int i=0; i<levelNum; i++) {
            if(queue.peek().left != null) queue.offer(queue.peek().left);
            if(queue.peek().right != null) queue.offer(queue.peek().right);
            subList.add(queue.poll().val);
        }
        wrapList.add(0, subList);
    }
    return wrapList;
}
```

### 二叉树「之」字形层次遍历

[Leetcode - 103 Binary Tree Zigzag Level Order Traversal (Medium)](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

```
Given binary tree [3,9,20,null,null,15,7],
    3
   / \
  9  20
    /  \
   15   7
return its zigzag level order traversal as:
[
  [3],
  [20,9],
  [15,7]
]
```

解法一：递归

```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> ansList = new ArrayList<>();
    helper(ansList, root, 0);
    return ansList;
}

public void helper(List<List<Integer>> ansList, TreeNode root, int level){
    if(root == null) return;
    if(level >= ansList.size()){
        ansList.add(new ArrayList<Integer>());
    }
    if(level % 2 == 0){
        ansList.get(level).add(root.val);
    }else{
        ansList.get(level).add(0, root.val);
    }
    helper(ansList, root.left, level + 1);
    helper(ansList, root.right, level + 1);
}
```

解法二：遍历
```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> ansList = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    if(root == null) return ansList;
    queue.offer(root);
    boolean isOdd = false;
    int size = 1;
    while(!queue.isEmpty()){
        List<Integer> curList = new ArrayList<>();
        for(int i = 0; i < size; i++){
            TreeNode curNode = queue.poll();
            if(!isOdd){
                curList.add(curNode.val);
            }else{
                curList.add(0, curNode.val);
            }
            if(curNode.left != null) queue.offer(curNode.left);
            if(curNode.right != null) queue.offer(curNode.right);
        }
        ansList.add(curList);
        isOdd = isOdd ? false : true;
        size = queue.size();
    }
    return ansList;
}
```

### 二叉树的右侧视图

[Leetcode - 199 Binary Tree Right Side View (Medium)](https://leetcode.com/problems/binary-tree-right-side-view/)

题目描述：输出从二叉树右侧看到的节点，也就是每一层最右边的节点。

```
Input: [1,2,3,null,5,null,4]
Output: [1, 3, 4]
Explanation:

   1            <---
 /   \
2     3         <---
 \     \
  5     4       <---
```

解法一：递归

```java
public List<Integer> rightSideView(TreeNode root) {
    List<Integer> result = new ArrayList<Integer>();
    rightView(root, result, 0);
    return result;
}
public void rightView(TreeNode curr, List<Integer> result, int currDepth){
    if(curr == null){
        return;
    }
    if(currDepth == result.size()){
        result.add(curr.val);
    }
    rightView(curr.right, result, currDepth + 1);
    rightView(curr.left, result, currDepth + 1);
}
```
解法二：遍历

```java
public List<Integer> rightSideView(TreeNode root) {
    List<Integer> ansList = new ArrayList<Integer>();
    Queue<TreeNode> queue = new LinkedList<>();
    if(root == null) return ansList;
    queue.offer(root);
    int size = 1;
    while(!queue.isEmpty()){
        for(int i = 0; i < size; i++){
            TreeNode curNode = queue.poll();
            if(i == size - 1){
                ansList.add(curNode.val);
            }
            if(curNode.left != null) queue.offer(curNode.left);
            if(curNode.right != null) queue.offer(curNode.right);
        }
        size = queue.size();
    }
    return ansList;
}
```

## 二叉搜索树(BST)

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

### 二叉搜索树的最小公共父节点

[Leetcode - 235 Lowest Common Ancestor of a Binary Search Tree (Easy)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.
```

解题思路：利用好二叉搜索树的性质。

解法一：递归

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (p.val > root.val && q.val > root.val) {
        return lowestCommonAncestor(root.right, p, q);
    } else if (p.val < root.val && q.val < root.val) {
        return lowestCommonAncestor(root.left, p, q);
    } else {
        return root;
    }
}
```

解法二：遍历

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    while(root != null){
        if (p.val > root.val && q.val > root.val) {
            root = root.right;
        } else if (p.val < root.val && q.val < root.val) {
            root = root.left;
        } else {
            return root;
        }
    }
    return null;
}
```

### 二叉树的最小公共父节点

[Leetcode - 236 Lowest Common Ancestor of a Binary Tree (Medium)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    return left == null ? right : right == null ? left : root;
}
```

### 有序数组转为二叉搜索树

[Leetcode - 108 Convert Sorted Array to Binary Search Tree (Easy)](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

解题思路：二分搜索的思想，不明白为什么我用 0 到 num.length 划分，就只有 22%，而改成 0 到 num.length - 1 就是 100% 了。

```java
public TreeNode sortedArrayToBST(int[] num) {
    return helper(num, 0, num.length - 1);
}
public TreeNode helper(int[] num, int low, int high) {
    if (low > high) return null;
    int mid = (low + high) / 2;
    TreeNode node = new TreeNode(num[mid]);
    node.left = helper(num, low, mid - 1);
    node.right = helper(num, mid + 1, high);
    return node;
}
```

### 有序链表转为二叉搜索树

[Leetcode - 109 Convert Sorted List to Binary Search Tree](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)

```java
public TreeNode sortedListToBST(ListNode head) {
    if(head==null) return null;
    return toBST(head,null);
}
public TreeNode toBST(ListNode head, ListNode tail){
    ListNode slow = head;
    ListNode fast = head;
    if(head==tail) return null;

    while(fast!=tail&&fast.next!=tail){
        fast = fast.next.next;
        slow = slow.next;
    }
    TreeNode thead = new TreeNode(slow.val);
    thead.left = toBST(head,slow);
    thead.right = toBST(slow.next,tail);
    return thead;
}
```

### 二叉搜索树迭代器

[Leetcode - 173 Binary Search Tree Iterator (Medium)](https://leetcode.com/problems/binary-search-tree-iterator/)

题目描述：构造一个迭代器，每次调用 next() 方法返回下一个最小节点的值，调用 hasNext() 返回是否有下一个最小节点。时间复杂度 O(1)，空间复杂度 O(h)，h 是树的高度。

```java
class BSTIterator {
    Stack<TreeNode> stack = new Stack<TreeNode>();
    
    public BSTIterator(TreeNode root) {
        pushAll(root);
    }
    
    public int next() {
        TreeNode node = stack.pop();
        pushAll(node.right);
        return node.val;
    }
    
    public boolean hasNext() {
        return !stack.isEmpty();
    }
    
    public void pushAll(TreeNode node){
        for(;node != null;stack.push(node), node = node.left);
    }
}
```

### 二叉搜索树中第 K 小的节点

[Leetcode - 230 Kth Smallest Element in a BST (Medium)](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

解法一：递归

```java
int result = 0;
int count = 0;
public int kthSmallest(TreeNode root, int k) {
    traverse(root, k);
    return result;
}
public void traverse(TreeNode root, int k){
    if(root == null) return;
    traverse(root.left, k);
    if(++count == k) result = root.val;
    traverse(root.right, k);
}
```

解法二：遍历

```java
public int kthSmallest(TreeNode root, int k) {
    Stack<TreeNode> stack = new Stack<TreeNode>();
    TreeNode cur = root;
    int count = 0;

    while(!stack.isEmpty() || cur != null){
        if(cur != null){
            stack.push(cur);
            cur = cur.left;
        }else{
            cur = stack.pop();
            if(++count == k){
                return cur.val;
            }
            cur = cur.right;
        }
    }
    return -1;
}
```

### 二叉树的序列化与反序列化

[Leetcode - 297 Serialize and Deserialize Binary Tree (Hard)](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

题目描述：不限制方法。

```java
private static final String spliter = ",";
private static final String NN = "X";
// 序列化
public String serialize(TreeNode root) {
    StringBuilder sb = new StringBuilder();
    buildString(root, sb);
    return sb.toString();
}

private void buildString(TreeNode node, StringBuilder sb) {
    if (node == null) {
        sb.append(NN).append(spliter);
    } else {
        sb.append(node.val).append(spliter);
        buildString(node.left, sb);
        buildString(node.right,sb);
    }
}
// 反序列化
public TreeNode deserialize(String data) {
    Deque<String> nodes = new LinkedList<>();
    nodes.addAll(Arrays.asList(data.split(spliter)));
    return buildTree(nodes);
}

private TreeNode buildTree(Deque<String> nodes) {
    String val = nodes.remove();
    if (val.equals(NN)) return null;
    else {
        TreeNode node = new TreeNode(Integer.valueOf(val));
        node.left = buildTree(nodes);
        node.right = buildTree(nodes);
        return node;
    }
}
```

### 恢复二叉搜索树

[Leetcode - 99 Recover Binary Search Tree (Hard)](https://leetcode.com/problems/recover-binary-search-tree/)

题目描述：二叉搜索树中有两个节点是错位的，在不改变树结构的条件下使二叉树复位。

解题思路：首先想到的就是中序遍历的方法，找到两个错位的节点，因为中序遍历之后前面的节点一定小于后面的节点，如果不是则表示错位了，但是题目进阶要求空间复杂度为 O(1)，中序遍历不论是栈+遍历还是递归的方式空间复杂度都是 O(n)，有没有一种二叉树的遍历方式空间复杂度为 O(1)？有，那就是 Morris Traversal。

解法一：递归

```java
TreeNode firstNode = null;
    TreeNode secondNode = null;
    TreeNode preNode = new TreeNode(Integer.MIN_VALUE);
    public void recoverTree(TreeNode root) {
        traverse(root);
        int temp = firstNode.val;
        firstNode.val = secondNode.val;
        secondNode.val = temp;
    }
    public void traverse(TreeNode root){
        if(root == null) return;
        traverse(root.left);
        if(firstNode == null && preNode.val >= root.val){
            firstNode = preNode;  // 注意，第一个是 preNode
        }
        if(firstNode != null && preNode.val >= root.val){
            secondNode = root;    // 第二个是 root
        }
        preNode = root;
        traverse(root.right);
    }
```

解法二：Morris Traversal

利用所有叶子节点的 right 指针，指向后继节点，组成一个环，这样就能通过指针的方式记录下当前根节点的位置。

```java
public void recoverTree(TreeNode root) {
    TreeNode first = null;
    TreeNode second = null;

    TreeNode pred = null; // 左子树的最右边节点
    TreeNode prev = null; 

    TreeNode curr = root;

    while(curr != null){
        //for each node, we compare it with prev node as we did in in-order-traversal
        if(prev != null && curr.val <= prev.val){
            if(first == null) first = prev;
            second = curr;
        }

        if(curr.left != null){
            // 找到左子树的最右边的节点
            pred = curr.left;
            while(pred.right != null && pred.right != curr){
                pred = pred.right;
            }

            if(pred.right == curr){
                // 第二次访问该节点，返回到当前子树的后继节点
                pred.right = null;
                prev = curr;
                curr = curr.right;
            }else{
                pred.right = curr;
                curr = curr.left;
            }

        }else{
            // 没有左子树，直接访问右子树
            prev = curr;
            curr = curr.right;
        }
    }

    int temp = first.val;
    first.val = second.val;
    second.val = temp;
}
```

## 其它

### 每个节点的右向指针

[Leetcode - 116 Populating Next Right Pointers in Each Node (Medium)](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

题目描述：假设二叉树是满二叉树。

<div align="center">  <img src="/img/leetcode-116.png" width="80%"/> </div><br>

解题思路：这道题出的非常好，从上一层对下一层修改。

```java
public Node connect(Node root) {
    Node levelStart = root;
    while(levelStart != null){
        Node cur = levelStart;
        while(cur != null){
            if(cur.left != null) cur.left.next = cur.right;
            if(cur.right != null && cur.next != null) cur.right.next = cur.next.left;
            cur = cur.next;
        }
        levelStart = levelStart.left;
    }
    return root;
}
```

### 每个节点的右向指针

[Leetcode - 117 Populating Next Right Pointers in Each Node II (Medium)](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

题目描述：与上一道题不同的是，本次没有规定是满二叉树。

```java
public Node connect(Node root) {
    Node cur = root;
    while (cur != null) {
        Node dummy = new Node(0);
        Node son = dummy;
        while (cur != null) {
            if (cur.left != null) {
                son.next = cur.left;
                son = son.next;
            }
            if (cur.right != null) {
                son.next = cur.right;
                son = son.next;
            }
            cur = cur.next;
        }
        cur = dummy.next;
    }
    return root;
}
```

### 实现前缀树(字典树)

[Leetcode - 207 Implement Trie(Prefix Tree) (Medium)](https://leetcode.com/problems/implement-trie-prefix-tree/)

```java
class TrieNode{
    public char val;
    public boolean isWord;
    public TrieNode[] children = new TrieNode[26];
    public TrieNode(){}
    TrieNode(char c){
        TrieNode node = new TrieNode();
        node.val = c;
    }
}

class Trie {

    private TrieNode root;
    public Trie() {
        root = new TrieNode();
        root.val = ' ';
    }
    
    // 插入
    public void insert(String word) {
        TrieNode ws = root;
        for(int i = 0; i < word.length(); i++){
            char c = word.charAt(i);
            if(ws.children[c - 'a'] == null){
                ws.children[c - 'a'] = new TrieNode(c);
            }
            ws = ws.children[c - 'a'];
        }
        ws.isWord = true;
    }
    
    // 搜索
    public boolean search(String word) {
        TrieNode ws = root;
        for(int i = 0; i < word.length(); i++){
            char c = word.charAt(i);
            if(ws.children[c - 'a'] == null) return false;
            ws = ws.children[c - 'a'];
        }
        return ws.isWord;
    }
    
    // 判断是否包含前缀字符
    public boolean startsWith(String prefix) {
        TrieNode ws = root; 
        for(int i = 0; i < prefix.length(); i++){
            char c = prefix.charAt(i);
            if(ws.children[c - 'a'] == null) return false;
            ws = ws.children[c - 'a'];
        }
        return true;
    }
}
```

### 根据前序和中序遍历构建二叉树

[Leetcode - 105 Construct Binary Tree from Preorder and Inorder Traversal (Medium)](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```java
public TreeNode buildTree(int[] preorder, int[] inorder) {
    return backtrack(preorder, inorder, 0, 0, inorder.length - 1);
}
public TreeNode backtrack(int[] preorder, int[] inorder, int preStart, int inStart, int inEnd{
    if (preStart > preorder.length - 1 || inStart > inEnd) {
        return null;
    }
    TreeNode root = new TreeNode(preorder[preStart]);
    int index = 0;
    for(int i = inStart; i <= inEnd; i++){
        if(inorder[i] == preorder[preStart]){
            index = i;
        }
    }
    root.left = backtrack(preorder, inorder, preStart + 1, inStart, index - 1);
    root.right = backtrack(preorder, inorder, preStart + index - inStart + 1, index + 1, inEnd);
    return root;
}
```

### 将二叉树展开成链表

[Leetcode - 114 Flatten Binary Tree to Linked List (Medium)](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)


```
Input:
    1
   / \
  2   5
 / \   \
3   4   6
Output:
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```

解法一：递归。

```java
public void flatten(TreeNode root) {
    if(root == null) return;
    flatten(root.left);
    flatten(root.right);
    TreeNode temp = root.right;
    root.right = root.left;
    root.left = null;
    while(root.right != null) root = root.right;
    root.right = temp;
}
```

解法二：遍历。

```java
public void flatten(TreeNode root) {
    while(root != null){
        if(root.left != null){
            TreeNode prev = root.left;
            while(prev.right != null){
                prev = prev.right;
            }
            prev.right = root.right;
            root.right = root.left;
            root.left = null;
        }
        root = root.right;
    }
}
```

### 二叉树路径和 Ⅲ

[Leetcode - 437 Path Sum III (Easy)](https://leetcode.com/problems/path-sum-iii/)

题目描述：找出二叉树中路径和为 sum 的数量，开始结点不需要一定是根节点或叶子节点，但是必须是向下遍历。

```
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1

Return 3. The paths that sum to 8 are:

1.  5 -> 3
2.  5 -> 2 -> 1
3. -3 -> 11
```

```java
public int pathSum(TreeNode root, int sum) {
    if(root == null) return 0;
    return pathSumFrom(root, sum) + pathSum(root.left, sum)
        + pathSum(root.right, sum);
}

public int pathSumFrom(TreeNode root, int sum){
    if(root == null) return 0;
    return (root.val == sum ? 1 : 0) + pathSumFrom(root.left, sum - root.val) 
        + pathSumFrom(root.right, sum - root.val);
}
```

### 合并两棵二叉树

[Leetcode - 617 Merge Two Binary Trees (Easy)](https://leetcode.com/problems/merge-two-binary-trees/)

```
Input: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
Output: 
Merged tree:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7
```

```java
public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
    if(t1 == null && t2 == null) return null;
    int val = (t1 == null ? 0 : t1.val) + (t2 == null ? 0 : t2.val);
    TreeNode root = new TreeNode(val);
    root.left = mergeTrees(t1 != null ? t1.left : null, t2 != null ? t2.left : null);
    root.right = mergeTrees(t1 != null ? t1.right : null, t2 != null ? t2.right : null);
    return root;
}
```
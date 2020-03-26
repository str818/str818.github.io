---
layout: article
title: 算法 — 经典例题
tags: 算法

lang: zh-Hans
key: Algorithm_Classic_Examples
pageview: true
toc: true
show_subscribe: false
---

[课程链接](https://www.bilibili.com/video/av46292575/)

## 一、复杂度

[图片来源](http://www.bigocheatsheet.com/)

<div align="center">  <img src="/img/algorithm_common_data_structure_operations.png" width="100%"/> </div><br>

<div align="center">  <img src="/img/algorithm_array_sorting_algorithms.png" width="80%"/> </div><br>


## 二、数组和链表

### 1. 反转链表

[Leetcode - 206 Reverse Linked List (Easy)](https://leetcode-cn.com/problems/reverse-linked-list/)

反转一个单链表。

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

解法一：遍历

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode cur = head;
        head = head.next;
        cur.next = prev;
        prev = cur;
    }
    return prev;
}
```

解法二：递归

```java
public ListNode reverseList(ListNode head) {
    if(head == null || head.next == null) return head;
    ListNode p = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    return p;
}
```

### 2. 成对交换节点

[Leetcode - 24 Swap Nodes in Pairs (Medium)](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

```
给定 1->2->3->4, 应该返回 2->1->4->3.
```

解法一：遍历

```java
public ListNode swapPairs(ListNode head) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;
    while(prev.next != null && prev.next.next != null){
        ListNode first = prev.next;
        ListNode second = prev.next.next;
        first.next = second.next;
        second.next = first;
        prev.next = second;
        prev = first;
    }
    return dummy.next;
}
```

解法二：递归

```java
public ListNode swapPairs(ListNode head) {
    if(head == null || head.next == null) return head;
    ListNode n = head.next;
    head.next = swapPairs(head.next.next);
    n.next = head;
    return n;
}
```

### 3. 单链表中的环

[Leetcode - 141 Linked List Cycle (Easy)](https://leetcode-cn.com/problems/linked-list-cycle/)

给定一个链表，判断链表中是否有环。

```java
public boolean hasCycle(ListNode head) {
    if(head == null) return false;
    ListNode slow = head, fast = head;
    while(fast.next != null && fast.next.next != null){
        slow = slow.next;
        fast = fast.next.next;
        if(slow.val == fast.val) return true;
    }
    return false;
}
```

### 4. 单链表中的环 II

[Leetcode - 142 Linked List Cycle II (Medium)](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

题目描述：找到环的入口位置。

```java
public ListNode detectCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null){
        fast = fast.next.next;
        slow = slow.next;
        if (fast == slow){
            ListNode slow2 = head; 
            while (slow2 != slow){
                slow = slow.next;
                slow2 = slow2.next;
            }
            return slow;
        }
    }
    return null;
}
```

### 5. 每 K 个一组反转链表

[Leetcode - 25 Reverse Nodes in k-Group (Hard)](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

解法一：遍历

```java
public ListNode reverseKGroup(ListNode head, int k) {
    if(head == null || head.next == null || k < 2) return head;
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy, tail = dummy, temp;
    while(true){
        int count = k;
        while(count > 0 && tail != null){
            count--;
            tail = tail.next;
        }
        if(tail == null) break;
        head = prev.next;
        while(prev.next != tail){
            temp = prev.next;
            prev.next = temp.next;
            temp.next = tail.next;
            tail.next = temp;
        }
        prev = head;
        tail = head;
    }
    return dummy.next;
}
```

解法二：递归

```java
public ListNode reverseKGroup(ListNode head, int k) {
    ListNode curr = head;
    int count = 0;
    while (curr != null && count != k) {
        curr = curr.next;
        count++;
    }
    if (count == k) {
        curr = reverseKGroup(curr, k);
        while (count-- > 0) {
            ListNode tmp = head.next;
            head.next = curr;
            curr = head;
            head = tmp;
        }
        head = curr;
    }
    return head;
}
```

## 三、堆栈和队列

### 1. 有效括号

[Leetcode - 20 Valid Parentheses (Easy)](https://leetcode-cn.com/problems/valid-parentheses/)

```java
public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    for (Character c : s.toCharArray()){
        if(c == '('){
            stack.push(')');
        }else if(c == '['){
            stack.push(']');
        }else if(c == '{'){
            stack.push('}');
        }else if(stack.isEmpty() || stack.pop() != c){
            return false;
        }
    }
    return stack.isEmpty();
}
```

### 2. 使用栈实现队列

[Leetcode - 232 Implement Queue using Stacks (Easy)](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

把第一个入队列的元素放入栈 1，其他的放入栈 2，只有在将队首元素推出之后，才将栈 2 的元素推入栈 1，这样就 peek() 时就不需要交换元素了。

```java
private Stack<Integer> mainStack = new Stack<>();
private Stack<Integer> secStack = new Stack<>();
public MyQueue() {}
public void push(int x) {
    if (mainStack.isEmpty()) {
        mainStack.push(x);
    } else {
        secStack.push(x);
    }
}
public int pop() {
    int pop = mainStack.pop();
    if (mainStack.isEmpty()) {
        while (!secStack.isEmpty()) {
            mainStack.push(secStack.pop());
        }
    }
    return pop;
}
public int peek() {
    return mainStack.peek();
}
public boolean empty() {
    return mainStack.isEmpty();
}
```

### 3. 使用队列实现栈

[Leetcode - 225 Implement Stack using Queues (Easy)](https://leetcode-cn.com/problems/implement-stack-using-queues/)

```java
Queue<Integer> queue;
public MyStack(){
    this.queue=new LinkedList<Integer>();
}
public void push(int x) {
    queue.add(x);
    for(int i=0; i<queue.size()-1; i++){
        queue.add(queue.poll());
    }
}
public int pop() {
    return queue.poll();
}
public int top() {
    return queue.peek();
}
public boolean empty() {
    return queue.isEmpty();
}
```

## 四、优先队列

底层实现方式：堆或二叉搜索树。

### 1. 数据流中第 k 大的元素

[Leetcode - 703 Kth Largest Element in a Stream (Easy)](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)

```java
final PriorityQueue<Integer> q;
final int k;
public KthLargest(int k, int[] nums) {
    this.k = k;
    q = new PriorityQueue<>(k);
    for(int n : nums){
        add(n);
    }
}

public int add(int val) {
    if(q.size() < k){
        q.offer(val);
    }else if(q.peek() < val) {
        q.poll();
        q.offer(val);
    }
    return q.peek();
}
```

### 2. 滑动窗口的最大值

[Leetcode - 239 Sliding Window Maximum (Hard)](https://leetcode-cn.com/problems/sliding-window-maximum/)

```java
public int[] maxSlidingWindow(int[] nums, int k) {
    if(nums == null || k <= 0) return new int[0];
    int[] res = new int[nums.length - k + 1];
    int ri = 0;
    
    Deque<Integer> q = new ArrayDeque<>();
    for(int i = 0; i < nums.length; i++){
        // 删除前面超出滑动窗口的元素
        while(!q.isEmpty() && q.peek() < i - k + 1){
            q.poll();
        }
        // 删除比 i 小的元素
        while(!q.isEmpty() && nums[q.peekLast()] < nums[i]){
            q.pollLast();
        }
        q.offer(i);
        if(i >= k - 1){
            res[ri++] = nums[q.peek()];
        }
    }
    return res;
}
```

## 五、哈希表

### 1. 验证变位词

[Leetcode - 242 Valid Anagram (Easy)](https://leetcode-cn.com/problems/valid-anagram/)

```java
public boolean isAnagram(String s, String t) {
    int[] alphabet = new int[26];
    for (int i = 0; i < s.length(); i++) alphabet[s.charAt(i) - 'a']++;
    for (int i = 0; i < t.length(); i++) alphabet[t.charAt(i) - 'a']--;
    for (int i : alphabet) if (i != 0) return false;
    return true;
}
```

### 2. 两数之和

[Leetcode - 1 Two Sum (Easy)](https://leetcode-cn.com/problems/two-sum/)

```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for(int i = 0; i < nums.length; i++){
        int complement = target - nums[i];
        if(map.containsKey(complement)){
            return new int[]{map.get(complement), i};
        }
        map.put(nums[i], i);
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

### 3. 三数之和

[Leetcode - 15 3Sum (Medium)](https://leetcode-cn.com/problems/3sum/)

使用排序后的双指针代替 Set。

```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> res = new ArrayList<>();
    for(int i = 0; i < nums.length - 2; i++){
        if(i == 0 || (i > 0 && nums[i] != nums[i - 1])){
            int l = i + 1, r = nums.length - 1;
            while(l < r){
                if(nums[i] + nums[l] + nums[r] == 0){
                    res.add(Arrays.asList(nums[i], nums[l], nums[r]));
                    while(l < r && nums[l] == nums[l + 1]) l++;
                    while(l < r && nums[r] == nums[r - 1]) r--;
                    l++; r--;
                }else if(nums[i] + nums[l] + nums[r] < 0){
                    l++;
                }else{
                    r--;
                }
            }
        }
    }
    return res;
}
```

### 4. 四数之和

[Leetcode - 18 4Sum (Medium)](https://leetcode-cn.com/problems/4sum/)

```java
public List<List<Integer>> fourSum(int[] nums, int target) {
    Arrays.sort(nums);
    List<List<Integer>> res = new ArrayList<>();
    for(int i = 0; i < nums.length - 3; i++){
        if(i > 0 && nums[i] == nums[i - 1]) continue;
        for(int j = i + 1; j < nums.length - 2; j++){
            if(j > i + 1 && nums[j] == nums[j - 1]) continue;
            int l = j + 1, r = nums.length - 1;
            while(l < r){
                int sum = nums[i] + nums[j] + nums[l] + nums[r];
                if(sum == target){
                    res.add(Arrays.asList(nums[i], nums[j], nums[l], nums[r]));
                    while(l < r && nums[l] == nums[l + 1]) l++;
                    while(l < r && nums[r] == nums[r - 1]) r--;
                    l++; r--;
                }else if(sum < target){
                    l++;
                }else{
                    r--;
                }
            }
        }
    }
    return res;
}
```

## 六、树

### 1. 验证二叉搜索树

[Leetcode - 98 Validate Binary Search Tree (Medium)](https://leetcode-cn.com/problems/validate-binary-search-tree/)

```java
public boolean isValidBST(TreeNode root) {
    return backtrack(root, Long.MAX_VALUE, Long.MIN_VALUE);
}
public boolean backtrack(TreeNode root, long max, long min){
    if(root == null) return true;
    if(root.val >= max || root.val <= min) return false;
    return backtrack(root.left, root.val, min) && backtrack(root.right, max, root.val); 
}
```

### 2. 二叉树的最小公共祖先

[Leetcode - 236 Lowest Common Ancestor of a Binary Tree (Medium)](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if(root == null || root == p || root == q) return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    return left == null ? right : right == null ? left : root;
}
```

### 3. 二叉搜索树的最小公共祖先

[Leetcode - 235 Lowest Common Ancestor of a Binary Search Tree (Easy)](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

解法一：遍历

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

解法二：递归

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if(root == null || root == p || root == q) return root;
    if(root.val < p.val && root.val < q.val){
        return lowestCommonAncestor(root.right, p, q);
    }else if(root.val > p.val && root.val > q.val){
        return lowestCommonAncestor(root.left, p, q);
    }
    return root;
}
```

### 4. 先序遍历

[Leetcode - 144 Binary Tree Preorder Traversal (Medium)](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

解法一：递归

```java
public List<Integer> preorderTraversal(TreeNode node) {
    List<Integer> res = new LinkedList<Integer>();
    preHelper(res, node);
    return res;
}
public void preHelper(List<Integer> res, TreeNode root){
    if(root == null) return;
    res.add(root.val);
    preHelper(res, root.left);
    preHelper(res, root.right);
}
```

解法二：遍历

不将左子节点放入栈中，而是直接处理。

```java
List<Integer> res = new LinkedList<Integer>();
    if(node == null) return res;
    Stack<TreeNode> stack = new Stack<>();
    stack.push(node);
    while(!stack.isEmpty()){
        TreeNode curr = stack.pop();
        res.add(curr.val);
        if(curr.right != null) stack.push(curr.right);
        if(curr.left != null) stack.push(curr.left);
    }
    return res;
}
```

### 5. 中序遍历

[Leetcode - 94 Binary Tree Inorder Traversal (Medium)](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

解法一：递归

```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<Integer>();
    inHelper(res, root);
    return res;
}
public void inHelper(List<Integer> res, TreeNode root){
    if(root == null) return;
    inHelper(res, root.left);
    res.add(root.val);
    inHelper(res, root.right);
}
```

解法二：遍历

```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<Integer>();
    Stack<TreeNode> stack = new Stack<TreeNode>();
    while(root != null || !stack.isEmpty()){
        while(root != null){
            stack.push(root);
            root = root.left;
        }
        root = stack.pop();
        res.add(root.val);
        root = root.right;
    }
    return res;
}
```

### 6. 后序遍历

[Leetcode - 145 Binary Tree Postorder Traversal (Hard)](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

解法一：递归

```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    postHelper(res, root);
    return res;
}
public void postHelper(List<Integer> res, TreeNode root){
    if(root == null) return;
    postHelper(res, root.left);
    postHelper(res, root.right);
    res.add(root.val);
}
```

解法二：遍历

后序遍历结果是：left -> right -> root，反过来就是 root -> right -> left。

```java
public List<Integer> postorderTraversal(TreeNode root) {
    LinkedList<Integer> res = new LinkedList<>();
    Stack<TreeNode> stack = new Stack<>();
    if (root == null) return res;
    stack.push(root);
    while (!stack.isEmpty()) {
        root = stack.pop();
        res.addFirst(root.val);
        if (root.left != null) {
            stack.push(root.left);
        }
        if (root.right != null) {
            stack.push(root.right);
        }
    }
    return res;
}
```

## 七、递归和分治

### 1. x 的 n 次方

[Leetcode - 50 Pow(x,n) (Medium)](https://leetcode-cn.com/problems/powx-n/)

解法一：递归

```java
public double myPow(double x, int n) {
    if(n == 0)
        return 1;
    if( n < 0 ) {
        if(n == Integer.MIN_VALUE) { // 防止溢出
            ++n;
            n = -n;
            x = 1 / x;
            return x * x * myPow( x * x, n / 2 );
        }
        n = -n;
        x = 1 / x;
    }
    return (n % 2 == 0) ? myPow(x * x, n / 2) : x * myPow(x * x, n / 2);
}
```

解法二：遍历

```java
public double myPow(double x, int n) {
    double res = 1.0;
    for(int i = n; i != 0; i /= 2){
        if(i % 2 != 0) res *= x;
        x *= x;
    }
    return n < 0 ? 1 / res : res;
}
```

### 2. 多数元素

[Leetcode - 169 Majority Element (Easy)](https://leetcode-cn.com/problems/majority-element/submissions/)

题目描述：题目描述：找出数组中出现次数超过 ⌊ n/2 ⌋ 次的元素，假设这个元素一定存在。

解题思路：利用了摩尔投票算法的思想，每次从序列里选择两个不相同的数字删除掉（抵消），最后剩下一个数字或几个相同的数字，就是出现次数大于总数一半的那个。
当然，既然放在了分治的目录下，也可以使用分治的方法解题，解题方法不止一种。

```java
public int majorityElement(int[] nums) {
    int major = nums[0], count = 1;
    for(int i = 1; i < nums.length; i++){
        if(count == 0){
            major = nums[i];
            count++;
        }else if(major == nums[i]){
            count++;
        }else{
            count--;
        }
    }
    return major;
}
```

## 八、贪心算法

### 1. 买卖股票的最佳时间 II

[Leetcode - 122 Best Time to Buy and Sell Stock II (Easy)](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

题目描述：给定每日股票的价格，每日可以进行无数次买卖交易，但最多同时只能持有一只股票。

解题思路：利用贪心的思想，只要第二天股票价格高于当天，就在当天买第二天卖。

```java
public int maxProfit(int[] prices) {
    int profit = 0;
    for(int i = 1; i < prices.length; i++){
        if(prices[i] > prices[i - 1]){
            profit += prices[i] - prices[i - 1];
        }
    }
    return profit;
}
```

## 九、BFS 和 DFS

### 1. 二叉树的层次遍历

[Leetcode - 102 Binary Tree Level Order Traversal (Medium)](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

解法一：BFS

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

解法二：DFS

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

### 2. 二叉树的最大深度

[Leetcode - 104 Maximum Depth of Binary Tree (Easy)](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```java
public int maxDepth(TreeNode root) {
    if(root == null) return 0;
    return Math.max(maxDepth(root.left),maxDepth(root.right)) + 1;
}
```

### 3. 二叉树的最小深度

[Leetcode - 111 Minimum Depth of Binary Tree (Easy)](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

```java
public int minDepth(TreeNode root) {
    if(root == null) return 0;
    int left = minDepth(root.left);
    int right = minDepth(root.right);
    return (left == 0 || right == 0) ? left + right + 1: Math.mi(left,right) + 1;
}
```

### 4. 生成括号

[Leetcode - 22 Generate Parentheses (Medium)](https://leetcode-cn.com/problems/generate-parentheses/)

题目描述：给定整数 n，生成包含 n 个括号合法字符串的所有组合。

```java
public List<String> generateParenthesis(int n) {
    List<String> res = new ArrayList<String>();
    helper(res, "", 0, 0, n);
    return res;
}

public void helper(List<String> res, String curString, int openCount, int closeCount, int max){
    if(curString.length() == 2 * max){
        res.add(curString);
        return;
    }
    
    if(openCount < max){
        helper(res, curString + '(', openCount + 1, closeCount, max);
    }
    
    if(closeCount < openCount){
        helper(res, curString + ')', openCount, closeCount + 1, max);
    }
}
```

## 十、剪枝

### 1. N 皇后

[Leetcode - 51 N-Queens (Hard)](https://leetcode-cn.com/problems/n-queens/)

题目描述：找到 N 皇后的所有情况。

```java
public List<List<String>> solveNQueens(int n) {
    char[][] board = new char[n][n];
    for(int i = 0; i < n; i++){
        for(int j = 0; j < n; j++){
            board[i][j] = '.';
        }
    }
    List<List<String>> res = new ArrayList<>();
    dfs(board, res, 0);
    return res;
}
public void dfs(char[][] board, List<List<String>> res, int index) {
    if(index == board.length){
        List<String> cur = new ArrayList<>();
        for(int i = 0; i < board.length; i++){
            String s = new String(board[i]);
            cur.add(s);
        }
        res.add(cur);
        return;
    }
    for(int i = 0; i < board.length; i++){
        if(validate(board, i, index)){
            board[i][index] = 'Q';
            dfs(board, res, index + 1);
            board[i][index] = '.';
        }
    }
}
public boolean validate(char[][] board, int row, int col) {
    for(int i = 0; i < col ; i++){
        if(board[row][i] == 'Q') return false;
    }
    for(int i = row, j = col; i >= 0 && j >= 0; i--, j--){
        if(board[i][j] == 'Q') return false;
    }
    for(int i = row, j = col; i < board.length && j >= 0; i++, j--){
        if(board[i][j] == 'Q') return false;
    }
    return true;
}
```

### 2. N 皇后 II

[Leetcode - 52 N-Queens II (Hard)](https://leetcode-cn.com/problems/n-queens-ii/)

题目描述：求出 N 皇后的摆放数量。

解题思路：只是求数量的话就不同列出每次的摆放情况了，只需要使用三个数组标记列、正反对象线上是否摆放皇后即可。正对角线上的元素行列序号相加结果相等，反对角线上的元素行列序号相减结果相等。

```java
int count = 0;
public int totalNQueens(int n) {
    boolean[] cols = new boolean[n];     // columns   |
    boolean[] d1 = new boolean[2 * n];   // diagonals \
    boolean[] d2 = new boolean[2 * n];   // diagonals /
    backtracking(0, cols, d1, d2, n);
    return count;
}

public void backtracking(int row, boolean[] cols, boolean[] d1, boolean []d2, int n) {
    if(row == n) count++;

    for(int col = 0; col < n; col++) {
        int id1 = col - row + n;
        int id2 = col + row;
        if(cols[col] || d1[id1] || d2[id2]) continue;
        
        cols[col] = true; d1[id1] = true; d2[id2] = true;
        backtracking(row + 1, cols, d1, d2, n);
        cols[col] = false; d1[id1] = false; d2[id2] = false;
    }
}
```

### 3. 有效数独

[Leetcode - 36 Valid Sudoku (Medium)](https://leetcode-cn.com/problems/valid-sudoku/)

题目描述：给定一个 9 × 9 的不完整数独数组，判定是否合法。

解题思路：判断每一行、每一列和每个 3 × 3 单元格是否有重复的数字。

```java
public boolean isValidSudoku(char[][] board) {
    boolean[][] row = new boolean[9][9];
    boolean[][] col = new boolean[9][9];
    boolean[][] block = new boolean[9][9];
    for(int i = 0; i < 9; i++) {
        for(int j = 0; j < 9; j++) {
            if(board[i][j] != '.') {
                int num = board[i][j] - '1';
                if(row[i][num] || col[j][num] || block[i/3*3 + j/3][num]) {
                    return false;
                } else {
                    row[i][num] = true;
                    col[j][num] = true;
                    block[i/3*3 + j/3][num] = true;
                }
            }
        }
    }
    return true;
}
```

### 4. 求解数独

[Leetcode - 37 Sudoku Solver (Hard)](https://leetcode-cn.com/problems/sudoku-solver/)

```java
public void solveSudoku(char[][] board) {
    dfs(board, 0);
}

public boolean dfs(char[][] b, int n) {
    if (n == 81) return true;
    int x = n / 9;
    int y = n % 9;
    if (b[x][y] != '.') return dfs(b, n + 1);
    for (int i = 0; i < 9; i++) {
        b[x][y] = (char)('1' + i);
        if (validate(b, x, y) && dfs(b, n + 1)) return true;
        b[x][y] = '.';
    }
    return false;
}

public boolean validate(char[][] b, int x, int y) {
    for (int i = 0; i < 9; i++) {
        if (i != x && b[i][y] == b[x][y]) return false;
        if (i != y && b[x][i] == b[x][y]) return false;
    }
    int r = x / 3 * 3;
    int c = y / 3 * 3;
    for (int i = r; i < r + 3; i++) {
        for (int j = c; j < c + 3; j++) {
            if (i == x && j == y) continue;
            if (b[i][j] == b[x][y]) return false;
        }
    }
    return true;
}
```

## 十一、二分查找

### 1. 求开方

[Leetcode - 69 Sqrt(x) (Easy)](https://leetcode-cn.com/problems/sqrtx/)

题目描述：输入一个整数，求该整数的开方，去除结果的小数位，返回整数。

解题思路：测试用例中含有较大的整数，所以要用 x / m 进行比较。

```java
public int mySqrt(int x) {
    if(x <= 1) return x;
    int l = 1, r = x;
    while(l <= r){
        int m = l + (r - l) / 2;
        if(m == x / m){
            return m;
        }else if(m < x / m){
            l = m + 1;
        }else{
            r = m - 1;
        }
    }
    return r;
}
```

## 十二、字典树

### 1. 实现字典树（Trie）

[Leetcode - 208 Implement Trie (Prefix Tree) (Medium)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

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

### 2. 单词搜索 II

[Leetcode - 212 Word Search II (Hard)](https://leetcode-cn.com/problems/word-search-ii/)

题目描述：搜索在字符矩阵中的字符串。

```
Input: 
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath","pea","eat","rain"]

Output: ["eat","oath"]
```

解题思路：先将带查找字符串构建成字典树，再利用回溯法判断矩阵中的字符串是否在字典树中。

```java
public List<String> findWords(char[][] board, String[] words) {
    List<String> res = new ArrayList<>();
    TrieNode root = buildTrie(words);
    for(int i = 0; i < board.length; i++){
        for(int j = 0; j < board[0].length; j++){
            dfs(board, i, j, root, res);
        }
    }
    return res;
}

public void dfs(char[][] board, int i, int j, TrieNode p, List<String> res){
    char c = board[i][j];
    if(c == '#' || p.next[c - 'a'] == null) return;
    p = p.next[c - 'a'];
    if(p.word != null){
        res.add(p.word);
        p.word = null; // 去重
    }
    
    board[i][j] = '#';
    if (i > 0) dfs(board, i - 1, j, p, res); 
    if (j > 0) dfs(board, i, j - 1, p, res);
    if (i < board.length - 1) dfs(board, i + 1, j, p, res); 
    if (j < board[0].length - 1) dfs(board, i, j + 1, p, res); 
    board[i][j] = c;
}

public TrieNode buildTrie(String[] words){
    TrieNode root = new TrieNode();
    for(String s : words){
        TrieNode p = root;
        for(char c : s.toCharArray()){
            int i = c - 'a';
            if(p.next[i] == null) p.next[i] = new TrieNode();
            p = p.next[i];
        }
        p.word = s;
    }
    return root;
}

class TrieNode{
    TrieNode[] next = new TrieNode[26];
    String word;
}
```


---
layout: article
title: 剑指Offer - Java题解 「中」
tags: 剑指Offer

lang: zh-Hans
key: Sword_Refers_To_Offer_Middle
pageview: true
toc: true
show_subscribe: false
---

## 26. 树的子结构

[Online Programming Link](https://www.nowcoder.com/practice/6e196c44c7004d15b1610b9afca8bd88?tpId=13&tqId=11170&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：判断二叉树 B 是不是二叉树 A 的子结构，空树不是任何树的子结构。

```java
public boolean HasSubtree(TreeNode root1, TreeNode root2) {
    if (root1 == null || root2 == null)
        return false;
    return isSubtreeWithRoot(root1, root2) || HasSubtree(root1.left, root2) || HasSubtree(root1.right, root2);
}
private boolean isSubtreeWithRoot(TreeNode root1, TreeNode root2) {
    if (root2 == null) return true;
    if (root1 == null) return false;
    if (root1.val != root2.val) return false;
    return isSubtreeWithRoot(root1.left, root2.left) && isSubtreeWithRoot(root1.right, root2.right);
}
```

## 27. 二叉树的镜像

[Online Programming Link](https://www.nowcoder.com/practice/564f4c26aa584921bc75623e48ca3011?tpId=13&tqId=11171&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public void Mirror(TreeNode root) {
    if (root == null) return;
    TreeNode tmp = root.left;
    root.left = root.right;
    root.right = tmp;
    Mirror(root.left);
    Mirror(root.right);
}
```

## 28. 对称的二叉树

[Online Programming Link](https://www.nowcoder.com/practice/ff05d44dfdb04e1d83bdbdab320efbcb?tpId=13&tqId=11211&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

```java
public boolean isSymmetrical(TreeNode pRoot) {
    if (pRoot == null) return true;
    return isSymmetrical(pRoot.left, pRoot.right);
}
private boolean isSymmetrical(TreeNode root1, TreeNode root2) {
    if (root1 == null && root2 == null)
        return true;
    if (root1 == null || root2 == null)
        return false;
    if (root1.val != root2.val)
        return false;
    return isSymmetrical(root1.left, root2.right) && isSymmetrical(root1.right, root2.left);
}
```

## 29. 顺时针打印矩阵

[Online Programming Link](https://www.nowcoder.com/practice/9b4c81a02cd34f76be2659fa0d54342a?tpId=13&tqId=11172&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public ArrayList<Integer> printMatrix(int [][] matrix) {
    ArrayList<Integer> res = new ArrayList<>();
    int r1 = 0, r2 = matrix.length - 1, c1 = 0, c2 = matrix[0].length - 1;
    while (r1 <= r2 && c1 <= c2) {
        for (int i = c1; i <= c2; i++) {
            res.add(matrix[r1][i]);
        }
        for (int i = r1 + 1; i <= r2; i++) {
            res.add(matrix[i][c2]);
        }
        if (r1 != r2) {
            for (int i = c2 - 1; i >= c1; i--) {
                res.add(matrix[r2][i]);
            }
        }
        if (c1 != c2) {
            for (int i = r2 - 1; i > r1; i--) {
                res.add(matrix[i][c1]);
            }
        }
        r1++; r2--; c1++; c2--;
    }
    return res;
}
```

## 30. 包含 min 函数的栈

[Online Programming Link](https://www.nowcoder.com/practice/4c776177d2c04c2494f2555c9fcc1e49?tpId=13&tqId=11173&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

```java
private Stack<Integer> dataStack = new Stack<>();
private Stack<Integer> minStack = new Stack<>();

public void push(int node) {
    dataStack.push(node);
    minStack.push(minStack.isEmpty() ? node : Math.min(minStack.peek(), node));
}

public void pop() {
    dataStack.pop();
    minStack.pop();
}

public int top() {
    return dataStack.peek();
}

public int min() {
    return minStack.peek();
}
```

## 31. 栈的压入、弹出序列

[Online Programming Link](https://www.nowcoder.com/practice/d77d11405cc7470d82554cb392585106?tpId=13&tqId=11174&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列 1,2,3,4,5 是某栈的压入顺序，序列 4,5,3,2,1 是该压栈序列对应的一个弹出序列，但 4,3,5,1,2 就不可能是该压栈序列的弹出序列。

```java
public boolean IsPopOrder(int[] pushA, int[] popA) {
    int n = pushA.length;
    Stack<Integer> stack = new Stack<>();
    for (int pushIndex = 0, popIndex = 0; pushIndex < n; pushIndex++) {
        stack.push(pushA[pushIndex]);
        while (popIndex < n && !stack.isEmpty() && stack.peek() == popA[popIndex]) {
            stack.pop();
            popIndex++;
        }
    }
    return stack.isEmpty();
}
```

## 32.1 从上往下打印二叉树

[Online Programming Link](https://www.nowcoder.com/practice/7fe2212963db4790b57431d9ed259701?tpId=13&tqId=11175&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：层次遍历。

```java
public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
    ArrayList<Integer> res = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        int cnt = queue.size();
        for (int i = 0; i < cnt; i++) {
            TreeNode t = queue.poll();
            if (t == null) continue;
            res.add(t.val);
            queue.offer(t.left);
            queue.offer(t.right);
        }
    }
    return res;
}
```

## 32.2 把二叉树打印成多行

[Online Programming Link](https://www.nowcoder.com/practice/445c44d982d04483b04a54f298796288?tpId=13&tqId=11213&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    ArrayList<ArrayList<Integer>> ret = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    queue.add(pRoot);
    while (!queue.isEmpty()) {
        ArrayList<Integer> list = new ArrayList<>();
        int cnt = queue.size();
        while (cnt-- > 0) {
            TreeNode node = queue.poll();
            if (node == null)
                continue;
            list.add(node.val);
            queue.add(node.left);
            queue.add(node.right);
        }
        if (list.size() != 0)
            ret.add(list);
    }
    return ret;
}
```

## 32.3 按之字形顺序打印二叉树

[Online Programming Link](https://www.nowcoder.com/practice/91b69814117f4e8097390d107d2efbe0?tpId=13&tqId=11212&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    ArrayList<ArrayList<Integer>> res = new ArrayList<>();
    boolean reverse = false;
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(pRoot);
    while (!queue.isEmpty()) {
        ArrayList<Integer> list = new ArrayList<>();
        int cnt = queue.size();
        while (cnt-- > 0) {
            TreeNode t = queue.poll();
            if (t == null) continue;
            list.add(t.val);
            queue.offer(t.left);
            queue.offer(t.right);
        }
        if (reverse) {
            Collections.reverse(list);
        }
        reverse = !reverse;
        if (list.size() != 0) res.add(list); 
    }
    return res;
}
```

## 33. 二叉搜索树的后续遍历序列

[Online Programming Link](https://www.nowcoder.com/practice/a861533d45854474ac791d90e447bafd?tpId=13&tqId=11176&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。假设输入的数组的任意两个数字都互不相同。

```java
public boolean VerifySquenceOfBST(int[] sequence) {
    if (sequence == null || sequence.length == 0)
        return false;
    return verify(sequence, 0, sequence.length - 1);
}
public boolean verify(int[] nums, int l, int r) {
    if (r - l <= 1)
        return true;
    int root = nums[r];
    int first = l;
    while(first < r && nums[first] < root) {
        first++;
    }
    for (int i = first; i < r; i++) {
        if (nums[i] < root) return false;
    }
    return verify(nums, l, first - 1) && verify(nums, first, r - 1);
}
```

## 34. 二叉树中和为某一值的路径

[Online Programming Link](https://www.nowcoder.com/practice/b736e784e3e34731af99065031301bca?tpId=13&tqId=11177&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

```java
private ArrayList<ArrayList<Integer>> ret = new ArrayList<>();

public ArrayList<ArrayList<Integer>> FindPath(TreeNode root, int target) {
    backtracking(root, target, new ArrayList<>());
    return ret;
}

private void backtracking(TreeNode node, int target, ArrayList<Integer> path) {
    if (node == null)
        return;
    path.add(node.val);
    target -= node.val;
    if (target == 0 && node.left == null && node.right == null) {
        ret.add(new ArrayList<>(path));
    } else {
        backtracking(node.left, target, path);
        backtracking(node.right, target, path);
    }
    path.remove(path.size() - 1);
}
```

## 35. 复杂链表的复制

[Online Programming Link](https://www.nowcoder.com/practice/f836b2c43afc4b35ad6adc41ec941dba?tpId=13&tqId=11178&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的 head。

```java
public RandomListNode Clone(RandomListNode pHead) {
    if (pHead == null)
        return null;
    // 插入新节点
    RandomListNode cur = pHead;
    while (cur != null) {
        RandomListNode clone = new RandomListNode(cur.label);
        clone.next = cur.next;
        cur.next = clone;
        cur = clone.next;
    }
    // 建立 random 链接
    cur = pHead;
    while (cur != null) {
        RandomListNode clone = cur.next;
        if (cur.random != null)
            clone.random = cur.random.next;
        cur = clone.next;
    }
    // 拆分
    cur = pHead;
    RandomListNode pCloneHead = pHead.next;
    while (cur.next != null) {
        RandomListNode next = cur.next;
        cur.next = next.next;
        cur = next;
    }
    return pCloneHead;
}
```

## 36. 二叉树与双向链表

[Online Programming Link](https://www.nowcoder.com/practice/947f6eb80d944a84850b0538bf0ec3a5?tpId=13&tqId=11179&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

解题思路：中序遍历。

```java
private TreeNode pre = null;
private TreeNode head = null;
public TreeNode Convert(TreeNode pRootOfTree) {
    inOrder(pRootOfTree);
    return head;
}
public void inOrder(TreeNode node) {
    if (node == null) 
        return;
    inOrder(node.left);
    node.left = pre;
    if (pre != null) {
        pre.right = node;
    }
    pre = node;
    if (head == null) {
        head = node;
    }
    inOrder(node.right);
}
```

## 37. 序列化二叉树

[Online Programming Link](https://www.nowcoder.com/practice/cf7e25aa97c04cc1a68c8f040e71fb84?tpId=13&tqId=11214&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：请实现两个函数，分别用来序列化和反序列化二叉树。

解题思路：前序遍历。

```java
private String deserializeStr;

public String Serialize(TreeNode root) {
    if (root == null)
        return "#";
    return root.val + " " + Serialize(root.left) + " " + Serialize(root.right);
}

public TreeNode Deserialize(String str) {
    deserializeStr = str;
    return Deserialize();
}

private TreeNode Deserialize() {
    if (deserializeStr.length() == 0)
        return null;
    int index = deserializeStr.indexOf(" ");
    String node = index == -1 ? deserializeStr : deserializeStr.substring(0, index);
    deserializeStr = index == -1 ? "" : deserializeStr.substring(index + 1);
    if (node.equals("#"))
        return null;
    int val = Integer.valueOf(node);
    TreeNode t = new TreeNode(val);
    t.left = Deserialize();
    t.right = Deserialize();
    return t;
}
```

## 38. 字符串的排列

[Online Programming Link](https://www.nowcoder.com/practice/fe6b651b66ae47d7acce78ffdd9a96c7?tpId=13&tqId=11180&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串 abc,则打印出由字符 a,b,c 所能排列出来的所有字符串 abc,acb,bac,bca,cab 和 cba。输入一个字符串,长度不超过9(可能有字符重复),字符只包括大小写字母。

解题思路：注意可能有重复。

```java
private ArrayList<String> res = new ArrayList<>();
public ArrayList<String> Permutation(String str) {
    if (str.length() == 0) 
        return res;
    char[] c = str.toCharArray();
    Arrays.sort(c);
    backtracking(c, new boolean[c.length], new StringBuilder());
    return res;
}

public void backtracking(char[] c, boolean[] hasUsed, StringBuilder s) {
    if (s.length() == c.length) {
        res.add(s.toString());
        return;
    }
    for (int i = 0; i < c.length; i++) {
        if (hasUsed[i])
            continue;
        if (i != 0 && c[i] == c[i - 1] && !hasUsed[i - 1])
            continue;
        hasUsed[i] = true;
        s.append(c[i]);
        backtracking(c, hasUsed, s);
        s.deleteCharAt(s.length() - 1);
        hasUsed[i] = false;
    }
}
```

## 39. 数组中出现次数超过一半的数字

[Online Programming Link](https://www.nowcoder.com/practice/e8a1b01a2df14cb2b228b30ee6a92163?tpId=13&tqId=11181&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。

解题思路：将数组中出现次数超过一半的数字与其他数字抵消。

```java
public int MoreThanHalfNum_Solution(int [] array) {
    if (array == null || array.length == 0)
        return 0;
    int major = array[0];
    for (int i = 1, cnt = 1; i < array.length; i++) {
        if (cnt == 0) {
            major = array[i];
            cnt = 1;
        } else {
            cnt = array[i] == major ? cnt + 1 : cnt - 1;
        }
    }
    
    int cnt = 0;
    for (int x : array) {
        if (x == major)
            cnt++;
    }
    return cnt > array.length / 2 ? major : 0;
}
```

## 40. 最小的 K 个数

[Online Programming Link](https://www.nowcoder.com/practice/6a296eb82cf844ca8539b57c23e6e9bf?tpId=13&tqId=11182&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

解法一：快速选择。

```java
public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
    ArrayList<Integer> res = new ArrayList<>();
    if (k > input.length || k <= 0)
        return res;
    findKthSamllest(input, k - 1);
    for (int i = 0; i < k; i++) {
        res.add(input[i]);
    }
    return res;
}
public void findKthSamllest(int[] nums, int k) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int j = partition(nums, l, h);
        if (j == k)
            break;
        if (j > k)
            h = j - 1;
        else
            l = j + 1;
    }
}
private int partition(int[] nums, int l, int h) {
    int p = nums[l];
    int i = l, j = h + 1;
    while (true) {
        while (i != h && nums[++i] < p);
        while (j != l && nums[--j] > p);
        if (i >= j)
            break;
        swap(nums, i, j);
    }
    swap(nums, l, j);
    return j;
}
private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

解法二：最大堆。

```java
public ArrayList<Integer> GetLeastNumbers_Solution(int [] nums, int k) {
    if (k > nums.length || k < 0)
        return new ArrayList<>();
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>((o1, o2) -> o2 - o1);
    for (int num : nums) {
        maxHeap.add(num);
        if (maxHeap.size() > k)
            maxHeap.poll();
    }
    return new ArrayList<>(maxHeap);
}
```

## 41.1 数据流中的中位数

[Online Programming Link](https://www.nowcoder.com/practice/9be0172896bd43948f8a32fb954e1be1?tpId=13&tqId=11216&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

解题思路：大顶推存储左面的元素，小顶堆存储右面的元素。

```java
private PriorityQueue<Integer> left = new PriorityQueue<>((o1, o2) -> o2 - o1);
private PriorityQueue<Integer> right = new PriorityQueue<>();
private int N = 0;
public void Insert(Integer num) {
    if (N % 2 == 0) {
        left.add(num);
        right.add(left.poll());
    } else {
        right.add(num);
        left.add(right.poll());
    }
    N++;
}
public Double GetMedian() {
    if (N % 2 == 0)
        return (left.peek() + right.peek()) / 2.0;
    else
        return (double) right.peek();
}
```

## 41.2 字符流中第一个不重复的字符

[Online Programming Link](https://www.nowcoder.com/practice/00de97733b8e4f97a3fb5c680ee10720?tpId=13&tqId=11207&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符 "go" 时，第一个只出现一次的字符是 "g"。当从该字符流中读出前六个字符“google" 时，第一个只出现一次的字符是 "l"。

```java
private int[] cnts = new int[256];
private Queue<Character> queue = new LinkedList<>();
public void Insert(char ch) {
    cnts[ch]++;
    queue.offer(ch);
    while (!queue.isEmpty() && cnts[queue.peek()] > 1) {
        queue.poll();
    }
}
public char FirstAppearingOnce() {
    return queue.isEmpty() ? '#' : queue.peek();
}
```

## 42. 连续子数组的最大和

[Online Programming Link](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=11183&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：{6, -3, -2, 7, -15, 1, 2, 2}，连续子数组的最大和为 8（从第 0 个开始，到第 3 个为止）。

解题思路：贪心，舍弃负数。

```java
public int FindGreatestSumOfSubArray(int[] nums) {
    if (nums == null || nums.length == 0)
        return 0;
    int max = Integer.MIN_VALUE;
    int sum = 0;
    for (int num : nums) {
        sum = sum <= 0 ? num : sum + num;
        max = Math.max(max, sum);
    }
    return max;
}
```

## 43. 从 1 到 n 整数中 1 出现的次数

[Online Programming Link](https://www.nowcoder.com/practice/bd7f978302044eee894445e244c7eee6?tpId=13&tqId=11184&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

解题思路：找规律，从最高位计算到最低位。

```java
public int NumberOf1Between1AndN_Solution(int n) {
    int cnt = 0;
    for (int m = 1; m <= n; m *= 10) {
        int a = n / m, b = n % m;
        cnt += (a + 8) / 10 * m + (a % 10 == 1 ? b + 1 : 0);
    }
    return cnt;
}
```

## 45. 把数组排成最小

[Online Programming Link](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

解题思路：可以看成是一个排序问题，在比较两个字符串 S1 和 S2 的大小时，应该比较的是 S1+S2 和 S2+S1 的大小，如果 S1+S2 < S2+S1，那么应该把 S1 排在前面，否则应该把 S2 排在前面。

```java
public String PrintMinNumber(int[] numbers) {
    if (numbers == null || numbers.length == 0)
        return "";
    int n = numbers.length;
    String[] nums = new String[n];
    for (int i = 0; i < n; i++)
        nums[i] = numbers[i] + "";
    Arrays.sort(nums, (s1, s2) -> (s1 + s2).compareTo(s2 + s1));
    String ret = "";
    for (String str : nums)
        ret += str;
    return ret;
}
```

## 46. 把数字翻译成字符串

[Online Programming Link](https://leetcode.com/problems/decode-ways/)

题目描述：给定一个数字，按照如下规则翻译成字符串：1 翻译成“a”，2 翻译成“b”... 26 翻译成“z”。一个数字有多种翻译可能，例如 12258 一共有 5 种，分别是 abbeh，lbeh，aveh，abyh，lyh。实现一个函数，用来计算一个数字有多少种不同的翻译方法。

```java
public int numDecodings(String s) {
    if (s == null || s.length() == 0)
        return 0;
    int n = s.length();
    int[] dp = new int[n + 1];
    dp[0] = 1;
    dp[1] = s.charAt(0) == '0' ? 0 : 1;
    for (int i = 2; i <= n; i++) {
        int one = Integer.valueOf(s.substring(i - 1, i));
        if (one != 0)
            dp[i] += dp[i - 1];
        if (s.charAt(i - 2) == '0')
            continue;
        int two = Integer.valueOf(s.substring(i - 2, i));
        if (two <= 26)
            dp[i] += dp[i - 2];
    }
    return dp[n];
}
```

## 47. 礼物的最大价值

[Online Programming Link](https://www.nowcoder.com/questionTerminal/72a99e28381a407991f2c96d8cb238ab)

题目描述：在一个 m*n 的棋盘的每一个格都放有一个礼物，每个礼物都有一定价值（大于 0）。从左上角开始拿礼物，每次向右或向下移动一格，直到右下角结束。给定一个棋盘，求拿到礼物的最大价值。

```java
public int getMost(int[][] values) {
    if (values == null || values.length == 0 || values[0].length == 0)
        return 0;
    int n = values[0].length;
    int[] dp = new int[n];
    for (int[] value : values) {
        dp[0] += value[0];
        for (int i = 1; i < n; i++)
            dp[i] = Math.max(dp[i], dp[i - 1]) + value[i];
    }
    return dp[n - 1];
}
```
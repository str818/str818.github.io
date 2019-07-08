---
layout: article
title: 剑指Offer - Java题解 「上」
tags: 剑指Offer

lang: zh-Hans
key: Sword_Refers_To_Offer_up
pageview: true
toc: true
show_subscribe: false
---

## 3. 数组中重复的数字

[Online Programming Link](https://www.nowcoder.com/practice/623a5ac0ea5b4e5f95552655361ae0a8?tpId=13&tqId=11203&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

### I. 找出数组中的重复数字

题目描述：在一个长度为 n 的数组里，所有的数字都在 0 ~ n-1 的范围内。数组中某些数字是重复的，找出数组中任意一个重复的数字。

解题思路：首先可以考虑排序，时间复杂度 O(nlogn)；其次哈希表也能够解决，时间复杂度 O(n)，额外空间复杂度 O(n);最优的解法时间复杂度 O(n)，额外空间复杂度 O(1)。

数组中的数字都在 0 ~ n-1 的范围内，如果数组中没有重复的数字，那么当数组排序后数字 i 将都会出现在下标为 i 的位置。如果有重复数字，就不出现不止一个数字 i 出现在下标为 i 的位置，利用这一原理可以求解。

```java
public boolean duplicate(int[] nums, int length,int[] duplication) {
    if (nums == null || length <= 0)
        return false;
    for (int i = 0; i < length; i++) {
        while (nums[i] != i && nums[nums[i]] != nums[i]) {
            swap(nums, i, nums[i]);
        }
        if (nums[i] != i) {
            duplication[0] = nums[i];
            return true;
        }
    }
    return false;
}
private void swap(int[] nums, int a, int b) {
    int tmp = nums[a];
    nums[a] = nums[b];
    nums[b] = tmp;
}
```

### II. 不修改数组找出重复的数字

解题思路：可以开辟一个数组，依次将原数组中的元素放入对应的位置，判断是否重复，额外空间复杂度 O(n)；还有一种额外空间复杂度 O(1) 的方法，利用二分的思想，把从 1 ~ n 的数字从中间的数字 m 分为两部分，前面一半为 1 ~ m，后面一半为 m + 1 ~ n。如果 1 ~ m 的数字数目超过 m，那么这一版的区间一定包含重复的数字。

```java
public int getDuplication(int[] nums, int length){
    if(nums == null || length <= 0) return -1;
    int start = 1;
    int end = length - 1;
    while(end >= start){
        int mid = start + ((end - start) >> 1);
        int count = countRange(nums, length, start, middle);
        if(end == start){
            if(count > 1) return start;
            else break;
        }
        if(count > (middle - start + 1)){
            end = middle;
        }else{
            start = middle;
        }
    }
}

public int countRange(int[] nums, int length, int start, int end){
    if(nums == null) return 0;
    int count = 0;
    for(int i = 0; i < length; i++){
        if(nums[i] >= start && numbers[i] <= end){
            ++count;
        }
    }
    return count;
}
```

## 4. 二维数组中的查找

[Online Programming Link](https://www.nowcoder.com/practice/abc3fe2ce8e146608e868a70efebf62e?tpId=13&tqId=11154&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：一个二维数组的每一行从左到右递增，每一列从上到下递增，判断数组中是否包含一个整数。

解题思路：从右上角开始找。

```java
public boolean Find(int target, int[][] array) {
    if (array == null || array.length == 0 || array[0].length == 0)
        return false;
    int i = 0, j = array[0].length - 1;
    while (i < array.length && j >= 0) {
        if (array[i][j] == target) {
            return true;
        }
        if (array[i][j] > target) {
            j--;
        } else {
            i++;
        }
    }
    return false;
}
```

## 5. 替换空格

[Online Programming Link](https://www.nowcoder.com/practice/4060ac7e3e404ad1a894ef3e17650423?tpId=13&tqId=11155&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：将字符串中的空格替换成"%20"。

解题思路：如果从前向后替换的话，需要不断将后面的字符后移，所以可以先遍历字符串将需要添加的字符数量扩充到字符串的后面，从后向前依次替换。

```java
public String replaceSpace(StringBuffer str) {
    int p1 = str.length() - 1;
    for(int i = 0; i <= p1; i++){
        if(str.charAt(i) == ' '){
            str.append("  ");
        }
    }
    int p2 = str.length() - 1;
    while(p1 >= 0 && p2 > p1){
        char c = str.charAt(p1--);
        if(c == ' '){
            str.setCharAt(p2--, '0');
            str.setCharAt(p2--, '2');
            str.setCharAt(p2--, '%');
        }else{
            str.setCharAt(p2--, c);
        }
    }
    return str.toString();
}
```

## 6. 从尾到头打印链表

[Online Programming Link](https://www.nowcoder.com/practice/d0267f7f55b3412ba93bd35cfa8e8035?tpId=13&tqId=11156&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

解法一：递归

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    ArrayList<Integer> list = new ArrayList<>();
    if (listNode != null) {
        list.addAll(printListFromTailToHead(listNode.next));
        list.add(listNode.val);
    }
    return list;
}
```

解法二：栈。

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    Stack<Integer> stack = new Stack<>();
    while (listNode != null) {
        stack.add(listNode.val);
        listNode = listNode.next;
    }
    ArrayList<Integer> list = new ArrayList<>();
    while (!stack.isEmpty())
        list.add(stack.pop());
    return list;
}
```

## 7. 重建二叉树

[Online Programming Link](https://www.nowcoder.com/practice/8a19cbe657394eeaac2f6ea9b0f6fcf6?tpId=13&tqId=11157&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：给定二叉树的先序与中序遍历，重建二叉树。

解题思路：二叉树先序遍历的第一个结点是根节点，找到其在中序遍历里的位置，二分。

```java
public TreeNode reConstructBinaryTree(int[] pre,int[] in) {
    return reConstructBinaryTree(pre, 0, in, 0, in.length);
}
private TreeNode reConstructBinaryTree(int[] pre, int preIndex, int[] in, int inL, int inR) {
    
    if (preIndex >= pre.length || inL == inR)
        return null;
    
    TreeNode root = new TreeNode(pre[preIndex]);
    for (int i = inL; i < inR; i++) {
        if (in[i] == pre[preIndex]) {
            root.left = reConstructBinaryTree(pre, preIndex + 1, in, inL, i);
            root.right = reConstructBinaryTree(pre, preIndex + i - inL + 1, in, i + 1, inR);
        }
    }
    return root;
}
```

## 8. 二叉树的下一个结点

[Online Programming Link](https://www.nowcoder.com/practice/9023a0c988684a53960365b889ceaf5e?tpId=13&tqId=11210&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

解题思路：如果右结点不为空，则为右子树的最左结点；否则，为当前树的根结点，前提是当前结点为该左子树中的结点。

```java
public TreeLinkNode GetNext(TreeLinkNode pNode) {
    if (pNode.right != null) {
        TreeLinkNode node = pNode.right;
        while (node.left != null) {
            node = node.left;
        }
        return node;
    } else {
        while (pNode.next != null) {
            TreeLinkNode parent = pNode.next;
            if (parent.left == pNode)
                return parent;
            pNode = pNode.next;
        }
    }
    return null;
}
```

## 9. 用两个栈实现队列

[Online Programming Link](https://www.nowcoder.com/practice/54275ddae22f475981afa2244dd448c6?tpId=13&tqId=11158&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
Stack<Integer> in = new Stack<Integer>();
Stack<Integer> out = new Stack<Integer>();

public void push(int node) {
    in.push(node);
}

public int pop() throws Exception {
    if (out.isEmpty())
        while (!in.isEmpty())
            out.push(in.pop());

    if (out.isEmpty())
        throw new Exception("queue is empty");

    return out.pop();
}
```

## 10.1 斐波那契数列

[Online Programming Link](https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=11160&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public int Fibonacci(int n) {
    if (n <= 1) return n;
    int a = 0, b = 1;
    for (int i = 2; i <= n; i++) {
        int tmp = b;
        b = a + b;
        a = tmp;
    }
    return b;
}
```

## 10.2 矩形覆盖

[Online Programming Link](https://www.nowcoder.com/practice/72a5a919508a4251859fb2cfb987a0e6?tpId=13&tqId=11163&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：我们可以用 `2*1` 的小矩形横着或者竖着去覆盖更大的矩形。请问用 n 个 `2*1` 的小矩形无重叠地覆盖一个 `2*n` 的大矩形，总共有多少种方法？

```java
public int RectCover(int target) {
    if (target <= 2) return target;
    int a = 1, b = 2;
    for (int i = 3; i <= target; i++) {
        int tmp = b;
        b += a;
        a = tmp;
    }
    return b;
}
```

## 10.3 跳台阶

[Online Programming Link](https://www.nowcoder.com/practice/8c82a5b80378478f9484d87d1c5f12a4?tpId=13&tqId=11161&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：一只青蛙一次可以跳上 1 级台阶，也可以跳上 2 级。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

```java
public int JumpFloor(int target) {
    if (target <= 2) return target;
    int a = 1, b = 2;
    for (int i = 3; i <= target; i++) {
        int tmp = b;
        b += a;
        a = tmp;
    }
    return b;
}
```

## 10.4 变态跳台阶

[Online Programming Link](https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId=13&tqId=11162&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：一只青蛙一次可以跳上 1 级台阶，也可以跳上 2 级……它也可以跳上 n 级。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

```java
public int JumpFloorII(int target) {
    int[] dp = new int[target];
    Arrays.fill(dp, 1);
    for (int i = 1; i < target; i++) {
        for (int j = 0; j < i; j++) {
            dp[i] += dp[j];
        }
    }
    return dp[target - 1];
}
```

## 11. 旋转数组的最小数字

[Online Programming Link](https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。

```java
public int minNumberInRotateArray(int [] array) {
    int l = 0, r = array.length - 1;
    while (l < r) {
        int mid = l + (r - l) / 2;
        if (array[r] >= array[mid]) {
            r = mid;
        } else {
            l = mid + 1;
        }
    }
    return array[l];
}
```

## 12. 矩阵中的路径

[Online Programming Link](https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向上下左右移动一个格子。如果一条路径经过了矩阵中的某一个格子，则该路径不能再进入该格子。

```java
private static final int[][] next = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
private int cols;
private int rows;
public boolean hasPath(char[] array, int rows, int cols, char[] str) {
    this.cols = cols;
    this.rows = rows;
    boolean[][] marked = new boolean[rows][cols];
    char[][] matrix = buildMatrix(array);
    
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (backtrack(matrix, marked, str, 0, i, j)) return true;
        }
    }
    return false;
}

private boolean backtrack(char[][] matrix, boolean[][] marked, char[] str, int pathLen, int r, int c) {
    
    if (str.length == pathLen) return true;
    if (r < 0 || r >= rows || c < 0 || c >= cols || marked[r][c]
        || matrix[r][c] != str[pathLen]) return false;
    
    marked[r][c] = true;
    for (int i = 0; i < next.length; i++) {
        if (backtrack(matrix, marked, str, pathLen + 1, r + next[i][0], c + next[i][1])) {
            return true;
        }
    }
    marked[r][c] = false;
    return false;
}

private char[][] buildMatrix(char[] array) {
    char[][] matrix = new char[rows][cols];
    for (int i = 0, idx = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            matrix[i][j] = array[idx++];
        }
    }
    return matrix;
}
```

## 13. 机器人的运动范围

[Online Programming Link](https://www.nowcoder.com/practice/6e5207314b5241fb83f2329e89fdecc8?tpId=13&tqId=11219&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：地上有一个 m 行和 n 列的方格。一个机器人从坐标 [0,0] 的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于 k 的格子。 例如，当 k 为 18 时，机器人能够进入方格 [35,37]，因为 3+5+3+7 = 18。但是，它不能进入方格 [35,38]，因为 3+5+3+8 = 19。请问该机器人能够达到多少个格子？

```java
private static final int[][] next = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
private int[][] digitSum;
private int count = 0;
private int rows, cols, threshold;
public int movingCount(int threshold, int rows, int cols) {
    this.rows = rows;
    this.cols = cols;
    this.threshold = threshold;
    initStatus(rows, cols);
    boolean[][] marked = new boolean[rows][cols];
    dfs(marked, 0, 0);
    return count;
}

private void dfs(boolean[][] marked, int r, int c) {
    if (r < 0 || r >= rows || c < 0 || c >= cols || marked[r][c]) return;
    
    marked[r][c] = true;
    if (digitSum[r][c] > threshold) return;
        count++;
    for (int[] n : next) {
        dfs(marked, r + n[0], c + n[1]);
    }
}

private void initStatus(int rows, int cols) {
    int n = Math.max(rows, cols);
    int[] digitSum = new int[n];
    for (int i = 0; i < n; i++) {
        int tmp = i;
        while (tmp != 0) {
            digitSum[i] += tmp % 10;
            tmp /= 10;
        }
    }
    
    this.digitSum = new int[rows][cols];
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            this.digitSum[i][j] = digitSum[i] + digitSum[j];
        }
    }
}
```

## 14. 剪绳子

[Online Programming Link](https://leetcode.com/problems/integer-break)

题目描述：把一根绳子剪成多段，并且使得每段的长度乘积最大。

```java
public int integerBreak(int n) {
    int[] dp = new int[n + 1];
    dp[1] = 1;
    for (int i = 2; i <= n; i++)
        for (int j = 1; j < i; j++)
            dp[i] = Math.max(dp[i], Math.max(j * (i - j), dp[j] * (i - j)));
    return dp[n];
}
```

## 15. 二进制中 1 的个数

[Online Programming Link](https://www.nowcoder.com/practice/8ee967e43c2c4ec193b040ea7fbb10b8?tpId=13&tqId=11164&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

```java
public int NumberOf1(int n) {
    int cnt = 0;
    while (n != 0) {
        cnt++;
        n &= (n - 1);
    }
    return cnt;
}
```

## 16. 数值的整数次方 

[Online Programming Link](https://www.nowcoder.com/practice/8ee967e43c2c4ec193b040ea7fbb10b8?tpId=13&tqId=11164&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：给定一个 double 类型的浮点数 base 和 int 类型的整数 exponent，求 base 的 exponent 次方。

```java
public double Power(double base, int exponent) {
    if (exponent == 1) return base;
    if (exponent == 0) return 1;
    boolean isNegative = false;
    if (exponent < 0) {
        isNegative = true;
        exponent = -exponent;
    }
    double pow = Power(base * base, exponent / 2);
    if (exponent % 2 == 1) {
        pow *= base;
    }
    return isNegative ? 1 / pow : pow;
}
```

## 17. 打印从 1 到最大的 n 位数

题目描述：输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数即 999。

```java
public void print1ToMaxOfNDigits(int n) {
    if (n <= 0)
        return;
    char[] number = new char[n];
    print1ToMaxOfNDigits(number, 0);
}

private void print1ToMaxOfNDigits(char[] number, int digit) {
    if (digit == number.length) {
        printNumber(number);
        return;
    }
    for (int i = 0; i < 10; i++) {
        number[digit] = (char) (i + '0');
        print1ToMaxOfNDigits(number, digit + 1);
    }
}

private void printNumber(char[] number) {
    int index = 0;
    while (index < number.length && number[index] == '0')
        index++;
    while (index < number.length)
        System.out.print(number[index++]);
    System.out.println();
}
```

## 18.1 在 O(1) 时间内删除链表节点

解题思路：如果该节点不是尾节点，那么可以直接将下一个节点的值赋给该节点，然后令该节点指向下下个节点，再删除下一个节点，时间复杂度为 O(1)；否则，就需要先遍历链表，找到节点的前一个节点，然后让前一个节点指向 null，时间复杂度为 O(N)。

```java
public ListNode deleteNode(ListNode head, ListNode tobeDelete) {
    if (head == null || tobeDelete == null)
        return null;
    if (tobeDelete.next != null) {
        // 要删除的节点不是尾节点
        ListNode next = tobeDelete.next;
        tobeDelete.val = next.val;
        tobeDelete.next = next.next;
    } else {
        if (head == tobeDelete)
            // 只有一个节点
            head = null;
        else {
            ListNode cur = head;
            while (cur.next != tobeDelete)
                cur = cur.next;
            cur.next = null;
        }
    }
    return head;
}
```

## 18.2 删除链表中重复的结点

[Online Programming Link](https://www.nowcoder.com/practice/fc533c45b73a41b0b44ccba763f866ef?tpId=13&tqId=11209&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public ListNode deleteDuplication(ListNode pHead) {
    if (pHead == null || pHead.next == null)
        return pHead;
    ListNode next = pHead.next;
    if (pHead.val == next.val) {
        while (next != null && pHead.val == next.val)
            next = next.next;
        return deleteDuplication(next);
    } else {
        pHead.next = deleteDuplication(pHead.next);
        return pHead;
    }
}
```

## 19. 正则表达式匹配

[Online Programming Link](https://www.nowcoder.com/practice/45327ae22b7b413ea21df13ee7d6429c?tpId=13&tqId=11205&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：请实现一个函数用来匹配包括 `.` 和 `*` 的正则表达式。模式中的字符 `.` 表示任意一个字符，而 `*` 表示它前面的字符可以出现任意次（包含 0 次）。

```java
public boolean match(char[] str, char[] pattern) {
    int m = str.length, n = pattern.length;
    boolean[][] dp = new boolean[m + 1][n + 1];
    
    dp[0][0] = true;
    for (int i = 1; i <= n; i++) {
        if (pattern[i - 1] == '*') {
            dp[0][i] = dp[0][i - 2];
        }
    }
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (str[i - 1] == pattern[j - 1] || pattern[j - 1] == '.') {
                dp[i][j] = dp[i - 1][j - 1];
            } else if (pattern[j - 1] == '*') {
                if (pattern[j - 2] == str[i - 1] || pattern[j - 2] == '.') {
                    dp[i][j] |= dp[i][j - 1]; // a* counts as single a
                    dp[i][j] |= dp[i - 1][j]; // a* counts as multiple a
                    dp[i][j] |= dp[i][j - 2]; // a* counts as empty
                } else {
                    dp[i][j] = dp[i][j - 2]; // a* only counts as empty
                }
            }
        }
    }
    return dp[m][n];
}
```

## 20. 表示数值的字符串

[Online Programming Link](https://www.nowcoder.com/practice/6f8c901d091949a5837e24bb82a731f2?tpId=13&tqId=11206&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。

```java
public boolean isNumeric(char[] str) {
    if (str == null || str.length == 0) return false;
    return new String(str).matches("[+-]?\\d*(\\.\\d+)?([eE][+-]?\\d+)?");
}
```

## 21. 调整数组顺序使奇数位于偶数前面

[Online Programming Link](https://www.nowcoder.com/practice/beb5aa231adc45b2a5dcc5b62c93f593?tpId=13&tqId=11166&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：需要保证奇数和奇数，偶数和偶数之间的相对位置不变。

```java
public void reOrderArray(int[] nums) {
    int oddCount = 0;
    for (int x : nums) {
        if (x % 2 == 1) oddCount++;
    }
    int[] copy = nums.clone();
    int i = 0, j = oddCount;
    for (int x : copy) {
        if (x % 2 == 1) nums[i++] = x;
        else nums[j++] = x;
    }
}
```

## 22. 链表中倒数第 K 个结点

[Online Programming Link](https://www.nowcoder.com/practice/529d3ae5a407492994ad2a246518148a?tpId=13&tqId=11167&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public ListNode FindKthToTail(ListNode head, int k) {
    if (head == null) return null;
    ListNode p1 = head;
    while (p1 != null && k-- > 0) {
        p1 = p1.next;
    }
    if (k > 0) return null;
    ListNode p2 = head;
    while (p1 != null) {
        p1 = p1.next;
        p2 = p2.next;
    }
    return p2;
}
```

## 23. 链表中环的入口结点

[Online Programming Link](https://www.nowcoder.com/practice/253d2c59ec3e4bc68da16833f79a38e4?tpId=13&tqId=11208&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public ListNode EntryNodeOfLoop(ListNode pHead) {
    if (pHead == null || pHead.next == null)
        return null;
    ListNode slow = pHead, fast = pHead;
    do {
        fast = fast.next.next;
        slow = slow.next;
    } while (slow != fast);
    fast = pHead;
    while (slow != fast) {
        slow = slow.next;
        fast = fast.next;
    }
    return slow;
}
```

## 24. 反转链表

[Online Programming Link](https://www.nowcoder.com/practice/75e878df47f24fdc9dc3e400ec6058ca?tpId=13&tqId=11168&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

方法一：递归

```java
public ListNode ReverseList(ListNode head) {
    if (head == null || head.next == null) return head;
    ListNode next = head.next;
    head.next = null;
    ListNode newHead = ReverseList(next);
    next.next = head;
    return newHead;
}
```

方法二：头插法

```java
public ListNode ReverseList(ListNode head) {
    ListNode newList = new ListNode(-1);
    while (head != null) {
        ListNode next = head.next;
        head.next = newList.next;
        newList.next = head;
        head = next;
    }
    return newList.next;
}
```

## 25. 合并两个排序的链表

[Online Programming Link](https://www.nowcoder.com/practice/d8b6b4358f774294a89de2a6ac4d9337?tpId=13&tqId=11169&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```java
public ListNode Merge(ListNode list1,ListNode list2) {
    if (list1 == null) return list2;
    if (list2 == null) return list1;
    if (list1.val > list2.val) {
        list2.next = Merge(list1, list2.next);
        return list2;
    } else {
        list1.next = Merge(list1.next, list2);
        return list1;
    }
}
```


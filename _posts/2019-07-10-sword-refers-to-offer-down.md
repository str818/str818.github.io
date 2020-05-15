---
layout: article
title: 剑指Offer - Java题解 「下」
tags: 剑指Offer

lang: zh-Hans
key: Sword_Refers_To_Offer_Down
pageview: true
toc: true
show_subscribe: false
---

## 48. 最长不含重复字符的子字符串

[Code It Now!!!](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)

**题目描述**：请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。

```
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**解题思路**：滑动窗口，Map 记录字符出现的位置，判断是否重复。

```java
public int lengthOfLongestSubstring(String s) {
    int n = s.length(), ans = 0;
    Map<Character, Integer> map = new HashMap<>();
    for (int end = 0, start = 0; end < n; end++) {
        char alpha = s.charAt(end);
        if (map.containsKey(alpha)) {
            start = Math.max(map.get(alpha), start);
        }
        ans = Math.max(ans, end - start + 1);
        map.put(s.charAt(end), end + 1);
    }
    return ans;
}
```

## 49. 丑数

[Code It Now!!!](https://leetcode-cn.com/problems/chou-shu-lcof/)

**题目描述**：我们把只包含因子 `2`、`3` 和 `5` 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 `n` 个丑数。

```
输入: n = 10
输出: 12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
```

**解题思路**：动态规划。由于丑数只包含因子 `2、3、5`，因此较大的丑数一定能够通过 某较小丑数 × 某因子 得到，所以丑数 `p = 2 ^ x * 3 ^ y * 5 ^ z`，但是这样是无序的，所以使用 a、b 和 c 维护三个有序队列，使结果有序。三个队列最小的数可能是重复的，比如 `2 * 3 = 6` 和 `3 * 2 = 6`，`6` 可能出现两次。

```java
public int nthUglyNumber(int n) {
    int a = 0, b = 0, c = 0;
    int[] dp = new int[n];
    dp[0] = 1;
    for(int i = 1; i < n; i++) {
        int n2 = dp[a] * 2, n3 = dp[b] * 3, n5 = dp[c] * 5;
        dp[i] = Math.min(Math.min(n2, n3), n5);
        if(dp[i] == n2) a++;
        if(dp[i] == n3) b++;
        if(dp[i] == n5) c++;
    }
    return dp[n - 1];
}
```

## 50. 第一个只出现一次的字符位置

[Code It Now!!!](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

**题目描述**：在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。

```
s = "abaccdeff"
返回 "b"
```

```java
public char firstUniqChar(String s) {
    int[] cnts = new int[256];
    for (int i = 0; i < s.length(); i++) {
        cnts[s.charAt(i)]++;
    }
    for (int i = 0; i < s.length(); i++) {
        if (cnts[s.charAt(i)] == 1)
            return s.charAt(i);
    }
    return ' ';
}
```

## 51. 数组中的逆序对

[Code It Now!!!](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

**题目描述**：在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

```
输入: [7,5,6,4]
输出: 5
```

**解题思路**：归并排序。

- 归并排序先将数组不断二分成左右两边各 1 个元素，判断这两个元素是否构成逆序对，并将这两个元素合并成一个有序的数组，归并后的数组因为是递增排序的，所以不再包含逆序对
- 之后比较长度为 2 的两个数组，这两个数组内部没有逆序对了，所以只有在合并两个数组的过程中包含逆序对

```java
int cnt = 0;
int[] tmp;
public int reversePairs(int[] nums) {
    tmp = new int[nums.length];
    mergeSort(nums, 0, nums.length - 1);
    return cnt;
}

public void mergeSort(int[] nums, int l, int h) {
    if (h <= l) return;
    int mid = l + (h - l) / 2;
    mergeSort(nums, l, mid);
    mergeSort(nums, mid + 1, h);
    merge(nums, l, mid, h);
}

public void merge(int[] nums, int l, int m, int h) {
    int i = l, j = m + 1, k = l;
    while (i <= m || j <= h) {
        if (i > m) {
            tmp[k++] = nums[j++];
        } else if (j > h) {
            tmp[k++] = nums[i++];
        } else if (nums[i] <= nums[j]) {
            tmp[k++] = nums[i++];
        } else {
            tmp[k++] = nums[j++];
            cnt += m - i + 1;
        }
    }
    for (k = l; k <= h; k++) {
        nums[k] = tmp[k];
    }
}
```

## 52. 两个链表的第一个公共结点

[Code It Now!!!](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

**题目描述**：输入两个链表，找出它们的第一个公共节点。

**解题思路**：设 `A` 的长度为 `a + c`，`B` 的长度为 `b + c`，其中 `c` 为尾部公共部分长度，可知 `a + c + b = b + c + a`。

使用两个指针 `p1`、`p2` 分别指向两个链表 `headA`、`headB` 的头结点，然后同时分别逐结点遍历，当 `p1` 到达链表 `headA` 的末尾时，重新定位到链表 `headB` 的头结点；当 `p2` 到达链表 `headB` 的末尾时，重新定位到链表 `headA` 的头结点。

这样，当它们相遇时，所指向的结点就是第一个公共结点。

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    ListNode p1 = headA, p2 = headB;
    while (p1 != p2) {
        p1 = p1 == null ? headB : p1.next;
        p2 = p2 == null ? headA : p2.next; 
    }
    return p1;
}
```

## 53.1 在排序数组中查找数字

[Code It Now!!!](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

**题目描述：**统计一个数字在排序数组中出现的次数。

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

**解题思路：**二分查找，分别找到第一个 `target` 和最后一个 `target` 右面的位置，两个位置相减即为 `target` 出现的次数。

```java
public int search(int[] nums, int target) {
    return helper(nums, target) - helper(nums, target - 1);
}

public int helper(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (target >= nums[mid]) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return left;
}
```

## 53.2 0～n-1中缺失的数字

[Code It Now!!!](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)

**题目描述：**一个长度为 `n-1` 的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围 `0～n-1` 之内。在范围 `0～n-1` 内的 `n` 个数字中有且只有一个数字不在该数组中，请找出这个数字。

```
输入: [0,1,2,3,4,5,6,7,9]
输出: 8
```

**解题思路：**二分查找，可以将数组划分为两部分，左半部分 `nums[i] = i` ，右半部分 `nums[i] != i` ，那么分隔点的下标即为缺失的数字。

```java
public int missingNumber(int[] nums) {
    int l = 0, r = nums.length - 1;
    while (l <= r) {
        int mid = l + (r - l) / 2;
        if (nums[mid] == mid) {
            l = mid + 1;
        } else {
            r = mid - 1;
        }
    }
    return l;
}
```

## 54. 二叉搜索树的第 K 大节点

[Code It Now!!!](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

**题目描述：**给定一棵二叉搜索树，请找出其中第 `k` 大的节点。

```
输入: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
输出: 4
```

**解题思路：**二叉树中序遍历，注意是第 K 大的节点，先去遍历右面的节点。

```java
int res, k;
public int kthLargest(TreeNode root, int k) {
    this.k = k;
    helper(root);
    return res;
}

public void helper(TreeNode root) {
    if (root == null) return;
    helper(root.right);
    if (k == 0) return;
    if (--k == 0) res = root.val;
    helper(root.left);
}
```

## 55.1 二叉树的深度

[Code It Now!!!](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

**题目描述：**输入一棵二叉树的根节点，求该树的深度。

```java
public int maxDepth(TreeNode root) {
    return root == null ? 0 : 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

## 55.2 平衡二叉树

[Code It Now!!!](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

**题目描述：**判断是否是平衡二叉树，高度差小于一。

```
给定二叉树：
			 1
      / \
     2   2
    / \
   3   3
  / \
 4   4
返回 false
```

**解题思路：**后序遍历 + 剪枝。

```java
public boolean isBalanced(TreeNode root) {
    if(root == null) return true;
    return recur(root) != -1;
}

int recur(TreeNode root){
    if(root == null) return 0;
    int left = recur(root.left);
    if(left == -1) return -1;
    int right = recur(root.right);
    if(right == -1) return -1;
    return Math.abs(left - right) <= 1 ? Math.max(left, right) + 1 : -1;
}
```

## 56.1 数组中数字出现的次数

[Code It Now!!!](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

**题目描述：**一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

**解题思路：**用异或元素抵消掉所有出现两次的数字，得到只出现一次的两个数字的异或结果，通过 `diff ^= -diff` 取出从右数第一个不为 0 的位置，按照这个位置是 0 还是 1 划分成两个不同的部分，两个部分分别异或可求出最终结果。

```java
public int[] singleNumbers(int[] nums) {
    int diff = 0;
    for (int num : nums) {
        diff ^= num;
    }
    diff &= -diff;
    int[] res = new int[2];
    for (int num : nums) {
        if ((num & diff) == 0) {
            res[0] ^= num;
        } else {
            res[1] ^= num;
        }
    }
    return res;
}
```

## 56.2 数组中数字出现的次数II

[Code It Now!!!](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

**题目描述：**在一个数组 `nums` 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

**解题思路：**对于出现三次的数字，各二进制位 出现的次数都是 3 的倍数。因此，统计所有数字的各二进制位中 11 的出现次数，并对 3 求余，结果则为只出现一次的数字。

```java
public int singleNumber(int[] nums) {
    int[] counts = new int[32];
    for(int num : nums) {
        for(int j = 0; j < 32; j++) {
            counts[j] += num & 1;
            num >>>= 1;
        }
    }
    int res = 0, m = 3;
    for(int i = 0; i < 32; i++) {
        res <<= 1;
        res |= counts[31 - i] % m;
    }
    return res;
}
```

## 57.1 和为 S 的两个数字

[Online Programming Link](https://www.nowcoder.com/practice/390da4f7a00f44bea7c2f3d19491311b?tpId=13&tqId=11195&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输入一个递增排序的数组和一个数字 S，在数组中查找两个数，使得他们的和正好是 S。如果有多对数字的和等于 S，输出两个数的乘积最小的。

```java
public ArrayList<Integer> FindNumbersWithSum(int[] array, int sum) {
    int i = 0, j = array.length - 1;
    while (i < j) {
        int cur = array[i] + array[j];
        if (cur == sum)
            return new ArrayList<>(Arrays.asList(array[i], array[j]));
        if (cur < sum)
            i++;
        else
            j--;
    }
    return new ArrayList<>();
}
```

## 57.2 和为 S 的连续正数序列

[Online Programming Link](https://www.nowcoder.com/practice/c451a3fd84b64cb19485dad758a55ebe?tpId=13&tqId=11194&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：输出所有和为 S 的连续正数序列。

```java
public ArrayList<ArrayList<Integer> > FindContinuousSequence(int sum) {
    ArrayList<ArrayList<Integer>> ret = new ArrayList<>();
    int start = 1, end = 2;
    int curSum = 3;
    while (end < sum) {
        if (curSum > sum) {
            curSum -= start;
            start++;
        } else if (curSum < sum) {
            end++;
            curSum += end;
        } else {
            ArrayList<Integer> list = new ArrayList<>();
            for (int i = start; i <= end; i++)
                list.add(i);
            ret.add(list);
            curSum -= start;
            start++;
            end++;
            curSum += end;
        }
    }
    return ret;
}
```

## 58.1 翻转单词顺序列

[Online Programming Link](https://www.nowcoder.com/practice/3194a4f4cf814f63919d0790578d51f3?tpId=13&tqId=11197&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：

```
Input:
"I am a student."

Output:
"student. a am I"
```

解题思路：先旋转每个单词，再旋转整个字符串。

```java
public String ReverseSentence(String str) {
    int n = str.length();
    char[] chars = str.toCharArray();
    int i = 0, j = 0;
    while (j <= n) {
        if (j == n || chars[j] == ' ') {
            reverse(chars, i, j - 1);
            i = j + 1;
        }
        j++;
    }
    reverse(chars, 0, n - 1);
    return new String(chars);
}

private void reverse(char[] c, int i, int j) {
    while (i < j)
        swap(c, i++, j--);
}

private void swap(char[] c, int i, int j) {
    char t = c[i];
    c[i] = c[j];
    c[j] = t;
}
```

## 58.2 左旋转字符串

[Online Programming Link](https://www.nowcoder.com/practice/12d959b108cb42b1ab72cef4d36af5ec?tpId=13&tqId=11196&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：

```
Input:
S="abcXYZdef"
K=3

Output:
"XYZdefabc"
```

解题思路：先将 "abc" 和 "XYZdef" 分别翻转，得到 "cbafedZYX"，然后再把整个字符串翻转得到 "XYZdefabc"。

```java
public String LeftRotateString(String str, int n) {
    if (n >= str.length())
        return str;
    char[] chars = str.toCharArray();
    reverse(chars, 0, n - 1);
    reverse(chars, n, chars.length - 1);
    reverse(chars, 0, chars.length - 1);
    return new String(chars);
}

private void reverse(char[] chars, int i, int j) {
    while (i < j)
        swap(chars, i++, j--);
}

private void swap(char[] chars, int i, int j) {
    char t = chars[i];
    chars[i] = chars[j];
    chars[j] = t;
}
```

## 59. 滑动窗口的最大值

[Online Programming Link](https://www.nowcoder.com/practice/1624bc35a45c42c0bc17d17fa0cba788?tpId=13&tqId=11217&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。

```java
public ArrayList<Integer> maxInWindows(int[] num, int size) {
    ArrayList<Integer> ret = new ArrayList<>();
    if (size > num.length || size < 1)
        return ret;
    PriorityQueue<Integer> heap = new PriorityQueue<>((o1, o2) -> o2 - o1);  /* 大顶堆 */
    for (int i = 0; i < size; i++)
        heap.add(num[i]);
    ret.add(heap.peek());
    for (int i = 0, j = i + size; j < num.length; i++, j++) { /* 维护一个大小为 size 的大顶堆 */
        heap.remove(num[i]);
        heap.add(num[j]);
        ret.add(heap.peek());
    }
    return ret;
}
```

## 60. n 个骰子的点数

[Online Programming Link](https://www.lintcode.com/problem/dices-sum/description)

题目描述：把 n 个骰子仍在地上，求点数和为 s 的概率。

解题思路：定义 `dp[i][j]` 为前 i 个骰子产生点数 j 的次数。

状态转移方程为：`dp[i][j] = dp(i-1,j-1) + dp(i-1,j-2) + dp(i-1,j-3) + dp(i-1,j-4) + dp(i-1,j-5)+dp(c-1,k-6)`。

```java
public List<Map.Entry<Integer, Double>> dicesSum(int n) {
    final int face = 6;
    final int pointNum = face * n;
    long[][] dp = new long[n + 1][pointNum + 1];

    for (int i = 1; i <= face; i++)
        dp[1][i] = 1;

    for (int i = 2; i <= n; i++)
        for (int j = i; j <= pointNum; j++)     /* 使用 i 个骰子最小点数为 i */
            for (int k = 1; k <= face && k <= j; k++)
                dp[i][j] += dp[i - 1][j - k];

    final double totalNum = Math.pow(6, n);
    List<Map.Entry<Integer, Double>> ret = new ArrayList<>();
    for (int i = n; i <= pointNum; i++)
        ret.add(new AbstractMap.SimpleEntry<>(i, dp[n][i] / totalNum));

    return ret;
}
```

## 61. 扑克牌顺子

[Online Programming Link](https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=11198&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：五张牌，其中大小鬼为癞子，牌面为 0，判断这五张牌是否能组成顺子。

解题思路：必须要满足：(1) 没有重复的牌 (2) max - min < 5

```java
public boolean isContinuous(int[] nums) {
    if (nums == null || nums.length == 0) {
        return false;
    }
    int max = -1, min = 14;
    int[] d = new int[14];
    for (int num : nums) {
        d[num]++;
        if (num == 0) continue;
        if (d[num] > 1) return false;
        if (num > max) max = num;
        if (num < min) min = num;
    }
    return max - min < 5;
}
```

## 62. 圆圈中最后剩下的数 {X}

[Online Programming Link](https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：让小朋友们围成一个大圈。然后，随机指定一个数 m，让编号为 0 的小朋友开始报数。每次喊到 m-1 的那个小朋友要出列唱首歌，然后可以在礼品箱中任意的挑选礼物，并且不再回到圈中，从他的下一个小朋友开始，继续 0...m-1 报数 .... 这样下去 .... 直到剩下最后一个小朋友，可以不用表演。

解题思路：约瑟夫环，圆圈长度为 n 的解可以看成长度为 n-1 的解再加上报数的长度 m。因为是圆圈，所以最后需要对 n 取余。

```java
public int LastRemaining_Solution(int n, int m) {
    if (n == 0)     /* 特殊输入的处理 */
        return -1;
    if (n == 1)     /* 递归返回条件 */
        return 0;
    return (LastRemaining_Solution(n - 1, m) + m) % n;
}
```

## 63. 股票的最大利润

[Online Programming Link](https://leetcode.com/problems/best-time-to-buy-and-sell-stock)

题目描述：可以有一次买入和一次卖出，买入必须在前。求最大收益。

```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0)
        return 0;
    int soFarMin = prices[0];
    int maxProfit = 0;
    for (int i = 1; i < prices.length; i++) {
        soFarMin = Math.min(soFarMin, prices[i]);
        maxProfit = Math.max(maxProfit, prices[i] - soFarMin);
    }
    return maxProfit;
}
```

## 64. 求 1+2+3+...+n

[Online Programming Link](https://www.nowcoder.com/practice/7a0da8fc483247ff8800059e12d7caf1?tpId=13&tqId=11200&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：要求不能使用乘除法、for、while、if、else、switch、case 等关键字及条件判断语句 A ? B : C。

```java
public int Sum_Solution(int n) {
    int sum = n;
    boolean b = (n > 0) && ((sum += Sum_Solution(n - 1)) > 0);
    return sum;
}
```

## 65. 不用加减乘除做加法

[Online Programming Link](https://www.nowcoder.com/practice/59ac416b4b944300b617d4f7f111b215?tpId=13&tqId=11201&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：写一个函数，求两个整数之和，要求不得使用 +、-、*、/ 四则运算符号。

解题思路：a ^ b 表示没有考虑进位的情况下两数的和，(a & b) << 1 就是进位。

递归会终止的原因是 (a & b) << 1 最右边会多一个 0，那么继续递归，进位最右边的 0 会慢慢增多，最后进位会变为 0，递归终止。

```java
public int Add(int a,int b) {
    return b == 0 ? a : Add(a ^ b, (a & b) << 1);
}
```

## 66. 构建乘积数组

[Online Programming Link](https://www.nowcoder.com/practice/94a4d381a68b47b7a8bed86f2975db46?tpId=13&tqId=11204&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：给定一个数组 A[0, 1,..., n-1]，请构建一个数组 B[0, 1,..., n-1]，其中 B 中的元素 B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。要求不能使用除法。

```java
public int[] multiply(int[] A) {
    int n = A.length;
    int[] B = new int[n];
    for (int i = 0, product = 1; i < n; product *= A[i], i++)       /* 从左往右累乘 */
        B[i] = product;
    for (int i = n - 1, product = 1; i >= 0; product *= A[i], i--)  /* 从右往左累乘 */
        B[i] *= product;
    return B;
}
```

## 67. 把字符串转换成整数

[Online Programming Link](https://www.nowcoder.com/practice/1277c681251b4372bdef344468e4f26e?tpId=13&tqId=11202&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

题目描述：将一个字符串转换成一个整数，字符串不是一个合法的数值则返回 0，要求不能使用字符串转换整数的库函数。

```java
public int StrToInt(String str) {
    if (str == null || str.length() == 0)
        return 0;
    boolean isNegative = str.charAt(0) == '-';
    int ret = 0;
    for (int i = 0; i < str.length(); i++) {
        char c = str.charAt(i);
        if (i == 0 && (c == '+' || c == '-'))  /* 符号判定 */
            continue;
        if (c < '0' || c > '9')                /* 非法输入 */
            return 0;
        ret = ret * 10 + (c - '0');
    }
    return isNegative ? -ret : ret;
}
```

## 68. 树中两个节点的最低公共祖先

#### 1. 二叉查找树

[Leetcode : 235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree)

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null)
        return root;
    if (root.val > p.val && root.val > q.val)
        return lowestCommonAncestor(root.left, p, q);
    if (root.val < p.val && root.val < q.val)
        return lowestCommonAncestor(root.right, p, q);
    return root;
}
```

#### 2. 普通二叉树

[Leetcode : 236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/)

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q)
        return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    return left == null ? right : right == null ? left : root;
}
```
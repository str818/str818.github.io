---
layout: article
title: Leetcode —「搜索」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Searching
pageview: true
toc: true
show_subscribe: false
---

## 回溯

### 子集

[Leetcode - 78 Subsets (Medium)](https://leetcode.com/problems/subsets/)

题目描述：给定一个不重复的整数数组，找出所有的子集。

```
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}
private void backtrack(List<List<Integer>> list , List<Integer> curList, int [] nums, int start){
    list.add(new ArrayList<>(curList));
    for(int i = start; i < nums.length; i++){
        curList.add(nums[i]);
        backtrack(list, curList, nums, i + 1);
        curList.remove(curList.size() - 1);
    }
}
```

### 子集 II

[Leetcode - 90 Subsets II (Medium)](https://leetcode.com/problems/subsets-ii/)

题目描述：给定一个重复的整数数组，找出所有不重复的子集。

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> list = new ArrayList<>();
    backtrack(list, new ArrayList<Integer>(), nums, 0);
    return list;
}
public void backtrack(List<List<Integer>> list, List<Integer> curList, int[] nums, int index){
    list.add(new ArrayList(curList));
    for(int i = index; i < nums.length; i++){
        if(i > index && nums[i] == nums[i - 1]) continue;
        curList.add(nums[i]);
        backtrack(list, curList, nums, i + 1);
        curList.remove(curList.size() - 1);
    }
}
```

### 组合

[Leetcode - 77 Combinations (Medium)](https://leetcode.com/problems/combinations/)

题目描述：找出 1 - n 中 k 个数字组合的数目。

```
Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

解题思路：为什么是 n - k + 1 ? 因为当到后面的时候数字不够了，不能继续循环，例如 n = 10, k = 5，第一个开始循环的数只能是 1 - 6，当为 7 的时候，就算循环到了最后，7 8 9 10，最多也是 4 个数字，达不到 5。

```java
public List<List<Integer>> combine(int n, int k) {
    List<List<Integer>> ansList = new ArrayList<>();
    backtrack(ansList, new ArrayList<Integer>(), 1, n, k);
    return ansList;
}

public void backtrack(List<List<Integer>> ansList, List<Integer> curList, int start, int n, int k){
    if(k == 0){
        ansList.add(new ArrayList(curList));
        return;
    }
    
    for(int i = start; i <= n - k + 1; i++){ // 注意是 n - k + 1
        curList.add(i);
        backtrack(ansList, curList, i + 1, n, k - 1);
        curList.remove(curList.size() - 1);
    }
}
```

### 组合数之和

[Leetcode - 39 Combination Sum (Medium)](https://leetcode.com/problems/combination-sum/)

题目描述：给定一无重复的候选序列和一个 target 值，找出序列中所有能组成和为 target 的组合。可以从候选序列中重复使用同一个元素。假设所有元素均为正数，返回结果中不能出现相同的组合。

```
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]
```

```java
public List<List<Integer>> combinationSum(int[] nums, int target) {
    Arrays.sort(nums);
    List<List<Integer>> ansList = new ArrayList<>();
    backtrack(ansList, new ArrayList<Integer>(), nums, 0, target);
    return ansList;
}

public void backtrack(List<List<Integer>> ansList, List<Integer> curList, int[] nums, int start, int target){
    if(target == 0){
        ansList.add(new ArrayList(curList));
        return;
    }
    for(int i = start; i < nums.length; i++){
        if(target - nums[i] < 0) break;
        curList.add(nums[i]);
        backtrack(ansList, curList, nums, i, target - nums[i]);
        curList.remove(curList.size() - 1);
    }
}
```

### 组合数之和 II

[Leetcode - 40 Combination Sum II (Medium)](https://leetcode.com/problems/combination-sum-ii/)

题目描述：候选序列中有重复的元素，求和过程不能重复使用一个元素。

```java
public List<List<Integer>> combinationSum2(int[] candidates, int target) {
    Arrays.sort(candidates);
    List<List<Integer>> ansList = new ArrayList<>();
    backtrack(ansList, new ArrayList<Integer>(), candidates, target, 0);
    return ansList;
}

public void backtrack(List<List<Integer>> ansList, List<Integer> curList, int[] candidates, int target, int start){
    if(target < 0) return;
    if(target == 0){
        ansList.add(new ArrayList(curList));
    }else{
        for(int i = start; i < candidates.length; i++){
            if(target - candidates[i] < 0) break; // 剪枝
            if(i > start && candidates[i] == candidates[i - 1]) continue; // 避免使用重复元素
            curList.add(candidates[i]);
            backtrack(ansList, curList, candidates, target - candidates[i], i + 1);
            curList.remove(curList.size() - 1);
        }
    }
}
```

### 组合数之和 III

[Leetcode - 216 Combination Sum III (Medium)](https://leetcode.com/problems/combination-sum-iii/)

题目描述：找出 k 个数字之和为 n 的组合，从 1 - 9 中选择，数字不能重复使用。

```
Input: k = 3, n = 9
Output: [[1,2,6], [1,3,5], [2,3,4]]
```

```java
public List<List<Integer>> combinationSum3(int k, int n) {
    List<List<Integer>> ansList = new ArrayList<>();
    backtrack(ansList, new ArrayList<Integer>(), k, n, 1);
    return ansList;
}
public void backtrack(List<List<Integer>> ansList, List<Integer> curList, int k, int n, int start){
    if(n < 0 || k < 0) return;
    if(k == 0 && n == 0){
        ansList.add(new ArrayList<Integer>(curList));
        return;
    }
    for(int i = start; i <= 9 - k + 1; i++){
        curList.add(i);
        backtrack(ansList, curList, k - 1, n - i, i + 1);
        curList.remove(curList.size() - 1);
    }
}
```

### 全排列

[Leetcode - 46 Permutations (Medium)](https://leetcode.com/problems/permutations/)

题目描述: 给定无重复的整数序列，返回所有全排列的可能。

```
Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> ansList = new ArrayList<>();
    backtrack(ansList, new ArrayList<Integer>(), nums);
    return ansList;
}
public void backtrack(List<List<Integer>> ansList, List<Integer> curList, int[] nums){
    if(curList.size() == nums.length){
        ansList.add(new ArrayList(curList));
    }else{
        for(int i = 0; i <nums.length; i++){
            if(curList.contains(nums[i])) continue;
            curList.add(nums[i]);
            backtrack(ansList, curList, nums);
            curList.remove(curList.size() - 1);
        }
    }
}
```

### 全排列 II

[Leetcode - 47 Permutations II (Medium)](https://leetcode.com/problems/permutations-ii/)

题目描述：给定有重复的整数序列，返回所有全排列的可能。

```
Input: [1,1,2]
Output:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> ansList = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(ansList, new ArrayList<Integer>(), new boolean[nums.length], nums);
    return ansList;
}

public void backtrack(List<List<Integer>> ansList, List<Integer> curList, boolean[] used, int[] nums){
    if(curList.size() == nums.length){
        ansList.add(new ArrayList(curList));
        return;
    }
    
    for(int i = 0; i < nums.length; i++){
        if(used[i]) continue;
        if(i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue; // used[i - 1] 也可以，但效率低
        used[i] = true;
        curList.add(nums[i]);
        backtrack(ansList, curList, used, nums);
        curList.remove(curList.size() - 1);
        used[i] = false;
    }
}
```

### 下一种全排列

[Leetcode - 31 Next Permutation (Medium)](https://leetcode.com/problems/next-permutation/)

题目描述：给定一个整型数组，输出该整型数组的下一种全排列，如果是最后一种，则输出第一种全排列，要求只能使用常数的额外空间。

```
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1
```

计算全排列的方式有三种：

1. 递归实现 ：从第一个数字起每个数分别于它后面的数字交换。
2. 去掉重复的递归实现：从第一个数字起每个数分别于它后面非重复的数字交换。
3. 非递归实现：不断的计算下一种的排列方式。

那么如果计算下一种全排列呢？

以序列 158476531 为例，首先从后向前找到第一个相邻的递增序列，47 满足要求，将第一个数字 4 作为替换数，记录其下标位置，之后，再从后面的序列中找到一个比替换数 4 大的最小数，5 满足要求，将 4 与 5 交换，得到序列 158576431 ，最后将替换数下标后面的序列反转，得到下一种全排列 158513467。

<div align="center">  <img src="/img/leetcode-31.gif" width="70%"/> </div><br>

如题目所述，本题使用第三种方法直接计算下一种全排列最为合适。

```java
public class Solution {
    public static void nextPermutation(int[] num) {
        
        // 从后向前找到第一个增序序列
        int i = num.length - 2;
        while(i >= 0 && num[i] >= num[i + 1]){
            i--;
        }
        
        // 将替换数与后面序列中比替换数大的最小数交换
        if(i >= 0){
            int j = i + 1;
            while(j < num.length && num[i] < num[j]){
                j++;
            }
            swap(num, i, j - 1);
        }
        
        // 将替换数后面的序列反转
        i++;
        int k = num.length - 1;
        for(; i < k; i++, k--){
            swap(num, i, k);
        }

    }
    
    public static void swap(int[] num, int a, int b){
        int temp = num[a];
        num[a] = num[b];
        num[b] = temp;
    }
}
```

### 序列排序

[Leetcode - 60 Permutation Sequence](https://leetcode.com/problems/permutation-sequence/)

题目描述：返回 n 个数字的第 k 个排列，共有 n! 种排列方式。

```
Input: n = 3, k = 3
Output: "213"

1 "123"
2 "132"
3 "213"
4 "231"
5 "312"
6 "321"
```

解题思路：如果一个一个推的话一定会超时，所以肯定有优化的方法。可以把排列结果分成组，例如 n = 3 的全排列可以根据最高位分成 3 组，每组的数量是相同的，都是 (n - 1)! = 2，第一组："123" 与 "132"，第二组："213" 与 "231"，第三组："312" 与 "321"。这样我们就可以通过这个性质定位到第 k 种排列在哪个组，例如当 n = 3，k = 3，能够通过 (k - 1) / (n - 1)! = 3 / 2 = 1，这样就定位到了在第二组，也就确定了最高位是 2，依次后推，就能得到所有的位数字。

```java
public String getPermutation(int n, int k) {
    // 转为list
    List<Integer> list = new ArrayList<Integer>();
    for(int i = 1; i <= n; i++) list.add(i);
    
    // 计算阶乘
    int[] fact = new int[n];
    fact[0] = 1;
    for(int i = 1; i < fact.length; i++) fact[i] = i * fact[i - 1];
    
    k -= 1;// 下标从 0 开始
    
    StringBuilder sb = new StringBuilder();
    for(int i = n; i > 0 ; i--){
        int ind = k / fact[i - 1];
        k = k % fact[i - 1];
        sb.append(list.get(ind));
        list.remove(ind);
    }
    return sb.toString();
}
```

### 电话号码的字母组合

[Leetcode - 17 Letter Combinations of a Phone Number (Medium)](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)

题目描述：输出数字键盘 9 宫格可能的所有字母组合。


<div align="center">  <img src="/img/algorithm_code_thought_2.png" width="30%"/> </div><br>

```
Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

解题思路：用 Map 存储数字对应的字母，回溯遍历所有情况。

```java
Map<Character,String> map = new HashMap<Character, String>(){{
    put('2',"abc");put('3',"def");put('4',"ghi");
    put('5',"jkl");put('6',"mno");put('7',"pqrs");
    put('8',"tuv");put('9',"wxyz");
}};
List<String> ans = new ArrayList<String>();
public List<String> letterCombinations(String digits) {
    if(digits.length() == 0) return ans;
    backtrack("",digits);
    return ans;
}
public void backtrack(String combination, String nextDigits) {
    if(nextDigits.length() == 0){
        ans.add(combination);
        return;
    }
    Character cur = nextDigits.charAt(0);
    String curString = map.get(cur);
    for(int i = 0; i < curString.length(); i++){
        backtrack(combination + curString.charAt(i), nextDigits.substring(1));
    }
}
```

### 复原 IP 地址

[Leetcode - 93 Restore IP Addresses (Medium)](https://leetcode.com/problems/restore-ip-addresses/)

题目描述：给定一个只含有数字的字符串，返回所有 IP 地址的可能情况。

```
Input: "25525511135"
Output: ["255.255.11.135", "255.255.111.35"]
```

解题思路：要考虑到所有的情况，IP 地址共包含四段，每段最大值为 255，且不能超过三个数字，如果首数字是 0 后面就不能跟任何数字了。

```java
public List<String> restoreIpAddresses(String s) {
    List<String> ansList = new ArrayList<String>();
    backtrack(s, ansList, "", 0, 0);
    return ansList;
}

public void backtrack(String ip, List<String> ansList, String curString, int index, int count){
    if(count > 4) return;
    if(count == 4 && index == ip.length()){
        ansList.add(curString);
    }
    for(int i = 1; i < 4; i++){
        if(index + i > ip.length()) break;
        String s = ip.substring(index, index + i);
        if(s.startsWith("0") && s.length() > 1 || s.length() == 3 && Integer.parseInt(s) >= 256) continue;
        backtrack(ip, ansList, curString + s + (count == 3?"":"."), index + i, count + 1);
    }
}
```

### 表达式增加操作符

[Leetcode - 282 Expression Add Operators (Hard)](https://leetcode.com/problems/expression-add-operators/)

题目描述：给定一个由数字组成的字符串，向字符串中添加 `+、-、*` 形成一个表达式，使该表达式的计算和为 target。

```
Input: num = "123", target = 6
Output: ["1+2+3", "1*2*3"] 
Input: num = "105", target = 5
Output: ["1*0+5","10-5"]
```

解题思路：注意，可以拆成多位的数字，不只是一位数字。需要遍历所有的情况，找出和为 target 的表达式，需要注意几个边界条件：1. 计算结果可能溢出，所有使用 Long 存储结果。2. 0 开头的数字不成立，例如 05。3. 需要存储上次的结果，用以计算 1 + 2 * 3，这时需要先计算后面的乘法，而不是按前后顺序计算。

```java
public List<String> addOperators(String num, int target) {
    List<String> ansList = new ArrayList<String>();
    if(num == null || num.length() == 0) return ansList;
    backtrack(ansList, "", num, target, 0, 0, 0);
    return ansList;
}

public void backtrack(List<String> ansList, String path, String num, int target, int pos, long eval, long multed){
    if(pos == num.length()){
        if(eval == target){
            ansList.add(path);
        }
        return;
    }
    for(int i = pos; i < num.length(); i++){
        if(i != pos && num.charAt(pos) == '0') break; // 跳过首位为0的数字
        long cur = Long.parseLong(num.substring(pos, i + 1));
        if(pos == 0){
            backtrack(ansList, path + cur, num, target, i + 1, cur, cur);
        }else{
            backtrack(ansList, path + "+" + cur, num, target, i + 1, eval + cur, cur);
            backtrack(ansList, path + "-" + cur, num, target, i + 1, eval - cur, -cur);
            backtrack(ansList, path + "*" + cur, num, target, i + 1, eval - multed + cur * multed, cur * multed);
        }
    }
}
```

### 拆分词句 II

[Leetcode - 140 Word Break II (Hard)](https://leetcode.com/problems/word-break-ii/)

题目描述：计算出所有合法的拆分情况。

```
Input:
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
Output:
[
  "cats and dog",
  "cat sand dog"
]
```

解题思路：如果不记录会超时。

```java
public List<String> wordBreak(String s, List<String> wordDict) {
    Set<String> dict = new HashSet<>(wordDict);
    Map<String, List<String>> cache = new HashMap<>();
    return dfs(s, cache, dict);
}

private List<String> dfs(String s, Map<String, List<String>> cache, Set<String> dict){
    if(cache.containsKey(s)) return cache.get(s);
    
    LinkedList<String> res = new LinkedList<>();
    if(s.length()==0){
        res.add("");
        return res;
    }
    
    for(String word : dict){
        if(s.startsWith(word)){
            List<String> sublist = dfs(s.substring(word.length()), cache, dict);
            for(String sub : sublist){
                if(sub.isEmpty()){
                    res.add(word + sub);
                }
                else res.add(word + " " + sub);
            }
        }
    }
    cache.put(s, res);
    return res;
}
```

## BFS & DFS

### 岛屿的数量

[Leetcode - 200 Number of Islands (Medium)](https://leetcode.com/problems/number-of-islands/)

题目描述：给定一个二维数组，1 代表陆地，0 代表海洋，计算岛屿的数量。

```
Input:
11000
11000
00100
00011

Output: 3
```

```java
public int numIslands(char[][] grid) {
    int count = 0;
    if (grid.length == 0) return 0;
    for (int i = 0; i < grid.length; i++){
        for (int j = 0; j < grid[0].length; j++)
            if (grid[i][j] == '1') {
                DFSMarking(grid, i, j);
                ++count;
            }
    }    
    return count;
}

private void DFSMarking(char[][] grid, int i, int j) {
    if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] != '1') return;
    grid[i][j] = '0';
    DFSMarking(grid, i + 1, j);
    DFSMarking(grid, i - 1, j);
    DFSMarking(grid, i, j + 1);
    DFSMarking(grid, i, j - 1);
}
```

### 单词搜索

[Leetcode - 79 Word Search (Medium)](https://leetcode.com/problems/word-search/)

题目描述：从一个由字母组成的二维数组中，搜索一个字符串。

```
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

Given word = "ABCCED", return true.
Given word = "SEE", return true.
Given word = "ABCB", return false.
```
```java
public boolean exist(char[][] board, String word) {
    for(int i = 0; i < board.length; i++){
        for(int j = 0; j < board[0].length; j++){
            if(board[i][j] == word.charAt(0)){
                if(backtrack(board, word, 0, i, j)) return true;
            }
        }
    }
    return false;
}
public boolean backtrack(char[][] board, String word, int index, int x, int y){
    if(index == word.length()){
        return true;
    }
    if(x < 0 || y < 0 || x >= board.length || y >= board[0].length) return false;
    if(board[x][y] != word.charAt(index)) return false;
    char c = board[x][y];
    board[x][y] = '*';
    boolean flag =  backtrack(board, word, index + 1, x, y + 1)
                ||  backtrack(board, word, index + 1, x, y - 1)
                ||  backtrack(board, word, index + 1, x + 1, y)
                ||  backtrack(board, word, index + 1, x - 1, y);
    
    board[x][y] = c;
    return flag;
}
```

### 包围区域

[Leetcode - 130 Surrounded Regions (Medium)](https://leetcode.com/problems/surrounded-regions/)

题目描述：将被包围的 O 全部替换成 X，边界的 O 不是被包围的。

```
X X X X
X O O X
X X O X
X O X X
After running your function, the board should be:
X X X X
X X X X
X X X X
X O X X
```

解题思路：本来想从中间的每个 O 进行 DFS，发现太麻烦了，应该从四个边向中间遍历，将 O 串成一个字符串，例如 *，然后剩下的 O 是被包围的，替换成 X，最后再将 * 替换为 O。

```java
public void solve(char[][] board) {
    if (board.length == 0 || board[0].length == 0)
        return;
    if (board.length < 2 || board[0].length < 2)
        return;
    int m = board.length, n = board[0].length;

    for (int i = 0; i < m; i++) {
        if (board[i][0] == 'O')
            boundaryDFS(board, i, 0);
        if (board[i][n-1] == 'O')
            boundaryDFS(board, i, n-1);	
    }

    for (int j = 0; j < n; j++) {
        if (board[0][j] == 'O')
            boundaryDFS(board, 0, j);
        if (board[m-1][j] == 'O')
            boundaryDFS(board, m-1, j);	
    }

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] == 'O')
                board[i][j] = 'X';
            else if (board[i][j] == '*')
                board[i][j] = 'O';
        }
    }
}

private void boundaryDFS(char[][] board, int i, int j) {
    if (i < 0 || i > board.length - 1 || j <0 || j > board[0].length - 1)
        return;
    if (board[i][j] == 'O')
        board[i][j] = '*';
    if (i > 1 && board[i-1][j] == 'O')
        boundaryDFS(board, i-1, j);
    if (i < board.length - 2 && board[i+1][j] == 'O')
        boundaryDFS(board, i+1, j);
    if (j > 1 && board[i][j-1] == 'O')
        boundaryDFS(board, i, j-1);
    if (j < board[i].length - 2 && board[i][j+1] == 'O' )
        boundaryDFS(board, i, j+1);
}
```

### 词语阶梯

[Leetcode - 127 Word Ladder (Medium)](https://leetcode.com/problems/word-ladder/)

题目描述：给定两个单词和一个字典，每次变换开始单词的一个字母，变换后的单词存在于字典中，计算通过多少次变换能变换到终止单词。

```
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

解题思路：BFS，每次将单词中的每个字符替换成 26 个字母，判断变换后的单词是否存在于字典中。

```java
public int ladderLength(String beginWord, String endWord, List<String> wordList) {
    Set<String> set = new HashSet<>(wordList);
    Queue<String> q = new LinkedList<>();
    q.offer(beginWord);
    int step = 1;
    while (!q.isEmpty()) {
        int size = q.size();
        for (int j = 0; j < size; j++) {
            String cur = q.poll();
            for (int i = 0; i < endWord.length(); i++) {
                for (char letter = 'a'; letter <= 'z'; letter++) {
                    StringBuilder newWord = new StringBuilder(cur);
                    newWord.setCharAt(i, letter);
                    if (set.contains(newWord.toString())) {
                        if (newWord.toString().equals(endWord)) return step + 1;
                        set.remove(newWord.toString());
                        q.offer(newWord.toString());
                    }
                }
            }

        }
        step++;
    }
    return 0;
}
```

### N 皇后

[Leetcode - 51 N-Queens (Hard)](https://leetcode.com/problems/n-queens/)

题目描述：找到 N 皇后的所有情况。

<div align="center">  <img src="/img/leetcode-51.png" width="40%"/> </div><br>

```
Input: 4
Output: [
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above.
```

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
public void dfs(char[][] board, List<List<String>> res, int index){
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
public boolean validate(char[][] board, int row, int col){
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

### N 皇后 II

[Leetcode - 52 N-Queens II (Hard)](https://leetcode.com/problems/n-queens-ii/)

题目描述：这道题只是让求出 N 皇后的摆放数量。

```
Input: 4
Output: 2
Explanation: There are two distinct solutions to the 4-queens puzzle as shown below.
[
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
```

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

### 有效数独

[Leetcode - 36 Valid Sudoku (Medium)](https://leetcode.com/problems/valid-sudoku/)

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

### 求解数独

[Leetcode - 37 Sudoku Solver (Hard)](https://leetcode.com/problems/sudoku-solver/)

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
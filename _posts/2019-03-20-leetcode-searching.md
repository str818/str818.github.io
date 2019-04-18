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

## 电话号码的字母组合

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
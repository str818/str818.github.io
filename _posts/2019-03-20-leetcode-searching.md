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
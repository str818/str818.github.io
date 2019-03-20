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
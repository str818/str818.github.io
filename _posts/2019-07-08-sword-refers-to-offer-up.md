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

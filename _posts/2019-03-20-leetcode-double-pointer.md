---
layout: article
title: Leetcode —「双指针」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Double_Pointer
pageview: true
toc: true
show_subscribe: false
---


## 有序数组的两数之和

[Leetcode - 167 Two Sum II - Input array is sorted (Easy)](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)

```
Input: nums = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.
```

```java
public int[] twoSum(int[] nums, int target) {
    int i = 0, j = nums.length - 1;
    while (i < j) {
        int sum = nums[i] + nums[j];
        if (sum == target) {
            return new int[]{i + 1, j + 1};
        } else if (sum < target) {
            i++;
        } else {
            j--;
        }
    }
    return null;
}
```

## 两数平方和

[Leetcode - 633 Sum of Square Numbers (Easy)](https://leetcode.com/problems/sum-of-square-numbers/)

```
Input: 5
Output: True
Explanation: 1 * 1 + 2 * 2 = 5
```

```java
public boolean judgeSquareSum(int c) {
    int i = 0, j = (int) Math.sqrt(c);
    while (i <= j) {
        int powSum = i * i + j * j;
        if (powSum == c) {
            return true;
        } else if (powSum > c) {
            j--;
        } else {
            i++;
        }
    }
    return false;
}
```

## 反转字符串中的元音字母

[Leetcode - 345 Reverse Vowels of a String (Easy)](https://leetcode.com/problems/reverse-vowels-of-a-string/description/)

```
Input: "leetcode"
Output: "leotcede"
```

```java
private final HashSet<Character> vowels = new HashSet<>(
    Arrays.asList('a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'));

public String reverseVowels(String s) {
    int i = 0, j = s.length() - 1;
    char[] res = new char[s.length()];
    while (i <= j) {
        char ci = s.charAt(i);
        char cj = s.charAt(j);
        if (!vowels.contains(ci)) {
            res[i++] = ci;
        } else if (!vowels.contains(cj)) {
            res[j--] = cj;
        } else {
            res[i++] = cj;
            res[j--] = ci;
        }
    }
    return new String(res);
}
```

## 验证回文字符串

[Leetcode - 680 Valid Palindrome II (Easy)](https://leetcode.com/problems/valid-palindrome-ii/)

题目描述：可以删除一个字符，判断是否能构成回文字符串。

```
Input: "abca"
Output: True
Explanation: You could delete the character 'c'.
```

```java
public boolean validPalindrome(String s) {
    for (int i = 0, j = s.length() - 1; i < s.length(); i++, j--) {
        if (s.charAt(i) != s.charAt(j)) {
            return isPalindrome(s, i + 1, j) || isPalindrome(s, i, j - 1);
        }
    }
    return true;
}

private boolean isPalindrome(String s, int i, int j) {
    while (i < j) {
        if (s.charAt(i++) != s.charAt(j--)) {
            return false;
        }
    }
    return true;
}
```

## 合并两个有序数组

[Leetcode - 88 Merge Sorted Array (Easy)](https://leetcode.com/problems/merge-sorted-array/)

题目描述：把归并结果存到第一个数组上。

```
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int len = m + n - 1;
    int index1 = m - 1, index2 = n - 1;
    while (len >= 0) {
        if (index2 >= 0 && index1 >= 0) {
            nums1[len--] = nums1[index1] > nums2[index2] ? nums1[index1--] : nums2[index2--];
        } else {
            nums1[len--] = index1 >= 0 ? nums1[index1--] : nums2[index2--];
        }
    }
}
```

## 判断链表是否有环

[Leetcode - 141 Linked List Cycle (Easy)](https://leetcode.com/problems/linked-list-cycle/)

解题思路：使用双指针，一个指针每次移动一个节点，一个指针每次移动两个节点，如果存在环，那么这两个指针一定会相遇。

```java
public boolean hasCycle(ListNode head) {
    if (head == null) return false;
    ListNode l1 = head, l2 = head;
    while (l2.next != null && l2.next.next != null) {
        l1 = l1.next;
        l2 = l2.next.next;
        if (l1 == l2) return true;
    }
    return false;
}
```

## 寻找重复数

[Leetcode - 287 Find the Duplicate Number (Medium)](https://leetcode.com/problems/find-the-duplicate-number/)

题目描述：给定一个包含 n + 1 个整数的数组，其中每一个整数均介于 [1,n] 之间，证明其至少有一个重复元素存在。假设只有一个数字出现重复，找出这个重复的数字。

要求：
1. 不能修改原数组
2. 空间复杂度为 O(1)
3. 时间复杂度小于 $O(n^2)$
4. 数组中只存在一个重复数，但是可能重复多次

解题思路：这道题加上要求之后就有很多限制了，可以把这道题巧妙的转化成求环的入口问题，类似[Leetcode - 142 Linked List Cycle II (Medium)](https://leetcode.com/problems/linked-list-cycle-ii/) 。把数组中的 index 看成链表结点的值，数组中 index 对应的 value 看成结点的 next，由于一定存在重复数字，所以一定会有两个结点的 next 指向相同的位置，所以一定会存在环。从数组的 0 位置开始，由于整数介于 [1,n] 之间，不同有结点指向数组的 0 位置。

```java
public int findDuplicate(int[] nums) { 
    int slow = nums[0];
    int fast = nums[0];
    do{
        slow = nums[slow];
        fast = nums[nums[fast]];
    }while(slow != fast);
    
    fast = nums[0];
    while(slow != fast){
        slow = nums[slow];
        fast = nums[fast];
    }
    return slow;
}
```


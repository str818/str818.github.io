---
layout: article
title: 算法 —「排序」计数排序
tags: 算法

lang: zh-Hans
key: Algorithmic_Counting_Sort
pageview: true
toc: true
show_subscribe: false
---

## 一、思想

**开辟额外空间，统计每个元素的数量。**

计数排序是一种不基于比较的排序算法，主要思想是计算出待排序序列的最大值 maxValue 与 最小值 minValue，开辟一个长度为 maxValue - minValue + 1 的额外空间，统计待排序序列中每个元素的数量，记录在额外空间中，最后遍历一遍额外空间，按照顺序把每个元素赋值到原始序列中。

## 二、图解过程

<div align="center">  <img src="/img/algorithmic_counting_sort.png" width="80%"/> </div><br>

## 三、核心代码

```java
public static void countingSort(int[] arr){

    if(arr == null || arr.length < 2) return;

    int maxValue = Integer.MIN_VALUE;
    int minValue = Integer.MAX_VALUE;
    // 找出最大值与最小值
    for(int i = 0; i < arr.length; i++){
        if(arr[i] > maxValue) maxValue = arr[i];
        else if(arr[i] < minValue) minValue = arr[i];
    }
    // 初始化计数数组
    int[] counter = new int[maxValue - minValue + 1];
    for(int i = 0; i < arr.length; i++){
        counter[arr[i] - minValue]++;
    }
    // 计算每个元素的位置
    for(int i = 0; i < counter.length - 1; i++){
        counter[i + 1] += counter[i];
    }
    // 初始化一个新的数组存放排序后的元素
    int[] ans = new int[arr.length];
    for(int i = arr.length - 1; i >= 0; i--) {
        ans[counter[arr[i] - minValue] - 1] = arr[i];
        counter[arr[i] - minValue]--;
    }
    //将排序结果赋值到原始数组中
    for(int i = 0; i < arr.length; i++){
        arr[i] = ans[i];
    }
}
```

## 四、复杂度分析

### 1. 时间复杂度

当待排序元素是 n 个 0 到 k 之间的整数时，时间复杂度是 $O(n+k)$。

### 2. 额外空间复杂度

计数排序需要两个额外的数组，分别用于记录元素数量与排序结果，$O(n+k)$。

## 五、稳定性分析

生成排序结果的过程，是从后向前遍历的，也就是说原始数组中先出现的元素依旧会在前面。

所以，计数排序是稳定的排序算法。

---
layout: article
title: 算法 - 希尔排序
tags: 算法

lang: zh-Hans
key: Algorithmic_Shell_Sort
pageview: true
toc: true
show_subscribe: false
---

## 一、思想

**通过粗粒度的插入排序改善数列的有序程序，逐步细化，直到间隔为 1。**

希尔排序是对直接插入排序的改进算法，在使用直接排序算法时，如果序列很长，且无序程度较高，向前插入一次可能导致移动的操作很大。如果原序列相对有序（小的数大致在前，大的数大致在后），则能缓解这一问题。希尔排序就是在插入排序的之前进行了粗略的排序，使原序列相对有序。

## 二、图解过程

<div align="center">  <img src="/img/algorithmic_shell_sort.png" width="80%"/> </div><br>

间隔 D 有很多中选择，此处使用的是希尔增量 D = n/2, n/4 ,..., 1。

## 三、核心代码

```java
public static void shellSort(int[] arr){
    
    if(arr == null || arr.length < 2) return;

    for(int D = arr.length / 2; D >= 1; D = D / 2){
        for(int i = D; i < arr.length; i++){
            int j = i;
            int temp = number[j];//要插入的值
            int insert_position = j;

            while(j - D >= 0 && temp < arr[j - D]){
                arr[j] = arr[j - D];
                j -= D;
            }
            arr[j] = temp;
        }
    }
}
```

## 四、复杂度分析

### 1. 时间复杂度

希尔排序最坏的时间复杂度依然为 $O(N^2)$，但其能够有效改善直接插入排序序列无序且长度大时的大长度数列移位。希尔排序中对于增量序列的选择十分重要，直接影响到希尔排序的性能，本文使用的是希尔增量，还有 Hibbard 增量，时间复杂度为 $O(N^{1.5})$。

### 2. 额外空间复杂度

未借助其它辅助空间，$O(1)$。

## 五、稳定性分析

与直接插入排序不同，希尔排序中的分组插入可能导致顺序移位。

所以，插入排序是稳定的排序算法。
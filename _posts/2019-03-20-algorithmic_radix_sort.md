---
layout: article
title: 算法 - 基数排序
tags: 算法

lang: zh-Hans
key: Algorithmic_Radix_Sort
pageview: true
toc: true
show_subscribe: false
---

## 一、思想

基数排序可以看成是桶排序的扩展，以整数排序为例，主要思想是将整数按位数划分，准备 10 个桶，代表 0 - 9，根据整数个位数字的数值将元素放入对应的桶中，之后按照输入赋值到原序列中，依次对十位、百位等进行同样的操作，最终就完成了排序的操作。

## 二、图解过程

<div align="center">  <img src="/img/algorithmic_radix_sort.png" width="100%"/> </div><br>

## 三、核心代码

```java
public static void radixSort(int[] arr){
    if(arr == null || arr.length < 2) return;
    radixSort(arr, 0, arr.length - 1, maxbit(arr));
}

// 计算最大位数
public static int maxbits(int[] arr){
    int max = Integer.MIN_VALUE;
    for (int i = 0; i < arr.length; i++) {
        max = Math.max(max, arr[i]);
    }
    int res = 0;
    while (max != 0) {
        res++;
      max /= 10;
    }
    return res;
}

// 基数排序
public static void radixSort(int[] arr, int begin, int end, int digit) {
    final int radix = 10;
    int i = 0, j = 0;
    int[] count = new int[radix];
    int[] bucket = new int[end - begin + 1];
    // 依次遍历每个位数
    for (int d = 1; d <= digit; d++) {
        for (i = 0; i < radix; i++) {
            count[i] = 0;
        }
			
        // 统计数量
        for (i = begin; i <= end; i++) {
            j = getDigit(arr[i], d);
            count[j]++;
        }
          
        // 计算位置
        for (i = 1; i < radix; i++) {
            count[i] = count[i] + count[i - 1];
        }
          
        // 记录到对应位置
        for (i = end; i >= begin; i--) {
            j = getDigit(arr[i], d);
            bucket[count[j] - 1] = arr[i];
            count[j]--;
        }
        for (i = begin, j = 0; i <= end; i++, j++) {
            arr[i] = bucket[j];
        }
    }
}

// 获取位数数值
public static int getDigit(int x, int d) {
    return ((x / ((int) Math.pow(10, d - 1))) % 10);
}
```

## 四、复杂度分析

### 1. 时间复杂度

k 为关键字个数，本文的上述距离关键字为 2 个，分别是个位和十位；m 为 关键字的取值范围，本文的举例关键字取值范围 m 为 10（0 - 9）；n 为待排序序列的元素数量。

总共遍历 k 遍，每一遍包含：将每个元素放进桶中（n） 与 将桶中元素收回到原序列中（m），所以时间复杂度为 O(k*(n+m))。

### 2. 额外空间复杂度

m 个桶与存放 n 个元素的空间，O(n + m)。

## 五、稳定性分析

基数排序不会改变相同元素之间的相对位置，仔细思考一下过程，元素收回的过程中是从后向前进行的。

所以，基数排序是稳定的排序算法。
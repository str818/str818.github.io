---
layout: article
title: 算法 - 快速排序
tags: 算法

lang: zh-Hans
key: Algorithmic_Quick_Sort
pageview: true
toc: true
show_subscribe: false
---

## 一、思想

**选择一个基准数，使得基准数左边小右面大，左右再次迭代。**

与归并排序相同，快速排序也是用分支的思想。首先从数组中随机选取一个元素最为基准数，将该元素交换的数组的最后一位。将所有小于基准数的放置在左边，大于基准数的放置在右边，中间剩下的是等于基准数的元素，这时将基准数交换到中间，形成了一个以基准数为中心左右分治的数组，左面的元素都小于基准数，右边的元素都大于基准数。这时，分别将左右两个大于基准数与小于基准数的序列递归重新执行上述步骤，即可得到最终有序的数组。

## 二、图解过程

<div align="center">  <img src="/img/algorithmic_quick_sort.png" width="90%"/> </div><br>

## 三、核心代码

```java
public static void quickSort(int[] arr, int l, int r){
    if(l < r){
        swap(arr, l + (int)(Math.random() * (r - l + 1))， r);  //随机选取一个基准数
        int[] p = partition(arr, l, r); //小于区域[0,p[0]] 大于区域[p[1],r]
        quickSort(arr, l, p[0] - 1);
		    quickSort(arr, p[1] + 1, r);
    }
}
// 以基准数为界，大的在右边，小的在左边   基准数放置在数组的最后一位
public static int[] partition(int[] arr, int l, int r){
    int less = l - 1;
    int more = r;
    while(l < more){
        if(arr[l] < arr[r]){
            swap(arr, ++less, l++);
        }else if(arr[l] > arr[r]){
            swap(arr, --more, l);
        }else{
            l++;
        }
    }
    swap(arr, more, r);
    //返回值分别为小于序列与大于序列的分界线
    return new int[]{less + 1, more};
}
```

## 四、复杂度分析

### 1. 时间复杂度

最好时间复杂度：$O(logN)$。每次选的基准数正好等分当前数列

最坏时间复杂度：$O(N^2)$。待排序数组为逆序时，每次划分只少了一个元素。

平均时间复杂度：$O(NlogN)$。

### 2. 额外空间复杂度

未借助其它辅助空间，$O(1)$。

## 五、稳定性分析

从前到后扫描，将第一个大数置于后方的大端，如果后面紧邻相等的大数，则被置于该数前面。

所以，快速排序是不稳定的。
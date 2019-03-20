---
layout: article
title: 算法 - 堆排序
tags: 算法

lang: zh-Hans
key: Algorithmic_Heap_Sort
pageview: true
toc: true
show_subscribe: false
---

## 一、思想

**通过建立最大/小堆 + 下沉元素找到最值。**

在学习堆排序之前，先了解一下什么是堆。堆是一个数组，可以被看成一个近似的完全二叉树，如下图所示，树上的每一个结点对应数组中的一个元素。除了最底层外，该树是完全充满的，而且是从左到右填充。在不越界的情况下，任何一个节点的左叶子节点在数组中的下标为 2i+1 ，右叶子节点在数组中的下标为 2i+2 ，父节点在数组中的下标为 (i-1)/2 。

<div align="center">  <img src="/img/algorithmic_heap_sort_0.png" width="80%"/> </div><br>

堆可以分为两种形式：最大堆和最小堆。

**最大堆：** 每个节点的值都大于等于其左右子节点的值。 

**最小堆：** 每个节点的值都小于等于其左右子节点的值。

堆排序算法的思想就是，将待排序数组构造成一个最大堆，这时，堆的根节点就是最大值。将其与末尾节点交换，然后将剩下的 n-1 个元素重新构造成一个最大堆，这样就得到了第二大的元素，以此类推，便能够得到一个有序序列。

## 二、图解过程

### 1. 建立最大堆

首先，我们有一个待排序数组，如何把这一数组调整成最大堆的形式呢？这个过程就是建立最大堆的过程。例如，数组 [ 3 6 2 1 0 4 ]，可以先将第一个元素 3 看成一个堆，之后将第二个元素 6 加入堆中，将其与父节点 3 进行比较，若比父节点 3 大，则与之交换，一直向上比较直到到达根节点或不比父节点大为止。这样的一轮操作，就将元素 6 插入到了堆中，之后又调整成了最大堆。以此类推，不断将后面的元素插入到堆中，并调整成最大堆，直到数组中的元素都插入到了堆，整个数组就成为了最大堆的排序形式。

<div align="center">  <img src="/img/algorithmic_heap_sort_1.png" width="80%"/> </div><br>

<div align="center">  <img src="/img/algorithmic_heap_sort_2.png" width="80%"/> </div><br>

### 2. 下沉元素

将待排序数组建立成最大堆顺序后，最大堆的根节点即为数组中的最大元素。这时，将根节点与堆的最后一个节点进行交换，也就是将最大元素放到数组的最后，再将 0 到 n-2 重新调整为最大堆，也就是将新的根节点下沉到正确的位置。重复上述步骤，将最大的元素沉到数组的后面，最后，就得到了一个升序排列的数组。

<div align="center">  <img src="/img/algorithmic_heap_sort_3.png" width="80%"/> </div><br>

## 三、核心代码

```java
public static void heapSort(int[] arr){
    if(arr == null || arr.length < 2) return null;
    //建立最大堆
    for (int i = 0; i < arr.length; i++) {
        heapInsert(arr, i);
    }
    int size = arr.length;
	  swap(arr, 0, --size);
    // 循环将最大元素下沉
    while (size > 0) {
        heapify(arr, 0, size);
        swap(arr, 0, --size);
    }
}
// 将 index 位置的元素插入最大堆，并调整最大堆
public static void heapInsert(int[] arr, int index) {
	  while (arr[index] > arr[(index - 1) / 2]) {
		    swap(arr, index, (index - 1) / 2);
		    index = (index - 1) / 2;
	  }
}
// 下沉元素
public static void heapify(int[] arr, int index, int size) {
	  int left = index * 2 + 1;
	  // 若有左子节点
	  while (left < size) {
        // 左子节点与右子节点中大的下标
        int largest = left + 1 < size && arr[left + 1] > arr[left] ? left + 1 : left;
        // 若左右子节点都小于自己，则不用继续下沉
        largest = arr[largest] > arr[index] ? largest : index;
        if (largest == index) {
            break;
        }
        // 否则，与大的子节点交换
        swap(arr, largest, index);
        index = largest;
        left = index * 2 + 1;
	  }
}
```

## 四、复杂度分析

### 1. 时间复杂度

堆排序由建立最大堆与下沉元素并重建堆两个部分组成。建立最大堆的时间复杂度为 $O(n)$，在下沉元素并创建堆的过程中，需要下沉 n - 1 个元素，重建堆就是逐渐下沉的过程，根据完全二叉树的性质，每个元素都下沉 $log_2(n-1),log_2(n-2)...1$ 层，近似为 $nlogn$，所以堆排序的时间复杂度为 $O(nlogn)$。

### 2. 额外空间复杂度

未借助其他辅助空间。

## 五、稳定性分析

堆排序是不稳定排序。

例如序列：19 5 3 5，19 是最大堆的堆顶，将 19 与最后一个元素 5 交换后，最后一个元素 5 交换到了前一个元素 5 的前面。
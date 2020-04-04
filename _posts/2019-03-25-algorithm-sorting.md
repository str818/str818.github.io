---
layout: article
title: 算法 —「排序」十大排序算法汇总
tags: 算法

lang: zh-Hans
key: Algorithm_Sorting
pageview: true
toc: true
show_subscribe: false
---

## 一、选择排序

#### 1. 思想

**将最 小/大 的数交换到待排序序列的最前面。**

首先，找到数组中最小的那个元素，其次，将它和数组的第一个元素交换位置（如果第一个元素就是最小元素那么就和自己交换）。在剩下的元素中找到最小的元素，将它与数组的第二个元素交换位置。如此往复，直到将整个数组排序。这种方法叫做选择排序，因为它在不断地选择剩余元素之中的最小者。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/24/8Ol1fg.png" width="80%"/> </div><br>

#### 3. 核心代码

```java
public static void selectionSort(int[] arr) {
  
    if (arr == null || arr.length < 2) return;
  
    for (int i = 0; i < arr.length - 1; i++) {
        // 寻找当前循环最小元素的下标
        int minIndex = i;
        for (int j = i + 1; j < arr.length; j++) {
            minIndex = arr[j] < arr[minIndex] ? j : minIndex;
        }
        swap(arr, i, minIndex);
    }
}
```

#### 4. 算法分析

###### Ⅰ. 时间复杂度

若待排序的元素总数为 N，外层循环 N 次（本文代码简写外层循环 N-1 次，不影响复杂度分析），第一次内循环比较 N-1 次（与除了本身的所有元素进行比较，找出最值），第二次内循环比较 N-2 次，以此类推，最后一次内循环比较 1 次，总共比较 (N-1) + (N-2) + ... + 1 次，等差求和，再乘上外层循环次数，共循环 N(N-1)/2 次，每次进行常数次的比较、赋值操作，时间复杂度为 $O(N^2)$。

###### Ⅱ. 额外空间复杂度

未借助其它辅助空间，$O(1)$。

###### Ⅲ. 稳定性

排序算法的稳定性：假设有两个相同大小的数，排序后能否维持排序前的顺序不变呢？若能维持，则说明是稳定的排序。

假设排序：5，8，5，2，9，第一次选择排序后第一个 5 与 2 交换，变为：2，8，5，5，9，第一个 5 交换到了另一个 5 的后边。

所以，选择排序不是稳定排序。

## 二、冒泡排序

#### 1. 思想

**每轮冒泡（循环）把最小/大的数放在最后。**

对相邻的元素进行两两比较，顺序相反则进行交换，这样，每一趟都会将最小或最大的元素交换的顶端，最终达到完全有序，例如，升序排列，就是最大的元素移动后面的过程。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/24/8O1AEV.png" width="80%"/> </div><br>

冒泡排序升序排列的整个过程如下图，数组共包含 6 个元素，第一轮将 6 个元素中最大的元素「11」交换至数组的最后；第二轮将剩下 5 个元素中的最大元素「8」交换至「11」的前面。依次执行，直到所有的元素比较完成。总的来说，冒泡排序升序排列就是不断将最大的元素移动到后面的过程。

<div align="center">  <img src="https://s1.ax1x.com/2020/03/24/8O3AsA.gif" width="60%"/> </div><br>

#### 3. 核心代码

```java
public static void bubbleSort(int[] arr) {
  
    if (arr == null || arr.length < 2) return;
  
    for (int i = arr.length - 1; i > 0; i--) {
        for (int j = 0; j < i; j++) {
            if (arr[j] > arr[j + 1]) {
                swap(arr, j, j + 1);
            }
        }
    }
}
```

从上面的动图可以看出，当将最大的两个元素交换到正确位置时，前面的4个元素已经排序完成，为了减少不必要的比较，可以增加一个标志位，判断排序的结果，只要有一个轮次没有发生交换，表示数组已经排序完成，不再执行下面的轮次。

#### 4. 算法分析

###### Ⅰ. 时间复杂度

若数组为倒序，即所有的轮次都必须执行完（最坏情况），比较次数为 `n-1 + n-2 +...+ 1 = n(n-1)/2`，交换次数与比较次数相同，所以时间复杂度为$O(N^2)$。

###### Ⅱ. 额外空间复杂度

未借助其它辅助空间，$O(1)$。

###### Ⅲ、稳定性

冒泡排序每次都比较相邻的元素，只有前者比后者大才交换位置，相同大小的数，原本在前面，不可能「冒」到另一个后面。

所以，冒泡排序是一种稳定的排序方法。


## 三、插入排序

#### 1. 思想

**维护一个有序列，不停把新元素插入其中。**

插入排序的工作方式像许多人排序一手扑克牌，开始时，我们的左手为空并且桌子上的牌面朝下。然后，我们每次从桌子上拿走一张牌并将它插入左手中正确的的位置，从右到左将它与已在手中的每张牌进行比较，选择正确位置进行插入，这样，当桌子上的牌全部插入到手中，手中的扑克牌则是所有扑克牌的已排序状态。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/24/8O3aJU.png" width="80%"/> </div><br>

#### 3. 核心代码

```java
public static void insertionSort(int[] arr) {
  
    if (arr == null || arr.length < 2) return;
  
        for (int i = 0; i < arr.length - 1; i++) {
            for (int j = i + 1; j > 0 && arr[j] < arr[j - 1]; j--) {
                swap(arr, j, j - 1);
            }
        }
    }
}
```

#### 4. 算法分析

###### Ⅰ. 时间复杂度

最坏的情况是待排序数组完全逆序，也就是每一次插入都要和有序集合中的所有元素进行比较，插入第二个元素时需要与前 1 个元素进行比较，插入第3个元素时，需要与前 2 个元素比较，以此类推，插入前 N 个元素需要与前 N-1 个元素比较。`1 + 2 + 3 + ... + (N-1)`，结果为 (N-1)*N/2，即 $O(N^2)$。

最好的情况是待排序数组已经是有序的了，每插入一个元素只需要和前一个元素进行比较，复杂度为 $O(N)$。

###### Ⅱ. 额外空间复杂度

未借助其它辅助空间，$O(1)$。

###### Ⅲ. 稳定性

插入排序是从前向后依次选择目标向前插入，向前插入时，遇到相同的数，插在相同的数后边。

所以，插入排序是稳定的排序算法。

## 四、希尔排序

#### 1. 思想

**通过粗粒度的插入排序改善数列的有序程序，逐步细化，直到间隔为 1。**

希尔排序是对直接插入排序的改进算法，在使用直接排序算法时，如果序列很长，且无序程度较高，向前插入一次可能导致移动的操作很大。如果原序列相对有序（小的数大致在前，大的数大致在后），则能缓解这一问题。希尔排序就是在插入排序的之前进行了粗略的排序，使原序列相对有序。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/24/8O3RJO.png" width="80%"/> </div><br>

间隔 D 有很多中选择，此处使用的是希尔增量 D = n/2, n/4 ,..., 1。

#### 3. 核心代码

```java
public static void shellSort(int[] arr){
    
    if(arr == null || arr.length < 2) return;

    for (int D = arr.length / 2; D >= 1; D /= 2) {
        for (int i = D; i < arr.length; i++) {
            int j = i;
            int temp = arr[j]; // 要插入的值

            while (j - D >= 0 && temp < arr[j - D]) {
                arr[j] = arr[j - D];
                j -= D;
            }
            arr[j] = temp;
        }
    }
}
```

#### 4. 算法分析

###### Ⅰ. 时间复杂度

希尔排序最坏的时间复杂度依然为 $O(N^2)$，但其能够有效改善直接插入排序序列无序且长度大时的大长度数列移位。希尔排序中对于增量序列的选择十分重要，直接影响到希尔排序的性能，本文使用的是希尔增量，还有 Hibbard 增量，时间复杂度为 $O(N^{1.5})$。

###### Ⅱ. 额外空间复杂度

未借助其它辅助空间，$O(1)$。

###### Ⅲ. 稳定性

与直接插入排序不同，希尔排序中的分组插入可能导致顺序移位。

所以，插入排序是稳定的排序算法。


## 五、归并排序

#### 1. 思想

**先拆分，后按顺序合并。**

归并排序是创建在归并操作上的一种有效的排序算法，该算法是采用分治法的一个非常典型的应用。主要的思想为「分而治之」，将大问题化解成一个个的小问题，逐个求解，最后将这些结果组合到一起。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/24/8O3LY8.png" width="90%"/> </div><br>

#### 3. 核心代码

```java
public static void mergeSort(int[] arr) {
    if (arr == null || arr.length < 2) {
        return;
    }
    separate(arr, 0, arr.length - 1);
}

// 分治
public static void separate(int[] arr, int l, int r) {
    if (l == r) return;
    int mid = l + (r - l) / 2;
    separate(arr, l, mid);
    separate(arr, mid + 1, r);
    merge(arr, l, mid, r);
}

// 合并
public static void merge(int[] arr, int l, int mid, int r) {
    int[] helper = new int[r - l + 1];
    int index = 0;
    int p1 = l;
    int p2 = mid + 1;

    while (p1 <= mid && p2 <= r) {
        helper[index++] = arr[p1] > arr[p2] ? arr[p2++] : arr[p1++];
    }
    while (p1 <= mid) {
        helper[index++] = arr[p1++];
    }
    while (p2 <= r) {
        helper[index++] = arr[p2++];
    }
    for (int i = 0; i < helper.length; i++) {
        arr[l + i] = helper[i];
    }
}
```

#### 4. 算法分析

###### Ⅰ. 时间复杂度

每次合并的操作时间复杂度为 $O(N)$，完全二叉树深度为 $log_2N$。最好、最坏、平均时间复杂度为均为 $O(NlogN)$。

平均速度比快速排序慢，虽然都是$O(NlogN)$，但是归并排序操作次数正比于数列长度，常数系数更大。

###### Ⅱ. 额外空间复杂度

合并时借助了一个数组进行外排，$O(N)$。

###### Ⅲ. 稳定性

每次归并确保相同的数，第一个数列的先加入，所以归并排序是稳定的，这是相对快速排序的一大优势。

## 六、快速排序

#### 1. 思想

**选择一个基准数，使得基准数左边小右面大，左右再次迭代。**

与归并排序相同，快速排序也是用分支的思想。首先从数组中随机选取一个元素最为基准数，将该元素交换的数组的最后一位。将所有小于基准数的放置在左边，大于基准数的放置在右边，中间剩下的是等于基准数的元素，这时将基准数交换到中间，形成了一个以基准数为中心左右分治的数组，左面的元素都小于基准数，右边的元素都大于基准数。这时，分别将左右两个大于基准数与小于基准数的序列递归重新执行上述步骤，即可得到最终有序的数组。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8OXYB6.png" width="90%"/> </div><br>

#### 3. 核心代码

```java
public static void sort(int[] arr) {
    if (arr == null || arr.length < 2) {
        return;
    }
    quickSort(arr, 0, arr.length - 1);
}
public static void quickSort(int[] arr, int l, int r) {
    if (l < r) {
        // 随机选取一个基准数
        Main.swap(arr, l + (int)(Math.random() * (r - l + 1)), r);
        // 小于区域[0,p[0]) 大于区域(p[1],r]
        int[] partition = partition(arr, l, r);
        quickSort(arr, l, partition[0] - 1);
        quickSort(arr, partition[1] + 1, r);
    }
}
// 以基准数为界，大的在右边，小的在左边   基准数放置在数组的最后一位
public static int[] partition(int[] arr, int l, int r) {
    int less = l - 1;
    int more = r;
    while (l < more) {
        if (arr[l] < arr[r]) {
            swap(arr, ++less, l++);
        } else if (arr[l] > arr[r]) {
            swap(arr, --more, l);
        } else {
            l++;
        }
    }
    Main.swap(arr, more, r);
    // 返回值分别为小于序列与大于序列的分界线
    return new int[]{less + 1, more};
}
```

#### 4. 算法分析

###### Ⅰ. 时间复杂度

最好时间复杂度：$O(logN)$。每次选的基准数正好等分当前数列

最坏时间复杂度：$O(N^2)$。待排序数组为逆序时，每次划分只少了一个元素。

平均时间复杂度：$O(NlogN)$。

###### Ⅱ. 额外空间复杂度

未借助其它辅助空间，$O(1)$。

###### Ⅲ. 稳定性

从前到后扫描，将第一个大数置于后方的大端，如果后面紧邻相等的大数，则被置于该数前面。

所以，快速排序是不稳定的。


## 七、堆排序

#### 1. 思想

**通过建立最大/小堆 + 下沉元素找到最值。**

在学习堆排序之前，先了解一下什么是堆。堆是一个数组，可以被看成一个近似的完全二叉树，如下图所示，树上的每一个结点对应数组中的一个元素。除了最底层外，该树是完全充满的，而且是从左到右填充。在不越界的情况下，任何一个节点的左叶子节点在数组中的下标为 `2i+1` ，右叶子节点在数组中的下标为 `2i+2` ，父节点在数组中的下标为 `(i-1)/2` 。

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8OXaND.png" width="50%"/> </div><br>

堆可以分为两种形式：最大堆和最小堆。

**最大堆：** 每个节点的值都大于等于其左右子节点的值。 

**最小堆：** 每个节点的值都小于等于其左右子节点的值。

堆排序算法的思想就是，将待排序数组构造成一个最大堆，这时，堆的根节点就是最大值。将其与末尾节点交换，然后将剩下的 n-1 个元素重新构造成一个最大堆，这样就得到了第二大的元素，以此类推，便能够得到一个有序序列。

#### 2. 图解过程

###### Ⅰ. 建立最大堆

首先，我们有一个待排序数组，如何把这一数组调整成最大堆的形式呢？这个过程就是建立最大堆的过程。例如，数组 `[ 3 6 2 1 0 4 ]`，可以先将第一个元素 3 看成一个堆，之后将第二个元素 6 加入堆中，将其与父节点 3 进行比较，若比父节点 3 大，则与之交换，一直向上比较直到到达根节点或不比父节点大为止。这样的一轮操作，就将元素 6 插入到了堆中，之后又调整成了最大堆。以此类推，不断将后面的元素插入到堆中，并调整成最大堆，直到数组中的元素都插入到了堆，整个数组就成为了最大堆的排序形式。

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8OXcHf.png" width="85%"/> </div><br>

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8OXIvn.png" width="85%"/> </div><br>

###### Ⅱ. 下沉元素

将待排序数组建立成最大堆顺序后，最大堆的根节点即为数组中的最大元素。这时，将根节点与堆的最后一个节点进行交换，也就是将最大元素放到数组的最后，再将 0 到 n-2 重新调整为最大堆，也就是将新的根节点下沉到正确的位置。重复上述步骤，将最大的元素沉到数组的后面，最后，就得到了一个升序排列的数组。

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8OXO5F.png" width="85%"/> </div><br>

#### 3. 核心代码

```java
public static void heapSort(int[] arr) {
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

#### 4. 算法分析

###### Ⅰ. 时间复杂度

堆排序由建立最大堆与下沉元素并重建堆两个部分组成。建立最大堆的时间复杂度为 $O(n)$，在下沉元素并创建堆的过程中，需要下沉 n - 1 个元素，重建堆就是逐渐下沉的过程，根据完全二叉树的性质，每个元素都下沉 $log_2(n-1),log_2(n-2)...1$ 层，近似为 $nlogn$，所以堆排序的时间复杂度为 $O(nlogn)$。

###### Ⅱ. 额外空间复杂度

未借助其他辅助空间。

###### Ⅲ. 稳定性

堆排序是不稳定排序。

例如序列：19 5 3 5，19 是最大堆的堆顶，将 19 与最后一个元素 5 交换后，最后一个元素 5 交换到了前一个元素 5 的前面。


## 八、计数排序

#### 1. 思想

**开辟额外空间，统计每个元素的数量。**

计数排序是一种不基于比较的排序算法，主要思想是计算出待排序序列的最大值 maxValue 与 最小值 minValue，开辟一个长度为 maxValue - minValue + 1 的额外空间，统计待排序序列中每个元素的数量，记录在额外空间中，最后遍历一遍额外空间，按照顺序把每个元素赋值到原始序列中。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8OXzvR.png" width="80%"/> </div><br>

#### 3. 核心代码

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

#### 4. 算法分析

###### Ⅰ. 时间复杂度

当待排序元素是 n 个 0 到 k 之间的整数时，时间复杂度是 $O(n+k)$。

###### Ⅱ. 额外空间复杂度

计数排序需要两个额外的数组，分别用于记录元素数量与排序结果，$O(n+k)$。

###### Ⅲ.稳定性

生成排序结果的过程，是从后向前遍历的，也就是说原始数组中先出现的元素依旧会在前面。

所以，计数排序是稳定的排序算法。


## 九、桶排序

#### 1. 思想

**划分多个范围相同的区间，每个自区间自排序，最后合并。**

桶排序是计数排序的扩展版本，计数排序可以看成每个桶只存储相同元素，而桶排序每个桶存储一定范围的元素，通过映射函数，将待排序数组中的元素映射到各个对应的桶中，对每个桶中的元素进行排序，最后将非空桶中的元素逐个放入原序列中。

桶排序需要尽量保证元素分散均匀，否则当所有数据集中在同一个桶中时，桶排序失效。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8Ojk5D.png" width="80%"/> </div><br>

#### 3. 核心代码

```java
public static void bucketSort(int[] arr){
    
    // 计算最大值与最小值
    int max = Integer.MIN_VALUE;
    int min = Integer.MAX_VALUE;
    for(int i = 0; i < arr.length; i++){
        max = Math.max(max, arr[i]);
        min = Math.min(min, arr[i]);
    }
    
    // 计算桶的数量
    int bucketNum = (max - min) / arr.length + 1;
    ArrayList<ArrayList<Integer>> bucketArr = new ArrayList<>(bucketNum);
    for(int i = 0; i < bucketNum; i++){
        bucketArr.add(new ArrayList<Integer>());
    }
    
    // 将每个元素放入桶
    for(int i = 0; i < arr.length; i++){
        int num = (arr[i] - min) / (arr.length);
        bucketArr.get(num).add(arr[i]);
    }
    
    // 对每个桶进行排序
    for(int i = 0; i < bucketArr.size(); i++){
        Collections.sort(bucketArr.get(i));
    }
    
    // 将桶中的元素赋值到原序列
    int index = 0;
    for(int i = 0; i < bucketArr.size(); i++){
        for(int j = 0; j < bucketArr.get(i).size(); j++){
            arr[index++] = bucketArr.get(i).get(j);
        }
    }  
}
```

#### 4. 算法分析

###### Ⅰ. 时间复杂度

O(N + C)

对于待排序序列大小为 N，共分为 M 个桶，主要步骤有：

- N 次循环，将每个元素装入对应的桶中
- M 次循环，对每个桶中的数据进行排序（平均每个桶有 N/M 个元素）
一般使用较为快速的排序算法，时间复杂度为 $O(NlogN)$，实际的桶排序过程是以链表形式插入的。

整个桶排序的时间复杂度为：

$O(N)+O(M*(N/Mlog(N/M)))=O(N(log(N/M)+1))$

当 N = M 时，复杂度为 $O(N)$

###### Ⅱ. 额外空间复杂度

O(N + M)

###### Ⅲ. 稳定性

桶排序的稳定性取决于桶内排序使用的算法。

## 十、基数排序

#### 1. 思想

基数排序可以看成是桶排序的扩展，以整数排序为例，主要思想是将整数按位数划分，准备 10 个桶，代表 0 - 9，根据整数个位数字的数值将元素放入对应的桶中，之后按照输入赋值到原序列中，依次对十位、百位等进行同样的操作，最终就完成了排序的操作。

#### 2. 图解过程

<div align="center">  <img src="https://s1.ax1x.com/2020/03/25/8Ojurt.png" width="100%"/> </div><br>

#### 3. 核心代码

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

#### 4. 算法分析

###### Ⅰ. 时间复杂度

k 为关键字个数，本文的上述距离关键字为 2 个，分别是个位和十位；m 为 关键字的取值范围，本文的举例关键字取值范围 m 为 10（0 - 9）；n 为待排序序列的元素数量。

总共遍历 k 遍，每一遍包含：将每个元素放进桶中（n） 与 将桶中元素收回到原序列中（m），所以时间复杂度为 O(k*(n+m))。

###### Ⅱ. 额外空间复杂度

m 个桶与存放 n 个元素的空间，O(n + m)。

###### Ⅲ. 稳定性

基数排序不会改变相同元素之间的相对位置，仔细思考一下过程，元素收回的过程中是从后向前进行的。

所以，基数排序是稳定的排序算法。
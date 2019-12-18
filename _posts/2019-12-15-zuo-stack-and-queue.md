---
layout: article
title: 左神 — 栈和队列
tags: 左神算法

lang: zh-Hans
key: Zuo_Stack_And_Queue
pageview: true
toc: true
show_subscribe: false
---

## 1. 设计一个有 getMin 功能的栈

#### 题目

实现一个特殊的栈，在实现栈的基本功能的基础上，再实现返回栈中最小元素的操作。

#### 要求

1. pop、push、getMin 操作的时间复杂度都是 `O(1)`
2. 设计的栈类型可以使用现成的栈结构

#### 思路一

使用两个栈，一个栈保存当前栈中的元素，一个栈保存最小值。

```java
private Stack<Integer> stackData = new Stack<Integer>();
private Stack<Integer> stackMin = new Stack<Integer>();

public void push(int x) {
    stackData.push(x);
    if (stackMin.isEmpty() || x < getMin()) {
        stackMin.push(x);
    } else {
        stackMin.push(getMin());
    }
}

public void pop() {
    stackData.pop();
    stackMin.pop();
}

public int top() {
    return stackData.peek();
}

public int getMin() {
    return stackMin.peek();
}
```

#### 思路二

只是用一个栈，将最小值和当前元素都存放进去。

```java
Stack<Integer> stack = new Stack<Integer>();
int min = Integer.MAX_VALUE;
public void push(int x) {
    if (x <= min){
        stack.push(min);
        min = x;
    }
    stack.push(x);
}

public void pop() {
    if (stack.pop() == min){
        min = stack.pop();
    }
}

public int top() {
    return stack.peek();
}

public int getMin() {
    return min;
}
```

## 2. 由两个栈组成的队列

#### 题目

编写一个类，用两个栈实现队列，支持队列的基本操作（add、pull、peek）。

#### 思路

两个栈，push 进栈 1 中，弹出时将栈 1 全部元素导入到栈 2 中，再从栈 2 pop。

```java
private Stack<Integer> stackPush = new Stack<Integer>();
private Stack<Integer> stackPop = new Stack<Integer>();

private void pushToPop() {
    if (stackPop.isEmpty()) {
        while (!stackPush.isEmpty()) {
            stackPop.push(stackPush.pop());
        }
    }
}

public void push(int x) {
    stackPush.push(x);
}

public int pop() {
    pushToPop();
    return stackPop.pop();
}

public int peek() {
    pushToPop();
    return stackPop.peek();
}

public boolean empty() {
    return stackPush.isEmpty() && stackPop.isEmpty();
}
```

## 3. 如何仅用递归函数和栈操作逆序一个栈

#### 题目

一个栈依次压入 `1、2、3、4、5`，那么从栈顶到栈底分别为 `5、4、3、2、1`。将这个栈转置后，从栈顶到栈底为 `1、2、3、4、5`，也就是实现栈中元素的逆序，但是只能用递归函数来实现，不能用其他数据结构。

#### 思路

1. 将栈底元素返回并删除；
2. 实现栈的逆序。

```java
// 返回栈底元素并返回
public static int getAndRemoveLastElement(Stack<Integer> stack) {
    int result = stack.pop();
    if (stack.isEmpty()) {
        return result;
    } else {
        int last = getAndRemoveLastElement(stack);
        stack.push(result);
        return last;
    }
}

// 逆序栈
public static void reverse(Stack<Integer> stack) {
    if (stack.isEmpty()) return;
    int i = getAndRemoveLastElement(stack);
    reverse(stack);
    stack.push(i);
}
```

## 4. 猫狗队列

#### 题目

实现一种宠物、猫、狗的队列结构，宠物与猫、狗是继承关系。

1. 用户可以调用 add 方法将 cat 或者 dog 放入队列中
2. 用户可以调用 pollAll 方法将队列中的 cat 和 dog 按照进队列的先后顺序依次弹出
3. 用户可以调用 pollDog 方法将队列中的 dog 按照进队列的先后顺序依次弹出
4. 用户可以调用 pollCat 方法将队列中的 cat 按照进队列的先后顺序依次弹出
5. 用户可以调用 isEmpty 方法检查队列中是否还有 dog 或 cat
6. 用户可以调用 isDogEmpty 方法检查队列中是否还有 dog
7. 用户可以调用 isCatEmpty 方法检查队列中是否还有 cat

#### 思路

构造新的 PetEnterQueue 类，其中 pet 是用户原有的实例，count 是这个实例的时间戳。

```java
public class PetEnterQueue {
    private Pet pet;
    private long count;

    public PetEnterQueue (Pet pet, long count) {
        this.pet = pet;
        this.count = count;
    }

    public Pet getPet() {
        return this.pet;
    }

    public long getCount() {
        return this.count;
    }

    public String getEnterPetType() {
        return this.pet.getPetType();
    }
}
```

我们需要构造的队列其实是 PetEnterQueue 类的实例。有一个不断累加的数据项用来表示实例进队列的时间；同时有两个队列，一个是只放 dog 类实例的队列 dogQ，另一个是只放 cat 类实例的队列 catQ，通过比较时间戳大小来依次弹出 pet。

```java
public class DogCatQueue {
	private Queue<PetEnterQueue> dogQ;
	private Queue<PetEnterQueue> catQ;
	private long count;
	
	public DogCatQueue() {
		dogQ = new LinkedList<>();
		catQ = new LinkedList<>();
		count = 0;
	}
	public void add(Pet pet) {
		if (pet.getType().equals("dog"))
			this.dogQ.add(new PetEnterQueue(pet,this.count++));
		else if (pet.getType().equals("cat"))
			this.catQ.add(new PetEnterQueue(pet,this.count++));
		else
			throw new RuntimeException("erro,no cat or dog");
	}
	public Pet pollAll(){
		if (!this.dogQ.isEmpty() && !this.catQ.isEmpty()) {
			if (this.dogQ.peek().getCount() < this.catQ.peek().getCount())
				return this.dogQ.poll().getPet();
			else
				return this.catQ.poll().getPet();
		}
		else if (!this.dogQ.isEmpty())
			return this.dogQ.poll().getPet();
		else if (!this.catQ.isEmpty())
			return this.catQ.poll().getPet();
		else
			throw new RuntimeException("erro,queue is empty");
	}
	public Dog pollDog() {
		if (!this.isDogQueueEmpty())
			return (Dog) this.dogQ.poll().getPet();
		else
			throw new RuntimeException("Dog quque is empty");
	}
	public Cat pollCat() {
		if(!this.isCatQueueEmpty())
			return (Cat)this.catQ.poll().getPet();
		else
			throw new RuntimeException("Cat queue is empty");
	}
	public boolean isDogQueueEmpty() {
		return this.dogQ.isEmpty();
	}
	public boolean isCatQueueEmpty() {
		return this.catQ.isEmpty();
	}
}
```

## 5. 用一个栈实现另一个栈的排序

#### 题目

将一个栈中的元素从顶到底按从大到小排序，只允许申请一个栈，可以申请新的变量，但不能申请额外的数据结构。

#### 思路

将要排序的栈记为 stack，辅助栈记为 help。在 stack 上执行 pop 操作，弹出的元素记为 cur。

- 如果 cur 小于或等于 help 栈顶元素，则将 cur 直接压入 help；
- 如果 cur 大于 help 的栈顶元素，则将 help 的元素逐一弹出，逐一压入 stack，直到 cur 小于或等于 help 的栈顶元素，再将 cur 压入 help。

```java
public static void sortStackByStack(Stack<Integer> stack){
    Stack<Integer> help = new Stack<Integer>();
    while (!stack.isEmpty()) {
        int cur = stack.pop();
        while (!help.isEmpty() && help.peek()<cur) {
            stack.push(help.pop());
        }
        help.push(cur);
    }
    while (!help.isEmpty()) {
        stack.push(help.pop());
    }
}
```

## 6. 用栈来求解汉诺塔问题

#### 题目

汉诺塔问题比较经典，这里修改一下游戏规则：现在限制不能从最左侧的塔直接移动到最右侧，也不能从最右侧直接移动到最左侧，而是必须经过中间。求当塔有 N 层的时候，打印最优移动过程和最优移动总步数。

#### 递归解法

```java
public int hanoiProbleml(int num, String left, String mid, String right) {
    if (num < 1) {
        return 0;
    }
    return process(num, left, mid, right, left, right);
}

public int process(int num, String left, String mid, String right, String form, String to) {
    if (num == 1) {
        if (form.equals(mid) || to.equals(mid)) {
            System.out.println("Move 1 from" + form + "to" + mid);
            return 1;
        } else {
            System.out.println("Move 1 from" + form + "to" + mid);
            System.out.println("Move 1 from" + mid + "to" + to);
            return 2;
        }
    }
    if (form.equals(mid) || to.equals(mid)) {
        String another = (form.equals(left) || to.equals(left)) ? right : left;
        int part1 = process(num - 1, left, mid, right, form, another);
        int part2 = 1;
        System.out.println("Move" + num + "form" + form + "to" + to);
        int part3 = process(num - 1, left, mid, right, another, to);
        return part1 + part2 + part3;
    } else {
        int part1 = process(num - 1, left, mid, right, form, to);
        int part2 = 1;
        System.out.println("Move" + num + "form" + form + "to" + mid);
        int part3 = process(num - 1, left, mid, right, to, form);
        int part4 = 1;
        System.out.println("Move" + num + "form" + mid + "to" + to);
        int part5 = process(num - 1, left, mid, right, form, to);
        return part1 + part2 + part3 + part4 + part5;
    }
}
```

#### 栈解法

- 小压大原则
- 相邻不可逆原则

```java
public enum Action {
    No, LToM, MToL, MToR, RToM
}

public int hanoiProblem2(int num, String left, String mid, String right) {
    Stack<Integer> ls = new Stack<>();
    Stack<Integer> ms = new Stack<>();
    Stack<Integer> rs = new Stack<>();
    ls.push(Integer.MAX_VALUE);
    rs.push(Integer.MAX_VALUE);
    ms.push(Integer.MAX_VALUE);
    for (int i = num; i > 0; i--) {
        ls.push(i);
    }
    Action[] record = {Action.No};
    int step = 0;
    while (rs.size() != num + 1) {
        step += fStackToStack(record, Action.MToL, Action.LToM, ls, ms, left, mid);
        step += fStackToStack(record, Action.LToM, Action.MToL, ms, ls, mid, left);
        step += fStackToStack(record, Action.RToM, Action.MToR, ms, rs, mid, right);
        step += fStackToStack(record, Action.MToR, Action.RToM, rs, ms, right, mid);
    }
    return step;
}

public static int fStackToStack(Action[] record, Action preNoAct, Action nowAct, Stack<Integer> fStack,
                                Stack<Integer> tStack, String from, String to) {
    if (record[0] != preNoAct && fStack.peek() < tStack.peek()) {
        tStack.push(fStack.pop());
        System.out.println("Move" + tStack.peek() + "from" + from + "to" + to);
        record[0] = nowAct;
        return 1;
    }
    return 0;
}
```

## 7. 生成窗口最大值数组

#### 题目

有一个整型数组 arr 和一个大小为 w 的窗口从数组的最左边滑到最右边，窗口每次向右滑一个位置。

实现一个函数：

- 输入：整型数组 arr，窗口大小为 w。
- 输出：一个长度为 n-w+1 的数组 res，res[i] 表示每一种窗口状态下的最大值。

#### 思路

使用双端队列实现窗口最大值的更新，双端队列中存放数组 arr 中的下标，在双端队列 qmax 的队头用来保存当前的最大值的坐标，当队头的下标等于 i-w 的时候，就过期，直接弹出。

```java
public static int[] getMaxWindow(int[] arr,int w){
    if (arr == null || arr.length < w || w < 1){
        return null;
    }
    LinkedList<Integer> qmax = new LinkedList<Integer>();   // 记录最大值的更新
    int[] res = new int[arr.length-w+1];                    // 记录结果
    int index = 0;
    for (int i = 0; i < arr.length; i++) {
        while (!qmax.isEmpty() && arr[qmax.peekLast()] <= arr[i]){
            qmax.pollLast();
        }
        qmax.addLast(i);

        // 过期计算
        if (qmax.peekFirst() == i-w){
            qmax.pollFirst();
        }
        // 记录res值
        if (i >= w - 1){
            res[index++]=arr[qmax.peekFirst()];
        }
    }
    return res;
}
```

## 8. 单调栈结构

#### 题目

给定一个不含有重复值的数组 arr，找到每一个 i 位置左边和右边离 i 位置最近且值比 arr[i] 小的位置，返回所有位置相应的信息，-1 表示不存在。

#### 思路

维护一个单调栈，从栈顶到栈底单调递减，若当前遍历到的位置比栈顶元素小，则弹出栈顶元素 x，那么栈中 x 位置下面的位置就是 x 左边离 x 最近且值比 arr[x] 小的位置；当前遍历到的位置就是 x 位置右边离 x 位置最近且值比 arr[x] 小的位置。

```java
public int[][] getNearLessNoRepeat(int[] arr) {
    int[][] res = new int[arr.length][2];
    Stack<Integer> stack = new Stack<>();
    for (int i = 0; i < arr.length; i++) {
        while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
            int popIndex = stack.pop();
            int leftLessIndex = stack.isEmpty() ? -1 : stack.peek();
            res[popIndex][0] = leftLessIndex;
            res[popIndex][1] = i;
        }
        stack.push(i);
    }
    while (!stack.isEmpty()) {
        int popIndex = stack.pop();
        int leftLessIndex = stack.isEmpty() ? -1 : stack.peek();
        res[popIndex][0] = leftLessIndex;
        res[popIndex][1] = -1;
    }
    return res;6
}
```

#### 问题进阶

数组 arr 可能有重复值。

#### 思路

如果栈中相邻元素值相同，则压在一起，他们的结果相同。

```java
public int[][] getNearLess(int[] arr) {
    int[][] res = new int[arr.length][2];
    Stack<List<Integer>> stack = new Stack<>();
    for (int i = 0; i < arr.length; i++) {
        while (!stack.isEmpty() && arr[stack.peek().get(0)] > arr[i]) {
            List<Integer> popIs = stack.pop();
            // 取位于下面位置的列表中，最晚加入的那个
            int leftLessIndex = stack.isEmpty() ? -1 : stack.peek().get(steeck.peek().size() - 1);
            for (Integer popi : popIs) {
                res[popi][0] = leftLessIndex;
                res[popi][1] = i;
            }
        }
        if (!stack.isEmpty() && arr[stack.peek().get(0)] == arr[i]) {
            stack.peek().add(Integer.valueOf(i));
        } else {
            ArrayList<Integer> list = new ArrayList<>();
            list.add(i);
            stack.push(list);
        }
    }
    while (!stack.isEmpty()) {
        List<Integer> list = stack.pop();
        int leftLessIndex = stack.isEmpty() ? -1 : stack.peek().get(steeck.peek().size() - 1);
        for (Integer popi : popIs) {
            res[popi][0] = leftLessIndex;
            res[popi][1] = -1;
        }
    }
    return res;
}
```

## 9. 求最大子矩阵的大小

#### 题目

给定一个整型矩阵 map，其中的值只有 0 和 1 两种，求其中全是 1 的所有矩形区域中，最大的矩形区域为 1 的数量。

```
Input:
[
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6
```

#### 思路

以每一行做切割，统计以当前行作为底的情况下，每个位置往上 1 的数量，使用数组 height 表示，之后利用单调栈计算每一行 height 的最大矩形区域，最后求每一行的最大值即为最后结果。

```java
public int maxRecSize(int[][] map) {
    if (map ==null || map.length == 0 || map[0].length == 0) {
        return 0;
    }
    int maxArea = 0;
    int[] height = new int[map[0].length];
    for (int i = 0; i < map.length; i++) {
        for (int j = 0; j < map[0].length; j++) {
            height[j] = map[i][j] == 0 ? 0 : height[j] + 1;
        }
        maxArea = Math.max(maxRecFromBottom(height), maxArea);
    }
    return maxArea;
}

public int maxRecFromBottom(int[] height) {
    if (height == null || height.length == 0) {
        return 0;
    }
    int maxArea = 0;
    Stack<Integer> stack = new Stack<Integer>();
    for (int i = 0; i < height.length; i++) {
        while (!stack.isEmpty() && height[i] <= height[stack.peek()]) {
            int j = stack.pop();
            int k = stack.isEmpty() ? -1 : stack.peek();
            int curArea = (i - k - 1) * height[j];
            maxArea = Math.max(maxArea, curArea);
        }
        stack.push(i);
    }
    while (!stack.isEmpty()) {
        int j = stack.pop();
        int k = stack.isEmpty() ? -1 : stack.peek();
        int curArea = (height.length - k - 1) * height[j];
        maxArea = Math.max(maxArea, curArea);
    }
    return maxArea;
}
```

## 10. 最大值减去最小值小于或等于 num 的子数组数量

#### 题目

给定数组 arr 和整数 num，共返回有多少个子数组满足：`max(arr[i..j]) - min(arr[i..j]) <= num`，`max(arr[i..j])` 表示子数组 `arr[i..j]` 中的最大值，`min(arr[i..j])` 表示子数组 `arr[i..j]` 中的最小值。

#### 思路

维护两个最大与最小值更新的双端队列。

关键结论：
- 如果子数组 `arr[i..j]` 满足条件，即 `max(arr[i..j]) - min(arr[i..j]) <= num`，那么 `arr[i..j]` 中的每一个子数组都满足条件。
- 如果子数组 `arr[i..j]` 不满足条件，那么所有包含 `arr[i..j]` 的子数组都不满足条件。

```java
public int getNum(int[] arr, int num) {
    if (arr == null || arr.length == 0 || num < 0) {
        return 0;
    }
    LinkedList<Integer> qmin = new LinkedList<Integer>();
    LinkedList<Integer> qmax = new LinkedList<Integer>();

    int i, j, res = 0;
    while (i < arr.length) {
        while (j < arr.length) {
            if (qmin.isEmpty() || qmin.peekLast() != j) {
                while (!qmin.isEmpty() && arr[qmin.peekLast()] >= arr[j]) {
                    qmin.pollLast();
                }
                qmin.addLast(j);
                while (!qmax.isEmpty() && arr[qmax.peekLast()] <= arr[j]) {
                    qmax.pollLast();
                }
                qmax.addLast(j);
            }
            if (arr[qmax.getFirst()] - arr[qmin.getFirst()] > num) {
                break;
            }
            j++;
        }
        res += j - i;
        if (qmin.peekFirst() == i) {
            qmin.poolFirst();
        }
        if (qmax.peekFirst() == i) {
            qmax.poolFirst();
        }
        i++;
    }
    return res;
}
```
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

**题目**

实现一个特殊的栈，在实现栈的基本功能的基础上，再实现返回栈中最小元素的操作。

**要求**

1. pop、push、getMin 操作的时间复杂度都是 `O(1)`
2. 设计的栈类型可以使用现成的栈结构

**思路一**

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

**思路二**

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
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

## 2. 由两个栈组成的队列

**题目**

编写一个类，用两个栈实现队列，支持队列的基本操作（add、pull、peek）。

**思路**

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

**题目**

一个栈依次压入 `1、2、3、4、5`，那么从栈顶到栈底分别为 `5、4、3、2、1`。将这个栈转置后，从栈顶到栈底为 `1、2、3、4、5`，也就是实现栈中元素的逆序，但是只能用递归函数来实现，不能用其他数据结构。

**思路**

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

**题目**

实现一种宠物、猫、狗的队列结构，宠物与猫、狗是继承关系。

1. 用户可以调用 add 方法将 cat 或者 dog 放入队列中
2. 用户可以调用 pollAll 方法将队列中的 cat 和 dog 按照进队列的先后顺序依次弹出
3. 用户可以调用 pollDog 方法将队列中的 dog 按照进队列的先后顺序依次弹出
4. 用户可以调用 pollCat 方法将队列中的 cat 按照进队列的先后顺序依次弹出
5. 用户可以调用 isEmpty 方法检查队列中是否还有 dog 或 cat
6. 用户可以调用 isDogEmpty 方法检查队列中是否还有 dog
7. 用户可以调用 isCatEmpty 方法检查队列中是否还有 cat

**思路**

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
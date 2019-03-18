---
layout: article
title: Java 函数式编程
tags: Java

lang: zh-Hans
key: Java_Functional_programming_for_the_masses
pageview: true
comment: false
toc: true
sharing: true
---

## 一、概述

函数式编程是种编程范式。与之相对的是命令是编程，来做一下对比你就可以更好的理解函数式编程了。
比如要计算 `(1 + 2) * 3 – 4` 的结果。

##### 命令式编程

为了体现命令式和函数式的区别，所以使用下面这种方法：

```java
int a = 1 + 2;
int b = a * 3;
int c = b - 4;
```

##### 函数式编程

```java
subtract(multiply(add(1,2), 3), 4);
```

总的来说，函数式编程使用的是 函数 + 表达式，不使用语句，因此函数式编程的代码量会更精简。在 java 8 里面开始推荐使用函数式编程了，并且也更新了很多内容便于开发者使用函数式编程。

## 二、lambda 表达式

### 1. 函数式接口

在一个接口中只有一个抽象方法的接口被称为函数式接口，例如 `java.lang.Runnable`，`java.util.concurrent.Callable`。java 8 中新增了 @FunctionalInterface 注解来标注一个函数式接口。

### 2. default 方法

java 8 中新增了 default 方法， java 8 之前接口中的方法必须都是抽象的，在 java 8 中允许接口中定义非抽象方法，在接口中的非抽象方法上使用 default 修饰即可，比如在 java8 中新增了一个函数式接口：`java.util.function`。java8 中打破了接口中的方法必须都是抽象的这一规范，有一个好处就是可以提高程序的兼容性，在 java.lang.Iterable 接口中新增了 forEach 方法，该方法就是使用 default 修饰的。

自定义函数式接口：
```java
@FunctionalInterface
public interface MyInterface {
  void m1(int a, int b);

  default String m2(String s){return null;};

  default void m3(){};
}
```

### 3. lambda 表达式格式

可以将 lambda 看做是一个匿名方法， **lambda 只能用于函数式接口**。

lambda 表达式实际上就是重写了接口中的抽象方法，在函数式接口中只有一个抽象方法，此时编译器会认定重写就是该唯一的抽象方法，如果一个接口中有多个抽象方法，编译器就无法判断其重写的是哪个抽象方法了。

使用 lambda 表达式可以编写出比匿名内部类更简洁的代码。

```
(类型 参数, 类型 参数, …, 类型 参数) -> {
  代码块;
  return 结果;
}
```
主要由几个符号构成：`() -> {}` 。

```java
// 不使用 lambda 表达式
MyInterface oldType = new MyInterface() {
    @Override
    public void m1(int a, int b) {
        System.out.println(a + b);
    }
};

// 使用 lambda 表达式
MyInterface newType = (int a, int b) -> {
  System.out.println(a+b);
};

// 编译器都可以从上下文环境中推断出lambda表达式的参数类型,因此可以省略参数类型
LambdaInterface newType = (a, b) -> {
  System.out.println(a + b);
};

```

### 4. forEach 方法

java 8 中的 `java.lang.Iterable` 接口中新增了非抽象的 forEach 方法，可以使用该方法配合 lambda 来遍历集合。

```java
List<Integer> list = new ArrayList<>();

// java 8 之前
for(Integer i : list){
    System.out.println(i);
}

// java 8 之后，在 Iterator 接口中新增了非抽象的 forEach 方法：
list.forEach((Integer n) -> {System.out.println(n);});

// 如果在 lambda 表达式中只有一个参数一行语句的时候，可以简写为下面格式
list.forEach(n -> System.out.println(n));

// java 8 新增方法引用，方法引用由::双冒号操作符标示
list.forEach(System.out::println);
```

### 5. 方法引用

方法引用是用来简写 lambda 表达式的，有些表达式里面仅仅是执行一个方法调用，这时使用方法引用可以简写 lambda 表达式。

一共有四种类型的方法引用：
- 静态方法引用：类名::方法名
- 某个对象的引用：对象变量名::方法名
- 特定类的任意对象的方法引用：类名::方法名
- 构造方法：类名::new

```java
Integer[] num = {5,8,13,6};

//不使用lambda
Arrays.sort(num, new Comparator<Integer>(){
    @Override
    public int compare(Integer i1, Integer i2) {

        return Integer.compare(i1, i2);
    }
});

//使用lambda
Arrays.sort(num, (x,y) -> Integer.compare(x, y));

//方法引用
Arrays.sort(num, Integer :: compare);
```

## 三、Stream

所有继承自 Collection 的接口都可以转换为 Stream。

### 1. collect(toList())

该方法由 Stream 里的值生成一个列表，可以理解为 Stream 向 Collection 的转换。

```java
List<String> collected = Stream.of("a", "b", "c")
                               .collect(Collectors.toList());
assertEquals(Arrays.asList("a", "b", "c"), collected);
```

### 2. map

如果有一个函数可以将一种类型的值转换成另外一种类型，map 操作就可以使用该函数，将一个流中的值转换成一个新的流。

```java
List<String> collected = Stream.of("a", "b", "hello")
                               .map(string -> string.toUpperCase())
                               .collect(toList());
assertEquals(asList("A", "B", "HELLO"), collected);
```

### 3. filter

遍历数据并检查其中的元素时，可尝试使用 Stream 中提供的新方法 filter。

```java
List<String> beginningWithNumbers = 
        Stream.of("a", "1abc", "abc1")
              .filter(value -> isDigit(value.charAt(0)))
              .collect(toList());
assertEquals(asList("1abc"), beginningWithNumbers);
```

filter 方法就是接受的一个 Predicate 的匿名函数类，判断对象是否符合条件，符合条件的才保留下来。

### 4. flatMap

flatMap 方法可用 Stream 替换值，然后将多个 Stream 连接成一个 Stream。

```java
List<Integer> together = Stream.of(asList(1, 2), asList(3, 4))
                               .flatMap(numbers -> numbers.stream())
                               .collect(toList());
assertEquals(asList(1, 2, 3, 4), together);
```
flatMap 最常用的操作就是合并多个 Collection。

### 5. reduce

reduce 操作可以实现从一组值中生成一个值。

```java
int result = Stream.of(1, 2, 3, 4)
                   .reduce(0, (acc, element) -> acc + element);
assertEquals(10, result);
```

## 参考

[JDK 8 函数式编程入门](https://www.cnblogs.com/snowInPluto/p/5981400.html)
[Java8新特性之lambda表达式](http://www.monkey1024.com/javase/706)

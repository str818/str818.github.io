---
layout: article
title: Python - 进阶学习
tags: Python

lang: zh-Hans
key: Python_Advanced_Study
pageview: true
toc: true
show_subscribe: false
---

## 一、闭包

简单理解，闭包就是一个定义在函数内部的函数，闭包使得变量即使脱离了该函数的作用域也依然能被访问到。

函数身为第一类对象，它可以作为函数的返回值返回。

```python
def print_msg():
    # print_msg 是外围函数
    msg = "zen of python"
    def printer():
        # printer 是嵌套函数
        print(msg)
    return printer

another = print_msg()
# 输出 zen of python
another()
```

一般情况下，函数中的局部变量仅在函数的执行期间可用，一旦 print_msg() 执行过后，我们会认为 msg变量将不再可用。然而，在这里我们发现 print_msg 执行完之后，在调用 another 的时候 msg 变量的值正常输出了，这就是闭包的作用，闭包使得局部变量在函数外被访问成为可能。

这里的 another 就是一个闭包，闭包本质上是一个函数，它有两部分组成，printer 函数和变量 msg。闭包使得这些变量的值始终保存在内存中。

闭包，顾名思义，就是一个封闭的包裹，里面包裹着自由变量，就像在类里面定义的属性值一样，自由变量的可见范围随同包裹，哪里可以访问到这个包裹，哪里就可以访问到这个自由变量。

闭包避免了使用全局变量，此外，闭包允许将函数与其所操作的某些数据（环境）关连起来。

```python
def adder(x):
    def wrapper(y):
        return x + y
    return wrapper

adder5 = adder(5)
# 输出 15
adder5(10)
# 输出 11
adder5(6)
```

## 二、Lambda 函数

Python 中定义函数有两种方法，一种是用常规方式 def 定义，函数要指定名字，第二种是用 lambda 定义，不需要指定名字，称为 Lambda 函数。

```python
add = lambda x, y : x+y
add         # <function <lambda> at 0x102bc2140>
add(1,2)    # 3
```

它等同于常规函数：

```python
def add2(x, y):
    return x+y
```

## 三、高阶函数

### 1. map()

```python
map(func, iterable)
```

`map()` 需要两个必填参数，第一个参数是一个函数名，第二个参数是一个可迭代对象，如列表、元组等。

`map()` 实现的功能很简单，就是将第二个参数（iterable）中的每一个元素分别传给第一个参数（func），依次执行函数得到结果，并将结果组成一个新的 list 对象后进行返回。返回结果永远都是一个 list。

```python
double_func = lambda s : s * 2
map(double_func, [1,2,3,4,5]) # [2, 4, 6, 8, 10]
```

### 2. reduce()

```python
reduce(func, iterable[, initializer])
```

`reduce()` 函数的功能是对可迭代对象（iterable）中的元素从左到右进行累计运算，最终得到一个数值。第三个参数 initializer 是初始数值，可以空置，空置为 None 时就从可迭代对象（iterable）的第二个元素开始，并将第一个元素作为之前的结果。

```python
plus = lambda x, y : x + y
reduce(plus, [1,2,3,4,5]) # 15
reduce(plus, [1,2,3,4,5], 10) # 25
```

### 3. filter()

```python
filter(func, iterable)
```

`filter()` 函数的调用形式与 `map()` 比较相近，都是将第二个参数（iterable）中的每一个元素分别传给第一个参数（func），依次执行函数得到结果；差异在于，`filter()` 会判断每次执行结果的 bool 值，并只将 bool 值为 true 的筛选出来，组成一个新的列表并进行返回。

## 四、装饰器

装饰器本质上是一个 Python 函数或类，它可以让其它函数或类在不需要做任何代码修改的前提下增加额外功能，装饰器的返回值也是一个函数/类对象。它经常用于有切面需求的场景，比如：插入日志、性能测试、事物处理、缓存、权限校验等场景。概括的讲，装饰器的作用就是为已经存在的对象添加额外的功能。

### 1. 简单装饰器

```python
def use_logging(func):

    def wrapper():
        logging.warn("%s is running" % func.__name__)
        return func()   # 把 foo 当做参数传递进来时，执行func()就相当于执行foo()
    return wrapper

def foo():
    print('i am foo')

foo = use_logging(foo)  # 因为装饰器 use_logging(foo) 返回的时函数对象 wrapper，这条语句相当于  foo = wrapper
foo()                   # 执行foo()就相当于执行 wrapper()
```

### 2. 语法糖

能够省略最后一步再次赋值的操作。

```python
def use_logging(func):

    def wrapper():
        logging.warn("%s is running" % func.__name__)
        return func()
    return wrapper

@use_logging
def foo():
    print("i am foo")

foo()
```

### 3. *args 、 **kwargs

如果业务逻辑函数需要参数时，可以指定参数。

```python
def wrapper(*args, **kwargs):
        # args是一个数组，kwargs一个字典
        logging.warn("%s is running" % func.__name__)
        return func(*args, **kwargs)
    return wrapper
```

## 参考

- [一步一步教你认识Python闭包](https://foofish.net/python-closure.html)
- [什么时候使用Lambda函数？](https://foofish.net/lambda.html)
- [Python 的函数式编程 -- 从入门到放弃](https://debugtalk.com/post/python-functional-programming-getting-started/)
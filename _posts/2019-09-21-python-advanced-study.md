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

## 闭包

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

## Lambda 函数

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

## 参考

- [一步一步教你认识Python闭包](https://foofish.net/python-closure.html)
- [什么时候使用Lambda函数？](https://foofish.net/lambda.html)
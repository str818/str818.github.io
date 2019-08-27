---
layout: article
title: Python - 快速入门学习
tags: Python

lang: zh-Hans
key: Python_Quick_Study
pageview: true
toc: true
show_subscribe: false
---

## 字符串

### 1. 字符编码

- ASCII 编码：1 个字节，只有 127 个字符
- GB2312 编码：在 ASCII 的基础上加入了中文
- Unicode 编码：2 个字节，把所有语言都统一到一套编码里
- UTF-8 编码：可变长编码，把一个 Unicode 字符根据不同的数字大小编码成 1- 6 个字节

在计算机内存中，统一使用 Unicode 编码，当需要保存到硬盘或者需要传输的时候，就转换为 UTF-8 编码。

Python 文件首行加入 `# -*- coding: utf-8 -*-` 是为了告诉 Python 解释器，按照 UTF-8 编码读取源代码。如果 `.py` 文件本身使用 UTF-8 编码，并且也申明了 `# -*- coding: utf-8 -*-`，就能够正常显示中文。

### 2. 格式化

```python
'Hello, %s' % 'world'
'Hi, %s, you have $%d.' % ('Michael', 1000000)
```

```python
'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
```

## 容器

### 1. list

列表。可变有序表，list 里面的元素数据类型可以不同。

```python
classmates = ['Michael', 'Bob', 'Tracy'] 	# 定义 list
len(classmates)														# 长度
classmates[0]															# 访问第一个元素
classmates[-1]														# 访问倒数第一个元素
classmates.append('Adam')									# 追加元素到末尾
classmates.insert(1, 'Jack')							# 插入元素到指定位置
classmates.pop()													# 删除末尾元素
classmates.pop(i)													# 删除指定位置的元素
classmates[1] = 'Sarah'										# 替换元素
```

### 2. tuple

元组。一旦初始化就不能修改。

```python
classmates = ('Michael', 'Bob', 'Tracy')	# 定义 tuple
t = (1,)																	# 当元组中只有一个数时，必须加逗号消除和小括号的歧义
```

### 3. dict

字典。使用 键-值（key - value）存储，key 必须是不可变对象。

```python
d = {'Michael': 95, 'Bob': 75}						# 定义字典
d['Adam'] = 67														# 字典插入/赋值
'Thomas' in d															# 判断字典中是否存在 key
d.get('Thomas')														# 若 key 不存在，返回 None
d.get('Thomas', -1)												# 若 key 不存在，返回指定 value
d.pop('Thomas')														# 删除元素
```

### 4. Set

无序、不重复，重复元素会被自动过滤。

```python
s = set([1, 2, 3])												# 定义集合
s.add(4)																	# 添加元素
s.remove(4)																# 删除元素
s1 & s2																		# 并集操作
s1 | s2																		# 交集操作
```

## 函数参数

### 1. 默认参数

`def power(x, n=2)`，默认参数必须指向不变对象。

```python
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

### 2. 可变参数

在参数前面加了一个 `*` 号，参数 `numbers` 接收到的是一个 tuple，调用该函数时，可以传入任意个参数，包括 0 个参数。

```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

如果已经有一个 list 或 tuple，可以在前面加一个 `*` 号，把 list 或 tuple 元素变成可变参数传进去。

```python
nums = [1, 2, 3]
calc(*nums)
```

### 3. 关键字参数

允许传入 0 个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个 dict。

`**extra` 表示把 `extra` 这个 dict 的所有 key-value 用关键字参数传入到函数 `**kw` 参数，`kw` 将获得一个 dict，注意 `kw` 获得的 dict 是 `extra` 的一份拷贝，对 `kw` 的改动不会影响到函数外的 `extra`。

```python
# 函数定义
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
# 函数调用
person('Adam', 45, gender='M', job='Engineer')
# 简化写法
extra = {'city': 'Beijing', 'job': 'Engineer'}
person('Jack', 24, **extra)
```

### 4. 命名关键字参数

命名关键字参数用于限制关键字参数的名字，必须传入参数名。

```python
# 特殊分隔符 * 后面的参数被视为命名关键字参数
def person(name, age, *, city, job):
    print(name, age, city, job)

# 如果函数定义中已经有了一个可变参数，后面的命名关键字参数就不需要特殊分隔符了
def person(name, age, *args, city, job):
    print(name, age, args, city, job)

```

## 高级特性

### 1. 切片

list、tuple 和字符串都可以使用切片操作。

```python
L = list(range(100))
L[0:3]												# 取前 3 个元素
L[:3]													# 若第一个元素为 0，可以省略
L[-1]													# 取倒数第一个元素
L[-2:-1]											# 倒数第二个 — 倒数第一个
L[-10:]												# 取后 10 个数
L[:10:2]											# 前 10 个数，每两个取一个
L[::5]												# 所有数，每 5 个取一个
L[:]													# 原样复制一个 list
```

### 2. 迭代

通过 `for ... in` 完成迭代，只要作用于一个可迭代的对象，`for` 循环就可以正常运行。

```python
d = {'a': 1, 'b': 2, 'c': 3}
# 迭代 key
for key in d:
# 迭代 value
for value in d.values():
# 同时迭代 key 和 value
for k, v in d.items():
# 下标迭代
for i, value in enumerate(['A', 'B', 'C']):
```

### 3. 列表生成式

```python
list(range(1, 11))													# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
[x * x for x in range(1, 11)]								# [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
[m + n for m in 'ABC' for n in 'XYZ']				# ['AX','AY','AZ','BX','BY','BZ','CX','CY','CZ']
```

### 4. 生成器

列表容量是有限的，如果创建一个 100 万个元素的列表，只需要访问前面几个元素的话，后面元素占用的空间就浪费了，这时就可以使用生成器。

```python
g = (x * x for x in range(10))							# 使用小括号
next(g)																			# 获得下一个返回值，没有下一个元素时抛出 StopIteration
for n in g:																	# 一般使用 for 进行迭代
  	print(n)
```

除此之外，也可以通过 `yield` 关键字定义迭代器。

```python
# 斐波那契生成器
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
# 生成器调用
f = fib()
next(f)
```

### 5. 迭代器

凡是可作用于 `for` 循环的对象都是 `Iterable` 类型。

凡是可作用于 `next()` 函数的对象都是 `Iterator` 类型，它们表示一个惰性计算的序列。

集合数据类型如 `list`、`dict`、`str` 等是 `Iterable` 但不是 `Iterator`，不过可以通过 `iter()` 函数获得一个 `Iterator` 对象。

Python 的`for`循环本质上就是通过不断调用 `next()` 函数实现的，



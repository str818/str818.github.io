---
layout: article
title: Python - 语言 & 风格规范
tags: Python

lang: zh-Hans
key: Python_Specification
pageview: true
toc: true
show_subscribe: false
---

## 一、命名约定

- 类名使用大写字母开头的单词(CapWords, Pascal 风格)，模块名用小写加下划线的方式命名(lower_with_under)。
- 单下划线开头(_)表示模块变量或函数是 protected 的(使用 from module import * 时不会包含)。
- 双下划线开头(__)的实例变量或方法表示类内私有。

### Python 之父 Guido 推荐的规范

|            Type            |       Public       |                           Internal                           |
| :------------------------: | :----------------: | :----------------------------------------------------------: |
|          Modules           |  lower_with_under  |                      _lower_with_under                       |
|          Packages          |  lower_with_under  |                                                              |
|          Classes           |      CapWords      |                          _CapWords                           |
|         Exceptions         |      CapWords      |                                                              |
|         Functions          | lower_with_under() |                     _lower_with_under()                      |
|   Global/Class Constants   |  CAPS_WITH_UNDER   |                       _CAPS_WITH_UNDER                       |
|   Global/Class Variables   |  lower_with_under  |                      _lower_with_under                       |
|     Instance Variables     |  lower_with_under  | _lower_with_under (protected) or __lower_with_under (private) |
|        Method Names        | lower_with_under() | _lower_with_under() (protected) or __lower_with_under() (private) |
| Function/Method Parameters |  lower_with_under  |                                                              |
|      Local Variables       |  lower_with_under  |                                                              |

## 二、用 with 子句自动管理资源

使用 with as 语句后，无需手动调用 fp.close(), 在作用域结束后，文件会被自动 close 掉。

```python
with open('pythonic.py') as fp:
    for line in fp:
        print(line[:-1])
```

## 三、用 items 遍历 map

```python
m = {'one': 1, 'two': 2, 'three': 3}
for k, v in m.items():
    print(k, v)
```

## 四、交换变量

```python
a, b = b, a
```

## 五、列表推导式

能够用一行代码简明扼要地解决问题时，绝不要用两行，比如

```python
result = []
for i in range(10):
    s = i*2
    result.append(s)
```

```python
[i*2 for i in xrange(10)]
```

## 参考

- [Python 风格规范](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules/)
- [几种使用的 pythonic 语法](https://segmentfault.com/a/1190000013131003)
- [代码这样写更优雅(Python版)](https://foofish.net/idiomatic_python.html)
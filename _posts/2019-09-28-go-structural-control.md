---
layout: article
title: Go - 结构控制
tags: Go

lang: zh-Hans
key: Go_Structural_Control
pageview: true
toc: true
show_subscribe: false
---

## 一、if

if 的条件里不需要括号。

```go
func bounded(v int) int {
    if v > 100 {
        return 100
    } else if v < 100 {
        return 0
    } else {
        return v
    }
}
```

if 的条件里可以赋值。

```go
if contents, err := ioutil.ReadFile(filename); err == nil {
    fmt.Println(string(contents))
} else {
    fmt.Println("cannot print file contents: ", err)
}
```

## 二、switch

switch 会自动 break，除非使用 `fallthrough`。

```go
switch var1 {
    case val1:
      ...
    case val2:
      ...
    default:
      ...
}
```

变量 var1 可以是任何类型，但 val1 和 val2 必须是相同类型。

执行完某个分支后还希望继续执行后续代码，可以使用 `fallthrough` 关键字实现。

```go
switch i {
    case 0: fallthrough
    case 1:
      f()
}
```

switch 可以不提供任何被判断的值，当任一分支的测试结果为 true 时，该分支的代码就会被执行。

```go
switch {
    case i < 0:
      f1()
    case i == 0:
      f2()
    case i > 0:
      f3()
}
```

此外，switch 可以包含初始化语句，可以优雅的进行条件判断。

```go
switch result := calculate() {
    case result < 0:
        ...
    case result > 0:
        ...
    default:
        // 0
}
```

## 三、for

### 基于计数器的迭代

for 初始化语句; 条件语句; 修饰语句 {}

```go
for i := 0; i < 5; i++ {}
for i, j := 0, N; i < j; i, j = i+1, j-1 {}
```

### 基于条件判断的迭代

for 条件语句 {}，类似于 while 循环。

```go
var i int = 5
for i >= 0 {
    i = i - 1
    fmt.Printf("The variable i is now: %d\n", i)
}
```

### for-range 结构

for ix, val := range coll { }，val 是集合中对应索引的值拷贝，一般只具有只读性质，对它所做的任何修改不会影响到集合中原有的值，如果 val 为指针，则会产生指针的拷贝，依旧可以修改集合中的原值。

```go
for pos, char := range str {
    ...
}
```
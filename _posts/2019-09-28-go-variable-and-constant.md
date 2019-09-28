---
layout: article
title: Go - 变量&常量
tags: Go

lang: zh-Hans
key: Go_Variable_And_Constant
pageview: true
toc: true
show_subscribe: false
---

## 一、变量定义

### 1. 使用 var 关键字

变量类型写在变量名之后。

可以放在函数内，或直接放在包内，使用 `var()` 可以集中定义变量。

```go
var a, b, c bool
var s1, s2 string = "hello", "world"
```

### 2. 让编译器自动决定类型

编译器可推测变量类型。

```go
var a, b, i, s1, s2 = true, false, 3, "hello", "world"
```

### 3. 使用 := 定义变量

只能在函数内使用。

```go
a, b, i, s1, s2 := true, false, 3, "hello", "world"
```

## 二、内建变量类型

没有 `char`，只有 `rune`；原生支持复数类型。

- `bool`, `string`
- `(u)int`, `(u)int8`, `(u)int16`, `(u)int32`, `(u)int64`, `uintptr`
- `byte`, `rune`
- `float32`, `float64`, `complex64`, `complex128`

## 三、强制类型转换

GO 的类型转换是强制的。

```go
var a, b int = 3, 4
var c int = int(math.Sqrt(float64(a*a + b*b)))
```

## 四、常量

### 1. 常量定义

常量使用关键字 `const` 定义，用于存储不会改变的数据。

- 显示类型定义：`const b string = "abc"`
- 隐式类型定义：`const b = "abc"`

### 2. 枚举类型
常量还可以用作枚举：

#### 普通枚举类型

```go
const (
    Unknown = 0
    Female = 1
    Male = 2
)
fmt.Println(Unknown, Female, Male) // 0 1 2
```

#### 自增值枚举类型

```go
const (
    Unknown = iota
    _
    Female
    Male
)
fmt.Println(Unknown, Female, Male) // 0 2 3
```
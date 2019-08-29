---
layout: article
title: Go - 快速入门学习
tags: Go

lang: zh-Hans
key: Go_Quick_Study
pageview: true
toc: true
show_subscribe: false
---

## 一、环境变量

- **\$GOROOT** 表示 Go 的安装位置，一般都都是 `$HOME/go`。
- **\$GOPATH** 可以包含多个 Go 语言源码文件、包文件和可执行文件的路径，这些路径下必须分别包含三个规定的目录：`src`、`pkg`、`bin`，这三个目录分别用于存放源码文件、包文件和可执行文件。

配置环境变量：

```
export GOROOT=$HOME/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=$HOME/Applications/Go
```

在完成这些设置后，你需要在终端输入指令 `source .bashrc` 以使这些环境变量生效。然后重启终端，输入 `go env` 和 `env` 来检查环境变量是否设置正确。

## 二、基本结构和基本数据类型

### 1.基本结构和要素

```go
package main

import "fmt"

func main() {
	fmt.Println("hello, world")
}
```

#### I. 包

每个程序都由包的概念组成，可以使用自身的包或者从其它包中导入内容。必须在源文件中非注释的第一行指明这个文件属于哪个包。`package main` 表示一个可独立执行的程序.

每个 Go 文件都仅属于一个包，一个包可以由许多以 `.go` 为扩展名的源文件组成。

如果对一个包进行更改或重新编译，所有引用了这个包的客户端程序都必须全部重新编译。每一段代码只会被编译一次。

导入包：
```go
import (
   "fmt"
   "os"
)
```

如果导入了一个包却没有使用它，构建程序的时候就会发生错误，正如 Go 的格言：”没有不必要的代码!“

##### 可见性规则

- 当标识符以大写字母开头，如：Group1，该标识符的对象可以被外部包的代码所使用，类似 public。
- 当标识符以小写字母开头，对包外是不可见的，类似 private。

#### II. 函数

```go
func Sum(a, b int) int { return a + b }
```

可执行程序必须包含 main 函数，main 函数既没有参数也没有返回类型。

只有当某个函数需要被外部包调用的时候才使用大写字母开头，并遵循 Pascal 命名法；否则就遵循骆驼命名法，即第一个单词的首字母小写，其余单词的首字母大写。

#### III. 类型

使用 var 声明的变量的值会自动初始化为该类型的零值。

type 关键字能够定义自己的类型：`type IZ int`。

Go 语言是一种静态类型语言，每个值都必须在经过编译后属于某个类型（编译器必须能够推断出所有值的类型）。

#### IV. 类型转换

类型 B 的值 = 类型 B(类型 A 的值)

```go
a := 5.0
b := int(a)
```

#### V. 命名规范

通过 gofmt 来强制实现统一的代码风格，返回某个对象的函数或方法的名称一般都是使用名词，没有 `Get...` 之类的字符，如果是用于修改某个对象，则使用 `SetName`。有必要的话可以使用大小写混合的方式，而不是是使用下划线来分隔多个名称。

#### VI. Go 程序的一般结构

```go
package main

import (
   "fmt"
)

const c = "C"

var v int = 5

type T struct{}

func init() { // initialization of package
}

func main() {
   var a int
   Func1()
   // ...
   fmt.Println(a)
}

func (t T) Method1() {
   //...
}

func Func1() { // exported function Func1
   //...
}
```

### 2. 常量与变量

#### I. 常量

常量使用关键字 `const` 定义，用于存储不会改变的数据。

存储在常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

- 显示类型定义：`const b string = "abc"`
- 隐式类型定义：`const b = "abc"`

常量还可以用作枚举：

```go
const (
	Unknown = 0
	Female = 1
	Male = 2
)
```

#### II. 变量

##### 声明变量

声明变量的一般形式是使用 `var` 关键字：`var identifier type`，变量的类型放在变量的名称之后。

```go
var a int
var b bool
var str string
```

因式分解关键字的写法一般用于声明全局变量。

```go
var (
	a int
	b bool
	str string
)
```

当一个变量被声明之后，系统自动赋予它该类型的零值：int 为 0，float 为 0.0，bool 为 false，string 为空字符串，指针为 nil。记住，所有的内存在 Go 中都是经过初始化的。

##### 作用域

- 全局变量：声明在函数体之外，可以在整个包甚至外部包（被导出后）中使用。
- 局部变量：声明在函数体之内，作用域只在函数体内。

##### 初始化

```go
var identifier [type] = value
var a int = 15
var i = 5
var a, b, c int
a, b, c = 5, 7, "abc"
a, b, c := 5, 7, "abc"
```

函数体内声明局部变量时，应使用简短声明语法：`:=`，例如：`a := 1`。

空白标识符 _ 也被用于抛弃值，如值 5 在：_, b = 5, 7 中被抛弃。

##### 值类型与引用类型

像 int、float、bool 和 string 这些基本类型都属于值类型，使用这些类型的变量直接指向存在内存中的值。

数组和结构这些复合类型也是值类型。

当使用等号 = 将一个变量的值赋值给另一个变量时，如：j = i，实际上是在内存中将 i 的值进行了拷贝。

一个引用类型的变量 r1 存储的是 r1 的值所在的内存地址（数字），或内存地址中第一个字所在的位置，这个内存地址被称之为指针，当使用赋值语句 `r2 = r1` 时，只有引用（地址）被复制。

##### init 函数

每个包完成后自动执行 init 函数，初始化总是以单线程并且按照包的依赖关系顺序执行，也可以在 init 函数中对全局变量进行初始化。

### 3. 基本类型和运算符


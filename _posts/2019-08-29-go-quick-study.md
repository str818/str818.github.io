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

`$GOPATH` 可以包含多个 Go 语言源码文件、包文件和可执行文件的路径。

这些路径下必须分别包含三个规定的目录：`src`：存放源码文件、`pkg`：包文件、`bin`：可执行文件。

`$GOROOT` 表示 Go 的安装位置，一般都是 `$HOME/go`。

配置环境变量：

```sh
export GOROOT=$HOME/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=$HOME/Applications/Go
```

在完成这些设置后，你需要在终端输入指令 `source .bashrc` 以使这些环境变量生效。然后重启终端，输入 `go env` 和 `env` 来检查环境变量是否设置正确。

## 二、基本结构

### 1. 基本结构和要素

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

Go 是强类型语言，不会进行隐式转换，任何不同类型之间的转换都必须显式说明。

`&&` 和 `||` 是具有快捷性质的运算符，当运算符左边表达式的值已经能够决定整个表达式的值的时候（`&&` 左边的值为 false，`||` 左边的值为 true），运算符右边的表达式将不会被执行。

- 整数：
int8（-128 -> 127）
int16（-32768 -> 32767）
int32（-2,147,483,648 -> 2,147,483,647）
int64（-9,223,372,036,854,775,808 -> 9,223,372,036,854,775,807）

- 无符号整数：
uint8（0 -> 255）
uint16（0 -> 65,535）
uint32（0 -> 4,294,967,295）
uint64（0 -> 18,446,744,073,709,551,615）

- 浮点型（IEEE-754 标准）：
float32（+- 1e-45 -> +- 3.4 * 1e38）
float64（+- 5 * 1e-324 -> 107 * 1e308）

float32 精确到小数点后 7 位，float64 精确到小数点后 15 位。

带有 ++ 和 -- 的只能作为语句，而非表达式，因此 `n = i++` 这种写法是无效的，其它像 `f(i++)` 或者 `a[i]=b[i++]` 这些可以用于 C、C++ 和 Java 中的写法在 Go 中也是不允许的。

#### 格式化说明符

在格式化字符串里，`%d` 用于格式化整数（`%x` 和 `%X` 用于格式化 16 进制表示的数字），`%g` 用于格式化浮点型（`%f` 输出浮点数，`%e` 输出科学计数表示法），`%0nd` 用于规定输出长度为 `n` 的整数，其中开头的数字 0 是必须的。

`%n.mg` 用于表示数字 n 并精确到小数点后 m 位，除了使用 g 之外，还可以使用 e 或者 f，例如：使用格式化字符串 `%5.2e` 来输出 3.4 的结果为 3.40e+00。

### 4. 字符串

判断字符串 s 是否以 prefix 开头或结尾：
```go
strings.HasPrefix(s, prefix string) bool
strings.HasSuffix(s, suffix string) bool
```

判断字符串 s 是否包含 substr：
```go
strings.Contains(s, substr string) bool
```

判断子字符串或字符在父字符串中出现的位置：
```go
strings.Index(s, str string) int        // str 在字符串 s 中的索引位置
strings.LastIndex(s, str string) int    // str 在字符串 s 中最后出现的索引位置
```

将字符串 str 中前 n 个字符串 old 替换为字符串 new，并返回一个新的字符串，如果 `n = -1` 则替换所有字符串 old 为字符串 new：
```go
strings.Replace(str, old, new, n) string
```

计算字符串 str 在 s 中出现的非重叠次数：
```go
strings.Count(s, str string) int
```

重复 count 次字符串 s 并返回一个新的字符串：
```go
strings.Repeat(s, count int) string
```

修改大小写：
```go
strings.ToLower(s) string
strings.ToUpper(s) string
```

修剪字符串：
```go
strings.TrimSpace(s)
strings.Trim(s, "cut")
strings.TrimLeft(s, "cut")
strings.TrimRight(s, "cut")
```

分割字符串：
```go
strings.Fields(s)       // 利用 1 个或多个空白符号进行分割
strings.Split(s, sep)   // 自定义分割符号进行分割
```

拼接 slice 到字符串，将元素类型为 string 的 slice 使用分割符号来拼接组成一个字符串。
```go
strings.Join(sl []string, sep string) string
```

类型转换：
```go
strconv.Itoa(i int) string                              // 返回数字 i 所表示的字符串类型的十进制数
// 将 64 位浮点型的数字转换为字符串，其中 fmt 表示格式，prec 表示精度
strconv.FormatFloat(f float64, fmt byte, prec int, bitSize int) string
strconv.Atoi(s string) (i int, err error)                           // 将字符串转换为 int 型
strconv.ParseFloat(s string, bitSize int) (f float64, err error)    // 将字符串转换为 float64 型
```

### 5. 指针

一个指针变量可以指向任何一个值的内存地址，内存地址与值的大小无关，在 32 位机器上占用 4 个字节，在 64 位机器上占用 8 个字节。

`&` 为取地址符，放到一个变量前就会返回相应变量的内存地址；在指针类型前面加上 * 号能够获取指针所指向的内容。

使用一个指针引用一个值被称为间接引用。为了保证内存安全，移动指针指向字符串的字节数或数组的某个位置是不被允许的。

```go
var i1 = 5
var intP *int
intP = &i1
```

## 三、控制结构

### 1. if-else 结构

```go
if condition1 {
    // do something	
} else if condition2 {
    // do something else	
} else {
    // catch-all or default
}
```

if 可以包含一个初始化语句：

```go
if val := 10; val > max {
    // do something
}
```

### 2. 多返回值

```go
anInt, _ = strconv.Atoi(origStr)
value, err := pack1.Function1(param1)
if err != nil {
    fmt.Printf("An error occured in pack1.Function1 with parameter %v", param1)
    return err
}
```

下面这种方式是能够通过编译的，必须要有新的变量生成否则不会通过编译，重复的变量会由定义变为赋值。
```go
a, b := 1, 2
k, b := 3, 4
```

### 3. switch 结构

#### 第一种形式

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

匹配到某个分支并执行完成后，会退出整个 switch 代码块，不需要特别使用 break 语句来表示结束。

执行完某个分支后还希望继续执行后续代码，可以使用 `fallthrough` 关键字实现。

```go
switch i {
    case 0: fallthrough
    case 1:
      f()
}
```

#### 第二种形式

不提供任何被判断的值，当任一分支的测试结果为 true 时，该分支的代码就会被执行。

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

#### 第三种形式

包含初始化语句，可以优雅的进行条件判断。

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

### 4. for 结构

#### 基于计数器的迭代

for 初始化语句; 条件语句; 修饰语句 {}

```go
for i := 0; i < 5; i++ {}
for i, j := 0, N; i < j; i, j = i+1, j-1 {}
```

#### 基于条件判断的迭代

for 条件语句 {}，类似于 while 循环。

```go
var i int = 5
for i >= 0 {
    i = i - 1
    fmt.Printf("The variable i is now: %d\n", i)
}
```

#### for-range 结构

for ix, val := range coll { }，val 是集合中对应索引的值拷贝，一般只具有只读性质，对它所做的任何修改不会影响到集合中原有的值，如果 val 为指针，则会产生指针的拷贝，依旧可以修改集合中的原值。

```go
for pos, char := range str {
    ...
}
```

### 5. 标签与 goto

for、switch 或 select 语句都可以配合标签（label）形式的标识符使用，建议全部使用大写字母。

```go
LABEL1:
for i := 0; i <= 5; i++ {
    for j := 0; j <= 5; j++ {
        f j == 4 {
            continue LABEL1
        }
            fmt.Printf("i is: %d, and j is: %d\n", i, j)
        }
}
```

Go 也支持 goto 配合标签使用，但可读性很差，不建议使用。

## 四、函数

### 1. 函数参数与返回值

#### 函数参数

Go 函数能够返回零个或者多个值，Go 默认使用按值传递来传递参数，在变量前加上符号 `&` 传递参数的地址作为引用传递。

指针也是变量类型，有自己的地址和值，通常指针的值指向一个变量的地址。所以，按引用传递也是按值传递。

#### 命名返回值

当使用命名返回值时，只需要使用一条不带参数的 return 语句即可，建议使用命名返回值，会使代码更清晰、简短且易懂。

```go
func getX2AndX3(input int) (int, int) {
    return 2 * input, 3 * input
}
func getX2AndX3_2(input int) (x2 int, x3 int) {
    x2 = 2 * input
    x3 = 3 * input
    // return x2, x3
    return
}
```

#### 空白符

空白符用来匹配一些不需要的值，然后丢弃调。

```go
i1, _, f1 = ThreeValues()
func ThreeValues() (int, int, float32) {
    return 5, 6, 7.5
}
```

#### 变长参数

如果函数的最后一个参数是采用 `...type` 的形式，那么这个函数就可以处理一个变长的参数，这个长度可以为 0。

```go
func myFunc(a, b, arg ...int) {}
```

#### defer

关键字 defer 允许我们推迟到函数返回之前（或任意位置执行 return 语句之后）一刻才执行某个语句或函数（为什么要在返回之后才执行这些语句？因为 return 语句同样可以包含一些操作，而不是单纯地返回某个值）。

关键字 defer 的用法类似于面向对象编程语言 Java 和 C# 的 finally 语句块，它一般用于释放某些已分配的资源。

当有多个 defer 行为被注册时，它们会以逆序执行（类似栈，即后进先出）。

#### 将函数作为参数

```go
func main() {
    callback(1, Add)
}

func Add(a, b int) {
    fmt.Printf("The sum of %d and %d is: %d\n", a, b, a+b)
}

func callback(y int, f func(int, int)) {
    f(y, 2) // this becomes Add(1, 2)
}
```

### 2. 闭包

闭包同样称为匿名函数，不需要给函数起名字。

闭包函数能够将地址保存到变量中：`fplus := func(x, y int) int { return x + y }`，然后通过变量名对函数进行调用：`fplus(3,4)`。

闭包函数保存并积累其中的变量的值，不管外部函数退出与否，他都能够继续操作外部函数中的局部变量。

```go
func MakeAddSuffix(suffix string) func(string) string {
    return func(name string) string {
        if !strings.HasSuffix(name, suffix) {
        		return name + suffix
        }
        return name
    }
}
addBmp := MakeAddSuffix(".bmp")
addJpeg := MakeAddSuffix(".jpeg")

addBmp("file") // returns: file.bmp
addJpeg("file") // returns: file.jpeg
```

## 五、数组与切片

### 1. 声明与初始化

#### 概念

声明格式：

```go
var arr1 [5]int           // arr1 的类型为 [5]int
var arr2 = new([5]int)    // arr2 的类型为 *[5]int
```

数组赋值，进行了一次数组内存拷贝操作，修改 arr2 不会对 arr1 生效。

```go
arr2 := *arr1
```

同样，将数组作为参数传入函数中，`func1(arr2)` 会产生一次数组拷贝，方法不会修改原数组，如果向修改原数组，需要以引用的方式传递进来 `func1(&arr2)`。

遍历数组：

```go
// 普通 for 循环
for i:=0; i < len(arr1); i++｛
    arr1[i] = ...
}

// for-range 生成方式
for i,_:= range arr1 {
    ...
}
```

#### 数组常量

如果数组值已经提前知道了，可以通过数组常量的方式来初始化数组。

```go
var arrAge = [10]int{18, 20, 15, 22, 16}          // 前 5 个元素被赋值，后 5 个元素默认为 0
var arrLazy = [...]int{5, 6, 7, 8, 22}            // ...可以忽略
var arrKeyValue = [5]string{3: "Chris", 4: "Ron"} // key-value 语法，索引 3 和 4 位置被赋值
```

### 2. 切片

切片（slice）是对数组一个连续片段的引用，所以切片是一个引用类型。这个片段可以是整个数组，或者是由起始和终止索引标识的一些项的子集。

切片是一个 **长度可变的数组**。

#### 声明

一个切片在未初始化之前默认为 nil，长度为 0。

```go
var identifier []type                   // 不需要说明长度
```

#### 初始化

```go
var slice1 []type = arr1[start:end]     // 从 start 到 end - 1 索引之间的元素构成的子集：
s2 := s[:]                              // 用切片组成的切片，拥有相同的元素
```

#### 将切片传递给函数

```go
func sum(a []int) int {
    s := 0
    for i := 0; i < len(a); i++ {
        s += a[i]
    }
    return s
}

func main() {
    var arr = [5]int{0, 1, 2, 3, 4}
    sum(arr[:])
}
```

#### 用 make() 创建切片

默认 len 是数组的长度也是 slice 的初始长度。

```go
// func make([]T, len, cap)
var slice1 []type = make([]type, len)
```

#### new() 和 make() 的区别

- new(T) 为每个新的类型 T 分配一片内存，初始化为 0 并且返回类型为 *T 的内存地址：这种方法 **返回一个指向类型为 T，值为 0 的地址的指针**，它适用于值类型如数组和结构体，相当于 `&T{}`。
- make(T) capacity 个类型为 T 的初始值**，它只适用于 3 中内建的引用类型：切片、map 和 channel。

## 六、Map

### 1. 概念

map 是引用类型，可以使用如下声明：

```go
var map1 map[string]int
```

在声明的时候不需要知道 map 的长度，map 是可以动态增长的，未初始化的 map 的值的 nil。

内存用 make 方法来分配：

```go
map1 := make(map[string]float32)
map2 := map[string]float32{}
```

### 2. 判断键值对是否存在

```go
if _, ok := map1[key1]; ok {
	// ...
}
```

### 3. 删除 key

```go
delete(map1, key1)
```

## 七、并发

### 1. 互斥锁

Go 语言中的锁机制是通过 sync 包中的 Mutex 实现的，线程将有序的对同一变量进行访问。当执行了 `mutex.Lock()` 操作后，如果有另外一个 goroutine 又执行了上锁操作，那么该操作被阻塞，直到该互斥锁恢复到解锁状态。

```go
func main() {
    var mutex sync.Mutex
    count := 0

    for r := 0; r < 50; r++ {
    go func() {
            mutex.Lock()
            count += 1
            mutex.Unlock()
        }()
    }

    time.Sleep(time.Second)
    fmt.Println("the count is : ", count)
}
```

### 2. 读写锁

读写锁是对读写操作进行加锁，多个读操作之间不存在互斥关系，这样能提高对共享资源的访问效率。

- Lock()：写锁定
- Unlock()：写解锁
- RLock()：读锁定
- RUnlock()：读解锁

## 八、结构体

结构体定义方式如下：

```go
type identifier struct {
    field1 type1
    field2 type2
    ...
}
```

结构体里的字段都有名字，像 field1、field2 等，如果字段在代码中从来也不会被用到，那么可以命名它为 _。

### 1. new

使用 new 函数给一个新的结构体变量分配内存，它返回指向已分配内存的指针：

```go
var t *T = new(T)
```

### 2. 内存分布

Go 语言中，结构体和它所包含的数据在内存中是以连续块的形式存在的，即使结构体中嵌套有其他的结构体，这在性能上带来了很大的优势。不像 Java 中的引用类型，一个对象和它里面包含的队形可能会在不同的内存空间中，这点和 Go 语言中的指针很像。

### 3. 内嵌结构体

结构体可以包含一个或多个 **匿名（或内嵌）字段**，即这些字段没有显式的名字，只有字段的类型是必须的，此时类型就是字段的名字。匿名字段本身可以是一个结构体类型，即 **结构体可以包含内嵌结构体**。

Go 语言中的继承是通过内嵌或组合来实现的，所以可以说，在 Go 语言中，相比较于继承，组合更受青睐。

```go
type innerS struct {
	in1 int
	in2 int
}

type outerS struct {
	b    int
	c    float32
	int     // anonymous field
	innerS  //anonymous field
}

func main() {
	outer := new(outerS)
	outer.b = 6
	outer.c = 7.5
	outer.int = 60
	outer.in1 = 5
	outer.in2 = 10

	// 使用结构体字面量
	outer2 := outerS{6, 7.5, 60, innerS{5, 10}}
	fmt.Println("outer2 is:", outer2)
}
```

在一个结构体中对于每一种数据类型只能有一个匿名字段。

内层结构体被简单的插入或者内嵌进外层结构体。这个简单的「继承」机制提供了一种方式，使得可以从另外一个或一些类型继承部分或全部实现。

#### 命名冲突

当两个字段会覆盖内层的名字（但是两者的内存空间都保留）时：

1. 外层名字会覆盖内层名字（但是两者的内存空间都保留），这提供了一种重载字段或方法的方式；
2. 如果相同的名字在同一级别出现了两次，如果这个名字被程序使用了，将会引发一个错误。没有办法来解决这种问题引起的二义性，必须由程序员自己修正。

### 4. 方法

在 Go 语言中，结构体就像是类的一种简化形式，那么面向对象程序员可能会问：类的方法在哪里呢？在 Go 中有一个概念，它和方法有着同样的名字，并且大体上意思相同：Go 方法是作用在接收者上的一个函数，接收者是某种类型的变量。因此方法是一种特殊类型的函数。

接收者类型可以是（几乎）任何类型，不仅仅是结构体类型：任何类型都可以有方法，甚至可以是函数类型，可以是 int、bool、string 或数组的别名类型。但是接收者不能是一个接口类型，因为接口是一个抽象定义，但是方法却是具体实现。

一个类型加上它的方法等价于面向对象中的一个类。一个重要的区别是：在 Go 中，类型的代码和绑定在它上面的方法的代码可以不放置在一起，它们可以存在在不同的源文件，唯一的要求是：它们必须是同一个包的。

类型 T（或 *T）上的所有方法的集合叫做类型 T（或 *T）的方法集（method set）。

#### 函数和方法的区别

- 函数将变量作为参数：`Function1(recv)`
- 方法在变量上被调用：`recv.Method1()`

### 5. 多重继承

多重继承指的是类型获得多个父类型行为的能力，它在传统的面向对象语言中通常是不被实现的（C++ 和 Python  除外）。因为在类继承层次中，多重继承会给编译器引入额外的复杂度。但是在 Go 语言中，通过在类型中嵌入所有必要的父类型，可以很简单的实现多重继承。

## 九、接口

接口提供了一种方式来说明对象的行为：如果谁能搞定这件事，它就可以用在这儿。

接口定义了一组方法（方法集），但是这些方法不包含实现代码，同时也不能包含变量。

```go
type Namer interface {
    Method1(param_list) return_type
    Method2(param_list) return_type
    ...
}
```

类型实现接口方法集中的方法，每一个方法的实现说明了此方法是如何作用于该类型的：实现接口，同时方法集也构成了该类型的接口。

```go
type Shaper interface {
    Area() float32
}

type Square struct {
    side float32
}

func (sq *Square) Area() float32 {
    return sq.side * sq.side
}

func main() {
    sq1 := new(Square)
    sq1.side = 5

    var areaIntf Shaper
    areaIntf = sq1

    fmt.Printf("The square has area: %f\n", areaIntf.Area())
}
```




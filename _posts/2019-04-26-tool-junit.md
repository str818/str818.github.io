---
layout: article
title: 工具 — JUnit 单元测试
tags: 工具

lang: zh-Hans
key: Tool_JUnit
pageview: true
toc: true
show_subscribe: false
---

## 一、什么是 JUnit？

JUnit 是一个 Java 编程语言的单元测试框架。

单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。

比如对函数 abs()，可以编写出以下几个测试用例：

- 输入整数，比如 1、1.2、0.99，期待返回值与输入相同
- 输入负数，比如 -1、-1.2、-0.99，期待返回值与输入相反
- 输入 0，期待返回 0。

把上面的测试用例放到一个测试模块里，就是一个完整的单元测试。

如果单元测试通过，说明我们测试的这个函数能够正常工作。如果单元测试不通过，要么函数有 Bug，要么测试条件输入不正确，总之，需要修复使单元测试能够通过。

单元测试通过后有什么意义呢？如果我们对 abs() 函数代码做了修改，只需要再跑一遍单元测试，如果通过，说明我们的修改不会对 abs() 函数原有的行为造成影响，如果测试不通过，说明我们的修改与原有行为不一致，要么修改代码，要么修改测试。

这种以测试为驱动的开发模式最大的好处就是确保一个程序模块的行为符合我们设计的测试用例。在将来修改的时候，可以极大程度地保证该模块行为仍然是正确的。

## 二、实践

计算类。

```java
public class Calculate {
    public int abs(int a){
        if(a < 0){
            return -a;
        }
        return a;
    }
}
```

测试类。

```java
public class CalculateTest {
    @Test
    public void testDivide() throws Exception {
        assertEquals(1, new Calculate().abs(1));
        assertEquals(1, new Calculate().abs(-1));
        assertEquals(0, new Calculate().abs(0));
    }
} 
```

当运行单元测试类时，如果测试用例不通过，则会报错。

## 三、常用注解

1. `@Test`：测试方法
  - (expected = XX.class) 如果程序抛出 XX 异常，则测试通过
  - (timeout = 100) 如果程序在 100 毫秒内完成，则测试通过，防止死循环
2. `@Ignore`：被忽略的测试方法，暂时不运行此段代码
3. `@Before`：每个测试方法执行前运行
4. `@After`：每个测试方法执行后运行
5. `@BeforeClass`：方法必须声明为 public static，当前类测试开始前执行
6. `@AfterClass`：方法必须声明为 public static，当前类测试结束后执行

## 参考

- [Junit5](https://junit.org/junit5/)
- [JUnit-W3Cschool](https://www.w3cschool.cn/junit/fegu1hv3.html)
- [单元测试-廖雪峰](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143191629979802b566644aa84656b50cd484ec4a7838000)
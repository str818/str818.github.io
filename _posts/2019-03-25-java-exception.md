---
layout: article
title: Java 异常
tags: Java

lang: zh-Hans
key: Java_Exception
pageview: true
toc: true
show_subscribe: false
---

Throwable 可以用来表示任何可以作为异常抛出的类，分为两种： Error 和 Exception。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：

- 受检异常 ：除了 Error 和 RuntimeException 的其它异常，需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
- 非受检异常 ：Error 和 RuntimeException 以及他们的子类，是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

<div align="center">  <img src="/img/java_basic_exception.png" width="100%"/> </div><br>

[Java 中的异常和处理详解](http://www.importnew.com/26613.html)


---
title: UML 类图
permalink: /notes/java/internal/class-diagram
key: internal-class-diagram
---

## 一、类的表示方式

类图是面向对象系统中最常用和最重要的图，在类图中类使用包含类名、属性和方法且带有分割线的矩形来表示，比如下图表示一个 Employee 类，它包含 name、age 和 email 这 3 个属性，以及 modifyInfo() 方法。

<div align="center">  <img src="/img/internal_design_class_diagram_1.png" width="30%"/> </div><br>

属性/方法名称前的符号：

| 符号 |   含义    |
| :--: | :-------: |
|  +   |  public   |
|  -   |  private  |
|  #   | protected |

## 二、类与类之间的关系

### 1. 泛化关系 (Generalization)

用来描述继承关系，在 Java 中使用 extends 关键字。

<div align="center">  <img src="/img/internal_design_class_diagram_2.png" width="30%"/> </div><br>

### 2. 实现关系 (Realization)

用来实现一个接口，在 Java 中使用 implements 关键字。

<div align="center">  <img src="/img/internal_design_class_diagram_3.png" width="30%"/> </div><br>

### 3. 聚合关系 (Aggregation)

表示整体由部分组成，但是整体和部分不是强依赖的，整体不存在了部分还是会存在。

<div align="center">  <img src="/img/internal_design_class_diagram_4.png" width="43%"/> </div><br>

### 4. 组合关系 (Composition)

和聚合不同，组合中整体和部分是强依赖的，整体不存在了部分也不会存在。比如公司和部门，公司没了部门就不存在了。但是公司和员工就属于聚合关系了，因为公司没了员工还在。

<div align="center">  <img src="/img/internal_design_class_diagram_5.png" width="43%"/> </div><br>

### 5. 关联关系 (Association)

表示不同类对象之前有关联，这是一种静态关系，与运行过程的状态无关，在最开始就可以确定。因此也可以用 1 对 1、多对 1、多对多这种关联关系来表示。比如学生和学校就是一种关联关系，一个学校可以有很多学生，但是一个学生只属于一个学校，因此这是一种多对一的关系，在运行开始之前就可以确定。

<div align="center">  <img src="/img/internal_design_class_diagram_6.png" width="43%"/> </div><br>

### 6. 依赖关系 (Dependency)

和关联关系不同，依赖关系是在运行过程中起作用的。A 类和 B 类是依赖关系主要有三种形式：
- A 类是 B 类方法的局部变量；
- A 类是 B 类方法当中的一个参数；
- A 类向 B 类发送消息，从而影响 B 类发生变化。

<div align="center">  <img src="/img/internal_design_class_diagram_7.png" width="28%"/> </div><br>
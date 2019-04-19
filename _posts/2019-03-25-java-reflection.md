---
layout: article
title: Java 反射
tags: Java

lang: zh-Hans
key: Java_Reflection
pageview: true
toc: true
show_subscribe: false
---

反射（Reflection）是 Java 程序开发语言的特征之一，它允许运行中的 Java 程序获取自身的信息，并且可以操作类或对象的内部属性。通过 Class 获取 class 信息称之为反射。

简而言之，通过反射，可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。

## 一、Class 类

在面向对象的世界里，万事万物皆对象。

Java 语言中只有静态的成员和普通数据类型不是对象，其中静态的成员不是某一个类的，它是属于类的，普通数据类型有其对应的包装类。

同样，类也是对象，类是 `java.lang.Class` 类的实例对象。

### 1. Class 实例对象的表示方法

```java
class Foo{}
Foo foo = new Foo();
```

任何一个类都是 Class 的实例对象，这个实例对象有三种表示方式。

#### 第一种表示方式

任何一个类都有一个隐含的静态成员变量 class。

```java
Class c1 = Foo.class;
```
#### 第二种表示方式

已经知道该类的对象通过 getClass 方法。

```java
Class c2 = foo.getClass();
```

> c1 与 c2 表示了 Foo 类的类类型（class type）。

c1 和 c2 都代表了 Foo 类的类类型，一个类只可能是 Class 类的一个实例对象。

```java
System.out.println(c1 == c2); // true
```

#### 第三种表示方式

```java
Class c3 = null;
try{
    c3 = Class.forName("com.reflect.Foo");
}catch(ClassNotFoundException e){
    e.printStackTrace();
}
```

```java
System.out.println(c2 == c3);
```

可以通过类的类类型创建该类的对象实例：

```java
try{
    Foo foo = (Foo)c1.newInstance(); // 需要有无参的构造器
}catch(InstantiationException e){
    e.printStackTrace();
}catch(IllegalAccessException e){
    e.printStackTrace();
}
```
基本的数据类型、void 关键字等都存在类类型。


### 2. 加载 Class 类

#### 静态加载类

在编译时刻就需要加载所有的可能使用到的类。

```java
Circle s = new Circle();
```

#### 动态加载类

```java
// 在运行时刻加载
Class c = Class.forName(args[0]);
// 通过类类型，创建该类对象
Shape s = (Circle)c.newInstance();
s.draw();
```

### 3. Class 类的基本 API 操作

打印类的信息，包括类的成员函数、成员变量。

```java
public static void printClassMessage(Object obj){
    // 要获取类的信息 首先要获取类的类类型
    Class c = obj.getClass(); // 传递的是哪个子类的对象 c 就是该子类的类类型

    // 获取类的名称
    System.out.println("类的名称是：" + c.getName());

    /* Method 类，方法对象
     * 一个成员方法就是一个 Method 对象
     * getMethods() 方法获取的是所有的 public 函数，包括父类继承而来的
     * getDeclaredMethods() 获取的是所有该类自己声明的方法，不管访问权限
     */
    Method[] ms = c.getMethods();//c.getDeclaredMethods()
    for(int i = 0; i < ms.length; i++){
        // 得到方法的返回类型的类类型
        Class returnType = ms[i].getReturnType();
        System.out.print(returnType.getName()+"");

        // 得到方法的名称
        System.out.print(ms[i].getName()+"(");

        // 获取参数类型 --> 得到的是参数列表的类型的类类型
        for(Class class1 : parameTypes){
            System.out.print(class1.getName()+",");
        }

        System.out.println(")");

        /*
         * 成员变量也是对象
         * java.lang.reflect.Field 类封装了关于变量的操作
         * getFields() 方法获取的是所有 public 的成员变量的信息
         * getDeclaredFields() 获取的是该类自己声明的成员变量信息
         */

        Field[] fs = c.getDeclaredFields();// c.getFields();

        for(Field field : fs){
            // 得到成员变量的类型的类类型
            Class fieldType = field.getType();
            String typeName = fieldType.getName();

            // 得到成员变量的名称
            String fieldName = field.getName();
            System.out.println(typeName + " " + fieldName);
        }
    }

    // 构造函数信息 java.lang.Constructor
    Constructor[] cs = c.getConstructors();


}
```

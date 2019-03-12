---
title: 设计模式
permalink: /notes/java/internal/design-pattern
key: internal-design-pattern
---

## 创建型

### 单例

单例模式(Singleton Pattern)是指确保一个类在任何情况下都绝对只有一个实例，并提供一个全局访问点。

#### 1. 饿汉式

在类加载的时候就立即初始化，并且创建单例对象。

- 优点：没有加任何的锁、执行效率比较高，在用户体验上来说，比懒汉式更好。
- 缺点：类加载的时候就初始化，不管用与不用都占着空间，浪费了内存，有可能占着茅坑不拉屎。

```java
public class HungrySingleton {
 
  private static final HungrySingleton hungrySingleton = new HungrySingleton();
  
  private HungrySingleton(){}

  public static HungrySingleton getInstance(){
    return hungrySingleton;
  }
}
```

还有另一种写法，利用静态代码块的机制。

```java
public class HungryStaticSingleton {

  private static final HungryStaticSingleton hungrySingleton;
  
  static {
    hungrySingleton = new HungryStaticSingleton();
  }

  private HungryStaticSingleton(){}
}
```

#### 2. 懒汉式

在外部类调用的时候内部类才会加载。这种写法存在线程安全的问题，如果多个线程同时进入 `if(lazy == null)`，并且此时 lazy 为 null，那么会有多个线程执行 `lazy = new LazySimpleSingleton();` 语句，导致实例化多次 lazy。

```java
public class LazySimpleSingleton {

    private LazySimpleSingleton(){}

    private static LazySimpleSingleton lazy = null;

    public static LazySimpleSingleton getInstance(){
        if(lazy == null){
            lazy = new LazySimpleSingleton();
        }
        return lazy;
    }
}
```

当然，可以通过给方法加锁的方式解决线程不安全的问题，但是，用 synchronized 加锁，在线程数量较多的情况下，如果 CPU 分配压力上升，会导致大批量线程出现阻塞，从而导致程序运行性能大幅下降。

```java
public synchronized static LazySimpleSingleton getInstance(){
    if(lazy == null){
        lazy = new LazySimpleSingleton();
    }
    return lazy;
}
```

#### 3. 双重校验锁

加锁操作只需要对实例化那部分的代码进行，只有当 lazy 没有被实例化时才需要进行加锁。

```java
public class LazyDoubleCheckSingleton {

    private volatile static LazyDoubleCheckSingleton lazy = null;

    private LazyDoubleCheckSingleton(){}

    public static LazyDoubleCheckSingleton getInstance(){
        if(lazy == null){
            synchronized (LazyDoubleCheckSingleton.class){
                if(lazy == null){
                    lazy = new LazyDoubleCheckSingleton();
                }
            }
        }
        return lazy;
    }
}
```

如果只写外层的 if 语句，在 lazy 为 null 的情况下，两个线程都会进入到 if 语句块内，虽然加了锁，但只是先后执行 `lazy = new LazyDoubleCheckSingleton();` 的问题，所以只写外层的 if 语句还是会创建多个实例。

`lazy = new LazyDoubleCheckSingleton();` 分为三步执行：

 1. 为 lazy 分配内存空间
 2. 初始化 lazy 对象
 3. 将 lazy 指向刚分配的内存地址

 由于 JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2，在多线程环境下会导致一个线程获得还没有初始化的实例。
 
 使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。

#### 4. 静态内部类

这种形式兼顾饿汉式的内存浪费，也兼顾 synchronized 的性能问题。

```java
public class LazyInnerClassSingleton {

    private LazyInnerClassSingleton(){}

    public static final LazyInnerClassSingleton getInstance(){
        // 再返回结果之前，一定会先加载内部类
        return  LazyHolder.LAZY;
    }

    // 默认不加载，JVM 能够保证 LZAY 只被实例化一次
    private static class LazyHolder{
        private static final LazyInnerClassSingleton LAZY = new LazyInnerClassSingleton();
    }
}
```

上述的构造方法除了加上 private 之外，没有做任何处理，如果使用反射来调用其构造方法，然后再调用 getInstance() 方法，就会出现两个不同的实例。

```java
public static void main(String[] args) {
    try{
        // 很无聊的情况下进行破坏
        Class<?> clazz = LazyInnerClassSingleton.class;
        // 通过反射拿到私有的构造方法
        Constructor c = clazz.getDeclaredConstructor(null);
        // 强制访问
        c.setAccessible(true);
        //暴力初始化
        Object o1 = c.newInstance();
        // 调用了两次构造方法
        Object o2 = c.newInstance();
        System.out.println(o1 == o2);// false
    }catch (Exception e){
        e.printStackTrace();
    }
}
```

所以需要加一些限制，一旦出现多次重复创建，则直接抛出异常。

```java
private LazyInnerClassSingleton(){
    if(LazyHolder.LAZY != null){
        throw new RuntimeException("不允许创建多个实例");
    }
}
```

#### 5. 枚举式单例

将每一个实例都登记到某一个地方，使用唯一的标识获取实例，通过反编译可以看出，枚举式单例在静态代码中就给 INSTANCE 进行了赋值，是饿汉式单例的实现。

```java
public enum EnumSingleton {

    INSTANCE;
    private Object data;
    public Object getData(){
        return data;
    }
    public void setData(Object data){
        this.data = data;
    }
    public static EnumSingleton getInstance(){
        return INSTANCE;
    }
}
```
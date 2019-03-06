---
title: 软件架构设计的七大原则
permalink: /notes/java/internal/design-principles
key: internal-design-principles
---

## 开闭原则

开闭原则（Open-Closed Principle, OCP）是指一个软件实体如类、模块和函数应该对扩展开放，对修改关闭。所谓的开闭，也正是对扩展和修改两个行为的一个原则。强调的是用抽象构建框架，用实现扩展细节。可以提高软件系统的可复用性及可维护性。开闭原则，是面向对象设计中最基础的设计原则。它指导我们如何建立稳定灵活的系统，例如：我们版本更新，我尽可能不修改源代码，但是可以增加新功能。

在现实生活中对于开闭原则也有体现。比如，很多互联网公司都实行弹性制作息时间，规定每天工作 8 小时。意思就是说，对于每天工作 8 小时这个规定是关闭的，但是你什么时候来，什么时候走是开放的。早来早走，晚来晚走。

实现开闭原则的核心思想就是面向抽象编程，接下来我们来看一段代码：

以咕泡学院的课程体系为例，首先创建一个课程接口 ICourse：

```java
public interface ICourse {
  Integer getId();
  String getName();
  Double getPrice();
}
```
整个课程生态有 Java 架构、大数据、人工智能、前端、软件测试等，我们来创建一个 Java 架构课程的类 JavaCourse：

```java
public class JavaCourse implements ICourse{
  private Integer Id;
  private String name;
  private Double price;
  public JavaCourse(Integer id, String name, Double price) {
    this.Id = id;
    this.name = name;
    this.price = price;
  } 
  public Integer getId() {
   return this.Id;
  }
  public String getName() {
    return this.name;
  }
  public Double getPrice() {
    return this.price;
  }
}
```

现在我们要给 Java 架构课程做活动，价格优惠。如果修改 JavaCourse 中的 getPrice() 方法，则会存在一定的风险，可能影响其他地方的调用结果。我们如何在不修改原有代码前提前下，实现价格优惠这个功能呢？现在，我们再写一个处理优惠逻辑的类，JavaDiscountCourse 类（思考一下为什么要叫 JavaDiscountCourse，而不叫 DiscountCourse）：

```java
public class JavaDiscountCourse extends JavaCourse {
  public JavaDiscountCourse(Integer id, String name, Double price) {
    super(id, name, price);
  } 
  public Double getOriginPrice(){
    return super.getPrice();
  }
  public Double getPrice(){
    return super.getPrice() * 0.61;
  }
}
```
简要的类图为：

<div align="center">  <img src="/img/internal_design_principles_1.png" width="40%"/> </div><br>

## 依赖倒置原则

依赖倒置原则（Dependence Inversion Principle, DIP）是指设计代码结构时，高层模块不应该依赖底层模块，二者都应该依赖其抽象。抽象不应该依赖细节；细节应该依赖抽象。通过依赖倒置，可以减少类与类之间的耦合性，提高系统的稳定性，提高代码的可读性和可维护性，并能够降低修改程序所造成的风险。接下来看一个案例，还是以课程为例，先来创建一个类 Tom：

```java
public class Tom {
  public void studyJavaCourse(){
    System.out.println("Tom 在学习 Java 的课程");
  }
  public void studyPythonCourse(){
    System.out.println("Tom 在学习 Python 的课程");
  }
}
```

来调用一下：

```java
public static void main(String[] args) {
  Tom tom = new Tom();
  tom.studyJavaCourse();
  tom.studyPythonCourse();
}
```

Tom 热爱学习，目前正在学习 Java 课程和 Python 课程。大家都知道，学习也是会上瘾的。随着学习兴趣的暴涨，现在 Tom 还想学习 AI 人工智能的课程。这个时候，业务扩展，我们的代码要从底层到高层（调用层）一次修改代码。在 Tom 类中增加 studyAICourse() 方法，在高层也要追加调用。如此一来，系统发布以后，实际上是非常不稳定的，在修改代码的同时也会带来意想不到的风险。接下来我们优化代码，创建一个课程的抽象 ICourse 接口：

```java
public interface ICourse {
  void study();
}
```

然后写 JavaCourse 类：
```java
public class JavaCourse implements ICourse {
  @Override
  public void study() {
    System.out.println("Tom 在学习 Java 课程");
  }
}
```

再实现 PythonCourse 类：
```java
public class PythonCourse implements ICourse {
  @Override
  public void study() {
    System.out.println("Tom 在学习 Python 课程");
  }
}
```

修改 Tom 类：
```java
public class Tom {
  public void study(ICourse course){
    course.study();
  }
}
```

来看调用：
```java
public static void main(String[] args) {
  Tom tom = new Tom();
  tom.study(new JavaCourse());
  tom.study(new PythonCourse());
}
```

我们这时候再看来代码，Tom 的兴趣无论怎么暴涨，对于新的课程，我只需要新建一个类，通过传参的方式告诉 Tom，而不需要修改底层代码。实际上这是一种大家非常熟悉的方式，叫依赖注入。注入的方式还有构造器方式和 Setter 方式。我们来看构造器注入方式：

```java
public class Tom {
  private ICourse course;
  public Tom(ICourse course){
    this.course = course;
  }
  public void study(){
    course.study();
  }
}
```

看调用代码：
```java
public static void main(String[] args) {
  Tom tom = new Tom(new JavaCourse());
  tom.study();
}
```

根据构造器方式注入，在调用时，每次都要创建实例。那么，如果 Tom 是全局单例，则我们就只能选择用 Setter 方式来注入，继续修改 Tom 类的代码：
```java
public class Tom {
  private ICourse course;
  public void setCourse(ICourse course) {
  this.course = course;
  }
  public void study(){
    course.study();
  }
}
```

看调用代码：
```java
public static void main(String[] args) {
  Tom tom = new Tom();
  tom.setCourse(new JavaCourse());
  tom.study();
  tom.setCourse(new PythonCourse());
  tom.study();
}
```

<div align="center">  <img src="/img/internal_design_principles_2.png" width="40%"/> </div><br>

切记：以抽象为基准比以细节为基准搭建起来的架构要稳定得多，因此在拿到需求之后，要面向接口编程，先顶层再细节来设计代码结构。

## 单一职能原则

单一职责（Simple Responsibility Pinciple，SRP）是指不要存在多于一个导致类变更的原因。假设我们有一个 Class 负责两个职责，一旦发生需求变更，修改其中一个职责的逻辑代码，有可能会导致另一个职责的功能发生故障。这样一来，这个 Class 存在两个导致类变更的原因。如何解决这个问题呢？我们就要给两个职责分别用两个Class 来实现，进行解耦。后期需求变更维护互不影响。这样的设计，可以降低类的复杂度，提高类的可读性，提高系统的可维护性，降低变更引起的风险。总体来说就是一个 Class/Interface/Method 只负责一项职责。

接下来，我们来看代码实例，还是用课程举例，我们的课程有直播课和录播课。直播课不能快进和快退，录播可以可以任意的反复观看，功能职责不一样。还是先创建一个 Course 类：

```java
public class Course {
  public void study(String courseName){
    if("直播课".equals(courseName)){
      System.out.println(courseName + "不能快进");
    }else{
      System.out.println(courseName + "可以反复回看");
    }
  }
}
```

看代码调用：
```java
public static void main(String[] args) {
  Course course = new Course();
  course.study("直播课");
  course.study("录播课");
}
```

从上面代码来看，Course 类承担了两种处理逻辑。假如，现在要对课程进行加密，那么直播课和录播课的加密逻辑都不一样，必须要修改代码。而修改代码逻辑势必会相互影响容易造成不可控的风险。我们对职责进行分离解耦，来看代码，分别创建两个类ReplayCourse 和 LiveCourse：

LiveCourse 类：
```java
public class LiveCourse{
  public void study(String courseName){
    System.out.println(courseName + "可以反复回看");
  }
}
```

ReplayCourse 类：
```java
public class ReplayCourse {
  public void study(String courseName){
    System.out.println(courseName + "不能快进");
  }
}
```

调用代码：
```java
public static void main(String[] args) {
  LiveCourse liveCourse = new LiveCourse();
  liveCourse.study("直播课");
  
  ReplayCourse replayCourse = new ReplayCourse();
  replayCourse.study("录播课");
}
```

业务继续发展，课程要做权限。没有付费的学员可以获取课程基本信息，已经付费的学员可以获得视频流，即学习权限。那么对于控制课程层面上至少有两个职责。我们可以把展示职责和管理职责分离开来，都实现同一个抽象依赖。设计一个顶层接口,创建ICourse 接口：

```java
public interface ICourse {
  //获得基本信息
  String getCourseName();
  //获得视频流
  byte[] getCourseVideo();
  //学习课程
  void studyCourse();
  //退款
  void refundCourse();
}
```

可以把这个接口拆成两个接口，创建一个接口 ICourseInfo 和 ICourseManager：

ICourseInfo 接口：
```java
public interface ICourseInfo {
  String getCourseName();
  byte[] getCourseVideo();
}
```

ICourseManager 接口：
```java
public interface ICourseManager {
  void studyCourse();
  void refundCourse();
}
```

来看一下类图：

<div align="center">  <img src="/img/internal_design_principles_3.png" width="40%"/> </div><br>

下面我们来看一下方法层面的单一职责设计。有时候，我们为了偷懒，通常会把一个方法写成下面这样：

```java
private void modifyUserInfo(String userName,String address){
  userName = "Tom";
  address = "Changsha";
}
```

还可能写成这样：
```java
private void modifyUserInfo(String userName,String... fileds){
  userName = "Tom";
  //address = "Changsha";
}
private void modifyUserInfo(String userName,String address,boolean bool){
  if(bool){

  }else{

  }
  userName = "Tom";
  address = "Changsha";
}
```

显然，上面的 modifyUserInfo()方法中都承担了多个职责，既可以修改 userName,也可以修改 address，甚至更多，明显不符合单一职责。那么我们做如下修改，把这个方法拆成两个：
```java
private void modifyUserName(String userName){
  userName = "Tom";
}
private void modifyAddress(String address){
  address = "Changsha";
}
```

这修改之后，开发起来简单，维护起来也容易。但是，我们在实际开发中会项目依赖，组合，聚合这些关系，还有还有项目的规模，周期，技术人员的水平，对进度的把控，很多类都不符合单一职责。但是，我们在编写代码的过程，尽可能地让接口和方法保持单一职责，对我们项目后期的维护是有很大帮助的。

## 接口隔离原则

接口隔离原则（Interface Segregation Principle, ISP）是指用多个专门的接口，而不使用单一的总接口，客户端不应该依赖它不需要的接口。这个原则指导我们在设计接口时应当注意一下几点：

1. 一个类对一类的依赖应该建立在最小的接口之上。
2. 建立单一接口，不要建立庞大臃肿的接口。
3. 尽量细化接口，接口中的方法尽量少（不是越少越好，一定要适度）。

接口隔离原则符合我们常说的高内聚低耦合的设计思想，从而使得类具有很好的可读性、可扩展性和可维护性。我们在设计接口的时候，要多花时间去思考，要考虑业务模型，包括以后有可能发生变更的地方还要做一些预判。所以，对于抽象，对业务模型的理解是非常重要的。下面我们来看一段代码，写一个动物行为的抽象：

IAnimal 接口：
```java
public interface IAnimal {
  void eat();
  void fly();
  void swim();
}
```

Bird 类实现：
```java
public class Bird implements IAnimal {
  @Override
  public void eat() {}
  @Override
  public void fly() {}
  @Override
  public void swim() {}
}
```

Dog 类实现：
```java
public class Dog implements IAnimal {
  @Override
  public void eat() {}
  @Override
  public void fly() {}
  @Override
  public void swim() {}
}
```

可以看出，Bird 的 swim()方法可能只能空着，Dog 的 fly()方法显然不可能的。这时候，我们针对不同动物行为来设计不同的接口，分别设计 IEatAnimal，IFlyAnimal 和 ISwimAnimal 接口，来看代码：

IEatAnimal 接口：
```java
public interface IEatAnimal {
  void eat();
}
```

IFlyAnimal 接口：
```java
public interface IFlyAnimal {
  void fly();
}
```

ISwimAnimal 接口：
```java
public interface ISwimAnimal {
  void swim();
}
```

Dog 只实现 IEatAnimal 和 ISwimAnimal 接口：
```java
public class Dog implements ISwimAnimal,IEatAnimal {
  @Override
  public void eat() {}
  @Override
  public void swim() {}
}
```

来看下两种类图的对比，还是非常清晰明了的：





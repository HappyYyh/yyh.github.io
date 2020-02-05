---
title: Java基础
permalink: /interview/java/java-basis
key: java-basis
---

## **面向对象特性**

### **【函数式编程和面向对象编程的区别】**  

面向对象编程是封装，继承，多态，代码重用。

面向函数式编程是数据不可变，惰性求值



### **【面向对象的原则】**  

- 单一职责原则
- 开-闭原则
- 里氏替换原则
- 依赖倒置原则
- 接口隔离原则
- 迪米特法则



### **【面向对象的特性】**  

封装、继承、多态



### 【讲讲你所理解的Java面向对象】

Java是面向对象语言，面向对象特性封装、继承、多态，所以在我看来Java是一种抽象的编程思维，

可以把问题抽象成对象（Object），然后处理问题



### **【Java和c++区别】**  

1、C++创建对象之后，需要再使用完将其调用delete方法将其销毁；而java自动回收垃圾

2、作用域

3、C++有goto关键字，而Java作为保留字段

4、C++多继承，而Java只能单继承

5、C++有指针，而Java没有






## **JDK相关**

### **【JDK1.8为什么要引入函数式编程】**

函数式接口在Java中是指：有且仅有一个抽象方法的接口。

Java中的函数式编程体现就是Lambda



### **【JDK1.8的新特性】**  

- Lambda表达式

- 函数式接口

- 方法引用和构造器调用

- Stream API

- 接口中的默认方法和静态方法

- 新时间日期API

  

### **【JDK1.8和之前版本的不同】**  

同上



## **接口和类**

### **【Java的多态】**  

多态性的解释：子类对象的多态性，父类的引用指向子类对象

Java的多态分为运行时多态和编译时多态。

**编译时多态**：主要是指方法的重载，它是根据参数列表的不同来区分不同的方法。通过编译之后会变成两个不同的方法，在运行时谈不上多态。

**运行时多态**:它是通过动态绑定来实现的，也就是大家通常所说的多态性

即子类可以定义由父类创建，父类的引用指向子类对象

~~~java
List<T> list = new ArraryList<>(); 
~~~



### 【抽象类和接口你倾向用哪个？什么场景下用抽象类】  

1、更加倾向于使用接口；

2、这里要知道抽象类里不仅仅有抽象方法还有普通方法；可以看spring里面的一些源码



### 【类的关系，组合和聚合的区别、哪个关系更紧密】  

依赖(Dependency)关系是类与类之间的联接，**依赖关系在Java语言中体现为局部变量、方法的形参，或者对静态方法的调用。**

~~~java
class Car {  
    public static void run(){  
        System.out.println("汽车在奔跑");  
    }  
}  
   
class Driver {  
    //使用形参方式发生依赖关系  
    public void drive1(Car car){  
        car.run();  
    }  
    //使用局部变量发生依赖关系  
    public void drive2(){  
        Car car = new Car();  
        car.run();  
    }  
    //使用静态变量发生依赖关系  
    public void drive3(){  
        Car.run();  
    }  
}
~~~



关联(Association）关系是类与类之间的联接，**在Java语言中，关联关系一般使用成员变量来实现。**

~~~java
class Driver {  
    //使用成员变量形式实现关联  
    Car mycar;  
    public void drive(){  
        mycar.run();  
    }  
    ...  
    //使用方法参数形式实现关联  
    public void drive(Car car){  
        car.run();  
    }  
}
~~~



聚合(Aggregation) 关系是**关联**关系的一种，是**强的关联关系**

~~~java
class Driver {  
    //使用成员变量形式实现聚合关系  
    Car mycar;  
    public void drive(){  
        mycar.run();  
    }  
}
~~~



组合(Composition) 关系是**关联关系的一种，是比聚合关系强的关系**

~~~java
//为了表示组合关系，常常会使用构造方法来达到初始化的目的
public Driver(Car car){  
    mycar = car;  
}
~~~

**总结**

一般来说依赖、关联、聚合、组合四中关系中，类的关系是依次增强的




## **数据类型**

static修饰用法和区别 ?  
Java基本类型有多少种?  
Java中，char型变量能不能存储一个汉字  
谈谈对BigDecimal的理解   
说说对包装类的理解  
java 泛型理解？  

## **String相关**

String是否可以被继承及相关原因   ?  
String为什么要是final类型的;为什么String是不可变的？  
String是不是java的基本类型？  
StringBuilder 为什么比String快？  
String，StringBuffer，StringBuilder的区别?   
StringBuffer和StringBuilder哪个是线程安全的，他们分别适用于什么场景?  


## **异常**

异常分类，什么时候throws  
Exception 与 Error；  


## **注解的使用**

注解处理器  
怎么实现自定注解  


## **反射**

说一下反射，及你在项目中的应用   
了解浅拷贝和深拷贝的区别吗  
引用（四个强度之类的）  
string example=“一个网址”，求一个example的实例  
反射讲一下，写一个反射的例子   
反射机制的底层实现是什么  


## **其他**

==和equals区别？  
重写hashcode()是否需要重写equals()，不重写会有什么后果 ？  
不加任何修饰符的java最像哪个访问修饰符   
序列化和实现方式，作用？  
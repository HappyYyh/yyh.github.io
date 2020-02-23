---
title: Java基础
permalink: /interview/java/java-basis
key: java-basis
aside:
  toc: true
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



### **【接口设计需要考虑哪些方面】**

[原文：](https://www.cnblogs.com/accident/p/8872204.html)

1. 接口的命名。
2. 请求参数。
3. 支持的协议。
4. TPS、并发数、响应时长。
5. 数据存储。DB选型、缓存选型。
6. 是否需要依赖于第三方。
7. 接口是否拆分。
8. 接口是否需要幂等。
9. 防刷。
10. 接口限流、降级。
11. 负载均衡器支持。
12. 如何部署。
13. 是否需要服务治理。
14. 是否存在单点。
15. 接口是否资源包、预加载还是内置。
16. 是否需要本地缓存。
17. 是否需要分布式缓存、缓存穿透怎么办。
18. 是否需要白名单。



### 【接口设计的原则】  

- **原则一：**必须符合Restful，统一返回格式，约定业务层错误编码，每个编码可以携带可选的错误信息。

- **原则二：** 命名必须规范、优雅。

- **原则三：**单一性。

  单一性是指接口要做的事情应该是一个比较单一的事情，比如登陆接口，登陆完成应该只是返回登陆成功以后一些用户信息即可，但很多人为了减少接口交互，返回一大堆额外的数据。

  比如有人设计一个用户列表接口，接口他返回每一条数据都是包含用户了一大堆跟另外无关的数据，结果一问，原来其他无关的数据是他下一步想要获取的，想达成数据的懒加载。

- **原则四：**可扩展。

  接口扩展性，是指设计接口的时候多想想多种情况，多考虑各个方面，其实我觉得单独将扩展性放在这里也是不妥的，感觉说的跟单一性有点相反的意思，其实这个不是这个意思。

  这边的扩展性是指我们的接口充分考虑客户端，想想他们是如何调用的，他要怎样使用我的代码，他会如何扩展我的代码，不要把过多的工作写在你的接口里面，而应该把更多的主动权交给客户程序员。

  如获取不同的列表数据接口，我们不可能将每个列表都写成一个接口。 还有一点，我这里特别想指出来的是很多开发人员为了省事（姑且只能这么理解），将接口设计当成只是 app 页面展示。

  这些人将一个页面展示就用一个接口实现，而不考虑这些数据是不是属于不同的模块、是不是属于不同的展示范畴、结果下次视觉一改，整个接口又得重写，不能复用。

- **原则五：**必须有文档。

- **原则六：**产品心。

  为什么我说要有产品心？因为我觉得很多人忽略了这一点。我来说一下假如开发一个app，如果一开始连个交互文档给你都没有的话，你怎么设计接口？

  所以我觉得作为一个服务端后台开发人员应该要有产品心，特别是对于交互文档应该好好理解，因为这些都会对我们的接口设计有很大的影响。

  我在设计接口的时候就很常发现很多交互文档根本就走不通，产品没有考虑到位，交互文档缺失，这时候作为一个开发要主动推动，完善。

- **原则七：**第三方服务接口数据能缓存就缓存。

- **原则八：**第三方服务需要做降级。

- **原则九：**建议消除单点。

- **原则十：**接口粒度要小。

- **原则十一：**客户端能处理的逻辑就不要给服务端处理，减少服务端压力。

- **原则十二：**资源预加载。

- **原则十三：**不要过度设计。

- **原则十四：**缓存尽量不要穿透。

- **原则十五：**接口能缓存就缓存。

- **原则十六：**思辨大于执行



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

### 【static修饰用法和区别】

static是java的关键字，可以用来修饰属性、类、方法

**一、static修饰属性**
1.属性随着类的加载而加载，是类变量,其加载早于对象,不需要new即可加载

2.类变量所在的类的所有对象共用这一个属性,存放在静态域中

**二、static修饰方法**
1.方法随着类的加载而加载随着类的加载而加载，是类方法，其加载早于对象,不需要new

2.类方法所在的类的所有对象共用这一个方法.

3.类方法内部只可调用静态的属性和静态的方法，而不能调用非静态的属性和方法
反之，非静态方法可以调用静态的属性和方法

**三、static修饰内部类**
1普通类是不允许声明为静态的，只有内部类才可以

2被static修饰的内部类可以直接作为一个普通类来使用，而不需实例一个外部类



### 【Java基本类型有多少种】  

|  类型   |        精度范围         | 占位 |
| :-----: | :---------------------: | :--: |
|  byte   |        -128 ~127        |  8   |
|  short  |     -32768 ~ 32767      |  16  |
|   int   |    -2^31 ~ 2^31 - 1     |  32  |
|  long   |     -2^63 ~ 2^63 -1     |  64  |
|  float  |  -3.40E+38 ~ +3.40E+38  |  32  |
| double  | -1.79E+308 ~ +1.79E+308 |  64  |
| boolean |       true/false        |  8   |
|  char   |      Unicode 字符       |  16  |



### 【Java中，char型变量能不能存储一个汉字】  

char型变量是用来存储Unicode编码的字符的，unicode编码字符集中包含了汉字

补充：使用Unicode意味着字符在JVM内部和外部有不同的表现形式，在JVM内部都是Unicode，当这个字符被从JVM内部转移到外部时（例如存入文件系统中），需要进行编码转换。



### 【谈谈对BigDecimal的理解】   

一般用于货币计算

https://blog.csdn.net/qq_34530405/article/details/85718774



### 【说说对包装类的理解】  

Java5出现的概念，是对八大基本数据类型的封装

| 基本类型 | 包装类型  |
| :------: | :-------: |
|   byte   |   Byte    |
|  short   |   Short   |
|   int    |  Integer  |
|   long   |   Long    |
|  float   |   Float   |
|  double  |  Double   |
| boolean  |  Boolean  |
|   char   | Character |

1、可以通过包装类于基本数据类型进行自动拆装箱

2、包装类的初始值为null，不同于基本类型

3、泛型无法使用包装类型



### 【java 泛型理解】  

**一、概述**

Java泛型（`generics`）是JDK 5中引入的一个新特性，**泛型提供了编译时类型安全监测机制**，该机制允许程序员在编译时监测非法的类型。使用泛型机制编写的程序代码要比那些杂乱地使用`Object`变量，然后再进行强制类型转换的代码具有**更好的安全性和可读性**。泛型对于集合类尤其有用，例如，`ArrayList`就是一个无处不在的集合类。

**泛型的本质是参数化类型**，也就是所操作的数据类型被指定为一个参数。

**二、泛型的使用**

泛型有三种常用的使用方式：**泛型类**，**泛型接口**和**泛型方法**。





## **String相关**

### 【String是否可以被继承及相关原因】

`String.class`是final修饰的，无法被继承



### 【String为什么要是final类型的;为什么String是不可变的】

主要是为了”安全性“和”效率“的缘故

1、因为**只有当字符串是不可变的，字符串池才有可能实现**。字符串池的实现可以在运行时节约很多heap空间，因为不同的字符串变量都指向池中的同一个字符串。但如果字符串是可变的，那么String interning将不能实现，因为这样的话，如果变量改变了它的值，那么其它指向这个值的变量的值也会一起改变

------

 2、如果字符串是可变的，那么会引起很严重的安全问题。譬如，数据库的用户名、密码都是以字符串的形式传入来获得数据库的连接，或者在socket编程中，主机名和端口都是以字符串的形式传入。因为字符串是不可变的，所以它的值是不可改变的，否则黑客们可以钻到空子，改变字符串指向的对象的值，造成安全漏洞。

------

3、 因为字符串是不可变的，所以**是多线程安全的**，同一个字符串实例可以被多个线程共享。这样便不用因为线程安全问题而使用同步。字符串自己便是线程安全的。

------

4、  因为字符串是不可变的，所以在它创建的时候**HashCode**就被缓存了，不需要重新计算。这就使得字符串很适合作为Map中的键，字符串的处理速度要快过其它的键对象。这就是HashMap中的键往往都使用字符串。





### 【String是不是java的基本类型】

不是



### 【StringBuilder 为什么比String快】

因为String是不可变的，所以在大量拼接等操作字符串需要创建很多个对象，而StringBuilder的操作是同一个对象（字符数组扩容）



### 【String，StringBuffer，StringBuilder的区别】   

**String** : 里面所有的属性几乎也是final,由于它的不可变性，类似拼接，裁剪字符串等动作都会产生大量无用的中间对象。由于字符串操作在项目中很常见，所以对String的操作对项目的性能往往有很明显的影响。

**StringBuffer** : 这个类是为了解决String拼接产生多余对象的问题而提供的一个类。StringBuffer保证了线程的安全，也带来了多余的开销。

**StringBuilder** : StringBuilder的功能与StringBuffer一样。但是区别是StringBuilder没有处理线程安全，减少了开销。




## **异常**

### 【Exception 与 Error】

在java中程序的运行往往会因为设计或者编写过程中引起一些错误的操作，这些错误信息主要包含两种类型

**错误(Error)**：通常是JVM内部错误，或者资源耗尽等一些无法从本质上解决的问题(严重问题)

**异常(Exception)**:因为一些编程错误或者外在因素引起的可以被修复的问题

Error和Exception都是从Throwable类继承过来



### 【异常分类】

1、运行时异常（RuntimeException）:在程序运行时才会出现

- java.lang.NullPointerException<空指针异常>

- java.lang.IndexOutOfBoundsException< 索引超出范围>

- java.lang.ArrayIndexOutOfBoundsException<数组索引越界>

- java.lang.NumberFormatException<转换为数值类型异常>
- java.lang.ClassCastException <类型转换异常>

- java.lang.ArithmeticException <算数异常>




2、一般异常(检查异常)：在编译期就显式的通知程序员必须处理

- java.lang.ClassNotFoundException<类未找到异常>

- java.io.IOException<IO异常>




### 【什么时候throws】


 java程序在运行时或者编译时如果出现异常，则会产生异常对象，对于该异常对象的处理方法，通常包含两种解决方法：

1. 异常抛出（throws）
2. 异常捕获（try...catch...fianlly）





## **注解的使用**

### 【注解处理器】

[原文](https://segmentfault.com/a/1190000019757327?utm_source=tag-newest)

lombok就是一个注解处理器

**概念**

注解处理器其实全称叫Pluggable Annotation Processing API,插入式注解处理器,它是对JSR269提案的实现,具体可以看链接里面的内容,[JSR269链接](https://jcp.org/aboutJava/communityprocess/final/jsr269/index.html).

工作过程：

1. parse and enter:解析和输入,java编译器这个阶段会把源代码解析生成AST(抽象语法分析树)
2. annotation processing:注解处理器阶段,此时将调用注解处理器,这时候可以校验代码,生成新文件等等(处理完可以循环到第一步)
3. analyse and generate:分析和生成,此时前两步完成后,生成字节码(这个阶段进行了解糖,比如类型擦除)

**实践**

在一个类上加上@InterfaceAnnotation,编译的时候去生成一个"I"+类名的接口类。

两个步骤:
1.自定义一个注解

~~~java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.SOURCE)
public @interface InterfaceAnnotation {
}
~~~

2.继承AbstractProcessor并且实现process方法

~~~java
@SupportedAnnotationTypes(value = {"com.example.processor.InterfaceAnnotation"})
@SupportedSourceVersion(value = SourceVersion.RELEASE_8)
public class InterfaceProcessor extends AbstractProcessor {

    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
        Messager messager = processingEnv.getMessager();
        messager.printMessage(Diagnostic.Kind.NOTE, "进入到InterfaceProcessor中了~~~");
        // 将带有InterfaceProcessor的类给找出来
        Set<? extends Element> clazz = roundEnv.getElementsAnnotatedWith(InterfaceAnnotation.class);
        clazz.forEach(item -> {
            // 生成一个 I + 类名的接口类
            String className = item.getSimpleName().toString();
            className = "I" + className.substring(0, 1) + className.substring(1);
            TypeSpec typeSpec = TypeSpec.interfaceBuilder(className).addModifiers(Modifier.PUBLIC).build();

            try {
                // 生成java文件
                JavaFile.builder("com.example.processor", typeSpec).build().writeTo(new File("./src/main/java/"));
            } catch (IOException e) {
                e.printStackTrace();
            }
        });
        return true;
    }
}
~~~

1.@SupportedAnnotationTypes:表示这个processor类要对什么注解生效  
2.@SupportedSourceVersion:表示支持的java版本  
3.annotations:被要求的注解,就是@SupportedAnnotationTypes对应的注解  
4.roundEnv:存放着当前和上一轮processing的环境信息  
5.TypeSpec这个可能有点没看懂是干嘛的,它是javaPoet中的一个类,javaPoet是java用于生成java文件的一款第三方插件很好用,所以这里使用了这个类来生成java文件,  
实际上这里用java自带的PrintWriter等输入输出流也可以生成java文件,生成文件有很多方式  
6.Messager是用来打印输出信息的,System.out.println其实也可以;  
7.process如果返回是true后续的注解处理器就不会再处理这个注解,如果是false,在下一轮processing中,其他注解处理器也会来处理改注解.   

写好之后,这里需要指定processor,META-INF/services/javax.annotation.processing.Processor 写好com.example.processor.InterfaceProcessor.如果你不知道这是啥,可以看下我另一篇博客(实力推广XD)[什么是SPI](https://juejin.im/post/5d0629bfe51d45772a49ad41)
我们在把注解处理器给编译好,maven里插件的设置:

```maven
<plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.7.0</version>
        <configuration>
              <source>1.8</source>
              <target>1.8</target>
              <!-- 不加这一句编译会报找不到processor的异常-->
              <compilerArgument>-proc:none</compilerArgument>
        </configuration>
</plugin>
```

此时的目录结构是这样:

```
.
├── HELP.md
├── pom.xml
├── processor.iml
└── src
    └── main
        ├── java
        │   └── com
        │       └── example
        │           └── processor
        │               ├── InterfaceAnnotation.java
        │               └── InterfaceProcessor.java
        └── resources
            └── META-INF
                └── services
                    └── javax.annotation.processing.Processor
```

然后mvn clean install.

**第三步:使用注解**

在使用之前呢,注解处理器要是编译好的.引入注解处理器的jar包.
测试类加上@InterfaceAnnotation

```java
@InterfaceAnnotation
public class TestProcessor {
}
```

maven指定编译时使用的注解处理器.

```
<plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.7.0</version>
        <configuration>
              <source>1.8</source>
              <target>1.8</target>
              <encoding>UTF-8</encoding>
              <annotationProcessors>
                  <annotationProcessor>
                        com.example.processor.InterfaceProcessor
                  </annotationProcessor>
              </annotationProcessors>
        </configuration>
</plugin>
```

此时目录结构是

```
.
├── HELP.md
├── pom.xml
├── src
│   └── main
│       ├── java
│       │   └── com
│       │       └── example
│       │           └── test
│       │               └── TestProcessor.java
│       └── resources
└── test.iml
```

然后mvn compile,生成了java文件,此时目录结构是:

```
.
├── HELP.md
├── pom.xml
├── src
│   └── main
│       ├── java
│       │   └── com
│       │       └── example
│       │           ├── processor
│       │           │   └── ITestProcessor.java  // 这里就是生成的java文件
│       │           └── test
│       │               └── TestProcessor.java
│       └── resources
├── target
│   ├── classes
│   │   └── com
│   │       └── example
│   │           └── test
│   │               └── TestProcessor.class
│   ├── generated-sources
│   │   └── annotations
│   └── maven-status
│       └── maven-compiler-plugin
│           └── compile
│               └── default-compile
│                   ├── createdFiles.lst
│                   └── inputFiles.lst
└── test.iml
```

看到了生成的java文件就大功告成~

**总结:**

1.java注解处理器在很多地方都可以使用,实际应用比如lombok,安卓生成fragment等等,只使用一个注解可以省去很多代码,提高效率;  
2.本文只是列举了一个很简单的例子,很多注解处理器里面的api都没有使用到,读者有兴趣的可以自行研究,而且有涉及到抽象语法树的api;  
3.注解处理器可以用于生成新的类来完成某些功能,但是不能直接修改当前的类.  



### 【怎么实现自定义注解】  

注解是形如接口，形式如下：

~~~java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD})
public @interface Annotations {
    
}
~~~

上面的含义：

1、@Target（用于描述修饰对象的范围）范围取值于`ElementType`这个枚举类

- ANNOTATION_TYPE（注释类型声明）

- CONSTRUCTOR(构造方法声明)

- FIELD (字段声明)

- LOCAL_VARIABLE(局部变量声明)

- METHOD (方法声明)

- PACKAGE(包声明)

- PARAMETER(参数声明)

- TYPE(类、接口（包括注释类型）或枚举声明)

2、@Retention（注释类型的注释要保留多久）范围取值于`RetentionPolicy`这个枚举类：

- CLASS 编译器将把注释记录在类文件中，但在运行时 VM 不需要保留注释
- RUNTIME  编译器将把注释记录在类文件中，在运行时 VM 将保留注释，因此可以反射性地读取
- SOURCE 编译器要丢弃的注释

3、@Document（进行文档转化）


4、@Inhrited(被标注的类型是被继承的)



## **反射**

### 【反射的概念】

~~~
Java的反射（reflection）机制是指在程序的运行状态中，可以构造任意一个类的对象，可以了解任意一个对象所属的类，可以了解任意一个类的成员变量和方法，可以调用任意一个对象的属性和方法。这种动态获取程序信息以及动态调用对象的功能称为Java语言的反射机制。反射被视为动态语言的关键
                                                                               --百度百科
~~~

反射获取类实例：

~~~java
Class<?> cls=Stu.getClass();
		 
Class<?> cls=Student.class;
		 
Class<?> cls=Class.forName("com.bit.javse.HomeWork.Student");
~~~



### 【写一个反射的例子 】

关于method：

~~~java
public class ReflectCase {

    public static void main(String[] args) throws Exception {
        Proxy target = new Proxy();
        Method method = Proxy.class.getDeclaredMethod("run");
        method.invoke(target);
    }

    static class Proxy {
        public void run() {
            System.out.println("run");
        }
    }
}
~~~

关于field

~~~java
public static <T> T map2Bean(Map<String, Object> map, Class<T> clazz) {
        try {
            //获取一个class实例
            T result = clazz.newInstance();
            //获取所有字段
            Field[] fields = result.getClass().getDeclaredFields();
            for (Field field : fields) {
                field.setAccessible(true);
                if (excludeField.contains(field.getName())) {
                    continue;
                }
                field.set(result, map.getOrDefault(field.getName(), null));
            }
            return result;
        } catch (InstantiationException | IllegalAccessException e) {
            logger.error("exception",e);
        }
        return null;
    }
~~~



### 【反射在项目中的应用】   

1、写工具类的时候会需要用到反射获取类实例

2、写aspect切面的时候会用到反射



### 【反射机制的底层实现是什么】

这个需要翻源码，后续会出源码



### 【浅拷贝和深拷贝的区别】

深复制和浅复制最根本的区别在于是否是真正获取了一个对象的复制**实体**，而不是引用。   

**浅复制** —-只是拷贝了基本类型的数据，而引用类型数据，复制后也是会发生引用，我们把这种拷贝叫做“（浅复制）浅拷贝”，换句话说，浅复制仅仅是指向被复制的内存地址，如果原地址中对象被改变了，那么浅复制出来的对象也会相应改变。  
**深复制** —-在计算机中开辟了一块新的内存地址用于存放复制的对象。





### 【引用强度】  

```dart
>强引用 
        在程序代码之中普遍存在，类似“Obejct obj=new Object()”这类的引用，
        只要强引用存在，垃圾收集器永远不会回收掉被引用的对象。 内存不足，系统抛出异常OutOfMemoryError

>软引用
        用来 描述一些还有用，但并非必需的对象。对于软引用关联的对象，
        在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中并进行第二次回收。
        如果这次回收还没有足够的内存，才会抛出内存溢出异常。

>弱引用
        也是用来描述非必需对象的，但是它的强度比软引用更弱一些，
        被弱引用关联的对象只能生存到下一次垃圾收集发生之前。
        当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。

>虚引用
        它是最弱的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。
        为一个对象设置虚引用关联的唯一目的就是希望能在这个对象被收集器回收时收到一个系统通知。
```



### 【string example=“一个网址”，求一个example的实例】

~~~java
 //1.调用运行时类本身的.class属性
Class clazz1 = String.class;

 //2.通过运行时类的对象获取
Class clazz2 = example.getClass();

//3、forName
Class clazz3 = Class.forName(example);

//4、类加载器
ClassLoader classLoader = this.getClass().getClassLoader();
Class clazz4 = classLoader.loadClass(example);
~~~








## **其他**

### 【==和equals区别】

==比较的是两个对象的物理地址

而equals默认使用的object类的比较，即地址的比较，而string重写了equals的方法，比较的是 内容



### 【重写equals是否需要重写hashCode，不重写会有什么后果】

需要重写。

我们知道hash表是利用对象的hash值进行定位的，而hash值就是调用的hashcode方法，如果不重写，那么在运用hash表的时候会造成多个对象相同或者相同对象不同

### 【不加任何修饰符的java最像哪个访问修饰符】

default

### 【序列化和实现方式，作用】  

Java序列化是指把Java对象转换为字节序列的过程，用于存储在硬盘上或者进行网络传输

**序列化实现方式：**

1、首先序列化的类必须实现以下两个接口：

①实现Serializable接口，这个接口是一个空接口，只是用于标注该类序列化

如果某些字段不需要序列化，用`transient`关键字表示或者静态变量也不会被序列化

②实现Externalizable接口，这个接口实现了Serializable，并且提供了两个方法

~~~java
 /**
  * 序列化操作的扩展类
  */
void writeExternal(ObjectOutput out) throws IOException;
 /**
  * 反序列化操作的扩展类
  */
void readExternal(ObjectInput in) throws IOException, ClassNotFoundException;
~~~



2、序列化形如：

~~~java
//创建一个对象输出流，它可以包装一个其它类型的目标输出流，如文件输出流：
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("D:\\object.out"));
//通过对象输出流的writeObject()方法写对象：
oos.writeObject(new User("xuliugen", "123456", "male"));
~~~

3、反序列化

~~~java
//创建一个对象输入流，它可以包装一个其它类型输入流，如文件输入流：
ObjectInputStream ois= new ObjectInputStream(new FileInputStream("object.out"));
//通过对象输出流的readObject()方法读取对象：
User user = (User) ois.readObject();
~~~


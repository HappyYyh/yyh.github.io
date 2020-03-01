---
title: Spring
permalink: /interview/frameworkAndTools/spring
key: frameworkAndTools-spring
---

## Spring

### 【讲一讲对Spring的理解】

Spring是一个轻量级控制反转(IoC)和面向切面(AOP)的**容器**框架。



### 【讲一下spring的作用是什么】

1. spring的**轻量级**是是从它的大小和开销来说的，完整的Spring框架可以在一个大小只有1MB多的JAR文件里发布。并且Spring所需的处理开销也是微不足道的。

2. Spring是**非侵入式**的,spring的api是不会出现在业务逻辑上出现的，对于应用而言，业务逻辑可以从当前应用剥离出来，实现复用，对于框架而言，业务逻辑也可以从spring框架中快速的移植到别的框架
3. spring**提供容器功能**，容器可以管理对象的生命周期，对象和对象之间的依赖关系等。通常我们都是可以写一个配置文件，在上面定义对象的名字等，在容器启动以后，这些对象就被实例化好了，我们可以直接去用。而且依赖关系也建立好了。

4. spring的**ioc**指的是控制权的转移，将控制权交给容器，调用者可以专心自己的业务逻辑就可以了。对象控制权由调用者移交给容器，使得调用者不必关心对象的创建和管理，专注于业务逻辑开发；解耦对象间的依赖关系，避免通过硬编码的方式耦合在一起；

5. spring的**aop**是面向切面编程，一种新的模块化方式，专门处理系统各模块中的交叉关注点问题，将具有横切性质的系统级业务提取到切面中，与核心业务逻辑分离(解耦)；

6. spring可以很好的和别的**框架组合**



### 【spring bean是什么】

官方文档对于bean的解释：

> In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.

翻译过来就是：

> 在 Spring 中，构成应用程序**主干**并由**Spring IoC容器**管理的**对象**称为**bean**。bean是一个由Spring IoC容器实例化、组装和管理的对象。

所以总结

1. bean是对象，一个或者多个不限定
2. bean由Spring中一个叫IoC的东西管理
3. 我们的应用程序由一个个bean构成



### 【Spring Bean的生命周期，几种scope区别】

Spring Bean的完整生命周期从创建Spring容器开始，直到最终Spring容器销毁Bean，这其中包含了一系列关键点



### 【spring BeanDefinition作用】   

### 【beanFactory和factoryBean的区别】

  

### 【说一下spring容器的启动过程】 



## IOC

谈谈你对SpringIOC和DI的理解  
spring @Autowired (@Resource, 类似） 怎么做的   
看过IOC和AOP的源码吗？它们底层是如何实现的  
spring IOC 过程  
讲一下ioc容器的启动过程  
Spring如何解决循环依赖  
如何自己设计IOC框架   



## AOP

Spring的AOP理解  
什么是AOP？实现原理是什么？  
什么是静态代理和动态代理？它们的优缺点  
什么是动态代理?有几种实现  
JDK动态代理和CGLIB动态代理的区别？  
哪种情况下用JDK动态代理，哪种情况下用CGLIB动态代理？   
你知道AOP的底层实现原理吗？   



## SpringMVC

springmvc处理流程  
SpringMVC不同用户登录的信息怎么保证线程安全的？  

## SpringBoot

对SpringBoot的理解  
SpringBoot是如何工作的  
用过springboot吗  
Springboot的自动配置   
Spring boot定时任务   
Spring boot注解和原理  
SpringMVC、Spring和SpringBoot的区别  
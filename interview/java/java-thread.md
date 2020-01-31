---
title: Java多线程
permalink: /interview/java/java-thread
key: java-thread
---

## **进程**

进程间通信方式，socket和消息队列区别  
ipc的常见方式(进程通信)  
信号量  
线程间的通信  
如何在两个进程共享数据  


## **线程和线程池**

如何让两个线程共享数据  
线程和协程的区别   
java线程启动、终止方式  
wait、notify等方法属于哪个类？为什么不属于Thread?  
如何中断线程，await和sleep区别?  
线程的中断、休眠相关以及区别  
线程不安全的例子，如何解决  
杀死线程池中线程的方法  
线程池数量和io型、cpu型,原因  
多线程进行1到100加和操作。开10个线程进行加和  
线程的状态  
创建线程的方式   
Runnable和callable区别  
线程池的好处   
线程池的参数   
线程池的实现？  
线程池的几个参数的作用？  
线程池的工作原理，核心线程数和最大线程数什么时候用到  
线程池的内容，如何定义自己的拒绝策略  
对线程安全的理解  
如何保证线程安全？   
阻塞队列有哪几个实现？  
怎么保证多线程的运行安全性  
关于多线程锁的升级原理  
线程池的线程是不是必须手动remove才可以回收value？  
内存泄漏是指主线程还是线程池  
线程池的线程数怎么确定   
实现2个线程循环打印  
双线程交替打印奇偶  


## **锁**

如何理解锁的概念，列举出几个锁来说明它   
对java中锁的理解  
了解过哪些锁  
什么是自旋锁？  
自旋锁和阻塞锁的区别  
公平锁和非公平锁的区别  
那么公平锁是怎样的底层实现  
说说你对乐观锁、悲观锁的理解  
死锁场景  
死循环怎么造成的  
加锁的静态方法和普通方法区别  
有看过synchronize的源码吗？
synchronize的实现原理    
jdk对synchronize的优化  
synchronized 和 lock 区别   
这两种锁在Java和MySQL分别是怎么实现的？  
jdk中哪种数据结构或工具可以实现当多个线程到达某个状态时执行一段代码   
栅栏和闭锁的区别   
Java程序启动至少启动几个线程  
创建线程以及线程运行时代码的方式  
ReadWriteLock与ReentrantReadWriteLock的理解和区别  
ReentrantReadWriteLock哪些特性   
那谈谈Lock接口的API以及其实现类相关的了解  
ReentrantLock的理解  
ReentrantLock中的lock和unlock之间的同步如何进行线程间的通信   



## **Voliate**

volatile底层实现  
说一说volatile关键字的作用？它为什么能保证可见性？  
volatile关键字是如何保证可见性的  
为什么uniqueInstance声明使用volitie修饰  



## java内存模型

java内存模型    
简单阐述下Java内存模型内部原理  
Java中多线程操作下的三个特性  



## **CAS**

cas的ABA问题怎么解决？  
对CAS的理解，java中的CAS，如何不用unsafe实现CAS  
atomic下的原子类有用到吗？采用了CAS  
CAS算法在哪里有应用？  
什么是CAS算法   
原子类底层机制(cas, Unsafe)   


## **TreadLocal** 

有没有用过TreadLocal  
介绍ThreadLocal   
主线程的ThreadLocal怎么传递到线程池？  


## **JUC**和AQS

JUC包下面了解哪些？  
JUC包下的计数锁？CountDownLatch  
CyclicBarrier的理解  
Semaphore类的了解  
谈谈你对AbstractQueuedSynchronizer的理解  
根据AQS实现的几种锁   
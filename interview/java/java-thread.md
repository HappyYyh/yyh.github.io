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



**做题：手写一个对象池**   

**1.** **对象池**

当调用对象时，不使用常规的new 构造子的方式，而是通过一个对象池操作。即如果池中存在该对象，则取出；如果不存在，则新建一个对象并存储在池中。当使用完该对象后，则将该对象的归还给对象池。

这里会存在几个问题，必须注意。

Tips 1，考虑多线程状态下的存取对象；

Tips 2，考虑将对象池目录表设计为Singleton模式，这样使得内存中仅存在唯一的一份缓存对象的表。

**2.对象单元设计**

每个对象单元指定一种类型的对象，由Class<T> type维护。对象单元有两个List，List<T> items用于存放该类型的同类对象，List<Boolean> checkedOut用于指定当前是否可用。

设置信号量**int** semaphore，当semaphore < items.size()说明目前List中还有“空闲”的对象。每次取出对象后需semaphore++，归还对象后需semaphore--。

对象单元ObjectUnit.java

~~~java
import java.util.ArrayList;
import java.util.List;
 

public class ObjectUnit<T> {
    private Class<T> type;
    private List<T> items = new ArrayList<T>();
    private List<Boolean> checkedOut = new ArrayList<Boolean>();
    private int semaphore;
 

    public ObjectUnit(Class<T> type) {
       this.type = type;
    }
 

    public synchronized T addItem() {
       T obj;
       try {
           obj = type.newInstance();
       } catch (Exception e) {
           throw new RuntimeException(e);
       }
       items.add(obj);
       checkedOut.add(false);
       return obj;
    }
 

    public synchronized T checkOut() {
       if (semaphore < items.size()) {
           semaphore++;
           return getItem();
       } else
           return addItem();
    }
 

    public synchronized void checkIn(T x) {
       if (releaseItem(x))
           semaphore--;
    }
 

    private synchronized T getItem() {
       for (int index = 0; index < checkedOut.size(); index++)
           if (!checkedOut.get(index)) {
              checkedOut.set(index, true);
              return items.get(index);
           }
       return null;
    }
 

    private synchronized boolean releaseItem(T item) {
       int index = items.indexOf(item);
       if (index == -1)
           return false; // Not in the list
       if (checkedOut.get(index)) {
           checkedOut.set(index, false);
           return true;
       }
       return false;
    }
}
~~~

**3.对象池目录表设计**

使用Map<Class<?>, ObjectUnit<?>>来保存当前对象池中类型目录，并把它设计为线程安全的ConcurrentHashMap。

这里的getObj方法和renObj方法不用加锁，因为它调用的对象单元类是线程安全的，并且Map是线程安全的。

此外，这里在处理泛型的时候，会有warning产生，因为之前定义Map中使用<?>，而后面的两个泛型方法指定<T>。还没有想到更好的解决办法。

Provider.java

~~~java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
 

public class Provider {
    private Map<Class<?>, ObjectUnit<?>> providers = new ConcurrentHashMap<Class<?>, ObjectUnit<?>>();
    private static Provider instance = new Provider();
 

    private Provider() {
    }
 

    public static Provider getInstance() {
       return instance;
    }
 

    @SuppressWarnings("unchecked")
    public <T> T getObj(Class<T> key) {
       ObjectUnit value = providers.get(key);
        if (value != null) {
           return (T) value.checkOut();
       } else {
           value = new ObjectUnit<T>(key);
           providers.put(key, value);
           return (T) value.addItem();
       }
    }
 

    @SuppressWarnings("unchecked")
    public <T> void renObj(T x) {
       if (providers.containsKey(x.getClass())) {
           ObjectUnit value = providers.get(x.getClass());
           value.checkIn(x);
       }
    }
}
~~~




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
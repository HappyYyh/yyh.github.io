---
title: Java多线程
permalink: /interview/java/java-thread
key: java-thread
---

## **进程**

### 【进程间通信(Ipc)方式】

- ​	管道
- ​	FIFO(命名管道)
- ​	消息队列
- ​	信号量(semaphore)
- ​	共享内存



### 【socket和消息队列区别】

​	socket（套接字）是是计算机之间进行**通信**的**一种约定**或一种方式

​	消息队列MQ(Message Queue)可以简单理解为：把要传输的数据放在队列中



### 【信号量】  

 	信号量的使用主要是用来保护共享资源，使得资源在一个时刻只有一个进程（线程）所拥有



### 【如何在两个进程共享数据】  

​	共享内存

​	

## **线程**

### 【线程间的通信】 

- ​	**同步**

  ​	使用Synchronize类似于共享内存

- ​	**while轮询**

- ​	**wait/notify**

  ​	线程的等待唤醒机制

- ​	**管道通信**

  ​	使用java.io.PipedInputStream 和 java.io.PipedOutputStream进行通信



### 【如何让两个线程共享数据】

​	即在线程中共享对象

​	

### 【线程和协程的区别】

   **协程是一种用户态的轻量级线程**，协程的调度完全由用户控制。从技术的角度来说，“协程就是你可以暂停执行的函数”。协程拥有自己的寄存器上下文和栈。协程调度切换时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈，直接操作栈则基本没有内核切换的开销，可以不加锁的访问全局变量，所以上下文的切换非常快。


1. 一个线程可以多个协程，一个进程也可以单独拥有多个协程。
2. 线程进程都是同步机制，而协程则是异步。

3. 协程能保留上一次调用时的状态，每次过程重入时，就相当于进入上一次调用的状态。

4. 线程是抢占式，而协程是非抢占式的，所以需要用户自己释放使用权来切换到其他协程，因此同一时间其实只有一个协程拥有运行权，相当于单线程的能力。

5. 协程并不是取代线程, 而且抽象于线程之上, 线程是被分割的CPU资源, 协程是组织好的代码流程, 协程需要线程来承载运行, 线程是协程的资源, 但协程不会直接使用线程, 协程直接利用的是执行器(Interceptor), 执行器可以关联任意线程或线程池, 可以使当前线程, UI线程, 或新建新程.。

6. 线程是协程的资源。协程通过Interceptor来间接使用线程这个资源。
   


### 【线程的状态】

  **1. 新建状态(New):** 

​      线程对象被创建后，就进入了新建状态。例如，Thread thread = new Thread()。

  **2. 就绪状态(Runnable):** 

​     也被称为“可执行状态”。线程对象被创建后，其它线程调用了该对象的start()方法，从而来启动该线程。例如，thread.start()。处于就绪状态的线程，随时可能被CPU调度执行。

  **3. 运行状态(Running):** 

​     线程获取CPU权限进行执行。需要注意的是，线程只能从就绪状态进入到运行状态。

  **4. 阻塞状态(Blocked):** 

​     阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。阻塞的情况分三种：

- (01) 等待阻塞 -- 通过调用线程的wait()方法，让线程等待某工作的完成。
- (02) 同步阻塞 -- 线程在获取synchronized同步锁失败(因为锁被其它线程所占用)，它会进入同步阻塞状态。
- (03) 其他阻塞 -- 通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。

  **5. 死亡状态(Dead):** 线程执行完了或者因异常退出了run()方法，该线程结束生命周期。



### 【java线程启动、终止方式】  

​	启动：线程初始化以后，调用start方法即可启动线程

​	终止：

​	1、**自然**终止：要么是run执行完成了，要么是抛出了一个未处理的异常导致线程提前结束。

​	2、**手动**终止：暂停、恢复和停止操作对应在线程Thread的API就是suspend（）、resume（）和stop（）。但		  是这些API是过期的，也就是不建议使用的； 安全的终止则是**其他线程**通过调用某个线程A的interrupt（）		  方法对其进行中断操作  



### 【wait和sleep区别 】

​	1、wait是属于Obejct的方法，当调用时线程会放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象调用notify()方法后本线程才进入对象锁定池准备获取对象锁进入运行状态。

​	2、sleep是属于Thread的方法，当调用时会导致程序暂停执行指定的时间，让出cpu给其他线程，但是他的监控状态依然保持者，当指定的时间到了又会自动恢复运行状态。线程不会释放对象锁



### 【线程的中断、休眠相关以及区别】

​	中断`interrup()`

​	休眠`sleep()`



### 【wait、notify等方法属于哪个类？为什么不属于Thread?】

​	属于Obejct类

​	原因：Java提供的锁是对象级的而不是线程级的，每个对象都有锁，通过线程获得。简单的说，由于wait，notify，notifyAll都是锁级别的操作，所以把他们定义在object类中因为锁属于对象。



### 【创建线程的方式 】

1. ​	继承Thread类

2. ​	实现Runnable接口

3. ​	实现Callable接口

   

### 【Runnable和callable区别】

​	callable相比于runnable可以有返回值，也可以手动抛出异常



### 【对线程安全的理解 】

​	多线程程序里面，如果对一个类，对象，方法，变量的操作不需要做额外的加锁，同步等操作，我们就认为它是线程安全的。     

​	在网上有另一种比较通俗的解释，如果多线程的程序的运行过程和单线程运行的结果一致，则认为它是线程安全的。  

​	线程安全问题，通常都是由于对共享资源的竞争导致的，这里的共享资源反映在代码里面一般是全局变量或者静态变量。



### 【如何保证线程安全】

1. ​	互斥同步：Synchronize和ReentrantLook
2. ​	非阻塞同步：乐观锁（Cas）
3. ​	可重入代码
4. ​	ThreedLocal

   

### 【线程不安全的例子，如何解决】  

这个代码多线程下修改同一个变量肯定会有问题，使用AtomicInteger

~~~java
public class UserStat {
    int userCount;
    public int getUserCount() {
        return userCount;
    }
    public void increment() {
        userCount++;
    }
    public void decrement() {
        userCount--;
    }
}

~~~





### 【做题：实现2个线程循环打印/双线程交替打印奇偶】

wait/notify，性能比较差

~~~java
	public static int i = 1;
    public static final int TOTAL = 100;
    public static Object lock = new Object();

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            while (i <= TOTAL) {
                synchronized (lock) {
                    if (i % 2 == 1) {
                        System.out.println("i=" + i++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        });

        Thread thread2 = new Thread(() -> {
            while (i <= TOTAL) {
                synchronized (lock) {
                    if (i % 2 == 0) {
                        System.out.println("i=" + i++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        });

        thread1.start();
        thread2.start();
    }
~~~



利用CountDownLatch

~~~java
	private static AtomicInteger num = new AtomicInteger(1);
    private static CountDownLatch countDownLatch = new CountDownLatch(2);

    public static void main(String[] args) throws InterruptedException {

        Thread thread1 = new Thread(() -> {
            while (num.intValue() < 100) {
                if (num.intValue() % 2 == 0) {
                    System.out.println(Thread.currentThread().getName() + ":" + num );
                    num.incrementAndGet();
                }
                countDownLatch.countDown();
            }

        },"偶数");

        Thread thread2 = new Thread(()->{
            while (num.intValue() < 100) {
                if (num.intValue() % 2 == 1) {
                    System.out.println(Thread.currentThread().getName() + ":" + num );
                    num.incrementAndGet();
                }
                countDownLatch.countDown();
            }
        },"奇数");

        thread1.start();
        thread2.start();
        countDownLatch.await();
    }
~~~






##  线程池

线程池的好处   
线程池的参数   
线程池的实现？  
线程池的几个参数的作用？  
线程池的工作原理，核心线程数和最大线程数什么时候用到  

杀死线程池中线程的方法  
线程池数量和io型、cpu型,原因  
多线程进行1到100加和操作。开10个线程进行加和  

线程池的内容，如何定义自己的拒绝策略  
阻塞队列有哪几个实现？  
怎么保证多线程的运行安全性  
关于多线程锁的升级原理  
线程池的线程是不是必须手动remove才可以回收value？  
内存泄漏是指主线程还是线程池  
线程池的线程数怎么确定   



### **[做题：手写一个对象池]**   

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
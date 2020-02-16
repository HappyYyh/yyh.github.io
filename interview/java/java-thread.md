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

### 【线程池的好处】

1. 线程是稀缺资源，使用线程池可以减少创建和销毁线程的次数，每个工作线程都可以重复使用。
2. 可以根据系统的承受能力，调整线程池中工作线程的数量，防止因为消耗过多内存导致服务器崩溃。



### 【线程池的参数】

~~~java
public ThreadPoolExecutor(int corePoolSize,      //核心线程数
                          int maximumPoolSize,   //最大线程数
                          long keepAliveTime,    //活跃时间
                          TimeUnit unit,         //时间单位
                          BlockingQueue<Runnable> workQueue, //存放任务的队列
                          ThreadFactory threadFactory, //线程工厂
                          RejectedExecutionHandler handler  //超出线程范围和队列容量的任务的处理程序
                         )


~~~

   1）当池子大小小于corePoolSize就新建线程，并处理请求   
   2）当池子大小等于corePoolSize，把请求放入workQueue中，池子里的空闲线程就去从workQueue中取任务并处理  
   3）当workQueue放不下新入的任务时，新建线程入池，并处理请求，如果池子大小撑到了maximumPoolSize就用RejectedExecutionHandler来做拒绝处理  
   4）另外，当池子的线程数大于corePoolSize的时候，多余的线程会等待keepAliveTime长的时间，如果无请求可处理就自行销毁  



### 【线程池的实现】

1、手动创建线程池：

~~~java
ThreadFactory namedThreadFactory = new ThreadFactoryBuilder()
        .setNameFormat("demo-pool-%d").build();
ExecutorService threadPool = new ThreadPoolExecutor(2, 5,
        10L, TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<>(10), namedThreadFactory, new ThreadPoolExecutor.AbortPolicy());
~~~

 2、用Executors创建：

~~~java
ExecutorService executorService = Executors.newSingleThreadExecutor();//单个线程
                                            newCachedThreadPool();  //可缓存线程池
                                            newScheduledThreadPool(2);//定时任务
                                            newFixedThreadPool(2);   //固定核心数
~~~



### 【线程池的工作原理】

提交一个任务到线程池中，线程池的处理流程如下：

1、判断**线程池里的核心线程**是否都在执行任务，如果不是（核心线程空闲或者还有核心线程没有被创建）则创建一个新的工作线程来执行任务。如果核心线程都在执行任务，则进入下个流程。

2、线程池判断工作队列是否已满，如果工作队列没有满，则将新提交的任务存储在这个工作队列里。如果工作队列满了，则进入下个流程。

3、判断**线程池里的线程**是否都处于工作状态，如果没有，则创建一个新的工作线程来执行任务。如果已经满了，则交给饱和策略来处理这个任务。



### 【核心线程数和最大线程数什么时候用到】  

1、线程池任务到来时会创建线程去处理任务，当任务达到核心线程数(corePoolSize) 则会把剩余的任务放到任务队列(workQueue)中，等到活跃线程空闲下来再去处理

2、当核心线程数(corePoolSize)+任务队列(workQueue)的任务数量 任然 < 任务数（即任务队列塞满了），则会开辟线程去处理任务

3、当活跃线程数 = 最大线程数时则不会继续创建线程了，超过的会拒绝策略



### 【线程池的线程数怎么确定】   

1. **CPU密集型任务**
   
   要进行大量的计算，消耗CPU资源，比如计算圆周率、对视频进行高清解码等等，全靠CPU的运算能力。要最高效地利用CPU，计算密集型任务同时进行的数量应当等于CPU的核心数。  
   
   **一般配置线程数=CPU总核心数+1    (+1是为了利用等待空闲)**
   
2. **IO密集型任务**

   这类任务的CPU消耗很少，任务的大部分时间都在等待IO操作完成（因为IO的速度远远低于CPU和内存的速度）。常见的大部分任务都是IO密集型任务，比如Web应用。对于IO密集型任务，任务越多，CPU效率越高(但也有限度)。

   **一般配置线程数=CPU总核心数 * 2 +1**

**总结**：

       最佳线程数目 = （线程等待时间与线程CPU时间之比 + 1）* CPU数目

所以线程等待时间所占比例越高，需要越多线程。线程CPU时间所占比例越高，需要越少线程



### 【杀死线程池中线程的方法】  

​	一般都是让线程自动停止

​	如果要关闭线程池：`ExecutorService.shutdown`

​	如果非要杀死线程，

​	1、可以尝试 在外部定义一个变量，在线程内部执行任务时判断达到条件后return

​	2、杀进程



### 【线程池数量和io型、cpu型,原因】  

​	这里可以看上上题《线程池的线程数怎么确定》



### 【如何定义自己的拒绝策略】 

​	手动创建线程池的最后一个参数就是拒绝策略，如果不适用提供好的，可以利用匿名内部类实现：

~~~java
new RejectedExecutionHandler(){
    @Override
    public void rejectedExecution(Runnable r,ThreadPoolExecutor ExecutorService){
        System.out.println(r.toString()+"is discard");
    }
}
~~~



### 【阻塞队列有哪几个实现】  

​	队列：满足FIFO(先进先出)

​	阻塞队列就是：

1. 入队时，如果队列已经满了，就阻塞等待直到队列中有位置可以插入
2. 出队时，如果队列中为空，就阻塞等待直到队列中有数据可以出队列

​	Java中阻塞队列的实现：

- ​	**ArrayBlockingQueue**

  ​	基于**数组**实现的阻塞队列，初始化时需要定义数组的大小，也就是队列的大小，所以这个队列是一个有界队列。

- ​	**LinkedBlockingQueue**

  ​	基于**链表**实现的阻塞队列，既然是链表，那么就可以看出这种阻塞队列含有链表的特性，那就是无界。但是实际上LinkedBlockingQueue是有界队列，默认大小是Integer的最大值，而也可以通过构造方法传入固定的capacity大小设置

- ​	**DelayQueue**

  ​	**延迟队列**,顾名思义就是只有当元素达到指定的时间后才可以从队列中取出。

- ​	**PriorityBlockingQueue**

  ​	有**优先级**的阻塞队列，底层也是通过数组实现，默认初始容量为11，容量不够会自动扩容，扩容的最大值为Integer的最大值-8（有些虚拟机再实现数组头部存储内容所预留的空间），所以基本上可以认为是无界阻塞队列

- ​	**SynchronousQueue**

  ​	SynchonousQueue是比较特殊的阻塞队列，特殊之处就是这个叫队列的队列没有容量，又或者说容量为0，所以一旦有元素插入此队列，由于没有容量，就必须被阻塞直到元素被取出

  

### 【线程池的线程是不是必须手动remove才可以回收value】  

​	是的，因为线程池的核心线程是一直存在的，如果不清理，那么核心线程的threadLocals变量会一直持有ThreadLocal变量



### 【内存泄漏具体是怎么产生的】

​	主要分两种场景：主线程仍然对ThreadLocal有引用和主线程不存在对ThreadLocal的引用。

1. ​	第一种场景因为主线程仍然在运行，所以还是有对ThreadLocal的引用，那么ThreadLocal变量的引用和value是不会被回收的。

2. ​	第二种场景虽然主线程不存在对ThreadLocal的引用，且该引用是弱引用，所以会在gc的时候被回收，但是对用的value不是弱引用，不会被内存回收，仍然会造成内存泄漏

   

### 【内存泄漏是指主线程还是线程池】

​	主线程



### 【做题：多线程进行1到100加和操作。开10个线程进行加和 】  

​	1、开启10个线程进行加和（合并的时候线程安全）

~~~java
public class SumDemo extends Thread{

    private static volatile int sum;
    private int start;
    private int end;

    public SumDemo(int start,int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    public void run() {
        int tmp = 0 ;
        for (int i = start; i < end ; i++) {
            tmp+=i;
        }
        put(tmp);
    }

    /**
     * 如果不在同步方法中进行，多线程时更改sum的值会有线程安全问题
     * @param num
     */
    private static synchronized void put(int num){
        System.out.println(Thread.currentThread().getName() + "正在将结果附上" + num);
        sum += num;
    }

    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 10 ; i++) {
            SumDemo sumDemo = new SumDemo(i * 10 + 1, i * 10 + 10);
            sumDemo.start();
            sumDemo.join();
        }
        System.out.println("运行结果：" + SumDemo.sum);
    }
}
~~~



​	2、利用ForkJoin进行加和计算

~~~java
public class ForkJoinTest extends RecursiveTask<Integer> {

    //开始数
    private Integer start;
	//结束数
    private Integer end;
	//阈值
    private static int thresold = 10;

    public ForkJoinTest(Integer start, Integer end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        int sum = 0;
        boolean canCompute = (end - start) <= thresold;
        if (canCompute) {
            for (int i = start; i <= end; i++) {
                sum += i;
            }
        } else {
            int mid = (start + end) >> 1;
            ForkJoinTest leftTask = new ForkJoinTest(start, mid);
            ForkJoinTest rightTask = new ForkJoinTest(mid + 1, end);
            invokeAll(leftTask, rightTask);
            sum = leftTask.join() + rightTask.join();
        }
        return sum;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ForkJoinPool pool = new ForkJoinPool();
        ForkJoinTest task = new ForkJoinTest(1,100);
        ForkJoinTask<Integer> submit = pool.submit(task);
        System.out.println(submit.get());
        System.out.println(pool.getPoolSize());
    }
}
~~~





### **【做题：手写一个对象池】**   

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

【关于多线程锁的升级原理】  

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
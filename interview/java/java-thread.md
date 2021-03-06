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

1.   **新建状态(New):** 

   线程对象被创建后，就进入了新建状态。例如，Thread thread = new Thread()。

2.   **就绪状态(Runnable):** 

   也被称为“可执行状态”。线程对象被创建后，其它线程调用了该对象的start()方法，从而来启动该线程。例如，thread.start()。处于就绪状态的线程，随时可能被CPU调度执行。

3.   **运行状态(Running):** 

   线程获取CPU权限进行执行。需要注意的是，线程只能从就绪状态进入到运行状态。

4.   **阻塞状态(Blocked):** 

   阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。阻塞的情况分三种：

      - 等待阻塞 -- 通过调用线程的wait()方法，让线程等待某工作的完成。

      - 同步阻塞 -- 线程在获取synchronized同步锁失败(因为锁被其它线程所占用)，它会进入同步阻塞状态。

      - 其他阻塞 -- 通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。

6.   **死亡状态(Dead):** 

​		线程执行完了或者因异常退出了run()方法，该线程结束生命周期。

实际中，Thead类中有一个枚举类就是用于描述线程的状态：

~~~java
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
}
~~~

- **RUNNABLE** 线程运行中或I/O等待
- **BLOCKED** 线程在等待monitor锁(synchronized关键字)
- **TIMED_WAITING** 线程在等待唤醒，但设置了时限
  - 调用Thread.sleep方法。
  - 调用Object对象的wait方法，指定超时值。
  - 调用Thread对象的join方法，指定超时值。
  - 调用LockSupport对象的parkNanos方法。
  - 调用LockSupport对象的parkUntil方法。
- **WAITING** 线程在无限等待唤醒
  - 调用Object对象的wait方法，但没有指定超时值。
  - 调用Thread对象的join方法，但没有指定超时值。
  - 调用LockSupport对象的park方法。



### 【Java程序启动至少启动几个线程】

​	一般想到两个Main线程，GC线程

​	实际调用JMX的API：

~~~java
public class TestOne {
	public static void main(String[] args) {
		ThreadMXBean mxBean = ManagementFactory.getThreadMXBean();
		ThreadInfo[] allThreads = mxBean.dumpAllThreads(false, false);
		for (ThreadInfo threadInfo : allThreads) {
				System.out.println(threadInfo.getThreadId()+"==="+
                                   threadInfo.getThreadName());
		}
	}
}
~~~

~~~java
5===Attach Listener 
4===Signal Dispatcher 分发处理发送给jvm信号的线程
3===Finalizer 调用对象finalize方法的线程，就是垃圾回收的线程
2===Reference Handler 清除reference的线程
1===main
~~~





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

​	**原因**：Java提供的锁是对象级的而不是线程级的，每个对象都有锁，通过线程获得。简单的说，由于wait，notify，notifyAll都是锁级别的操作，所以把他们定义在object类中因为锁属于对象。



### 【创建线程的方式 】

1. ​	继承Thread类

2. ​	实现Runnable接口

3. ​	实现Callable接口

   

### 【Runnable和Callable区别】

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

这个代码多线程下修改同一个变量肯定会有问题，使用synchronized修饰sell方法即可

~~~java
public class SyncTest{

    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        new Thread(ticket,"线程1").start();
        new Thread(ticket,"线程2").start();
        new Thread(ticket,"线程3").start();
    }
}

class Ticket implements Runnable{

    int ticket = 100;

    @Override
    public void run() {
        while (true){
            try {
                //模拟网络延迟
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            if (ticket > 0) {
                // this表示Ticket对象
                sell();
            }else {
                break;
            }
        }
    }

    public void sell(){
        System.out.println(Thread.currentThread().getName() + "售票，票号为：" + ticket--);
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
                          RejectedExecutionHandler handler//超出线程范围和队列容量的任务处理程序
                         )
~~~

1. 当池子大小小于corePoolSize就新建线程，并处理请求   
2. 当池子大小等于corePoolSize，把请求放入workQueue中，池子里的空闲线程就去从workQueue中取任务并处理  
3. 当workQueue放不下新入的任务时，新建线程入池，并处理请求，如果池子大小撑到了maximumPoolSize就用RejectedExecutionHandler来做拒绝处理  
4. 另外，当池子的线程数大于corePoolSize的时候，多余的线程会等待keepAliveTime长的时间，如果无请求可处理就自行销毁  



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

​	调用 Executor 的 `shutdown()` 方法会等待线程都执行完毕之后再关闭，但是如果调用的是 `shutdownNow()` 方法，则相当于调用每个线程的 `interrupt()` 方法。

​	如果非要杀死线程，

​	1、可以尝试 在外部定义一个变量，在线程内部执行任务时判断达到条件后return

​	2、杀进程



### 【线程池数量和io型、cpu型,原因】  

​	这里可以看上上题《线程池的线程数怎么确定》



### 【如何定义自己的拒绝策略】 

​	手动创建线程池的最后一个参数就是拒绝策略，如果不使用提供好的，可以利用匿名内部类实现：

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

   LinkedBlockingQueue·`put`方法源码：

   ~~~java
   public void put(E e) throws InterruptedException {
       if (e == null) throw new NullPointerException();
       // Note: convention in all put/take/etc is to preset local var
       // holding count negative to indicate failure unless set.
       int c = -1;
       Node<E> node = new Node<E>(e);
       final ReentrantLock putLock = this.putLock;
       final AtomicInteger count = this.count;
       putLock.lockInterruptibly();
       try {
           /*
                * Note that count is used in wait guard even though it is
                * not protected by lock. This works because count can
                * only decrease at this point (all other puts are shut
                * out by lock), and we (or some other waiting put) are
                * signalled if it ever changes from capacity. Similarly
                * for all other uses of count in other wait guards.
                */
           while (count.get() == capacity) {
               // 如果队列满了，则当前线程阻塞等待
               notFull.await();
           }
           enqueue(node);
           c = count.getAndIncrement();
           if (c + 1 < capacity)
               // 当有空闲位置时，唤醒一个等待的线程
               notFull.signal();
       } finally {
           putLock.unlock();
       }
       if (c == 0)
           signalNotEmpty();
   }
   ~~~

2. 出队时，如果队列中为空，就阻塞等待直到队列中有数据可以出队列

   LinkedBlockingQueue·`take`方法源码：

   ~~~java
   public E take() throws InterruptedException {
       E x;
       int c = -1;
       final AtomicInteger count = this.count;
       final ReentrantLock takeLock = this.takeLock;
       takeLock.lockInterruptibly();
       try {
           // 如果队列为空
           while (count.get() == 0) {
               //阻塞等待
               notEmpty.await();
           }
           x = dequeue();
           c = count.getAndDecrement();
           if (c > 1)
               // 当队列不为空，唤醒阻塞的线程
               notEmpty.signal();
       } finally {
           takeLock.unlock();
       }
       if (c == capacity)
           signalNotFull();
       return x;
   }
   ~~~

   

​	Java中阻塞队列的实现：

- **ArrayBlockingQueue**

  基于**数组**实现的阻塞队列，初始化时需要定义数组的大小，也就是队列的大小，所以这个队列是一个有界队列。

- **LinkedBlockingQueue**

  基于**链表**实现的阻塞队列，既然是链表，那么就可以看出这种阻塞队列含有链表的特性，那就是无界。但是实际上LinkedBlockingQueue是有界队列，默认大小是Integer的最大值，而也可以通过构造方法传入固定的capacity大小设置

- **DelayQueue**

  **延迟队列**,顾名思义就是只有当元素达到指定的时间后才可以从队列中取出。

- **PriorityBlockingQueue**

  有**优先级**的阻塞队列，底层也是通过数组实现，默认初始容量为11，容量不够会自动扩容，扩容的最大值为Integer的最大值-8（有些虚拟机再实现数组头部存储内容所预留的空间），所以基本上可以认为是无界阻塞队列

- **SynchronousQueue**

  SynchonousQueue是比较特殊的阻塞队列，特殊之处就是这个叫队列的队列没有容量，又或者说容量为0，所以一旦有元素插入此队列，由于没有容量，就必须被阻塞直到元素被取出

**应用：**

解决**生产者消费者**问题

~~~java
public class ProducerConsumer {

    private static BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);

    private static class Producer extends Thread {
        @Override
        public void run() {
            try {
                queue.put("product");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("produce..");
        }
    }

    private static class Consumer extends Thread {
        @Override
        public void run() {
            try {
                String product = queue.take();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("consume..");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 2; i++) {
            Producer producer = new Producer();
            producer.start();
        }
        for (int i = 0; i < 5; i++) {
            // 当2个消费完以后线程就会阻塞，直到有新的生产者
            Consumer consumer = new Consumer();
            consumer.start();
        }
        Thread.sleep(100);
        for (int i = 0; i < 3; i++) {
            Producer producer = new Producer();
            producer.start();
        }
    }
}
~~~

结果如下：

~~~java
produce..produce..consume..consume..produce..consume..produce..consume..produce..consume..
~~~



  

### 【线程池的线程是不是必须手动remove才可以回收value】  

​	是的，因为线程池的核心线程是一直存在的，如果不清理，那么核心线程的threadLocals变量会一直持有ThreadLocal变量



### 【内存泄漏具体是怎么产生的】

​	主要分两种场景：主线程仍然对ThreadLocal有引用和主线程不存在对ThreadLocal的引用。

1. 第一种场景因为主线程仍然在运行，所以还是有对ThreadLocal的引用，那么ThreadLocal变量的引用和value是不会被回收的。

2. 第二种场景虽然主线程不存在对ThreadLocal的引用，且该引用是弱引用，所以会在gc的时候被回收，但是对用的value不是弱引用，不会被内存回收，仍然会造成内存泄漏

   

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




## **锁的概念**

### 【锁的分类和介绍】

#### **公平锁/非公平锁**

- **公平锁**是指多个线程按照申请锁的顺序来获取锁。  
- **非公平锁**是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能，会造成优先级反转或者饥饿现象。  

对于Java `ReentrantLock`而言，通过构造函数指定该锁是否是公平锁，默认是非公平锁。非公平锁的优点在于吞吐量比公平锁大。  

对于`Synchronized`而言，也是一种非公平锁。由于其并不像`ReentrantLock`是通过AQS的来实现线程调度，所以并没有任何办法使其变成公平锁。



#### **可重入锁**  

可重入锁又名**递归锁**，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。说的有点抽象，下面会有一个代码的示例。
对于Java `ReentrantLock`而言, 他的名字就可以看出是一个可重入锁，其名字是`Re entrant Lock`重新进入锁。
对于`Synchronized`而言,也是一个可重入锁。可重入锁的一个好处是可一定程度避免死锁。

```
synchronized void setA() throws Exception{
    Thread.sleep(1000);
    setB();
}

synchronized void setB() throws Exception{
    Thread.sleep(1000);
}
```

上面的代码就是一个可重入锁的一个特点，如果不是可重入锁的话，setB可能不会被当前线程执行，可能造成死锁。



#### **独享锁/共享锁**

- **独享锁**是指该锁一次只能被一个线程所持有。
- **共享锁**是指该锁可被多个线程所持有。

对于Java `ReentrantLock`而言，其是独享锁。但是对于Lock的另一个实现类`ReadWriteLock`，其读锁是共享锁，其写锁是独享锁。  

读锁的共享锁可保证并发读是非常高效的，读写，写读 ，写写的过程是互斥的。  

独享锁与共享锁也是通过AQS来实现的，通过实现不同的方法，来实现独享或者共享。  

对于`Synchronized`而言，当然是独享锁。     



#### **互斥锁/读写锁**

上面讲的独享锁/共享锁就是一种广义的说法，互斥锁/读写锁就是具体的实现。  

- **互斥锁**在Java中的具体实现就是`ReentrantLock`
- **读写锁**在Java中的具体实现就是`ReadWriteLock`



#### **乐观锁/悲观锁**

乐观锁与悲观锁不是指具体的什么类型的锁，而是指看待并发同步的角度。

- **悲观锁**认为对于同一个数据的并发操作，一定是会发生修改的，哪怕没有修改，也会认为修改。因此对于同一个数据的并发操作，悲观锁采取加锁的形式。悲观的认为，不加锁的并发操作一定会出问题。

- **乐观锁**则认为对于同一个数据的并发操作，是不会发生修改的。在更新数据的时候，会采用尝试更新，不断重新的方式更新数据。乐观的认为，不加锁的并发操作是没有事情的。

从上面的描述我们可以看出，悲观锁适合写操作非常多的场景，乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。

悲观锁在Java中的使用，就是利用各种锁**Synchronize/Lock**。

乐观锁在Java中的使用，是无锁编程，常常采用的是**CAS**算法，典型的例子就是原子类，通过CAS自旋实现原子操作的更新。



#### **分段锁**

分段锁其实是一种锁的设计，并不是具体的一种锁，对于`ConcurrentHashMap`而言，其并发的实现就是通过分段锁的形式来实现高效的并发操作。  

我们以`ConcurrentHashMap`来说一下分段锁的含义以及设计思想，`ConcurrentHashMap`中的分段锁称为`Segment`，它即类似于HashMap（JDK7与JDK8中HashMap的实现）的结构，即内部拥有一个Entry数组，数组中的每个元素又是一个链表；同时又是一个ReentrantLock（Segment继承了ReentrantLock)。

当需要put元素的时候，并不是对整个hashmap进行加锁，而是先通过hashcode来知道他要放在那一个分段中，然后对这个分段进行加锁，所以当多线程put的时候，只要不是放在一个分段中，就实现了真正的并行的插入。
但是，在统计size的时候，可就是获取hashmap全局信息的时候，就需要获取所有的分段锁才能统计。
分段锁的设计目的是细化锁的粒度，当操作不需要更新整个数组的时候，就仅仅针对数组中的一项进行加锁操作。



#### **偏向锁/轻量级锁/重量级锁**

这三种锁是指锁的状态，并且是针对`Synchronized`。在Java 5通过引入锁升级的机制来实现高效`Synchronized`。这三种锁的状态是通过**对象监视器**在对象头中的字段来表明的。

- **偏向锁**是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。  
- **轻量级**锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。 
- **重量级**锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。重量级锁会让其他申请的线程进入阻塞，性能降低。



#### **自旋锁**

在Java中，自旋锁是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。
典型的自旋锁实现的例子，可以参考[自旋锁的实现](http://ifeve.com/java_lock_see1/)

~~~java
public class SpinLock {

  private AtomicReference<Thread> sign =new AtomicReference<>();
  public void lock(){
    Thread current = Thread.currentThread();
    while(!sign .compareAndSet(null, current)){
    }
  }

  public void unlock (){
    Thread current = Thread.currentThread();
    sign .compareAndSet(current, null);
  }
}
/**
使用了CAS原子操作，lock函数将owner设置为当前线程，并且预测原来的值为空。unlock函数将owner设置为null，并且预测值为当前线程。

当有第二个线程调用lock操作时由于owner值不为空，导致循环一直被执行，直至第一个线程调用unlock函数将owner设置为null，第二个线程才能进入临界区。

由于自旋锁只是将当前线程不停地执行循环体，不进行线程状态的改变，所以响应速度更快。但当线程数不停增加时，性能下降明显，因为每个线程都需要执行，占用CPU时间。如果线程竞争不激烈，并且保持锁的时间段。适合使用自旋锁。

注：该例子为非公平锁，获得锁的先后顺序，不会按照进入lock的先后顺序进行。
*/
~~~



### 【自旋锁和阻塞锁的区别】  

​	互斥锁的起始原始开销要高于自旋锁，但是基本是一劳永逸，临界区持锁时间的大小并不会对互斥锁的开销造成影响，而自旋锁是死循环检测，加锁全程消耗cpu，起始开销虽然低于互斥锁，但是随着持锁时间，加锁的开销是线性增长。



### 【乐观/悲观锁在Java和MySQL分别是怎么实现的】

- **Mysql**

  - 乐观锁实现：

     利用数据库版本（version）实现，一般是通过为数据库表增加一个数字类型的 “version” 字段，当我们提交更新的时候，判断数据库表对应记录的当前版本信息与第一次取出来的version值进行比对，如果数据库表当前版本号与第一次取出来的version值相等，则予以更新，否则认为是过期数据   

  - 悲观锁实现：

    注：要使用悲观锁，我们必须关闭mysql数据库的自动提交属性，因为MySQL默认使用autocommit模式，也就是说，当你执行一个更新操作后，MySQL会立刻将结果进行提交。

  ​	一般都是select xxx for update，之后数据就被锁定了，其它的事务必须等本次事务提交之后才能执行

  ~~~sql
  select status from t_goods where id=1 for update;
  ~~~

- **Java**

  - 	乐观锁实现：CAS和原子类

  - 	悲观锁实现：Synchronize和Lock




### 【公平锁是怎样的底层实现】  

构造方法传入是否是公平锁

~~~java
public ReentrantLock(boolean fair) {
     sync = fair ? new FairSync() : new NonfairSync();
}
~~~

~~~java
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;

    final void lock() {
        acquire(1);
    }

    /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        //拿到当前的同步状态, 如果是无锁状态， 则进行hasQueuedPredecessors方法逻辑
        if (c == 0) {
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
}
~~~





### 【死锁场景】  

死锁指的是多个进程因为竞争资源而造成的僵局（互相等待），没有外力，那么所有进程都会无法向前推进。

**场景：**

- 系统资源的竞争。只有资源不足时才会出现死锁可能，另外，可剥夺资源的竞争是不会引发死锁的；

- 进程推进顺序不对。多进程在运行时，请求和释放资源的顺序不当。

- 系统资源分配不当。



### 【死循环怎么造成的】  

**四大必要条件：**

- 互斥：进程对分配到的资源排它性使用。独占资源，是由资源本身的属性决定的。
- 请求和保持：保持已有资源，同时请求新的资源，在请求过程中以及因为没有得到新资源而阻塞，已有资源仍然保持；
- 不可剥夺：进程已有的资源在使用完之前不能被剥夺，只能自己释放；
- 环路等待：必然存在一个进程资源环形请求链。

**死锁预防**：打破之前四个条件

- 打破互斥在实际中应用不大；
- 打破请求与保持，可以实行资源预先分配策略，即进程在运行前一次性申请所需要的全部资源，如果不能满足，则暂不运行。实际应用中，进程在执行时是动态的，不可预测的，并且资源利用率低，降低了进程并发性。
- 打破不可剥夺，当请求新资源不能满足，需要释放已有资源，系统性能受到很大降低
- 打破循环等待：实行资源有序分配策略。可以将资源事先分类编号，按号分配，使进程在申请、占用资源是不会形成环路。所有进程对资源的请求必须严格按资源序号递增的顺序提出。但是也有问题，合理编号困难，增大系统开销，另外也增加了进程对资源的占有时间。

**死锁避免：**

　　不限制进程有关申请资源的命令，而是对进程所发出的每一个申请资源命令加以动态地检查，并根据检查结果决定是否进行资源分配。就是说，在资源分配过程中若预测有发生死锁的可能性，则加以避免。这种方法的关键是确定资源分配的安全性。

　　银行家算法（1968年）：允许进程动态地申请资源，系统在每次实施资源分配之前，先计算资源分配的安全性，若此次资源分配安全（即资源分配后，系统能按某种顺序来为每个进程分配其所需的资源，直至最大需求，使每个进程都可以顺利地完成），便将资源分配给进程，否则不分配资源，让进程等待。

**死锁检测与修复：**

　　预防和避免的手段达到排除死锁的目的是很困难的。一种简便的方法是系统为进程分配资源时，不采取任何限制性措施，但是提供了检测和解脱死锁的手段：能发现死锁并从死锁状态中恢复出来。因此，在实际的操作系统中往往采用死锁的检测与恢复方法来排除死锁。



### 【加锁的静态方法和普通方法区别】

**结论**：

`static synchronized`是**类锁**，`synchronized`是**对象锁**。

**区别：**

- **对象锁**（又称实例锁，`synchronized`）：该锁针对的是该实例对象（当前对象）。
   `synchronized`是对类的**当前实例（当前对象）**进行加锁，防止其他线程同时访问该类的**该实例的所有synchronized块**，注意这里是“类的当前实例”， 类的两个不同实例就没有这种约束了。
  **每个对象都有一个锁，且是唯一的**。
- **类锁**（又称全局锁，`static synchronized`）：该锁针对的是类，无论**实例**出多少个**对象**，那么线程依然共享该锁。 `static synchronized`是限制多线程中该类的所有实例同时访问该类**所对应的代码块**。（`实例.fun`实际上相当于`class.fun`）



### 【栅栏和闭锁的区别】  

1. **闭锁**用来等待事件，就是说闭锁用来等待的事件就是countDown事件,只有该countDown事件执行后所有之前在等待的线程才有可能继续执行;而栅栏没有类似countDown事件控制线程的执行,只有线程的await方法能控制等待的线程执行。

2. **栅栏**用来等待线程，CyclicBarrier强调的是n个线程，大家相互等待，只要有一个没完成，所有线程都得等着。

   闭锁是一次性对象，一旦进入终止状态，就不能重置。而栅栏可以使一定数量的参入方反复的在栅栏位置汇集。
   



## 锁的原理

### 【Synchronize的实现原理】    

​	**锁的数据结构：**

​	同步代码块是使用monitorenter和monitorexit指令实现的，任何java对象都有一个monitor与之关联，当一个monitor被持有后，对象就处于锁定状态。

​	Synchronized是通过对象内部的一个叫做监视器锁（monitor）来实现的。但是监视器锁本质又是依赖于底层的操作系统的Mutex Lock来实现的。

​	Synchronize锁是通过monitorenter和monitorexit来实现的

​	**每个对象**都有一个monitor监视器，调用monitorenter就是尝试获取这个对象，成功获取到了就将值+1，离开就将值减1。如果是线程重入，在将值+1，说明monitor对象是支持可重入的。



### 【Jdk对synchronize的优化】  

​	HotSpot中锁的具体实现以及对它的优化：

​	**重量级锁**：

​	最基础的实现方式，JVM会阻塞未获取到锁的线程，在锁被释放的时候唤醒这些线程。阻塞和唤醒操作是依赖操作系统来完成的,所以需要从用户态切换到内核态，开销很大。并且monitor调用的是操作系统底层的互斥量(mutex),本身也有用户态和内核态的切换，所以JVM引入了自旋的概念，减少上面说的线程切换的成本。  

​	**自旋锁**：

​	因为JVM不知道锁被占用的时间长短，所以使用的是自适应自旋。就是**线程空循环的次数时会动态调整的**。

​	**轻量级锁：**

​	JDK1.6之后加入，它的目的并不是为了替换前面的重量级锁，而是在实际**没有锁竞争的情况下， 通过CAS将申请互斥量这步也省掉。**

​	**偏向锁：**

​	无竞争条件下 消除**整个**同步互斥，连CAS都不操作。



### 【Lock接口的API以及其实现类】  

​	API:

~~~java
//获取锁。拿不到lock就不罢休，不然线程就一直block
void lock();

//如果当前线程未被中断，则获取锁。 
void lockInterruptibly() throws InterruptedException;

//仅在调用时锁未被另一个线程保持的情况下，才获取该锁，拿不到返回false
boolean tryLock();

//仅在调用时锁未被另一个线程保持的情况下，才获取该锁，拿不到lock，就等一段时间，超时返回false
boolean tryLock(long time, TimeUnit unit) throws InterruptedException;

//释放锁
void unlock();

//获取等待通知组件，该组件和当前的锁绑定，当前线程只有获得了锁，才能调用该组件的wait()方法，而调用后，当前线程将释放锁
Condition newCondition();
~~~

​	实现类：

~~~java
// 可重入锁
ReentrantLock.class;
// 读写锁    
ReentrantReadWriteLock.class    
~~~



### 【ReentrantLock的理解】 

​	ReentrantLock是可重入锁，其内有公平锁和非公平锁两种实现，和synchronized不可响应中断不同，而ReentrantLock可以相应中断，其原理需要结合AQS和CAS



### 【ReentrantLock中的lock和unlock之间的同步如何进行线程间的通信】 

​	利用Condition

~~~java
Lock lock = new ReentrantLock();
Condition c = lock.newCondition()
~~~

​	condition对象可以利用await/signal（**等待/唤醒**） 等同于Object里面的wait/notify方法进行线程间通信



### 【ReentrantReadWriteLock的理解】 

​	JUC提供了读写锁ReentrantReadWriteLock，它表示两个锁，一个是读操作相关的锁，称为**共享锁**；一个是写相关的锁，称为**排他锁**

​	读写锁有以下三个重要的特性：

（1）公平选择性：支持非公平（默认）和公平的锁获取方式，吞吐量还是非公平优于公平。

（2）重进入：读锁和写锁都支持线程重进入。

（3）锁降级：遵循获取写锁、获取读锁再释放写锁的次序，写锁能够降级成为读锁。

​	其内部有5个内部类，

~~~java
abstract static class Sync extends AbstractQueuedSynchronizer
//非公平
static final class NonfairSync extends Sync
//公平    
static final class FairSync extends Sync
//读
public static class ReadLock implements Lock, java.io.Serializable 
//写    
public static class WriteLock implements Lock, java.io.Serializable    
~~~



### 【ReadWriteLock与ReentrantReadWriteLock区别】  

​	后者是前者的实现类，前者是一个接口，只有两个方法：

~~~java
	/**
     * Returns the lock used for reading.
     *
     * @return the lock used for reading
     */
    Lock readLock();

    /**
     * Returns the lock used for writing.
     *
     * @return the lock used for writing
     */
    Lock writeLock();
~~~

​	后者多了很多实现方法（怀疑题目有点问题，要是ReentrantLock和ReentrantReadWriteLock比较才有意思）



### 【根据AQS实现的几种锁】 

ReentrantLock和ReentrantReadWriteLock以及CountDownLatch等锁相关的类内部都是维护了一个内部类比如

Sync、NonfairSync、FairSync都是基于AQS实现

~~~java
abstract static class Sync extends AbstractQueuedSynchronizer
//非公平
static final class NonfairSync extends Sync
//公平    
static final class FairSync extends Sync
~~~



### 【对AbstractQueuedSynchronizer的理解】 

​	后续源码分析



### 【Synchronized 和 lock 区别】 

1. Synchronized 是关键字，可以用在代码块、方法、类上面，由JVM底层优化支持；Lock是一个接口，只能写在方法中
2. Synchronized 会自动释放锁，而Lock一般需要在finally块中释放锁
3. Lock可以让等待锁的线程响应中断处理，如tryLock(long time, TimeUnit unit)，而Synchronized 不行
4. synchronized是非公平锁，Lock可以设置是否公平锁，默认是非公平锁；
5. Lock的实现类ReentrantReadWriteLock提供了readLock()和writeLock()用来获取读锁和写锁的两个方法，这样多个线程可以进行同时读操作；
6. Lock可以绑定条件，实现分组唤醒需要的线程；synchronized要么随机唤醒一个，要么唤醒全部线程。



### 【多线程锁的升级原理】   

​	锁的级别从低到高：

​	**无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁**

​	锁分级别**原因**：

​	没有优化以前，sychronized是重量级锁（悲观锁），使用 wait 和 notify、notifyAll 来切换线程状态非常消耗系统资源；线程的挂起和唤醒间隔很短暂，这样很浪费资源，影响性能。所以 JVM 对 sychronized 关键字进行了优化，把锁分为 无锁、偏向锁、轻量级锁、重量级锁 状态。

锁状态对比：

 

|          |                            偏向锁                            |                           轻量级锁                           |                    重量级锁                    |
| :------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :--------------------------------------------: |
| 适用场景 |                    只有一个线程进入同步块                    | 虽然很多线程，但是没有冲突：多条线程进入同步块，但是线程进入时间错开因而并未争抢锁 | 发生了锁争抢的情况：多条线程进入同步块并争用锁 |
|   本质   |                         取消同步操作                         |                     CAS操作代替互斥同步                      |                    互斥同步                    |
|   优点   | 不阻塞，执行效率高（只有第一次获取偏向锁时需要CAS操作，后面只是比对ThreadId） |                           不会阻塞                           |                  不会空耗CPU                   |
|   缺点   |    适用场景太局限。若竞争产生，会有额外的偏向锁撤销的消耗    |                   长时间获取不到锁空耗CPU                    | 阻塞，上下文切换，重量级操作，消耗操作系统资源 |




## **Voliate**

### 【说一说volatile关键字的作用】

​	保证变量的可见性，一定程度上禁止指令重排序



### 【volatile为什么能保证可见性】

​	问题：Java内存模型规定变量存储在**主存**中，对于每个线程都有自己的**工作内存**，线程的工作内存中保存了被改线程使用到的变量的主内存副本拷贝，这就造成多线程环境下读取的时候可能读取到的值不是最新的值；

​	解决：用来volatile关键字修饰变量以后，Java内存模型将在写操作后插入一个**写屏障**指令（即内存屏障），在读操作前插入一个**读屏障**指令，**内存屏障**会把之前的写入值都刷新到缓存，这样会保证：

​	1、一旦完成写操作，任何线程得到的值都是最新的值

​	2、写入前，会保证所有之前发生的事已经发生，并且任何更新过的数据值也是可见的

​	由此volatile保证了可见性（通俗点就是强制刷新主存）



### 【volatile底层实现】

​	volatile的底层是通过lock前缀指令、内存屏障来实现的。



### 【为什么uniqueInstance声明使用volitie修饰】  

​	单例模式中会学到双重检查加锁（double-checked locking)

~~~java
public class Singleton {
    private volatile static Singleton uniqueInstance;
    private Singleton(){}
    //双检锁
    public static Singleton getInstance(){
        if(uniqueInstance == null){
            synchronized(Singleton.class){
                if(uniqueInstance == null){
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
~~~

​	synchronized虽然保证了原子性，但却没有保证指令重排序的正确性，会出现A线程执行初始化，但可能因为构造函数里面的操作太多了，所以A线程的uniqueInstance实例还没有造出来，但已经被赋值了。而B线程这时过来了，错以为uniqueInstance已经被实例化出来，一用才发现uniqueInstance尚未被初始化。   



## java内存模型

### 【Java中多线程操作下的三个特性】

1. 原子性：即一个操作或者多个操作 要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。
2. 可见性：当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。
3. 有序性：程序执行的顺序按照代码的先后顺序执行。



### 【Java内存模型及内部原理】

​	Java内存模型（Java Memory Model，**JMM**）就是一种符合**内存模型**规范的，屏蔽了各种硬件和操作系统的访问差异的，保证了 Java 程序在各种平台下对内存的访问都能保证效果一致的机制及规范。

​	Java 内存模型规定了所有的变量都存储在主内存中，每条线程还有自己的工作内存。

​	线程的工作内存中保存了该线程中用到的变量的主内存副本拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存。

​	不同的线程之间也无法直接访问对方工作内存中的变量，线程间变量的传递均需要自己的工作内存和主存之间进行数据同步进行。

​	而 JMM 就作用于工作内存和主存之间数据同步过程。它规定了如何做数据同步以及什么时候做数据同步。

​	**内存模型解决并发问题主要采用两种方式：**

- 限制处理器优化
- 使用内存屏障



### 【Happen-Before原则】

- **什么是happen-before**

  （1）编写的程序都要经过优化（编译器和处理器会对程序进行优化）后才会被运行，优化分为很多种，其中有一种优化叫做重排序，**重排序需要遵守happens-before规则。**

  （2）a happens-before b :happens-before关系保证a操作将对b操作可见。

- **happen-before原则（前一个操作的执行结果必须对后一个操作可见）**

  一个线程中的每一个操作happens-before于该线程的任意后续操作，这里的happens-before并不是前一个操作必须早于后一个操作， 而是前一个操作必须对后一个操作可见，否则不能重排序。

- **具体规则**

  1.   程序顺序规则：一个线程中的每个操作，happens-before于该线程中的任意后续操作。

  2.  监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。

  3.  volatile变量规则：对一个volatile域的写，happens-before于任意后续对这个volatile域的读。

  4.  传递性：如果A happens-before B，且B happens-before C，那么A happens-before C。

  5.  start()规则：如果线程A执行操作ThreadB.start()（启动线程B），那么A线程的ThreadB.start()操作happens-before于线程B中的任意操作。

  6.  Join()规则：如果线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回。

  7.  程序中断规则：对线程interrupted()方法的调用先行于被中断线程的代码检测到中断时间的发生。

  8. 对象finalize规则：一个对象的初始化完成（构造函数执行结束）先行于发生它的finalize()方法的开始。
  
     
  



## **CAS**

### 【原子类底层机制】

​	 对于简单的data++操作，如果使用synchronized显得有些大材小用，而且会导致线程的串行化，所以这个时候并发包下的Atomic原子类就闪亮登场，比如`AtomicInteger`

~~~java
// volatile保证可见性
private volatile int value;

/**
     * Creates a new AtomicInteger with the given initial value.
     *
     * @param initialValue the initial value
     */
public AtomicInteger(int initialValue) {
    value = initialValue;
}

/**
     * Atomically increments by one the current value.
     *
     * @return the previous value
     */
public final int getAndIncrement() {
    //这里利用unsafe方法
    return unsafe.getAndAddInt(this, valueOffset, 1); // i++操作
}
~~~

可以看见，Atomic原子类底层用的不是传统意义的锁机制，而是无锁化的CAS机制，通过CAS机制保证多线程修改一个数值的安全性，如下：

~~~java
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}

//native方法
public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5);
~~~





### 【什么是CAS算法,对CAS的理解】   

​	CAS(Compare And Swap)比较交换,其实现方式是基于硬件平台的汇编指令，在intel的CPU中，使用的是`cmpxchg`指令，也就是说CAS是靠硬件实现的，从而在硬件层面提升效率。

**实现思想**
	在线程开启的时候，会从主存中给每个线程拷贝一个变量副本到线程各自的运行环境中，CAS算法中包含三个参数(V,E,N)，**V**表示内存地址、**E**表示预期的值、**N**表示新值。  

**实现过程**

​	假如现在有两个线程t1,t2,，他们各自的运行环境中都有共享变量的副本V1、V2，预期值E1、E2，预期主存中的值还没有被改变，假设现在在并发环境，并且t1先拿到了执行权限，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次发起尝试，然后t1比较预期值E1和主存中的V，发现E1=V，说明预期值是正确的，执行N1=V1+1，并将N1的值传入主存。这时候贮存中的V=21，然后t2又紧接着拿到了执行权，比较E2和主存V的值，由于V已经被t1改为21，所以E2！=V，t2线程将主存中已经改变的值更新到自己的副本中，再发起重试；直到预期值等于主存中的值，说明没有别的线程对旧值进行修改，继续执行代码，退出；

**底层原理**
CPU实现原理指令有两种方式：

- **通过总线锁定来保证原子性**
  ​	总线锁定其实就是处理器使用了总线锁，所谓总线锁就是使用处理器提供的一个 LOCK# 信号，当一个处理器在总线上输出此信号时，其他处理器的请求将被阻塞住，那么该处理器可以独占共享内存。但是该方法成本太大。因此有了下面的方式。
- **通过缓存锁来保证**
  ​	所谓缓存锁定是指内存区域如果被缓存在处理器的缓存行中，并且在Lock操作期间被锁定，那么当它执行锁操作写回内存时，处理器不在总线上声言LOCK#信号，而是修改内部地址，并允许它的缓存一致性机制来保证操作的原子性，因为缓存一致性机制会阻止同时修改两个以上处理器缓存的内存区域数据，当其他处理器回写已被锁定的缓存行的数据时，会使缓存行无效。

有两种情况下处理器不会使用缓存锁定：

- 当操作的数据不能被缓存在处理器内部，或操作的数据跨多个缓存行时，则处理器会调用总总线锁定；
- 有些处理器不支持缓存锁定，对于Intel486和pentinum处理器，就是锁定的内存区域在处理器的缓存航也会调用总线锁定。

**CAS缺点**

- 循环时间太长；
- 只能保证一个共享变量原子操作；
- 会出现ABA问题；



### 【CAS算法在哪里有应用】

1、synchronized轻量级锁采用的就是类似于cas的实现 	

2、原子类

3、在`java.util.concurrent`包下提供了大量支持高效并发访问的集合接口和实现类。如：ConcurrentMap、ConcurrentLinkedQueue等线程安全集合



### 【java中的CAS，如何不用unsafe实现CAS 】

​	CAS是利用硬件实现的，如果不用unsafe来操纵硬件，只能自己写死循环



### 【CAS的ABA问题怎么解决】

​	ABA问题：当一个值从A更新为B，再从B更新为A，普通CAS机制会误判通过检测	

​	解决方案：添加版本号，通过比较值和版本号才判断是否可以替换




## **TreadLocal** 

### 【介绍ThreadLocal】

   ThreadLocal可以使每个线程保存自己的一些私有数据，起到线程隔离的作用。

~~~java
	/**
     * Returns the value in the current thread's copy of this
     * thread-local variable.  If the variable has no value for the
     * current thread, it is first initialized to the value returned
     * by an invocation of the {@link #initialValue} method.
     *
     * @return the current thread's value of this thread-local
     */
    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }

	/**
     * Sets the current thread's copy of this thread-local variable
     * to the specified value.  Most subclasses will have no need to
     * override this method, relying solely on the {@link #initialValue}
     * method to set the values of thread-locals.
     *
     * @param value the value to be stored in the current thread's copy of
     *        this thread-local.
     */
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }

	/**
     * Removes the current thread's value for this thread-local
     * variable.  If this thread-local variable is subsequently
     * {@linkplain #get read} by the current thread, its value will be
     * reinitialized by invoking its {@link #initialValue} method,
     * unless its value is {@linkplain #set set} by the current thread
     * in the interim.  This may result in multiple invocations of the
     * {@code initialValue} method in the current thread.
     *
     * @since 1.5
     */
     public void remove() {
         ThreadLocalMap m = getMap(Thread.currentThread());
         if (m != null)
             m.remove(this);
     }
~~~



### 【主线程的ThreadLocal怎么传递到线程池】

​	线程池可以直接使用主线程中的ThreadLocal

###   


## **JUC**和AQS

### 【JUC包下面了解哪些】

https://blog.csdn.net/weixin_34414196/article/details/93149855



### 【CountDownLatch的理解】

**概念**

- countDownLatch这个类使一个线程等待其他线程各自执行完毕后再执行。
- 是通过一个计数器来实现的，计数器的初始值是线程的数量。每当一个线程执行完毕后，计数器的值就-1，当计数器的值为0时，表示所有线程都执行完毕，然后在闭锁上等待的线程就可以恢复工作了。

**源码**

- countDownLatch类中只提供了一个构造器：

```cpp
//参数count为计数值
public CountDownLatch(int count) {  };  
```

- 类中有三个方法是最重要的：

```java
//调用await()方法的线程会被挂起，它会等待直到count值为0才继续执行
public void await() throws InterruptedException { }; 

//和await()类似，只不过等待一定的时间后count值还没变为0的话就会继续执行
public boolean await(long timeout, TimeUnit unit) throws InterruptedException { };  

//将count值减1
public void countDown() { };  
```

**使用**：

~~~java
public class CountDownLatchTest {

    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 10;
        CountDownLatch countDownLatch = new CountDownLatch(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("run..");
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        System.out.println("end");
        executorService.shutdown();
    }
    
}
~~~

结果：

~~~java
run..run..run..run..run..run..run..run..run..run..end
~~~



**CountDownLatch和CyclicBarrier区别：**  

1. CountDownLatch是一个计数器，线程完成一个记录一个，计数器递减，只能只用一次   
2. CyclicBarrier的计数器更像一个阀门，需要所有线程都到达，然后继续执行，计数器递增，提供`reset`功能，可以多次使用  



### 【CyclicBarrier的理解】

**概念：**

​	利用CyclicBarrier类可以实现一组线程相互等待，当所有线程都到达某个屏障点后再进行后续的操作。

**源码**：

构造器

~~~java
public CyclicBarrier(int parties, Runnable barrierAction) {
    if (parties <= 0) throw new IllegalArgumentException();
    this.parties = parties;
    this.count = parties;
    this.barrierCommand = barrierAction;
}
public CyclicBarrier(int parties) {
    this(parties, null);
}
~~~

主要方法：

~~~java
public int await() throws InterruptedException, BrokenBarrierException {
    try {
        return dowait(false, 0L);
    } catch (TimeoutException toe) {
        throw new Error(toe); // cannot happen
    }
}

public void reset() {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        breakBarrier();   // break the current generation
        nextGeneration(); // start a new generation
    } finally {
        lock.unlock();
    }
}
~~~



**使用：**

~~~java
public class CyclicBarrierTest {

    public static void main(String[] args) {
        final int totalThread = 10;
        CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("before..");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException | BrokenBarrierException e) {
                    e.printStackTrace();
                }
                System.out.print("after..");
            });
        }
        executorService.shutdown();
    }
}
~~~

结果：

~~~java
before..before..before..before..before..before..before..before..before..before..after..after..after..after..after..after..after..after..after..after..
~~~



### 【Semaphore的理解】

**概念：**  

​	信号量Semaphore也是一个线程同步的辅助类，可以维护当前访问自身的线程个数，并提供了同步机制。使用Semaphore可以控制同时访问资源的线程个数，例如，实现一个文件允许的并发访问数。

**源码：**

~~~java
//从此信号量获取一个许可，在提供一个许可前一直将线程阻塞，否则线程被中断。
void acquire();

//释放一个许可，将其返回给信号量。   
void release();

//返回此信号量中当前可用的许可数。
int availablePermits();

//查询是否有线程正在等待获取。
boolean hasQueuedThreads();
~~~

**使用：**

以下代码模拟了对某个服务的并发请求，每次只能有 3 个客户端同时访问，请求总数为 10。

~~~java
public class SemaphoreTest {

    public static void main(String[] args) {
        final int clientCount = 3;
        final int totalRequestCount = 10;
        Semaphore semaphore = new Semaphore(clientCount);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalRequestCount; i++) {
            executorService.execute(()->{
                try {
                    semaphore.acquire();
                    System.out.print(semaphore.availablePermits() + " ");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            });
        }
        executorService.shutdown();
    }
}
~~~

**结果：**

~~~java
1 0 0 1 2 2 0 1 2 2 
~~~


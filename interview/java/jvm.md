---
title: JVM
permalink: /interview/java/jvm
key: jvm
---

## start

### 【1.7和1.8的JVM有哪些不同】

1.8版本用**元数据区**取代了1.7版本及以前的**永久代**。元数据区和永久代本质上都是方法区的实现。

元空间与永久代之间最大的区别在于：元空间并不在虚拟机中，而是使用本地内存(也就是说Jvm可以使用外边的内存)。因此，默认情况下，元空间的大小仅受本地内存限制，但可以通过以下参数来指定元空间的大小：

`-XX:MetaspaceSize/-XX:MaxMetaspaceSize`




## **内存区域**

### 【虚拟内存】

> 虚拟内存是计算机系统**内存管理**的一种技术。它使得应用程序认为它拥有连续的可用的内存（一个连续完整的地址空间），而实际上，它通常是被分隔成多个物理内存碎片，还有部分暂时存储在外部磁盘存储器上，在需要时进行**数据交换**。目前，大多数操作系统都使用了虚拟内存，如Windows家族的“虚拟内存”；Linux的“交换空间”等                                                                                --[百度百科](https://baike.baidu.com/item/%E8%99%9A%E6%8B%9F%E5%86%85%E5%AD%98/101812?fr=aladdin)
>

比如windows电脑，打开任务管理器，进入性能选项，然后点击内存，可以查看内存使用情况，可以看到其已提交的内存容量是大于实际物理内存条容量的



### 【如果访问虚拟地址时，该地址在物理内存中不存在，会发生什么】

​	盲猜无法访问



### 【Jvm内存模型】   

参考文章：https://www.nowcoder.com/discuss/151138?type=1

​	![JVM内存划分](http://image.yangyhao.top/blog/JVM-%E5%86%85%E5%AD%98%E5%88%92%E5%88%86.png)

- **程序计数器**

  **作用**：

  字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理

  在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

  **特点：**

  线程私有，唯一一个不会出现OOM的内存区域

- **Java虚拟机栈**

- **本地方法栈**

- **方法区**

- **堆**



### 【结合内存模型，Java为什么可以跨平台】

​	因为Java不是运行在CPU上的，它是运行在JVM上面的，Java程序编译成字节码文件以后可以由JVM去“翻译”成对应平台硬件能执行的代码；

​	同时JVM在不同的操作系统，windows、linux、mac等都有支持，相当于JVM进行了适配，做到了JVM跨平台，所以开发人员只需要遵循Java语法就能在不同的平台上运行



### 【堆的原理和分代】   

​	JVM根据对象在内存中**存活时间**的长短，把堆内存分为**新生代**（包括一个Eden区、两个Survivor区）和**老年代**（Tenured或Old）。Perm代（**永久代**，Java 8开始被“**元空间**”取代）属于方法区了，而且仅在Full GC时被回收



### 【哪些是线程共有的，和私有的】

​	堆是线程共有的、栈是线程私有的



### 【Jvm内存中什么地方会发生OOM】  

​	除了程序计数器没有规定OOM外，其他任何内存区域都会发生OOM



### 【运行时常量池】 

**概念**
	运行时常量池是方法区的一部分。Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息时常量池，用于存放编译期生成的各种字面量和符号引用，这部分内容将在类加载后进入方法区的运行时常量池中存放。

​	运行时常量池相对于Class文件常量池的另外一个重要特征是具备**动态性**，Java语言并不要求常量一定只有编译期才能产生，也就是并非预置入Class文件中常量池的内容才能进入方法区运行时常量池，运行时常量池，运行期间也可能将新的常量放入池中，这种特性被开发人员利用比较多的就是String类的intern()方法。

​	final 常量 static 静态变量都是存放在运行时常量池中的

**好处**
	常量池是为了避免频繁的创建和销毁对象而影响系统性能，其实现了对象的共享。

**位置**

​	Jdk1.6 常量池在方法区 1.7在堆 1.8变元空间



### 【Java中如何实现共享内存】

​	对UNIX系统来说，共享内存分为一般共享内存和映像文件共享内存两种，对windows实际上只有映像文件共享内存一种。所以java应用中也是只能**创建映像文件共享内存**。

​	在jdk1.4中提供的类`MappedByteBuffer`为我们提供了实现共享内存的方法，该缓冲区实际上是一个磁盘文件的内存映像。



### 【Java内存泄漏如何排查】

**概念**：

内存泄漏是指程序在申请内存后，无法释放已申请的内存空间

**原因：**

内存泄漏的原因分析，总结出来只有一条：**存在无效的引用！**

**排查**：

1. 识别症状

   正如所讨论的，在许多情况下，Java进程最终会抛出一个OOM运行时异常，这是一个明确的指示，表明您的内存资源已经耗尽。在这种情况下，您需要区分正常的内存耗尽和泄漏。分析OOM的消息并尝试根据上面提供的讨论找到罪魁祸首。

   通常，如果Java应用程序请求的存储空间超过运行时堆提供的存储空间，则可能是由于设计不佳导致的。例如，如果应用程序创建映像的多个副本或将文件加载到数组中，则当映像或文件非常大时，它将耗尽存储空间。这是正常的资源耗尽。该应用程序按设计工作（虽然这种设计显然是愚蠢的）。

   但是，如果应用程序在处理相同类型的数据时稳定地增加其内存利用率，则可能会发生内存泄漏。

2. 启用详细垃圾回收

   通常可以通过检查verbosegc输出中的模式来识别内存约束问题。

3. 启用分析

   不同的JVM提供了生成跟踪文件以反映堆活动的不同方法，这些方法通常包括有关对象类型和大小的详细信息。这称为分析堆。

4. 分析踪迹



## **垃圾回收**

### 【JVM的垃圾回收机制】 

​	Java不同于C语言等，Java开发者大多数情况下不需要关心垃圾回收情况是由于Jvm会自己回收产生的垃圾，JVM垃圾回收主要面向堆和方法区中的对象



### 【说说GC的过程】  

1. 首先知道哪些内存需要回收
2. 其次判断哪些对象需要回收
3. 然后决定何时回收
4. 最后进行回收

------

 

**判断哪些需要回收** 

### 【怎么判断对象可被回收】

1. **引用计数法**（有缺陷，无法解决循环引用问题，JVM 没有采用）

   对象被引用一次，计数器+1，失去引用，计数器-1，当计数器在一段时间内为0时，即认为该对象可以被回收了。
   （无法解决相互引用的问题，例如：A引用B，B引用A，它们永远都不会再被使用）

   ~~~java
   public class ReferenceFindTest {
       public static void main(String[] args) {
           MyObject object1 = new MyObject();
           MyObject object2 = new MyObject();
             
           object1.object = object2;
           object2.object = object1;
             
           object1 = null;
           object2 = null;
       }
   }
   // o1 和 o2 都置空了，但是用引用计数法却无法被回收，因为他们互相应用
   ~~~

   

2. **可达性分析**（解决了引用计数的缺陷，被 JVM 采用）

   通过一系列的称为 “GC Roots” 的对象作为起始点，从这些节点开始向下搜索，所有所走过的路径称为引用链，当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是不可用的



### 【Jvm中哪些可以作为垃圾回收的GC Root?为什么呢】

1. 虚拟机栈中引用的对象（本地变量表）
2. 方法区中静态属性引用的对象
3. 方法区中常量引用的对象
4. 本地方法栈中引用的对象（Native对象）



### 【强软弱虚引用，以及软引用的回收时机】

在Java语言中，将引用又分为强引用、软引用、弱引用、虚引用4种，这四种引用强度依次逐渐减弱。

- 强引用

  在程序代码中普遍存在的，类似 `Object obj = new Object()` 这类引用，只要强引用还存在，垃圾收集器**永远不会回收**掉被引用的对象。

- 软引用

  用来描述一些还有用但并非必须的对象。对于软引用关联着的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行第二次回收。如果这次回收后还没有足够的内存，才会抛出内存溢出异常。

​		如果内存空间足够，垃圾回收器就不会回收它，如果**内存空间不足**了，就会回收这些对象的内存。

- 弱引用

  也是用来描述非必需对象的，但是它的强度比软引用更弱一些，被弱引用关联的对象只能生存到下一次垃圾收集发生之前。当垃圾收集器工作时，无论当前内存是否足够，**都会回收**掉只被弱引用关联的对象。

- 虚引用

  也叫幽灵引用或幻影引用（名字真会取，很魔幻的样子），是最弱的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。它的作用是能在这个对象被收集器回收时收到一个系统通知。

------

**垃圾回收算法**

### 【GC算法】  

1. **标记-清除算法**

   **概念**：标记-清除算法采用从根集合（GC Roots）进行扫描，对存活的对象进行标记，标记完毕后，再扫描整个空间中未被标记的对象，进行回收。

   **优点**：标记-清除算法不需要进行对象的移动，只需对不存活的对象进行处理，在存活对象比较多的情况下极为高效。

   **缺点：**但由于标记-清除算法直接回收不存活的对象，因此会造成内存碎片。

   

2. **复制算法**

   **概念：**它开始时把堆分成 一个对象 面和多个空闲面， 程序从对象面为对象分配空间，当对象满了，基于copying算法的垃圾收集就从根集合（GC Roots）中扫描活动对象，并将每个活动对象复制到**空闲面**(使得活动对象所占的内存之间没有空闲洞)，这样空闲面变成了对象面，原来的对象面变成了空闲面，程序会在新的对象面中分配内存。

   **优点：**复制算法的提出是为了克服句柄的开销和解决内存碎片的问题。

   **缺点：**效率低、需要的空间大

   

3. **标记-整理算法**

   **概念：**标记-整理算法采用标记-清除算法一样的方式进行对象的标记，但在清除时不同，在回收不存活的对象占用的空间后，会将所有的存活对象往左端空闲空间移动，并更新对应的指针。

   **优点：**标记-整理算法是在标记-清除算法的基础上，又进行了对象的移动，因此成本更高，但是却解决了内存碎片的问题。

   

4. **分代收集算法**

     分代收集算法是目前大部分JVM的垃圾收集器采用的算法。它的核心思想是根据对象存活的生命周期将内存划分为若干个不同的区域。一般情况下将堆区划分为老年代（Tenured Generation）和新生代（Young Generation），在堆区之外还有一个代就是永久代（Permanet Generation）。老年代的特点是每次垃圾收集时只有少量对象需要被回收，而新生代的特点是每次垃圾回收时都有大量的对象需要被回收，那么就可以根据不同代的特点采取最适合的收集算法。





### 【标记清除和标记整理的区别】 

​		标记清除：是先用**可达性分析法**标记哪些对象需要清理，然后再进行清除

​		标记整理：第一阶段和标记清除算法一样，先标记对象，不同点在于其不是立马清除对象，而是把对象**整理**好（按内存地址次序依次排列），然后将末端内存地址以后的内存全部回收



### 【JDK1.8的垃圾回收算法，介绍你所知道的垃圾回收算法】

- ​	标记-清除算法
- ​	复制算法
- ​	标记-整理算法
- ​	分代收集算法



### 【新生代和年老代的GC算法分别是什么】  

​	**新生代：复制算法**

​	**老年代：标记整理算法**



### 【老年代用别的回收算法行不行】 


 不行，因为老年代的空间比较小，不能够用复制算法





------

**垃圾收集器**

### 【为什么要进行分代回收】

分代回收的优势：

1. 首先就是分代回收可以对堆中对象采用不同的gc策略。在实际程序中，对象的生命周期有长有短。进行分代垃圾回收，能针对这个特点做很好的优化。
2. 分代以后，gc时进行可达性分析的范围能大大降低。

而如果不进行分代，由于老年代对象长期存活，所以总的gc频率应该和分代以后的young gc差不多，但是每次gc都需要从gc roots进行完整的堆遍历，无疑大大增加了开销。



### 【各种垃圾收集器、GC算法，安全点】

1. **Serial    （新生代）**

   - 年轻代收集器，可以和Serial Old、CMS组合使用

   - 采用复制算法

   - 使用单线程进行垃圾回收，回收时会导致Stop The World，用户进程停止

   - client模式年轻代默认算法

   - GC日志关键字：**DefNew(Default New Generation)**

     

2. **ParNew    （新生代）**

   - 新生代收集器，可以和Serial Old、CMS组合使用

   - 采用复制算法

   - 使用多线程进行垃圾回收，回收时会导致Stop The World，其它策略和Serial一样

   - server模式年轻代默认算法

   - 使用-XX:ParallelGCthreads参数来限制垃圾回收的线程数

   - GC日志关键字：**ParNew(Parallel New Generation)**

     

3. **Parallel Scavenge（新生代）**

   - 新生代收集器，可以和Serial Old、Parallel组合使用，不能和CMS组合使用

   - 采用复制算法

   - 使用多线程进行垃圾回收，回收时会导致Stop The World

   - 关注系统**吞吐量**

   - - -XX:MaxGCPauseMillis：设置大于0的毫秒数，收集器尽可能在该时间内完成垃圾回收

     - -XX:GCTimeRatio：大于0小于100的整数，即垃圾回收时间占总时间的比率，设置越小则希望垃圾回收所占时间越小，CPU能花更多的时间进行系统操作，提高吞吐量

     - -XX:UseAdaptiveSizePolicy：参数开关，启动后系统动态自适应调节各参数，如-Xmn、-XX：SurvivorRatio等参数，这是和ParNew收集器重要的区别

       

4. **Serial Old （老年代）**

   - 老年代收集器，可以和所有的年轻代收集器组合使用(Serial收集器的年老代版本）
   - 采用 ”标记-整理“算法，会对垃圾回收导致的内存碎片进行整理
   - 使用单线程进行垃圾回收，回收时会导致Stop The World，用户进程停止
   - GC日志关键字：**Tenured**

   

5. **Parallel Old（老年代）**

   - 年老代收集器，只能和Parallel Scavenge组合使用(Parallel Scavenge收集器的年老代版本）
   - 采用 ”标记-整理“算法，会对垃圾回收导致的内存碎片进行整理
   - 关注**吞吐量**的系统可以将Parallel Scavenge+Parallel Old组合使用
   - GC日志关键字：**ParOldGen**

   

6. **CMS   （老年代）**

   - 年老代收集器，可以和Serial、ParNew组合使用

   - 采用 ”标记-清除“算法，可以通过设置参数在垃圾回收时进行内存碎片的整理
     1、UserCMSCompactAtFullCollection：默认开启，FullGC时进行内存碎片整理，整理时用户进程需停止，即发生Stop The World
     2、CMSFullGCsBeforeCompaction：设置执行多少次不压缩的Full GC后，执行一个带压缩的（默认为0，表示每次进入Full GC时都进行碎片整理）

   - CMS是并发算法，表示垃圾回收和用户进行同时进行，但是不是所有阶段都同时进行，在初始标记、重新标记阶段还是需要Stop the World。CMS垃圾回收分这四个阶段
     1、初始标记（CMS Initial mark）   **Stop the World**  仅仅标记一下GC Roots能直接关联到的对象，速度快
     2、并发标记（CMS concurrent mark） 进行GC Roots Tracing，时间长，不发生用户进程停顿
     3、重新标记（CMS remark）      **Stop the World**  修正并发标记期间因用户程序继续运行导致标记变动的那一部分对象的标记记录，停顿时间较长，但远比并发标记时间短
     4、并发清除（CMS concurrent sweep） 清除的同时用户进程会导致新的垃圾，时间长，不发生用户进程停顿

   - 适合于对**响应时间要求高**的系统

   - GC日志关键字：CMS-initial-mark、CMS-concurrent-mark-start、CMS-concurrent-mark、CMS-concurrent-preclean-start、CMS-concurrent-preclean、CMS-concurrent-sweep、CMS-concurrent-reset等等

   - 缺点
     1、对CPU资源非常敏感
     2、CMS收集器无法处理浮动垃圾，即清除时用户进程同时产生的垃圾，只能等到下次GC时回收
     3、因为是使用“标记-清除”算法，所以会产生大量碎片

     

7. **G1    （新生代和老年代）**

   - 并行与并发
   - 分代收集
   - 空间整合
   - 可预测的停顿



### 【垃圾回收器简要介绍，讲一个单线程和一个多线程的】

​	单线程：Serial

​	多线程：ParNew



### 【了解过CMS收集器吗？CMS GC有什么问题？CMS会STW吗？ 】

**CMS缺点：**

- 浮动垃圾
- 空间碎片
- 对CPU资源敏感

**STW概念：**

Java中Stop-The-World机制简称STW，是在执行垃圾收集算法时，Java应用程序的其他所有线程都被挂起（除了垃圾收集帮助器之外）。Java中一种全局暂停现象，全局停顿，所有Java代码停止，native代码可以执行，但不能与JVM交互；这些现象多半是由于gc引起。

**CMS会STW吗:**

CMS在初始标记和重复标记阶段会停顿



### 【Jdk1.8用的是哪个垃圾回收器】  

~~~shell
java -XX:+PrintCommandLineFlags -version

-XX:InitialHeapSize=132318784 -XX:MaxHeapSize=2117100544 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
java version "1.8.0_171"
Java(TM) SE Runtime Environment (build 1.8.0_171-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.171-b11, mixed mode)
~~~

**Parallel Scavenge + Serial Old**



### 【G1 回收过程是怎么样的？】  

**G1概念：**

​	在G1中，堆被划分成 许多个连续的**区域**(region)。每个区域大小相等，在1M~32M之间。JVM最多支持2000个区域，可推算G1能支持的最大内存为2000*32M=62.5G。区域(region)的大小在JVM初始化的时候决定，也可以用-XX:G1HeapReginSize设置。

**GC模式：**

G1中提供了三种模式垃圾回收模式，young gc、mixed gc 和 full gc，在不同的条件下被触发。

- Young GC

  发生在年轻代的GC算法，一般对象（除了巨型对象）都是在eden region中分配内存，当所有eden region被耗尽无法申请内存时，就会触发一次young gc，这种触发机制和之前的young gc差不多，执行完一次young gc，活跃对象会被拷贝到survivor region或者晋升到old region中，空闲的region会被放入空闲列表中，等待下次被使用。

  

- Mixed GC

  当越来越多的对象晋升到老年代old region时，为了避免堆内存被耗尽，虚拟机会触发一个混合的垃圾收集器，即mixed gc，该算法并不是一个old gc，除了回收整个young region，还会回收一部分的old region，这里需要注意：是一部分老年代，而不是全部老年代，可以选择哪些old region进行收集，从而可以对垃圾回收的耗时时间进行控制。

  

- Full GC

  如果对象内存分配速度过快，mixed gc来不及回收，导致老年代被填满，就会触发一次full gc，G1的full gc算法就是单线程执行的serial old gc，会导致异常长时间的暂停时间，需要进行不断的调优，尽可能的避免full gc.



### 【CMS和G1的比较】  

- **CMS收集器**

  **运作过程**：

  1、初始标记  ；2、并发标记 ；3、重新标记 ； 4、并发清除

  **优点：**

  并发收集、低停顿。

  **缺点：**

  -  CMS收集器对CPU资源非常敏感

  -  CMS收集器无法处理浮动垃圾

  -  CMS是一款“标记--清除”算法实现的收集器，容易出现大量空间碎片

  

- **G1收集器**

   **运作步骤：**

  1、初始标记；2、并发标记；3、最终标记；4、筛选回收

  **优点：**

  1、并行于并发：G1能充分利用CPU、多核环境下的硬件优势，使用多个CPU（CPU或者CPU核心）来缩短stop-The-World停顿时间。部分其他收集器原本需要停顿Java线程执行的GC动作，G1收集器仍然可以通过并发的方式让java程序继续执行。

  2、分代收集：虽然G1可以不需要其他收集器配合就能独立管理整个GC堆，但是还是保留了分代的概念。它能够采用不同的方式去处理新创建的对象和已经存活了一段时间，熬过多次GC的旧对象以获取更好的收集效果。

  3、空间整合：与CMS的“标记--清理”算法不同，G1从整体来看是基于“标记整理”算法实现的收集器；从局部上来看是基于“复制”算法实现的。

  4、可预测的停顿：这是G1相对于CMS的另一个大优势，降低停顿时间是G1和ＣＭＳ共同的关注点，但Ｇ１除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为M毫秒的时间片段内，




### 【强制Young GC会有什么问题】 

​	会消耗处理时间，而且也会提高Young GC处理时间间隔



### 【Full GC是否可以回收方法区 】

​	GC主要是回收堆内存中的对象

> By default, CMS does not collect permgen (or the metaspace), so if it fills up, a full GC is needed to discard any unreferenced classes. 

> 默认情况下，CMS不会收集permgen（或元空间），因此，如果CMS填满了，则需要一个完整的GC来丢弃所有未引用的类。



### 【如何触发Full GC?用Jvm命令如何触发】  

**触发：**

1.老年代空间不足

2.metaspace空间不足也会造成Full GC。

3.CMS GC时出现promotion failed和concurrent mode failure

4.统计得到的Minor GC晋升到旧生代的平均大小大于旧生代的剩余空间

5.System.gc(）(手动调用可能触发)



**命令：**

jmap -histo:live <pid>

或者

jmap -dump:live,file=dump_001.bin PID 



### 【FullGC的时候会导致接口的响应速度特别慢，如何排查和解决】  

1、首先我们可以使用`top`命令查看系统CPU的占用情况

2、可以通过jstack命令查看线程id为xxx的线程为什么耗费CPU最高。

3、命令可以查看GC的情况

4、Jmap dump出日志，然后确定哪个对象的问题

Full GC次数过多，主要有以下两种**原因**：

- 代码中一次获取了大量的对象，导致内存溢出，此时可以通过eclipse的mat工具查看内存中有哪些对象比较多；
- 内存占用不高，但是Full GC次数还是比较多，此时可能是显示的`System.gc()`调用导致GC次数过多，这可以通过添加`-XX:+DisableExplicitGC`来禁用JVM对显示GC的响应。



### 【频繁FullGC怎么办 】 

 同上



### 【怎么避免产生浮动垃圾】

用G1垃圾收集器



### 【Remembered Set底层是怎么实现的】

  虚拟机使用rememberedSet来避免全堆扫描  

 




## **类加载**

### 【新建一个对象的流程。（从类加载开始讲）】 

`Student s = new Student();`的流程：

1. Student.class加载进内存
2. 声明一个Student类型引用s
3. 在堆内存创建对象,
4. 给对象中属性默认初始化值
5. 属性进行显示初始化
6. 构造方法进栈,对对象中的属性赋值,构造方法弹栈
7. 将对象的地址值赋值给s



### 【类加载的机制以及过程】

类加载流程是：

- **加载**：查找并加载类的二进制数据，即加载`class`文件

  加载方式有：

  ----从本地文件系统中加载

  ----通过网络下载`.class`文件

  ----从jar，war等归档文件中加载`.class`文件

  ----从专有数据库中提取`.class`文件

  ----将Java源文件动态编译为`.class`文件

  

- **连接**：

  - [ ] **验证**：确保被加载的类的正确性

    类的验证的内容：

    ----类文件的结构检查

    ----语义检查

    ----字节码验证

    ----二进制兼容性的验证

    

  - [ ] **准备**：为类的静态变量分配内存，并将其设置为默认值

    将静态变量设置为默认值，而实例变量将会在对象实例化时随着对象一起分配到Java堆中去。

    

  - [ ] **解析**：将类中的符号引用转换为直接引用

    将类中的符号引用转换为直接引用，（摘自深入理解JAVA虚拟机）

    > **符号引用：**
    >
    > 符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能够无歧义的定位到目标即可。在Java中，一个java类将会编译成一个class文件。在编译时，java类并不知道所引用的类的实际地址，因此只能使用符号引用来代替。比如org.simple.People类引用了org.simple.Language类，在编译时People类并不知道Language类的实际内存地址，因此只能使用符号org.simple.Language（假设是这个，当然实际中是由类似于CONSTANT_Class_info的常量来表示的）来表示Language类的地址。各种虚拟机实现的内存布局可能有所不同，但是它们能接受的符号引用都是一致的，因为符号引用的字面量形式明确定义在Java虚拟机规范的Class文件格式中。
    >
    > **直接引用：**
    >
    > 直接引用可以是直接指向目标的指针、相对偏移量或是一个能间接定位到目标的句柄。直接引用是和虚拟机实现的内存布局相关的，同一个符号引用在不同的虚拟机实例上翻译出来的直接引用一般不会相同。有了直接引用，那引用的目标必定已经在内存中存在。

    

- **初始化**：顺序执行静态代码，即为类的静态变量赋予正确的初始值以及执行静态代码块

  `Java`程序对类的使用分为两种：

  ----主动使用

  ----被动使用

  所有的`Java`虚拟机实现必须在每个类或接口被`Java`程序**首次主动使用**时才会初始化它们。

  主动使用分为七种：

  ----创建类的实例

  ----访问某个类或接口的静态变量，或者对该静态变量赋值

  ----调用类的静态方法

  ----反射（如`Class.forName(com.test.Test)`）

  ----初始化一个子类

  ----Java虚拟机启动时被标明为启动类的类

  ----JDK1.7开始提供的动态语言支持：`java.lang.invoke.MethodHandle`实例的解析结果`REF_getStatic,REF_putStatic,REF_invokeStatic`句柄对应的类没有初始化，则初始化。

  除了以上七种情况，其他使用Java类的方法都被看作是对类的被动使用，都不会导致类的初始化。
  



### 【如何动态加载类】  

**编译**时刻加载的类是静态加载类，**运行**时刻加载的类是动态加载类。

动态加载类是按需加载的，即需要什么类就加载什么类，不会影响其他的类使用

~~~java
Class A{
　　Public static void main(String[] args){
　　　　if("B".equal(args[0])){
　　　　　　B b=new B();
　　　　　　b.start();
　　　　}
　　　　if("C".equal(args[0])){
　　　　　　C c=new C();
　　　　　　C.start();
　　　　}
　　}
}
~~~

上面程序编译时会报错，当我们直接在cmd使用javac访问A.java类的时候，就会抛出问题:

但是如果我们动态加载就可以满足（参数不为“B”或者“C”时候，本应该可以运行）

如：

~~~java
public class Test2 {

    public static void main(String[] args) throws Exception{
        Class b = null;
        try {
            b = Class.forName("service.impl.B");
        } catch (ClassNotFoundException e) {
            System.err.println("something is wrong!!!");
            e.printStackTrace();
        }
        Stand stand = (Stand)b.newInstance();
        stand.start();
    }
}

interface Stand{
    void start();
}

class B implements Stand{
    public void start() {
        System.out.println("B...start");
    }
}

class C implements Stand{
    public void start() {
        System.out.println("C...start");
    }
}
~~~

所以，动态加载的方式如下：

~~~java
Class clazz = Class.forName("xxx");
Object xxx = clazz.newInstance();    
~~~



### 【类的编译过程】

Java编译器编译类的过程：

源码 -》词法分析器-》语法分析器-》语义分析器-》代码生成器（生成字节码）



### 【类加载在那个区域进行的】

方法区



### 【class文件在JDK1.8存储在哪】

堆内存：对象，每个对象包含与之对应的.class文件

栈内存：基本数据类型、对象的引用（ps：栈上分配使得局部变量分配在栈内存中）

方法区：static静态变量，class二进制文件



所以本题答案是方法区，即1.8的**元空间**



### 【如何加载一个不在根目录下的类】

~~~java
// 第一种：获取类加载的根路径  
this.getClass().getResource("/").getPath();

// 获取当前类的所在工程路径; 如果不加“/”  获取当前类的加载目录 
this.getClass().getResource("").getPath();

// 第二种：获取项目路径    
File directory = new File("");// 参数为空
String courseFile = directory.getCanonicalPath();

// 第三种：  
URL xmlpath = this.getClass().getClassLoader().getResource("");

// 第四种： 获取当前工程路径 
System.getProperty("user.dir"));

// 第五种：  获取所有的类路径 包括jar包的路径
System.getProperty("java.class.path");
~~~



### 【说一下Java类加载器的工作机制】

**概念：**

类加载器负责加载所有的类，其为所有被载入内存中的类生成一个`java.lang.Class`实例对象。一旦一个类被加载如JVM中，同一个类就不会被再次载入了。

**工作机制：**

类加载器加载Class大致要经过如下8个步骤：

1. 检测此Class是否载入过，即在缓冲区中是否有此Class，如果有直接进入第8步，否则进入第2步。
2. 如果没有父类加载器，则要么Parent是根类加载器，要么本身就是根类加载器，则跳到第4步，如果父类加载器存在，则进入第3步。
3. 请求使用父类加载器去载入目标类，如果载入成功则跳至第8步，否则接着执行第5步。
4. 请求使用根类加载器去载入目标类，如果载入成功则跳至第8步，否则跳至第7步。
5. 当前类加载器尝试寻找Class文件，如果找到则执行第6步，如果找不到则执行第7步。
6. 从文件中载入Class，成功后跳至第8步。
7. 抛出ClassNotFountException异常。
8. 返回对应的java.lang.Class对象
   



### 【类加载器之间的关系】

`Java`虚拟机自带的加载器

- **根类加载器**（`Bootstrap`）

  它用来加载 Java 的核心类，是用原生代码来实现的，并不继承自 java.lang.ClassLoader（负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类）。由于引导类加载器涉及到虚拟机本地实现细节，开发者无法直接获取到启动类加载器的引用，所以不允许直接通过引用进行操作。
  
- **扩展类加载器**（`Extension`）

  它负责加载JRE的扩展目录，lib/ext或者由java.ext.dirs系统属性指定的目录中的JAR包的类。由Java语言实现，父类加载器为null。

- **系统（应用）加载器**（`System`）

  它负责在JVM启动时加载来自Java命令的-classpath选项、java.class.path系统属性，或者CLASSPATH换将变量所指定的JAR包和类路径。程序可以通过ClassLoader的静态方法getSystemClassLoader()来获取系统类加载器。如果没有特别指定，则用户自定义的类加载器都以此类加载器作为父加载器。由Java语言实现，父类加载器为ExtClassLoader。
  

**总结：**

​		启动类加载器，由C++实现，没有父类。  
　　拓展类加载器(ExtClassLoader)，由Java语言实现，父类加载器为null  
　　系统类加载器(SystemClassLoader)，由Java语言实现，父类加载器为ExtClassLoader  
　　自定义类加载器，父类加载器肯定为SystemClassLoader。  



### 【什么时候需要自定义类加载器？如何实现自己的Classloader？会被什么类加载器加载？】  

**为什么自定义**：

1. 加密：众所周知，java代码很容易被反编译，如果你需要把自己的代码进行加密，可以先将编译后的代码用某种加密算法加密，然后实现自己的类加载器，负责将这段加密后的代码还原。

2. 从非标准的来源加载代码：例如你的部分字节码是放在数据库中甚至是网络上的，就可以自己写个类加载器，从指定的来源加载类。

3. 动态创建：为了性能等等可能的理由，根据实际情况动态创建代码并执行。

   

**如何实现：**

​	1、继承`java.lang.ClassLoader`类

​	2、重写父类的`findClass`方法

**被什么加载：**

​	系统级类加载器



### 【什么是双亲委派机制？为什么要有双亲委派机制？双亲委派机制是怎样实现的  ？】

- **概念：**

  所谓的双亲委派，则是先让父类加载器试图加载该Class，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类。通俗的讲，就是某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父加载器，依次递归，如果父加载器可以完成类加载任务，就成功返回；只有父加载器无法完成此加载任务时，才自己去加载。

  

- **原因：**

  1 安全，可避免用户自己编写的类动态替换Java的核心类，如java.lang.String

  2 避免全限定命名的类重复加载(使用了findLoadClass()判断当前类是否已加载)

  

- **原理：**

  `Launcher.java`是启动类，具体看源码分析



### 【你自己定义的类能被最顶级的类加载器加载吗？为什么？】  

不能，因为bootstrap只能够加载$JAVA_HOME中jre/lib/rt.jar里所有的class



## **分析调优** 

### 【OOM的分类？发生的原因？解决方式？】

[原文](https://blog.csdn.net/qq_40916779/article/details/99680243)

当 JVM 内存严重不足时，就会抛出 java.lang.OutOfMemoryError 错误。

1. **Java heap space**

   **概念**：

   当堆内存（Heap Space）没有足够空间存放新创建的对象时，就会抛出 `java.lang.OutOfMemoryError:Javaheap space` 错误（根据实际生产经验，可以对程序日志中的 OutOfMemoryError 配置关键字告警，一经发现，立即处理）。

   

   **原因分析：**

   `Java heap space` 错误产生的常见原因可以分为以下几类：

   1、请求创建一个超大对象，通常是一个大数组。

   2、超出预期的访问量/数据量，通常是上游系统请求流量飙升，常见于各类促销/秒杀活动，可以结合业务流量指标排查是否有尖状峰值。

   3、过度使用终结器（Finalizer），该对象没有立即被 GC。

   4、内存泄漏（Memory Leak），大量对象引用没有释放，JVM 无法对其自动回收，常见于使用了 File 等资源没有回收。

   

   **解决方案:**

   针对大部分情况，通常只需要通过 `-Xmx` 参数调高 JVM 堆内存空间即可。如果仍然没有解决，可以参考以下情况做进一步处理：

   1、如果是超大对象，可以检查其合理性，比如是否一次性查询了数据库全部结果，而没有做结果数限制。

   2、如果是业务峰值压力，可以考虑添加机器资源，或者做限流降级。

   3、如果是内存泄漏，需要找到持有的对象，修改代码设计，比如关闭没有释放的连接。

   

2. **GC overhead limit exceeded**

   当 Java 进程花费 98% 以上的时间执行 GC，但只恢复了不到 2% 的内存，且该动作连续重复了 5 次，就会抛出 `java.lang.OutOfMemoryError:GC overhead limit exceeded` 错误。简单地说，就是应用程序已经基本耗尽了所有可用内存， GC 也无法回收。

   此类问题的原因与解决方案跟 `Javaheap space` 非常类似，可以参考上文。

   

3. **Permgen space**

   **概念：**

   该错误表示永久代（Permanent Generation）已用满，通常是因为加载的 class 数目太多或体积太大。

   

   **原因分析:**

   永久代存储对象主要包括以下几类：

   1、加载/缓存到内存中的 class 定义，包括类的名称，字段，方法和字节码；

   2、常量池；

   3、对象数组/类型数组所关联的 class；

   4、JIT 编译器优化后的 class 信息。

   PermGen 的使用量与加载到内存的 class 的数量/大小正相关。

   

   **解决方案:**

   根据 Permgen space 报错的时机，可以采用不同的解决方案，如下所示：

   1、程序启动报错，修改 `-XX:MaxPermSize` 启动参数，调大永久代空间。

   2、应用重新部署时报错，很可能是没有应用没有重启，导致加载了多份 class 信息，只需重启 JVM 即可解决。

   3、运行时报错，应用程序可能会动态创建大量 class，而这些 class 的生命周期很短暂，但是 JVM 默认不会卸载 class，可以设置 `-XX:+CMSClassUnloadingEnabled` 和 `-XX:+UseConcMarkSweepGC`这两个参数允许 JVM 卸载 class。

   如果上述方法无法解决，可以通过 jmap 命令 dump 内存对象 `jmap-dump:format=b,file=dump.hprof` ，然后利用 Eclipse MAT https://www.eclipse.org/mat 功能逐一分析开销最大的 classloader 和重复 class。

   

4. **Metaspace**

   JDK 1.8 使用 Metaspace 替换了永久代（Permanent Generation），该错误表示 Metaspace 已被用满，通常是因为加载的 class 数目太多或体积太大。

   此类问题的原因与解决方法跟 `Permgenspace` 非常类似，可以参考上文。需要特别注意的是调整 Metaspace 空间大小的启动参数为 `-XX:MaxMetaspaceSize`。

   

5. **Unable to create new native thread**

   **概念：**

   每个 Java 线程都需要占用一定的内存空间，当 JVM 向底层操作系统请求创建一个新的 native 线程时，如果没有足够的资源分配就会报此类错误。

   

   **原因分析：**

   JVM 向 OS 请求创建 native 线程失败，就会抛出 `Unableto createnewnativethread`，常见的原因包括以下几类：

   1、线程数超过操作系统最大线程数 ulimit 限制；

   2、线程数超过 kernel.pid_max（只能重启）；

   3、native 内存不足；

   该问题发生的常见过程主要包括以下几步：

   1、JVM 内部的应用程序请求创建一个新的 Java 线程；

   2、JVM native 方法代理了该次请求，并向操作系统请求创建一个 native 线程；

   3、操作系统尝试创建一个新的 native 线程，并为其分配内存；

   4、如果操作系统的虚拟内存已耗尽，或是受到 32 位进程的地址空间限制，操作系统就会拒绝本次 native 内存分配；

   5、JVM 将抛出 `java.lang.OutOfMemoryError:Unableto createnewnativethread` 错误。

   

   **解决方案:**

   1、升级配置，为机器提供更多的内存；

   2、降低 Java Heap Space 大小；

   3、修复应用程序的线程泄漏问题；

   4、限制线程池大小；

   5、使用 -Xss 参数减少线程栈的大小；

   6、调高 OS 层面的线程最大数：执行 `ulimia-a` 查看最大线程数限制，使用 `ulimit-u xxx` 调整最大线程数限制。

   ulimit -a .... 省略部分内容 ..... max user processes (-u) 16384

   

6. **Out of swap space？**

   **概念：**

   该错误表示所有可用的虚拟内存已被耗尽。虚拟内存（Virtual Memory）由物理内存（Physical Memory）和交换空间（Swap Space）两部分组成。当运行时程序请求的虚拟内存溢出时就会报 `Outof swap space?` 错误。

   

   **原因分析:**

   该错误出现的常见原因包括以下几类：

   1、地址空间不足；

   2、物理内存已耗光；

   3、应用程序的本地内存泄漏（native leak），例如不断申请本地内存，却不释放。

   4、执行 `jmap-histo:live` 命令，强制执行 Full GC；如果几次执行后内存明显下降，则基本确认为 Direct ByteBuffer 问题。

   

   **解决方案：**

   根据错误原因可以采取如下解决方案：

   1、升级地址空间为 64 bit；

   2、使用 Arthas 检查是否为 Inflater/Deflater 解压缩问题，如果是，则显式调用 end 方法。

   3、Direct ByteBuffer 问题可以通过启动参数 `-XX:MaxDirectMemorySize` 调低阈值。

   4、升级服务器配置/隔离部署，避免争用。

   

7. **Kill process or sacrifice child**

   **概念：**

   有一种内核作业（Kernel Job）名为 Out of Memory Killer，它会在可用内存极低的情况下“杀死”（kill）某些进程。OOM Killer 会对所有进程进行打分，然后将评分较低的进程“杀死”，具体的评分规则可以参考 Surviving the Linux OOM Killer。

   不同于其他的 OOM 错误， `Killprocessorsacrifice child` 错误不是由 JVM 层面触发的，而是由操作系统层面触发的。

   

   **原因分析：**

   默认情况下，Linux 内核允许进程申请的内存总量大于系统可用内存，通过这种“错峰复用”的方式可以更有效的利用系统资源。

   然而，这种方式也会无可避免地带来一定的“超卖”风险。例如某些进程持续占用系统内存，然后导致其他进程没有可用内存。此时，系统将自动激活 OOM Killer，寻找评分低的进程，并将其“杀死”，释放内存资源。

   

   **解决方案：**

   1、升级服务器配置/隔离部署，避免争用。

   2、OOM Killer 调优。

   

8. **Requested array size exceeds VM limit**

   JVM 限制了数组的最大长度，该错误表示程序请求创建的数组超过最大长度限制。

   JVM 在为数组分配内存前，会检查要分配的数据结构在系统中是否可寻址，通常为 `Integer.MAX_VALUE-2`。

   此类问题比较罕见，通常需要检查代码，确认业务是否需要创建如此大的数组，是否可以拆分为多个块，分批执行。

   

9. **Direct buffer memory**

   **概念：**

   Java 允许应用程序通过 Direct ByteBuffer 直接访问堆外内存，许多高性能程序通过 Direct ByteBuffer 结合内存映射文件（Memory Mapped File）实现高速 IO。

   

   **原因分析：**

   Direct ByteBuffer 的默认大小为 64 MB，一旦使用超出限制，就会抛出 `Directbuffer memory` 错误。

   

   **解决方案：**

   1、Java 只能通过 ByteBuffer.allocateDirect 方法使用 Direct ByteBuffer，因此，可以通过 Arthas 等在线诊断工具拦截该方法进行排查。

   2、检查是否直接或间接使用了 NIO，如 netty，jetty 等。

   3、通过启动参数 `-XX:MaxDirectMemorySize` 调整 Direct ByteBuffer 的上限值。

   4、检查 JVM 参数是否有 `-XX:+DisableExplicitGC` 选项，如果有就去掉，因为该参数会使 `System.gc()` 失效。

   5、检查堆外内存使用代码，确认是否存在内存泄漏；或者通过反射调用 `sun.misc.Cleaner` 的 `clean()` 方法来主动释放被 Direct ByteBuffer 持有的内存空间。

   6、内存容量确实不足，升级配置。



### 【让你自己实现OOM，你会怎么做】

通过死循环无限创建字节数组（大对象），无法回收，很快就会OOM

~~~java
public static void main(String[] args){
    List<Byte[]> list = new ArrayList<>();
    while(true){
        list.add(new Byte[20000 * 1024 * 1024]);
    }
}
~~~





### 【高并发环境如何避免OOM】  

1. 尽量少的创建对象，优化业务处理逻辑，特别是占用内存大的对象。例如：我们可以把收到请求的 Request 对象在业务流程中一直传递下去，而不是每执行一个步骤，就创建一个内容和 Request 对象差不多的新对象。

2. 考虑自行回收并重用对象。建立一个对象池。收到请求后，在对象池内申请一个对象，使用完后再放回到对象池中，这样就可以反复地重用这些对象，非常有效地避免频繁触发垃圾回收。

3. 尽可能使用内存大的服务器




### 【如果大访问量，哪部分可能会OOM，如何处理】

大访问量即高并发，除了程序计数器外都会有可能OOM，处理方式可以参照上上题



### 【数据如果出现了阻塞，你是怎么排查的】

数据阻塞即请求没有响应



### 【JVM参数调优】

[摘自：](https://www.cnblogs.com/anyehome/p/9071619.html)

**JVM参数的含义**

|        **参数名称**         |                          **含义**                          |      **默认值**      |                                                              |
| :-------------------------: | :--------------------------------------------------------: | :------------------: | ------------------------------------------------------------ |
|            -Xms             |                         初始堆大小                         | 物理内存的1/64(<1GB) | 默认(MinHeapFreeRatio参数可以调整)空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制. |
|            -Xmx             |                         最大堆大小                         | 物理内存的1/4(<1GB)  | 默认(MaxHeapFreeRatio参数可以调整)空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制 |
|            -Xmn             |                  年轻代大小(1.4or lator)                   |                      | **注意**：此处的大小是（eden+ 2 survivor space).与jmap -heap中显示的New gen是不同的。 整个堆大小=年轻代大小 + 年老代大小 + 持久代大小. 增大年轻代后,将会减小年老代大小.此值对系统性能影响较大,Sun官方推荐配置为整个堆的3/8 |
|         -XX:NewSize         |                设置年轻代大小(for 1.3/1.4)                 |                      |                                                              |
|       -XX:MaxNewSize        |                 年轻代最大值(for 1.3/1.4)                  |                      |                                                              |
|        -XX:PermSize         |                 设置持久代(perm gen)初始值                 |    物理内存的1/64    |                                                              |
|       -XX:MaxPermSize       |                      设置持久代最大值                      |    物理内存的1/4     |                                                              |
|            -Xss             |                     每个线程的堆栈大小                     |                      | JDK5.0以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K.更具应用的线程所需内存大小进行 调整.在相同物理内存下,减小这个值能生成更多的线程.但是操作系统对一个进程内的线程数还是有限制的,不能无限生成,经验值在3000~5000左右 一般小的应用， 如果栈不是很深， 应该是128k够用的 大的应用建议使用256k。这个选项对性能影响比较大，需要严格的测试。（校长） 和threadstacksize选项解释很类似,官方文档似乎没有解释,在论坛中有这样一句话:"” -Xss is translated in a VM flag named ThreadStackSize” 一般设置这个值就可以了。 |
|    -*XX:ThreadStackSize*    |                     Thread Stack Size                      |                      | (0 means use default stack size) [Sparc: 512; Solaris x86: 320 (was 256 prior in 5.0 and earlier); Sparc 64 bit: 1024; Linux amd64: 1024 (was 0 in 5.0 and earlier); all others 0.] |
|        -XX:NewRatio         | 年轻代(包括Eden和两个Survivor区)与年老代的比值(除去持久代) |                      | -XX:NewRatio=4表示年轻代与年老代所占比值为1:4,年轻代占整个堆栈的1/5 Xms=Xmx并且设置了Xmn的情况下，该参数不需要进行设置。 |
|      -XX:SurvivorRatio      |                Eden区与Survivor区的大小比值                |                      | 设置为8,则两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个年轻代的1/10 |
|  -XX:LargePageSizeInBytes   |        内存页的大小不可设置过大， 会影响Perm的大小         |                      | =128m                                                        |
| -XX:+UseFastAccessorMethods |                     原始类型的快速优化                     |                      |                                                              |
|   -XX:+DisableExplicitGC    |                      关闭System.gc()                       |                      | 这个参数需要严格的测试                                       |
|  -XX:MaxTenuringThreshold   |                        垃圾最大年龄                        |                      | 如果设置为0的话,则年轻代对象不经过Survivor区,直接进入年老代. 对于年老代比较多的应用,可以提高效率.如果将此值设置为一个较大值,则年轻代对象会在Survivor区进行多次复制,这样可以增加对象再年轻代的存活 时间,增加在年轻代即被回收的概率 该参数只有在串行GC时才有效. |
|     -XX:+AggressiveOpts     |                          加快编译                          |                      |                                                              |
|    -XX:+UseBiasedLocking    |                      锁机制的性能改善                      |                      |                                                              |
|         -Xnoclassgc         |                        禁用垃圾回收                        |                      |                                                              |
| -XX:SoftRefLRUPolicyMSPerMB |          每兆堆空闲空间中SoftReference的存活时间           |          1s          | softly reachable objects will remain alive for some amount of time after the last time they were referenced. The default value is one second of lifetime per free megabyte in the heap |
| -XX:PretenureSizeThreshold  |               对象超过多大是直接在旧生代分配               |          0           | 单位字节 新生代采用Parallel Scavenge GC时无效 另一种直接在旧生代分配的情况是大的数组对象,且数组中无外部引用对象. |
| -XX:TLABWasteTargetPercent  |                    TLAB占eden区的百分比                    |          1%          |                                                              |
|   -XX:+*CollectGen0First*   |                     FullGC时是否先YGC                      |        false         |                                                              |

**并行收集器相关参数**

|                             |                                                   |      |                                                              |
| :-------------------------: | :-----------------------------------------------: | :--: | :----------------------------------------------------------: |
|     -XX:+UseParallelGC      |       Full GC采用parallel MSC (此项待验证)        |      | 选择垃圾收集器为并行收集器.此配置仅对年轻代有效.即上述配置下,年轻代使用并发收集,而年老代仍旧使用串行收集.(此项待验证) |
|      -XX:+UseParNewGC       |               设置年轻代为并行收集                |      | 可与CMS收集同时使用 JDK5.0以上,JVM会根据系统配置自行设置,所以无需再设置此值 |
|    -XX:ParallelGCThreads    |                并行收集器的线程数                 |      |          此值最好配置与处理器数目相等 同样适用于CMS          |
|    -XX:+UseParallelOldGC    | 年老代垃圾收集方式为并行收集(Parallel Compacting) |      |                  这个是JAVA 6出现的参数选项                  |
|    -XX:MaxGCPauseMillis     |    每次年轻代垃圾回收的最长时间(最大暂停时间)     |      |    如果无法满足此时间,JVM会自动调整年轻代大小,以满足此值.    |
| -XX:+UseAdaptiveSizePolicy  |    自动选择年轻代区大小和相应的Survivor区比例     |      | 设置此选项后,并行收集器会自动选择年轻代区大小和相应的Survivor区比例,以达到目标系统规定的最低相应时间或者收集频率等,此值建议使用并行收集器时,一直打开. |
|       -XX:GCTimeRatio       |      设置垃圾回收时间占程序运行时间的百分比       |      |                        公式为1/(1+n)                         |
| -XX:+*ScavengeBeforeFullGC* |                 Full GC前调用YGC                  | true | Do young generation GC prior to a full GC. (Introduced in 1.4.1.) |

**CMS相关参数**

|                                        |                                           |      |                                                              |
| :------------------------------------: | :---------------------------------------: | ---- | :----------------------------------------------------------: |
|        -XX:+UseConcMarkSweepGC         |              使用CMS内存收集              |      | 测试中配置这个以后,-XX:NewRatio=4的配置失效了,原因不明.所以,此时年轻代大小最好用-Xmn设置.??? |
|          -XX:+AggressiveHeap           |                                           |      | 试图是使用大量的物理内存 长时间大内存使用的优化，能检查计算资源（内存， 处理器数量） 至少需要256MB内存 大量的CPU／内存， （在1.4.1在4CPU的机器上已经显示有提升） |
|     -XX:CMSFullGCsBeforeCompaction     |           多少次后进行内存压缩            |      | 由于并发收集器不对内存空间进行压缩,整理,所以运行一段时间以后会产生"碎片",使得运行效率降低.此值设置运行多少次GC以后对内存空间进行压缩,整理. |
|     -XX:+CMSParallelRemarkEnabled      |               降低标记停顿                |      |                                                              |
|   -XX+UseCMSCompactAtFullCollection    |     在FULL GC的时候， 对年老代的压缩      |      | CMS是不会移动内存的， 因此， 这个非常容易产生碎片， 导致内存不够用， 因此， 内存的压缩这个时候就会被启用。 增加这个参数是个好习惯。 可能会影响性能,但是可以消除碎片 |
|   -XX:+UseCMSInitiatingOccupancyOnly   |     使用手动定义初始化定义开始CMS收集     |      |                  禁止hostspot自行触发CMS GC                  |
| -XX:CMSInitiatingOccupancyFraction=70  | 使用cms作为垃圾回收 使用70％后开始CMS收集 | 92   | 为了保证不出现promotion failed(见下面介绍)错误,该值的设置需要满足以下公式**[CMSInitiatingOccupancyFraction计算公式](https://www.cnblogs.com/redcreen/archive/2011/05/04/2037057.html#CMSInitiatingOccupancyFraction_value)** |
| -XX:CMSInitiatingPermOccupancyFraction |    设置Perm Gen使用到达多少比率时触发     | 92   |                                                              |
|        -XX:+CMSIncrementalMode         |              设置为增量模式               |      |                        用于单CPU情况                         |
|     -XX:+CMSClassUnloadingEnabled      |                                           |      |                                                              |

**辅助信息**

|                                       |                                                          |      |                                                              |
| :-----------------------------------: | :------------------------------------------------------: | :--: | :----------------------------------------------------------: |
|             -XX:+PrintGC              |                                                          |      | 输出形式:[GC 118250K->113543K(130112K), 0.0094143 secs] [Full GC 121376K->10414K(130112K), 0.0650971 secs] |
|          -XX:+PrintGCDetails          |                                                          |      | 输出形式:[GC [DefNew: 8614K->781K(9088K), 0.0123035 secs] 118250K->113543K(130112K), 0.0124633 secs] [GC [DefNew: 8614K->8614K(9088K), 0.0000665 secs][Tenured: 112761K->10414K(121024K), 0.0433488 secs] 121376K->10414K(130112K), 0.0436268 secs] |
|        -XX:+PrintGCTimeStamps         |                                                          |      |                                                              |
|    -XX:+PrintGC:PrintGCTimeStamps     |                                                          |      | 可与-XX:+PrintGC -XX:+PrintGCDetails混合使用 输出形式:11.851: [GC 98328K->93620K(130112K), 0.0082960 secs] |
|  -XX:+PrintGCApplicationStoppedTime   |     打印垃圾回收期间程序暂停的时间.可与上面混合使用      |      | 输出形式:Total time for which application threads were stopped: 0.0468229 seconds |
| -XX:+PrintGCApplicationConcurrentTime | 打印每次垃圾回收前,程序未中断的执行时间.可与上面混合使用 |      |         输出形式:Application time: 0.5291524 seconds         |
|          -XX:+PrintHeapAtGC           |                 打印GC前后的详细堆栈信息                 |      |                                                              |
|           -Xloggc:filename            |   把相关日志信息记录到文件以便分析. 与上面几个配合使用   |      |                                                              |
|       -XX:+PrintClassHistogram        |     garbage collects before printing the histogram.      |      |                                                              |
|            -XX:+PrintTLAB             |                  查看TLAB空间的使用情况                  |      |                                                              |
|     XX:+PrintTenuringDistribution     |           查看每次minor GC后新的存活周期的阈值           |      | Desired survivor size 1048576 bytes, new threshold 7 (max 15) new threshold 7即标识新的存活周期的阈值为7。 |



### 【JVM如何支持编解码】 

​	JVM参数可以设置字符编码	`-Dfile.encoding=utf-8` 



### 【JVM相关的分析工具有使用过哪些】

[原文：](https://blog.csdn.net/moshang_3377/article/details/94714716)

一、CLI

- jps
- jstatd
- jmap
- jinfo
- jstack
- jstat

二、GUI

- jconsole
- jvisualvm


---
title: Java集合框架
permalink: /interview/java/java-collections
key: java-collections
---

## **List**

### 【迭代器的实现原理】

**迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。**

其接口主要有三个：

- hasNext()
- next()
- remove()

~~~java
ArrayList list = new ArrayList();
list.add("a");
list.add("b");
list.add("c");
Iterator it = list.iterator();
while(it.hasNext()){
    String str = (String) it.next();
    System.out.println(str);
}
~~~

 每个集合框架内部都维持了一个Iterator，用于遍历集合，以ArrayList为例

~~~java
// 获取迭代器
public Iterator<E> iterator() {
     return new Itr();
}

private class Itr implements Iterator<E> {
    int cursor;       // index of next element to return
    int lastRet = -1; // index of last element returned; -1 if no such
    int expectedModCount = modCount;

    Itr() {}

    public boolean hasNext() {
        // 游标是否到头
        return cursor != size;
    }

    @SuppressWarnings("unchecked")
    public E next() {
        // 检测是否改动
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        // 获取当前游标的下一个元素
        return (E) elementData[lastRet = i];
    }

    public void remove() {
        if (lastRet < 0)
            throw new IllegalStateException();
        checkForComodification();

        try {
            ArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;
            expectedModCount = modCount;
        } catch (IndexOutOfBoundsException ex) {
            throw new ConcurrentModificationException();
     }
}
~~~

需要注意的一点是`next()`方法和`remove()`方法都有一个检测`checkForComodification()`

~~~java
final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
~~~

这个modCount在增（add）删（remove）元素的的时候会修改，所以在迭代器遍历的时候进行增删操作会抛出

`ConcurrentModificationException`异常



### 【ArrayList和LinkedList区别】   

ArrayList基于动态数组，随机访问效率高，适合查找，删除效率低，因为需要移动数组

LinkedList基于双向链表，适合删除，但访问效率低

 

### 【如何实现一个线程安全的List】

ArrayList不是线程安全的，比如我们不能一个线程向 ArrayList中添加元素，一个线程从其中 删除元素，这时会抛`ConcurrentModificationException`异常。



1、使用Vector，其内部每个方法都加上了`synchronization`保证线程安全

2、`Collections.synchronizedList(List list)`

~~~java
public static <T> List<T> synchronizedList(List<T> list) {
    return (list instanceof RandomAccess ?
            new SynchronizedRandomAccessList<>(list) :
            new SynchronizedList<>(list));
}
~~~



### 【ArrayList中如何删除某个元素的所有相同元素 】

1、for循环倒续删除

~~~java
for (int i = list.size() - 1; i >= 0; i--) {
    list.remove("xxx");
}
~~~

2、迭代器删除

~~~java
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()){
    if("xxx".equals(iterator.next())){
        iterator.remove();
    }
}
~~~

这个等价于：

~~~java
list.removeIf("xxx"::equals);
~~~



### 【两个list求差集】

1、`listA.removeAll(listB);`

2、利用hashMap空间换时间




## **HashMap** 

### 【HashMap底层实现】  

- 哈希

  ~~~java
  static final int hash(Object key) {
      int h;
      return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
  }
  ~~~

  

  **哈希取模如何哈希？**

  根据hash函数来

  

  **哈希冲突怎么办？**

  网上搜到：

  1、开放定址法

  2、链地址法

  3、再哈希法

  我的理解：

  哈希冲突时hashMap会把相同hash值的元素放到同一个数组中

  用**链表**或者**红黑树**解决(即拉链法)

  

  **能完全解决哈希冲突吗?**

   一般不可能

  

  **其他Hash冲突解决方式？**

  三种方式

  1、开放定址法

  2、链地址法

  3、再哈希法

  

  **一个对象调用hashcode返回的是什么**

  hash值

  

- 扩容机制

- put过程

- 扩容为什么是2

- 转红黑树节点数目为什么是8

- 头插

- 成环


这个可以见   [《HashMap源码解析》](/2019/12/16/HashMap源码解析.html)



### 【HashMap线程安全问题】

**1、为什么不是线程安全的？**

Rehash是HashMap在扩容时候的一个步骤

而多线程环境下Resize会出现环形链表的问题

**2、多线程有什么问题？**

环形链表

**3、怎么让HashMap变得线程安全？**  

1、利用ConcurrentHashMap代替HashMap

2、`Collections.synchronizedMap()`

**4、HashMap不用同步/并发容器，实现线程安全**  

 

### 【HashMap的key和value是否可以为空】

可以，运行一个key为null，value没有限制  



### 【红黑树需要比较大小才能进行插入，是依据什么进行比较的】

当链表元素大于8的时候会插入，当红黑树大小小于6会退化成链表

  


## **HashTable**

### 【HashTable的操作get和put的时间复杂度】

~~~java
public synchronized V get(Object key) {
    Entry<?,?> tab[] = table;
    //获取hash值
    int hash = key.hashCode();
    //根据hash值计算在数组中的索引
    int index = (hash & 0x7FFFFFFF) % tab.length;
    //遍历Entry链表
    for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
        //如果找到相同的返回值
        if ((e.hash == hash) && e.key.equals(key)) {
            return (V)e.value;
        }
    }
    return null;
}
~~~

时间复杂度：O(1) 如果hash特别差，会O(n)

~~~java
public synchronized V put(K key, V value) {
    // Make sure the value is not null
    if (value == null) {
        throw new NullPointerException();
    }

    // Makes sure the key is not already in the hashtable.
    Entry<?,?> tab[] = table;
    // 计算hash值和链表所在索引
    int hash = key.hashCode();
    int index = (hash & 0x7FFFFFFF) % tab.length;
    @SuppressWarnings("unchecked")
    Entry<K,V> entry = (Entry<K,V>)tab[index];
    // 遍历链表
    for(; entry != null ; entry = entry.next) {
        //如果hash冲突并且key相同
        if ((entry.hash == hash) && entry.key.equals(key)) {
            //覆盖旧值
            V old = entry.value;
            entry.value = value;
            //返回旧值
            return old;
        }
    }
	// 如果遍历数组和链表中没有对应key的值,则新添加一个
    addEntry(hash, key, value, index);
    return null;
}

private void addEntry(int hash, K key, V value, int index) {
    modCount++;

    Entry<?,?> tab[] = table;
    // 如果大于负载容量则重新计算hash
    if (count >= threshold) {
        // Rehash the table if the threshold is exceeded
        rehash();

        tab = table;
        hash = key.hashCode();
        index = (hash & 0x7FFFFFFF) % tab.length;
    }

    // Creates the new entry.
    @SuppressWarnings("unchecked")
    Entry<K,V> e = (Entry<K,V>) tab[index];
    // 根据新的参数在链表的头部添加新的entry
    tab[index] = new Entry<>(hash, key, value, e);
    count++;
}
~~~

时间复杂度：O(1) 如果hash特别差，会O(n)



### 【HashTable为什么效率低】   

​	因为其是线程安全的，效率比较低



### 【HashTable有没有对整个类加锁】 

​	没有，但是其对和hashmap相同的方法都加上了synchronized锁



### 【HashTable的key和value是否可以为空】 

​	不可以

~~~java
public synchronized V put(K key, V value) {
    // Make sure the value is not null
    if (value == null) {
        throw new NullPointerException();
    }
    ....
}        
~~~






## **ConcurrentHashMap**

### 【ConcurrentHashMap各版本（JDK1.7和1.8）的差异】

在JDK1.7中ConcurrentHashMap采用了**数组+Segment+分段锁**的方式实现。   

JDK8中ConcurrentHashMap参考了JDK8 HashMap的实现，采用了**数组+链表+红黑树**的实现方式来设计，**内部大量采用CAS操作 ** 

**1.数据结构**：取消了Segment分段锁的数据结构，取而代之的是数组+链表+红黑树的结构。  

**2.保证线程安全机制**：JDK1.7采用segment的分段锁机制实现线程安全，其中segment继承自ReentrantLock。JDK1.8采用CAS+Synchronized保证线程安全。  

**3.锁的粒度**：原来是对需要进行数据操作的Segment加锁，现调整为对每个数组元素加锁（Node）。  

**4.链表转化为红黑树**:定位结点的hash算法简化会带来弊端,Hash冲突加剧,因此在链表节点数量大于8时，会将链表转化为红黑树进行存储。  

**5.查询时间复杂度**：从原来的遍历链表O(n)，变成遍历红黑树O(logN)  



### 【jdk1.8对ConcurrentHashMap做了哪些优化】  

1、取消了segment字段，直接采用`transient volatile HashEntry[] table`保存数据，采用table数组元素作为锁，从而实现了对每一行数据进行加锁，进一步减少并发冲突的概率   

2、将原先table数组＋单向链表的数据结构，变更为table**数组＋单向链表＋红黑树**的结构，查询时间复杂度由O(n)提升到O(logn),提高性能



### 【ConcurrentHashMap的size()函数1.7和1.8的不同，或者介绍一下如果是你如何设计】



1.8

~~~java
public int size() {
    long n = sumCount();
    return ((n < 0L) ? 0 :
            (n > (long)Integer.MAX_VALUE) ? Integer.MAX_VALUE :
            (int)n);
}
~~~



### 【concurrentHashMap分段锁原理，用java8实现和java7有什么区别 】  

Jdk1.8变成**数组+链表+红黑树**的结构，优势如下：

**锁的粒度**  
首先锁的粒度并没有变粗，甚至变得更细了。每当扩容一次，ConcurrentHashMap的并发度就扩大一倍。  

**Hash冲突**  
JDK1.7中，ConcurrentHashMap从过二次hash的方式（Segment -> HashEntry）能够快速的找到查找的元素。在1.8中通过链表加红黑树的形式弥补了put、get时的性能差距。  

**扩容** 
JDK1.8中，在ConcurrentHashmap进行扩容时，其他线程可以通过检测数组中的节点决定是否对这条链表（红黑树）进行扩容，减小了扩容的粒度，提高了扩容的效率。  



### 【ConcurrentHashMap1.8为什么放弃了分段锁】 

**Segment**继承了重入锁**ReentrantLock**，有了锁的功能，每个锁控制的是一段，当每个Segment越来越大时，锁的粒度就变得有些大了。

- 分段锁的优势在于保证在操作不同段 map 的时候可以并发执行，操作同段 map 的时候，进行锁的竞争和等待。这相对于直接对整个map同步synchronized是有优势的。
- 缺点在于分成很多段时会比较浪费内存空间(不连续，碎片化); 操作map时竞争同一个分段锁的概率非常小时，分段锁反而会造成更新等操作的长时间等待; 当某个段很大时，分段锁的性能会下降。

1.8利用Synchronization代替了Segment

为什么是synchronized，而不是可重入锁
1. 减少内存开销  

  假设使用可重入锁来获得同步支持，那么每个节点都需要通过继承AQS来获得同步支持。但并不是每个节点都需要获得同步支持的，只有链表的头节点（红黑树的根节点）需要同步，这无疑带来了巨大内存浪费。

2. 获得JVM的支持 

  可重入锁毕竟是API这个级别的，后续的性能优化空间很小。  

  synchronized则是JVM直接支持的，JVM能够在运行时作出相应的优化措施：锁粗化、锁消除、锁自旋等等。这就使得synchronized能够随着JDK版本的升级而不改动代码的前提下获得性能上的提升。  


### 【ConcurrentHashMap默认桶数量，多线程写时冲突概率】  

​	16

### 【ConcurrentHashMap怎么实现线程安全的】 

  利用**CAS+Synchronized**



## **TreeMap**

### 【TreeMap解释下】

TreeMap中的元素默认按照keys的自然排序排列,其存储结构是红黑树

用法类似于HashMap

- HashMap可实现快速存储和检索，但其缺点是其包含的元素是无序的，这导致它在存在大量迭代的情况下表现不佳。
- LinkedHashMap保留了HashMap的优势，且其包含的元素是有序的。它在有大量迭代的情况下表现更好。
- TreeMap能便捷的实现对其内部元素的各种排序，但其一般性能比前两种map差。



### 【TreeMap查询写入的时间复杂度多少】 

​	logN

| Data Structure                               | 新增     | **查询/Find** | 删除/Delete | GetByIndex |
| -------------------------------------------- | -------- | ------------- | ----------- | ---------- |
| 数组 Array (T[])                             | O(n)     | **O(n)**      | O(n)        | O(1)       |
| 链表 Linked list (LinkedList<T>)             | O(1)     | **O(n)**      | O(n)        | O(n)       |
| Resizable array list (List<T>)               | O(1)     | **O(n)**      | O(n)        | O(1)       |
| Stack (Stack<T>)                             | O(1)     | **-**         | O(1)        | -          |
| Queue (Queue<T>)                             | O(1)     | **-**         | O(1)        | -          |
| Hash table (Dictionary<K,T>)                 | O(1)     | **O(1)**      | O(1)        | -          |
| Tree-based dictionary(SortedDictionary<K,T>) | O(log n) | **O(log n)**  | O(log n)    | -          |
| Hash table based set (HashSet<T>)            | O(1)     | **O(1)**      | O(1)        | -          |
| Tree based set (SortedSet<T>)                | O(log n) | **O(log n)**  | O(log n)    | -          |



### 【如何扩展TreeMap】 

创建的时候可以在构造方法里面传入实现了compartpor的匿名函数



## **比较**

### 【Set、Map、List适用场景】   



![](http://image.yangyhao.top/blog/java-collections-%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%85%B3%E7%B3%BB%E5%9B%BE.png)



- **List(有序,可重复) ** 
          
          ​    **ArrayList**  
          
          ​        底层数据结构是数组,查询快,增删慢  
          ​        线程不安全,效率高  
          ​    **Vector** 
          
          ​        底层数据结构是数组,查询快,增删慢  
          ​        线程安全,效率低  
          ​    **LinkedList ** 
          ​        底层数据结构是链表,查询慢,增删快  
          ​        线程不安全,效率高    
          
- **Set(无序,唯一)**
              **HashSet**  
                  底层数据结构是哈希表。             
                  **LinkedHashSet**  
                      底层数据结构由链表和哈希表组成。  
                      由链表保证元素有序。  
                      由哈希表保证元素唯一。  
              **TreeSet**  
                  底层数据结构是红黑树。(是一种自平衡的二叉树)  
                      根据比较的返回值是否是0来决定保证元素唯一性  
                      两种排序方式  
                          自然排序(元素具备比较性)  
                              让元素所属的类实现Comparable接口  
                          比较器排序(集合具备比较性)  
                              让集合接收一个Comparator的实现类对象  
          
- **Map(双列集合)**
          注：Map集合的数据结构仅仅针对键有效，与值无关。存储的是键值对形式的元素，键唯一，值可重复。        
          **HashMap**  
              底层数据结构是哈希表。线程不安全，效率高  
                  哈希表依赖两个方法：hashCode()和equals()  
                  执行顺序：  
                      首先判断hashCode()值是否相同  
                          是：继续执行equals(),看其返回值  
                              是true:说明元素重复，不添加  
                              是false:就直接添加到集合  
                          否：就直接添加到集合  
                  最终：  
                      自动生成hashCode()和equals()即可  
              **LinkedHashMap**  
                  底层数据结构由链表和哈希表组成。  
                      由链表保证元素有序。  
                      由哈希表保证元素唯一。  
          **Hashtable**  
              底层数据结构是哈希表。线程安全，效率低  
                  哈希表依赖两个方法：hashCode()和equals()  
                  执行顺序：  
                      首先判断hashCode()值是否相同  
                          是：继续执行equals(),看其返回值  
                              是true:说明元素重复，不添加  
                              是false:就直接添加到集合  
                          否：就直接添加到集合  
                  最终：  
                      自动生成hashCode()和equals()即可  
          **TreeMap**  
              底层数据结构是红黑树。(是一种自平衡的二叉树)  
                  根据比较的返回值是否是0来决定保证元素唯一性  
                      两种排序方式  
                          自然排序(元素具备比较性)  
                              让元素所属的类实现Comparable接口  
                          比较器排序(集合具备比较性)  
                              让集合接收一个Comparator的实现类对象   



### 【HashMap、Hashtable、ConcurrentHashMap的区别】   

#### HashTable

- 底层数组+链表实现，无论key还是value都**不能为null**，线程**安全**，实现线程安全的方式是在修改数据时锁住整个HashTable，效率低，ConcurrentHashMap做了相关优化
- 初始size为**11**，扩容：newsize = olesize*2+1
- 计算index的方法：index = (hash & 0x7FFFFFFF) % tab.length

#### HashMap

- 底层数组+链表实现，可**以存储null键和null值**，线程**不安全**
- 初始size为**16**，扩容：newsize = oldsize*2，size一定为2的n次幂
- 扩容针对整个Map，每次扩容时，原来数组中的元素依次重新计算存放位置，并重新插入
- 插入元素后才判断该不该扩容，有可能无效扩容（插入后如果扩容，如果没有再次插入，就会产生无效扩容）
- 当Map中元素总数超过Entry数组的75%，触发扩容操作，为了减少链表长度，元素分配更均匀
- 计算index方法：index = hash & (tab.length – 1)

#### ConcurrentHashMap

- 底层采用分段的数组+链表+红黑树实现，线程**安全**
- 通过把整个Map分为N个Segment，可以提供相同的线程安全，但是效率提升N倍，默认提升16倍。(读操作不加锁，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。)
- Hashtable的synchronized是针对整张Hash表的，即每次锁住整张表让线程独占，ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术
- 有些方法需要跨段，比如size()和containsValue()，它们可能需要锁定整个表而而不仅仅是某个段，这需要按顺序锁定所有段，操作完毕后，又按顺序释放所有段的锁
- 扩容：段内扩容（段内元素超过该段对应Entry数组长度的75%触发扩容，不会对整个Map进行扩容），插入前检测需不需要扩容，有效避免无效扩容

 

### 【Collections.sychronizedMap与ConcurrentHashMap的区别】  

1、Collections.synchronizedMap()和Hashtable一样，实现上在调用map所有方法时，都对整个map进行同步，而ConcurrentHashMap的实现却更加精细，它对map中的所有桶加了锁。    

2、ConcurrentHashMap从类的命名就能看出，它必然是个HashMap。而Collections.synchronizedMap()可以接收任意Map实例，实现Map的同步  



### 【HashMap与ConcurrentHashMap的性能比较】  

 hashMap是线程不安全的，性能高于ConcurrentHashMap
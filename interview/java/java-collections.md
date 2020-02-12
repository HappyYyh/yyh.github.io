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

JDK8中ConcurrentHashMap参考了JDK8 HashMap的实现，采用了**数组+链表+红黑树**的实现方式来设计，**内部大量采用CAS操作**

**1.数据结构**：取消了Segment分段锁的数据结构，取而代之的是数组+链表+红黑树的结构。
**2.保证线程安全机制**：JDK1.7采用segment的分段锁机制实现线程安全，其中segment继承自ReentrantLock。JDK1.8采用CAS+Synchronized保证线程安全。
**3.锁的粒度**：原来是对需要进行数据操作的Segment加锁，现调整为对每个数组元素加锁（Node）。
**4.链表转化为红黑树**:定位结点的hash算法简化会带来弊端,Hash冲突加剧,因此在链表节点数量大于8时，会将链表转化为红黑树进行存储。
**5.查询时间复杂度**：从原来的遍历链表O(n)，变成遍历红黑树O(logN)



### 【jdk1.8对ConcurrentHashMap做了哪些优化】  

1、取消了segment字段，直接采用transient volatile HashEntry[] table保存数据，采用table数组元素作为锁，从而实现了对每一行数据进行加锁，进一步减少并发冲突的概率

2、将原先table数组＋单向链表的数据结构，变更为table数组＋单向链表＋红黑树的结构，查询时时间复杂度由O(n)提升到O(logn),提高性能



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



### 【concurrentHashmap1.8为什么放弃了分段锁】 



### 【concurrentHashMap默认桶数量，多线程写时冲突概率】  



### 【ConcurrentHashMap怎么实现线程安全的】  


## **TreeMap**

TreeMap解释下？  
TreeMap查询写入的时间复杂度多少？  
扩展了TreeMap（是否自己实现了compartpor接口   



## **比较**

Set、Map、List适用场景   
HashMap、Hashtable、ConcurrentHashMap的区别，同步器实现机制。   
Collections.sychronizedMap与ConcurrentHashMap的区别  
HashMap与ConcurrentHashMap的性能比较  
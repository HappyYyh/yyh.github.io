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

HashMap底层实现  

-    扩容机制
-    put过程
-    扩容为什么是2
-    转红黑树节点数目为什么是8
-    头插
-    成环

HashMap为什么不是线程安全的？  
HashMap多线程有什么问题？怎么解决？  
怎么让HashMap变得线程安全？  
HashMap不用同步/并发容器，实现线程安全  
哈希取模如何哈希？哈希冲突怎么办？能完全解决哈希冲突吗?  
hash算法 ?  
一个对象调用hashcode返回的是什么？  
HashMap key和value是否可以为空  
为什么链表长度为8要转为红黑树  
红黑树需要比较大小才能进行插入，是依据什么进行比较的？  
其他Hash冲突解决方式？  


## **HashTable**

HashTable的操作get和put的时间复杂度？   
HashTable为什么效率低？    
HashTable有没有对整个类加锁？  
HashTablekey和value是否可以为空  


## **ConcurrentHashMap**

ConcurrentHashMap各版本的差异？  
ConcurrentHashMap，JDK1.7和1.8的不同实现  
jdk1.8对ConcurrentHashMap做了哪些优化？  
ConcurrentHashMap的size()函数1.7和1.8的不同，或者介绍一下如果是你如何设计  
concurrentHashMap分段锁原理，用java8实现和java7有什么区别   
concurrentHashmap1.8为什么放弃了分段锁 ?   
concurrentHashMap默认桶数量，多线程写时冲突概率  
ConcurrentHashMap怎么实现线程安全的？  


## **TreeMap**

TreeMap解释下？  
TreeMap查询写入的时间复杂度多少？  
扩展了TreeMap（是否自己实现了compartpor接口   



## **比较**

Set、Map、List适用场景   
HashMap、Hashtable、ConcurrentHashMap的区别，同步器实现机制。   
Collections.sychronizedMap与ConcurrentHashMap的区别  
HashMap与ConcurrentHashMap的性能比较  
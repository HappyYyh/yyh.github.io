---
title: Java集合框架
permalink: /interview/java/java-collections
key: java-collections
---

## **List**

如何实现一个线程安全的List  
ArrayList和LinkedList区别     
两个list求差集  
ArrayList中如何删除某个元素的所有相同元素  
讲一下迭代器的实现原理 


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
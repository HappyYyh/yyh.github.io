---
title: ConcurrentHashMap源码解析
tags: ["Java集合框架","源码"]
---

尚未完成，参考资料：https://www.cnblogs.com/zerotomax/p/8687425.html

几个重要属性

~~~java
private static final int MAXIMUM_CAPACITY = 1 << 30;
private static final int DEFAULT_CAPACITY = 16;
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;
static final int MIN_TREEIFY_CAPACITY = 64;
static final int MOVED     = -1; // 表示正在转移
static final int TREEBIN   = -2; // 表示已经转换成树
static final int RESERVED  = -3; // hash for transient reservations
static final int HASH_BITS = 0x7fffffff; // usable bits of normal node hash
transient volatile Node<K,V>[] table;//默认没初始化的数组，用来保存元素
private transient volatile Node<K,V>[] nextTable;//转移的时候用的数组
/**
     * 用来控制表初始化和扩容的，默认值为0，当在初始化的时候指定了大小，这会将这个大小保存在sizeCtl中，大小为数组的0.75
     * 当为负的时候，说明表正在初始化或扩张，
     *     -1表示初始化
     *     -(1+n) n:表示活动的扩张线程
     */
private transient volatile int sizeCtl;
~~~





## 1、构造函数

空参默认大小16，这个类似于HashMap

~~~java
/**
  * Creates a new, empty map with the default initial table size (16).
  */
public ConcurrentHashMap() {
}
/**
  * 指定容量
  */
public ConcurrentHashMap(int initialCapacity) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException();
    int cap = ((initialCapacity >= (MAXIMUM_CAPACITY >>> 1)) ?
               MAXIMUM_CAPACITY :
               tableSizeFor(initialCapacity + (initialCapacity >>> 1) + 1));
    this.sizeCtl = cap;
}

/**
  * 指定容量和负载因子
  */
public ConcurrentHashMap(int initialCapacity, float loadFactor) {
    this(initialCapacity, loadFactor, 1);
}
~~~



## 2、put

存放值

~~~java
/**
  *  单纯的额调用putVal方法，并且putVal的第三个参数设置为false
  *  当设置为false的时候表示这个value一定会设置
  *  true的时候，只有当这个key的value为空的时候才会设置
  */
public V put(K key, V value) {
    return putVal(key, value, false);
}

/*
 * 1、当添加一对键值对的时候，首先会去判断保存这些键值对的数组是不是初始化了，如果没有的话就初始化数组
 * 2、然后通过计算hash值来确定放在数组的哪个位置
 *    ①如果这个位置为空则直接添加，如果不为空的话，则取出这个节点来
 *    ②如果取出来的节点的hash值是MOVED(-1)的话，则表示当前正在对这个数组进行扩容，复制到新的数组，则当前线程也去帮助复制
 *    ③最后一种情况就是，如果这个节点，不为空，也不在扩容，则通过synchronized来加锁，进行添加操作
 * 3、然后判断当前取出的节点位置存放的是链表还是树
 *    ①如果是链表的话，则遍历整个链表，直到取出来的节点的key来个要放的key进行比较，如果key相等，并且key的hash值也相等的话，则说明是同一个key，则覆盖掉value，否则的话则添加到链表的末尾
 *    ②如果是树的话，则调用putTreeVal方法把这个元素添加到树中去
 * 4、最后在添加完成之后，会判断在该节点处共有多少个节点（注意是添加前的个数），如果达到8个以上了的话，则调用treeifyBin方法来尝试将处的链表转为树，或者扩容数组
 */
final V putVal(K key, V value, boolean onlyIfAbsent) {
    // 不允许空key和value
    if (key == null || value == null) throw new NullPointerException();
    // 获取key的hash值
    int hash = spread(key.hashCode());
    //用来计算在这个节点总共有多少个元素，用来控制扩容或者转移为树
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        // 如果tab为空则初始化
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            //如果这个位置没有元素的话，则通过cas的方式尝试添加，注意这个时候是没有加锁的
            if (casTabAt(tab, i, null,
                         //创建一个Node添加到数组中区，null表示的是下一个节点为空
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        /*
         * 如果检测到某个节点的hash值是MOVED，则表示正在进行数组扩张的数据复制阶段，
         * 则当前线程也会参与去复制，通过允许多线程复制的功能，一次来减少数组的复制所带来的性能损失
         */
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            /*
             * 如果在这个位置有元素的话，就采用synchronized的方式加锁，
             * 1、如果是链表的话(hash大于0)，就对这个链表的所有元素进行遍历，
             *         如果找到了key和key的hash值都一样的节点，则把它的值替换到
             *         如果没找到的话，则添加在链表的最后面
             * 2、如果是树的话，则调用putTreeVal方法添加到树中去 
             * 3、在添加完之后，会对该节点上关联的的数目进行判断，如果在8个以上的话，则会调用                           treeifyBin方法，来尝试转化为树，或者是扩容
             */
            V oldVal = null;
            synchronized (f) {
                //再次取出要存储的位置的元素，跟前面取出来的比较
                if (tabAt(tab, i) == f) {
                    //取出来的元素的hash值大于0，当转换为树之后，hash值为-2
                    if (fh >= 0) {
                        binCount = 1;
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            //要存的元素的hash，key跟要存储的位置的节点的相同的时候，替换掉该节点的value即可
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            //如果不是同样的hash，同样的key的时候，则判断该节点的下一个节点是否为空
                            if ((e = e.next) == null) {
                                //为空的话把这个要加入的节点设置为当前节点的下一个节点
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    //表示已经转化成红黑树类型了
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        //调用putTreeVal方法，将该元素添加到树中去
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                              value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                //当在同一个节点的数目达到8个的时候，则扩张数组或将给节点的数据转为tree
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    //计数
    addCount(1L, binCount);
    return null;
}
~~~



## 3、get

~~~java
public V get(Object key) {
    Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
    //计算hash值
    int h = spread(key.hashCode());
    // 找到key所在node
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (e = tabAt(tab, (n - 1) & h)) != null) {
        if ((eh = e.hash) == h) {
            //在链表第一个位置找到
            if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                return e.val;
        }
        //如果是树
        else if (eh < 0)
            return (p = e.find(h, key)) != null ? p.val : null;
        // 链表遍历
        while ((e = e.next) != null) {
            if (e.hash == h &&
                ((ek = e.key) == key || (ek != null && key.equals(ek))))
                return e.val;
        }
    }
    return null;
}
~~~

## **4、同步机制**

　　前面分析了下`ConcurrentHashMap`的源码，那么，对于一个映射集合来说，`ConcurrentHashMap`是如果来做到并发安全，又是如何做到高效的并发的呢？

　　首先是读操作，从源码中可以看出来，在get操作中，根本没有使用同步机制，也没有使用unsafe方法，所以读操作是支持并发操作的。

　　那么写操作呢？

　　分析这个之前，先看看什么情况下会引起数组的扩容，扩容是通过transfer方法来进行的。而调用transfer方法的只有`trePresize`、`helpTransfer`和`addCount`三个方法。

　　这三个方法又是分别在什么情况下进行调用的呢？

- `tryPresize`是在`treeIfybin`和`putAll`方法中调用，`treeIfybin`主要是在put添加元素完之后，判断该数组节点相关元素是不是已经超过8个的时候，如果超过则会调用这个方法来扩容数组或者把链表转为树。
- `helpTransfer`是在当一个线程要对table中元素进行操作的时候，如果检测到节点的HASH值为MOVED的时候，就会调用`helpTransfer`方法，在`helpTransfer`中再调用transfer方法来帮助完成数组的扩容
- `addCount`是在当对数组进行操作，使得数组中存储的元素个数发生了变化的时候会调用的方法。

　

　　**所以引起数组扩容的情况如下**：

- 只有在往map中添加元素的时候，在某一个节点的数目已经超过了8个，同时数组的长度又小于64的时候，才会触发数组的扩容。

- 当数组中元素达到了sizeCtl的数量的时候，则会调用transfer方法来进行扩容


　　

　　**那么在扩容的时候，可以不可以对数组进行读写操作呢？**

　　事实上是可以的。当在进行数组扩容的时候，如果当前节点还没有被处理（也就是说还没有设置为fwd节点），那就可以进行设置操作。

　　如果该节点已经被处理了，则当前线程也会加入到扩容的操作中去。

　　

　　**那么，多个线程又是如何同步处理的呢？**

　　在ConcurrentHashMap中，同步处理主要是通过`Synchronized`和`unsafe`两种方式来完成的。

- 在取得sizeCtl、某个位置的Node的时候，使用的都是unsafe的方法，来达到并发安全的目的

- 当需要在某个位置设置节点的时候，则会通过Synchronized的同步机制来锁定该位置的节点。

- 在数组扩容的时候，则通过处理的步长和fwd节点来达到并发安全的目的，通过设置hash值为MOVED

- 当把某个位置的节点复制到扩张后的table的时候，也通过Synchronized的同步机制来保证现程安全




## 其他

### spread

计算hash值

~~~java
// 这里的h传入的是key的hashCode（）方法
static final int spread(int h) {
    return (h ^ (h >>> 16)) & HASH_BITS;
}
~~~

### cas

~~~java
/**
 * 用来返回节点数组的指定位置的节点的原子操作
 */
static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) {
    return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);
}

/**
 * cas原子操作，在指定位置设定值
 */
static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i,
                                    Node<K,V> c, Node<K,V> v) {
    return U.compareAndSwapObject(tab, ((long)i << ASHIFT) + ABASE, c, v);
}

/**
 * 原子操作，在指定位置设定值
 */
static final <K,V> void setTabAt(Node<K,V>[] tab, int i, Node<K,V> v) {
    U.putObjectVolatile(tab, ((long)i << ASHIFT) + ABASE, v);
}
~~~





### initTable

初始化Table

~~~java
/**
 * Initializes table, using the size recorded in sizeCtl.
 */
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    while ((tab = table) == null || tab.length == 0) {
        //sizeCtl初始值为0，当小于0的时候表示在别的线程在初始化表或扩展表
        if ((sc = sizeCtl) < 0)
            Thread.yield(); // lost initialization race; just spin
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
            ////SIZECTL：表示当前对象的内存偏移量，sc表示期望值，-1表示要替换的值，设定为-1表示要初始化表了
            try {
                if ((tab = table) == null || tab.length == 0) {
                     //指定了大小的时候就创建指定大小的Node数组，否则创建指定大小(16)的Node数组
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                    table = tab = nt;
                    sc = n - (n >>> 2);
                }
            } finally {
                //初始化后，sizeCtl长度为数组长度的3/4
                sizeCtl = sc;
            }
            break;
        }
    }
    return tab;
}
~~~




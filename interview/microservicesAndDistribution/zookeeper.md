---
title: Zookeeper
permalink: /interview/microservicesAndDistribution/zookeeper
key: microservicesAndDistribution-zookeeper
---

## 概念

### 【对Zookeeper了解哪些？】

[原文：](https://blog.csdn.net/weijifeng_/article/details/79775738)

1. **Zookeeper功能简介：**

   ZooKeeper 是一个开源的分布式协调服务，由雅虎创建，是 Google Chubby 的开源实现。
   分布式应用程序可以基于 ZooKeeper 实现诸如数据发布/订阅、负载均衡、命名服务、分布式协
   调/通知、集群管理、Master 选举、配置维护，名字服务、分布式同步、分布式锁和分布式队列
   等功能。

2. **ZooKeeper基本概念:**

   - **集群角色**

     在zk中，有三种角色leader、Follower、Observe

   - **节点读写服务分工**

     1. ZooKeeper 集群的所有机器通过一个 Leader 选举过程来选定一台被称为『Leader』
        的机器，Leader服务器为客户端提供读和写服务。

     2. Follower 和 Observer 都能提供读服务，不能提供写服务。两者唯一的区别在于，
        Observer机器不参与 Leader 选举过程，也不参与写操作的『过半写成功』策略，因
        此 Observer 可以在不影响写性能的情况下提升集群的读性能

   - **Session**

     Session 是指客户端会话，在讲解客户端会话之前，我们先来了解下客户端连接。在  ZooKeeper 中，一个客户端连接是指客户端和 ZooKeeper 服务器之间的TCP长连接。

   - **数据节点**

     zookeeper的结构其实就是一个树形结构，leader就相当于其中的根结点，其它节点就相当于 follow节点，每个节点都保留自己的内容。

     zookeeper的节点分两类：持久节点和临时节点

     - 持久节点：
     所谓持久节点是指一旦这个 树形结构上被创建了，除非主动进行对树节点的移除操
     作，否则这个 节点将一直保存在 ZooKeeper 上。

     - 临时节点：
     临时节点的生命周期跟客户端会话绑定，一旦客户端会话失效，那么这个客户端创
     建的所有临时节点都会被移除。

   - **状态信息**

     每个 节点除了存储数据内容之外，还存储了 节点本身的一些状态信息。用 get 命令可以 同时获得某个 节点的内容和状态信息

   - **事物操作**

     在ZooKeeper中，能改变ZooKeeper服务器状态的操作称为事务操作。一般包括数据节点创建与删除、数据内容更新和客户端会话创建与失效等操作。对应每一个事务请求，ZooKeeper都会为其分配一个全局唯一的事务ID，用 ZXID 表示，通常是一个64位的数字。每一个 ZXID对应一次更新操作，从这些 ZXID 中可以间接地识别出 ZooKeeper 处理这些事务操作请求的全局顺序。
     
   - **Watcher(事件监听器)**
   
     是 ZooKeeper 中一个很重要的特性。ZooKeeper允许用户在指定节点上注册一些 Watcher， 并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去。该 机制是 ZooKeeper 实现分布式协调服务的重要特性。
   
3. **ZooKeeper应用的典型场景：**

   - **数据发布与订阅（配置中心）**

     数据发布与订阅，即所谓的配置中心，顾名思义就是发布者将数据发布到 ZooKeeper 节点上, 供订阅者进行数据订阅，进而达到动态获取数据的目的，实现配置信息的集中式管理和动态更新。

     发布/订阅系统一般有两种设计模式，分别是推（Push）和拉（Pull）模式。
     - 推模式
       服务端主动将数据更新发送给所有订阅的客户端
     - 拉模式
       客户端主动发起请求来获取最新数据，通常客户端都采用定时轮询拉取的方式

     ZooKeeper 采用的是推拉相结合的方式：
     客户端想服务端注册自己需要关注的节点，一旦该节点的数据发生变更，那么服务端就会向相应的客户端发送Watcher事件通知，客户端接收到这个消息通知后，需要主动到服务端获取最新的数据

   - **命名服务**

     命名服务也是分布式系统中比较常见的一类场景。在分布式系统中，通过使用命名服务，客户端
     应用能够根据指定名字来获取资源或服务的地址，提供者等信息。被命名的实体通常可以是集群中的
     机器，提供的服务，远程对象等等——这些我们都可以统称他们为名字。

     其中较为常见的就是一些分布式服务框架（如RPC）中的服务地址列表。通过在ZooKeepr里
     创建顺序节点，能够很容易创建一个全局唯一的路径，这个路径就可以作为一个名字。

     ZooKeeper 的命名服务即生成全局唯一的ID。      

   - **分布式协调服务/通知**

     ZooKeeper 中特有 Watcher 注册与异步通知机制，能够很好的实现分布式环境下不同机器，甚至不同系统之间的通知与协调，从而实现对数据变更的实时处理。使用方法通常是不同的客户端
     如果 机器节点 发生了变化，那么所有订阅的客户端都能够接收到相应的Watcher通知，并做出相应
     的处理。

     ZooKeeper的分布式协调/通知，是一种通用的分布式系统机器间的通信方式。

   - **Master选举**

     利用 ZooKeepr 的强一致性，能够很好地保证在分布式高并发情况下节点的创建一定能够
     保证全局唯一性，即 ZooKeeper 将会保证客户端无法创建一个已经存在的 数据单元节点。

     也就是说，如果同时有多个客户端请求创建同一个临时节点，那么最终一定只有一个客户端
     请求能够创建成功。利用这个特性，就能很容易地在分布式环境中进行 Master 选举了。

   - **分布式锁**

     分布式锁是控制分布式系统之间同步访问共享资源的一种方式




### 【Zookeeper属于CAP中哪个】 

- 一致性（C:Consistency）
- 可用性（A:Available）
- 分区容错性（P:Partition Tolerance）

ZooKeeper保证的是**CP**,没有A可用性理由如下：

- **不能保证每次服务请求的可用性**。

  任何时刻对ZooKeeper的访问请求能得到一致的数据结果，同时系统对网络分割具备容错性；但是它不能保证每次服务请求的可用性（注：也就是在极端环境下，ZooKeeper可能会丢弃一些请求，消费者程序需要重新请求才能获得结果）。所以说，ZooKeeper不能保证服务可用性。

- **进行leader选举时集群都是不可用的**。

  在使用ZooKeeper获取服务列表时，当master节点因为网络故障与其他节点失去联系时，剩余节点会重新进行leader选举。问题在于，选举leader的时间太长，30 ~ 120s, 且选举期间整个zk集群都是不可用的，这就导致在选举期间注册服务瘫痪，虽然服务能够最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。所以说，ZooKeeper不能保证服务可用性。
  

### 【Zookeeper的作用】

- 数据发布与订阅（配置中心）
- 负载均衡
- 命名服务(Naming Service)
- 分布式通知/协调
- 集群管理与Master选举
- 分布式锁
- 分布式队列



  

## 应用

### 【Zookeeper的监听原理？你来实现你怎么做】

**监听器原理:**

1. 在Zookeeper的API操作中，创建main()主方法即主线程；

2. 在main线程中创建Zookeeper客户端（zkClient），这时会创建两个线程：

    线程connet负责网络通信连接，连接服务器；

    线程Listener负责监听；

3. 客户端通过connet线程连接服务器；

4. 在Zookeeper的注册监听列表中将注册的监听事件添加到列表中，表示这个服务器中的/path，即根目录这个路径被客户端监听了；

5. 一旦被监听的服务器根目录下，数据或路径发生改变，Zookeeper就会将这个消息发送给Listener线程；

6. Listener线程内部调用process方法，采取相应的措施，例如更新服务器列表等。



**自定义实现：**

​	参照其监听原理



### 【Zookeeper底层如何去只让一个客户端成功创建临时节点】 

zookeeper中有两种类型的节点：

- 持久节点(PERSISENT)：一旦创建，除非主动调用删除操作，否则一直存储在zk上；
- 临时节点(EPHEMERAL)：与客户端会话绑定，一旦客户端会话失效，这个客户端所创建的所有临时节点都会被移除；

//创建节点的api

~~~java
//同步
String create(final String path, byte data[], List<ACL> acl, CreateMode createMode);
//异步
void create(final String path, byte data[], List<ACL> acl, CreateMode createMode, StringCallback cb, Object ctx);
~~~

底层原理得去看底层源码了。



### 【Zookeeper实现分布式锁】

**实现步骤:**

多个Jvm同时在Zookeeper上创建同一个相同的节点( /Lock)

**实现原理：** 

1. zk节点唯一的！ 不能重复！节点类型为临时节点， jvm1创建成功时候，jvm2和jvm3创建节点时候会报错，该节点已经存在。这时候 Jvm2和Jvm3进行等待。
2. Jvm1的程序现在执行完毕，执行释放锁。关闭当前会话。临时节点不复存在了并且事件通知Watcher，Jvm2和Jvm3继续创建。

>  ps：zk强制关闭时候，通知会有延迟。但是close（）方法关闭时候，延迟小

**实现步骤：**

这里引用了[文章](https://www.cnblogs.com/toov5/p/9899489.html)，没有具体实现，后续有空回尝试实现

1、引入依赖

~~~pom
<dependencies>
	<dependency>
		<groupId>com.101tec</groupId>
		<artifactId>zkclient</artifactId>
		<version>0.10</version>
	</dependency>
</dependencies>
~~~

2、创建锁的接口

~~~java
public interface ExtLock {
   
    //ExtLock基于zk实现分布式锁
    public void  getLock();
    
    //释放锁
    public void unLock();
    
}
~~~

3、模板方法模式

~~~java
import org.I0Itec.zkclient.ZkClient;

/**
 * 将重复代码抽象到子类中（模板方法设计模式）
 */
public abstract class ZookeeperAbstractLock implements ExtLock {
    
    private static final String CONNECTION="ip:2181";
    protected ZkClient zkClient = new ZkClient(CONNECTION);
    private String lockPath="/lockPath";
    
    /**
     * 获取锁
     */
      public void getLock() { 
          //1、连接zkClient 创建一个/lock的临时节点
          //2、如果节点创建成果，直接执行业务逻辑，如果节点创建失败，进行等待 
        if (tryLock()) {
            System.out.println("#####成功获取锁######");
        }else {
            //进行等待
            waitLock();
        }
        //3、使用事件通知监听该节点是否被删除，如果是，重新进入获取锁的资源  
    }
      
    //创建失败 进行等待
    abstract void waitLock();

    abstract boolean tryLock();
     
      
    /**
     * 释放锁
     */
      public void unLock() {
        //执行完毕 直接连接
          if (zkClient != null) {
              zkClient.close();
              System.out.println("######释放锁完毕######");
          }
      }
      
}
~~~

4、创建子类实现上面的 抽象方法

~~~java
import java.util.concurrent.CountDownLatch;
import org.I0Itec.zkclient.IZkDataListener;

public class ZookeeperDistrbuteLock extends ZookeeperAbstractLock {

    @Override
    boolean tryLock() {
        try {
            zkClient.createEphemeral(lockPath);
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    @Override
    void waitLock() {
        IZkDataListener iZkDataListener = new IZkDataListener() {
            // 节点被删除
            public void handleDataDeleted(String arg0) throws Exception {
                if (countDownLatch != null) {
                    countDownLatch.countDown(); // 计数器为0的情况，await 后面的继续执行
                }
            }

            // 节点被修改
            public void handleDataChange(String arg0, Object arg1) throws Exception {
            }
        };

        // 监听事件通知
        zkClient.subscribeDataChanges(lockPath, iZkDataListener);
        // 控制程序的等待
        if (zkClient.exists(lockPath)) {  //如果 检查出 已经被创建了 就new 然后进行等待
            countDownLatch = new CountDownLatch(1);
            try {
                countDownLatch.await(); //等待时候 就不往下走了   当为0 时候 后面的继续执行
            } catch (Exception e) {
                // TODO: handle exception
            }
        }
        //后面代码继续执行
        //为了不影响程序的执行 建议删除该事件监听 监听完了就删除掉
        zkClient.unsubscribeDataChanges(lockPath, iZkDataListener);
    }
}
~~~

5、生产订单号：

~~~java
import java.text.SimpleDateFormat;
import java.util.Date;

//生成订单号 时间戳
public class OrderNumGenerator {
 	//区分不同的订单号
    private static int count = 0;
	//单台服务器，多个线程 同事生成订单号
    public String getNumber(){
        try {
            Thread.sleep(500);
        } catch (Exception e) {
        }
        SimpleDateFormat simpt = new SimpleDateFormat("yyyy-MM-dd-HH-mm-ss");  
        return simpt.format(new Date()) + "-" + ++count;  //时间戳后面加了 count
    }
}
~~~

6、运行方法

~~~java
public class OrderService implements Runnable {

    private OrderNumGenerator orderNumGenerator = new OrderNumGenerator(); // 定义成全局的
    private ExtLock lock = new ZookeeperDistrbuteLock();

    public void run() {
        getNumber();
    }

    public synchronized void getNumber() { // 加锁 保证线程安全问题 让一个线程操作
        try {
            lock.getLock();
            String number = orderNumGenerator.getNumber();
            System.out.println(Thread.currentThread().getName() + ",number" + number);
        } catch (Exception e) {
        } finally {
            lock.unLock();
        }
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) { // 开启100个线程
            //模拟分布式锁的场景
            new Thread(new OrderService()).start();
        }
    }

}
~~~


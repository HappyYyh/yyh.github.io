---
title: Redis
permalink: /interview/microservicesAndDistribution/redis
key: microservicesAndDistribution-redis
---

## 概念应用

### 【谈谈你对Redis的了解】

**Redis概念：**

>  基于键值的内存数据库，其性能十分优秀，可以支持每秒十几万次的读写操作，其性能远超关系型数据库，并且支持集群，分布式，主从同步等配置

**Redis特征：**

1. 速度快：   

   数据存在哪里： 内存   

   什么语言写的： C语言   

   线程模式： 单线程 

2. 持久化：   

   Redis 所有数据保存在内存中，对数据的更新将异步的保存道磁盘上。

3. 支持多种编辑语言：  

   基于TCP的通信协议    

   Java  / PHP / Python / node.js 

4. 简单：   

   不依赖外部库  /  单线程模型 

5. 多种数据结构：  

   String / Hash / List / Set / Zset 

6. 高可用：   

   主从复制： 主服务器，从服务器 

7. 分布式：  

    Redis-Cluster(V3.0)支持分布式



### 【Redis你们都用来做什么】 

1. **缓存热点数据**

   对于热点数据，可以利用Redis缓存来降低db数据库的压力

2. **存放临时数据**

   对于临时数据，比如验证码等临时数据，可以使用redis的过期时间

3. **分布式锁**

   对于分布式多线程环境下可以用redis分布式锁



### 【一般设置过期时间吗？业务场景有哪些】

**过期时间概念：**

​	设置过期时间指的是在key上设置一个时间，使得key在这个时间之内存活，过了这个时间，则删除该key及其对应的值；redis中一般设置过期时间，而非使用del命令消除元素



**如何设置：**

~~~shell
expire key 10      #为给定的key设置过期时间为10秒
setex key 10 value #设置key的值为value，存活10秒--->针对key的value为String类型
~~~



**业务场景：**

就如上题所说的存放token或者登录信息，尤其是短信验证码都是有时间限制的，按照传统的数据库处理方式，一般都是自己判断过期，这样无疑会严重影响项目性能。

 

### 【Redis事务的了解】

[原文：](https://www.cnblogs.com/DeepInThought/p/10720132.html)

**Redis事务的概念：**

　　Redis 事务的本质是一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。

　　总结说：redis事务就是**一次性、顺序性、排他性**的执行一个队列中的一系列命令。　　

**Redis事务没有隔离级别的概念：**

　　批量操作在发送 EXEC 命令前被放入队列缓存，并不会被实际执行，也就不存在事务内的查询要看到事务里的更新，事务外查询不能看到。

**Redis不保证原子性：**

　　Redis中，单条命令是原子性执行的，但事务不保证原子性，且没有回滚。事务中任意命令执行失败，其余的命令仍会被执行。

**Redis事务的三个阶段：**

- 开始事务
- 命令入队
- 执行事务

**Redis事务相关命令：**

- `watch key1 key2 ...` : 监视一或多个key,如果在事务执行之前，被监视的key被其他命令改动，则事务被打断 （ 类似乐观锁 ）
- `multi` : 标记一个事务块的开始（ queued ）
- `exec` : 执行所有事务块的命令 （ 一旦执行exec后，之前加的监控锁都会被取消掉 ）　
- `discard` : 取消事务，放弃事务块中的所有命令
- `unwatch` : 取消watch对所有key的监控



## 数据结构

### 【Redis有哪些数据结构】

- **string**

  是redis中最基本的数据类型，一个key对应一个value。

  String类型是二进制安全的，意思是 redis 的 string 可以包含任何数据。如数字，字符串，jpg图片或者序列化的对象。

  ~~~shell
  set college szu
  ~~~

- **hash**

  这个是类似 map 的一种结构，这个一般就是可以将结构化的数据，比如一个对象（前提是**这个对象没嵌套其他的对象**）给缓存在 redis 里，然后每次读写缓存的时候，可以就操作 hash 里的**某个字段**。

  ~~~shell
  hset person name bingo
  hset person age 20
  hset person id 1
  hget person name
  
  person = {
      "name": "bingo",
      "age": 20,
      "id": 1
  }
  ~~~

- **list**

  List 说白了就是链表（redis 使用双端链表实现的 List），是有序的，value可以重复，可以通过下标取出对应的value值，左右两边都能进行插入和删除数据。

  使用列表的技巧

  - lpush+lpop=Stack(栈)
  - lpush+rpop=Queue（队列）
  - lpush+ltrim=Capped Collection（有限集合）
  - lpush+brpop=Message Queue（消息队列）

  ~~~shell
  lpush mylist 1 2 ll ls mem
  (integer) 5
  
  lrange mylist 0 -1
  1) "mem"
  2) "ls"
  3) "ll"
  4) "2"
  5) "1"
  127.0.0.1:6379>
  ~~~

- **set**

  set 是无序集合，自动去重。

  ~~~shell
  #-------操作一个set-------
  # 添加元素
  sadd mySet 1
  
  # 查看全部元素
  smembers mySet
  
  # 判断是否包含某个值
  sismember mySet 3
  
  # 删除某个/些元素
  srem mySet 1
  srem mySet 2 4
  
  # 查看元素个数
  scard mySet
  
  # 随机删除一个元素
  spop mySet
  
  #-------操作多个set-------
  # 将一个set的元素移动到另外一个set
  smove yourSet mySet 2
  
  # 求两set的交集
  sinter yourSet mySet
  
  # 求两set的并集
  sunion yourSet mySet
  
  # 求在yourSet中而不在mySet中的元素
  sdiff yourSet mySet
  ~~~

- **sorted set**

  sorted set 是排序的 set，去重但可以排序。

  ~~~shell
  zadd board 85 zhangsan
  zadd board 72 lisi
  zadd board 96 wangwu
  zadd board 63 zhaoliu
  
  # 获取排名前三的用户（默认是升序，所以需要 rev 改为降序）
  zrevrange board 0 3
  
  # 获取某用户的排名
  zrank board zhaoliu
  
  ~~~



### 【Redis的hash底层结构】

1. **概念**

   Redis的哈希对象的底层存储可以使用`ziplist`（压缩列表）和`hashtable`。当hash对象可以同时满足一下两个条件时，哈希对象使用`ziplist`编码。

   - 哈希对象保存的所有键值对的键和值的字符串长度都小于64字节
   - 哈希对象保存的键值对数量小于512个

2. **Redis hash数据结构**

   Redis的hash架构就是标准的`hashtab`的结构，通过挂链解决冲突问题。

3. **Redis ziplist数据结构**

   `ziplist`的数据结构主要包括两层，`ziplist`和`zipEntry`。

   - ziplist包括zip header、zip entry、zip end三个模块。
   - zip entry由prevlen、encoding&length、value三部分组成。
   - prevlen主要是指前面zipEntry的长度，coding&length是指编码字段长度和实际- 存储value的长度，value是指真正的内容。
   - 每个key/value存储结果中key用一个zipEntry存储，value用一个zipEntry存储。

4. **Redis hash存储过程源码分析**

   以hset命令为例进行分析，整个过程如下：

   - 首先查看hset中key对应的value是否存在，hashTypeLookupWriteOrCreate。
   - 判断key和value的长度确定是否需要从zipList到hashtab转换，hashTypeTryConversion。
   - 对key/value进行string层面的编码，解决内存效率问题。
   - 更新hash节点中key/value问题。
   - 其他后续操作的问题





### 【自定义设计一个hash底层如何设计】 

### 【Redis的set的底层结构】 

1. **概念**

   redis的集合对象set的底层存储结构使用了intset和hashtable两种数据结构存储的，intset我们可以理解为数组，hashtable就是普通的哈希表（key为set的值，value为null）。是不是觉得用hashtable存储set是一件很神奇的事情。

   set的底层存储**intset**和**hashtable**是存在编码转换的，使用**intset**存储必须满足下面两个条件，否则使用hashtable，条件如下：

   - 结合对象保存的所有元素都是整数值
   - 集合对象保存的元素数量不超过512个

2. **intset的数据结构**

   intset内部其实是一个数组（int8_t coentents[]数组），而且存储数据的时候是有序的，因为在查找数据的时候是通过二分查找来实现的。

   ~~~c
   typedef struct intset {
       
       // 编码方式
       uint32_t encoding;
   
       // 集合包含的元素数量
       uint32_t length;
   
       // 保存元素的数组
       int8_t contents[];
   
   } intset;
   ~~~

3. **redis set存储过程**

   以set的sadd命令为例子，整个添加过程如下：

   - 检查set是否存在不存在则创建一个set结合。
   - 根据传入的set集合一个个进行添加，添加的时候需要进行内存压缩。
   - setTypeAdd执行set添加过程中会判断是否进行编码转换。

   

### 【Redis的zset的底层结构】

1. **zset底层存储结构**

    zset底层的存储结构包括ziplist或skiplist，在同时满足以下两个条件的时候使用ziplist，其他时候使用skiplist，两个条件如下：

    - 有序集合保存的元素数量小于128个

    - 有序集合保存的所有元素的长度小于64字节

    当ziplist作为zset的底层存储结构时候，每个集合元素使用两个紧挨在一起的压缩列表节点来保存，第一个节点保存元素的成员，第二个元素保存元素的分值。

    当skiplist作为zset的底层存储结构的时候，使用skiplist按序保存元素及分值，使用dict来保存元素和分值的映射关系。

    ~~~c
    /*
         * 有序集合
         */
    typedef struct zset {
    
        // 字典，键为成员，值为分值
        // 用于支持 O(1) 复杂度的按成员取分值操作
        dict *dict;
    
        // 跳跃表，按分值排序成员
        // 用于支持平均复杂度为 O(log N) 的按分值定位成员操作
        // 以及范围操作
        zskiplist *zsl;
    
    } zset;
    ~~~

2. **ziplist数据结构**

    ziplist作为zset的存储结构时，可以参照上面hash结构

3. **skiplist数据结构**

    skiplist作为zset的存储结构，整体存储结构如下图，核心点主要是包括一个dict对象和一个skiplist对象。dict保存key/value，key为元素，value为分值；skiplist保存的有序的元素列表，每个元素包括元素和分值。两种数据结构下的元素指向相同的位置。

    

    zskiplist作为skiplist的数据结构，包括指向头尾的header和tail指针，其中level保存的是skiplist的最大的层数。

    ~~~c
    /*
     * 跳跃表
     */
    typedef struct zskiplist {
    
        // 表头节点和表尾节点
        struct zskiplistNode *header, *tail;
    
        // 表中节点的数量
        unsigned long length;
    
        // 表中层数最大的节点的层数
        int level;
    
    } zskiplist;
    ~~~

    skiplist跳跃列表中每个节点的数据格式，每个节点有保存数据的robj指针，分值score字段，后退指针backward便于回溯，zskiplistLevel的数组保存跳跃列表每层的指针。

    ~~~c
    /*
     * 跳跃表节点
     */
    typedef struct zskiplistNode {
    
        // 成员对象
        robj *obj;
    
        // 分值
        double score;
    
        // 后退指针
        struct zskiplistNode *backward;
    
        // 层
        struct zskiplistLevel {
    
            // 前进指针
            struct zskiplistNode *forward;
    
            // 跨度
            unsigned int span;
    
        } level[];
    
    } zskiplistNode;
    ~~~

4. **zset存储过程**
   
     zset的添加过程我们以zadd的操作作为例子进行分析，整个过程如下： 
    - 解析参数得到每个元素及其对应的分值
    - 查找key对应的zset是否存在不存在则创建
    - 如果存储格式是ziplist，那么在执行添加的过程中我们需要区分元素存在和不存在两种情况，存在情况下先删除后添加；不存在情况下则添加并且需要考虑元素的长度是否超出限制或实际已有的元素个数是否超过最大限制进而决定是否转为skiplist对象。
    - 如果存储格式是skiplist，那么在执行添加的过程中我们需要区分元素存在和不存在两种情况，存在的情况下先删除后添加，不存在情况下那么就直接添加，在skiplist当中添加完以后我们同时需要更新dict的对象。
    



### 【Redis跳跃表】 

**概念：**

> 跳跃表也就是SkipList，是一种有序数据结构，他通过在每个节点中维持多个指向其他节点的指针，从而达到快速访问节点的目的。
>
> 跳跃表支持平均(logN)、最坏O(N)复杂度的节点查找，还可以通过**顺序性操作**来**批量处理节点**。

**Redis中数据结构：**

（上文zset结构有记录）

~~~c
/*
 * 跳跃表
 */
typedef struct zskiplist {

    // 表头节点和表尾节点
    struct zskiplistNode *header, *tail;

    // 表中节点的数量
    unsigned long length;

    // 表中层数最大的节点的层数
    int level;

} zskiplist;

/*
 * 跳跃表节点
 */
typedef struct zskiplistNode {

    // 成员对象
    robj *obj;

    // 分值
    double score;

    // 后退指针
    struct zskiplistNode *backward;

    // 层
    struct zskiplistLevel {

        // 前进指针
        struct zskiplistNode *forward;

        // 跨度
        unsigned int span;

    } level[];

} zskiplistNode;
~~~



### 【跳跃表结构怎么做到有序性的】

对有续链表建立“索引”，按照层级查找



### 【Redis的setbit实现是怎么样的】 

   ~~~shell
#设置或清除该位在存储在键的字符串偏移
SETBIT key offset value
   ~~~

[点我百度](https://blog.csdn.net/hgd613/article/details/54095729)



### 【一致性哈希】

**概念：**

> Distributed Hash Table（DHT） 是一种哈希分布方式，其目的是为了克服传统哈希分布在服务器节点数量变化时大量数据迁移的问题。

**[基本原理](https://doocs.github.io/advanced-java/#/./docs/high-concurrency/redis-cluster)**

一致性 hash 算法将整个 hash 值空间组织成一个虚拟的圆环，整个空间按顺时针方向组织，下一步将各个 master 节点（使用服务器的 ip 或主机名）进行 hash。这样就能确定每个节点在其哈希环上的位置。

来了一个 key，首先计算 hash 值，并确定此数据在环上的位置，从此位置沿环**顺时针“行走”**，遇到的第一个 master 节点就是 key 所在位置。

在一致性哈希算法中，如果一个节点挂了，受影响的数据仅仅是此节点到环空间前一个节点（沿着逆时针方向行走遇到的第一个节点）之间的数据，其它不受影响。增加一个节点也同理。

燃鹅，一致性哈希算法在节点太少时，容易因为节点分布不均匀而造成**缓存热点**的问题。为了解决这种热点问题，一致性 hash 算法引入了虚拟节点机制，即对每一个节点计算多个 hash，每个计算结果位置都放置一个虚拟节点。这样就实现了数据的均匀分布，负载均衡。




## 持久化

### 【Redis持久化的方式】

**redis 持久化的两种方式**

- RDB：RDB 持久化机制，是对 redis 中的数据执行**周期性**的持久化。
- AOF：AOF 机制对每条写入命令作为日志，以 `append-only` 的模式写入一个日志文件中，在 redis 重启的时候，可以通过**回放** AOF 日志中的写入指令来重新构建整个数据集。

**RDB 优缺点**

- RDB 会生成多个数据文件，每个数据文件都代表了某一个时刻中 redis 的数据，这种多个数据文件的方式，**非常适合做冷备**，可以将这种完整的数据文件发送到一些远程的安全存储上去，比如说 Amazon 的 S3 云服务上去，在国内可以是阿里云的 ODPS 分布式存储上，以预定好的备份策略来定期备份 redis 中的数据。
- RDB 对 redis 对外提供的读写服务，影响非常小，可以让 redis **保持高性能**，因为 redis 主进程只需要 fork 一个子进程，让子进程执行磁盘 IO 操作来进行 RDB 持久化即可。
- 相对于 AOF 持久化机制来说，直接基于 RDB 数据文件来重启和恢复 redis 进程，更加快速。
- 如果想要在 redis 故障时，尽可能少的丢失数据，那么 RDB 没有 AOF 好。一般来说，RDB 数据快照文件，都是每隔 5 分钟，或者更长时间生成一次，这个时候就得接受一旦 redis 进程宕机，那么会丢失最近 5 分钟的数据。
- RDB 每次在 fork 子进程来执行 RDB 快照数据文件生成的时候，如果数据文件特别大，可能会导致对客户端提供的服务暂停数毫秒，或者甚至数秒。

**AOF 优缺点**

- AOF 可以更好的保护数据不丢失，一般 AOF 会每隔 1 秒，通过一个后台线程执行一次`fsync`操作，最多丢失 1 秒钟的数据。
- AOF 日志文件以 `append-only` 模式写入，所以没有任何磁盘寻址的开销，写入性能非常高，而且文件不容易破损，即使文件尾部破损，也很容易修复。
- AOF 日志文件即使过大的时候，出现后台重写操作，也不会影响客户端的读写。因为在 `rewrite` log 的时候，会对其中的指令进行压缩，创建出一份需要恢复数据的最小日志出来。在创建新日志文件的时候，老的日志文件还是照常写入。当新的 merge 后的日志文件 ready 的时候，再交换新老日志文件即可。
- AOF 日志文件的命令通过非常可读的方式进行记录，这个特性非常**适合做灾难性的误删除的紧急恢复**。比如某人不小心用 `flushall` 命令清空了所有数据，只要这个时候后台 `rewrite` 还没有发生，那么就可以立即拷贝 AOF 文件，将最后一条 `flushall` 命令给删了，然后再将该 `AOF` 文件放回去，就可以通过恢复机制，自动恢复所有数据。
- 对于同一份数据来说，AOF 日志文件通常比 RDB 数据快照文件更大。
- AOF 开启后，支持的写 QPS 会比 RDB 支持的写 QPS 低，因为 AOF 一般会配置成每秒 `fsync` 一次日志文件，当然，每秒一次 `fsync`，性能也还是很高的。（如果实时写入，那么 QPS 会大降，redis 性能会大大降低）
- 以前 AOF 发生过 bug，就是通过 AOF 记录的日志，进行数据恢复的时候，没有恢复一模一样的数据出来。所以说，类似 AOF 这种较为复杂的基于命令日志 / merge / 回放的方式，比基于 RDB 每次持久化一份完整的数据快照文件的方式，更加脆弱一些，容易有 bug。不过 AOF 就是为了避免 rewrite 过程导致的 bug，因此每次 rewrite 并不是基于旧的指令日志进行 merge 的，而是**基于当时内存中的数据进行指令的重新构建**，这样健壮性会好很多。



### 【AOF持久化怎么写入的】

​	[点我百度](https://www.cnblogs.com/chinaifae/articles/10384158.html) 

​	AOF 机制对每条写入命令作为日志，以 `append-only` 的模式写入一个日志文件中，在 redis 重启的时候，可以通过**回放** AOF 日志中的写入指令来重新构建整个数据集。



### 【只用AOF行不行】

​	不行；因为那样有两个问题：

- 第一，你通过 AOF 做冷备，没有 RDB 做冷备来的恢复速度更快；
- 第二，RDB 每次简单粗暴生成数据快照，更加健壮，可以避免 AOF 这种复杂的备份和恢复机制的 bug；



## 淘汰策略

### 【淘汰策略】

- FIFO（First In First Out）：先进先出策略，在实时性的场景下，需要经常访问最新的数据，那么就可以使用 FIFO，使得最先进入的数据（最晚的数据）被淘汰。
- LRU（Least Recently Used）：最近最久未使用策略，优先淘汰最久未使用的数据，也就是上次被访问时间距离现在最久的数据。该策略可以保证内存中的数据都是热点数据，也就是经常被访问的数据，从而保证缓存命中率。
- LFU（Least Frequently Used）：最不经常使用策略，优先淘汰一段时间内使用次数最少的数据。



### 【Redis过期策略】

Redis 过期策略是：**定期删除+惰性删除**。

> 所谓**定期删除**，指的是 redis 默认是每隔 100ms 就随机抽取一些设置了过期时间的 key，检查其是否过期，如果过期就删除。
>
> 获取 key 的时候，如果此时 key 已经过期，就删除，不会返回任何东西。

Redis 内存淘汰机制有以下几个：

- `noeviction`: 当内存不足以容纳新写入数据时，新写入操作会报错，这个一般没人用吧，实在是太恶心了。
- `allkeys-lru`：当内存不足以容纳新写入数据时，在**键空间**中，移除最近最少使用的 key（这个是**最常用**的）。
- `allkeys-random`：当内存不足以容纳新写入数据时，在**键空间**中，随机移除某个 key，这个一般没人用吧，为啥要随机，肯定是把最近最少使用的 key 给干掉啊。
- `volatile-lru`：当内存不足以容纳新写入数据时，在**设置了过期时间的键空间**中，移除最近最少使用的 key（这个一般不太合适）。
- `volatile-random`：当内存不足以容纳新写入数据时，在**设置了过期时间的键空间**中，**随机移除**某个 key。
- `volatile-ttl`：当内存不足以容纳新写入数据时，在**设置了过期时间的键空间**中，有**更早过期时间**的 key 优先移除。



### 【过期策略的具体过期方法】



### 【如何自己实现LRU】 

基于 双向链表 + HashMap 的 LRU 算法实现，对算法的解释如下：

- 访问某个节点时，将其从原来的位置删除，并重新插入到链表头部。这样就能保证链表尾部存储的就是最近最久未使用的节点，当节点数量大于缓存最大空间时就淘汰链表尾部的节点。
- 为了使删除操作时间复杂度为 O(1)，就不能采用遍历的方式找到某个节点。HashMap 存储着 Key 到节点的映射，通过 Key 就能以 O(1) 的时间得到节点，然后再以 O(1) 的时间将其从双向队列中删除。

~~~java
public class LRU<K, V> implements Iterable<K> {

    private Node head;
    private Node tail;
    private HashMap<K, Node> map;
    private int maxSize;

    private class Node {

        Node pre;
        Node next;
        K k;
        V v;

        public Node(K k, V v) {
            this.k = k;
            this.v = v;
        }
    }


    public LRU(int maxSize) {

        this.maxSize = maxSize;
        this.map = new HashMap<>(maxSize * 4 / 3);

        head = new Node(null, null);
        tail = new Node(null, null);

        head.next = tail;
        tail.pre = head;
    }


    public V get(K key) {

        if (!map.containsKey(key)) {
            return null;
        }

        Node node = map.get(key);
        unlink(node);
        appendHead(node);

        return node.v;
    }


    public void put(K key, V value) {

        if (map.containsKey(key)) {
            Node node = map.get(key);
            unlink(node);
        }

        Node node = new Node(key, value);
        map.put(key, node);
        appendHead(node);

        if (map.size() > maxSize) {
            Node toRemove = removeTail();
            map.remove(toRemove.k);
        }
    }


    private void unlink(Node node) {

        Node pre = node.pre;
        Node next = node.next;

        pre.next = next;
        next.pre = pre;

        node.pre = null;
        node.next = null;
    }


    private void appendHead(Node node) {
        Node next = head.next;
        node.next = next;
        next.pre = node;
        node.pre = head;
        head.next = node;
    }


    private Node removeTail() {

        Node node = tail.pre;

        Node pre = node.pre;
        tail.pre = pre;
        pre.next = tail;

        node.pre = null;
        node.next = null;

        return node;
    }


    @Override
    public Iterator<K> iterator() {

        return new Iterator<K>() {
            private Node cur = head.next;

            @Override
            public boolean hasNext() {
                return cur != tail;
            }

            @Override
            public K next() {
                Node node = cur;
                cur = cur.next;
                return node.k;
            }
        };
    }
}

~~~



## 分布式锁

### 【Redis分布式锁如何使用】

#### 	超时时间如何设置 



### 【分布式锁Redis和Zookeeper实现区别，使用场景】

###  

##  架构模式

### 【Redis为什么快】

### 【Redis是单线程的吗？有什么好处？】

### 【Redis的IO模型】

### 【怎么样保证Redis的高可用】

### 【分布式缓存读写不一致问题】

### 【Redis主从机制了解么？怎么实现的？】

### 【Redis同步策略】

### 【Redis哨兵机制】

### 【Redis 集群的实现？集群一致性？】

### 【生产环境Redis 如何做数据迁移】

### 【Redis 怎么保证不丢数据，能不能保证严格意义的一定不会丢】

### 【Redis扛不住的话有哪些解决方案】
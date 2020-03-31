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

   主从复制： 主服务器写，从服务器读

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

[原文链接](https://www.cnblogs.com/moxiaotao/p/10829799.html)

为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下四个条件：

1. 互斥性。在任意时刻，只有一个客户端能持有锁。
2. 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
3. 具有容错性。只要大部分的Redis节点正常运行，客户端就可以加锁和解锁。
4. 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。

**代码实现：**

~~~pom
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.9.0</version>
</dependency>
~~~

- **加锁**

  ~~~java
  public class RedisTool {
  
      private static final String LOCK_SUCCESS = "OK";
      private static final String SET_IF_NOT_EXIST = "NX";
      private static final String SET_WITH_EXPIRE_TIME = "PX";
  
      /**
       * 尝试获取分布式锁
       * @param jedis Redis客户端
       * @param lockKey 锁
       * @param requestId 请求标识
       * @param expireTime 超期时间
       * @return 是否获取成功
       */
      public static boolean tryGetDistributedLock(Jedis jedis, String lockKey, String requestId, int expireTime) {
  
          String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);
  
          if (LOCK_SUCCESS.equals(result)) {
              return true;
          }
          return false;
      }
  }
  ~~~

  这个set()方法一共有五个形参：

  - 第一个为key，我们使用key来当锁，因为key是唯一的。
  - 第二个为value，我们传的是requestId，很多童鞋可能不明白，有key作为锁不就够了吗，为什么还要用到value？原因就是我们在上面讲到可靠性时，分布式锁要满足第四个条件解铃还须系铃人，通过给value赋值为requestId，我们就知道这把锁是哪个请求加的了，在解锁的时候就可以有依据。requestId可以使用`UUID.randomUUID().toString()`方法生成。
  - 第三个为nxxx，这个参数我们填的是NX，意思是SET IF NOT EXIST，即当key不存在时，我们进行set操作；若key已经存在，则不做任何操作；
  - 第四个为expx，这个参数我们传的是PX，意思是我们要给这个key加一个过期的设置，具体时间由第五个参数决定。
  - 第五个为time，与第四个参数相呼应，代表key的过期时间。

  总的来说，执行上面的set()方法就只会导致两种结果：1. 当前没有锁（key不存在），那么就进行加锁操作，并对锁设置个有效期，同时value表示加锁的客户端。2. 已有锁存在，不做任何操作。

- **解锁**

  ~~~java
  public class RedisTool {
  
      private static final Long RELEASE_SUCCESS = 1L;
  
      /**
       * 释放分布式锁
       * @param jedis Redis客户端
       * @param lockKey 锁
       * @param requestId 请求标识
       * @return 是否释放成功
       */
      public static boolean releaseDistributedLock(Jedis jedis, String lockKey, String requestId) {
  
          String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
          Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId));
  
          if (RELEASE_SUCCESS.equals(result)) {
              return true;
          }
          return false;
      }
  }
  ~~~

  在eval命令执行Lua代码的时候，Lua代码将被当成一个命令去执行，并且直到eval命令执行完成，Redis才会执行其他命令。



### 【分布式锁Redis和Zookeeper实现区别，使用场景】

- **性能开销**

  Redis分布式锁，其实需要自己不断去尝试获取锁，比较消耗性能；zk分布式锁，获取不到锁，注册个监听器即可，不需要不断主动尝试获取锁，性能开销较小

- **锁释放**

  如果是redis获取锁的那个客户端bug了或者挂了，那么只能等待超时时间之后才能释放锁；而zk的话，因为创建的是临时znode，只要客户端挂了，znode就没了，此时就自动释放锁

- **可靠性**

  redis无法正确实现分布式锁！即使是redis单节点也不行！redis的所谓分布式锁无法用在对锁要求严格的场景下，比如：同一个时间点只能有一个客户端获取锁。zookeeper跟redis不一样，它是完全不依赖客户端的状态的，因此zookeeper才可以严格实现分布式锁



##  架构模式

参考文章：https://doocs.github.io/advanced-java/#/?id=%e7%bc%93%e5%ad%98

### 【Redis为什么快】

- 首先Redis是基于内存的，内存的速度会高于磁盘的速度；
- 其次它核心是基于非阻塞的 IO 多路复用机制；
- 然后它是C语言实现，一般来说，C 语言实现的程序“距离”操作系统更近，执行速度相对会更快。
- 最后Redis是单线程的，单线程反而避免了多线程的频繁上下文切换问题，预防了多线程可能产生的竞争问题。



### 【Redis是单线程的吗？有什么好处？】

Redis 内部使用文件事件处理器 `file event handler`，这个文件事件处理器是单线程的，所以 redis 才叫做**单线程的模型**。

**好处：**

单线程反而避免了多线程的频繁上下文切换问题，预防了多线程可能产生的竞争问题。



### 【Redis的IO模型】

Redis采用 IO 多路复用机制同时监听多个 socket，将产生事件的 socket 压入内存队列中，事件分派器根据 socket 上的事件类型来选择对应的事件处理器进行处理。

文件事件处理器的结构包含 4 个部分：

- 多个 socket
- IO 多路复用程序
- 文件事件分派器
- 事件处理器（连接应答处理器、命令请求处理器、命令回复处理器）

多个 socket 可能会并发产生不同的操作，每个操作对应不同的文件事件，但是 IO 多路复用程序会监听多个 socket，会将产生事件的 socket 放入队列中排队，事件分派器每次从队列中取出一个 socket，根据 socket 的事件类型交给对应的事件处理器进行处理。



### 【怎么样保证Redis的高可用】

redis 实现**高并发**主要依靠**主从架构**，一主多从，一般来说，很多项目其实就足够了，单主用来写入数据，单机几万 QPS，多从用来查询数据，多个从实例可以提供每秒 10w 的 QPS。

如果想要在实现高并发的同时，容纳大量的数据，那么就需要 redis 集群，使用 redis 集群之后，可以提供每秒几十万的读写并发。

redis 高可用，如果是做主从架构部署，那么加上哨兵就可以了，就可以实现，任何一个实例宕机，可以进行主备切换。



### 【分布式缓存读写不一致问题】

**读写不一致问题由来：**

> 查询时一般先查询缓存，如果缓存**命中**的话，那么直接将数据返回；如果缓存中没有数据（如**失效**，或者根本没设置数据），那么，应用程序先从数据库中查询数据，如果不为空，则将数据放在缓存中。
>
> 而更新时，无论是先更新缓存后更新数据库还是先更新数据库在更新缓存，只要其中任意一个更新失败，那么就有可能导致之后独写缓存不一致问题。

缓存和数据库一致性问题，有很多解决方案，没有最完美的方案，只有适合自身业务的尽可能完美的方案。

1. **Cache Aside Pattern**

   最经典的缓存+数据库读写的模式，就是 Cache Aside Pattern。

   - 读的时候，先读缓存，缓存没有的话，就读数据库，然后取出数据后放入缓存，同时返回响应。
   - 更新的时候，**先更新数据库，然后再删除缓存**。

   

3. **先删除缓存，再更新数据库**

   **问题：**如果先更新数据库，再删除缓存。如果删除缓存失败了，那么会导致数据库中是新数据，缓存中是旧数据，数据就出现了不一致。

   **解决方案：**先删除缓存，再更新数据库。如果数据库更新失败了，那么数据库中是旧数据，缓存中是空的，那么数据不会不一致。因为读的时候缓存没有，所以去读了数据库中的旧数据，然后更新到缓存中。

   

3. **读请求和写请求串行化**

   **问题：**数据发生了变更，先删除了缓存，然后要去修改数据库，此时还没修改。一个请求过来，去读缓存，发现缓存空了，去查询数据库，**查到了修改前的旧数据**，放到了缓存中。随后数据变更的程序完成了数据库的修改。完了，数据库和缓存中的数据不一样了...

   **解决方案**:

   更新数据的时候，根据**数据的唯一标识**，将操作路由之后，发送到一个 jvm 内部队列中。读取数据的时候，如果发现数据不在缓存中，那么将重新执行“读取数据+更新缓存”的操作，根据唯一标识路由之后，也发送到同一个 jvm 内部队列中。

   一个队列对应一个工作线程，每个工作线程**串行**拿到对应的操作，然后一条一条的执行。这样的话，一个数据变更的操作，先删除缓存，然后再去更新数据库，但是还没完成更新。此时如果一个读请求过来，没有读到缓存，那么可以先将缓存更新的请求发送到队列中，此时会在队列中积压，然后同步等待缓存更新完成。

   这里有一个**优化点**，一个队列中，其实**多个更新缓存请求串在一起是没意义的**，因此可以做过滤，如果发现队列中已经有一个更新缓存的请求了，那么就不用再放个更新请求操作进去了，直接等待前面的更新操作请求完成即可。

   待那个队列对应的工作线程完成了上一个操作的数据库的修改之后，才会去执行下一个操作，也就是缓存更新的操作，此时会从数据库中读取最新的值，然后写入缓存中。

   如果请求还在等待时间范围内，不断轮询发现可以取到值了，那么就直接返回；如果请求等待的时间超过一定时长，那么这一次直接从数据库中读取当前的旧值。



### 【Redis主从机制了解么？怎么实现的？】

**Redis主从架构：**

单机的Redis，能够承载的QPS 大概就在上万到几万不等。对于缓存来说，一般都是用来支撑**读高并发**的。因此架构做成主从(master-slave)架构，一主多从，主负责写，并且将数据复制到其它的 slave 节点，从节点负责读。所有的**读请求全部走从节点**。这样也可以很轻松实现水平扩容，**支撑读高并发**。



**Redis replication 的核心机制**

redis replication -> 主从架构 -> 读写分离 -> 水平扩容支撑读高并发

- redis 采用**异步方式**复制数据到 slave 节点，不过 redis2.8 开始，slave node 会周期性地确认自己每次复制的数据量；
- 一个 master node 是可以配置多个 slave node 的；
- slave node 也可以连接其他的 slave node；
- slave node 做复制的时候，不会 block master node 的正常工作；
- slave node 在做复制的时候，也不会 block 对自己的查询操作，它会用旧的数据集来提供服务；但是复制完成的时候，需要删除旧数据集，加载新数据集，这个时候就会暂停对外服务了；
- slave node 主要用来进行横向扩容，做读写分离，扩容的 slave node 可以提高读的吞吐量。

注意，如果采用了主从架构，那么建议必须**开启** master node 的持久化，不建议用 slave node 作为 master node 的数据热备，因为那样的话，如果你关掉 master 的持久化，可能在 master 宕机重启的时候数据是空的，然后可能一经过复制， slave node 的数据也丢了。

另外，master 的各种备份方案，也需要做。万一本地的所有文件丢失了，从备份中挑选一份 rdb 去恢复 master，这样才能**确保启动的时候，是有数据的**，即使采用了后续讲解的高可用机制，slave node 可以自动接管 master node，但也可能 sentinel 还没检测到 master failure，master node 就自动重启了，还是可能导致上面所有的 slave node 数据被清空。



**如何配置：**

在从节点配置文件上：

~~~shell
vim /etc/nginx/nginx.cof
replicaof 填写主节点的ip 和 端口号

info replication#可以查看本身的角色是主节点 还是 从节点
~~~





### 【Redis同步策略】

Redis主从复制可以根据是否是全量分为**全量同步和增量同步**。

**全量同步**

Redis全量复制一般发生在Slave初始化阶段，这时Slave需要将Master上的所有数据都复制一份。具体步骤如下： 

1. 从服务器连接主服务器，发送SYNC命令； 
2. 主服务器接收到SYNC命名后，开始执行BGSAVE命令生成RDB文件并使用缓冲区记录此后执行的所有写命令； 
3. 主服务器BGSAVE执行完后，向所有从服务器发送快照文件，并在发送期间继续记录被执行的写命令； 
4. 从服务器收到快照文件后丢弃所有旧数据，载入收到的快照； 
5. 主服务器快照发送完毕后开始向从服务器发送缓冲区中的写命令； 
6. 从服务器完成对快照的载入，开始接收命令请求，并执行来自主服务器缓冲区的写命令；  

完成上面几个步骤后就完成了从服务器数据初始化的所有操作，从服务器此时可以接收来自用户的读请求。

**增量同步**

Redis增量复制是指Slave初始化后开始正常工作时主服务器发生的写操作同步到从服务器的过程。 增量复制的过程主要是**主服务器每执行一个写命令就会向从服务器发送相同的写命令**，从服务器接收并执行收到的写命令。

**总结：**



### 【Redis哨兵机制】

**哨兵的介绍**

sentinel，中文名是哨兵。哨兵是 redis 集群机构中非常重要的一个组件，主要有以下功能：

- 集群监控：负责监控 redis master 和 slave 进程是否正常工作。
- 消息通知：如果某个 redis 实例有故障，那么哨兵负责发送消息作为报警通知给管理员。
- 故障转移：如果 master node 挂掉了，会自动转移到 slave node 上。
- 配置中心：如果故障转移发生了，通知 client 客户端新的 master 地址。

哨兵用于实现 redis 集群的高可用，本身也是分布式的，作为一个哨兵集群去运行，互相协同工作。

- 故障转移时，判断一个 master node 是否宕机了，需要大部分的哨兵都同意才行，涉及到了分布式选举的问题。
- 即使部分哨兵节点挂掉了，哨兵集群还是能正常工作的，因为如果一个作为高可用机制重要组成部分的故障转移系统本身是单点的，那就很坑爹了。

**哨兵的核心知识**

- 哨兵至少需要 3 个实例，来保证自己的健壮性。
- 哨兵 + redis 主从的部署架构，是**不保证数据零丢失**的，只能保证 redis 集群的高可用性。
- 对于哨兵 + redis 主从这种复杂的部署架构，尽量在测试环境和生产环境，都进行充足的测试和演练

**如何配置：**

在Redis安装目录下有一个sentinel.conf文件，copy一份进行修改

~~~shell
# 禁止保护模式
protected-mode no
# 配置监听的主服务器，这里sentinel monitor代表监控，mymaster代表服务器的名称，可以自定义，192.168.11.128代表监控的主服务器，6379代表端口，2代表只有两个或两个以上的哨兵认为主服务器不可用的时候，才会进行failover操作。
sentinel monitor mymaster 192.168.11.128 6379 2
# sentinel author-pass定义服务的密码，mymaster是服务名称，123456是Redis服务器密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster 123456
~~~

~~~shell
# 启动Redis服务器进程
./redis-server ../redis.conf
# 启动哨兵进程
./redis-sentinel ../sentinel.conf
~~~

|               配置项               |           参数类型           |                             作用                             |
| :--------------------------------: | :--------------------------: | :----------------------------------------------------------: |
|               `port`               |             整数             |                       启动哨兵进程端口                       |
|               `dir`                |          文件夹目录          |   哨兵进程服务临时文件夹，默认为/tmp，要保证有可写入的权限   |
| `sentinel down-after-milliseconds` |  <服务名称><毫秒数（整数）>  | 指定哨兵在监控Redis服务时，当Redis服务在一个默认毫秒数内都无法回答时，单个哨兵认为的主观下线时间，默认为30000（30秒） |
|     `sentinel parallel-syncs`      | <服务名称><服务器数（整数）> | 指定可以有多少个Redis服务同步新的主机，一般而言，这个数字越小同步时间越长，而越大，则对网络资源要求越高 |
|    `sentinel failover-timeout`     |  <服务名称><毫秒数（整数）>  | 指定故障切换允许的毫秒数，超过这个时间，就认为故障切换失败，默认为3分钟 |
|   `sentinel notification-script`   |     <服务名称><脚本路径>     | 指定sentinel检测到该监控的redis实例指向的实例异常时，调用的报警脚本。该配置项可选，比较常用 |





### 【Redis 集群的实现？集群一致性？】

> redis3.0版本后才支持集群

- **Redis集群简介：**

  1.  redis集群采用P2P模式，是完全去中心化的，不存在中心节点或者代理节点

  2.  redis集群是没有统一的入口的，客户端（client）连接集群的时候连接集群中的任意节点（node）即可，集群内部的节点是相互通信的（PING-PONG机制），每个节点都是一个redis实例；

  3.  为了实现集群的高可用，即判断节点是否健康（能否正常使用），redis-cluster有这么一个投票容错机制：如果集群中超过半数的节点投票认为某个节点挂了，那么这个节点就挂了（fail）。这是判断节点是否挂了的方法；

  4.  那么如何判断集群是否挂了呢? -> 如果集群中任意一个节点挂了，而且该节点没有从节点（备份节点），那么这个集群就挂了。这是判断集群是否挂了的方法；

- **环境准备：**

  Redis集群至少需要3个节点，因为投票容错机制要求超过半数节点认为某个节点挂了该节点才是挂了，所以2个节点无法构成集群

  要保证集群的高可用，需要每个节点都有从节点，也就是备份节点，所以Redis集群至少需要**6台服务器。**
  
- **具体实现：**

  一台服务器虚拟运行6个redis实例

  ~~~shell
  #1、在usr/local目录下新建redis-cluster目录，用于存放集群节点
  cd /usr/local
  mkdir redis-cluster
  
  #2 把redis目录下的bin目录下的所有文件复制到/usr/local/redis-cluster/redis01目录下，不用担心这里没有redis01目录，会自动创建的
  cp -r redis/bin/ redis-cluster/redis01
  
  #3 删除redis01目录下的快照文件dump.rdb，并且修改该目录下的redis.cnf文件，具体修改两处地方：一是端口号修改为7001，二是开启集群创建模式，打开注释即可。
  cd redis01
  rm -rf dump.rdb
  vim redis.conf
  
  port 7001  #端口
  cluster-enabled yes #打开集群模式
  
  #4 将redis-cluster/redis01文件复制5份到redis-cluster目录下（redis02-redis06），创建6个redis实例，模拟Redis集群的6个节点。然后将其余5个文件下的redis.conf里面的端口号分别修改为7002-7006
  cp -r redis01/ redis02
  ...
  
  #5 接着启动所有redis节点，由于一个一个启动太麻烦了，所以在这里创建一个批量启动redis节点的脚本文件，命令为start-all.sh
  touch start-all.sh
  vim start-all.sh
  cd redis01
  ./redis-server redis.conf
  cd ..
  cd redis02
  ./redis-server redis.conf
  cd ..
  cd redis03
  ./redis-server redis.conf
  cd ..
  cd redis04
  ./redis-server redis.conf
  cd ..
  cd redis05
  ./redis-server redis.conf
  cd ..
  cd redis06
  ./redis-server redis.conf
  cd ..
  
  #6 创建好启动脚本文件之后，需要修改该脚本的权限，使之能够执行
  chmod +x start-all.sh
  
  #7 执行start-all.sh脚本，启动6个redis节点
  start-all.sh
  
  ~~~

  创建一个集群

  ~~~shell
  redis-cli -a cyclone --cluster create --cluster-replicas 1 ip:7001 ip:7002 ip:7003 ip:7004 ip:7005 ip:7006
  ~~~

  连接到集群的某个节点：

  ~~~shell
  redis-cli -a cyclone -c -h 192.168.220.11 -p 7001
  ~~~

  查看当前集群信息

  ~~~shell
  cluster info
  ~~~

  查看集群里有多少个节点

  ~~~shell
  cluster nodes
  ~~~



### 【生产环境Redis 如何做数据迁移】

1. 使用`info replication` 命令找出主redis
2. 执行 `BGSAVE` 命令,会返回 Background saving started (保存redis中最新的key值)
3. 执行`LASTSAVE` 命令 ,会返回一个时间戳 (返回最近一次 Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示),检查主redis src目录下的dump.rdb生成时间,与当前时间相同
4. 关闭客户端,进入主redis的src目录下,拷贝此目录下的**dump.rdb** 文件
5. 将拷贝的**dump.rdb**文件替换新环境下 src目录下的配置文件



### 【Redis 怎么保证不丢数据，能不能保证严格意义的一定不会丢】

​	**RDB+AOF持久化机制+主从复制架构+哨兵模式**可以保证不丢数据

​	但不能保证严格意义的一定不会丢



### 【Redis扛不住的话有哪些解决方案】

单机的 redis，能够承载的 QPS 大概就在上万到几万不等。如果扛不住就换成**主从架构**,通俗点就是加机器
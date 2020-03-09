---
title: MQ
permalink: /interview/microservicesAndDistribution/mq
key: microservicesAndDistribution-mq
---



## 消费

### 【消息队列简介、用途】

**简介：**

> “消息队列”是在消息的传输过程中保存消息的容器。   ——[百度百科](https://baike.baidu.com/item/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/4751675?fr=aladdin)

**用途：**

- 解耦

  允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。

- 异步

  很多时候，用户不想也不需要立即处理消息。比如发红包，发短信等流程。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们

- 削峰

  使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。



### 【消息的推拉模型】

**Push 和 Pull的概念**

- 所谓 Push 模型，即当 Producer 发出的消息到达后，服务端马上将这条消息投递给 Consumer；
- 而 Pull 则是服务端收到这条消息后什么也不做，只是等着 Consumer 主动到自己这里来读，即 Consumer 这里有一个“拉取”的动作。

**Push模型优缺点**

- Push模型优点

  实时（因为服务端Broker一旦收到消息，就会发送给消费者，不管消费这准备好没有，消费者是死是活，缓存到消费端的BlockingQueue中）

- Push缺点

  1. 消息保存在服务端broker，容易造成消息堆积。

  2. 服务端broker需要维护每次传输状态，遇到问题需要重试

  3. 服务端broker需要依据订阅者消费能力做流控(流转机制)。

**Pull模型优缺点**

- Pull模型优点
  1. 保存在消费端，获取消息方便。

  2. 传输失败，不需要重试。

  3. 消费端可以根据自身消费能力决定是否pull(流转机制) 

- Pull缺点

  默认的短轮询方式的实时性依赖于pull间隔时间，间隔越大，实时性越低，长轮询方式和push一致。

**各消息中间件推拉支持**

|  中间件  | push模型 |   pull模型   |
| :------: | :------: | :----------: |
| RabbitMQ |   支持   |     支持     |
|  Kafka   |   ---    | 支持（只有） |
| RocketMQ |   支持   |     支持     |



### 【消息主动推送怎么实现】

​	可以结合websocket或者stom协议实现消息的主动推送



### 【消息如何保证一定被消费，如何没有消费到怎么办】

以RabbitMQ为例，可以使用消息持久化+ACK（消息确认机制）

**流程：**

1. 订单服务生产者再投递消息之前，先把消息持久化到Redis或DB中，建议redis，高性能。消息的状态为发送中。
2. confirm机制监听消息是否发送成功？如ack成功消息，删除redis中此消息。
3. 如果nack不成功的消息，这个可以根据自身的业务选择是否重发此消息。也可以删除此消息，由自己的业务决定。
4. 这边加了个定时任务，来拉取隔一定时间了，消息状态还是为发送中的，这个状态就表明，订单服务是没有收到ack成功消息。
5. 定时任务会作补偿性的投递消息。这个时候如果MQ回调ack成功接收了，再把redis中此消息删除



**处理失败消息：死信队列**

一般生产环境中，如果你有丰富的架构设计经验，都会在使用MQ的时候设计两个队列：一个是**核心业务队列**，一个是**死信队列**。

- 核心业务队列就是用来业务逻辑的。
- 死信队列就是用来处理异常情况的。

比如说要是第三方物流系统故障了，此时无法请求，那么仓储系统每次消费到一条订单消息，尝试通知发货和配送，都会遇到对方的接口报错。此时仓储系统就可以把这条消息拒绝访问，或者标志位处理失败！注意，这个步骤很重要。一旦标志这条消息处理失败了之后，MQ就会把这条消息转入提前设置好的一个死信队列中。然后你会看到的就是，在第三方物流系统故障期间，所有订单消息全部处理失败，全部会转入死信队列。然后你的仓储系统得专门有一个后台线程，监控第三方物流系统是否正常，能否请求的，不停的监视。一旦发现对方恢复正常，这个后台线程就从死信队列消费出来处理失败的订单，重新执行发货和配送的通知逻辑。

死信队列的使用，其实就是MQ在生产实践中非常重要的一环，也就是架构设计必须要考虑的。



### 【如何保证消息不被重复消费（消息幂等）】

> 幂等性是指一个请求，不管重复来多少次，结果是不会改变的。

消息被重复消费可能造成数据库插入多条相同的记录等坏情况，解决方法得结合实际业务情况来看：

- 比如你拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update 一下好吧。
- 比如你是写 Redis，那没问题了，反正每次都是 set，天然幂等性。
- 比如你不是上面两个场景，那做的稍微复杂一点，你需要让生产者发送每条数据的时候，里面加一个全局唯一的 id，类似订单 id 之类的东西，然后你这里消费到了之后，先根据这个 id 去比如 Redis 里查一下，之前消费过吗？如果没有消费过，你就处理，然后这个 id 写 Redis。如果消费过了，那你就别处理了，保证别重复处理相同的消息即可。
- 比如基于数据库的唯一键来保证重复数据不会重复插入多条。因为有唯一键约束了，重复数据插入只会报错，不会导致数据库中出现脏数据。



### 【MQ的可靠性怎么保证（处理消息丢失）】 

以RabbitMQ为例：

**生产者防止数据丢失**

生产者将数据发送到 RabbitMQ 的时候，可能数据就在半路给搞丢了，因为网络问题啥的，都有可能。

- **解决方案一：**

  用 RabbitMQ 提供的事务功能，就是生产者**发送数据之前**开启 RabbitMQ 事务`channel.txSelect`，然后发送消息，如果消息没有成功被 RabbitMQ 接收到，那么生产者会收到异常报错，此时就可以回滚事务`channel.txRollback`，然后重试发送消息；如果收到了消息，那么可以提交事务`channel.txCommit`。

  ```java
  // 开启事务
  channel.txSelect
  try {
      // 这里发送消息
  } catch (Exception e) {
      channel.txRollback
  
      // 这里再次重发这条消息
  }
  
  // 提交事务
  channel.txCommit
  ```

- **解决方案二：**

  但是问题是，RabbitMQ 事务机制（同步）一搞，基本上**吞吐量会下来，因为太耗性能**。

  所以一般来说，如果你要确保说写 RabbitMQ 的消息别丢，可以开启 `confirm` 模式，在生产者那里设置开启 `confirm` 模式之后，你每次写的消息都会分配一个唯一的 id，然后如果写入了 RabbitMQ 中，RabbitMQ 会给你回传一个 `ack` 消息，告诉你说这个消息 ok 了。如果 RabbitMQ 没能处理这个消息，会回调你的一个 `nack` 接口，告诉你这个消息接收失败，你可以重试。而且你可以结合这个机制自己在内存里维护每个消息 id 的状态，如果超过一定时间还没接收到这个消息的回调，那么你可以重发。

  事务机制和 `confirm` 机制最大的不同在于，**事务机制是同步的**，你提交一个事务之后会**阻塞**在那儿，但是 `confirm` 机制是**异步**的，你发送个消息之后就可以发送下一个消息，然后那个消息 RabbitMQ 接收了之后会异步回调你的一个接口通知你这个消息接收到了。

  所以一般在生产者这块**避免数据丢失**，都是用 `confirm` 机制的。

**消息队列防止数据丢失**

​	 RabbitMQ 自己弄丢了数据，这个你必须**开启 RabbitMQ 的持久化**，就是消息写入之后会持久化到磁盘，哪怕是 RabbitMQ 自己挂了，**恢复之后会自动读取之前存储的数据**，一般数据不会丢。

**消费端防止数据丢失**

​	消费者消费的时候，**刚消费到，还没处理，结果进程挂了**，比如重启了，那么就尴尬了，RabbitMQ 认为你都消费了，这数据就丢了。

​	这个时候得用 RabbitMQ 提供的 `ack` 机制，简单来说，就是你必须关闭 RabbitMQ 的自动 `ack`，可以通过一个 api 来调用就行，然后每次你自己代码里确保处理完的时候，再在程序里 `ack` 一把。这样的话，如果你还没处理完，不就没有 `ack` 了？那 RabbitMQ 就认为你还没处理完，这个时候 RabbitMQ 会把这个消费分配给别的 consumer 去处理，消息是不会丢的。



### 【消息队列消费积压如何解决】  

**问题由来：**

消费端出了问题，不消费了；或者消费的速度极其慢，这回会导致消息积压

一般这个时候，只能临时紧急扩容了，具体操作步骤和思路如下：

- 先修复 consumer 的问题，确保其恢复消费速度，然后将现有 consumer 都停掉。
- 新建一个 topic，partition 是原来的 10 倍，临时建立好原先 10 倍的 queue 数量。
- 然后写一个临时的分发数据的 consumer 程序，这个程序部署上去消费积压的数据，**消费之后不做耗时的处理**，直接均匀轮询写入临时建立好的 10 倍数量的 queue。
- 接着临时征用 10 倍的机器来部署 consumer，每一批 consumer 消费一个临时 queue 的数据。这种做法相当于是临时将 queue 资源和 consumer 资源扩大 10 倍，以正常的 10 倍速度来消费数据。
- 等快速消费完积压数据之后，**得恢复原先部署的架构**，**重新**用原先的 consumer 机器来消费消息。



### 【MQ对比】

|           特性           |               ActiveMQ                |                      RabbitMQ                      |                           RocketMQ                           |                            Kafka                             |
| :----------------------: | :-----------------------------------: | :------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|        单机吞吐量        | 万级，比 RocketMQ、Kafka 低一个数量级 |                    同 ActiveMQ                     |                     10 万级，支撑高吞吐                      | 10 万级，高吞吐，一般配合大数据类的系统来进行实时数据计算、日志采集等场景 |
| topic 数量对吞吐量的影响 |                                       |                                                    | topic 可以达到几百/几千的级别，吞吐量会有较小幅度的下降，这是 RocketMQ 的一大优势，在同等机器下，可以支撑大量的 topic | topic 从几十到几百个时候，吞吐量会大幅度下降，在同等机器下，Kafka 尽量保证 topic 数量不要过多，如果要支撑大规模的 topic，需要增加更多的机器资源 |
|          时效性          |                 ms 级                 |     微秒级，这是 RabbitMQ 的一大特点，延迟最低     |                            ms 级                             |                       延迟在 ms 级以内                       |
|          可用性          |      高，基于主从架构实现高可用       |                    同 ActiveMQ                     |                      非常高，分布式架构                      | 非常高，分布式，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用 |
|        消息可靠性        |         有较低的概率丢失数据          |                      基本不丢                      |              经过参数优化配置，可以做到 0 丢失               |                         同 RocketMQ                          |
|         功能支持         |         MQ 领域的功能极其完备         | 基于 erlang 开发，并发能力很强，性能极好，延时很低 |           MQ 功能较为完善，还是分布式的，扩展性好            | 功能较为简单，主要支持简单的 MQ 功能，在大数据领域的实时计算以及日志采集被大规模使用 |



## Kafka

### 【Kafka最早是为了解决什么问题设计的】

### 【Kafka有哪些组件】 

1. **producer**

   消息的生产者, 自己决定哪个 partions 中生产消息, 两种机制:hash 与 轮询    

2. **consumer**

   通过 zookeeper 进行维护消费者偏移量, consumer有自己的消费组,不同组之间维护同一个 topic 数据,互不影响.相同组的不同 consumer消费同一个 topic,这个 topic相同的数据只被消费一次   

3. **broker**

   broker 组成 kafka 集群的节点,之间没有主从关系, 依赖 zookeeper进行协调, broker 负责消息的读写与存储, 一个 broker可以管理读个 partions    

4. **topic**

   一类消息的总称/消息队里, topic是由 partions组成, 一个 topic 由多台 server 里的 partions 组成 

5. **zookeeper** 

   协调 kafka broker存储元数据, consumer的 offset+ broker 信息 +topic信息+ partions信息   

6. **partions**

   组成 topic 的单元, 每个 topic有副本(创建 topic 指定), 每个 partions 只能有有个 broker管理



### 【Kafka两种消费模式】



### 【Kafka的作用和架构】 

### 【为什么Kafka用Zookeeper来存储Metadata，而不是用db来存储】  

### 【Kafka怎么做有序消费】

### 【Kafka局部有序】  

### 【Kafka中patiton如何保证有序】

### 【Kafka的replicas的作用，为什么比其他的消息队列好】  

### 【Kafka只有一次生产 只有一次消费怎么做】 

### 【Kafka如何保证不丢消息又不会重复消费】 

### 【Kafka一致性】 

### 【Kafka如何保证可靠，高可用，幂等】

### 【假设公司有上万人，一则通知要快速的推送到每个人，如何通过Kafka来实现】  



## RabbitMQ

### 【RabbitMQ介绍】   

**简介：**

> **RabbitMQ**是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用[Erlang](https://baike.baidu.com/item/Erlang)语言编写的，而集群和故障转移是构建在[开放电信平台](https://baike.baidu.com/item/开放电信平台)框架上的。所有主要的编程语言均有与代理接口通讯的客户端[库](https://baike.baidu.com/item/库)。                                                                                    ——[百度百科](https://baike.baidu.com/item/rabbitmq/9372144?fr=aladdin)

**核心概念：**

- **ConnectionFactory**

  Connection是RabbitMQ的socket链接，它封装了socket协议相关部分逻辑。

- **Connection**

  ConnectionFactory为Connection的制造工厂。

- **Channel**

  Channel是我们与RabbitMQ打交道的最重要的一个接口，我们大部分的业务操作是在Channel这个接口中完成的，包括定义Queue、定义Exchange、绑定Queue与Exchange、发布消息等。

- **Queue**

  Queue（队列）是RabbitMQ的内部对象；用于存储消息，生产者生产消息并**最终**投递到Queue中，消费者可以从Queue中获取消息并消费；多个消费者可以订阅同一个Queue，这时Queue中的消息会被平均分摊给多个消费者进行处理，而不是每个消费者都收到所有的消息并处理。

- **Exchange**

  exchange交换机，生产者将消息发送到Exchange，由Exchange将消息路由到一个或多个Queue中。

  RabbitMQ常用的Exchange Type有**fanout、direct、topic、headers**这四种

- **Routing key**

  生产者在将消息发送给Exchange的时候，一般会指定一个routing key，来指定这个消息的路由规则，而这个routing key需要与Exchange Type及binding key联合使用才能最终生效。

  在Exchange Type与binding key固定的情况下（在正常使用时一般这些内容都是固定配置好的），我们的生产者就可以在发送消息给Exchange时，通过指定routing key来决定消息流向哪里。

  RabbitMQ为routing key设定的长度限制为**255** bytes。

- **Binding**

  RabbitMQ中通过Binding将Exchange与Queue关联起来，这样RabbitMQ就知道如何正确地将消息路由到指定的Queue了。



### 【RabbitMQ消息队列如何解决消息丢失(可靠性)】

- **生产者生产消息到RabbitMQ Server 可靠性保证**
- **RabbitMQ Server中存储的消息如何保证**
- **RabbitMQ Server到消费者消息如何不丢**  

这题可以参照上面【MQ的可靠性怎么保证（处理消息丢失）】 ，其就是以RabbitMQ为例



### 【RabbitMQ能避免发送重复数据吗】

​	ack机制能保证消息发送的可靠性，但是无法保证发送数据的内容是否重复



## RocketMQ

### 【RocketMQ介绍】

**简介：**

> RocketMQ作为一款纯java、分布式、队列模型的开源消息中间件，支持事务消息、顺序消息、批量消息、定时消息、消息回溯等。

**特点：**

- 支持发布/订阅（Pub/Sub）和点对点（P2P）消息模型
- 在一个队列中可靠的先进先出（FIFO）和严格的顺序传递 （RocketMQ可以保证严格的消息顺序，而ActiveMQ无法保证）
- 支持拉（pull）和推（push）两种消息模式 （Push好理解，比如在消费者端设置Listener回调；而Pull，控制权在于应用，即应用需要主动的调用拉消息方法从Broker获取消息，这里面存在一个消费位置记录的问题（如果不记录，会导致消息重复消费））
- 单一队列百万消息的堆积能力       （RocketMQ提供亿级消息的堆积能力，这不是重点，重点是堆积了亿级的消息后，依然保持写入低延迟）
- 支持多种消息协议，如 JMS、MQTT 等
- 分布式高可用的部署架构,满足至少一次消息传递语义    （RocketMQ原生就是支持分布式的，而ActiveMQ原生存在单点性）
- 提供 docker 镜像用于隔离测试和云集群部署
- 提供配置、指标和监控等功能丰富的 Dashboard

**核心概念：**

- **Producer**

  消息生产者，生产者的作用就是将消息发送到 MQ，生产者本身既可以产生消息，如读取文本信息等。也可以对外提供接口，由外部应用来调用接口，再由生产者将收到的消息发送到 MQ。

- **Producer Group**

  生产者组，简单来说就是多个发送同一类消息的生产者称之为一个生产者组。在这里可以不用关心，只要知道有这么一个概念即可。

- **Consumer**

  消息消费者，简单来说，消费 MQ 上的消息的应用程序就是消费者，至于消息是否进行逻辑处理，还是直接存储到数据库等取决于业务需要。

- **Consumer Group**

  消费者组，和生产者类似，消费同一类消息的多个 consumer 实例组成一个消费者组。

- **Topic**

  Topic 是一种消息的逻辑分类，比如说你有订单类的消息，也有库存类的消息，那么就需要进行分类，一个是订单 Topic 存放订单相关的消息，一个是库存 Topic 存储库存相关的消息。

- **Message**

  Message 是消息的载体。一个 Message 必须指定 topic，相当于寄信的地址。Message 还有一个可选的 tag 设置，以便消费端可以基于 tag 进行过滤消息。也可以添加额外的键值对，例如你需要一个业务 key 来查找 broker 上的消息，方便在开发过程中诊断问题。

- **Tag**

  标签可以被认为是对 Topic 进一步细化。一般在相同业务模块中通过引入标签来标记不同用途的消息。

- **Broker**

  Broker 是 RocketMQ 系统的主要角色，其实就是前面一直说的 MQ。Broker 接收来自生产者的消息，储存以及为消费者拉取消息的请求做好准备。

- **Name Server**

  Name Server 为 producer 和 consumer 提供路由信息。



### 【RocketMQ的commitLog的作用】

对于`RoceketMQ`而言，所有的消息最终都需要被持久化到`CommitLog`文件中。

- **存储地址**

  $HOME\store\commitlog\${fileName}；

- **文件大小**

  默认1G =1024×1024×1024

- **文件名**

  名字长度为20位，左边补零，剩余为起始偏移量；

  比如00000000000000000000代表了第一个文件，起始偏移量为0，文件大小为1G=1073741824；

  当这个文件满了，第二个文件名字为00000000001073741824，起始偏移量为1073741824，

  以此类推，第三个文件名字为00000000002147483648，起始偏移量为2147483648。

**消息存储的时候会顺序写入文件，当文件满了，写入下一个文件。**

CommitLog源码：

~~~java
// commitlog构造器
public CommitLog(final DefaultMessageStore defaultMessageStore) {
    this.mappedFileQueue = new MappedFileQueue(defaultMessageStore.getMessageStoreConfig().getStorePathCommitLog(),
        defaultMessageStore.getMessageStoreConfig().getMapedFileSizeCommitLog(), defaultMessageStore.getAllocateMappedFileService());// 创建mapperFileQueue
    this.defaultMessageStore = defaultMessageStore;
    // 刷盘对象线程
    if (FlushDiskType.SYNC_FLUSH == defaultMessageStore.getMessageStoreConfig().getFlushDiskType()) {
        this.flushCommitLogService = new GroupCommitService();
    } else {
        this.flushCommitLogService = new FlushRealTimeService();
    }
    // 提交
    this.commitLogService = new CommitRealTimeService();
    // append消息回调（描述的是将消息在文件末尾不断的append上去）
    this.appendMessageCallback = new DefaultAppendMessageCallback(defaultMessageStore.getMessageStoreConfig().getMaxMessageSize());
    batchEncoderThreadLocal = new ThreadLocal<MessageExtBatchEncoder>() {
        @Override
        protected MessageExtBatchEncoder initialValue() {
            return new MessageExtBatchEncoder(defaultMessageStore.getMessageStoreConfig().getMaxMessageSize());
        }
    };
    // 消息写入锁
    this.putMessageLock = defaultMessageStore.getMessageStoreConfig().isUseReentrantLockWhenPutMessage() ? new PutMessageReentrantLock() : new PutMessageSpinLock();
}
~~~



### 【为什么commitLog每个文件的大小是1G】   



### 【NameServer的作用】 

- **NameServer用来保存活跃的broker列表，包括Master和Slave**
- **NameServer用来保存所有topic和该topic所有队列的列表**
- **NameServer用来保存所有broker的Filter列表**



### 【NameServer所有的节点数据是一致的吗】
---
title: Start
permalink: /interview/microservicesAndDistribution/start
key: microservicesAndDistribution-start
---

## 微服务

### 【微服务概念】 

**概念：**

> 微服务是一种以业务功能为主的服务设计概念，每一个服务都具有自主运行的业务功能，对外开放不受语言限制的 API (最常用的是 HTTP)，应用程序则是由一个或多个微服务组成。

**总结：**

1. 服务种类根据业务划分
2. 每个服务可独立部署运维且相互隔离
3. 服务之间通过 API 调用服务
4. 服务需保证良好的高可用性
5. 分散式管理以及分散式数据。每个微服务团队有充分自由选择自己团队熟悉的编程语言、数据库和其他中间件等技术栈。



### 【说几种你说熟悉的微服务架构】

[原文](https://blog.csdn.net/xzh121121/article/details/103733361)

1. 聚合器微服务设计模式
2. 代理微服务设计模式
3. 链式微服务设计模式
4. 分支微服务设计模式
5. 数据共享微服务设计模式
6. 异步消息传递微服务设计模式



### 【聊一聊SOA和微服务】

- **SOA:**

  服务导向式架构（SOA）是集成多个较大组件（一般是应用）的一种机制，它们将整体构成一个彼此协作的套件

- **区别：**

  | 功能     | SOA                  | 微服务                       |
  | -------- | -------------------- | ---------------------------- |
  | 组件大小 | 大块业务逻辑         | 单独任务或小块业务逻辑       |
  | 耦合     | 通常松耦合           | 总是松耦合                   |
  | 公司架构 | 任何类型             | 小型、专注于功能交叉的团队   |
  | 管理     | 着重中央管理         | 着重分散管理                 |
  | 目标     | 确保应用能够交互操作 | 执行新功能，快速拓展开发团队 |

- **关系：**

  微服务是SOA发展出来的产物，它是一种比较现代化的细粒度的SOA实现方式。



## 分布式

### 【集群和分布式的区别】

1. **集群**：同一个业务，部署在多个服务器上(不同的服务器运行同样的代码，干同一件事)
2. **分布式**：一个业务分拆多个子业务，部署在不同的服务器上(不同的服务器，运行不同的代码，为了同一个目的)



### 【讲一讲对分布式系统模型的理解】

**分布式系统**：多个能独立运行的计算机（称为结点）组成。各个结点利用计算机网络进行信息传递，从而实现共同的“目标或者任务”。



### 【分布式一致性】

数据的一致性模型可以分成以下 3 类：

- 强一致性：数据更新成功后，任意时刻所有副本中的数据都是一致的，一般采用同步的方式实现。
- 弱一致性：数据更新成功后，系统不承诺立即可以读到最新写入的值，也不承诺具体多久之后可以读到。
- 最终一致性：弱一致性的一种形式，数据更新成功后，系统不承诺立即可以返回最新写入的值，但是保证最终会返回上一次更新操作的值。



### 【CAP理论】

**概念：**

> 一个分布式系统不可能同时**满足一致性**、**可用性**和**分区容错性**这三个基本需求，最多只能同时满足其中两项。

-  C：数据一致性(consistency) ， 所有节点拥有数据的最新版本。其中，一致性又分为强一致性、弱一致性、最终一致性。
-  A：可用性(availability) ，数据具备高可用性
-  P：分区容错性(partition-tolerance)，容忍网络出现分区，分区之间网络不可达。



### 【了解过分布式或者微服务的开源框架吗】 

1. 基于spring boot, spring cloud和netflix等开源技术搭建微服务架构
2. 基于dubbo+zookeeper的微服务架构



### 【分布式系统如何实现负载均衡】

- 第 1 层：客户端层 -> 反向代理层 的负载均衡。通过 DNS 轮询
- 第 2 层：反向代理层 -> Web 层 的负载均衡。通过 Nginx 的负载均衡模块
- 第 3 层：Web 层 -> 业务服务层 的负载均衡。通过服务治理框架的负载均衡模块
- 第 4 层：业务服务层 -> 数据存储层 的负载均衡。通过数据的水平分布，数据均匀了，理论上请求也会均匀。比如通过买家ID分片类似



### 【分布式系统中有一个节点宕机怎么办】

考虑如何通过读取持久化的介质，如机械键盘，固态硬盘等来恢复内存信息，使其恢复到宕机前某个一致的状态。



### 【分布式任务调度怎么做】

只了解两个：

- **XXL-JOB**

  XXL-JOB 是一个轻量级分布式任务调度框架，支持通过 Web 页面对任务进行 CRUD 操作，支持动态修改任务状态、暂停/恢复任务，以及终止运行中任务，支持在线配置调度任务入参和在线查看调度结果。

- **Elastic-Job**

  Elastic-Job 是一个分布式调度解决方案，由两个相互独立的子项目 Elastic-Job-Lite 和 Elastic-Job-Cloud 组成。定位为轻量级无中心化解决方案，使用 jar 包的形式提供分布式任务的协调服务。支持分布式调度协调、弹性扩容缩容、失效转移、错过执行作业重触发、并行调度、自诊断和修复等等功能特性。



### 【分布式事务】 

**事务**：事务是由一组操作构成的可靠的独立的工作单元，事务具备ACID的特性，即原子性、一致性、隔离性和持久性。

在分布式系统中，分布式事务[解决方案](https://www.cnblogs.com/mayundalao/p/11798502.html)有如下几种：

1. **两阶段提交（2PC）**

   两阶段提交（Two-phase Commit，2PC），通过引入协调者（Coordinator）来协调参与者的行为，并最终决定这些参与者是否要真正执行事务。

   **存在问题：**

   - 同步阻塞 所有事务参与者在等待其它参与者响应的时候都处于同步阻塞状态，无法进行其它操作。

   - 单点问题 协调者在 2PC 中起到非常大的作用，发生故障将会造成很大影响。特别是在阶段二发生故障，所有参与者会一直等待状态，无法完成其它操作。

   - 数据不一致 在阶段二，如果协调者只发送了部分 Commit 消息，此时网络发生异常，那么只有部分参与者接收到 Commit 消息，也就是说只有部分参与者提交了事务，使得系统数据不一致。

   - 太过保守 任意一个节点失败就会导致整个事务失败，没有完善的容错机制。

   

2. **补偿事务（TCC）**

   CC 其实就是采用的补偿机制，其核心思想是：针对每个操作，都要注册一个与其对应的确认和补偿（撤销）操作。它分为三个阶段：

   - Try 阶段主要是对业务系统做检测及资源预留
   - Confirm 阶段主要是对业务系统做确认提交，Try阶段执行成功并开始执行 Confirm阶段时，默认 Confirm阶段是不会出错的。即：只要Try成功，Confirm一定成功。
   - Cancel 阶段主要是在业务执行错误，需要回滚的状态下执行的业务取消，预留资源释放。

   **优点**： 跟2PC比起来，实现以及流程相对简单了一些，但数据的一致性比2PC也要差一些

   **缺点**： 缺点还是比较明显的，在2,3步中都有可能失败。TCC属于应用层的一种补偿方式，所以需要程序员在实现的时候多写很多补偿的代码，在一些场景中，一些业务流程可能用TCC不太好定义及处理。

   

3. **本地消息表（异步确保）**

   本地消息表与业务数据表处于同一个数据库中，这样就能利用本地事务来保证在对这两个表的操作满足事务特性，并且使用了消息队列来保证最终一致性。

   - 在分布式事务操作的一方完成写业务数据的操作之后向本地消息表发送一个消息，本地事务能保证这个消息一定会被写入本地消息表中。
   - 之后将本地消息表中的消息转发到 Kafka 等消息队列中，如果转发成功则将消息从本地消息表中删除，否则继续重新转发。
   - 在分布式事务操作的另一方从消息队列中读取一个消息，并执行消息中的操作。

   **优点**： 一种非常经典的实现，避免了分布式事务，实现了最终一致性。

   **缺点**： 消息表会耦合到业务系统中，如果没有封装好的解决方案，会有很多杂活需要处理。

   

4. **MQ 事务消息**

   有一些第三方的MQ是支持事务消息的，比如RocketMQ，他们支持事务消息的方式也是类似于采用的二阶段提交

   **优点**： 实现了最终一致性，不需要依赖本地数据库事务。

   **缺点**： 实现难度大，主流MQ不支持，RocketMQ事务消息部分代码也未开源。



### 【分布式链路追踪】

**概念：**

> 分布式链路追踪（Distributed Tracing），也叫分布式链路跟踪，分布式跟踪，分布式追踪；
>
> 分布式链路追踪就是将一次分布式请求还原成调用链路，将一次分布式请求的调用情况集中展示，比如各个服务节点上的耗时、请求具体到达哪台机器上、每个服务节点的请求状态等等。

**Dapper**

​	目前业界的链路追踪系统，如Twitter的**Zipkin**，Uber的**Jaeger**，阿里的**鹰眼**，美团的**Mtrace**等都基本被启发于google发表的**Dapper**。

参考：https://www.cnblogs.com/lidawn/p/10230903.html



### 【服务发现的链路】

### 【分布式追踪的上下文是怎么存储和传递的】

​	ThreadLocal + spanId，当前节点的spanId作为下个节点的父spanId



### 【链路追踪的信息是怎么传递的】 

  RpcContext的attachment，说了Span的结构:parentSpanId + curSpanId



### 【ELK(日志分析系统)】

​	ELK即Elastic Stack，是三种技术的名称首字母(ElasticSearch + Logstash + Kibana),具体可以看

​	[《ELK部署》](/2020/03/05/ElasticStack环境部署.html)



### 【Docker讲解】

> Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从 Apache2.0 协议开源。
>
> Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。
>
> 容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

常见的命令:

~~~shell
#查找
docker serach xxx
#拉取镜像
docker pull  xxx
#查看已下载镜像
docker iamges
#查看运行中的容器
docker ps 
#查看所有的容器
docker ps -a
#运行镜像   -d后台运行 -v映射 -e参数 -p端口映射
docker run -d  -v -e -p
#停止容器
docker stop
#进入容器内部
docker exec -it xx /bin/bash
~~~



### 【Docker的NameSpace和Cgroups】 

**总结**：Docker通过namespace实现了资源的隔离，通过cgroups实现了资源限制

**NameSpace**

- kernel提供了namespace的机制用来隔离相关资源。namespace设计之初就是为了实现轻量级的系统资源隔离。
- 可以让容器中的进程仿佛置身于一个独立的系统环境中。

| namespace |    系统调用参数 |          隔离内容          |
| --------- | --------------: | :------------------------: |
| UTC       |  `CLONE_NEWUTS` |        主机名和域名        |
| IPC       |  `CLONE_NEWIPC` | 信号量、消息队列和共享内存 |
| PID       |  `CLONE_NEWPID` |          进程编号          |
| Network   |  `CLONE_NEWNET` |  网络设备、网络栈、端口等  |
| Mount     |   `CLONE_NEWNS` |          文件系统          |
| User      | `CLONE_NEWUSER` |        用户和用户组        |

**Cgroups**

​	从字面上理解，Cgroups就是把任务放到一个组里面统一加以控制。怎么控制呢？就是限制、记录任务组所使用的物理资源，包括CPU、Memory、IO等。

​	 本质上来说，Cgroups是内核附加在程序上的一系列hook，通过程序运行时对资源的调度触发相应的钩子以达到资源跟踪和限制的目的。

​	 在Cgroups里，任务(task)就是系统的一个进程或者线程。

​	**四大作用**：

- 资源限制： 比如设定任务内存使用的上限。
- 优先级分配： 比如给任务分配CPU的时间片数量和磁盘IO的带宽大小来控制任务运行的优先级。
- 资源统计：比如统计CPU的使用时长、内存用量等。这个功能非常适用于计费。
- 任务控制：cgroups可以对任务执行挂起、恢复等操作。




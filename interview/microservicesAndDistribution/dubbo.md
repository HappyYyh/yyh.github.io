---
title: Dubbo
permalink: /interview/microservicesAndDistribution/dubbo
key: microservicesAndDistribution-dubbo
---

### 【Dubbo的概念、作用】

**概念：**

Dubbo是阿里旗下的一个弹性的分布式服务框架，致力于提供高性能和透明化的RPC远程服务调用方案，以及SOA服务治理方案。

**作用：**

- 透明化的远程方法调用
- 服务自动注册与发现
- 软负载均衡及容错机制

Dubbo文档：http://dubbo.apache.org/zh-cn/index.html



### 【HTTP 和 RPC协议的区别】

**RPC概念：**

> RPC是一种远程过程调用的协议，使用这种协议向另一台计算机上的程序请求服务，不需要了解底层网络技术的协议。

**区别：**

1. RPC是一种API，HTTP是一种无状态的网络协议。RPC可以基于HTTP协议实现，也可以直接在TCP协议上实现。
2. HTTP开发方便简单、直接。开发一个完善的RPC框架难度比较大。
3. HTTP发明的初衷是为了传送超文本的资源，协议设计的比较复杂，参数传递的方式效率也不高。开源的RPC框架针对远程调用协议上的效率会比HTTP快很多。
4. HTTP需要事先通知，修改Nginx/HAProxy配置。RPC能做到自动通知，不影响上游。
5. HTTP大部分是通过Json来实现的，字节大小和序列化耗时都比Thrift要更消耗性能。RPC，可以基于Thrift实现高效的二进制传输。



### 【Dubbo的整个调用过程】 

1. 服务容器Container 负责启动加载运行服务提供者Provider。根据Provider配置的文件根据协议发布服务 , 完成服务的初始化.
2. Provider在启动时，根据配置中的Registry地址连接Registry，将Provider的服务信息发布到Registry，在Registry注册自己提供的服务。
3. Consumer在启动时，根据消费者XML配置文件中的服务引用信息，连接到Registry，向Registry订阅自己所需的服务。
4. Registry根据服务订阅关系，返回Provider地址列表给Consumer，如果有变更，Registry会推送最新的服务地址信息给Consumer。
5. Consumer调用远程服务时，会根据路由策略，先从缓存的Provider地址列表中选择一台进行，跨进程调用服务，假如调用失败，再重新选另一台调用。
6. 服务Provider和Consumer，会在内存中记录调用次数和调用时间，每分钟发送一次统计数据到Monitor。



### 【Dubbo的远程调用怎么实现的】

代理调用、容错负载和远程通信。



### 【Dubbo的原理】

- **初始化过程细节**：


  第一步，就是将服务装载到容器中，然后准备注册服务。和Spring中启动过程类似，Spring启动时，将bean装载进容器中的时候，首先要解析bean。所以dubbo也是先读配置文件解析服务。

- **解析服务**：

  1. 基于dubbo.jar内的META-INF/spring.handlers配置，spring在遇到dubbo名称空间时，会回调DubboNamespaceHandler类。

  2. 所有的dubbo标签都统一用DubboBeanDefinitionParser进行解析，基于一对一属性映射，将XML标签解析为Bean对象，生产者或者消费者初始化的时候，会将Bean对象转换为URL格式，将所有Bean属性转换成URL的参数。然后将URL传给Protocal扩展点，基于扩展点的Adaptive机制，根据URL的协议头，进行不同协议的服务暴露和引用。

- **暴露服务**：

  1. 直接暴露服务端口：在没有注册中心的情况下，配置ServiceConfig解析出的URL，基于扩展点Adaptive机制，通过URL的协议头识别，直接调用DubboProtocol的export（）方法，打开服务端口

  2. 向注册中心暴露服务：将服务的IP和端口一同暴露给注册中心。ServiceConfig解析出的url格式，基于扩展点的Adaptive机制，通过URL的协议头识别，调用RegistryProtocol的export（）方法，将export参数中的提供者URL先注册到注册中心，再重新传给Protocol扩展点进行暴露。

- **引用服务**：

  1. 直接引用服务：在没有注册中心的情况下，直连提供者，ReferenceConfig解析出URL格式，基于扩展点的Adaptive机制，通过URL协议头识别，直接调用DubboProtocol的refer方法，返回提供者引用。

  2. 从注册中心发现引用服务：ReferenceConfig解析出的URL的格式，基于扩展点的Adaptive机制，通过URL协议头识别，就会调用RegistryProtocol的refer方法，从refer参数总的条件，查询提供者URL，通过提供者URL协议头识别，就会调用DubboProtocol的refer（）方法，得到提供者引用。然后RegistryProtocol将多个提供者引用通过Cluster扩展点，伪装成单个提供者引用返回。



### 【Dubbo的RpcContext是怎么传递的？是在什么维度传递的】 

- **如何传递：**

  RpcContext是一个ThreadLocal的临时状态记录器，当接收到RPC请求，或发起RPC请求时，RpcContext的状态都会变化。

  ~~~java
  //提供者获取参数
  RpcContext.getContext().getAttachments();
  //消费者传递参数
  RpcContext.getContext().setAttachment();
  ~~~

- **维度：**

  线程



### 【Dubbo服务导出，服务引入】

- **服务导出**

  http://dubbo.apache.org/zh-cn/docs/source_code_guide/export-service.html

- **服务引入**

   http://dubbo.apache.org/zh-cn/docs/source_code_guide/refer-service.html



### 【RPC原理】

**RPC角色**

在RPC框架中主要有三个角色：Provider、Consumer和Registry。



**调用过程**

一次完整的RPC调用流程（同步调用，异步另说）如下：

1. 服务消费方（client）调用以本地调用方式调用服务；
2. client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体；
3. client stub找到服务地址，并将消息发送到服务端；
4. server stub收到消息后进行解码；
5. server stub根据解码结果调用本地的服务；
6. 本地服务执行并将结果返回给server stub；
7. server stub将返回结果打包成消息并发送至消费方；
8. client stub接收到消息，并进行解码；
9. 服务消费方得到最终结果。

RPC框架的目标就是要2~8这些步骤都封装起来，让用户对这些细节透明。



**使用的技术**

1. **动态代理**

   生成 client stub和server stub需要用到 Java 动态代理技术 ，我们可以使用JDK原生的动态代理机制，可以使用一些开源字节码工具框架 如：CgLib、Javassist等。

2. **序列化**

   为了能在网络上传输和接收 Java对象，我们需要对它进行 序列化 和 反序列化操作。

   * 序列化：将Java对象转换成byte[]的过程，也就是编码的过程；

   * 反序列化：将byte[]转换成Java对象的过程；

   可以使用Java原生的序列化机制，但是效率非常低，推荐使用一些开源的、成熟的序列化技术，例如：protobuf、Thrift、hessian、Kryo、Msgpack

   关于序列化工具性能比较可以参考：jvm-serializers

3. **NIO**

   当前很多RPC框架都直接基于netty这一IO通信框架，比如阿里巴巴的HSF、dubbo，Hadoop Avro，推荐使用Netty 作为底层通信框架。

4. **服务注册中心**

   可选技术：

   * Redis

   * Zookeeper

   * Consul

   * Etcd
   
   * Nacos



### 【Netty原理】

[原文](https://blog.csdn.net/tugangkai/article/details/80560495)

- **Netty简介**

  Netty是一个高性能、异步事件驱动的NIO框架，基于JAVA NIO提供的API实现。它提供了对TCP、UDP和文件传输的支持，作为一个异步NIO框架，Netty的所有IO操作都是异步非阻塞的，通过Future-Listener机制，用户可以方便的主动获取或者通过通知机制获得IO操作结果。 作为当前最流行的NIO框架，Netty在互联网领域、大数据分布式计算领域、游戏行业、通信行业等获得了广泛的应用，一些业界著名的开源组件也基于Netty的NIO框架构建。

- **总结**

  Netty其实本质上就是Reactor模式的实现，Selector作为多路复用器，EventLoop作为转发器，Pipeline作为事件处理器。但是和一般的Reactor不同的是，Netty使用串行化实现，并在Pipeline中使用了责任链模式。

  Netty中的buffer相对有NIO中的buffer又做了一些优化，大大提高了性能。



### 【Netty如何避免NIO空循环，实现零拷贝】

**避免空循环：**

1. `selector.select(timeoutMillis)`，调用了select方法，并默认设置1秒超时时间，同时记录轮询次数：`selectCnt ++`;
2. 获取当前时间，计算select方法的操作时间是否真的阻塞了`timeoutMillis`，如果是就证明是一次正常的`select()`，重置`selectCnt = 1`;如果不是，就可能触发了JDK的空轮询BUG，然后判断`selectCnt` 轮询次数是否大于默认的512，然后进行`rebuildSelector()`。
3. `rebuildSelector()`方法重新打开一个Selector；然后遍历`oldSelector`，将所有的key重新注册到新的Selector；然后重新赋值selector，`selectCnt = 1`;这时候已经规避了空轮询。



**零拷贝**即`Zero-copy`

> 就是在操作数据时, 不需要将数据 buffer 从一个内存区域拷贝到另一个内存区域. 因为少了一次内存的拷贝, 因此 CPU 的效率就得到的提升.

Netty 的 `Zero-copy`体现在如下几个个方面:

- Netty 提供了 `CompositeByteBuf`类, 它可以将多个 ByteBuf 合并为一个逻辑上的 ByteBuf, 避免了各个 ByteBuf 之间的拷贝.
- 通过 wrap 操作, 我们可以将 byte[] 数组、ByteBuf、ByteBuffer等包装成一个 Netty ByteBuf 对象, 进而避免了拷贝操作.
- ByteBuf 支持 slice 操作, 因此可以将 ByteBuf 分解为多个共享同一个存储区域的 ByteBuf, 避免了内存的拷贝.
- 通过 `FileRegion`包装的`FileChannel.tranferTo`实现文件传输, 可以直接将文件缓冲区的数据发送到目标 `Channel`, 避免了传统通过循环 write 方式导致的内存拷贝问题.
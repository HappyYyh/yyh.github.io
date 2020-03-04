---
title: MQ
permalink: /interview/microservicesAndDistribution/mq
key: microservicesAndDistribution-mq
---

MQ对比，项目思考，反思中间件  

## 消费

你们项目中用过MQ，平时都用MQ来做什么？  
消息如何保证一定被消费，如何没有消费到怎么办?  
mq 通知时，消费者没消费到怎么办  ?  
用MQ采集消息的时候，有没有做消息重复消费处理？怎么做的？  
消息队列消费积压如何解决  
MQ的可靠性怎么保证  
消息的推拉模型   
消息幂等怎么实现？  
主动推送怎么实现?  
自增批次号是怎么生成的？   
roi从哪来。供谁使用  

## Kafka

kafka有哪些组件  
kafka两种消费模式  
kafka怎么做有序消费  
kafka的作用和架构  
kafka 最早是为了解决什么问题设计的  
kafka 网络中有哪些组件，为什么kafka 用zookeeper来存储metadata，而不是用db来存储  
Kafka局部有序  
kafka的replicas的作用，为什么比其他的消息队列好。  
kafka中patiton如何保证有序  
kafka 只有一次生产 只有一次消费怎么做  
kafka如何保证不丢消息又不会重复消费。  
kafka一致性  
kafka相对于其他的优点   
kafka 重消费解决  
kafka如何保证可靠，高可用，幂等
假设公司有上万人，一则通知要快速的推送到每个人，如何通过kafka来实现 ?  

## RabbitMQ

对rabbitmq了解哪些？   
rabbitmq消息队列如何解决消息丢失  
rabbitmq和其他消息队列的对比   
rabbitmq能避免发送重复数据吗？  
rabbitmq的可靠性如何保证 ？  

## RocketMQ

RocketMQ的commitLog的作用    
为什么commitLog每个文件的大小是1G？   



【nameServer的作用】 

【nameServer所有的节点数据是一致的吗】
---
title: Redis
permalink: /interview/microservicesAndDistribution/redis
key: microservicesAndDistribution-redis
---

谈谈你对Redis的了解？  
redis你们都用来做什么？  
一般设置过期时间吗，业务场景有哪些  
Redis事务的了解      
redis的IO模型   

## 数据结构

Redis有哪些数据结构，底层实现是怎么样的？  
hash底层，问设计一个hash底层如何设计  
redis的set的底层结构   
redis的zset的底层结构，跳跃表结构怎么做到有序性的？   
redis的setbit实现是怎么样的？   
redis存储数据，在工程中的作用   
redis跳跃表  

## 持久化

redis持久化的方式 ?  
只用AOF行不行 ?  
aof持久化怎么写入的?  

## 淘汰策略

如何自己实现lru ？  
redis缓存回收机制，准备同步，哨兵机制  
redis回收策略  
过期策略的具体过期方法  

## 分布式锁

分布式锁redis和zookeeper实现区别，使用场景  
redis分布式锁如何玩？超时时间如何设置  

##  架构模式

你知道redis为什么快吗？  
redis 是单线程了吗？有什么好处？   
怎么样保证redis的高可用？  
redis同步策略 ？   
Redis 集群的实现  ？集群一致性 ？  
生产环境Redis 如何做数据迁移  
redis主从机制了解么？怎么实现的？  
关于哨兵机制？  
Redis 怎么保证不丢数据，能不能保证严格意义的一定不会丢  
分布式缓存读写不一致问题  
redis扛不住的话有哪些解决方案？  
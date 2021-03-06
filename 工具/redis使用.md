---
title: redis使用
date: 2018-03-27 10:55:11
categories: 
- 工具
tags:
- 工具
- java
---

# redis使用

## redis哨兵模式部署

## 数据结构

Redis数据类型   底层数据结构
String         数组
List           双向链表
Hash           二维结构 第一维度:数组 第二维度:链表   (同java的HashMap数据结构)
Set            Hash
ZSet           Hash+跳跃表

- [https://blog.csdn.net/shengqianfeng/article/details/82684354](https://blog.csdn.net/shengqianfeng/article/details/82684354)



## 缓存问题

### 缓存穿透

#### 描述

业务系统要查询的数据根本就存在！当业务系统发起查询时，按照上述流程，首先会前往缓存中查询，由于缓存中不存在，然后再前往数据库中查询。由于该数据压根就不存在，因此数据库也返回空。这就是缓存穿透。业务系统访问压根就不存在的数据，就称为缓存穿透。

#### 解决方案

1. 缓存null值，防止大量对单一不存在的key查询（可临时短时间缓存，如一分钟，不需要和普通缓存相同时间），适合对相同重复key过滤
2. 缓存所有可用key，防止大量对多个不存在的key查询

#### 缓存雪崩

#### 描述
缓存其实扮演了一个保护数据库的角色。它帮数据库抵挡大量的查询请求，从而避免脆弱的数据库受到伤害。如果缓存因某种原因发生了宕机，那么原本被缓存抵挡的海量查询请求就会像疯狗一样涌向数据库。此时数据库如果抵挡不了这巨大的压力，它就会崩溃。这就是缓存雪崩。

#### 解决方案

1. 使用缓存集群，保证缓存高可用
2. 使用Hystrix熔断器

#### 热点数据集中失效

#### 描述

我们一般都会给缓存设定一个失效时间，过了失效时间后，该数据库会被缓存直接删除，从而一定程度上保证数据的实时性。但是，对于一些请求量极高的热点数据而言，一旦过了有效时间，此刻将会有大量请求落在数据库上，从而可能会导致数据库崩溃。如果某一个热点数据失效，那么当再次有该数据的查询请求[req-1]时就会前往数据库查询。但是，从请求发往数据库，到该数据更新到缓存中的这段时间中，由于缓存中仍然没有该数据，因此这段时间内到达的查询请求都会落到数据库上，这将会对数据库造成巨大的压力。此外，当这些请求查询完成后，都会重复更新缓存。

#### 解决方案

1. 互斥锁
我们可以使用缓存自带的锁机制，当第一个数据库查询请求发起后，就将缓存中该数据上锁；此时到达缓存的其他查询请求将无法查询该字段，从而被阻塞等待；当第一个请求完成数据库查询，并将数据更新值缓存后，释放锁；此时其他被阻塞的查询请求将可以直接从缓存中查到该数据。当某一个热点数据失效后，只有第一个数据库查询请求发往数据库，其余所有的查询请求均被阻塞，从而保护了数据库。但是，由于采用了互斥锁，其他请求将会阻塞等待，此时系统的吞吐量将会下降。这需要结合实际的业务考虑是否允许这么做。互斥锁可以避免某一个热点数据失效导致数据库崩溃的问题，而在实际业务中，往往会存在一批热点数据同时失效的场景。那么，对于这种场景该如何防止数据库过载呢？

2. 设置不同的失效时间
当我们向缓存中存储这些数据的时候，可以将他们的缓存失效时间错开。这样能够避免同时失效。如：在一个基础时间上加/减一个随机数，从而将这些缓存的失效时间错开。

#### 问题

- redistemple获取存在的key，得到的value为null？ 答：redisTemplate设置setEnableTransactionSupport(true)了，当前线程会强制先 MULTI命令。Redis Multi 命令 Redis 事务 Redis Multi 命令用于标记一个事务块的开始。事务块内的多条命令会按照先后顺序被放进一个队列当中，最后由 EXEC 命令原子性(atomic)地执行。在一个事务中，设置的值外部不可见。[https://www.cnblogs.com/softidea/p/5720938.html](https://www.cnblogs.com/softidea/p/5720938.html)


## 缓存击穿问题



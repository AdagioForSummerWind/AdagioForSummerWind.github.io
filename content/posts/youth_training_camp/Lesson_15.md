---
title: "Lesson_15"
date: 2022-06-02T10:59:40+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
- [消息队列的背景](#消息队列的背景)
  - [为什么需要消息队列？](#为什么需要消息队列)
  - [消息队列给出的方案](#消息队列给出的方案)
  - [常见消息队列](#常见消息队列)
- [Kafka](#kafka)
  - [使用场景](#使用场景)
  - [如何使用](#如何使用)
  - [分区和副本](#分区和副本)
  - [kafka架构](#kafka架构)
  - [kafka高效的原因一：批处理和压缩](#kafka高效的原因一批处理和压缩)
  - [kafka高效的原因二：利于频繁写的存储设计](#kafka高效的原因二利于频繁写的存储设计)
  - [kafka高效的原因三：高效的IO数据拷贝](#kafka高效的原因三高效的io数据拷贝)
  - [分区的rebalance](#分区的rebalance)
  - [kafka的问题](#kafka的问题)
- [BMQ](#bmq)
  - [运维操作对比](#运维操作对比)
  - [底层的分布式存储文件系统：HDFS](#底层的分布式存储文件系统hdfs)
  - [broker的状态机](#broker的状态机)
  - [broker的写文件流程](#broker的写文件流程)
  - [proxy](#proxy)
  - [泳道消息](#泳道消息)
  - [databus](#databus)
  - [mirror](#mirror)
  - [index](#index)
  - [parquet](#parquet)
- [rocketMQ](#rocketmq)
  - [使用场景](#使用场景-1)
  - [架构](#架构)
  - [存储模型](#存储模型)
  - [高级特性：事务场景](#高级特性事务场景)
  - [高级特性：延时发送](#高级特性延时发送)
  - [高级特性：消费重试和死信队列](#高级特性消费重试和死信队列)

## 消息队列的背景

### 为什么需要消息队列？

- 案例一：系统崩溃 一个线性的流程，会因为某一环的破坏而阻塞整个流程
- 案例二：服务能力有限 请求量过大，服务如何处理
- 案例三：链路耗时长尾，影响用户体验

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb30c574c5974521ace33a89ea7efaf6~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

- 案例四：日志存储和处理

### 消息队列给出的方案

- 案例一： 解耦，将某些服务的操作发给消息队列
- 案例二： 削峰，将请求放入消息队列
- 案例三： 异步，请求订单放入消息队列，此时就把中间结果告知用户。随后处理。
- 案例四： 将日志记录发到消息队列里，通过些日志组件做专门的处理，搜索和可视化分析等等。我们不再需要在业务代码里面侵入我们的各种打点or日志，只需要简单的发布一条消息，再去关注做处理就好了

### 常见消息队列

- kafka
- RocketMQ
- Pulsar
- BMQ ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/073597f83351468fa555dbe2805b77a7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

## Kafka

### 使用场景

- 搜索服务
- 直播服务
- 订单服务
- 支付服务 提供的功能：
- 日志信息采集分析
- metrics数据
- 用户行为异步处理

### 如何使用

1. 创建集群
2. 新增topic(并设置分区数量)
3. 编写生产者逻辑
4. 编写消费者逻辑

### 分区和副本

有以下关键概念

topic:一个逻辑队列，可以有多个分区

生产者：生产消息

消费者：消费数据

分区(partation)：提高某个topic的负载能力

offset:消息在一个分区内的相对信息位置，即唯一严格递增ID

副本（replica）：每个分区有多个副本，提供容灾能力。副本分为leader和follower，leader副本会从ISR中选出。leader是可读写的，follower是只读的。

ISR（in-sync replicas）：指和leader差距在一定范围（offset或者时间来衡量）内的follower副本组

broker：一个节点

controller：一个计算并管理各个分区副本所在的位置信息，告诉消费者和生产者。是由一个broker担任。

### kafka架构

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bcdc968b1f2143aab085996d0f91dd32~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### kafka高效的原因一：批处理和压缩

如果发送一条消息，等待成功后再发一条有什么问题？

- 答：处理消息和等待返回消息，吞吐量低。
- 解决方案：批处理，批量发送消息，减少IO次数，从而增强发送能力。 单条消息太大，带宽不够用了怎么办？
- 解决方案：将一批次的消息进行数据压缩（推荐ZSTD压缩算法）

### kafka高效的原因二：利于频繁写的存储设计

broker如何存储数据到磁盘？

- 一个副本，最终会以日志形式放到磁盘里。日志会切成多段，具体组成如下

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/618a267aa6bf4ea88fccce2a33eba921~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?) 为了磁盘IO友好，kafka的日志遵循顺序写，那么broker如何找到消息？

其实如果了解LSMTree的同学，应该能意识到kafka的数据存储就是类似这个。推荐阅读DDIA（数据密集应用设计）[Designing Data-Intensive Applications (DDIA) — an O’Reilly book by Martin Kleppmann (The Wild Boar Book) (dataintensive.net)](https://link.juejin.cn/?target=https%3A%2F%2Fdataintensive.net%2F "https://dataintensive.net/")这本书的第三章节对这部分做进一步了解。里面很详细，这里不再赘述了。这本书真心不错，建议通篇阅读。

### kafka高效的原因三：高效的IO数据拷贝

传统的数据拷贝模型下，数据往往先从外围设备（磁盘，网卡等）拷贝到内核空间的buffer，然后再拷贝到应用空间的buffer给应用进程使用。这比较慢。（下面的图是从读数据的角度看，是类似写数据的逆过程）

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbbe9c4803b3496fbe4d737d4a484f81~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

为了改进，则使用了零拷贝的技术

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e8346f14b494ed0842bc47327d4c547~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### 分区的rebalance

如何解决分区和消费者组的分配问题，即某个消费者去消费哪个分区？

- 手动分配
- 自动分配 手动的问题：消费者容灾问题；增加或者减少消费者时的启停问题。

自动分配由coordinator来帮忙做，会持续探测现有的消费者组，分配消费队列，可以解决上述问题。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5b8cbc1ed02f423f8ad35e6bf14fbf24~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### kafka的问题

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/75d2f0c27f1c4d58aaa63c83c915287b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

## BMQ

兼容kafkax协议，存算分离，云原生消息队列

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/050c475501ef445eb281d50bac77a413~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### 运维操作对比

主要是因为存储分离了，所以运维操作涉及的数据复制代价很小。 ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/89adf14e84c74d31aaec37b62f76e39d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### 底层的分布式存储文件系统：HDFS

如何解决负载均衡？随机选择节点去写入。当然，随机写入会带来查询上的一些问题，可以另做了解。

### broker的状态机

对于任意分片，同一时刻只能在一个broker上存活。 老师说这里是为了防止脑裂（就是保证只有1个leader），造成乱序，用了状态机来保证。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dd155d73d9dc4c9283e6bc68d4e62d5b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?) 进一步解释的话，按我的理解，应该是因为存算分离了，broker其实只相当于接一个承担写入的角色了，没有必要另外的副本了。数据的容灾由底层存储系统提供了。从上面的架构图也可以看出，读功能并不经过broker。所以也不需要有follower部署在broker上了，还不懂的可以了解下单主多从架构.

### broker的写文件流程

流程图如下

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb3d33ab0e4e4045a98df86f98342cf1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?) 这里有两个写入策略：

- 写到buffer就返回
- 写到磁盘才返回 前者更快，但可能导致数据的丢失（因为没有写到磁盘里，如果这个时候宕机了就丢了）。

faliover:如果写数据到datanote时出错，就换个节点继续写入。

### proxy

有一个缓存，流程如下

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/30facfee493e411faf807fc0b19f6af5~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### 泳道消息

BOE：完全独立的线下机房环境 PPE：产品预览环境 在BOE中测试时，多个人同时使用，需要等待上一个人测试完成，比较久。

但如果重新生产一个相同配置的topic，人力和资源浪费了。

而在PPE环境中，由于资源没有生产环境多，所以无法承受生产环境的流量。

为了解决以上问题，使用泳道来做隔离

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/710d9241e6c046d4b7efa65df976f9a8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### databus

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/801092479e7b48b79f81146ea4c57e26~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### mirror

由于跨域跨国家的不同机房之间延迟很高，普通的多机房部署方式性能不太行。所以使用了镜像。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d38ffaf30c994143bdac3068108220b3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### index

有时候不仅仅是通过offset或者时间戳查询，有时候还想通过logid\\userid等做查询。所以需要建立一些索引。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a3e0b3490da4af7b4fb3ceb2698fb21~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### parquet

新型列示存储格式，兼容hadoop的多种计算框架。如果查询业务特征是列存储友好的，就可以使用列式存储加速查询。

## rocketMQ

### 使用场景

针对电商业务线，和某些业务的高峰值时刻。

和kafka组件关键词对比如下：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68b7495aa7794dcb927b60cd9110cfce~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### 架构

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/abf6a44e85334162ab79fbcac77afc60~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?) 在rocketMQ中，主从副本是针对整个节点集群而言了，即一整个broker是一个主节点或者从节点

### 存储模型

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/476affb609304a1e91acce475e95d456~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?) 每一个broker只有一个commitlog，接收所有的topic消息。然后会根据commitlog，dispatch出一些消费索引队列，这些索引是密集存储的，而不是kafka那样稀疏存储。

### 高级特性：事务场景

某个使用消息队列的场景如下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f8d3ac03935f4631985ec8252702257d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

事务的保证：ACID

库存记录和消息队列必须包装到一个事务中。

那么一个推荐的设计如下： ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a11c562622e47e2a7b11247c73f518d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### 高级特性：延时发送

可以使用消息队列来做定时任务的存储。可以启动一个延迟服务来做异步的消费这个定时任务，流程如下：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e50fcf2da2d444568c78924bdee1297d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

### 高级特性：消费重试和死信队列

在消费的过程中处理失败，进行重试，方案类似定时方案。流程如下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ae6ca04fb0e64f3eab5cf7d7a37e2657~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)
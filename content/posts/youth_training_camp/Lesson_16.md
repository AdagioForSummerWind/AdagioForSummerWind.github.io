---
title: "Lesson_16"
date: 2022-06-02T10:59:43+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---

- [分布式定时任务](#分布式定时任务)
  - [春节瓜分20亿](#春节瓜分20亿)
  - [发展历程](#发展历程)
    - [Windows批处理](#windows批处理)
    - [Windows任务计划程序](#windows任务计划程序)
    - [Linux命令-CronJob](#linux命令-cronjob)
    - [单机定时任务Timer，Ticker](#单机定时任务timerticker)
    - [单机定时任务 ScheduleExecutorService](#单机定时任务-scheduleexecutorservice)
    - [任务调度Quartz](#任务调度quartz)
    - [分布式定时任务](#分布式定时任务-1)
    - [什么是分布式定时任务](#什么是分布式定时任务)
    - [特点](#特点)
    - [执行方式](#执行方式)
    - [执行方式VS春节集卡](#执行方式vs春节集卡)
    - [业内定时任务框架](#业内定时任务框架)
    - [知识面扩充](#知识面扩充)
      - [分布式定时任务VS单机定时任务](#分布式定时任务vs单机定时任务)
      - [分布式定时任务VS大数据处理引擎](#分布式定时任务vs大数据处理引擎)
  - [实现原理](#实现原理)
    - [核心架构](#核心架构)
      - [数据流](#数据流)
      - [功能架构](#功能架构)
    - [控制台](#控制台)
      - [基本概念](#基本概念)
      - [基本概念-任务元数据](#基本概念-任务元数据)
      - [基本概念-任务实例](#基本概念-任务实例)
    - [触发器](#触发器)
      - [核心职责](#核心职责)
      - [方案1：定时扫描+延时消息](#方案1定时扫描延时消息)
      - [方案2：时间轮（Quartz方案）](#方案2时间轮quartz方案)
      - [触发器-高可用](#触发器-高可用)
      - [高可用-问题引出](#高可用-问题引出)
      - [高可用-数据库行锁模式(Quartz)](#高可用-数据库行锁模式quartz)
      - [高可用-分布式锁模式(Quartz)](#高可用-分布式锁模式quartz)
    - [调度器](#调度器)
      - [资源来源](#资源来源)
      - [资源调度-节点选择](#资源调度-节点选择)
      - [资源调度-任务分片](#资源调度-任务分片)
      - [高级特性-任务编排](#高级特性-任务编排)
      - [高级特性-故障转移](#高级特性-故障转移)
      - [调度器-高可用](#调度器-高可用)
    - [执行器](#执行器)
  - [业务应用](#业务应用)
    - [其他解决方案](#其他解决方案)
    - [对比](#对比)

## 分布式定时任务

### 春节瓜分20亿

作为后端开发同学，怎么设计最终的开奖环节技术方案？

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/53fb8bc8e1c2454c865b243eea037c33~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

自动化+定时执行+海量数据+高效稳定 = 》分布式定时任务

### 发展历程

#### Windows批处理

Case 1:10min后自动关机

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/617bfdb4996e44eea5dc20c25a753d30~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### Windows任务计划程序

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6c80284a0894442581a470b0502564d1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### Linux命令-CronJob

每天2:30清理日志

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2fe3ad70ada94f08b039abe296d5f167~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 单机定时任务Timer，Ticker

case 4：每隔五分钟定时刷新本地缓存数据

- Java

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b1a8261d90114c3fa78fdd1d483edae3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- GO

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0aa7fb7158c84d6b9d57b20d96918e71~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

\\

#### 单机定时任务 ScheduleExecutorService

- 每隔五分钟定时执行多个任务

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7648ddb0358c4848be3b9443e79ff1fc~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 任务调度Quartz

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a709cc0de5c493facc700ecd4f3dfb8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- 单任务机制控制
- 没有负载均衡

#### 分布式定时任务

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f15cbd6f31464017b76297a4645efbae~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 什么是分布式定时任务

定时任务是指系统为了自动完成特定任务，实时、延时、周期性完成任务调度的过程。

分布式定时任务是把分散的、可靠性差的定时任务纳入统一的平台，并实现集群管理调度和分布式部署的一种定时任务的管理方式。

按触发时机分类:

- 定时任务: 特定时间触发，比如今天15:06执行
- 延时任务：延时触发，比如10s后执行.
- 周期任务:固定周期时间,或固定频率周期调度触发,比如每隔5s或者每天12点执行

#### 特点

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/843d37131c684aeb874803f5659fb5b0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 执行方式

- 单机任务:**随机触发一台机器**执行任务,适用于计算量小、并发度低的任务
- 广播任务:**广播到所有机器上执行同一个任务，比如所有机器一起清理日志.**
- Map任务:**一个任务可以分出多个子任务,每个子任务负责一部分的计算**。适用于计算量大,单机无法满足要求的任务
- MapReduce任务: 在Map任务的基础上，还可以**对所有子任务的结果做汇总计算**，适用于计算量大,并且需要对子任务结果做汇总的任务\\

#### 执行方式VS春节集卡

MapReduce任务 Map任务

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3d3abbac3bdf47489a6a0f4829fb859f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 业内定时任务框架

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a18d72daa5046429f24eb4e0b09b1d2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 知识面扩充

##### 分布式定时任务VS单机定时任务

关系:

- 都可以实现自动化的定时、延时、周期任务调度

差异:

- 分布式定时任务可支撑更大的业务体量
- 分布式定时任务的性能、伸缩性、稳定性更高

##### 分布式定时任务VS大数据处理引擎

关系:

- 都可以对海量数据做处理
- 性能、伸缩性、稳定性都很高

差异:

- **定时并不是大数据处理引警要解决的核心问题**
- 大数据处理引擎往往致力于将源数据处理成结果数据,**分布式定时任务除了能做这个之外,还可以调用HTTP和RPC服务**

### 实现原理

#### 核心架构

分布式定时任务核心要解决触发，调度，执行三个问题

- 触发器:Trigger，解析任务，生成触发事件
- 调度器: Scheduler,分配任务,管理任务生命周期.
- 执行器: Executor,获取执行任务单元,执行任务逻辑

除此之外，还需要提供一个控制台(Admin),提供任务管理和干预的功能。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6a662bb2c8c54c248239d6854ae6d3ed~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 数据流

任务创建：用户提交信息到控制台Admin

- 任务基础信息
- 触发规则
- 任务代码

任务执行：

- 触发器判断任务什么时候执行
- 调度器协调
- 执行

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d633919c5d1c48548de8a1382983446f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 功能架构

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/318b871281b04bd9b87ba851b5ee4b5d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 控制台

##### 基本概念

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7bc19f14a9684b05acce33f7bbd131c4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- **任务**:Job,任务元数据
- **任务实例:** Joblnstance,**周期任务会生成多个任务实例 1：n**
- **任务结果**: JobResult,任务实例运行的结果（1：n 失败了会重试，每次失败都会有一个结果）
- **任务历史**: JobHistory,用户可以修改任务信息,任务实例对应的任务元数据可以不同,因而使用任务历史存储

##### 基本概念-任务元数据

任务元数据是用户对任务属性的定义，包括任务调度时机，执行行为等

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e5820d846c349d0b867ff6e2d077885~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 基本概念-任务实例

是一个确定的Job的一次运行实例

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b9cc5808e3948acb35e7d4851a7713b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/464b0603ac854a44a7c77ad992a0449e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 触发器

##### 核心职责

核心职责

- 给定一系列任务,解析它们的触发规则,在规定的时间点触发任务的调度

**设计约束**

- 需支持大量任务
- 需支持秒级的调度周期任务
- 需要多次执行
- 需保证秒级扫描的高性能,并避免资源浪费

##### 方案1：定时扫描+延时消息

- 扫描器：定时执行，扫描db，扫描出最近时间要执行的任务（如10min后执行）
- 将这部分发送到消息队列延时消息
- 改变db中任务的状态
- 等时间到了，调度器会触发调度，执行任务，与数据库交互

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1b9c79e46aaf437ca76fe2df79eb373d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 方案2：时间轮（Quartz方案）

时间轮是一种高效利用线程资源进行批量化调度的一种调度模型。**时间轮是一个存储环形队列，底层采用数组实现,数组中的每个无素可以存放一个定时任务列表。**  
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3cdd1cb7538f487ba227632ea95d2e2e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

目标：遍历任务列表，从中找出当前时间点需要触发的任务

- 简单方案

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d80089b4c124857a4ef7e62065b0728~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- 优化：使用最小堆，因为只需要知道时间节点最近的元素

- - 缺点：数组长度无限扩展

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa92e5fe6091497abd239a3e22854384~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- 优化：环形数组：可以当做时钟

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ddaf5a816c941419c372f0dc0fd18ba~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- 优化：多级时间轮

- - 小时，分钟，秒，甚至还可以扩展
    - 应用：心跳检测等

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/09d5ebb13af4424c959806572be104ca~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 触发器-高可用

核心问题

- 不同业务之间,**任务的调度相互影响**怎么办?
- **负责扫描和触发的机器挂了**怎么办?

解法思路

- **存储上**,不同国别、业务做资源隔离
- **运行时**,不同国别、业务分开执行
- **部署时**,采用多机房集群化部署,避免单点故障,通过数据库锁或分布式锁保证任务只被触发一次

##### 高可用-问题引出

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9579ec854fce492abed2ce6cf1acb9d1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 高可用-数据库行锁模式(Quartz)

**在触发调度之前,更新数据库中Joblnstance的状态,成功抢锁的才会触发调度**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b7dcaa8ea0594a8aa8d4b88f8ee44a94~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- **多台机器频繁竞争数据库锁，节点越多性能越差**

##### 高可用-分布式锁模式(Quartz)

**在触发调度之前,尝试抢占分布式锁**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbc94cc232f0409184059a6f43564204~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 调度器

##### 资源来源

- 业务系统提供机器资源
- 定时任务平台提供机器方案

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c076df5a70af4bb3bf5b0331b341f3db~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 资源调度-节点选择

- 随机节点执行：随机选择一个

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5cb6eb7148ac4a18a83b38696e531121~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- 广播执行：集群中所有机器

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/73281f57384743c08b22dabb4967d850~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- 分片执行：按照用户自定义逻辑分片进行拆分，分配到不同的节点并行执行

##### 资源调度-任务分片

通过任务分片提高任务执行效率和资源利用率

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/02800fbfd3ed47a2b9e04bbb4fcf0d17~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

N个执行器Executor, M个业务数据区段,最好M>=N, 且M是N的整数倍

##### 高级特性-任务编排

使用有向无环图进行可视化任务编排

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/93eb81ebc1884d56b6f937f39c43252e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 高级特性-故障转移

确保部分执行单元任务失败时，任务最终成功

分片任务基于一致性hash策略分发任务,当某Executor异常时,调度器会将任务分发到其他Executor

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/737a745bb7ef450c9deb60d41aa15846~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

##### 调度器-高可用

调度器可以集群部署,做到完全的无状态,靠消息队列的重试机制保障任务一定会被调度

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d9cd97e588744ea0b2fe747d09030c95~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 执行器

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32811c3c0f6542ac8467f42251e42df4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

- 基于注册中心，可以做到执行器的弹性扩缩容

### 业务应用

所有需要定时、延时、周期性执行任务的业务场景,都可以考虑使用分布式定时任务。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b8605ad42d1449491126fcefcd5bc45~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 其他解决方案

发货后超过10天未收货时系统自动确认收货民.

- 使用分布式定时任务的延时任务
- 使用消息队列的延时消息或者定时消息

春节集卡活动统计完成集卡的用户个数和总翻倍数

- 使用分布式定时任务的MapReduce任务
- 使用大数据离线处理引擎Hive离线做统计
- 使用大数据实时处理引擎Flink实时做累计

#### 对比

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c715d6b508c845eca268d6ef139bfb36~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)
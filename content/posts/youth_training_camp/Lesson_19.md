---
title: "Lesson_19"
date: 2022-06-02T10:59:52+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---

- [抖音背后的存储](#抖音背后的存储)
  - [短视频架构初探](#短视频架构初探)
  - [存储需求](#存储需求)
  - [存储需求量细化](#存储需求量细化)
  - [寻找天选存储](#寻找天选存储)
- [为什么对象存储](#为什么对象存储)
  - [前情回顾](#前情回顾)
  - [存储对比](#存储对比)
  - [分布式存储选型](#分布式存储选型)
  - [易用性：接口对比](#易用性接口对比)
  - [适用场景](#适用场景)
- [对象存储怎么用](#对象存储怎么用)
  - [申请Bucket](#申请bucket)
  - [Restful 接口](#restful-接口)
  - [Restful接口演示](#restful接口演示)
  - [MultiUpload接口](#multiupload接口)
  - [Listprefix 接口](#listprefix-接口)
- [TOS字节内部实战](#tos字节内部实战)
  - [开发一个对象存储](#开发一个对象存储)
  - [开发一个对象存储](#开发一个对象存储-1)
  - [可扩展性解法之Partition](#可扩展性解法之partition)
  - [持久度解法之Replication](#持久度解法之replication)
  - [成本解法之EC](#成本解法之ec)
  - [成本解法之温冷转换](#成本解法之温冷转换)
  - [架构细化](#架构细化)
  - [存储需求量细化](#存储需求量细化-1)
  - [高可用解法之拆分降低爆炸半径](#高可用解法之拆分降低爆炸半径)
  - [高可用之粤核算的启发](#高可用之粤核算的启发)
  - [高可用之镜像灾备](#高可用之镜像灾备)



## 抖音背后的存储

### 短视频架构初探

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/559f4e29a7414f74b97adfce979502d6~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 存储需求

> 把短视频生产/消费链路做更细粒度分解，小明发现到处都有视频/图片的公共存储需求

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/666aeb35ee2c42c5a4664dea1c47f854~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 存储需求量细化

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c454db18175142ebaac520e5fad543c5~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 寻找天选存储

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/55effecf1c404bd887e4a586a34c1f23~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 为什么对象存储

### 前情回顾

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1e9df0f7a2ca4dcd97cd89f4330c92f9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 存储对比

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/415add891ae241a38ae05218cfff954e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 分布式存储选型

> 但分布式存储也有分布式文件系统和对象存储，应该选择哪个呢?

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1167721fc1334fb4ab0629b18755718d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 易用性：接口对比

弱Posix文件系统语义

- 目录/文件
- Append写
- 无法直接HTTP访问 接口速览
- Mkdirs
- Append
- Create
- CreateSymlink
- Delete
- Open
- Get
- ...

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/599c7b0bb7a44773aa9ff75af1309e7a~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

Bucket/Object语义

- Bucket:存储对象的桶，可类比一个云上的Map
- Object:对象，包含如下三个部分
    - Key: 对象的名字，可类比Map的Key
    - Data:对象的内容,例如视—/图片内容
    - MetaData:对象的一些元信息，如对象大小，对象Content-Type，也可以存储自定义元信息 HTTP接口
- 任何时间、任何地点、任何互联网设备上传和下载数 据
- 支持HTTP协议的各种客户端都可访问 接口速览
- GET:下载对象
- HEAD:查看对象元言息
- PUT:上传对象
- DELETE:删除对象
- ...

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e793e6f277444329c97cf770d39a604~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 适用场景

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b4f651132c6342369eb79d107df41381~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 对象存储怎么用

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/781db58ae42c4a4d8a3238a6222cc8de~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 申请Bucket

对象存储很多云厂商都支持.小明选择了字节的TOS:

### Restful 接口

PUT: -参数：Bucket, key。对象内容

- 返回:成功/失败 GET:
- 参数: Bucket, Key
- 返回:对象内容 HEAD: Lite版GET
- 参数: Bucket, Key
- 返回:对象元信息，如大小/Content-Type等 DELETE:
- 参数:Bucket, Key。
- 返回:成功/失败

> 小明申请完Bucket后，要开启开发，他了解到对象存储对外提供的一般都是Restful风格的接口。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03d962e0a01b4268bda7e62a713c68f7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### Restful接口演示

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2cac486268704eef998a224d672ded2f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### MultiUpload接口

> 随着开发的深入，小明发现一个问题，自己上传数GB的大视频时，由于网络不好，总是上传到99%就网络卡住了，他很恼火,翻漏对象存储名类手册，终于发现了一个解决此场景的闪电三连鞭: MultiUpload

lnitUpload:

- 参数:Bucket , Key
- 返回:uploadld UploadPart:
- 参数:uploadld, Partld , Part内容
- 返回:成功/失败 CompleteUpload:
- 参数:uploadld, Partld Array
- 返回:成功/失败 `当一口吃不成胖子时，你应该慢慢来，到最后你会发现，肚子总会比钱包先鼓起来!`

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/27d8483f926146e38f924c615dcb5496~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### Listprefix 接口

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/560570d5f9e44c9a830f4b46dbe7efa0~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d6a1be8a4845498c98fa1a226b86f2f3~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## TOS字节内部实战

### 开发一个对象存储

> 短视频应用上线后，大获成功，对象存储在公有云的使用量特别大，数据量大了，领导决定自研对象存储，这个重任又交给了小明。小明很快想出了一个经典的三层架构:

- 接入层:接入解析并处理接口请求
- 元信息云:存诸对象元信息
- 存储引擎层:存储对象内容

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3ef44b9cb9c04cada67054a255048b7f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 开发一个对象存储

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d548f048e1104519aa212513432d01a1~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 可扩展性解法之Partition

分布式存储=分布式＋单机存储 分布式:

- 存储均匀分布
- 计算均匀分布
- 压力均匀分布 `分布式系统相当于一个蜂群，每个节点都负责一小部分数据存储和计算，达到1+1 >=2的效果`

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67bfb7dde8d74c3ea89758d41b4b781e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

artition (分而治之)

- 分而治之:不同数据映射至不同Partition分区
- Partition Logic: Hash/Range 可扩展性如何达成
- 数据量增加:扩容机器新建Partition
- Parition Logic:新增数据写入映射导向新artition

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1e21e35b7a204d109ca6495de2544461~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 持久度解法之Replication

持久度的敌人:不可靠的一切

- 不可靠的硬件
- 不可靠的软件
- 自然灾害
- 甚至是太阳黑子! `数据单节点存储，一定有较大概率丢失无法找回!!!`

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ddb17a4329b4c7cb1f0dd2dd3088fa9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

复制(Replication)

- 数据复制多份，即多个副本
- 副本放置策略:
    - 多机架:可抵抗机架级别故障
    - 多机房:可抵抗机房级别故障
    - 多Region:可抵抗Region级别故障 带来
- 高持久度:不丢数据
- 强吞吐能力:多个副本可以提供服务

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b0233379e7849b9a1048ab19fc8ec7e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 成本解法之EC

> Replication虽然可以解决持久度问题，但是单纯多副本拷贝成本也非常高!

EC (Erasure Coding)

- 冗余编码:可达到和多副本一样的持久度 特点
- 低冗余度:成本较单纯多副本低
- 额外计算:增加了额外的编码计算步骤 思考

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d604d0a57ad64263a2d3c753c11aac90~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 成本解法之温冷转换

数据都是有温度的，将冷数据转移到性能更差但更廉价的存储介质不就可以省下来成本么?

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5bc7f37e22164e9a8e5b7f3fba90b2fa~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 架构细化

- API:接入层
- Bucket Meta: Bucket元信息服务
- Object Meta:对象元信息服务
- Distributed Kv: Range Partition的分布式KV、用于持久化对象元数据
- Storage Engine:对象内容存取服务
- Distributed storage Pool:分布式存储池.三副本 or Ec存储
- GC:垃圾回收后合服务
- Lifecycle:温冷转换后合服务

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/66cd1b5354154073b62828affa4a2a22~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 存储需求量细化

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b7db2cc6d641431e82cbeb2d48f4aea8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 高可用解法之拆分降低爆炸半径

> 首先想到的就是，一个集群拆分成多个集群，有效降低爆炸半径

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f94b6ff84aba4601adffe675826978df~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 高可用之粤核算的启发

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ea49f3905fa4736bd7e01c641235f40~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 高可用之镜像灾备

> 完全镜像的主备 Bucket，出现问题随时切换，真正100%的可用性!

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/90cc34327f6b46a3a942eb404f5a17d0~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)
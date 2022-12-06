---
title: "Lesson_14"
date: 2022-06-02T10:59:37+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---

- [一、微服务架构介绍](#一微服务架构介绍)
- [二、微服务架构原理及特性](#二微服务架构原理及特性)
- [三、核心服务治理功能](#三核心服务治理功能)
- [四、字节跳动服务治理实践](#四字节跳动服务治理实践)

## 一、微服务架构介绍

系统架构的演变历史

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/db5bd0e0df0e4180931c52c475e89907~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

微服务架构的整体概览

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d25821d2cafe4434a3ff609cec618ab7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?) 微服务架构中的核心要素

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf1fb97ad2384080ad1c830982da19ef~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 二、微服务架构原理及特性

基本概念：

- 服务：一组具有相同逻辑的运行实体
- 实例：一个服务中，每个运行实体即为一个实例
- 实例与进程的关系：实例与进程之间没有必然对应关系，可以一个实例可以对应一个或多个进程
- 集群：通常指服务内部的逻辑划分，包含多个实例
- 常见的实例承载形式：进程、VM、k8s pod...
- 有状态/无状态服务：服务的实例是否存储了可持久化的数据

服务注册及发现：新增一个统一的服务注册中心用于存储服务名到服务实例的映射

流量特性

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/36c66e9dc2b8474ea46c84f4b4b5164e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 三、核心服务治理功能

**服务发布**

难点：

- 服务不可用
- 服务抖动
- 服务回滚

发布方式：

- 蓝绿部署
- 灰度发布（金丝雀发布）

**流量治理**

在微服务架构下，可以基于地区、集群、实例、请求等维度，对端到端流量的路由路径进行精确控制

**负载均衡**

负责分配请求在每个下游实例上的分布

**稳定性治理**

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d2e26a34e68f4b4ea2257af2665695a8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 四、字节跳动服务治理实践

重试的难点：

- 幂等性：多次请求可能会造成数据不一致
- 重试风暴：随着调用深度的增加，重试次数会指数级上涨
- 超时设置：假设一个调用正常是1s的超时时间，如果允许一次重试，那么第一次请求经过多少时间进行重试呢？

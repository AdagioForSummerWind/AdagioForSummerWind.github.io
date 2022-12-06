---
title: "Lesson_18"
date: 2022-06-02T10:59:49+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---

- [1. 经典案例](#1-经典案例)
  - [1.1 从一场红包雨说起](#11-从一场红包雨说起)
  - [1.2 RDBMS事务ACID](#12-rdbms事务acid)
  - [1.3 红包雨与ACID](#13-红包雨与acid)
  - [1.4 红包雨与高并发](#14-红包雨与高并发)
  - [1.5 红包雨与高可靠](#15-红包雨与高可靠)
- [2. 发展历史](#2-发展历史)
  - [2.1 前DBMS时代-人工管理](#21-前dbms时代-人工管理)
  - [2.2 前DBMS时代-文件管理](#22-前dbms时代-文件管理)
  - [2.3 DBMS时代](#23-dbms时代)
    - [2.3.1 DBMS数据模型-网状模型](#231-dbms数据模型-网状模型)
    - [2.3.2 DBMS数据模型-层次模型](#232-dbms数据模型-层次模型)
    - [2.3.3 DBMS数据模型-关系模型](#233-dbms数据模型-关系模型)
  - [2.4 DBMS数据模型](#24-dbms数据模型)
  - [2.5 SQL语言](#25-sql语言)
  - [2.6 历史回顾](#26-历史回顾)
- [3. 关键技术](#3-关键技术)
  - [3.1 一条SQL的一生](#31-一条sql的一生)
  - [3.2 SQL引擎](#32-sql引擎)
    - [3.2.1 SQL引擎-Parser](#321-sql引擎-parser)
    - [3.2.2 SQL引擎-Optimizer](#322-sql引擎-optimizer)
    - [3.2.3 SQL引擎-Executor](#323-sql引擎-executor)
  - [3.3 存储引擎](#33-存储引擎)
    - [3.3.1 存储引擎-InnoDB](#331-存储引擎-innodb)
    - [3.3.2 存储引擎-Buffer Pool](#332-存储引擎-buffer-pool)
    - [3.3.3 存储引擎-Page](#333-存储引擎-page)
    - [3.3.4 存储引擎-B+ Tree](#334-存储引擎-b-tree)
  - [3.4 事务引擎](#34-事务引擎)
    - [3.4.1 事务引擎-Atomicity与Undo Log](#341-事务引擎-atomicity与undo-log)
    - [3.4.2 事务引擎-Isolation与锁](#342-事务引擎-isolation与锁)
    - [3.4.3 事务引擎-Isolation与MVCC](#343-事务引擎-isolation与mvcc)
    - [3.4.4 事务引擎-Durability与Redo Log](#344-事务引擎-durability与redo-log)
- [4. 企业实践](#4-企业实践)
  - [4.1 春节红包雨挑战](#41-春节红包雨挑战)
  - [4.2 大流量-Sharding](#42-大流量-sharding)
  - [4.3 流量突增-扩容](#43-流量突增-扩容)
  - [4.4 流量突增-代理连接池](#44-流量突增-代理连接池)
  - [4.5 稳定性\&可靠性](#45-稳定性可靠性)
    - [4.5.1 稳定性\&可靠性-3AZ高可用](#451-稳定性可靠性-3az高可用)
    - [4.5.2 稳定性\&可靠性-HA管理](#452-稳定性可靠性-ha管理)


## 1\. 经典案例

### 1.1 从一场红包雨说起

![image-20220604222855959](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/17fa4675f43c466faf13cd9597c45532~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 1.2 RDBMS事务ACID

![image-20220604222906949](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/581357be65ef41e5a779b1b12997a997~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 1.3 红包雨与ACID

![image-20220604223013234](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/97271a8696f44de3aff4c67b4911587a~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604223026032](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/981dddce8271454d9fedbe3a629e267f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604223105524](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da475882b54f43e0a2ab224b1652c395~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604223119997](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33727597ac544bf29794f0e6a802a5d2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 1.4 红包雨与高并发

![image-20220604223154640](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b025c71126494c27a1e53d07ebdeaa99~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 1.5 红包雨与高可靠

![image-20220604223202978](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33a1595e3d274f37b5850cd989bc12d1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

## 2\. 发展历史

### 2.1 前DBMS时代-人工管理

![image-20220604223520641](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9957edde853842b398bbf8586846e99e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 2.2 前DBMS时代-文件管理

![image-20220604223534666](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ae94200009644cf5b74ca808b13638f2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 2.3 DBMS时代

![image-20220604223552366](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9c1c1fb2587a4cb3a635499b059ed741~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 2.3.1 DBMS数据模型-网状模型

![image-20220604223606518](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2004d97db775451a9087b7f8a905c189~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 2.3.2 DBMS数据模型-层次模型

![image-20220604223618604](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b4c46e7f452f40499cf9af7065f94a12~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 2.3.3 DBMS数据模型-关系模型

![image-20220604223628273](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fd57ab7881854356b5604fae60f9c173~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 2.4 DBMS数据模型

![image-20220604223637749](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4df56ccf59cb426e8ea3f189b91180e3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 2.5 SQL语言

![image-20220604223647184](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ef7acd823028414caccbd7b4ed5acb51~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 2.6 历史回顾

![image-20220604223657561](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2dd6686db2da4a70949c9af7e15f4a83~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

## 3\. 关键技术

### 3.1 一条SQL的一生

![image-20220604224027241](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4be1b391b8f146119cbad881e70198e8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 3.2 SQL引擎

#### 3.2.1 SQL引擎-Parser

![image-20220604224040585](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42525000ca8047bdb4efe0d65ae60d7d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.2.2 SQL引擎-Optimizer

![image-20220604224052583](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/625006256dcd48f9a8201af38871492c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604224105451](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8397a742b29144f79bf66b500c99dad8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604224116589](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c42f3d91f7f44164bad4a68c826c4717~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.2.3 SQL引擎-Executor

![image-20220604224459984](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a5798017d5f3431b9cbafc83df76dc42~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604224510409](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed6ccdee512546f296eef1e78311dcaf~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604224521816](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/659ed058f8f74029a7eb7b9773cf008d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 3.3 存储引擎

#### 3.3.1 存储引擎-InnoDB

![image-20220604224534066](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/869e440513c441b1b5933be550f6a94b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.3.2 存储引擎-Buffer Pool

![image-20220604224543793](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e10e6e08b56f444380bda7d671e15003~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![image-20220604224553427](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/83b965279bb24a43b2c2b1aea74d548b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.3.3 存储引擎-Page

![image-20220604224606589](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2446bebb33cd4eb9866a6ec7aecb271d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.3.4 存储引擎-B+ Tree

![image-20220604224627793](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/320ff74ed89c41928bb07142ec175a45~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 3.4 事务引擎

![image-20220604224638627](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/22fd6c8de9564c8d851d97806fec792a~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.4.1 事务引擎-Atomicity与Undo Log

![image-20220604224648241](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ea211aed54c4701b4081d567139fe70~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.4.2 事务引擎-Isolation与锁

![image-20220604224709410](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68bc4160c5c847d0a6ffcaf0a2797094~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.4.3 事务引擎-Isolation与MVCC

![image-20220604224720317](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/45084241097a4e4e97032ef6810b3b03~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 3.4.4 事务引擎-Durability与Redo Log

![image-20220604224731386](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b02f7a4692004170a548f7fac5d2967f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

## 4\. 企业实践

### 4.1 春节红包雨挑战

![image-20220604225514821](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a7b95e05404e48ceb8bb6de2578cb1df~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 4.2 大流量-Sharding

![image-20220604225524692](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5128833e1e54ddfb0c5dc038edd7996~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 4.3 流量突增-扩容

![image-20220604225537372](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a851a92eb0546b293f4491b4c573510~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

### 4.4 流量突增-代理连接池

![image-20220604225551044](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a2cd7c7cae384056bb2e67f41ea19175~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

问题背景

- 突增流量导致大量建联

- 大量建联导致负载变大,延时上升

解决方案

- 业务侧预热连接池
- 代理侧预热连接池
- 代理侧支持连接队列

实施效果

- 避免DB被突增流量打死
- 避免代理和DB被大量建联打死

### 4.5 稳定性&可靠性

![image-20220604225603018](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e7f428cc5f1343c68670f6e51b955bfa~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 4.5.1 稳定性&可靠性-3AZ高可用

![image-20220604225910132](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d18f8c55d4cb41bc9ede49aa7020f075~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

#### 4.5.2 稳定性&可靠性-HA管理

![image-20220604225918444](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ea849087e85a467587682b77b41ac16e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

> 课程总结

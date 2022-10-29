---
title: "raft_algo"
date: 2022-07-14T10:04:50+08:00
lastmod: 2022-07-14
tags: [algo]
categories: [School courses]
slug: raft-algo
draft: true
---
> https://zhuanlan.zhihu.com/p/32052223
# Raft

Raft 算法比Zookeeper的ZAB协议要简单一些，Raft算法是分布式一致性算法，它将一致性分解为多个子问题：Leader选举（Leader election）、日志同步（Log replication）、安全性（Safety）、日志压缩（Log compaction）、成员变更（Membership change）等，用于管理多副本状态机的日志复制。

Raft将系统中的角色分为领导者（Leader）、跟从者（Follower）和候选人（Candidate）：

Leader：接受客户端请求，并向Follower同步请求日志，当日志同步到大多数节点上后告诉Follower提交日志。
Follower：接受并持久化Leader同步的日志，在Leader告之日志可以提交之后，提交日志。
Candidate：Leader选举过程中的临时角色。































---
title: "CN_base_04"
date: 2022-01-23T10:50:37+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
# 数据链路层与局域网
## 3.1 数据链路层服务
数据链路层：
- 向下：利用物理层提供的位流服务
- 向上：向网络层提供明确的 (well-defined) 服务接口

DLL包括：
- LLC逻辑链路控制（局域网的数据链路层）
- MAC介质访问控制

DLL功能：
- 负责结点-结点（node-to-node）数据传输
- 组帧（Framing）
    - 封装网络层数据报构成数据帧
    - 帧同步：从物理层接收的比特流中准确区分出一帧的开始和结束
        - 字节计数法（ Byte count ）
        - 带字节填充的定界符法（ Flag bytes with byte stuffing ）
            - 定界符（FLAG）：一个特殊的字节，比如01111110，即 0x7E，用于区分前后两个不同的帧
- 物理寻址（Physical addressing）
- 流量控制（Flow control）
- 差错控制（Error control）
- 访问控制（Access control）

数据链路层负责通过一条链路从一个节点向另一个物理链路直接相连的相邻结点传送数据报

## 3.2 差错检测与纠正
## 3.3 可靠数据传输
## 3.4 多路访问控制协议
## 3.5 局域网技术
## 3.6 PPP协议
## 3.7 链路虚拟化

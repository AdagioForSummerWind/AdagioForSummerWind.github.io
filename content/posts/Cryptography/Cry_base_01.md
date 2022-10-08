---
title: "Cry_base_01"
date: 2021-12-16T14:32:42+08:00
lastmod: 2021-12-16
tags: [cryptography]
categories: [School courses]
slug: cryptography intro
draft: true
---
> 笔记来自2021年秋哈工大深圳密码学基础授课老师：蒋琳

# 课程简介
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211216143711.png)

不包含内容
- 计算机安全，例如病毒
- 网络安全，例如网络攻击
- 安全网络协议，例如安全传输层协议

公钥密码使得发送端和接收端无密钥传输的保密通信成为可能。

- 对称加密(Symmetric encryption)：加密和解密由同一个密钥来控制，也叫“单钥加密” 。
- 非对称加密(Asymmetric encryption)：用作加密的密钥不同于用作解密的密钥，而且解密密钥不能根据加密密钥计算出来，也叫“公钥加密”

密码算法分类：![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211216144246.png)

密码术：
- 密码编码学
    - 古典密码学
        - 隐写
        - 替换（代换）
            - 凯撒密码
            - 维吉尼亚密码
            - Hill cipher
        - 置换
            - 栅栏密码
    - 现代密码学
        - 对称密码
            - 分组密码
                - DES
                - AES
            - 序列密码
                - RC4
            - 消息认证
                - MAC
                - Hash
        - 非对称密码
            - 公钥密码学
                - REA加密
                - EIGamal签名
                - 椭圆曲线密码学
            - 基于身份密码学
            - 高级密码法
                - 属性加密
                - 谓词加密
                - 环签名
                - 秘密分享
- 密码分析学

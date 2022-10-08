# 

<!--
 * @Author: JF-011101 2838264218@qq.com
 * @Date: 2022-08-26 17:25:59
 * @LastEditors: JF-011101 2838264218@qq.com
 * @LastEditTime: 2022-10-01 21:40:36
 * @FilePath: \posts\go_projects\dytt.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
---
title: "dytt"
date: 2022-01-12T22:17:18+08:00
lastmod: 2022-01-06
tags: [dytt]
categories: [fytt]
slug: Go_Data_Operation
draft: true
---
# dytt
## 介绍一下项目的架构

DYTT 是我做的一个基于 Gin 和 gRPC 开发的抖音后端项目，包括 API 网关以及一些业务服务，比如 用户 服务实现了用户登陆注册获取用户主页信息的接口。一个简单的流程就是，API 服务接受 RESTful 请求，路由交给相应的 handler 处理，handler 经过参数验证后又传给 RPC 客户端，然后 RPC 客户端通过 rpc 框架发送请求数据给某个服务，并接受响应数据，然后便继续将数据向上传，最后响应 json 序列化的数据。


1. 采用 (HTTP API 层 + RPC Service 层+Dal 层) 项目结构；

2. 使用 x.509 证书对服务间通信进行加密和认证；
   首先安装证书签发工具，创建 CA 配置文件，用来配置根证书的使用场景和具体参数。然后创建证书签名请求文件 CSR 配置文件。然后通过 CFSSL工具创建 CA 证书和私钥，创建的同时会生成 csr 文件，也就是证书签名请求。用于交叉签名或者重新签名。创建完根证书以及私钥之后，创建业务服务的 csr 配置文件，最后利用根证书和私钥创建各服务的 CA 证书和私钥。

3. 使用 [go-grpc-middleware](https://github.com/grpc-ecosystem/go-grpc-middleware)中的日志记录、认证、和恢复；
   拦截器的话客户端和服务端都可以设置，分为流式拦截器和一元拦截器，根据请求调用的类型决定，请求如果是流式的用到的就是流式拦截器。grpc本身的话一种类型的拦截器只能设置一个，不用这个grpc中间件的话只能将所有逻辑耦合在一起，代码结构就不清晰了。具体的话，自己实现一下go-grpc-middleware每个拦截器相应函数类型然后调用就行了。
   日志记录其实就是自己实现一个可以返回zap.Logger实例的 grpc-middleware ZapInterceptor 的拦截器函数， 认证的话用的一个bearer token验证，就是客户端调用时添加token然后在服务端对以authorization为头部，形式为`bearer token`的Token进行验证，使用bearer token是规范了与HTTP请求的对接，毕竟gRPC也可以同时支持HTTP请求。然后恢复的话把gRPC中的panic转成error，从而恢复程序。

4. 使用 **JWT** 进行用户token的校验；
   简单地实现了两个方法，创建 token 解析token
   
5. 使用 **ETCD** 进行服务发现和服务注册；
   etcd采用的是raft算法，

6. 使用 **Minio** 实现视频文件和图片的对象存储；

7. 使用 **Gorm** 对 MySQL 进行 ORM 操作；

8. 使用 **Zipkin** 实现链路跟踪；

9.  数据库表建立了索引和外键约束，对于具有关联性的操作一旦出错立刻回滚，保证数据一致性和安全性；

10. HTTP 服务和 RPC 服务的优雅停止。


## 遇到了哪些困难，怎么解决的？



采用 (HTTP API 层 + RPC Service 层+Dal 层) 项目结构；
使用了 x509 证书对服务间通信进行加密和认证；
使用了 go-grpc-middleware 拦截器做日志记录、认证、和恢复；
使用了 JWT 进行用户token的校验；
使用 ETCD 进行服务发现和服务注册；
使用 Gorm 对 MySQL 进行 ORM 操作，使用 Minio 实现视频文件和图片的对象存储；
使用 Zipkin 实现链路跟踪；
数据库表建立了索引和外键约束，对于具有关联性的操作一旦出错立刻回滚，保证数据一致性和安全性；





## 项目开发

## 项目测试

## 项目部署



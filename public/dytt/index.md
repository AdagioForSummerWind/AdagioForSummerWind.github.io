# dytt

- [dytt](#dytt)
  - [介绍一下项目的架构](#介绍一下项目的架构)
  - [遇到了哪些困难，怎么解决的？](#遇到了哪些困难怎么解决的)
  - [项目开发](#项目开发)
  - [项目测试](#项目测试)
  - [项目部署](#项目部署)
  - [protobuff 的坑](#protobuff-的坑)


# dytt
## 介绍一下项目的架构
DYTT 是我做的一个基于 Gin 和 gRPC 开发的抖音后端项目，包括 API 网关以及一些业务服务，比如 用户 服务实现了用户登陆注册获取用户主页信息的接口。一个简单的流程就是，API 服务接受 RESTful 请求，路由交给相应的 handler 处理，handler 经过参数验证后又传给 RPC 客户端，然后 RPC 客户端通过 rpc 框架发送请求数据给某个服务，并接受响应数据，然后便继续将数据向上传，最后响应 json 序列化的数据。


1. 采用 (HTTP API 层 + RPC Service 层+Dal 层) 项目结构；

2. 使用 x.509 证书对服务间通信进行加密和认证；
   首先安装证书签发工具，创建 CA 配置文件，用来配置根证书的使用场景和具体参数。然后创建证书签名请求文件 CSR 配置文件。然后通过 CFSSL工具创建 CA 证书和私钥，创建的同时会生成 csr 文件，也就是证书签名请求。用于交叉签名或者重新签名。创建完根证书以及私钥之后，创建业务服务的 csr 配置文件，最后利用根证书和私钥创建各服务的 CA 证书和私钥。

3. 使用 [go-grpc-middleware](https://github.com/grpc-ecosystem/go-grpc-middleware)中的日志记录、认证、和恢复；
   拦截器的话客户端和服务端都可以设置，分为流式拦截器和一元拦截器，根据请求调用的类型决定，请求如果是流式的用到的就是流式拦截器。grpc本身的话一种类型的拦截器只能设置一个，不用这个grpc中间件的话只能将所有逻辑耦合在一起，代码结构就不清晰了。具体的话，自己实现一下go-grpc-middleware每个拦截器相应函数类型然后调用就行了。
   日志记录其实就是自己实现一个可以返回zap.Logger实例的 grpc-middleware ZapInterceptor 的拦截器函数， 认证的话采用了bearer令牌认证，bearer token 服务端根据密钥生成。客户端在请求服务端时，必须在请求头中包含Authorization: Bearer 。服务端收到请求后，进行解析并校验。使用bearer token是规范了与HTTP请求的对接，毕竟gRPC也可以同时支持HTTP请求。然后恢复的话把gRPC中的panic转成error，从而恢复程序。

4. 使用 **JWT** 进行用户token的校验；
   简单地实现了两个方法，创建 token 解析token，在用户注册或者登陆时创建token 在需要验证身份的时候进行解析验证
   
5. 使用 **ETCD** 进行服务发现和服务注册；（https://blog.51cto.com/u_15314183/5457223）
   服务实例定义以及一些工具函数、服务注册、服务发现
   先构造一个etcd的结构体，包括名称、地址、版本、和权重（后续做降级熔断的话会用到），工具函数包括注册路径，判断一个服务是否存在之类的
   服务注册：定义注册对象结构体，存储全部的实例信息，包括连接超时时间，etcd服务，clientv3实例等，客户端实例的grant方法能创建lease租约,相当于一个TTL存活时间，用于对etcd客户端和服务端之间进行活性检测。在到达TTL时间之前，etcd服务端不会删除相关租约上绑定的键值对；超过TTL时间就会删除，需要在TTL到来之前续租才能实现etcd服务端和客户端之间的保活。具体注册服务的时候先定义一个node存放服务信息，然后调用Register进行注册。
   服务发现：构造服务发现结构体，用了grpc/resolver这个库做解析器，创建一个基于etcd的resolver。然后有一些操作，比如服务发现，watch机制，更新节点操作，同步获取所有地址信息

6. 使用 **Minio** 实现视频文件和图片的对象存储；
   为什么使用minio：性能比较高，比较适合存储图片、视频之类的，采用golang实现，整个系统运行在用户态。

7. 使用 **Gorm** 对 MySQL 进行 ORM 操作；
   orm object-relational mapping。我觉得最大的优点是能够更好的构建查询，能相对简单地动态构造出复杂的sql语句。单纯用sql的话只是字符串意义上的拼接。
   当然也可以防sql注入，gorm通过占位符的形式一定程度上减少了sql注入
   不足：不能明确sql的内容

8. 使用 **Zipkin** 分布式追踪系统实现链路跟踪；(https://zhuanlan.zhihu.com/p/453190067)
   可以收集服务的一些计时数据，可以用于一些延迟问题的故障排除。
   reporter报告器：将服务之间的请求调用信息报告给zipkin后台，用于生成链路追踪和依赖图。包括http报告器和log报告器，HTTP报告器将请求调用信息封装为span异步报告给zipkin服务器，log报告器将服务调用信息打印到控制台。
   Endpoint: 一个标记信息，标记此服务的服务名以及地址信息，用于zipkin后台画图和分析
   Tracer追踪器：解析服务调用之间的上下文信息context,将链路信息封装为span
   grpc整合zipkin：把创建追踪器的过程封装为一个方法，进行一些初始化即可

9.  数据库表建立了索引和外键约束，对于具有关联性的操作一旦出错立刻回滚，保证数据一致性和安全性；

10. HTTP 服务和 RPC 服务的优雅停止。（https://leileiluoluo.com/posts/golang-shutdown-server-gracefully.html）
    用一个goroutine启动服务，为了不让这个启动过程阻塞后面的优雅停止。
    http服务主要就是使用 http.Server 的 Shutdown 方法结合 signal.Notify 来优雅的终止服务
    初始化一个channel，利用signal.Notify等待系统中断信号，不然就一直阻塞，当中断信号来了的时候，开始优雅关闭，关闭http.server的话直接调用标准库net/http包的Shutdown方法（由文档可知，可以优雅地终止服务，但不会直接中断活跃连接，先关闭所有闲置连接，等待活跃连接闲置后才终止服务）


数据库表建立了索引和外键约束，对于具有关联性的操作一旦出错立刻回滚，保证数据一致性和安全性；

## 遇到了哪些困难，怎么解决的？







## 项目开发

## 项目测试

## 项目部署



## protobuff 的坑

在本地执行
```
protoc -I. --go_out=plugins=grpc:$GOPATH/src ./idl/*.proto
```
时报错
```
--go_out: protoc-gen-go: plugins are not supported; use 'protoc --go-grpc_out=...' to generate gRPC
```
[原因](https://github.com/golang/protobuf/issues/1070)

The google.golang.org/protobuf/cmd/protoc-gen-go program doesn't include gRPC support. Instead, that support will soon be provided by a google.golang.org/grpc/cmd/protoc-gen-go-grpc program. That program is In code review now, but not quite yet available.

The github.com/golang/protobuf/protoc-gen-go program supports gRPC as always, and will continue to do so. Versions v1.4 and newer of that program support the new protobuf reflection features. (Right now, the newest release is v1.4.0-rc.4.)




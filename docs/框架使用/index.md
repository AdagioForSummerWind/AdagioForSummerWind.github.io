# 框架使用

- [gin](#gin)
  - [用过gin是吧？gin是怎么处理请求的？](#用过gin是吧gin是怎么处理请求的)
  - [简介](#简介)
- [你用过gorm？那我直接用sql不也可以吗？为什么用gorm？](#你用过gorm那我直接用sql不也可以吗为什么用gorm)
- [grpc](#grpc)
  - [定义一个服务的流程](#定义一个服务的流程)


> 了解掌握 Gin，Gorm，gRPC 等框架使用，了解 C，Python 等其他编程语言

## gin
###  用过gin是吧？gin是怎么处理请求的？

Gin其实是通过一个context来进行上下文的传递，将这个传递参数，参数返回。
###  简介
Gin 是基于 net/http 包封装的一个 Web 框架，所以它天然就支持 HTTP/HTTPS


##  你用过gorm？那我直接用sql不也可以吗？为什么用gorm？

用 sql 是可以，但是会有sql注入的风险，而gorm是通过占位符的形式来一定程度减少了sql的注入。

除此之外，更方便管理sql语句，构建查询比较方便

支持钩子方法。这些钩子方法可以应用在 Create、Save、Update、Delete、Find 方法中。


## grpc
### 定义一个服务的流程
定义idl文件，包括数据结构和服务接口

gRPC 支持定义 **4 种类型的服务方法**，分别是**简单模式、服务端数据流模式、客户端数据流模式和双向数据流模式。**
- 简单模式（Simple RPC）：是最简单的 gRPC 模式。客户端发起一次请求，服务端响应一个数据。定义格式为 rpc SayHello (HelloRequest) returns (HelloReply) {}。
- 服务端数据流模式（Server-side streaming RPC）：客户端发送一个请求，服务器返回数据流响应，客户端从流中读取数据直到为空。定义格式为 rpc SayHello (HelloRequest) returns (stream HelloReply) {}。
- 客户端数据流模式（Client-side streaming RPC）：客户端将消息以流的方式发送给服务器，服务器全部处理完成之后返回一次响应。定义格式为 rpc SayHello (stream HelloRequest) returns (HelloReply) {}。
- 双向数据流模式（Bidirectional streaming RPC）：客户端和服务端都可以向对方发送数据流，这个时候双方的数据可以同时互相发送，也就是可以实现实时交互 RPC 框架原理。定义格式为 rpc SayHello (stream HelloRequest) returns (stream HelloReply) {}。

使用 protoc 编译工具，并指定使用其 Go 语言插件来生成客户端和服务器代码

创建结构体，实现响应接口的方法就行了




























































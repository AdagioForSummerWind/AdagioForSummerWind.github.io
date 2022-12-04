# interview

## 自我介绍
## 了解rust吗
## grpc协议 为什么json传输比protobuf效率低,不是因为反射
Protocol Buffers（ProtocolBuffer/ protobuf）是 Google 开发的一套对数据结构进行序列化的方法（结构化数据存储格式），可用作（数据）通信协议、数据存储格式等，也是一种更加灵活、高效的数据格式，与 XML、JSON 类似。它的传输性能非常好，所以常被用在一些对数据传输性能要求比较高的系统中，作为数据传输格式。Protocol Buffers 的主要特性有下面这几个。
- 更快的数据传输速度：protobuf 在传输时，会将数据序列化为二进制数据，和 XML、JSON 的文本传输格式相比，这可以节省大量的 IO 操作，从而提高数据传输速度。
- 跨平台多语言：protobuf 自带的编译工具 protoc 可以基于 protobuf 定义文件，编译出不同语言的客户端或者服务端，供程序直接调用，因此可以满足多语言需求的场景。
- 具有非常好的扩展性和兼容性，可以更新已有的数据结构，而不破坏和影响原有的程序。
- 基于 IDL 文件定义服务，通过 proto3 工具生成指定语言的数据结构、服务端和客户端接口。

JSON 是典型的 Key-Value 方式，没有数据类型，是一种文本型序列化框架。JSON 进行序列化的额外空间开销比较大

**Protobuf序列化后体积相比 JSON、Hessian 小很多；IDL 能清晰地描述语义**，所以足以帮助并保证应用程序之间的类型不会丢失，无需类似 XML 解析器；**序列化反序列化速度很快**，不需要通过反射获取类型；**消息格式升级和兼容性不错**，可以做到向后兼容。

## http协议和rpc区别
## etcd 是什么为什么用 redis 不行
## 进程与协程
## mysql索引 具体索引如何加快查找速度
## docker compose
## 什么是k8s，与docker compose的区别
## 10 是在linux上进行开发吗
## 了解哪些shell命令
## curl top netstat ip 什么协议
## curl 是 http 还是 https 
## 可以查看端口是否能够访问的命令


# DistributedSystem_HongweiDu_01

# Intr
HongweiDu(堵宏伟)
## Topics
- Introduction
- Distributed Systems models
- Distributed Time and Clock Synchronization
- Socket Communication
- Remote Method Invocation(RMI)
- Group Communication
- Mutual exclusion & election algorithms
- Replication
- ……
## Motivation
- Resource sharing
    - Computers connected by the network and share resources.
    - Hardware sharing, software sharing,  data sharing, service sharing.media stream sharing.

- Collaborative computing
    - Parallel computing, distributed computing
## Definition
A distributed system is defined as one in which components at networked computers communicate and coordinate their actions only by passing messages.
## Features
The distributed system features:
- Concurrency
    - Multi-process and multi-threads concurrently execute, share resources.
- No global clock
    - Program coordination depend on message passing.
- Independent failure
    - Some processes failure,  can not be known by other processes.
## Challenges
### Heterogeneity
- Middleware
    - Apply to  a software layer that provides a program abstraction as well as masking the heterogeneity of the underlying layers (networks, hardware, operating systems and programming languages). 
    - Example: Java RMI

- Mobile code
    - Program code that can transferred from one computer to another and run at the destination.  Example: Java Applet.The Java virtual machine (JVM) provide a way of making code executable on a variety of host computers.
### Openness
- Computer System Openness
    - Determines whether the system can be extended and reimplemented in various ways.  For example: UNIX.
- Distributed System Openness
    - The degree to which new resource-sharing services can be added and be made available for use by a variety of client programs.
    - RFC (‘Request For Comments’)
### Security
- Confidentiality(机密性）
    - Protection against disclosure to unauthorized individuals.
- Integrity （完整性）
    - Protection against alteration or corruption.
    - e.g.  Checksum (校验和）
- Availability（可用性）
    - Protection against interference with the means to access the resources.
### Scalability(可拓展性)
- Controlling the cost of physical resources
    - As the demand for a resource grows, it should be possible to extend the system, at reasonable cost, to meet it.
- Controlling the performance loss
    - Consider the management of a set of data whose size is proportional to the number of users or resources in the system.
- Preventing software resources running out
    - IPv4, IPv6…..
- Avoiding performance bottlenecks
    - In general, algorithms should be decentralized to avoid having performance bottlenecks. 
### Failure handling
- Detecting failures 
    - Some failure can be detected.
- Masking failures
    - Some failure that have been detected can be hidden or made less severe.
- Tolerating failures
    - Most of the services in the Internet do exhibit failures.
- Recovering from failures
    - Recovery involves the design of software so that the state of permanent data can be recovered or ‘roll back’ after a server has crashed.
- Redundancy
    - Services can be made to tolerate failures by the use of redundant components.
### Concurrency
- Consistent
    - Multi-thread concurrent access the sharing resource.
- Performance
### Transparency
- Access transparency: enables local and remote resources to be accessed using identical operations.
- Location transparency: enables resources to be accessed without knowledge of their physical or network location (for example, which building or IP address).
- Concurrency transparency: enables several processes to operate concurrently using shared resources without interference between them.
- Replication transparency: enables multiple instances of resources to be used to increase reliability and performance without knowledge of the replicas by users or application programmers.
- Failure transparency: enables the concealment of faults, allowing users and application programs to complete their tasks despite the failure of hardware or software components.
- Mobility transparency: allows the movement of resources and clients within a system without affecting the operation of users or programs.
- Performance transparency: allows the system to be reconfigured to improve performance as loads vary.
- Scaling transparency: allows the system and applications to expand in scale without change to the system structure or the application algorithms.
### Quality of Service
- Reliability
- Security
- Performance
- Adaptability
## Conclusion
- Distributed system is everywhere.
- The motivation of constructing a distributed system is resource sharing and collaborative computing
- Distributed system features.
    - Concurrency
    - No global clock
    - Independent failure
- Distributed system challenges.
    - Heterogeneity
    - Openness
    - Security
    - Scalability
    - Failure handling
    - Concurrency
    - Transparency
    - Quality of Service


## 复习
- 分布式系统 1.4  16:00
- 8-15小题，英文题目
- 包括计算题、证明题，最好的答题方法：记住答题要点。
### 绪论：
- 1. 分布式系统的产生、动机：共享资源，
- 2. 分布式系统特点：并发性、没有全局时钟、独立故障；
- 3. 分布式系统定义：分布式系统定义为网络计算机上的组件仅通过传递消息进行通信和协调其动作的系统。
- 4. 分布式系统挑战：
    - 异质性（不同设备应用场景等造成异质性问题），可通过中间件和移动代码解决。
    - 公开性：可以添加新资源共享服务并使其可供各种客户端程序使用的程度。
    - 并发性：同一设备进程之间的并发性，不同设备进程之间的并发性
    - 透明性，所有系统里所发生的对于用户设备来说是透明的。
    - 服务质量：分布式系统建立在不稳定的网络之中，增加服务器解决的是负载问题，单台服务器承受不了，所以要用轮询的机制随机地给一台服务器对客户端进行访问。整个网络问题，不稳定，
### 系统模型，
画系统架构图，描述系统，软硬件：机械式的物理模型。体系结构模型。实体：对象、组件、服务……实体通讯方法：直接式、RMI/RPC、间接通讯模式。分布式方式：水平式分布方式，垂直分布方式。轮询机构。两种实现的方式：接口、逻辑结构（通常放在服务器端）、数据。基础模型：故障模型、交互模型、安全模型……
### 时钟同步
- 物理时间：时间是怎么测量的，每一个时钟的标准是什么。如何调整计算机的时钟。线性补偿函数。两个算法：知道大体原理。NTP协议。及相关计算。
- 逻辑时间。逻辑时间本身是做时间顺序、时间顺序。对于相同时间里的事件，我们希望通过时间戳才决定事件之间的顺序结构。最终通过向量时钟，（**给你一个因果关系的组播，一张图画出来了，随便扔出两个消息，告诉两三个向量，问你哪些有因果关系请解释原因**）。**或者说已知一个向量时钟，有些有，有些没有需要填写，对比他们之间的关系。解释为什么相关**。对于每一个向量时钟之间的元素。属性之间的关系。要么全小于、要么全大于。既小于又大于就没有什么关系了，并发的，一个练习题。
### IPC Socket operations：
本来是重点，考怎么创建连接，但是做了pro。不考编程。因为pro普遍做的不好。**但是所有它所用的系统的sockets操作针对于客户端和用户端。这个需要知道。**
### RPC
就过了，不考。因为第二个项目没做。给你小段程序，去设计服务器端的程序。再生成一个接口程序。
### 群组通信：组播式的通讯，因为所有因特网里面访问的主要是组播通讯。
- 组播通讯：原子组播、稳定组播、不稳定组播。对于原子组播：**怎么样满足原子性的、为什么要满足原子性的(两种方法：发送方和接收方维护一个持久的日志;重新定义组**)
- 对于消息的顺序，全局时钟的顺序。全序、偏序、因果顺序。对于消息，判断消息之间接受顺序，遵循了全局时钟的按照发送之间的顺序结构在接受端按顺序的接收。

###  **互斥选举算法**。
每个方法，要知道对于发送者来说对于接收者来说关注的是什么，**消息类型是什么**
- 互斥
    - 集中式算法
    - 令牌环算法
    - Ricart & Agrawala 算法
    - 分布式互斥
- 选举
    - 欺凌算法
    - 环选举算法
    - Robert & Chang 算法
### 复制、一致性：
- 数据一致性：严格一致性、顺序一致性、因果一致性等模型
    - **会怎么样考**：可能会给你几段的程序/进程（五个进程，上面有不同的操作），问你哪些是符合什么样的模型。解释为什么。
    - 一定会考，比如说：在逻辑时间里面，判断逻辑关系，问你这事件里哪些符合顺序一致性、因果一致性？每个一致性基本概念要了解。
- 面向客户端的一致性：
    - 四个规则：单调读、单调写、读后写、写后读
    - **什么在传播消息，传播什么样的消息,消息类型**
        - 1. 无效化消息（传播无效化消息干什么：通知复制服务器，哪些数据项无效）**（消息可能是组播的方式传出去的，需要知道所有接收节点的标识ID，一般协调者知道）
        - 2. 数据消息（完整的更新数据）
        - 3. 更新操作消息（一般的主动式的复制，发送更新的操作）
    - 怎么样传播消息：
        - push，组播的通讯方式
        - pull，单播的通讯方式
    - 一致性协议
        - 基于主要的协议
        - 复制写协议（两种）
        - 缓存一致性协议：讨论什么时候去验证这个数据是一致的，访问前还是访问后还是无验证
### internetworking因特网物流：
- 虽然没学计算机网络，但应该知道基本的协议，**考了，没考深入的**
- CIDR - 无类别域间路由
- NAT - 网络地址转换
    - 把内部网络地址转化为外部网络地址进行通讯，要知道怎么转化，端口分配，怎么回信息
- DHCP - 动态主机配置协议
    - 是ARP协议的reverse（返回），分配一个内网IP地址给你，怎么分配。**要知道有哪几段是内网地址，三个范围**，哪些用来做网络地址、哪些用来分配的地址，哪些用来做本地端的地址。
- ARP - 地址解析问题
- OSPF - 内部网关路由协议
- BGP - 外部网关路由协议
    - 链路状态协议、距离向量协议，他们怎么做的路由，适用于哪些类型的网络？**给一个示意图，计算如果这个节点到达另外一个节点花费了多少，……这是去年的题**  **在一个DNS里面，画一个示意图，哪种场景适用于……**
### DNS不考
### 文件系统
- 本地端文件系统和分布式文件系统的区别在哪
- 所有分布式系统的组成有什么，三个
- 本地端的文件系统里面如何去做路径解析的，过程中访问了多少次硬盘，从根目录逐层次访问
- 什么是一个超级块，定义
- 文件访问的内部架构是什么，访问过程中三个不同的……table
- 挂卸载机制
- ……
### 事务处理
- 原子交易的定义
- RM-ODP 属性
- 如何实现事务的原子性？
    - 故障恢复（保证一无所有）
        - 意向清单法
        - 影子版方法
    - **并发控制（保证序列化）**（优缺点）
        - 两相锁定
        - 时间戳排序
        - 乐观法
    - **给一个示意图，决定到底符合什么样的顺序呢？**
### 网络搜索
- 蜘蛛的架构是什么，操作是什么，（操作：不断地把url加入index）,通过广度优先的方法进行搜索。
- 页面排序：考虑了两个因素：
    - 指向它的网页的排名
        - 越高越好
    - 指向它的网页中传出链接的数量
        - 越少越好
- 排名计算
- 倒排文件：搜索索引









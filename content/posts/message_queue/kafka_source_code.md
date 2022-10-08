---
title: "Kafka_source_code"
date: 2022-07-17T16:26:15+08:00
lastmod: 2022-07-17
tags: [kafka]
categories: [Message queue]
slug: kafka source code
draft: true
---
# kafka核心源码解读
胡夕 2020-04-13

![img](https://static001.geekbang.org/resource/image/69/3c/6961bc3841b09586cfayyf97f1fc803c.jpg?wh=750*4027)
## 日志

## 01 | 日志段：保存消息文件的对象是怎么实现的？

### 总结
今天，我们对 Kafka 日志段源码进行了重点的分析，包括日志段的 append 方法、read 方法和 recover 方法。append 方法：我重点分析了源码是如何写入消息到日志段的。你要重点关注一下写操作过程中更新索引的时机是如何设定的。read 方法：我重点分析了源码底层读取消息的完整流程。你要关注下 Kafka 计算待读取消息字节数的逻辑，也就是 maxSize、maxPosition 和 startOffset 是如何共同影响 read 方法的。recover 方法：这个操作会读取日志段文件，然后重建索引文件。再强调一下，这个操作在执行过程中要读取日志段文件。因此，如果你的环境上有很多日志段文件，你又发现 Broker 重启很慢，那你现在就知道了，这是因为 Kafka 在执行 recover 的过程中需要读取大量的磁盘文件导致的。你看，这就是我们读取源码的收获。

![img](https://static001.geekbang.org/resource/image/15/80/158bed3c92e7205fc450bb8b2d136480.jpg?wh=2521*1875)

这三个方法是日志段对象最重要的功能。你一定要仔细阅读它们，尽量做到对源码中每行代码的作用都了然于心。没有什么代码是读一遍不能理解的，如果有，那就再多读几遍。另外，我希望你特别关注下 append 和 read 方法，它们将是后面我们讨论日志对象时重点会用到的两个方法。毕竟，读写日志是 Kafka 最常用的操作，而日志读取底层调用的就是日志段的这两个方法。


## 02 | 日志（上）：日志究竟是如何加载日志段的？


### 总结
今天，我重点向你介绍了 Kafka 的 Log 源码，主要包括：Log 文件的源码结构：你可以看下下面的导图，它展示了 Log 类文件的架构组成，你要重点掌握 Log 类及其相关方法。加载日志段机制：我结合源码重点分析了日志在初始化时是如何加载日志段的。前面说过了，日志是日志段的容器，弄明白如何加载日志段是后续学习日志段管理的前提条件。

![img](https://static001.geekbang.org/resource/image/dd/fc/dd2bf4882021d969accb14c0017d9dfc.jpg?wh=2543*3019)

总的来说，虽然洋洋洒洒几千字，但我也只讲了最重要的部分。我建议你多看几遍 Log.scala 中加载日志段的代码，这对后面我们理解 Kafka Broker 端日志段管理原理大有裨益。在下节课，我会继续讨论日志部分的源码，带你学习常见的 Kafka 日志操作。

## 03 | 日志（下）：彻底搞懂Log对象的常见操作

### 总结
今天，我重点讲解了 Kafka 的 Log 对象以及常见的操作。我们复习一下。高水位管理：Log 对象定义了高水位对象以及管理它的各种操作，主要包括更新和读取。日志段管理：作为日志段的容器，Log 对象保存了很多日志段对象。你需要重点掌握这些日志段对象被组织在一起的方式以及 Kafka Log 对象是如何对它们进行管理的。关键位移值管理：主要涉及对 Log Start Offset 和 LEO 的管理。这两个位移值是 Log 对象非常关键的字段。比如，副本管理、状态机管理等高阶功能都要依赖于它们。读写操作：日志读写是实现 Kafka 消息引擎基本功能的基石。虽然你不需要掌握每行语句的含义，但你至少要明白大体的操作流程。

讲到这里，Kafka Log 部分的源码我就介绍完了。我建议你特别关注下高水位管理和读写操作部分的代码（特别是后者），并且结合我今天讲的内容，重点分析下这两部分的实现原理。最后，我用一张思维导图来帮助你理解和记忆 Log 源码中的这些常见操作：

![img](https://static001.geekbang.org/resource/image/d0/99/d0cb945d7284f09ab2b6ffa764190399.jpg?wh=2573*1902)


## 04 | 索引（上）：改进的二分查找算法在Kafka索引的应用

### 总结
今天，我带你详细学习了 Kafka 中的索引机制，以及社区如何应用二分查找算法实现快速定位索引项。有两个重点，你一定要记住。AbstractIndex：这是 Kafka 所有类型索引的抽象父类，里面的 mmap 变量是实现索引机制的核心，你一定要掌握它。改进版二分查找算法：社区在标准原版的基础上，对二分查找算法根据实际访问场景做了定制化的改进。你需要特别关注改进版在提升缓存性能方面做了哪些努力。改进版能够有效地提升页缓存的使用率，从而在整体上降低物理 I/O，缓解系统负载瓶颈。你最好能够从索引这个维度去思考社区在这方面所做的工作。

![img](https://static001.geekbang.org/resource/image/31/22/31e35198818b0bbc05ef333b5897b022.jpg?wh=2581*3727)

实际上，无论是 AbstractIndex 还是它使用的二分查找算法，它们都属于 Kafka 索引共性的东西，即所有 Kafka 索引都具备这些特点或特性。那么，你是否想了解不同类型索引之间的区别呢？比如位移索引和时间戳索引有哪些异同之处。这些问题的答案我会在下节课揭晓，你千万不要错过。


## 05 | 索引（下）：位移索引和时间戳索引的区别是什么？


### 区别
讲到这里，这节课就接近尾声了。最后，我用一张表格汇总下 OffsetIndex 和 TimeIndex 的特点和区别，希望能够帮助你更好地理解和消化今天的重点内容。

![img](https://static001.geekbang.org/resource/image/a3/78/a359ce4e81eb073a9ebed2979082b578.jpg?wh=3860*2007)

### 总结
今天，我带你详细分析了 OffsetIndex 和 TimeIndex，以及它们的不同之处。虽然 OffsetIndex 和 TimeIndex 是不同类型的索引，但 Kafka 内部是把二者结合使用的。通常的流程是，先使用 TimeIndex 寻找满足时间戳要求的消息位移值，然后再利用 OffsetIndex 去定位该位移值所在的物理文件位置。因此，它们其实是合作的关系。最后，我还想提醒你一点：不要对索引文件做任何修改！我碰到过因用户擅自重命名索引文件，从而导致 Broker 崩溃无法启动的场景。另外，虽然 Kafka 能够重建索引，但是随意地删除索引文件依然是一个很危险的操作。在生产环境中，我建议你尽量不要执行这样的操作。

## 请求处理模块

## 06 | 请求通道：如何实现Kafka请求队列？

### 总结
好了，又到了总结时间。今天，我带你阅读了 Kafka 请求队列的实现源码。围绕这个问题，我们学习了几个重点内容。Request：定义了 Kafka Broker 支持的各类请求。Response：定义了与 Request 对应的各类响应。RequestChannel：实现了 Kafka Request 队列。监控指标：封装了与 Request 队列相关的重要监控指标。

![img](https://static001.geekbang.org/resource/image/4b/15/4bf7b31f368743496018b3f21a528b15.jpg?wh=2284*1921)

希望你结合今天所讲的内容思考一下，Request 和 Response 在请求通道甚至是 SocketServer 中的流转过程，这将极大地帮助你了解 Kafka 是如何处理外部发送的请求的。当然，如果你觉得这个有难度，也不必着急，因为后面我会专门用一节课来告诉你这些内容。


## 07 | SocketServer（上）：Kafka到底是怎么应用NIO实现网络通信的？

### 总结
今天，我带你了解了 Kafka 网络通信层的全貌，大致介绍了核心组件 SocketServer，还花了相当多的时间研究 SocketServer 下的 Acceptor 和 Processor 线程代码。我们来简单总结一下。网络通信层由 SocketServer 组件和 KafkaRequestHandlerPool 组件构成。SocketServer 实现了 Reactor 模式，用于高性能地并发处理 I/O 请求。SocketServer 底层使用了 Java 的 Selector 实现 NIO 通信。

![img](https://static001.geekbang.org/resource/image/41/51/41317d400ed096bbca8efadf43186f51.jpg?wh=2349*2028)

在下节课，我会重点介绍 SocketServer 处理不同类型 Request 所做的设计及其对应的代码。这是社区为了提高 Broker 处理控制类请求的重大举措，也是为了改善 Broker 一致性所做的努力，非常值得我们重点关注。


## 08 | SocketServer（中）：请求还要区分优先级？

### 总结
好了，我们来小结一下。今天，我们重点学习了社区实现不同类型请求优先级的方法。结合监听器的概念，我们深入到 SocketServer 的源码中，分析了 Data plane 和 Control plane 的实现原理。我们来回顾一下这节课的重点。

Data plane：负责处理数据类请求，这类请求通常不需要高优先级处理。Control plane：负责处理控制类请求，这类请求需要高优先级处理。监听器：Kafka 允许 Broker 定义多套监听器，每套监听器可用于 Data plane 或 Control plane。优先级实现原理：你要知道的是，社区设计了两套资源分别处理 Data plane 和 Control plane 请求。

![img](https://static001.geekbang.org/resource/image/ee/8c/eec8d1027bf77384d8d2fb8116af948c.jpg?wh=2591*1476)

下节课，我会带你串联起网络 I/O 层的所有组件，并且结合源码，带你深入理解一个请求在 Kafka 中是如何被处理的。敬请期待。


## 09 | SocketServer（下）：请求处理全流程源码分析


### 总结
今天，我们学习了 KafkaRequestHandlerPool 线程池及其下辖的 KafkaRequestHandler 线程，该线程就是 Kafka 社区所称的 I/O 线程。另外，我结合源代码把 Kafka 的请求处理流程串讲了一遍。我们来回顾下这节课的重点。KafkaRequestHandler：I/O 线程，负责处理 Processor 线程下发的 Request 对象。KafkaRequestHandlerPool：创建和管理一组 KafkaRequestHandler 线程。请求处理流程：总共分为 6 步。

Clients 或其他 Broker 通过 Selector 机制发起创建连接请求。Processor 线程接收请求，并将其转换成可处理的 Request 对象。Processor 线程将 Request 对象放入 Request 队列。KafkaRequestHandler 线程从 Request 队列中取出待处理请求，并进行处理。KafkaRequestHandler 线程将 Response 放回到对应 Processor 线程的 Response 队列。Processor 线程发送 Response 给 Request 发送方。

![img](https://static001.geekbang.org/resource/image/45/c4/458e65efcab7964911bb6a1755fa89c4.jpg?wh=2449*1499)

其实，今天在谈到 Request 逻辑执行的时候，我卖了个关子——我提到，KafkaApis 是请求逻辑的真正处理方法。也就是说，所有类型的请求处理逻辑都封装在 KafkaApis 文件下，但我并没有深入地去讲它。下节课，我会重点和你聊聊这个 KafkaApis 类。我一直认为，该类是查看所有 Kafka 源码的首要入口类，绝对值得我们花一整节课的时间去学习。

## 10 | KafkaApis：Kafka最重要的源码入口，没有之一

### 总结
好了， 我们总结一下 KafkaApis 类的要点。如前所述，我们重点学习了 KafkaApis 类的定义及其重要方法 handle。下面这些关键知识点，希望你能掌握。KafkaApis 是 Broker 端所有功能的入口，同时关联了超多的 Kafka 组件。它绝对是你学习源码的第一入口。面对庞大的源码工程，如果你不知道从何下手，那就先从 KafkaApis.scala 这个文件开始吧。handle 方法封装了所有 RPC 请求的具体处理逻辑。每当社区新增 RPC 协议时，增加对应的 handle×××Request 方法和 case 分支都是首要的。sendResponse 系列方法负责发送 Response 给请求发送方。发送 Response 的逻辑是将 Response 对象放置在 Processor 线程的 Response 队列中，然后交由 Processor 线程实现网络发送。authorize 方法是请求处理前权限校验层的主要逻辑实现。你可以查看一下[官方文档](https://docs.confluent.io/current/kafka/authorization.html)，了解一下当前都有哪些权限，然后对照着具体的方法，找出每类 RPC 协议都要求 Clients 端具备什么权限。

![img](https://static001.geekbang.org/resource/image/9e/4c/9ebd3f25518e387a7a60200a8b62114c.jpg?wh=2380*3004)

至此，关于 Kafka 请求处理模块的内容，我们就全部学完了。在这个模块中，我们先从 RequestChannel 入手，探讨了 Kafka 中请求队列的实现原理，之后，我花了两节课的时间，重点介绍了 SocketServer 组件，包括 Acceptor 线程、Processor 线程等子组件的源码以及请求被处理的全流程。今天，我们重点研究了 KafkaApis 类这个顶层的请求功能处理逻辑入口，补齐了请求处理的最后一块“拼图”。我希望你能够把这个模块的课程多看几遍，认真思考一下这里面的关键实现要点，彻底搞明白 Kafka 网络通信的核心机制。从下节课开始，我们将进入鼎鼎有名的控制器（Controller）组件的源码学习。我会花 5 节课的时间，带你深入学习 Controller 的方方面面，敬请期待。

## controller 模块
## 11 | Controller元数据：Controller都保存有哪些东西？有几种状态？

### 总结
今天，我们揭开了 Kafka 重要组件 Controller 的学习大幕。我给出了 Controller 模块的学习路线，还介绍了 Controller 的重要元数据。Controller 元数据：Controller 当前定义了 17 种元数据，涵盖 Kafka 集群数据的方方面面。ControllerContext：定义元数据以及操作它们的类。关键元数据字段：最重要的元数据包括 offlinePartitionCount、liveBrokers、partitionAssignments 等。ControllerContext 工具方法：ControllerContext 类定义了很多实用方法来管理这些元数据信息。

下节课，我们将学习 Controller 是如何给 Broker 发送请求的。Controller 与 Broker 进行交互与通信，是 Controller 奠定王者地位的重要一环，我会向你详细解释它是如何做到这一点的。

## 12 | ControllerChannelManager：Controller如何管理请求发送？

### 总结
今天，我结合 ControllerChannelManager.scala 文件，重点分析了 Controller 向 Broker 发送请求机制的实现原理。Controller 主要通过 ControllerChannelManager 类来实现与其他 Broker 之间的请求发送。其中，ControllerChannelManager 类中定义的 RequestSendThread 是主要的线程实现类，用于实际发送请求给集群 Broker。除了 RequestSendThread 之外，ControllerChannelManager 还定义了相应的管理方法，如添加 Broker、移除 Broker 等。通过这些管理方法，Controller 在集群扩缩容时能够快速地响应到这些变化，完成对应 Broker 连接的创建与销毁。我们来回顾下这节课的重点。

Controller 端请求：Controller 发送三类请求给 Broker，分别是 LeaderAndIsrRequest、StopReplicaRequest 和 UpdateMetadataRequest。RequestSendThread：该线程负责将请求发送给集群中的相关或所有 Broker。请求阻塞队列 +RequestSendThread：Controller 会为集群上所有 Broker 创建对应的请求阻塞队列和 RequestSendThread 线程。

其实，今天讲的所有东西都只是这节课的第二张图中“消费者”的部分，我们并没有详细了解请求是怎么被放到请求队列中的。接下来，我们就会针对这个问题，深入地去探讨 Controller 单线程的事件处理器是如何实现的。

![img](https://static001.geekbang.org/resource/image/00/b9/00fce28d26a94389f2bb5e957b650bb9.jpg?wh=2308*1068)

## 13 | ControllerEventManager：变身单线程后的Controller如何处理事件？

### 总结
今天，我们重点学习了 Controller 端的单线程事件队列实现方式，即 ControllerEventManager 通过构建 ControllerEvent、ControllerState 和对应的 ControllerEventThread 线程，并且结合专属事件队列，共同实现事件处理。我们来回顾下这节课的重点。ControllerEvent：定义 Controller 能够处理的各类事件名称，目前总共定义了 25 类事件。ControllerState：定义 Controller 状态。你可以认为，它是 ControllerEvent 的上一级分类，因此，ControllerEvent 和 ControllerState 是多对一的关系。ControllerEventManager：Controller 定义的事件管理器，专门定义和维护专属线程以及对应的事件队列。ControllerEventThread：事件管理器创建的事件处理线程。该线程排他性地读取事件队列并处理队列中的所有事件。

![img](https://static001.geekbang.org/resource/image/4e/26/4ec79e1ff2b83d0a1e850b6acf30b226.jpg?wh=2388*1033)

下节课，我们将正式进入到 KafkaController 的学习。这是一个有着 2100 多行的大文件，不过大部分的代码都是实现那 27 类 ControllerEvent 的处理逻辑，因此，你不要被它吓到了。我们会先学习 Controller 是如何选举出来的，后面会再详谈 Controller 的具体作用。

## 14 | Controller选举是怎么实现的？


### 总结
今天，我们梳理了 Controller 选举的全过程，包括 Controller 如何借助 ZooKeeper 监听器实现监听 Controller 节点，以及 Controller 的选举触发场景和完整流程。我们来回顾一下这节课的重点。Controller 依赖 ZooKeeper 实现 Controller 选举，主要是借助于 /controller 临时节点和 ZooKeeper 的监听器机制。Controller 触发场景有 3 种：集群启动时；/controller 节点被删除时；/controller 节点数据变更时。源码最终调用 elect 方法实现 Controller 选举。

下节课，我将带你学习 Controller 的其他重要功能，包括它如何管理 Broker 和副本等。你千万不要错过。


## 15 | 如何理解Controller在Kafka集群中的作用？


### 总结
今天，我们学习了 Controller 的两个主要功能：管理集群 Broker 成员和主题。这两个功能是 Controller 端提供的重要服务。我建议你仔细地查看这两部分的源码，弄明白 Controller 是如何管理集群中的重要资源的。针对这些内容，我总结了几个重点，希望可以帮助你更好地理解和记忆。

集群成员管理：Controller 负责对集群所有成员进行有效管理，包括自动发现新增 Broker、自动处理下线 Broker，以及及时响应 Broker 数据的变更。主题管理：Controller 负责对集群上的所有主题进行高效管理，包括创建主题、变更主题以及删除主题，等等。对于删除主题而言，实际的删除操作由底层的 TopicDeletionManager 完成。

![img](https://static001.geekbang.org/resource/image/00/37/0035a579a02def8f5234831bf0857f37.jpg?wh=2250*1149)

接下来，我们将进入到下一个模块：状态机模块。在该模块中，我们将系统学习 Kafka 提供的三大状态机或管理器。Controller 非常依赖这些状态机对下辖的所有 Kafka 对象进行管理。在下一个模块中，我将带你深入了解分区或副本在底层的状态流转是怎么样的，你一定不要错过。

## 状态机模块
## 16 | TopicDeletionManager： Topic是怎么被删除的？

### 总结
今天，我们主要学习了 TopicDeletionManager.scala 中关于主题删除的代码。这里有几个要点，需要你记住。

在主题删除过程中，Kafka 会调整集群中三个地方的数据：ZooKeeper、元数据缓存和磁盘日志文件。删除主题时，ZooKeeper 上与该主题相关的所有 ZNode 节点必须被清除；Controller 端元数据缓存中的相关项，也必须要被处理，并且要被同步到集群的其他 Broker 上；而磁盘日志文件，更是要清理的首要目标。这三个地方必须要统一处理，就好似我们常说的原子性操作一样。现在回想下开篇提到的那个“秘籍”，你就会发现它缺少了非常重要的一环，那就是：无法清除 Controller 端的元数据缓存项。因此，你要尽力避免使用这个“大招”。DeletionClient 接口的作用，主要是操作 ZooKeeper，实现 ZooKeeper 节点的删除等操作。TopicDeletionManager，是在 KafkaController 创建过程中被初始化的，主要通过与元数据缓存进行交互的方式，来更新各类数据。

![img](https://static001.geekbang.org/resource/image/3a/3c/3a47958f44b81cef7b502da53495ef3c.jpg?wh=2250*1332)

今天我们看到的代码中出现了大量的 replicaStateMachine 和 partitionStateMachine，其实这就是我今天反复提到的副本状态机和分区状态机。接下来两讲，我会逐步带你走进它们的代码世界，让你领略下 Kafka 通过状态机机制管理副本和分区的风采。

## 17 | ReplicaStateMachine：揭秘副本状态机实现原理


### 总结
今天，我们重点学习了 Kafka 的副本状态机实现原理，还仔细研读了这部分的源码。我们简单回顾一下这节课的重点。副本状态机：ReplicaStateMachine 是 Kafka Broker 端源码中控制副本状态流转的实现类。每个 Broker 启动时都会创建 ReplicaStateMachine 实例，但只有 Controller 组件所在的 Broker 才会启动它。副本状态：当前，Kafka 定义了 7 类副本状态。同时，它还规定了每类状态合法的前置状态。handleStateChanges：用于执行状态转换的核心方法。底层调用 doHandleStateChanges 方法，以 7 路 case 分支的形式穷举每类状态的转换逻辑。

![img](https://static001.geekbang.org/resource/image/27/29/272df3f3b024c34fde619e122eeba429.jpg?wh=2250*1701)

下节课，我将带你学习 Kafka 中另一类著名的状态机：分区状态机。掌握了这两个状态机，你就能清楚地知道 Kafka Broker 端管理分区和副本对象的完整流程和手段了。事实上，弄明白了这两个组件之后，Controller 负责主题方面的所有工作内容基本上都不会难倒你了。

## 18 | PartitionStateMachine：分区状态转换如何实现？

### 总结
今天，我们重点学习了 PartitionStateMachine.scala 文件的源码，主要是研究了 Kafka 分区状态机的构造原理和工作机制。学到这里，我们再来回答开篇面试官的问题，应该就不是什么难事了。现在我们知道了，Kafka 目前提供 4 种 Leader 选举策略，分别是分区下线后的 Leader 选举、分区执行副本重分配时的 Leader 选举、分区执行 Preferred 副本 Leader 选举，以及 Broker 下线时的分区 Leader 选举。这 4 类选举策略在选择 Leader 这件事情上有着类似的逻辑，那就是，它们几乎都是选择当前副本有序集合中的、首个处于 ISR 集合中的存活副本作为新的 Leader。当然，个别选举策略可能会有细小的差别，你可以结合我们今天学到的源码，课下再深入地研究一下每一类策略的源码。我们来回顾下这节课的重点。

PartitionStateMachine 是 Kafka Controller 端定义的分区状态机，负责定义、维护和管理合法的分区状态转换。每个 Broker 启动时都会实例化一个分区状态机对象，但只有 Controller 所在的 Broker 才会启动它。Kafka 分区有 4 类状态，分别是 NewPartition、OnlinePartition、OfflinePartition 和 NonExistentPartition。其中 OnlinPartition 是分区正常工作时的状态。NewPartition 是未初始化状态，处于该状态下的分区尚不具备选举 Leader 的资格。Leader 选举有 4 类场景，分别是 Offline、Reassign、Preferrer Leader Election 和 ControlledShutdown。每类场景都对应于一种特定的 Leader 选举策略。handleStateChanges 方法是主要的入口方法，下面调用 doHandleStateChanges 私有方法实现实际的 Leader 选举功能。


下个模块，我们将来到 Kafka 延迟操作代码的世界。在那里，你能了解 Kafka 是如何实现一个延迟请求的处理的。另外，一个 O(N) 时间复杂度的时间轮算法也等候在那里，到时候我们一起研究下它！

## 延迟操作模块
## 19 | TimingWheel：探究Kafka定时器背后的高效时间轮算法

### 总结
今天，我简要介绍了时间轮机制，并结合代码重点讲解了分层时间轮在 Kafka 中的代码实现。Kafka 正是利用这套分层时间轮机制实现了对于延迟请求的处理。在源码层级上，Kafka 定义了 4 个类来构建整套分层时间轮体系。

TimerTask 类：建模 Kafka 延时请求。它是一个 Runnable 类，Kafka 使用一个单独线程异步添加延时请求到时间轮。TimerTaskEntry 类：建模时间轮 Bucket 下延时请求链表的元素类型，封装了 TimerTask 对象和定时任务的过期时间戳信息。TimerTaskList 类：建模时间轮 Bucket 下的延时请求双向循环链表，提供 O(1) 时间复杂度的请求插入和删除。TimingWheel 类：建模时间轮类型，统一管理下辖的所有 Bucket 以及定时任务。

![img](https://static001.geekbang.org/resource/image/ae/8d/ae956dfc9f494be6c50440c347f5fc8d.jpg?wh=2250*3068)

在下一讲中，我们将继续学习 Kafka 延时请求，以及管理它们的 DelayedOperation 家族的源码。只有了解了 DelayedOperation 及其具体实现子类的代码，我们才能完整地了解，当请求不能被及时处理时，Kafka 是如何应对的。在分布式系统中，如何优雅而高效地延迟处理任务是摆在设计者面前的难题之一。我建议你好好学习下这套实现机制在 Kafka 中的应用代码，活学活用，将其彻底私有化，加入到你的工具箱中。


## 20 | DelayedOperation：Broker是怎么延时处理请求的？

### 总结
今天，我们重点学习了分层时间轮的上层组件，包括 Timer 接口及其实现类 SystemTimer、DelayedOperation 类以及 DelayedOperationPurgatory 类。你基本上可以认为，它们是逐级被调用的关系，即 DelayedOperation 调用 SystemTimer 类，DelayedOperationPurgatory 管理 DelayedOperation。它们共同实现了 Broker 端对于延迟请求的处理，基本思想就是，能立即完成的请求马上完成，否则就放入到名为 Purgatory 的缓冲区中。后续，DelayedOperationPurgatory 类的方法会自动地处理这些延迟请求。

我们来回顾一下重点。SystemTimer 类：Kafka 定义的定时器类，封装了底层分层时间轮，实现了时间轮 Bucket 的管理以及时钟向前推进功能。它是实现延迟请求后续被自动处理的基础。DelayedOperation 类：延迟请求的高阶抽象类，提供了完成请求以及请求完成和过期后的回调逻辑实现。DelayedOperationPurgatory 类：Purgatory 实现类，该类定义了 WatcherList 对象以及对 WatcherList 的操作方法，而 WatcherList 是实现延迟请求后续自动处理的关键数据结构。

总的来说，延迟请求模块属于 Kafka 的冷门组件。毕竟，大部分的请求还是能够被立即处理的。了解这部分模块的最大意义在于，你可以学习 Kafka 这个分布式系统是如何异步循环操作和管理定时任务的。这个功能是所有分布式系统都要面临的课题，因此，弄明白了这部分的原理和代码实现，后续我们在自行设计类似的功能模块时，就非常容易了。


## 副本管理模块
## 21 | AbstractFetcherThread：拉取消息分几步？

### 总结
今天，我们主要学习了 Kafka 的副本同步机制和副本管理器组件。目前，Kafka 副本之间的消息同步是依靠 ReplicaFetcherThread 线程完成的。我们重点阅读了它的抽象基类 AbstractFetcherThread 线程类的代码。作为拉取线程的公共基类，AbstractFetcherThread 类定义了很多重要方法。我们来回顾一下这节课的重点。

AbstractFetcherThread 类：拉取线程的抽象基类。它定义了公共方法来处理所有拉取线程都要实现的逻辑，如执行截断操作，获取消息等。拉取线程逻辑：循环执行截断操作和获取数据操作。分区读取状态：当前，源码定义了 3 类分区读取状态。拉取线程只能拉取处于可读取状态的分区的数据。

![img](https://static001.geekbang.org/resource/image/75/8d/750998c099f3d3575f6ba4c418bfce8d.jpg?wh=2250*1874)


下节课，我会带你一起对照着 doWork 方法的代码，把拉取线程的完整执行逻辑串联一遍，这样的话，我们就能彻底掌握 Follower 副本拉取线程的工作原理了。在这个过程中，我们还会陆续接触到 ReplicaFetcherThread 类源码的 3 个重要方法的代码。你需要理解它们的实现机制以及 doWork 是怎么把它们组织在一起的。


## 22 | ReplicaFetcherThread：Follower如何拉取Leader消息？


### 总结
好了，我们总结一下。就像我在开头时所说，AbstractFetcherThread 线程的 doWork 方法把上一讲提到的 3 个重要方法全部连接在一起，共同完整了拉取线程要执行的逻辑，即日志截断（truncate）+ 日志获取（buildFetch）+ 日志处理（processPartitionData），而其子类 ReplicaFetcherThread 类是真正实现该 3 个方法的地方。如果用一句话归纳起来，那就是：Follower 副本利用 ReplicaFetcherThread 线程实时地从 Leader 副本拉取消息并写入到本地日志，从而实现了与 Leader 副本之间的同步。以下是一些要点：

doWork 方法：拉取线程工作入口方法，联结所有重要的子功能方法，如执行截断操作，获取 Leader 副本消息以及写入本地日志。truncate 方法：根据 Leader 副本返回的位移值和 Epoch 值执行本地日志的截断操作。buildFetch 方法：为一组特定分区构建 FetchRequest 对象所需的数据结构。processPartitionData 方法：处理从 Leader 副本获取到的消息，主要是写入到本地日志中。

![img](https://static001.geekbang.org/resource/image/eb/52/ebd9a667369fc304bce3yybdd439a152.jpg?wh=2251*2293)

实际上，今天的内容中多次出现副本管理器的身影。如果你仔细查看代码，你会发现 Follower 副本正是利用它来获取对应分区 Partition 对象的，然后依靠该对象执行消息写入。那么，副本管理器还有哪些其他功能呢？下一讲我将一一为你揭晓。


## 23 | ReplicaManager（上）：必须要掌握的副本管理类定义和核心字段

### 总结
这节课，我主要介绍了 ReplicaManager 类的定义以及重要字段。它们是理解后面 ReplicaManager 类管理功能的基础。总的来说，ReplicaManager 类是 Kafka Broker 端管理分区和副本对象的重要组件。每个 Broker 在启动的时候，都会创建 ReplicaManager 实例。该实例一旦被创建，就会开始行使副本管理器的职责，对其下辖的 Leader 副本或 Follower 副本进行管理。我们再简单回顾一下这节课的重点。


ReplicaManager 类：副本管理器的具体实现代码，里面定义了读写副本、删除副本消息的方法，以及其他的一些管理方法。allPartitions 字段：承载了 Broker 上保存的所有分区对象数据。ReplicaManager 类通过它实现对分区下副本的管理。replicaFetcherManager 字段：创建 ReplicaFetcherThread 类实例，该线程类实现 Follower 副本向 Leader 副本实时拉取消息的逻辑。

![img](https://static001.geekbang.org/resource/image/b8/27/b84b7e14a664f0907994ec78c1d19827.jpg?wh=2250*1573)

今天，我多次提到 ReplicaManager 是副本管理器这件事。实际上，副本管理中的两个重要功能就是读取副本对象和写入副本对象。对于 Leader 副本而言，Follower 副本需要读取它的消息数据；对于 Follower 副本而言，它拿到 Leader 副本的消息后，需要将消息写入到自己的底层日志上。那么，读写副本的机制是怎么样的呢？下节课，我们深入地探究一下 ReplicaManager 类重要的副本读写方法。


## 24 | ReplicaManager（中）：副本管理器是如何读写副本的？

### 总结
今天，我们学习了 Kafka 副本状态机类 ReplicaManager 是如何读写副本的，重点学习了它的两个重要方法 appendRecords 和 fetchMessages。我们再简单回顾一下。


appendRecords：向副本写入消息的方法，主要利用 Log 的 append 方法和 Purgatory 机制，共同实现 Follower 副本向 Leader 副本获取消息后的数据同步工作。fetchMessages：从副本读取消息的方法，为普通 Consumer 和 Follower 副本所使用。当它们向 Broker 发送 FETCH 请求时，Broker 上的副本管理器调用该方法从本地日志中获取指定消息。

![img](https://static001.geekbang.org/resource/image/29/b3/295faae205df4255d2861d658df10db3.jpg?wh=2250*2068)

下节课中，我们要把重心转移到副本管理器对副本和分区对象的管理上。这是除了读写副本之外，副本管理器另一大核心功能，你一定不要错过！


## 25 | ReplicaManager（下）：副本管理器是如何管理副本的？

### 总结
今天，我们重点学习了 ReplicaManager 类的分区和副本管理功能，以及 ISR 管理。我们再完整地梳理下 ReplicaManager 类的核心功能和方法。

分区 / 副本管理。ReplicaManager 类的核心功能是，应对 Broker 端接收的 LeaderAndIsrRequest 请求，并将请求中的分区信息抽取出来，让所在 Broker 执行相应的动作。becomeLeaderOrFollower 方法。它是应对 LeaderAndIsrRequest 请求的入口方法。它会将请求中的分区划分成两组，分别调用 makeLeaders 和 makeFollowers 方法。makeLeaders 方法。它的作用是让 Broker 成为指定分区 Leader 副本。makeFollowers 方法。它的作用是让 Broker 成为指定分区 Follower 副本的方法。ISR 管理。ReplicaManager 类提供了 ISR 收缩和定期传播 ISR 变更通知的功能。

![img](https://static001.geekbang.org/resource/image/b6/f2/b63ecd5619213340df68f0771607f6f2.jpg?wh=2250*1029)

掌握了这些核心知识点，你差不多也就掌握了绝大部分的副本管理器功能，比如说 Broker 如何成为分区的 Leader 副本和 Follower 副本，以及 ISR 是如何被管理的。你可能也发现了，有些非核心小功能我们今天并没有展开，比如 Broker 上的元数据缓存是怎么回事。下一节课，我将带你深入到这个缓存当中，去看看它到底是什么。


## 26 | MetadataCache：Broker是怎么异步更新元数据缓存的？

### 总结
今天，我们学习了 Broker 端的 MetadataCache 类，即所谓的元数据缓存类。该类保存了当前集群上的主题分区详细数据和 Broker 数据。每台 Broker 都维护了一个 MetadataCache 实例。Controller 通过给 Broker 发送 UpdateMetadataRequest 请求的方式，来异步更新这部分缓存数据。我们来回顾下这节课的重点。

MetadataCache 类：Broker 元数据缓存类，保存了分区详细数据和 Broker 节点数据。四大调用方：分别是 ReplicaManager、KafkaApis、TransactionCoordinator 和 AdminManager。updateMetadata 方法：Controller 给 Broker 发送 UpdateMetadataRequest 请求时，触发更新。

![img](https://static001.geekbang.org/resource/image/e9/81/e95db24997c6cb615150ccc269aeb781.jpg?wh=2250*2112)

最后，我想和你讨论一个话题。有人认为，Kafka Broker 是无状态的。学完了今天的内容，现在你应该知道了，Broker 并非是无状态的节点，它需要从 Controller 端异步更新保存集群的元数据信息。由于 Kafka 采用的是 Leader/Follower 模式，跟多 Leader 架构和无 Leader 架构相比，这种分布式架构的一致性是最容易保证的，因此，Broker 间元数据的最终一致性是有保证的。不过，就像我前面说过的，你需要处理 Follower 滞后或数据过期的问题。需要注意的是，这里的 Leader 其实是指 Controller，而 Follower 是指普通的 Broker 节点。总之，这一路学到现在，不知道你有没有这样的感受，很多分布式架构设计的问题与方案是相通的。比如，在应对数据备份这个问题上，元数据缓存和 Kafka 副本其实都是相同的设计思路，即使用单 Leader 的架构，令 Leader 对外提供服务，Follower 只是被动地同步 Leader 上的数据。每次学到新的内容之后，希望你不要把它们当作单一的知识看待，要善于进行思考和总结，做到融会贯通。源码学习固然重要，但能让学习源码引领我们升级架构思想，其实是更难得的收获！


## 消费者组管理模块

## 27 | 消费者组元数据（上）：消费者组都有哪些元数据？

### 总结
今天，我带你深入到了 GroupMetadata.scala 和 MemberMetadata.scala 这两个源码文件中，学习了消费者组元数据和组成员元数据的定义。它们封装了一个消费者组及其成员的所有数据。后续的 GroupCoordinator 和其他消费者组组件，都会大量利用这部分元数据执行消费者组的管理。为了让你更好地掌握今天的内容，我们来回顾下这节课的重点。

消费者组元数据：包括组元数据和组成员元数据两部分，分别由 GroupMetadata 和 MemberMetadata 类表征。MemberMetadata 类：保存组成员元数据，比如组 ID、Consumer 主机名、协议类型等。同时，它还提供了 MemberSummary 类，封装了组成员元数据的概要信息。GroupMetadata 类：保存组元数据，包括组状态、组成员元数据列表，等等。1 对多关系：组元数据与组成员元数据是 1 对多的关系。这是因为每个消费者组下存在若干个组成员。

![img](https://static001.geekbang.org/resource/image/14/1f/14a63dda57facee5f686ea539848131f.jpg?wh=2250*3267)

今天这节课的逻辑不是特别复杂，我们重点学习了消费者组元数据的构成，几乎未曾涉及元数据的操作。在下节课，我们将继续在这两个 scala 文件中探索，去学习操作这些元数据的方法实现。但我要再次强调的是，今天学习的这些方法是上层组件调用的基础。如果你想彻底了解消费者组的工作原理，就必须先把这部分基础“铺平夯实”了，这样你才能借由它们到达“完全掌握消费者组实现源码”的目的地。

## 28 | 消费者组元数据（下）：Kafka如何管理这些元数据？

### 总结
今天，我们结合 GroupMetadata 源码，学习了 Kafka 对消费者组元数据的管理，主要包括组状态、成员、位移和分区分配策略四个维度。我建议你在课下再仔细地阅读一下这些管理数据的方法，对照着源码和注释走一遍完整的操作流程。另外，在这两节课中，我没有谈及待决成员列表（Pending Members）和待决位移（Pending Offsets）的管理，因为这两个元数据项属于中间临时状态，因此我没有展开讲，不理解这部分代码的话，也不会影响我们理解消费者组元数据以及 Coordinator 是如何使用它们的。不过，我建议你可以阅读下与它们相关的代码部分。要知道，Kafka 是非常喜欢引用中间状态变量来管理各类元数据或状态的。现在，我们再来简单回顾下这节课的重点。

消费者组元数据管理：主要包括对组状态、成员、位移和分区分配策略的管理。组状态管理：transitionTo 方法负责设置状态，is、not 和 get 方法用于查询状态。成员管理：add、remove 方法用于增减成员，has 和 get 方法用于查询特定成员。分区分配策略管理：定义了专属方法 selectProtocols，用于在每轮 Rebalance 时选举分区分配策略。

![img](https://static001.geekbang.org/resource/image/a3/e7/a3eafee6b5d17b97f7661c24ccdcd4e7.jpg?wh=2250*1823)



至此，我们花了两节课的时间，详细地学习了消费者组元数据及其管理方法的源码。这些操作元数据的方法被上层调用方 GroupCoordinator 大量使用，就像我在开头提到的，如果现在我们不彻底掌握这些元数据被操作的手法，等我们学到 GroupCoordinator 代码时，就会感到有些吃力，所以，你一定要好好地学习这两节课。有了这些基础，等到学习 GroupCoordinator 源码时，你就能更加深刻地理解它的底层实现原理了。


## 29 | GroupMetadataManager：组元数据管理器是个什么东西？

### 总结
今天，我们学习了 GroupMetadataManager 类的源码。作为消费者组管理器，它负责管理消费者组的方方面面。其中，非常重要的两个管理功能是消费者组元数据管理和消费者组位移管理，分别包括查询获取、移除、添加和加载消费者组元数据，以及保存和查询消费者组位移，这些方法是上层组件 GroupCoordinator 倚重的重量级功能载体，你一定要彻底掌握它们。我画了一张思维导图，帮助你复习一下今天的重点内容。



![img](https://static001.geekbang.org/resource/image/eb/5a/eb8fe45e1d152e2ac9cb52c81390265a.jpg?wh=2250*1869)

实际上，GroupMetadataManager 类的地位举足轻重。虽然它在 Coordinator 组件中不显山不露水，但却是一些线上问题的根源所在。

我再跟你分享一个小案例。之前，我碰到过一个问题：在消费者组成员超多的情况下，无法完成位移加载，这导致 Consumer 端总是接收到 Marking the coordinator dead 的错误。当时，我查遍各种资料，都无法定位问题，最终，还是通过阅读源码，发现是这个类的 doLoadGroupsAndOffsets 方法中创建的 buffer 过小导致的。后来，通过调大 offsets.load.buffer.size 参数值，我们顺利地解决了问题。试想一下，如果当时没有阅读这部分的源码，仅凭日志，我们肯定无法解决这个问题。因此，我们花三节课的时间，专门阅读 GroupMetadataManager 类源码，是非常值得的。下节课，我将带你继续研读 GroupMetadataManager 源码，去探寻有关位移主题的那些代码片段。

## 30 | GroupMetadataManager：位移主题保存的只是位移吗？

### 总结
Kafka 内部位移主题，是 Coordinator 端用来保存和记录消费者组信息的重要工具。具体而言，消费者组信息包括消费者组元数据以及已提交位移，它们分别对应于我们今天讲的位移主题中的注册消息和已提交位移消息。前者定义了消费者组的元数据信息，包括组名、成员列表和分区消费分配方案；后者则是消费者组各个成员提交的位移值。这两部分信息共同构成了位移主题的消息类型。除了消息类型，我还介绍了消费者组确定 Coordinator 端的代码。明白了这一点，下次你的消费者组成员出现问题的时候，你就会知道，要去哪台 Broker 上去查找相应的日志了。我们来回顾一下这节课的重点。

位移主题：即 __consumer_offsets。该主题是内部主题，默认有 50 个分区，Kafka 负责将其创建出来，因此你不需要亲自执行创建主题操作。消息类型：位移主题分为注册消息和已提交位移消息。Tombstone 消息：Value 为 null 的位移主题消息，用于清除消费者组已提交的位移值和注册信息。Coordinator 确认原则：消费者组名的哈希值与位移主题分区数求模的绝对值，即为目标分区，目标分区 Leader 副本所在的 Broker 即为 Coordinator。

![img](https://static001.geekbang.org/resource/image/03/e8/03843d5742157064dbb8bd227b9fb7e8.jpg?wh=2250*2066)

定义了消息格式，明确了 Coordinator，下一步，就是 Coordinator 对位移主题进行读写操作了。具体来说，就是构建今天我们所学的两类消息，并将其序列化成字节数组，写入到位移主题，以及从位移主题中读取出字节数组，并反序列化成对应的消息类型。下节课，我们一起研究下这个问题。

## 31 | GroupMetadataManager：查询位移时，不用读取位移主题？

### 总结
今天，我们重点学习了 GroupMetadataManager 类中读写位移主题的方法代码。Coordinator 会使用这些方法对位移主题进行操作，实现对消费者组的管理。写入操作比较简单，它和一般的消息写入并无太大区别，而读取操作相对复杂一些。更重要的是，和我们的直观理解可能相悖的是，Kafka 在查询消费者组已提交位移时，是不会读取位移主题的，而是直接从内存中的消费者组元数据缓存中查询。这一点你一定要重点关注。我们来简单回顾一下这节课的重点。

读写方法：appendForGroup 方法负责写入位移主题，doLoadGroupsAndOffsets 负责读取位移主题，并加载组信息和位移值。查询消费者组位移：查询位移时不读取位移主题，而是读取消费者组元数据缓存。

![img](https://static001.geekbang.org/resource/image/19/3b/19304a381e75783fd584dyye5cc0733b.jpg?wh=2250*537)



至此，GroupMetadataManager 类的重要源码，我们就学完了。作为一个有着将近 1000 行代码，而且集这么多功能于一身的大文件，这个类的代码绝对值得你多读几遍。除了我们集中介绍的这些功能之外，GroupMetadataManager 类其实还是连接 GroupMetadata 和 Coordinator 的重要纽带，Coordinator 利用 GroupMetadataManager 类实现操作 GroupMetadata 的目的。我刚开始学习这部分源码的时候，居然不清楚 GroupMetadata 和 GroupMetadataManager 的区别是什么。现在，经过这 3 节课的内容，相信你已经知道，GroupMetadata 建模的是元数据信息，而 GroupMetadataManager 类建模的是管理元数据的方法，也是管理内部位移主题的唯一组件。以后碰到任何有关位移主题的问题，你都可以直接到这个类中去寻找答案。

## 32 | GroupCoordinator：在Rebalance中，Coordinator如何处理成员入组？


### 总结
至此，我们学完了 Rebalance 流程的第一大步，也就是加入组的源码学习。在这一步中，你要格外注意，**加入组时是区分有无消费者组成员 ID。**对于未设定成员 ID 的分支，代码调用 doUnkwonwJoinGroup 为成员生成 ID 信息；对于已设定成员 ID 的分支，则调用 doJoinGroup 方法。而这两个方法，底层都是调用 addMemberAndRebalance 方法，实现将消费者组成员添加进组的逻辑。我们来简单回顾一下这节课的重点。


Rebalance 流程：包括 JoinGroup 和 SyncGroup 两大步。handleJoinGroup 方法：Coordinator 端处理成员加入组申请的方法。Member Id：成员 ID。Kafka 源码根据成员 ID 的有无，决定调用哪种加入组逻辑方法，比如 doUnknownJoinGroup 或 doJoinGroup 方法。addMemberAndRebalance 方法：实现加入组功能的实际方法，用于完成“加入组 + 开启 Rebalance”这两个操作。

![img](https://static001.geekbang.org/resource/image/41/01/41212f50defaffd79b04f851a278eb01.jpg?wh=2250*1323)

当所有成员都成功加入到组之后，所有成员会开启 Rebalance 的第二大步：组同步。在这一步中，成员会发送 SyncGroupRequest 请求给 Coordinator。那么，Coordinator 又是如何应对的呢？咱们下节课见分晓。


## 33 | GroupCoordinator：在Rebalance中，如何进行组同步？


### 总结
今天，我们重点学习了 Rebalance 流程的第 2 步，也就是组同步。至此，关于 Rebalance 的完整流程，我们就全部学完了。Rebalance 流程是 Kafka 提供的一个非常关键的消费者组功能。由于它非常重要，所以，社区在持续地对它进行着改进，包括引入增量式的 Rebalance 以及静态成员等。我们在这两节课学的 Rebalance 流程，是理解这些高级功能的基础。如果你不清楚 Rebalance 过程中的这些步骤都是做什么的，你就无法深入地掌握增量式 Rebalance 或静态成员机制所做的事情。因此，我建议你结合上节课的内容，好好学习一下消费者组的 Rebalance，彻底弄明白一个消费者组成员是如何参与其中并最终完成 Rebalance 过程的。我们来回顾一下这节课的重点。

组同步：成员向 Coordinator 发送 SyncGroupRequest 请求以获取分配方案。handleSyncGroup 方法：接收 KafkaApis 发来的 SyncGroupRequest 请求体数据，执行组同步逻辑。doSyncGroup 方法：真正执行组同步逻辑的方法，执行组元数据保存、分配方案下发以及状态变更操作。



![img](https://static001.geekbang.org/resource/image/fc/e9/fccc73c2867102f2ec6e8e3666f101e9.jpg?wh=2250*1984)

讲到这里，Coordinator 组件的源码，我就介绍完了。在这个模块中，我们基本上还是践行“自上而下 + 自下而上”的学习方式。我们先从最低层次的消费者组元数据类开始学习，逐渐上浮到它的管理器类 GroupMetadataManager 类以及顶层类 GroupCoordinator 类。接着，在学习 Rebalance 流程时，我们反其道而行之，先从 GroupCoordinator 类的入口方法进行拆解，又逐渐下沉到 GroupMetadataManager 和更底层的 GroupMetadata 以及 MemberMetadata。

如果你追随着课程的脚步一路走来，你就会发现，我经常采用这种方式讲解源码。我希望，你在日后的源码学习中，也可以多尝试运用这种方法。所谓择日不如撞日，我今天就给你推荐一个课后践行此道的绝佳例子。我建议你去阅读下 clients 工程中的实现消息、消息批次以及消息集合部分的源码，也就是 Record、RecordBatch 和 Records 这些接口和类的代码，去反复实践“自上而下”和“自下而上”这两种阅读方法。其实，这种方式不只适用于 Kafka 源码，在阅读其他框架的源码时，也可以采用这种方式。希望你可以不断总结经验，最终提炼出一套适合自己的学习模式。


## 特别放送（一）| 经典的Kafka学习资料有哪些？


### 英文资料
1.Apache Kafka 官方网站我不知道你有没有认真地读过官网上面的文字，这里面的所有内容都是出自 Kafka Committer 之手，文字言简意赅，而且内容翔实丰富。我推荐你重点研读一下其中的 Configuration 篇、Operations 篇以及 Security 篇，特别是 Configuration 中的参数部分。熟练掌握这些关键的参数配置，是进阶学习 Kafka 的必要条件。

2.Confluent 公司自己维护的官方文档Confluent 公司是 Kafka 初创团队创建的商业化公司，主要是为了提供基于 Kafka 的商业化解决方案。我们经常把他们提供的产品称为 Confluent Kafka。就我个人的感觉而言，这个公司的官网质量要比社区版（即 Apache Kafka 官网）上乘，特别是关于 Security 和 Kafka Streams 两部分的介绍，明显高出社区版一筹。因此，我推荐你重点学习 Confluent 官网上关于Security 配置和Kafka Streams 组件的文档。


3.Kafka 的Jira 列表，也就是我们俗称的 Bug 列表你可以在这个页面上搜索自己在实际环境中碰到的 Kafka 异常名称，然后结合自己的 Kafka 版本来看，这样的话，你通常能判断出该异常是不是由已知 Bug 造成的，从而避免浪费更多的时间去定位问题原因。另外，你还可以通过认领 Jira 的方式来为社区贡献代码。后面我会单独再用一节课的时间，给你具体介绍一下为社区贡献代码的完整流程。


4.Kafka 的KIP 网站KIP 的完整名称是 Kafka Improvement Proposals，即 Kafka 的新功能提案。在这里你能够了解到 Kafka 社区关于新功能的所有提案与相关讨论。有意思的是，有些 KIP 的讨论要比 KIP 本身精彩得多。针对各种新功能，全球开发者在这里审思明辨，彼此讨论，有时协同互利，有时针锋相对，实在是酣畅淋漓。KIP 的另一大魅力则在于它非常民主——任何人都能申请新功能提案，将自己的想法付诸实践。


5.StackOverflow 的Kafka 专区大名鼎鼎的 StackOverflow 网站我就不过多介绍了。这上面的 Kafka 问题五花八门，而且难度参差不齐，不过你通常都能找到你想要的答案。同时，如果你对 Kafka 非常了解，不妨尝试回答一些问题，不仅能赚取一些积分，还能和全球的使用者一起交流，实在是一举两得。


6.Confluent 博客这里面的文章含金量特别高，你一定要仔细研读下。举个简单的例子，我已经被太多人问过这样的问题了：“一个 Kafka 集群到底能支撑多少分区？”其实我们都知道这种问题是没有标准答案的，你需要了解的是原理！碰巧，Confluent 博客上就有一篇这样的原理性解释文章，是 Kafka 创始人饶军写的，你不妨看一看。

7.Kafka 社区非公开的各种资料，这包括社区维护的Confluence 文档和 Google Docs你几乎能在 Confluence Space 中找到所有的 Kafka 设计文档，其中关于 Controller 和新版 Clients 设计的文章非常值得一读；而 Google Docs 主要是两篇：一篇是 Kafka 事务的详细设计文档，另一篇是Controller 重设计文档。这两篇是我目前所见过的最详细的 Kafka 设计文档。国内的很多 Kafka 书籍和博客多是援引这两篇文章，甚至是直接翻译的，足见它们的价值非凡。

8.Kafka 社区的Twitter 首页和 Confluent 的Twitter 首页你可能会说，Twitter 算哪门子学习资料啊？但实际上，很多时候，你就是能够在这上面搜罗到有价值的 Kafka 文章，特别是 Confluent 的 Twitter，它会定期推送关于 Kafka 最佳实践方面的内容。每次看到这类文章， 我都有一种意外淘到宝藏的感觉。我给你举个例子，Kafka Twitter 在 2019 年 10 月 12 日转载了一篇名为Exploit Apache Kafka’s Message Format to Save Storage and Bandwidth 的文章，这里面的内容水准很高，读起来非常过瘾，我建议你好好读一读。

9.YouTube 上的Kafka 视频这些视频内容主要包括 Kafka 原理的讲解、业界牛人分享等。有的时候，你会发现，我们对文字类资料的掌握程度远不如看视频来得深入。如果你的英语基础还不错的话，我推荐你重点关注下 YouTube 上的这些分享视频。

### 中文资料
首先，我给出我认为比较好的五本 Apache Kafka 书籍。

1.《Kafka 权威指南》这本书是“Kafka Definitive Guide”的中译本。实际上，这本书的中英两个版本我都看过，应该说中文版翻译得很棒，你直接看中译本就行了。这本书虽然很薄，但它包含的内容几乎囊括了 Kafka 的方方面面，而且这本书由 Committer 执笔，质量上无可挑剔。

2.《Kafka 技术内幕》这本书出版后一跃成为市面上能见到的 Kafka 最好书籍。这本书当得起“技术内幕”这四个字，里面很多 Kafka 原理的讲解清晰而深入，我自己读起来觉得收获很大。

3.《深入理解 Kafka：核心设计与实践原理》我与这本书的作者相识，他同时精通 Kafka 和 RabbitMQ，可以说是消息中间件领域内的大家。这本书成书于 2019 年，是目前市面上最新的一本 Kafka 书籍。我推荐你买来读一下。

4.《Kafka Streams 实战》这本书是“Kafka Streams in action”的中译本，由 Kafka Committer 撰写而成。该书是我见到的最深入讲解 Kafka Streams 的书籍。如果你想学习基于 Kafka Streams 的实时流处理，那么这本书是不能不看的。

5.《Apache Kafka 实战》我这本书是 2018 年出版的，和之前那些面向 Kafka 设计原理的国内佳作不同的是，该书以讨论 Kafka 实际应用为主。我在里面分享了我这几年参与 Kafka 社区以及在使用 Kafka 的过程中积累的各种“江湖杂技”。如果你以使用为主，那么我推荐你看下这本书。


书籍的推荐告一段落，下面，我再介绍三个网站给你。第一个是 OrcHome。据我所知，OrcHome 是国人自己维护的一个 Kafka 教程网站。这个网站最具特色的是它有个Kafka 问答区，你可以在这上面提问，会有人专门负责解答你提出的问题。第二个是我本人的[博客](https://www.cnblogs.com/huxi2b/)。这个博客里大多是关于 Kafka 或者是其他大数据技术的原创文章。另外，我也会定期分享转载一些国内外的优秀博客。第三个是知乎的Kafka 专区。和 StackOverflow 不同的是，这个专区上的问题多以理论探讨为主。通常大家对于这类问题的解答还是很踊跃的，我也经常在这里回复问题。如果你对 Kafka 的某些原理想要做深入的了解，不妨在知乎的这个专区上提出你的问题，我相信很快就会有人回复的。

![img](https://static001.geekbang.org/resource/image/4d/b9/4d773e45c4a3f86c5d9e86bb4a7ac7b9.jpg?wh=2083*3672)

另外，我强烈建议你把社区官网和 Confluent 官网文档仔仔细细地读几遍，我保证你一定会有很大的收获，毕竟，相比于清单上的其他项，官网文档是最最权威的第一手资料。

## 特别放送（二）| 一篇文章带你了解参与开源社区的全部流程

![img](https://static001.geekbang.org/resource/image/a0/11/a025813482db2a3e93fb6b4574e38011.jpg?wh=1980*3710)


## 特别放送（三）| 我是怎么度过日常一天的？

规律+番茄工作法+手写笔记+反思

## 特别放送（四）| 20道经典的Kafka面试题详解

1. Apache Kafka 是什么？这是一道很常见的题目，看似很无聊，其实考核的知识点很多。首先，它考验的是，你对 Kafka 的定位认知是否准确。Apache Kafka 一路发展到现在，已经由最初的分布式提交日志系统逐渐演变成了实时流处理框架。因此，这道题你最好这么回答：**Apach Kafka 是一款分布式流处理框架，用于实时构建流处理应用。它有一个核心的功能广为人知，即作为企业级的消息引擎被广泛使用**。其实，这里暗含了一个小技巧。Kafka 被定位为实时流处理框架，在国内的接受度还不是很高，因此，在回答这道题的时候，你一定要先明确它的流处理框架地位，这样能给面试官留下一个很专业的印象。

2. 什么是消费者组？从某种程度上说，这可是个“送命题”。消费者组是 Kafka 独有的概念，如果面试官问这个，就说明他对此是有一定了解的。我先给出标准答案：**关于它的定义，官网上的介绍言简意赅，即消费者组是 Kafka 提供的可扩展且具有容错性的消费者机制**。切记，一定要加上前面那句，以显示你对官网很熟悉。另外，你最好再解释下消费者组的原理：**在 Kafka 中，消费者组是一个由多个消费者实例构成的组。多个实例共同订阅若干个主题，实现共同消费。同一个组下的每个实例都配置有相同的组 ID，被分配不同的订阅分区。当某个实例挂掉的时候，其他实例会自动地承担起它负责消费的分区。**

此时，又有一个小技巧给到你：消费者组的题目，能够帮你在某种程度上掌控下面的面试方向。如果你擅长位移值原理，就不妨再提一下消费者组的位移提交机制；如果你擅长 Kafka Broker，可以提一下消费者组与 Broker 之间的交互；如果你擅长与消费者组完全不相关的 Producer，那么就可以这么说：“消费者组要消费的数据完全来自于 Producer 端生产的消息，我对 Producer 还是比较熟悉的。

使用这个策略的话，面试官可能会被你的话术所影响，偏离他之前想问的知识路径。当然了，如果你什么都不擅长，那就继续往下看题目吧。

3. 在 Kafka 中，ZooKeeper 的作用是什么？这是一道能够帮助你脱颖而出的题目。碰到这个题目，请在心中暗笑三声。先说标准答案：**目前，Kafka 使用 ZooKeeper 存放集群元数据、成员管理、Controller 选举，以及其他一些管理类任务。之后，等 KIP-500 提案完成后，Kafka 将完全不再依赖于 ZooKeeper。**


记住，一定要突出“目前”，以彰显你非常了解社区的演进计划。“存放元数据”是指主题分区的所有数据都保存在 ZooKeeper 中，且以它保存的数据为权威，其他“人”都要与它保持对齐。“成员管理”是指 Broker 节点的注册、注销以及属性变更，等等。“Controller 选举”是指选举集群 Controller，而其他管理类任务包括但不限于主题删除、参数配置等。不过，抛出 KIP-500 也可能是个双刃剑。碰到非常资深的面试官，他可能会进一步追问你 KIP-500 是做的。一言以蔽之：**KIP-500 思想，是使用社区自研的基于 Raft 的共识算法，替代 ZooKeeper，实现 Controller 自选举**。你可能会担心，如果他继续追问的话，该怎么办呢？别怕，在下一期“特别发送”，我会专门讨论这件事。


4. 解释下 Kafka 中位移（offset）的作用这也是一道常见的面试题。位移概念本身并不复杂，你可以这么回答：**在 Kafka 中，每个主题分区下的每条消息都被赋予了一个唯一的 ID 数值，用于标识它在分区中的位置。这个 ID 数值，就被称为位移，或者叫偏移量。一旦消息被写入到分区日志，它的位移值将不能被修改**。答完这些之后，你还可以把整个面试方向转移到你希望的地方。常见方法有以下 3 种：

如果你深谙 Broker 底层日志写入的逻辑，可以强调下消息在日志中的存放格式；如果你明白位移值一旦被确定不能修改，可以强调下“Log Cleaner 组件都不能影响位移值”这件事情；如果你对消费者的概念还算熟悉，可以再详细说说位移值和消费者位移值之间的区别。

5. 阐述下 Kafka 中的领导者副本（Leader Replica）和追随者副本（Follower Replica）的区别这道题表面上是考核你对 Leader 和 Follower 区别的理解，但很容易引申到 Kafka 的同步机制上。因此，我建议你主动出击，一次性地把隐含的考点也答出来，也许能够暂时把面试官“唬住”，并体现你的专业性。

你可以这么回答：**Kafka 副本当前分为领导者副本和追随者副本。只有 Leader 副本才能对外提供读写服务，响应 Clients 端的请求。Follower 副本只是采用拉（PULL）的方式，被动地同步 Leader 副本中的数据，并且在 Leader 副本所在的 Broker 宕机后，随时准备应聘 Leader 副本。**


通常来说，回答到这个程度，其实才只说了 60%，因此，我建议你再回答两个额外的加分项。
- 强调 Follower 副本也能对外提供读服务。自 Kafka 2.4 版本开始，社区通过引入新的 Broker 端参数，允许 Follower 副本有限度地提供读服务。
- 强调 Leader 和 Follower 的消息序列在实际场景中不一致。很多原因都可能造成 Leader 和 Follower 保存的消息序列不一致，比如程序 Bug、网络问题等。这是很严重的错误，必须要完全规避。你可以补充下，之前确保一致性的主要手段是高水位机制，但高水位值无法保证 Leader 连续变更场景下的数据一致性，因此，社区引入了 Leader Epoch 机制，来修复高水位值的弊端。关于“Leader Epoch 机制”，国内的资料不是很多，它的普及度远不如高水位，不妨大胆地把这个概念秀出来，力求惊艳一把。上一季专栏的第 27 节课讲的就是 Leader Epoch 机制的原理，推荐你赶紧去学习下。

**实操题目**

6. 如何设置 Kafka 能接收的最大消息的大小？这道题除了要回答消费者端的参数设置之外，一定要加上 Broker 端的设置，这样才算完整。毕竟，如果 Producer 都不能向 Broker 端发送数据很大的消息，又何来消费一说呢？因此，你需要同时设置 Broker 端参数和 Consumer 端参数。

Broker 端参数：message.max.bytes、max.message.bytes（主题级别）和 replica.fetch.max.bytes。Consumer 端参数：fetch.message.max.bytes。


Broker 端的最后一个参数比较容易遗漏。我们必须调整 Follower 副本能够接收的最大消息的大小，否则，副本同步就会失败。因此，把这个答出来的话，就是一个加分项。

7. 监控 Kafka 的框架都有哪些？其实，目前业界并没有公认的解决方案，各家都有各自的监控之道。所以，面试官其实是在考察你对监控框架的了解广度，或者说，你是否知道很多能监控 Kafka 的框架或方法。下面这些就是 Kafka 发展历程上比较有名气的监控系统。

Kafka Manager：应该算是最有名的专属 Kafka 监控框架了，是独立的监控系统。Kafka Monitor：LinkedIn 开源的免费框架，支持对集群进行系统测试，并实时监控测试结果。CruiseControl：也是 LinkedIn 公司开源的监控框架，用于实时监测资源使用率，以及提供常用运维操作等。无 UI 界面，只提供 REST API。JMX 监控：由于 Kafka 提供的监控指标都是基于 JMX 的，因此，市面上任何能够集成 JMX 的框架都可以使用，比如 Zabbix 和 Prometheus。已有大数据平台自己的监控体系：像 Cloudera 提供的 CDH 这类大数据平台，天然就提供 Kafka 监控方案。JMXTool：社区提供的命令行工具，能够实时监控 JMX 指标。答上这一条，属于绝对的加分项，因为知道的人很少，而且会给人一种你对 Kafka 工具非常熟悉的感觉。如果你暂时不了解它的用法，可以在命令行以无参数方式执行一下kafka-run-class.sh kafka.tools.JmxTool，学习下它的用法。

8.Broker 的 Heap Size 如何设置？如何设置 Heap Size 的问题，其实和 Kafka 关系不大，它是一类非常通用的面试题目。一旦你应对不当，面试方向很有可能被引到 JVM 和 GC 上去，那样的话，你被问住的几率就会增大。因此，我建议你简单地介绍一下 Heap Size 的设置方法，并把重点放在 Kafka Broker 堆大小设置的最佳实践上。

比如，你可以这样回复：**任何 Java 进程 JVM 堆大小的设置都需要仔细地进行考量和测试。一个常见的做法是，以默认的初始 JVM 堆大小运行程序，当系统达到稳定状态后，手动触发一次 Full GC，然后通过 JVM 工具查看 GC 后的存活对象大小。之后，将堆大小设置成存活对象总大小的 1.5~2 倍。对于 Kafka 而言，这个方法也是适用的。不过，业界有个最佳实践，那就是将 Broker 的 Heap Size 固定为 6GB。经过很多公司的验证，这个大小是足够且良好的。**

9. 如何估算 Kafka 集群的机器数量？这道题目考查的是机器数量和所用资源之间的关联关系。所谓资源，也就是 CPU、内存、磁盘和带宽。通常来说，CPU 和内存资源的充足是比较容易保证的，因此，你需要从磁盘空间和带宽占用两个维度去评估机器数量。在预估磁盘的占用时，你一定不要忘记计算副本同步的开销。如果一条消息占用 1KB 的磁盘空间，那么，在有 3 个副本的主题中，你就需要 3KB 的总空间来保存这条消息。显式地将这些考虑因素答出来，能够彰显你考虑问题的全面性，是一个难得的加分项。

对于评估带宽来说，常见的带宽有 1Gbps 和 10Gbps，但你要切记，这两个数字仅仅是最大值。因此，你最好和面试官确认一下给定的带宽是多少。然后，明确阐述出当带宽占用接近总带宽的 90% 时，丢包情形就会发生。这样能显示出你的网络基本功。

10.Leader 总是 -1，怎么破？在生产环境中，你一定碰到过“某个主题分区不能工作了”的情形。使用命令行查看状态的话，会发现 Leader 是 -1，于是，你使用各种命令都无济于事，最后只能用“重启大法”。但是，有没有什么办法，可以不重启集群，就能解决此事呢？这就是此题的由来。

我直接给答案：**删除 ZooKeeper 节点 /controller，触发 Controller 重选举。Controller 重选举能够为所有主题分区重刷分区状态，可以有效解决因不一致导致的 Leader 不可用问题**。我几乎可以断定，当面试官问出此题时，要么就是他真的不知道怎么解决在向你寻求答案，要么他就是在等你说出这个答案。所以，千万别一上来就说“来个重启”之类的话。


**炫技式题目**

11. LEO、LSO、AR、ISR、HW 都表示什么含义？在我看来，这纯属无聊的炫技。试问我不知道又能怎样呢？！不过既然问到了，我们就统一说一说。

LEO：Log End Offset。日志末端位移值或末端偏移量，表示日志下一条待插入消息的位移值。举个例子，如果日志有 10 条消息，位移值从 0 开始，那么，第 10 条消息的位移值就是 9。此时，LEO = 10。LSO：Log Stable Offset。这是 Kafka 事务的概念。如果你没有使用到事务，那么这个值不存在（其实也不是不存在，只是设置成一个无意义的值）。该值控制了事务型消费者能够看到的消息范围。它经常与 Log Start Offset，即日志起始位移值相混淆，因为有些人将后者缩写成 LSO，这是不对的。在 Kafka 中，LSO 就是指代 Log Stable Offset。AR：Assigned Replicas。AR 是主题被创建后，分区创建时被分配的副本集合，副本个数由副本因子决定。ISR：In-Sync Replicas。Kafka 中特别重要的概念，指代的是 AR 中那些与 Leader 保持同步的副本集合。在 AR 中的副本可能不在 ISR 中，但 Leader 副本天然就包含在 ISR 中。关于 ISR，还有一个常见的面试题目是如何判断副本是否应该属于 ISR。目前的判断依据是：Follower 副本的 LEO 落后 Leader LEO 的时间，是否超过了 Broker 端参数 replica.lag.time.max.ms 值。如果超过了，副本就会被从 ISR 中移除。HW：高水位值（High watermark）。这是控制消费者可读取消息范围的重要字段。一个普通消费者只能“看到”Leader 副本上介于 Log Start Offset 和 HW（不含）之间的所有消息。水位以上的消息是对消费者不可见的。关于 HW，问法有很多，我能想到的最高级的问法，就是让你完整地梳理下 Follower 副本拉取 Leader 副本、执行同步机制的详细步骤。这就是我们的第 20 道题的题目，一会儿我会给出答案和解析。

12. Kafka 能手动删除消息吗？其实，Kafka 不需要用户手动删除消息。它本身提供了留存策略，能够自动删除过期消息。当然，它是支持手动删除消息的。因此，你最好从这两个维度去回答。

对于设置了 Key 且参数 cleanup.policy=compact 的主题而言，我们可以构造一条 < Key，null> 的消息发送给 Broker，依靠 Log Cleaner 组件提供的功能删除掉该 Key 的消息。对于普通主题而言，我们可以使用 kafka-delete-records 命令，或编写程序调用 Admin.deleteRecords 方法来删除消息。这两种方法殊途同归，底层都是调用 Admin 的 deleteRecords 方法，通过将分区 Log Start Offset 值抬高的方式间接删除消息。

13. __consumer_offsets 是做什么用的？这是一个内部主题，公开的官网资料很少涉及到。因此，我认为，此题属于面试官炫技一类的题目。你要小心这里的考点：该主题有 3 个重要的知识点，你一定要全部答出来，才会显得对这块知识非常熟悉。

它是一个内部主题，无需手动干预，由 Kafka 自行管理。当然，我们可以创建该主题。它的主要作用是负责注册消费者以及保存位移值。可能你对保存位移值的功能很熟悉，但其实该主题也是保存消费者元数据的地方。千万记得把这一点也回答上。另外，这里的消费者泛指消费者组和独立消费者，而不仅仅是消费者组。Kafka 的 GroupCoordinator 组件提供对该主题完整的管理功能，包括该主题的创建、写入、读取和 Leader 维护等。

14. 分区 Leader 选举策略有几种？分区的 Leader 副本选举对用户是完全透明的，它是由 Controller 独立完成的。你需要回答的是，在哪些场景下，需要执行分区 Leader 选举。每一种场景对应于一种选举策略。当前，Kafka 有 4 种分区 Leader 选举策略。

OfflinePartition Leader 选举：每当有分区上线时，就需要执行 Leader 选举。所谓的分区上线，可能是创建了新分区，也可能是之前的下线分区重新上线。这是最常见的分区 Leader 选举场景。ReassignPartition Leader 选举：当你手动运行 kafka-reassign-partitions 命令，或者是调用 Admin 的 alterPartitionReassignments 方法执行分区副本重分配时，可能触发此类选举。假设原来的 AR 是[1，2，3]，Leader 是 1，当执行副本重分配后，副本集合 AR 被设置成[4，5，6]，显然，Leader 必须要变更，此时会发生 Reassign Partition Leader 选举。PreferredReplicaPartition Leader 选举：当你手动运行 kafka-preferred-replica-election 命令，或自动触发了 Preferred Leader 选举时，该类策略被激活。所谓的 Preferred Leader，指的是 AR 中的第一个副本。比如 AR 是[3，2，1]，那么，Preferred Leader 就是 3。ControlledShutdownPartition Leader 选举：当 Broker 正常关闭时，该 Broker 上的所有 Leader 副本都会下线，因此，需要为受影响的分区执行相应的 Leader 选举。

这 4 类选举策略的大致思想是类似的，即从 AR 中挑选首个在 ISR 中的副本，作为新 Leader。当然，个别策略有些微小差异。不过，回答到这种程度，应该足以应付面试官了。毕竟，微小差别对选举 Leader 这件事的影响很小。


15. Kafka 的哪些场景中使用了零拷贝（Zero Copy）？Zero Copy 是特别容易被问到的高阶题目。在 Kafka 中，体现 Zero Copy 使用场景的地方有两处：**基于 mmap 的索引和日志文件读写所用的 TransportLayer。**

先说第一个。索引都是基于 MappedByteBuffer 的，也就是让用户态和内核态共享内核态的数据缓冲区，此时，数据不需要复制到用户态空间。不过，mmap 虽然避免了不必要的拷贝，但不一定就能保证很高的性能。在不同的操作系统下，mmap 的创建和销毁成本可能是不一样的。很高的创建和销毁开销会抵消 Zero Copy 带来的性能优势。由于这种不确定性，在 Kafka 中，只有索引应用了 mmap，最核心的日志并未使用 mmap 机制。

再说第二个。TransportLayer 是 Kafka 传输层的接口。它的某个实现类使用了 FileChannel 的 transferTo 方法。该方法底层使用 sendfile 实现了 Zero Copy。对 Kafka 而言，如果 I/O 通道使用普通的 PLAINTEXT，那么，Kafka 就可以利用 Zero Copy 特性，直接将页缓存中的数据发送到网卡的 Buffer 中，避免中间的多次拷贝。相反，如果 I/O 通道启用了 SSL，那么，Kafka 便无法利用 Zero Copy 特性了。

**深度思考题**

16. Kafka 为什么不支持读写分离？这道题目考察的是你对 Leader/Follower 模型的思考。Leader/Follower 模型并没有规定 Follower 副本不可以对外提供读服务。很多框架都是允许这么做的，只是 Kafka 最初为了避免不一致性的问题，而采用了让 Leader 统一提供服务的方式。

不过，在开始回答这道题时，你可以率先亮出观点：**自 Kafka 2.4 之后，Kafka 提供了有限度的读写分离，也就是说，Follower 副本能够对外提供读服务**。说完这些之后，你可以再给出之前的版本不支持读写分离的理由。

场景不适用。读写分离适用于那种读负载很大，而写操作相对不频繁的场景，可 Kafka 不属于这样的场景。同步机制。Kafka 采用 PULL 方式实现 Follower 的同步，因此，Follower 与 Leader 存在不一致性窗口。如果允许读 Follower 副本，就势必要处理消息滞后（Lagging）的问题。

17. 如何调优 Kafka？回答任何调优问题的第一步，就是确定优化目标，并且定量给出目标！这点特别重要。对于 Kafka 而言，常见的优化目标是吞吐量、延时、持久性和可用性。每一个方向的优化思路都是不同的，甚至是相反的。

确定了目标之后，还要明确优化的维度。有些调优属于通用的优化思路，比如对操作系统、JVM 等的优化；有些则是有针对性的，比如要优化 Kafka 的 TPS。我们需要从 3 个方向去考虑。

Producer 端：增加 batch.size、linger.ms，启用压缩，关闭重试等。Broker 端：增加 num.replica.fetchers，提升 Follower 同步 TPS，避免 Broker Full GC 等。Consumer：增加 fetch.min.bytes 等


18. Controller 发生网络分区（Network Partitioning）时，Kafka 会怎么样？这道题目能够诱发我们对分布式系统设计、CAP 理论、一致性等多方面的思考。不过，针对故障定位和分析的这类问题，我建议你首先言明“实用至上”的观点，即不论怎么进行理论分析，永远都要以实际结果为准。一旦发生 Controller 网络分区，那么，第一要务就是查看集群是否出现“脑裂”，即同时出现两个甚至是多个 Controller 组件。这可以根据 Broker 端监控指标 ActiveControllerCount 来判断。

现在，我们分析下，一旦出现这种情况，Kafka 会怎么样。由于 Controller 会给 Broker 发送 3 类请求，即 LeaderAndIsrRequest、StopReplicaRequest 和 UpdateMetadataRequest，因此，一旦出现网络分区，这些请求将不能顺利到达 Broker 端。这将影响主题的创建、修改、删除操作的信息同步，表现为集群仿佛僵住了一样，无法感知到后面的所有操作。因此，网络分区通常都是非常严重的问题，要赶快修复。

19. Java Consumer 为什么采用单线程来获取消息？在回答之前，如果先把这句话说出来，一定会加分：**Java Consumer 是双线程的设计。一个线程是用户主线程，负责获取消息；另一个线程是心跳线程，负责向 Kafka 汇报消费者存活情况。将心跳单独放入专属的线程，能够有效地规避因消息处理速度慢而被视为下线的“假死”情况。**

单线程获取消息的设计能够避免阻塞式的消息获取方式。单线程轮询方式容易实现异步非阻塞式，这样便于将消费者扩展成支持实时流处理的操作算子。因为很多实时流处理操作算子都不能是阻塞式的。另外一个可能的好处是，可以简化代码的开发。多线程交互的代码是非常容易出错的。


20. 简述 Follower 副本消息同步的完整流程首先，Follower 发送 FETCH 请求给 Leader。接着，Leader 会读取底层日志文件中的消息数据，再更新它内存中的 Follower 副本的 LEO 值，更新为 FETCH 请求中的 fetchOffset 值。最后，尝试更新分区高水位值。Follower 接收到 FETCH 响应之后，会把消息写入到底层日志，接着更新 LEO 和 HW 值。

Leader 和 Follower 的 HW 值更新时机是不同的，Follower 的 HW 更新永远落后于 Leader 的 HW。这种时间上的错配是造成各种不一致的原因。



## 特别放送（五） | Kafka 社区的重磅功能：移除 ZooKeeper 依赖

### 总结
基于以上的这些解决方案，社区打算分三步来完成对 ZooKeeper 的依赖：


第一步：移除 Clients 端对 ZooKeeper 的依赖。这一步基本上已经完成了，除了目前 AdminClient 还有少量的 API 依赖 ZooKeeper 之外，其他 Clients 端应该说都不需要访问 ZooKeeper 了。第二步：移除 Broker 端的 ZooKeeper 依赖。这主要包括移除 Broker 端需要访问 ZooKeeper 的代码，以及增加新的 Broker 端 API，比如前面说的 AlterISR 等，最后是将对 ZooKeeper 的访问全部集中在 controller 端。最后一步：实现 controller quorum，也就是实现 Raft-based 的 quorum 负责 controller 的选举。

应该说，移除 ZooKeeper 的功能算是近几年社区最为重磅的提案了。这个提案涉及组件之广、历时之长、复杂程度之高在社区中非常罕见。一旦后续完整地实现了这个功能，Apache Kafka 将极大地提升自己的可维护性，以一个更加“清爽”的形象出现在我们面前。至于最后的效果如何，就让我们拭目以待吧。



---
title: "Go_advanced_02"
date: 2022-08-05T16:42:40+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
# Go并发编程实战课

## 开篇词 | 想吃透Go并发编程，你得这样学！
晁岳攀 2020-10-12

你好，我是晁岳攀，网名鸟窝。之前我在微博研发平台架构中心担任资深架构师，同时也是微服务框架 rpcx 的作者，欢迎来到“Go 并发编程实战课”。


### 并发编程，为什么选 Go？

为什么要学 Go 并发呢？我想先和你聊聊我和 Go 结缘的经历。作为一位老程序员，我在清华同方、摩托罗拉、Comcast 等公司，一直使用 Java 做项目开发。但是后来，我毅然抛弃了十几年的 Java 编程经验，投入到了 Go 语言的怀抱，为什么呢？一句话，我被 Go 的简单高效所打动。它不仅部署方便，自带完善的工具链，特别是 Go 在处理并发场景上表现出的独特性能，更是让我着迷。我们知道，Java 语言的编码非常繁琐，为了应用设计模式而做了大量的冗长设计，而 Go 就不一样了。它提供了便利的并发编程方式，简简单单的 Go 语句，就可以创建多个 goroutine 执行并发任务。而且，Go 还提供了独特的 Channel 类型，很容易实现 goroutine 之间的数据交流。所以，Go 并发编程入门很容易，即使是初学者，要写一个使用 goroutine 异步输出“Hello World”的例子，也可以不费吹灰之力。不过，和其他语言相比，Go 微服务治理框架的发展还是比较晚的。当阿里出品的 Java 微服务框架 Dubbo 被广泛应用时，Go 生态圈还没有微服务框架。

于是，四五年前，为了填补 Go 生态圈微服务化的缺失，我就用 Go 开发了一个微服务的框架 rpcx。它既有类似标准 rpc 库的易用特点，又包含了非常丰富的服务治理的功能。而且，根据benchmark 测试，rpcx 有着数一数二的性能，很多互联网企业（比如马蜂窝、百度等）都在使用。在微博的四年时间里，我使用 Go 参与开发多个基础架构系统，并负责中国版权链、微博下一代的 Redis 集群系统、数据库资源云等系统的设计和开发工作。在多年的实战中，我遇见过各种各样的并发难题，积累了大量的高并发高吞吐的服务器开发经验，也梳理了一整套并发编程的知识体系。2019 年，astaxie（谢孟军）邀请我在 Gopher China 大会上做一个关于 Go 并发编程的分享。我准备了一份 120 页的PPT，全面地介绍了 Go 并发编程的基础内容，包括基本并发原语、扩展并发原语和 Channel 等。会后，现场的观众都说干货满满，希望我能提供无删改版的 PPT。后来，在 Go 爱好者的强烈要求下，我又在滴滴举办了一场 Go 并发编程的培训，详细地分享了我的并发编程心得和经验，包括各种并发原语的基本用法和实现机制。结合我自己的开发经验，以及这些年的技术分享经历，我真切地感受到了这一点：**Go 并发编程的重要性不容置疑。只要是使用 Go 开发的大型应用程序，并发是必然要采用的技术**。但同时，我也了解到，很多人想要学习 Go 并发编程，却不知道该从何学起，也不知该如何精进。

### 学习 Go 并发编程，有哪些困难？

那学习 Go 并发会有哪些困难呢？我总结了一下，主要是有 5 大问题。

在面对并发难题时，感觉无从下手，不知道该用什么并发原语来解决问题。如果多个并发原语都可以解决问题，那么，究竟哪个是最优解呢？比如说是用互斥锁，还是用 Channel。不知道如何编排并发任务。并发编程不像是传统的串行编程，程序的运行存在着很大的不确定性。这个时候，就会面临一个问题，怎么才能让相应的任务按照你设想的流程运行呢？有时候，按照正常理解的并发方式去实现的程序，结果莫名其妙就 panic 或者死锁了，排查起来非常困难。已知的并发原语都不能解决并发问题，程序写起来异常复杂，而且代码混乱，容易出错。

每一位刚入门 Go 的程序员，在深入学习 Go 语言的时候，尤其是面对 Go 并发编程的时候，都会遇到这些问题。那么，具体该怎么学呢？

### 怎么提升 Go 并发编程能力？

学习这件事儿，最怕的就是不成体系，即使知识点之间是彼此独立的，也必定存在着联系。我们要做的，就是找出逻辑关系，拎出知识线。我认为，关于 Go 并发编程，有两条主线，分别是知识主线和学习主线。具体是啥意思呢？可以看下面的这张知识地图。

![img](https://static001.geekbang.org/resource/image/81/e3/81fa1cfd8d39632d871baeedf4081ce3.jpg?wh=4000*2250)

从图中可以看到，在知识主线层面，这门课程的核心内容设计了 5 个模块：

基本并发原语：在这部分，我会介绍 Mutex、RWMutex、Waitgroup、Cond、Pool、Context 等标准库中的并发原语，这些都是传统的并发原语，在其它语言中也很常见，是我们在并发编程中常用的类型。原子操作：在这部分，我会介绍 Go 标准库中提供的原子操作。原子操作是其它并发原语的基础，学会了你就可以自己创造新的并发原语。Channel：Channel 类型是 Go 语言独特的类型，因为比较新，所以难以掌握。但是别怕，我会带你全方位地学习 Channel 类型，你不仅能掌握它的基本用法，而且还能掌握它的处理场景和应用模式，避免踩坑。扩展并发原语：目前来看，Go 开发组不准备在标准库中扩充并发原语了，但是还有一些并发原语应用广泛，比如信号量、SingleFlight、循环栅栏、ErrGroup 等。掌握了它们，就可以在处理一些并发问题时，取得事半功倍的效果。分布式并发原语：分布式并发原语是应对大规模的应用程序中并发问题的并发类型。我主要会介绍使用 etcd 实现的一些分布式并发原语，比如 Leader 选举、分布式互斥锁、分布式读写锁、分布式队列等，在处理分布式场景的并发问题时，特别有用。

![img](https://static001.geekbang.org/resource/image/7a/de/7af6e5216dd0cc449878b3949a3e81de.jpg?wh=750*2137)

沿着这条知识主线，我会带你建立起一个丰富的并发原语库。你可以把并发问题当成一个强大的敌人，而这些并发原语，就是我们的武器。每一种并发原语都有它的用处，你只有知道足够多的并发原语，才能灵活地应对各种场景。那具体怎么掌握这些武器呢？课程的每一个模块都是独立的，它们之间没有任何依赖问题，你可以结合自己的实际情况，有重点地进行学习。如果你对 Channel 类型不是太熟悉，就可以先看 Channel 这个模块的内容；如果你已经非常熟悉标准库的并发原语了，就可以看看扩展并发原语和分布式并发原语的内容。同时，在学习主线层面，主要是四大步骤，**包括基础用法、实现原理、易错场景、知名项目中的 Bug**。每一个模块，我都会带着你按照这四个步骤来学习，目的就是带你熟知每一种并发原语的实现机制和适用场景。



Go 中有一个大的方向，就是任务编排用 Channel，共享资源保护用传统并发原语。在刚开始学习时，你可以基于这个原则去选择相应的并发原语，这是没错的。但是，如果你想要在 Go 并发编程的道路上向前走，就不能局限于这个原则。



实际上，针对同一种场景，也许存在很多并发原语都适用的情况，但是一定是有最合适的那一个。所以，你必须非常清楚每种并发原语的实现机制和适用场景，千万不要被网上的一些文章误导，万事皆用 Channel。而且，你还可以深入学习下 Go 并发原语的源代码。你会发现很多独到的设计，比如 Mutex 为了公平性考量的设计、sync.Map 为提升性能做的设计，以及很多并发原语的异常状况的处理方式。尤其是这些异常状况，常常是并发编程中程序 panic 的原因。所以，如果你能深入了解这些并发原语的实现，不但会提高你的编程能力，还能让你避免在开发中踩并发问题的坑。这个时候，你就达到精通的程度了。如果没有做过大型并发项目，你可能还不太清楚并发原语的重要性。那么，我建议你先阅读一下课程中介绍的知名项目中犯的错，这也是这门课里我特别设计的一部分内容。通过理解这些 Go 大牛们犯的错误以及解决方案，你就可以积累一套避坑指南和应对之道。有了这两条线的学习，我们就从广度和深度上掌握了 Go 并发编程的知识点。这些是不是就足够了呢？我们还可以更进一步，你要有野心能够创造出自己需要的并发原语。



这里的创造有两层含义。第一层是对既有的并发原语进行组合，使用两个、三个或者更多的并发原语去解决问题。比如说，我们可以通过信号量和 WaitGroup 组合成一个新的并发原语，这个并发原语可以使用有限个 goroutine 并发处理子任务。第二层含义是“无中生有”，根据已经掌握的并发原语的设计经验，创造出合适的新的并发原语，以应对一些特殊的并发问题。比如说，标准库中并没有信号量，你可以自己创造出这个类型。达到了这一层，那就不得了了，可以说你对 Go 并发原语的掌握已经出神入化了。那想要达到这个程度是不是很难呢？确实不容易，不过我相信，如果你仔细学习了我们课程里的每一节课，心里牢牢地锚定 3 个目标：建立起一个丰富的并发原语库；熟知每一种并发原语的实现机制和适用场景；能够创造出自己需要的并发原语。达到了这 3 个目标，你就可以轻松地应对各种并发问题了。甚至可以说，你几乎能站在 Go 并发编程的顶端，成为大牛中的一员。

## 基本并发原语

## 01 | Mutex：如何解决资源并发访问问题？

今天是我们 Go 并发编程实战课的第一讲，我们就直接从解决并发访问这个棘手问题入手。说起并发访问问题，真是太常见了，比如多个 goroutine 并发更新同一个资源，像计数器；同时更新用户的账户信息；秒杀系统；往同一个 buffer 中并发写入数据等等。如果没有互斥控制，就会出现一些异常情况，比如计数器的计数不准确、用户的账户可能出现透支、秒杀系统出现超卖、buffer 中的数据混乱，等等，后果都很严重。这些问题怎么解决呢？对，用互斥锁，那在 Go 语言里，就是 Mutex。这节课，我会带你详细了解互斥锁的实现机制，以及 Go 标准库的互斥锁 Mutex 的基本使用方法。在后面的 3 节课里，我还会讲解 Mutex 的具体实现原理、易错场景和一些拓展用法。好了，我们先来看看互斥锁的实现机制。

### 互斥锁的实现机制

互斥锁是并发控制的一个基本手段，是为了避免竞争而建立的一种并发控制机制。在学习它的具体实现原理前，我们要先搞懂一个概念，就是临界区。在并发编程中，如果程序中的一部分会被并发访问或修改，那么，为了避免并发访问导致的意想不到的结果，这部分程序需要被保护起来，这部分被保护起来的程序，就叫做临界区。可以说，临界区就是一个被共享的资源，或者说是一个整体的一组共享资源，比如对数据库的访问、对某一个共享数据结构的操作、对一个 I/O 设备的使用、对一个连接池中的连接的调用，等等。如果很多线程同步访问临界区，就会造成访问或操作错误，这当然不是我们希望看到的结果。所以，我们可以**使用互斥锁，限定临界区只能同时由一个线程持有。**

当临界区由一个线程持有的时候，其它线程如果想进入这个临界区，就会返回失败，或者是等待。直到持有的线程退出临界区，这些等待线程中的某一个才有机会接着持有这个临界区。

![img](https://static001.geekbang.org/resource/image/44/b8/44c08abdd0aff633ca932fc89386ebb8.jpg?wh=3747*1972)



你看，互斥锁就很好地解决了资源竞争问题，有人也把互斥锁叫做排它锁。那在 Go 标准库中，它提供了 Mutex 来实现互斥锁这个功能。

根据 2019 年第一篇全面分析 Go 并发 Bug 的论文Understanding Real-World Concurrency Bugs in Go，**Mutex 是使用最广泛的同步原语**（Synchronization primitives，有人也叫做并发原语。我们在这个课程中根据英文直译优先用同步原语，但是并发原语的指代范围更大，还可以包括任务编排的类型，所以后面我们讲 Channel 或者扩展类型时也会用并发原语）。关于同步原语，并没有一个严格的定义，你可以把它看作解决并发问题的一个基础的数据结构。



在这门课的前两个模块，我会和你讲互斥锁 Mutex、读写锁 RWMutex、并发编排 WaitGroup、条件变量 Cond、Channel 等同步原语。所以，在这里，我先和你说一下同步原语的适用场景。

共享资源。并发地读写共享资源，会出现数据竞争（data race）的问题，所以需要 Mutex、RWMutex 这样的并发原语来保护。任务编排。需要 goroutine 按照一定的规律执行，而 goroutine 之间有相互等待或者依赖的顺序关系，我们常常使用 WaitGroup 或者 Channel 来实现。消息传递。信息交流以及不同的 goroutine 之间的线程安全的数据交流，常常使用 Channel 来实现。



今天这一讲，咱们就从公认的使用最广泛的 Mutex 开始学习吧。是骡子是马咱得拉出来遛遛，看看我们到底可以怎么使用 Mutex。

### Mutex 的基本使用方法

在正式看 Mutex 用法之前呢，我想先给你交代一件事：Locker 接口。在 Go 的标准库中，package sync 提供了锁相关的一系列同步原语，这个 package 还定义了一个 Locker 的接口，Mutex 就实现了这个接口。Locker 的接口定义了锁同步原语的方法集：

```

type Locker interface {
    Lock()
    Unlock()
}

```
可以看到，Go 定义的锁接口的方法集很简单，就是请求锁（Lock）和释放锁（Unlock）这两个方法，秉承了 Go 语言一贯的简洁风格。但是，这个接口在实际项目应用得不多，因为我们一般会直接使用具体的同步原语，而不是通过接口。我们这一讲介绍的 Mutex 以及后面会介绍的读写锁 RWMutex 都实现了 Locker 接口，所以首先我把这个接口介绍了，让你做到心中有数。下面我们直接看 Mutex。

**简单来说，互斥锁 Mutex 就提供两个方法 Lock 和 Unlock：进入临界区之前调用 Lock 方法，退出临界区的时候调用 Unlock 方法：**

```go
  func(m *Mutex)Lock()
  func(m *Mutex)Unlock()
```
**当一个 goroutine 通过调用 Lock 方法获得了这个锁的拥有权后， 其它请求锁的 goroutine 就会阻塞在 Lock 方法的调用上，直到锁被释放并且自己获取到了这个锁的拥有权。**

看到这儿，你可能会问，为啥一定要加锁呢？别急，我带你来看一个并发访问场景中不使用锁的例子，看看实现起来会出现什么状况。在这个例子中，我们创建了 10 个 goroutine，同时不断地对一个变量（count）进行加 1 操作，每个 goroutine 负责执行 10 万次的加 1 操作，我们期望的最后计数的结果是 10 * 100000 = 1000000 (一百万)。

```
 import (
        "fmt"
        "sync"
    )
    
    func main() {
        var count = 0
        // 使用WaitGroup等待10个goroutine完成
        var wg sync.WaitGroup
        wg.Add(10)
        for i := 0; i < 10; i++ {
            go func() {
                defer wg.Done()
                // 对变量count执行10次加1
                for j := 0; j < 100000; j++ {
                    count++
                }
            }()
        }
        // 等待10个goroutine完成
        wg.Wait()
        fmt.Println(count)
    }

```
在这段代码中，我们使用 sync.WaitGroup 来等待所有的 goroutine 执行完毕后，再输出最终的结果。sync.WaitGroup 这个同步原语我会在后面的课程中具体介绍，现在你只需要知道，我们使用它来控制等待一组 goroutine 全部做完任务。但是，每次运行，你都可能得到不同的结果，基本上不会得到理想中的一百万的结果。

![img](https://static001.geekbang.org/resource/image/60/e2/6080fdf493e047917aa099ea33279de2.png?wh=427*137)



这是为什么呢？其实，这是因为，count++ 不是一个原子操作，它至少包含几个步骤，比如读取变量 count 的当前值，对这个值加 1，把结果再保存到 count 中。因为不是原子操作，就可能有并发的问题。比如，10 个 goroutine 同时读取到 count 的值为 9527，接着各自按照自己的逻辑加 1，值变成了 9528，然后把这个结果再写回到 count 变量。但是，实际上，此时我们增加的总数应该是 10 才对，这里却只增加了 1，好多计数都被“吞”掉了。这是并发访问共享数据的常见错误。

```
 // count++操作的汇编代码
    MOVQ    "".count(SB), AX
    LEAQ    1(AX), CX
    MOVQ    CX, "".count(SB)
```
这个问题，有经验的开发人员还是比较容易发现的，但是，很多时候，并发问题隐藏得非常深，即使是有经验的人，也不太容易发现或者 Debug 出来。针对这个问题，Go 提供了一个检测并发访问共享资源是否有问题的工具： [race detector](https://blog.golang.org/race-detector)，它可以帮助我们自动发现程序有没有 data race 的问题。

Go race detector 是基于 Google 的 C/C++ [sanitizers](https://github.com/google/sanitizers) 技术实现的，编译器通过探测所有的内存访问，加入代码能监视对这些内存地址的访问（读还是写）。在代码运行的时候，race detector 就能监控到对共享变量的非同步访问，出现 race 的时候，就会打印出警告信息。这个技术在 Google 内部帮了大忙，探测出了 Chromium 等代码的大量并发问题。Go 1.1 中就引入了这种技术，并且一下子就发现了标准库中的 42 个并发问题。现在，race detector 已经成了 Go 持续集成过程中的一部分。

我们来看看这个工具怎么用。在编译（compile）、测试（test）或者运行（run）Go 代码的时候，加上 race 参数，就有可能发现并发问题。比如在上面的例子中，我们可以加上 race 参数运行，检测一下是不是有并发问题。如果你 go run -race counter.go，就会输出警告信息。

![img](https://static001.geekbang.org/resource/image/f5/ff/f5eec2d6458e4bddc882ebb213f05aff.png?wh=792*359)



这个警告不但会告诉你有并发问题，而且还会告诉你哪个 goroutine 在哪一行对哪个变量有写操作，同时，哪个 goroutine 在哪一行对哪个变量有读操作，就是这些并发的读写访问，引起了 data race。例子中的 goroutine 10 对内存地址 0x00c000126010 有读的操作（counter.go 文件第 16 行），同时，goroutine 7 对内存地址 0x00c000126010 有写的操作（counter.go 文件第 16 行）。而且还可能有多个 goroutine 在同时进行读写，所以，警告信息可能会很长。虽然这个工具使用起来很方便，但是，因为它的实现方式，只能通过真正对实际地址进行读写访问的时候才能探测，所以它并不能在编译的时候发现 data race 的问题。而且，在运行的时候，只有在触发了 data race 之后，才能检测到，如果碰巧没有触发（比如一个 data race 问题只能在 2 月 14 号零点或者 11 月 11 号零点才出现），是检测不出来的。而且，把开启了 race 的程序部署在线上，还是比较影响性能的。运行 go tool compile -race -S counter.go，可以查看计数器例子的代码，重点关注一下 count++ 前后的编译后的代码：

```
0x002a 00042 (counter.go:13)    CALL    runtime.racefuncenter(SB)
       ......
        0x0061 00097 (counter.go:14)    JMP     173
        0x0063 00099 (counter.go:15)    MOVQ    AX, "".j+8(SP)
        0x0068 00104 (counter.go:16)    PCDATA  $0, $1
        0x0068 00104 (counter.go:16)    MOVQ    "".&count+128(SP), AX
        0x0070 00112 (counter.go:16)    PCDATA  $0, $0
        0x0070 00112 (counter.go:16)    MOVQ    AX, (SP)
        0x0074 00116 (counter.go:16)    CALL    runtime.raceread(SB)
        0x0079 00121 (counter.go:16)    PCDATA  $0, $1
        0x0079 00121 (counter.go:16)    MOVQ    "".&count+128(SP), AX
        0x0081 00129 (counter.go:16)    MOVQ    (AX), CX
        0x0084 00132 (counter.go:16)    MOVQ    CX, ""..autotmp_8+16(SP)
        0x0089 00137 (counter.go:16)    PCDATA  $0, $0
        0x0089 00137 (counter.go:16)    MOVQ    AX, (SP)
        0x008d 00141 (counter.go:16)    CALL    runtime.racewrite(SB)
        0x0092 00146 (counter.go:16)    MOVQ    ""..autotmp_8+16(SP), AX
       ......
        0x00b6 00182 (counter.go:18)    CALL    runtime.deferreturn(SB)
        0x00bb 00187 (counter.go:18)    CALL    runtime.racefuncexit(SB)
        0x00c0 00192 (counter.go:18)    MOVQ    104(SP), BP
        0x00c5 00197 (counter.go:18)    ADDQ    $112, SP
```
在编译的代码中，增加了 runtime.racefuncenter、runtime.raceread、runtime.racewrite、runtime.racefuncexit 等检测 data race 的方法。通过这些插入的指令，Go race detector 工具就能够成功地检测出 data race 问题了。总结一下，通过在编译的时候插入一些指令，在运行时通过这些插入的指令检测并发读写从而发现 data race 问题，就是这个工具的实现机制。既然这个例子存在 data race 问题，我们就要想办法来解决它。这个时候，我们这节课的主角 Mutex 就要登场了，它可以轻松地消除掉 data race。具体怎么做呢？下面，我就结合这个例子，来具体给你讲一讲 Mutex 的基本用法。我们知道，这里的共享资源是 count 变量，临界区是 count++，只要在临界区前面获取锁，在离开临界区的时候释放锁，就能完美地解决 data race 的问题了。

```go
package main


    import (
        "fmt"
        "sync"
    )


    func main() {
        // 互斥锁保护计数器
        var mu sync.Mutex
        // 计数器的值
        var count = 0
        
        // 辅助变量，用来确认所有的goroutine都完成
        var wg sync.WaitGroup
        wg.Add(10)

        // 启动10个gourontine
        for i := 0; i < 10; i++ {
            go func() {
                defer wg.Done()
                // 累加10万次
                for j := 0; j < 100000; j++ {
                    mu.Lock()
                    count++
                    mu.Unlock()
                }
            }()
        }
        wg.Wait()
        fmt.Println(count)
    }
```


如果你再运行一下程序，就会发现，data race 警告没有了，系统干脆地输出了 1000000：

![img](https://static001.geekbang.org/resource/image/d3/8e/d3c577aec0322488e349acf17789a08e.png?wh=457*73)

怎么样，使用 Mutex 是不是非常高效？效果很惊喜。这里有一点需要注意：Mutex 的零值是还没有 goroutine 等待的未加锁的状态，所以你不需要额外的初始化，直接声明变量（如 var mu sync.Mutex）即可。那 Mutex 还有哪些用法呢？很多情况下，Mutex 会嵌入到其它 struct 中使用，比如下面的方式：

```
type Counter struct {
    mu    sync.Mutex
    Count uint64
}
```
在初始化嵌入的 struct 时，也不必初始化这个 Mutex 字段，不会因为没有初始化出现空指针或者是无法获取到锁的情况。有时候，我们还可以采用嵌入字段的方式。通过嵌入字段，你可以在这个 struct 上直接调用 Lock/Unlock 方法。

```go
func main() {
    var counter Counter
    var wg sync.WaitGroup
    wg.Add(10)
    for i := 0; i < 10; i++ {
        go func() {
            defer wg.Done()
            for j := 0; j < 100000; j++ {
                counter.Lock()
                counter.Count++
                counter.Unlock()
            }
        }()
    }
    wg.Wait()
    fmt.Println(counter.Count)
}


type Counter struct {
    sync.Mutex
    Count uint64
}
```
如果嵌入的 struct 有多个字段，我们一般会把 Mutex 放在要控制的字段上面，然后使用空格把字段分隔开来。即使你不这样做，代码也可以正常编译，只不过，用这种风格去写的话，逻辑会更清晰，也更易于维护。甚至，你还可以把获取锁、释放锁、计数加一的逻辑封装成一个方法，对外不需要暴露锁等逻辑：

```go
func main() {
    // 封装好的计数器
    var counter Counter

    var wg sync.WaitGroup
    wg.Add(10)

    // 启动10个goroutine
    for i := 0; i < 10; i++ {
        go func() {
            defer wg.Done()
            // 执行10万次累加
            for j := 0; j < 100000; j++ {
                counter.Incr() // 受到锁保护的方法
            }
        }()
    }
    wg.Wait()
    fmt.Println(counter.Count())
}

// 线程安全的计数器类型
type Counter struct {
    CounterType int
    Name        string

    mu    sync.Mutex
    count uint64
}

// 加1的方法，内部使用互斥锁保护
func (c *Counter) Incr() {
    c.mu.Lock()
    c.count++
    c.mu.Unlock()
}

// 得到计数器的值，也需要锁保护
func (c *Counter) Count() uint64 {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.count
}
```
### 总结

这节课，我介绍了并发问题的背景知识、标准库中 Mutex 的使用和最佳实践、通过 race detector 工具发现计数器程序的问题以及修复方法。相信你已经大致了解了 Mutex 这个同步原语。在项目开发的初始阶段，我们可能并没有仔细地考虑资源的并发问题，因为在初始阶段，我们还不确定这个资源是否被共享。经过更加深入的设计，或者新功能的增加、代码的完善，这个时候，我们就需要考虑共享资源的并发问题了。当然，如果你能在初始阶段预见到资源会被共享并发访问就更好了。意识到共享资源的并发访问的早晚不重要，重要的是，一旦你意识到这个问题，你就要及时通过互斥锁等手段去解决。

比如 Docker issue 37583、35517、32826、30696等、kubernetes issue 72361、71617等，都是后来发现的 data race 而采用互斥锁 Mutex 进行修复的。



## 02 | Mutex：庖丁解牛看实现



上一讲我们一起体验了 Mutex 的使用，竟是那么简单，只有简简单单两个方法，Lock 和 Unlock，进入临界区之前调用 Lock 方法，退出临界区的时候调用 Unlock 方法。这个时候，你一定会有一丝好奇：“它的实现是不是也很简单呢？”其实不是的。如果你阅读 Go 标准库里 Mutex 的源代码，并且追溯 Mutex 的演进历史，你会发现，从一个简单易于理解的互斥锁的实现，到一个非常复杂的数据结构，这是一个逐步完善的过程。Go 开发者们做了种种努力，精心设计。我自己每次看，都会被这种匠心和精益求精的精神打动。所以，今天我就想带着你一起去探索 Mutex 的实现及演进之路，希望你能和我一样体验到这种技术追求的美妙。我们从 Mutex 的一个简单实现开始，看看它是怎样逐步提升性能和公平性的。在这个过程中，我们可以学习如何逐步设计一个完善的同步原语，并能对复杂度、性能、结构设计的权衡考量有新的认识。经过这样一个学习，我们不仅能通透掌握 Mutex，更好地使用这个工具，同时，对我们自己设计并发数据接口也非常有帮助。那具体怎么来讲呢？我把 Mutex 的架构演进分成了四个阶段，下面给你画了一张图来说明。

“初版”的 Mutex 使用一个 flag 来表示锁是否被持有，实现比较简单；后来照顾到新来的 goroutine，所以会让新的 goroutine 也尽可能地先获取到锁，这是第二个阶段，我把它叫作“给新人机会”；那么，接下来就是第三阶段“多给些机会”，照顾新来的和被唤醒的 goroutine；但是这样会带来饥饿问题，所以目前又加入了饥饿的解决方案，也就是第四阶段“解决饥饿”。

![img](https://static001.geekbang.org/resource/image/c2/35/c28531b47ff7f220d5bc3c9650180835.jpg?wh=4000*2250)

有了这四个阶段，我们学习的路径就清晰了，那接下来我会从代码层面带你领略 Go 开发者这些大牛们是如何逐步解决这些问题的。

### 初版的互斥锁

我们先来看怎么实现一个最简单的互斥锁。在开始之前，你可以先想一想，如果是你，你会怎么设计呢？你可能会想到，可以通过一个 flag 变量，标记当前的锁是否被某个 goroutine 持有。如果这个 flag 的值是 1，就代表锁已经被持有，那么，其它竞争的 goroutine 只能等待；如果这个 flag 的值是 0，就可以通过 CAS（compare-and-swap，或者 compare-and-set）将这个 flag 设置为 1，标识锁被当前的这个 goroutine 持有了。实际上，Russ Cox 在 2008 年提交的第一版 Mutex 就是这样实现的。

```
   // CAS操作，当时还没有抽象出atomic包
    func cas(val *int32, old, new int32) bool
    func semacquire(*int32)
    func semrelease(*int32)
    // 互斥锁的结构，包含两个字段
    type Mutex struct {
        key  int32 // 锁是否被持有的标识
        sema int32 // 信号量专用，用以阻塞/唤醒goroutine
    }
    
    // 保证成功在val上增加delta的值
    func xadd(val *int32, delta int32) (new int32) {
        for {
            v := *val
            if cas(val, v, v+delta) {
                return v + delta
            }
        }
        panic("unreached")
    }
    
    // 请求锁
    func (m *Mutex) Lock() {
        if xadd(&m.key, 1) == 1 { //标识加1，如果等于1，成功获取到锁
            return
        }
        semacquire(&m.sema) // 否则阻塞等待
    }
    
    func (m *Mutex) Unlock() {
        if xadd(&m.key, -1) == 0 { // 将标识减去1，如果等于0，则没有其它等待者
            return
        }
        semrelease(&m.sema) // 唤醒其它阻塞的goroutine
    }    
```
这里呢，我先简单补充介绍下刚刚提到的 CAS。CAS 指令将给定的值和一个内存地址中的值进行比较，如果它们是同一个值，就使用新值替换内存地址中的值，这个操作是原子性的。那啥是原子性呢？如果你还不太理解这个概念，那么在这里只需要明确一点就行了，那就是原子性保证这个指令总是基于最新的值进行计算，如果同时有其它线程已经修改了这个值，那么，CAS 会返回失败。CAS 是实现互斥锁和同步原语的基础，我们很有必要掌握它。好了，我们继续来分析下刚才的这段代码。虽然当时的 Go 语法和现在的稍微有些不同，而且标准库的布局、实现和现在的也有很大的差异，但是，这些差异不会影响我们对代码的理解，因为最核心的结构体（struct）和函数、方法的定义几乎是一样的。

Mutex 结构体包含两个字段：字段 key：是一个 flag，用来标识这个排外锁是否被某个 goroutine 所持有，如果 key 大于等于 1，说明这个排外锁已经被持有；字段 sema：是个信号量变量，用来控制等待 goroutine 的阻塞休眠和唤醒。



![img](https://static001.geekbang.org/resource/image/82/25/825e23e1af96e78f3773e0b45de38e25.jpg?wh=3032*2199)

调用 Lock 请求锁的时候，通过 xadd 方法进行 CAS 操作（第 24 行），xadd 方法通过循环执行 CAS 操作直到成功，保证对 key 加 1 的操作成功完成。如果比较幸运，锁没有被别的 goroutine 持有，那么，Lock 方法成功地将 key 设置为 1，这个 goroutine 就持有了这个锁；如果锁已经被别的 goroutine 持有了，那么，当前的 goroutine 会把 key 加 1，而且还会调用 semacquire 方法（第 27 行），使用信号量将自己休眠，等锁释放的时候，信号量会将它唤醒。持有锁的 goroutine 调用 Unlock 释放锁时，它会将 key 减 1（第 31 行）。如果当前没有其它等待这个锁的 goroutine，这个方法就返回了。但是，如果还有等待此锁的其它 goroutine，那么，它会调用 semrelease 方法（第 34 行），利用信号量唤醒等待锁的其它 goroutine 中的一个。所以，到这里，我们就知道了，初版的 Mutex 利用 CAS 原子操作，对 key 这个标志量进行设置。key 不仅仅标识了锁是否被 goroutine 所持有，还记录了当前持有和等待获取锁的 goroutine 的数量。Mutex 的整体设计非常简洁，学习起来一点也没有障碍。但是，注意，我要划重点了。

**Unlock 方法可以被任意的 goroutine 调用释放锁，即使是没持有这个互斥锁的 goroutine，也可以进行这个操作。这是因为，Mutex 本身并没有包含持有这把锁的 goroutine 的信息，所以，Unlock 也不会对此进行检查。Mutex 的这个设计一直保持至今。**

这就带来了一个有趣而危险的功能。为什么这么说呢？你看，其它 goroutine 可以强制释放锁，这是一个非常危险的操作，因为在临界区的 goroutine 可能不知道锁已经被释放了，还会继续执行临界区的业务操作，这可能会带来意想不到的结果，因为这个 goroutine 还以为自己持有锁呢，有可能导致 data race 问题。所以，我们在使用 Mutex 的时候，必须要保证 goroutine 尽可能不去释放自己未持有的锁，一定要遵循“谁申请，谁释放”的原则。在真实的实践中，我们使用互斥锁的时候，很少在一个方法中单独申请锁，而在另外一个方法中单独释放锁，一般都会在同一个方法中获取锁和释放锁。如果你接触过其它语言（比如 Java 语言）的互斥锁的实现，就会发现这一点和其它语言的互斥锁不同，所以，如果是从其它语言转到 Go 语言开发的同学，一定要注意。以前，我们经常会基于性能的考虑，及时释放掉锁，所以在一些 if-else 分支中加上释放锁的代码，代码看起来很臃肿。而且，在重构的时候，也很容易因为误删或者是漏掉而出现死锁的现象。

```
type Foo struct {
    mu    sync.Mutex
    count int
}

func (f *Foo) Bar() {
    f.mu.Lock()

    if f.count < 1000 {
        f.count += 3
        f.mu.Unlock() // 此处释放锁
        return
    }

    f.count++
    f.mu.Unlock() // 此处释放锁
    return
}
```
从 1.14 版本起，Go 对 defer 做了优化，采用更有效的内联方式，取代之前的生成 defer 对象到 defer chain 中，defer 对耗时的影响微乎其微了，所以基本上修改成下面简洁的写法也没问题：

```go
func (f *Foo) Bar() {
    f.mu.Lock()
    defer f.mu.Unlock()


    if f.count < 1000 {
        f.count += 3
        return
    }


    f.count++
    return
}
```


这样做的好处就是 Lock/Unlock 总是成对紧凑出现，不会遗漏或者多调用，代码更少。但是，如果临界区只是方法中的一部分，为了尽快释放锁，还是应该第一时间调用 Unlock，而不是一直等到方法返回时才释放。初版的 Mutex 实现之后，Go 开发组又对 Mutex 做了一些微调，比如把字段类型变成了 uint32 类型；调用 Unlock 方法会做检查；使用 atomic 包的同步原语执行原子操作等等，这些小的改动，都不是核心功能，你简单知道就行了，我就不详细介绍了。但是，初版的 Mutex 实现有一个问题：请求锁的 goroutine 会排队等待获取互斥锁。虽然这貌似很公平，但是从性能上来看，却不是最优的。因为如果我们能够把锁交给正在占用 CPU 时间片的 goroutine 的话，那就不需要做上下文的切换，在高并发的情况下，可能会有更好的性能。接下来，我们就继续探索 Go 开发者是怎么解决这个问题的。



### 给新人机会

Go 开发者在 2011 年 6 月 30 日的 commit 中对 Mutex 做了一次大的调整，调整后的 Mutex 实现如下：

```
   type Mutex struct {
        state int32
        sema  uint32
    }


    const (
        mutexLocked = 1 << iota // mutex is locked
        mutexWoken
        mutexWaiterShift = iota
    )
```
虽然 Mutex 结构体还是包含两个字段，但是第一个字段已经改成了 state，它的含义也不一样了。

![img](https://static001.geekbang.org/resource/image/4c/15/4c4a3dd2310059821f41af7b84925615.jpg?wh=2978*1756)

state 是一个复合型的字段，一个字段包含多个意义，这样可以通过尽可能少的内存来实现互斥锁。这个字段的第一位（最小的一位）来表示这个锁是否被持有，第二位代表是否有唤醒的 goroutine，剩余的位数代表的是等待此锁的 goroutine 数。所以，state 这一个字段被分成了三部分，代表三个数据。请求锁的方法 Lock 也变得复杂了。复杂之处不仅仅在于对字段 state 的操作难以理解，而且代码逻辑也变得相当复杂。

```
   func (m *Mutex) Lock() {
        // Fast path: 幸运case，能够直接获取到锁
        if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
            return
        }

        awoke := false
        for {
            old := m.state
            new := old | mutexLocked // 新状态加锁
            if old&mutexLocked != 0 {
                new = old + 1<<mutexWaiterShift //等待者数量加一
            }
            if awoke {
                // goroutine是被唤醒的，
                // 新状态清除唤醒标志
                new &^= mutexWoken
            }
            if atomic.CompareAndSwapInt32(&m.state, old, new) {//设置新状态
                if old&mutexLocked == 0 { // 锁原状态未加锁
                    break
                }
                runtime.Semacquire(&m.sema) // 请求信号量
                awoke = true
            }
        }
    }
```


首先是通过 CAS 检测 state 字段中的标志（第 3 行），如果没有 goroutine 持有锁，也没有等待持有锁的 gorutine，那么，当前的 goroutine 就很幸运，可以直接获得锁，这也是注释中的 Fast path 的意思。如果不够幸运，state 不是零值，那么就通过一个循环进行检查。接下来的第 7 行到第 26 行这段代码虽然只有几行，但是理解起来却要费一番功夫，因为涉及到对 state 不同标志位的操作。这里的位操作以及操作后的结果和数值比较，并没有明确的解释，有时候你需要根据后续的处理进行推断。所以说，如果你充分理解了这段代码，那么对最新版的 Mutex 也会比较容易掌握了，因为你已经清楚了这些位操作的含义。我们先前知道，如果想要获取锁的 goroutine 没有机会获取到锁，就会进行休眠，但是在锁释放唤醒之后，它并不能像先前一样直接获取到锁，还是要和正在请求锁的 goroutine 进行竞争。这会给后来请求锁的 goroutine 一个机会，也让 CPU 中正在执行的 goroutine 有更多的机会获取到锁，在一定程度上提高了程序的性能。for 循环是不断尝试获取锁，如果获取不到，就通过 runtime.Semacquire(&m.sema) 休眠，休眠醒来之后 awoke 置为 true，尝试争抢锁。代码中的第 10 行将当前的 flag 设置为加锁状态，如果能成功地通过 CAS 把这个新值赋予 state（第 19 行和第 20 行），就代表抢夺锁的操作成功了。不过，需要注意的是，如果成功地设置了 state 的值，但是之前的 state 是有锁的状态，那么，state 只是清除 mutexWoken 标志或者增加一个 waiter 而已。请求锁的 goroutine 有两类，一类是新来请求锁的 goroutine，另一类是被唤醒的等待请求锁的 goroutine。锁的状态也有两种：加锁和未加锁。我用一张表格，来说明一下 goroutine 不同来源不同状态下的处理逻辑。

![img](https://static001.geekbang.org/resource/image/de/ac/de281d2a9e022b2e026bb6126f28c9ac.jpg?wh=1690x620)

刚刚说的都是获取锁，接下来，我们再来看看释放锁。释放锁的 Unlock 方法也有些复杂，我们来看一下。

```
   func (m *Mutex) Unlock() {
        // Fast path: drop lock bit.
        new := atomic.AddInt32(&m.state, -mutexLocked) //去掉锁标志
        if (new+mutexLocked)&mutexLocked == 0 { //本来就没有加锁
            panic("sync: unlock of unlocked mutex")
        }
    
        old := new
        for {
            if old>>mutexWaiterShift == 0 || old&(mutexLocked|mutexWoken) != 0 { // 没有等待者，或者有唤醒的waiter，或者锁原来已加锁
                return
            }
            new = (old - 1<<mutexWaiterShift) | mutexWoken // 新状态，准备唤醒goroutine，并设置唤醒标志
            if atomic.CompareAndSwapInt32(&m.state, old, new) {
                runtime.Semrelease(&m.sema)
                return
            }
            old = m.state
        }
    }
```
下面我来给你解释一下这个方法。第 3 行是尝试将持有锁的标识设置为未加锁的状态，这是通过减 1 而不是将标志位置零的方式实现。第 4 到 6 行还会检测原来锁的状态是否已经未加锁的状态，如果是 Unlock 一个未加锁的 Mutex 会直接 panic。不过，即使将加锁置为未加锁的状态，这个方法也不能直接返回，还需要一些额外的操作，因为还可能有一些等待这个锁的 goroutine（有时候我也把它们称之为 waiter）需要通过信号量的方式唤醒它们中的一个。所以接下来的逻辑有两种情况。

第一种情况，如果没有其它的 waiter，说明对这个锁的竞争的 goroutine 只有一个，那就可以直接返回了；如果这个时候有唤醒的 goroutine，或者是又被别人加了锁，那么，无需我们操劳，其它 goroutine 自己干得都很好，当前的这个 goroutine 就可以放心返回了。

第二种情况，如果有等待者，并且没有唤醒的 waiter，那就需要唤醒一个等待的 waiter。在唤醒之前，需要将 waiter 数量减 1，并且将 mutexWoken 标志设置上，这样，Unlock 就可以返回了。

通过这样复杂的检查、判断和设置，我们就可以安全地将一把互斥锁释放了。

**相对于初版的设计，这次的改动主要就是，新来的 goroutine 也有机会先获取到锁，甚至一个 goroutine 可能连续获取到锁，打破了先来先得的逻辑。但是，代码复杂度也显而易见。**

虽然这一版的 Mutex 已经给新来请求锁的 goroutine 一些机会，让它参与竞争，没有空闲的锁或者竞争失败才加入到等待队列中。但是其实还可以进一步优化。我们接着往下看

### 多给些机会

在 2015 年 2 月的改动中，如果新来的 goroutine 或者是被唤醒的 goroutine 首次获取不到锁，它们就会通过自旋（spin，通过循环不断尝试，spin 的逻辑是在[runtime](https://github.com/golang/go/blob/846dce9d05f19a1f53465e62a304dea21b99f910/src/runtime/proc.go#L5580) 实现的）的方式，尝试检查锁是否被释放。在尝试一定的自旋次数后，再执行原来的逻辑。

```go
   func (m *Mutex) Lock() {
        // Fast path: 幸运之路，正好获取到锁
        if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
            return
        }

        awoke := false
        iter := 0
        for { // 不管是新来的请求锁的goroutine, 还是被唤醒的goroutine，都不断尝试请求锁
            old := m.state // 先保存当前锁的状态
            new := old | mutexLocked // 新状态设置加锁标志
            if old&mutexLocked != 0 { // 锁还没被释放
                if runtime_canSpin(iter) { // 还可以自旋
                    if !awoke && old&mutexWoken == 0 && old>>mutexWaiterShift != 0 &&
                        atomic.CompareAndSwapInt32(&m.state, old, old|mutexWoken) {
                        awoke = true
                    }
                    runtime_doSpin()
                    iter++
                    continue // 自旋，再次尝试请求锁
                }
                new = old + 1<<mutexWaiterShift
            }
            if awoke { // 唤醒状态
                if new&mutexWoken == 0 {
                    panic("sync: inconsistent mutex state")
                }
                new &^= mutexWoken // 新状态清除唤醒标记
            }
            if atomic.CompareAndSwapInt32(&m.state, old, new) {
                if old&mutexLocked == 0 { // 旧状态锁已释放，新状态成功持有了锁，直接返回
                    break
                }
                runtime_Semacquire(&m.sema) // 阻塞等待
                awoke = true // 被唤醒
                iter = 0
            }
        }
    }
```


这次的优化，增加了第 13 行到 21 行、第 25 行到第 27 行以及第 36 行。我来解释一下主要的逻辑，也就是第 13 行到 21 行。如果可以 spin 的话，第 9 行的 for 循环会重新检查锁是否释放。对于临界区代码执行非常短的场景来说，这是一个非常好的优化。因为临界区的代码耗时很短，锁很快就能释放，而抢夺锁的 goroutine 不用通过休眠唤醒方式等待调度，直接 spin 几次，可能就获得了锁。

### 解决饥饿

经过几次优化，Mutex 的代码越来越复杂，应对高并发争抢锁的场景也更加公平。但是你有没有想过，因为新来的 goroutine 也参与竞争，有可能每次都会被新来的 goroutine 抢到获取锁的机会，在极端情况下，等待中的 goroutine 可能会一直获取不到锁，这就是饥饿问题。

说到这儿，我突然想到了最近看到的一种叫做鹳的鸟。如果鹳妈妈寻找食物很艰难，找到的食物只够一个幼鸟吃的，鹳妈妈就会把食物给最强壮的一只，这样一来，饥饿弱小的幼鸟总是得不到食物吃，最后就会被啄出巢去。先前版本的 Mutex 遇到的也是同样的困境，“悲惨”的 goroutine 总是得不到锁。Mutex 不能容忍这种事情发生。所以，2016 年 Go 1.9 中 Mutex 增加了饥饿模式，让锁变得更公平，不公平的等待时间限制在 1 毫秒，并且修复了一个大 Bug：总是把唤醒的 goroutine 放在等待队列的尾部，会导致更加不公平的等待时间。之后，2018 年，Go 开发者将 fast path 和 slow path 拆成独立的方法，以便内联，提高性能。2019 年也有一个 Mutex 的优化，虽然没有对 Mutex 做修改，但是，对于 Mutex 唤醒后持有锁的那个 waiter，调度器可以有更高的优先级去执行，这已经是很细致的性能优化了。为了避免代码过多，这里只列出当前的 Mutex 实现。想要理解当前的 Mutex，我们需要好好泡一杯茶，仔细地品一品了。当然，现在的 Mutex 代码已经复杂得接近不可读的状态了，而且代码也非常长，删减后占了几乎三页纸。但是，作为第一个要详细介绍的同步原语，我还是希望能更清楚地剖析 Mutex 的实现，向你展示它的演化和为了一个貌似很小的 feature 不得不将代码变得非常复杂的原因。

![img](https://static001.geekbang.org/resource/image/e0/76/e0c23794c8a1d355a7a183400c036276.jpg?wh=3096*1644)

**当然，你也可以暂时略过这一段，以后慢慢品，只需要记住，Mutex 绝不容忍一个 goroutine 被落下，永远没有机会获取锁。不抛弃不放弃是它的宗旨，而且它也尽可能地让等待较长的 goroutine 更有机会获取到锁。**

```
   type Mutex struct {
        state int32
        sema  uint32
    }
    
    const (
        mutexLocked = 1 << iota // mutex is locked
        mutexWoken
        mutexStarving // 从state字段中分出一个饥饿标记
        mutexWaiterShift = iota
    
        starvationThresholdNs = 1e6
    )
    
    func (m *Mutex) Lock() {
        // Fast path: 幸运之路，一下就获取到了锁
        if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
            return
        }
        // Slow path：缓慢之路，尝试自旋竞争或饥饿状态下饥饿goroutine竞争
        m.lockSlow()
    }
    
    func (m *Mutex) lockSlow() {
        var waitStartTime int64
        starving := false // 此goroutine的饥饿标记
        awoke := false // 唤醒标记
        iter := 0 // 自旋次数
        old := m.state // 当前的锁的状态
        for {
            // 锁是非饥饿状态，锁还没被释放，尝试自旋
            if old&(mutexLocked|mutexStarving) == mutexLocked && runtime_canSpin(iter) {
                if !awoke && old&mutexWoken == 0 && old>>mutexWaiterShift != 0 &&
                    atomic.CompareAndSwapInt32(&m.state, old, old|mutexWoken) {
                    awoke = true
                }
                runtime_doSpin()
                iter++
                old = m.state // 再次获取锁的状态，之后会检查是否锁被释放了
                continue
            }
            new := old
            if old&mutexStarving == 0 {
                new |= mutexLocked // 非饥饿状态，加锁
            }
            if old&(mutexLocked|mutexStarving) != 0 {
                new += 1 << mutexWaiterShift // waiter数量加1
            }
            if starving && old&mutexLocked != 0 {
                new |= mutexStarving // 设置饥饿状态
            }
            if awoke {
                if new&mutexWoken == 0 {
                    throw("sync: inconsistent mutex state")
                }
                new &^= mutexWoken // 新状态清除唤醒标记
            }
            // 成功设置新状态
            if atomic.CompareAndSwapInt32(&m.state, old, new) {
                // 原来锁的状态已释放，并且不是饥饿状态，正常请求到了锁，返回
                if old&(mutexLocked|mutexStarving) == 0 {
                    break // locked the mutex with CAS
                }
                // 处理饥饿状态

                // 如果以前就在队列里面，加入到队列头
                queueLifo := waitStartTime != 0
                if waitStartTime == 0 {
                    waitStartTime = runtime_nanotime()
                }
                // 阻塞等待
                runtime_SemacquireMutex(&m.sema, queueLifo, 1)
                // 唤醒之后检查锁是否应该处于饥饿状态
                starving = starving || runtime_nanotime()-waitStartTime > starvationThresholdNs
                old = m.state
                // 如果锁已经处于饥饿状态，直接抢到锁，返回
                if old&mutexStarving != 0 {
                    if old&(mutexLocked|mutexWoken) != 0 || old>>mutexWaiterShift == 0 {
                        throw("sync: inconsistent mutex state")
                    }
                    // 有点绕，加锁并且将waiter数减1
                    delta := int32(mutexLocked - 1<<mutexWaiterShift)
                    if !starving || old>>mutexWaiterShift == 1 {
                        delta -= mutexStarving // 最后一个waiter或者已经不饥饿了，清除饥饿标记
                    }
                    atomic.AddInt32(&m.state, delta)
                    break
                }
                awoke = true
                iter = 0
            } else {
                old = m.state
            }
        }
    }
    
    func (m *Mutex) Unlock() {
        // Fast path: drop lock bit.
        new := atomic.AddInt32(&m.state, -mutexLocked)
        if new != 0 {
            m.unlockSlow(new)
        }
    }
    
    func (m *Mutex) unlockSlow(new int32) {
        if (new+mutexLocked)&mutexLocked == 0 {
            throw("sync: unlock of unlocked mutex")
        }
        if new&mutexStarving == 0 {
            old := new
            for {
                if old>>mutexWaiterShift == 0 || old&(mutexLocked|mutexWoken|mutexStarving) != 0 {
                    return
                }
                new = (old - 1<<mutexWaiterShift) | mutexWoken
                if atomic.CompareAndSwapInt32(&m.state, old, new) {
                    runtime_Semrelease(&m.sema, false, 1)
                    return
                }
                old = m.state
            }
        } else {
            runtime_Semrelease(&m.sema, true, 1)
        }
    }
```


跟之前的实现相比，当前的 Mutex 最重要的变化，就是增加饥饿模式。第 12 行将饥饿模式的最大等待时间阈值设置成了 1 毫秒，这就意味着，一旦等待者等待的时间超过了这个阈值，Mutex 的处理就有可能进入饥饿模式，优先让等待者先获取到锁，新来的同学主动谦让一下，给老同志一些机会。通过加入饥饿模式，可以避免把机会全都留给新来的 goroutine，保证了请求锁的 goroutine 获取锁的公平性，对于我们使用锁的业务代码来说，不会有业务一直等待锁不被处理。那么，接下来的部分就是选学内容了。如果你还有精力，并且对饥饿模式很感兴趣，那就跟着我一起继续来挑战吧。如果你现在理解起来觉得有困难，也没关系，后面可以随时回来复习。

### 饥饿模式和正常模式

Mutex 可能处于两种操作模式下：正常模式和饥饿模式。接下来我们分析一下 Mutex 对饥饿模式和正常模式的处理。请求锁时调用的 Lock 方法中一开始是 fast path，这是一个幸运的场景，当前的 goroutine 幸运地获得了锁，没有竞争，直接返回，否则就进入了 lockSlow 方法。这样的设计，方便编译器对 Lock 方法进行内联，你也可以在程序开发中应用这个技巧。正常模式下，waiter 都是进入先入先出队列，被唤醒的 waiter 并不会直接持有锁，而是要和新来的 goroutine 进行竞争。新来的 goroutine 有先天的优势，它们正在 CPU 中运行，可能它们的数量还不少，所以，在高并发情况下，被唤醒的 waiter 可能比较悲剧地获取不到锁，这时，它会被插入到队列的前面。如果 waiter 获取不到锁的时间超过阈值 1 毫秒，那么，这个 Mutex 就进入到了饥饿模式。在饥饿模式下，Mutex 的拥有者将直接把锁交给队列最前面的 waiter。新来的 goroutine 不会尝试获取锁，即使看起来锁没有被持有，它也不会去抢，也不会 spin，它会乖乖地加入到等待队列的尾部。如果拥有 Mutex 的 waiter 发现下面两种情况的其中之一，它就会把这个 Mutex 转换成正常模式:



此 waiter 已经是队列中的最后一个 waiter 了，没有其它的等待锁的 goroutine 了；此 waiter 的等待时间小于 1 毫秒。

正常模式拥有更好的性能，因为即使有等待抢锁的 waiter，goroutine 也可以连续多次获取到锁。饥饿模式是对公平性和性能的一种平衡，它避免了某些 goroutine 长时间的等待锁。在饥饿模式下，优先对待的是那些一直在等待的 waiter。

**接下来，我们逐步分析下 Mutex 代码的关键行，彻底搞清楚饥饿模式的细节。**

我们从请求锁（lockSlow）的逻辑看起。第 9 行对 state 字段又分出了一位，用来标记锁是否处于饥饿状态。现在一个 state 的字段被划分成了阻塞等待的 waiter 数量、饥饿标记、唤醒标记和持有锁的标记四个部分。第 25 行记录此 goroutine 请求锁的初始时间，第 26 行标记是否处于饥饿状态，第 27 行标记是否是唤醒的，第 28 行记录 spin 的次数。第 31 行到第 40 行和以前的逻辑类似，只不过加了一个不能是饥饿状态的逻辑。它会对正常状态抢夺锁的 goroutine 尝试 spin，和以前的目的一样，就是在临界区耗时很短的情况下提高性能。第 42 行到第 44 行，非饥饿状态下抢锁。怎么抢？就是要把 state 的锁的那一位，置为加锁状态，后续 CAS 如果成功就可能获取到了锁。第 46 行到第 48 行，如果锁已经被持有或者锁处于饥饿状态，我们最好的归宿就是等待，所以 waiter 的数量加 1。第 49 行到第 51 行，如果此 goroutine 已经处在饥饿状态，并且锁还被持有，那么，我们需要把此 Mutex 设置为饥饿状态。第 52 行到第 57 行，是清除 mutexWoken 标记，因为不管是获得了锁还是进入休眠，我们都需要清除 mutexWoken 标记。第 59 行就是尝试使用 CAS 设置 state。如果成功，第 61 行到第 63 行是检查原来的锁的状态是未加锁状态，并且也不是饥饿状态的话就成功获取了锁，返回。第 67 行判断是否第一次加入到 waiter 队列。到这里，你应该就能明白第 25 行为什么不对 waitStartTime 进行初始化了，我们需要利用它在这里进行条件判断。第 72 行将此 waiter 加入到队列，如果是首次，加入到队尾，先进先出。如果不是首次，那么加入到队首，这样等待最久的 goroutine 优先能够获取到锁。此 goroutine 会进行休眠。第 74 行判断此 goroutine 是否处于饥饿状态。注意，执行这一句的时候，它已经被唤醒了。第 77 行到第 88 行是对锁处于饥饿状态下的一些处理。第 82 行设置一个标志，这个标志稍后会用来加锁，而且还会将 waiter 数减 1。第 84 行，设置标志，在没有其它的 waiter 或者此 goroutine 等待还没超过 1 毫秒，则会将 Mutex 转为正常状态。第 86 行则是将这个标识应用到 state 字段上。释放锁（Unlock）时调用的 Unlock 的 fast path 不用多少，所以我们主要看 unlockSlow 方法就行。如果 Mutex 处于饥饿状态，第 123 行直接唤醒等待队列中的 waiter。如果 Mutex 处于正常状态，如果没有 waiter，或者已经有在处理的情况了，那么释放就好，不做额外的处理（第 112 行到第 114 行）。否则，waiter 数减 1，mutexWoken 标志设置上，通过 CAS 更新 state 的值（第 115 行到第 119 行）。



### 总结

“罗马不是一天建成的”，Mutex 的设计也是从简单设计到复杂处理逐渐演变的。初版的 Mutex 设计非常简洁，充分展示了 Go 创始者的简单、简洁的设计哲学。但是，随着大家的使用，逐渐暴露出一些缺陷，为了弥补这些缺陷，Mutex 不得不越来越复杂。有一点值得我们学习的，同时也体现了 Go 创始者的哲学，就是他们强调 Go 语言和标准库的稳定性，新版本要向下兼容，用新的版本总能编译老的代码。Go 语言从出生到现在已经 10 多年了，这个 Mutex 对外的接口却没有变化，依然向下兼容，即使现在 Go 出了两个版本，每个版本也会向下兼容，保持 Go 语言的稳定性，你也能领悟他们软件开发和设计的思想。还有一点，你也可以观察到，为了一个程序 20% 的特性，你可能需要添加 80% 的代码，这也是程序越来越复杂的原因。所以，最开始的时候，如果能够有一个清晰而且易于扩展的设计，未来增加新特性时，也会更加方便。

## 03｜Mutex：4种易错场景大盘点

上一讲，我带你一起领略了 Mutex 的架构演进之美，现在我们已经清楚 Mutex 的实现细节了。当前 Mutex 的实现貌似非常复杂，其实主要还是针对饥饿模式和公平性问题，做了一些额外处理。但是，我们在第一讲中已经体验过了，Mutex 使用起来还是非常简单的，毕竟，它只有 Lock 和 Unlock 两个方法，使用起来还能复杂到哪里去？正常使用 Mutex 时，确实是这样的，很简单，基本不会有什么错误，即使出现错误，也是在一些复杂的场景中，比如跨函数调用 Mutex 或者是在重构或者修补 Bug 时误操作。但是，我们使用 Mutex 时，确实会出现一些 Bug，比如说忘记释放锁、重入锁、复制已使用了的 Mutex 等情况。那在这一讲中，我们就一起来看看使用 Mutex 常犯的几个错误，做到“Bug 提前知，后面早防范”。

### 常见的 4 种错误场景

我总结了一下，使用 Mutex 常见的错误场景有 4 类，分别是 Lock/Unlock 不是成对出现、Copy 已使用的 Mutex、重入和死锁。下面我们一一来看。

#### Lock/Unlock 不是成对出现

Lock/Unlock 没有成对出现，就意味着会出现死锁的情况，或者是因为 Unlock 一个未加锁的 Mutex 而导致 panic。我们先来看看缺少 Unlock 的场景，常见的有三种情况：

代码中有太多的 if-else 分支，可能在某个分支中漏写了 Unlock；在重构的时候把 Unlock 给删除了；Unlock 误写成了 Lock。

在这种情况下，锁被获取之后，就不会被释放了，这也就意味着，其它的 goroutine 永远都没机会获取到锁。我们再来看缺少 Lock 的场景，这就很简单了，一般来说就是误操作删除了 Lock。 比如先前使用 Mutex 都是正常的，结果后来其他人重构代码的时候，由于对代码不熟悉，或者由于开发者的马虎，把 Lock 调用给删除了，或者注释掉了。比如下面的代码，mu.Lock() 一行代码被删除了，直接 Unlock 一个未加锁的 Mutex 会 panic：

```go
func foo() {
    var mu sync.Mutex
    defer mu.Unlock()
    fmt.Println("hello world!")
}
```
运行的时候 panic：

![img](https://static001.geekbang.org/resource/image/55/4f/5597316079a8fa37abef2a82bdac7b4f.png?wh=831*189)

### Copy 已使用的 Mutex

第二种误用是 Copy 已使用的 Mutex。在正式分析这个错误之前，我先交代一个小知识点，那就是 Package sync 的同步原语在使用后是不能复制的。我们知道 Mutex 是最常用的一个同步原语，那它也是不能复制的。为什么呢？原因在于，Mutex 是一个有状态的对象，它的 state 字段记录这个锁的状态。如果你要复制一个已经加锁的 Mutex 给一个新的变量，那么新的刚初始化的变量居然被加锁了，这显然不符合你的期望，因为你期望的是一个零值的 Mutex。关键是在并发环境下，你根本不知道要复制的 Mutex 状态是什么，因为要复制的 Mutex 是由其它 goroutine 并发访问的，状态可能总是在变化。当然，你可能说，你说的我都懂，你的警告我都记下了，但是实际在使用的时候，一不小心就踩了这个坑，我们来看一个例子。

```
type Counter struct {
    sync.Mutex
    Count int
}


func main() {
    var c Counter
    c.Lock()
    defer c.Unlock()
    c.Count++
    foo(c) // 复制锁
}

// 这里Counter的参数是通过复制的方式传入的
func foo(c Counter) {
    c.Lock()
    defer c.Unlock()
    fmt.Println("in foo")
}
```
第 12 行在调用 foo 函数的时候，调用者会复制 Mutex 变量 c 作为 foo 函数的参数，不幸的是，复制之前已经使用了这个锁，这就导致，复制的 Counter 是一个带状态 Counter。怎么办呢？Go 在运行时，有死锁的检查机制（[checkdead()](https://golang.org/src/runtime/proc.go?h=checkdead#L4345)  方法），它能够发现死锁的 goroutine。这个例子中因为复制了一个使用了的 Mutex，导致锁无法使用，程序处于死锁的状态。程序运行的时候，死锁检查机制能够发现这种死锁情况并输出错误信息，如下图中错误信息以及错误堆栈：

![img](https://static001.geekbang.org/resource/image/cf/ee/cfb7a4a0e744c5ff534a676fd830d0ee.png?wh=777*251)

你肯定不想运行的时候才发现这个因为复制 Mutex 导致的死锁问题，那么你怎么能够及时发现问题呢？可以使用 vet 工具，把检查写在 Makefile 文件中，在持续集成的时候跑一跑，这样可以及时发现问题，及时修复。我们可以使用 go vet 检查这个 Go 文件：

![img](https://static001.geekbang.org/resource/image/fa/b8/fa56520yy37009ca58d6640a933f01b8.png?wh=665*94)

你看，使用这个工具就可以发现 Mutex 复制的问题，错误信息显示得很清楚，是在调用 foo 函数的时候发生了 lock value 复制的情况，还告诉我们出问题的代码行数以及 copy lock 导致的错误。那么，vet 工具是怎么发现 Mutex 复制使用问题的呢？我带你简单分析一下。检查是通过[copylock](https://github.com/golang/tools/blob/master/go/analysis/passes/copylock/copylock.go)分析器静态分析实现的。这个分析器会分析函数调用、range 遍历、复制、声明、函数返回值等位置，有没有锁的值 copy 的情景，以此来判断有没有问题。可以说，只要是实现了 Locker 接口，就会被分析。我们看到，下面的代码就是确定什么类型会被分析，其实就是实现了 Lock/Unlock 两个方法的 Locker 接口：

```
var lockerType *types.Interface
  
  // Construct a sync.Locker interface type.
  func init() {
    nullary := types.NewSignature(nil, nil, nil, false) // func()
    methods := []*types.Func{
      types.NewFunc(token.NoPos, nil, "Lock", nullary),
      types.NewFunc(token.NoPos, nil, "Unlock", nullary),
    }
    lockerType = types.NewInterface(methods, nil).Complete()
  }
```
其实，有些没有实现 Locker 接口的同步原语（比如 WaitGroup），也能被分析。我先卖个关子，后面我们会介绍这种情况是怎么实现的。

### 重入

接下来，我们来讨论“重入”这个问题。在说这个问题前，我先解释一下个概念，叫“可重入锁”。如果你学过 Java，可能会很熟悉 ReentrantLock，就是可重入锁，这是 Java 并发包中非常常用的一个同步原语。它的基本行为和互斥锁相同，但是加了一些扩展功能。如果你没接触过 Java，也没关系，这里只是提一下，帮助会 Java 的同学对比来学。那下面我来具体讲解可重入锁是咋回事儿。当一个线程获取锁时，如果没有其它线程拥有这个锁，那么，这个线程就成功获取到这个锁。之后，如果其它线程再请求这个锁，就会处于阻塞等待的状态。但是，如果拥有这把锁的线程再请求这把锁的话，不会阻塞，而是成功返回，所以叫可重入锁（有时候也叫做递归锁）。只要你拥有这把锁，你可以可着劲儿地调用，比如通过递归实现一些算法，调用者不会阻塞或者死锁。

**了解了可重入锁的概念，那我们来看 Mutex 使用的错误场景。划重点了：Mutex 不是可重入的锁。**

想想也不奇怪，因为 Mutex 的实现中没有记录哪个 goroutine 拥有这把锁。理论上，任何 goroutine 都可以随意地 Unlock 这把锁，所以没办法计算重入条件，毕竟，“臣妾做不到啊”！所以，一旦误用 Mutex 的重入，就会导致报错。下面是一个误用 Mutex 的重入例子：

```go
func foo(l sync.Locker) {
    fmt.Println("in foo")
    l.Lock()
    bar(l)
    l.Unlock()
}


func bar(l sync.Locker) {
    l.Lock()
    fmt.Println("in bar")
    l.Unlock()
}


func main() {
    l := &sync.Mutex{}
    foo(l)
}
```


写完这个 Mutex 重入的例子后，运行一下，你会发现类似下面的错误。程序一直在请求锁，但是一直没有办法获取到锁，结果就是 Go 运行时发现死锁了，没有其它地方能够释放锁让程序运行下去，你通过下面的错误堆栈信息就能定位到哪一行阻塞请求锁：

![img](https://static001.geekbang.org/resource/image/0b/79/0bc98ef74c15d9640806d52bf030f979.png?wh=855*299)



学到这里，你可能要问了，虽然标准库 Mutex 不是可重入锁，但是如果我就是想要实现一个可重入锁，可以吗？可以，那我们就自己实现一个。这里的关键就是，实现的锁要能记住当前是哪个 goroutine 持有这个锁。我来提供两个方案。

方案一：通过 hacker 的方式获取到 goroutine id，记录下获取锁的 goroutine id，它可以实现 Locker 接口。方案二：调用 Lock/Unlock 方法时，由 goroutine 提供一个 token，用来标识它自己，而不是我们通过 hacker 的方式获取到 goroutine id，但是，这样一来，就不满足 Locker 接口了。

可重入锁（递归锁）解决了代码重入或者递归调用带来的死锁问题，同时它也带来了另一个好处，就是我们可以要求，只有持有锁的 goroutine 才能 unlock 这个锁。这也很容易实现，因为在上面这两个方案中，都已经记录了是哪一个 goroutine 持有这个锁。下面我们具体来看这两个方案怎么实现。

**方案一：goroutine id**

这个方案的关键第一步是获取 goroutine id，方式有两种，分别是简单方式和 hacker 方式。简单方式，就是通过 runtime.Stack 方法获取栈帧信息，栈帧信息里包含 goroutine id。你可以看看上面 panic 时候的贴图，goroutine id 明明白白地显示在那里。runtime.Stack 方法可以获取当前的 goroutine 信息，第二个参数为 true 会输出所有的 goroutine 信息，信息的格式如下：

```
goroutine 1 [running]:
main.main()
        ....../main.go:19 +0xb1
```
第一行格式为 goroutine xxx，其中 xxx 就是 goroutine id，你只要解析出这个 id 即可。解析的方法可以采用下面的代码：

```go
func GoID() int {
    var buf [64]byte
    n := runtime.Stack(buf[:], false)
    // 得到id字符串
    idField := strings.Fields(strings.TrimPrefix(string(buf[:n]), "goroutine "))[0]
    id, err := strconv.Atoi(idField)
    if err != nil {
        panic(fmt.Sprintf("cannot get goroutine id: %v", err))
    }
    return id
}
```


了解了简单方式，接下来我们来看 hacker 的方式，这也是我们方案一采取的方式。首先，我们获取运行时的 g 指针，反解出对应的 g 的结构。每个运行的 goroutine 结构的 g 指针保存在当前 goroutine 的一个叫做 TLS 对象中。

第一步：我们先获取到 TLS 对象；第二步：再从 TLS 中获取 goroutine 结构的 g 指针；第三步：再从 g 指针中取出 goroutine id。

需要注意的是，不同 Go 版本的 goroutine 的结构可能不同，所以需要根据 Go 的[不同版本](https://github.com/golang/go/blob/89f687d6dbc11613f715d1644b4983905293dd33/src/runtime/runtime2.go#L412)进行调整。当然了，如果想要搞清楚各个版本的 goroutine 结构差异，所涉及的内容又过于底层而且复杂，学习成本太高。怎么办呢？我们可以重点关注一些库。我们没有必要重复发明轮子，直接使用第三方的库来获取 goroutine id 就可以了。好消息是现在已经有很多成熟的方法了，可以支持多个 Go 版本的 goroutine id，给你推荐一个常用的库：[petermattis/goid](petermattis/goid)。知道了如何获取 goroutine id，接下来就是最后的关键一步了，我们实现一个可以使用的可重入锁：

```
// RecursiveMutex 包装一个Mutex,实现可重入
type RecursiveMutex struct {
    sync.Mutex
    owner     int64 // 当前持有锁的goroutine id
    recursion int32 // 这个goroutine 重入的次数
}

func (m *RecursiveMutex) Lock() {
    gid := goid.Get()
    // 如果当前持有锁的goroutine就是这次调用的goroutine,说明是重入
    if atomic.LoadInt64(&m.owner) == gid {
        m.recursion++
        return
    }
    m.Mutex.Lock()
    // 获得锁的goroutine第一次调用，记录下它的goroutine id,调用次数加1
    atomic.StoreInt64(&m.owner, gid)
    m.recursion = 1
}

func (m *RecursiveMutex) Unlock() {
    gid := goid.Get()
    // 非持有锁的goroutine尝试释放锁，错误的使用
    if atomic.LoadInt64(&m.owner) != gid {
        panic(fmt.Sprintf("wrong the owner(%d): %d!", m.owner, gid))
    }
    // 调用次数减1
    m.recursion--
    if m.recursion != 0 { // 如果这个goroutine还没有完全释放，则直接返回
        return
    }
    // 此goroutine最后一次调用，需要释放锁
    atomic.StoreInt64(&m.owner, -1)
    m.Mutex.Unlock()
}
```
上面这段代码你可以拿来即用。我们一起来看下这个实现，真是非常巧妙，它相当于给 Mutex 打一个补丁，解决了记录锁的持有者的问题。可以看到，我们用 owner 字段，记录当前锁的拥有者 goroutine 的 id；recursion 是辅助字段，用于记录重入的次数。有一点，我要提醒你一句，尽管拥有者可以多次调用 Lock，但是也必须调用相同次数的 Unlock，这样才能把锁释放掉。这是一个合理的设计，可以保证 Lock 和 Unlock 一一对应。

**方案二：token**

方案一是用 goroutine id 做 goroutine 的标识，我们也可以让 goroutine 自己来提供标识。不管怎么说，Go 开发者不期望你利用 goroutine id 做一些不确定的东西，所以，他们没有暴露获取 goroutine id 的方法。下面的代码是第二种方案。调用者自己提供一个 token，获取锁的时候把这个 token 传入，释放锁的时候也需要把这个 token 传入。通过用户传入的 token 替换方案一中 goroutine id，其它逻辑和方案一一致。

```
// Token方式的递归锁
type TokenRecursiveMutex struct {
    sync.Mutex
    token     int64
    recursion int32
}

// 请求锁，需要传入token
func (m *TokenRecursiveMutex) Lock(token int64) {
    if atomic.LoadInt64(&m.token) == token { //如果传入的token和持有锁的token一致，说明是递归调用
        m.recursion++
        return
    }
    m.Mutex.Lock() // 传入的token不一致，说明不是递归调用
    // 抢到锁之后记录这个token
    atomic.StoreInt64(&m.token, token)
    m.recursion = 1
}

// 释放锁
func (m *TokenRecursiveMutex) Unlock(token int64) {
    if atomic.LoadInt64(&m.token) != token { // 释放其它token持有的锁
        panic(fmt.Sprintf("wrong the owner(%d): %d!", m.token, token))
    }
    m.recursion-- // 当前持有这个锁的token释放锁
    if m.recursion != 0 { // 还没有回退到最初的递归调用
        return
    }
    atomic.StoreInt64(&m.token, 0) // 没有递归调用了，释放锁
    m.Mutex.Unlock()
}

```
**死锁**

接下来，我们来看第四种错误场景：死锁。我先解释下什么是死锁。两个或两个以上的进程（或线程，goroutine）在执行过程中，因争夺共享资源而处于一种互相等待的状态，如果没有外部干涉，它们都将无法推进下去，此时，我们称系统处于死锁状态或系统产生了死锁。我们来分析一下死锁产生的必要条件。如果你想避免死锁，只要破坏这四个条件中的一个或者几个，就可以了。

互斥： 至少一个资源是被排他性独享的，其他线程必须处于等待状态，直到资源被释放。持有和等待：goroutine 持有一个资源，并且还在请求其它 goroutine 持有的资源，也就是咱们常说的“吃着碗里，看着锅里”的意思。不可剥夺：资源只能由持有它的 goroutine 来释放。环路等待：一般来说，存在一组等待进程，P={P1，P2，…，PN}，P1 等待 P2 持有的资源，P2 等待 P3 持有的资源，依此类推，最后是 PN 等待 P1 持有的资源，这就形成了一个环路等待的死结。

![img](https://static001.geekbang.org/resource/image/4a/d5/4ace1eecf856ef80607yyb6f7a45abd5.jpg?wh=2106*1597)

你看，死锁问题还真是挺有意思的，所以有很多人研究这个事儿。一个经典的死锁问题就是哲学家就餐问题，我不做介绍了，你可以点击链接进一步了解。其实，死锁问题在现实生活中也比比皆是。

举个例子。有一次我去派出所开证明，派出所要求物业先证明我是本物业的业主，但是，物业要我提供派出所的证明，才能给我开物业证明，结果就陷入了死锁状态。你可以把派出所和物业看成两个 goroutine，派出所证明和物业证明是两个资源，双方都持有自己的资源而要求对方的资源，而且自己的资源自己持有，不可剥夺。这是一个最简单的只有两个 goroutine 相互等待的死锁的例子，转化成代码如下：

```go
package main


import (
    "fmt"
    "sync"
    "time"
)


func main() {
    // 派出所证明
    var psCertificate sync.Mutex
    // 物业证明
    var propertyCertificate sync.Mutex


    var wg sync.WaitGroup
    wg.Add(2) // 需要派出所和物业都处理


    // 派出所处理goroutine
    go func() {
        defer wg.Done() // 派出所处理完成


        psCertificate.Lock()
        defer psCertificate.Unlock()


        // 检查材料
        time.Sleep(5 * time.Second)
        // 请求物业的证明
        propertyCertificate.Lock()
        propertyCertificate.Unlock()
    }()


    // 物业处理goroutine
    go func() {
        defer wg.Done() // 物业处理完成


        propertyCertificate.Lock()
        defer propertyCertificate.Unlock()


        // 检查材料
        time.Sleep(5 * time.Second)
        // 请求派出所的证明
        psCertificate.Lock()
        psCertificate.Unlock()
    }()


    wg.Wait()
    fmt.Println("成功完成")
}
```
这个程序没有办法运行成功，因为派出所的处理和物业的处理是一个环路等待的死结。

![img](https://static001.geekbang.org/resource/image/3e/f4/3ea07805dea9d33d5a5c1a8244a7ccf4.png?wh=854*262)

Go 运行时，有死锁探测的功能，能够检查出是否出现了死锁的情况，如果出现了，这个时候你就需要调整策略来处理了。你可以引入一个第三方的锁，大家都依赖这个锁进行业务处理，比如现在政府推行的一站式政务服务中心。或者是解决持有等待问题，物业不需要看到派出所的证明才给开物业证明，等等。好了，到这里，我给你讲了使用 Mutex 常见的 4 类问题。你是不是觉得，哎呀，这几类问题也太不应该了吧，真的会有人犯这么基础的错误吗？还真是有。虽然 Mutex 使用起来很简单，但是，仍然可能出现使用错误的问题。而且，就连一些经验丰富的开发人员，也会出现一些 Mutex 使用的问题。接下来，我就带你围观几个非常流行的 Go 开发项目，看看这些错误是怎么产生和修复的。

### 流行的 Go 开发项目踩坑记

#### Docker

Docker 容器是一个开源的应用容器引擎，开发者可以以统一的方式，把他们的应用和依赖包打包到一个可移植的容器中，然后发布到任何安装了 docker 引擎的服务器上。Docker 是使用 Go 开发的，也算是 Go 的一个杀手级产品了，它的 Mutex 相关的 Bug 也不少，我们来看几个典型的 Bug。

#### issue 36114

Docker 的issue 36114 是一个死锁问题。原因在于，hotAddVHDsAtStart 方法执行的时候，执行了加锁 svm 操作。但是，在其中调用 hotRemoveVHDsAtStart 方法时，这个 hotRemoveVHDsAtStart 方法也是要加锁 svm 的。很不幸，Go 标准库中的 Mutex 是不可重入的，所以，代码执行到这里，就出现了死锁的现象。

![img](https://static001.geekbang.org/resource/image/da/8c/dac838666ee09c98dd9ac5db479aae8c.png?wh=1206*654)



针对这个问题，解决办法就是，再提供一个不需要锁的 hotRemoveVHDsNoLock 方法，避免 Mutex 的重入。

### issue 34881

issue 34881本来是修复 Docker 的一个简单问题，如果节点在初始化的时候，发现自己不是一个 swarm mananger，就快速返回，这个修复就几行代码，你看出问题来了吗？

![img](https://static001.geekbang.org/resource/image/bf/34/bf78904a947d4228dc006fff94f97334.png?wh=897*325)

在第 34 行，节点发现不满足条件就返回了，但是，c.mu 这个锁没有释放！为什么会出现这个问题呢？其实，这是在重构或者添加新功能的时候经常犯的一个错误，因为不太了解上下文，或者是没有仔细看函数的逻辑，从而导致锁没有被释放。现在的 Docker 当然已经没有这个问题了。

![img](https://static001.geekbang.org/resource/image/1c/f1/1c7a5f8e12f642d82aac8045c046c6f1.png?wh=690*170)

这样的 issue 还有很多，我就不一一列举了。我给你推荐几个关于 Mutex 的 issue 或者 pull request，你可以关注一下，分别是 36840、37583、35517、35482、33305、32826、30696、29554、29191、28912、26507 等。

### Kubernetes

#### issue 72361

issue 72361 增加 Mutex 为了保护资源。这是为了解决 data race 问题而做的一个修复，修复方法也很简单，使用互斥锁即可，这也是我们解决 data race 时常用的方法。

![img](https://static001.geekbang.org/resource/image/21/31/2171a7a0de179904ceba463026ee7231.png?wh=755*375)

#### issue 45192

issue 45192也是一个返回时忘记 Unlock 的典型例子，和 docker issue 34881 犯的错误都是一样的。两大知名项目的开发者都犯了这个错误，所以，你就可以知道，引入这个 Bug 是多么容易，记住晁老师这句话：保证 Lock/Unlock 成对出现，尽可能采用 defer mutex.Unlock 的方式，把它们成对、紧凑地写在一起。

![img](https://static001.geekbang.org/resource/image/ba/61/ba0f7671fd64951a47365e46ab68db61.png?wh=985*429)

除了这些，我也建议你关注一下其它的 Mutex 相关的 issue，比如 71617、70605 等。

### gRPC

gRPC 是 Google 发起的一个开源远程过程调用 （Remote procedure call）系统。该系统基于 HTTP/2 协议传输，使用 Protocol Buffers 作为接口描述语言。它提供 Go 语言的实现。即使是 Google 官方出品的系统，也有一些 Mutex 的 issue。

#### issue 795

issue 795是一个你可能想不到的 bug，那就是将 Unlock 误写成了 Lock。

![img](https://static001.geekbang.org/resource/image/b6/f0/b6e97a6938586e95c3427e693eb712f0.png?wh=580*223)

关于这个项目，还有一些其他的为了保护共享资源而添加 Mutex 的 issue，比如 1318、2074、2542 等。

### etcd

etcd 是一个非常知名的分布式一致性的 key-value 存储技术， 被用来做配置共享和服务发现。

#### issue 10419

issue 10419是一个锁重入导致的问题。 Store 方法内对请求了锁，而调用的 Compact 的方法内又请求了锁，这个时候，会导致死锁，一直等待，解决办法就是提供不需要加锁的 Compact 方法。

![img](https://static001.geekbang.org/resource/image/5f/7f/5fed22fb735c107d130477562c28477f.png?wh=878*344)

### 总结

这节课，我们学习了 Mutex 的一些易错场景，而且，我们还分析了流行的 Go 开源项目的错误，我也给你分享了我自己在开发中的经验总结。需要强调的是，**手误和重入导致的死锁，是最常见的使用 Mutex 的 Bug**。Go 死锁探测工具只能探测整个程序是否因为死锁而冻结了，不能检测出一组 goroutine 死锁导致的某一块业务冻结的情况。你还可以通过 Go 运行时自带的死锁检测工具，或者是第三方的工具（比如go-deadlock、go-tools）进行检查，这样可以尽早发现一些死锁的问题。不过，有些时候，死锁在某些特定情况下才会被触发，所以，如果你的测试或者短时间的运行没问题，不代表程序一定不会有死锁问题。并发程序最难跟踪调试的就是很难重现，因为并发问题不是按照我们指定的顺序执行的，由于计算机调度的问题和事件触发的时机不同，死锁的 Bug 可能会在极端的情况下出现。通过搜索日志、查看日志，我们能够知道程序有异常了，比如某个流程一直没有结束。这个时候，可以通过 Go pprof 工具分析，它提供了一个 block profiler 监控阻塞的 goroutine。除此之外，我们还可以查看全部的 goroutine 的堆栈信息，通过它，你可以查看阻塞的 groutine 究竟阻塞在哪一行哪一个对象上了。

## 04｜ Mutex：骇客编程，如何拓展额外功能？

前面三讲，我们学习了互斥锁 Mutex 的基本用法、实现原理以及易错场景，可以说是涵盖了互斥锁的方方面面。如果你能熟练掌握这些内容，那么，在大多数的开发场景中，你都可以得心应手。但是，在一些特定的场景中，这些基础功能是不足以应对的。这个时候，我们就需要开发一些扩展功能了。我来举几个例子。比如说，我们知道，如果互斥锁被某个 goroutine 获取了，而且还没有释放，那么，其他请求这把锁的 goroutine，就会阻塞等待，直到有机会获得这把锁。有时候阻塞并不是一个很好的主意，比如你请求锁更新一个计数器，如果获取不到锁的话没必要等待，大不了这次不更新，我下次更新就好了，如果阻塞的话会导致业务处理能力的下降。

再比如，如果我们要监控锁的竞争情况，一个监控指标就是，等待这把锁的 goroutine 数量。我们可以把这个指标推送到时间序列数据库中，再通过一些监控系统（比如 Grafana）展示出来。要知道，**锁是性能下降的“罪魁祸首”之一，所以，有效地降低锁的竞争，就能够很好地提高性能。因此，监控关键互斥锁上等待的 goroutine 的数量，是我们分析锁竞争的激烈程度的一个重要指标。**

实际上，不论是不希望锁的 goroutine 继续等待，还是想监控锁，我们都可以基于标准库中 Mutex 的实现，通过 Hacker 的方式，为 Mutex 增加一些额外的功能。这节课，我就来教你实现几个扩展功能，包括实现 TryLock，获取等待者的数量等指标，以及实现一个线程安全的队列。

### TryLock

我们可以为 Mutex 添加一个 TryLock 的方法，也就是尝试获取排外锁。PS：在 Go 1.18 官方标准库中，已经为 Mutex/RWMutex 增加了 TryLock 方法。这个方法具体是什么意思呢？我来解释一下这里的逻辑。当一个 goroutine 调用这个 TryLock 方法请求锁的时候，如果这把锁没有被其他 goroutine 所持有，那么，这个 goroutine 就持有了这把锁，并返回 true；如果这把锁已经被其他 goroutine 所持有，或者是正在准备交给某个被唤醒的 goroutine，那么，这个请求锁的 goroutine 就直接返回 false，不会阻塞在方法调用上。如下图所示，如果 Mutex 已经被一个 goroutine 持有，调用 Lock 的 goroutine 阻塞排队等待，调用 TryLock 的 goroutine 直接得到一个 false 返回。

![img](https://static001.geekbang.org/resource/image/e7/65/e7787d959b60d66cc3a46ee921098865.jpg?wh=2973*1913)

在实际开发中，如果要更新配置数据，我们通常需要加锁，这样可以避免同时有多个 goroutine 并发修改数据。有的时候，我们也会使用 TryLock。这样一来，当某个 goroutine 想要更改配置数据时，如果发现已经有 goroutine 在更改了，其他的 goroutine 调用 TryLock，返回了 false，这个 goroutine 就会放弃更改。很多语言（比如 Java）都为锁提供了 TryLock 的方法，但是，Go 官方issue 6123有一个讨论（后来一些 issue 中也提到过），标准库的 Mutex 不会添加 TryLock 方法。虽然通过 Go 的 Channel 我们也可以实现 TryLock 的功能，但是基于 Channel 的实现我们会放在 Channel 那一讲中去介绍，这一次我们还是基于 Mutex 去实现，毕竟大部分的程序员还是熟悉传统的同步原语，而且传统的同步原语也不容易出错。所以这节课，还是希望带你掌握基于 Mutex 实现的方法。那怎么实现一个扩展 TryLock 方法的 Mutex 呢？我们直接来看代码。

```
// 复制Mutex定义的常量
const (
    mutexLocked = 1 << iota // 加锁标识位置
    mutexWoken              // 唤醒标识位置
    mutexStarving           // 锁饥饿标识位置
    mutexWaiterShift = iota // 标识waiter的起始bit位置
)

// 扩展一个Mutex结构
type Mutex struct {
    sync.Mutex
}

// 尝试获取锁
func (m *Mutex) TryLock() bool {
    // 如果能成功抢到锁
    if atomic.CompareAndSwapInt32((*int32)(unsafe.Pointer(&m.Mutex)), 0, mutexLocked) {
        return true
    }

    // 如果处于唤醒、加锁或者饥饿状态，这次请求就不参与竞争了，返回false
    old := atomic.LoadInt32((*int32)(unsafe.Pointer(&m.Mutex)))
    if old&(mutexLocked|mutexStarving|mutexWoken) != 0 {
        return false
    }

    // 尝试在竞争的状态下请求锁
    new := old | mutexLocked
    return atomic.CompareAndSwapInt32((*int32)(unsafe.Pointer(&m.Mutex)), old, new)
}
```


第 17 行是一个 fast path，如果幸运，没有其他 goroutine 争这把锁，那么，这把锁就会被这个请求的 goroutine 获取，直接返回。如果锁已经被其他 goroutine 所持有，或者被其他唤醒的 goroutine 准备持有，那么，就直接返回 false，不再请求，代码逻辑在第 23 行。如果没有被持有，也没有其它唤醒的 goroutine 来竞争锁，锁也不处于饥饿状态，就尝试获取这把锁（第 29 行），不论是否成功都将结果返回。因为，这个时候，可能还有其他的 goroutine 也在竞争这把锁，所以，不能保证成功获取这把锁。我们可以写一个简单的测试程序，来测试我们的 TryLock 的机制是否工作。这个测试程序的工作机制是这样子的：程序运行时会启动一个 goroutine 持有这把我们自己实现的锁，经过随机的时间才释放。主 goroutine 会尝试获取这把锁。如果前一个 goroutine 一秒内释放了这把锁，那么，主 goroutine 就有可能获取到这把锁了，输出“got the lock”，否则没有获取到也不会被阻塞，会直接输出“can't get the lock”。

```go
func try() {
    var mu Mutex
    go func() { // 启动一个goroutine持有一段时间的锁
        mu.Lock()
        time.Sleep(time.Duration(rand.Intn(2)) * time.Second)
        mu.Unlock()
    }()

    time.Sleep(time.Second)

    ok := mu.TryLock() // 尝试获取到锁
    if ok { // 获取成功
        fmt.Println("got the lock")
        // do something
        mu.Unlock()
        return
    }

    // 没有获取到
    fmt.Println("can't get the lock")
}
```
### 获取等待者的数量等指标

接下来，我想和你聊聊怎么获取等待者数量等指标。第二讲中，我们已经学习了 Mutex 的结构。先来回顾一下 Mutex 的数据结构，如下面的代码所示。它包含两个字段，state 和 sema。前四个字节（int32）就是 state 字段。

```
type Mutex struct {
    state int32
    sema  uint32
}
```
Mutex 结构中的 state 字段有很多个含义，通过 state 字段，你可以知道锁是否已经被某个 goroutine 持有、当前是否处于饥饿状态、是否有等待的 goroutine 被唤醒、等待者的数量等信息。但是，state 这个字段并没有暴露出来，所以，我们需要想办法获取到这个字段，并进行解析。怎么获取未暴露的字段呢？很简单，我们可以通过 unsafe 的方式实现。我来举一个例子，你一看就明白了。

```
const (
    mutexLocked = 1 << iota // mutex is locked
    mutexWoken
    mutexStarving
    mutexWaiterShift = iota
)

type Mutex struct {
    sync.Mutex
}

func (m *Mutex) Count() int {
    // 获取state字段的值
    v := atomic.LoadInt32((*int32)(unsafe.Pointer(&m.Mutex)))
    v = v >> mutexWaiterShift + (v & mutexLocked)
    return int(v)
```
这个例子的第 14 行通过 unsafe 操作，我们可以得到 state 字段的值。第 15 行我们右移三位（这里的常量 mutexWaiterShift 的值为 3），就得到了当前等待者的数量。如果当前的锁已经被其他 goroutine 持有，那么，我们就稍微调整一下这个值，加上一个 1（第 16 行），你基本上可以把它看作是当前持有和等待这把锁的 goroutine 的总数。state 这个字段的第一位是用来标记锁是否被持有，第二位用来标记是否已经唤醒了一个等待者，第三位标记锁是否处于饥饿状态，通过分析这个 state 字段我们就可以得到这些状态信息。我们可以为这些状态提供查询的方法，这样就可以实时地知道锁的状态了。

```
// 锁是否被持有
func (m *Mutex) IsLocked() bool {
    state := atomic.LoadInt32((*int32)(unsafe.Pointer(&m.Mutex)))
    return state&mutexLocked == mutexLocked
}

// 是否有等待者被唤醒
func (m *Mutex) IsWoken() bool {
    state := atomic.LoadInt32((*int32)(unsafe.Pointer(&m.Mutex)))
    return state&mutexWoken == mutexWoken
}

// 锁是否处于饥饿状态
func (m *Mutex) IsStarving() bool {
    state := atomic.LoadInt32((*int32)(unsafe.Pointer(&m.Mutex)))
    return state&mutexStarving == mutexStarving
}
```
我们可以写一个程序测试一下，比如，在 1000 个 goroutine 并发访问的情况下，我们可以把锁的状态信息输出出来：

```go
func count() {
    var mu Mutex
    for i := 0; i < 1000; i++ { // 启动1000个goroutine
        go func() {
            mu.Lock()
            time.Sleep(time.Second)
            mu.Unlock()
        }()
    }

    time.Sleep(time.Second)
    // 输出锁的信息
    fmt.Printf("waitings: %d, isLocked: %t, woken: %t,  starving: %t\n", mu.Count(), mu.IsLocked(), mu.IsWoken(), mu.IsStarving())
}
```
有一点你需要注意一下，在获取 state 字段的时候，并没有通过 Lock 获取这把锁，所以获取的这个 state 的值是一个瞬态的值，可能在你解析出这个字段之后，锁的状态已经发生了变化。不过没关系，因为你查看的就是调用的那一时刻的锁的状态。

### 使用 Mutex 实现一个线程安全的队列

最后，我们来讨论一下，如何使用 Mutex 实现一个线程安全的队列。为什么要讨论这个话题呢？因为 Mutex 经常会和其他非线程安全（对于 Go 来说，我们其实指的是 goroutine 安全）的数据结构一起，组合成一个线程安全的数据结构。新数据结构的业务逻辑由原来的数据结构提供，而 Mutex 提供了锁的机制，来保证线程安全。比如队列，我们可以通过 Slice 来实现，但是通过 Slice 实现的队列不是线程安全的，出队（Dequeue）和入队（Enqueue）会有 data race 的问题。这个时候，Mutex 就要隆重出场了，通过它，我们可以在出队和入队的时候加上锁的保护。

```
type SliceQueue struct {
    data []interface{}
    mu   sync.Mutex
}

func NewSliceQueue(n int) (q *SliceQueue) {
    return &SliceQueue{data: make([]interface{}, 0, n)}
}

// Enqueue 把值放在队尾
func (q *SliceQueue) Enqueue(v interface{}) {
    q.mu.Lock()
    q.data = append(q.data, v)
    q.mu.Unlock()
}

// Dequeue 移去队头并返回
func (q *SliceQueue) Dequeue() interface{} {
    q.mu.Lock()
    if len(q.data) == 0 {
        q.mu.Unlock()
        return nil
    }
    v := q.data[0]
    q.data = q.data[1:]
    q.mu.Unlock()
    return v
}
```


因为标准库中没有线程安全的队列数据结构的实现，所以，你可以通过 Mutex 实现一个简单的队列。通过 Mutex 我们就可以为一个非线程安全的 data interface{}实现线程安全的访问。

### 总结

好了，我们来做个总结。Mutex 是 package sync 的基石，其他的一些同步原语也是基于它实现的，所以，我们“隆重”地用了四讲来深度学习它。学到后面，你一定能感受到，多花些时间来完全掌握 Mutex 是值得的。今天这一讲我和你分享了几个 Mutex 的拓展功能，这些方法是不是给你带来了一种“骇客”的编程体验呢，通过 Hacker 的方式，我们真的可以让 Mutex 变得更强大。

我们学习了基于 Mutex 实现 TryLock，通过 unsafe 的方式读取到 Mutex 内部的 state 字段，这样，我们就解决了开篇列举的问题，一是不希望锁的 goroutine 继续等待，一是想监控锁。另外，使用 Mutex 组合成更丰富的数据结构是我们常见的场景，今天我们就实现了一个线程安全的队列，未来我们还会讲到实现线程安全的 map 对象。到这里，Mutex 我们就系统学习完了，最后给你总结了一张 Mutex 知识地图，帮你复习一下。

![img](https://static001.geekbang.org/resource/image/5a/0b/5ayy6cd9ec9fe0bcc13113302056ac0b.jpg?wh=2396*3131)

## 05｜ RWMutex：读写锁的实现原理及避坑指南

在前面的四节课中，我们学习了第一个同步原语，即 Mutex，我们使用它来保证读写共享资源的安全性。不管是读还是写，我们都通过 Mutex 来保证只有一个 goroutine 访问共享资源，这在某些情况下有点“浪费”。比如说，在写少读多的情况下，即使一段时间内没有写操作，大量并发的读访问也不得不在 Mutex 的保护下变成了串行访问，这个时候，使用 Mutex，对性能的影响就比较大。怎么办呢？你是不是已经有思路了，对，就是区分读写操作。

我来具体解释一下。如果某个读操作的 goroutine 持有了锁，在这种情况下，其它读操作的 goroutine 就不必一直傻傻地等待了，而是可以并发地访问共享变量，这样我们就可以将串行的读变成并行读，提高读操作的性能。当写操作的 goroutine 持有锁的时候，它就是一个排外锁，其它的写操作和读操作的 goroutine，需要阻塞等待持有这个锁的 goroutine 释放锁。这一类并发读写问题叫作readers-writers 问题，意思就是，同时可能有多个读或者多个写，但是只要有一个线程在执行写操作，其它的线程都不能执行读写操作。**Go 标准库中的 RWMutex（读写锁）就是用来解决这类 readers-writers 问题的**。所以，这节课，我们就一起来学习 RWMutex。我会给你介绍读写锁的使用场景、实现原理以及容易掉入的坑，你一定要记住这些陷阱，避免在实际的开发中犯相同的错误。

### 什么是 RWMutex？

我先简单解释一下读写锁 RWMutex。标准库中的 RWMutex 是一个 reader/writer 互斥锁。RWMutex 在某一时刻只能由任意数量的 reader 持有，或者是只被单个的 writer 持有。RWMutex 的方法也很少，总共有 5 个。

Lock/Unlock：写操作时调用的方法。如果锁已经被 reader 或者 writer 持有，那么，Lock 方法会一直阻塞，直到能获取到锁；Unlock 则是配对的释放锁的方法。RLock/RUnlock：读操作时调用的方法。如果锁已经被 writer 持有的话，RLock 方法会一直阻塞，直到能获取到锁，否则就直接返回；而 RUnlock 是 reader 释放锁的方法。RLocker：这个方法的作用是为读操作返回一个 Locker 接口的对象。它的 Lock 方法会调用 RWMutex 的 RLock 方法，它的 Unlock 方法会调用 RWMutex 的 RUnlock 方法。

RWMutex 的零值是未加锁的状态，所以，当你使用 RWMutex 的时候，无论是声明变量，还是嵌入到其它 struct 中，都不必显式地初始化。我以计数器为例，来说明一下，如何使用 RWMutex 保护共享资源。计数器的 count++操作是写操作，而获取 count 的值是读操作，这个场景非常适合读写锁，因为读操作可以并行执行，写操作时只允许一个线程执行，这正是 readers-writers 问题。在这个例子中，使用 10 个 goroutine 进行读操作，每读取一次，sleep 1 毫秒，同时，还有一个 gorotine 进行写操作，每一秒写一次，这是一个 1 writer-n reader 的读写场景，而且写操作还不是很频繁（一秒一次）：

```go
func main() {
    var counter Counter
    for i := 0; i < 10; i++ { // 10个reader
        go func() {
            for {
                counter.Count() // 计数器读操作
                time.Sleep(time.Millisecond)
            }
        }()
    }

    for { // 一个writer
        counter.Incr() // 计数器写操作
        time.Sleep(time.Second)
    }
}
// 一个线程安全的计数器
type Counter struct {
    mu    sync.RWMutex
    count uint64
}

// 使用写锁保护
func (c *Counter) Incr() {
    c.mu.Lock()
    c.count++
    c.mu.Unlock()
}

// 使用读锁保护
func (c *Counter) Count() uint64 {
    c.mu.RLock()
    defer c.mu.RUnlock()
    return c.count
}
```
可以看到，Incr 方法会修改计数器的值，是一个写操作，我们使用 Lock/Unlock 进行保护。Count 方法会读取当前计数器的值，是一个读操作，我们使用 RLock/RUnlock 方法进行保护。Incr 方法每秒才调用一次，所以，writer 竞争锁的频次是比较低的，而 10 个 goroutine 每毫秒都要执行一次查询，通过读写锁，可以极大提升计数器的性能，因为在读取的时候，可以并发进行。如果使用 Mutex，性能就不会像读写锁这么好。因为多个 reader 并发读的时候，使用互斥锁导致了 reader 要排队读的情况，没有 RWMutex 并发读的性能好。

**如果你遇到可以明确区分 reader 和 writer goroutine 的场景，且有大量的并发读、少量的并发写，并且有强烈的性能需求，你就可以考虑使用读写锁 RWMutex 替换 Mutex。**

在实际使用 RWMutex 的时候，如果我们在 struct 中使用 RWMutex 保护某个字段，一般会把它和这个字段放在一起，用来指示两个字段是一组字段。除此之外，我们还可以采用匿名字段的方式嵌入 struct，这样，在使用这个 struct 时，我们就可以直接调用 Lock/Unlock、RLock/RUnlock 方法了，这和我们前面在01 讲中介绍 Mutex 的使用方法很类似，你可以回去复习一下。

### RWMutex 的实现原理

RWMutex 是很常见的并发原语，很多编程语言的库都提供了类似的并发类型。RWMutex 一般都是基于互斥锁、条件变量（condition variables）或者信号量（semaphores）等并发原语来实现。Go 标准库中的 RWMutex 是基于 Mutex 实现的。readers-writers 问题一般有三类，基于对读和写操作的优先级，读写锁的设计和实现也分成三类。

Read-preferring：读优先的设计可以提供很高的并发性，但是，在竞争激烈的情况下可能会导致写饥饿。这是因为，如果有大量的读，这种设计会导致只有所有的读都释放了锁之后，写才可能获取到锁。Write-preferring：写优先的设计意味着，如果已经有一个 writer 在等待请求锁的话，它会阻止新来的请求锁的 reader 获取到锁，所以优先保障 writer。当然，如果有一些 reader 已经请求了锁的话，新请求的 writer 也会等待已经存在的 reader 都释放锁之后才能获取。所以，写优先级设计中的优先权是针对新来的请求而言的。这种设计主要避免了 writer 的饥饿问题。不指定优先级：这种设计比较简单，不区分 reader 和 writer 优先级，某些场景下这种不指定优先级的设计反而更有效，因为第一类优先级会导致写饥饿，第二类优先级可能会导致读饥饿，这种不指定优先级的访问不再区分读写，大家都是同一个优先级，解决了饥饿的问题。

**Go 标准库中的 RWMutex 设计是 Write-preferring 方案。一个正在阻塞的 Lock 调用会排除新的 reader 请求到锁**。RWMutex 包含一个 Mutex，以及四个辅助字段 writerSem、readerSem、readerCount 和 readerWait：

```
type RWMutex struct {
  w           Mutex   // 互斥锁解决多个writer的竞争
  writerSem   uint32  // writer信号量
  readerSem   uint32  // reader信号量
  readerCount int32   // reader的数量
  readerWait  int32   // writer等待完成的reader的数量
}

const rwmutexMaxReaders = 1 << 30
```
我来简单解释一下这几个字段。字段 w：为 writer 的竞争锁而设计；字段 readerCount：记录当前 reader 的数量（以及是否有 writer 竞争锁）；readerWait：记录 writer 请求锁时需要等待 read 完成的 reader 的数量；writerSem 和 readerSem：都是为了阻塞设计的信号量。

这里的常量 rwmutexMaxReaders，定义了最大的 reader 数量。好了，知道了 RWMutex 的设计方案和具体字段，下面我来解释一下具体的方法实现。

### RLock/RUnlock 的实现

首先，我们看一下移除了 race 等无关紧要的代码后的 RLock 和 RUnlock 方法：

```go
func (rw *RWMutex) RLock() {
    if atomic.AddInt32(&rw.readerCount, 1) < 0 {
            // rw.readerCount是负值的时候，意味着此时有writer等待请求锁，因为writer优先级高，所以把后来的reader阻塞休眠
        runtime_SemacquireMutex(&rw.readerSem, false, 0)
    }
}
func (rw *RWMutex) RUnlock() {
    if r := atomic.AddInt32(&rw.readerCount, -1); r < 0 {
        rw.rUnlockSlow(r) // 有等待的writer
    }
}
func (rw *RWMutex) rUnlockSlow(r int32) {
    if atomic.AddInt32(&rw.readerWait, -1) == 0 {
        // 最后一个reader了，writer终于有机会获得锁了
        runtime_Semrelease(&rw.writerSem, false, 1)
    }
}
```
第 2 行是对 reader 计数加 1。你可能比较困惑的是，readerCount 怎么还可能为负数呢？其实，这是因为，readerCount 这个字段有双重含义：没有 writer 竞争或持有锁时，readerCount 和我们正常理解的 reader 的计数是一样的；但是，如果有 writer 竞争锁或者持有锁时，那么，readerCount 不仅仅承担着 reader 的计数功能，还能够标识当前是否有 writer 竞争或持有锁，在这种情况下，请求锁的 reader 的处理进入第 4 行，阻塞等待锁的释放。

调用 RUnlock 的时候，我们需要将 Reader 的计数减去 1（第 8 行），因为 reader 的数量减少了一个。但是，第 8 行的 AddInt32 的返回值还有另外一个含义。如果它是负值，就表示当前有 writer 竞争锁，在这种情况下，还会调用 rUnlockSlow 方法，检查是不是 reader 都释放读锁了，如果读锁都释放了，那么可以唤醒请求写锁的 writer 了。当一个或者多个 reader 持有锁的时候，竞争锁的 writer 会等待这些 reader 释放完，才可能持有这把锁。打个比方，在房地产行业中有条规矩叫做“买卖不破租赁”，意思是说，就算房东把房子卖了，新业主也不能把当前的租户赶走，而是要等到租约结束后，才能接管房子。这和 RWMutex 的设计是一样的。当 writer 请求锁的时候，是无法改变既有的 reader 持有锁的现实的，也不会强制这些 reader 释放锁，它的优先权只是限定后来的 reader 不要和它抢。所以，rUnlockSlow 将持有锁的 reader 计数减少 1 的时候，会检查既有的 reader 是不是都已经释放了锁，如果都释放了锁，就会唤醒 writer，让 writer 持有锁。

### Lock

RWMutex 是一个多 writer 多 reader 的读写锁，所以同时可能有多个 writer 和 reader。那么，为了避免 writer 之间的竞争，RWMutex 就会使用一个 Mutex 来保证 writer 的互斥。一旦一个 writer 获得了内部的互斥锁，就会反转 readerCount 字段，把它从原来的正整数 readerCount(>=0) 修改为负数（readerCount-rwmutexMaxReaders），让这个字段保持两个含义（既保存了 reader 的数量，又表示当前有 writer）。我们来看下下面的代码。第 5 行，还会记录当前活跃的 reader 数量，所谓活跃的 reader，就是指持有读锁还没有释放的那些 reader。

```go
func (rw *RWMutex) Lock() {
    // 首先解决其他writer竞争问题
    rw.w.Lock()
    // 反转readerCount，告诉reader有writer竞争锁
    r := atomic.AddInt32(&rw.readerCount, -rwmutexMaxReaders) + rwmutexMaxReaders
    // 如果当前有reader持有锁，那么需要等待
    if r != 0 && atomic.AddInt32(&rw.readerWait, r) != 0 {
        runtime_SemacquireMutex(&rw.writerSem, false, 0)
    }
}
```
如果 readerCount 不是 0，就说明当前有持有读锁的 reader，RWMutex 需要把这个当前 readerCount 赋值给 readerWait 字段保存下来（第 7 行）， 同时，这个 writer 进入阻塞等待状态（第 8 行）。每当一个 reader 释放读锁的时候（调用 RUnlock 方法时），readerWait 字段就减 1，直到所有的活跃的 reader 都释放了读锁，才会唤醒这个 writer。



### Unlock

当一个 writer 释放锁的时候，它会再次反转 readerCount 字段。可以肯定的是，因为当前锁由 writer 持有，所以，readerCount 字段是反转过的，并且减去了 rwmutexMaxReaders 这个常数，变成了负数。所以，这里的反转方法就是给它增加 rwmutexMaxReaders 这个常数值。既然 writer 要释放锁了，那么就需要唤醒之后新来的 reader，不必再阻塞它们了，让它们开开心心地继续执行就好了。在 RWMutex 的 Unlock 返回之前，需要把内部的互斥锁释放。释放完毕后，其他的 writer 才可以继续竞争这把锁。

```go
func (rw *RWMutex) Unlock() {
    // 告诉reader没有活跃的writer了
    r := atomic.AddInt32(&rw.readerCount, rwmutexMaxReaders)
    
    // 唤醒阻塞的reader们
    for i := 0; i < int(r); i++ {
        runtime_Semrelease(&rw.readerSem, false, 0)
    }
    // 释放内部的互斥锁
    rw.w.Unlock()
}
```
在这段代码中，我删除了 race 的处理和异常情况的检查，总体看来还是比较简单的。这里有几个重点，我要再提醒你一下。首先，你要理解 readerCount 这个字段的含义以及反转方式。其次，你还要注意字段的更改和内部互斥锁的顺序关系。在 Lock 方法中，是先获取内部互斥锁，才会修改的其他字段；而在 Unlock 方法中，是先修改的其他字段，才会释放内部互斥锁，这样才能保证字段的修改也受到互斥锁的保护。好了，到这里我们就完整学习了 RWMutex 的概念和实现原理。RWMutex 的应用场景非常明确，就是解决 readers-writers 问题。学完了今天的内容，之后当你遇到这类问题时，要优先想到 RWMutex。另外，Go 并发原语代码实现的质量都很高，非常精炼和高效，所以，你可以通过看它们的实现原理，学习一些编程的技巧。当然，还有非常重要的一点就是要知道 reader 或者 writer 请求锁的时候，既有的 reader/writer 和后续请求锁的 reader/writer 之间的（释放锁 / 请求锁）顺序关系。有个很有意思的事儿，就是官方的文档对 RWMutex 介绍是错误的，或者说是[不明确的](https://github.com/golang/go/issues/41555)，在下一个版本（Go 1.16）中，官方会更改对 RWMutex 的介绍，具体是这样的：

A RWMutex is a reader/writer mutual exclusion lock.The lock can be held by any number of readers or a single writer, anda blocked writer also blocks new readers from acquiring the lock.

这个描述是相当精确的，它指出了 RWMutex 可以被谁持有，以及 writer 比后续的 reader 有获取锁的优先级。虽然 RWMutex 暴露的 API 也很简单，使用起来也没有复杂的逻辑，但是和 Mutex 一样，在实际使用的时候，也会很容易踩到一些坑。接下来，我给你重点介绍 3 个常见的踩坑点。

### RWMutex 的 3 个踩坑点

#### 坑点 1：不可复制

前面刚刚说过，RWMutex 是由一个互斥锁和四个辅助字段组成的。我们很容易想到，互斥锁是不可复制的，再加上四个有状态的字段，RWMutex 就更加不能复制使用了。不能复制的原因和互斥锁一样。一旦读写锁被使用，它的字段就会记录它当前的一些状态。这个时候你去复制这把锁，就会把它的状态也给复制过来。但是，原来的锁在释放的时候，并不会修改你复制出来的这个读写锁，这就会导致复制出来的读写锁的状态不对，可能永远无法释放锁。那该怎么办呢？其实，解决方案也和互斥锁一样。你可以借助 vet 工具，在变量赋值、函数传参、函数返回值、遍历数据、struct 初始化等时，检查是否有读写锁隐式复制的情景。

#### 坑点 2：重入导致死锁

读写锁因为重入（或递归调用）导致死锁的情况更多。我先介绍第一种情况。因为读写锁内部基于互斥锁实现对 writer 的并发访问，而互斥锁本身是有重入问题的，所以，writer 重入调用 Lock 的时候，就会出现死锁的现象，这个问题，我们在学习互斥锁的时候已经了解过了。

```go
func foo(l *sync.RWMutex) {
    fmt.Println("in foo")
    l.Lock()
    bar(l)
    l.Unlock()
}

func bar(l *sync.RWMutex) {
    l.Lock()
    fmt.Println("in bar")
    l.Unlock()
}

func main() {
    l := &sync.RWMutex{}
    foo(l)
}
```


运行这个程序，你就会得到死锁的错误输出，在 Go 运行的时候，很容易就能检测出来。第二种死锁的场景有点隐蔽。我们知道，有活跃 reader 的时候，writer 会等待，如果我们在 reader 的读操作时调用 writer 的写操作（它会调用 Lock 方法），那么，这个 reader 和 writer 就会形成互相依赖的死锁状态。Reader 想等待 writer 完成后再释放锁，而 writer 需要这个 reader 释放锁之后，才能不阻塞地继续执行。这是一个读写锁常见的死锁场景。

第三种死锁的场景更加隐蔽。当一个 writer 请求锁的时候，如果已经有一些活跃的 reader，它会等待这些活跃的 reader 完成，才有可能获取到锁，但是，如果之后活跃的 reader 再依赖新的 reader 的话，这些新的 reader 就会等待 writer 释放锁之后才能继续执行，这就形成了一个环形依赖： **writer 依赖活跃的 reader -> 活跃的 reader 依赖新来的 reader -> 新来的 reader 依赖 writer。**

![img](https://static001.geekbang.org/resource/image/c1/35/c18e897967d29e2d5273b88afe626035.jpg?wh=2089*1666)

这个死锁相当隐蔽，原因在于它和 RWMutex 的设计和实现有关。啥意思呢？我们来看一个计算阶乘 (n!) 的例子：

```go
func main() {
    var mu sync.RWMutex

    // writer,稍微等待，然后制造一个调用Lock的场景
    go func() {
        time.Sleep(200 * time.Millisecond)
        mu.Lock()
        fmt.Println("Lock")
        time.Sleep(100 * time.Millisecond)
        mu.Unlock()
        fmt.Println("Unlock")
    }()

    go func() {
        factorial(&mu, 10) // 计算10的阶乘, 10!
    }()
    
    select {}
}

// 递归调用计算阶乘
func factorial(m *sync.RWMutex, n int) int {
    if n < 1 { // 阶乘退出条件 
        return 0
    }
    fmt.Println("RLock")
    m.RLock()
    defer func() {
        fmt.Println("RUnlock")
        m.RUnlock()
    }()
    time.Sleep(100 * time.Millisecond)
    return factorial(m, n-1) * n // 递归调用
}
```
factoria 方法是一个递归计算阶乘的方法，我们用它来模拟 reader。为了更容易地制造出死锁场景，我在这里加上了 sleep 的调用，延缓逻辑的执行。这个方法会调用读锁（第 27 行），在第 33 行递归地调用此方法，每次调用都会产生一次读锁的调用，所以可以不断地产生读锁的调用，而且必须等到新请求的读锁释放，这个读锁才能释放。同时，我们使用另一个 goroutine 去调用 Lock 方法，来实现 writer，这个 writer 会等待 200 毫秒后才会调用 Lock，这样在调用 Lock 的时候，factoria 方法还在执行中不断调用 RLock。这两个 goroutine 互相持有锁并等待，谁也不会退让一步，满足了“writer 依赖活跃的 reader -> 活跃的 reader 依赖新来的 reader -> 新来的 reader 依赖 writer”的死锁条件，所以就导致了死锁的产生。所以，使用读写锁最需要注意的一点就是尽量避免重入，重入带来的死锁非常隐蔽，而且难以诊断。

#### 坑点 3：释放未加锁的 RWMutex

和互斥锁一样，Lock 和 Unlock 的调用总是成对出现的，RLock 和 RUnlock 的调用也必须成对出现。Lock 和 RLock 多余的调用会导致锁没有被释放，可能会出现死锁，而 Unlock 和 RUnlock 多余的调用会导致 panic。在生产环境中出现 panic 是大忌，你总不希望半夜爬起来处理生产环境程序崩溃的问题吧？所以，在使用读写锁的时候，一定要注意，不遗漏不多余。

### 流行的 Go 开发项目中的坑

好了，又到了泡一杯宁夏枸杞加新疆大滩枣的养生茶，静静地欣赏知名项目出现 Bug 的时候了，这次被拉出来的是 RWMutex 的 Bug。

#### Docker

issue 36840修复的是错误地把 writer 当成 reader 的 Bug。 这个地方本来需要修改数据，需要调用的是写锁，结果用的却是读锁。或许是被它紧挨着的 findNode 方法调用迷惑了，认为这只是一个读操作。可实际上，代码后面还会有 changeNodeState 方法的调用，这是一个写操作。修复办法也很简单，只需要改成 Lock/Unlock 即可。

![img](https://static001.geekbang.org/resource/image/e4/4b/e4d153cb5f81873a726b09bc436b8a4b.png?wh=1642*554)

#### Kubernetes

issue 62464就是读写锁第二种死锁的场景，这是一个典型的 reader 导致的死锁的例子。知道墨菲定律吧？“凡是可能出错的事，必定会出错”。你可能觉得我前面讲的 RWMutex 的坑绝对不会被人踩的，因为道理大家都懂，但是你看，Kubernetes 就踩了这个重入的坑。这个 issue 在移除 pod 的时候可能会发生，原因就在于，GetCPUSetOrDefault 方法会请求读锁，同时，它还会调用 GetCPUSet 或 GetDefaultCPUSet 方法，这时又会请求读锁。如果期间有其它 goroutine 请求写锁的话，GetCPUSetOrDefault 方法调用 GetCPUSet 或 GetDefaultCPUSet 方法时就不会返回了，请求写锁的 goroutine 也不会返回，这就会形成死锁。

![img](https://static001.geekbang.org/resource/image/06/c2/062ae5d2a6190f86cb7bf57db643d8c2.png?wh=2126*986)

### 总结

在开发过程中，一开始考虑共享资源并发访问问题的时候，我们就会想到互斥锁 Mutex。因为刚开始的时候，我们还并不太了解并发的情况，所以，就会使用最简单的同步原语来解决问题。等到系统成熟，真正到了需要性能优化的时候，我们就能静下心来分析并发场景的可能性，这个时候，我们就要考虑将 Mutex 修改为 RWMutex，来压榨系统的性能。当然，如果一开始你的场景就非常明确了，比如我就要实现一个线程安全的 map，那么，一开始你就可以考虑使用读写锁。正如我在前面提到的，如果你能意识到你要解决的问题是一个 readers-writers 问题，那么你就可以毫不犹豫地选择 RWMutex，不用考虑其它选择。那在使用 RWMutex 时，最需要注意的一点就是尽量避免重入，重入带来的死锁非常隐蔽，而且难以诊断。另外我们也可以扩展 RWMutex，不过实现方法和互斥锁 Mutex 差不多，在技术上是一样的，都是通过 unsafe 来实现，我就不再具体讲了。课下你可以参照我们上节课学习的方法，实现一个扩展的 RWMutex。这一讲我们系统学习了读写锁的相关知识，这里提供给你一个知识地图，帮助你复习本节课的知识。

![img](https://static001.geekbang.org/resource/image/69/42/695b9aa6027b5d3a61e92cbcbba10042.jpg?wh=2250*2019)

## 06 | WaitGroup：协同等待，任务编排利器

WaitGroup，我们以前都多多少少学习过，或者是使用过。其实，WaitGroup 很简单，就是 package sync 用来做任务编排的一个并发原语。它要解决的就是并发 - 等待的问题：现在有一个 goroutine A 在检查点（checkpoint）等待一组 goroutine 全部完成，如果在执行任务的这些 goroutine 还没全部完成，那么 goroutine A 就会阻塞在检查点，直到所有 goroutine 都完成后才能继续执行。我们来看一个使用 WaitGroup 的场景。

比如，我们要完成一个大的任务，需要使用并行的 goroutine 执行三个小任务，只有这三个小任务都完成，我们才能去执行后面的任务。如果通过轮询的方式定时询问三个小任务是否完成，会存在两个问题：一是，性能比较低，因为三个小任务可能早就完成了，却要等很长时间才被轮询到；二是，会有很多无谓的轮询，空耗 CPU 资源。那么，这个时候使用 WaitGroup 并发原语就比较有效了，它可以阻塞等待的 goroutine。等到三个小任务都完成了，再即时唤醒它们。其实，很多操作系统和编程语言都提供了类似的并发原语。比如，Linux 中的 barrier、Pthread（POSIX 线程）中的 barrier、C++ 中的 std::barrier、Java 中的 CyclicBarrier 和 CountDownLatch 等。由此可见，这个并发原语还是一个非常基础的并发类型。所以，我们要认真掌握今天的内容，这样就可以举一反三，轻松应对其他场景下的需求了。我们还是从 WaitGroup 的基本用法学起吧。

### WaitGroup 的基本用法

Go 标准库中的 WaitGroup 提供了三个方法，保持了 Go 简洁的风格。

```
    func (wg *WaitGroup) Add(delta int)
    func (wg *WaitGroup) Done()
    func (wg *WaitGroup) Wait()
```
我们分别看下这三个方法：Add，用来设置 WaitGroup 的计数值；Done，用来将 WaitGroup 的计数值减 1，其实就是调用了 Add(-1)；Wait，调用这个方法的 goroutine 会一直阻塞，直到 WaitGroup 的计数值变为 0。

接下来，我们通过一个使用 WaitGroup 的例子，来看下 Add、Done、Wait 方法的基本用法。在这个例子中，我们使用了以前实现的计数器 struct。我们启动了 10 个 worker，分别对计数值加一，10 个 worker 都完成后，我们期望输出计数器的值。

```
// 线程安全的计数器
type Counter struct {
    mu    sync.Mutex
    count uint64
}
// 对计数值加一
func (c *Counter) Incr() {
    c.mu.Lock()
    c.count++
    c.mu.Unlock()
}
// 获取当前的计数值
func (c *Counter) Count() uint64 {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.count
}
// sleep 1秒，然后计数值加1
func worker(c *Counter, wg *sync.WaitGroup) {
    defer wg.Done()
    time.Sleep(time.Second)
    c.Incr()
}

func main() {
    var counter Counter
    
    var wg sync.WaitGroup
    wg.Add(10) // WaitGroup的值设置为10

    for i := 0; i < 10; i++ { // 启动10个goroutine执行加1任务
        go worker(&counter, &wg)
    }
    // 检查点，等待goroutine都完成任务
    wg.Wait()
    // 输出当前计数器的值
    fmt.Println(counter.Count())
}
```


我们一起来分析下这段代码。第 28 行，声明了一个 WaitGroup 变量，初始值为零。第 29 行，把 WaitGroup 变量的计数值设置为 10。因为我们需要编排 10 个 goroutine(worker) 去执行任务，并且等待 goroutine 完成。第 35 行，调用 Wait 方法阻塞等待。第 32 行，启动了 goroutine，并把我们定义的 WaitGroup 指针当作参数传递进去。goroutine 完成后，需要调用 Done 方法，把 WaitGroup 的计数值减 1。等 10 个 goroutine 都调用了 Done 方法后，WaitGroup 的计数值降为 0，这时，第 35 行的主 goroutine 就不再阻塞，会继续执行，在第 37 行输出计数值。

这就是我们使用 WaitGroup 编排这类任务的常用方式。而“这类任务”指的就是，需要启动多个 goroutine 执行任务，主 goroutine 需要等待子 goroutine 都完成后才继续执行。熟悉了 WaitGroup 的基本用法后，我们再看看它具体是如何实现的吧。

### WaitGroup 的实现

首先，我们看看 WaitGroup 的数据结构。它包括了一个 noCopy 的辅助字段，一个 state1 记录 WaitGroup 状态的数组。noCopy 的辅助字段，主要就是辅助 vet 工具检查是否通过 copy 赋值这个 WaitGroup 实例。我会在后面和你详细分析这个字段；state1，一个具有复合意义的字段，包含 WaitGroup 的计数、阻塞在检查点的 waiter 数和信号量。

WaitGroup 的数据结构定义以及 state 信息的获取方法如下：

```
type WaitGroup struct {
    // 避免复制使用的一个技巧，可以告诉vet工具违反了复制使用的规则
    noCopy noCopy
    // 64bit(8bytes)的值分成两段，高32bit是计数值，低32bit是waiter的计数
    // 另外32bit是用作信号量的
    // 因为64bit值的原子操作需要64bit对齐，但是32bit编译器不支持，所以数组中的元素在不同的架构中不一样，具体处理看下面的方法
    // 总之，会找到对齐的那64bit作为state，其余的32bit做信号量
    state1 [3]uint32
}


// 得到state的地址和信号量的地址
func (wg *WaitGroup) state() (statep *uint64, semap *uint32) {
    if uintptr(unsafe.Pointer(&wg.state1))%8 == 0 {
        // 如果地址是64bit对齐的，数组前两个元素做state，后一个元素做信号量
        return (*uint64)(unsafe.Pointer(&wg.state1)), &wg.state1[2]
    } else {
        // 如果地址是32bit对齐的，数组后两个元素用来做state，它可以用来做64bit的原子操作，第一个元素32bit用来做信号量
        return (*uint64)(unsafe.Pointer(&wg.state1[1])), &wg.state1[0]
    }
}
```
因为对 64 位整数的原子操作要求整数的地址是 64 位对齐的，所以针对 64 位和 32 位环境的 state 字段的组成是不一样的。在 64 位环境下，state1 的第一个元素是 waiter 数，第二个元素是 WaitGroup 的计数值，第三个元素是信号量。

![img](https://static001.geekbang.org/resource/image/71/ea/71b5fyy6284140986d04c0b6f87aedea.jpg?wh=3033*1572)

在 32 位环境下，如果 state1 不是 64 位对齐的地址，那么 state1 的第一个元素是信号量，后两个元素分别是 waiter 数和计数值。

![img](https://static001.geekbang.org/resource/image/22/ac/22c40ac54cfeb53669a6ae39020c23ac.jpg?wh=3874*1439)

然后，我们继续深入源码，看一下 Add、Done 和 Wait 这三个方法的实现。在查看这部分源码实现时，我们会发现，除了这些方法本身的实现外，还会有一些额外的代码，主要是 race 检查和异常检查的代码。其中，有几个检查非常关键，如果检查不通过，会出现 panic，这部分内容我会在下一小节分析 WaitGroup 的错误使用场景时介绍。现在，我们先专注在 Add、Wait 和 Done 本身的实现代码上。我先为你梳理下 Add 方法的逻辑。Add 方法主要操作的是 state 的计数部分。你可以为计数值增加一个 delta 值，内部通过原子操作把这个值加到计数值上。需要注意的是，这个 delta 也可以是个负数，相当于为计数值减去一个值，Done 方法内部其实就是通过 Add(-1) 实现的。它的实现代码如下：

```go
func (wg *WaitGroup) Add(delta int) {
    statep, semap := wg.state()
    // 高32bit是计数值v，所以把delta左移32，增加到计数上
    state := atomic.AddUint64(statep, uint64(delta)<<32)
    v := int32(state >> 32) // 当前计数值
    w := uint32(state) // waiter count

    if v > 0 || w == 0 {
        return
    }

    // 如果计数值v为0并且waiter的数量w不为0，那么state的值就是waiter的数量
    // 将waiter的数量设置为0，因为计数值v也是0,所以它们俩的组合*statep直接设置为0即可。此时需要并唤醒所有的waiter
    *statep = 0
    for ; w != 0; w-- {
        runtime_Semrelease(semap, false, 0)
    }
}


// Done方法实际就是计数器减1
func (wg *WaitGroup) Done() {
    wg.Add(-1)
}
```
Wait 方法的实现逻辑是：不断检查 state 的值。如果其中的计数值变为了 0，那么说明所有的任务已完成，调用者不必再等待，直接返回。如果计数值大于 0，说明此时还有任务没完成，那么调用者就变成了等待者，需要加入 waiter 队列，并且阻塞住自己。其主干实现代码如下：

```go
func (wg *WaitGroup) Wait() {
    statep, semap := wg.state()
    
    for {
        state := atomic.LoadUint64(statep)
        v := int32(state >> 32) // 当前计数值
        w := uint32(state) // waiter的数量
        if v == 0 {
            // 如果计数值为0, 调用这个方法的goroutine不必再等待，继续执行它后面的逻辑即可
            return
        }
        // 否则把waiter数量加1。期间可能有并发调用Wait的情况，所以最外层使用了一个for循环
        if atomic.CompareAndSwapUint64(statep, state, state+1) {
            // 阻塞休眠等待
            runtime_Semacquire(semap)
            // 被唤醒，不再阻塞，返回
            return
        }
    }
}
```
### 使用 WaitGroup 时的常见错误

在分析 WaitGroup 的 Add、Done 和 Wait 方法的实现的时候，为避免干扰，我删除了异常检查的代码。但是，这些异常检查非常有用。我们在开发的时候，经常会遇见或看到误用 WaitGroup 的场景，究其原因就是没有弄明白这些检查的逻辑。所以接下来，我们就通过几个小例子，一起学习下在开发时绝对要避免的 3 个问题。

#### 常见问题一：计数器设置为负值

WaitGroup 的计数器的值必须大于等于 0。我们在更改这个计数值的时候，WaitGroup 会先做检查，如果计数值被设置为负数，就会导致 panic。一般情况下，有两种方法会导致计数器设置为负数。

第一种方法是：调用 Add 的时候传递一个负数。如果你能保证当前的计数器加上这个负数后还是大于等于 0 的话，也没有问题，否则就会导致 panic。比如下面这段代码，计数器的初始值为 10，当第一次传入 -10 的时候，计数值被设置为 0，不会有啥问题。但是，再紧接着传入 -1 以后，计数值就被设置为负数了，程序就会出现 panic。

```go
func main() {
    var wg sync.WaitGroup
    wg.Add(10)

    wg.Add(-10)//将-10作为参数调用Add，计数值被设置为0

    wg.Add(-1)//将-1作为参数调用Add，如果加上-1计数值就会变为负数。这是不对的，所以会触发panic
}
```
第二个方法是：调用 Done 方法的次数过多，超过了 WaitGroup 的计数值。

**使用 WaitGroup 的正确姿势是，预先确定好 WaitGroup 的计数值，然后调用相同次数的 Done 完成相应的任务**。比如，在 WaitGroup 变量声明之后，就立即设置它的计数值，或者在 goroutine 启动之前增加 1，然后在 goroutine 中调用 Done。如果你没有遵循这些规则，就很可能会导致 Done 方法调用的次数和计数值不一致，进而造成死锁（Done 调用次数比计数值少）或者 panic（Done 调用次数比计数值多）。比如下面这个例子中，多调用了一次 Done 方法后，会导致计数值为负，所以程序运行到这一行会出现 panic。

```go
func main() {
    var wg sync.WaitGroup
    wg.Add(1)

    wg.Done()

    wg.Done()
}
```
### 常见问题二：不期望的 Add 时机

在使用 WaitGroup 的时候，你一定要遵循的原则就是，等所有的 Add 方法调用之后再调用 Wait，否则就可能导致 panic 或者不期望的结果。我们构造这样一个场景：只有部分的 Add/Done 执行完后，Wait 就返回。我们看一个例子：启动四个 goroutine，每个 goroutine 内部调用 Add(1) 然后调用 Done()，主 goroutine 调用 Wait 等待任务完成。

```go
func main() {
    var wg sync.WaitGroup
    go dosomething(100, &wg) // 启动第一个goroutine
    go dosomething(110, &wg) // 启动第二个goroutine
    go dosomething(120, &wg) // 启动第三个goroutine
    go dosomething(130, &wg) // 启动第四个goroutine

    wg.Wait() // 主goroutine等待完成
    fmt.Println("Done")
}

func dosomething(millisecs time.Duration, wg *sync.WaitGroup) {
    duration := millisecs * time.Millisecond
    time.Sleep(duration) // 故意sleep一段时间

    wg.Add(1)
    fmt.Println("后台执行, duration:", duration)
    wg.Done()
}
```
在这个例子中，我们原本设想的是，等四个 goroutine 都执行完毕后输出 Done 的信息，但是它的错误之处在于，将 WaitGroup.Add 方法的调用放在了子 gorotuine 中。等主 goorutine 调用 Wait 的时候，因为四个任务 goroutine 一开始都休眠，所以可能 WaitGroup 的 Add 方法还没有被调用，WaitGroup 的计数还是 0，所以它并没有等待四个子 goroutine 执行完毕才继续执行，而是立刻执行了下一步。导致这个错误的原因是，没有遵循先完成所有的 Add 之后才 Wait。要解决这个问题，一个方法是，预先设置计数值：

```go
func main() {
    var wg sync.WaitGroup
    wg.Add(4) // 预先设定WaitGroup的计数值

    go dosomething(100, &wg) // 启动第一个goroutine
    go dosomething(110, &wg) // 启动第二个goroutine
    go dosomething(120, &wg) // 启动第三个goroutine
    go dosomething(130, &wg) // 启动第四个goroutine

    wg.Wait() // 主goroutine等待
    fmt.Println("Done")
}

func dosomething(millisecs time.Duration, wg *sync.WaitGroup) {
    duration := millisecs * time.Millisecond
    time.Sleep(duration)

    fmt.Println("后台执行, duration:", duration)
    wg.Done()
}

```
另一种方法是在启动子 goroutine 之前才调用 Add：

```go
func main() {
    var wg sync.WaitGroup

    dosomething(100, &wg) // 调用方法，把计数值加1，并启动任务goroutine
    dosomething(110, &wg) // 调用方法，把计数值加1，并启动任务goroutine
    dosomething(120, &wg) // 调用方法，把计数值加1，并启动任务goroutine
    dosomething(130, &wg) // 调用方法，把计数值加1，并启动任务goroutine

    wg.Wait() // 主goroutine等待，代码逻辑保证了四次Add(1)都已经执行完了
    fmt.Println("Done")
}

func dosomething(millisecs time.Duration, wg *sync.WaitGroup) {
    wg.Add(1) // 计数值加1，再启动goroutine

    go func() {
        duration := millisecs * time.Millisecond
        time.Sleep(duration)
        fmt.Println("后台执行, duration:", duration)
        wg.Done()
    }()
}

```
可见，无论是怎么修复，都要保证所有的 Add 方法是在 Wait 方法之前被调用的。

### 常见问题三：前一个 Wait 还没结束就重用 WaitGroup

“前一个 Wait 还没结束就重用 WaitGroup”这一点似乎不太好理解，我借用田径比赛的例子和你解释下吧。在田径比赛的百米小组赛中，需要把选手分成几组，一组选手比赛完之后，就可以进行下一组了。为了确保两组比赛时间上没有冲突，我们在模型化这个场景的时候，可以使用 WaitGroup。

WaitGroup 等一组比赛的所有选手都跑完后 5 分钟，才开始下一组比赛。下一组比赛还可以使用这个 WaitGroup 来控制，因为 WaitGroup 是可以重用的。只要 WaitGroup 的计数值恢复到零值的状态，那么它就可以被看作是新创建的 WaitGroup，被重复使用。但是，如果我们在 WaitGroup 的计数值还没有恢复到零值的时候就重用，就会导致程序 panic。我们看一个例子，初始设置 WaitGroup 的计数值为 1，启动一个 goroutine 先调用 Done 方法，接着就调用 Add 方法，Add 方法有可能和主 goroutine 并发执行。

```go
func main() {
    var wg sync.WaitGroup
    wg.Add(1)
    go func() {
        time.Sleep(time.Millisecond)
        wg.Done() // 计数器减1
        wg.Add(1) // 计数值加1
    }()
    wg.Wait() // 主goroutine等待，有可能和第7行并发执行
}
```
在这个例子中，第 6 行虽然让 WaitGroup 的计数恢复到 0，但是因为第 9 行有个 waiter 在等待，如果等待 Wait 的 goroutine，刚被唤醒就和 Add 调用（第 7 行）有并发执行的冲突，所以就会出现 panic。总结一下：WaitGroup 虽然可以重用，但是是有一个前提的，那就是必须等到上一轮的 Wait 完成之后，才能重用 WaitGroup 执行下一轮的 Add/Wait，如果你在 Wait 还没执行完的时候就调用下一轮 Add 方法，就有可能出现 panic。

### noCopy：辅助 vet 检查

我们刚刚在学习 WaitGroup 的数据结构时，提到了里面有一个 noCopy 字段。你还记得它的作用吗？其实，它就是指示 vet 工具在做检查的时候，这个数据结构不能做值复制使用。更严谨地说，是不能在第一次使用之后复制使用 ( must not be copied after first use)。

你可能会说了，为什么要把 noCopy 字段单独拿出来讲呢？一方面，把 noCopy 字段穿插到 waitgroup 代码中讲解，容易干扰我们对 WaitGroup 整体的理解。另一方面，也是非常重要的原因，noCopy 是一个通用的计数技术，其他并发原语中也会用到，所以单独介绍有助于你以后在实践中使用这个技术。我们在第 3 讲学习 Mutex 的时候用到了 vet 工具。vet 会对实现 Locker 接口的数据类型做静态检查，一旦代码中有复制使用这种数据类型的情况，就会发出警告。但是，WaitGroup 同步原语不就是 Add、Done 和 Wait 方法吗？vet 能检查出来吗？其实是可以的。通过给 WaitGroup 添加一个 noCopy 字段，我们就可以为 WaitGroup 实现 Locker 接口，这样 vet 工具就可以做复制检查了。而且因为 noCopy 字段是未输出类型，所以 WaitGroup 不会暴露 Lock/Unlock 方法。noCopy 字段的类型是 noCopy，它只是一个辅助的、用来帮助 vet 检查用的类型:

```
type noCopy struct{}

// Lock is a no-op used by -copylocks checker from `go vet`.
func (*noCopy) Lock()   {}
func (*noCopy) Unlock() {}

```
如果你想要自己定义的数据结构不被复制使用，或者说，不能通过 vet 工具检查出复制使用的报警，就可以通过嵌入 noCopy 这个数据类型来实现。

### 流行的 Go 开发项目中的坑

接下来又到了喝枸杞红枣茶的时间了。你可以稍微休息一下，心态放轻松地跟我一起围观下知名项目犯过的错，比如 copy Waitgroup、Add/Wait 并发执行问题、遗漏 Add 等 Bug。有网友在 Go 的issue 28123中提了以下的例子，你能发现这段代码有什么问题吗？

```
type TestStruct struct {
  Wait sync.WaitGroup
}

func main() {
  w := sync.WaitGroup{}
  w.Add(1)
  t := &TestStruct{
    Wait: w,
  }

  t.Wait.Done()
  fmt.Println("Finished")
}
```
这段代码最大的一个问题，就是第 9 行 copy 了 WaitGroup 的实例 w。虽然这段代码能执行成功，但确实是违反了 WaitGroup 使用之后不要复制的规则。在项目中，我们可以通过 vet 工具检查出这样的错误。Docker issue 28161 和 issue 27011  ，都是因为在重用 WaitGroup 的时候，没等前一次的 Wait 结束就 Add 导致的错误。Etcd issue 6534 也是重用 WaitGroup 的 Bug，没有等前一个 Wait 结束就 Add。Kubernetes issue 59574 的 Bug 是忘记 Wait 之前增加计数了，这就属于我们通常认为几乎不可能出现的 Bug。

![img](https://static001.geekbang.org/resource/image/3f/f8/3ff86f54893c23d997113440a3a0e2f8.png?wh=1122*680)

即使是开发 Go 语言的开发者自己，在使用 WaitGroup 的时候，也可能会犯错。比如 issue 12813，因为 defer 的使用，Add 方法可能在 Done 之后才执行，导致计数负值的 panic。

![img](https://static001.geekbang.org/resource/image/2f/5c/2f69127691a431300478d7d7d1c7bd5c.png?wh=1129*391)



### 总结

学完这一讲，我们知道了使用 WaitGroup 容易犯的错，是不是有些手脚被束缚的感觉呢？其实大可不必，只要我们不是特别复杂地使用 WaitGroup，就不用有啥心理负担。而关于如何避免错误使用 WaitGroup 的情况，我们只需要尽量保证下面 5 点就可以了：

不重用 WaitGroup。新建一个 WaitGroup 不会带来多大的资源开销，重用反而更容易出错。保证所有的 Add 方法调用都在 Wait 之前。不传递负数给 Add 方法，只通过 Done 来给计数值减 1。不做多余的 Done 方法调用，保证 Add 的计数值和 Done 方法调用的数量是一样的。不遗漏 Done 方法的调用，否则会导致 Wait hang 住无法返回。

这一讲我们详细学习了 WaitGroup 的相关知识，这里我整理了一份关于 WaitGroup 的知识地图，方便你复习。

![img](https://static001.geekbang.org/resource/image/84/ff/845yyf00c6db85c0yy59867e6de77dff.jpg?wh=2250*1741)

## 07 | Cond：条件变量的实现机制及避坑指南

在写 Go 程序之前，我曾经写了 10 多年的 Java 程序，也面试过不少 Java 程序员。在 Java 面试中，经常被问到的一个知识点就是等待 / 通知（wait/notify）机制。面试官经常会这样考察候选人：请实现一个限定容量的队列（queue），当队列满或者空的时候，利用等待 / 通知机制实现阻塞或者唤醒。在 Go 中，也可以实现一个类似的限定容量的队列，而且实现起来也比较简单，只要用条件变量（Cond）并发原语就可以。Cond 并发原语相对来说不是那么常用，但是在特定的场景使用会事半功倍，比如你需要在唤醒一个或者所有的等待者做一些检查操作的时候。那么今天这一讲，我们就学习下 Cond 这个并发原语。

### Go 标准库的 Cond

Go 标准库提供 Cond 原语的目的是，为等待 / 通知场景下的并发问题提供支持。Cond 通常应用于等待某个条件的一组 goroutine，等条件变为 true 的时候，其中一个 goroutine 或者所有的 goroutine 都会被唤醒执行。顾名思义，Cond 是和某个条件相关，这个条件需要一组 goroutine 协作共同完成，在条件还没有满足的时候，所有等待这个条件的 goroutine 都会被阻塞住，只有这一组 goroutine 通过协作达到了这个条件，等待的 goroutine 才可能继续进行下去。那这里等待的条件是什么呢？等待的条件，可以是某个变量达到了某个阈值或者某个时间点，也可以是一组变量分别都达到了某个阈值，还可以是某个对象的状态满足了特定的条件。总结来讲，等待的条件是一种可以用来计算结果是 true 还是 false 的条件。从开发实践上，我们真正使用 Cond 的场景比较少，因为一旦遇到需要使用 Cond 的场景，我们更多地会使用 Channel 的方式（我会在第 12 和第 13 讲展开 Channel 的用法）去实现，因为那才是更地道的 Go 语言的写法，甚至 Go 的开发者有个“把 Cond 从标准库移除”的提议（issue 21165）。而有的开发者认为，Cond 是唯一难以掌握的 Go 并发原语。至于其中原因，我先卖个关子，到这一讲的后半部分我再和你解释。今天，这一讲我们就带你仔细地学一学 Cond 这个并发原语吧。

### Cond 的基本用法

标准库中的 Cond 并发原语初始化的时候，需要关联一个 Locker 接口的实例，一般我们使用 Mutex 或者 RWMutex。我们看一下 Cond 的实现：

```
type Cond
  func NeWCond(l Locker) *Cond
  func (c *Cond) Broadcast()
  func (c *Cond) Signal()
  func (c *Cond) Wait()
```
首先，Cond 关联的 Locker 实例可以通过 c.L 访问，它内部维护着一个先入先出的等待队列。然后，我们分别看下它的三个方法 Broadcast、Signal 和 Wait 方法。

Signal 方法，允许调用者 Caller 唤醒一个等待此 Cond 的 goroutine。如果此时没有等待的 goroutine，显然无需通知 waiter；如果 Cond 等待队列中有一个或者多个等待的 goroutine，则需要从等待队列中移除第一个 goroutine 并把它唤醒。在其他编程语言中，比如 Java 语言中，Signal 方法也被叫做 notify 方法。

调用 Signal 方法时，不强求你一定要持有 c.L 的锁。

Broadcast 方法，允许调用者 Caller 唤醒所有等待此 Cond 的 goroutine。如果此时没有等待的 goroutine，显然无需通知 waiter；如果 Cond 等待队列中有一个或者多个等待的 goroutine，则清空所有等待的 goroutine，并全部唤醒。在其他编程语言中，比如 Java 语言中，Broadcast 方法也被叫做 notifyAll 方法。

同样地，调用 Broadcast 方法时，也不强求你一定持有 c.L 的锁。

Wait 方法，会把调用者 Caller 放入 Cond 的等待队列中并阻塞，直到被 Signal 或者 Broadcast 的方法从等待队列中移除并唤醒。

调用 Wait 方法时必须要持有 c.L 的锁。

Go 实现的 sync.Cond 的方法名是 Wait、Signal 和 Broadcast，这是计算机科学中条件变量的[通用方法名](https://en.wikipedia.org/wiki/Monitor_(synchronization)#Condition_variables_2)。比如，C 语言中对应的方法名是 pthread_cond_wait、pthread_cond_signal 和 pthread_cond_broadcast。

知道了 Cond 提供的三个方法后，我们再通过一个百米赛跑开始时的例子，来学习下 Cond 的使用方法。10 个运动员进入赛场之后需要先做拉伸活动活动筋骨，向观众和粉丝招手致敬，在自己的赛道上做好准备；等所有的运动员都准备好之后，裁判员才会打响发令枪。

每个运动员做好准备之后，将 ready 加一，表明自己做好准备了，同时调用 Broadcast 方法通知裁判员。因为裁判员只有一个，所以这里可以直接替换成 Signal 方法调用。调用 Broadcast 方法的时候，我们并没有请求 c.L 锁，只是在更改等待变量的时候才使用到了锁。裁判员会等待运动员都准备好（第 22 行）。虽然每个运动员准备好之后都唤醒了裁判员，但是裁判员被唤醒之后需要检查等待条件是否满足（运动员都准备好了）。可以看到，裁判员被唤醒之后一定要检查等待条件，如果条件不满足还是要继续等待。

```go
func main() {
    c := sync.NewCond(&sync.Mutex{})
    var ready int

    for i := 0; i < 10; i++ {
        go func(i int) {
            time.Sleep(time.Duration(rand.Int63n(10)) * time.Second)

            // 加锁更改等待条件
            c.L.Lock()
            ready++
            c.L.Unlock()

            log.Printf("运动员#%d 已准备就绪\n", i)
            // 广播唤醒所有的等待者
            c.Broadcast()
        }(i)
    }

    c.L.Lock()
    for ready != 10 {
        c.Wait()
        log.Println("裁判员被唤醒一次")
    }
    c.L.Unlock()

    //所有的运动员是否就绪
    log.Println("所有运动员都准备就绪。比赛开始，3，2，1, ......")
}
```
你看，Cond 的使用其实没那么简单。它的复杂在于：一，这段代码有时候需要加锁，有时候可以不加；二，Wait 唤醒后需要检查条件；三，条件变量的更改，其实是需要原子操作或者互斥锁保护的。所以，有的开发者会认为，Cond 是唯一难以掌握的 Go 并发原语。我们继续看看 Cond 的实现原理。

### Cond 的实现原理

其实，Cond 的实现非常简单，或者说复杂的逻辑已经被 Locker 或者 runtime 的等待队列实现了。我们直接看看 Cond 的源码吧。

```
type Cond struct {
    noCopy noCopy

    // 当观察或者修改等待条件的时候需要加锁
    L Locker

    // 等待队列
    notify  notifyList
    checker copyChecker
}

func NewCond(l Locker) *Cond {
    return &Cond{L: l}
}

func (c *Cond) Wait() {
    c.checker.check()
    // 增加到等待队列中
    t := runtime_notifyListAdd(&c.notify)
    c.L.Unlock()
    // 阻塞休眠直到被唤醒
    runtime_notifyListWait(&c.notify, t)
    c.L.Lock()
}

func (c *Cond) Signal() {
    c.checker.check()
    runtime_notifyListNotifyOne(&c.notify)
}

func (c *Cond) Broadcast() {
    c.checker.check()
    runtime_notifyListNotifyAll(&c.notify）
}
```
这部分源码确实很简单，我来带你学习下其中比较关键的逻辑。runtime_notifyListXXX 是运行时实现的方法，实现了一个等待 / 通知的队列。如果你想深入学习这部分，可以再去看看 runtime/sema.go 代码中。copyChecker 是一个辅助结构，可以在运行时检查 Cond 是否被复制使用。Signal 和 Broadcast 只涉及到 notifyList 数据结构，不涉及到锁。Wait 把调用者加入到等待队列时会释放锁，在被唤醒之后还会请求锁。在阻塞休眠期间，调用者是不持有锁的，这样能让其他 goroutine 有机会检查或者更新等待变量。我们继续看看使用 Cond 常见的两个错误，一个是调用 Wait 的时候没有加锁，另一个是没有检查条件是否满足程序就继续执行了。

### 使用 Cond 的 2 个常见错误

我们先看 Cond 最常见的使用错误，也就是调用 Wait 的时候没有加锁。

以前面百米赛跑的程序为例，在调用 cond.Wait 时，把前后的 Lock/Unlock 注释掉，如下面的代码中的第 20 行和第 25 行：

```go
func main() {
    c := sync.NewCond(&sync.Mutex{})
    var ready int

    for i := 0; i < 10; i++ {
        go func(i int) {
            time.Sleep(time.Duration(rand.Int63n(10)) * time.Second)

            // 加锁更改等待条件
            c.L.Lock()
            ready++
            c.L.Unlock()

            log.Printf("运动员#%d 已准备就绪\n", i)
            // 广播唤醒所有的等待者
            c.Broadcast()
        }(i)
    }

    // c.L.Lock()
    for ready != 10 {
        c.Wait()
        log.Println("裁判员被唤醒一次")
    }
    // c.L.Unlock()

    //所有的运动员是否就绪
    log.Println("所有运动员都准备就绪。比赛开始，3，2，1, ......")
}
```
再运行程序，就会报释放未加锁的 panic：

![img](https://static001.geekbang.org/resource/image/47/76/4780dca40087277be0d183674bc42c76.jpeg?wh=877*367)

出现这个问题的原因在于，cond.Wait 方法的实现是，把当前调用者加入到 notify 队列之中后会释放锁（如果不释放锁，其他 Wait 的调用者就没有机会加入到 notify 队列中了），然后一直等待；等调用者被唤醒之后，又会去争抢这把锁。如果调用 Wait 之前不加锁的话，就有可能 Unlock 一个未加锁的 Locker。所以切记，**调用 cond.Wait 方法之前一定要加锁**。使用 Cond 的另一个常见错误是，只调用了一次 Wait，没有检查等待条件是否满足，结果条件没满足，程序就继续执行了。出现这个问题的原因在于，误以为 Cond 的使用，就像 WaitGroup 那样调用一下 Wait 方法等待那么简单。比如下面的代码中，把第 21 行和第 24 行注释掉：



```go
func main() {
    c := sync.NewCond(&sync.Mutex{})
    var ready int

    for i := 0; i < 10; i++ {
        go func(i int) {
            time.Sleep(time.Duration(rand.Int63n(10)) * time.Second)

            // 加锁更改等待条件
            c.L.Lock()
            ready++
            c.L.Unlock()

            log.Printf("运动员#%d 已准备就绪\n", i)
            // 广播唤醒所有的等待者
            c.Broadcast()
        }(i)
    }

    c.L.Lock()
    // for ready != 10 {
    c.Wait()
    log.Println("裁判员被唤醒一次")
    // }
    c.L.Unlock()

    //所有的运动员是否就绪
    log.Println("所有运动员都准备就绪。比赛开始，3，2，1, ......")
}
```


运行这个程序，你会发现，可能只有几个运动员准备好之后程序就运行完了，而不是我们期望的所有运动员都准备好才进行下一步。原因在于，每一个运动员准备好之后都会唤醒所有的等待者，也就是这里的裁判员，比如第一个运动员准备好后就唤醒了裁判员，结果这个裁判员傻傻地没做任何检查，以为所有的运动员都准备好了，就继续执行了。所以，我们一定要记住，waiter goroutine 被唤醒不等于等待条件被满足，只是有 goroutine 把它唤醒了而已，等待条件有可能已经满足了，也有可能不满足，我们需要进一步检查。你也可以理解为，等待者被唤醒，只是得到了一次检查的机会而已。到这里，我们小结下。如果你想在使用 Cond 的时候避免犯错，只要时刻记住调用 cond.Wait 方法之前一定要加锁，以及 waiter goroutine 被唤醒不等于等待条件被满足这两个知识点。

### 知名项目中 Cond 的使用

Cond 在实际项目中被使用的机会比较少，原因总结起来有两个。

第一，同样的场景我们会使用其他的并发原语来替代。Go 特有的 Channel 类型，有一个应用很广泛的模式就是通知机制，这个模式使用起来也特别简单。所以很多情况下，我们会使用 Channel 而不是 Cond 实现 wait/notify 机制。

第二，对于简单的 wait/notify 场景，比如等待一组 goroutine 完成之后继续执行余下的代码，我们会使用 WaitGroup 来实现。因为 WaitGroup 的使用方法更简单，而且不容易出错。比如，上面百米赛跑的问题，就可以很方便地使用 WaitGroup 来实现。

所以，我在这一讲开头提到，Cond 的使用场景很少。先前的标准库内部有几个地方使用了 Cond，比如 io/pipe.go 等，后来都被其他的并发原语（比如 Channel）替换了，sync.Cond 的路越走越窄。但是，还是有一批忠实的“粉丝”坚持在使用 Cond，原因在于 Cond 有三点特性是 Channel 无法替代的：

Cond 和一个 Locker 关联，可以利用这个 Locker 对相关的依赖条件更改提供保护。Cond 可以同时支持 Signal 和 Broadcast 方法，而 Channel 只能同时支持其中一种。Cond 的 Broadcast 方法可以被重复调用。等待条件再次变成不满足的状态后，我们又可以调用 Broadcast 再次唤醒等待的 goroutine。这也是 Channel 不能支持的，Channel 被 close 掉了之后不支持再 open。

开源项目中使用 sync.Cond 的代码少之又少，包括标准库原先一些使用 Cond 的代码也改成使用 Channel 实现了，所以别说找 Cond 相关的使用 Bug 了，想找到的一个使用的例子都不容易，我找了 Kubernetes 中的一个例子，我们一起看看它是如何使用 Cond 的。Kubernetes 项目中定义了优先级队列 [PriorityQueue](https://github.com/kubernetes/kubernetes/blob/master/pkg/scheduler/internal/queue/scheduling_queue.go) 这样一个数据结构，用来实现 Pod 的调用。它内部有三个 Pod 的队列，即 activeQ、podBackoffQ 和 unschedulableQ，其中 activeQ 就是用来调度的活跃队列（heap）。Pop 方法调用的时候，如果这个队列为空，并且这个队列没有 Close 的话，会调用 Cond 的 Wait 方法等待。你可以看到，调用 Wait 方法的时候，调用者是持有锁的，并且被唤醒的时候检查等待条件（队列是否为空）。

```
// 从队列中取出一个元素
func (p *PriorityQueue) Pop() (*framework.QueuedPodInfo, error) {
    p.lock.Lock()
    defer p.lock.Unlock()
    for p.activeQ.Len() == 0 { // 如果队列为空
      if p.closed {
        return nil, fmt.Errorf(queueClosed)
      }
      p.cond.Wait() // 等待，直到被唤醒
    }
    ......
    return pInfo, err
  }

```
当 activeQ 增加新的元素时，会调用条件变量的 Boradcast 方法，通知被 Pop 阻塞的调用者。

```
// 增加元素到队列中
func (p *PriorityQueue) Add(pod *v1.Pod) error {
    p.lock.Lock()
    defer p.lock.Unlock()
    pInfo := p.newQueuedPodInfo(pod)
    if err := p.activeQ.Add(pInfo); err != nil {//增加元素到队列中
      klog.Errorf("Error adding pod %v to the scheduling queue: %v", nsNameForPod(pod), err)
      return err
    }
    ......
    p.cond.Broadcast() //通知其它等待的goroutine，队列中有元素了

    return nil
  }
```
这个优先级队列被关闭的时候，也会调用 Broadcast 方法，避免被 Pop 阻塞的调用者永远 hang 住。

```go
func (p *PriorityQueue) Close() {
    p.lock.Lock()
    defer p.lock.Unlock()
    close(p.stop)
    p.closed = true
    p.cond.Broadcast() //关闭时通知等待的goroutine，避免它们永远等待
}
```
你可以思考一下，这里为什么使用 Cond 这个并发原语，能不能换成 Channel 实现呢？

### 总结

好了，我们来做个总结。Cond 是为等待 / 通知场景下的并发问题提供支持的。它提供了条件变量的三个基本方法 Signal、Broadcast 和 Wait，为并发的 goroutine 提供等待 / 通知机制。在实践中，处理等待 / 通知的场景时，我们常常会使用 Channel 替换 Cond，因为 Channel 类型使用起来更简洁，而且不容易出错。但是对于需要重复调用 Broadcast 的场景，比如上面 Kubernetes 的例子，每次往队列中成功增加了元素后就需要调用 Broadcast 通知所有的等待者，使用 Cond 就再合适不过了。使用 Cond 之所以容易出错，就是 Wait 调用需要加锁，以及被唤醒后一定要检查条件是否真的已经满足。你需要牢记这两点。虽然我们讲到的百米赛跑的例子，也可以通过 WaitGroup 来实现，但是本质上 WaitGroup 和 Cond 是有区别的：WaitGroup 是主 goroutine 等待确定数量的子 goroutine 完成任务；而 Cond 是等待某个条件满足，这个条件的修改可以被任意多的 goroutine 更新，而且 Cond 的 Wait 不关心也不知道其他 goroutine 的数量，只关心等待条件。而且 Cond 还有单个通知的机制，也就是 Signal 方法。

![img](https://static001.geekbang.org/resource/image/47/5d/477157d2dbe1b7e4511f56c2c9c2105d.jpg?wh=2251*1332)

## 08 | Once：一个简约而不简单的并发原语

这一讲我来讲一个简单的并发原语：Once。为什么要学习 Once 呢？我先给你答案：Once 可以用来执行且仅仅执行一次动作，常常用于单例对象的初始化场景。那这节课，我们就从对单例对象进行初始化这件事儿说起。初始化单例资源有很多方法，比如定义 package 级别的变量，这样程序在启动的时候就可以初始化：

```go
package abc

import time

var startTime = time.Now()
```
或者在 init 函数中进行初始化：

```go
package abc

var startTime time.Time

func init() {
  startTime = time.Now()
}

```
又或者在 main 函数开始执行的时候，执行一个初始化的函数：

```go
package abc

var startTime time.Tim

func initApp() {
    startTime = time.Now()
}
func main() {
  initApp()
}
```
这三种方法都是线程安全的，并且后两种方法还可以根据传入的参数实现定制化的初始化操作。但是很多时候我们是要延迟进行初始化的，所以有时候单例资源的初始化，我们会使用下面的方法：

```go
package main

import (
    "net"
    "sync"
    "time"
)

// 使用互斥锁保证线程(goroutine)安全
var connMu sync.Mutex
var conn net.Conn

func getConn() net.Conn {
    connMu.Lock()
    defer connMu.Unlock()

    // 返回已创建好的连接
    if conn != nil {
        return conn
    }

    // 创建连接
    conn, _ = net.DialTimeout("tcp", "baidu.com:80", 10*time.Second)
    return conn
}

// 使用连接
func main() {
    conn := getConn()
    if conn == nil {
        panic("conn is nil")
    }
}
```
这种方式虽然实现起来简单，但是有性能问题。一旦连接创建好，每次请求的时候还是得竞争锁才能读取到这个连接，这是比较浪费资源的，因为连接如果创建好之后，其实就不需要锁的保护了。怎么办呢？这个时候就可以使用这一讲要介绍的 Once 并发原语了。接下来我会详细介绍 Once 的使用、实现和易错场景。



### Once 的使用场景

sync.Once 只暴露了一个方法 Do，你可以多次调用 Do 方法，但是只有第一次调用 Do 方法时 f 参数才会执行，这里的 f 是一个无参数无返回值的函数。

```go
func (o *Once) Do(f func())
```
因为当且仅当第一次调用 Do 方法的时候参数 f 才会执行，即使第二次、第三次、第 n 次调用时 f 参数的值不一样，也不会被执行，比如下面的例子，虽然 f1 和 f2 是不同的函数，但是第二个函数 f2 就不会执行。

```go
package main


import (
    "fmt"
    "sync"
)

func main() {
    var once sync.Once

    // 第一个初始化函数
    f1 := func() {
        fmt.Println("in f1")
    }
    once.Do(f1) // 打印出 in f1

    // 第二个初始化函数
    f2 := func() {
        fmt.Println("in f2")
    }
    once.Do(f2) // 无输出
}
```
因为这里的 f 参数是一个无参数无返回的函数，所以你可能会通过闭包的方式引用外面的参数，比如：

```
    var addr = "baidu.com"

    var conn net.Conn
    var err error

    once.Do(func() {
        conn, err = net.Dial("tcp", addr)
    })
```
而且在实际的使用中，绝大多数情况下，你会使用闭包的方式去初始化外部的一个资源。你看，Once 的使用场景很明确，所以，在标准库内部实现中也常常能看到 Once 的身影。比如标准库内部[cache](https://github.com/golang/go/blob/f0e97546962736fe4aa73b7c7ed590f0134515e1/src/cmd/go/internal/cache/default.go)的实现上，就使用了 Once 初始化 Cache 资源，包括 defaultDir 值的获取：

```
    func Default() *Cache { // 获取默认的Cache
    defaultOnce.Do(initDefaultCache) // 初始化cache
    return defaultCache
  }
  
    // 定义一个全局的cache变量，使用Once初始化，所以也定义了一个Once变量
  var (
    defaultOnce  sync.Once
    defaultCache *Cache
  )

    func initDefaultCache() { //初始化cache,也就是Once.Do使用的f函数
    ......
    defaultCache = c
  }

    // 其它一些Once初始化的变量，比如defaultDir
    var (
    defaultDirOnce sync.Once
    defaultDir     string
    defaultDirErr  error
  )


```
还有一些测试的时候初始化测试的资源（[export_windows_test](https://github.com/golang/go/blob/50bd1c4d4eb4fac8ddeb5f063c099daccfb71b26/src/time/export_windows_test.go)）：

```
    // 测试window系统调用时区相关函数
    func ForceAusFromTZIForTesting() {
    ResetLocalOnceForTest()
        // 使用Once执行一次初始化
    localOnce.Do(func() { initLocalFromTZI(&aus) })
  }
```
除此之外，还有保证只调用一次 copyenv 的 envOnce，strings 包下的 Replacer，time 包中的[测试](https://github.com/golang/go/blob/b71eafbcece175db33acfb205e9090ca99a8f984/src/time/export_test.go#L12)，Go 拉取库时的[proxy](https://github.com/golang/go/blob/8535008765b4fcd5c7dc3fb2b73a856af4d51f9b/src/cmd/go/internal/modfetch/proxy.go#L103)，net.pipe，crc64，Regexp，……，数不胜数。我给你重点介绍一下很值得我们学习的 math/big/sqrt.go 中实现的一个数据结构，它通过 Once 封装了一个只初始化一次的值：

```
   // 值是3.0或者0.0的一个数据结构
   var threeOnce struct {
    sync.Once
    v *Float
  }
  
    // 返回此数据结构的值，如果还没有初始化为3.0，则初始化
  func three() *Float {
    threeOnce.Do(func() { // 使用Once初始化
      threeOnce.v = NewFloat(3.0)
    })
    return threeOnce.v
  }
```
它将 sync.Once 和 *Float 封装成一个对象，提供了只初始化一次的值 v。 你看它的 three 方法的实现，虽然每次都调用 threeOnce.Do 方法，但是参数只会被调用一次。当你使用 Once 的时候，你也可以尝试采用这种结构，将值和 Once 封装成一个新的数据结构，提供只初始化一次的值。

**总结一下 Once 并发原语解决的问题和使用场景：Once 常常用来初始化单例资源，或者并发访问只需初始化一次的共享资源，或者在测试的时候初始化一次测试资源。**

了解了 Once 的使用场景，那应该怎样实现一个 Once 呢？

### 如何实现一个 Once？

很多人认为实现一个 Once 一样的并发原语很简单，只需使用一个 flag 标记是否初始化过即可，最多是用 atomic 原子操作这个 flag，比如下面的实现：

```
type Once struct {
    done uint32
}

func (o *Once) Do(f func()) {
    if !atomic.CompareAndSwapUint32(&o.done, 0, 1) {
        return
    }
    f()
}
```
这确实是一种实现方式，但是，这个实现有一个很大的问题，就是如果参数 f 执行很慢的话，后续调用 Do 方法的 goroutine 虽然看到 done 已经设置为执行过了，但是获取某些初始化资源的时候可能会得到空的资源，因为 f 还没有执行完。



所以，**一个正确的 Once 实现要使用一个互斥锁，这样初始化的时候如果有并发的 goroutine，就会进入doSlow 方法**。互斥锁的机制保证只有一个 goroutine 进行初始化，同时利用双检查的机制（double-checking），再次判断 o.done 是否为 0，如果为 0，则是第一次执行，执行完毕后，就将 o.done 设置为 1，然后释放锁。

即使此时有多个 goroutine 同时进入了 doSlow 方法，因为双检查的机制，后续的 goroutine 会看到 o.done 的值为 1，也不会再次执行 f。这样既保证了并发的 goroutine 会等待 f 完成，而且还不会多次执行 f。

```
type Once struct {
    done uint32
    m    Mutex
}

func (o *Once) Do(f func()) {
    if atomic.LoadUint32(&o.done) == 0 {
        o.doSlow(f)
    }
}


func (o *Once) doSlow(f func()) {
    o.m.Lock()
    defer o.m.Unlock()
    // 双检查
    if o.done == 0 {
        defer atomic.StoreUint32(&o.done, 1)
        f()
    }
}
```
好了，到这里我们就了解了 Once 的使用场景，很明确，同时呢，也感受到 Once 的实现也是相对简单的。在实践中，其实很少会出现错误使用 Once 的情况，但是就像墨菲定律说的，凡是可能出错的事就一定会出错。使用 Once 也有可能出现两种错误场景，尽管非常罕见。我这里提前讲给你，咱打个预防针。

### 使用 Once 可能出现的 2 种错误

#### 第一种错误：死锁

你已经知道了 Do 方法会执行一次 f，但是如果 f 中再次调用这个 Once 的 Do 方法的话，就会导致死锁的情况出现。这还不是无限递归的情况，而是的的确确的 Lock 的递归调用导致的死锁。

```go
func main() {
    var once sync.Once
    once.Do(func() {
        once.Do(func() {
            fmt.Println("初始化")
        })
    })
}
```
当然，想要避免这种情况的出现，就不要在 f 参数中调用当前的这个 Once，不管是直接的还是间接的。

#### 第二种错误：未初始化

如果 f 方法执行的时候 panic，或者 f 执行初始化资源的时候失败了，这个时候，Once 还是会认为初次执行已经成功了，即使再次调用 Do 方法，也不会再次执行 f。比如下面的例子，由于一些防火墙的原因，googleConn 并没有被正确的初始化，后面如果想当然认为既然执行了 Do 方法 googleConn 就已经初始化的话，会抛出空指针的错误：

```go
func main() {
    var once sync.Once
    var googleConn net.Conn // 到Google网站的一个连接

    once.Do(func() {
        // 建立到google.com的连接，有可能因为网络的原因，googleConn并没有建立成功，此时它的值为nil
        googleConn, _ = net.Dial("tcp", "google.com:80")
    })
    // 发送http请求
    googleConn.Write([]byte("GET / HTTP/1.1\r\nHost: google.com\r\n Accept: */*\r\n\r\n"))
    io.Copy(os.Stdout, googleConn)
}
```
既然执行过 Once.Do 方法也可能因为函数执行失败的原因未初始化资源，并且以后也没机会再次初始化资源，那么这种初始化未完成的问题该怎么解决呢？这里我来告诉你一招独家秘笈，我们可以**自己实现一个类似 Once 的并发原语**，既可以返回当前调用 Do 方法是否正确完成，还可以在初始化失败后调用 Do 方法再次尝试初始化，直到初始化成功才不再初始化了。

```
// 一个功能更加强大的Once
type Once struct {
    m    sync.Mutex
    done uint32
}
// 传入的函数f有返回值error，如果初始化失败，需要返回失败的error
// Do方法会把这个error返回给调用者
func (o *Once) Do(f func() error) error {
    if atomic.LoadUint32(&o.done) == 1 { //fast path
        return nil
    }
    return o.slowDo(f)
}
// 如果还没有初始化
func (o *Once) slowDo(f func() error) error {
    o.m.Lock()
    defer o.m.Unlock()
    var err error
    if o.done == 0 { // 双检查，还没有初始化
        err = f()
        if err == nil { // 初始化成功才将标记置为已初始化
            atomic.StoreUint32(&o.done, 1)
        }
    }
    return err
}
```
我们所做的改变就是 Do 方法和参数 f 函数都会返回 error，如果 f 执行失败，会把这个错误信息返回。对 slowDo 方法也做了调整，如果 f 调用失败，我们不会更改 done 字段的值，这样后续的 goroutine 还会继续调用 f。如果 f 执行成功，才会修改 done 的值为 1。可以说，真是一顿操作猛如虎，我们使用 Once 有点得心应手的感觉了。等等，还有个问题，我们怎么查询是否初始化过呢？目前的 Once 实现可以保证你调用任意次数的 once.Do 方法，它只会执行这个方法一次。但是，有时候我们需要打一个标记。如果初始化后我们就去执行其它的操作，标准库的 Once 并不会告诉你是否初始化完成了，只是让你放心大胆地去执行 Do 方法，所以，**你还需要一个辅助变量，自己去检查是否初始化过了**，比如通过下面的代码中的 inited 字段：

```
type AnimalStore struct {once   sync.Once;inited uint32}
func (a *AnimalStore) Init() // 可以被并发调用
  a.once.Do(func() {
    longOperationSetupDbOpenFilesQueuesEtc()
    atomic.StoreUint32(&a.inited, 1)
  })
}
func (a *AnimalStore) CountOfCats() (int, error) { // 另外一个goroutine
  if atomic.LoadUint32(&a.inited) == 0 { // 初始化后才会执行真正的业务逻辑
    return 0, NotYetInitedError
  }
        //Real operation
}
```
当然，通过这段代码，我们可以解决这类问题，但是，如果官方的 Once 类型有 Done 这样一个方法的话，我们就可以直接使用了。这是有人在 Go 代码库中提出的一个 issue(#41690)。对于这类问题，一般都会被建议采用其它类型，或者自己去扩展。我们可以尝试扩展这个并发原语：

```
// Once 是一个扩展的sync.Once类型，提供了一个Done方法
type Once struct {
    sync.Once
}

// Done 返回此Once是否执行过
// 如果执行过则返回true
// 如果没有执行过或者正在执行，返回false
func (o *Once) Done() bool {
    return atomic.LoadUint32((*uint32)(unsafe.Pointer(&o.Once))) == 1
}

func main() {
    var flag Once
    fmt.Println(flag.Done()) //false

    flag.Do(func() {
        time.Sleep(time.Second)
    })

    fmt.Println(flag.Done()) //true
}
```
好了，到这里关于并发原语 Once 的内容我讲得就差不多了。最后呢，和你分享一个 Once 的踩坑案例。其实啊，使用 Once 真的不容易犯错，想犯错都很困难，因为很少有人会傻傻地在初始化函数 f 中递归调用 f，这种死锁的现象几乎不会发生。另外如果函数初始化不成功，我们一般会 panic，或者在使用的时候做检查，会及早发现这个问题，在初始化函数中加强代码。所以查看大部分的 Go 项目，几乎找不到 Once 的错误使用场景，不过我还是发现了一个。这个 issue 先从另外一个需求 (go#25955) 谈起。

### Once 的踩坑案例

go#25955 有网友提出一个需求，希望 Once 提供一个 Reset 方法，能够将 Once 重置为初始化的状态。比如下面的例子，St 通过两个 Once 控制它的 Open/Close 状态。但是在 Close 之后再调用 Open 的话，不会再执行 init 函数，因为 Once 只会执行一次初始化函数。

```
type St struct {
    openOnce *sync.Once
    closeOnce *sync.Once
}

func(st *St) Open(){
    st.openOnce.Do(func() { ... }) // init
    ...
}

func(st *St) Close(){
    st.closeOnce.Do(func() { ... }) // deinit
    ...
}
```
所以提交这个 Issue 的开发者希望 Once 增加一个 Reset 方法，Reset 之后再调用 once.Do 就又可以初始化了。Go 的核心开发者 Ian Lance Taylor 给他了一个简单的解决方案。在这个例子中，只使用一个 ponce *sync.Once 做初始化，Reset 的时候给 ponce 这个变量赋值一个新的 Once 实例即可 (ponce = new(sync.Once))。Once 的本意就是执行一次，所以 Reset 破坏了这个并发原语的本意。这个解决方案一点都没问题，可以很好地解决这位开发者的需求。Docker 较早的版本（1.11.2）中使用了它们的一个网络库 libnetwork，这个网络库在使用 Once 的时候就使用 Ian Lance Taylor 介绍的方法，但是不幸的是，它的 Reset 方法中又改变了 Once 指针的值，导致程序 panic 了。原始逻辑比较复杂，一个简化版可重现的[代码](https://play.golang.org/p/xPULnrVKiY)如下：

```go
package main

import (
  "fmt"
  "sync"
  "time"
)

// 一个组合的并发原语
type MuOnce struct {
  sync.RWMutex
  sync.Once
  mtime time.Time
  vals  []string
}

// 相当于reset方法，会将m.Once重新复制一个Once
func (m *MuOnce) refresh() {
  m.Lock()
  defer m.Unlock()
  m.Once = sync.Once{}
  m.mtime = time.Now()
  m.vals = []string{m.mtime.String()}
}

// 获取某个初始化的值，如果超过某个时间，会reset Once
func (m *MuOnce) strings() []string {
  now := time.Now()
  m.RLock()
  if now.After(m.mtime) {
    defer m.Do(m.refresh) // 使用refresh函数重新初始化
  }
  vals := m.vals
  m.RUnlock()
  return vals
}

func main() {
  fmt.Println("Hello, playground")
  m := new(MuOnce)
  fmt.Println(m.strings())
  fmt.Println(m.strings())
}
```
如果你执行这段代码就会 panic:

![img](https://static001.geekbang.org/resource/image/f3/af/f3401f75a86e1d0c3b257f52696228af.png?wh=1323*482)

原因在于第 31 行执行 m.Once.Do 方法的时候，使用的是 m.Once 的指针，然后调用 m.refresh，在执行 m.refresh 的时候 Once 内部的 Mutex 首先会加锁（可以再翻看一下这一讲的 Once 的实现原理）， 但是，在 refresh 中更改了 Once 指针的值之后，结果在执行完 refresh 释放锁的时候，释放的是一个刚初始化未加锁的 Mutex，所以就 panic 了。如果你还不太明白，我再给你简化成一个更简单的例子：

```go
package main


import (
    "sync"
)

type Once struct {
    m sync.Mutex
}

func (o *Once) doSlow() {
    o.m.Lock()
    defer o.m.Unlock()

    // 这里更新的o指针的值!!!!!!!, 会导致上一行Unlock出错
    *o = Once{}
}

func main() {
    var once Once
    once.doSlow()
}
```
doSlow 方法就演示了这个错误。Ian Lance Taylor 介绍的 Reset 方法没有错误，但是你在使用的时候千万别再初始化函数中 Reset 这个 Once，否则势必会导致 Unlock 一个未加锁的 Mutex 的错误。总的来说，这还是对 Once 的实现机制不熟悉，又进行复杂使用导致的错误。不过最新版的 libnetwork 相关的地方已经去掉了 Once 的使用了。所以，我带你一起来看这个案例，主要目的还是想巩固一下我们对 Once 的理解。



### 总结

今天我们一起学习了 Once，我们常常使用它来实现单例模式。单例是 23 种设计模式之一，也是常常引起争议的设计模式之一，甚至有人把它归为反模式。为什么说它是反模式呢，我拿标准库中的单例模式给你介绍下。因为 Go 没有 immutable 类型，导致我们声明的全局变量都是可变的，别的地方或者第三方库可以随意更改这些变量。比如 package io 中定义了几个全局变量，比如 io.EOF：

```
var EOF = errors.New("EOF")
```
因为它是一个 package 级别的变量，我们可以在程序中偷偷把它改了，这会导致一些依赖 io.EOF 这个变量做判断的代码出错。

```
io.EOF = errors.New("我们自己定义的EOF")
```
从我个人的角度来说，一些单例（全局变量）的确很方便，比如 Buffer 池或者连接池，所以有时候我们也不要谈虎色变。虽然有人把单例模式称之为反模式，但毕竟只能代表一部分开发者的观点，否则也不会把它列在 23 种设计模式中了。如果你真的担心这个 package 级别的变量被人修改，你可以不把它们暴露出来，而是提供一个只读的 GetXXX 的方法，这样别人就不会进行修改了。而且，Once 不只应用于单例模式，一些变量在也需要在使用的时候做延迟初始化，所以也是可以使用 Once 处理这些场景的。总而言之，Once 的应用场景还是很广泛的。**一旦你遇到只需要初始化一次的场景，首先想到的就应该是 Once 并发原语。**

![img](https://static001.geekbang.org/resource/image/4b/ba/4b1721a63d7bd3f3995eb18cee418fba.jpg?wh=2250*880)



## 09 | map：如何实现线程安全的map类型？

哈希表（Hash Table）这个数据结构，我们已经非常熟悉了。它实现的就是 key-value 之间的映射关系，主要提供的方法包括 Add、Lookup、Delete 等。因为这种数据结构是一个基础的数据结构，每个 key 都会有一个唯一的索引值，通过索引可以很快地找到对应的值，所以使用哈希表进行数据的插入和读取都是很快的。Go 语言本身就内建了这样一个数据结构，也就是 map 数据类型。今天呢，我们就先来学习 Go 语言内建的这个 map 类型，了解它的基本使用方法和使用陷阱，然后再学习如何实现线程安全的 map 类型，最后我还会给你介绍 Go 标准库中线程安全的 sync.Map 类型。学完了这节课，你可以学会几种可以并发访问的 map 类型。

### map 的基本使用方法

Go 内建的 map 类型如下：

```
map[K]V
```
其中，key 类型的 K 必须是可比较的（comparable），也就是可以通过 == 和 != 操作符进行比较；value 的值和类型无所谓，可以是任意的类型，或者为 nil。在 Go 语言中，bool、整数、浮点数、复数、字符串、指针、Channel、接口都是可比较的，包含可比较元素的 struct 和数组，这俩也是可比较的，而 slice、map、函数值都是不可比较的。那么，上面这些可比较的数据类型都可以作为 map 的 key 吗？显然不是。通常情况下，我们会选择内建的基本类型，比如整数、字符串做 key 的类型，因为这样最方便。这里有一点需要注意，如果使用 struct 类型做 key 其实是有坑的，因为如果 struct 的某个字段值修改了，查询 map 时无法获取它 add 进去的值，如下面的例子：

```
type mapKey struct {
    key int
}

func main() {
    var m = make(map[mapKey]string)
    var key = mapKey{10}


    m[key] = "hello"
    fmt.Printf("m[key]=%s\n", m[key])


    // 修改key的字段的值后再次查询map，无法获取刚才add进去的值
    key.key = 100
    fmt.Printf("再次查询m[key]=%s\n", m[key])
}
```
那该怎么办呢？如果要使用 struct 作为 key，我们要保证 struct 对象在逻辑上是不可变的，这样才会保证 map 的逻辑没有问题。以上就是选取 key 类型的注意点了。接下来，我们看一下使用 map[key]函数时需要注意的一个知识点。**在 Go 中，map[key]函数返回结果可以是一个值，也可以是两个值，这是容易让人迷惑的地方**。原因在于，如果获取一个不存在的 key 对应的值时，会返回零值。为了区分真正的零值和 key 不存在这两种情况，可以根据第二个返回值来区分，如下面的代码的第 6 行、第 7 行：

```go
func main() {
    var m = make(map[string]int)
    m["a"] = 0
    fmt.Printf("a=%d; b=%d\n", m["a"], m["b"])

    av, aexisted := m["a"]
    bv, bexisted := m["b"]
    fmt.Printf("a=%d, existed: %t; b=%d, existed: %t\n", av, aexisted, bv, bexisted)
}
```
map 是无序的，所以当遍历一个 map 对象的时候，迭代的元素的顺序是不确定的，无法保证两次遍历的顺序是一样的，也不能保证和插入的顺序一致。那怎么办呢？如果我们想要按照 key 的顺序获取 map 的值，需要先取出所有的 key 进行排序，然后按照这个排序的 key 依次获取对应的值。而如果我们想要保证元素有序，比如按照元素插入的顺序进行遍历，可以使用辅助的数据结构，比如[orderedmap](https://github.com/elliotchance/orderedmap)，来记录插入顺序。好了，总结下关于 map 我们需要掌握的内容：map 的类型是 map[key]，key 类型的 K 必须是可比较的，通常情况下，我们会选择内建的基本类型，比如整数、字符串做 key 的类型。如果要使用 struct 作为 key，我们要保证 struct 对象在逻辑上是不可变的。在 Go 中，map[key]函数返回结果可以是一个值，也可以是两个值。map 是无序的，如果我们想要保证遍历 map 时元素有序，可以使用辅助的数据结构，比如[orderedmap](https://github.com/elliotchance/orderedmap)。

### 使用 map 的 2 种常见错误

那接下来，我们来看使用 map 最常犯的两个错误，就是未初始化和并发读写。

#### 常见错误一：未初始化

和 slice 或者 Mutex、RWmutex 等 struct 类型不同，map 对象必须在使用之前初始化。如果不初始化就直接赋值的话，会出现 panic 异常，比如下面的例子，m 实例还没有初始化就直接进行操作会导致 panic（第 3 行）:

```go
func main() {
    var m map[int]int
    m[100] = 100
}
```
解决办法就是在第 2 行初始化这个实例（m := make(map[int]int)）。从一个 nil 的 map 对象中获取值不会 panic，而是会得到零值，所以下面的代码不会报错:

```go
func main() {
    var m map[int]int
    fmt.Println(m[100])
}
```
这个例子很简单，我们可以意识到 map 的初始化问题。但有时候 map 作为一个 struct 字段的时候，就很容易忘记初始化了。

```
type Counter struct {
    Website      string
    Start        time.Time
    PageCounters map[string]int
}

func main() {
    var c Counter
    c.Website = "baidu.com"


    c.PageCounters["/"]++
}
```
所以，关于初始化这一点，我再强调一下，目前还没有工具可以检查，我们只能记住“别忘记初始化”这一条规则。

#### 常见错误二：并发读写

对于 map 类型，另一个很容易犯的错误就是并发访问问题。这个易错点，相当令人讨厌，如果没有注意到并发问题，程序在运行的时候就有可能出现并发读写导致的 panic。Go 内建的 map 对象不是线程（goroutine）安全的，并发读写的时候运行时会有检查，遇到并发问题就会导致 panic。我们一起看一个并发访问 map 实例导致 panic 的例子：

```go
func main() {
    var m = make(map[int]int,10) // 初始化一个map
    go func() {
        for {
            m[1] = 1 //设置key
        }
    }()

    go func() {
        for {
            _ = m[2] //访问这个map
        }
    }()
    select {}
}
```
虽然这段代码看起来是读写 goroutine 各自操作不同的元素，貌似 map 也没有扩容的问题，但是运行时检测到同时对 map 对象有并发访问，就会直接 panic。panic 信息会告诉我们代码中哪一行有读写问题，根据这个错误信息你就能快速定位出来是哪一个 map 对象在哪里出的问题了。

![img](https://static001.geekbang.org/resource/image/82/62/82fb958bb73128cf8afc438de4acc862.png?wh=983*441)

这个错误非常常见，是几乎每个人都会踩到的坑。其实，不只是我们写代码时容易犯这个错，一些知名的项目中也是屡次出现这个问题，比如 Docker issue 40772，在删除 map 对象的元素时忘记了加锁：

![img](https://static001.geekbang.org/resource/image/60/ce/60642481f9707520045991030d0f00ce.png?wh=898*268)

Docker issue 40772，Docker issue 35588、34540、39643 等等，也都有并发读写 map 的问题。除了 Docker 中，Kubernetes 的 issue 84431、72464、68647、64484、48045、45593、37560 等，以及 TiDB 的 issue 14960 和 17494 等，也出现了这个错误。这么多人都会踩的坑，有啥解决方案吗？肯定有，那接下来，我们就继续来看如何解决内建 map 的并发读写问题。

### 如何实现线程安全的 map 类型？

避免 map 并发读写 panic 的方式之一就是加锁，考虑到读写性能，可以使用读写锁提供性能。

#### 加读写锁：扩展 map，支持并发读写

比较遗憾的是，目前 Go 还没有正式发布泛型特性，我们还不能实现一个通用的支持泛型的加锁 map。但是，将要发布的泛型方案已经可以验证测试了，离发布也不远了，也许发布之后 sync.Map 就支持泛型了。当然了，如果没有泛型支持，我们也能解决这个问题。我们可以通过 interface{}来模拟泛型，但还是要涉及接口和具体类型的转换，比较复杂，还不如将要发布的泛型方案更直接、性能更好。这里我以一个具体的 map 类型为例，来演示利用读写锁实现线程安全的 map[int]int 类型：

```
type RWMap struct { // 一个读写锁保护的线程安全的map
    sync.RWMutex // 读写锁保护下面的map字段
    m map[int]int
}
// 新建一个RWMap
func NewRWMap(n int) *RWMap {
    return &RWMap{
        m: make(map[int]int, n),
    }
}
func (m *RWMap) Get(k int) (int, bool) { //从map中读取一个值
    m.RLock()
    defer m.RUnlock()
    v, existed := m.m[k] // 在锁的保护下从map中读取
    return v, existed
}

func (m *RWMap) Set(k int, v int) { // 设置一个键值对
    m.Lock()              // 锁保护
    defer m.Unlock()
    m.m[k] = v
}

func (m *RWMap) Delete(k int) { //删除一个键
    m.Lock()                   // 锁保护
    defer m.Unlock()
    delete(m.m, k)
}

func (m *RWMap) Len() int { // map的长度
    m.RLock()   // 锁保护
    defer m.RUnlock()
    return len(m.m)
}

func (m *RWMap) Each(f func(k, v int) bool) { // 遍历map
    m.RLock()             //遍历期间一直持有读锁
    defer m.RUnlock()

    for k, v := range m.m {
        if !f(k, v) {
            return
        }
    }
}

```
正如这段代码所示，对 map 对象的操作，无非就是增删改查和遍历等几种常见操作。我们可以把这些操作分为读和写两类，其中，查询和遍历可以看做读操作，增加、修改和删除可以看做写操作。如例子所示，我们可以通过读写锁对相应的操作进行保护。

#### 分片加锁：更高效的并发 map

虽然使用读写锁可以提供线程安全的 map，但是在大量并发读写的情况下，锁的竞争会非常激烈。我在第 4 讲中提到过，锁是性能下降的万恶之源之一。在并发编程中，我们的一条原则就是尽量减少锁的使用。一些单线程单进程的应用（比如 Redis 等），基本上不需要使用锁去解决并发线程访问的问题，所以可以取得很高的性能。但是对于 Go 开发的应用程序来说，并发是常用的一个特性，在这种情况下，我们能做的就是，尽量减少锁的粒度和锁的持有时间。

你可以优化业务处理的代码，以此来减少锁的持有时间，比如将串行的操作变成并行的子任务执行。不过，这就是另外的故事了，今天我们还是主要讲对同步原语的优化，所以这里我重点讲如何减少锁的粒度。

**减少锁的粒度常用的方法就是分片**（Shard），将一把锁分成几把锁，每个锁控制一个分片。Go 比较知名的分片并发 map 的实现是[orcaman/concurrent-map](orcaman/concurrent-map)。

它默认采用 32 个分片，GetShard 是一个关键的方法，能够根据 key 计算出分片索引。

```

    var SHARD_COUNT = 32
  
    // 分成SHARD_COUNT个分片的map
  type ConcurrentMap []*ConcurrentMapShared
  
  // 通过RWMutex保护的线程安全的分片，包含一个map
  type ConcurrentMapShared struct {
    items        map[string]interface{}
    sync.RWMutex // Read Write mutex, guards access to internal map.
  }
  
  // 创建并发map
  func New() ConcurrentMap {
    m := make(ConcurrentMap, SHARD_COUNT)
    for i := 0; i < SHARD_COUNT; i++ {
      m[i] = &ConcurrentMapShared{items: make(map[string]interface{})}
    }
    return m
  }
  

  // 根据key计算分片索引
  func (m ConcurrentMap) GetShard(key string) *ConcurrentMapShared {
    return m[uint(fnv32(key))%uint(SHARD_COUNT)]
  }
```
增加或者查询的时候，首先根据分片索引得到分片对象，然后对分片对象加锁进行操作：

```go
func (m ConcurrentMap) Set(key string, value interface{}) {
    // 根据key计算出对应的分片
    shard := m.GetShard(key)
    shard.Lock() //对这个分片加锁，执行业务操作
    shard.items[key] = value
    shard.Unlock()
}

func (m ConcurrentMap) Get(key string) (interface{}, bool) {
    // 根据key计算出对应的分片
    shard := m.GetShard(key)
    shard.RLock()
    // 从这个分片读取key的值
    val, ok := shard.items[key]
    shard.RUnlock()
    return val, ok
}
```
当然，除了 GetShard 方法，ConcurrentMap 还提供了很多其他的方法。这些方法都是通过计算相应的分片实现的，目的是保证把锁的粒度限制在分片上。好了，到这里我们就学会了解决 map 并发 panic 的两个方法：加锁和分片。

**在我个人使用并发 map 的过程中，加锁和分片加锁这两种方案都比较常用，如果是追求更高的性能，显然是分片加锁更好，因为它可以降低锁的粒度，进而提高访问此 map 对象的吞吐。如果并发性能要求不是那么高的场景，简单加锁方式更简单。**

接下来，我会继续给你介绍 sync.Map，这是 Go 官方线程安全 map 的标准实现。虽然是官方标准，反而是不常用的，为什么呢？一句话来说就是 map 要解决的场景很难描述，很多时候在做抉择时根本就不知道该不该用它。但是呢，确实有一些特定的场景，我们需要用到 sync.Map 来实现，所以还是很有必要学习这个知识点。具体什么场景呢，我慢慢给你道来。

### 应对特殊场景的 sync.Map

Go 内建的 map 类型不是线程安全的，所以 Go 1.9 中增加了一个线程安全的 map，也就是 sync.Map。但是，我们一定要记住，这个 sync.Map 并不是用来替换内建的 map 类型的，它只能被应用在一些特殊的场景里。那这些特殊的场景是啥呢？[官方的文档](https://golang.org/pkg/sync/#Map)中指出，在以下两个场景中使用 sync.Map，会比使用 map+RWMutex 的方式，性能要好得多：

只会增长的缓存系统中，一个 key 只写入一次而被读很多次；多个 goroutine 为不相交的键集读、写和重写键值对。

这两个场景说得都比较笼统，而且，这些场景中还包含了一些特殊的情况。所以，官方建议你针对自己的场景做性能评测，如果确实能够显著提高性能，再使用 sync.Map。这么来看，我们能用到 sync.Map 的场景确实不多。即使是 sync.Map 的作者 Bryan C. Mills，也很少使用 sync.Map，即便是在使用 sync.Map 的时候，也是需要临时查询它的 API，才能清楚记住它的功能。所以，我们可以把 sync.Map 看成一个生产环境中很少使用的同步原语。

### sync.Map 的实现

那 sync.Map 是怎么实现的呢？它是如何解决并发问题提升性能的呢？其实 sync.Map 的实现有几个优化点，这里先列出来，我们后面慢慢分析。

空间换时间。通过冗余的两个数据结构（只读的 read 字段、可写的 dirty），来减少加锁对性能的影响。对只读字段（read）的操作不需要加锁。优先从 read 字段读取、更新、删除，因为对 read 字段的读取不需要锁。动态调整。miss 次数多了之后，将 dirty 数据提升为 read，避免总是从 dirty 中加锁读取。double-checking。加锁之后先还要再检查 read 字段，确定真的不存在才操作 dirty 字段。延迟删除。删除一个键值只是打标记，只有在提升 dirty 字段为 read 字段的时候才清理删除的数据。

要理解 sync.Map 这些优化点，我们还是得深入到它的设计和实现上，去学习它的处理方式。我们先看一下 map 的数据结构：

```
type Map struct {
    mu Mutex
    // 基本上你可以把它看成一个安全的只读的map
    // 它包含的元素其实也是通过原子操作更新的，但是已删除的entry就需要加锁操作了
    read atomic.Value // readOnly

    // 包含需要加锁才能访问的元素
    // 包括所有在read字段中但未被expunged（删除）的元素以及新加的元素
    dirty map[interface{}]*entry

    // 记录从read中读取miss的次数，一旦miss数和dirty长度一样了，就会把dirty提升为read，并把dirty置空
    misses int
}

type readOnly struct {
    m       map[interface{}]*entry
    amended bool // 当dirty中包含read没有的数据时为true，比如新增一条数据
}

// expunged是用来标识此项已经删掉的指针
// 当map中的一个项目被删除了，只是把它的值标记为expunged，以后才有机会真正删除此项
var expunged = unsafe.Pointer(new(interface{}))

// entry代表一个值
type entry struct {
    p unsafe.Pointer // *interface{}
}
```
如果 dirty 字段非 nil 的话，map 的 read 字段和 dirty 字段会包含相同的非 expunged 的项，所以如果通过 read 字段更改了这个项的值，从 dirty 字段中也会读取到这个项的新值，因为本来它们指向的就是同一个地址。dirty 包含重复项目的好处就是，一旦 miss 数达到阈值需要将 dirty 提升为 read 的话，只需简单地把 dirty 设置为 read 对象即可。不好的一点就是，当创建新的 dirty 对象的时候，需要逐条遍历 read，把非 expunged 的项复制到 dirty 对象中。接下来，我们就深入到源码去看看 sync.map 的实现。在看这部分源码的过程中，我们只要重点关注 Store、Load 和 Delete 这 3 个核心的方法就可以了。Store、Load 和 Delete 这三个核心函数的操作都是先从 read 字段中处理的，因为读取 read 字段的时候不用加锁。

### Store 方法

我们先来看 Store 方法，它是用来设置一个键值对，或者更新一个键值对的。

```go
func (m *Map) Store(key, value interface{}) {
    read, _ := m.read.Load().(readOnly)
    // 如果read字段包含这个项，说明是更新，cas更新项目的值即可
    if e, ok := read.m[key]; ok && e.tryStore(&value) {
        return
    }

    // read中不存在，或者cas更新失败，就需要加锁访问dirty了
    m.mu.Lock()
    read, _ = m.read.Load().(readOnly)
    if e, ok := read.m[key]; ok { // 双检查，看看read是否已经存在了
        if e.unexpungeLocked() {
            // 此项目先前已经被删除了，通过将它的值设置为nil，标记为unexpunged
            m.dirty[key] = e
        }
        e.storeLocked(&value) // 更新
    } else if e, ok := m.dirty[key]; ok { // 如果dirty中有此项
        e.storeLocked(&value) // 直接更新
    } else { // 否则就是一个新的key
        if !read.amended { //如果dirty为nil
            // 需要创建dirty对象，并且标记read的amended为true,
            // 说明有元素它不包含而dirty包含
            m.dirtyLocked()
            m.read.Store(readOnly{m: read.m, amended: true})
        }
        m.dirty[key] = newEntry(value) //将新值增加到dirty对象中
    }
    m.mu.Unlock()
}
```
可以看出，Store 既可以是新增元素，也可以是更新元素。如果运气好的话，更新的是已存在的未被删除的元素，直接更新即可，不会用到锁。如果运气不好，需要更新（重用）删除的对象、更新还未提升的 dirty 中的对象，或者新增加元素的时候就会使用到了锁，这个时候，性能就会下降。所以从这一点来看，sync.Map 适合那些只会增长的缓存系统，可以进行更新，但是不要删除，并且不要频繁地增加新元素。新加的元素需要放入到 dirty 中，如果 dirty 为 nil，那么需要从 read 字段中复制出来一个 dirty 对象：

```go
func (m *Map) dirtyLocked() {
    if m.dirty != nil { // 如果dirty字段已经存在，不需要创建了
        return
    }

    read, _ := m.read.Load().(readOnly) // 获取read字段
    m.dirty = make(map[interface{}]*entry, len(read.m))
    for k, e := range read.m { // 遍历read字段
        if !e.tryExpungeLocked() { // 把非punged的键值对复制到dirty中
            m.dirty[k] = e
        }
    }
}
```
### Load 方法

Load 方法用来读取一个 key 对应的值。它也是从 read 开始处理，一开始并不需要锁。

```go
func (m *Map) Load(key interface{}) (value interface{}, ok bool) {
    // 首先从read处理
    read, _ := m.read.Load().(readOnly)
    e, ok := read.m[key]
    if !ok && read.amended { // 如果不存在并且dirty不为nil(有新的元素)
        m.mu.Lock()
        // 双检查，看看read中现在是否存在此key
        read, _ = m.read.Load().(readOnly)
        e, ok = read.m[key]
        if !ok && read.amended {//依然不存在，并且dirty不为nil
            e, ok = m.dirty[key]// 从dirty中读取
            // 不管dirty中存不存在，miss数都加1
            m.missLocked()
        }
        m.mu.Unlock()
    }
    if !ok {
        return nil, false
    }
    return e.load() //返回读取的对象，e既可能是从read中获得的，也可能是从dirty中获得的
}
```
如果幸运的话，我们从 read 中读取到了这个 key 对应的值，那么就不需要加锁了，性能会非常好。但是，如果请求的 key 不存在或者是新加的，就需要加锁从 dirty 中读取。所以，读取不存在的 key 会因为加锁而导致性能下降，读取还没有提升的新值的情况下也会因为加锁性能下降。其中，missLocked 增加 miss 的时候，如果 miss 数等于 dirty 长度，会将 dirty 提升为 read，并将 dirty 置空。

```go
func (m *Map) missLocked() {
    m.misses++ // misses计数加一
    if m.misses < len(m.dirty) { // 如果没达到阈值(dirty字段的长度),返回
        return
    }
    m.read.Store(readOnly{m: m.dirty}) //把dirty字段的内存提升为read字段
    m.dirty = nil // 清空dirty
    m.misses = 0  // misses数重置为0
}
```
### Delete 方法

sync.map 的第 3 个核心方法是 Delete 方法。在 Go 1.15 中欧长坤提供了一个 LoadAndDelete 的实现（go#issue 33762），所以 Delete 方法的核心改在了对 LoadAndDelete 中实现了。同样地，Delete 方法是先从 read 操作开始，原因我们已经知道了，因为不需要锁。

```go
func (m *Map) LoadAndDelete(key interface{}) (value interface{}, loaded bool) {
    read, _ := m.read.Load().(readOnly)
    e, ok := read.m[key]
    if !ok && read.amended {
        m.mu.Lock()
        // 双检查
        read, _ = m.read.Load().(readOnly)
        e, ok = read.m[key]
        if !ok && read.amended {
            e, ok = m.dirty[key]
            // 这一行长坤在1.15中实现的时候忘记加上了，导致在特殊的场景下有些key总是没有被回收
            delete(m.dirty, key)
            // miss数加1
            m.missLocked()
        }
        m.mu.Unlock()
    }
    if ok {
        return e.delete()
    }
    return nil, false
}

func (m *Map) Delete(key interface{}) {
    m.LoadAndDelete(key)
}
func (e *entry) delete() (value interface{}, ok bool) {
    for {
        p := atomic.LoadPointer(&e.p)
        if p == nil || p == expunged {
            return nil, false
        }
        if atomic.CompareAndSwapPointer(&e.p, p, nil) {
            return *(*interface{})(p), true
        }
    }
}
```
如果 read 中不存在，那么就需要从 dirty 中寻找这个项目。最终，如果项目存在就删除（将它的值标记为 nil）。如果项目不为 nil 或者没有被标记为 expunged，那么还可以把它的值返回。最后，我补充一点，sync.map 还有一些 LoadAndDelete、LoadOrStore、Range 等辅助方法，但是没有 Len 这样查询 sync.Map 的包含项目数量的方法，并且官方也不准备提供。如果你想得到 sync.Map 的项目数量的话，你可能不得不通过 Range 逐个计数。

### 总结

Go 内置的 map 类型使用起来很方便，但是它有一个非常致命的缺陷，那就是它存在着并发问题，所以如果有多个 goroutine 同时并发访问这个 map，就会导致程序崩溃。所以 Go 官方 Blog 很早就提供了一种加锁的[方法](https://go.dev/blog/maps#TOC_6.)，还有后来提供了适用特定场景的线程安全的 sync.Map，还有第三方实现的分片式的 map，这些方法都可以应用于并发访问的场景。

这里我给你的建议，也是 Go 开发者给的建议，就是通过性能测试，看看某种线程安全的 map 实现是否满足你的需求。当然还有一些扩展其它功能的 map 实现，比如带有过期功能的[timedmap](https://github.com/zekroTJA/timedmap)、使用红黑树实现的 key 有序的[treemap](https://godoc.org/github.com/emirpasic/gods/maps/treemap)等，因为和并发问题没有关系，就不详细介绍了。这里我给你提供了链接，你可以自己探索。

![img](https://static001.geekbang.org/resource/image/a8/03/a80408a137b13f934b0dd6f2b6c5cc03.jpg?wh=2250*1771)

## 10 | Pool：性能提升大杀器

Go 是一个自动垃圾回收的编程语言，采用[三色并发标记算法](https://go.dev/blog/ismmkeynote)标记对象并回收。和其它没有自动垃圾回收的编程语言不同，使用 Go 语言创建对象的时候，我们没有回收 / 释放的心理负担，想用就用，想创建就创建。

但是，**如果你想使用 Go 开发一个高性能的应用程序的话，就必须考虑垃圾回收给性能带来的影响**，毕竟，Go 的自动垃圾回收机制还是有一个 STW（stop-the-world，程序暂停）的时间，而且，大量地创建在堆上的对象，也会影响垃圾回收标记的时间。

所以，一般我们做性能优化的时候，会采用对象池的方式，把不用的对象回收起来，避免被垃圾回收掉，这样使用的时候就不必在堆上重新创建了。不止如此，像数据库连接、TCP 的长连接，这些连接在创建的时候是一个非常耗时的操作。如果每次都创建一个新的连接对象，耗时较长，很可能整个业务的大部分耗时都花在了创建连接上。所以，如果我们能把这些连接保存下来，避免每次使用的时候都重新创建，不仅可以大大减少业务的耗时，还能提高应用程序的整体性能。Go 标准库中提供了一个通用的 Pool 数据结构，也就是 sync.Pool，我们使用它可以创建池化的对象。这节课我会详细给你介绍一下 sync.Pool 的使用方法、实现原理以及常见的坑，帮助你全方位地掌握标准库的 Pool。

不过，这个类型也有一些使用起来不太方便的地方，就是**它池化的对象可能会被垃圾回收掉**，这对于数据库长连接等场景是不合适的。所以在这一讲中，我会专门介绍其它的一些 Pool，包括 TCP 连接池、数据库连接池等等。除此之外，我还会专门介绍一个池的应用场景： Worker Pool，或者叫做 goroutine pool，这也是常用的一种并发模式，可以使用有限的 goroutine 资源去处理大量的业务数据。

### sync.Pool

首先，我们来学习下标准库提供的 sync.Pool 数据类型。sync.Pool 数据类型用来保存一组可独立访问的临时对象。请注意这里加粗的“临时”这两个字，它说明了 sync.Pool 这个数据类型的特点，也就是说，它池化的对象会在未来的某个时候被毫无预兆地移除掉。而且，如果没有别的对象引用这个被移除的对象的话，这个被移除的对象就会被垃圾回收掉。因为 Pool 可以有效地减少新对象的申请，从而提高程序性能，所以 Go 内部库也用到了 sync.Pool，比如 fmt 包，它会使用一个动态大小的 buffer 池做输出缓存，当大量的 goroutine 并发输出的时候，就会创建比较多的 buffer，并且在不需要的时候回收掉。有两个知识点你需要记住：

sync.Pool 本身就是线程安全的，多个 goroutine 可以并发地调用它的方法存取对象；sync.Pool 不可在使用之后再复制使用。



### sync.Pool 的使用方法

知道了 sync.Pool 这个数据类型的特点，接下来，我们来学习下它的使用方法。其实，这个数据类型不难，它只提供了三个对外的方法：New、Get 和 Put。

1.New

Pool struct 包含一个 New 字段，这个字段的类型是函数 func() interface{}。当调用 Pool 的 Get 方法从池中获取元素，没有更多的空闲元素可返回时，就会调用这个 New 方法来创建新的元素。如果你没有设置 New 字段，没有更多的空闲元素可返回时，Get 方法将返回 nil，表明当前没有可用的元素。有趣的是，New 是可变的字段。这就意味着，你可以在程序运行的时候改变创建元素的方法。当然，很少有人会这么做，因为一般我们创建元素的逻辑都是一致的，要创建的也是同一类的元素，所以你在使用 Pool 的时候也没必要玩一些“花活”，在程序运行时更改 New 的值。

2.Get

如果调用这个方法，就会从 Pool取走一个元素，这也就意味着，这个元素会从 Pool 中移除，返回给调用者。不过，除了返回值是正常实例化的元素，Get 方法的返回值还可能会是一个 nil（Pool.New 字段没有设置，又没有空闲元素可以返回），所以你在使用的时候，可能需要判断。

3.Put

这个方法用于将一个元素返还给 Pool，Pool 会把这个元素保存到池中，并且可以复用。但如果 Put 一个 nil 值，Pool 就会忽略这个值。好了，了解了这几个方法，下面我们看看 sync.Pool 最常用的一个场景：buffer 池（缓冲池）。因为 byte slice 是经常被创建销毁的一类对象，使用 buffer 池可以缓存已经创建的 byte slice，比如，著名的静态网站生成工具 Hugo 中，就包含这样的实现[bufpool](https://github.com/gohugoio/hugo/blob/master/bufferpool/bufpool.go)，你可以看一下下面这段代码：

```
var buffers = sync.Pool{
  New: func() interface{} { 
    return new(bytes.Buffer)
  },
}

func GetBuffer() *bytes.Buffer {
  return buffers.Get().(*bytes.Buffer)
}

func PutBuffer(buf *bytes.Buffer) {
  buf.Reset()
  buffers.Put(buf)
}
```
除了 Hugo，这段 buffer 池的代码非常常用。很可能你在阅读其它项目的代码的时候就碰到过，或者是你自己实现 buffer 池的时候也会这么去实现，但是请你注意了，这段代码是有问题的，你一定不要将上面的代码应用到实际的产品中。它可能会有内存泄漏的问题，下面我会重点讲这个问题。

### 实现原理

了解了 sync.Pool 的基本使用方法，下面我们就来重点学习下它的实现。Go 1.13 之前的 sync.Pool 的实现有 2 大问题：

**1. 每次 GC 都会回收创建的对象。**

如果缓存元素数量太多，就会导致 STW 耗时变长；缓存元素都被回收后，会导致 Get 命中率下降，Get 方法不得不新创建很多对象。

**2. 底层实现使用了 Mutex，对这个锁并发请求竞争激烈的时候，会导致性能的下降。**

在 Go 1.13 中，sync.Pool 做了大量的优化。前几讲中我提到过，提高并发程序性能的优化点是尽量不要使用锁，如果不得已使用了锁，就把锁 Go 的粒度降到最低。Go 对 Pool 的优化就是避免使用锁，同时将加锁的 queue 改成 lock-free 的 queue 的实现，给即将移除的元素再多一次“复活”的机会。当前，sync.Pool 的数据结构如下图所示：

![img](https://static001.geekbang.org/resource/image/f4/96/f4003704663ea081230760098f8af696.jpg?wh=3659*2186)

Pool 最重要的两个字段是 local 和 victim，因为它们两个主要用来存储空闲的元素。弄清楚这两个字段的处理逻辑，你就能完全掌握 sync.Pool 的实现了。下面我们来看看这两个字段的关系。每次垃圾回收的时候，Pool 会把 victim 中的对象移除，然后把 local 的数据给 victim，这样的话，local 就会被清空，而 victim 就像一个垃圾分拣站，里面的东西可能会被当做垃圾丢弃了，但是里面有用的东西也可能被捡回来重新使用。victim 中的元素如果被 Get 取走，那么这个元素就很幸运，因为它又“活”过来了。但是，如果这个时候 Get 的并发不是很大，元素没有被 Get 取走，那么就会被移除掉，因为没有别人引用它的话，就会被垃圾回收掉。下面的代码是垃圾回收时 sync.Pool 的处理逻辑：

```go
func poolCleanup() {
    // 丢弃当前victim, STW所以不用加锁
    for _, p := range oldPools {
        p.victim = nil
        p.victimSize = 0
    }

    // 将local复制给victim, 并将原local置为nil
    for _, p := range allPools {
        p.victim = p.local
        p.victimSize = p.localSize
        p.local = nil
        p.localSize = 0
    }

    oldPools, allPools = allPools, nil
}
```
在这段代码中，你需要关注一下 local 字段，因为所有当前主要的空闲可用的元素都存放在 local 字段中，请求元素时也是优先从 local 字段中查找可用的元素。local 字段包含一个 poolLocalInternal 字段，并提供 CPU 缓存对齐，从而避免 false sharing。而 poolLocalInternal 也包含两个字段：private 和 shared。

private，代表一个缓存的元素，而且只能由相应的一个 P 存取。因为一个 P 同时只能执行一个 goroutine，所以不会有并发的问题。shared，可以由任意的 P 访问，但是只有本地的 P 才能 pushHead/popHead，其它 P 可以 popTail，相当于只有一个本地的 P 作为生产者（Producer），多个 P 作为消费者（Consumer），它是使用一个 local-free 的 queue 列表实现的。

### Get 方法

我们来看看 Get 方法的具体实现原理。

```go
func (p *Pool) Get() interface{} {
    // 把当前goroutine固定在当前的P上
    l, pid := p.pin()
    x := l.private // 优先从local的private字段取，快速
    l.private = nil
    if x == nil {
        // 从当前的local.shared弹出一个，注意是从head读取并移除
        x, _ = l.shared.popHead()
        if x == nil { // 如果没有，则去偷一个
            x = p.getSlow(pid) 
        }
    }
    runtime_procUnpin()
    // 如果没有获取到，尝试使用New函数生成一个新的
    if x == nil && p.New != nil {
        x = p.New()
    }
    return x
}
```
我来给你解释下这段代码。首先，从本地的 private 字段中获取可用元素，因为没有锁，获取元素的过程会非常快，如果没有获取到，就尝试从本地的 shared 获取一个，如果还没有，会使用 getSlow 方法去其它的 shared 中“偷”一个。最后，如果没有获取到，就尝试使用 New 函数创建一个新的。这里的重点是 getSlow 方法，我们来分析下。看名字也就知道了，它的耗时可能比较长。它首先要遍历所有的 local，尝试从它们的 shared 弹出一个元素。如果还没找到一个，那么，就开始对 victim 下手了。在 vintim 中查询可用元素的逻辑还是一样的，先从对应的 victim 的 private 查找，如果查不到，就再从其它 victim 的 shared 中查找。下面的代码是 getSlow 方法的主要逻辑：

```go
func (p *Pool) getSlow(pid int) interface{} {

    size := atomic.LoadUintptr(&p.localSize)
    locals := p.local                       
    // 从其它proc中尝试偷取一个元素
    for i := 0; i < int(size); i++ {
        l := indexLocal(locals, (pid+i+1)%int(size))
        if x, _ := l.shared.popTail(); x != nil {
            return x
        }
    }

    // 如果其它proc也没有可用元素，那么尝试从vintim中获取
    size = atomic.LoadUintptr(&p.victimSize)
    if uintptr(pid) >= size {
        return nil
    }
    locals = p.victim
    l := indexLocal(locals, pid)
    if x := l.private; x != nil { // 同样的逻辑，先从vintim中的local private获取
        l.private = nil
        return x
    }
    for i := 0; i < int(size); i++ { // 从vintim其它proc尝试偷取
        l := indexLocal(locals, (pid+i)%int(size))
        if x, _ := l.shared.popTail(); x != nil {
            return x
        }
    }

    // 如果victim中都没有，则把这个victim标记为空，以后的查找可以快速跳过了
    atomic.StoreUintptr(&p.victimSize, 0)

    return nil
}
```
这里我没列出 pin 代码的实现，你只需要知道，pin 方法会将此 goroutine 固定在当前的 P 上，避免查找元素期间被其它的 P 执行。固定的好处就是查找元素期间直接得到跟这个 P 相关的 local。有一点需要注意的是，pin 方法在执行的时候，如果跟这个 P 相关的 local 还没有创建，或者运行时 P 的数量被修改了的话，就会新创建 local。

### Put 方法

我们来看看 Put 方法的具体实现原理。

```go
func (p *Pool) Put(x interface{}) {
    if x == nil { // nil值直接丢弃
        return
    }
    l, _ := p.pin()
    if l.private == nil { // 如果本地private没有值，直接设置这个值即可
        l.private = x
        x = nil
    }
    if x != nil { // 否则加入到本地队列中
        l.shared.pushHead(x)
    }
    runtime_procUnpin()
}
```
put 的逻辑相对简单，优先设置本地 private，如果 private 字段已经有值了，那么就把此元素 push 到本地队列中。

### sync.Pool 的坑

到这里，我们就掌握了 sync.Pool 的使用方法和实现原理，接下来，我要再和你聊聊容易踩的两个坑，分别是内存泄漏和内存浪费。



#### 内存泄漏

这节课刚开始的时候，我讲到，可以使用 sync.Pool 做 buffer 池，但是，如果用刚刚的那种方式做 buffer 池的话，可能会有内存泄漏的风险。为啥这么说呢？我们来分析一下。取出来的 bytes.Buffer 在使用的时候，我们可以往这个元素中增加大量的 byte 数据，这会导致底层的 byte slice 的容量可能会变得很大。这个时候，即使 Reset 再放回到池子中，这些 byte slice 的容量不会改变，所占的空间依然很大。而且，因为 Pool 回收的机制，这些大的 Buffer 可能不被回收，而是会一直占用很大的空间，这属于内存泄漏的问题。即使是 Go 的标准库，在内存泄漏这个问题上也栽了几次坑，比如 issue 23199、@dsnet提供了一个简单的可重现的例子，演示了内存泄漏的问题。再比如 encoding、json 中类似的问题：将容量已经变得很大的 Buffer 再放回 Pool 中，导致内存泄漏。后来在元素放回时，增加了检查逻辑，改成放回的超过一定大小的 buffer，就直接丢弃掉，不再放到池子中，如下所示：

![img](https://static001.geekbang.org/resource/image/e3/9f/e3e23d2f2ab55b64741e14856a58389f.png?wh=1098*319)

package fmt 中也有这个问题，修改方法是一样的，超过一定大小的 buffer，就直接丢弃了：

![img](https://static001.geekbang.org/resource/image/06/62/06c68476cac13a860c470b006718c462.png?wh=1012*392)

在使用 sync.Pool 回收 buffer 的时候，一定要检查回收的对象的大小。如果 buffer 太大，就不要回收了，否则就太浪费了。

#### 内存浪费

除了内存泄漏以外，还有一种浪费的情况，就是池子中的 buffer 都比较大，但在实际使用的时候，很多时候只需要一个小的 buffer，这也是一种浪费现象。接下来，我就讲解一下这种情况的处理方法。要做到物尽其用，尽可能不浪费的话，我们可以将 buffer 池分成几层。首先，小于 512 byte 的元素的 buffer 占一个池子；其次，小于 1K byte 大小的元素占一个池子；再次，小于 4K byte 大小的元素占一个池子。这样分成几个池子以后，就可以根据需要，到所需大小的池子中获取 buffer 了。在标准库 net/http/server.go中的代码中，就提供了 2K 和 4K 两个 writer 的池子。你可以看看下面这段代码：

![img](https://static001.geekbang.org/resource/image/55/35/55086ccba91975a0f65bd35d1192e335.png?wh=628*611)

YouTube 开源的知名项目 vitess 中提供了[bucketpool](https://github.com/vitessio/vitess/blob/master/go/bucketpool/bucketpool.go)的实现，它提供了更加通用的多层 buffer 池。你在使用的时候，只需要指定池子的最大和最小尺寸，vitess 就会自动计算出合适的池子数。而且，当你调用 Get 方法的时候，只需要传入你要获取的 buffer 的大小，就可以了。下面这段代码就描述了这个过程，你可以看看：

![img](https://static001.geekbang.org/resource/image/c5/08/c5cd474aa53fe57e0722d840a6c7f308.png?wh=591*146)

### 第三方库

除了这种分层的为了节省空间的 buffer 设计外，还有其它的一些第三方的库也会提供 buffer 池的功能。接下来我带你熟悉几个常用的第三方的库。

1.bytebufferpool

这是 fasthttp 作者 valyala 提供的一个 buffer 池，基本功能和 sync.Pool 相同。它的底层也是使用 sync.Pool 实现的，包括会检测最大的 buffer，超过最大尺寸的 buffer，就会被丢弃。valyala 一向很擅长挖掘系统的性能，这个库也不例外。它提供了校准（calibrate，用来动态调整创建元素的权重）的机制，可以“智能”地调整 Pool 的 defaultSize 和 maxSize。一般来说，我们使用 buffer size 的场景比较固定，所用 buffer 的大小会集中在某个范围里。有了校准的特性，bytebufferpool 就能够偏重于创建这个范围大小的 buffer，从而节省空间。

2.oxtoacart/bpool

这也是比较常用的 buffer 池，它提供了以下几种类型的 buffer。bpool.BufferPool： 提供一个固定元素数量的 buffer 池，元素类型是 bytes.Buffer，如果超过这个数量，Put 的时候就丢弃，如果池中的元素都被取光了，会新建一个返回。Put 回去的时候，不会检测 buffer 的大小。bpool.BytesPool：提供一个固定元素数量的 byte slice 池，元素类型是 byte slice。Put 回去的时候不检测 slice 的大小。bpool.SizedBufferPool： 提供一个固定元素数量的 buffer 池，如果超过这个数量，Put 的时候就丢弃，如果池中的元素都被取光了，会新建一个返回。Put 回去的时候，会检测 buffer 的大小，超过指定的大小的话，就会创建一个新的满足条件的 buffer 放回去。

bpool 最大的特色就是能够保持池子中元素的数量，一旦 Put 的数量多于它的阈值，就会自动丢弃，而 sync.Pool 是一个没有限制的池子，只要 Put 就会收进去。bpool 是基于 Channel 实现的，不像 sync.Pool 为了提高性能而做了很多优化，所以，在性能上比不过 sync.Pool。不过，它提供了限制 Pool 容量的功能，所以，如果你想控制 Pool 的容量的话，可以考虑这个库。

### 连接池

Pool 的另一个很常用的一个场景就是保持 TCP 的连接。一个 TCP 的连接创建，需要三次握手等过程，如果是 TLS 的，还会需要更多的步骤，如果加上身份认证等逻辑的话，耗时会更长。所以，为了避免每次通讯的时候都新创建连接，我们一般会建立一个连接的池子，预先把连接创建好，或者是逐步把连接放在池子中，减少连接创建的耗时，从而提高系统的性能。事实上，我们很少会使用 sync.Pool 去池化连接对象，原因就在于，sync.Pool 会无通知地在某个时候就把连接移除垃圾回收掉了，而我们的场景是需要长久保持这个连接，所以，我们一般会使用其它方法来池化连接，比如接下来我要讲到的几种需要保持长连接的 Pool。

### 标准库中的 http client 池

标准库的 http.Client 是一个 http client 的库，可以用它来访问 web 服务器。为了提高性能，这个 Client 的实现也是通过池的方法来缓存一定数量的连接，以便后续重用这些连接。http.Client 实现连接池的代码是在 Transport 类型中，它使用 idleConn 保存持久化的可重用的长连接：

![img](https://static001.geekbang.org/resource/image/14/ec/141ced98a81466b793b0f90b9652afec.png?wh=1192*430)

### TCP 连接池

最常用的一个 TCP 连接池是 fatih 开发的[fatih/pool](fatih/pool)，虽然这个项目已经被 fatih 归档（Archived），不再维护了，但是因为它相当稳定了，我们可以开箱即用。即使你有一些特殊的需求，也可以 fork 它，然后自己再做修改。它的使用套路如下：

```
// 工厂模式，提供创建连接的工厂方法
factory    := func() (net.Conn, error) { return net.Dial("tcp", "127.0.0.1:4000") }

// 创建一个tcp池，提供初始容量和最大容量以及工厂方法
p, err := pool.NewChannelPool(5, 30, factory)

// 获取一个连接
conn, err := p.Get()

// Close并不会真正关闭这个连接，而是把它放回池子，所以你不必显式地Put这个对象到池子中
conn.Close()

// 通过调用MarkUnusable, Close的时候就会真正关闭底层的tcp的连接了
if pc, ok := conn.(*pool.PoolConn); ok {
  pc.MarkUnusable()
  pc.Close()
}

// 关闭池子就会关闭=池子中的所有的tcp连接
p.Close()

// 当前池子中的连接的数量
current := p.Len()
```
虽然我一直在说 TCP，但是它管理的是更通用的 net.Conn，不局限于 TCP 连接。它通过把 net.Conn 包装成 PoolConn，实现了拦截 net.Conn 的 Close 方法，避免了真正地关闭底层连接，而是把这个连接放回到池中：

```
    type PoolConn struct {
    net.Conn
    mu       sync.RWMutex
    c        *channelPool
    unusable bool
  }
  
    //拦截Close
  func (p *PoolConn) Close() error {
    p.mu.RLock()
    defer p.mu.RUnlock()
  
    if p.unusable {
      if p.Conn != nil {
        return p.Conn.Close()
      }
      return nil
    }
    return p.c.put(p.Conn)
  }
```
它的 Pool 是通过 Channel 实现的，空闲的连接放入到 Channel 中，这也是 Channel 的一个应用场景：

```
    type channelPool struct {
    // 存储连接池的channel
    mu    sync.RWMutex
    conns chan net.Conn
  

    // net.Conn 的产生器
    factory Factory
  }
```
### 数据库连接池

标准库 sql.DB 还提供了一个通用的数据库的连接池，通过 MaxOpenConns 和 MaxIdleConns 控制最大的连接数和最大的 idle 的连接数。默认的 MaxIdleConns 是 2，这个数对于数据库相关的应用来说太小了，我们一般都会调整它。

![img](https://static001.geekbang.org/resource/image/49/15/49c14b5bccb6d6ac7a159eece17a2215.png?wh=552*106)



DB 的 freeConn 保存了 idle 的连接，这样，当我们获取数据库连接的时候，它就会优先尝试从 freeConn 获取已有的连接（[conn](https://github.com/golang/go/blob/4fc3896e7933e31822caa50e024d4e139befc75f/src/database/sql/sql.go#L1196)）。

![img](https://static001.geekbang.org/resource/image/d0/b5/d043yyd649a216fe37885yy4e03af3b5.png?wh=789*345)

### Memcached Client 连接池

Brad Fitzpatrick 是知名缓存库 Memcached 的原作者，前 Go 团队成员。gomemcache是他使用 Go 开发的 Memchaced 的客户端，其中也用了连接池的方式池化 Memcached 的连接。接下来让我们看看它的连接池的实现。gomemcache Client 有一个 freeconn 的字段，用来保存空闲的连接。当一个请求使用完之后，它会调用 putFreeConn 放回到池子中，请求的时候，调用 getFreeConn 优先查询 freeConn 中是否有可用的连接。它采用 Mutex+Slice 实现 Pool：

```
   // 放回一个待重用的连接
   func (c *Client) putFreeConn(addr net.Addr, cn *conn) {
    c.lk.Lock()
    defer c.lk.Unlock()
    if c.freeconn == nil { // 如果对象为空，创建一个map对象
      c.freeconn = make(map[string][]*conn)
    }
    freelist := c.freeconn[addr.String()] //得到此地址的连接列表
    if len(freelist) >= c.maxIdleConns() {//如果连接已满,关闭，不再放入
      cn.nc.Close()
      return
    }
    c.freeconn[addr.String()] = append(freelist, cn) // 加入到空闲列表中
  }
  
    // 得到一个空闲连接
  func (c *Client) getFreeConn(addr net.Addr) (cn *conn, ok bool) {
    c.lk.Lock()
    defer c.lk.Unlock()
    if c.freeconn == nil { 
      return nil, false
    }
    freelist, ok := c.freeconn[addr.String()]
    if !ok || len(freelist) == 0 { // 没有此地址的空闲列表，或者列表为空
      return nil, false
    }
    cn = freelist[len(freelist)-1] // 取出尾部的空闲连接
    c.freeconn[addr.String()] = freelist[:len(freelist)-1]
    return cn, true
  }

```


### Worker Pool

最后，我再讲一个 Pool 应用得非常广泛的场景。你已经知道，goroutine 是一个很轻量级的“纤程”，在一个服务器上可以创建十几万甚至几十万的 goroutine。但是“可以”和“合适”之间还是有区别的，你会在应用中让几十万的 goroutine 一直跑吗？基本上是不会的。一个 goroutine 初始的栈大小是 2048 个字节，并且在需要的时候可以扩展到 1GB（具体的内容你可以课下看看代码中的配置：[不同的架构最大数会不同](https://github.com/golang/go/blob/f296b7a6f045325a230f77e9bda1470b1270f817/src/runtime/proc.go#L120)），所以，大量的 goroutine 还是很耗资源的。同时，大量的 goroutine 对于调度和垃圾回收的耗时还是会有影响的，因此，goroutine 并不是越多越好。

有的时候，我们就会创建一个 Worker Pool 来减少 goroutine 的使用。比如，我们实现一个 TCP 服务器，如果每一个连接都要由一个独立的 goroutine 去处理的话，在大量连接的情况下，就会创建大量的 goroutine，这个时候，我们就可以创建一个固定数量的 goroutine（Worker），由这一组 Worker 去处理连接，比如 fasthttp 中的[Worker Pool](https://github.com/valyala/fasthttp/blob/9f11af296864153ee45341d3f2fe0f5178fd6210/workerpool.go#L16)。

Worker 的实现也是五花八门的：有些是在后台默默执行的，不需要等待返回结果；有些需要等待一批任务执行完；有些 Worker Pool 的生命周期和程序一样长；有些只是临时使用，执行完毕后，Pool 就销毁了。

大部分的 Worker Pool 都是通过 Channel 来缓存任务的，因为 Channel 能够比较方便地实现并发的保护，有的是多个 Worker 共享同一个任务 Channel，有些是每个 Worker 都有一个独立的 Channel。综合下来，精挑细选，我给你推荐三款易用的 Worker Pool，这三个 Worker Pool 的 API 设计简单，也比较相似，易于和项目集成，而且提供的功能也是我们常用的功能。

gammazero/workerpool：gammazero/workerpool 可以无限制地提交任务，提供了更便利的 Submit 和 SubmitWait 方法提交任务，还可以提供当前的 worker 数和任务数以及关闭 Pool 的功能。ivpusic/grpool：grpool 创建 Pool 的时候需要提供 Worker 的数量和等待执行的任务的最大数量，任务的提交是直接往 Channel 放入任务。dpaks/goworkers：dpaks/goworkers 提供了更便利的 Submit 方法提交任务以及 Worker 数、任务数等查询方法、关闭 Pool 的方法。它的任务的执行结果需要在 ResultChan 和 ErrChan 中去获取，没有提供阻塞的方法，但是它可以在初始化的时候设置 Worker 的数量和任务数。

类似的 Worker Pool 的实现非常多，比如还有panjf2000/ants、Jeffail/tunny 、benmanns/goworker、go-playground/pool、Sherifabdlnaby/gpool等第三方库。pond也是一个非常不错的 Worker Pool，关注度目前不是很高，但是功能非常齐全。

其实，你也可以自己去开发自己的 Worker Pool，但是，对于我这种“懒惰”的人来说，只要满足我的实际需求，我还是倾向于从这个几个常用的库中选择一个来使用。所以，我建议你也从常用的库中进行选择。

### 总结

Pool 是一个通用的概念，也是解决对象重用和预先分配的一个常用的优化手段。即使你自己没在项目中直接使用过，但肯定在使用其它库的时候，就享受到应用 Pool 的好处了，比如数据库的访问、http API 的请求等等。我们一般不会在程序一开始的时候就开始考虑优化，而是等项目开发到一个阶段，或者快结束的时候，才全面地考虑程序中的优化点，而 Pool 就是常用的一个优化手段。如果你发现程序中有一种 GC 耗时特别高，有大量的相同类型的临时对象，不断地被创建销毁，这时，你就可以考虑看看，是不是可以通过池化的手段重用这些对象。另外，在分布式系统或者微服务框架中，可能会有大量的并发 Client 请求，如果 Client 的耗时占比很大，你也可以考虑池化 Client，以便重用。如果你发现系统中的 goroutine 数量非常多，程序的内存资源占用比较大，而且整体系统的耗时和 GC 也比较高，我建议你看看，是否能够通过 Worker Pool 解决大量 goroutine 的问题，从而降低这些指标。

![img](https://static001.geekbang.org/resource/image/58/aa/58358f16bcee0281b55299f0386e17aa.jpg?wh=2250*2404)



## 11 | Context：信息穿透上下文

在这节课正式开始之前，我想先带你看一个工作中的场景。假设有一天你进入办公室，突然同事们都围住你，然后大喊“小王小王你最帅”，此时你可能一头雾水，只能尴尬地笑笑。为啥呢？因为你缺少上下文的信息，不知道之前发生了什么。但是，如果同事告诉你，由于你业绩突出，一天之内就把云服务化的主要架构写好了，因此被评为 9 月份的工作之星，总经理还特意给你发 1 万元的奖金，那么，你心里就很清楚了，原来同事恭喜你，是因为你的工作被表扬了，还获得了奖金。同事告诉你的这些前因后果，就是上下文信息，他把上下文传递给你，你接收后，就可以获取之前不了解的信息。你看，上下文（Context）就是这么重要。在我们的开发场景中，上下文也是不可或缺的，缺少了它，我们就不能获取完整的程序信息。那到底啥是上下文呢？其实，这就是指，在 API 之间或者方法调用之间，所传递的除了业务参数之外的额外信息。比如，服务端接收到客户端的 HTTP 请求之后，可以把客户端的 IP 地址和端口、客户端的身份信息、请求接收的时间、Trace ID 等信息放入到上下文中，这个上下文可以在后端的方法调用中传递，后端的业务方法除了利用正常的参数做一些业务处理（如订单处理）之外，还可以从上下文读取到消息请求的时间、Trace  ID 等信息，把服务处理的时间推送到 Trace 服务中。Trace 服务可以把同一 Trace ID 的不同方法的调用顺序和调用时间展示成流程图，方便跟踪。不过，Go 标准库中的 Context 功能还不止于此，它还提供了超时（Timeout）和取消（Cancel）的机制，下面就让我一一道来。

### Context 的来历

在学习 Context 的功能之前呢，我先带你了解下它的来历。毕竟，知道了它的来龙去脉，我们才能应用得更加得心应手一些。Go 在 1.7 的版本中才正式把 Context 加入到标准库中。在这之前，很多 Web 框架在定义自己的 handler 时，都会传递一个自定义的 Context，把客户端的信息和客户端的请求信息放入到 Context 中。Go 最初提供了 golang.org/x/net/context 库用来提供上下文信息，最终还是在 Go1.7 中把此库提升到标准库 context 包中。为啥呢？这是因为，在 Go1.7 之前，有很多库都依赖 golang.org/x/net/context 中的 Context 实现，这就导致 Go 1.7 发布之后，出现了标准库 Context 和 golang.org/x/net/context 并存的状况。新的代码使用标准库 Context 的时候，没有办法使用这个标准库的 Context 去调用旧有的使用 x/net/context 实现的方法。所以，在 Go1.9 中，还专门实现了一个叫做 type alias 的新特性，然后把 x/net/context 中的 Context 定义成标准库 Context 的别名，以解决新旧 Context 类型冲突问题，你可以看一下下面这段代码：

```go
    // +build go1.9
  package context
  
  import "context"
  
  type Context = context.Context
  type CancelFunc = context.CancelFunc
```
go 标准库的 Context 不仅提供了上下文传递的信息，还提供了 cancel、timeout 等其它信息，这些信息貌似和 context 这个包名没关系，但是还是得到了广泛的应用。所以，你看，context 包中的 Context 不仅仅传递上下文信息，还有 timeout 等其它功能，是不是“名不副实”呢？其实啊，这也是这个 Context 的一个问题，比较容易误导人，Go 布道师 Dave Cheney 还专门写了一篇文章讲述这个问题：[Context isn’t for cancellation](https://dave.cheney.net/2017/08/20/context-isnt-for-cancellation)。

同时，也有一些批评者针对 Context 提出了批评：[Context should go away for Go 2](https://faiface.github.io/post/context-should-go-away-go2/)，这篇文章把 Context 比作病毒，病毒会传染，结果把所有的方法都传染上了病毒（加上 Context 参数），绝对是视觉污染。

Go 的开发者也注意到了“关于 Context，存在一些争议”这件事儿，所以，Go 核心开发者 Ian Lance Taylor 专门开了一个issue 28342，用来记录当前的 Context 的问题：

Context 包名导致使用的时候重复 ctx context.Context；Context.WithValue 可以接受任何类型的值，非类型安全；Context 包名容易误导人，实际上，Context 最主要的功能是取消 goroutine 的执行；Context 漫天飞，函数污染。

尽管有很多的争议，但是，在很多场景下，使用 Context 其实会很方便，所以现在它已经在 Go 生态圈中传播开来了，包括很多的 Web 应用框架，都切换成了标准库的 Context。标准库中的 database/sql、os/exec、net、net/http 等包中都使用到了 Context。而且，如果我们遇到了下面的一些场景，也可以考虑使用 Context：

**上下文信息传递 （request-scoped）**，比如处理 http 请求、在请求处理链路上传递信息；**控制子 goroutine 的运行；超时控制的方法调用；可以取消的方法调用**。

所以，我们需要掌握 Context 的具体用法，这样才能在不影响主要业务流程实现的时候，实现一些通用的信息传递，或者是能够和其它 goroutine 协同工作，提供 timeout、cancel 等机制。

### Context 基本使用方法

首先，我们来学习一下 Context 接口包含哪些方法，这些方法都是干什么用的。包 context 定义了 Context 接口，Context 的具体实现包括 4 个方法，分别是 Deadline、Done、Err 和 Value，如下所示：

```
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```
下面我来具体解释下这 4 个方法。

Deadline 方法会返回这个 Context 被取消的截止日期。如果没有设置截止日期，ok 的值是 false。后续每次调用这个对象的 Deadline 方法时，都会返回和第一次调用相同的结果。

Done 方法返回一个 Channel 对象。在 Context 被取消时，此 Channel 会被 close，如果没被取消，可能会返回 nil。后续的 Done 调用总是返回相同的结果。当 Done 被 close 的时候，你可以通过 ctx.Err 获取错误信息。Done 这个方法名其实起得并不好，因为名字太过笼统，不能明确反映 Done 被 close 的原因，因为 cancel、timeout、deadline 都可能导致 Done 被 close，不过，目前还没有一个更合适的方法名称。关于 Done 方法，你必须要记住的知识点就是：如果 Done 没有被 close，Err 方法返回 nil；如果 Done 被 close，Err 方法会返回 Done 被 close 的原因。

Value 返回此 ctx 中和指定的 key 相关联的 value。**Context 中实现了 2 个常用的生成顶层 Context 的方法。**

context.Background()：返回一个非 nil 的、空的 Context，没有任何值，不会被 cancel，不会超时，没有截止日期。一般用在主函数、初始化、测试以及创建根 Context 的时候。

context.TODO()：返回一个非 nil 的、空的 Context，没有任何值，不会被 cancel，不会超时，没有截止日期。当你不清楚是否该用 Context，或者目前还不知道要传递一些什么上下文信息的时候，就可以使用这个方法。

官方文档是这么讲的，你可能会觉得像没说一样，因为界限并不是很明显。其实，你根本不用费脑子去考虑，可以直接使用 context.Background。事实上，它们两个底层的实现是一模一样的：

```go
var (
    background = new(emptyCtx)
    todo       = new(emptyCtx)
)

func Background() Context {
    return background
}

func TODO() Context {
    return todo
}
```
在使用 Context 的时候，有一些约定俗成的规则。

一般函数使用 Context 的时候，会把这个参数放在第一个参数的位置。从来不把 nil 当做 Context 类型的参数值，可以使用 context.Background() 创建一个空的上下文对象，也不要使用 nil。Context 只用来临时做函数之间的上下文透传，不能持久化 Context 或者把 Context 长久保存。把 Context 持久化到数据库、本地文件或者全局变量、缓存中都是错误的用法。key 的类型不应该是字符串类型或者其它内建类型，否则容易在包之间使用 Context 时候产生冲突。**使用 WithValue 时，key 的类型应该是自己定义的类型**。常常使用 struct{}作为底层类型定义 key 的类型。对于 exported key 的静态类型，常常是接口或者指针。这样可以尽量减少内存分配。

其实官方的文档也是比较搞笑的，文档中强调 key 的类型不要使用 string，结果接下来的例子中就是用 string 类型作为 key 的类型。你自己把握住这个要点就好，如果你能保证别人使用你的 Context 时不会和你定义的 key 冲突，那么 key 的类型就比较随意，因为你自己保证了不同包的 key 不会冲突，否则建议你尽量采用保守的 unexported 的类型。

### 创建特殊用途 Context 的方法

接下来，我会介绍标准库中几种创建特殊用途 Context 的方法：WithValue、WithCancel、WithTimeout 和 WithDeadline，包括它们的功能以及实现方式。

#### WithValue

WithValue 基于 parent Context 生成一个新的 Context，保存了一个 key-value 键值对。它常常用来传递上下文。WithValue 方法其实是创建了一个类型为 valueCtx 的 Context，它的类型定义如下：

```go
type valueCtx struct {
    Context
    key, val interface{}
}
```
它持有一个 key-value 键值对，还持有 parent 的 Context。它覆盖了 Value 方法，优先从自己的存储中检查这个 key，不存在的话会从 parent 中继续检查。Go 标准库实现的 Context 还实现了链式查找。如果不存在，还会向 parent Context 去查找，如果 parent 还是 valueCtx 的话，还是遵循相同的原则：valueCtx 会嵌入 parent，所以还是会查找 parent 的 Value 方法的。

```
ctx = context.TODO()
ctx = context.WithValue(ctx, "key1", "0001")
ctx = context.WithValue(ctx, "key2", "0001")
ctx = context.WithValue(ctx, "key3", "0001")
ctx = context.WithValue(ctx, "key4", "0004")

fmt.Println(ctx.Value("key1"))
```
![img](https://static001.geekbang.org/resource/image/03/fe/035a1b8e090184c1feba1ef194ec53fe.jpg?wh=4000*557)



### WithCancel

WithCancel 方法返回 parent 的副本，只是副本中的 Done Channel 是新建的对象，它的类型是 cancelCtx。**我们常常在一些需要主动取消长时间的任务时，创建这种类型的 Context，然后把这个 Context 传给长时间执行任务的 goroutine。当需要中止任务时，我们就可以 cancel 这个 Context，这样长时间执行任务的 goroutine，就可以通过检查这个 Context，知道 Context 已经被取消了**。WithCancel 返回值中的第二个值是一个 cancel 函数。其实，这个返回值的名称（cancel）和类型（Cancel）也非常迷惑人。记住，**不是只有你想中途放弃，才去调用 cancel，只要你的任务正常完成了，就需要调用 cancel，这样，这个 Context 才能释放它的资源（通知它的 children 处理 cancel，从它的 parent 中把自己移除，甚至释放相关的 goroutine）**。很多同学在使用这个方法的时候，都会忘记调用 cancel，切记切记，而且一定尽早释放。我们来看下 WithCancel 方法的实现代码：

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc) {
    c := newCancelCtx(parent)
    propagateCancel(parent, &c)// 把c朝上传播
    return &c, func() { c.cancel(true, Canceled) }
}

// newCancelCtx returns an initialized cancelCtx.
func newCancelCtx(parent Context) cancelCtx {
    return cancelCtx{Context: parent}
}
```
代码中调用的 propagateCancel 方法会顺着 parent 路径往上找，直到找到一个 cancelCtx，或者为 nil。如果不为空，就把自己加入到这个 cancelCtx 的 child，以便这个 cancelCtx 被取消的时候通知自己。如果为空，会新起一个 goroutine，由它来监听 parent 的 Done 是否已关闭。当这个 cancelCtx 的 cancel 函数被调用的时候，或者 parent 的 Done 被 close 的时候，这个 cancelCtx 的 Done 才会被 close。cancel 是向下传递的，如果一个 WithCancel 生成的 Context 被 cancel 时，如果它的子 Context（也有可能是孙，或者更低，依赖子的类型）也是 cancelCtx 类型的，就会被 cancel，但是不会向上传递。parent Context 不会因为子 Context 被 cancel 而 cancel。cancelCtx 被取消时，它的 Err 字段就是下面这个 Canceled 错误：

```
var Canceled = errors.New("context canceled")
```
#### WithTimeout

WithTimeout 其实是和 WithDeadline 一样，只不过一个参数是超时时间，一个参数是截止时间。超时时间加上当前时间，其实就是截止时间，因此，WithTimeout 的实现是：

```go
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc) {
    // 当前时间+timeout就是deadline
    return WithDeadline(parent, time.Now().Add(timeout))
}
```
#### WithDeadline

WithDeadline 会返回一个 parent 的副本，并且设置了一个不晚于参数 d 的截止时间，类型为 timerCtx（或者是 cancelCtx）。如果它的截止时间晚于 parent 的截止时间，那么就以 parent 的截止时间为准，并返回一个类型为 cancelCtx 的 Context，因为 parent 的截止时间到了，就会取消这个 cancelCtx。如果当前时间已经超过了截止时间，就直接返回一个已经被 cancel 的 timerCtx。否则就会启动一个定时器，到截止时间取消这个 timerCtx。综合起来，timerCtx 的 Done 被 Close 掉，主要是由下面的某个事件触发的：

截止时间到了；cancel 函数被调用；parent 的 Done 被 close。

下面的代码是 WithDeadline 方法的实现：

```go
func WithDeadline(parent Context, d time.Time) (Context, CancelFunc) {
    // 如果parent的截止时间更早，直接返回一个cancelCtx即可
    if cur, ok := parent.Deadline(); ok && cur.Before(d) {
        return WithCancel(parent)
    }
    c := &timerCtx{
        cancelCtx: newCancelCtx(parent),
        deadline:  d,
    }
    propagateCancel(parent, c) // 同cancelCtx的处理逻辑
    dur := time.Until(d)
    if dur <= 0 { //当前时间已经超过了截止时间，直接cancel
        c.cancel(true, DeadlineExceeded)
        return c, func() { c.cancel(false, Canceled) }
    }
    c.mu.Lock()
    defer c.mu.Unlock()
    if c.err == nil {
        // 设置一个定时器，到截止时间后取消
        c.timer = time.AfterFunc(dur, func() {
            c.cancel(true, DeadlineExceeded)
        })
    }
    return c, func() { c.cancel(true, Canceled) }
}
```
和 cancelCtx 一样，WithDeadline（WithTimeout）返回的 cancel 一定要调用，并且要尽可能早地被调用，这样才能尽早释放资源，不要单纯地依赖截止时间被动取消。正确的使用姿势是啥呢？我们来看一个例子。

```go
func slowOperationWithTimeout(ctx context.Context) (Result, error) {
  ctx, cancel := context.WithTimeout(ctx, 100*time.Millisecond)
  defer cancel() // 一旦慢操作完成就立马调用cancel
  return slowOperation(ctx)
}
```


### 总结

我们经常使用 Context 来取消一个 goroutine 的运行，这是 Context 最常用的场景之一，Context 也被称为 goroutine 生命周期范围（goroutine-scoped）的 Context，把 Context 传递给 goroutine。但是，goroutine 需要尝试检查 Context 的 Done 是否关闭了：

```go
func main() {
    ctx, cancel := context.WithCancel(context.Background())

    go func() {
        defer func() {
            fmt.Println("goroutine exit")
        }()

        for {
            select {
            case <-ctx.Done():
                return
            default:
                time.Sleep(time.Second)
            }
        }
    }()

    time.Sleep(time.Second)
    cancel()
    time.Sleep(2 * time.Second)
}
```
如果你要为 Context 实现一个带超时功能的调用，比如访问远程的一个微服务，超时并不意味着你会通知远程微服务已经取消了这次调用，大概率的实现只是避免客户端的长时间等待，远程的服务器依然还执行着你的请求。所以，有时候，Context 并不会减少对服务器的请求负担。如果在 Context 被 cancel 的时候，你能关闭和服务器的连接，中断和数据库服务器的通讯、停止对本地文件的读写，那么，这样的超时处理，同时能减少对服务调用的压力，但是这依赖于你对超时的底层处理机制。

![img](https://static001.geekbang.org/resource/image/2d/2b/2dcbb1ca54c31b4f3e987b602a38e82b.jpg?wh=2250*2441)


## 原子操作

## 12 | atomic：要保证原子操作，一定要使用这几种方法

前面我们在学习 Mutex、RWMutex 等并发原语的实现时，你可以看到，最底层是通过 atomic 包中的一些原子操作来实现的。当时，为了让你的注意力集中在这些原语的功能实现上，我并没有展开介绍这些原子操作是干什么用的。你可能会说，这些并发原语已经可以应对大多数的并发场景了，为啥还要学习原子操作呢？其实，这是因为，在很多场景中，使用并发原语实现起来比较复杂，而原子操作可以帮助我们更轻松地实现底层的优化。所以，现在，我会专门用一节课，带你仔细地了解一下什么是原子操作，atomic 包都提供了哪些实现原子操作的方法。另外，我还会带你实现一个基于原子操作的数据结构。好了，接下来我们先来学习下什么是原子操作。



### 原子操作的基础知识

Package sync/atomic 实现了同步算法底层的原子的内存操作原语，我们把它叫做原子操作原语，它提供了一些实现原子操作的方法。之所以叫原子操作，是因为一个原子在执行的时候，其它线程不会看到执行一半的操作结果。在其它线程看来，原子操作要么执行完了，要么还没有执行，就像一个最小的粒子 - 原子一样，不可分割。CPU 提供了基础的原子操作，不过，不同架构的系统的原子操作是不一样的。对于单处理器单核系统来说，如果一个操作是由一个 CPU 指令来实现的，那么它就是原子操作，比如它的 XCHG 和 INC 等指令。如果操作是基于多条指令来实现的，那么，执行的过程中可能会被中断，并执行上下文切换，这样的话，原子性的保证就被打破了，因为这个时候，操作可能只执行了一半。在多处理器多核系统中，原子操作的实现就比较复杂了。

由于 cache 的存在，单个核上的单个指令进行原子操作的时候，你要确保其它处理器或者核不访问此原子操作的地址，或者是确保其它处理器或者核总是访问原子操作之后的最新的值。x86 架构中提供了指令前缀 LOCK，LOCK 保证了指令（比如 LOCK CMPXCHG op1、op2）不会受其它处理器或 CPU 核的影响，有些指令（比如 XCHG）本身就提供 Lock 的机制。不同的 CPU 架构提供的原子操作指令的方式也是不同的，比如对于多核的 MIPS 和 ARM，提供了 LL/SC（Load Link/Store Conditional）指令，可以帮助实现原子操作（ARMLL/SC 指令 LDREX 和 STREX）。

因为不同的 CPU 架构甚至不同的版本提供的原子操作的指令是不同的，所以，要用一种编程语言实现支持不同架构的原子操作是相当有难度的。不过，还好这些都不需要你操心，因为 Go 提供了一个通用的原子操作的 API，将更底层的不同的架构下的实现封装成 atomic 包，提供了修改类型的原子操作（[atomic read-modify-write](https://preshing.com/20150402/you-can-do-any-kind-of-atomic-read-modify-write-operation/)，RMW）和加载存储类型的原子操作（[Load 和 Store](https://preshing.com/20130618/atomic-vs-non-atomic-operations/)）的 API，稍后我会一一介绍。有的代码也会因为架构的不同而不同。有时看起来貌似一个操作是原子操作，但实际上，对于不同的架构来说，情况是不一样的。比如下面的代码的第 4 行，是将一个 64 位的值赋值给变量 i：

```
const x int64 = 1 + 1<<33

func main() {
    var i = x
    _ = i
}
```
如果你使用 GOARCH=386 的架构去编译这段代码，那么，第 5 行其实是被拆成了两个指令，分别操作低 32 位和高 32 位（使用 GOARCH=386 go tool compile -N -l test.go；GOARCH=386 go tool objdump -gnu test.o 反编译试试）：

![img](https://static001.geekbang.org/resource/image/45/62/4563ac42f379d1500d191377db16a162.png?wh=1391*83)

如果 GOARCH=amd64 的架构去编译这段代码，那么，第 5 行其中的赋值操作其实是一条指令：

![img](https://static001.geekbang.org/resource/image/6e/66/6e20a0f44d95d78c1bca4303f1a32966.png?wh=1341*216)

所以，如果要想保证原子操作，切记一定要使用 atomic 提供的方法。好了，了解了什么是原子操作以及不同系统的不同原子操作，接下来，我来介绍下 atomic 原子操作的应用场景。

### atomic 原子操作的应用场景

开篇我说过，使用 atomic 的一些方法，我们可以实现更底层的一些优化。如果使用 Mutex 等并发原语进行这些优化，虽然可以解决问题，但是这些并发原语的实现逻辑比较复杂，对性能还是有一定的影响的。举个例子：假设你想在程序中使用一个标志（flag，比如一个 bool 类型的变量），来标识一个定时任务是否已经启动执行了，你会怎么做呢？我们先来看看加锁的方法。如果使用 Mutex 和 RWMutex，在读取和设置这个标志的时候加锁，是可以做到互斥的、保证同一时刻只有一个定时任务在执行的，所以使用 Mutex 或者 RWMutex 是一种解决方案。其实，这个场景中的问题不涉及到对资源复杂的竞争逻辑，只是会并发地读写这个标志，这类场景就适合使用 atomic 的原子操作。具体怎么做呢？你可以使用一个 uint32 类型的变量，如果这个变量的值是 0，就标识没有任务在执行，如果它的值是 1，就标识已经有任务在完成了。你看，是不是很简单呢？

再来看一个例子。假设你在开发应用程序的时候，需要从配置服务器中读取一个节点的配置信息。而且，在这个节点的配置发生变更的时候，你需要重新从配置服务器中拉取一份新的配置并更新。你的程序中可能有多个 goroutine 都依赖这份配置，涉及到对这个配置对象的并发读写，你可以使用读写锁实现对配置对象的保护。在大部分情况下，你也可以利用 atomic 实现配置对象的更新和加载。分析到这里，可以看到，这两个例子都可以使用基本并发原语来实现的，只不过，我们不需要这些基本并发原语里面的复杂逻辑，而是只需要其中的简单原子操作，所以，这些场景可以直接使用 atomic 包中的方法去实现。

**有时候，你也可以使用 atomic 实现自己定义的基本并发原语**，比如 Go issue 有人提议的 CondMutex、Mutex.LockContext、WaitGroup.Go 等，我们可以使用 atomic 或者基于它的更高一级的并发原语去实现。我先前讲的几种基本并发原语的底层（比如 Mutex），就是基于通过 atomic 的方法实现的。除此之外，atomic 原子操作还是实现 lock-free 数据结构的基石。

在实现 lock-free 的数据结构时，我们可以不使用互斥锁，这样就不会让线程因为等待互斥锁而阻塞休眠，而是让线程保持继续处理的状态。另外，不使用互斥锁的话，lock-free 的数据结构还可以提供并发的性能。不过，lock-free 的数据结构实现起来比较复杂，需要考虑的东西很多，有兴趣的同学可以看一位微软专家写的一篇经验分享：[Lockless Programming Considerations for Xbox 360 and Microsoft Windows](https://docs.microsoft.com/zh-cn/windows/win32/dxtecharts/lockless-programming)，这里我们不细谈了。不过，这节课的最后我会带你开发一个 lock-free 的 queue，来学习下使用 atomic 操作实现 lock-free 数据结构的方法，你可以拿它和使用互斥锁实现的 queue 做性能对比，看看在性能上是否有所提升。看到这里，你是不是觉得 atomic 非常重要呢？不过，要想能够灵活地应用 atomic，我们首先得知道 atomic 提供的所有方法。

### atomic 提供的方法

目前的 Go 的泛型的特性还没有发布，Go 的标准库中的很多实现会显得非常啰嗦，多个类型会实现很多类似的方法，尤其是 atomic 包，最为明显。相信泛型支持之后，atomic 的 API 会清爽很多。atomic 为了支持 int32、int64、uint32、uint64、uintptr、Pointer（Add 方法不支持）类型，分别提供了 AddXXX、CompareAndSwapXXX、SwapXXX、LoadXXX、StoreXXX 等方法。不过，你也不要担心，你只要记住了一种数据类型的方法的意义，其它数据类型的方法也是一样的。

关于 atomic，还有一个地方你一定要记住，**atomic 操作的对象是一个地址，你需要把可寻址的变量的地址作为参数传递给方法，而不是把变量的值传递给方法**。好了，下面我就来给你介绍一下 atomic 提供的方法。掌握了这些，你就可以说完全掌握了 atomic 包。

### Add

首先，我们来看 Add 方法的签名：

![img](https://static001.geekbang.org/resource/image/95/de/95dcf8742593b1191e87beaca16f59de.png?wh=811*133)

其实，Add 方法就是给第一个参数地址中的值增加一个 delta 值。对于有符号的整数来说，delta 可以是一个负数，相当于减去一个值。对于无符号的整数和 uinptr 类型来说，怎么实现减去一个值呢？毕竟，atomic 并没有提供单独的减法操作。我来跟你说一种方法。你可以利用计算机补码的规则，把减法变成加法。以 uint32 类型为例：

```
AddUint32(&x, ^uint32(c-1)).
```
如果是对 uint64 的值进行操作，那么，就把上面的代码中的 uint32 替换成 uint64。尤其是减 1 这种特殊的操作，我们可以简化为：

```
AddUint32(&x, ^uint32(0))
```
好了，我们再来看看 CAS 方法。

### CAS （CompareAndSwap）

以 int32 为例，我们学习一下 CAS 提供的功能。在 CAS 的方法签名中，需要提供要操作的地址、原数据值、新值，如下所示：

```go
func CompareAndSwapInt32(addr *int32, old, new int32) (swapped bool)
```
我们来看下这个方法的功能。这个方法会比较当前 addr 地址里的值是不是 old，如果不等于 old，就返回 false；如果等于 old，就把此地址的值替换成 new 值，返回 true。这就相当于“判断相等才替换”。如果使用伪代码来表示这个原子操作，代码如下：

```
if *addr == old {
  *addr = new
  return true
}
return false
```
它支持的类型和方法如图所示：

![img](https://static001.geekbang.org/resource/image/1b/77/1b0ffac37d8f952ca485ff58daf27177.png?wh=942*156)

### Swap

如果不需要比较旧值，只是比较粗暴地替换的话，就可以使用 Swap 方法，它替换后还可以返回旧值，伪代码如下：

```
old = *addr
*addr = new
return old
```
它支持的数据类型和方法如图所示：

![img](https://static001.geekbang.org/resource/image/c0/0a/c02e210607aa45734bb1812c97f77c0a.png?wh=844*160)

### Load

Load 方法会取出 addr 地址中的值，即使在多处理器、多核、有 CPU cache 的情况下，这个操作也能保证 Load 是一个原子操作。它支持的数据类型和方法如图所示：

![img](https://static001.geekbang.org/resource/image/3f/5d/3faba284bda2a666caa5727d0f0c275d.png?wh=586*149)

### Store

Store 方法会把一个值存入到指定的 addr 地址中，即使在多处理器、多核、有 CPU cache 的情况下，这个操作也能保证 Store 是一个原子操作。别的 goroutine 通过 Load 读取出来，不会看到存取了一半的值。它支持的数据类型和方法如图所示：

![img](https://static001.geekbang.org/resource/image/8b/a0/8b77dc0e1ede98394aa21cf10fecc9a0.png?wh=818*160)

### Value 类型

刚刚说的都是一些比较常见的类型，其实，atomic 还提供了一个特殊的类型：Value。它可以原子地存取对象类型，但也只能存取，不能 CAS 和 Swap，常常用在配置变更等场景中。

![img](https://static001.geekbang.org/resource/image/47/76/478b665391766de77043ffeb0d6fff76.png?wh=393*81)

接下来，我以一个配置变更的例子，来演示 Value 类型的使用。这里定义了一个 Value 类型的变量 config， 用来存储配置信息。首先，我们启动一个 goroutine，然后让它随机 sleep 一段时间，之后就变更一下配置，并通过我们前面学到的 Cond 并发原语，通知其它的 reader 去加载新的配置。接下来，我们启动一个 goroutine 等待配置变更的信号，一旦有变更，它就会加载最新的配置。通过这个例子，你可以了解到 Value 的 Store/Load 方法的使用，因为它只有这两个方法，只要掌握了它们的使用，你就完全掌握了 Value 类型。

```
type Config struct {
    NodeName string
    Addr     string
    Count    int32
}

func loadNewConfig() Config {
    return Config{
        NodeName: "北京",
        Addr:     "10.77.95.27",
        Count:    rand.Int31(),
    }
}
func main() {
    var config atomic.Value
    config.Store(loadNewConfig())
    var cond = sync.NewCond(&sync.Mutex{})

    // 设置新的config
    go func() {
        for {
            time.Sleep(time.Duration(5+rand.Int63n(5)) * time.Second)
            config.Store(loadNewConfig())
            cond.Broadcast() // 通知等待着配置已变更
        }
    }()

    go func() {
        for {
            cond.L.Lock()
            cond.Wait()                 // 等待变更信号
            c := config.Load().(Config) // 读取新的配置
            fmt.Printf("new config: %+v\n", c)
            cond.L.Unlock()
        }
    }()

    select {}
}
```
好了，关于标准库的 atomic 提供的方法，到这里我们就学完了。事实上，atomic 包提供了非常好的支持各种平台的一致性的 API，绝大部分项目都是直接使用它。接下来，我再给你介绍一下第三方库，帮助你稍微开拓一下思维。



### 第三方库的扩展

其实，atomic 的 API 已经算是很简单的了，它提供了包一级的函数，可以对几种类型的数据执行原子操作。不过有一点让人觉得不爽的是，或者是让熟悉面向对象编程的程序员不爽的是，函数调用有一点点麻烦。所以，有些人就对这些函数做了进一步的包装，跟 atomic 中的 Value 类型类似，这些类型也提供了面向对象的使用方式，比如关注度比较高的[uber-go/atomic](https://github.com/uber-go/atomic)，它定义和封装了几种与常见类型相对应的原子操作类型，这些类型提供了原子操作的方法。这些类型包括 Bool、Duration、Error、Float64、Int32、Int64、String、Uint32、Uint64 等。比如 Bool 类型，提供了 CAS、Store、Swap、Toggle 等原子方法，还提供 String、MarshalJSON、UnmarshalJSON 等辅助方法，确实是一个精心设计的 atomic 扩展库。关于这些方法，你一看名字就能猜出来它们的功能，我就不多说了。其它的数据类型也和 Bool 类型相似，使用起来就像面向对象的编程一样，你可以看下下面的这段代码。

```
    var running atomic.Bool
    running.Store(true)
    running.Toggle()
    fmt.Println(running.Load()) // false
```
### 使用 atomic 实现 Lock-Free queue

atomic 常常用来实现 Lock-Free 的数据结构，这次我会给你展示一个 Lock-Free queue 的实现。Lock-Free queue 最出名的就是 Maged M. Michael 和 Michael L. Scott 1996 年发表的论文中的算法，算法比较简单，容易实现，伪代码的每一行都提供了注释，我就不在这里贴出伪代码了，因为我们使用 Go 实现这个数据结构的代码几乎和伪代码一样：

```go
package queue
import (
  "sync/atomic"
  "unsafe"
)
// lock-free的queue
type LKQueue struct {
  head unsafe.Pointer
  tail unsafe.Pointer
}
// 通过链表实现，这个数据结构代表链表中的节点
type node struct {
  value interface{}
  next  unsafe.Pointer
}
func NewLKQueue() *LKQueue {
  n := unsafe.Pointer(&node{})
  return &LKQueue{head: n, tail: n}
}
// 入队
func (q *LKQueue) Enqueue(v interface{}) {
  n := &node{value: v}
  for {
    tail := load(&q.tail)
    next := load(&tail.next)
    if tail == load(&q.tail) { // 尾还是尾
      if next == nil { // 还没有新数据入队
        if cas(&tail.next, next, n) { //增加到队尾
          cas(&q.tail, tail, n) //入队成功，移动尾巴指针
          return
        }
      } else { // 已有新数据加到队列后面，需要移动尾指针
        cas(&q.tail, tail, next)
      }
    }
  }
}
// 出队，没有元素则返回nil
func (q *LKQueue) Dequeue() interface{} {
  for {
    head := load(&q.head)
    tail := load(&q.tail)
    next := load(&head.next)
    if head == load(&q.head) { // head还是那个head
      if head == tail { // head和tail一样
        if next == nil { // 说明是空队列
          return nil
        }
        // 只是尾指针还没有调整，尝试调整它指向下一个
        cas(&q.tail, tail, next)
      } else {
        // 读取出队的数据
        v := next.value
                // 既然要出队了，头指针移动到下一个
        if cas(&q.head, head, next) {
          return v // Dequeue is done.  return
        }
      }
    }
  }
}

// 将unsafe.Pointer原子加载转换成node
func load(p *unsafe.Pointer) (n *node) {
  return (*node)(atomic.LoadPointer(p))
}

// 封装CAS,避免直接将*node转换成unsafe.Pointer
func cas(p *unsafe.Pointer, old, new *node) (ok bool) {
  return atomic.CompareAndSwapPointer(
    p, unsafe.Pointer(old), unsafe.Pointer(new))
}
```


我来给你介绍下这里的主要逻辑。这个 lock-free 的实现使用了一个辅助头指针（head），头指针不包含有意义的数据，只是一个辅助的节点，这样的话，出队入队中的节点会更简单。入队的时候，通过 CAS 操作将一个元素添加到队尾，并且移动尾指针。出队的时候移除一个节点，并通过 CAS 操作移动 head 指针，同时在必要的时候移动尾指针。

### 总结

好了，我们来小结一下。这节课，我们学习了 atomic 的基本使用方法，以及它提供的几种方法，包括 Add、CAS、Swap、Load、Store、Value 类型。除此之外，我还介绍了一些第三方库，并且带你实现了 Lock-free queue。到这里，相信你已经掌握了 atomic 提供的各种方法，并且能够应用到实践中了。最后，我还想和你讨论一个额外的问题：对一个地址的赋值是原子操作吗？这是一个很有趣的问题，如果是原子操作，还要 atomic 包干什么？官方的文档中并没有特意的介绍，不过，在一些 issue 或者论坛中，每当有人谈到这个问题时，总是会被建议用 atomic 包。

[Dave Cheney](https://dave.cheney.net/2018/01/06/if-aligned-memory-writes-are-atomic-why-do-we-need-the-sync-atomic-package)就谈到过这个问题，讲得非常好。我来给你总结一下他讲的知识点，这样你就比较容易理解使用 atomic 和直接内存操作的区别了。在现在的系统中，write 的地址基本上都是对齐的（aligned）。 比如，32 位的操作系统、CPU 以及编译器，write 的地址总是 4 的倍数，64 位的系统总是 8 的倍数（还记得 WaitGroup 针对 64 位系统和 32 位系统对 state1 的字段不同的处理吗）。对齐地址的写，不会导致其他人看到只写了一半的数据，因为它通过一个指令就可以实现对地址的操作。如果地址不是对齐的话，那么，处理器就需要分成两个指令去处理，如果执行了一个指令，其它人就会看到更新了一半的错误的数据，这被称做撕裂写（torn write） 。所以，你可以认为赋值操作是一个原子操作，这个“原子操作”可以认为是保证数据的完整性。但是，对于现代的多处理多核的系统来说，由于 cache、指令重排，可见性等问题，我们对原子操作的意义有了更多的追求。在多核系统中，一个核对地址的值的更改，在更新到主内存中之前，是在多级缓存中存放的。这时，多个核看到的数据可能是不一样的，其它的核可能还没有看到更新的数据，还在使用旧的数据。多处理器多核心系统为了处理这类问题，使用了一种叫做内存屏障（memory fence 或 memory barrier）的方式。一个写内存屏障会告诉处理器，必须要等到它管道中的未完成的操作（特别是写操作）都被刷新到内存中，再进行操作。此操作还会让相关的处理器的 CPU 缓存失效，以便让它们从主存中拉取最新的值。

atomic 包提供的方法会提供内存屏障的功能，所以，atomic 不仅仅可以保证赋值的数据完整性，还能保证数据的可见性，一旦一个核更新了该地址的值，其它处理器总是能读取到它的最新值。但是，需要注意的是，因为需要处理器之间保证数据的一致性，atomic 的操作也是会降低性能的。

![img](https://static001.geekbang.org/resource/image/53/13/53d55255fe851754659d90cbee814f13.jpg?wh=2250*1693)



atomic.Value 只有 Load/Store 方法，你是不是感觉意犹未尽？你可以尝试为 Value 类型增加 Swap 和 CompareAndSwap 方法（可以参考一下这份[资料](https://github.com/golang/go/issues/39351)）。


## Channel

## 13 | Channel：另辟蹊径，解决并发问题

Channel 是 Go 语言内建的 first-class 类型，也是 Go 语言与众不同的特性之一。Go 语言的 Channel 设计精巧简单，以至于也有人用其它语言编写了类似 Go 风格的 Channel 库，比如docker/libchan、tylertreat/chan，但是并不像 Go 语言一样把 Channel 内置到了语言规范中。从这一点，你也可以看出来，Channel 的地位在编程语言中的地位之高，比较罕见。所以，这节课，我们就来学习下 Channel。



### Channel 的发展

要想了解 Channel 这种 Go 编程语言中的特有的数据结构，我们要追溯到 CSP 模型，学习一下它的历史，以及它对 Go 创始人设计 Channel 类型的影响。CSP 是 Communicating Sequential Process 的简称，中文直译为通信顺序进程，或者叫做交换信息的循序进程，是用来描述并发系统中进行交互的一种模式。CSP 最早出现于计算机科学家 Tony Hoare 在 1978 年发表的论文中（你可能不熟悉 Tony Hoare 这个名字，但是你一定很熟悉排序算法中的 Quicksort 算法，他就是 Quicksort 算法的作者，图灵奖的获得者）。最初，论文中提出的 CSP 版本在本质上不是一种进程演算，而是一种并发编程语言，但之后又经过了一系列的改进，最终发展并精炼出 CSP 的理论。**CSP 允许使用进程组件来描述系统，它们独立运行，并且只通过消息传递的方式通信。**

就像 Go 的创始人之一 Rob Pike 所说的：“每一个计算机程序员都应该读一读 Tony Hoare 1978 年的关于 CSP 的论文。”他和 Ken Thompson 在设计 Go 语言的时候也深受此论文的影响，并将 CSP 理论真正应用于语言本身（Russ Cox 专门写了一篇文章记录这个[历史](https://swtch.com/~rsc/thread/)），通过引入 Channel 这个新的类型，来实现 CSP 的思想。

**Channel 类型是 Go 语言内置的类型，你无需引入某个包，就能使用它**。虽然 Go 也提供了传统的并发原语，但是它们都是通过库的方式提供的，你必须要引入 sync 包或者 atomic 包才能使用它们，而 Channel 就不一样了，它是内置类型，使用起来非常方便。Channel 和 Go 的另一个独特的特性 goroutine 一起为并发编程提供了优雅的、便利的、与传统并发控制不同的方案，并演化出很多并发模式。接下来，我们就来看一看 Channel 的应用场景。



### Channel 的应用场景

首先，我想先带你看一条 Go 语言中流传很广的谚语：

Don’t communicate by sharing memory, share memory by communicating.Go Proverbs by Rob Pike

这是 Rob Pike 在 2015 年的一次 Gopher 会议中提到的一句话，虽然有一点绕，但也指出了使用 Go 语言的哲学，我尝试着来翻译一下：“**执行业务处理的 goroutine 不要通过共享内存的方式通信，而是要通过 Channel 通信的方式分享数据**。”

“communicate by sharing memory”和“share memory by communicating”是两种不同的并发处理模式。“communicate by sharing memory”是传统的并发编程处理方式，就是指，共享的数据需要用锁进行保护，goroutine 需要获取到锁，才能并发访问数据。“share memory by communicating”则是类似于 CSP 模型的方式，通过通信的方式，一个 goroutine 可以把数据的“所有权”交给另外一个 goroutine（虽然 Go 中没有“所有权”的概念，但是从逻辑上说，你可以把它理解为是所有权的转移）。从 Channel 的历史和设计哲学上，我们就可以了解到，Channel 类型和基本并发原语是有竞争关系的，它应用于并发场景，涉及到 goroutine 之间的通讯，可以提供并发的保护，等等。综合起来，我把 Channel 的应用场景分为五种类型。这里你先有个印象，这样你可以有目的地去学习 Channel 的基本原理。下节课我会借助具体的例子，来带你掌握这几种类型。

数据交流：当作并发的 buffer 或者 queue，解决生产者 - 消费者问题。多个 goroutine 可以并发当作生产者（Producer）和消费者（Consumer）。数据传递：一个 goroutine 将数据交给另一个 goroutine，相当于把数据的拥有权 (引用) 托付出去。信号通知：一个 goroutine 可以将信号 (closing、closed、data ready 等) 传递给另一个或者另一组 goroutine 。任务编排：可以让一组 goroutine 按照一定的顺序并发或者串行的执行，这就是编排的功能。锁：利用 Channel 也可以实现互斥锁的机制。

下面，我们来具体学习下 Channel 的基本用法。

### Channel 基本用法

你可以往 Channel 中发送数据，也可以从 Channel 中接收数据，所以，Channel 类型（为了说起来方便，我们下面都把 Channel 叫做 chan）分为只能接收、只能发送、既可以接收又可以发送三种类型。下面是它的语法定义：

```
ChannelType = ( "chan" | "chan" "<-" | "<-" "chan" ) ElementType .
```
相应地，Channel 的正确语法如下：

```
chan string          // 可以发送接收string
chan<- struct{}      // 只能发送struct{}
<-chan int           // 只能从chan接收int
```


我们把既能接收又能发送的 chan 叫做双向的 chan，把只能发送和只能接收的 chan 叫做单向的 chan。其中，“<-”表示单向的 chan，如果你记不住，我告诉你一个简便的方法：这个箭头总是射向左边的，元素类型总在最右边。**如果箭头指向 chan，就表示可以往 chan 中塞数据；如果箭头远离 chan，就表示 chan 会往外吐数据。**

chan 中的元素是任意的类型，所以也可能是 chan 类型，我来举个例子，比如下面的 chan 类型也是合法的：

```
chan<- chan int   
chan<- <-chan int  
<-chan <-chan int
chan (<-chan int)
```
可是，怎么判定箭头符号属于哪个 chan 呢？其实，“<-”有个规则，总是尽量和左边的 chan 结合（The <- operator associates with the leftmost chan possible:），因此，上面的定义和下面的使用括号的划分是一样的：

```
chan<- （chan int） // <- 和第一个chan结合
chan<- （<-chan int） // 第一个<-和最左边的chan结合，第二个<-和左边第二个chan结合
<-chan （<-chan int） // 第一个<-和最左边的chan结合，第二个<-和左边第二个chan结合 
chan (<-chan int) // 因为括号的原因，<-和括号内第一个chan结合
```
通过 make，我们可以初始化一个 chan，未初始化的 chan 的零值是 nil。你可以设置它的容量，比如下面的 chan 的容量是 9527，我们把这样的 chan 叫做 buffered chan；如果没有设置，它的容量是 0，我们把这样的 chan 叫做 unbuffered chan。

```
make(chan int, 9527)
```
如果 chan 中还有数据，那么，从这个 chan 接收数据的时候就不会阻塞，如果 chan 还未满（“满”指达到其容量），给它发送数据也不会阻塞，否则就会阻塞。unbuffered chan 只有读写都准备好之后才不会阻塞，这也是很多使用 unbuffered chan 时的常见 Bug。还有一个知识点需要你记住：nil 是 chan 的零值，是一种特殊的 chan，对值是 nil 的 chan 的发送接收调用者总是会阻塞。下面，我来具体给你介绍几种基本操作，分别是发送数据、接收数据，以及一些其它操作。学会了这几种操作，你就能真正地掌握 Channel 的用法了。



**1. 发送数据**

往 chan 中发送一个数据使用“ch<-”，发送数据是一条语句:

```
ch <- 2000
```
这里的 ch 是 chan int 类型或者是 chan <-int。

**2. 接收数据**

从 chan 中接收一条数据使用“<-ch”，接收数据也是一条语句：

```
  x := <-ch // 把接收的一条数据赋值给变量x
  foo(<-ch) // 把接收的一个的数据作为参数传给函数
  <-ch // 丢弃接收的一条数据
```
这里的 ch 类型是 chan T 或者 <-chan T。接收数据时，还可以返回两个值。第一个值是返回的 chan 中的元素，很多人不太熟悉的是第二个值。第二个值是 bool 类型，代表是否成功地从 chan 中读取到一个值，如果第二个参数是 false，chan 已经被 close 而且 chan 中没有缓存的数据，这个时候，第一个值是零值。所以，如果从 chan 读取到一个零值，可能是 sender 真正发送的零值，也可能是 closed 的并且没有缓存元素产生的零值。

**3. 其它操作**

Go 内建的函数 close、cap、len 都可以操作 chan 类型：close 会把 chan 关闭掉，cap 返回 chan 的容量，len 返回 chan 中缓存的还未被取走的元素数量。send 和 recv 都可以作为 select 语句的 case clause，如下面的例子：

```go
func main() {
    var ch = make(chan int, 10)
    for i := 0; i < 10; i++ {
        select {
        case ch <- i:
        case v := <-ch:
            fmt.Println(v)
        }
    }
}
```
chan 还可以应用于 for-range 语句中，比如：

```
    for v := range ch {
        fmt.Println(v)
    }
```
或者是忽略读取的值，只是清空 chan：

```
    for range ch {
    }
```
好了，到这里，Channel 的基本用法，我们就学完了。下面我从代码实现的角度分析 chan 类型的实现。毕竟，只有掌握了原理，你才能真正地用好它。

### Channel 的实现原理

接下来，我会给你介绍 chan 的数据结构、初始化的方法以及三个重要的操作方法，分别是 send、recv 和 close。通过学习 Channel 的底层实现，你会对 Channel 的功能和异常情况有更深的理解。

#### chan 数据结构

chan 类型的数据结构如下图所示，它的数据类型是[runtime.hchan](https://github.com/golang/go/blob/master/src/runtime/chan.go#L32)。

![img](https://static001.geekbang.org/resource/image/81/dd/81304c1f1845d21c66195798b6ba48dd.jpg?wh=2334*2250)

下面我来具体解释各个字段的意义。

qcount：代表 chan 中已经接收但还没被取走的元素的个数。内建函数 len 可以返回这个字段的值。dataqsiz：队列的大小。chan 使用一个循环队列来存放元素，循环队列很适合这种生产者 - 消费者的场景（我很好奇为什么这个字段省略 size 中的 e）。buf：存放元素的循环队列的 buffer。elemtype 和 elemsize：chan 中元素的类型和 size。因为 chan 一旦声明，它的元素类型是固定的，即普通类型或者指针类型，所以元素大小也是固定的。sendx：处理发送数据的指针在 buf 中的位置。一旦接收了新的数据，指针就会加上 elemsize，移向下一个位置。buf 的总大小是 elemsize 的整数倍，而且 buf 是一个循环列表。recvx：处理接收请求时的指针在 buf 中的位置。一旦取出数据，此指针会移动到下一个位置。recvq：chan 是多生产者多消费者的模式，如果消费者因为没有数据可读而被阻塞了，就会被加入到 recvq 队列中。sendq：如果生产者因为 buf 满了而阻塞，会被加入到 sendq 队列中。

### 初始化

Go 在编译的时候，会根据容量的大小选择调用 makechan64，还是 makechan。下面的代码是处理 make chan 的逻辑，它会决定是使用 makechan 还是 makechan64 来实现 chan 的初始化：

![img](https://static001.geekbang.org/resource/image/e9/d7/e96f2fee0633c8157a88b8b725f702d7.png?wh=1137*489)

**我们只关注 makechan 就好了，因为 makechan64 只是做了 size 检查，底层还是调用 makechan 实现的**。makechan 的目标就是生成 hchan 对象。那么，接下来，就让我们来看一下 makechan 的主要逻辑。主要的逻辑我都加上了注释，它会根据 chan 的容量的大小和元素的类型不同，初始化不同的存储空间：

```go
func makechan(t *chantype, size int) *hchan {
    elem := t.elem
  
        // 略去检查代码
        mem, overflow := math.MulUintptr(elem.size, uintptr(size))
        
    //
    var c *hchan
    switch {
    case mem == 0:
      // chan的size或者元素的size是0，不必创建buf
      c = (*hchan)(mallocgc(hchanSize, nil, true))
      c.buf = c.raceaddr()
    case elem.ptrdata == 0:
      // 元素不是指针，分配一块连续的内存给hchan数据结构和buf
      c = (*hchan)(mallocgc(hchanSize+mem, nil, true))
            // hchan数据结构后面紧接着就是buf
      c.buf = add(unsafe.Pointer(c), hchanSize)
    default:
      // 元素包含指针，那么单独分配buf
      c = new(hchan)
      c.buf = mallocgc(mem, elem, true)
    }
  
        // 元素大小、类型、容量都记录下来
    c.elemsize = uint16(elem.size)
    c.elemtype = elem
    c.dataqsiz = uint(size)
    lockInit(&c.lock, lockRankHchan)

    return c
  }
```
最终，针对不同的容量和元素类型，这段代码分配了不同的对象来初始化 hchan 对象的字段，返回 hchan 对象。

### send

Go 在编译发送数据给 chan 的时候，会把 send 语句转换成 chansend1 函数，chansend1 函数会调用 chansend，我们分段学习它的逻辑：

```go
func chansend1(c *hchan, elem unsafe.Pointer) {
    chansend(c, elem, true, getcallerpc())
}
func chansend(c *hchan, ep unsafe.Pointer, block bool, callerpc uintptr) bool {
        // 第一部分
    if c == nil {
      if !block {
        return false
      }
      gopark(nil, nil, waitReasonChanSendNilChan, traceEvGoStop, 2)
      throw("unreachable")
    }
      ......
  }
```
最开始，第一部分是进行判断：如果 chan 是 nil 的话，就把调用者 goroutine park（阻塞休眠）， 调用者就永远被阻塞住了，所以，第 11 行是不可能执行到的代码。

```
  // 第二部分，如果chan没有被close,并且chan满了，直接返回
    if !block && c.closed == 0 && full(c) {
      return false
  }
```
第二部分的逻辑是当你往一个已经满了的 chan 实例发送数据时，并且想不阻塞当前调用，那么这里的逻辑是直接返回。chansend1 方法在调用 chansend 的时候设置了阻塞参数，所以不会执行到第二部分的分支里。

```
  // 第三部分，chan已经被close的情景
    lock(&c.lock) // 开始加锁
    if c.closed != 0 {
      unlock(&c.lock)
      panic(plainError("send on closed channel"))
  }
```
第三部分显示的是，如果 chan 已经被 close 了，再往里面发送数据的话会 panic。

```
      // 第四部分，从接收队列中出队一个等待的receiver
        if sg := c.recvq.dequeue(); sg != nil {
      // 
      send(c, sg, ep, func() { unlock(&c.lock) }, 3)
      return true
    }
```
第四部分，如果等待队列中有等待的 receiver，那么这段代码就把它从队列中弹出，然后直接把数据交给它（通过 memmove(dst, src, t.size)），而不需要放入到 buf 中，速度可以更快一些。

```
    // 第五部分，buf还没满
      if c.qcount < c.dataqsiz {
      qp := chanbuf(c, c.sendx)
      if raceenabled {
        raceacquire(qp)
        racerelease(qp)
      }
      typedmemmove(c.elemtype, qp, ep)
      c.sendx++
      if c.sendx == c.dataqsiz {
        c.sendx = 0
      }
      c.qcount++
      unlock(&c.lock)
      return true
    }
```
第五部分说明当前没有 receiver，需要把数据放入到 buf 中，放入之后，就成功返回了。

```
      // 第六部分，buf满。
        // chansend1不会进入if块里，因为chansend1的block=true
        if !block {
      unlock(&c.lock)
      return false
    }
        ......
```
第六部分是处理 buf 满的情况。如果 buf 满了，发送者的 goroutine 就会加入到发送者的等待队列中，直到被唤醒。这个时候，数据或者被取走了，或者 chan 被 close 了。



### recv

在处理从 chan 中接收数据时，Go 会把代码转换成 chanrecv1 函数，如果要返回两个返回值，会转换成 chanrecv2，chanrecv1 函数和 chanrecv2 会调用 chanrecv。我们分段学习它的逻辑：

```
    func chanrecv1(c *hchan, elem unsafe.Pointer) {
    chanrecv(c, elem, true)
  }
  func chanrecv2(c *hchan, elem unsafe.Pointer) (received bool) {
    _, received = chanrecv(c, elem, true)
    return
  }

    func chanrecv(c *hchan, ep unsafe.Pointer, block bool) (selected, received bool) {
        // 第一部分，chan为nil
    if c == nil {
      if !block {
        return
      }
      gopark(nil, nil, waitReasonChanReceiveNilChan, traceEvGoStop, 2)
      throw("unreachable")
    }
```
chanrecv1 和 chanrecv2 传入的 block 参数的值是 true，都是阻塞方式，所以我们分析 chanrecv 的实现的时候，不考虑 block=false 的情况。第一部分是 chan 为 nil 的情况。和 send 一样，从 nil chan 中接收（读取、获取）数据时，调用者会被永远阻塞。

```
  // 第二部分, block=false且c为空
    if !block && empty(c) {
      ......
    }
```
第二部分你可以直接忽略，因为不是我们这次要分析的场景。

```
        // 加锁，返回时释放锁
      lock(&c.lock)
      // 第三部分，c已经被close,且chan为空empty
    if c.closed != 0 && c.qcount == 0 {
      unlock(&c.lock)
      if ep != nil {
        typedmemclr(c.elemtype, ep)
      }
      return true, false
    }
```
第三部分是 chan 已经被 close 的情况。如果 chan 已经被 close 了，并且队列中没有缓存的元素，那么返回 true、false。

```
      // 第四部分，如果sendq队列中有等待发送的sender
        if sg := c.sendq.dequeue(); sg != nil {
      recv(c, sg, ep, func() { unlock(&c.lock) }, 3)
      return true, true
    }
```
第四部分是处理 buf 满的情况。这个时候，如果是 unbuffer 的 chan，就直接将 sender 的数据复制给 receiver，否则就从队列头部读取一个值，并把这个 sender 的值加入到队列尾部。

```
      // 第五部分, 没有等待的sender, buf中有数据
    if c.qcount > 0 {
      qp := chanbuf(c, c.recvx)
      if ep != nil {
        typedmemmove(c.elemtype, ep, qp)
      }
      typedmemclr(c.elemtype, qp)
      c.recvx++
      if c.recvx == c.dataqsiz {
        c.recvx = 0
      }
      c.qcount--
      unlock(&c.lock)
      return true, true
    }

    if !block {
      unlock(&c.lock)
      return false, false
    }

        // 第六部分， buf中没有元素，阻塞
        ......
```
第五部分是处理没有等待的 sender 的情况。这个是和 chansend 共用一把大锁，所以不会有并发的问题。如果 buf 有元素，就取出一个元素给 receiver。第六部分是处理 buf 中没有元素的情况。如果没有元素，那么当前的 receiver 就会被阻塞，直到它从 sender 中接收了数据，或者是 chan 被 close，才返回。



### close

通过 close 函数，可以把 chan 关闭，编译器会替换成 closechan 方法的调用。下面的代码是 close chan 的主要逻辑。如果 chan 为 nil，close 会 panic；如果 chan 已经 closed，再次 close 也会 panic。否则的话，如果 chan 不为 nil，chan 也没有 closed，就把等待队列中的 sender（writer）和 receiver（reader）从队列中全部移除并唤醒。下面的代码就是 close chan 的逻辑:

```
    func closechan(c *hchan) {
    if c == nil { // chan为nil, panic
      panic(plainError("close of nil channel"))
    }
  
    lock(&c.lock)
    if c.closed != 0 {// chan已经closed, panic
      unlock(&c.lock)
      panic(plainError("close of closed channel"))
    }

    c.closed = 1  

    var glist gList

    // 释放所有的reader
    for {
      sg := c.recvq.dequeue()
      ......
      gp := sg.g
      ......
      glist.push(gp)
    }
  
    // 释放所有的writer (它们会panic)
    for {
      sg := c.sendq.dequeue()
      ......
      gp := sg.g
      ......
      glist.push(gp)
    }
    unlock(&c.lock)
  
    for !glist.empty() {
      gp := glist.pop()
      gp.schedlink = 0
      goready(gp, 3)
    }
  }
```
掌握了 Channel 的基本用法和实现原理，下面我再来给你讲一讲容易犯的错误。你一定要认真看，毕竟，这些可都是帮助你避坑的。

### 使用 Channel 容易犯的错误

据 2019 年第一篇全面分析 Go 并发 Bug 的论文，那些知名的 Go 项目中使用 Channel 所犯的 Bug 反而比传统的并发原语的 Bug 还要多。主要有两个原因：一个是，Channel 的概念还比较新，程序员还不能很好地掌握相应的使用方法和最佳实践；第二个是，Channel 有时候比传统的并发原语更复杂，使用起来很容易顾此失彼。

#### 使用 Channel 最常见的错误是 panic 和 goroutine 泄漏。

首先，我们来总结下会 panic 的情况，总共有 3 种：

close 为 nil 的 chan；send 已经 close 的 chan；close 已经 close 的 chan。

goroutine 泄漏的问题也很常见，下面的代码也是一个实际项目中的例子：

```go
func process(timeout time.Duration) bool {
    ch := make(chan bool)

    go func() {
        // 模拟处理耗时的业务
        time.Sleep((timeout + time.Second))
        ch <- true // block
        fmt.Println("exit goroutine")
    }()
    select {
    case result := <-ch:
        return result
    case <-time.After(timeout):
        return false
    }
}
```


在这个例子中，process 函数会启动一个 goroutine，去处理需要长时间处理的业务，处理完之后，会发送 true 到 chan 中，目的是通知其它等待的 goroutine，可以继续处理了。我们来看一下第 10 行到第 15 行，主 goroutine 接收到任务处理完成的通知，或者超时后就返回了。这段代码有问题吗？如果发生超时，process 函数就返回了，这就会导致 unbuffered 的 chan 从来就没有被读取。我们知道，unbuffered chan 必须等 reader 和 writer 都准备好了才能交流，否则就会阻塞。超时导致未读，结果就是子 goroutine 就阻塞在第 7 行永远结束不了，进而导致 goroutine 泄漏。解决这个 Bug 的办法很简单，就是将 unbuffered chan 改成容量为 1 的 chan，这样第 7 行就不会被阻塞了。Go 的开发者极力推荐使用 Channel，不过，这两年，大家意识到，Channel 并不是处理并发问题的“银弹”，有时候使用并发原语更简单，而且不容易出错。所以，我给你提供一套选择的方法:

共享资源的并发访问使用传统并发原语；复杂的任务编排和消息传递使用 Channel；消息通知机制使用 Channel，除非只想 signal 一个 goroutine，才使用 Cond；简单等待所有任务的完成用 WaitGroup，也有 Channel 的推崇者用 Channel，都可以；需要和 Select 语句结合，使用 Channel；需要和超时配合时，使用 Channel 和 Context。

### 它们踩过的坑

接下来，我带你围观下知名 Go 项目的 Channel 相关的 Bug。

etcd issue 6857是一个程序 hang 住的问题：在异常情况下，没有往 chan 实例中填充所需的元素，导致等待者永远等待。具体来说，Status 方法的逻辑是生成一个 chan Status，然后把这个 chan 交给其它的 goroutine 去处理和写入数据，最后，Status 返回获取的状态信息。不幸的是，如果正好节点停止了，没有 goroutine 去填充这个 chan，会导致方法 hang 在返回的那一行上（下面的截图中的第 466 行）。解决办法就是，在等待 status chan 返回元素的同时，也检查节点是不是已经停止了（done 这个 chan 是不是 close 了）。当前的 etcd 的代码就是修复后的代码，如下所示：

![img](https://static001.geekbang.org/resource/image/5f/da/5f3c15c110077714be81be8eb1fd3fda.png?wh=920*481)

其实，我感觉这个修改还是有问题的。问题就在于，如果程序执行了 466 行，成功地把 c 写入到 Status 待处理队列后，执行到第 467 行时，如果停止了这个节点，那么，这个 Status 方法还是会阻塞在第 467 行。你可以自己研究研究，看看是不是这样。

etcd issue 5505 虽然没有任何的 Bug 描述，但是从修复内容上看，它是一个往已经 close 的 chan 写数据导致 panic 的问题。etcd issue 11256  是因为 unbuffered chan goroutine 泄漏的问题。TestNodeProposeAddLearnerNode 方法中一开始定义了一个 unbuffered 的 chan，也就是 applyConfChan，然后启动一个子 goroutine，这个子 goroutine 会在循环中执行业务逻辑，并且不断地往这个 chan 中添加一个元素。TestNodeProposeAddLearnerNode 方法的末尾处会从这个 chan 中读取一个元素。

这段代码在 for 循环中就往此 chan 中写入了一个元素，结果导致 TestNodeProposeAddLearnerNode 从这个 chan 中读取到元素就返回了。悲剧的是，子 goroutine 的 for 循环还在执行，阻塞在下图中红色的第 851 行，并且一直 hang 在那里。这个 Bug 的修复也很简单，只要改动一下 applyConfChan 的处理逻辑就可以了：只有子 goroutine 的 for 循环中的主要逻辑完成之后，才往 applyConfChan 发送一个元素，这样，TestNodeProposeAddLearnerNode 收到通知继续执行，子 goroutine 也不会被阻塞住了。

![img](https://static001.geekbang.org/resource/image/d5/9f/d53573c8fc515f78ea590bf73396969f.png?wh=1521*614)

etcd issue 9956 是往一个已 close 的 chan 发送数据，其实它是 grpc 的一个 bug（grpc issue 2695），修复办法就是不 close 这个 chan 就好了：

![img](https://static001.geekbang.org/resource/image/65/21/650f0911b1c7278cc0438c85bbc4yy21.png?wh=1052*185)

### 总结

chan 的值和状态有多种情况，而不同的操作（send、recv、close）又可能得到不同的结果，这是使用 chan 类型时经常让人困惑的地方。为了帮助你快速地了解不同状态下各种操作的结果，我总结了一个表格，你一定要特别关注下那些 panic 的情况，另外还要掌握那些会 block 的场景，它们是导致死锁或者 goroutine 泄露的罪魁祸首。还有一个值得注意的点是，只要一个 chan 还有未读的数据，即使把它 close 掉，你还是可以继续把这些未读的数据消费完，之后才是读取零值数据。

![img](https://static001.geekbang.org/resource/image/51/98/5108954ea36559860e5e5aaa42b2f998.jpg?wh=3601*1075)

## 14 | Channel：透过代码看典型的应用模式

前一讲，我介绍了 Channel 的基础知识，并且总结了几种应用场景。这一讲，我将通过实例的方式，带你逐个学习 Channel 解决这些问题的方法，帮你巩固和完全掌握它的用法。在开始上课之前，我先补充一个知识点：通过反射的方式执行 select 语句，在处理很多的 case clause，尤其是不定长的 case clause 的时候，非常有用。而且，在后面介绍任务编排的实现时，我也会采用这种方法，所以，我先带你具体学习下 Channel 的反射用法。

### 使用反射操作 Channel

select 语句可以处理 chan 的 send 和 recv，send 和 recv 都可以作为 case clause。如果我们同时处理两个 chan，就可以写成下面的样子：

```
    select {
    case v := <-ch1:
        fmt.Println(v)
    case v := <-ch2:
        fmt.Println(v)
    }
```
如果需要处理三个 chan，你就可以再添加一个 case clause，用它来处理第三个 chan。可是，如果要处理 100 个 chan 呢？一万个 chan 呢？或者是，chan 的数量在编译的时候是不定的，在运行的时候需要处理一个 slice of chan，这个时候，也没有办法在编译前写成字面意义的 select。那该怎么办？这个时候，就要“祭”出我们的反射大法了。通过 reflect.Select 函数，你可以将一组运行时的 case clause 传入，当作参数执行。Go 的 select 是伪随机的，它可以在执行的 case 中随机选择一个 case，并把选择的这个 case 的索引（chosen）返回，如果没有可用的 case 返回，会返回一个 bool 类型的返回值，这个返回值用来表示是否有 case 成功被选择。如果是 recv case，还会返回接收的元素。Select 的方法签名如下：

```go
func Select(cases []SelectCase) (chosen int, recv Value, recvOK bool)
```
下面，我来借助一个例子，来演示一下，动态处理两个 chan 的情形。因为这样的方式可以动态处理 case 数据，所以，你可以传入几百几千几万的 chan，这就解决了不能动态处理 n 个 chan 的问题。首先，createCases 函数分别为每个 chan 生成了 recv case 和 send case，并返回一个 reflect.SelectCase 数组。然后，通过一个循环 10 次的 for 循环执行 reflect.Select，这个方法会从 cases 中选择一个 case 执行。第一次肯定是 send case，因为此时 chan 还没有元素，recv 还不可用。等 chan 中有了数据以后，recv case 就可以被选择了。这样，你就可以处理不定数量的 chan 了。

```go
func main() {
    var ch1 = make(chan int, 10)
    var ch2 = make(chan int, 10)

    // 创建SelectCase
    var cases = createCases(ch1, ch2)

    // 执行10次select
    for i := 0; i < 10; i++ {
        chosen, recv, ok := reflect.Select(cases)
        if recv.IsValid() { // recv case
            fmt.Println("recv:", cases[chosen].Dir, recv, ok)
        } else { // send case
            fmt.Println("send:", cases[chosen].Dir, ok)
        }
    }
}

func createCases(chs ...chan int) []reflect.SelectCase {
    var cases []reflect.SelectCase


    // 创建recv case
    for _, ch := range chs {
        cases = append(cases, reflect.SelectCase{
            Dir:  reflect.SelectRecv,
            Chan: reflect.ValueOf(ch),
        })
    }

    // 创建send case
    for i, ch := range chs {
        v := reflect.ValueOf(i)
        cases = append(cases, reflect.SelectCase{
            Dir:  reflect.SelectSend,
            Chan: reflect.ValueOf(ch),
            Send: v,
        })
    }

    return cases
}
```
### 典型的应用场景

了解刚刚的反射用法，我们就解决了今天的基础知识问题，接下来，我就带你具体学习下 Channel 的应用场景。首先来看消息交流。

首先来看消息交流。

#### 消息交流

从 chan 的内部实现看，它是以一个循环队列的方式存放数据，所以，它有时候也会被当成线程安全的队列和 buffer 使用。一个 goroutine 可以安全地往 Channel 中塞数据，另外一个 goroutine 可以安全地从 Channel 中读取数据，goroutine 就可以安全地实现信息交流了。

我们来看几个例子。

第一个例子是 worker 池的例子。Marcio Castilho 在 [使用 Go 每分钟处理百万请求](http://marcio.io/2015/07/handling-1-million-requests-per-minute-with-golang/)  这篇文章中，就介绍了他们应对大并发请求的设计。他们将用户的请求放在一个 chan Job 中，这个 chan Job 就相当于一个待处理任务队列。除此之外，还有一个 chan chan Job 队列，用来存放可以处理任务的 worker 的缓存队列。

dispatcher 会把待处理任务队列中的任务放到一个可用的缓存队列中，worker 会一直处理它的缓存队列。通过使用 Channel，实现了一个 worker 池的任务处理中心，并且解耦了前端 HTTP 请求处理和后端任务处理的逻辑。我在讲 Pool 的时候，提到了一些第三方实现的 worker 池，它们全部都是通过 Channel 实现的，这是 Channel 的一个常见的应用场景。worker 池的生产者和消费者的消息交流都是通过 Channel 实现的。第二个例子是 etcd 中的 node 节点的实现，包含大量的 chan 字段，比如 recvc 是消息处理的 chan，待处理的 protobuf 消息都扔到这个 chan 中，node 有一个专门的 run goroutine 负责处理这些消息。

![img](https://static001.geekbang.org/resource/image/06/a4/0643503a1yy135b476d41345d71766a4.png?wh=573*413)

#### 数据传递

“击鼓传花”的游戏很多人都玩过，花从一个人手中传给另外一个人，就有点类似流水线的操作。这个花就是数据，花在游戏者之间流转，这就类似编程中的数据传递。还记得上节课我给你留了一道任务编排的题吗？其实它就可以用数据传递的方式实现。

有 4 个 goroutine，编号为 1、2、3、4。每秒钟会有一个 goroutine 打印出它自己的编号，要求你编写程序，让输出的编号总是按照 1、2、3、4、1、2、3、4……这个顺序打印出来。

为了实现顺序的数据传递，我们可以定义一个令牌的变量，谁得到令牌，谁就可以打印一次自己的编号，同时将令牌传递给下一个 goroutine，我们尝试使用 chan 来实现，可以看下下面的代码。

```
type Token struct{}

func newWorker(id int, ch chan Token, nextCh chan Token) {
    for {
        token := <-ch         // 取得令牌
        fmt.Println((id + 1)) // id从1开始
        time.Sleep(time.Second)
        nextCh <- token
    }
}
func main() {
    chs := []chan Token{make(chan Token), make(chan Token), make(chan Token), make(chan Token)}

    // 创建4个worker
    for i := 0; i < 4; i++ {
        go newWorker(i, chs[i], chs[(i+1)%4])
    }

    //首先把令牌交给第一个worker
    chs[0] <- struct{}{}
  
    select {}
}
```
我来给你具体解释下这个实现方式。首先，我们定义一个令牌类型（Token），接着定义一个创建 worker 的方法，这个方法会从它自己的 chan 中读取令牌。哪个 goroutine 取得了令牌，就可以打印出自己编号，因为需要每秒打印一次数据，所以，我们让它休眠 1 秒后，再把令牌交给它的下家。接着，在第 16 行启动每个 worker 的 goroutine，并在第 20 行将令牌先交给第一个 worker。如果你运行这个程序，就会在命令行中看到每一秒就会输出一个编号，而且编号是以 1、2、3、4 这样的顺序输出的。这类场景有一个特点，就是当前持有数据的 goroutine 都有一个信箱，信箱使用 chan 实现，goroutine 只需要关注自己的信箱中的数据，处理完毕后，就把结果发送到下一家的信箱中。

#### 信号通知

chan 类型有这样一个特点：chan 如果为空，那么，receiver 接收数据的时候就会阻塞等待，直到 chan 被关闭或者有新的数据到来。利用这个机制，我们可以实现 wait/notify 的设计模式。传统的并发原语 Cond 也能实现这个功能。但是，Cond 使用起来比较复杂，容易出错，而使用 chan 实现 wait/notify 模式，就方便多了。除了正常的业务处理时的 wait/notify，我们经常碰到的一个场景，就是程序关闭的时候，我们需要在退出之前做一些清理（doCleanup 方法）的动作。这个时候，我们经常要使用 chan。比如，使用 chan 实现程序的 graceful shutdown，在退出之前执行一些连接关闭、文件 close、缓存落盘等一些动作。

```go
func main() {
  go func() {
      ...... // 执行业务处理
    }()

  // 处理CTRL+C等中断信号
  termChan := make(chan os.Signal)
  signal.Notify(termChan, syscall.SIGINT, syscall.SIGTERM)
  <-termChan 

  // 执行退出之前的清理动作
    doCleanup()
  
  fmt.Println("优雅退出")
}
```


有时候，doCleanup 可能是一个很耗时的操作，比如十几分钟才能完成，如果程序退出需要等待这么长时间，用户是不能接受的，所以，在实践中，我们需要设置一个最长的等待时间。只要超过了这个时间，程序就不再等待，可以直接退出。所以，退出的时候分为两个阶段：closing，代表程序退出，但是清理工作还没做；closed，代表清理工作已经做完。

所以，上面的例子可以改写如下：

```go
func main() {
    var closing = make(chan struct{})
    var closed = make(chan struct{})

    go func() {
        // 模拟业务处理
        for {
            select {
            case <-closing:
                return
            default:
                // ....... 业务计算
                time.Sleep(100 * time.Millisecond)
            }
        }
    }()

    // 处理CTRL+C等中断信号
    termChan := make(chan os.Signal)
    signal.Notify(termChan, syscall.SIGINT, syscall.SIGTERM)
    <-termChan

    close(closing)
    // 执行退出之前的清理动作
    go doCleanup(closed)

    select {
    case <-closed:
    case <-time.After(time.Second):
        fmt.Println("清理超时，不等了")
    }
    fmt.Println("优雅退出")
}

func doCleanup(closed chan struct{}) {
    time.Sleep((time.Minute))
    close(closed)
}
```
#### 锁

使用 chan 也可以实现互斥锁。在 chan 的内部实现中，就有一把互斥锁保护着它的所有字段。从外在表现上，chan 的发送和接收之间也存在着 happens-before 的关系，保证元素放进去之后，receiver 才能读取到（关于 happends-before 的关系，是指事件发生的先后顺序关系，我会在下一讲详细介绍，这里你只需要知道它是一种描述事件先后顺序的方法）。要想使用 chan 实现互斥锁，至少有两种方式。一种方式是先初始化一个 capacity 等于 1 的 Channel，然后再放入一个元素。这个元素就代表锁，谁取得了这个元素，就相当于获取了这把锁。另一种方式是，先初始化一个 capacity 等于 1 的 Channel，它的“空槽”代表锁，谁能成功地把元素发送到这个 Channel，谁就获取了这把锁。这是使用 Channel 实现锁的两种不同实现方式，我重点介绍下第一种。理解了这种实现方式，第二种方式也就很容易掌握了，我就不多说了。

```
// 使用chan实现互斥锁
type Mutex struct {
    ch chan struct{}
}

// 使用锁需要初始化
func NewMutex() *Mutex {
    mu := &Mutex{make(chan struct{}, 1)}
    mu.ch <- struct{}{}
    return mu
}

// 请求锁，直到获取到
func (m *Mutex) Lock() {
    <-m.ch
}

// 解锁
func (m *Mutex) Unlock() {
    select {
    case m.ch <- struct{}{}:
    default:
        panic("unlock of unlocked mutex")
    }
}

// 尝试获取锁
func (m *Mutex) TryLock() bool {
    select {
    case <-m.ch:
        return true
    default:
    }
    return false
}

// 加入一个超时的设置
func (m *Mutex) LockTimeout(timeout time.Duration) bool {
    timer := time.NewTimer(timeout)
    select {
    case <-m.ch:
        timer.Stop()
        return true
    case <-timer.C:
    }
    return false
}

// 锁是否已被持有
func (m *Mutex) IsLocked() bool {
    return len(m.ch) == 0
}


func main() {
    m := NewMutex()
    ok := m.TryLock()
    fmt.Printf("locked v %v\n", ok)
    ok = m.TryLock()
    fmt.Printf("locked %v\n", ok)
}
```
你可以用 buffer 等于 1 的 chan 实现互斥锁，在初始化这个锁的时候往 Channel 中先塞入一个元素，谁把这个元素取走，谁就获取了这把锁，把元素放回去，就是释放了锁。元素在放回到 chan 之前，不会有 goroutine 能从 chan 中取出元素的，这就保证了互斥性。在这段代码中，还有一点需要我们注意下：利用 select+chan 的方式，很容易实现 TryLock、Timeout 的功能。具体来说就是，在 select 语句中，我们可以使用 default 实现 TryLock，使用一个 Timer 来实现 Timeout 的功能。

#### 任务编排

前面所说的消息交流的场景是一个特殊的任务编排的场景，这个“击鼓传花”的模式也被称为流水线模式。在第 6 讲，我们学习了 WaitGroup，我们可以利用它实现等待模式：启动一组 goroutine 执行任务，然后等待这些任务都完成。其实，我们也可以使用 chan 实现 WaitGroup 的功能。这个比较简单，我就不举例子了，接下来我介绍几种更复杂的编排模式。这里的编排既指安排 goroutine 按照指定的顺序执行，也指多个 chan 按照指定的方式组合处理的方式。goroutine 的编排类似“击鼓传花”的例子，我们通过编排数据在 chan 之间的流转，就可以控制 goroutine 的执行。接下来，我来重点介绍下多个 chan 的编排方式，总共 5 种，分别是 Or-Done 模式、扇入模式、扇出模式、Stream 和 map-reduce。

#### Or-Done 模式

首先来看 Or-Done 模式。Or-Done 模式是信号通知模式中更宽泛的一种模式。这里提到了“信号通知模式”，我先来解释一下。我们会使用“信号通知”实现某个任务执行完成后的通知机制，在实现时，我们为这个任务定义一个类型为 chan struct{}类型的 done 变量，等任务结束后，我们就可以 close 这个变量，然后，其它 receiver 就会收到这个通知。这是有一个任务的情况，如果有多个任务，只要有任意一个任务执行完，我们就想获得这个信号，这就是 Or-Done 模式。比如，你发送同一个请求到多个微服务节点，只要任意一个微服务节点返回结果，就算成功，这个时候，就可以参考下面的实现：

```go
func or(channels ...<-chan interface{}) <-chan interface{} {
    // 特殊情况，只有零个或者1个chan
    switch len(channels) {
    case 0:
        return nil
    case 1:
        return channels[0]
    }

    orDone := make(chan interface{})
    go func() {
        defer close(orDone)

        switch len(channels) {
        case 2: // 2个也是一种特殊情况
            select {
            case <-channels[0]:
            case <-channels[1]:
            }
        default: //超过两个，二分法递归处理
            m := len(channels) / 2
            select {
            case <-or(channels[:m]...):
            case <-or(channels[m:]...):
            }
        }
    }()

    return orDone
}
```
我们可以写一个测试程序测试它：

```go
func sig(after time.Duration) <-chan interface{} {
    c := make(chan interface{})
    go func() {
        defer close(c)
        time.Sleep(after)
    }()
    return c
}


func main() {
    start := time.Now()

    <-or(
        sig(10*time.Second),
        sig(20*time.Second),
        sig(30*time.Second),
        sig(40*time.Second),
        sig(50*time.Second),
        sig(01*time.Minute),
    )

    fmt.Printf("done after %v", time.Since(start))
}
```
这里的实现使用了一个巧妙的方式，当 chan 的数量大于 2 时，使用递归的方式等待信号。在 chan 数量比较多的情况下，递归并不是一个很好的解决方式，根据这一讲最开始介绍的反射的方法，我们也可以实现 Or-Done 模式：

```go
func or(channels ...<-chan interface{}) <-chan interface{} {
    //特殊情况，只有0个或者1个
    switch len(channels) {
    case 0:
        return nil
    case 1:
        return channels[0]
    }

    orDone := make(chan interface{})
    go func() {
        defer close(orDone)
        // 利用反射构建SelectCase
        var cases []reflect.SelectCase
        for _, c := range channels {
            cases = append(cases, reflect.SelectCase{
                Dir:  reflect.SelectRecv,
                Chan: reflect.ValueOf(c),
            })
        }

        // 随机选择一个可用的case
        reflect.Select(cases)
    }()


    return orDone
}
```
这是递归和反射两种方法实现 Or-Done 模式的代码。反射方式避免了深层递归的情况，可以处理有大量 chan 的情况。其实最笨的一种方法就是为每一个 Channel 启动一个 goroutine，不过这会启动非常多的 goroutine，太多的 goroutine 会影响性能，所以不太常用。你只要知道这种用法就行了，不用重点掌握。

### 扇入模式

扇入借鉴了数字电路的概念，它定义了单个逻辑门能够接受的数字信号输入最大量的术语。一个逻辑门可以有多个输入，一个输出。在软件工程中，模块的扇入是指有多少个上级模块调用它。而对于我们这里的 Channel 扇入模式来说，就是指有多个源 Channel 输入、一个目的 Channel 输出的情况。扇入比就是源 Channel 数量比 1。每个源 Channel 的元素都会发送给目标 Channel，相当于目标 Channel 的 receiver 只需要监听目标 Channel，就可以接收所有发送给源 Channel 的数据。扇入模式也可以使用反射、递归，或者是用最笨的每个 goroutine 处理一个 Channel 的方式来实现。这里我列举下递归和反射的方式，帮你加深一下对这个技巧的理解。反射的代码比较简短，易于理解，主要就是构造出 SelectCase slice，然后传递给 reflect.Select 语句。

```go
func fanInReflect(chans ...<-chan interface{}) <-chan interface{} {
    out := make(chan interface{})
    go func() {
        defer close(out)
        // 构造SelectCase slice
        var cases []reflect.SelectCase
        for _, c := range chans {
            cases = append(cases, reflect.SelectCase{
                Dir:  reflect.SelectRecv,
                Chan: reflect.ValueOf(c),
            })
        }
        
        // 循环，从cases中选择一个可用的
        for len(cases) > 0 {
            i, v, ok := reflect.Select(cases)
            if !ok { // 此channel已经close
                cases = append(cases[:i], cases[i+1:]...)
                continue
            }
            out <- v.Interface()
        }
    }()
    return out
}
```
递归模式也是在 Channel 大于 2 时，采用二分法递归 merge。

```go
func fanInRec(chans ...<-chan interface{}) <-chan interface{} {
    switch len(chans) {
    case 0:
        c := make(chan interface{})
        close(c)
        return c
    case 1:
        return chans[0]
    case 2:
        return mergeTwo(chans[0], chans[1])
    default:
        m := len(chans) / 2
        return mergeTwo(
            fanInRec(chans[:m]...),
            fanInRec(chans[m:]...))
    }
}
```
这里有一个 mergeTwo 的方法，是将两个 Channel 合并成一个 Channel，是扇入形式的一种特例（只处理两个 Channel）。 下面我来借助一段代码帮你理解下这个方法。

```go
func mergeTwo(a, b <-chan interface{}) <-chan interface{} {
    c := make(chan interface{})
    go func() {
        defer close(c)
        for a != nil || b != nil { //只要还有可读的chan
            select {
            case v, ok := <-a:
                if !ok { // a 已关闭，设置为nil
                    a = nil
                    continue
                }
                c <- v
            case v, ok := <-b:
                if !ok { // b 已关闭，设置为nil
                    b = nil
                    continue
                }
                c <- v
            }
        }
    }()
    return c
}
```
### 扇出模式

有扇入模式，就有扇出模式，扇出模式是和扇入模式相反的。扇出模式只有一个输入源 Channel，有多个目标 Channel，扇出比就是 1 比目标 Channel 数的值，经常用在设计模式中的[观察者模式](https://baike.baidu.com/item/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/5881786?fr=aladdin)中（观察者设计模式定义了对象间的一种一对多的组合关系。这样一来，一个对象的状态发生变化时，所有依赖于它的对象都会得到通知并自动刷新）。在观察者模式中，数据变动后，多个观察者都会收到这个变更信号。下面是一个扇出模式的实现。从源 Channel 取出一个数据后，依次发送给目标 Channel。在发送给目标 Channel 的时候，可以同步发送，也可以异步发送：

```go
func fanOut(ch <-chan interface{}, out []chan interface{}, async bool) {
    go func() {
        defer func() { //退出时关闭所有的输出chan
            for i := 0; i < len(out); i++ {
                close(out[i])
            }
        }()

        for v := range ch { // 从输入chan中读取数据
            v := v
            for i := 0; i < len(out); i++ {
                i := i
                if async { //异步
                    go func() {
                        out[i] <- v // 放入到输出chan中,异步方式
                    }()
                } else {
                    out[i] <- v // 放入到输出chan中，同步方式
                }
            }
        }
    }()
}
```
你也可以尝试使用反射的方式来实现，我就不列相关代码了，希望你课后可以自己思考下。

### Stream

这里我来介绍一种把 Channel 当作流式管道使用的方式，也就是把 Channel 看作流（Stream），提供跳过几个元素，或者是只取其中的几个元素等方法。首先，我们提供创建流的方法。这个方法把一个数据 slice 转换成流：

```go
func asStream(done <-chan struct{}, values ...interface{}) <-chan interface{} {
    s := make(chan interface{}) //创建一个unbuffered的channel
    go func() { // 启动一个goroutine，往s中塞数据
        defer close(s) // 退出时关闭chan
        for _, v := range values { // 遍历数组
            select {
            case <-done:
                return
            case s <- v: // 将数组元素塞入到chan中
            }
        }
    }()
    return s
}
```


流创建好以后，该咋处理呢？下面我再给你介绍下实现流的方法。takeN：只取流中的前 n 个数据；takeFn：筛选流中的数据，只保留满足条件的数据；takeWhile：只取前面满足条件的数据，一旦不满足条件，就不再取；skipN：跳过流中前几个数据；skipFn：跳过满足条件的数据；skipWhile：跳过前面满足条件的数据，一旦不满足条件，当前这个元素和以后的元素都会输出给 Channel 的 receiver。

这些方法的实现很类似，我们以 takeN 为例来具体解释一下。

```go
func takeN(done <-chan struct{}, valueStream <-chan interface{}, num int) <-chan interface{} {
    takeStream := make(chan interface{}) // 创建输出流
    go func() {
        defer close(takeStream)
        for i := 0; i < num; i++ { // 只读取前num个元素
            select {
            case <-done:
                return
            case takeStream <- <-valueStream: //从输入流中读取元素
            }
        }
    }()
    return takeStream
}
```
### map-reduce

map-reduce 是一种处理数据的方式，最早是由 Google 公司研究提出的一种面向大规模数据处理的并行计算模型和方法，开源的版本是 hadoop，前几年比较火。不过，我要讲的并不是分布式的 map-reduce，而是单机单进程的 map-reduce 方法。map-reduce 分为两个步骤，第一步是映射（map），处理队列中的数据，第二步是规约（reduce），把列表中的每一个元素按照一定的处理方式处理成结果，放入到结果队列中。就像做汉堡一样，map 就是单独处理每一种食材，reduce 就是从每一份食材中取一部分，做成一个汉堡。我们先来看下 map 函数的处理逻辑:

```go
func mapChan(in <-chan interface{}, fn func(interface{}) interface{}) <-chan interface{} {
    out := make(chan interface{}) //创建一个输出chan
    if in == nil { // 异常检查
        close(out)
        return out
    }

    go func() { // 启动一个goroutine,实现map的主要逻辑
        defer close(out)
        for v := range in { // 从输入chan读取数据，执行业务操作，也就是map操作
            out <- fn(v)
        }
    }()

    return out
}
```
reduce 函数的处理逻辑如下：

```go
func reduce(in <-chan interface{}, fn func(r, v interface{}) interface{}) interface{} {
    if in == nil { // 异常检查
        return nil
    }

    out := <-in // 先读取第一个元素
    for v := range in { // 实现reduce的主要逻辑
        out = fn(out, v)
    }

    return out
}
```
我们可以写一个程序，这个程序使用 map-reduce 模式处理一组整数，map 函数就是为每个整数乘以 10，reduce 函数就是把 map 处理的结果累加起来：

```
// 生成一个数据流
func asStream(done <-chan struct{}) <-chan interface{} {
    s := make(chan interface{})
    values := []int{1, 2, 3, 4, 5}
    go func() {
        defer close(s)
        for _, v := range values { // 从数组生成
            select {
            case <-done:
                return
            case s <- v:
            }
        }
    }()
    return s
}

func main() {
    in := asStream(nil)

    // map操作: 乘以10
    mapFn := func(v interface{}) interface{} {
        return v.(int) * 10
    }

    // reduce操作: 对map的结果进行累加
    reduceFn := func(r, v interface{}) interface{} {
        return r.(int) + v.(int)
    }

    sum := reduce(mapChan(in, mapFn), reduceFn) //返回累加结果
    fmt.Println(sum)
}
```
### 总结

这节课，我借助代码示例，带你学习了 Channel 的应用场景和应用模式。这几种模式不是我们学习的终点，而是学习的起点。掌握了这几种模式之后，我们可以延伸出更多的模式。虽然 Channel 最初是基于 CSP 设计的用于 goroutine 之间的消息传递的一种数据类型，但是，除了消息传递这个功能之外，大家居然还演化出了各式各样的应用模式。我不确定 Go 的创始人在设计这个类型的时候，有没有想到这一点，但是，我确实被各位大牛利用 Channel 的各种点子折服了，比如有人实现了一个基于 TCP 网络的分布式的 Channel。在使用 Go 开发程序的时候，你也不妨多考虑考虑是否能够使用 chan 类型，看看你是不是也能创造出别具一格的应用模式。

![img](https://static001.geekbang.org/resource/image/41/c9/4140728d1f331beaf92e712cd34681c9.jpg?wh=2250*3290)



## 15 | 内存模型：Go如何保证并发读写的顺序？

Go 官方文档里专门介绍了 Go 的[内存模型](https://golang.org/ref/mem)，你不要误解这里的内存模型的含义，它并不是指 Go 对象的内存分配、内存回收和内存整理的规范，它描述的是并发环境中多 goroutine 读相同变量的时候，变量的可见性条件。具体点说，就是指，在什么条件下，goroutine 在读取一个变量的值的时候，能够看到其它 goroutine 对这个变量进行的写的结果。

由于 CPU 指令重排和多级 Cache 的存在，保证多核访问同一个变量这件事儿变得非常复杂。毕竟，不同 CPU 架构（x86/amd64、ARM、Power 等）的处理方式也不一样，再加上编译器的优化也可能对指令进行重排，所以编程语言需要一个规范，来明确多线程同时访问同一个变量的可见性和顺序（ Russ Cox 在麻省理工学院 [6.824 分布式系统 Distributed Systems 课程](https://pdos.csail.mit.edu/6.824/) 的一课，专门介绍了相关的知识）。在编程语言中，这个规范被叫做内存模型。

除了 Go，Java、C++、C、C#、Rust 等编程语言也有内存模型。为什么这些编程语言都要定义内存模型呢？在我看来，主要是两个目的。

向广大的程序员提供一种保证，以便他们在做设计和开发程序时，面对同一个数据同时被多个 goroutine 访问的情况，可以做一些串行化访问的控制，比如使用 Channel 或者 sync 包和 sync/atomic 包中的并发原语。允许编译器和硬件对程序做一些优化。这一点其实主要是为编译器开发者提供的保证，这样可以方便他们对 Go 的编译器做优化。

既然内存模型这么重要，今天，我们就来花一节课的时间学习一下。首先，我们要先弄明白重排和可见性的问题，因为它们影响着程序实际执行的顺序关系。

### 重排和可见性的问题

**由于指令重排，代码并不一定会按照你写的顺序执行**。

举个例子，当两个 goroutine 同时对一个数据进行读写时，假设 goroutine g1 对这个变量进行写操作 w，goroutine g2 同时对这个变量进行读操作 r，那么，如果 g2 在执行读操作 r 的时候，已经看到了 g1 写操作 w 的结果，那么，也不意味着 g2 能看到在 w 之前的其它的写操作。这是一个反直观的结果，不过的确可能会存在。接下来，我再举几个具体的例子，带你来感受一下，重排以及多核 CPU 并发执行导致程序的运行和代码的书写顺序不一样的情况。先看第一个例子，代码如下：

```
var a, b int

func f() {
  a = 1 // w之前的写操作
  b = 2 // 写操作w
}

func g() {
  print(b) // 读操作r
  print(a) // ???
}

func main() {
  go f() //g1
  g() //g2
}
```
可以看到，第 9 行是要打印 b 的值。需要注意的是，即使这里打印出的值是 2，但是依然可能在打印 a 的值时，打印出初始值 0，而不是 1。这是因为，程序运行的时候，不能保证 g2 看到的 a 和 b 的赋值有先后关系。再来看一个类似的例子。

```
var a string
var done bool

func setup() {
  a = "hello, world"
  done = true
}

func main() {
  go setup()
  for !done {
  }
  print(a)
}
```
在这段代码中，主 goroutine main 即使观察到 done 变成 true 了，最后读取到的 a 的值仍然可能为空。更糟糕的情况是，main 根本就观察不到另一个 goroutine 对 done 的写操作，这就会导致 main 程序一直被 hang 住。甚至可能还会出现半初始化的情况，比如：

```
type T struct {
  msg string
}

var g *T

func setup() {
  t := new(T)
  t.msg = "hello, world"
  g = t
}

func main() {
  go setup()
  for g == nil {
  }
  print(g.msg)
}
```
即使 main goroutine 观察到 g 不为 nil，也可能打印出空的 msg（第 17 行）。看到这里，你可能要说了，我都运行这个程序几百万次了，怎么也没有观察到这种现象？我可以这么告诉你，能不能观察到和提供保证（guarantee）是两码事儿。由于 CPU 架构和 Go 编译器的不同，即使你运行程序时没有遇到这些现象，也不代表 Go 可以 100% 保证不会出现这些问题。刚刚说了，程序在运行的时候，两个操作的顺序可能不会得到保证，那该怎么办呢？接下来，我要带你了解一下 Go 内存模型中很重要的一个概念：happens-before，这是用来描述两个时间的顺序关系的。如果某些操作能提供 happens-before 关系，那么，我们就可以 100% 保证它们之间的顺序。

### happens-before

**在一个 goroutine 内部，程序的执行顺序和它们的代码指定的顺序是一样的，即使编译器或者 CPU 重排了读写顺序，从行为上来看，也和代码指定的顺序一样**。这是一个非常重要的保证，我们一定要记住。我们来看一个例子。在下面的代码中，即使编译器或者 CPU 对 a、b、c 的初始化进行了重排，但是打印结果依然能保证是 1、2、3，而不会出现 1、0、0 或 1、0、1 等情况。

```go
func foo() {
    var a = 1
    var b = 2
    var c = 3

    println(a)
    println(b)
    println(c)
}
```
但是，对于另一个 goroutine 来说，重排却会产生非常大的影响。因为 Go 只保证 goroutine 内部重排对读写的顺序没有影响，比如刚刚我们在讲“可见性”问题时提到的三个例子，那该怎么办呢？这就要用到 happens-before 关系了。

如果两个 action（read 或者 write）有明确的 happens-before 关系，你就可以确定它们之间的执行顺序（或者是行为表现上的顺序）。Go 内存模型通过 happens-before 定义两个事件（读、写 action）的顺序：如果事件 e1  happens before 事件 e2，那么，我们就可以说事件 e2 在事件 e1 之后发生（happens after）。如果 e1 不是 happens before e2， 同时也不 happens after e2，那么，我们就可以说事件 e1 和 e2 是同时发生的。如果要保证对“变量 v 的读操作 r”能够观察到一个对“变量 v 的写操作 w”，并且 r 只能观察到 w 对变量 v 的写，没有其它对 v 的写操作，也就是说，我们要保证 r 绝对能观察到 w 操作的结果，那么就需要同时满足两个条件：

w happens before r；其它对 v 的写操作（w2、w3、w4, ......） 要么 happens before w，要么 happens after r，绝对不会和 w、r 同时发生，或者是在它们之间发生。

你可能会说，这是很显然的事情啊，但我要和你说的是，这是一个非常严格、严谨的数学定义。对于单个的 goroutine 来说，它有一个特殊的 happens-before 关系，Go 内存模型中是这么讲的：

Within a single goroutine, the happens-before order is the order expressed by the program.

我来解释下这句话。它的意思是，在单个的 goroutine 内部， happens-before 的关系和代码编写的顺序是一致的。其实，在这一章的开头我已经用橙色把这句话标注出来了。我再具体解释下。

在 goroutine 内部对一个局部变量 v 的读，一定能观察到最近一次对这个局部变量 v 的写。如果要保证多个 goroutine 之间对一个共享变量的读写顺序，在 Go 语言中，可以使用并发原语为读写操作建立 happens-before 关系，这样就可以保证顺序了。说到这儿，我想先给你补充三个 Go 语言中和内存模型有关的小知识，掌握了这些，你就能更好地理解下面的内容。

在 Go 语言中，对变量进行零值的初始化就是一个写操作。如果对超过机器 word（64bit、32bit 或者其它）大小的值进行读写，那么，就可以看作是对拆成 word 大小的几个读写无序进行。Go 并不提供直接的 CPU 屏障（CPU fence）来提示编译器或者 CPU 保证顺序性，而是使用不同架构的内存屏障指令来实现统一的并发原语。

接下来，我就带你学习下 Go 语言中提供的 happens-before 关系保证。

### Go 语言中保证的 happens-before 关系

除了单个 goroutine 内部提供的 happens-before 保证，Go 语言中还提供了一些其它的 happens-before 关系的保证，下面我来一个一个介绍下。



#### init 函数

应用程序的初始化是在单一的 goroutine 执行的。如果包 p 导入了包 q，那么，q 的 init 函数的执行一定 happens before  p 的任何初始化代码。这里有一个特殊情况需要你记住：main 函数一定在导入的包的 init 函数之后执行。包级别的变量在同一个文件中是按照声明顺序逐个初始化的，除非初始化它的时候依赖其它的变量。同一个包下的多个文件，会按照文件名的排列顺序进行初始化。这个顺序被定义在[Go 语言规范](https://go.dev/ref/spec#Program_initialization_and_execution)中，而不是 Go 的内存模型规范中。你可以看看下面的例子中各个变量的值：

```
var (
  a = c + b  // == 9
  b = f()    // == 4
  c = f()    // == 5
  d = 3      // == 5 全部初始化完成后
)

func f() int {
  d++
  return d
}
```
具体怎么对这些变量进行初始化呢？Go 采用的是依赖分析技术。不过，依赖分析技术保证的顺序只是针对同一包下的变量，而且，只有引用关系是本包变量、函数和非接口的方法，才能保证它们的顺序性。同一个包下可以有多个 init 函数，甚至一个文件中也可以包含多个相同签名的 init 函数。刚刚讲的这些都是不同包的 init 函数执行顺序，下面我举一个具体的例子，把这些内容串起来，你一看就明白了。这个例子是一个 main 程序，它依赖包 p1，包 p1 依赖包 p2，包 p2 依赖 p3。

![img](https://static001.geekbang.org/resource/image/d5/2a/d5059fab1977602934339e18f9eddb2a.jpg?wh=3214*1645)

为了追踪初始化过程，并输出有意义的日志，我定义了一个辅助方法，打印出日志并返回一个用来初始化的整数值：

```go
func Trace(t string, v int) int {
    fmt.Println(t, ":", v)
    return v
}
```
包 p3 包含两个文件，分别定义了一个 init 函数。第一个文件中定义了两个变量，这两个变量的值还会在 init 函数中进行修改。我们来分别看下包 p3 的这两个文件：

```
// lib1.go in p3

var V1_p3 = trace.Trace("init v1_p3", 3)
var V2_p3 = trace.Trace("init v2_p3", 3)


func init() {
    fmt.Println("init func in p3")
    V1_p3 = 300
    V2_p3 = 300
}
```
```
// lib2.go in p3

func init() {
    fmt.Println("another init func in p3")
}
```
下面再来看看包 p2。包 p2 定义了变量和 init 函数。第一个变量初始化为 2，并在 init 函数中更改为 200。第二个变量是复制的 p3.V2_p3。

```
var V1_p2 = trace.Trace("init v1_p2", 2)
var V2_p2 = trace.Trace("init v2_p2", p3.V2_p3)

func init() {
    fmt.Println("init func in p2")
    V1_p2 = 200
}
```
包 p1 定义了变量和 init 函数。它的两个变量的值是复制的 p2 对应的两个变量值。

```
var V1_p1 = trace.Trace("init v1_p1", p2.V1_p2)
var V2_p1 = trace.Trace("init v2_p1", p2.V2_p2)

func init() {
    fmt.Println("init func in p1")
}
```
main 定义了 init 函数和 main 函数。

```go
func init() {
    fmt.Println("init func in main")
}


func main() {
    fmt.Println("V1_p1:", p1.V1_p1)
    fmt.Println("V2_p1:", p1.V2_p1)
}
```
运行 main 函数会依次输出 p3、p2、p1、main 的初始化变量时的日志（变量初始化时的日志和 init 函数调用时的日志）：

```
// 包p3的变量初始化
init v1_p3 : 3
init v2_p3 : 3
// p3的init函数
init func in p3
// p3的另一个init函数 
another init func in p3

// 包p2的变量初始化
init v1_p2 : 2
init v2_p2 : 300
// 包p2的init函数
init func in p2

// 包p1的变量初始化
init v1_p1 : 200
init v2_p1 : 300
// 包p1的init函数
init func in p1

// 包main的init函数
init func in main
// main函数
V1_p1: 200
V2_p1: 300
```
下面，我们再来看看 goroutine 对 happens-before 关系的保证情况。

### goroutine

首先，我们需要明确一个规则：启动 goroutine 的 go 语句的执行，一定 happens before 此 goroutine 内的代码执行。

根据这个规则，我们就可以知道，如果 go 语句传入的参数是一个函数执行的结果，那么，这个函数一定先于 goroutine 内部的代码被执行。我们来看一个例子。在下面的代码中，第 8 行 a 的赋值和第 9 行的 go 语句是在同一个 goroutine 中执行的，所以，在主 goroutine 看来，第 8 行肯定 happens before 第 9 行，又由于刚才的保证，第 9 行子 goroutine 的启动 happens before 第 4 行的变量输出，那么，我们就可以推断出，第 8 行 happens before 第 4 行。也就是说，在第 4 行打印 a 的值的时候，肯定会打印出“hello world”。

```
var a string

func f() {
  print(a)
}

func hello() {
  a = "hello, world"
  go f()
}
```
刚刚说的是启动 goroutine 的情况，goroutine 退出的时候，是没有任何 happens-before 保证的。所以，如果你想观察某个 goroutine 的执行效果，你需要使用同步机制建立 happens-before 关系，比如 Mutex 或者 Channel。接下来，我会讲 Channel 的 happens-before 的关系保证。

### Channel

Channel 是 goroutine 同步交流的主要方法。往一个 Channel 中发送一条数据，通常对应着另一个 goroutine 从这个 Channel 中接收一条数据。通用的 Channel happens-before 关系保证有 4 条规则，我分别来介绍下。第 1 条规则是，往 Channel 中的发送操作，happens before 从该 Channel 接收相应数据的动作完成之前，即第 n 个 send 一定 happens before 第 n 个 receive 的完成。

```
var ch = make(chan struct{}, 10) // buffered或者unbuffered
var s string

func f() {
  s = "hello, world"
  ch <- struct{}{}
}

func main() {
  go f()
  <-ch
  print(s)
}
```
在这个例子中，s 的初始化（第 5 行）happens before 往 ch 中发送数据， 往 ch 发送数据 happens before 从 ch 中读取出一条数据（第 11 行），第 12 行打印 s 的值 happens after 第 11 行，所以，打印的结果肯定是初始化后的 s 的值“hello world”。

第 2 条规则是，close 一个 Channel 的调用，肯定 happens before 从关闭的 Channel 中读取出一个零值。还是拿刚刚的这个例子来说，如果你把第 6 行替换成 close(ch)，也能保证同样的执行顺序。因为第 11 行从关闭的 ch 中读取出零值后，第 6 行肯定被调用了。

第 3 条规则是，对于 unbuffered 的 Channel，也就是容量是 0 的 Channel，从此 Channel 中读取数据的调用一定 happens before 往此 Channel 发送数据的调用完成。所以，在上面的这个例子中呢，如果想保持同样的执行顺序，也可以写成这样：

```
var ch = make(chan int)
var s string

func f() {
  s = "hello, world"
  <-ch
}

func main() {
  go f()
  ch <- struct{}{}
  print(s)
}
```
如果第 11 行发送语句执行成功（完毕），那么根据这个规则，第 6 行（接收）的调用肯定发生了（执行完成不完成不重要，重要的是这一句“肯定执行了”），那么 s 也肯定初始化了，所以一定会打印出“hello world”。这一条比较晦涩，但是，因为 Channel 是 unbuffered 的 Channel，所以这个规则也成立。

第 4 条规则是，如果 Channel 的容量是 m（m>0），那么，第 n 个 receive 一定 happens before 第 n+m 个 send 的完成。前一条规则是针对 unbuffered channel 的，这里给出了更广泛的针对 buffered channel 的保证。利用这个规则，我们可以实现信号量（Semaphore）的并发原语。Channel 的容量相当于可用的资源，发送一条数据相当于请求信号量，接收一条数据相当于释放信号。关于信号量这个并发原语，我会在下一讲专门给你介绍一下，这里你只需要知道它可以控制多个资源的并发访问，就可以了。



### Mutex/RWMutex

对于互斥锁 Mutex m 或者读写锁 RWMutex m，有 3 条 happens-before 关系的保证。

1. 第 n 次的 m.Unlock 一定 happens before 第 n+1 m.Lock 方法的返回；
2. 对于读写锁 RWMutex m，如果它的第 n 个 m.Lock 方法的调用已返回，那么它的第 n 个 m.Unlock 的方法调用一定 happens before 任何一个 m.RLock 方法调用的返回，只要这些 m.RLock 方法调用 happens after 第 n 次 m.Lock 的调用的返回。这就可以保证，只有释放了持有的写锁，那些等待的读请求才能请求到读锁。
3. 对于读写锁 RWMutex m，如果它的第 n 个 m.RLock 方法的调用已返回，那么它的第 k （k<=n）个成功的 m.RUnlock 方法的返回一定 happens before 任意的 m.RUnlockLock 方法调用，只要这些 m.Lock 方法调用 happens after 第 n 次 m.RLock。

读写锁的保证有点绕，我再带你看看官方的描述：

对于读写锁 l 的 l.RLock 方法调用，如果存在一个 n，这次的 l.RLock 调用 happens after 第 n 次的 l.Unlock，那么，和这个 RLock 相对应的 l.RUnlock 一定 happens before 第 n+1 次 l.Lock。意思是，读写锁的 Lock 必须等待既有的读锁释放后才能获取到。

我再举个例子。在下面的代码中，第 6 行第一次的 Unlock 一定 happens before 第二次的 Lock（第 12 行），所以这也能保证正确地打印出“hello world”。

```
var mu sync.Mutex
var s string

func foo() {
  s = "hello, world"
  mu.Unlock()
}

func main() {
  mu.Lock()
  go foo()
  mu.Lock()
  print(s)
```


### WaitGroup

接下来是 WaitGroup 的保证。对于一个 WaitGroup 实例 wg，在某个时刻 t0 时，它的计数值已经不是零了，假如 t0 时刻之后调用了一系列的 wg.Add(n) 或者 wg.Done()，并且只有最后一次调用 wg 的计数值变为了 0，那么，可以保证这些 wg.Add 或者 wg.Done() 一定 happens before t0 时刻之后调用的 wg.Wait 方法的返回。这个保证的通俗说法，就是 Wait 方法等到计数值归零之后才返回。

### Once

我们在第 8 讲学过 Once 了，相信你已经很熟悉它的功能了。它提供的保证是：对于 once.Do(f) 调用，f 函数的那个单次调用一定 happens before 任何 once.Do(f) 调用的返回。换句话说，就是函数 f 一定会在 Do 方法返回之前执行。还是以 hello world 的例子为例，这次我们使用 Once 并发原语实现，可以看下下面的代码：

```
var s string
var once sync.Once

func foo() {
  s = "hello, world"
}

func twoprint() {
  once.Do(foo)
  print(s)
}
```
第 5 行的执行一定 happens before 第 9 行的返回，所以执行到第 10 行的时候，sd 已经初始化了，所以会正确地打印“hello world”。最后，我再来说说 atomic 的保证。

### atomic

其实，Go 内存模型的官方文档并没有明确给出 atomic 的保证，有一个相关的 issue go# 5045记录了相关的讨论。光看 issue 号，就知道这个讨论由来已久了。Russ Cox 想让 atomic 有一个弱保证，这样可以为以后留下充足的可扩展空间，所以，Go 内存模型规范上并没有严格的定义。对于 Go 1.15 的官方实现来说，可以保证使用 atomic 的 Load/Store 的变量之间的顺序性。在下面的例子中，打印出的 a 的结果总是 1，但是官方并没有做任何文档上的说明和保证。依照 Ian Lance Taylor 的说法，Go 核心开发组的成员几乎没有关注这个方向上的研究，因为这个问题太复杂，有很多问题需要去研究，所以，现阶段还是不要使用 atomic 来保证顺序性。

```go
func main() {
  var a, b int32 = 0, 0

  go func() {
    atomic.StoreInt32(&a, 1)
    atomic.StoreInt32(&b, 1)
  }()

  for atomic.LoadInt32(&b) == 0{
    runtime.Gosched()
  }
    fmt.Println(atomic.LoadInt32(&a))
}
```
### 总结

Go 的内存模型规范中，一开始有这么一段话：

If you must read the rest of this document to understand the behavior of your program, you are being too clever.Don't be clever.

我来说说我对这句话的理解：你通过学习这节课来理解你的程序的行为是聪明的，但是，不要自作聪明。谨慎地使用这些保证，能够让你的程序按照设想的 happens-before 关系执行，但是不要以为完全理解这些概念和保证，就可以随意地制造所谓的各种技巧，否则就很容易掉进“坑”里，而且会给代码埋下了很多的“定时炸弹”。比如，Go 里面已经有值得信赖的互斥锁了，如果没有额外的需求，就不要使用 Channel 创造出自己的互斥锁。当然，我也不希望你畏手畏脚地把思想局限住，我还是建议你去做一些有意义的尝试，比如使用 Channel 实现信号量等扩展并发原语。

![img](https://static001.geekbang.org/resource/image/dc/4d/dc68fc5f93a4af96c8f4d45d6282104d.jpg?wh=2250*2046)


## 拓展并发原语

## 16 | Semaphore：一篇文章搞懂信号量

在前面的课程里，我们学习了标准库的并发原语、原子操作和 Channel，掌握了这些，你就可以解决 80% 的并发编程问题了。但是，如果你要想进一步提升你的并发编程能力，就需要学习一些第三方库。所以，在接下来的几节课里，我会给你分享 Go 官方或者其他人提供的第三方库，这节课我们先来学习信号量，信号量（Semaphore）是用来控制多个 goroutine 同时访问多个资源的并发原语。

### 信号量是什么？都有什么操作？

信号量的概念是荷兰计算机科学家 Edsger Dijkstra 在 1963 年左右提出来的，广泛应用在不同的操作系统中。在系统中，会给每一个进程一个信号量，代表每个进程目前的状态。未得到控制权的进程，会在特定的地方被迫停下来，等待可以继续进行的信号到来。最简单的信号量就是一个变量加一些并发控制的能力，这个变量是 0 到 n 之间的一个数值。当 goroutine 完成对此信号量的等待（wait）时，该计数值就减 1，当 goroutine 完成对此信号量的释放（release）时，该计数值就加 1。当计数值为 0 的时候，goroutine 调用 wait 等待该信号量是不会成功的，除非计数器又大于 0，等待的 goroutine 才有可能成功返回。更复杂的信号量类型，就是使用抽象数据类型代替变量，用来代表复杂的资源类型。实际上，大部分的信号量都使用一个整型变量来表示一组资源，并没有实现太复杂的抽象数据类型，所以你只要知道有更复杂的信号量就行了，我们这节课主要是学习最简单的信号量。说到这儿呢，我想借助一个生活中的例子，来帮你进一步理解信号量。

举个例子，图书馆新购买了 10 本《Go 并发编程的独家秘籍》，有 1 万个学生都想读这本书，“僧多粥少”。所以，图书馆管理员先会让这 1 万个同学进行登记，按照登记的顺序，借阅此书。如果书全部被借走，那么，其他想看此书的同学就需要等待，如果有人还书了，图书馆管理员就会通知下一位同学来借阅这本书。这里的资源是《Go 并发编程的独家秘籍》这十本书，想读此书的同学就是 goroutine，图书管理员就是信号量。怎么样，现在是不是很好理解了？那么，接下来，我们来学习下信号量的 P/V 操作。

### P/V 操作

Dijkstra 在他的论文中为信号量定义了两个操作 P 和 V。P 操作（descrease、wait、acquire）是减少信号量的计数值，而 V 操作（increase、signal、release）是增加信号量的计数值。使用伪代码表示如下（中括号代表原子操作）：

```
function V(semaphore S, integer I):
    [S ← S + I]

function P(semaphore S, integer I):
    repeat:
        [if S ≥ I:
        S ← S − I
        break]
```


可以看到，初始化信号量 S 有一个指定数量（n）的资源，它就像是一个有 n 个资源的池子。P 操作相当于请求资源，如果资源可用，就立即返回；如果没有资源或者不够，那么，它可以不断尝试或者阻塞等待。V 操作会释放自己持有的资源，把资源返还给信号量。信号量的值除了初始化的操作以外，只能由 P/V 操作改变。现在，我们来总结下信号量的实现。

初始化信号量：设定初始的资源的数量。P 操作：将信号量的计数值减去 1，如果新值已经为负，那么调用者会被阻塞并加入到等待队列中。否则，调用者会继续执行，并且获得一个资源。V 操作：将信号量的计数值加 1，如果先前的计数值为负，就说明有等待的 P 操作的调用者。它会从等待队列中取出一个等待的调用者，唤醒它，让它继续执行。

讲到这里，我想再稍微说一个题外话，我们在第 2 讲提到过饥饿，就是说在高并发的极端场景下，会有些 goroutine 始终抢不到锁。为了处理饥饿的问题，你可以在等待队列中做一些“文章”。比如实现一个优先级的队列，或者先入先出的队列，等等，保持公平性，并且照顾到优先级。在正式进入实现信号量的具体实现原理之前，我想先讲一个知识点，就是信号量和互斥锁的区别与联系，这有助于我们掌握接下来的内容。其实，信号量可以分为计数信号量（counting semaphore）和二进位信号量（binary semaphore）。刚刚所说的图书馆借书的例子就是一个计数信号量，它的计数可以是任意一个整数。在特殊的情况下，如果计数值只能是 0 或者 1，那么，这个信号量就是二进位信号量，提供了互斥的功能（要么是 0，要么是 1），所以，有时候互斥锁也会使用二进位信号量来实现。我们一般用信号量保护一组资源，比如数据库连接池、一组客户端的连接、几个打印机资源，等等。如果信号量蜕变成二进位信号量，那么，它的 P/V 就和互斥锁的 Lock/Unlock 一样了。有人会很细致地区分二进位信号量和互斥锁。比如说，有人提出，在 Windows 系统中，互斥锁只能由持有锁的线程释放锁，而二进位信号量则没有这个限制（Stack Overflow上也有相关的讨论）。实际上，虽然在 Windows 系统中，它们的确有些区别，但是对 Go 语言来说，互斥锁也可以由非持有的 goroutine 来释放，所以，从行为上来说，它们并没有严格的区别。我个人认为，没必要进行细致的区分，因为互斥锁并不是一个很严格的定义。实际在遇到互斥并发的问题时，我们一般选用互斥锁。好了，言归正传，刚刚我们掌握了信号量的含义和具体操作方式，下面，我们就来具体了解下官方扩展库的实现。

### Go 官方扩展库的实现

在运行时，Go 内部使用信号量来控制 goroutine 的阻塞和唤醒。我们在学习基本并发原语的实现时也看到了，比如互斥锁的第二个字段：

```
type Mutex struct {
    state int32
    sema  uint32
}
```
信号量的 P/V 操作是通过函数实现的：

```go
func runtime_Semacquire(s *uint32)
func runtime_SemacquireMutex(s *uint32, lifo bool, skipframes int)
func runtime_Semrelease(s *uint32, handoff bool, skipframes int)
```
遗憾的是，它是 Go 运行时内部使用的，并没有封装暴露成一个对外的信号量并发原语，原则上我们没有办法使用。不过没关系，Go 在它的扩展包中提供了信号量[semaphore](https://godoc.org/golang.org/x/sync/semaphore)，不过这个信号量的类型名并不叫 Semaphore，而是叫 Weighted。之所以叫做 Weighted，我想，应该是因为可以在初始化创建这个信号量的时候设置权重（初始化的资源数），其实我觉得叫 Semaphore 或许会更好。

![img](https://static001.geekbang.org/resource/image/1a/b0/1a13a551346cd6b910f38f5ed2bfc6b0.png?wh=702*174)



我们来分析下这个信号量的几个实现方法。

Acquire 方法：相当于 P 操作，你可以一次获取多个资源，如果没有足够多的资源，调用者就会被阻塞。它的第一个参数是 Context，这就意味着，你可以通过 Context 增加超时或者 cancel 的机制。如果是正常获取了资源，就返回 nil；否则，就返回 ctx.Err()，信号量不改变。Release 方法：相当于 V 操作，可以将 n 个资源释放，返还给信号量。TryAcquire 方法：尝试获取 n 个资源，但是它不会阻塞，要么成功获取 n 个资源，返回 true，要么一个也不获取，返回 false。

知道了信号量的实现方法，在实际的场景中，我们应该怎么用呢？我来举个 Worker Pool 的例子，来帮助你理解。我们创建和 CPU 核数一样多的 Worker，让它们去处理一个 4 倍数量的整数 slice。每个 Worker 一次只能处理一个整数，处理完之后，才能处理下一个。当然，这个问题的解决方案有很多种，这一次我们使用信号量，代码如下：

```
var (
    maxWorkers = runtime.GOMAXPROCS(0)                    // worker数量
    sema       = semaphore.NewWeighted(int64(maxWorkers)) //信号量
    task       = make([]int, maxWorkers*4)                // 任务数，是worker的四倍
)

func main() {
    ctx := context.Background()

    for i := range task {
        // 如果没有worker可用，会阻塞在这里，直到某个worker被释放
        if err := sema.Acquire(ctx, 1); err != nil {
            break
        }

        // 启动worker goroutine
        go func(i int) {
            defer sema.Release(1)
            time.Sleep(100 * time.Millisecond) // 模拟一个耗时操作
            task[i] = i + 1
        }(i)
    }

    // 请求所有的worker,这样能确保前面的worker都执行完
    if err := sema.Acquire(ctx, int64(maxWorkers)); err != nil {
        log.Printf("获取所有的worker失败: %v", err)
    }

    fmt.Println(task)
}
```


在这段代码中，main goroutine 相当于一个 dispatcher，负责任务的分发。它先请求信号量，如果获取成功，就会启动一个 goroutine 去处理计算，然后，这个 goroutine 会释放这个信号量（有意思的是，信号量的获取是在 main goroutine，信号量的释放是在 worker goroutine 中），如果获取不成功，就等到有信号量可以使用的时候，再去获取。需要提醒你的是，其实，在这个例子中，还有一个值得我们学习的知识点，就是最后的那一段处理（第 25 行）。**如果在实际应用中，你想等所有的 Worker 都执行完，就可以获取最大计数值的信号量。**

Go 扩展库中的信号量是使用互斥锁 +List 实现的。互斥锁实现其它字段的保护，而 List 实现了一个等待队列，等待者的通知是通过 Channel 的通知机制实现的。我们来看一下信号量 Weighted 的数据结构：

```
type Weighted struct {
    size    int64         // 最大资源数
    cur     int64         // 当前已被使用的资源
    mu      sync.Mutex    // 互斥锁，对字段的保护
    waiters list.List     // 等待队列
}
```
在信号量的几个实现方法里，Acquire 是代码最复杂的一个方法，它不仅仅要监控资源是否可用，而且还要检测 Context 的 Done 是否已关闭。我们来看下它的实现代码。

```go
func (s *Weighted) Acquire(ctx context.Context, n int64) error {
    s.mu.Lock()
        // fast path, 如果有足够的资源，都不考虑ctx.Done的状态，将cur加上n就返回
    if s.size-s.cur >= n && s.waiters.Len() == 0 {
      s.cur += n
      s.mu.Unlock()
      return nil
    }
  
        // 如果是不可能完成的任务，请求的资源数大于能提供的最大的资源数
    if n > s.size {
      s.mu.Unlock()
            // 依赖ctx的状态返回，否则一直等待
      <-ctx.Done()
      return ctx.Err()
    }
  
        // 否则就需要把调用者加入到等待队列中
        // 创建了一个ready chan,以便被通知唤醒
    ready := make(chan struct{})
    w := waiter{n: n, ready: ready}
    elem := s.waiters.PushBack(w)
    s.mu.Unlock()
  

        // 等待
    select {
    case <-ctx.Done(): // context的Done被关闭
      err := ctx.Err()
      s.mu.Lock()
      select {
      case <-ready: // 如果被唤醒了，忽略ctx的状态
        err = nil
      default: 通知waiter
        isFront := s.waiters.Front() == elem
        s.waiters.Remove(elem)
        // 通知其它的waiters,检查是否有足够的资源
        if isFront && s.size > s.cur {
          s.notifyWaiters()
        }
      }
      s.mu.Unlock()
      return err
    case <-ready: // 被唤醒了
      return nil
    }
  }
```
其实，为了提高性能，这个方法中的 fast path 之外的代码，可以抽取成 acquireSlow 方法，以便其它 Acquire 被内联。Release 方法将当前计数值减去释放的资源数 n，并唤醒等待队列中的调用者，看是否有足够的资源被获取。

```go
func (s *Weighted) Release(n int64) {
    s.mu.Lock()
    s.cur -= n
    if s.cur < 0 {
      s.mu.Unlock()
      panic("semaphore: released more than held")
    }
    s.notifyWaiters()
    s.mu.Unlock()
}
```
notifyWaiters 方法就是逐个检查等待的调用者，如果资源不够，或者是没有等待者了，就返回：

```go
func (s *Weighted) notifyWaiters() {
    for {
      next := s.waiters.Front()
      if next == nil {
        break // No more waiters blocked.
      }
  

      w := next.Value.(waiter)
      if s.size-s.cur < w.n {
        //避免饥饿，这里还是按照先入先出的方式处理
        break
      }

      s.cur += w.n
      s.waiters.Remove(next)
      close(w.ready)
    }
  }
```
notifyWaiters 方法是按照先入先出的方式唤醒调用者。当释放 100 个资源的时候，如果第一个等待者需要 101 个资源，那么，队列中的所有等待者都会继续等待，即使有的等待者只需要 1 个资源。这样做的目的是避免饥饿，否则的话，资源可能总是被那些请求资源数小的调用者获取，这样一来，请求资源数巨大的调用者，就没有机会获得资源了。好了，到这里，你就知道了官方扩展库的信号量实现方法，接下来你就可以使用信号量了。不过，在此之前呢，我想给你讲几个使用时的常见错误。这部分内容可是帮助你避坑的，我建议你好好学习。



### 使用信号量的常见错误

保证信号量不出错的前提是正确地使用它，否则，公平性和安全性就会受到损害，导致程序 panic。在使用信号量时，最常见的几个错误如下：

请求了资源，但是忘记释放它；释放了从未请求的资源；长时间持有一个资源，即使不需要它；不持有一个资源，却直接使用它。

不过，即使你规避了这些坑，在同时使用多种资源，不同的信号量控制不同的资源的时候，也可能会出现死锁现象，比如哲学家就餐问题。就 Go 扩展库实现的信号量来说，在调用 Release 方法的时候，你可以传递任意的整数。但是，如果你传递一个比请求到的数量大的错误的数值，程序就会 panic。如果传递一个负数，会导致资源永久被持有。如果你请求的资源数比最大的资源数还大，那么，调用者可能永远被阻塞。所以，**使用信号量遵循的原则就是请求多少资源，就释放多少资源**。你一定要注意，必须使用正确的方法传递整数，不要“耍小聪明”，而且，请求的资源数一定不要超过最大资源数。

### 其它信号量的实现

除了官方扩展库的实现，实际上，我们还有很多方法实现信号量，比较典型的就是使用 Channel 来实现。根据之前的 Channel 类型的介绍以及 Go 内存模型的定义，你应该能想到，使用一个 buffer 为 n 的 Channel 很容易实现信号量，比如下面的代码，我们就是使用 chan struct{}类型来实现的。在初始化这个信号量的时候，我们设置它的初始容量，代表有多少个资源可以使用。它使用 Lock 和 Unlock 方法实现请求资源和释放资源，正好实现了 Locker 接口。

```
  // Semaphore 数据结构，并且还实现了Locker接口
  type semaphore struct {
    sync.Locker
    ch chan struct{}
  }
  
  // 创建一个新的信号量
  func NewSemaphore(capacity int) sync.Locker {
    if capacity <= 0 {
      capacity = 1 // 容量为1就变成了一个互斥锁
    }
    return &semaphore{ch: make(chan struct{}, capacity)}
  }
  
  // 请求一个资源
  func (s *semaphore) Lock() {
    s.ch <- struct{}{}
  }
  
  // 释放资源
  func (s *semaphore) Unlock() {
    <-s.ch
  }
```
当然，你还可以自己扩展一些方法，比如在请求资源的时候使用 Context 参数（Acquire(ctx)）、实现 TryLock 等功能。看到这里，你可能会问，这个信号量的实现看起来非常简单，而且也能应对大部分的信号量的场景，为什么官方扩展库的信号量的实现不采用这种方法呢？其实，具体是什么原因，我也不知道，但是我必须要强调的是，官方的实现方式有这样一个功能：**它可以一次请求多个资源，这是通过 Channel 实现的信号量所不具备的。**

除了 Channel，**marusama/semaphore**也实现了一个可以动态更改资源容量的信号量，也是一个非常有特色的实现。如果你的资源数量并不是固定的，而是动态变化的，我建议你考虑一下这个信号量库。

### 总结

这是一个很奇怪的现象：标准库中实现基本并发原语（比如 Mutex）的时候，强烈依赖信号量实现等待队列和通知唤醒，但是，标准库中却没有把这个实现直接暴露出来放到标准库，而是通过第三库提供。不管怎样，信号量这个并发原语在多资源共享的并发控制的场景中被广泛使用，有时候也会被 Channel 类型所取代，因为一个 buffered chan 也可以代表 n 个资源。但是，官方扩展的信号量也有它的优势，就是可以一次获取多个资源。**在批量获取资源的场景中，我建议你尝试使用官方扩展的信号量。**

![img](https://static001.geekbang.org/resource/image/67/73/674bc464d4e3d11c96fa1ac71d317e73.jpg?wh=2250*1413)



## 17 | SingleFlight 和 CyclicBarrier：请求合并和循环栅栏该怎么用？

这节课，我来给你介绍两个非常重要的扩展并发原语：SingleFlight 和 CyclicBarrier。SingleFlight 的作用是将并发请求合并成一个请求，以减少对下层服务的压力；而 CyclicBarrier 是一个可重用的栅栏并发原语，用来控制一组请求同时执行的数据结构。其实，它们两个并没有直接的关系，只是内容相对来说比较少，所以我打算用最短的时间带你掌握它们。一节课就能掌握两个“武器”，是不是很高效？

### 请求合并 SingleFlight

SingleFlight 是 Go 开发组提供的一个扩展并发原语。它的作用是，在处理多个 goroutine 同时调用同一个函数的时候，只让一个 goroutine 去调用这个函数，等到这个 goroutine 返回结果的时候，再把结果返回给这几个同时调用的 goroutine，这样可以减少并发调用的数量。这里我想先回答一个问题：标准库中的 sync.Once 也可以保证并发的 goroutine 只会执行一次函数 f，那么，SingleFlight 和 sync.Once 有什么区别呢？其实，sync.Once 不是只在并发的时候保证只有一个 goroutine 执行函数 f，而是会保证永远只执行一次，而 SingleFlight 是每次调用都重新执行，并且在多个请求同时调用的时候只有一个执行。它们两个面对的场景是不同的，**sync.Once 主要是用在单次初始化场景中，而 SingleFlight 主要用在合并并发请求的场景中，尤其是缓存场景。**

如果你学会了 SingleFlight，在面对秒杀等大并发请求的场景，而且这些请求都是读请求时，你就可以把这些请求合并为一个请求，这样，你就可以将后端服务的压力从 n 降到 1。尤其是在面对后端是数据库这样的服务的时候，采用 SingleFlight 可以极大地提高性能。那么，话不多说，就让我们开始学习 SingleFlight 吧。

### 实现原理

SingleFlight 使用互斥锁 Mutex 和 Map 来实现。Mutex 提供并发时的读写保护，Map 用来保存同一个 key 的正在处理（in flight）的请求。SingleFlight 的数据结构是 Group，它提供了三个方法。

![img](https://static001.geekbang.org/resource/image/2a/da/2a260ccce4e06ea1be2cf3f7abbe84da.png?wh=1000*123)

Do：这个方法执行一个函数，并返回函数执行的结果。你需要提供一个 key，对于同一个 key，在同一时间只有一个在执行，同一个 key 并发的请求会等待。第一个执行的请求返回的结果，就是它的返回结果。函数 fn 是一个无参的函数，返回一个结果或者 error，而 Do 方法会返回函数执行的结果或者是 error，shared 会指示 v 是否返回给多个请求。DoChan：类似 Do 方法，只不过是返回一个 chan，等 fn 函数执行完，产生了结果以后，就能从这个 chan 中接收这个结果。Forget：告诉 Group 忘记这个 key。这样一来，之后这个 key 请求会执行 f，而不是等待前一个未完成的 fn 函数的结果。

下面，我们来看具体的实现方法。首先，SingleFlight 定义一个辅助对象 call，这个 call 就代表正在执行 fn 函数的请求或者是已经执行完的请求。Group 代表 SingleFlight。

```go
  // 代表一个正在处理的请求，或者已经处理完的请求
  type call struct {
    wg sync.WaitGroup
  

    // 这个字段代表处理完的值，在waitgroup完成之前只会写一次
        // waitgroup完成之后就读取这个值
    val interface{}
    err error
  
        // 指示当call在处理时是否要忘掉这个key
    forgotten bool
    dups  int
    chans []chan<- Result
  }
  
    // group代表一个singleflight对象
  type Group struct {
    mu sync.Mutex       // protects m
    m  map[string]*call // lazily initialized
  }
```
我们只需要查看一个 Do 方法，DoChan 的处理方法是类似的。

```go
  func (g *Group) Do(key string, fn func() (interface{}, error)) (v interface{}, err error, shared bool) {
    g.mu.Lock()
    if g.m == nil {
      g.m = make(map[string]*call)
    }
    if c, ok := g.m[key]; ok {//如果已经存在相同的key
      c.dups++
      g.mu.Unlock()
      c.wg.Wait() //等待这个key的第一个请求完成
      return c.val, c.err, true //使用第一个key的请求结果
    }
    c := new(call) // 第一个请求，创建一个call
    c.wg.Add(1)
    g.m[key] = c //加入到key map中
    g.mu.Unlock()
  

    g.doCall(c, key, fn) // 调用方法
    return c.val, c.err, c.dups > 0
  }
```
doCall 方法会实际调用函数 fn：

```go
  func (g *Group) doCall(c *call, key string, fn func() (interface{}, error)) {
    c.val, c.err = fn()
    c.wg.Done()
  

    g.mu.Lock()
    if !c.forgotten { // 已调用完，删除这个key
      delete(g.m, key)
    }
    for _, ch := range c.chans {
      ch <- Result{c.val, c.err, c.dups > 0}
    }
    g.mu.Unlock()
  }
```
在这段代码中，你要注意下第 7 行。在默认情况下，forgotten==false，所以第 8 行默认会被调用，也就是说，第一个请求完成后，后续的同一个 key 的请求又重新开始新一次的 fn 函数的调用。Go 标准库的代码中就有一个 SingleFlight 的[实现](https://github.com/golang/go/blob/50bd1c4d4eb4fac8ddeb5f063c099daccfb71b26/src/internal/singleflight/singleflight.go)，而扩展库中的 SingleFlight 就是在标准库的代码基础上改的，逻辑几乎一模一样，我就不多说了。

### 应用场景

了解了 SingleFlight 的实现原理，下面我们来看看它都应用于什么场景中。Go 代码库中有两个地方用到了 SingleFlight。第一个是在[net/lookup.go](net/lookup.go)中，如果同时有查询同一个 host 的请求，lookupGroup 会把这些请求 merge 到一起，只需要一个请求就可以了：

```
// lookupGroup merges LookupIPAddr calls together for lookups for the same
// host. The lookupGroup key is the LookupIPAddr.host argument.
// The return values are ([]IPAddr, error).
lookupGroup singleflight.Group
```
第二个是 Go 在查询仓库版本信息时，将并发的请求合并成 1 个请求：

```go
func metaImportsForPrefix(importPrefix string, mod ModuleMode, security web.SecurityMode) (*urlpkg.URL, []metaImport, error) {
        // 使用缓存保存请求结果
    setCache := func(res fetchResult) (fetchResult, error) {
      fetchCacheMu.Lock()
      defer fetchCacheMu.Unlock()
      fetchCache[importPrefix] = res
      return res, nil
    
        // 使用 SingleFlight请求
    resi, _, _ := fetchGroup.Do(importPrefix, func() (resi interface{}, err error) {
      fetchCacheMu.Lock()
            // 如果缓存中有数据，那么直接从缓存中取
      if res, ok := fetchCache[importPrefix]; ok {
        fetchCacheMu.Unlock()
        return res, nil
      }
      fetchCacheMu.Unlock()
            ......
```
需要注意的是，这里涉及到了缓存的问题。上面的代码会把结果放在缓存中，这也是常用的一种解决缓存击穿的例子。设计缓存问题时，我们常常需要解决缓存穿透、缓存雪崩和缓存击穿问题。缓存击穿问题是指，在平常高并发的系统中，大量的请求同时查询一个 key 时，如果这个 key 正好过期失效了，就会导致大量的请求都打到数据库上。这就是缓存击穿。用 SingleFlight 来解决缓存击穿问题再合适不过了。因为，这个时候，只要这些对同一个 key 的并发请求的其中一个到数据库中查询，就可以了，这些并发的请求可以共享同一个结果。因为是缓存查询，不用考虑幂等性问题。事实上，在 Go 生态圈知名的缓存框架 groupcache 中，就使用了较早的 Go 标准库的 SingleFlight 实现。接下来，我就来给你介绍一下 groupcache 是如何使用 SingleFlight 解决缓存击穿问题的。groupcache 中的 SingleFlight 只有一个方法：

```go
func (g *Group) Do(key string, fn func() (interface{}, error)) (interface{}, error)
```
SingleFlight 的作用是，在加载一个缓存项的时候，合并对同一个 key 的 load 的并发请求：

```
  type Group struct {
    。。。。。。
    // loadGroup ensures that each key is only fetched once
    // (either locally or remotely), regardless of the number of
    // concurrent callers.
    loadGroup flightGroup
        ......
  }

    func (g *Group) load(ctx context.Context, key string, dest Sink) (value ByteView, destPopulated bool, err error) {
    viewi, err := g.loadGroup.Do(key, func() (interface{}, error)  {
      // 从cache, peer, local尝试查询cache
      return value, nil
    })
    if err == nil {
      value = viewi.(ByteView)
    }
    return
  }
```
其它的知名项目如 Cockroachdb（小强数据库）、CoreDNS（DNS 服务器）等都有 SingleFlight 应用，你可以查看这些项目的代码，加深对 SingleFlight 的理解。总结来说，使用 SingleFlight 时，可以通过合并请求的方式降低对下游服务的并发压力，从而提高系统的性能，常常用于缓存系统中。最后，我想给你留一个思考题，你觉得，SingleFlight 能不能合并并发的写操作呢？

### 循环栅栏 CyclicBarrier

接下来，我再给你介绍另外一个并发原语：循环栅栏（CyclicBarrier），它常常应用于重复进行一组 goroutine 同时执行的场景中。[CyclicBarrier](https://github.com/marusama/cyclicbarrier)允许一组 goroutine 彼此等待，到达一个共同的执行点。同时，因为它可以被重复使用，所以叫循环栅栏。具体的机制是，大家都在栅栏前等待，等全部都到齐了，就抬起栅栏放行。

事实上，这个 CyclicBarrier 是参考[Java CyclicBarrier](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/util/concurrent/CyclicBarrier.html)和[C# Barrier](https://docs.microsoft.com/en-us/dotnet/api/system.threading.barrier?redirectedfrom=MSDN&view=netcore-3.1)的功能实现的。Java 提供了 CountDownLatch（倒计时器）和 CyclicBarrier（循环栅栏）两个类似的用于保证多线程到达同一个执行点的类，只不过前者是到达 0 的时候放行，后者是到达某个指定的数的时候放行。C# Barrier 功能也是类似的，你可以查看链接，了解它的具体用法。你可能会觉得，CyclicBarrier 和 WaitGroup 的功能有点类似，确实是这样。不过，CyclicBarrier 更适合用在“固定数量的 goroutine 等待同一个执行点”的场景中，而且在放行 goroutine 之后，CyclicBarrier 可以重复利用，不像 WaitGroup 重用的时候，必须小心翼翼避免 panic。处理可重用的多 goroutine 等待同一个执行点的场景的时候，CyclicBarrier 和 WaitGroup 方法调用的对应关系如下：

![img](https://static001.geekbang.org/resource/image/c2/79/c2123588d4aa9f7dedec7fc35435c679.jpg?wh=2486*1633)

可以看到，如果使用 WaitGroup 实现的话，调用比较复杂，不像 CyclicBarrier 那么清爽。更重要的是，如果想重用 WaitGroup，你还要保证，将 WaitGroup 的计数值重置到 n 的时候不会出现并发问题。WaitGroup 更适合用在“一个 goroutine 等待一组 goroutine 到达同一个执行点”的场景中，或者是不需要重用的场景中。好了，了解了 CyclicBarrier 的应用场景和功能，下面我们来学习下它的具体实现。

### 实现原理

CyclicBarrier 有两个初始化方法：

第一个是 New 方法，它只需要一个参数，来指定循环栅栏参与者的数量；第二个方法是 NewWithAction，它额外提供一个函数，可以在每一次到达执行点的时候执行一次。具体的时间点是在最后一个参与者到达之后，但是其它的参与者还未被放行之前。我们可以利用它，做放行之前的一些共享状态的更新等操作。

这两个方法的签名如下：

```go
func New(parties int) CyclicBarrier
func NewWithAction(parties int, barrierAction func() error) CyclicBarrier
```
CyclicBarrier 是一个接口，定义的方法如下

```
type CyclicBarrier interface {
    // 等待所有的参与者到达，如果被ctx.Done()中断，会返回ErrBrokenBarrier
    Await(ctx context.Context) error

    // 重置循环栅栏到初始化状态。如果当前有等待者，那么它们会返回ErrBrokenBarrier
    Reset()

    // 返回当前等待者的数量
    GetNumberWaiting() int

    // 参与者的数量
    GetParties() int

    // 循环栅栏是否处于中断状态
    IsBroken() bool
}
```
循环栅栏的使用也很简单。循环栅栏的参与者只需调用 Await 等待，等所有的参与者都到达后，再执行下一步。当执行下一步的时候，循环栅栏的状态又恢复到初始的状态了，可以迎接下一轮同样多的参与者。有一道非常经典的并发编程的题目，非常适合使用循环栅栏，下面我们来看一下。

### 并发趣题：一氧化二氢制造工厂

题目是这样的：有一个名叫大自然的搬运工的工厂，生产一种叫做一氧化二氢的神秘液体。这种液体的分子是由一个氧原子和两个氢原子组成的，也就是水。这个工厂有多条生产线，每条生产线负责生产氧原子或者是氢原子，每条生产线由一个 goroutine 负责。这些生产线会通过一个栅栏，只有一个氧原子生产线和两个氢原子生产线都准备好，才能生成出一个水分子，否则所有的生产线都会处于等待状态。也就是说，一个水分子必须由三个不同的生产线提供原子，而且水分子是一个一个按照顺序产生的，每生产一个水分子，就会打印出 HHO、HOH、OHH 三种形式的其中一种。HHH、OOH、OHO、HOO、OOO 都是不允许的。生产线中氢原子的生产线为 2N 条，氧原子的生产线为 N 条。

你可以先想一下，我们怎么来实现呢？首先，我们来定义一个 H2O 辅助数据类型，它包含两个信号量的字段和一个循环栅栏。

semaH 信号量：控制氢原子。一个水分子需要两个氢原子，所以，氢原子的空槽数资源数设置为 2。semaO 信号量：控制氧原子。一个水分子需要一个氧原子，所以资源数的空槽数设置为 1。循环栅栏：等待两个氢原子和一个氧原子填补空槽，直到任务完成。

我们来看下具体的代码：

```go
package water
import (
  "context"
  "github.com/marusama/cyclicbarrier"
  "golang.org/x/sync/semaphore"
)
// 定义水分子合成的辅助数据结构
type H2O struct {
  semaH *semaphore.Weighted // 氢原子的信号量
  semaO *semaphore.Weighted // 氧原子的信号量
  b     cyclicbarrier.CyclicBarrier // 循环栅栏，用来控制合成
}
func New() *H2O {
  return &H2O{
    semaH: semaphore.NewWeighted(2), //氢原子需要两个
    semaO: semaphore.NewWeighted(1), // 氧原子需要一个
    b:     cyclicbarrier.New(3),  // 需要三个原子才能合成
  }
}
```
接下来，我们看看各条流水线的处理情况。流水线分为氢原子处理流水线和氧原子处理流水线，首先，我们先看一下氢原子的流水线：如果有可用的空槽，氢原子的流水线的处理方法是 hydrogen，hydrogen 方法就会占用一个空槽（h2o.semaH.Acquire），输出一个 H 字符，然后等待栅栏放行。等其它的 goroutine 填补了氢原子的另一个空槽和氧原子的空槽之后，程序才可以继续进行。

```go
func (h2o *H2O) hydrogen(releaseHydrogen func()) {
  h2o.semaH.Acquire(context.Background(), 1)

  releaseHydrogen() // 输出H
  h2o.b.Await(context.Background()) //等待栅栏放行
  h2o.semaH.Release(1) // 释放氢原子空槽
}
```
然后是氧原子的流水线。氧原子的流水线处理方法是 oxygen， oxygen 方法是等待氧原子的空槽，然后输出一个 O，就等待栅栏放行。放行后，释放氧原子空槽位。

```go
func (h2o *H2O) oxygen(releaseOxygen func()) {
  h2o.semaO.Acquire(context.Background(), 1)

  releaseOxygen() // 输出O
  h2o.b.Await(context.Background()) //等待栅栏放行
  h2o.semaO.Release(1) // 释放氢原子空槽
}
```
在栅栏放行之前，只有两个氢原子的空槽位和一个氧原子的空槽位。只有等栅栏放行之后，这些空槽位才会被释放。栅栏放行，就意味着一个水分子组成成功。这个算法是不是正确呢？我们来编写一个单元测试检测一下。

```go
package water


import (
    "math/rand"
    "sort"
    "sync"
    "testing"
    "time"
)


func TestWaterFactory(t *testing.T) {
    //用来存放水分子结果的channel
    var ch chan string
    releaseHydrogen := func() {
        ch <- "H"
    }
    releaseOxygen := func() {
        ch <- "O"
    }

    // 300个原子，300个goroutine,每个goroutine并发的产生一个原子
    var N = 100
    ch = make(chan string, N*3)


    h2o := New()

    // 用来等待所有的goroutine完成
    var wg sync.WaitGroup
    wg.Add(N * 3)
   
    // 200个氢原子goroutine
    for i := 0; i < 2*N; i++ {
        go func() {
            time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
            h2o.hydrogen(releaseHydrogen)
            wg.Done()
        }()
    }
    // 100个氧原子goroutine
    for i := 0; i < N; i++ {
        go func() {
            time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
            h2o.oxygen(releaseOxygen)
            wg.Done()
        }()
    }
    
    //等待所有的goroutine执行完
    wg.Wait()

    // 结果中肯定是300个原子
    if len(ch) != N*3 {
        t.Fatalf("expect %d atom but got %d", N*3, len(ch))
    }

    // 每三个原子一组，分别进行检查。要求这一组原子中必须包含两个氢原子和一个氧原子，这样才能正确组成一个水分子。
    var s = make([]string, 3)
    for i := 0; i < N; i++ {
        s[0] = <-ch
        s[1] = <-ch
        s[2] = <-ch
        sort.Strings(s)


        water := s[0] + s[1] + s[2]
        if water != "HHO" {
            t.Fatalf("expect a water molecule but got %s", water)
        }
    }
}
```
### 总结

每一个并发原语都有它存在的道理，也都有它应用的场景。如果你没有学习 CyclicBarrier，你可能只会想到，用 WaitGroup 来实现这个水分子制造工厂的例子。

```
type H2O struct {
    semaH *semaphore.Weighted
    semaO *semaphore.Weighted
    wg    sync.WaitGroup //将循环栅栏替换成WaitGroup
}

func New() *H2O {
    var wg sync.WaitGroup
    wg.Add(3)

    return &H2O{
        semaH: semaphore.NewWeighted(2),
        semaO: semaphore.NewWeighted(1),
        wg:    wg,
    }
}


func (h2o *H2O) hydrogen(releaseHydrogen func()) {
    h2o.semaH.Acquire(context.Background(), 1)
    releaseHydrogen()

    // 标记自己已达到，等待其它goroutine到达
    h2o.wg.Done()
    h2o.wg.Wait()

    h2o.semaH.Release(1)
}

func (h2o *H2O) oxygen(releaseOxygen func()) {
    h2o.semaO.Acquire(context.Background(), 1)
    releaseOxygen()

    // 标记自己已达到，等待其它goroutine到达
    h2o.wg.Done()
    h2o.wg.Wait()
    //都到达后重置wg 
    h2o.wg.Add(3)

    h2o.semaO.Release(1)
}
```
你一看代码就知道了，使用 WaitGroup 非常复杂，而且，重用和 Done 方法的调用有并发的问题，程序可能 panic，远远没有使用循环栅栏更加简单直接。所以，我建议你多了解一些并发原语，甚至是从其它编程语言、操作系统中学习更多的并发原语，这样可以让你的知识库更加丰富，在面对并发场景的时候，你也能更加游刃有余。

![img](https://static001.geekbang.org/resource/image/82/4f/826f346ac0ccd687dc5d9bcc46621d4f.jpg?wh=2250*1233)



## 18 | 分组操作：处理一组子任务，该用什么并发原语？

共享资源保护、任务编排和消息传递是 Go 并发编程中常见的场景，而分组执行一批相同的或类似的任务则是任务编排中一类情形，所以，这节课，我专门来介绍一下分组编排的一些常用场景和并发原语，包括 ErrGroup、gollback、Hunch 和 schedgroup。我们先来学习一类非常常用的并发原语，那就是 ErrGroup。



### ErrGroup

[ErrGroup](https://github.com/golang/sync/tree/master/errgroup)是 Go 官方提供的一个同步扩展库。我们经常会碰到需要将一个通用的父任务拆成几个小任务并发执行的场景，其实，将一个大的任务拆成几个小任务并发执行，可以有效地提高程序的并发度。就像你在厨房做饭一样，你可以在蒸米饭的同时炒几个小菜，米饭蒸好了，菜同时也做好了，很快就能吃到可口的饭菜。ErrGroup 就是用来应对这种场景的。它和 WaitGroup 有些类似，但是它提供功能更加丰富：

和 Context 集成；error 向上传播，可以把子任务的错误传递给 Wait 的调用者。

接下来，我来给你介绍一下 ErrGroup 的基本用法和几种应用场景。

### 基本用法

golang.org/x/sync/errgroup 包下定义了一个 Group struct，它就是我们要介绍的 ErrGroup 并发原语，底层也是基于 WaitGroup 实现的。在使用 ErrGroup 时，我们要用到三个方法，分别是 WithContext、Go 和 Wait。

**1.WithContext**

在创建一个 Group 对象时，需要使用 WithContext 方法：

```go
func WithContext(ctx context.Context) (*Group, context.Context)
```
这个方法返回一个 Group 实例，同时还会返回一个使用 context.WithCancel(ctx) 生成的新 Context。一旦有一个子任务返回错误，或者是 Wait 调用返回，这个新 Context 就会被 cancel。Group 的零值也是合法的，只不过，你就没有一个可以监控是否 cancel 的 Context 了。注意，如果传递给 WithContext 的 ctx 参数，是一个可以 cancel 的 Context 的话，那么，它被 cancel 的时候，并不会终止正在执行的子任务。

**2.Go**

我们再来学习下执行子任务的 Go 方法：

```go
func (g *Group) Go(f func() error)
```
传入的子任务函数 f 是类型为 func() error 的函数，如果任务执行成功，就返回 nil，否则就返回 error，并且会 cancel 那个新的 Context。一个任务可以分成好多个子任务，而且，可能有多个子任务执行失败返回 error，不过，Wait 方法只会返回第一个错误，所以，如果想返回所有的错误，需要特别的处理，我先留个小悬念，一会儿再讲。

**3.Wait**

类似 WaitGroup，Group 也有 Wait 方法，等所有的子任务都完成后，它才会返回，否则只会阻塞等待。如果有多个子任务返回错误，它只会返回第一个出现的错误，如果所有的子任务都执行成功，就返回 nil：

```go
func (g *Group) Wait() error
```


### ErrGroup 使用例子

好了，知道了基本用法，下面我来给你介绍几个例子，帮助你全面地掌握 ErrGroup 的使用方法和应用场景。

**简单例子：返回第一个错误**

先来看一个简单的例子。在这个例子中，启动了三个子任务，其中，子任务 2 会返回执行失败，其它两个执行成功。在三个子任务都执行后，group.Wait 才会返回第 2 个子任务的错误。

```go
package main


import (
    "errors"
    "fmt"
    "time"

    "golang.org/x/sync/errgroup"
)

func main() {
    var g errgroup.Group


    // 启动第一个子任务,它执行成功
    g.Go(func() error {
        time.Sleep(5 * time.Second)
        fmt.Println("exec #1")
        return nil
    })
    // 启动第二个子任务，它执行失败
    g.Go(func() error {
        time.Sleep(10 * time.Second)
        fmt.Println("exec #2")
        return errors.New("failed to exec #2")
    })

    // 启动第三个子任务，它执行成功
    g.Go(func() error {
        time.Sleep(15 * time.Second)
        fmt.Println("exec #3")
        return nil
    })
    // 等待三个任务都完成
    if err := g.Wait(); err == nil {
        fmt.Println("Successfully exec all")
    } else {
        fmt.Println("failed:", err)
    }
}
```
如果执行下面的这个程序，会显示三个任务都执行了，而 Wait 返回了子任务 2 的错误：

![img](https://static001.geekbang.org/resource/image/92/11/92d746f7a1ab943e73b83796fb436a11.png?wh=782*129)

**更进一步，返回所有子任务的错误**

Group 只能返回子任务的第一个错误，后续的错误都会被丢弃。但是，有时候我们需要知道每个任务的执行情况。怎么办呢？这个时候，我们就可以用稍微有点曲折的方式去实现。我们使用一个 result slice 保存子任务的执行结果，这样，通过查询 result，就可以知道每一个子任务的结果了。下面的这个例子，就是使用 result 记录每个子任务成功或失败的结果。其实，你不仅可以使用 result 记录 error 信息，还可以用它记录计算结果。

```go
package main

import (
    "errors"
    "fmt"
    "time"

    "golang.org/x/sync/errgroup"
)

func main() {
    var g errgroup.Group
    var result = make([]error, 3)

    // 启动第一个子任务,它执行成功
    g.Go(func() error {
        time.Sleep(5 * time.Second)
        fmt.Println("exec #1")
        result[0] = nil // 保存成功或者失败的结果
        return nil
    })


    // 启动第二个子任务，它执行失败
    g.Go(func() error {
        time.Sleep(10 * time.Second)
        fmt.Println("exec #2")

        result[1] = errors.New("failed to exec #2") // 保存成功或者失败的结果
        return result[1]
    })

    // 启动第三个子任务，它执行成功
    g.Go(func() error {
        time.Sleep(15 * time.Second)
        fmt.Println("exec #3")
        result[2] = nil // 保存成功或者失败的结果
        return nil
    })

    if err := g.Wait(); err == nil {
        fmt.Printf("Successfully exec all. result: %v\n", result)
    } else {
        fmt.Printf("failed: %v\n", result)
    }
}
```
**任务执行流水线 Pipeline**

Go 官方文档中还提供了一个 pipeline 的例子。这个例子是说，由一个子任务遍历文件夹下的文件，然后把遍历出的文件交给 20 个 goroutine，让这些 goroutine 并行计算文件的 md5。这个例子中的计算逻辑你不需要重点掌握，我来把这个例子简化一下（如果你想看原始的代码，可以看[这里](https://godoc.org/golang.org/x/sync/errgroup#example-Group--Pipeline)）：

```go
package main

import (
   ......
    "golang.org/x/sync/errgroup"
)

// 一个多阶段的pipeline.使用有限的goroutine计算每个文件的md5值.
func main() {
    m, err := MD5All(context.Background(), ".")
    if err != nil {
        log.Fatal(err)
    }

    for k, sum := range m {
        fmt.Printf("%s:\t%x\n", k, sum)
    }
}

type result struct {
    path string
    sum  [md5.Size]byte
}

// 遍历根目录下所有的文件和子文件夹,计算它们的md5的值.
func MD5All(ctx context.Context, root string) (map[string][md5.Size]byte, error) {
    g, ctx := errgroup.WithContext(ctx)
    paths := make(chan string) // 文件路径channel

    g.Go(func() error {
        defer close(paths) // 遍历完关闭paths chan
        return filepath.Walk(root, func(path string, info os.FileInfo, err error) error {
            ...... //将文件路径放入到paths
            return nil
        })
    })

    // 启动20个goroutine执行计算md5的任务，计算的文件由上一阶段的文件遍历子任务生成.
    c := make(chan result)
    const numDigesters = 20
    for i := 0; i < numDigesters; i++ {
        g.Go(func() error {
            for path := range paths { // 遍历直到paths chan被关闭
                ...... // 计算path的md5值，放入到c中
            }
            return nil
        })
    }
    go func() {
        g.Wait() // 20个goroutine以及遍历文件的goroutine都执行完
        close(c) // 关闭收集结果的chan
    }()


    m := make(map[string][md5.Size]byte)
    for r := range c { // 将md5结果从chan中读取到map中,直到c被关闭才退出
        m[r.path] = r.sum
    }

    // 再次调用Wait，依然可以得到group的error信息
    if err := g.Wait(); err != nil {
        return nil, err
    }
    return m, nil
}
```
通过这个例子，你可以学习到多阶段 pipeline 的实现（这个例子是遍历文件夹和计算 md5 两个阶段），还可以学习到如何控制执行子任务的 goroutine 数量。很多公司都在使用 ErrGroup 处理并发子任务，比如 Facebook、bilibili 等公司的一些项目，但是，这些公司在使用的时候，发现了一些不方便的地方，或者说，官方的 ErrGroup 的功能还不够丰富。所以，他们都对 ErrGroup 进行了扩展。接下来呢，我就带你看看几个扩展库。

### 扩展库

bilibili/errgroup如果我们无限制地直接调用 ErrGroup 的 Go 方法，就可能会创建出非常多的 goroutine，太多的 goroutine 会带来调度和 GC 的压力，而且也会占用更多的内存资源。就像go#34457指出的那样，当前 Go 运行时创建的 g 对象只会增长和重用，不会回收，所以在高并发的情况下，也要尽可能减少 goroutine 的使用。

常用的一个手段就是使用 worker pool(goroutine pool)，或者是类似containerd/stargz-snapshotter的方案，使用前面我们讲的信号量，信号量的资源的数量就是可以并行的 goroutine 的数量。但是在这一讲，我来介绍一些其它的手段，比如下面介绍的 bilibili 实现的 errgroup。

bilibili 实现了一个扩展的 ErrGroup，可以使用一个固定数量的 goroutine 处理子任务。如果不设置 goroutine 的数量，那么每个子任务都会比较“放肆地”创建一个 goroutine 并发执行。

这个链接里的文档已经很详细地介绍了它的几个扩展功能，所以我就不通过示例的方式来进行讲解了。除了可以控制并发 goroutine 的数量，它还提供了 2 个功能：

cancel，失败的子任务可以 cancel 所有正在执行任务；recover，而且会把 panic 的堆栈信息放到 error 中，避免子任务 panic 导致的程序崩溃。

但是，有一点不太好的地方就是，一旦你设置了并发数，超过并发数的子任务需要等到调用者调用 Wait 之后才会执行，而不是只要 goroutine 空闲下来，就去执行。如果不注意这一点的话，可能会出现子任务不能及时处理的情况，这是这个库可以优化的一点。另外，这个库其实是有一个并发问题的。在高并发的情况下，如果任务数大于设定的 goroutine 的数量，并且这些任务被集中加入到 Group 中，这个库的处理方式是把子任务加入到一个数组中，但是，这个数组不是线程安全的，有并发问题，问题就在于，下面图片中的标记为 96 行的那一行，这一行对 slice 的 append 操作不是线程安全的：

![img](https://static001.geekbang.org/resource/image/ef/5b/ef65c08c041f7b98c71e461f1497bc5b.png?wh=835*348)

我们可以写一个简单的程序来测试这个问题：

```go
package main

import (
    "context"
    "fmt"
    "sync/atomic"
    "time"

    "github.com/bilibili/kratos/pkg/sync/errgroup"
)

func main() {
    var g errgroup.Group
    g.GOMAXPROCS(1) // 只使用一个goroutine处理子任务

    var count int64
    g.Go(func(ctx context.Context) error {
        time.Sleep(time.Second) //睡眠5秒，把这个goroutine占住
        return nil
    })

    total := 10000

    for i := 0; i < total; i++ { // 并发一万个goroutine执行子任务，理论上这些子任务都会加入到Group的待处理列表中
        go func() {
            g.Go(func(ctx context.Context) error {
                atomic.AddInt64(&count, 1)
                return nil
            })
        }()
    }

    // 等待所有的子任务完成。理论上10001个子任务都会被完成
    if err := g.Wait(); err != nil {
        panic(err)
    }

    got := atomic.LoadInt64(&count)
    if got != int64(total) {
        panic(fmt.Sprintf("expect %d but got %d", total, got))
    }
}
```
运行这个程序的话，你就会发现死锁问题，因为我们的测试程序是一个简单的命令行工具，程序退出的时候，Go runtime 能检测到死锁问题。如果是一直运行的服务器程序，死锁问题有可能是检测不出来的，程序一直会 hang 在 Wait 的调用上。

neilotoole/errgroupneilotoole/errgroup 是今年年中新出现的一个 ErrGroup 扩展库，它可以直接替换官方的 ErrGroup，方法都一样，原有功能也一样，只不过增加了可以控制并发 goroutine 的功能。它的方法集如下：

```
type Group
  func WithContext(ctx context.Context) (*Group, context.Context)
  func WithContextN(ctx context.Context, numG, qSize int) (*Group, context.Context)
  func (g *Group) Go(f func() error)
  func (g *Group) Wait() error
```
新增加的方法 WithContextN，可以设置并发的 goroutine 数，以及等待处理的子任务队列的大小。当队列满的时候，如果调用 Go 方法，就会被阻塞，直到子任务可以放入到队列中才返回。如果你传给这两个参数的值不是正整数，它就会使用 runtime.NumCPU 代替你传入的参数。当然，你也可以把 bilibili 的 recover 功能扩展到这个库中，以避免子任务的 panic 导致程序崩溃。

facebookgo/errgroup Facebook 提供的这个 ErrGroup，其实并不是对 Go 扩展库 ErrGroup 的扩展，而是对标准库 WaitGroup 的扩展。不过，因为它们的名字一样，处理的场景也类似，所以我把它也列在了这里。标准库的 WaitGroup 只提供了 Add、Done、Wait 方法，而且 Wait 方法也没有返回子 goroutine 的 error。而 Facebook 提供的 ErrGroup 提供的 Wait 方法可以返回 error，而且可以包含多个 error。子任务在调用 Done 之前，可以把自己的 error 信息设置给 ErrGroup。接着，Wait 在返回的时候，就会把这些 error 信息返回给调用者。我们来看下 Group 的方法：

```
type Group
  func (g *Group) Add(delta int)
  func (g *Group) Done()
  func (g *Group) Error(e error)
  func (g *Group) Wait() error
```
关于 Wait 方法，我刚刚已经介绍了它和标准库 WaitGroup 的不同，我就不多说了。这里还有一个不同的方法，就是 Error 方法，我举个例子演示一下 Error 的使用方法。在下面的这个例子中，第 26 行的子 goroutine 设置了 error 信息，第 39 行会把这个 error 信息输出出来。

```go
package main

import (
    "errors"
    "fmt"
    "time"

    "github.com/facebookgo/errgroup"
)

func main() {
    var g errgroup.Group
    g.Add(3)

    // 启动第一个子任务,它执行成功
    go func() {
        time.Sleep(5 * time.Second)
        fmt.Println("exec #1")
        g.Done()
    }()

    // 启动第二个子任务，它执行失败
    go func() {
        time.Sleep(10 * time.Second)
        fmt.Println("exec #2")
        g.Error(errors.New("failed to exec #2"))
        g.Done()
    }()

    // 启动第三个子任务，它执行成功
    go func() {
        time.Sleep(15 * time.Second)
        fmt.Println("exec #3")
        g.Done()
    }()

    // 等待所有的goroutine完成，并检查error
    if err := g.Wait(); err == nil {
        fmt.Println("Successfully exec all")
    } else {
        fmt.Println("failed:", err)
    }
}
```
关于 ErrGroup，你掌握这些就足够了，接下来，我再介绍几种有趣而实用的 Group 并发原语。这些并发原语都是控制一组子 goroutine 执行的面向特定场景的并发原语，当你遇见这些特定场景时，就可以参考这些库。

### 其它实用的 Group 并发原语

#### SizedGroup/ErrSizedGroup

go-pkgz/syncs提供了两个 Group 并发原语，分别是 SizedGroup 和 ErrSizedGroup。SizedGroup 内部是使用信号量和 WaitGroup 实现的，它通过信号量控制并发的 goroutine 数量，或者是不控制 goroutine 数量，只控制子任务并发执行时候的数量（通过）。它的代码实现非常简洁，你可以到它的代码库中了解它的具体实现，你一看就明白了，我就不多说了。下面我重点说说它的功能。

**默认情况下，SizedGroup 控制的是子任务的并发数量，而不是 goroutine 的数量**。在这种方式下，每次调用 Go 方法都不会被阻塞，而是新建一个 goroutine 去执行。如果想控制 goroutine 的数量，你可以使用 syncs.Preemptive 设置这个并发原语的可选项。如果设置了这个可选项，但在调用 Go 方法的时候没有可用的 goroutine，那么调用者就会等待，直到有 goroutine 可以处理这个子任务才返回，这个控制在内部是使用信号量实现的。我们来看一个使用 SizedGroup 的例子：

```go
package main

import (
    "context"
    "fmt"
    "sync/atomic"
    "time"

    "github.com/go-pkgz/syncs"
)

func main() {
    // 设置goroutine数是10
    swg := syncs.NewSizedGroup(10)
    // swg := syncs.NewSizedGroup(10, syncs.Preemptive)
    var c uint32

    // 执行1000个子任务，只会有10个goroutine去执行
    for i := 0; i < 1000; i++ {
        swg.Go(func(ctx context.Context) {
            time.Sleep(5 * time.Millisecond)
            atomic.AddUint32(&c, 1)
        })
    }

    // 等待任务完成
    swg.Wait()
    // 输出结果
    fmt.Println(c)
}
```
ErrSizedGroup 为 SizedGroup 提供了 error 处理的功能，它的功能和 Go 官方扩展库的功能一样，就是等待子任务完成并返回第一个出现的 error。不过，它还提供了额外的功能，我来介绍一下。第一个额外的功能，就是可以控制并发的 goroutine 数量，这和 SizedGroup 的功能一样。第二个功能是，如果设置了 termOnError，子任务出现第一个错误的时候会 cancel Context，而且后续的 Go 调用会直接返回，Wait 调用者会得到这个错误，这相当于是遇到错误快速返回。如果没有设置 termOnError，Wait 会返回所有的子任务的错误。

**不过，ErrSizedGroup 和 SizedGroup 设计得不太一致的地方是，SizedGroup 可以把 Context 传递给子任务，这样可以通过 cancel 让子任务中断执行，但是 ErrSizedGroup 却没有实现。我认为，这是一个值得加强的地方。**

总体来说，syncs 包提供的并发原语的质量和功能还是非常赞的。不过，目前的 star 只有十几个，这和它的功能严重不匹配，我建议你 star 这个项目，支持一下作者。好了，关于 ErrGroup，你掌握这些就足够了，下面我再来给你介绍一些非 ErrGroup 的并发原语，它们用来编排子任务。

#### gollback

gollback也是用来处理一组子任务的执行的，不过它解决了 ErrGroup 收集子任务返回结果的痛点。使用 ErrGroup 时，如果你要收到子任务的结果和错误，你需要定义额外的变量收集执行结果和错误，但是这个库可以提供更便利的方式。我刚刚在说官方扩展库 ErrGroup 的时候，举了一些例子（返回第一个错误的例子和返回所有子任务错误的例子），在例子中，如果想得到每一个子任务的结果或者 error，我们需要额外提供一个 result slice 进行收集。使用 gollback 的话，就不需要这些额外的处理了，因为它的方法会把结果和 error 信息都返回。接下来，我们看一下它提供的三个方法，分别是 All、Race 和 Retry。

#### All 方法

All 方法的签名如下：

```go
func All(ctx context.Context, fns ...AsyncFunc) ([]interface{}, []error)
```
它会等待所有的异步函数（AsyncFunc）都执行完才返回，而且返回结果的顺序和传入的函数的顺序保持一致。第一个返回参数是子任务的执行结果，第二个参数是子任务执行时的错误信息。其中，异步函数的定义如下：

```
type AsyncFunc func(ctx context.Context) (interface{}, error)
```
可以看到，ctx 会被传递给子任务。如果你 cancel 这个 ctx，可以取消子任务。我们来看一个使用 All 方法的例子：

```go
package main

import (
  "context"
  "errors"
  "fmt"
  "github.com/vardius/gollback"
  "time"
)

func main() {
  rs, errs := gollback.All( // 调用All方法
    context.Background(),
    func(ctx context.Context) (interface{}, error) { 
      time.Sleep(3 * time.Second)
      return 1, nil // 第一个任务没有错误，返回1
    },
    func(ctx context.Context) (interface{}, error) {
      return nil, errors.New("failed") // 第二个任务返回一个错误
    },
    func(ctx context.Context) (interface{}, error) {
      return 3, nil // 第三个任务没有错误，返回3
    },
  )

  fmt.Println(rs) // 输出子任务的结果
  fmt.Println(errs) // 输出子任务的错误信息
}
```
#### Race 方法

Race 方法跟 All 方法类似，只不过，在使用 Race 方法的时候，只要一个异步函数执行没有错误，就立马返回，而不会返回所有的子任务信息。如果所有的子任务都没有成功，就会返回最后一个 error 信息。Race 方法签名如下：

```go
func Race(ctx context.Context, fns ...AsyncFunc) (interface{}, error)
```
如果有一个正常的子任务的结果返回，Race 会把传入到其它子任务的 Context cancel 掉，这样子任务就可以中断自己的执行。Race 的使用方法也跟 All 方法类似，我就不再举例子了，你可以把 All 方法的例子中的 All 替换成 Race 方式测试下。

#### Retry 方法

Retry 不是执行一组子任务，而是执行一个子任务。如果子任务执行失败，它会尝试一定的次数，如果一直不成功 ，就会返回失败错误  ，如果执行成功，它会立即返回。如果 retires 等于 0，它会永远尝试，直到成功。

```go
func Retry(ctx context.Context, retires int, fn AsyncFunc) (interface{}, error)
```
再来看一个使用 Retry 的例子：

```go
package main

import (
  "context"
  "errors"
  "fmt"
  "github.com/vardius/gollback"
  "time"
)

func main() {
  ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
  defer cancel()

  // 尝试5次，或者超时返回
  res, err := gollback.Retry(ctx, 5, func(ctx context.Context) (interface{}, error) {
    return nil, errors.New("failed")
  })

  fmt.Println(res) // 输出结果
  fmt.Println(err) // 输出错误信息
} 
```
#### Hunch

[Hunch](https://github.com/AaronJan/Hunch)提供的功能和 gollback 类似，不过它提供的方法更多，而且它提供的和 gollback 相应的方法，也有一些不同。我来一一介绍下。它定义了执行子任务的函数，这和 gollback 的 AyncFunc 是一样的，它的定义如下：

```
type Executable func(context.Context) (interface{}, error)
```
#### All 方法

All 方法的签名如下：

```go
func All(parentCtx context.Context, execs ...Executable) ([]interface{}, error)
```
它会传入一组可执行的函数（子任务），返回子任务的执行结果。和 gollback 的 All 方法不一样的是，一旦一个子任务出现错误，它就会返回错误信息，执行结果（第一个返回参数）为 nil。

#### Take 方法

Take 方法的签名如下：

```go
func Take(parentCtx context.Context, num int, execs ...Executable) ([]interface{}, error)
```
你可以指定 num 参数，只要有 num 个子任务正常执行完没有错误，这个方法就会返回这几个子任务的结果。一旦一个子任务出现错误，它就会返回错误信息，执行结果（第一个返回参数）为 nil。

#### Last 方法

Last 方法的签名如下：

```go
func Last(parentCtx context.Context, num int, execs ...Executable) ([]interface{}, error)
```
它只返回最后 num 个正常执行的、没有错误的子任务的结果。一旦一个子任务出现错误，它就会返回错误信息，执行结果（第一个返回参数）为 nil。比如 num 等于 1，那么，它只会返回最后一个无错的子任务的结果。

#### Retry 方法

Retry 方法的签名如下：

```go
func Retry(parentCtx context.Context, retries int, fn Executable) (interface{}, error)
```
它的功能和 gollback 的 Retry 方法的功能一样，如果子任务执行出错，就会不断尝试，直到成功或者是达到重试上限。如果达到重试上限，就会返回错误。如果 retries 等于 0，它会不断尝试。

#### Waterfall 方法

Waterfall 方法签名如下：

```go
func Waterfall(parentCtx context.Context, execs ...ExecutableInSequence) (interface{}, error)
```
它其实是一个 pipeline 的处理方式，所有的子任务都是串行执行的，前一个子任务的执行结果会被当作参数传给下一个子任务，直到所有的任务都完成，返回最后的执行结果。一旦一个子任务出现错误，它就会返回错误信息，执行结果（第一个返回参数）为 nil。gollback 和 Hunch 是属于同一类的并发原语，对一组子任务的执行结果，可以选择一个结果或者多个结果，这也是现在热门的微服务常用的服务治理的方法。



#### schedgroup

接下来，我再介绍一个和**时间相关的处理一组 goroutine 的并发原语 schedgroup**。schedgroup是 Matt Layher 开发的 worker pool，可以指定任务在某个时间或者某个时间之后执行。Matt Layher 也是一个知名的 Gopher，经常在一些会议上分享一些他的 Go 开发经验，他在 GopherCon Europe 2020 大会上专门介绍了这个并发原语：schedgroup: a timer-based goroutine concurrency primitive ，课下你可以点开这个链接看一下，下面我来给你介绍一些重点。这个并发原语包含的方法如下：

```
type Group
  func New(ctx context.Context) *Group
  func (g *Group) Delay(delay time.Duration, fn func())
  func (g *Group) Schedule(when time.Time, fn func())
  func (g *Group) Wait() error
```
我来介绍下这些方法。先说 Delay 和 Schedule。它们的功能其实是一样的，都是用来指定在某个时间或者之后执行一个函数。只不过，Delay 传入的是一个 time.Duration 参数，它会在 time.Now()+delay 之后执行函数，而 Schedule 可以指定明确的某个时间执行。再来说说 Wait 方法。这个方法调用会阻塞调用者，直到之前安排的所有子任务都执行完才返回。如果 Context 被取消，那么，Wait 方法会返回这个 cancel error。在使用 Wait 方法的时候，有 2 点需要注意一下。

**第一点是，如果调用了 Wait 方法，你就不能再调用它的 Delay 和 Schedule 方法，否则会 panic。第二点是，Wait 方法只能调用一次，如果多次调用的话，就会 panic。**

你可能认为，简单地使用 timer 就可以实现这个功能。其实，如果只有几个子任务，使用 timer 不是问题，但一旦有大量的子任务，而且还要能够 cancel，那么，使用 timer 的话，CPU 资源消耗就比较大了。所以，schedgroup 在实现的时候，就使用 container/heap，按照子任务的执行时间进行排序，这样可以避免使用大量的 timer，从而提高性能。我们来看一个使用 schedgroup 的例子，下面代码会依次输出 1、2、3：

```
sg := schedgroup.New(context.Background())

// 设置子任务分别在100、200、300之后执行
for i := 0; i < 3; i++ {
    n := i + 1
    sg.Delay(time.Duration(n)*100*time.Millisecond, func() {
        log.Println(n) //输出任务编号
    })
}

// 等待所有的子任务都完成
if err := sg.Wait(); err != nil {
    log.Fatalf("failed to wait: %v", err)
}
```


### 总结

这节课，我给你介绍了几种常见的处理一组子任务的并发原语，包括 ErrGroup、gollback、Hunch、schedgroup，等等。这些常见的业务场景共性处理方式的总结，你可以把它们加入到你的知识库中，等以后遇到相同的业务场景时，你就可以考虑使用这些并发原语。当然，类似的并发原语还有别的，比如[go-waitgroup](https://github.com/pieterclaerhout/go-waitgroup)等，而且，我相信还会有新的并发原语不断出现。所以，你不仅仅要掌握这些并发原语，而且还要通过学习这些并发原语，学会构造新的并发原语来处理应对你的特有场景，实现代码重用和业务逻辑简化。

![img](https://static001.geekbang.org/resource/image/ee/9c/ee46d1dbed154a24063d3b0795fb5d9c.jpg?wh=2250*2664)

## 分布式并发原语

## 19 | 在分布式环境中，Leader选举、互斥锁和读写锁该如何实现？

在前面的课程里，我们学习的并发原语都是在进程内使用的，也就是我们常见的一个运行程序为了控制共享资源、实现任务编排和进行消息传递而提供的控制类型。在接下来的这两节课里，我要讲的是几个分布式的并发原语，它们控制的资源或编排的任务分布在不同进程、不同机器上。分布式的并发原语实现更加复杂，因为在分布式环境中，网络状况、服务状态都是不可控的。不过还好有相应的软件系统去做这些事情。这些软件系统会专门去处理这些节点之间的协调和异常情况，并且保证数据的一致性。我们要做的就是在它们的基础上实现我们的业务。常用来做协调工作的软件系统是 Zookeeper、etcd、Consul 之类的软件，Zookeeper 为 Java 生态群提供了丰富的分布式并发原语（通过 Curator 库），但是缺少 Go 相关的并发原语库。Consul 在提供分布式并发原语这件事儿上不是很积极，而 etcd 就提供了非常好的分布式并发原语，比如分布式互斥锁、分布式读写锁、Leader 选举，等等。所以，今天，我就以 etcd 为基础，给你介绍几种分布式并发原语。既然我们依赖 etcd，那么，在生产环境中要有一个 etcd 集群，而且应该保证这个 etcd 集群是 7*24 工作的。在学习过程中，你可以使用一个 etcd 节点进行测试。这节课我要介绍的就是 Leader 选举、互斥锁和读写锁。

### Leader 选举

Leader 选举常常用在主从架构的系统中。主从架构中的服务节点分为主（Leader、Master）和从（Follower、Slave）两种角色，实际节点包括 1 主 n 从，一共是 n+1 个节点。主节点常常执行写操作，从节点常常执行读操作，如果读写都在主节点，从节点只是提供一个备份功能的话，那么，主从架构就会退化成主备模式架构。主从架构中最重要的是如何确定节点的角色，也就是，到底哪个节点是主，哪个节点是从？

**在同一时刻，系统中不能有两个主节点，否则，如果两个节点都是主，都执行写操作的话，就有可能出现数据不一致的情况，所以，我们需要一个选主机制，选择一个节点作为主节点，这个过程就是 Leader 选举。**

当主节点宕机或者是不可用时，就需要新一轮的选举，从其它的从节点中选择出一个节点，让它作为新主节点，宕机的原主节点恢复后，可以变为从节点，或者被摘掉。我们可以通过 etcd 基础服务来实现 leader 选举。具体点说，我们可以将 Leader 选举的逻辑交给 etcd 基础服务，这样，我们只需要把重心放在业务开发上。etcd 基础服务可以通过多节点的方式保证 7*24 服务，所以，我们也不用担心 Leader 选举不可用的问题。如下图所示：

![img](https://static001.geekbang.org/resource/image/78/47/78010df8677171d9bf29c64d346d9647.jpg?wh=4000*1710)

接下来，我会给你介绍业务开发中跟 Leader 选举相关的选举、查询、Leader 变动监控等功能。我要先提醒你一句，如果你想运行我下面讲到的测试代码，就要先部署一个 etcd 的集群，或者部署一个 etcd 节点做测试。首先，我们来实现一个测试分布式程序的框架：它会先从命令行中读取命令，然后再执行相应的命令。你可以打开两个窗口，模拟不同的节点，分别执行不同的命令。这个测试程序如下：

```go
package main

// 导入所需的库
import (
    "bufio"
    "context"
    "flag"
    "fmt"
    "log"
    "os"
    "strconv"
    "strings"

    "github.com/coreos/etcd/clientv3"
    "github.com/coreos/etcd/clientv3/concurrency"
)

// 可以设置一些参数，比如节点ID
var (
    nodeID    = flag.Int("id", 0, "node ID")
    addr      = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
    electName = flag.String("name", "my-test-elect", "election name")
)

func main() {
    flag.Parse()

    // 将etcd的地址解析成slice of string
    endpoints := strings.Split(*addr, ",")

    // 生成一个etcd的clien
    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()

    // 创建session,如果程序宕机导致session断掉，etcd能检测到
    session, err := concurrency.NewSession(cli)
    defer session.Close()

    // 生成一个选举对象。下面主要使用它进行选举和查询等操作
    // 另一个方法ResumeElection可以使用既有的leader初始化Election
    e1 := concurrency.NewElection(session, *electName)

    // 从命令行读取命令
    consolescanner := bufio.NewScanner(os.Stdin)
    for consolescanner.Scan() {
        action := consolescanner.Text()
        switch action {
        case "elect": // 选举命令
            go elect(e1, *electName)
        case "proclaim": // 只更新leader的value
            proclaim(e1, *electName)
        case "resign": // 辞去leader,重新选举
            resign(e1, *electName)
        case "watch": // 监控leader的变动
            go watch(e1, *electName)
        case "query": // 查询当前的leader
            query(e1, *electName)
        case "rev":
            rev(e1, *electName)
        default:
            fmt.Println("unknown action")
        }
    }
}
```
部署完以后，我们就可以开始选举了。



### 选举

如果你的业务集群还没有主节点，或者主节点宕机了，你就需要发起新一轮的选主操作，主要会用到 Campaign 和 Proclaim。如果你需要主节点放弃主的角色，让其它从节点有机会成为主节点，就可以调用 Resign 方法。这里我提到了三个和选主相关的方法，下面我来介绍下它们的用法。第一个方法是 Campaign。它的作用是，把一个节点选举为主节点，并且会设置一个值。它的签名如下所示：

```go
func (e *Election) Campaign(ctx context.Context, val string) error
```
需要注意的是，这是一个阻塞方法，在调用它的时候会被阻塞，直到满足下面的三个条件之一，才会取消阻塞。

成功当选为主；此方法返回错误；ctx 被取消。

第二个方法是 Proclaim。它的作用是，重新设置 Leader 的值，但是不会重新选主，这个方法会返回新值设置成功或者失败的信息。方法签名如下所示：

```go
func (e *Election) Proclaim(ctx context.Context, val string) error
```
第三个方法是 Resign：开始新一次选举。这个方法会返回新的选举成功或者失败的信息。它的签名如下所示：

```go
func (e *Election) Resign(ctx context.Context) (err error)
```
这三个方法的测试代码如下。你可以使用测试程序进行测试，具体做法是，启动两个节点，执行和这三个方法相关的命令。

```
var count int
// 选主
func elect(e1 *concurrency.Election, electName string) {
    log.Println("acampaigning for ID:", *nodeID)
    // 调用Campaign方法选主,主的值为value-<主节点ID>-<count>
    if err := e1.Campaign(context.Background(), fmt.Sprintf("value-%d-%d", *nodeID, count)); err != nil {
        log.Println(err)
    }
    log.Println("campaigned for ID:", *nodeID)
    count++
}
// 为主设置新值
func proclaim(e1 *concurrency.Election, electName string) {
    log.Println("proclaiming for ID:", *nodeID)
    // 调用Proclaim方法设置新值,新值为value-<主节点ID>-<count>
    if err := e1.Proclaim(context.Background(), fmt.Sprintf("value-%d-%d", *nodeID, count)); err != nil {
        log.Println(err)
    }
    log.Println("proclaimed for ID:", *nodeID)
    count++
}
// 重新选主，有可能另外一个节点被选为了主
func resign(e1 *concurrency.Election, electName string) {
    log.Println("resigning for ID:", *nodeID)
    // 调用Resign重新选主
    if err := e1.Resign(context.TODO()); err != nil {
        log.Println(err)
    }
    log.Println("resigned for ID:", *nodeID)
}
```


### 查询

除了选举 Leader，程序在启动的过程中，或者在运行的时候，还有可能需要查询当前的主节点是哪一个节点？主节点的值是什么？版本是多少？不光是主从节点需要查询和知道哪一个节点，在分布式系统中，还有其它一些节点也需要知道集群中的哪一个节点是主节点，哪一个节点是从节点，这样它们才能把读写请求分别发往相应的主从节点上。etcd 提供了查询当前 Leader 的方法 Leader，如果当前还没有 Leader，就返回一个错误，你可以使用这个方法来查询主节点信息。这个方法的签名如下：

```go
func (e *Election) Leader(ctx context.Context) (*v3.GetResponse, error)
```
每次主节点的变动都会生成一个新的版本号，你还可以查询版本号信息（Rev 方法），了解主节点变动情况：

```go
func (e *Election) Rev() int64
```
你可以在测试完选主命令后，测试查询命令（query、rev），代码如下：

```
// 查询主的信息
func query(e1 *concurrency.Election, electName string) {
    // 调用Leader返回主的信息，包括key和value等信息
    resp, err := e1.Leader(context.Background())
    if err != nil {
        log.Printf("failed to get the current leader: %v", err)
    }
    log.Println("current leader:", string(resp.Kvs[0].Key), string(resp.Kvs[0].Value))
}
// 可以直接查询主的rev信息
func rev(e1 *concurrency.Election, electName string) {
    rev := e1.Rev()
    log.Println("current rev:", rev)
}
```
### 监控

有了选举和查询方法，我们还需要一个监控方法。毕竟，如果主节点变化了，我们需要得到最新的主节点信息。我们可以通过 Observe 来监控主的变化，它的签名如下：

```go
func (e *Election) Observe(ctx context.Context) <-chan v3.GetResponse
```
它会返回一个 chan，显示主节点的变动信息。需要注意的是，它不会返回主节点的全部历史变动信息，而是只返回最近的一条变动信息以及之后的变动信息。它的测试代码如下：

```go
func watch(e1 *concurrency.Election, electName string) {
    ch := e1.Observe(context.TODO())


    log.Println("start to watch for ID:", *nodeID)
    for i := 0; i < 10; i++ {
        resp := <-ch
        log.Println("leader changed to", string(resp.Kvs[0].Key), string(resp.Kvs[0].Value))
    }
}
```
etcd 提供了选主的逻辑，而你要做的就是利用这些方法，让它们为你的业务服务。在使用的过程中，你还需要做一些额外的设置，比如查询当前的主节点、启动一个 goroutine 阻塞调用 Campaign 方法，等等。虽然你需要做一些额外的工作，但是跟自己实现一个分布式的选主逻辑相比，大大地减少了工作量。接下来，我们继续看 etcd 提供的分布式并发原语：互斥锁。

### 互斥锁

互斥锁是非常常用的一种并发原语，我专门花了 4 讲的时间，重点介绍了互斥锁的功能、原理和易错场景。不过，前面说的互斥锁都是用来保护同一进程内的共享资源的，今天，我们要掌握的是分布式环境中的互斥锁。**我们要重点学习下分布在不同机器中的不同进程内的 goroutine，如何利用分布式互斥锁来保护共享资源。**互斥锁的应用场景和主从架构的应用场景不太一样。**使用互斥锁的不同节点是没有主从这样的角色的，所有的节点都是一样的，只不过在同一时刻，只允许其中的一个节点持有锁**。下面，我们就来学习下互斥锁相关的两个原语，即 Locker 和 Mutex。

### Locker

etcd 提供了一个简单的 Locker 原语，它类似于 Go 标准库中的 sync.Locker 接口，也提供了 Lock/UnLock 的机制：

```go
func NewLocker(s *Session, pfx string) sync.Locker
```
可以看到，它的返回值是一个 sync.Locker，因为你对标准库的 Locker 已经非常了解了，而且它只有 Lock/Unlock 两个方法，所以，接下来使用这个锁就非常容易了。下面的代码是一个使用 Locker 并发原语的例子：

```go
package main

import (
    "flag"
    "log"
    "math/rand"
    "strings"
    "time"

    "github.com/coreos/etcd/clientv3"
    "github.com/coreos/etcd/clientv3/concurrency"
)

var (
    addr     = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
    lockName = flag.String("name", "my-test-lock", "lock name")
)

func main() {
    flag.Parse()
    
    rand.Seed(time.Now().UnixNano())
    // etcd地址
    endpoints := strings.Split(*addr, ",")
    // 生成一个etcd client
    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()
    useLock(cli) // 测试锁
}

func useLock(cli *clientv3.Client) {
    // 为锁生成session
    s1, err := concurrency.NewSession(cli)
    if err != nil {
        log.Fatal(err)
    }
    defer s1.Close()
    //得到一个分布式锁
    locker := concurrency.NewLocker(s1, *lockName)

    // 请求锁
    log.Println("acquiring lock")
    locker.Lock()
    log.Println("acquired lock")

    // 等待一段时间
    time.Sleep(time.Duration(rand.Intn(30)) * time.Second)
    locker.Unlock() // 释放锁

    log.Println("released lock")
}
```
你可以同时在两个终端中运行这个测试程序。可以看到，它们获得锁是有先后顺序的，一个节点释放了锁之后，另外一个节点才能获取到这个分布式锁。

### Mutex

事实上，刚刚说的 Locker 是基于 Mutex 实现的，只不过，Mutex 提供了查询 Mutex 的 key 的信息的功能。测试代码也类似：

```go
func useMutex(cli *clientv3.Client) {
    // 为锁生成session
    s1, err := concurrency.NewSession(cli)
    if err != nil {
        log.Fatal(err)
    }
    defer s1.Close()
    m1 := concurrency.NewMutex(s1, *lockName)

    //在请求锁之前查询key
    log.Printf("before acquiring. key: %s", m1.Key())
    // 请求锁
    log.Println("acquiring lock")
    if err := m1.Lock(context.TODO()); err != nil {
        log.Fatal(err)
    }
    log.Printf("acquired lock. key: %s", m1.Key())

    //等待一段时间
    time.Sleep(time.Duration(rand.Intn(30)) * time.Second)

    // 释放锁
    if err := m1.Unlock(context.TODO()); err != nil {
        log.Fatal(err)
    }
    log.Println("released lock")
}
```
可以看到，Mutex 并没有实现 sync.Locker 接口，它的 Lock/Unlock 方法需要提供一个 context.Context 实例做参数，这也就意味着，在请求锁的时候，你可以设置超时时间，或者主动取消请求。

### 读写锁

学完了分布式 Locker 和互斥锁 Mutex，你肯定会联想到读写锁 RWMutex。是的，etcd 也提供了分布式的读写锁。不过，互斥锁 Mutex 是在 github.com/coreos/etcd/clientv3/concurrency 包中提供的，读写锁 RWMutex 却是在 github.com/coreos/etcd/contrib/recipes 包中提供的。etcd 提供的分布式读写锁的功能和标准库的读写锁的功能是一样的。只不过，etcd 提供的读写锁，可以在分布式环境中的不同的节点使用。它提供的方法也和标准库中的读写锁的方法一致，分别提供了 RLock/RUnlock、Lock/Unlock 方法。下面的代码是使用读写锁的例子，它从命令行中读取命令，执行读写锁的操作：

```go
package main


import (
    "bufio"
    "flag"
    "fmt"
    "log"
    "math/rand"
    "os"
    "strings"
    "time"

    "github.com/coreos/etcd/clientv3"
    "github.com/coreos/etcd/clientv3/concurrency"
    recipe "github.com/coreos/etcd/contrib/recipes"
)

var (
    addr     = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
    lockName = flag.String("name", "my-test-lock", "lock name")
    action   = flag.String("rw", "w", "r means acquiring read lock, w means acquiring write lock")
)


func main() {
    flag.Parse()
    rand.Seed(time.Now().UnixNano())

    // 解析etcd地址
    endpoints := strings.Split(*addr, ",")

    // 创建etcd的client
    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()
    // 创建session
    s1, err := concurrency.NewSession(cli)
    if err != nil {
        log.Fatal(err)
    }
    defer s1.Close()
    m1 := recipe.NewRWMutex(s1, *lockName)

    // 从命令行读取命令
    consolescanner := bufio.NewScanner(os.Stdin)
    for consolescanner.Scan() {
        action := consolescanner.Text()
        switch action {
        case "w": // 请求写锁
            testWriteLocker(m1)
        case "r": // 请求读锁
            testReadLocker(m1)
        default:
            fmt.Println("unknown action")
        }
    }
}

func testWriteLocker(m1 *recipe.RWMutex) {
    // 请求写锁
    log.Println("acquiring write lock")
    if err := m1.Lock(); err != nil {
        log.Fatal(err)
    }
    log.Println("acquired write lock")

    // 等待一段时间
    time.Sleep(time.Duration(rand.Intn(10)) * time.Second)

    // 释放写锁
    if err := m1.Unlock(); err != nil {
        log.Fatal(err)
    }
    log.Println("released write lock")
}

func testReadLocker(m1 *recipe.RWMutex) {
    // 请求读锁
    log.Println("acquiring read lock")
    if err := m1.RLock(); err != nil {
        log.Fatal(err)
    }
    log.Println("acquired read lock")

    // 等待一段时间
    time.Sleep(time.Duration(rand.Intn(10)) * time.Second)

    // 释放写锁
    if err := m1.RUnlock(); err != nil {
        log.Fatal(err)
    }
    log.Println("released read lock")
}
```
### 总结

自己实现分布式环境的并发原语，是相当困难的一件事，因为你需要考虑网络的延迟和异常、节点的可用性、数据的一致性等多种情况。所以，我们可以借助 etcd 这样成熟的框架，基于它提供的分布式并发原语处理分布式的场景。需要注意的是，在使用这些分布式并发原语的时候，你需要考虑异常的情况，比如网络断掉等。同时，分布式并发原语需要网络之间的通讯，所以会比使用标准库中的并发原语耗时更长。

![img](https://static001.geekbang.org/resource/image/a1/23/a18a98aa9ac5de17373c953484ee4c23.jpg?wh=2250*1040)

好了，这节课就到这里，下节课，我会带你继续学习其它的分布式并发原语，包括队列、栅栏和 STM，敬请期待。

## 20 | 在分布式环境中，队列、栅栏和STM该如何实现？

上一讲，我已经带你认识了基于 etcd 实现的 Leader 选举、互斥锁和读写锁，今天，我们来学习下基于 etcd 的分布式队列、栅栏和 STM。只要你学过计算机算法和数据结构相关的知识， 队列这种数据结构你一定不陌生，它是一种先进先出的类型，有出队（dequeue）和入队（enqueue）两种操作。在第 12 讲中，我专门讲到了一种叫做 lock-free 的队列。队列在单机的应用程序中常常使用，但是在分布式环境中，多节点如何并发地执行入队和出队的操作呢？这一讲，我会带你认识一下基于 etcd 实现的分布式队列。除此之外，我还会讲用分布式栅栏编排一组分布式节点同时执行的方法，以及简化多个 key 的操作并且提供事务功能的 STM（Software Transactional Memory，软件事务内存）。

### 分布式队列和优先级队列

前一讲我也讲到，我们并不是从零开始实现一个分布式队列，而是站在 etcd 的肩膀上，利用 etcd 提供的功能实现分布式队列。etcd 集群的可用性由 etcd 集群的维护者来保证，我们不用担心网络分区、节点宕机等问题。我们可以把这些通通交给 etcd 的运维人员，把我们自己的关注点放在使用上。下面，我们就来了解下 etcd 提供的分布式队列。etcd 通过 github.com/coreos/etcd/contrib/recipes 包提供了分布式队列这种数据结构。创建分布式队列的方法非常简单，只有一个，即 NewQueue，你只需要传入 etcd 的 client 和这个队列的名字，就可以了。代码如下：

```go
func NewQueue(client *v3.Client, keyPrefix string) *Queue
```
这个队列只有两个方法，分别是出队和入队，队列中的元素是字符串类型。这两个方法的签名如下所示：

```
// 入队
func (q *Queue) Enqueue(val string) error
//出队
func (q *Queue) Dequeue() (string, error)
```
需要注意的是，如果这个分布式队列当前为空，调用 Dequeue 方法的话，会被阻塞，直到有元素可以出队才返回。既然是分布式的队列，那就意味着，我们可以在一个节点将元素放入队列，在另外一个节点把它取出。在我接下来讲的例子中，你就可以启动两个节点，一个节点往队列中放入元素，一个节点从队列中取出元素，看看是否能正常取出来。etcd 的分布式队列是一种多读多写的队列，所以，你也可以启动多个写节点和多个读节点。下面我们来借助代码，看一下如何实现分布式队列。首先，我们启动一个程序，它会从命令行读取你的命令，然后执行。你可以输入push <value>，将一个元素入队，输入pop，将一个元素弹出。另外，你还可以使用这个程序启动多个实例，用来模拟分布式的环境：

```go
package main


import (
    "bufio"
    "flag"
    "fmt"
    "log"
    "os"
    "strings"


    "github.com/coreos/etcd/clientv3"
    recipe "github.com/coreos/etcd/contrib/recipes"
)


var (
    addr      = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
    queueName = flag.String("name", "my-test-queue", "queue name")
)


func main() {
    flag.Parse()


    // 解析etcd地址
    endpoints := strings.Split(*addr, ",")


    // 创建etcd的client
    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()


    // 创建/获取队列
    q := recipe.NewQueue(cli, *queueName)


    // 从命令行读取命令
    consolescanner := bufio.NewScanner(os.Stdin)
    for consolescanner.Scan() {
        action := consolescanner.Text()
        items := strings.Split(action, " ")
        switch items[0] {
        case "push": // 加入队列
            if len(items) != 2 {
                fmt.Println("must set value to push")
                continue
            }
            q.Enqueue(items[1]) // 入队
        case "pop": // 从队列弹出
            v, err := q.Dequeue() // 出队
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println(v) // 输出出队的元素
        case "quit", "exit": //退出
            return
        default:
            fmt.Println("unknown action")
        }
    }
}
```
我们可以打开两个终端，分别执行这个程序。在第一个终端中执行入队操作，在第二个终端中执行出队操作，并且观察一下出队、入队是否正常。除了刚刚说的分布式队列，etcd 还提供了优先级队列（PriorityQueue）。它的用法和队列类似，也提供了出队和入队的操作，只不过，在入队的时候，除了需要把一个值加入到队列，我们还需要提供 uint16 类型的一个整数，作为此值的优先级，优先级高的元素会优先出队。优先级队列的测试程序如下，你可以在一个节点输入一些不同优先级的元素，在另外一个节点读取出来，看看它们是不是按照优先级顺序弹出的：

```go
package main


import (
    "bufio"
    "flag"
    "fmt"
    "log"
    "os"
    "strconv"
    "strings"


    "github.com/coreos/etcd/clientv3"
    recipe "github.com/coreos/etcd/contrib/recipes"
)


var (
    addr      = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
    queueName = flag.String("name", "my-test-queue", "queue name")
)


func main() {
    flag.Parse()


    // 解析etcd地址
    endpoints := strings.Split(*addr, ",")


    // 创建etcd的client
    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()


    // 创建/获取队列
    q := recipe.NewPriorityQueue(cli, *queueName)


    // 从命令行读取命令
    consolescanner := bufio.NewScanner(os.Stdin)
    for consolescanner.Scan() {
        action := consolescanner.Text()
        items := strings.Split(action, " ")
        switch items[0] {
        case "push": // 加入队列
            if len(items) != 3 {
                fmt.Println("must set value and priority to push")
                continue
            }
            pr, err := strconv.Atoi(items[2]) // 读取优先级
            if err != nil {
                fmt.Println("must set uint16 as priority")
                continue
            }
            q.Enqueue(items[1], uint16(pr)) // 入队
        case "pop": // 从队列弹出
            v, err := q.Dequeue() // 出队
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println(v) // 输出出队的元素
        case "quit", "exit": //退出
            return
        default:
            fmt.Println("unknown action")
        }
    }
}
```
你看，利用 etcd 实现分布式队列和分布式优先队列，就是这么简单。所以，在实际项目中，如果有这类需求的话，你就可以选择用 etcd 实现。不过，在使用分布式并发原语时，除了需要考虑可用性和数据一致性，还需要考虑分布式设计带来的性能损耗问题。所以，在使用之前，你一定要做好性能的评估。

### 分布式栅栏

在第 17 讲中，我们学习了循环栅栏 CyclicBarrier，它和第 6 讲的标准库中的 WaitGroup，本质上是同一类并发原语，都是等待同一组 goroutine 同时执行，或者是等待同一组 goroutine 都完成。在分布式环境中，我们也会遇到这样的场景：一组节点协同工作，共同等待一个信号，在信号未出现前，这些节点会被阻塞住，而一旦信号出现，这些阻塞的节点就会同时开始继续执行下一步的任务。etcd 也提供了相应的分布式并发原语。

Barrier：分布式栅栏。如果持有 Barrier 的节点释放了它，所有等待这个 Barrier 的节点就不会被阻塞，而是会继续执行。DoubleBarrier：计数型栅栏。在初始化计数型栅栏的时候，我们就必须提供参与节点的数量，当这些数量的节点都 Enter 或者 Leave 的时候，这个栅栏就会放开。所以，我们把它称为计数型栅栏。



### Barrier：分布式栅栏

我们先来学习下分布式 Barrier。分布式 Barrier 的创建很简单，你只需要提供 etcd 的 Client 和 Barrier 的名字就可以了，如下所示：

```go
func NewBarrier(client *v3.Client, key string) *Barrier
```
Barrier 提供了三个方法，分别是 Hold、Release 和 Wait，代码如下：

```go
func (b *Barrier) Hold() error
func (b *Barrier) Release() error
func (b *Barrier) Wait() error
```
Hold 方法是创建一个 Barrier。如果 Barrier 已经创建好了，有节点调用它的 Wait 方法，就会被阻塞。Release 方法是释放这个 Barrier，也就是打开栅栏。如果使用了这个方法，所有被阻塞的节点都会被放行，继续执行。Wait 方法会阻塞当前的调用者，直到这个 Barrier 被 release。如果这个栅栏不存在，调用者不会被阻塞，而是会继续执行。

**学习并发原语最好的方式就是使用它**。下面我们就来借助一个例子，来看看 Barrier 该怎么用。你可以在一个终端中运行这个程序，执行"hold""release"命令，模拟栅栏的持有和释放。在另外一个终端中运行这个程序，不断调用"wait"方法，看看是否能正常地跳出阻塞继续执行：

```go
package main


import (
    "bufio"
    "flag"
    "fmt"
    "log"
    "os"
    "strings"


    "github.com/coreos/etcd/clientv3"
    recipe "github.com/coreos/etcd/contrib/recipes"
)


var (
    addr        = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
    barrierName = flag.String("name", "my-test-queue", "barrier name")
)


func main() {
    flag.Parse()


    // 解析etcd地址
    endpoints := strings.Split(*addr, ",")


    // 创建etcd的client
    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()


    // 创建/获取栅栏
    b := recipe.NewBarrier(cli, *barrierName)


    // 从命令行读取命令
    consolescanner := bufio.NewScanner(os.Stdin)
    for consolescanner.Scan() {
        action := consolescanner.Text()
        items := strings.Split(action, " ")
        switch items[0] {
        case "hold": // 持有这个barrier
            b.Hold()
            fmt.Println("hold")
        case "release": // 释放这个barrier
            b.Release()
            fmt.Println("released")
        case "wait": // 等待barrier被释放
            b.Wait()
            fmt.Println("after wait")
        case "quit", "exit": //退出
            return
        default:
            fmt.Println("unknown action")
        }
    }
}
```
### DoubleBarrier：计数型栅栏

etcd 还提供了另外一种栅栏，叫做 DoubleBarrier，这也是一种非常有用的栅栏。这个栅栏初始化的时候需要提供一个计数 count，如下所示：

```go
func NewDoubleBarrier(s *concurrency.Session, key string, count int) *DoubleBarrier
```
同时，它还提供了两个方法，分别是 Enter 和 Leave，代码如下：

```go
func (b *DoubleBarrier) Enter() error
func (b *DoubleBarrier) Leave() error
```
我来解释下这两个方法的作用。当调用者调用 Enter 时，会被阻塞住，直到一共有 count（初始化这个栅栏的时候设定的值）个节点调用了 Enter，这 count 个被阻塞的节点才能继续执行。所以，你可以利用它编排一组节点，让这些节点在同一个时刻开始执行任务。同理，如果你想让一组节点在同一个时刻完成任务，就可以调用 Leave 方法。节点调用 Leave 方法的时候，会被阻塞，直到有 count 个节点，都调用了 Leave 方法，这些节点才能继续执行。我们再来看一下 DoubleBarrier 的使用例子。你可以起两个节点，同时执行 Enter 方法，看看这两个节点是不是先阻塞，之后才继续执行。然后，你再执行 Leave 方法，也观察一下，是不是先阻塞又继续执行的。

```go
package main


import (
    "bufio"
    "flag"
    "fmt"
    "log"
    "os"
    "strings"


    "github.com/coreos/etcd/clientv3"
    "github.com/coreos/etcd/clientv3/concurrency"
    recipe "github.com/coreos/etcd/contrib/recipes"
)


var (
    addr        = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
    barrierName = flag.String("name", "my-test-doublebarrier", "barrier name")
    count       = flag.Int("c", 2, "")
)


func main() {
    flag.Parse()


    // 解析etcd地址
    endpoints := strings.Split(*addr, ",")


    // 创建etcd的client
    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()
    // 创建session
    s1, err := concurrency.NewSession(cli)
    if err != nil {
        log.Fatal(err)
    }
    defer s1.Close()


    // 创建/获取栅栏
    b := recipe.NewDoubleBarrier(s1, *barrierName, *count)


    // 从命令行读取命令
    consolescanner := bufio.NewScanner(os.Stdin)
    for consolescanner.Scan() {
        action := consolescanner.Text()
        items := strings.Split(action, " ")
        switch items[0] {
        case "enter": // 持有这个barrier
            b.Enter()
            fmt.Println("enter")
        case "leave": // 释放这个barrier
            b.Leave()
            fmt.Println("leave")
        case "quit", "exit": //退出
            return
        default:
            fmt.Println("unknown action")
        }
    }
}
```
好了，我们先来简单总结一下。我们在第 17 讲学习的循环栅栏，控制的是同一个进程中的不同 goroutine 的执行，而分布式栅栏和计数型栅栏控制的是不同节点、不同进程的执行。当你需要协调一组分布式节点在某个时间点同时运行的时候，可以考虑 etcd 提供的这组并发原语。

### STM

提到事务，你肯定不陌生。在开发基于数据库的应用程序的时候，我们经常用到事务。事务就是要保证一组操作要么全部成功，要么全部失败。在学习 STM 之前，我们要先了解一下 etcd 的事务以及它的问题。etcd 提供了在一个事务中对多个 key 的更新功能，这一组 key 的操作要么全部成功，要么全部失败。etcd 的事务实现方式是基于 CAS 方式实现的，融合了 Get、Put 和 Delete 操作。etcd 的事务操作如下，分为条件块、成功块和失败块，条件块用来检测事务是否成功，如果成功，就执行 Then(...)，如果失败，就执行 Else(...)：

```
Txn().If(cond1, cond2, ...).Then(op1, op2, ...,).Else(op1’, op2’, …)
```
我们来看一个利用 etcd 的事务实现转账的小例子。我们从账户 from 向账户 to 转账 amount，代码如下：

```go
func doTxnXfer(etcd *v3.Client, from, to string, amount uint) (bool, error) {
    // 一个查询事务
    getresp, err := etcd.Txn(ctx.TODO()).Then(OpGet(from), OpGet(to)).Commit()
    if err != nil {
         return false, err
    }
    // 获取转账账户的值
    fromKV := getresp.Responses[0].GetRangeResponse().Kvs[0]
    toKV := getresp.Responses[1].GetRangeResponse().Kvs[1]
    fromV, toV := toUInt64(fromKV.Value), toUint64(toKV.Value)
    if fromV < amount {
        return false, fmt.Errorf(“insufficient value”)
    }
    // 转账事务
    // 条件块
    txn := etcd.Txn(ctx.TODO()).If(
        v3.Compare(v3.ModRevision(from), “=”, fromKV.ModRevision),
        v3.Compare(v3.ModRevision(to), “=”, toKV.ModRevision))
    // 成功块
    txn = txn.Then(
        OpPut(from, fromUint64(fromV - amount)),
        OpPut(to, fromUint64(toV + amount))
    //提交事务 
    putresp, err := txn.Commit()
    // 检查事务的执行结果
    if err != nil {
        return false, err
    }
    return putresp.Succeeded, nil
}
```
从刚刚的这段代码中，我们可以看到，虽然可以利用 etcd 实现事务操作，但是逻辑还是比较复杂的。因为事务使用起来非常麻烦，所以 etcd 又在这些基础 API 上进行了封装，新增了一种叫做 STM 的操作，提供了更加便利的方法。下面我们来看一看 STM 怎么用。要使用 STM，你需要先编写一个 apply 函数，这个函数的执行是在一个事务之中的：

```
apply func(STM) error
```
这个方法包含一个 STM 类型的参数，它提供了对 key 值的读写操作。STM 提供了 4 个方法，分别是 Get、Put、Receive 和 Delete，代码如下：

```
type STM interface {
  Get(key ...string) string
  Put(key, val string, opts ...v3.OpOption)
  Rev(key string) int64
  Del(key string)
}
```
使用 etcd STM 的时候，我们只需要定义一个 apply 方法，比如说转账方法 exchange，然后通过 concurrency.NewSTM(cli, exchange)，就可以完成转账事务的执行了。STM 咋用呢？我们还是借助一个例子来学习下。下面这个例子创建了 5 个银行账号，然后随机选择一些账号两两转账。在转账的时候，要把源账号一半的钱要转给目标账号。这个例子启动了 10 个 goroutine 去执行这些事务，每个 goroutine 要完成 100 个事务。为了确认事务是否出错了，我们最后要校验每个账号的钱数和总钱数。总钱数不变，就代表执行成功了。这个例子的代码如下：

```go
package main


import (
    "context"
    "flag"
    "fmt"
    "log"
    "math/rand"
    "strings"
    "sync"


    "github.com/coreos/etcd/clientv3"
    "github.com/coreos/etcd/clientv3/concurrency"
)


var (
    addr = flag.String("addr", "http://127.0.0.1:2379", "etcd addresses")
)


func main() {
    flag.Parse()


    // 解析etcd地址
    endpoints := strings.Split(*addr, ",")


    cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
    if err != nil {
        log.Fatal(err)
    }
    defer cli.Close()


    // 设置5个账户，每个账号都有100元，总共500元
    totalAccounts := 5
    for i := 0; i < totalAccounts; i++ {
        k := fmt.Sprintf("accts/%d", i)
        if _, err = cli.Put(context.TODO(), k, "100"); err != nil {
            log.Fatal(err)
        }
    }


    // STM的应用函数，主要的事务逻辑
    exchange := func(stm concurrency.STM) error {
        // 随机得到两个转账账号
        from, to := rand.Intn(totalAccounts), rand.Intn(totalAccounts)
        if from == to {
            // 自己不和自己转账
            return nil
        }
        // 读取账号的值
        fromK, toK := fmt.Sprintf("accts/%d", from), fmt.Sprintf("accts/%d", to)
        fromV, toV := stm.Get(fromK), stm.Get(toK)
        fromInt, toInt := 0, 0
        fmt.Sscanf(fromV, "%d", &fromInt)
        fmt.Sscanf(toV, "%d", &toInt)


        // 把源账号一半的钱转账给目标账号
        xfer := fromInt / 2
        fromInt, toInt = fromInt-xfer, toInt+xfer


        // 把转账后的值写回
        stm.Put(fromK, fmt.Sprintf("%d", fromInt))
        stm.Put(toK, fmt.Sprintf("%d", toInt))
        return nil
    }


    // 启动10个goroutine进行转账操作
    var wg sync.WaitGroup
    wg.Add(10)
    for i := 0; i < 10; i++ {
        go func() {
            defer wg.Done()
            for j := 0; j < 100; j++ {
                if _, serr := concurrency.NewSTM(cli, exchange); serr != nil {
                    log.Fatal(serr)
                }
            }
        }()
    }
    wg.Wait()


    // 检查账号最后的数目
    sum := 0
    accts, err := cli.Get(context.TODO(), "accts/", clientv3.WithPrefix()) // 得到所有账号
    if err != nil {
        log.Fatal(err)
    }
    for _, kv := range accts.Kvs { // 遍历账号的值
        v := 0
        fmt.Sscanf(string(kv.Value), "%d", &v)
        sum += v
        log.Printf("account %s: %d", kv.Key, v)
    }


    log.Println("account sum is", sum) // 总数
}
```
总结一下，当你利用 etcd 做存储时，是可以利用 STM 实现事务操作的，一个事务可以包含多个账号的数据更改操作，事务能够保证这些更改要么全成功，要么全失败。

### 总结

如果我们把眼光放得更宽广一些，其实并不只是 etcd 提供了这些并发原语，比如我上节课一开始就提到了，Zookeeper 很早也提供了类似的并发原语，只不过只提供了 Java 的库，并没有提供合适的 Go 库。另外，根据 Consul 官方的反馈，他们并没有开发这些并发原语的计划，所以，从目前来看，etcd 是个不错的选择。当然，也有一些其它不太知名的分布式原语库，但是活跃度不高，可用性低，所以我们也不需要去了解了。其实，你也可以使用 Redis 实现分布式锁，或者是基于 MySQL 实现分布式锁，这也是常用的选择。对于大厂来说，选择起来是非常简单的，只需要看看厂内提供了哪个基础服务，哪个更稳定些。对于没有 etcd、Redis 这些基础服务的公司来说，很重要的一点，就是自己搭建一套这样的基础服务，并且运维好，这就需要考察你们对 etcd、Redis、MySQL 的技术把控能力了，哪个用得更顺手，就用哪个。一般来说，我不建议你自己去实现分布式原语，最好是直接使用 etcd、Redis 这些成熟的软件提供的功能，这也意味着，我们将程序的风险转嫁到了这些基础服务上，这些基础服务必须要能够提供足够的服务保障。

![img](https://static001.geekbang.org/resource/image/c0/1d/c0d48fd09b91685c836829570fdc7b1d.jpg?wh=2250*1452)

## 结束语 | 再聊Go并发编程的价值和精进之路

在过去的这些年里，我一直在研究 Go 并发编程，时间越久，越觉得，掌握 Go 并发原语是一件很有意思的事情。很多刚开始学习并发原语的同学给我留言说：“使用 Go 写并发程序很容易啊，为啥要学这么多并发原语呢？”如果你也有这样的疑问，我的答案就是在这节课的封面图中写的那句话：“并发原语，初识时简单，深交时复杂，熟识时又觉简单。”这是我的真实体会。

如果你处于刚开始接触并发原语的阶段，你可能会觉得：“这挺好理解的呀，我一看就会了。”但是随着学习的不断深入，你会看到各种复杂的用法，各种潜在的坑，这些东西打破了初印象，你会陷入到“千头万绪”的境地。只要你不畏困难，持续学习，最后你就可以轻松地使用这些并发原语了。如果说最初的“简单”是“初生牛犊不怕虎”的“简单”，那么“熟识”后的“简单”，就是“拨云见雾”的“简单”。这也是，我在这门课里想要带你达到的状态。

总之，使用 Go 写并发程序很容易，使用 Go 写好并发程序很不容易。

遗憾的是，很多人都没有意识并发编程的复杂性，甚至还没有意识到，并发编程错误带来的严重后果。所以，我想跟你分享关于并发编程 Bug 的两个小故事。

第一个故事，是我刚刚看到的澳大利亚交易所（ASX）的新系统在上线后崩溃的故事。11 月 16 日中午，ASX 发布声明说，当天将休市，会在次日的正常时间重新开放。官方给出的关闭原因是“局限于单个交易指令中交易多种证券（组合交易）的软件问题，导致了市场数据不准确。”虽然我并没有看到这个 Bug 的细节，但是，从官方提供的关闭原因中，我们可以简单地推断出是“单个指令中交易多种证券的问题”，大概率是一个并发问题的 Bug。虽然经过一天的排查和修复，第二天这个交易所就恢复上线了。但是，耽误一天的时间，损失也是非常大的。类似的软件 Bug，尤其是并发问题的 Bug，即使经过很长时间的测试，也不一定能被触发和发现。可是一旦出现，就可能是一个一级的 Bug。

如果看完这个故事，你还没有意识到并发编程的复杂性和并发问题的危害，我再给你讲一个故事。

1997 年 7 月，NASA 的 Mars Pathfinder（火星探路者）在降落火星表面后不久，就因并发软件中的一个缺陷受到了威胁。这是在飞行前的测试中发现的，但因为它只发生在某些没有预料到的重载条件下，所以被给予了较低的优先级。但是，飞船开始采集气象数据的时候，它所使用的 vxWorks 操作系统就出现了问题，不断地重启。这是经典的优先级反转的并发 Bug。幸好工程师上传了一小段 C 语言程序给飞船，在运行的时候，将优先级继承的互斥标志从 false 改成了 true，才成功地解决了这个 Bug。这次人为的忽视，险些酿成惨剧。所以，学好并发编程，是我们的重要责任。

那么，该怎么在编写 Go 程序时，避免并发编程的 Bug 呢？在开篇词里，我讲到了“两大主线”，现在学完了所有内容之后，你会发现，其实可以抽象成“三部曲”：全面地掌握 Go 并发编程的知识，不遗漏任何的知识点；熟悉每一个并发原语的功能和实现，在面对并发场景时，能够高效地选出最适合的并发原语；多看看别人踩的坑，避免自己再掉进相同的坑里。

在前面的课程中，我讲的所有内容，都是为了帮助你轻松地完成这三个目标。在课程的最后，我还想再给你多交代几句。学完这门课，并不代表你已经掌握了 Go 并发编程的知识。Go 并发编程的知识广、内容深，现在你再回顾前面的知识，可能已经遗忘了一大半了。即使你现在记得很清楚，等过一段时间，再提到这些知识点，你也可能答不上来。所以，学完这门课并不是一件一劳永逸的事情，你要在空闲的时候多复习下前面的内容。怎么复习呢？你可能也注意到了，每讲完一个并发原语，课程里都有一张知识导图，这些图既可以帮助你梳理知识主线，也可以帮助你高效地复习。你可以对照着图中的核心要点，去回顾我们学习的重要内容，如果感觉有些地方比较陌生了，就及时回去复习下。另外，你也可以做一些摘录，并且写上你自己的收获和思考。**学习过不等于能输出**，你一定要记住这句话。

另外，这门课的核心是讲 Go 并发原语的知识，并没有涉及到 Go 并发模型和调度的事情。这不是说，我认为这部分内容不重要，而是很多大牛已经把这些内容写得很清楚、很明白了。如果你对这方面的知识还不太熟悉，可以搜索关键字“golang gpm schedule”，你会看到很多资料。你读几篇，就明白了。如果要推荐的话，我建议你重点读一读欧长坤的 [《Go 语言原本》的 并发调度](https://golang.design/under-the-hood/zh-cn/part2runtime/ch06sched/)，这一篇的逻辑非常顺畅，能看出非常多的经验。

当然，我还想再给你推荐一些补充资料，如果你还有余力，可以再扩展一下知识面。首先是一本书，名字是“Concurrency in Go”。这是第一本全面介绍 Go 并发编程的图书。书中介绍了并发编程的背景知识、常见的原语和并发模式。我印象最深的，就是书里对 Channel 的介绍，比如 Channel 是粘合 goroutine 的胶水，而 select 是粘合 Channel 的胶水。这样形象的说法，可以帮助你快速地学到精髓。除此之外，Go 官方博客列出的一些技术分享，比如[Go Concurrency Patterns](https://www.youtube.com/watch?v=f6kdp27TYZs)、[Advanced Go Concurrency Patterns](https://www.youtube.com/watch?v=QDDwwePbDtw)，都是不错的阅读材料，我建议你好好读一读。

好了，关于结课后的学习方法，我就说到这里。在这节课的最后，我特别想再和你分享我自己的两个心得。

第一，开放的心态，可以拓展你的人生边界。

第二，无数人想要你的注意力，但只有你能决定你把它放在哪里。






























































































































































































































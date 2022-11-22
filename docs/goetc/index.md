# go


- [Go](#go)
  - [其他](#其他)
    - [和其他语言对比](#和其他语言对比)
    - [3、Go 程序中的包是什么？](#3go-程序中的包是什么)
    - [6、 如何在运行时检查变量类型？](#6-如何在运行时检查变量类型)
    - [go包初始化顺序](#go包初始化顺序)
    - [12、Go Convey 是什么？一般用来做什么？](#12go-convey-是什么一般用来做什么)
    - [new make](#new-make)
  - [atomic](#atomic)
    - [原子操作](#原子操作)
    - [13、原子操作和锁的区别](#13原子操作和锁的区别)
    - [14、什么是 CAS](#14什么是-cas)
  - [slice](#slice)
    - [21、Golang Slice 的底层实现](#21golang-slice-的底层实现)
    - [切片和数组的区别？](#切片和数组的区别)
    - [切片的访问的复杂度？](#切片的访问的复杂度)
    - [切片的扩容机制？](#切片的扩容机制)
    - [为什么切片底层不用链表？](#为什么切片底层不用链表)
  - [map](#map)
    - [25、Golang Map 底层实现](#25golang-map-底层实现)
    - [map 的扩容机制？](#map-的扩容机制)
    - [27、Golang Map 查找](#27golang-map-查找)
    - [map 的存储是有序的吗？](#map-的存储是有序的吗)
    - [map 是线程安全的吗？](#map-是线程安全的吗)
    - [map 有缩容机制吗？](#map-有缩容机制吗)
  - [channel](#channel)
    - [9、Go 语言当中 Channel（通道）有什么特点，需要注意什么？](#9go-语言当中-channel通道有什么特点需要注意什么)
    - [10、Go 语言当中 Channel 缓冲有什么特点？](#10go-语言当中-channel-缓冲有什么特点)
    - [11、Go 语言中 cap 函数可以作用于哪些内容？](#11go-语言中-cap-函数可以作用于哪些内容)
    - [14、Go 语言中 make 的作用是什么？](#14go-语言中-make-的作用是什么)
    - [24、Golang 的参数传递、引用类型](#24golang-的参数传递引用类型)
    - [28、介绍一下 Channel](#28介绍一下-channel)
    - [29、Go 语言的 Channel 特性？](#29go-语言的-channel-特性)
    - [30、Channel 的 ring buffer 实现](#30channel-的-ring-buffer-实现)
    - [6、Cond 是什么](#6cond-是什么)
    - [7、Broadcast 和 Signal 区别](#7broadcast-和-signal-区别)
    - [8、Cond 中 Wait 使用](#8cond-中-wait-使用)
    - [9、WaitGroup 用法](#9waitgroup-用法)
    - [10、WaitGroup 实现原理](#10waitgroup-实现原理)
  - [GMP](#gmp)
    - [GMP 模型中的 GMP 指的是什么](#gmp-模型中的-gmp-指的是什么)
    - [GMP 模型中有什么组件](#gmp-模型中有什么组件)
    - [3、1.0 之前 GM 调度模型](#310-之前-gm-调度模型)
    - [GMP 调度流程](#gmp-调度流程)
    - [5、GMP 中 work stealing 机制](#5gmp-中-work-stealing-机制)
    - [6、GMP 中 hand off 机制](#6gmp-中-hand-off-机制)
    - [7、协作式的抢占式调度](#7协作式的抢占式调度)
    - [8、基于信号的抢占式调度](#8基于信号的抢占式调度)
    - [9、GMP 调度过程中存在哪些阻塞](#9gmp-调度过程中存在哪些阻塞)
    - [10、Sysmon 有什么作用](#10sysmon-有什么作用)
    - [GMP 模型中为什么需要 P](#gmp-模型中为什么需要-p)
    - [P 调度器的设计策略](#p-调度器的设计策略)
  - [sync](#sync)
    - [11、什么是 sync.Once](#11什么是-synconce)
    - [15、sync.Pool 有什么用](#15syncpool-有什么用)
    - [sync.map 为什么是线程安全的](#syncmap-为什么是线程安全的)
    - [8、Go 当中同步锁有什么特点？作用是什么](#8go-当中同步锁有什么特点作用是什么)
    - [1、Mutex 几种状态](#1mutex-几种状态)
    - [2、Mutex 正常模式和饥饿模式](#2mutex-正常模式和饥饿模式)
    - [3、Mutex 允许自旋的条件](#3mutex-允许自旋的条件)
    - [sync.mutex 作用](#syncmutex-作用)
    - [4、RWMutex 实现](#4rwmutex-实现)
    - [sync.RWmutex 作用](#syncrwmutex-作用)
    - [5、RWMutex 注意事项](#5rwmutex-注意事项)
    - [sync.singleflight 作用](#syncsingleflight-作用)
  - [context](#context)
    - [context 有什么作用](#context-有什么作用)
  - [goroutine](#goroutine)
    - [5、什么是 Goroutine？你如何停止它？](#5什么是-goroutine你如何停止它)
    - [进程、线程和协程的区别](#进程线程和协程的区别)
    - [协程的好处](#协程的好处)
  - [defer](#defer)
    - [defer底层原理](#defer底层原理)
    - [defer配合recover](#defer配合recover)
    - [defer 的常用场景：](#defer-的常用场景)
  - [interface](#interface)
    - [7、Go 两个接口之间可以存在什么关系？](#7go-两个接口之间可以存在什么关系)
  - [goroutine](#goroutine-1)
    - [goroutine与线程的区别](#goroutine与线程的区别)
    - [goroutine调度机制](#goroutine调度机制)
    - [goroutine调度流程](#goroutine调度流程)
  - [GC](#gc)
    - [常用的垃圾回收的方法:](#常用的垃圾回收的方法)
    - [11、三色标记原理](#11三色标记原理)
    - [12、写屏障](#12写屏障)
    - [13、插入写屏障](#13插入写屏障)
    - [14、删除写屏障](#14删除写屏障)
    - [15、混合写屏障](#15混合写屏障)
    - [16、GC 触发时机](#16gc-触发时机)
    - [17、Go 语言中 GC 的流程是什么？](#17go-语言中-gc-的流程是什么)
    - [18、GC 如何调优](#18gc-如何调优)
##  Go
> 熟悉Go，了解Map，Slice，Channel，GMP模型调度器，GC垃圾回收等底层原理

### 其他
#### 和其他语言对比
1. 原生支持并发
2. 单一的标准代码格式
3. GC与程序同时执行
#### 3、Go 程序中的包是什么？
包 (pkg) 是 Go 工作区中包含 Go 源文件或其他包的目录。每个 Go 源文件都属于一个包
#### 6、 如何在运行时检查变量类型？
类型开关(Type Switch)是在运行时检查变量类型的最佳方式。类型开关按类型而不是值来评估变量。每个 Switch 至少包含一个 case 用作条件语句，如果没有一个 case 为真，则执行 default。
```go
func do(i interface{}) {
 switch v := i.(type) {
 case int:
  fmt.Printf("Twice %v is %v\n", v, v*2)
 case string:
  fmt.Printf("%q is %v bytes long\n", v, len(v))
 default:
  fmt.Printf("I don't know about type %T!\n", v)
 }
}
func main() {
 do(21)
 do("hello")
 do(true)
}
```
#### go包初始化顺序
**从main包开始按照深度优先初始化main包的依赖包，初始化一个包时的顺序是初始化依赖包、常量、变量、init()，回到main包时同样初始化常量、变量、init()，再执行main()函数。**

#### 12、Go Convey 是什么？一般用来做什么？
- go convey 是一个支持 Golang 的单元测试框架
- go convey 能够自动监控文件修改并启动测试，并可以将测试结果实时输出到 Web 界面
- go convey 提供了丰富的断言简化测试用例的编写
#### new make 
new 初始化一个值类型的指针
make 初始化一个引用类型的实例

### atomic
#### 原子操作
原子操作即是进行过程中不能被中断的操作，针对某个值的原子操作在被进行的过程中，CPU 绝不会再去进行其他的针对该值的操作。为了实现这样的严谨性，原子操作仅会由一个独立的 CPU 指令代表和完成。原子操作是无锁的，**常常直接通过 CPU 指令直接实现**。 事实上，其它同步技术的实现常常依赖于原子操作。

#### 13、原子操作和锁的区别
原子操作由底层硬件支持，而锁则由操作系统的调度器实现。
锁应当用来保护一段逻辑，对于一个变量更新的保护。
原子操作通常执行上会更有效率，并且更能利用计算机多核的优势，如果要更新的是一个复合对象，则应当使用 atomic.Value 封装好的实现。

#### 14、什么是 CAS
CAS 的全称为 Compare And Swap，直译就是比较交换。是一条 **CPU 的原子指令**，其作用是**让 CPU 先进行比较两个值是否相等，然后原子地更新某个位置的值，其实现方式是给予硬件平台的汇编指令**，在 intel 的 CPU 中，使用的cmpxchg 指令，就是说 CAS 是靠硬件实现的，从而在硬件层面提升效率。

简述过程是这样： 
假设包含 3 个参数内存位置(V)、预期原值(A)和新值(B)。V 表示要更新变量的值，E 表示预期值，N 表示新值。仅当 V 值等于 E 值时，才会将 V 的值设为 N，如果 V 值和 E 值不同，则说明已经有其他线程在做更新，则当前线程什么都不做，最后 CAS 返回当前 V 的真实值。CAS 操作时抱着乐观的态度进行的，它总是认为自己可以成功完成操作。基于这样的原理，CAS 操作即使没有锁，也可以发现其他线程对于当前线程的干扰。
### slice

#### 21、Golang Slice 的底层实现
切片是基于数组实现的，它的底层是数组，它自己本身非常小，可以理解为对底层数组的抽象。切片本身是一个只读对象，其工作机制类似数组指针的一种封装。
切片对象非常小，是因为它是只有 3 个字段的数据结构：
- 指向底层数组的指针
- 切片的长度
- 切片的容量

#### 切片和数组的区别？

数据类型不同。这是从数据结构上决定的，因为数组的数据结构只有元素类型和长度，而切片的数据结构是一个结构体，结构体有一个指向数组的指针，长度和容量，所以切片是一个引用类型而不是像数组一样的不可变的值类型。

额外一点说，在 go 中虽然函数传递中是值传递的，但是由于切片的数据结构中的结构体里面包含了指针，所以函数传递的时候会将指针的值拷贝走，从结果来说会操作到同一个切片。

#### 切片的访问的复杂度？

切片的底层实现是一块连续的内存地址，所以能进行 O(1) 的随机访问元素。 具体的源码都是在编译时编译好（类似于 内存地址=元素类型*下标，不在此处细究。 

#### 切片的扩容机制？

当扩容的时候需要扩容的容量是现在容量的 2 倍以上时，会直接扩容需要扩容的容量。否则的话，如果现在的长度（1.16 改成了容量）是 1024 以下时，会直接翻倍扩容。但是如果超过 1024 时，会反复扩容 25 % 直到达到或超过需要扩容的容量

#### 为什么切片底层不用链表？

1. 设计上，切片的设计上允许随机访问，而随机访问对于链表是不友好的 		
2. 容量上，切片的扩容机制无论从小切片是翻倍扩容，还是到大切片不断扩容 25 % 直至扩容到指定值的扩容机制，在容量上已经能覆盖大多数的场景内存上，使用链表会产生内存碎片，并且对于 Go 的垃圾回收算法（三色标记法）在递归根对象寻找可达对象的过程中不友好

---

### map
#### 25、Golang Map 底层实现
Golang 中 map 的底层实现是一个散列表，因此实现 map 的过程实际上就是实现散表的过程。在这个散列表中，主要出现的结构体有两个，一个叫 hmap(a header for a go map,包含一些信息比如键值对数量，指向哈希桶数组的指针等等)，一个叫 bmap(a bucket for a Go map，通常叫其bucket,Go map 的一个哈希桶，一个桶最多存放8个键值对)。

**当往map中存储一个kv对时，通过k获取hash值，hash值的低八位和bucket数组长度取余，定位到在数组中的那个下标，hash值的高八位存储在bucket中的tophash中，用来快速判断key是否存在，当一个bucket满时，通过overfolw指针链接到下一个bucket。**


#### map 的扩容机制？

分为了翻倍扩容、等量扩容。还有确定了要扩容后，使用的是渐进式扩容去避免性能抖动。 	

扩容的触发条件有两个，状态装载因子超过 6.5（现在的桶装在的元素超过 80 %），也就是元素太多了。

或者溢出桶太多（逻辑是判断现在桶的总数是否超过 map 结构体中的设置值，如果超过说明太多溢出桶），也就是太多元素被删除，元素非常稀疏。 

前者会使用翻倍扩容，会申请现有翻倍的空间。当有读操作时仍会访问旧桶，当有插入或者删除操作的时候会将旧桶分流到两个新桶。后者会使用等倍扩容，重新申请一块和现有大小相同的新桶，也是使用渐进式扩容在插入或者删除操作中进行。

1. 双倍扩容：扩容采取了一种称为“渐进式”的方式，原有的 key 并不会一次性搬迁完毕，每次最多只会搬迁 2 个 bucket。
2. 等量扩容：重新排列，极端情况下，重新排列也解决不了，map 存储就会蜕变成链表，性能大大降低，此时哈希因子 hash0 的设置，可以降低此类极端场景的发生。

#### 27、Golang Map 查找
Go 语言中 map 采用的是哈希查找表，由一个 key 通过哈希函数得到哈希值，64位系统中就生成一个 64bit 的哈希值，由这个哈希值将 key 对应存到不同的桶
（bucket）中，**当有多个哈希映射到相同的的桶中时，使用链表解决哈希冲突。**

细节：key 经过 hash 后共 64 位，根据 hmap 中 B 的值，计算它到底要落在哪个桶时，桶的数量为 2^B，如 B=5，那么用 64 位最后 5 位表示第几号桶，在用hash 值的高 8 位确定在 bucket 中的存储位置，当前 bmap 中的 bucket 未找到，则查询对应的 overflow bucket，对应位置有数据则对比完整的哈希值，确定是否是要查找的数据。如果当前 map 处于数据搬移状态，则优先从oldbuckets 查找。

#### map 的存储是有序的吗？

​	存储结构上**由于在扩容的时候会将 key 随机分流到另外两个桶**，这导致了 key  的相对位置会发生改变，所以 key 是无序的。 遍历上来说，每次遍历是取一个随机数，随机从一个桶开始遍历。所以每次遍历出来的结果都是不一样的。额外一点是，当未发生扩容前，key 的相对位置是确定的。

#### map 是线程安全的吗？

不是的，当赋值和删除时是置写操作位。当置了之后有别的协程用任何操作进行来时否都会报错。如果想要 map 线程安全，解决方案一般是用 **sync.map** 或者 **互斥锁+map**

#### map 有缩容机制吗？

go 的 map 没有缩容的机制。map 内部的存储结构是基于拉链法的，里面的元素如果被大批量的删除后，会触发等量扩容。等量扩容时会申请原有大小一样的内存块，渐进式的扩容过去，让原有的 map 中因为很多元素被删除后导致元素排序稀疏的情况经过 rehash 后会排序会变得紧密，减少溢出桶的使用。

---
### channel

#### 9、Go 语言当中 Channel（通道）有什么特点，需要注意什么？
如果给一个 nil 的 channel 发送数据，会造成永远阻塞。
如果从一个 nil 的 channel 中接收数据，也会造成永久阻塞。
给一个已经关闭的 channel 发送数据， 会引起 panic
从一个已经关闭的 channel 接收数据， 如果缓冲区中为空，则返回一个零
值。
#### 10、Go 语言当中 Channel 缓冲有什么特点？
无缓冲的 channel 是同步的，而有缓冲的 channel 是非同步的。

#### 11、Go 语言中 cap 函数可以作用于哪些内容？
可以作用于的类型有：
- array(数组)
- slice(切片)
- channel(通道)
#### 14、Go 语言中 make 的作用是什么？
make 的作用是为 slice, map or chan 的初始化 然后返回引用 make 函数是内
建函数，函数定义：
func make(Type, size IntegerType) Type
make(T, args)函数的目的和 new(T)不同 仅仅用于创建 slice, map, 
channel 而且返回类型是实例
#### 24、Golang 的参数传递、引用类型
Go 语言中所有的传参都是值传递（传值），都是一个副本，一个拷贝。因为拷
贝的内容有时候是非引用类型（int、string、struct 等这些），这样就在函
数中就无法修改原内容数据；有的是引用类型（指针、map、slice、chan 等这
些），这样就可以修改原内容数据。
Golang 的引用类型包括 slice、map 和 channel。它们有复杂的内部结构，除
了申请内存外，还需要初始化相关属性。内置函数 new 计算类型大小，为其分
配零值内存，返回指针。而 make 会被编译器翻译成具体的创建函数，由其分
配内存和初始化成员结构，返回对象而非指针。
#### 28、介绍一下 Channel
Go 语言中，不要通过共享内存来通信，而要通过通信来实现内存共享。Go 的
CSP(Communicating Sequential Process)并发模型，中文可以叫做通信顺序进
程，是通过 goroutine 和 channel 来实现的。
channel 收发遵循先进先出 FIFO 的原则。分为有缓冲区和无缓冲区，channel
中包括 buffer、sendx 和 recvx 收发的位置(ring buffer 记录实现)、sendq、
recv。当 channel 因为缓冲区不足而阻塞了队列，则使用双向链表存储。

#### 29、Go 语言的 Channel 特性？
1. 给一个 nil channel 发送数据，造成永远阻塞
2. 从一个 nil channel 接收数据，造成永远阻塞
3. 给一个已经关闭的 channel 发送数据，引起 panic
4. 从一个已经关闭的 channel 接收数据，如果缓冲区中为空，则返回一个零
值
5. 无缓冲的 channel 是同步的，而有缓冲的 channel 是非同步的
6. 关闭一个 nil channel 将会发生 panic

#### 30、Channel 的 ring buffer 实现
channel 底层是一个hchan结构体，里边字段挺多的，

```go
type hchan struct {
 qcount   uint   
 // channel 里的元素计数
 dataqsiz uint   
 // 可以缓冲的数量，如 ch := make(chan int, 10)。 此处的 10 即 dataqsiz
 elemsize uint16 
 // 要发送或接收的数据类型大小
 buf      unsafe.Pointer 
 // 当 channel 设置了缓冲数量时，该 buf 指向一个存储缓冲数据的区域，该区域是一个循环队列的数据结构
 closed   uint32 
 // 关闭状态
 sendx    uint  
 // 当 channel 设置了缓冲数量时，数据区域即循环队列此时已发送数据的索引位置
 recvx    uint  
 // 当 channel 设置了缓冲数量时，数据区域即循环队列此时已接收数据的索引位置
 recvq    waitq 
 // 想读取数据但又被阻塞住的 goroutine 队列
 sendq    waitq 
 // 想发送数据但又被阻塞住的 goroutine 队列
 
 lock mutex
 ...
}
```
重要的字段：buf 指向一个循环队列，存储缓冲数据也就是ring buffer
已发送数据的索引位置 sendx
已接受数据的索引位置 recvx

channel 中使用了 ring buffer（环形缓冲区) 来缓存写入的数据。ring buffer 有很多好处，而且非常适合用来实现 FIFO 式的固定长度队列。
在 channel 中，ring buffer 的实现如下：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221118182058.png)
上图展示的是一个缓冲区为 8 的 channel buffer，recvx 指向最早被读取的数据，sendx 指向再次写入时插入的位置。

#### 6、Cond 是什么
https://geektutu.com/post/hpg-sync-cond.html

sync.Cond 条件变量用来协调想要访问共享资源的那些 goroutine，当共享资源的状态发生变化的时候，它可以用来通知被互斥锁阻塞的 goroutine。

sync.Cond 经常用在多个 goroutine 等待，一个 goroutine 通知（事件发生）的场景。

有一个协程在异步地接收数据，剩下的多个协程必须等待这个协程接收完数据，才能读取到正确的数据。在这种情况下，如果单纯使用 chan 或互斥锁，那么只能有一个协程可以等待，并读取到数据，没办法通知其他的协程也读取数据。

这个时候，就需要有个全局的变量来标志第一个协程数据是否接受完毕，剩下的协程，反复检查该变量的值，直到满足要求。或者创建多个 channel，每个协程阻塞在一个 channel 上，由接收数据的协程在数据接收完毕后，逐个通知。总之，需要额外的复杂度来完成这件事。

Go 语言在标准库 sync 中内置一个 sync.Cond 用来解决这类问题。

```go
// Each Cond has an associated Locker L (often a \*Mutex or \*RWMutex),  
// which must be held when changing the condition and  
// when calling the Wait method.  
//  
// A Cond must not be copied after first use.  
type Cond struct {  
 noCopy noCopy  
  
 // L is held while observing or changing the condition  
 L Locker  
  
 notify  notifyList  
 checker copyChecker  
}  
```

每个 Cond 实例都会关联一个锁 L（互斥锁 *Mutex，或读写锁 *RWMutex），**当修改条件或者调用 Wait 方法时，必须加锁。**

和 sync.Cond 相关的有如下几个方法：NewCond Broadcast Signal wait

接下来我们实现一个简单的例子，三个协程调用 Wait() 等待，另一个协程调用 Broadcast() 唤醒所有等待的协程。
```go
var done = false

func read(name string, c *sync.Cond) {
	c.L.Lock()
	for !done {
		c.Wait()
	}
	log.Println(name, "starts reading")
	c.L.Unlock()
}

func write(name string, c *sync.Cond) {
	log.Println(name, "starts writing")
	time.Sleep(time.Second)
	c.L.Lock()
	done = true
	c.L.Unlock()
	log.Println(name, "wakes all")
	c.Broadcast()
}

func main() {
	cond := sync.NewCond(&sync.Mutex{})

	go read("reader1", cond)
	go read("reader2", cond)
	go read("reader3", cond)
	write("writer", cond)

	time.Sleep(time.Second * 3)
}
```
```
2009/11/10 23:00:00 writer starts writing
2009/11/10 23:00:01 writer wakes all
2009/11/10 23:00:01 reader1 starts reading
2009/11/10 23:00:01 reader3 starts reading
2009/11/10 23:00:01 reader2 starts reading
```
#### 7、Broadcast 和 Signal 区别
`func (c *Cond) Broadcast()`
Broadcast 会唤醒所有等待 c 的 goroutine。
调用 Broadcast 的时候，可以加锁，也可以不加锁。
`func (c *Cond) Signal()`
Signal 只唤醒 1 个等待 c 的 goroutine。
调用 Signal 的时候，可以加锁，也可以不加锁。
#### 8、Cond 中 Wait 使用
`func (c *Cond) Wait()`
Wait()**会自动释放 c.L 锁，并挂起调用者的 goroutine**。之后恢复执行，
Wait()会在返回时对 c.L 加锁。
**除非被 Signal 或者 Broadcast 唤醒，否则 Wait()不会返回。**
由于 Wait()第一次恢复时，C.L 并没有加锁，所以当 Wait 返回时，调用者通常并不能假设条件为真。如下代码：
取而代之的是, 调用者应该在循环中调用 Wait。（简单来说，只要想使用condition，就必须加锁。）
```go
c.L.Lock()
for !condition() {
 c.Wait()
}
... make use of condition ...
c.L.Unlock()
```
#### 9、WaitGroup 用法
一个 WaitGroup 对象可以等待一组协程结束。使用方法是：
1. main 协程通过调用 wg.Add(delta int) 设置 worker 协程的个数，然后创建 worker 协程；
2. worker 协程执行结束以后，都要调用 wg.Done()；
3. main 协程调用 wg.Wait() 且被 block，直到所有 worker 协程全部执行结束后返回。


#### 10、WaitGroup 实现原理
https://juejin.cn/post/7120548108966035470

- WaitGroup 底层是一个结构体，逻辑上包含两个关键属性，state(counter waiter) sema,三者都是32位的，由两个字段得到，state1(uint64),state2(uint32)，考虑到内存对齐以及并发安全对不同位数os有不同的设计
- `counter` 代表目前尚未调用的`WaitGroup.Done()` 的 groutine 的个数。`WaitGroup.Add(n)` 将会导致 `counter += n`, 而 `WaitGroup.Done()` 将导致 `counter--`。\*
- `waiter` 代表目前已调用 `WaitGroup.Wait()` 的 goroutine 的个数。
- `sema` 对应于 golang 中 runtime 内部的信号量的实现。WaitGroup 中会用到 sema 的两个相关函数，`runtime_Semacquire` 和 `runtime_Semrelease`。`runtime_Semacquire` 表示增加一个信号量，并挂起 当前 goroutine。`runtime_Semrelease` 表示减少一个信号量，并唤醒 sema 上其中一个正在等待的 goroutine。
- 对于64位对齐的情况，state就是字段state1的值，sema就是state2的值
- 对于32位对齐的情况，state是字段state1的低32位加上state2，sema是state1的高32位。

1. 当调用 `WaitGroup.Add(n)` 时，counter 将会自增: `counter += n`
2. 当调用 `WaitGroup.Wait()` 时，会将 `waiter++`。同时调用 `runtime_Semacquire(semap)`, 增加信号量，并挂起当前 goroutine。 
3. 当调用 `WaitGroup.Done()` 时，将会 `counter--`。如果自减后的 counter 等于 0，会调用 `runtime_Semrelease` 释放信号量，唤醒因 `WaitGroup.Wait` 而挂起的 goroutine。
```go

// A WaitGroup waits for a collection of goroutines to finish.
// The main goroutine calls Add to set the number of
// goroutines to wait for. Then each of the goroutines
// runs and calls Done when finished. At the same time,
// Wait can be used to block until all goroutines have finished.
//
// A WaitGroup must not be copied after first use.
type WaitGroup struct {
	noCopy noCopy

	// 64-bit value: high 32 bits are counter, low 32 bits are waiter count.
	// 64-bit atomic operations require 64-bit alignment, but 32-bit
	// compilers only guarantee that 64-bit fields are 32-bit aligned.
	// For this reason on 32 bit architectures we need to check in state()
	// if state1 is aligned or not, and dynamically "swap" the field order if
	// needed.
	state1 uint64
	state2 uint32
}


// state returns pointers to the state and sema fields stored within wg.state*.
func (wg *WaitGroup) state() (statep *uint64, semap *uint32) {
	if unsafe.Alignof(wg.state1) == 8 || uintptr(unsafe.Pointer(&wg.state1))%8 == 0 {
		// state1 is 64-bit aligned: nothing to do.
		return &wg.state1, &wg.state2
	} else {
		// state1 is 32-bit aligned but not 64-bit aligned: this means that
		// (&state1)+4 is 64-bit aligned.
    //将state1的地址显式转换为一个[3]uint32的地址，因为state1和state2在内存中是连续的，所以将64位的state1拓展至总长度为96位的数组时，默认将字段state2作为state[2]的数据了，所以这里返回的statep是state1字段的低32位加上字段state  2的值，sema是state1的高32位。
		state := (*[3]uint32)(unsafe.Pointer(&wg.state1))
		return (*uint64)(unsafe.Pointer(&state[1])), &state[0]
	}
}

```

noCopy是一个空的结构体，它实现了 mutex 包中的 Locker 接口，其主要作用是在go vet工具(go 自带的静态分析工具)检验时，表明 WaitGroup 结构体的具体实现是不可被复制的。
### GMP

#### GMP 模型中的 GMP 指的是什么
G（Goroutine）：我们所说的协程，为用户级的轻量级线程，每个 Goroutine对象中的 sched 保存着其上下文信息。

M（Machine）：对内核级线程的封装，数量对应真实的 CPU 数（真正干活的对象）。

P（Processor）：即为 G 和 M 的调度对象，用来调度 G 和 M 之间的关联关系，其数量可通过 GOMAXPROCS()来设置，默认为核心数。

其中 M 可能会自旋，也有可能会休眠，GC 会把一些休眠的线程销毁。 

#### GMP 模型中有什么组件

除开 GMP ，还有 P 的 G 本地队列， G 的全局队列，P 列表（MAXPROCS 决定），M 列表（最大限定 1W，runtime/debug 可以设置） 

#### 3、1.0 之前 GM 调度模型
调度器把 G 都分配到 M 上，不同的 G 在不同的 M 并发运行时，都需要向系统申请资源，比如堆栈内存等，因为资源是全局的，就会因为资源竞争照成很多性
能损耗。为了解决这一的问题 go 从 1.1 版本引入，在运行时系统的时候加入 p对象，让 P 去管理这个 G 对象，M 想要运行 G，必须绑定 P，才能运行 P 所管理
的 G。
GM 调度存在的问题：
1. 单一全局互斥锁（Sched.Lock）和集中状态存储
2. Goroutine 传递问题（M 经常在 M 之间传递”可运行”的 goroutine）
3. 每个 M 做内存缓存，导致内存占用过高，数据局部性较差
4. 频繁 syscall 调用，导致严重的线程阻塞/解锁，加剧额外的性能损耗。


#### GMP 调度流程
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221118183008.png)

- 每个 P 有个局部队列，局部队列保存待执行的 goroutine（流程 2），当 M 绑定的 P 的的局部队列已经满了之后就会把 goroutine 放到全局队列（流
程 2-1）
- 每个 P 和一个 M 绑定，M 是真正的执行 P 中 goroutine 的实体（流程 3），M 从绑定的 P 中的局部队列获取 G 来执行
- 当 M 绑定的 P 的局部队列为空时，M 会从全局队列获取到本地队列来执行 G（流程 3.1），当从全局队列中没有获取到可执行的 G 时候，M 会从其他 P
的局部队列中偷取 G 来执行（流程 3.2），这种从其他 P 偷的方式称为work stealing
- 当 G 因系统调用（syscall）阻塞时会阻塞 M，此时 P 会和 M 解绑即 hand off，并寻找新的 idle 的 M，若没有 idle 的 M 就会新建一个 M（流程 5.1）
- 当 G 因 channel 或者 network I/O 阻塞时，不会阻塞 M，M 会寻找其他 runnable 的 G；当阻塞的 G 恢复后会重新进入 runnable 进入 P 队列等待执
行（流程 5.3）
#### 5、GMP 中 work stealing 机制
获取 P 本地队列，当从绑定 P 本地 runq 上找不到可执行的 g，尝试从全局链表中拿，再拿不到从 netpoll 和事件池里拿，最后会从别的 P 里偷任务。P
此时去唤醒一个 M。P 继续执行其它的程序。M 寻找是否有空闲的 P，如果有则将该 G 对象移动到它本身。接下来 M 执行一个调度循环（调用 G 对象->执行->
清理线程→继续找新的 Goroutine 执行）

#### 6、GMP 中 hand off 机制
当本线程 M 因为 G 进行的系统调用阻塞时，线程释放绑定的 P，把 P 转移给其他空闲的 M 执行。
细节：当发生上线文切换时，需要对执行现场进行保护，以便下次被调度执行时进行现场恢复。Go 调度器 M 的栈保存在 G 对象上，只需要将 M 所需要的寄存
器（SP、PC 等）保存到 G 对象上就可以实现现场保护。当这些寄存器数据被保护起来，就随时可以做上下文切换了，在中断之前把现场保存起来。如果此时
G 任务还没有执行完，M 可以将任务重新丢到 P 的任务队列，等待下一次被调度执行。当再次被调度执行时，M 通过访问 G 的 vdsoSP、vdsoPC 寄存器进行现场
恢复（从上次中断位置继续执行）

#### 7、协作式的抢占式调度
在 1.14 版本之前，程序只能依靠 Goroutine 主动让出 CPU 资源才能触发调度。这种方式存在问题有：
- 某些 Goroutine 可以长时间占用线程，造成其它 Goroutine 的饥饿
- 垃圾回收需要暂停整个程序（Stop-the-world，STW），最长可能需要几分钟的时间，导致整个程序无法工作
#### 8、基于信号的抢占式调度
在任何情况下，Go 运行时并行执行（注意，不是并发）的 goroutines 数量是小于等于 P 的数量的。为了提高系统的性能，P 的数量肯定不是越小越好，所
以官方默认值就是 CPU 的核心数，设置的过小的话，如果一个持有 P 的 M，由于 P 当前执行的 G 调用了 syscall 而导致 M 被阻塞，那么此时关键点：
GO 的调度器是迟钝的，它很可能什么都没做，直到 M 阻塞了相当长时间以后，才会发现有一个 P/M 被 syscall 阻塞了。然后，才会用空闲的 M 来强这
个 P。**通过 sysmon 监控实现的抢占式调度**，最快在 20us，最慢在 10-20ms 才会发现有一个 M 持有 P 并阻塞了。操作系统在 1ms 内可以完成很多次线程调
度（一般情况 1ms 可以完成几十次线程调度），Go 发起 IO/syscall 的时候执行该 G 的 M 会阻塞然后被 OS 调度走，P 什么也不干，sysmon 最慢要 10-20ms
才能发现这个阻塞，说不定那时候阻塞已经结束了，这样宝贵的 P 资源就这么被阻塞的 M 浪费了。


#### 9、GMP 调度过程中存在哪些阻塞
- I/O，select
- block on syscall
- channel
- 等待锁
- runtime.Gosched()
#### 10、Sysmon 有什么作用
Sysmon 也叫监控线程，变动的周期性检查，好处
- 释放闲置超过 5 分钟的 span 物理内存；
- 如果超过 2 分钟没有垃圾回收，强制执行；
- 将长时间未处理的 netpoll 添加到全局队列；
- 向长时间运行的 G 任务发出抢占调度（超过 10ms 的 g，会进行retake）；
- 收回因 syscall 长时间阻塞的 P；
#### GMP 模型中为什么需要 P

假设没有 P 会发生什么事情，（没有 P 本地队列，没有 P 全局队列） 	

1. 出现资源竞争，由于没有 P 的本地以及全局队列的多级缓存，所以 G 都会放在一起，多个 M 去获取时会出现资源竞争 	
2. 协程切换资源消耗大，由于没有 P ，也没有 g0 去负责切换协程堆栈，相当于协程堆栈以及一些运行现场全部都在内核态去维护 		
3. 系统调用阻塞时切换成本高，M 会经常被阻塞和解阻塞切换内核态和用户态（类似于进程间切换），消耗大 	

引入了 P 相当于解决了上面的大部分问题，甚至引入了新的特性去榨干 CPU 的性能 		

1. 引入本地队列和全局队列做多级缓存，获 取 G 时都会从本地队列获取，没有竞争，就算去全局队列获取也比较少的几率出现大量 P 去获取，降低了资源竞争的概率 	
2. 切换协程堆栈效率提高，使用了 g0 的协程负责去管理协程切换的堆栈以及保护现场的工作，进入内核态去切换的时候少了很多切换指令以及寄存器的使用，甚至引入了 g0 去负责做垃圾回收部分工作的职能 	
3. 系统调用的曲线救国方案，当 G 和 M 发生了系统调用时，P 会解绑 M ，带着本地队列的 P 去找空闲的 M 或者新创建的 M 去继续剩下的工作 还引入了「工作窃取」的功能，让基于和 M 绑定的 P 更加灵活的让每个 M 都能够最大限度的运行 task，榨干 CPU 

#### P 调度器的设计策略

1. work stealing 机制：当 P 本地队列无运行 G 时，会去其他线程绑定的 P 窃取 G ，若其他 P 本地队列也没有时会去 G 全局队列进行窃取 	
2. hand off 机制：当 G 因为系统调用阻塞时，P 会和 M 解绑，将 G 和 M 绑定，P 会和空闲的线程进行绑定
3. 主动让出机制：当 G 占用了 CPU 超过 10MS 会主动让出（sysmon 轮询） 



### sync

#### 11、什么是 sync.Once
- Once 可以用来执行且仅仅执行一次动作，常常用于单例对象的初始化场景。
- Once 常常用来初始化单例资源，或者并发访问只需初始化一次的共享资源，或者在测试的时候初始化一次测试资源。
- sync.Once 只暴露了一个方法 Do，你可以多次调用 Do 方法，但是只有第一次调用 Do 方法时 f 参数才会执行，这里的 f 是一个无参数无返回值的函数。

#### 15、sync.Pool 有什么用
对于很多需要重复分配、回收内存的地方，sync.Pool 是一个很好的选择。频繁地分配、回收内存会给 GC 带来一定的负担，严重的时候会引起 CPU 的毛刺。而 sync.Pool 可以将暂时将不用的对象缓存起来，待下次需要的时候直接使用，不用再次经过内存分配，复用对象的内存，减轻 GC 的压力，提升系统的性能。
#### sync.map 为什么是线程安全的

因为原有的 map 会在赋值和删除的置写标志，置了后别的协程来做任何操作都会报错。所以引出了 sync.map 解决多协程问题。

**sync.map 使用了读写分离来去保证线程安全的**，sync.map 的数据结构分为读 map、写 map、还有互斥锁以及一个记录穿透次数的值。具体实现是每个协程来读取时都会先读取读部分的 kv，没有则去读写部分的 kv（操作写部分时都会上锁）。当穿透到写部分的次数大于写部分的长度时就会将写部分同步到读部分并且把写部分清空。所以多协程下一般都会先打到无锁的读部分，这能保证读取性能

#### 8、Go 当中同步锁有什么特点？作用是什么
当一个 Goroutine（协程）获得了 Mutex 后，其他 Goroutine（协程）就只能乖乖的等待，除非该 Goroutine 释放了该 Mutex。RWMutex 在读锁占用的情况下，会阻止写，但不阻止读 RWMutex。 在写锁占用情况下，会阻止任何其他Goroutine（无论读和写）进来，整个锁相当于由该 Goroutine 独占同步锁的作用是保证资源在使用时的独有性，不会因为并发而导致数据错乱，保证系统的稳定性。

#### 1、Mutex 几种状态
- mutexLocked — 表示互斥锁的锁定状态；
- mutexWoken — 表示从正常模式被从唤醒；
- mutexStarving — 当前的互斥锁进入饥饿状态；
- waitersCount — 当前互斥锁上等待的 Goroutine 个数；
#### 2、Mutex 正常模式和饥饿模式
1. 正常模式（非公平锁）
正常模式下，所有等待锁的 goroutine 按照 FIFO（先进先出）顺序等待。唤醒的 goroutine 不会直接拥有锁，而是会和新请求 goroutine 竞争锁。新请求的goroutine 更容易抢占：因为它正在 CPU 上执行，所以刚刚唤醒的 goroutine 有很大可能在锁竞争中失败。在这种情况下，这个被唤醒的 goroutine 会加入
到等待队列的前面。
2. 饥饿模式（公平锁）
为了解决了等待 goroutine 队列的长尾问题饥饿模式下，直接由 unlock 把锁交给等待队列中排在第一位的 goroutine (队头)，同时，饥饿模式下，新进来的 goroutine 不会参与抢锁也不会进入自旋状态，会直接进入等待队列的尾部。这样很好的解决了老的 goroutine 一直抢不到锁的场景。
饥饿模式的触发条件：当一个 goroutine 等待锁时间超过 1 毫秒时，或者当前队列只剩下一个 goroutine 的时候，Mutex 切换到饥饿模式。
3. 总结
对于两种模式，正常模式下的性能是最好的，goroutine 可以连续多次获取锁，饥饿模式解决了取锁公平的问题，但是性能会下降，这其实是性能和公平的一个平衡模式。

#### 3、Mutex 允许自旋的条件
- 锁已被占用，并且锁不处于饥饿模式。
- 积累的自旋次数小于最大自旋次数（active_spin=4）。
- CPU 核数大于 1。
- 有空闲的 P。
- 当前 Goroutine 所挂载的 P 下，本地待运行队列为空。

#### sync.mutex 作用

sync.mutex 是一把互斥锁，**具体作用是锁住限定区域的代码逻辑**，这段区域只能被一个协程占用。具体实现原理是，当一个协程去获取锁时（获取锁是 CAS 看锁的状态位），当获取不到会自选一定次数后加入到队列去休眠，当锁被释放时，正在自旋的协程 + 休眠中的任意一个协程会才能会被唤醒的会去抢锁，这是正常模式。还有一种模式是饥饿模式，由于被唤醒的协程有很大几率是抢不到正在自旋的协程的，所以当有协程超过 1 毫秒获取不到锁时将会进入饥饿模式。进入后会根据上面提到的队列中按照先进先出的模式依次获取锁，新来的协程直接进入队列中等待。当队列中已经清空或者队列中头个协程等待时间小于 1 毫秒后退化为正常模式。

#### 4、RWMutex 实现
通过记录 readerCount 读锁的数量来进行控制，当有一个写锁的时候，会将读锁数量设置为负数 1<<30。目的是让新进入的读锁等待之前的写锁释放通知读
锁。同样的当有写锁进行抢占时，也会等待之前的读锁都释放完毕，才会开始进行后续的操作。 而等写锁释放完之后，会将值重新加上 1<<30, 并通知刚才新进入的读锁（rw.readerSem），两者互相限制。
#### sync.RWmutex 作用

sync.RWmutex 是读写锁，用于解决 reader / wirter 问题（保证一个写协程和其他所有协程互斥的同步问题）。也就是可以读锁可以被多个协程拥有，但是只能一个协程拥有写锁。读写操作互斥、写写操作互斥。当有协程获取写锁时，会阻塞所有新来获取读锁的所有协程，并且会等所有正在拥有读锁的协程释放后才能获取到
#### 5、RWMutex 注意事项
- RWMutex 是单写多读锁，该锁可以加多个读锁或者一个写锁
- 读锁占用的情况下会阻止写，不会阻止读，多个 Goroutine 可以同时获取读锁
- 写锁会阻止其他 Goroutine（无论读和写）进来，整个锁由该 Goroutine 独占
- 适用于读多写少的场景
- RWMutex 类型变量的零值是一个未锁定状态的互斥锁
- RWMutex 在首次被使用之后就不能再被拷贝
- RWMutex 的读锁或写锁在未锁定状态，解锁操作都会引发 panic
- RWMutex 的一个写锁去锁定临界区的共享资源，如果临界区的共享资源已被（读锁或写锁）锁定，这个写锁操作的 goroutine 将被阻塞直到解锁
- RWMutex 的读锁不要用于递归调用，比较容易产生死锁
- RWMutex 的锁定状态与特定的 goroutine 没有关联。一个 goroutine 可以 RLock（Lock），另一个 goroutine 可以 RUnlock（Unlock）
- 写锁被解锁后，所有因操作锁定读锁而被阻塞的 goroutine 会被唤醒，并都可以成功锁定读锁
- 读锁被解锁后，在没有被其他读锁锁定的前提下，所有因操作锁定写锁而被阻塞的 Goroutine，其中等待时间最长的一个 Goroutine 会被唤醒
#### sync.singleflight 作用

sync.signleflight 能将对同一个资源访问 的多个请求合并为一个请求。常见的应用场景比如防缓冲击穿。具体的实现是使用了 map 对同一资源访问的请求进行去重，使用互斥锁让当个协程进入临界区后进行资源访问，其他线程阻塞等待资源访问完成后，共同拿到访问资源的结果并返回。


### context
#### context 有什么作用

1. 传递上下文。使用 context 传递业务参数是很不推荐的，但是比较常用的用来传递整个链路的 trace id 来作为链路追踪 		
2. 协程间同步信息。使用 context 后能在多个协程组成的协程树传递取消信号（通过 Cancel，超时计数器调用 Cancel 等）

### goroutine

#### 5、什么是 Goroutine？你如何停止它？
一个 Goroutine 是一个函数或方法执行同时旁边其他任何够程采用了特殊的Goroutine 线程。Goroutine 线程比标准线程更轻量级，大多数 Golang 程序
同时使用数千个 Goroutine。要创建 Goroutine，请 go 在函数声明之前添加关键字。`go f(x, y, z)`您可以通过向 Goroutine 发送一个信号通道来停止它。Goroutines 只能在被告知检查时响应信号，因此您需要在逻辑位置（例如 for 循环顶部）包含检查。
```go
package main
func main() {
  quit := make(chan bool)
  go func() {
    for {
      select {
        case <-quit:
          return
        default:
          // …
      }
  }
  }()
  // …
  quit <- true
}
```


#### 进程、线程和协程的区别

进程是应用程序的实体，分配**操作系统资源的最小单位**，拥有自己的内存空间以及堆栈。 	

线程是内核操作**系统调度CPU**的基本单位，在进程的内存空间及堆栈上进行执行运行。 	

协程指的是用户态线程，由用户的应用程序进行调度。

#### 协程的好处

1. 切换上下文代价小，协程切换时不用进行系统调用，走内核态的指令。 	
2. 没有多线程的竞争资源锁问题，多线程竞争资源还会锁内存走系统调用，而协程如果竞争资源直接在当前线程上的进行判断就可以了 
3. 内存占用小，只占用 2K，线程占用 2M

### defer
#### defer底层原理
1、每次defer语句在执⾏的时候，都会将函数进⾏"压栈"，函数参数会被拷⻉下来。当外层函数退出时，defer函数会按照定义的顺序逆序执⾏ 。如果defer执⾏的函数为nil，那么会在最终调⽤函数中产⽣panic。 

2、为什么defer要按照定义的顺序逆序执⾏
后⾯定义的函数可能会依赖前⾯的资源，所以要先执⾏。如果前⾯先执⾏，释放掉这个依赖，那后⾯的函数就找不到它的依赖了。 

3、defer函数定义时，对外部变量的引⽤⽅式有两种
分别是函数参数以及作为闭包引⽤。
在作为函数参数的时候，在defer定义时就把值传递给defer，并被缓存起来。
如果是作为闭包引⽤，则会在defer真正调⽤的时候，根据整个上下⽂去确定当前的值。 

4、defer后⾯的语句在执⾏的时候，函数调⽤的参数会被保存起来，也就是复制⼀份。
在真正执⾏的时候，实际上⽤到的是复制的变ᰁ，也就是说，如果这个变ᰁ是⼀个"值类型"，那他就和定义的时候
是⼀致的，如果是⼀个"引⽤"，那么就可能和定义的时候的值不⼀致

#### defer配合recover
recover(异常捕获)可以让程序在引发panic的时候不会崩溃退出。 
在引发panic的时候，panic会停掉当前正在执⾏的程序，但是，在这之前，它会有序的执⾏完当前goroutine的defer列表中的语句。 
所以我们通常在defer⾥⾯挂⼀个recover，防⽌程序直接挂掉，类似于try...catch，但绝对不能像try...catch这样使⽤，因为panic的作⽤不是为了抓异常。recover函数只在defer的上下⽂中才有效，如果直接调⽤recover，会返回nil
#### defer 的常用场景： 
- defer 语句经常被用于处理成对的操作，如打开、关闭、连接、断开连接、加锁、释放锁。
- 通过 defer 机制，不论函数逻辑多复杂，都能保证在任何执行路径下，资源被释放。
- 释放资源的 defer 应该直接跟在请求资源的语句后。
### interface
接口分为侵入式和非侵入式。
侵入式⽐如：java中的继承，必须显示的表明我要继承那个接⼝

而像go和Python则使用了非侵入式接口。实现了接口的方法便实现了该接口，有利于代码复用。
#### 7、Go 两个接口之间可以存在什么关系？
如果两个接口有相同的方法列表，那么他们就是等价的，可以相互赋值。如果接口 A 的方法列表是接口 B 的方法列表的自己，那么接口 B 可以赋值给接口 A。

### goroutine
#### goroutine与线程的区别
1、使⽤⽅⾯：
（1）goroutine⽐线程更加轻量级，可以轻松创建⼗万、百万，不⽤担⼼资源问题 
（2）goroutine与channel搭配使⽤，能够更加⽅便的实现⾼并发 
2、实现⽅⾯：
（1）从资源上讲 
1. 线程栈的内存⼤⼩⼀般固定为2MB 
2. goroutine栈内存是可变的，初始的时候⼀般为2KB，最⼤可以扩⼤到1GB 
（2）从调度上讲 
1. 线程的调度由OS的内核完成 
2. goroutine调度由⾃身的调度器完成 
goroutine与线程的联系：
（1）多个goroutine绑定在同⼀个线程上⾯，按照⼀定的调度算法执⾏

#### goroutine调度机制
三个基本概念：MPG
1、M
代表⼀个线程，所有的G(goroutine)任务最终都会在M上执⾏ 
2、P（Processor） 
1. 代表⼀个处理器，每个运⾏的M都必须绑定⼀个P。P的个数是GOMAXPOCS，最⼤为256，在程序启动时固
定，⼀般不去修改。 
2. GOMAXPOCS默认值是当前电脑的核⼼数，单核CPU就只能设置为1，如果设置>1，在GOMAXPOCS函数中
也会被修改为1。 
3. M和P的个数不⼀定⼀样多，M>=P，每⼀个P都会保存本地的G任务队列，另外还有⼀个全局的G任务队列。G
任务队列可以认为线程池中的线程队列。
3、G（Goroutine） 
1. 代表⼀个goroutine对象，每次go调⽤的时候都会创建⼀个G对象

#### goroutine调度流程
1、启动⼀个goroutine
也就是创建⼀个G对象，然后加⼊到本地队列或者全局队列中 
2、查找是否有空闲的P
如果没有就直接返回
如果有，就⽤系统API创建⼀个M（线程） 
3、由这个刚创建的M循环执⾏能找到的G任务 
4、G任务执⾏的循序
先从本地队列找，本地没有找到
就从全局队列找，如果还没有找到
就去其他P中找
5、所有的G任务的执⾏是按照go的调⽤顺序执⾏的 
6、如果⼀个系统调⽤或者G任务执⾏的时间太⻓，就会⼀直占⽤这个线程
（1）在启动的时候，会专⻔创建⼀个线程sysmon，⽤来监控和管理，在内部挨个循环
（2）sysmon主要执⾏任务（中断G任务）
1. 记录所有P的G任务并⽤schedtick变ᰁ计数，该变ᰁ在每执⾏⼀个G任务之后递增 
2. 如果schedtick⼀直没有递增，说明这个P⼀直在执⾏同⼀个任务
3. 如果持续超过10ms，就在这个G任务的栈信息加⼀个标记 
4. G任务在执⾏的时候，会检查这个标记，然后中断⾃⼰，把⾃⼰添加到队列的末尾，执⾏下⼀个G
（3）G任务的恢复
1. 中断的时候将寄存器中栈的信息保存到⾃⼰G对象⾥⾯ 
2. 当两次轮到⾃⼰执⾏的时候，将⾃⼰保存的栈信息复制到寄存器⾥⾯，这样就可以接着上⼀次运⾏
goroutine是按照抢占式进⾏调度的，⼀个goroutine最多执⾏10ms就会换下⼀个

### GC
内存管理是程序员开发应用的一大难题。传统的系统级编程语言（主要指C/C++）中，程序开发者必须对内存小心的进行管理操作，控制内存的申请及释放。因为稍有不慎，就可能产生内存泄露问题，这种问题不易发现并且难以定位，一直成为困扰程序开发者的噩梦。如何解决这个头疼的问题呢？
过去一般采用两种办法：

内存泄露检测工具。这种工具的原理一般是静态代码扫描，通过扫描程序检测可能出现内存泄露的代码段。然而检测工具难免有疏漏和不足，只能起到辅助作用。
智能指针。这是 c++ 中引入的自动内存管理方法，通过拥有自动内存管理功能的指针对象来引用对象，程序员不用太关注内存的释放，而达到内存自动释放的目的。这种方法是采用最广泛的做法，但是对程序开发者有一定的学习成本（并非语言层面的原生支持），而且一旦有忘记使用的场景依然无法避免内存泄露。

为了解决这个问题，后来开发出来的几乎所有新语言（java，python，php等等）都引入了语言层面的自动内存管理 – 也就是语言的使用者只用关注内存的申请而不必关心内存的释放，内存释放由虚拟机（virtual machine）或运行时（runtime）来自动进行管理。而这种对不再使用的内存资源进行自动回收的行为就被称为垃圾回收。

#### 常用的垃圾回收的方法:

1. 引用计数（reference counting）
这是最简单的一种垃圾回收算法，和之前提到的智能指针异曲同工。对每个对象维护一个引用计数，当引用该对象的对象被销毁或更新时被引用对象的引用计数自动减一，当被引用对象被创建或被赋值给其他对象时引用计数自动加一。当引用计数为0时则立即回收对象。
这种方法的优点是实现简单，并且内存的回收很及时。这种算法在内存比较紧张和实时性比较高的系统中使用的比较广泛，如ios cocoa框架，php，python等。
但是简单引用计数算法也有明显的缺点：

频繁更新引用计数降低了性能。
一种简单的解决方法就是编译器将相邻的引用计数更新操作合并到一次更新；还有一种方法是针对频繁发生的临时变量引用不进行计数，而是在引用达到0时通过扫描堆栈确认是否还有临时对象引用而决定是否释放，等等还有很多其他方法。
循环引用。
当对象间发生循环引用时引用链中的对象都无法得到释放。最明显的解决办法是避免产生循环引用，如cocoa引入了strong指针和weak指针两种指针类型。或者系统检测循环引用并主动打破循环链。当然这也增加了垃圾回收的复杂度。

2. 标记-清除（mark and sweep）
标记-清除（mark and sweep）分为两步，标记从根变量开始迭代到遍历所有被引用的对象，对能够通过应用遍历访问到的对象都进行标记为“被引用”；标记完成后进行清除操作，对没有标记过的内存进行回收（回收同时可能伴有碎片整理操作）。这种方法解决了引用计数的不足，但是也有比较明显的问题：每次启动垃圾回收都会暂停当前所有的正常代码执行，回收使系统响应能力大大降低！当然后续也出现了很多mark&sweep算法的变种（如三色标记法）优化了这个问题。

3. 分代搜集（generation）
java的jvm 就使用的分代回收的思路。在面向对象编程语言中，绝大多数对象的生命周期都非常短。分代收集的基本思想是，将堆划分为两个或多个称为代（generation）的空间。新创建的对象存放在称为新生代（young generation）中（一般来说，新生代的大小会比 老年代小很多），随着垃圾回收的重复执行，生命周期较长的对象会被提升（promotion）到老年代中（这里用到了一个分类的思路，这个是也是科学思考的一个基本思路）。
因此，新生代垃圾回收和老年代垃圾回收两种不同的垃圾回收方式应运而生，分别用于对各自空间中的对象执行垃圾回收。新生代垃圾回收的速度非常快，比老年代快几个数量级，即使新生代垃圾回收的频率更高，执行效率也仍然比老年代垃圾回收强，这是因为大多数对象的生命周期都很短，根本无需提升到老年代。
Golang 1.5后，采取的是“非分代的、非移动的、并发的、三色的”标记清除垃圾回收算法。



golang 中的 gc 基本上是标记清除的过程：
gc的过程一共分为四个阶段：

栈扫描（开始时STW）
第一次标记（并发）
第二次标记（STW）
清除（并发）
整个进程空间里申请每个对象占据的内存可以视为一个图，初始状态下每个内存对象都是白色标记。

先STW，做一些准备工作，比如 enable write barrier。然后取消STW，将扫描任务作为多个并发的goroutine立即入队给调度器，进而被CPU处理
第一轮先扫描root对象，包括全局指针和 goroutine 栈上的指针，标记为灰色放入队列
第二轮将第一步队列中的对象引用的对象置为灰色加入队列，一个对象引用的所有对象都置灰并加入队列后，这个对象才能置为黑色并从队列之中取出。循环往复，最后队列为空时，整个图剩下的白色内存空间即不可到达的对象，即没有被引用的对象；
第三轮再次STW，将第二轮过程中新增对象申请的内存进行标记（灰色），这里使用了write barrier（写屏障）去记录
Golang gc 优化的核心就是尽量使得 STW(Stop The World) 的时间越来越短。

#### 11、三色标记原理
我们首先看一张图，大概就会对 三色标记法有一个大致的了解

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221118201813.png)
原理：
首先把所有的对象都放到白色的集合中
- 从根节点开始遍历对象，遍历到的白色对象从白色集合中放到灰色集合中
- 遍历灰色集合中的对象，把灰色对象引用的白色集合的对象放入到灰色集合中，同时把遍历过的灰色集合中的对象放到黑色的集合中
- 循环步骤 3，知道灰色集合中没有对象
- 步骤 4 结束后，白色集合中的对象就是不可达对象，也就是垃圾，进行回收

#### 12、写屏障
Go 在进行三色标记的时候并没有 STW，也就是说，此时的对象还是可以进行修改。
那么我们考虑一下，下面的情况。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221118201921.png)

我们在进行三色标记中扫描灰色集合中，扫描到了对象 A，并标记了对象 A 的所有引用，这时候，开始扫描对象 D 的引用，而此时，另一个 goroutine 修改
了 D->E 的引用，变成了如下图所示
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221118202057.png)
这样会不会导致 E 对象就扫描不到了，而被误认为 为白色对象，也就是垃圾。写屏障就是为了解决这样的问题，引入写屏障后，在上述步骤后，E 会被认为
是存活的，即使后面 E 被 A 对象抛弃，E 会被在下一轮的 GC 中进行回收，这一轮 GC 中是不会对对象 E 进行回收的。

#### 13、插入写屏障
Go GC 在混合写屏障之前，一直是插入写屏障，由于栈赋值没有 hook 的原因，栈中没有启用写屏障，所以有 STW。Golang 的解决方法是：只是需要在结束时启动 STW 来重新扫描栈。这个自然就会导致整个进程的赋值器卡顿。


#### 14、删除写屏障
Golang 没有这一步，Golang 的内存写屏障是由插入写屏障到混合写屏障过渡的。简单介绍一下，一个对象即使被删除了最后一个指向它的指针也依旧可以
活过这一轮，在下一轮 GC 中才被清理掉。

#### 15、混合写屏障
- 混合写屏障继承了插入写屏障的优点，起始无需 STW 打快照，直接并发扫描垃圾即可；
- 混合写屏障继承了删除写屏障的优点，赋值器是黑色赋值器，GC 期间，任何在栈上创建的新对象，均为黑色。扫描过一次就不需要扫描了，这样就消除了插入写屏障时期最后 STW 的重新扫描栈；
- 混合写屏障扫描精度继承了删除写屏障，比插入写屏障更低，随着带来的是 GC 过程全程无 STW；
- 混合写屏障扫描栈虽然没有 STW，但是扫描某一个具体的栈的时候，还是要停止这个 goroutine 赋值器的工作（针对一个 goroutine 栈来说，是暂停扫的，要么全灰，要么全黑哈，原子状态切换）。


#### 16、GC 触发时机
主动触发：调用 runtime.GC 
被动触发：
使用系统监控，该触发条件由 runtime.forcegcperiod 变量控制，默认为 2 分钟。当超过两分钟没有产生任何 GC 时，强制触发 GC。使用步调（Pacing）算法，其核心思想是控制内存增长的比例。如 Go 的 GC 是一种比例 GC, 下一次 GC 结束时的堆大小和上一次 GC 存活堆大小成比例.

#### 17、Go 语言中 GC 的流程是什么？
Go1.14 版本以 STW 为界限，可以将 GC 划分为五个阶段：
- GCMark 标记准备阶段，为并发标记做准备工作，启动写屏障
- STWGCMark 扫描标记阶段，与赋值器并发执行，写屏障开启并发
- GCMarkTermination 标记终止阶段，保证一个周期内标记任务完成，停止写屏障
- GCoff 内存清扫阶段，将需要回收的内存归还到堆中，写屏障关闭
- GCoff 内存归还阶段，将过多的内存归还给操作系统，写屏障关闭。

#### 18、GC 如何调优
通过 go tool pprof 和 go tool trace 等工具
- 控制内存分配的速度，限制 Goroutine 的数量，从而提高赋值器对 CPU 的利用率。
- 减少并复用内存，例如使用 sync.Pool 来复用需要频繁创建临时对象，例如提前分配足够的内存来降低多余的拷贝。
- 需要时，增大 GOGC 的值，降低 GC 的运行频率。







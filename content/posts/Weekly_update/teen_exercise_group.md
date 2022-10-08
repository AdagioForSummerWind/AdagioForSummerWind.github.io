---
title: "Teen_exercise_group"
date: 2022-05-05T20:01:36+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---1
参考：
>https://juejin.cn/post/7095977466094682148/#heading-26

## go
### 基础
https://goproxy.cn/配置go mod proxy，加快第三方包的下载
SwitchyOmega Chrome的一个插件，可以设置切换代理。
### 工程实践
#### 并发编程
go是通过通信实现共享内存，而不是通过共享内存实现通信（不过也保存了这个机制）。共享内存会导致数据竞态问题，需要加锁实现互斥，性能受限。

无缓冲通道==同步通道

使用Sync.WaitGroup实现并发任务的同步

#### 依赖管理

工程项目不可能基于标准库0-1搭建

gopath->go verder->go module控制依赖库的版本

verder其实也是依赖的项目源码，不能清晰标识版本的代码，控制依赖的版本不方便，更新项目又可能出现依赖冲突，导致编译错误。

依赖配置:indirect 和 incompatible

MVS算法--最小版本选择。
**选择最低的兼容版本**


#### 测试
回归测试、集成测试、单元测试（成本降低，覆盖率上升）

外部依赖（db、cache、file）=》稳定&幂等

mock 测试
- monkey:github.com/bouk/monkey

快速mock函数
- 为一个函数打桩
- 为一个方法打桩

#### 项目实战
项目需求，需求拆解、逻辑设计、代码实现、测试运行

E-R图

分层结构：https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fbxcodec%2Fgo-clean-arch
- 数据层
    - 增删查改
- 逻辑层
    - 核心业务逻辑
- 视图层
    - 处理交互逻辑
### 高质量编程与性能调优实战
1. 简洁代码
    - 注释怎么写：代码作用（公共符号），如何做的（方法），实现原因（代码的外部因素），什么情况会出错，接口注释不重要，公共符号要有注释。代码就是最好的注释，注释应该表达出代码未表达出的上下文信息。
    - 命名规范：
        - variable:简洁胜于冗长，变量距离其使用的地方越远，则需要携带越多的上下文信息。
        - function:函数参数一般要具备足够的信息。函数名不携带包名的上下文信息。包名只有小写，没有大写与下划线，单数非复数。
        - pkg:不要与标准库同名
    - 控制流程：线性原理，避免复杂嵌套分支。
    - 错误处理
        - 简单错误处理：
            - **error 尽可能提供简明的上下文信息，方便定位问题**优先使用 errors.New 来创建匿名变量来直接表示该错误。有格式化需求时使用 fmt.Errorf
        - 错误的 Wrap 和 Unwrap：
            - 在 fmt.Errorf 中使用 %w 关键字来将一个错误 wrap 至其错误链中
            - Go1.13 在 errors 中新增了三个新 API 和一个新的 format 关键字，分别是 errors.Is、errors.As 、errors.Unwrap 以及 fmt.Errorf 的 %w。如果项目运行在小于 Go1.13 的版本中，导入 golang.org/x/xerrors 来使用。以下语法均以 Go1.13 作为标准。
        - 错误判定：
            - 使用 errors.Is 可以判定错误链上的所有错误是否含有特定的错误。在错误链上获取特定种类的错误，使用 errors.As
        - **panic 用于真正异常的情况**不建议业务使用panic，如果当前 goroutine 中所有 deferred 函数都不包含 recover 就会造成整个程序崩溃。当程序启动阶段发生不可逆转的错误时，可以在 init 或 main 函数中使用 panic
        - recover 只能在被 defer 的函数中使用，嵌套无法生效，只在当前 goroutine 生效。如果需要更多的上下文信息，可以 recover 后在 log 中记录当前的调用栈。
    - **[编程规范](https://github.com/xxjwxc/uber_go_guide_cn)**
2. 程序优化手段
    - slice map预分配容量更好，在已有切片上创建切片可能导致大内存无法释放，内存会一直占用，直到没有变量引用该底层数组。推荐的做法，使用 copy 替代 re-slice。map提前分配好空间可以减少内存拷贝和 Rehash 的消耗
    - 字符串处理：直接相加代价极高，strings.Builder（直接将底层byte数组转化成字符串类型返回）和bytes.Buffer（转化为字符串时重新申请了一块空间）性能更优，前者更快（二者底层都是[]byte）
    - 使用空结构体节省内存，不占内存，占位符。比如用map实现简单的set。
    - 使用atomic包：锁的实现是通过操作系统来实现，属于系统调用，atomic 操作是通过硬件实现的，效率比锁高很多。sync.Mutex 应该用来保护一段逻辑，不仅仅用于保护一个变量。对于非数值系列，可以使用 atomic.Value，atomic.Value 能承载一个 interface{}。
3. 性能分析工具
- 性能调优的核心是性能瓶颈的分析，对于 Go 应用程序，最方便的就是 pprof 工具：
    - pprof 是用于可视化和分析性能分析数据的工具
    - 可以知道应用在什么地方耗费了多少 CPU、memory 等运行指标
    - pprof
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220512105652.png)
- 排查 CPU 问题
    - 命令行分析
        - go tool pprof "http://localhost:6060/debug/pprof/profile?seconds=10"
    - top 命令
    - list 命令
    - 熟悉 web 页面分析
    - 调用关系图，火焰图
    - go tool pprof -http=:8080 "http://localhost:6060/debug/pprof/cpu"
- 排查堆内存问题
    - go tool pprof -http=:8080 "http://localhost:6060/debug/pprof/heap"
- 排查协程问题
    - go tool pprof -http=:8080 "http://localhost:6060/debug/pprof/goroutine"
- 排查锁问题
    - go tool pprof -http=:8080 "http://localhost:6060/debug/pprof/mutex"
- 排查阻塞问题
    - go tool pprof -http=:8080 "http://localhost:6060/debug/pprof/block"



4. 性能优化的原则和流程
    - 看数据而不是猜测
    - 定位最大瓶颈
    - 不要过早、过度优化


### 高性能go语言发行版优化和落地实践

优化：
- 内存管理优化
- 编译器优化

背景：
- 自动内存管理和GO内存管理机制
- 编译器优化的基本问题和思路

实践：字节遇到的性能问题以及优化方案

#### 自动内存管理
- Auto memory management: 自动内存管理
    - 由程序语言的运行时系统管理动态内存
    - 三个任务：为新对象分配空间、找到存活对象、回收死亡对象的内存空间
- Grabage collction: 垃圾回收
- Mutator: 业务线程
    - 分配新对象，修改对象指向关系
- Collector: GC 线程
    - 找到存活对象，回收死亡对象的内存空间
- Serial GC：只有一个Collector
- Concurrent GC: 并发 GC,支持 mutator(s) 和 collector(s) 同时执行的 GC 算法
    - Collectors 必须感知对象指向关系的改变！
- Parallel GC: 并行 GC,支持多个 collectors 同时回收的 GC 算法
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515105246.png)



- Tracing garbage collection: 追踪垃圾回收
    - 被回收的条件：不可达对象
    - 过程：
        - 标记根对象 (GC roots): 静态变量、全局变量、常量、线程栈等
        - 标记：找到所有可达对象
        - 清理：回收所有不可达对象占据的内存空间
            - Copying GC: 复制对象 GC，将存活对象从一块内存空间复制到另外一块内存空间，原先的空间可以直接进行对象分配
            - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515111749.png)
            - Mark-sweep GC: 标记-清理 GC，将死亡对象所在内存块标记为可分配，使用 free list 管理可分配的空间
            - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515112114.png)
            - Mark-compact GC: 标记-压缩 GC，将存活对象复制到同一块内存区域的开头
            - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515112138.png)

- Reference counting: 引用计数
    - 每个对象都有一个与之关联的引用数目
    - 对象存活的条件：当且仅当引用数大于 0
    - 优点
        - 内存管理的操作被平摊到程序运行中：指针传递的过程中进行引用计数的
        - 不需要了解 runtime 的细节：因为不需要标记 GC roots，因此不需要知道哪里是全局变量、线程栈等
    - 缺点
        - 开销大，因为对象可能会被多线程访问，对引用计数的修改需要原子操作保证原子性和可见性
        - 无法回收环形数据结构
        - 每个对象都引入额外存储空间存储引用计数
        - 虽然引用计数的操作被平摊到程序运行过程中，但是回收大的数据结构依然可能引发暂停




#### Go 内存管理及优化
- TCMalloc
- `mmap()` 系统调用
- scan object 和 noscan object
- mspan, mcache, mentral
- Bump-pointer object allocation: 指针碰撞风格的对象分配

Go内存管理：
- TCMalloc: TC is short for thread caching
- 目标：为对象在 heap 上分配内存
- 提前将内存分块
    - 调用系统调用 mmap() 向 OS 申请一大块内存，例如 4 MB
    - 先将内存划分成大块，例如 8 KB，称作 mspan
    - 再将大块继续划分成特定大小的小块，用于对象分配
    - noscan mspan: 分配不包含指针的对象 —— GC 不需要扫描
    - scan mspan: 分配包含指针的对象 —— GC 需要扫描
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515112736.png)
- 对象分配：根据对象的大小，选择最合适的块返回
- 内存缓存
    - Go 内存管理构成了多级缓存机制，从 OS 分配得的内存被内存管理回收后，也不会立刻归还给 OS，而是在 Go runtime 内部先缓存起来，从而避免频繁向 OS 申请内存。内存分配的路线图如下。
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515160603.png)

Go内存管理的问题：

mspan, mcache 和 mcentral 构成了内存管理的多级缓存机制。

- 对象分配是非常高频的操作：每秒分配 GB 级别的内存
- 线上 profiling 发现，Go 的内存分配占用很多 CPU
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515160824.png)

可以看到，用于分配对象的函数 mallocgc() 占用 CPU 较高

小对象分配占大多数：
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515160858.png)

**横轴是对象大小，纵轴是数目，可以看到绝大多数对象都小于 80 B。因此优化小对象分配是关键**
#### 字节跳动的优化方案
Balanced GC

核心：将 noscan 对象在 per-g allocation buffer (GAB) 上分配，并使用移动对象 GC 管理这部分内存，提高对象分配和回收效率

![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515161210.png)

每个 g 会附加一个较大的 allocation buffer (例如 1 KB) 用来分配小于 128 B 的 noscan 小对象
bump pointer 风格的对象分配。示意如下：
```go
if g.ab.end - g.ab.top < size {
    // Allocate a new allocation buffer
}
addr := g.ab.top
g.ab.top += size
return addr
```
- 分配对象时，根据对象大小移动 top 指针并返回，快速完成一次对象分配
- 同原先调用 mallocgc() 进行对象分配的方式相比，balanced GC 缩短了对象分配的路径，减少了对象分配执行的指令数目，降低 CPU 使用

从 Go runtime 内存管理模块的角度看，一个 allocation buffer 其实是一个大对象。本质上 balanced GC 是将多次小对象的分配合并成一次大对象的分配。因此，当 GAB 中哪怕只有一个小对象存活时，Go runtime 也会认为整个大对象（即 GAB）存活。为此，balanced GC 会根据 GC 策略，将 GAB 中存活的对象移动到另外的 GAB 中，从而压缩并清理 GAB 的内存空间，原先的 GAB 空间由于不再有存活对象，可以全部释放，如下图所示。

![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515161442.png)

上图上方是两个 GAB，其中虚线表示 GAB 中对象的分界线。黑色表示 GAB 中存活的对象，白色表示死掉的对象。由于 GAB 中有存活对象，整个 GAB 无法被回收。

Balanced GC 会将 GAB 中存活的对象移动到下面的 GAB 中，这样原先的两个 GABs 就可以被释放，压缩并清理 GAB 的内存空间。

Balanced GC 只负责 noscan 对象的分配和移动，对象的标记和回收依然依赖 Go GC 本身，并和 Go GC 保持兼容。


#### 编译器和静态分析
- 词法分析
- 语法分析
- 语义分析
- Intermediate representation (IR) 中间表示
- 代码优化
- 代码生成
- Control flow: 控制流
- Data flow: 数据流
- Intra-procedural analysis 过程内分析
- Inter-procedural analysis: 过程间分析


编译器的结构：
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515161704.png)

- 静态分析：不执行代码，推导程序的行为，分析程序的性质。
- 控制流：程序的执行流程
- 数据流：数据在控制流上的传递

![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515161741.png)

上图的程序转换成控制流图 (control-flow graph)

![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220515161803.png)

通过分析控制流和数据流，我们可以知道更多关于程序的性质(properties) ，这些事实可以帮助我们做编译优化。
- 例如上面的程序。我们通过分析数据流和控制流，知道这个程序始终返回 4。编译器可以根据这个结果做出优化。
```c
int a=30;
int b=3;
int c;
c=12;
if(true){
    c=2;
}

return c*2
```
- Intra-procedural analysis: 函数内分析：在函数内进行控制流和数据流的分析
- Inter-procedural analysis: 函数间分析：除了函数内的分析，还需要考虑跨函数的数据流和控制流，例如参数传递，函数返回值等

#### Go 编译器优化
- Function inlining: 函数内联
- Escape analysis: 逃逸分析


目的：
- 用户无感知，重新编译即可获得性能收益
- 通用的优化手段

现状：
- 采用的优化较少
- 追求编译时间短，因此没有进行复杂的代码分析和优化

思路：
- 面向后端长期执行的任务
- 用适当增加编译时间换取更高性能的代码

函数内联：
- 定义：将被调用函数的函数体的副本替换到调用位置上，同时重写代码以反映参数的绑定
- 优点
    - 消除调用开销
    - 将过程间分析的问题转换为过程内分析，帮助其他分析
- 缺点
    - 函数体变大
    - 编译生成的 Go 镜像文件变大
- 函数内联在大多数情况下是正向优化，即多内联，会提升性能
- 采取一定的策略决定是否内联
    - 调用和被调用函数的规模
- Go 内联的限制
    - 语言特性：interface, defer 等等，限制了内联优化
    - 内联策略非常保守
- 字节跳动的优化方案
    - 修改了内联策略，让更多函数被内联
    - 增加了其他优化的机会：逃逸分析
- 开销
    - Go 镜像大小略有增加
    - 编译时间增加
    - 运行时栈扩展开销增加


逃逸分析：
- 定义：分析代码中指针的动态作用域，即指针在何处可以被访问
- 大致思路：
    - 从对象分配处出发，沿着控制流，观察数据流。若发现指针 p 在当前作用域 s:
        - 作为参数传递给其他函数；
        - 传递给全局变量；
        - 传递给其他的 goroutine;
        - 传递给已逃逸的指针指向的对象；
    - 则指针 p 逃逸出 s，反之则没有逃逸出 s.
- **优化：未逃逸出当前函数的指针指向的对象可以在栈上分配**
    - 对象在栈上分配和回收很快：移动 sp 即可完成内存的分配和回收；
    - 减少在堆上分配对象，降低 GC 负担。



## 设计模式之 Database/SQL 与 GORM 实践
### 理解 Database/SQL

Database/SQL 的基本用法
- 提供了关系型数据库通用数据接口
- 统一的接口操作不一样数据库


设计原理
- DB连接的几种类型：
    - 直接连接/Conn
    - 预编译/Stmt
    - 事务/Tx
基础概念






### GORM 的使用简介

GORM 的基本用法
Model 定义
惯例约定
关联介绍






### GORM 的设计原理

SQL 生成的机制
插件扩展机制
ConnPool 扩展机制
Dialector 扩展机制






### GORM 最佳实践

GORM 最佳时间
定制企业级开发


FAQ & 工程成长


# redis

- [Redis](#redis)
  - [1、什么是 Redis?](#1什么是-redis)
  - [Redis 优势：](#redis-优势)
  - [Redis 与其他 key-value 存储有什么不同？](#redis-与其他-key-value-存储有什么不同)
  - [应用场景](#应用场景)
  - [单线程的Redis为什么快](#单线程的redis为什么快)
  - [Redis 的数据结构及使用场景](#redis-的数据结构及使用场景)
  - [3、使用 Redis 有哪些好处？](#3使用-redis-有哪些好处)
  - [4、Redis 相比 Memcached 有哪些优势？](#4redis-相比-memcached-有哪些优势)
  - [5、Memcache 与 Redis 的区别都有哪些？](#5memcache-与-redis-的区别都有哪些)
  - [6、Redis 是单进程单线程的？](#6redis-是单进程单线程的)
  - [7、一个字符串类型的值能存储最大容量是多少？](#7一个字符串类型的值能存储最大容量是多少)
  - [8、Redis 的持久化机制是什么？各自的优缺点？](#8redis-的持久化机制是什么各自的优缺点)
  - [9、Redis 常见性能问题和解决方案：](#9redis-常见性能问题和解决方案)
  - [10、Redis 过期键的删除策略？](#10redis-过期键的删除策略)
  - [11、Redis 的回收策略（淘汰策略）?](#11redis-的回收策略淘汰策略)
  - [12、为什么 Redis 需要把所有数据放到内存中？](#12为什么-redis-需要把所有数据放到内存中)
  - [13、Redis 的同步机制了解么？](#13redis-的同步机制了解么)
  - [14、Pipeline 有什么好处，为什么要用 Pipeline？](#14pipeline-有什么好处为什么要用-pipeline)
  - [15、是否使用过 Redis 集群，集群的原理是什么？](#15是否使用过-redis-集群集群的原理是什么)
  - [16、Redis 集群方案什么情况下会导致整个集群不可用？](#16redis-集群方案什么情况下会导致整个集群不可用)
  - [17、Redis 支持的 Java 客户端都有哪些？官方推荐用哪个？](#17redis-支持的-java-客户端都有哪些官方推荐用哪个)
  - [18、Jedis 与 Redisson 对比有什么优缺点？](#18jedis-与-redisson-对比有什么优缺点)
  - [19、Redis 如何设置密码及验证密码？](#19redis-如何设置密码及验证密码)
  - [20、说说 Redis 哈希槽的概念？](#20说说-redis-哈希槽的概念)
  - [21、Redis 集群的主从复制模型是怎样的？](#21redis-集群的主从复制模型是怎样的)
  - [22、Redis 集群会有写操作丢失吗？为什么？](#22redis-集群会有写操作丢失吗为什么)
  - [23、Redis 集群之间是如何复制的？](#23redis-集群之间是如何复制的)
  - [24、Redis 集群最大节点个数是多少？](#24redis-集群最大节点个数是多少)
  - [25、Redis 集群如何选择数据库？](#25redis-集群如何选择数据库)
  - [26、怎么测试 Redis 的连通性](#26怎么测试-redis-的连通性)
  - [27、怎么理解 Redis 事务？](#27怎么理解-redis-事务)
  - [28、Redis 事务相关的命令有哪几个？](#28redis-事务相关的命令有哪几个)
  - [29、Redis key 的过期时间和永久有效分别怎么设置？](#29redis-key-的过期时间和永久有效分别怎么设置)
  - [30、Redis 如何做内存优化？](#30redis-如何做内存优化)
  - [31、Redis 回收进程如何工作的？](#31redis-回收进程如何工作的)
  - [32、都有哪些办法可以降低 Redis 的内存使用情况呢？](#32都有哪些办法可以降低-redis-的内存使用情况呢)
  - [33、Redis 的内存用完了会发生什么？](#33redis-的内存用完了会发生什么)
  - [34、一个 Redis 实例最多能存放多少的 keys？ List、Set、](#34一个-redis-实例最多能存放多少的-keys-listset)
  - [35、MySQL 里有 2000w 数据，Redis 中只存 20w 的数据，如何保证 Redis 中的数据都是热点数据？](#35mysql-里有-2000w-数据redis-中只存-20w-的数据如何保证-redis-中的数据都是热点数据)
  - [36、Redis 最适合的场景？](#36redis-最适合的场景)
  - [37、假如 Redis 里面有 1 亿个 key，其中有 10w 个 key 是以某个固定的已知的前缀开头的，如果将它们全部找出来？](#37假如-redis-里面有-1-亿个-key其中有-10w-个-key-是以某个固定的已知的前缀开头的如果将它们全部找出来)
  - [38、如果有大量的 key 需要设置同一时间过期，一般需要注意什么？](#38如果有大量的-key-需要设置同一时间过期一般需要注意什么)
  - [39、使用过 Redis 做异步队列么，你是怎么用的？](#39使用过-redis-做异步队列么你是怎么用的)
  - [40、使用过 Redis 分布式锁么，它是什么回事](#40使用过-redis-分布式锁么它是什么回事)
  - [Redis 的数据过期策略](#redis-的数据过期策略)
  - [Redis的set和setnx](#redis的set和setnx)
  - [Redis的LRU具体实现：](#redis的lru具体实现)
  - [Redis如何发现热点key](#redis如何发现热点key)
  - [Redis的热点key解决方案](#redis的热点key解决方案)
  - [如何解决 Redis 缓存雪崩问题](#如何解决-redis-缓存雪崩问题)
  - [如何解决 Redis 缓存穿透问题](#如何解决-redis-缓存穿透问题)
  - [Redis的持久化机制](#redis的持久化机制)
  - [Redis的事务](#redis的事务)
  - [Redis事务相关命令](#redis事务相关命令)
  - [Redis和 memcached 的区别](#redis和-memcached-的区别)
  - [Redis的几种集群模式](#redis的几种集群模式)
  - [Redis的哨兵模式](#redis的哨兵模式)
  - [Redis的rehash](#redis的rehash)
  - [Redis的hash表被扩展的条件](#redis的hash表被扩展的条件)
  - [Redis并发竞争key的解决方案](#redis并发竞争key的解决方案)
  - [Redis与Mysql双写一致性方案](#redis与mysql双写一致性方案)
  - [Redis的管道pipeline](#redis的管道pipeline)
  - [内存淘汰算法实现](#内存淘汰算法实现)


##  Redis

### 1、什么是 Redis?
Redis 是完全开源免费的，遵守 BSD 协议，是一个高性能的 key-value 数据库。

Redis 与其他 key - value 缓存产品有以下三个特点：
- Redis 支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
- Redis 不仅仅支持简单的 key-value 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。
- Redis 支持数据的备份，即 master-slave 模式的数据备份。
### Redis 优势：
- 性能极高 – Redis 能读的速度是 110000 次/s,写的速度是 81000 次/s 。
- 丰富的数据类型 – Redis 支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
- 原子 – Redis 的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过 MULTI 和EXEC 指令包起来。
- 丰富的特性 – Redis 还支持 publish/subscribe, 通知, key 过期等等特性。


### Redis 与其他 key-value 存储有什么不同？
- Redis 有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。Redis 的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
- Redis 运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，因为数据量不能大于硬件内存。在内存数据库方面的另一个优点是，相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样 Redis 可以做很多内部复杂性很强的事情。同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。
### 应用场景

1. 缓存
2. 共享Session
3. 消息队列系统
4. 分布式锁

### 单线程的Redis为什么快

1. 纯内存操作
2. 单线程操作，避免了频繁的上下文切换
3. 合理高效的数据结构
4. 采用了非阻塞I/O多路复用机制（有一个文件描述符同时监听多个文件描述符是否有数据到来）

### Redis 的数据结构及使用场景

Redis 支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及 zsetsorted set：有序集合)。

我们实际项目中比较常用的是 string，hash 如果你是 Redis 中高级用户，还需要加上下面几种数据结构 HyperLogLog、Geo、Pub/Sub。

如果你说还玩过 Redis Module，像 BloomFilter，RedisSearch，Redis-ML，面试官得眼睛就开始发亮了。

1. String字符串:字符串类型是 Redis 最基础的数据结构，首先键都是字符串类型，而且 其他几种数据结构都是在字符串类型基础上构建的，我们常使用的 set key value 命令就是字符串。常用在缓存、计数、共享Session、限速等。
2. Hash哈希:在Redis中，哈希类型是指键值本身又是一个键值对结构，哈希可以用来存放用户信息，比如实现购物车。
3. List列表（双向链表）:列表（list）类型是用来存储多个有序的字符串。可以做简单的消息队列的功能。
4. Set集合：集合（set）类型也是用来保存多个的字符串元素，但和列表类型不一 样的是，集合中不允许有重复元素，并且集合中的元素是无序的，不能通过索引下标获取元素。利用 Set 的交集、并集、差集等操作，可以计算共同喜好，全部的喜好，自己独有的喜好等功能。
5. Sorted Set有序集合（跳表实现）：Sorted Set 多了一个权重参数 Score，集合中的元素能够按 Score 进行排列。可以做排行榜应用，取 TOP N 操作。

### 3、使用 Redis 有哪些好处？
- 速度快，因为数据存在内存中，类似于 HashMap，HashMap 的优势就是查找和操作的时间复杂度都是 O1)
- 支持丰富数据类型，支持 string，list，set，Zset，hash 等
- 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行
- 丰富的特性：可用于缓存，消息，按 key 设置过期时间，过期后将会自动删除

### 4、Redis 相比 Memcached 有哪些优势？
- Memcached 所有的值均是简单的字符串，redis 作为其替代者，支持更为丰富的数据类
- Redis 的速度比 Memcached 快很
- Redis 可以持久化其数据

### 5、Memcache 与 Redis 的区别都有哪些？
- 存储方式 Memecache 把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小。 Redis 有部份存在硬盘上，这样能保证数据的持久性。
- 数据支持类型 Memcache 对数据类型支持相对简单。 Redis 有复杂的数据类型。
- 使用底层模型不同 它们之间底层实现方式 以及与客户端之间通信的应用协议不一样。 Redis 直接自己构建了 VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。
### 6、Redis 是单进程单线程的？
Redis 是单进程单线程的，redis 利用队列技术将并发访问变为串行访 问，消除了传统数据库串行控制的开销。

### 7、一个字符串类型的值能存储最大容量是多少？
答：512M
### 8、Redis 的持久化机制是什么？各自的优缺点？
https://github.com/lifei6671/interview-go/blob/master/base/redis-rdb.md

Redis 提供两种持久化机制 RDB 和 AOF 机制：

RDB(Redis DataBase)持久化方式：
是指用数据集快照的方式半持久化模式)记录 Redis 数据库的所有键值对,在某个时间点将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次持久化的文件，达到数据恢复。

优点： 
- 只有一个文件 dump.rdb，方便持久化。
- 容灾性好，一个文件可以保存到安全的磁盘。
- 性能最大化，fork 子进程来完成写操作，让主进程继续处理命令，所以是 IO 最大化。使用单独子进程来进行持久化，主进程不会进行任何 IO 操作，保证了 Redis 的高性能)
- 相对于数据集大时，比 AOF 的启动效率更高。

缺点： 
数据安全性低。RDB 是间隔一段时间进行持久化，如果持久化之间 Redis 发生故障，会发生数据丢失。所以这种方式更适合数据要求不严谨的时候。

AOFAppend-only file)持久化方式：
是指所有的命令行记录以 Redis 命令请求协议的格式完全持久化存储)保存为aof 文件。

优点： 
- 数据安全，aof 持久化可以配置 appendfsync 属性，有 always，每进行一次命令操作就记录到 aof 文件中一次。
- 通过 append 模式写文件，即使中途服务器宕机，可以通过 redischeck-aof 工具解决数据一致性问题。
- AOF 机制的 rewrite 模式。AOF 文件没被 rewrite 之前（文件过大时会对命令进行合并重写），可以删除其中的某些命令（比如误操作的flushall）)

缺点： 
- AOF 文件比 RDB 文件大，且恢复速度慢。
- 数据集大的时候，比 rdb 启动效率低。


### 9、Redis 常见性能问题和解决方案：
（1）Master 最好不要写内存快照，如果 Master 写内存快照，save 命令调度rdbSave 函数，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务
（2）如果数据比较重要，某个 Slave 开启 AOF 备份数据，策略设置为每秒同步一次
（3）为了主从复制的速度和连接的稳定性，Master 和 Slave 最好在同一个局域网
（4）尽量避免在压力很大的主库上增加从
（5）主从复制不要用图状结构，用单向链表结构更为稳定，即：Master <-Slave1<- Slave2 <- Slave3…这样的结构方便解决单点故障问题，实现 Slave 对 Master 的替换。如果 Master 挂了，可以立刻启用 Slave1 做 Master，其他不变。

### 10、Redis 过期键的删除策略？
（1）定时删除:在设置键的过期时间的同时，创建一个定时器 timer). 让定时器在键的过期时间来临时，立即执行对键的删除操作。
（2）惰性删除:放任键过期不管，但是每次从键空间中获取键时，都检查取得的键是否过期，如果过期的话，就删除该键;如果没有过期，就返回该键。
（3）定期删除:每隔一段时间程序就对数据库进行一次检查，删除里面的过期键。至于要删除多少过期键，以及要检查多少个数据库，则由算法决定。


### 11、Redis 的回收策略（淘汰策略）?
- volatile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
- volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
- volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
- allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰
- allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰
- no-enviction（驱逐）：禁止驱逐数据

注意这里的 6 种机制，volatile 和 allkeys 规定了是对已设置过期时间的数据集淘汰数据还是从全部数据集淘汰数据，后面的 lru、ttl 以及 random 是三种不同的淘汰策略，再加上一种 no-enviction 永不回收的策略。

使用策略规则： 
- 如果数据呈现幂律分布，也就是一部分数据访问频率高，一部分数据访问频率低，则使用 allkeys-lr
- 如果数据呈现平等分布，也就是所有的数据访问频率都相同，则使用allkeys-random

### 12、为什么 Redis 需要把所有数据放到内存中？
Redis 为了达到最快的读写速度将数据都读到内存中，并通过异步的方式将数据写入磁盘。所以 Redis 具有快速和数据持久化的特征。如果不将数据放在内存中，磁盘 I/O 速度为严重影响 Redis 的性能。在内存越来越便宜的今天，Redis 将会越来越受欢迎。如果设置了最大使用的内存，则数据已有记录数达到内存限值后不能继续插入新值。

### 13、Redis 的同步机制了解么？
Redis 可以使用主从同步，从从同步。第一次同步时，主节点做一次 bgsave，并同时将后续修改操作记录到内存 buffer，待完成后将 rdb 文件全 量同步到复制节点，复制节点接受完成后将 rdb 镜像加载到内存。加载完成后，再通知主节点将期间修改的操作记录同步到复制节点进行重放就完成了同步过程。

### 14、Pipeline 有什么好处，为什么要用 Pipeline？
可以将多次 IO 往返的时间缩减为一次，前提是 pipeline 执行的指令之间没有因果相关性。使用 Redis-benchmark 进行压测的时候可以发现影响 Redis 的 QPS 峰值的一个重要因素是 pipeline 批次指令的数目。

### 15、是否使用过 Redis 集群，集群的原理是什么？
- Redis Sentinal 着眼于高可用，在 master 宕机时会自动将 slave 提升为 master，继续提供服务。
- Redis Cluster 着眼于扩展性，在单个 Redis 内存不足时，使用 Cluster 进行分片存储。

### 16、Redis 集群方案什么情况下会导致整个集群不可用？
有 A，B，C 三个节点的集群,在没有复制模型的情况下,如果节点 B 失败了，那么整个集群就会以为缺少 5501-11000 这个范围的槽而不可用。

### 17、Redis 支持的 Java 客户端都有哪些？官方推荐用哪个？
Redisson、Jedis、lettuce 等等，官方推荐使用 Redisson。

### 18、Jedis 与 Redisson 对比有什么优缺点？
Jedis 是 Redis 的 Java 实现的客户端，其 API 提供了比较全面的 Redis 命令的支持；Redisson 实现了分布式和可扩展的 Java 数据结构，和 Jedis 相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、 分区等Redis 特性。

Redisson 的宗旨是促进使用者对 Redis 的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。

### 19、Redis 如何设置密码及验证密码？
设置密码：config set requirepass 123456
授权密码：auth 123456

### 20、说说 Redis 哈希槽的概念？
Redis 集群没有使用一致性 hash,而是引入了哈希槽的概念，Redis 集群 有16384 个哈希槽，每个 key 通过 CRC16 校验后对 16384 取模来决定放置哪个槽，集群的每个节点负责一部分 hash 槽。

### 21、Redis 集群的主从复制模型是怎样的？
https://github.com/lifei6671/interview-go/blob/master/redis/redis-master-slave.md

为了使在部分节点失败或者大部分节点无法通信的情况下集群仍然可用， 所以集群使用了主从复制模型,每个节点都会有 N-1 个复制品。
### 22、Redis 集群会有写操作丢失吗？为什么？
Redis 并不能保证数据的强一致性，这意味这在实际中集群在特定的条件下可能会丢失写操作。
### 23、Redis 集群之间是如何复制的？
异步复制
### 24、Redis 集群最大节点个数是多少？
16384 个。
### 25、Redis 集群如何选择数据库？
Redis 集群目前无法做数据库选择，默认在 0 数据库。
### 26、怎么测试 Redis 的连通性
使用 ping 命令。
### 27、怎么理解 Redis 事务？
（1）事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
（2）事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。

### 28、Redis 事务相关的命令有哪几个？
MULTI、EXEC、DISCARD、WATCH

### 29、Redis key 的过期时间和永久有效分别怎么设置？
EXPIRE 和 PERSIST 命令。

### 30、Redis 如何做内存优化？
尽可能使用散列表（hashes），散列表（是说散列表里面存储的数少）使用的内存非常小，所以你应该尽可能的将你的数据模型抽象到一个散列表里面。比如你的 web 系统中有一个用户对象，不要为这个用户的名称，姓氏，邮箱，密码设置单独的 key,而是应该把这个用户的所有信息存储到一张散列表里面。
### 31、Redis 回收进程如何工作的？
一个客户端运行了新的命令，添加了新的数据。Redi 检查内存使用情况， 如果大于 maxmemory 的限制, 则根据设定好的策略进行回收。一个新的命令被 执行，等等。所以我们不断地穿越内存限制的边界，通过不断达到边界然后不 断地回收回到边界以下。如果一个命令的结果导致大量内存被使用（例如很大 的集合的交集保存到一个新的键），不用多久内存限制就会被这个内存使用量超越。

### 32、都有哪些办法可以降低 Redis 的内存使用情况呢？
如果你使用的是 32 位的 Redis 实例，可以好好利用 Hash,list,sorted set,set 等集合类型数据，因为通常情况下很多小的 Key-Value 可以用更紧凑
的方式存放到一起。
### 33、Redis 的内存用完了会发生什么？
如果达到设置的上限，Redis 的写命令会返回错误信息（但是读命令还可 以正常返回。）或者你可以将 Redis 当缓存来使用配置淘汰机制，当 Redis 达到
内存上限时会冲刷掉旧的内容。
### 34、一个 Redis 实例最多能存放多少的 keys？ List、Set、
Sorted Set 他们最多能存放多少元素？
理论上 Redis 可以处理多达 232 的 keys，并且在实际中进行了测试，每个实例至少存放了 2 亿 5 千万的 keys。我们正在测试一些较大的值。任何 list、set、和 sorted set 都可以放 232 个元素。换句话说，Redis 的存储极限是系统中的可用内存值。

### 35、MySQL 里有 2000w 数据，Redis 中只存 20w 的数据，如何保证 Redis 中的数据都是热点数据？
Redis 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。

相关知识：Redis 提供 6 种数据淘汰策略： 

- volatile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
- volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
- volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
- allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰
- allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰no-enviction（驱逐）：禁止驱逐数据
### 36、Redis 最适合的场景？
会话缓存（Session Cache）
- 最常用的一种使用 Redis 的情景是会话缓存（session cache）。用 Redis 缓存会话比其他存储（如 Memcached）的优势在于：Redis 提供持久化。当维护
一个不是严格要求一致性的缓存时，如果用户的购物车信息全部丢失，大部分人都会不高兴的，现在，他们还会这样吗？ 幸运的是，随着 Redis 这些年的改进，很容易找到怎么恰当的使用 Redis 来缓存会话的文档。甚至广为人知的商业平台 Magento 也提供 Redis 的插件。

全页缓存（FPC）
- 除基本的会话 token 之外，Redis 还提供很简便的 FPC 平台。回到一致性问题，即使重启了 Redis 实例，因为有磁盘的持久化，用户也不会看到页面加载速度的下降，这是一个极大改进，类似 PHP 本地 FPC。 再次以 Magento 为例，Magento 提供一个插件来使用 Redis 作为全页缓存后端。 此外，对WordPress 的用户来说，Pantheon 有一个非常好的插件 wp-redis，这个插件能帮助你以最快速度加载你曾浏览过的页面。

队列
- Redis 在内存存储引擎领域的一大优点是提供 list 和 set 操作，这使得 Redis 能作为一个很好的消息队列平台来使用。Redis 作为队列使用的操作，就类似于本地程序语言（如 Python）对 list 的 push/pop 操作。 如果你快速的在 Google 中搜索“Redis queues”，你马上就能找到大量的开源项目，这些项目的目的就是利用 Redis 创建非常好的后端工具，以满足各种队列需求。例如，Celery 有一个后台就是使用 Redis 作为 broker，你可以从这里去查看。

排行榜/计数器
- Redis 在内存中对数字进行递增或递减的操作实现的非常好。集合（Set）和有序集合（Sorted Set）也使得我们在执行这些操作的时候变的非常简单，Redis 只是正好提供了这两种数据结构。所以，我们要从排序集合中获取到排名最靠前的 10 个用户–我们称之为“user_scores”，我们只需要像下面一样执行即可： 当然，这是假定你是根据你用户的分数做递增的排序。如果你想返回用户及用户的分数，你需要这样执行： ZRANGE user_scores 0 10 WITHSCORES Agora Games 就是一个很好的例子，用 Ruby 实现的，它的排行榜就是使用Redis 来存储数据的，你可以在这里看到。


发布/订阅
- 最后（但肯定不是最不重要的）是 Redis 的发布/订阅功能。发布/订阅的使用场景确实非常多。我已看见人们在社交网络连接中使用，还可作为基于发布/订阅的脚本触发器，甚至用 Redis 的发布/订阅功能来建立聊天系统！


### 37、假如 Redis 里面有 1 亿个 key，其中有 10w 个 key 是以某个固定的已知的前缀开头的，如果将它们全部找出来？
使用 keys 指令可以扫出指定模式的 key 列表。

对方接着追问：如果这个 Redis 正在给线上的业务提供服务，那使用 keys 指令会有什么问题？

这个时候你要回答 Redis 关键的一个特性：Redis 的单线程的。keys 指令会导致线程阻塞一段时间，线上服务会停顿，直到指令执行完毕，服务才能恢复。这个时候可以使用 scan 指令，scan 指令可以无阻塞的提取出指定模式的key 列表，但是会有一定的重复概率，在客户端做一次去重就可以了，但是整体所花费的时间会比直接用 keys 指令长。

### 38、如果有大量的 key 需要设置同一时间过期，一般需要注意什么？
如果大量的 key 过期时间设置的过于集中，到过期的那个时间点，Redis 可能会出现短暂的卡顿现象。一般需要在时间上加一个随机值，使得过期时间 分散
一些。

### 39、使用过 Redis 做异步队列么，你是怎么用的？
一般使用 list 结构作为队列，rpush 生产消息，lpop 消费消息。当 lpop 没有消息的时候，要适当 sleep 一会再重试。

如果对方追问可不可以不 用 sleep 呢？ 

list 还有个指令叫 blpop，在没有消息的时候，它会阻塞住直 到消息到来。

如果对方追问能不能生产一次消费多次呢？

使用 pub/sub 主题订 阅者模式，可以实现 1:N 的消息队列。

如果对方追问 pub/sub 有什么缺点？ 

在消费者下线的情况下，生产的消息会丢失，得使用专业的消息队列如RabbitMQ 等。

如果对方追问 Redis 如何实现延时队列？ 

我估计现在你很想把面试官一棒打死如果你手上有一根棒球棍的话，怎么问的这么详细。但是你很克制，然后神态自若的回答道：使用 sortedset，拿时间戳作为 score，消息内容作为 key 调用 zadd 来生产消息，消费者用zrangebyscore 指令获取 N 秒之前的数据轮询进行处理。

到这里，面试官暗地 里已经对你竖起了大拇指。但是他不知道的是此刻你却竖起了中指，在椅子背后。

### 40、使用过 Redis 分布式锁么，它是什么回事
先拿 setnx 来争抢锁，抢到之后，再用 expire 给锁加一个过期时间防止锁忘记了释放。

这时候对方会告诉你说你回答得不错，然后接着问如果在 setnx 之后执行expire 之前进程意外 crash 或者要重启维护了，那会怎么样？

这时候你要给予惊讶的反馈：唉，是喔，这个锁就永远得不到释放了。紧接着你需要抓一抓自己得脑袋，故作思考片刻，好像接下来的结果是你主动思考出来的，然后回答：我记得 set 指令有非常复杂的参数，这个应该是可以同时把 setnx 和 expire 合成一条指令来用的！对方这时会显露笑容，心里开始默念：摁，这小
子还不错。

### Redis 的数据过期策略

Redis 中数据过期策略采用定期删除+惰性删除策略
* 定期删除策略：Redis 启用一个定时器定时监视所有的 key，判断key是否过期，过期的话就删除。这种策略可以保证过期的 key 最终都会被删除，但是也存在严重的缺点：每次都遍历内存中所有的数据，非常消耗 CPU 资源，并且当 key 已过期，但是定时器还处于未唤起状态，这段时间内 key 仍然可以用。
* 惰性删除策略：在获取 key 时，先判断 key 是否过期，如果过期则删除。这种方式存在一个缺点：如果这个 key 一直未被使用，那么它一直在内存中，其实它已经过期了，会浪费大量的空间。
* 这两种策略天然的互补，结合起来之后，定时删除策略就发生了一些改变，不在是每次扫描全部的 key 了，而是随机抽取一部分 key 进行检查，这样就降低了对 CPU 资源的损耗，惰性删除策略互补了为检查到的key，基本上满足了所有要求。但是有时候就是那么的巧，既没有被定时器抽取到，又没有被使用，这些数据又如何从内存中消失？没关系，还有内存淘汰机制，当内存不够用时，内存淘汰机制就会上场。淘汰策略分为：
    1. 当内存不足以容纳新写入数据时，新写入操作会报错。（Redis 默认策略）
    2. 当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 Key。（LRU推荐使用）
    3. 当内存不足以容纳新写入数据时，在键空间中，随机移除某个 Key。
    4. 当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的 Key。这种情况一般是把 Redis 既当缓存，又做持久化存储的时候才用。
    5. 当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 Key。
    6. 当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 Key 优先移除。

### Redis的set和setnx

Redis中setnx不支持设置过期时间，做分布式锁时要想避免某一客户端中断导致死锁，需设置lock过期时间，在高并发时 setnx与 expire 不能实现原子操作，如果要用，得在程序代码上显示的加锁。使用SET代替SETNX ，相当于SETNX+EXPIRE实现了原子性，不必担心SETNX成功，EXPIRE失败的问题。

### Redis的LRU具体实现：

传统的LRU是使用栈的形式，每次都将最新使用的移入栈顶，但是用栈的形式会导致执行select *的时候大量非热点数据占领头部数据，所以需要改进。Redis每次按key获取一个值的时候，都会更新value中的lru字段为当前秒级别的时间戳。Redis初始的实现算法很简单，随机从dict中取出五个key,淘汰一个lru字段值最小的。在3.0的时候，又改进了一版算法，首先第一次随机选取的key都会放入一个pool中(pool的大小为16),pool中的key是按lru大小顺序排列的。接下来每次随机选取的keylru值必须小于pool中最小的lru才会继续放入，直到将pool放满。放满之后，每次如果有新的key需要放入，需要将pool中lru最大的一个key取出。淘汰的时候，直接从pool中选取一个lru最小的值然后将其淘汰。

### Redis如何发现热点key

1. 凭借经验，进行预估：例如提前知道了某个活动的开启，那么就将此Key作为热点Key。
2. 服务端收集：在操作redis之前，加入一行代码进行数据统计。
3. 抓包进行评估：Redis使用TCP协议与客户端进行通信，通信协议采用的是RESP，所以自己写程序监听端口也能进行拦截包进行解析。
4. 在proxy层，对每一个 redis 请求进行收集上报。
5. Redis自带命令查询：Redis4.0.4版本提供了redis-cli –hotkeys就能找出热点Key。（如果要用Redis自带命令查询时，要**注意**需要先把内存逐出策略设置为allkeys-lfu或者volatile-lfu，否则会返回错误。进入Redis中使用config set maxmemory-policy allkeys-lfu即可。）

### Redis的热点key解决方案

1. 服务端缓存：即将热点数据缓存至服务端的内存中.(利用Redis自带的消息通知机制来保证Redis和服务端热点Key的数据一致性，对于热点Key客户端建立一个监听，当热点Key有更新操作的时候，服务端也随之更新。)
2. 备份热点Key：即将热点Key+随机数，随机分配至Redis其他节点中。这样访问热点key的时候就不会全部命中到一台机器上了。

### 如何解决 Redis 缓存雪崩问题

1. 使用 Redis 高可用架构：使用 Redis 集群来保证 Redis 服务不会挂掉
2. 缓存时间不一致，给缓存的失效时间，加上一个随机值，避免集体失效
3. 限流降级策略：有一定的备案，比如个性推荐服务不可用了，换成热点数据推荐服务

### 如何解决 Redis 缓存穿透问题

1. 在接口做校验
2. 存null值（缓存击穿加锁,或设置不过期）
3. 布隆过滤器拦截： 将所有可能的查询key 先映射到布隆过滤器中，查询时先判断key是否存在布隆过滤器中，存在才继续向下执行，如果不存在，则直接返回。布隆过滤器将值进行多次哈希bit存储，布隆过滤器说某个元素在，可能会被误判。布隆过滤器说某个元素不在，那么一定不在。

### Redis的持久化机制

Redis为了保证效率，数据缓存在了内存中，但是会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件中，以保证数据的持久化。Redis的持久化策略有两种：

1. RDB：快照形式是直接把内存中的数据保存到一个dump的文件中，定时保存，保存策略。当Redis需要做持久化时，Redis会fork一个子进程，子进程将数据写到磁盘上一个临时RDB文件中。当子进程完成写临时文件后，将原来的RDB替换掉。
2. AOF：把所有的对Redis的服务器进行修改的命令都存到一个文件里，命令的集合。

使用AOF做持久化，每一个写命令都通过write函数追加到appendonly.aof中。aof的默认策略是每秒钟fsync一次，在这种配置下，就算发生故障停机，也最多丢失一秒钟的数据。
缺点是对于相同的数据集来说，AOF的文件体积通常要大于RDB文件的体积。根据所使用的fsync策略，AOF的速度可能会慢于RDB。
Redis默认是快照RDB的持久化方式。对于主从同步来说，主从刚刚连接的时候，进行全量同步（RDB）；全同步结束后，进行增量同步(AOF)。

### Redis的事务

1. Redis 事务的本质是一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。总结说：redis事务就是一次性、顺序性、排他性的执行一个队列中的一系列命令。
2. Redis事务没有隔离级别的概念，批量操作在发送 EXEC 命令前被放入队列缓存，并不会被实际执行，也就不存在事务内的查询要看到事务里的更新，事务外查询不能看到。
3. Redis中，单条命令是原子性执行的，但事务不保证原子性，且没有回滚。事务中任意命令执行失败，其余的命令仍会被执行。

### Redis事务相关命令

1. watch key1 key2 ... : 监视一或多个key,如果在事务执行之前，被监视的key被其他命令改动，则事务被打断（类似乐观锁）
2. multi : 标记一个事务块的开始（queued）
3. exec : 执行所有事务块的命令（一旦执行exec后，之前加的监控锁都会被取消掉）
4. discard : 取消事务，放弃事务块中的所有命令
5. unwatch : 取消watch对所有key的监控

### Redis和 memcached 的区别

1. 存储方式上：memcache会把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小。redis有部分数据存在硬盘上，这样能保证数据的持久性。
2. 数据支持类型上：memcache对数据类型的支持简单，只支持简单的key-value，，而redis支持五种数据类型。
3. 用底层模型不同：它们之间底层实现方式以及与客户端之间通信的应用协议不一样。redis直接自己构建了VM机制，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。
4. value的大小：redis可以达到1GB，而memcache只有1MB。

### Redis的几种集群模式

1. 主从复制
2. 哨兵模式
3. cluster模式

### Redis的哨兵模式

哨兵是一个分布式系统,在主从复制的基础上你可以在一个架构中运行多个哨兵进程,这些进程使用流言协议来接收关于Master是否下线的信息,并使用投票协议来决定是否执行自动故障迁移,以及选择哪个Slave作为新的Master。

每个哨兵会向其它哨兵、master、slave定时发送消息,以确认对方是否活着,如果发现对方在指定时间(可配置)内未回应,则暂时认为对方已挂(所谓的”主观认为宕机”)。

若“哨兵群“中的多数sentinel,都报告某一master没响应,系统才认为该master"彻底死亡"(即:客观上的真正down机),通过一定的vote算法,从剩下的slave节点中,选一台提升为master,然后自动修改相关配置。

### Redis的rehash

Redis的rehash 操作并不是一次性、集中式完成的，而是分多次、渐进式地完成的，redis会维护维持一个索引计数器变量rehashidx来表示rehash的进度。

这种渐进式的 rehash 避免了集中式rehash带来的庞大计算量和内存操作，但是需要**注意**的是redis在进行rehash的时候，正常的访问请求可能需要做多要访问两次hashtable（ht[0]， ht[1]），例如键值被rehash到新ht1，则需要先访问ht0，如果ht0中找不到，则去ht1中找。

### Redis的hash表被扩展的条件

1. 哈希表中保存的key数量超过了哈希表的大小.
2. Redis服务器目前没有在执行BGSAVE命令（rdb）或BGREWRITEAOF命令，并且哈希表的负载因子大于等于1.
3. Redis服务器目前在执行BGSAVE命令（rdb）或BGREWRITEAOF命令，并且哈希表的负载因子大于等于5.(负载因子=哈希表已保存节点数量 / 哈希表大小，当哈希表的负载因子小于0.1时，对哈希表执行收缩操作。)

### Redis并发竞争key的解决方案

1. 分布式锁+时间戳
2. 利用消息队列

### Redis与Mysql双写一致性方案

先更新数据库，再删缓存。数据库的读操作的速度远快于写操作的，所以脏数据很难出现。可以对异步延时删除策略，保证读请求完成以后，再进行删除操作。

### Redis的管道pipeline

对于单线程阻塞式的Redis，Pipeline可以满足批量的操作，把多个命令连续的发送给Redis Server，然后一一解析响应结果。Pipelining可以提高批量处理性能，提升的原因主要是TCP连接中减少了“交互往返”的时间。pipeline 底层是通过把所有的操作封装成流，redis有定义自己的出入输出流。在 sync() 方法执行操作，每次请求放在队列里面，解析响应包。

### 内存淘汰算法实现
https://github.com/lifei6671/interview-go/blob/master/redis/redis-policy.md

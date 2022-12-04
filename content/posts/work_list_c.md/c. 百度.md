---
title: "interview-bd"
date: 2022-06-28T15:30:06+08:00
lastmod: 2022-08-28
tags: [interview]
categories: [interview]
slug: all-interview
draft: false
---
- [【Golang开发面经】百度（三轮技术面）](#golang开发面经百度三轮技术面)
	- [写在前面](#写在前面)
	- [笔试](#笔试)
	- [一面](#一面)
	- [算法：判断是否为镜面二叉树](#算法判断是否为镜面二叉树)
	- [算法：二叉树的俯视图](#算法二叉树的俯视图)
	- [一个协程被网络io卡住了，对应的线程会不会卡住？](#一个协程被网络io卡住了对应的线程会不会卡住)
	- [go 里面 make 和 new 有什么区别？](#go-里面-make-和-new-有什么区别)
	- [map 是怎么实现的？](#map-是怎么实现的)
	- [二面](#二面)
	- [go里面 slice 和 array 有区别吗？](#go里面-slice-和-array-有区别吗)
	- [归并排序是稳定的吗？时间复杂度是多少？](#归并排序是稳定的吗时间复杂度是多少)
	- [写一个归并排序吧](#写一个归并排序吧)
	- [空chan和关闭的chan进行读写会怎么样？](#空chan和关闭的chan进行读写会怎么样)
	- [三面](#三面)
	- [讲讲redis分布式锁的设计与实现](#讲讲redis分布式锁的设计与实现)
	- [讲讲redis的哨兵模式](#讲讲redis的哨兵模式)
	- [限流算法有哪些？令牌桶算法怎么实现？](#限流算法有哪些令牌桶算法怎么实现)
	- [算法：交换二叉树左右节点](#算法交换二叉树左右节点)
	- [✨百度面经](#百度面经)
	- [通用面经](#通用面经)
	- [Java](#java)
	- [C/C++](#cc)
	- [Linux](#linux)
	- [计算机网络](#计算机网络)
	- [前端](#前端)

# 【Golang开发面经】百度（三轮技术面）

## 写在前面
> 百度一顿面试下来感觉挺不错的，面试官水平很高，不愧是互联网的黄埔军校，技术都很硬。可能是我项目讲的不好吧，最终挂了。

## 笔试
略

## 一面
> 一直深挖项目，挖了快半小时。然后再写两道题，最后再问一些简单的问题。

##  算法：判断是否为镜面二叉树
##  算法：二叉树的俯视图
##  一个协程被网络io卡住了，对应的线程会不会卡住？
不会。因为都用epoll那是非阻塞调用，网络io和系统调用不一样的处理方式。网络io 是利用`非阻塞`，系统调用会创建新的线程来接管其他 goroutine。

##  go 里面 make 和 new 有什么区别？

make 一般用来创建引用类型 slice、map 以及 channel 等等，并且是非零值的。而new 用于类型的内存分配，并且内存置为零。make 返回的是引用类型本身；而 new 返回的是指向类型的指针。

##  map 是怎么实现的？
map 的底层是一个结构体

```go
// Go map 的底层结构体表示
type hmap struct {
    count     int    // map中键值对的个数，使用len()可以获取 
	flags     uint8
	B         uint8  // 哈希桶的数量的log2，比如有8个桶，那么B=3
	noverflow uint16 // 溢出桶的数量
	hash0     uint32 // 哈希种子

	buckets    unsafe.Pointer // 指向哈希桶数组的指针，数量为 2^B 
	oldbuckets unsafe.Pointer // 扩容时指向旧桶的指针，当扩容时不为nil 
	nevacuate  uintptr        

	extra *mapextra  // 可选字段
}

const (
	bucketCntBits = 3
	bucketCnt     = 1 << bucketCntBits     // 桶数量 1 << 3 = 8
)

// Go map 的一个哈希桶，一个桶最多存放8个键值对
type bmap struct {
    // tophash存放了哈希值的最高字节
	tophash [bucketCnt]uint8
    
    // 在这里有几个其它的字段没有显示出来，因为k-v的数量类型是不确定的，编译的时候才会确定
    // keys: 是一个数组，大小为bucketCnt=8，存放Key
    // elems: 是一个数组，大小为bucketCnt=8，存放Value
    // 你可能会想到为什么不用空接口，空接口可以保存任意类型。但是空接口底层也是个结构体，中间隔了一层。因此在这里没有使用空接口。
    // 注意：之所以将所有key存放在一个数组，将value存放在一个数组，而不是键值对的形式，是为了消除例如map[int64]所需的填充整数8（内存对齐）
    
    // overflow: 是一个指针，指向溢出桶，当该桶不够用时，就会使用溢出桶
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/4398d5a6281b4d82b42776897aa66980.png)
当向 map 中存储一个 kv 时，通过` k 的 hash 值与 buckets 长度取余`，定位到 key 在哪一个bucket中，**hash 值的高8位存储在 bucket 的 tophash[i] 中**，用来快速判断 key是否存在。**当一个 bucket 满时，通过 overflow 指针链接到下一个 bucket。**

## 二面
> 又深挖项目，这次挖了快40分钟了。。

##  go里面 slice 和 array 有区别吗？

slice， 是切片，是引用类型，长度可变。
array，是数组，是值类型，长度不可变。

**`slice的底层其实是基于array 实现的。`**

##  归并排序是稳定的吗？时间复杂度是多少？
是的，归并排序中相等元素的顺序不会改变。

> 总时间 = 分解时间+解决问题时间+合并时间。

1. 分解时间就是把一个待排序序列分解成两序列，时间为一常数，时间复杂度`o(1)`。
2. 解决问题时间是两个递归式，把一个规模为n 的问题分成两个规模分别为 `n/2` 的子问题，时间为`2T(n/2)。`
3. 合并时间复杂度为`o(n)`。
4. 总时间`T(n)=2T(n/2)+o(n)`，这个递归式可以用递归树来解，其解是`o(nlogn)`。

所以是` O(nlogn) `

##  写一个归并排序吧

```go
func mergeSort(arr *[]int, l int, r int) {
	if l >= r {
		//不可再分隔,只有一个元素
		return
	}
	mid := (l+r)/2
	mergeSort(arr,l,mid)
	mergeSort(arr,mid+1,r)
	if (*arr)[mid] > (*arr)[mid+1] {
		merge(arr, l, mid, r)
	}
}

//将arr[l...mid]和arr[mid+1...r]两部分进行归并
func merge(arr *[]int, l int, mid int, r int)  {
	//先把arr中[l..r]区间的值copy一份到arr2
	//注意:这里可优化，copyarr 可改为长度为r-l+1的数组,下面的赋值等操作按偏移量l来修改即可，
	copyarr := make([]int, r+1)
	for index := l; index <= r; index++ {
		copyarr[index] = (*arr)[index]
	}
	//定义要合并的两个子数组各自目前数组内还没被合并的首位数字下标为i,j
	//初始化i，j
	i := l
	j := mid+1
	//遍历并逐个确定数组[l,r]区间内数字的顺序
	for k := l; k <= r; k++ {
		//防止i/j"越界"，应该先判断i和j的下变是否符合条件（因为两个子数组应该符合i<=mid j<=r）
		if i > mid {
			(*arr)[k] = copyarr[j]
			j++
		}else if j > r {
			(*arr)[k] = copyarr[i]
			i++
		}else if copyarr[i] < copyarr[j] {
			(*arr)[k] = copyarr[i]
			i++
		}else{
			(*arr)[k] = copyarr[j]
			j++
		}
	}
}

func TestMerge(t *testing.T) {
	arr := []int{8,6,2,3,1,5,7,4}
	mergeSort(&arr, 0, len(arr)-1)
	fmt.Println(arr)
}

```

##  空chan和关闭的chan进行读写会怎么样？

- 空chan
  1. 读会读取到该chan类型的`零值`。
  2. 写会直接写到chan中。

- 关闭的chan
  1. 读已经关闭的 chan 能一直读到东西，但是读到的内容根据通道内关闭前是否有元素而不同。**`如果有元素，就继续读剩下的元素，如果没有就是这个chan类型的零值，比如整型是 int，字符串是"" `**。
  2. 写已经关闭的 chan 会 panic。因为源码上面就是这样写的，可以看`src/runtime/chan.go`




## 三面
> 感觉就走个过场。。面完简历就共享了。。

##  讲讲redis分布式锁的设计与实现
这篇博客很详细了，可以看这篇博客 [ redis分布式锁实现 ](https://blog.csdn.net/Me_xuan/article/details/124418176)

##  讲讲redis的哨兵模式
一主两从三哨兵集群，当 master 节点宕机时，通过哨兵(sentinel)重新推选出新的master节点，保证集群的可用性。

![在这里插入图片描述](https://img-blog.csdnimg.cn/96913a7b605f46fe89fe6006a58aa757.png)


##  限流算法有哪些？令牌桶算法怎么实现？
限流算法常见有计数器算法，滑动窗口，令牌桶算法。

令牌桶算法是比较常见的限流算法之一，如下：

1. 所有的请求在处理之前都需要拿到一个可用的令牌才会被处理；
2. 根据`限流大小`，设置按照一定的速率往桶里添加令牌；
3. 桶设置最大的放置`令牌限制`，当桶满时、新添加的令牌就被丢弃或者拒绝；
4. 请求达到后首先要获取令牌桶中的令牌，拿着`令牌才可以进行其他的业务逻辑`，处理完业务逻辑之后，`将令牌直接删除`；
5. 令牌桶有`最低限额`，当桶中的令牌达到最低限额的时候，请求处理完之后将不会删除令牌，以此保证`足够的限流`；

![在这里插入图片描述](https://img-blog.csdnimg.cn/463bf84e97a940f1b180781197c1e8bb.png)


##  算法：交换二叉树左右节点



## ✨百度面经
https://github.com/Mapshiny/CampusRecruit/blob/main/%E5%A4%A7%E5%8E%82%E9%9D%A2%E7%BB%8F/%E7%99%BE%E5%BA%A6/README.md?plain=1

> 本文档主要记录百度面试的一些常见问题以及一些面试经验，按[通用](#common)、[Java](#java)、[C++](#cpp)、[前端](#frontend)、[Linux](#linux)进行内容整理，希望能帮助到大家。

## <span id="common">通用面经</span>

- 项目中遇到了什么困难? 如何解决的?
- 最近遇到过的困难？**注意：**不要小瞧这些生活问题，非常容易尬住回答不出！
- 快速排序算法的原理是什么?
- Git
- 介绍研究生期间的课题和项目
- 实习内容
-  哈希一般使用场景是什么，了解吗？  



## <span id="java">Java</span>

- DAO层怎么开发的 
- 数据库表怎么设计的，表之间的关联怎么实现的 
- 项目里面用到了什么设计模式 
- Kafka在项目里怎么用的 
- Redis在项目里怎么用的，为什么用redis ？
- redis为什么这么快（从线程模型6.0之前和之后，多路复用select，poll和epoll，数据结构展开） 



## <span id="cpp">C/C++</span>

- 内存泄露，你是怎么定位的? gdb 调试定位到具体的模块，然后分析代码实现部分。
- malloc 和 new 区别？虚拟内存和物理内存
- 左值右值
- 内存对齐, 为什么要对齐？（计算机取数据按总线宽度进行取，一次取整个总线宽度的数据，对齐保证一次取完）


## <span id="linux">Linux</span>

-  Linux 下可以查看系统启动时间的命令 ？ 可以的命令：w，uptime，top；不可以的命令：ps。 
-  select, poll, epoll 的区别? 
-  查看进程的所有命令，根据文件名查找其所在目录，修改文件的权限  
-  线程和进程的区别
-  线程之间如何通信的 


## <span id="net">计算机网络</span>

-  粘包？Tcp是面向字节流的无边界协议
-  http和https的区别？https是http的安全版，https需要证书，http不需要...TLS握手等
-  拥塞控制和流量控制
-  http状态码，知道的都说出来 
-  HTTP缓存了解吗？ 强缓存协商缓存区别？ last-midified有什么缺点？etag有什么缺点？
-  HTTP2.0是如何解决“队头阻塞”的问题？ 




## <span id="frontend">前端</span>


-  Webpack的loader和plugin的区别？
-  前端监控了解吗？我以为是性能监控，面试官提醒是错误监控，然后我说了Vue中的三种方案
-  webpack的source-map了解吗？ 
-  HTTP长连接的缺点?为什么会有队头阻塞？ 
-  说一下回流和重绘？ 
-  CSS transform是如何减少回流和重绘的？ 回答了CSS3硬件加速 
-  那浏览器是如何开启硬件加速的呢？ 回答了浏览器多进程模型，GPU进程负责调度资源 
-  plugin在webpack中的内部运行机制了解吗？ 答了compiler事件钩子相关，但没说清楚，没达到面试官要求 









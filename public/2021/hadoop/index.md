# BigData_base_02

## hadoop 入门
### 引例：web搜索引擎
搜索引擎：网络文档集合的检索器   
怎么快速存储？  “水平拓展”--分布式存储    
怎么快速检索？   快速检索模型：基于倒排索引（ Inverted Index）  
怎么快速构建倒排索引？   “水平拓展”--分布式处理
### hadoop的基础架构
Hadoop：实现高效数据存储、处理的一种分布式框架。可以解决PB级别的数据存储与计算问题。基于Java语言开发：具有很好的跨平台性
#### Hadoop分布式框架的基本思想
Master-slave架构，分布式存储：HDFS，分布式计算：Mapreduce，存储与处理的一体化！
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211108185129.png)
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211108185218.png)

#### HDFS    
“高容错、低成本的分布式大磁盘”设计需求：    
简单的文件访问模型：类似于linux的文件系统！
* PB级数据的可靠存储
* 流数据读写：高吞吐率
* 支持上万台服务器集群
* 对硬件设备性能要求低
* 集群规模具有可扩展性
* 兼容性好，支持跨平台
HDFS文件的“分块”存储思想:    
* HDFS中的文件系统默认的最基本的存储单位是数据块（按照64MB或128MB），一
个大规模文件被切分成不同的块，每个块尽可能地存储于不同的数据节点中。
* 块的大小远远大于普通文件系统，可以最小化寻址开销
* 支持大规模文件存储、简化系统设计、适合数据备份
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211108190014.png)
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211108192232.png)

#### MapReduce    
MapReduce是一个统一的分布式并行计算软件框架，可以实现：
* 计算任务的划分和调度
* 数据的分布传输
* 计算及处理结果的收集
* 处理系统节点出错检测和失效恢复
* 系统管理、负载平衡、计算性能优化
* ……
* 提供简单、易用的编程接口
MapReduce的基本思想:   
* 采用分而治之的思想实现大规模数据的并行运算
* Map函数：
    * 大量数据记录进行重复、简单处理
    * 只需要局部信息，获得中间结果
* Reduce函数：
    * 整理（全局的）中间结果
    * 产生最终结果输出
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211108190837.png)

MapReduce 1.0既是一个计算框架也是一个资源管理调度框架，存在一些缺陷:    
* 存在单点故障
* JobTracker“大包大揽”导致任务过重（任务多时内存开销大，上限4000节点）
* 容易出现内存溢出（分配资源只考虑MapReduce任务数，不考虑CPU、内存）
* 资源划分不合理（强制划分Map slot和Reduce slot）
#### YARN
Hadoop2.0以后， MapReduce1.0中的资源管理调度功能被单独分离出来形成了YARN，它是一个纯粹的资源管理调度框架,MapReduce2.0成为了运行在YARN之上的一个纯粹的计算框架
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211108185129.png)

YARN的基本框架:    
* ResourceManager
    * 处理客户端请求
    * 启动/监控ApplicationMaster
    * 监控NodeManager
    * 资源分配与调度
* ApplicationMaster
    * 为应用程序申请资源，分配给内部任务
    * 任务调度、监控与容错
* NodeManager
    * 单个节点上的资源管理
    * 处理来自ResourceManager的命令
    * 处理来自ApplicationMaster的命令




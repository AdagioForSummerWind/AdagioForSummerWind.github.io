# Mysql_base_12

> 笔记来自哈工大深圳2021秋数据库系统理论课授课老师：刘圣鑫

# 数据查询优化
在建表和建索引的时候，要更有意识地为将来的查询优化做综合考虑，比如确定是否使用递增主键、主键的列怎样选择，等等。
## 为什么要及什么是查询优化?
- 为什么需要查询优化:
    - 关系数据库的执行效率问题
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115122516.png)
    - 关系代数操作执行次序对效率的影响
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115122720.png)
- 什么是查询优化
    - “如何使数据库查询的执行时间最短？”
    - 三个层面进行优化：
        - 语义优化：利用模型的语义及完整性规则，优化查询。
        - 语法优化---逻辑层优化：利用语法结构，优化操作执行顺序；
        - 执行优化---物理层优化：存取路径和执行算法的选择与执行次序优化；

## 查询优化的基本思路
- 查询优化的总体思路
    - 语义优化---内容等价性
    - 语法优化(逻辑层优化)---语法等价性：基本思想：改变关系代数的操作次序：尽可能早做选择和投影运算
    - 执行优化(物理层优化)
    - 在DBMS中的位置
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115123812.png)
    - 简要回顾
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115124047.png)

## 逻辑查询优化
一个待优化的示例背景：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115205559.png)
用语法树表达关系代数表达式:   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115205716.png)
逻辑层优化策略:   
- (1)尽可能地早做选择和投影：可使中间结果变小，节省几个数量级的执行时间。
- (2)把选择与投影串接起来：一元运算序列可一起执行，只需对整个关系扫描一遍。
- (3)把投影与其前或后的二元运算结合起来：在第一次用关系时去掉一些无关属性，可以避免多次扫描整个关系。
- (4)把某些选择与其前的笛卡尔积合并成一个连接：当R×S前有选择运算且其中有条件是R、S属性间比较的运算时，可将其转化为连接运算可节省时间。
- (5)执行连接运算前对关系做适当预处理：文件排序、建立临时索引等，可使两关系公共值高效联接。
- (6)找出表达式里的公共子表达式：若公共子表达式结果不大，则预先计算，以后可读入此结果，节时多，尤当视图情况下有用。

关系代数操作次序交换的等价性:
- “哪些关系代数操作能够交换次序”
- 什么是关系操作次序交换的等价性?
    - [定义] 设E1, E2是两个关系操作表达式。若E1, E2表示相同的映射，即当E1, E2的同名变量代入相同关系后产生相同的结果(映像集合)，则说E1, E2是等价的，记为E1 = E2 
- 哪些关系操作次序可以交换?
    - 定理L1：连接与连接，积与积的交换律   
    设E1, E2是关系代数表达式， F是E1, E2中属性的附加限制条件，则有：
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210330.png)
    - 定理L2：连接与连接、积和积的结合律若E1, E2, E3是关系代数表达式，F1，F2是条件，则有：
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210443.png)
    - 定理L3：投影串接律
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210534.png)
    - 定理L4：选择串接律   
    若E是关系代数表达式，F1, F2是条件，则有：
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210629.png)
    - 定理L5：选择和投影交换律
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210709.png)
    - 定理L6：选择和积的交换律
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210802.png)
    - 定理L7：投影和积的交换律
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210844.png)
    - 定理L8：选择和并的交换律
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115210926.png)
    - 定理L9：选择和差的交换律
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115211004.png)
    - L10：投影和并的交换律
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115211052.png)
- 基于关系代数的查询优化算法及示例   
略

## 物理查询优化
- 为什么要物理查询优化？
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115211243.png)
- 物理查询优化—总体思路
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115211721.png)
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115211848.png)
- 如何衡量一个物理查询计划的好与坏?
    - DBMS如何衡量物理查询计划的优劣呢？ 
    - 衡量I/O访问次数 衡量CPU的占用时间 
    - 内存使用代价(与缓冲区数目与大小的匹配) 
    - 中间结果存储代价
    - 计算量(如搜索记录、合并记录、排序记录、字段值的计算等) 网络通信量
    ……
- 依据什么信息判断物理查询计划的好与坏
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115212250.png)
- 如何收集这些信息?
    - 当一个表装入内存和创建索引的时候，统计信息不是被自动收集的，必须由DBA使用特定的命令来完成信息统计，这些命令就是收集统计信息并把其存入系统目录中的实用程序
    - 随着表的更新操作，统计信息可能会过时，过时的统计信息会使DBMS确定方案时决策错误，因此要求DBA定期的对有频繁更新操作的Table进行统计
    - DBA要熟悉统计信息收集命令的使用，并定期执行
    - IBM DB2使用Runstats收集统计信息
    ```
    RUNSTATS ON TABLE username.tablename
    [ WITH DISTRIBUTION [ AND DETAILED ]
    { INDEXES ALL | INDEX indexname ];
    ```
    - 例如，收集SCT数据库的Student表的统计信息
    ```
    Runstats on table SCT.student;
    ```
    - Oracle使用Analyze命令收集统计信息并将其放入系统表中
    ```
    ANALYZE { INDEX | TABLE | CLUSTER }{ indexname | tablename | clustername }
    COMPUTE STATISTICS
    { FOR TABLE | FOR ALL [ INDEXED ] COLUMNS [SIZE n] };
    ```
    - 例如，收集SCT数据库的Student表的统计信息
    ```
    Analyze table student compute statistics for table ;
    ```
- 代价估算
    - 什么是代价估算?
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115212831.png)
    - 投影运算的代价估算?
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115212949.png)
    - 不同选择运算的代价估算?
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115213055.png)
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115213145.png)
    - 代价估计简要结论
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115213321.png)




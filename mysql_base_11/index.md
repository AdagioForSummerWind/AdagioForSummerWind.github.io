# Mysql_base_11

> 笔记来自哈工大深圳2021秋数据库系统理论课授课老师：刘圣鑫

# 数据库查询实现算法
## 数据库查询实现算法-I
### 数据库查询实现算法概述
查询实现&查询优化：
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211113162053.png)
查询实现算法总览：
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211113162151.png)
### 以连接操作为例看逻辑实现算法与物理实现算法
连接操作的逻辑实现算法：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211113162320.png)
物理算法需要考虑：关系是存储在磁盘上的，磁盘是以磁盘块为操作单位，首先要被装载进内存(I/O操作)，然后再进行元组的处理

连接操作的基本实现算法：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211113162715.png)
连接操作的全主存实现算法：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211113162906.png)
连接操作的半主存实现算法：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211113163002.png)
连接操作的大关系实现算法：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211113163055.png)

连接操作的其他物理实现算法：   
- 表空间扫描法
    - 基本实现算法P1
        - 适用于任何情况：3块内存即可，但算法复杂性高: BR + BR * BS
    - 全主存实现算法P2
        - 要求内存能够完全装载两个关系。算法复杂性低：BR + BS
    - 半主存实现算法P3
        - 要求内存能够完全装载一个关系。算法复杂性低：BR + BS
    - 大关系实现算法P4
        - 适用于任何情况，尤其是大关系情况下比算法P1好。
        - 算法复杂性低：BR(BS/(M-2)) + BS
- 归并排序(Sort-Merge)连接算法P5
- 散列连接(Hash连接)算法P6
- 索引连接算法P7

### 利用迭代器构造查询实现算法
例如：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211114102441.png)
迭代器算法流水线计算策略：   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211114102549.png)
迭代器：迭代的读取一个集合中的每一个元素，而封装其读取细节

有一个抽象类：
```
class iterator { void Open(); tuple GetNext();
void Close();
iterator &inputs[];
}
```
所有关系操作可继承此迭代器进行构造。不同操作，可以构造不同的Open(),GetNext(), Close()函数

迭代器的构造,迭代器示例：
- 表空间扫描法— 读取关系--R:
```
Open() {
b := R的第一块；
t := b的第一个元组；
}
GetNext() {
IF ( t 已超过块b的最后一个元组 ）
{ 将b前进到下一块
IF (没有下一块)
RETURN NotFound;
ELSE /* b是一个新块 */
t := b的第一个元组；
}
oldt := t;
将t前进到b的下一元组； RETURN t；
}
Close() {
}
```
- SELECTION(R):
```
Open() {
R.Open();
}
GetNext() {
Cont:
t:=R.GetNext();
IF (t<> NotFound)
IF F(t) == TRUE
RETURN t;
ELSE GOTO Cont;
ELSE RETURN NotFound;
}
Close() {
R.Close();
}
```
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211114103220.png)
PROJECTION(SELECTION(R)):
```
Open() {
SELECTION.Open();
}
GetNext() {
t:= SELECTION.GetNext();
IF (t<> NotFound) {
p := PROJECTION(t, )
RETURN p;
}
ELSE RETURN NotFound;
}
Close() {
SELECTION.Close();
}
```
- R Join S:
```
Open() {
R.Open(); S.Open();
r:= R.GetNext();
}
GetNext() {
REPEAT{
s:= S.GetNext();
IF ( s == NotFound ）
{ S.Close();
r: = R.GetNext();
IF (r == NotFound)
RETURN NotFound;
ELSE { S.Open();
s := S.GetNext(); }
} }
UNTIL (r与s能够连接);
RETURN r和s的连接；
}
Close() {
R.Close(); S.Close();
}
```

### 几个关系操作的一趟扫描算法
- 关系/表数据的读取---完整地读取一个关系（B(R)是R的存储块数目 T(R)是R的元组数目）
    - 聚簇关系—关系的元组集中存放(一个块中仅是一个关系中的元组):
        - TableScan(R) ---表空间扫描算法
            - 扫描结果未排序: B(R)
        - SortTableScan(R)
            - 扫描结果排序: 3B(R)
        - IndexScan(R)---索引扫描算法
            - 扫描结果未排序: B(R)
        - SortIndexScan(R)
            - 扫描结果排序: B(R) or 3B
    - 非聚簇关系—关系的元组不一定集中存放(一个块中不仅是一个关系中的元组):
        - 扫描结果未排序: T(R)
        - 扫描结果排序: T(R) + 2B(R
- 整个关系的一元操作实现算法
    - 去重复：&(R)
        - 需要在内存中保存已处理过的元组
        - 当新元组到达时，需与之前处理过的元组进行比较
        - 建立不同的内存数据结构，来保存之前处理过的数据，以便快速处理整个关系上的操作
        - 算法复杂性：B(R)
        - 应用条件：B(&(R))<=M-2 
    - 分组聚集Y L(R)
        - 需要在内存中保存所有的分组
        - 保存每个分组上的聚集信息
        - 建立不同的内存数据结构(建立内存数据结构，以快速定位一个元组，如排序结构/散列结构/B+树等)，来保存之前处理过的数据，以便快速处理整个关系上的操作
        - 算法复杂性：B(R)
        - 应用条件：所有分组的数量应能在内存中完整保存
- 整个关系的二元操作实现算法
    - 集合上的操作(并交差)
        - 扫描一个关系，然后再扫描另一个关系
        - 集合的操作需要去重复
        - 算法复杂性：B(R)+B(S)
        - 应用条件：min(B(R), B(S))<=M-2
### 基于索引的算法
- 基于索引的选择算法
    - 选择条件中有涉及到索引属性时，可以使用索引，辅助快速检索；
    - 在某些属性上存在着索引，可能在多个属性上都存在着索引；
    - 聚簇和非聚簇索引，使用时其效率是不一样的。
    - 如何通过索引来判断范围性的条件？
    - 索引应用分析示例
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115103611.png)
    ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115103722.png)
- 基于有序索引的连接算法--Zig-Zag连接算法   
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115103949.png)
### 总结
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115115125.png)
## 数据库查询实现算法-II
### 为什么需要两趟算法? 
- 整个关系操作存在的问题?
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115104438.png)
- 两趟算法的基本思路?
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115104538.png)
### 两阶段多路归并排序TPMMS
- 内排序和外排序问题
    - 内排序问题: 待排序的数据可一次性地装入内存中，即排序者可以完整地看到和操纵所有数据。内存中数据的排序算法：插入排序算法、选择排序算法、冒泡排序算法…。 
    - 外排序问题: 待排序的数据不能一次性装入内存，即排序者不能一次完整地看到和操纵所有数据，需要将数据分批装入内存分批处理的排序问题；
- 外排序问题分析
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115113454.png)
- 算法基本思想
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115113626.png)
### 基于排序的两趟扫描算法
- 去重复操作
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115113809.png)
- 分组聚集操作
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115113853.png)
- 基于排序的并、交和差
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115113952.png)
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115114054.png)
对于集合差：R - S，当且仅当t出现在R中但不出现在S中时输出
- 基于排序的连接运算
    - 第一趟：划分R和S的子表并进行子表排序，排序均基于Y属性排序。
    - 第二趟：归并时注意是R的输入还是S的输入。R和S的两路输入之间进行连接检查并连接后输出。
    - “排序-连接”算法，“归并-连接”算法，“排序-归并-连接”算法
    - SORT-JOIN， MERGE-JOIN， SORT-MERGE-JOIN
### 基于散列的两趟扫描算法
- 基本思想
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115114512.png)
- 去重复操作
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115114623.png)
- 分组聚集操作
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115114811.png)
- 基于散列的并、交和差操作
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115114912.png)
- 基于散列的连接操作
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115114947.png)
### 总结
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211115115033.png)




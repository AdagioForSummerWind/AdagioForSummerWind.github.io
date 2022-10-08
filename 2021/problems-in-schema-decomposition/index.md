# Mysql_base_08

# 模式分解存在的问题
## 模式分解存在什么问题
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113100944.png)
模式分解需要关注：   
- R与p在数据内容方面是否等价：分解的无损连接性；
- R与p在数据依赖方面是否等价：分解的保持依赖性。

![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113101137.png)
- 数据约束的等价性,分解后是否有约束丢失了?
- 当模式不符合关系范式时，进行模式分解时，分解后的关系的连接与分解前关系的等价性? 分解前的约束，在分解后是否还存在?


## 无损连接分解及其检验算法
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113101525.png)
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113101600.png)
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113101714.png)
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113101742.png)
## 保持依赖分解及其检验算法
保持依赖分解：   
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113101841.png)
保持依赖性检验算法：   
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113105344.png)
## 关系模式无损连接或保持依赖的分解算法
无损连接分解成BCNF的算法：   
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113105507.png)
保持依赖分解成3NF的算法：   
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113105712.png)
无损连接分解成4NF：   
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113110538.png)
连接依赖：   
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113110804.png)
5NF:
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113110849.png)
## 数据库设计需要知道的
数据库设计理论要解决的根本问题：   
- 哪些属性被组织成一个关系?
- 是一个大关系模式呢，还是若干小关系模式?
- 大关系模式存在什么问题?

关系模式设计的折中：   
- 关系模式设计需要折中
- 遵循关系范式原则，则需要将一个关系模式，拆解成两个或多个小的模式；而查询时，需要将这两个或多个小的模式联结成一个模式；
- 遵循关系范式原则避免了冗余、插入异常、删除异常等问题，但由于联结运算的低效率，使得查询速度很慢。因此需要折中。
## 总结
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211113111108.png)




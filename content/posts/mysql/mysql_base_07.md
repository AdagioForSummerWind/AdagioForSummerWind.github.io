---
title: "Mysql_base_07"
date: 2021-11-09T11:28:16+08:00
lastmod: 2021-11-13
tags: [mysql]
categories: [School courses]
slug: Functional dependence and its axiom theorem
draft: true
---
> 笔记来自哈工大深圳2021秋数据库系统理论课授课老师：刘圣鑫

# 函数依赖及其公理定理
## 函数依赖
定义：   
函数依赖:
设R(U)是属性集合U={A1,A2,…,An}上的一个关系模式，X, Y是U上的两个子集，若对R(U)的任意一个可能的关系r, r中不可能有两个元组满足在X中的属性值相等而在Y中的属性值不等，则称“X函数决定Y”或“Y函数依赖于X”,记作X->Y。   
示例：U = {学号，姓名，年龄，班号，班长，课号，成绩}   
学号->{姓名，年龄}   
{学号，课号} -> 成绩   
函数依赖的特性：
* 对X->Y,但Y不包含于X, 则称X->Y为非平凡的函数依赖；
* 若X->Y,则任意两个元组,若X上值相等,则Y上值必然相等,则称X为决定因素；
* 若X->Y,Y->X,则记作X<->Y；
* X->Y,有基于模式R的,则要求对任意的关系r成立；有基于具体关系r 的,则要求对某一关系r成立；
* 如一关系r的某属性集X,r中根本没有X上相等的两个元组存在,则X->Y恒
成立。
## 完全函数依赖与传递函数依赖
部分或完全函数依赖：   
在R(U)中，若X->Y并且对于X的任何真子集X'都有y不依赖于x',则称Y完全函数依赖于X, 记为：![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211112113023.png)否则称Y部分函数依赖于X,记为：![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211112113050.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211112113259.png)

传递函数依赖:   
在R(U)中，![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211112113438.png)则称Z传递函于X。

几个重要概念：   
* 候选键
    * 设K为R(U)中的属性或属性组合，若U部分依赖于K, 则称K为R(U)上的候选键(Candidate Key)。说明：
        * 可任选一候选键作为R的主键(Primary Key)； 
        - 包含在任一候选键中的属性称主属性(Prime Attribute), 其他属性称非主属性；
        - 若K是R的一个候选键，S包含于K, 则称S为R的一个超键(Super Key)
- 逻辑蕴含
    - 设F是关系模式R(U)中的一个函数依赖集合X，Y是R的属性子集，如果从F中的函数依赖能够推导出X->Y，则称F逻辑蕴涵X->Y，或称X->Y是F的逻辑蕴涵。记作F|= X->Y
    - 设F是关系模式R(U)的函数依赖集, X->Y是一个函数依赖，若对R中的每个满足F的关系r, 能够用逻辑推理的方法推出r也满足X->Y，则称F|= X->Y。
- 闭包
    - 被F逻辑蕴涵的所有函数依赖集合称为F的闭包(Closure)，记作F+。
    - 若F+=F, 则说F是一个全函数依赖族(函数依赖完备集)。
- 属性(集)闭包
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113085606.png)
## 关于函数依赖的公理和定理
用于隐含函数依赖推导：   
- 设R(U)是属性集U={A1,A2,…,An}上的一个关系模式，F为R(U)的一组函数依赖，记为R(U, F), 则有如下规则：
    - [A1]自反律(Reflexivity rule)：若Y真包含于X真包含于U，则X->Y被F逻辑蕴涵。
    - [A2]增广律(Augmentation rule)：若X真包含于Y真包含于F，且Z->U,则XZ->YZ被F逻辑蕴涵。
    - [A3]传递律(Transtivity rule)：若X真包含于Y真包含于F，且Y->Z，则X->Z被F逻辑蕴涵。
- 由A1.A2,A3可推出如下结论：
    - 合并律(Union Rule)：若X->Y且X->Z, 则X->YZ。
    - 伪传递律(Pseudo Transitivity)：若X->Y且WY->Z，则XW->Z。
    - 分解律(Decomposition Rule)：若X->Y且Z->Y, 则X->Z
    - 如果A1,A2,…,An是属性，则X->A1,A2,…,An当且仅当对每个Ai有X->Ai(1 <= i <= n)
    - X->Y，可从F由Armstrong Axiom(即A1,A2,A3)导出，当且仅当真包含于X的属性闭包 
## 函数依赖集的最小覆盖
覆盖：   
对R(U)上的两个函数依赖集合F、G, 如果F+=G+，则称F和G是等价的，也称F覆盖G或者G覆盖   
引理：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113090019.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113090446.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113090514.png)   
每个函数依赖集F可被一个其右端至多有一个属性的函数依赖之集G覆盖。

最小覆盖：
若F满足以下条件，则称F为最小覆盖(minimal Cover)或最小依赖集 
(minimal set of Functional Depandency)：   
- F中每个函数依赖的右部是单个属性；
- 对任何X->A属于F，有F- {X->A}不等价于F；
- 对任何X->A属于F，Z包含于X，(F- {X->A})U{Z->A}不等价于F

每个函数依赖集F都有等价的最小覆盖F’。

## 关系模式设计之规范形式
### 关系的第1NF和第2NF
- 若关系模式R(U)中关系的每个分量都是不可分的数据项(值、原子)，则称R(U)属于第一范式，记为：R(U) 属于 1NF。
    - 1NF要求关系中不能有复合属性、多值属性及其组合
- 若R(U)属于1NF且U中的每一非主属性完全函数依赖于候选键，则称R(U)属于第二范式，记为：R(U) 属于 2NF。
    - 第二范式消除了非主属性对候选键的部分依赖
### 关系的第3NF和Boyce-Codd NF
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113093439.png)
第3范式消除了非主属性对侯选键的传递依赖
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113093851.png)
### 多值依赖及其公理定理
多值依赖：   
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113094234.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211117113449.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113094342.png)
[Armstrong's Axioms A4~A8]关于多值依赖的公理：   
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113094612.png)
**在设计数据库结构的时候，要尽量遵守三范式，如果不遵守，必须有足够的理由。比如性能。事实上我们经常会为了性能而妥协数据库的设计。**
### 关系的第4NF
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211113094422.png)
第四范式消除了非主属性对候选键以外属性的多值依赖


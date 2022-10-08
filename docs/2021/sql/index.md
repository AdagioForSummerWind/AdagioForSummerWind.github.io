# Mysql_base_03

# sql语言
## SQL语言概述
### SQL语言提出和发展
* 1974年，由Boyce和Chamber提出
* 1975-1979年，在System R上首次实现，由IBM的San Jose研究室研制，
称为Sequel(Structured English QUEry Language)
* 1986年推出了SQL标准：SQL-86，
“数据库语言SQL: Structured Query
Language”
* 1989年ANSI / ISO推出了SQL标准: SQL-89, 数据库语言SQL的标准集合
* 1992年进一步推出了SQL标准：SQL-92，也称为SQL2
    * 是SQL-89的超集
    * 增加了许多新特性，如新数据类型，更丰富数据操作，更强完整性支持等
    * 原SQL-89被称为entry-SQL, 扩展的被称为Intermediate级和Full级   
* 1999年进一步推出了SQL标准： SQL-99，也称为SQL3
    * 对面向对象的一些特征予以支持，支持抽象数据类型，支持行对象和列对象等
    * 对递归、触发等复杂操作也予以规范化定义
    * 有些特征，现有数据库厂商尚不能做到完全支持
    * 废弃了SQL2的分级，但定义了core-SQL及扩展的SQL
* SQL 2003； SQL 2006； SQL 2008
* SQL还有一个标准是X/Open标准，主要强调各厂商产品的可移植性，只包含被各厂商广泛认可的操作

### SQL语言概览
* SQL语言是集DDL、DML和DCL于一体的数据库语言
* SQL语言主要由以下9个单词引导的操作语句来构成，但每一种语句都能
    表达复杂的操作请求
    * DDL语句引导词：Create(建立), Alter (修改), Drop(撤消)   
        * 模式的定义和删除，包括定义Database, Table, View,Index,完整性约束条件等，也包括定义对象(RowType行对象, Type列对象)
    * DML语句引导词：Insert, Update, Delete, Select   
        * 各种方式的更新与检索操作，如直接输入记录，从其他Table(由SubQuery建立)输入,各种复杂条件的检索，如连接查找，模糊查找，分组查找，嵌套查找等
        * 各种聚集操作，求平均、求和、…等，分组聚集，分组过滤等
    * DCL语句引导词：Grant, Revoke
        * 安全性控制：授权和撤消授权

## 简单的SQL-DDL/DML: 创建数据库

create database 数据库名;   
create table 表名( 列名 数据类型 [Primary key |UNIQUE] [not null][, 列名 数据类型 [not null] , … ]) ;

在表中追加元组的值要使用DML（Data Manipulation Language）
* 向Table中追加新的元组：Insert
* 修改Table中某些元组中的某些属性的值: Update
* 删除Table中的某些元组: Delete
* 对Table中的数据进行各种条件的检索: Select
## SQL-DML之查询Select
### 基本的检索操作
Select 的简单语法形式为：
Select 列名 [[, 列名] … ]
From 表名
[ Where 检索条件 ] ;   
基本检索操作：   
* 结果唯一性问题：尽管关系模型要求无重复元组出现在数据库中，但现实DBMS操作中，是允许检索结果出现重复元组的，但也允许无重复元组
* 结果排序问题：DBMS可以对检索结果进行排序，可以升序排列，也可以
降序排列。order by 列名 [asc | desc]
* 模糊查询问题：
    * 含有like运算符的表达式
        * 列名 [not ] like “字符串”
    * 找出匹配给定字符串的字符串。其中给定字符串中可以出现%, _等匹配符
    * 匹配规则
        * % 0个或多个字符
        * _ 任意单个字符
        * \ 转义字符，用于去掉一些特殊字符的特定含义
    * 举例：Select S#, Sname From StudentWhere Sname Like ‘张%’
### 多表联合检索
连接运算涉及到重名的问题，如两个表中的属性重名，连接的两个表重名
(同一表的连接)等，因此需要使用别名以便区分   
select中采用别名的方式（as可省略）：
Select 列名 as 列别名 [ [, 列名 as 列别名] … ]
From 表名1 as 表别名1, 表名2 as 表别名2, …
Where 检索条件
### 子查询
出现在Where子句中的Select语句被称为子查询(subquery) , 子查询返回了一个集合，可以通过与这个集合的比较来确定另一个查询集合。   
子查询类型：   
* in谓词:表达式 [not ] in (子查询)
    * 非相关子查询： 内层查询独立进行，没有涉及任何外层查询相关信息的子查询被称为非相关子查询。
    * 相关子查询：内层查询需要依靠外层查询的某些参量作为限定条件才能进行，这样的子查询称为相关子查询。外层向内层传递的参量需要使用外层的表名或表别名来限定
* *some / *all谓词（*为< , > , >=, <=）：后接子查询
    * 语义：将表达式的值与子查询的结果进行比较：
        * 如果表达式的值至少与子查询结果的某一个值相比较满足* 关系，则“表达式 * some (子查询)”的结果便为真；
        * 如果表达式的值与子查询结果的所有值相比较都满足* 关系，则“表达式 * all (子查询)”的结果便为真；
    * 表达式 = some (子查询)和表达式 in (子查询)等价；表达式 not in (子查询)与表达式 <> all (子查询)等价
* Exists谓词：[not] Exists (子查询)，不加not形式的Exists谓词可以不用。
### 结果计算与聚集函数
前面介绍的select-from-where语句中，select子句后面不仅可以是列名，而且可以是一些计算表达式或聚集函数，表明在选择和投影的同时直接进行一些运算，如下所示：   
Select 列名 | expr | agfunc(列名) [[, 列名 | expr | agfunc(列名) ] … ]
From 表名1 [, 表名2 … ]
[ Where 检索条件];   
计算表达式可以是常量、列名或由常量、列名、特殊函数及算术运算符构
成的算术运算式。特殊函数的使用需结合各自DBMS的说明书。   
例如，有计算表达式投影列的示例:求有差额(差额>0)的任意两位教师的薪水差额    
```
Select T1.Tname as TR1, T2.Tname as TR2, T1.Salary – T2.Salary
From Teacher T1, Teacher T2
Where T1.Salary > T2.Salary;
```
* SQL提供了五个作用在简单列值集合上的内置聚集函数agfunc, 分别是：COUNT、SUM、AVG、MAX、MIN
* SQL聚集函数的参数类型、结果类型与作用如下：
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211109234009.png)
* eg:   
```
Select Sum(Salary) From Teacher;
Select AVG(Score) From Course C, SC
Where C.Cname = ‘数据库’ and C.C# = SC.C;
```

### 分组查询与分组过滤
为解决同时求解若干个集合的聚集运算问题，引出了分组的概念。SQL可
以将检索到的元组按照某一条件进行分类，具有相同条件值的元组划到一个组或一个集合中，这一过程就是分组过程。   
Select 列名 | expr | agfunc(列名) [[, 列名 | expr | agfunc(列名) ] … ]
From 表名1 [, 表名2 … ]
[ Where 检索条件 ]
[ Group by 分组条件(列名) ];
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211109234447.png)

若要对集合(即分组)进行条件过滤，可使用Having子句   
Having子句，又称为分组过滤子句。需要有Group by子句支持，换句话说，没有Group by子句，便不能有Having子句。   
Select 列名 | expr | agfunc(列名) [[, 列名 | expr | agfunc(列名) ] … ]
From 表名1 [, 表名2 … ]
[ Where 检索条件 ]
[ Group by 分组条件 [ Having 分组过滤条件] ];   
例如   
```求不及格课程超过两门的同学的学号:
Select S# From SC
Where Score < 60
Group by S# Having Count(*)>2;
```
```再如求有10人以上不及格的课程号:
Select C# From SC
Where Score < 60
Group by C# Having Count(*)>10;
```

分组查询仍需要注意语义问题:   
例如求有两门以上不及格课程的同学的学号及其平均成绩:   
错误写法：   
```
Select S#, Avg(Score) From SC
Where Score < 60
Group by S# Having Count(*)>2;
```
前述写法是不正确的，它求出的是那两门不及格课程的平均成绩，而不是
该同学所有课程的平均成绩，后者是题目要求的。因此正确写法为：   
```
Select S#, AVG(Score) From SC
Where S# in
( Select S# From SC
Where Score < 60
Group by S# Having Count(*)>2 )
Group by S#;
```

### 并、交、差的处理
SQL语言中为并运算提供了运算符UNION, 为交运算提供了运算符
INTERSECT, 为差运算提供了运算符EXCEPT，其基本语法形式为：   
子查询 { Union [ALL] | Intersect [ALL] | Except [ALL] 子查询       
通常情况下是自动删除重复元组。但有时为了保留重复的元
组，则需使用ALL保留字，ALL保留字是允许重复元组出现，具体使用如下：   
假设子查询1的一个元组出现m次，子查询2的一个元组出现n次，则
该元组在：   
* 子查询1 Union ALL 子查询2 ，出现m + n次
* 子查询1 Intersect ALL 子查询2 ，出现min(m,n)次
* 子查询1 Except ALL 子查询2 ，出现max(0, m – n)次

示例：
```
Select S# From SC Where C# = ‘002’
UNION
Select S# From SC Where C# ='003';
```

交运算符Intersect并没有增强SQL的表达能力，没有Intersect， SQL也
可以用其他方式表达同样的查询需求，如上例所述。只是有了Intersect更容易表达一些，但增加了SQL语言的不唯一性。

它们都是Core-SQL99的一部分，但有些DBMS并不支持这些运算，使用时要注意。
### 空值处理
空值是其值不知道、不确定、不存在的值   
数据库中有了空值，会影响许多方面，如影响聚集函数运算的正确性，不能参与算术、比较或逻辑运算等   
在SQL标准中和许多现流行的DBMS中，空值被用一种特殊的符号Null来
标记，使用特殊的空值检测函数来获得某列的值是否为空值
eg:   
```
Select Sname From Student
Where Sage is null ;
```
### 内连接、外连接
关系代数运算中，有连接运算，又分为连接和外连接   
在SQL的高级语法中引入了内连接与外连接运算，具体形式如下：   
Select 列名 [ [, 列名] … ]
From 表名1 [NATURAL]
[ INNER | { LEFT | RIGHT | FULL} [OUTER]] JOIN 表名2
{ ON 连接条件 | Using (Colname {, Colname …}) }
[ Where 检索条件 ] …   
连接运算由两部分构成：连接类型和连接条件    
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211110000120.png)
* Inner Join: 即关系代数中的-连接运算
* Left Outer Join, Right Outer Join, Full Outer Join: 即关系代数中的外连接运算
    * 如“表1 Left Outer Join 表2”，则连接后，表1的任何元组t都会出现在结果表中，如表2中有满足连接条件的元组s, 则t与s连接；否则t与空值元组连接；
    * 如“表1 Right Outer Join 表2”，则连接后，表2的任何元组s都会出现在结果表中，如表1中有满足连接条件的元组t, 则t与s连接；否则s与空值元组连接；
    * 如“表1 Full Outer Join 表2”，是前两者的并。
* 连接中使用 natural
    * 出现在结果关系中的两个连接关系的元组在公共属性上取值相等，且公共属性只出现一次
* 连接中使用 on <连接条件>
    * 出现在结果关系中的两个连接关系的元组取值满足连接条件，且公共属性出现两次
* 连接中使用 using (Col1, Col2, ..., Coln)
    * (Col1, Col2, ..., Coln)是两个连接关系的公共属性的子集，元组在(Col1,Col2, ..., Coln)上取值相等，且(Col1, Col2, ..., Coln)只出现一次

### SQL的完整语法
```
Subquery ::==
SELECT [ ALL | DISTINCT ] { * | expr [[AS] c_alias] {, … } }
FROM tableref {, … }
[WHERE search_condition]
[GROUP BY column {, … }]
[HAVING search_condition]
| subquery [UNION [ALL] | INTERSECT [ALL] | EXCEPT [ALL]]
[CORRESPONDING [BY] (colname {, … })] subquery;
Tableref ::== tablename [corr_name]
Select statement ::==
Subquery [ORDER BY result_column[ASC | DESC] { , … }]
```

## SQL-DML之更新Insert/Update/Delete
 元组新增Insert命令有两种形式
* 单一元组新增命令形式：插入一条指定元组值的元组insert into 表名 [(列名[，列名]…)]values (值 [，值]…)；
* 批数据新增命令形式：插入子查询结果中的若干条元组。待插入的元 组由子查询给出。insert into 表名 [(列名[，列名]…)]子查询;
    * eg:
    ```
    Insert Into St (S#, Sname, avgScore) Select S#, Sname, Avg(Score) From Student, SC Where Student.S# = SC.S# Group by Student.S#
    ```
* 当有元组新增操作时，DBMS会检查用户定义的完整性约束条件等，如不符合完整性约束条件，则将不会执行新增动作（同删除操作）。

SQL-DML之元组更新Update
* 元组更新Update命令: 用指定要求的值更新指定表中满足指定条
件的元组的指定的列的值
Update 表名
Set 列名 = 表达式 | (子查询)
[ [ , 列名 = 表达式 | (子查询) ] … ]
[ Where 条件表达式] ;
* 如果Where条件省略，则更新所有的元组。


## SQL-视图及DDL的进一步介绍
### 视图的定义与使用
```
Create View CompStud AS
( Select * From Student
Where D# in ( Select D# From Dept
Where Dname = ‘计算机’ );
```
### 视图更新问题
SQL视图更新操作受到很大的约束，很多情况是不能进行视图
更新的:   
* 如果视图的select目标列包含聚集函数，则不能更新
* 如果视图的select子句使用了unique或distinct，则不能更新
* 如果视图中包括了group by子句，则不能更新
* 如果视图中包括经算术表达式计算出来的列，则不能更新
* 如果视图是由单个表的列构成，但并没有包括主键，则不能更新

对于由单一Table子集构成的视图，即如果视图是从单个基本表使用选
择、投影操作导出的，并且包含了基本表的主键，则可以更新

### SQL-DDL的进一步介绍: 撤消语句的使用
不仅视图可以撤消，基本表、数据库等都可以撤消   
撤消基本表：drop table 表名   
**注意**，SQL-delete语句只是删除表中的元组, 而撤消基本表drop table的
操作是撤消包含表格式、表中所有元组、由该表导出的视图、等相关的所有内容，所以使用要特别注意。当表定义完成后，通常不是撤消表，而可能要修正表的定义，此时可使用Alter table语句修正基本表的定义：   
alter table tablename   
[add {colname datatype}]   
[drop {完整性约束名}]   
[modify {colname datatype, …}]   
指定当前数据库
use 数据库名;
关闭当前数据库
close 数据库;




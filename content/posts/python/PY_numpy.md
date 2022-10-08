---
title: "PY_numpy"
date: 2022-05-03T16:28:01+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
> 学习[菜鸟教程](https://www.runoob.com)笔记

## 前言
NumPy 通常与 SciPy（Scientific Python）和 Matplotlib（绘图库）一起使用， 这种组合广泛用于替代 MatLab，是一个强大的科学计算环境，有助于我们通过 Python 学习数据科学或者机器学习。

SciPy 是一个开源的 Python 算法库和数学工具包。

SciPy 包含的模块有最优化、线性代数、积分、插值、特殊函数、快速傅里叶变换、信号处理和图像处理、常微分方程求解和其他科学与工程中常用的计算。

Matplotlib 是 Python 编程语言及其数值数学扩展包 NumPy 的可视化操作界面。它为利用通用的图形用户界面工具包，如 Tkinter, wxPython, Qt 或 GTK+ 向应用程序嵌入式绘图提供了应用程序接口（API）。
## NumPy Ndarray对象
N 维数组对象 ndarray：
- 用于存放同类型元素的多维数组。
- 每个元素在内存中都有相同存储大小的区域。
- 以 0 下标为开始进行集合中元素的索引。

ndarray 内部由以下内容组成：
- 一个指向数据（内存或内存映射文件中的一块数据）的指针。
- 数据类型或 dtype，描述在数组中的固定大小值的格子。
- 一个表示数组形状（shape）的元组，表示各维度大小的元组。
- 一个跨度元组（stride），其中的整数指的是为了前进到当前维度下一个元素需要"跨过"的字节数。
    - 跨度可以是负数，这样会使数组在内存中后向移动，切片中 obj[::-1] 或 obj[:,::-1] 就是如此。

```
py
import numpy as np
np.array(object, dtype = None, copy = True, order = None, subok = False, ndmin = 0)
a = np.array([1,2,3])  
b = np.array([[1,  2],  [3,  4]])  
c = np.array([1, 2, 3, 4, 5], ndmin =  2)  
d = np.array([1,  2,  3], dtype = complex)  
```
- object	数组或嵌套的数列
- dtype	数组元素的数据类型，可选
- copy	对象是否需要复制，可选
- order	创建数组的样式，C为行方向，F为列方向，A为任意方向（默认）
- subok	默认返回一个与基类类型一致的数组
- ndmin	指定生成数组的最小维度

ndarray 对象由计算机内存的连续一维部分组成，并结合索引模式，将每个元素映射到内存块中的一个位置。内存块以行顺序(C样式)或列顺序(FORTRAN或MatLab风格，即前述的F样式)来保存元素。

## NumPy数据类型
numpy 支持的数据类型比 Python 内置的类型要多很多，基本上可以和 C 语言的数据类型对应上，其中部分类型对应为 Python 内置的类型。
```
py
bool_	布尔型数据类型（True 或者 False）
int_	默认的整数类型（类似于 C 语言中的 long，int32 或 int64）
intc	与 C 的 int 类型一样，一般是 int32 或 int 64
intp	用于索引的整数类型（类似于 C 的 ssize_t，一般情况下仍然是 int32 或 int64）
int8	字节（-128 to 127）
int16	整数（-32768 to 32767）
int32	整数（-2147483648 to 2147483647）
int64	整数（-9223372036854775808 to 9223372036854775807）
uint8	无符号整数（0 to 255）
uint16	无符号整数（0 to 65535）
uint32	无符号整数（0 to 4294967295）
uint64	无符号整数（0 to 18446744073709551615）
float_	float64 类型的简写
float16	半精度浮点数，包括：1 个符号位，5 个指数位，10 个尾数位
float32	单精度浮点数，包括：1 个符号位，8 个指数位，23 个尾数位
float64	双精度浮点数，包括：1 个符号位，11 个指数位，52 个尾数位
complex_	complex128 类型的简写，即 128 位复数
complex64	复数，表示双 32 位浮点数（实数部分和虚数部分）
complex128	复数，表示双 64 位浮点数（实数部分和虚数部分）
```
numpy 的数值类型实际上是 dtype 对象的实例，并对应唯一的字符，包括 np.bool_，np.int32，np.float32，等等。

### 数据类型对象（dtype）
数据类型对象（numpy.dtype 类的实例）用来描述与数组对应的内存区域是如何使用，它描述了数据的以下几个方面：
- 数据的类型（整数，浮点数或者 Python 对象）
- 数据的大小（例如， 整数使用多少个字节存储）
- 数据的字节顺序（小端法或大端法）
    - 字节顺序是通过对数据类型预先设定 < 或 > 来决定的。 < 意味着小端法(最小值存储在最小的地址，即低位组放在最前面)。> 意味着大端法(最重要的字节存储在最小的地址，即高位组放在最前面)。
- 在结构化类型的情况下，字段的名称、每个字段的数据类型和每个字段所取的内存块的部分
- 如果数据类型是子数组，那么它的形状和数据类型是什么。
```
py
import numpy as np
numpy.dtype(object, align, copy)
# 标量类型
dt = np.dtype(np.int32)
# int8, int16, int32, int64 四种数据类型可以使用字符串 'i1', 'i2','i4','i8' 代替
dt = np.dtype('i4')
# 字节顺序标注，小端
dt = np.dtype('<i4')
# 创建结构化数据类型
dt = np.dtype([('age',np.int8)]) #dt=[('age', 'i1')]
student = np.dtype([('name','S20'), ('age', 'i1'), ('marks', 'f4')]) #student=[('name', 'S20'), ('age', 'i1'), ('marks', 'f4')]
a = np.array([('abc', 21, 50),('xyz', 18, 75)], dtype = student) 
# a=[('abc', 21, 50.0), ('xyz', 18, 75.0)]
# 将数据类型应用于 ndarray 对象
dt = np.dtype([('age',np.int8)]) 
a = np.array([(10,),(20,),(30,)], dtype = dt) #a=[(10,) (20,) (30,)]
# 类型字段名可以用于存取实际的 age 列
dt = np.dtype([('age',np.int8)]) 
a = np.array([(10,),(20,),(30,)], dtype = dt) #a['age']=[10 20 30]

```
- object - 要转换为的数据类型对象
- align - 如果为 true，填充字段使其类似 C 的结构体。
- copy - 复制 dtype 对象 ，如果为 false，则是对内置数据类型对象的引用

每个内建类型都有一个唯一定义它的字符代码，如下：
```
b	布尔型
i	(有符号) 整型
u	无符号整型 integer
f	浮点型
c	复数浮点型
m	timedelta（时间间隔）
M	datetime（日期时间）
O	(Python) 对象
S, a	(byte-)字符串
U	Unicode
V	原始数据 (void)
```
## NumPy数组属性
NumPy 数组的维数称为秩（rank）。

在 NumPy中，每一个线性的数组称为是一个**轴（axis）**，也就是维度（dimensions）。比如说，二维数组相当于是两个一维数组，其中第一个一维数组中每个元素又是一个一维数组。所以一维数组就是 NumPy 中的轴（axis），第一个轴相当于是底层数组，第二个轴是底层数组里的数组。而轴的数量——秩，就是数组的维数。
- 很多时候可以声明 axis。axis=0，表示沿着第 0 轴进行操作，即对每一列进行操作；axis=1，表示沿着第1轴进行操作，即对每一行进行操作。

NumPy数组比较重要的ndarray对象属性：
```
py
# 1. ndarray.ndim	秩，即轴的数量或维度的数量
a = np.arange(24)  
print (a.ndim)             # a 现只有一个维度
# 现在调整其大小
b = a.reshape(2,4,3)  # b 现在拥有三个维度,reshape()和shape()都可以调整数组大小
print (b.ndim)

# 2. ndarray.shape	数组的维度，对于矩阵，n 行 m 列
a = np.array([[1,2,3],[4,5,6]])  
print (a.shape)#(2,3)

# 3. ndarray.size	数组元素的总个数，相当于 .shape 中 n*m 的值
# 4. ndarray.dtype	ndarray 对象的元素类型
# 5. ndarray.itemsize	ndarray 对象中每个元素的大小，以字节为单位
#例如，一个元素类型为 float64 的数组 itemsize 属性值为 8，一个元素类型为 complex32 的数组 item 属性为 4
# 6. ndarray.flags	ndarray 对象的内存信息
# 7. ndarray.real	ndarray元素的实部
# 8. ndarray.imag	ndarray 元素的虚部
# 9. ndarray.data	包含实际数组元素的缓冲区，由于一般通过数组的索引获取元素，所以通常不需要使用这个属性。
# 10. ndarray.flags 返回 ndarray 对象的内存信息，包含以下属性：
    # C_CONTIGUOUS (C)	数据是在一个单一的C风格的连续段中
    # F_CONTIGUOUS (F)	数据是在一个单一的Fortran风格的连续段中
    # OWNDATA (O)	数组拥有它所使用的内存或从另一个对象中借用它
    # WRITEABLE (W)	数据区域可以被写入，将该值设置为 False，则数据为只读
    # ALIGNED (A)	数据和所有元素都适当地对齐到硬件上
    # UPDATEIFCOPY (U)	这个数组是其它数组的一个副本，当这个数组被释放时，原数组的内容将被更新
x = np.array([1,2,3,4,5])  
>>>print (x.flags)
>>>
  C_CONTIGUOUS : True
  F_CONTIGUOUS : True
  OWNDATA : True
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False
```
## NumPy创建数组
ndarray 数组除了可以使用底层 ndarray 构造器来创建外，也可以通过以下几种方式来创建：
- np.empty
```
py
numpy.empty(shape, dtype = float, order = 'C')
#order有"C"和"F"两个选项,分别代表，行优先和列优先，在计算机内存中的存储元素的顺序。
x = np.empty([3,2], dtype = int) #随机创建一个3行2列的int型数组
```
- np.zeros
```
py
#以零填充，dtype可选，默认为浮点数
numpy.zeros(shape, dtype = float, order = 'C')
# order='C' 用于 C 的行数组，或者 'F' 用于 FORTRAN 的列数组
```
- np.ones
```
py
# 以1填充
numpy.ones(shape, dtype = None, order = 'C')
```
## NumPy 从已有的数组创建数组

```
py
import numpy as np
# 1. numpy.asarray(a, dtype = None, order = None)
    # a	任意形式的输入参数，可以是，列表, 列表的元组, 元组, 元组的元组, 元组的列表，多维数组
    # dtype	数据类型，可选
    # order	可选，有"C"和"F"两个选项,分别代表，行优先和列优先，在计算机内存中的存储元素的顺序。
x =  [1,2,3] #列表
x =  (1,2,3) #元组
x =  [(1,2,3),(4,5)]# 元组列表
a = np.asarray(x)

# 2. numpy.frombuffer  实现动态数组,接受 buffer 输入参数，以流的形式读入转化成 ndarray 对象
numpy.frombuffer(buffer, dtype = float, count = -1, offset = 0)
    # buffer	可以是任意对象，会以流的形式读入。
    # dtype	返回数组的数据类型，可选
    # count	读取的数据数量，默认为-1，读取所有数据。
    # offset	读取的起始位置，默认为0。
s =  b'Hello World' #**注意**：buffer 是字符串的时候，Python3 默认 str 是 Unicode 类型，所以要转成 bytestring 在原 str 前加上 b。Python2不用加
a = np.frombuffer(s, dtype =  'S1')  
print (a)#[b'H' b'e' b'l' b'l' b'o' b' ' b'W' b'o' b'r' b'l' b'd']

# 3. numpy.fromiter 方法从可迭代对象中建立 ndarray 对象，返回一维数组。
numpy.fromiter(iterable, dtype, count=-1)
    # iterable	可迭代对象
    # dtype	返回数组的数据类型
    # count	读取的数据数量，默认为-1，读取所有数据
# 使用 range 函数创建列表对象  
list=range(5)
it=iter(list)
 
# 使用迭代器创建 ndarray 
x=np.fromiter(it, dtype=float)
print(x)#[0. 1. 2. 3. 4.]
```
## NumPy从数值范围创建数组
## NumPy切片和索引
## NumPy高级索引
## NumPy广播
## NumPy迭代数组
## NumPy数组操作
## NumPy位运算
## NP字符串函数
## NP算术函数
## 统计函数
##













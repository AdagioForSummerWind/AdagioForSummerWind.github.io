---
title: "Py_base"
date: 2022-07-15T14:18:54+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---

# pyhton核心技术与实战

## 开篇词 | 从工程的角度深入理解Python

从内容上来说，专栏主要分为四大版块。

Python 基础篇

第一部分主要讲解 Python 的基础知识。当然，不同于其他基础教材，专栏的基础版块并不只有基础概念、操作，我同时加入了很多进阶难度的知识，或是一些重难点、易错点等需要注意的地方。如果你觉得自己基础的东西都会了，这部分不用学了，那你就大错特错了。比如，列表和元组存储结构的差异是怎样的？它们性能的详细比较又如何？字符串相加的时间复杂度，你真的清楚吗？

基础不牢，地动山摇。更深刻、实质的基础理解，才是更牢固的知识大厦的根基。我希望这一版块，不仅可以让入门级的程序员查漏补缺、打牢基础，也能让有经验的程序员，重新从工程角度认识基础、升华理解。


Python 进阶篇
这部分讲的是 Python 的一些进阶知识，比如装饰器、并发编程等等。如果你的工作只是写 100 行以下的脚本程序，可能不怎么会用得到。但如果你做的是大型程序的开发，则非常有必要。我希望通过这一版块，让你熟悉各种高级用法，真正理解 Python，理解这门编程语言的特点。

Python 规范篇
这部分着重于教你把程序写得更加规范、更加稳定。我在实际工作中见过不少程序员，会写程序，但写得实在有点“惨不忍睹”，导致最后调试起来错误不断，修改非常费劲儿。因此，我觉得用单独一个版块讲解这个问题非常有必要。当然，我不会用一些似是而非的规范来说教，而是会用具体的编程操作和技巧，教你提高代码质量。比如，如何合理地分解代码、运用 assert，如何写单元测试等等。

 Python 实战篇
 没上过战场开过枪的人，不可能做主官；没有实战经验的语言学习者，不可能成为高手。这部分，我会通过量化交易系统这个具体的实战案例，带你综合运用前面所学的 Python 知识。真正要掌握一门编程语言，仅仅学会分散的知识点是不够的，还必须要把知识点串联起来，做一些中型的项目才能有更深的领悟与提高。专栏篇幅只有 40 多篇，但是每篇绝对都是干货满满。我希望这个专栏，能帮助更多入门级和有一定项目基础的程序员，真正掌握 Python，并且给你一些学习上的启发。


## 基础
## 02 | Jupyter Notebook为什么是现代Python的必学技术？

Stack  Overflow 曾在 2017 年底，发布了在该站上各种语言的提问流量。其中，Python 已经超过了 JavaScript 成为了流量最高的语言，预测在 2020 年前会远远甩开 JavaScript。

![img](https://static001.geekbang.org/resource/image/5c/2d/5c3daf49453370c3aa7ddf3bb36cab2d.png?wh=1400*1200)



可能你已经知道，Python 在 14 年后的“崛起”，得益于机器学习和数学统计应用的兴起。那为什么 Python 如此适合数学统计和机器学习呢？作为“老司机”的我可以肯定地告诉你，Jupyter Notebook （https://jupyter.org/）功不可没。毫不夸张地说，根据我对 Facebook 等硅谷一线大厂的了解，一个 Python 工程师如果现在还不会使用 Jupyter Notebook 的话，可能就真的太落伍了。磨刀不误砍柴工，高效的工具让我们的编程事半功倍。这一节课，我就来带你学习一下 Jupyter Notebook，为后面的 Python 学习打下必备基础。




### 什么是 Jupyter Notebook？
说了这么多，到底什么是 Jupyter Notebook？按照 Jupyter 创始人 Fernando Pérez 的说法，他最初的梦想是做一个综合 Ju （Julia）、Py （Python）和 R 三种科学运算语言的计算工具平台，所以将其命名为 Ju-Py-te-R。发展到现在，Jupyter 已经成为一个几乎支持所有语言，能够把软件代码、计算输出、解释文档、多媒体资源整合在一起的多功能科学运算平台。英文里说一图胜千言（A picture is worth a thousand words）。看下面这个图片，你就明白什么是 Jupyter Notebook 了。

![img](https://static001.geekbang.org/resource/image/de/71/dee40d0f591d3f5e2f43839dccc24471.png?wh=1072*994)


你在一个框框中直接输入代码，运行，它立马就在下面给你输出。怎么样，是不是很酷？你可能会纳闷儿，这样一个看起来“华而不实”的玩意儿，真的就成了 Python 社区的颠覆者吗？说实话放在几年前我也是不信的。所以 Jupyter Notebook 的影响究竟有多大呢？


### Jupyter Notebook 的影响力
我们衡量一个技术的影响力，或者说要用自己的技术去影响世界时，必定绕不开这个技术对教育界的影响力。就拿微软的 Word 文本处理系统来说吧。从纯技术角度来讲，Word 的单机设计理念早已落后时代 20 年。但以 Google Doc 为代表的在线文档系统，却并没有像想象中那样，实现对 Word 的降维打击。直观的原因是用户习惯，使用 Word 修改文档，那就来回发几十遍呗，用着也还可以。但更深刻来想，之所以养成这样的用户习惯，是因为我们的教育根源。教育系统从娃娃抓起，用小学中学大学十几年的时间，训练了用户 Word 的使用习惯。到工作中，老员工又会带着新员工继续使用 Word，如此行程技术影响力生生不息的正向反馈。回到我们今天的主题，我们来看 Jupyter Notebook。从 2017 年开始，已有大量的北美顶尖计算机课程，开始完全使用 Jupyter Notebook 作为工具。比如李飞飞的 CS231N《计算机视觉与神经网络》课程，在 16 年时作业还是命令行 Python 的形式，但是 17 年的作业就全部在 Jupyter Notebook 上完成了。再如 UC Berkeley 的《数据科学基础》课程，从 17 年起，所有作业也全部用 Jupyter Notebook 完成。而 Jupyter Notebook 在工业界的影响力更甚。在 Facebook，虽然大规模的后台开发仍然借助于功能齐全的 IDE，但是几乎所有的中小型程序，比如内部的一些线下分析软件，机器学习模块的训练都是借助于 Jupyter Notebook 完成的。据我了解，在别的硅谷一线大厂，例如 Google 的 AI Research 部门 Google Brain，也是清一色地全部使用 Jupyter Notebook，虽然用的是他们自己的改进定制版，叫 Google Colab。看到这里，相信你已经认可了 Jupter Notebook 现如今的江湖地位。不过，说到技术的选择，有些人会说，这个技术流行，我们应该用；有些人认为，阿里已经在用这个技术了，这就是未来，我们也要用等等。不得不说，这些都是片面的认知。不管是阿里还是 Facebook 用的技术，其实不一定适用你的应用场景。我经常会鼓励技术同行，对于技术选择要有独立的思考，不要人云亦云。

### Jupyter 的优点
#### 整合所有的资源
在真正的软件开发中，上下文切换占用了大量的时间。什么意思呢？举个例子你就很好理解了，比如你需要切换窗口去看一些文档，再切换窗口去用另一个工具画图等等。这些都是影响生产效率的因素。正如我前面提到的，Jupyter 通过把所有和软件编写有关的资源全部放在一个地方，解决了这个问题。当你打开一个 Jupyter Notebook 时，就已经可以看到相应的文档、图表、视频和相应的代码。这样，你就不需要切换窗口去找资料，只要看一个文件，就可以获得项目的所有信息。
#### 交互性编程体验
在机器学习和数学统计领域，Python 编程的实验性特别强，经常出现的情况是，一小块代码需要重写 100 遍，比如为了尝试 100 种不同的方法，但别的代码都不想动。这一点和传统的 Python 开发有很大不同。如果是在传统的 Python 开发流程中，每一次实验都要把所有代码重新跑一遍，会花费开发者很多时间。特别是在像 Facebook 这样千万行级别的代码库里，即使整个公司的底层架构已经足够优化，真要重新跑一遍，也需要几分钟的时间。而 Jupyter Notebook 引进了 Cell 的概念，每次实验可以只跑一小个 Cell 里的代码；并且，所见即所得，在代码下面立刻就可以看到结果。这样强的互动性，让 Python 研究员可以专注于问题本身，不被繁杂的工具链所累，不用在命令行直接切换，所有科研工作都能在 Jupyter 上完成。
#### 零成本重现结果
同样在机器学习和数学统计领域，Python 的使用是非常短平快的。常见的场景是，我在论文里看到别人的方法效果很好，可是当我去重现时，却发现需要 pip 重新安装一堆依赖软件。这些准备工作可能会消耗你 80% 的时间，却并不是真正的生产力。Jupyter Notebook 如何解决这个问题呢？其实最初的 Jupyter Notebook 也是挺麻烦的，需要你先在本机上安装 IPython 引擎及其各种依赖软件。不过现在的技术趋势，则是彻底云端化了，例如 Jupyter 官方的 Binder 平台（介绍文档：https://mybinder.readthedocs.io/en/latest/index.html）和 Google 提供的 Google Colab 环境（介绍：https://colab.research.goog


### Jupyter Notebook 初体验
学习技术的最好方法就是用技术。不过，在今天的篇幅里，我不可能带你完全学会 Jupyter Notebook 的所有技巧。我想先带你直接感受一下，使用 Jupyter Notebook 的工作体验。比如这样一个[GitHub 文件](https://github.com/binder-examples/python2_with_3/blob/master/index3.ipynb)。在[Binder](https://mybinder.org/)中，你只要输入其对应的 GitHub Repository 的名字或者 URL，就能在云端打开整个 Repository，选择你需要的[notebook](https://mybinder.org/v2/gh/binder-examples/python2_with_3/master?filepath=index3.ipynb)，你就能看到下图这个界面。

![img](https://static001.geekbang.org/resource/image/f8/c9/f81efe2538074a3385b9ba70aced2cc9.png?wh=1298*994)

每一个 Jupyter 的运行单元都包含了 In、Out 的 Cell。如图所示，你可以使用 Run 按钮，运行单独的一个 Cell。当然，你也可以在此基础上加以修改，或者新建一个 notebook，写成自己想要的程序。赶紧打开链接试一试吧！另外，我还推荐下面这些 Jupyter Notebook，作为你实践的第一站。



第一个是 Jupyter 官方：https://mybinder.org/v2/gh/binder-examples/matplotlib-versions/mpl-v2.0/?filepath=matplotlib_versions_demo.ipynb第二个是 Google Research 提供的 Colab 环境，尤其适合机器学习的实践应用：https://colab.research.google.com/notebooks/basic_features_overview.ipynb如果你想在本地或者远程的机器上安装 Jupyter Notebook，可以参考下面的两个文档。安装：https://jupyter.org/install.html运行：https://jupyter.readthedocs.io/en/latest/running.html#running


## 03 | 列表和元组，到底用哪一个？

### 列表和元组基础

实际上，列表和元组，都是一个可以放置任意数据类型的有序集合。在绝大多数编程语言中，集合的数据类型必须一致。不过，对于 Python 的列表和元组来说，并无此要求：

```
l = [1, 2, 'hello', 'world'] # 列表中同时含有int和string类型的元素
l
[1, 2, 'hello', 'world']

tup = ('jason', 22) # 元组中同时含有int和string类型的元素
tup
('jason', 22)
```
其次，我们必须掌握它们的区别。列表是动态的，长度大小不固定，可以随意地增加、删减或者改变元素（mutable）。而元组是静态的，长度大小固定，无法增加删减或者改变（immutable）。

接下来我们来看一些列表和元组的基本操作和注意事项。首先，和其他语言不同，Python 中的列表和元组都支持负数索引，-1 表示最后一个元素，-2 表示倒数第二个元素，以此类推。

除了基本的初始化，索引外，列表和元组都支持切片操作：

```
l = [1, 2, 3, 4]
l[1:3] # 返回列表中索引从1到2的子列表
[2, 3]

tup = (1, 2, 3, 4)
tup[1:3] # 返回元组中索引从1到2的子元组
(2, 3) 
```
另外，列表和元组都可以随意嵌套：

当然，两者也可以通过 list() 和 tuple() 函数相互转换：

```
list((1, 2, 3))
[1, 2, 3]

tuple([1, 2, 3])
(1, 2, 3)
```
最后，我们来看一些列表和元组常用的内置函数：

```
l = [3, 2, 3, 7, 8, 1]
l.count(3) 
2
l.index(7)
3
l.reverse()
l
[1, 8, 7, 3, 2, 3]
l.sort()
l
[1, 2, 3, 3, 7, 8]

tup = (3, 2, 3, 7, 8, 1)
tup.count(3)
2
tup.index(7)
3
list(reversed(tup))
[1, 8, 7, 3, 2, 3]
sorted(tup)
[1, 2, 3, 3, 7, 8]
```
这里我简单解释一下这几个函数的含义。count(item) 表示统计列表 / 元组中 item 出现的次数。index(item) 表示返回列表 / 元组中 item 第一次出现的索引。list.reverse() 和 list.sort() 分别表示原地倒转列表和排序（注意，元组没有内置的这两个函数)。reversed() 和 sorted() 同样表示对列表 / 元组进行倒转和排序，reversed() 返回一个倒转后的迭代器（上文例子使用 list() 函数再将其转换为列表）；sorted() 返回排好序的新列表。


### 列表和元组存储方式的差异
前面说了，列表和元组最重要的区别就是，列表是动态的、可变的，而元组是静态的、不可变的。这样的差异，势必会影响两者存储方式。我们可以来看下面的例子：

```
l = [1, 2, 3]
l.__sizeof__()
64
tup = (1, 2, 3)
tup.__sizeof__()
48
```
你可以看到，对列表和元组，我们放置了相同的元素，但是元组的存储空间，却比列表要少 16 字节。这是为什么呢？事实上，由于列表是动态的，所以它需要存储指针，来指向对应的元素（上述例子中，对于 int 型，8 字节）。另外，由于列表可变，所以需要额外存储已经分配的长度大小（8 字节），这样才可以实时追踪列表空间的使用情况，当空间不足时，及时分配额外空间。

```
l = []
l.__sizeof__() // 空列表的存储空间为40字节
40
l.append(1)
l.__sizeof__() 
72 // 加入了元素1之后，列表为其分配了可以存储4个元素的空间 (72 - 40)/8 = 4
l.append(2) 
l.__sizeof__()
72 // 由于之前分配了空间，所以加入元素2，列表空间不变
l.append(3)
l.__sizeof__() 
72 // 同上
l.append(4)
l.__sizeof__() 
72 // 同上
l.append(5)
l.__sizeof__() 
104 // 加入元素5之后，列表的空间不足，所以又额外分配了可以存储4个元素的空间
```
上面的例子，大概描述了列表空间分配的过程。我们可以看到，为了减小每次增加 / 删减操作时空间分配的开销，Python 每次分配空间时都会额外多分配一些，这样的机制（over-allocating）保证了其操作的高效性：增加 / 删除的时间复杂度均为 O(1)。但是对于元组，情况就不同了。元组长度大小固定，元素不可变，所以存储空间固定。


### 列表和元组的性能
通过学习列表和元组存储方式的差异，我们可以得出结论：元组要比列表更加轻量级一些，所以总体上来说，元组的性能速度要略优于列表。另外，Python 会在后台，对静态数据做一些资源缓存（resource caching）。通常来说，因为垃圾回收机制的存在，如果一些变量不被使用了，Python 就会回收它们所占用的内存，返还给操作系统，以便其他变量或其他应用使用。但是对于一些静态变量，比如元组，如果它不被使用并且占用空间不大时，Python 会暂时缓存这部分内存。这样，下次我们再创建同样大小的元组时，Python 就可以不用再向操作系统发出请求，去寻找内存，而是可以直接分配之前缓存的内存空间，这样就能大大加快程序的运行速度。下面的例子，是计算初始化一个相同元素的列表和元组分别所需的时间。我们可以看到，元组的初始化速度，要比列表快 5 倍。
```
python3 -m timeit 'x=(1,2,3,4,5,6)'
20000000 loops, best of 5: 9.97 nsec per loop
python3 -m timeit 'x=[1,2,3,4,5,6]'
5000000 loops, best of 5: 50.1 nsec per loop
```
但如果是索引操作的话，两者的速度差别非常小，几乎可以忽略不计。


```
python3 -m timeit -s 'x=[1,2,3,4,5,6]' 'y=x[3]'
10000000 loops, best of 5: 22.2 nsec per loop
python3 -m timeit -s 'x=(1,2,3,4,5,6)' 'y=x[3]'
10000000 loops, best of 5: 21.9 nsec per loop
```
当然，如果你想要增加、删减或者改变元素，那么列表显然更优。原因你现在肯定知道了，那就是对于元组，你必须得通过新建一个元组来完成。

### 列表和元组的使用场景
那么列表和元组到底用哪一个呢？根据上面所说的特性，我们具体情况具体分析。1. 如果存储的数据和数量不变，比如你有一个函数，需要返回的是一个地点的经纬度，然后直接传给前端渲染，那么肯定选用元组更合适。

```
def get_location():
    ..... 
    return (longitude, latitude)
```
如果存储的数据或数量是可变的，比如社交平台上的一个日志功能，是统计一个用户在一周之内看了哪些用户的帖子，那么则用列表更合适。

```
viewer_owner_id_list = [] # 里面的每个元素记录了这个viewer一周内看过的所有owner的id
records = queryDB(viewer_id) # 索引数据库，拿到某个viewer一周内的日志
for record in records:
    viewer_owner_id_list.append(record.id)
```
## 04 | 字典、集合，你真的了解吗？

### 字典和集合基础
那究竟什么是字典，什么是集合呢？字典是一系列由键（key）和值（value）配对组成的元素的集合，在 Python3.7+，字典被确定为有序（注意：在 3.6 中，字典有序是一个 implementation detail，在 3.7 才正式成为语言特性，因此 3.6 中无法 100% 确保其有序性），而 3.6 之前是无序的，其长度大小可变，元素可以任意地删减和改变。相比于列表和元组，字典的性能更优，特别是对于查找、添加和删除操作，字典都能在常数时间复杂度内完成。而集合和字典基本相同，唯一的区别，就是集合没有键和值的配对，是一系列无序的、唯一的元素组合。首先我们来看字典和集合的创建，通常有下面这几种方式：

```
d1 = {'name': 'jason', 'age': 20, 'gender': 'male'}
d2 = dict({'name': 'jason', 'age': 20, 'gender': 'male'})
d3 = dict([('name', 'jason'), ('age', 20), ('gender', 'male')])
d4 = dict(name='jason', age=20, gender='male') 
d1 == d2 == d3 ==d4
True

s1 = {1, 2, 3}
s2 = set([1, 2, 3])
s1 == s2
True
```
这里注意，Python 中字典和集合，无论是键还是值，都可以是混合类型。比如下面这个例子，我创建了一个元素为1，'hello'，5.0的集合：

```
s = {1, 'hello', 5.0}
```
再来看元素访问的问题。字典访问可以直接索引键，如果不存在，就会抛出异常：

```
d = {'name': 'jason', 'age': 20}
d['name']
'jason'
d['location']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'location'
```
也可以使用 get(key, default) 函数来进行索引。如果键不存在，调用 get() 函数可以返回一个默认值。比如下面这个示例，返回了'null'。
```
d = {'name': 'jason', 'age': 20}
d.get('name')
'jason'
d.get('location', 'null')
'null'
```
说完了字典的访问，我们再来看集合。首先我要强调的是，集合并不支持索引操作，因为集合本质上是一个哈希表，和列表不一样。所以，下面这样的操作是错误的，Python 会抛出异常：

```
s = {1, 2, 3}
s[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'set' object does not support indexing
```
想要判断一个元素在不在字典或集合内，我们可以用 value in dict/set 来判断。

```
s = {1, 2, 3}
1 in s
True
10 in s
False

d = {'name': 'jason', 'age': 20}
'name' in d
True
'location' in d
False
```
当然，除了创建和访问，字典和集合也同样支持增加、删除、更新等操作。

```
d = {'name': 'jason', 'age': 20}
d['gender'] = 'male' # 增加元素对'gender': 'male'
d['dob'] = '1999-02-01' # 增加元素对'dob': '1999-02-01'
d
{'name': 'jason', 'age': 20, 'gender': 'male', 'dob': '1999-02-01'}
d['dob'] = '1998-01-01' # 更新键'dob'对应的值 
d.pop('dob') # 删除键为'dob'的元素对
'1998-01-01'
d
{'name': 'jason', 'age': 20, 'gender': 'male'}

s = {1, 2, 3}
s.add(4) # 增加元素4到集合
s
{1, 2, 3, 4}
s.remove(4) # 从集合中删除元素4
s
{1, 2, 3}
```
不过要注意，集合的 pop() 操作是删除集合中最后一个元素，可是集合本身是无序的，你无法知道会删除哪个元素，因此这个操作得谨慎使用。实际应用中，很多情况下，我们需要对字典或集合进行排序，比如，取出值最大的 50 对。对于字典，我们通常会根据键或值，进行升序或降序排序：

```
d = {'b': 1, 'a': 2, 'c': 10}
d_sorted_by_key = sorted(d.items(), key=lambda x: x[0]) # 根据字典键的升序排序
d_sorted_by_value = sorted(d.items(), key=lambda x: x[1]) # 根据字典值的升序排序
d_sorted_by_key
[('a', 2), ('b', 1), ('c', 10)]
d_sorted_by_value
[('b', 1), ('a', 2), ('c', 10)]
```
这里返回了一个列表。列表中的每个元素，是由原字典的键和值组成的元组。而对于集合，其排序和前面讲过的列表、元组很类似，直接调用 sorted(set) 即可，结果会返回一个排好序的列表。
```
s = {3, 4, 2, 1}
sorted(s) # 对集合的元素进行升序排序
[1, 2, 3, 4]
```
### 字典和集合性能
文章开头我就说到了，字典和集合是进行过性能高度优化的数据结构，特别是对于查找、添加和删除操作。那接下来，我们就来看看，它们在具体场景下的性能表现，以及与列表等其他数据结构的对比。比如电商企业的后台，存储了每件产品的 ID、名称和价格。现在的需求是，给定某件商品的 ID，我们要找出其价格。如果我们用列表来存储这些数据结构，并进行查找，相应的代码如下：

```
def find_product_price(products, product_id):
    for id, price in products:
        if id == product_id:
            return price
    return None 
     
products = [
    (143121312, 100), 
    (432314553, 30),
    (32421912367, 150) 
]

print('The price of product 432314553 is {}'.format(find_product_price(products, 432314553)))

# 输出
The price of product 432314553 is 30
```
假设列表有 n 个元素，而查找的过程要遍历列表，那么时间复杂度就为 O(n)。即使我们先对列表进行排序，然后使用二分查找，也会需要 O(logn) 的时间复杂度，更何况，列表的排序还需要 O(nlogn) 的时间。但如果我们用字典来存储这些数据，那么查找就会非常便捷高效，只需 O(1) 的时间复杂度就可以完成。原因也很简单，刚刚提到过的，字典的内部组成是一张哈希表，你可以直接通过键的哈希值，找到其对应的值。

```
products = {
  143121312: 100,
  432314553: 30,
  32421912367: 150
}
print('The price of product 432314553 is {}'.format(products[432314553])) 

# 输出
The price of product 432314553 is 30
```
类似的，现在需求变成，要找出这些商品有多少种不同的价格。我们还用同样的方法来比较一下。如果还是选择使用列表，对应的代码如下，其中，A 和 B 是两层循环。同样假设原始列表有 n 个元素，那么，在最差情况下，需要 O(n^2) 的时间复杂度。

```
# list version
def find_unique_price_using_list(products):
    unique_price_list = []
    for _, price in products: # A
        if price not in unique_price_list: #B
            unique_price_list.append(price)
    return len(unique_price_list)

products = [
    (143121312, 100), 
    (432314553, 30),
    (32421912367, 150),
    (937153201, 30)
]
print('number of unique price is: {}'.format(find_unique_price_using_list(products)))

# 输出
number of unique price is: 3
```
但如果我们选择使用集合这个数据结构，由于集合是高度优化的哈希表，里面元素不能重复，并且其添加和查找操作只需 O(1) 的复杂度，那么，总的时间复杂度就只有 O(n)。


```
# set version
def find_unique_price_using_set(products):
    unique_price_set = set()
    for _, price in products:
        unique_price_set.add(price)
    return len(unique_price_set)        

products = [
    (143121312, 100), 
    (432314553, 30),
    (32421912367, 150),
    (937153201, 30)
]
print('number of unique price is: {}'.format(find_unique_price_using_set(products)))

# 输出
number of unique price is: 3
```
可能你对这些时间复杂度没有直观的认识，我可以举一个实际工作场景中的例子，让你来感受一下。下面的代码，初始化了含有 100,000 个元素的产品，并分别计算了使用列表和集合来统计产品价格数量的运行时间：


```
import time
id = [x for x in range(0, 100000)]
price = [x for x in range(200000, 300000)]
products = list(zip(id, price))

# 计算列表版本的时间
start_using_list = time.perf_counter()
find_unique_price_using_list(products)
end_using_list = time.perf_counter()
print("time elapse using list: {}".format(end_using_list - start_using_list))
## 输出
time elapse using list: 41.61519479751587

# 计算集合版本的时间
start_using_set = time.perf_counter()
find_unique_price_using_set(products)
end_using_set = time.perf_counter()
print("time elapse using set: {}".format(end_using_set - start_using_set))
# 输出
time elapse using set: 0.008238077163696289
```
你可以看到，仅仅十万的数据量，两者的速度差异就如此之大。事实上，大型企业的后台数据往往有上亿乃至十亿数量级，如果使用了不合适的数据结构，就很容易造成服务器的崩溃，不但影响用户体验，并且会给公司带来巨大的财产损失。


### 字典和集合的工作原理
我们通过举例以及与列表的对比，看到了字典和集合操作的高效性。不过，字典和集合为什么能够如此高效，特别是查找、插入和删除操作？这当然和字典、集合内部的数据结构密不可分。不同于其他数据结构，字典和集合的内部结构都是一张哈希表。对于字典而言，这张表存储了哈希值（hash）、键和值这 3 个元素。而对集合来说，区别就是哈希表内没有键和值的配对，只有单一的元素了。我们来看，老版本 Python 的哈希表结构如下所示：

```
--+-------------------------------+
  | 哈希值(hash)  键(key)  值(value)
--+-------------------------------+
0 |    hash0      key0    value0
--+-------------------------------+
1 |    hash1      key1    value1
--+-------------------------------+
2 |    hash2      key2    value2
--+-------------------------------+
. |           ...
__+_______________________________+
```
不难想象，随着哈希表的扩张，它会变得越来越稀疏。举个例子，比如我有这样一个字典：
```
{'name': 'mike', 'dob': '1999-01-01', 'gender': 'male'}
```
那么它会存储为类似下面的形式：

```
entries = [
['--', '--', '--']
[-230273521, 'dob', '1999-01-01'],
['--', '--', '--'],
['--', '--', '--'],
[1231236123, 'name', 'mike'],
['--', '--', '--'],
[9371539127, 'gender', 'male']
]
```
这样的设计结构显然非常浪费存储空间。为了提高存储空间的利用率，现在的哈希表除了字典本身的结构，会把索引和哈希值、键、值单独分开，也就是下面这样新的结构：

```
Indices
----------------------------------------------------
None | index | None | None | index | None | index ...
----------------------------------------------------

Entries
--------------------
hash0   key0  value0
---------------------
hash1   key1  value1
---------------------
hash2   key2  value2
---------------------
        ...
---------------------
```
那么，刚刚的这个例子，在新的哈希表结构下的存储形式，就会变成下面这样：

```
indices = [None, 1, None, None, 0, None, 2]
entries = [
[1231236123, 'name', 'mike'],
[-230273521, 'dob', '1999-01-01'],
[9371539127, 'gender', 'male']
]

```
我们可以很清晰地看到，空间利用率得到很大的提高。清楚了具体的设计结构，我们接着来看这几个操作的工作原理。

### 插入操作
每次向字典或集合插入一个元素时，Python 会首先计算键的哈希值（hash(key)），再和 mask = PyDicMinSize - 1 做与操作，计算这个元素应该插入哈希表的位置 index = hash(key) & mask。如果哈希表中此位置是空的，那么这个元素就会被插入其中。而如果此位置已被占用，Python 便会比较两个元素的哈希值和键是否相等。若两者都相等，则表明这个元素已经存在，如果值不同，则更新值。若两者中有一个不相等，这种情况我们通常称为哈希冲突（hash collision），意思是两个元素的键不相等，但是哈希值相等。这种情况下，Python 便会继续寻找表中空余的位置，直到找到位置为止。值得一提的是，通常来说，遇到这种情况，最简单的方式是线性寻找，即从这个位置开始，挨个往后寻找空位。当然，Python 内部对此进行了优化（这一点无需深入了解，你有兴趣可以查看源码，我就不再赘述），让这个步骤更加高效。
### 查找操作
和前面的插入操作类似，Python 会根据哈希值，找到其应该处于的位置；然后，比较哈希表这个位置中元素的哈希值和键，与需要查找的元素是否相等。如果相等，则直接返回；如果不等，则继续查找，直到找到空位或者抛出异常为止。


### 删除操作
对于删除操作，Python 会暂时对这个位置的元素，赋于一个特殊的值，等到重新调整哈希表的大小时，再将其删除。不难理解，哈希冲突的发生，往往会降低字典和集合操作的速度。因此，为了保证其高效性，字典和集合内的哈希表，通常会保证其至少留有 1/3 的剩余空间。随着元素的不停插入，当剩余空间小于 1/3 时，Python 会重新获取更大的内存空间，扩充哈希表。不过，这种情况下，表内所有的元素位置都会被重新排放。虽然哈希冲突和哈希表大小的调整，都会导致速度减缓，但是这种情况发生的次数极少。所以，平均情况下，这仍能保证插入、查找和删除的时间复杂度为 O(1)。



## 技术见闻与分享

## 41 | 硅谷一线互联网公司的工作体验
前面四个版块，我们一起由浅入深地学习了 Python 这门语言，而最后一个版块，我想与你分享一些我的技术与工作见闻，谈谈我的领悟与理解。首先，我想带你去了解一下，硅谷 Top 互联网公司的工作体验与文化，这里就以我工作的 Facebook 为例。

### 扁平化的管理制度
硅谷的一线互联网公司，都会实行扁平化的管理制度，当然 FB 也不例外。在这里，虽然也有上下级之分，比如  Software Engineer -> Engineering Manager -> Director -> VP，但是我们大家的思想中，并没有严格上下级这样的概念。公司鼓励每个人积极发表自己的观点。比如，一个应届毕业生，因为一个问题和自己的老板，乃至老板的老板据理力争，这样的场景也是很常见的。另外，公司每隔一段时间便会组织一次 Q&A，我们大家可以向 CEO、CTO 等提问。比如你想了解某个产品的发展方向，公司目前的侧重点，甚至是一些敏感的问题，都可以提问。同时，公司的领导，哪怕是上到 CEO、CTO、COO 这样的高层，都没有自己的单独办公室，都是和我们一起坐在开放的区域内办公，这样即拉近了距离，也是为了方便交流和讨论。

### 开放式的讨论平台
第二点是开放式的讨论平台。我一直觉得这个方式非常好，也很喜欢。FB 用的是自己开发的 workplace，相当于一个开放的社区，里面会有不同的群组，无论你有什么问题，都可以去相应的群组提问，那里会有各个领域的高手来帮你解答。举个例子，如果你有 Python 相关的问题，便可以去 Python 的群组问；你如果有 Spark 的问题，就去 Spark 群组问。很多时候，各个组开发的产品，都会涉及很多的跨组合作，要用到其他组开发的一些 API、算法、框架等等。这样，在使用的时候就难免会遇到一些问题，这个时候我们大家通常便会在对应的群组中提问。问题解决后也保存了下来，之后再有人遇到相同的问题时，便能直接搜索到对应的帖子及答案，大大提高了办公的效率。除了上述 Q&A 形式的群组外，我们也会有很多其他形式的群组。比如，自己工作组内的群组，用于发布一些重要消息及技术交流；A/B 测试的群组，用于大家讨论某个实验的结果等等。当然，还有很多非技术的群组，比如足球俱乐部、篮球俱乐部等用于休闲娱乐的平台。在有了这么一个生态系统后，员工可以很方便地获取到自己想要的信息，也大大方便了公司内部员工的交流，可以算是一举多得的事情了。

### 数据驱动为中心
FB 是一个典型的数据驱动型的公司，一切都以数据为依据，这样实际上极大地提高了工程师的地位。比如，在决定一个实验要不要最终发起时，我们都会首先关注各项指标，是不是能带来正向影响，是不是提高了用户的体验等等。再比如，每次提出一个新项目时，我们都需要做大量的数据分析与调研，然后与组内的同事及上级领导  review 后再做决定。这样，每次绩效考核时，证明自己最好的依据，便是自己发起的实验对指标的提升等等。这样的一种策略，对于公司及个人的发展都更为有利。举一个反例，之前的 Snapchat，就是一个典型的不以数据驱动为中心的公司。他们产品的发布、改变，大多依赖一些产品经理和设计师的主观臆断，这样实际上是很偏颇的。后来的结局我们也都知道了，产品变得越来越不受用户喜欢，股价大跌，而我大部分在那里工作的同学，也都纷纷离职了。

### Bootcamp
Bootcamp 是 FB 中很著名的一个项目，所有入职 FB 的员工，在正式进入具体的工作组之前，都会参加 4-10 周的 Bootcamp；而每个员工也会分配一个导师，帮助其了解 FB 的技术栈、文化以及吃喝玩乐等等。Bootcamp 的前两周，通常会安排不少的课程，帮助新员工了解 FB 的内部工具。之后就会进入选组阶段，组和员工之间进行双向选择，形式通常是“聊天 + 做组内的项目”，这样双方都能对彼此有更深入的了解。在 Bootcamp 期间，特别是对于应届毕业生来说，你可以尝试各种不同的方向，这对于未来的职业发展是非常有裨益的。公司也鼓励 Bootcamp 的员工参加各种娱乐活动，增进交流，而且这期间的吃喝玩乐都可以报销。我身边的每个同事都会有这样的感受：Bootcamp 真是在公司最舒服的日子了。


### 鼓励工程师更换工作方向
在 FB，无论是内部换组，还是更换工作方向都是非常普遍的现象。很多工程师在一个组做的时间久了，就会想尝试一些新的方向，这在公司是非常鼓励的。方法也很简单，一般来说让你去新组做几个任务，或者花一个月的时间做一个 Hackamonth 就可以了。这种形式是对双方的考量，新组会对工程师的能力有一个大概的了解；而工程师也会对新组的工作、技术有所掌握，并进一步判断自己是否感兴趣。因此，在 FB，你会看到很多全栈工程师，比如我就是其中一个，对移动端、服务器端以及机器学习都有所涉猎。显然，这样的制度，非常有利于工程师的全面发展。



### 福利政策
FB 的福利，应该可以算是全球互联网公司中最好之一了。公司为了留住人才，提供了很多外人看来非比寻常的福利。首先从工位说起，其装备都是业内顶级标准。电脑是可以自己随意选配的，比如你可以随意选配 7000 多美金的 iMac Pro，显示器也可以随意选配价值 1000 多美金的 4K 屏幕。至于可升降桌子和椅子，都是 Herman Miller 标配，桌椅总价在 2000 美金以上。在技术交流方面，除了正常的学习培训外，公司还鼓励员工每年外出参加一次会议，比如机器学习方向的 ICML、KDD 等等，给予全程报销。另外，公司包一日三餐，包括内部的零食、甜品等全部免费。我们拥有一年 21 天带薪休假，女性还拥有 6 个月的带薪产假，同时提供免费的健身房、游泳池等，每年还会提供 720 美金的健身私教报销等等。

### 写在最后
以上就是我在 FB 工作的主要工作体验。其实，在硅谷工作，不仅仅有技术上的收获，比如你可以直接接触到业内顶级大牛，了解到最新最前沿的技术；还有很多认知和思维方式上的影响，比如对于流程、合作、开源等的思考。

## 42 | 细数技术研发的注意事项

技术研发一直以来都是各大公司的核心部分之一，其质量的好坏直接影响到了产品的质量以及用户对产品的体验。如何建立一套规范、健全的开发体系，就显得尤为重要。今天我就和你聊聊技术研发的注意事项。

### 选择合适的编程语言
比如我们正在开发一个系统，首先，根据具体的需求，我们需要对系统的各个部分选择合适的编程语言。一般来说，infra 这层我们更偏向于使用 C++，而纯的服务器端则是以 Python、Java、PHP 等等为主。以搜索引擎为例，下面我画了一个它的简略架构图：

![img](https://static001.geekbang.org/resource/image/72/d5/72caf6b3be8758651e6071bd49cb24d5.png?wh=1418*590)

你可以看到，大概的工作流程是：用户在客户端（client）输入一个查询（query），发送请求（request）到达服务器端（server-side）；服务器端首先向 NLP service 发请求，并对请求进行分析，等到拿到各项信号（signal）后，再向后端（backend）发送请求；后端会做特征抽取（feature extraction），利用 ML 模型进行结果的检索（candidate retrieval）、排序，最后再把结果返回给服务器端和客户端。这里的 NLP Service 和后端，我们都会使用 C++。因为这部分的处理最为复杂和耗时，都涉及到了特征抽取和 model serving，对延迟（latency）的要求极高，只有 C/C++ 这类语言才能满足需求。而服务器端或者叫中间层（middle tier），我们则会使用 Python、Java、PHP 等语言。因为这部分并没有特别复杂的处理和对延迟的高需求，主要是以一些业务逻辑为主；并且，对程序员来说，使用这种高级语言也更容易上手和调试。


### 合理使用缓存
缓存（cache）在实际工程中十分重要，可以想像，如果没了缓存，我们今天所用的绝大多数产品估计都会崩溃。缓存为我们节约了大量的 CPU 容量（capacity）和延迟。还是以刚刚的搜索引擎系统为例，我们在客户端、服务器端和后端都会设置缓存。在客户端，我们一般会缓存用户的搜索记录，比如当你点击搜索框时，自动弹出的建议关键词的前几个，就可以是缓存的结果。这不需要向服务器端发请求，可以直接从客户端下载。而在服务器端，我们也会设置缓存来存储一些搜索结果。这样，如果同一个用户多次发送相同的请求，就不需要再向后端请求，直接从缓存里面拿结果就可以了，快速又高效。同样的，后端也需要缓存。比如在 model serving 这块儿，我们通常会有几个小时的缓存，不然每次提供实时的在线服务时，对 CPU 的负荷很大，延迟也会很高。总而言之，如果没了缓存，容易造成很多问题。

服务器负荷迅速飙升，崩溃的几率大大增加。端对端的延迟迅速飙升，请求超时的概率大大增加。


但是不是缓存越多就越好呢？显然也不是。第一，通常来说，缓存比较昂贵，所以在使用上，我们都会有一个限度，不能无限制索取。第二，缓存不是万能的，过度增加缓存，也会损害用户的产品体验。比如搜索结果的 retrieval 和排序这两块，理想状况下，肯定是做实时的 model serving 最好，因为这样对用户的个性化推荐更准确和实时。之所以会对 model 有几个小时的缓存，更多的是出于性能的考虑，但如果把缓存从几小时改为几天，显然不合适，无疑会对用户的产品体验造成极大的负面影响。

因此，缓存到底取多久、取多少，往往是用户对产品参与度和性能的一个权衡，需要根据一些具体的分析以及 A/B 测试做出决定。


### 健全的日志记录系统
健全的日志记录系统也是尤其关键的一点。大型公司的系统，往往由成千十万个小系统组合而来，如果发生故障，比如 Google、Facebook 的某项服务突然宕机了，我们就需要以最快的速度找出原因并做出修复。这靠的是什么呢？靠的正是健全的日志记录系统，使得我们能够方便地分解错误原因，一层一层追溯，直到找到根源。一般来说，在线上环境中，我们需要两种类型的日志记录模式。一种是实时 logging，考虑到服务器的压力，通常会做降采样（downsampling），比如 log 实际流量的 1%。这样的好处是，可以及时跟踪各项指标，如果有情况，立即触发警报（alert）。比如，某天的中午 12 点，一位工程师 push 了一段会造成服务器奔溃的代码进入产品，实时 logging 检测到异常，发出警报，这时有关人员便会进行排查。如果发现这个代码的 push 时间和警报触发时间一致，就能够最快地恢复（revert），最小化其带来的负面影响。同时，实时 logging 也有利于我们进行各种线上实验。比如，ML 组的 A/B 测试常常需要调参，我们的通常做法，就是每隔几小时查看实时 logging 的 table，根据各项指标，适度调整参数。第二种是每天更新一次也就是 daily 的 full logging，有助于我们统计一些信息，进行分析，比如做成仪表板（dashboard），方便查看每天的各项指标，来跟踪进度。此外，full logging 的 table，也常常用于 ML 组的训练数据（training data）。

### Profiling 必不可少
关于 profile，之前我们也提到过，在实际开发中是非常重要的一项功能，能够帮助开发人员详细了解系统每个部分的效率，并加以提高。在线上环境中，我们通常会在许多必要的地方加上 profile 的代码，这样我们就能够知道这段代码的延迟是多少，哪个部分的延迟特别严重等等，然后对症下药。如果没有 profile，很容易导致开发人员随意增加功能而不进行优化，这样以来，随着时间的推移，系统越来越冗余，延迟也会越来越高。因此，一个成熟的系统，一定会有 profile 的代码，帮助开发人员随时监控内部的各项指标变化。



### test、test、test
这一点，我也已经在前面的文章中强调过了，测试（test）一定不能少。无论是单元测试（unit test）、集成测试（integration test）还是其他，都是保证代码质量、减小 bug 发生概率的一个有效手段。在真正规范的公司或是小组里，开发人员如果新增或改变了一个功能而不写测试，是过不了代码评审的。因此，测试一定要写，尤其是系统复杂了以后，很多工程师都要在上面开发各种不同的新功能，很难保证各个部分不受影响，测试便是一种很好的解决方法。除了日常开发中所写的测试外，在代码 push 到线上之前，最好还要加一层测试。还是以刚刚的搜索引擎系统为例，我所知道的，Google 或者 Facebook 的代码在 push 的过程中，都会有专门的 service，去模拟不同的用户发送请求，然后看返回的响应是不是符合要求。如果出错，就会阻止代码的 push，这也就告诉了开发人员，他们所写的代码可能存在问题，需要再次检查。


## 43 | Q&A：聊一聊职业发展和选择
2019-08-19

Q：程序员的岗位主要有哪些类型？我该如何选择？

A：无论是在求职阶段，还是正式进入公司工作后，你都会发现，工程师普遍按技术的不同，分为下面几个岗位。

前端：包括移动（Android、iOS）以及 Web 前端（JavaScript、CSS）开发。后端（服务器端）：主要是服务器端的开发，简单来说，就是输入为请求，输出为响应，发送给客户端。算法：主要涉及到的是机器学习，比如推荐系统如何更好地实现个性化推荐，搜索引擎返回的结果如何才能更符合地用户的需求等等。架构：涉及系统架构，偏底层，语言以 C++ 为主。

从薪酬的角度来看，普遍来说：算法 > 架构 > 后端 > 前端。当然，这主要是由市场的供需关系决定的。就拿算法岗来说，国内市场普遍缺少算法人才，也是因为这个岗位的培养难度更大，需要投入更大的精力。在顶尖互联网公司，参与核心产品研发的算法工程师们，工作三年，年收入 100-200W 人民币是很常见的。不过，我这里所说的算法人才，绝不是指类似在校生那种，看过几篇论文，写过一些 MATLAB，在学校做过几个科研项目的程度。算法工作岗位需要的算法能力，是你必须身体力行，有某些产品线的实践经历。还需要你真正了解市场，比如今日头条的推荐算法是怎样的，Google 搜索引擎是怎么工作的，头条里的广告排序又是怎么做的等等。

再来说说架构，这也是目前一个热门的方向。我一直认为这是一个很偏工程、很硬核的领域，发展前景也相当不错，可以说是一个产品的基石。就拿刚刚提到的推荐系统来说，广告的定位和排序系统背后，都需要强有力的架构支撑。因此，这一行也可以称得上是人才紧缺，是企业舍得花高薪聘请的对象之一。与算法不同的是，这个领域不会涉及很深的数学知识，工程师的主要关注点，在于如何提高系统性能，包括如何使系统高扩展、减小系统的延迟和所需 CPU 的容量等等。架构师需要很强的编程能力，常用的语言是 C++；当然，最重要的还是不断积累大型项目中获得的第一手经验，对常见的问题有最 principle 的处理方式。

最后说说后端和前端，这是绝大多数程序员从事的岗位，也是我刚进公司时的选择。也许比起前两个岗位，不少人会认为，后端、前端工程师的薪酬较低，没有什么发展前景。这其实大错特错了！从一个产品的角度出发，你可以没有算法工程师、没有架构师，但是你能缺少后端和前端的开发人员吗？显然是不可能的。

后端和前端，相当于是一个产品的框架。框架搭好了，才会有机器学习、算法等的锦上添花。诚然，这两年来看，后端和前端没有前两者那么热门（还是市场供需关系的问题），但这并不代表，这些岗位没有发展前景，或者你就可以小看其技术含量。比起算法和架构，后端、前端确实门槛更低些，但是其工作依然存在很高的技术含量。比如对一个产品或者其中的某些部件来说，如何设计搭建前后端的开发框架结构，使系统更加合理、可维护性更高，就是很多资深的开发工程师正在做的事。

**另外，从广义的角度来看，计算机这门技术存在着 study deep 和 study broad 这两个方向，你得想清楚你属于哪类。所谓的 study deep，就意味着数十年专攻一个领域，励志成为某个领域的专家；而 study broad，便是类似于全栈工程师，对一个产品、系统的 end to end 都有一个了解，能够随时胜任任意角色的工作，这一点在初创公司身上体现得最为明显。**


































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































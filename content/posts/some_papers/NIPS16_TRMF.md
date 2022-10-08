---
title: "NIPS16_TRMF"
date: 2022-05-13T09:33:30+08:00
lastmod: 2022-05-14
tags: [papers]
categories: [Graduation project]
slug: TRMF for High-dimensional Time Series Prediction
draft: false
---
> Hsiang-Fu Yu, Nikhil Rao, Inderjit S. Dhillon, 2016. Temporal regularized matrix factorization for high-dimensional time series prediction.   

> 笔记中部分公式未渲染出来，文末截图可见

# Temporal Regularized Matrix Factorization(TRMF) for High-dimensional Time Series Prediction
## 摘要
现代应用程序需要具有高度可扩展性的方法，并且可以处理有噪声的或有缺失值的数据。
本文提出了一个支持数据驱动的时态学习和预测的时态正则化矩阵分解（TRMF）框架。我们在学习自回归模型中的依赖关系的背景下，与图正则化方法建立了有趣的联系框架。
实验结果表明：TRMF在维数为50000的问题上比其他方法快两个数量级，并能对现实世界的数据集（如沃尔玛电子商务数据集）生成更好的预测。
## 1. 介绍

现代时间序列应用程序给从业者带来了两个挑战：处理大n（数字）和T（时间帧）的可伸缩性，以及处理缺失值的灵活性。

AR和DLM侧重于低维时间序列数据，而没有处理上述两个问题。

对高维时间序列数据建模的一种自然方式是以矩阵的形式，行对应于每个一维时间序列，列对应于时间点。

鉴于n个时间序列通常高度相关，有人尝试应用低秩矩阵分解（MF）或矩阵完成（MC）技术来分析高维时间序列[2,14,16,23,26]。与上面的AR和DLM模型不同，最先进的MF方法以n为单位线性扩展，因此可以处理大型数据集。

在MF中，观测到的n维时间序列数据被组织在矩阵 $\mathcal{Y} \in \mathbb{R}^{n \times T}$ 中，矩阵 y 由维度特性矩阵 $W \in \mathbb{R}^{n \times r}$ 与时间特性矩阵 $X \in \mathbb{R}^{r \times T}$ 的组合进行低秩逼近，从而修补缺失数据。
$$
\mathcal{Y} \approx W X
$$

使用最小二乘法、梯度下降等方法求解下述最小化问题，从而对矩阵 W 与矩阵 X 进行逼近：
$$
\min _{W X} \sum_{(i, t) \in \Omega}\left(\mathcal{Y}_{i t}-w_{i}^{T} x_{t}\right)^{2}+\lambda_{w} \mathcal{R}_{w}(W)+\lambda_{x} \mathcal{R}_{x}(X)
$$

其中， $\Omega$ 是原矩阵 $\mathcal{Y} \in \mathbb{R}^{n \times T}$ 中非零元所处位置的集合； $\sum_{(i, t) \in \Omega}\left(\mathcal{Y}_{i t}-w_{i}^{T} x_{t}\right)^{2}$ 为残差矩阵F范数的平方，用来描述 W X 矩阵 与原矩阵 Y 的差异；$R_w$ 与 $R_x$ 分别是 W 与 X 的正则项，用来防止过拟合。
$$
\mathcal{R}_{w}(W)=\|W\|_{F}^{2}=\sum_{i=1}^{n}\left\|\boldsymbol{w}_{i}\right\|^{2}=\sum_{i=1}^{n} w_{i}^{T} w_{i}
$$

$$
\mathcal{R}_{x}(X)=\|X\|_{F}^{2}=\sum_{t=1}^{T}\left\|\boldsymbol{x}_{t}\right\|^{2}=\sum_{t=1}^{T} x_{t}^{T} x_{t}
$$



大多数现有的MF方法采用基于图（这个图指的是同一个特征的时序关系拼接成图，也就是X矩阵的一行）的方法来处理时间依赖性。具体来说，依赖关系由加权相似图描述，并通过拉普拉斯正则项进行约束。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220513145708.png)
图1：多重时间序列的矩阵分解模型。F捕捉矩阵Y中每个时间序列的特征，X捕捉潜在和时变变量

MF方法可以对缺失数据进行修复，但是对于预测问题则无能为力。此外，由于MF方法并没有考虑数据的时序特性，对上述的交通与天气数据的修复效果并不理想。

本文提出了一个新的时间正则化矩阵分解框架(TRMF)用于高维时间序列分析。

在TRMF中，我们考虑了一种原则性的方法来描述潜在时间嵌入之间的时间依赖性结构{$x_t$}，并设计了一个时间正则化器来将这种时间依赖性结构纳入标准MF公式。

与大多数现有的MF方法不同，我们的TRMF方法支持数据驱动的时间依赖性学习，并为矩阵分解方法带来预测未来值的能力。此外，TRMF方法继承了MF方法的属性，即使在存在许多缺失值的情况下，TRMF也可以轻松处理高维时间序列数据。

作为一个具体的例子，我们展示了一种新的自回归时间正则化器，它鼓励时间嵌入{$x_t$}之间的AR（autoregressive）结构。

我们还将提出的正则化框架与基于图的方法联系起来，其中甚至可以解释负相关。

这种连接不仅有助于更好地理解我们的框架所包含的依赖结构，而且还有助于使用现成的高效求解器(如GRALS)直接求解TRMF。

## 2. 具有时间依赖性的数据的现有矩阵分解方法
标准MF公式对列的排列保持不变（列不管怎么变，权重矩阵保持不变），这不适用于具有时间依赖性的数据。

因此，对于时间依赖性{$x_t$}，大多数现有的时间MF方法都转向基于图的正则化框架，并用图编码时间依赖性。

### 时间依赖性的图正则化

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220513152011.png)

图2：时态依赖的基于图的正则化。

令G是一个时间依赖性{$x_t$}的图，$G_{ts}$是第t个点和第s个点之间的边权重。一种常见的正则化方式如下公式：
$$
\mathcal{R}_{x}(X)=\mathcal{G}(X \mid G, \eta):=\frac{1}{2} \sum_{t \sim s} G_{t s}\left\|\boldsymbol{x}_{t}-\boldsymbol{x}_{s}\right\|^{2}+\frac{\eta}{2} \sum_{t}\left\|\boldsymbol{x}_{t}\right\|^{2}(2)
$$

其中t~s代表了第t个点和第s个点之间的边；第二个正则化项是用来保证强凸性 

一个很大的$G_{ts}$可以保证$x_t$和$x_s$在欧几里得距离上很接近

为了保证$\mathcal{G}(X \mid G, \eta)$的凸性，我们让$G_{ts}$≥0 

为了将基于图的正则化应用于时间依赖关系上，我们需要通过滞后集L和权值向量w重复地指定各个点之间的依赖模式，以便距离L的所有边t ~ s共享相同的权值

于是上面的公式可以改写成：
$$
\mathcal{G}(X \mid G, \eta)=\frac{1}{2} \sum_{l \in \mathcal{L}} \sum_{t: t>l} w_{l}\left(\boldsymbol{x}_{t}-\boldsymbol{x}_{t-l}\right)^{2}+\frac{\eta}{2} \sum_{t}\left\|\boldsymbol{x}_{t}\right\|^{2}(3)
$$
这种直接使用基于图的方法虽然很直观，但有两个问题:
- 两个时间点之间可能存在负相关依赖关系;
- 显式的时态依赖结构通常不可用，必须使用者进行推断。

于是，很多现有的这种正则化的模型只能考虑很简单的时间依赖关系（比如滞后集L很小，L={1}），和统一的权重（比如不管两个点之间距离是多少，权重统一设置为1）

这导致现有MF方法对大规模时间序列的预测能力较差。
### 学习时间依赖性的挑战

也许有人会想：那我权重参数w让机器自己学不就好了吗？

在这种假设下，我们有了以下的优化方程：
$$
\min _{F, X, \boldsymbol{w} \geq \mathbf{0}} \sum_{(i, t) \in \Omega}\left(Y_{i t}-\boldsymbol{f}_{i}^{\top} \boldsymbol{x}_{t}\right)^{2}+\lambda_{f} \mathcal{R}_{f}(F)+\frac{\lambda_{x}}{2} \sum_{l \in \mathcal{L}} \sum_{t: t-l>0} w_{l}\left(\boldsymbol{x}_{t}-\boldsymbol{x}_{t-l}\right)^{2}+\frac{\lambda_{x} \eta}{2} \sum_{t}\left\|\boldsymbol{x}_{t}\right\|^{2}
(4)$$

我们不难发现，**最终的优化结果，是所有的w都是0**，意为没有空间依赖关系的时候，目标函数达到最小值。

为了避免让所有的w都是0，有人想到可以给w的和加上一个限制，比如$\sum_{l \in \mathcal{L}} w_{l}=1$ 

同样地，我们不难发现，最终的优化结果是$l^{*}=\arg \min _{l \in \mathcal{L}} \sum_{t: t>l}\left\|\boldsymbol{x}_{t}-\boldsymbol{x}_{t-l}\right\|^{2}$，对应的wl*是1，其他的w是0

因此，通过简单地在MF公式中插入正则化器来自动学习权重并不是一个可行的选择。

## 3. TRMF
为了解决前面提到的限制，本文提出了时间正则化矩阵分解(TRMF)框架，这是一种将时间依赖性纳入矩阵分解模型的新方法。

与前面提到的基于图的方法不同，我们建议使用经过充分研究的时间序列模型来明确地描述{$x_t$}之间的时间依赖性。

$$\boldsymbol{x}_{t}=M_{\Theta}\left(\left\{\boldsymbol{x}_{t-l}: l \in \mathcal{L}\right\}\right)+\boldsymbol{\epsilon}_{t}   (5)$$

- $\boldsymbol{\epsilon}_{t}$是一个高斯噪声向量
- $M_{\Theta}$是一个时间序列模型，参数是Θ和滞后集L
- L是一个包含滞后指标L的集合，表示t和t-l时间点之间的相关性
- Θ捕捉时间相关性的权重信息(如AR模型中的转移矩阵)

基于此，我们提出了一个新的正则化项$\mathcal{T}_{\mathrm{M}}(X \mid \Theta)$，这可以鼓励模型依照时间序列$M_{\Theta}$。

我们令：
$$
\mathcal{T}_{\mathrm{M}}(X \mid \Theta)=-\log \mathbb{P}\left(\boldsymbol{x}_{1}, \ldots, \boldsymbol{x}_{T} \mid \Theta\right)   (6)
$$

当θ给定的时候，我们令$\mathcal{T}_{\mathrm{M}}(X \mid \Theta)$为矩阵分解的一个正则化项；当θ未知的时候，我们令θ为另外一部分参数，并且设计$R_θ$以作为另一个正则化项。

$$
\min _{F, X, \Theta} \sum_{(i, t) \in \Omega}\left(Y_{i t}-\boldsymbol{f}_{i}^{\top} \boldsymbol{x}_{t}\right)^{2}+\lambda_{f} \mathcal{R}_{f}(F)+\lambda_{x} \mathcal{T}_{\mathrm{M}}(X \mid \Theta)+\lambda_{\theta} \mathcal{R}_{\theta}(\Theta)   (7)
$$

通过交替地优化更新F,X,Θ，可以解决上面的优化方程。
### TRMF中数据驱动的时间依赖性学习
在TRMF中，当F和X是固定的时候，式（7）可以简化为：
$$
\min _{\Theta} \lambda_{x} \mathcal{T}_{M}(X \mid \Theta)+\lambda_{\theta} \mathcal{R}_{\theta}(\Theta)
(8)$$
其中第一项可以看成：$\min _{\Theta} -\log{P}(X_1,...X_T \mid \Theta)$，即$\max _{\Theta}{P}(X_1,...X_T \mid \Theta)$

也就是说，后一项可以看成最大后验概率

### TRMF时间序列分析
我们可以看到，TRMF可以无缝地处理在分析具有时间依赖性的数据时经常遇到的各种任务:
- 时间序列预测
    - 一旦我们有了潜在的嵌入{$x_t:1,...T$}的$M_{\Theta}$，我们可以预测未来的嵌入{$x_t:t>T$}，然后使用来预测结果
- 缺失值补全
    - 我们可以使用$\boldsymbol{f}_{i}^{\top} \boldsymbol{x}_{t}$来对这些缺失的条目进行插补，就像标准矩阵补全，在推荐系统和传感器网络中很有用。
## 4. 一种新的自回归时间正则化算法
在小节3中，我们大致介绍了TRMF的框架：正则项$\mathcal{T}_{M}(X \mid \Theta)$（有时间序列模型$M_{\Theta}$确定）

在这一小节中，我们将介绍一种TRMF框架：自回归模型，参数为滞后集L和权重

我们令xt是以下形式:
$$
\boldsymbol{x}_{t}=\sum_{l \in \mathcal{L}} W^{(l)} \boldsymbol{x}_{t-l}+\boldsymbol{\epsilon}_{t}
$$

$\boldsymbol{\epsilon}_{t}$是一个高斯噪声向量。

为了简化，假设$\boldsymbol{\epsilon}_{t} \sim \mathcal{N}\left(0, \sigma^{2} I_{k}\right)$

于是，时间正则化项$\mathcal{T}_{M}(X \mid \Theta)$可以写成：

$$
\mathcal{T}_{\mathrm{AR}}(X \mid \mathcal{L}, \mathcal{W}, \eta):=\frac{1}{2} \sum_{t=m}^{T}\left\|\boldsymbol{x}_{t}-\sum_{l \in \mathcal{L}} W^{(l)} \boldsymbol{x}_{t-l}\right\|^{2}+\frac{\eta}{2} \sum_{t}\left\|\boldsymbol{x}_{t}\right\|^{2}
(9)$$

其中：$m := 1 + L, L := max(L)$, and $\eta > 0$

由于每个$W^{(l)} \in R^{k*k}$所以我们有$|\mathcal{L}| k^{2}$个参数要学习，这可能导致过拟合

为了避免过拟合，同时为了生成更可解释的结果，我们人为定义$W^{(l)} \in R^{k*k}$为对角矩阵，这可以使得参数量减少至$|\mathcal{L}| k$

出于简化的考虑，我们使用W来表示这个k×L的矩阵，其中第l列表示$W^{(l)} \in R^{k*k}$ 的对角线元素

简化后：
$$
\mathcal{T}_{\mathrm{AR}}(\overline{\boldsymbol{x}} \mid \mathcal{L}, \overline{\boldsymbol{w}}, \eta)=\frac{1}{2} \sum_{t=m}^{T}\left(x_{t}-\sum_{l \in \mathcal{L}} w_{l} x_{t-l}\right)^{2}+\frac{\eta}{2}\|\overline{\boldsymbol{x}}\|^{2}
(10)$$

$x_t$表示时刻t的向量。

将式10代入式7，得到式12：
$$
\min _{F, X, \mathcal{W}} \sum_{(i, t) \in \Omega}\left(Y_{i t}-\boldsymbol{f}_{i}^{\top} \boldsymbol{x}_{t}\right)^{2}+\lambda_{f} \mathcal{R}_{f}(F)+\sum_{r=1}^{k} \lambda_{x} \mathcal{T}_{\mathrm{AR}}\left(\overline{\boldsymbol{x}}_{r} \mid \mathcal{L}, \overline{\boldsymbol{w}}_{r}, \eta\right)+\lambda_{w} \mathcal{R}_{w}(\mathcal{W})
(12)$$

我们将式（12）命名为TRMF-AR.
### 不同时间序列之间的关联性
尽管$W^{(l)} \in R^{k*k}$是对角阵，但是TRMF还是可以建模不同时间序列（X矩阵不同行之间）的关联性。这个关联性在特征矩阵F中体现。
### 滞后集L的选择
TRMF中L的选择更加灵活。因此，TRMF可以提供重要的优势:

首先，因为不需要指定权重参数W，可以选择更大的L来考虑长期依赖性，这也可以产生更准确和稳健的预测。

其次，L中的时延不需要是连续的，这样就可以很容易地嵌入关于周期性或季节性的领域知识。例如，对于具有一年季节性的每周数据，可以考虑L ={1, 2, 3, 51, 52, 53}。

### 参数优化
式10和式12
## 5. 实验结果
### 数据集
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220513164853.png)

对于synthetic数据集，我们先随机生成一个$F \in R^{16*4}$，，生成{$x_t$}，它满足AR过程，且滞后集L={1,8}。然后Y通过$\boldsymbol{y}_{t}=F \boldsymbol{x}_{t}+\boldsymbol{\epsilon}_{t}$ 且 $\boldsymbol{\epsilon}_{t} \sim \mathcal{N}(\mathbf{0}, 0.1 I)$生成

电力和交通数据集从UCI存储库获得，而Walmart -1和Walmart -2是来自Walmart电子商务的两个专有数据集，其中包含每周的销售信息。由于缺货等原因，missing rate分别为55.3%和49.3%。为了评价预测性能，我们考虑了归一化偏差(ND)和归一化均方根(NRMSE)。

### 实验结果
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220513165317.png)
表3：缺失值插补结果：每种方法的ND/NRMSE。请**注意**，TRMF
在几乎所有情况下都优于所有竞争方法

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20220515165114.png)
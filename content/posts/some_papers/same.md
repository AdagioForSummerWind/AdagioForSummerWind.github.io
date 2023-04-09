---
title: "NIPS16_TRMF"
date: 2022-05-13T09:33:30+08:00
lastmod: 2022-05-14
tags: [papers]
categories: [Graduation project]
slug: TRMF for High-dimensional Time Series Prediction
draft: true
---

> FILLING THE G AP S: MULTIVARIATE TIME SERIES IMPUTATION BY GRAPH NEURAL NETWORKS

## 摘要
Dealing with missing values and incomplete time series is a labor-intensive, tedious, inevitable task when handling data coming from real-world applications.
Effective spatio-temporal representations would allow imputation methods to reconstruct missing temporal data by exploiting information coming from sensors at different locations. 
However, standard methods fall short in capturing the nonlinear time and space dependencies existing within networks of interconnected sensors and do not take full advantage of the available – and often strong – relational information. 
Notably, most state-of-the-art imputation methods based on deeplearning do not explicitly model relational aspects and, in any case, do not exploit processing frameworks able to adequately represent structured spatio-temporal data. 
Conversely, graph neural networks have recently surged in popularity as both expressive and scalable tools for processing sequential data with relational inductive biases. 
In this work, we present the first assessment of graph neural networks in the context of multivariate time series imputation. 
In particular, we introduce a novel graph neural network architecture, named GRIN, which aims at reconstructing missing data in the different channels of a multivariate time series by learning spatio-temporal representations through message passing. 
Empirical results show that our model outperforms state-of-the-art methods in the imputation task on relevant real-world benchmarks with mean absolute error improvements often higher than 20%
在处理来自实际应用程序的数据时，处理缺失值和不完整的时间序列是一项劳动密集型、乏味且不可避免的任务。
有效的时空表示将允许插补方法通过利用来自不同位置的传感器的信息来重建丢失的时间数据。
然而，标准方法在捕捉互连传感器网络中存在的非线性时间和空间依赖性方面存在不足，并且没有充分利用可用的（通常是强大的）关系信息。
值得注意的是，大多数基于深度学习的最先进的插补方法都没有明确地对关系方面进行建模，并且在任何情况下都没有利用能够充分表示结构化时空数据的处理框架。
相反，图神经网络最近作为用于处理具有关系归纳偏差的序列数据的表达性和可扩展性工具而大受欢迎。
在这项工作中，我们在多元时间序列插补的背景下首次评估了图神经网络。
特别是，我们引入了一种名为 GRIN 的新型图神经网络架构，旨在通过消息传递学习时空表示来重建多元时间序列的不同通道中的缺失数据。
实证结果表明，我们的模型在相关真实世界基准的插补任务中优于最先进的方法，平均绝对误差改进通常高于 20%

当处理来自现实应用的数据时，处理缺失值和不完整的时间序列是一项费时、繁琐、不可避免的任务。有效的时空表示可以利用来自不同位置传感器的信息，通过插值方法重建缺失的时间数据。然而，标准方法难以捕捉到相互连接的传感器网络中存在的非线性时空依赖关系，并且不能充分利用可用的-并且通常很强-关系信息。值得注意的是，大多数基于深度学习的最先进插值方法并没有明确地建模关系方面的问题，并且在任何情况下都不能利用能够充分表示结构化时空数据的处理框架。相反，图神经网络最近因为具有表达力和可扩展性而在处理具有关系归纳偏差的序列数据方面变得流行。在这项工作中，我们提出了图神经网络在多元时间序列插值中的第一次评估。特别地，我们引入了一种名为GRIN的新型图神经网络架构，它旨在通过消息传递学习时空表示，从而重建多元时间序列中不同通道的缺失数据。实证结果表明，我们的模型在相关的实际基准测试中的插值任务中优于最先进的方法，平均绝对误差改进通常高于20%。
## 介绍
Imputation of missing values is a prominent problem in multivariate time-series analysis (TSA) from both theoretical and practical perspectives (Little & Rubin, 2019).
In fact, in a world of complex interconnected systems such as those characterizing sensor networks or the Internet of Things, faulty sensors and network failures are widespread phenomena that cause disruptions in the data acquisition process. Luckily, failures of these types are often sparse and localized at the single sensor level, i.e., they do not compromise the entire sensor network at once. 
In other terms, it is often the case that, at a certain time step, missing data appear only at some of the channels of the resulting multivariate time series. In this context, spatio-temporal imputation methods (Yi et al., 2016; Yoon et al., 2018b) aim at reconstructing the missing parts of the signals by possibly exploiting both temporal and spatial dependencies. 
In particular, effective spatio-temporal approaches would reconstruct missing values by taking into account past and future values, and the concurrent measurements of spatially close neighboring sensors too. 
Here, spatial similarity does not necessarily mean physical (e.g., geographic) proximity, but rather indicates that considered sensors are related w.r.t. a generic (quantifiable) functional dependency (e.g., Pearson correlation or Granger causality – Granger, 1969) and/or that are close in a certain latent space. 
Relational information, then, can be interpreted as a set of constraints – linking the different time series – that allows replacing the malfunctioning sensors with virtual ones.

从理论和实践的角度来看，缺失值的插补是多元时间序列分析 (TSA) 中的一个突出问题（Little & Rubin，2019）。
事实上，在一个复杂互联系统的世界中，例如那些表征传感器网络或物联网的系统，故障传感器和网络故障是一种普遍现象，会导致数据采集过程中断。幸运的是，这些类型的故障通常是稀疏的并且局限于单个传感器级别，即它们不会立即危及整个传感器网络。
换句话说，通常情况下，在某个时间步长，缺失数据仅出现在生成的多元时间序列的某些通道上。在这种情况下，时空插补方法 (Yi et al., 2016; Yoon et al., 2018b) 旨在通过可能利用时间和空间依赖性来重建信号的缺失部分。
特别是，有效的时空方法将通过考虑过去和未来的值以及空间上相邻传感器的同时测量来重建缺失值。
在这里，空间相似性并不一定意味着物理（例如，地理）接近，而是表明所考虑的传感器与 w.r.t. 相关。一个通用的（可量化的）函数依赖（例如，皮尔逊相关性或格兰杰因果关系——格兰杰，1969）和/或在某个潜在空间中接近。
因此，关系信息可以被解释为一组约束——连接不同的时间序列——允许用虚拟传感器替换故障传感器。

Among different imputation methods, approaches based on deep learning (LeCun et al., 2015; Schmidhuber, 2015; Goodfellow et al., 2016) have become increasingly popular (Yoon et al., 2018a; Cao et al., 2018; Liu et al., 2019). 
However, these methods often completely disregard available relational information or rely on rather simplistic modifications of standard neural architectures tailored for sequence processing (Hochreiter & Schmidhuber, 1997; Chung et al., 2014; Bai et al., 2018;
Vaswani et al., 2017). 
We argue that stronger, structural, inductive biases are needed to advance the state of the art in time series imputation and allow to build effective inference engines in the context of large and complex sensor networks as those found in real-world applications.

在不同的插补方法中，基于深度学习的方法（LeCun 等，2015；Schmidhuber，2015；Goodfellow 等，2016）变得越来越流行（Yoon 等，2018a；Cao 等，2018；Liu 等 等人，2019）。
然而，这些方法通常完全忽略可用的关系信息或依赖于为序列处理量身定制的标准神经架构的相当简单的修改（Hochreiter & Schmidhuber，1997；Chung 等人，2014；Bai 等人，2018；
Vaswani 等人，2017 年）。
我们认为，需要更强大的、结构性的、归纳性偏差来推进时间序列插补的最新技术水平，并允许在大型和复杂传感器网络的背景下构建有效的推理引擎，就像在现实世界应用中发现的那样。
In this work, we model input multivariate time series as sequences of graphs where edges represent relationships among different channels. 
We propose graph neural networks (GNNs) (Scarselli et al., 2008; Bronstein et al., 2017; Battaglia et al., 2018) as the building block of a novel, bidirectional, recurrent neural network for multivariate time series imputation (MTSI). 
Our method, named Graph Recurrent Imputation Network (GRIN), has at its core a recurrent neural network cell where gates are implemented by message-passing neural networks (MPNNs; Gilmer et al., 2017).
Two of these networks process the input multivariate time series in both forward and backward time directions at each node, while hidden states are processed by a message-passing imputation layer which is constrained to learn how to perform imputation by looking at neighboring nodes. 
In fact, by considering each edge as a soft functional dependency that constraints the value observed at corresponding nodes, we argue that operating in the context of graphs introduces a positive inductive bias for MTSI.
Our contributions are manifold: 
1) we introduce a methodological framework to exploit graph neural networks in the context of MTSI, 
2) we propose a novel, practical and effective implementation of a GNN-based architecture for MTSI, and 
3) we achieve state-of-the-art results on several and varied MTSI benchmarks. 
Our method does not rely on any assumption on the distribution of the missing values (e.g., presence and duration of transient dynamics and/or length of missing sequences)other than stationarity of the underlying process. 
The rest of the paper is organized as follows. In Section 2 we discuss the related works. Then, in Section 3, we formally introduce the problem settings and the task of MTSI. We present our approach to MTSI in Section 4, by describing the novel framework to implement imputation architectures based on GNNs. We proceed with an empirical evaluation of the presented method against state-of-the-art baselines in Section 5 and, finally, we draw our conclusions in Section 6.
在这项工作中，我们将输入多元时间序列建模为图序列，其中边表示不同通道之间的关系。
我们提出图神经网络 (GNN) (Scarselli et al., 2008; Bronstein et al., 2017; Battaglia et al., 2018) 作为用于多元时间序列插补 (MTSI) 的新型双向循环神经网络的构建块）。
我们的方法名为 Graph Recurrent Imputation Network (GRIN)，其核心是一个循环神经网络单元，其中的门由消息传递神经网络 (MPNNs; Gilmer et al., 2017) 实现。
其中两个网络在每个节点处处理前向和后向时间方向的输入多元时间序列，而隐藏状态由消息传递插补层处理，该插补层受限于通过查看相邻节点来学习如何执行插补。
事实上，通过将每条边视为限制在相应节点观察到的值的软函数依赖，我们认为在图的上下文中操作会为 MTSI 引入正归纳偏差。
我们的贡献是多方面的：
1) 我们引入了一个方法框架来在 MTSI 的背景下利用图神经网络，
2) 我们为 MTSI 提出了一种新颖、实用且有效的基于 GNN 的架构实现，以及
3) 我们在多个不同的 MTSI 基准上取得了最先进的结果。
我们的方法不依赖于对缺失值分布的任何假设（例如，瞬态动态的存在和持续时间和/或缺失序列的长度），而不是基础过程的平稳性。
本文的其余部分安排如下。在第 2 节中，我们讨论了相关的工作。然后，在第 3 节中，我们正式介绍了问题设置和 MTSI 的任务。我们在第 4 节中介绍了我们的 MTSI 方法，通过描述实现基于 GNN 的插补架构的新框架。我们在第 5 节中对所提出的方法与最先进的基线进行实证评估，最后，我们在第 6 节中得出结论。

## 相关工作
Time series imputation There exists a large literature addressing missing value imputation in timeseries. Besides the simple and standard interpolation methods based on polynomial curve fitting, popular approaches aim at filling up missing values by taking advantage of standard forecasting methods and similarities among time series. 
For example, several approaches rely on k-nearest neighbors (Troyanskaya et al., 2001; Beretta & Santaniello, 2016), the expectation-maximization algorithm (Ghahramani & Jordan, 1994; Nelwamondo et al., 2007) or linear predictors and statespace models (Durbin & Koopman, 2012; Kihoro et al., 2013). Low-rank approximation methods, such as matrix factorization (Cichocki & Phan, 2009), are also popular alternatives which can also account for spatial (Cai et al., 2010; Rao et al., 2015) and temporal (Yu et al., 2016; Mei et al., 2017) information. Among linear methods, STMVL (Yi et al., 2016) combines temporal and spatial interpolation to fill missing values in geographically tagged time series.
时间序列插补 存在大量文献解决时间序列中的缺失值插补。除了基于多项式曲线拟合的简单和标准插值方法外，流行的方法旨在利用标准预测方法和时间序列之间的相似性来填补缺失值。
例如，有几种方法依赖于 k 近邻（Troyanskaya 等人，2001；Beretta 和 Santaniello，2016）、期望最大化算法（Ghahramani 和 Jordan，1994；Nelwamondo 等人，2007）或线性预测器和状态空间模型（Durbin 和 Koopman，2012 年；Kihoro 等人，2013 年）。低秩逼近方法，例如矩阵分解 (Cichocki & Phan, 2009)，也是可以解释空间 (Cai et al., 2010; Rao et al., 2015) 和时间 (Yu et al. , 2016; Mei et al., 2017) 信息。在线性方法中，STMVL (Yi et al., 2016) 结合时间和空间插值来填补地理标记时间序列中的缺失值。
More recently, several deep learning approaches have been proposed for MTSI. Among the others, deep autoregressive methods based on recurrent neural networks (RNNs) found widespread success (Lipton et al., 2016; Che et al., 2018; Luo et al., 2018; Yoon et al., 2018b; Cao et al., 2018). GRU-D (Che et al., 2018) learns how to process sequences with missing data by controllingthe decay of the hidden states of a gated RNN. Cao et al. (2018) propose BRITS, a bidirectional GRU-D-like RNN for multivariate time series imputation that takes into account correlation among different channels to perform spatial imputation. 
Other successful strategies in the literature havebeen proposed that exploit the adversarial training framework to generate realistic reconstructed sequences (Yoon et al., 2018a; Fedus et al., 2018; Luo et al., 2018; 2019). Notably, GAIN (Yoon et al., 2018a) uses GANs (Goodfellow et al., 2014) to learn models that perform imputation in the i.i.d. settings. Luo et al. (2018; 2019) aim, instead, at learning models that generate realistic synthetic sequences and exploit them to fill missing values. Miao et al. (2021) use an approach similar to GAIN, but condition the generator on the predicted label for the target incomplete time series. 
Concurrently to our work, Kuppannagari et al. (2021) developed a graph-based spatio-temporal denoising autoencoder for spatio-temporal data coming from smart grids with known topology. Liu et al. (2019), instead, uses adversarial learning to train a multiscale model that imputes highly sparse time series in a hierarchical fashion. However, we argue that none of the above-cited methods can take full advantage of relational information and nonlinear spatio-temporal dependencies.
Most importantly, the above methods do not fully exploit the flexibility and expressiveness enabled by operating in the context of graph processing.
最近，已经为 MTSI 提出了几种深度学习方法。其中，基于循环神经网络 (RNN) 的深度自回归方法获得了广泛的成功（Lipton 等人，2016；Che 等人，2018；Luo 等人，2018；Yoon 等人，2018b；Cao 等人., 2018)。 GRU-D (Che et al., 2018) 学习如何通过控制门控 RNN 隐藏状态的衰减来处理缺失数据的序列。曹等人。 (2018) 提出了 BRITS，这是一种双向 GRU-D-like RNN，用于多变量时间序列插补，考虑到不同通道之间的相关性以执行空间插补。
文献中提出了其他成功的策略，即利用对抗性训练框架生成逼真的重建序列（Yoon 等人，2018a；Fedus 等人，2018；Luo 等人，2018；2019）。值得注意的是，GAIN (Yoon et al., 2018a) 使用 GANs (Goodfellow et al., 2014) 来学习在 i.i.d. 中执行插补的模型。设置。罗等人。 （2018 年；2019 年）的目标是学习生成真实合成序列并利用它们来填充缺失值的模型。苗等人。 (2021) 使用类似于 GAIN 的方法，但将生成器设置在目标不完整时间序列的预测标签上。
在我们工作的同时，Kuppannagari 等人。 （2021）开发了一种基于图的时空去噪自动编码器，用于来自具有已知拓扑的智能电网的时空数据。刘等人。相反，(2019) 使用对抗性学习来训练多尺度模型，该模型以分层方式估算高度稀疏的时间序列。然而，我们认为上述方法都不能充分利用关系信息和非线性时空依赖性。
最重要的是，上述方法并没有充分利用在图处理上下文中操作所带来的灵活性和表现力。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220915101224.png)
Figure 1: Representation of a multivariate time series as a sequence of graphs. Red circles denotenodes with missing values, nodes are identified.图 1：将多元时间序列表示为图形序列。 红色圆圈表示具有缺失值的节点，节点已被识别。

Graph neural networks for TSA  Graph neural networks have been exploited in TSA mostly in spatio-temporal forecasting methods. The idea behind most of the methods present in the literature is to modify standard neural network architectures for sequential data by relying on operators that work in the graph domain. For example, Seo et al. (2018) propose a GRU cell where gates are implemented by spectral GNNs (Defferrard et al., 2016); Li et al. (2018) propose an analogous architecture replacing spectral GNNs with a diffusion-convolutional network (Atwood & Towsley, 2016). 
Note that these models are different w.r.t. approaches that use recurrent networks to propagate information graph-wise (Scarselli et al., 2008; Li et al., 2016). Yu et al. (2017) and Wu et al. (2019; 2020b) propose, instead, spatio-temporal convolutional neural networks that alternate convolutions on temporal and spatial dimensions. 
Similar approaches have also been studied in the context of attention-based models (Vaswani et al., 2017) with spatio-temporal Transformer-like architectures (Zhang et al.,2018; Cai et al., 2020). Another particularly interesting line of research is related to the problem of learning the graph structure underlying an input multivariate time series (Kipf et al., 2018; Wuet al., 2020b; Shang et al., 2020). 
While previously mentioned approaches focus on multivariate time series prediction, other methods aim at predicting changes in graph topology (Zambon et al.,2019; Paassen et al., 2020). Conversely, methods such as Temporal Graph Networks (Rossi et al.,2020) are tailored to learn node embeddings in dynamical graphs. 
Finally, recent works have proposed GNNs for imputing missing features in the context of i.i.d. data. Among the others, Spinelli et al. (2020) propose an adversarial framework to train GNNs on the data reconstruction task, while You et al. (2020) propose a bipartite graph representation for feature imputation. 
Lately, GNNs have also been exploited for spatial interpolation (Appleby et al., 2020; Wu et al., 2020a) – sometimes referred to as kriging (Stein, 1999). To the best of our knowledge, no previous GNN-based method targeted missing value imputation for generic multivariate time series.
用于 TSA 的图神经网络 图神经网络已在 TSA 中主要用于时空预测方法。文献中存在的大多数方法背后的想法是通过依赖在图域中工作的运算符来修改用于顺序数据的标准神经网络架构。例如，Seo 等人。 （2018 年）提出了一个 GRU 单元，其中门由光谱 GNN 实现（Defferrard 等人，2016 年）；李等人。 （2018 年）提出了一种类似的架构，用扩散卷积网络代替光谱 GNN（Atwood 和 Towsley，2016 年）。
请注意，这些模型是不同的 w.r.t。使用循环网络以图形方式传播信息的方法（Scarselli 等人，2008；Li 等人，2016）。于等人。 （2017）和吴等人。 （2019；2020b）提出了时空卷积神经网络，它在时间和空间维度上交替卷积。
在基于注意力的模型（Vaswani 等人，2017 年）和时空类 Transformer 架构（Zhang 等人，2018 年；Cai 等人，2020 年）的背景下，也研究了类似的方法。另一个特别有趣的研究方向与学习输入多元时间序列的图结构问题有关（Kipf 等人，2018；Wuet 等人，2020b；Shang 等人，2020）。
虽然前面提到的方法侧重于多变量时间序列预测，但其他方法旨在预测图拓扑的变化（Zambon 等人，2019；Paassen 等人，2020）。相反，时间图网络 (Rossi et al.,2020) 等方法专门用于学习动态图中的节点嵌入。
最后，最近的工作提出了在 i.i.d 的上下文中估算缺失特征的 GNN。数据。其中，斯皮内利等人。 （2020）提出了一个对抗性框架来训练 GNN 的数据重建任务，而 You 等人。 （2020）提出了一种用于特征插补的二部图表示。
最近，GNN 也被用于空间插值（Appleby 等人，2020；Wu 等人，2020a）——有时也称为克里金法（Stein，1999）。据我们所知，以前没有基于 GNN 的方法针对通用多元时间序列的缺失值插补。
## 预备
Sequences of graphs 
We consider sequences of weighted directed graphs, where we observe a graph Gt with Nt nodes at each time step t. A graph is a couple Gt = hXt,Wti, where Xt ∈ R Nt×d is the node-attribute matrix whose i-th row contains the d-dimensional node-attribute vector xit ∈Rd associated with the i-th node; entry w i,j t of the adjacency matrix Wt ∈ R Nt×Nt denotes the scalar weight of the edge (if any) connecting the i-th and j-th node. 
Fig. 1 exemplifies this modelling framework. We assume nodes to be identified, i.e., to have a unique ID that enables time-wise consistent processing. This problem setting can be easily extended to more general classes of graphs with attributed edges and global attributes. In this work, we mainly focus on problems where the topology of the graph is fixed and does not change over time, i.e., at each time step Wt = W and Nt = N.
图的序列
我们考虑加权有向图序列，其中我们在每个时间步 t 观察具有 $N_t$ 个节点的图 $g_t$。 一个图是一对 $g_t = <X_t,W_t>$，其中 $X_t ∈ R^{N_t×d}$ 是节点属性矩阵，其第 i 行包含与第 i 个节点关联的 d 维节点属性向量 $x^i_t ∈R^d$； 邻接矩阵 $W_t ∈ R^{N_t×N_t}$ 的条目 $w^{i,j}_t$ 表示连接第 i 个和第 j 个节点的边（如果有）的标量权重。
图 1 举例说明了这个建模框架。 我们假设节点被识别，即具有唯一的 ID，可以实现时间一致的处理。 这个问题设置可以很容易地扩展到具有属性边和全局属性的更一般的图类。 在这项工作中，我们主要关注图的拓扑是固定的并且不随时间变化的问题，即在每个时间步 $W_t = W$ 和 $N_t = N$。

Any generic multivariate time series fits the above framework by letting each channel of the sequence (i.e., each sensor) correspond to a node and using the available relation information to build an adjacency matrix. 
If no relational information is available, one could use the identity matrix, butthis would defeat the purpose of the formulation. A more proper choice of Wt can be made using any standard similarity score (e.g., Pearson correlation) or a (thresholded) kernel.
A more advanced approach instead could aim at learning an adjacency directly from data by using, for instance, spatial attention scores or resorting to graph learning techniques, e.g., Kipf et al. (2018). 
From now on, we assume that input multivariate time series have homogeneous channels, i.e., sensors are of the same type. Note that this assumption does not imply a loss in generality: it is always possible to standardize node features by adding sensor type attributes and additional dimensions to accommodate the different types of sensor readings. Alternatively, one might directly model the problem by exploiting heterogeneous graphs (Schlichtkrull et al., 2018).
通过让序列的每个通道（即每个传感器）对应一个节点并使用可用的关系信息来构建邻接矩阵，任何通用的多变量时间序列都符合上述框架。
如果没有可用的关系信息，可以使用单位矩阵，但这会违背公式的目的。可以使用任何标准相似度得分（例如，Pearson 相关性）或（阈值）内核来更合适地选择 Wt。
相反，更高级的方法可以通过使用空间注意力分数或求助于图学习技术（例如，Kipf 等人）直接从数据中学习邻接。 （2018 年）。
从现在开始，我们假设输入的多元时间序列具有同质通道，即传感器属于同一类型。请注意，此假设并不意味着一般性的损失：通过添加传感器类型属性和附加维度以适应不同类型的传感器读数，始终可以标准化节点特征。或者，可以通过利用异构图直接对问题进行建模（Schlichtkrull 等人，2018 年）。
Multivariate time series imputation 
To model the presence of missing values, we consider, at each step, a binary mask $M_t ∈ {0, 1}^{N_t×d}$ where each row $m^i_t$ indicates which of the corresponding node attributes of $x^i_t$ are available in $X_t$. It follows that, $m^{i,j}_t = 0$ implies $x^{i,j}_t$ to be missing;
conversely, if $m^{i,j}_t = 1$, then $x^{i,j}_t$ stores the actual sensor reading. We denote by $\widetilde{\boldsymbol{X}}_t$
 the unknown ground truth node-attribute matrix, i.e., the complete node-attribute matrix without any missing data.
We assume stationarity of missing data distribution and, in experiments, we mostly focus on the missing at random (MAR) scenario (Rubin, 1976). We neither make assumptions on the number of concurrent sensor failures, nor on the length of missing data blocks, i.e., multiple failures extended over time are accounted for. 
Clearly, one should expect imputation performance to scale with the number of concurrent faults and the time length of missing data bursts.

多元时间序列插补
为了对缺失值的存在进行建模，我们在每一步都考虑一个二进制掩码 $M_t ∈ {0, 1}^{N_t×d}$，其中每一行 $m^i_t$ 表示 $ 的哪些对应节点属性x^i_t$ 在 $X_t$ 中可用。因此，$m^{i,j}_t = 0$ 意味着 $x^{i,j}_t$ 缺失；
相反，如果 $m^{i,j}_t = 1$，则 $x^{i,j}_t$ 存储实际传感器读数。我们用 $\widetilde{\boldsymbol{X}}_t$ 表示
 未知的地面实况节点属性矩阵，即没有任何缺失数据的完整节点属性矩阵。
我们假设缺失数据分布的平稳性，并且在实验中，我们主要关注随机缺失（MAR）场景（Rubin，1976）。我们既不假设并发传感器故障的数量，也不假设丢失数据块的长度，即考虑到随时间延长的多个故障。
显然，人们应该期望插补性能与并发故障的数量和丢失数据突发的时间长度成比例。

The objective of MTSI is to impute missing values in a sequence of input data. More formally, given a graph sequence $G_{[t,t+T]}$ of length T, we can define the missing data reconstruction error as
MTSI 的目标是在输入数据序列中估算缺失值。 更正式地说，给定一个长度为 T 的图序列 $G_{[t,t+T]}$，我们可以将缺失数据重构误差定义为

$$
\mathcal{L}\left(\widehat{\boldsymbol{X}}_{[t, t+T]}, \widetilde{\boldsymbol{X}}_{[t, t+T]}, \overline{\boldsymbol{M}}_{[t, t+T]}\right)=\sum_{h=t}^{t+T} \sum_{i=1}^{N_t} \frac{\left\langle\overline{\boldsymbol{m}}_h^i, \ell\left(\hat{\boldsymbol{x}}_h^i, \tilde{\boldsymbol{x}}_h^i\right)\right\rangle}{\left\langle\overline{\boldsymbol{m}}_h^i, \overline{\boldsymbol{m}}_h^i\right\rangle}
$$


$\hat{\boldsymbol{x}}_h^i$  $\tilde{\boldsymbol{x}}_h^i$
$\overline{\boldsymbol{M}}_{[t, t+T]}$
$\overline{\boldsymbol{m}}_h^i$
$\widetilde{\boldsymbol{X}}_{[t, t+T]}$



where $\hat{\boldsymbol{x}}_h^i$ is the reconstructed $\tilde{\boldsymbol{x}}_h^i$; $\overline{\boldsymbol{M}}_{[t, t+T]}$ and $\overline{\boldsymbol{m}}_h^i$ are respectively the logical binary complement of ${\boldsymbol{M}}_{[t, t+T]}$ and ${\boldsymbol{m}}_h^i$, l( · , · ) is an element-wise error function (e.g., absolute or squared error) and < · , ·> 
indicates the standard dot product. 
Note that, in practice, it is impossible to have access to $\widetilde{\boldsymbol{X}}_{[t, t+T]}$ and, as a consequence, it is necessary to define a surrogate optimization objective by, for example, using a forecasting loss or generating synthetic missing values. In the context of trainable, parametric, imputation methods, we consider two different operational settings. 
In the first one, named in-sample imputation, the model is trained to reconstruct missing values in a given fixed input sequence ${\boldsymbol{X}}_{[t, t+T]}$
, i.e., the model is trained on all the available data except those that are missing and those that have been removed from the sequence to emulate additional failures for
evaluation. 
Differently, in the second one (referred to as out-of-sample imputation), the model is trained and evaluated on disjoint sequences. 
Note that in both cases the model does not have access to the ground-truth data used for the final evaluation. The first operational setting simulates the case where a practitioner fits the model directly on the sequence to fill up its gaps. The second, instead, simulates the case where one wishes to use a model fitted on a set of historical data to impute missing values in an unseen target sequence.

其中$\hat{\boldsymbol{x}}_h^i$是重构的$\tilde{\boldsymbol{x}}_h^i$； $\overline{\boldsymbol{M}}_{[t, t+T]}$ 和 $\overline{\boldsymbol{m}}_h^i$ 分别是 ${\boldsymbol{M} 的逻辑二进制补码}_{[t, t+T]}$ 和 ${\boldsymbol{m}}_h^i$, l( · , · ) 是元素级误差函数（例如，绝对误差或平方误差）并且 < · , ·>
表示标准点积。
请注意，在实践中，不可能访问 $\widetilde{\boldsymbol{X}}_{[t, t+T]}$，因此，有必要通过以下方式定义代理优化目标，例如，使用预测损失或生成合成缺失值。在可训练、参数化、插补方法的背景下，我们考虑了两种不同的操作设置。
在第一个名为 in-sample imputation 的方法中，模型被训练以重建给定固定输入序列 ${\boldsymbol{X}}_{[t, t+T]}$ 中的缺失值
，即模型在所有可用数据上进行训练，除了那些缺失的数据和那些已从序列中删除的数据以模拟额外的失败
评估。
不同的是，在第二个（称为样本外插补）中，模型是在不相交的序列上训练和评估的。
请注意，在这两种情况下，模型都无法访问用于最终评估的真实数据。第一个操作设置模拟了从业者直接在序列上拟合模型以填补其空白的情况。相反，第二种模拟了希望使用拟合一组历史数据的模型来估算看不见的目标序列中的缺失值的情况。

## 4 图形循环插补网络

In this section, we present our approach, the Graph Recurrent Imputation Network (GRIN), a graphbased, recurrent neural architecture for MTSI. 
Given a multivariate time series ${\boldsymbol{X}}_{[t, t+T]}$
 with mask ${\boldsymbol{M}}_{[t, t+T]}$ , our objective is to reconstruct missing values in the input sequence by combining the information coming from both the temporal and spatial dimensions. 
To do so, we design a novel bidirectional graph recurrent neural network which progressively processes the input sequence both
forward and backward in time by performing two stages of imputation for each direction. 
Then, a feed-forward network takes as input the representation learned by the forward and backward models and performs a final – refined – imputation for each node of the graph and step of the sequence.
More precisely, the final imputation depends on the output of two GRIN modules whose learned representations are finally processed (space and time wise) by a last decoding multilayer perceptron (MLP). 
An overview of the complete architecture is given in Fig. 2. As shown in the figure, the two modules impute missing values iteratively, using at each time step previously imputed values as input. We proceed by first describing in detail the unidirectional model, and then we provide the bidirectional extension.

在本节中，我们将介绍我们的方法，即图递归插补网络 (GRIN)，这是一种用于 MTSI 的基于图的递归神经架构。
给定一个多元时间序列 ${\boldsymbol{X}}_{[t, t+T]}$
 使用掩码 ${\boldsymbol{M}}_{[t, t+T]}$ ，我们的目标是通过结合来自时间和空间维度的信息来重建输入序列中的缺失值。
为此，我们设计了一种新颖的双向图循环神经网络，它逐步处理输入序列
通过对每个方向执行两个阶段的插补，在时间上向前和向后。
然后，前馈网络将前向和后向模型学习的表示作为输入，并对图的每个节点和序列的步骤执行最终的 - 细化 - 插补。
更准确地说，最终的插补取决于两个 GRIN 模块的输出，它们的学习表示最终由最后一个解码多层感知器 (MLP) 处理（空间和时间方面）。
图 2 给出了完整架构的概述。如图所示，这两个模块迭代地估算缺失值，在每个时间步使用先前估算的值作为输入。我们首先详细描述单向模型，然后提供双向扩展。


Unidirectional model 
Each GRIN module is composed of two blocks, a spatio-temporal encoder and a spatial decoder, which process the input sequence of graphs in two stages. 
The spatio-temporal encoder maps the input sequence ${\boldsymbol{X}}_{[t, t+T]}$ to a spatio-temporal representation $\boldsymbol{H}_{[t, t+T]} \in \mathbb{R}^{N_t \times l}$
 by exploiting an ad-hoc designed recurrent GNN. 
The spatial decoder, instead, takes advantage of the learned representations to perform two consecutive rounds of imputation. 
A first-stage imputation is obtained from the representation by using a linear readout; the second one exploits available relational, spatial, information at time step t. 
In particular, the decoder is implemented by an MPNN which learns to infer the observed values at each i-th node – $x^i_t$ – by refining first-stage imputations considering – locally – $H_{t−1}$ and values observed at neighboring nodes.

单向模型
每个 GRIN 模块由两个块组成，一个时空编码器和一个空间解码器，它们分两个阶段处理图的输入序列。
时空编码器将输入序列 ${\boldsymbol{X}}_{[t, t+T]}$ 映射到时空表示 $\boldsymbol{H}_{[t, t+T]} \in \mathbb{R}^{N_t \times l}$
  通过利用特别设计的循环 GNN。
相反，空间解码器利用学习的表示来执行两轮连续的插补。
通过使用线性读数从表示中获得第一阶段的插补； 第二个利用时间步 t 处可用的关系、空间信息。
特别是，解码器由 MPNN 实现，该 MPNN 学习推断每个第 i 个节点的观察值 - $x^i_t$ - 通过细化第一阶段插补考虑 - 本地 - $H_{t−1}$ 和值 在相邻节点上观察到。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220915110411.png)

Figure 2: An overview of the bidirectional architecture. Here, each unidirectional GRIN module is processing the τ -th step of an input sequence with 4 dimensions (sensors). Two values are missing at the considered time step. GRIN performs a first imputation, which is then processed and refined by the spatial decoder. These second-stage imputations are then used to continue the processing at the next step. An MLP processes learned representations node and time wise to obtain final imputations.

图 2：双向架构概述。 在这里，每个单向 GRIN 模块正在处理具有 4 维（传感器）的输入序列的第 τ 步。 在考虑的时间步长处缺少两个值。 GRIN 执行第一次插补，然后由空间解码器处理和细化。 然后使用这些第二阶段的插补继续下一步的处理。 MLP 处理学习的表示节点和时间，以获得最终的插补。

Spatio-temporal Encoder 
In the encoder, the input sequence ${\boldsymbol{X}}_{[t, t+T]}$ and mask ${\boldsymbol{M}}_{[t, t+T]}$ are processed sequentially one step at a time, by means of a recurrent neural network with gates implemented by message-passing layers. Any message-passing operator could be used in principle. In particular, given $z^i_{t,k−1} , i.e., the node features vector at layer k − 1, we consider the general class of MPNNs described as


时空编码器
在编码器中，依次处理输入序列${\boldsymbol{X}}_{[t, t+T]}$和掩码${\boldsymbol{M}}_{[t, t+T]}$ 一次一步，通过带有由消息传递层实现的门的循环神经网络。 原则上可以使用任何消息传递运算符。 特别是，给定 $z^i_{t,k−1} ，即第 k−1 层的节点特征向量，我们认为 MPNN 的一般类描述为
$$
\operatorname{MPNN}_k\left(\boldsymbol{z}_{t, k-1}^i, \boldsymbol{W}_t\right)=\gamma_k\left(\boldsymbol{z}_{t, k-1}^i, \sum_{j \in \mathcal{N}(i)} \rho_k\left(\boldsymbol{z}_{t, k-1}^i, \boldsymbol{z}_{t, k-1}^j\right)\right)=\boldsymbol{z}_{t, k}^i
$$

where $\mathcal{N}(i)$ is the set of neighbors of the i-th node in $g_t$, $γ_k$ and $ρ_k$ are generic, differentiable, update and message functions (e.g., MLPs), and Σ is a permutation invariant, differentiable aggregation function (e.g., sum or mean). Note that several definitions of neighborhood are possible, e.g., one might consider nodes connected by paths up to a certain length l. For the sake of simplicity, from now on, we indicate with MPNN($z^i_t$,$W_t$) the forward pass of a generic K-layered message-passing neural network. In the following, we use MPNNs as the building blocks for our spatio-temporal feature extractors. To learn the dynamics of the system, we leverage on gated recurrent units (GRUs; Cho et al., 2014). As previously mentioned, similarly to Seo et al. (2018) and Li et al. (2018), we implement the GRU gates by relying on the message-passing layers defined above. At the node level, the elements of the message-passing GRU (MPGRU) can be described as:

其中 $\mathcal{N}(i)$ 是 $g_t$ 中第 i 个节点的邻居集合，$γ_k$ 和 $ρ_k$ 是通用、可微分、更新和消息函数（例如 MLP），并且Σ 是一个置换不变、可微的聚合函数（例如，总和或均值）。请注意，邻域的几种定义是可能的，例如，可以考虑由最长为一定长度 l 的路径连接的节点。为了简单起见，从现在开始，我们用 MPNN($z^i_t$,$W_t$) 表示通用 K 层消息传递神经网络的前向传递。在下文中，我们使用 MPNN 作为时空特征提取器的构建块。为了学习系统的动态，我们利用门控循环单元（GRUs；Cho et al., 2014）。如前所述，类似于 Seo 等人。 （2018）和李等人。 （2018），我们依靠上面定义的消息传递层来实现 GRU 门。在节点级别，消息传递 GRU (MPGRU) 的元素可以描述为：

$$
\begin{aligned}
\boldsymbol{r}_t^i &=\sigma\left(\operatorname{MPNN}\left(\left[\hat{\boldsymbol{x}}_t^{i(2)}\left\|\boldsymbol{m}_t^i\right\| \boldsymbol{h}_{t-1}^i\right], \boldsymbol{W}_t\right)\right) (3)\\
\boldsymbol{u}_t^i &=\sigma\left(\operatorname{MPNN}\left(\left[\hat{\boldsymbol{x}}_t^{i(2)}\left\|\boldsymbol{m}_t^i\right\| \boldsymbol{h}_{t-1}^i\right], \boldsymbol{W}_t\right)\right) (4)\\
\boldsymbol{c}_t^i &=\tanh \left(\operatorname{MPNN}\left(\left[\hat{\boldsymbol{x}}_t^{i(2)}\left\|\boldsymbol{m}_t^i\right\| \boldsymbol{r}_t^i \odot \boldsymbol{h}_{t-1}^i\right], \boldsymbol{W}_t\right)\right) (5)\\
\boldsymbol{h}_t^i &=\boldsymbol{u}_t^i \odot \boldsymbol{h}_{t-1}^i+\left(1-\boldsymbol{u}_t^i\right) \odot \boldsymbol{c}_t^i (6)
\end{aligned}
$$

where $r^i_t$ ,$u^i_t$ are the reset and update gates, respectively, $h^i_t$ is the hidden representation of the i-th node at time t, and $\boldsymbol{x}_t^{i(2)}$ is the output of the decoding block at the previous time-step (see next paragraph). The symbols $\odot$  and || denote the Hadamard product and the concatenation operator, respectively. The initial representation $H_{t−1}$ can either be initialized as a constant or with a learnable embedding. Note that for the steps where input data are missing, the encoder is fed with predictions from the decoder block, as explained in the next subsection. By carrying out the above computation time and node wise, we get the encoded sequence $H_{[t,t+T]}$.


其中 $r^i_t$ ,$u^i_t$ 分别是重置门和更新门，$h^i_t$ 是第 i 个节点在时间 t 的隐藏表示，$\boldsymbol{x}_t^ {i(2)}$ 是解码块在前一个时间步的输出（见下一段）。 符号 $\odot$ 和 || 分别表示 Hadamard 积和串联运算符。 初始表示 $H_{t−1}$ 可以初始化为常数或具有可学习的嵌入。 请注意，对于缺少输入数据的步骤，编码器会收到来自解码器块的预测，如下一小节所述。 通过执行上述计算时间和节点，我们得到编码序列$H_{[t,t+T]}$。

Spatial Decoder 
As a first decoding step, we generate one-step-ahead predictions from the hidden representations of the MPGRU by means of a linear readout

空间解码器
作为第一个解码步骤，我们通过线性读出从 MPGRU 的隐藏表示中生成单步预测
$$
\widehat{\boldsymbol{Y}}_t^{(1)}=\boldsymbol{H}_{t-1} \boldsymbol{V}_h+\boldsymbol{b}_h
$$
where $V_h \in \mathbb{R}^{l \times d}$ is a learnable weight matrix and $b_h \in \mathbb{R}^{d}$
 is a learnable bias vector. We then define the filler operator as

其中 $V_h \in \mathbb{R}^{l \times d}$ 是一个可学习的权重矩阵， $b_h \in \mathbb{R}^{d}$
  是一个可学习的偏置向量。 然后我们将填充运算符定义为

$$
\Phi\left(\boldsymbol{Y}_t\right)=\boldsymbol{M}_t \odot \boldsymbol{X}_t+\overline{\boldsymbol{M}}_t \odot \boldsymbol{Y}_t (8)
$$

intuitively, the filler operator replaces the missing values in the input $X_t$ with the values at the same positions in $Y_t$. By feeding $\widehat{\boldsymbol{Y}}_t^{(1)}$ to the filler operator, we get the first stage imputation \widehat{\boldsymbol{X}}_t^{(1)} such that the output is $X_t$ with missing values replaced by the one step-ahead predictions $\widehat{\boldsymbol{Y}}_t^{(1)}$  . The resulting node-level predictions are then concatenated to the mask $M_t$ and the hidden representation $H_{t−1}$, and processed by a final one-layer MPNN which computes for each node an imputation representation $s^i_t$ as

直观地，填充运算符将输入 $X_t$ 中的缺失值替换为 $Y_t$ 中相同位置的值。 通过将 $\widehat{\boldsymbol{Y}}_t^{(1)}$ 提供给填充算子，我们得到第一阶段插补 $\widehat{\boldsymbol{X}}_t^{(1)}$，使得 输出是 $X_t$ ，缺失值替换为一步前预测 $\widehat{\boldsymbol{Y}}_t^{(1)}$ 。 然后将得到的节点级预测连接到掩码 $M_t$ 和隐藏表示 $H_{t−1}$，并由最终的一层 MPNN 处理，该 MPNN 为每个节点计算一个插补表示 $s^i_t$ 作为

$$
\boldsymbol{s}_t^i=\gamma\left(\boldsymbol{h}_{t-1}^i, \sum_{j \in \mathcal{N}(i) / i} \rho\left(\left[\Phi\left(\hat{\boldsymbol{x}}_t^{j(1)}\right)\left\|\boldsymbol{h}_{t-1}^j\right\| \boldsymbol{m}_t^j\right]\right)\right)
$$

Notice that, as previously highlighted, the imputation representations only depend on messages received from neighboring nodes and the representation at the previous step. In fact, by aggregating only messages from the one-hop neighborhood, the representations $s^i_t$ are independent of the input features $x^i_t$ of the i-th node itself. This constraint forces the model to learn how to reconstruct a target input by taking into account spatial dependencies: this has a regularizing effect since the model is constrained to focus on local information. Afterward, we concatenate imputation representation $S_t$ with hidden representation $H_{t−1}$, and generate second-stage imputations by using a second linear readout and applying the filler operator

请注意，如前所述，插补表示仅取决于从相邻节点接收到的消息和上一步的表示。 事实上，通过仅聚合来自一跳邻域的消息，表示 $s^i_t$ 独立于第 i 个节点本身的输入特征 $x^i_t$。 这种约束迫使模型通过考虑空间依赖性来学习如何重建目标输入：这具有正则化效果，因为模型被约束为专注于局部信息。 之后，我们将插补表示 $S_t$ 与隐藏表示 $H_{t−1}$ 连接起来，并通过使用第二个线性读数并应用填充算子来生成第二阶段插补

$$
\widehat{\boldsymbol{Y}}_t^{(2)}=\left[\boldsymbol{S}_t \| \boldsymbol{H}_{t-1}\right] \boldsymbol{V}_s+\boldsymbol{b}_s ; \quad \widehat{\boldsymbol{X}}_t^{(2)}=\Phi\left(\widehat{\boldsymbol{Y}}_t^{(2)}\right)(10)
$$
$\widehat{\boldsymbol{X}}_t^{(2)}$

Finally, we feed $\widehat{\boldsymbol{X}}_t^{(2)}$
as input to the MPGRU (Eq. 3 – 6) to update the hidden representation and proceed to process the next input graph $g_{t+1}$

最后，我们将 $\widehat{\boldsymbol{X}}_t^{(2)}$作为 MPGRU (Eq. 3 – 6) 的输入以更新隐藏表示并继续处理下一个输入图 $g_{t+1}$

Bidirectional Model 
Extending GRIN to account for both forward and backward dynamics is straightforward and can be achieved by duplicating the architecture described in the two previous paragraphs. The first module will process the sequence in the forward direction (from the beginning of the sequence towards its end), while the second one in the other way around. The final imputation is then obtained with an MLP aggregating representations extracted by the two modules:

双向模型
将 GRIN 扩展到考虑前向和后向动态是很简单的，可以通过复制前两段中描述的架构来实现。 第一个模块将正向处理序列（从序列的开头到结尾），而第二个模块则相反。 然后使用由两个模块提取的 MLP 聚合表示获得最终的插补：

$$
\widehat{\boldsymbol{y}}_t^i=\operatorname{MLP}\left(\left[\boldsymbol{s}_t^{i, f w d}\left\|\boldsymbol{h}_{t-1}^{i, f w d}\right\| \boldsymbol{s}_t^{i, b w d} \| \boldsymbol{h}_{t+1}^{i, b w d}\right]\right)
$$

where fwd and bwd denote the forward and backward modules, respectively. The final output can then be easily obtained as $\widehat{\boldsymbol{X}}_{[t, t+T]} =\Phi\left(\widehat{\boldsymbol{Y}}_{[t, t+T]}\right)$. Note that, by construction, our model can exploit all the available relevant spatio-temporal information, since the only value explicitly masked out for each node is $x^i_t$ . At the same time, it is important to realize that our model does not merely reconstruct the input as an autoencoder, but it is specifically tailored for the imputation task due to its inductive biases. The model is trained by minimizing the reconstruction error of all imputation stages in both directions (see Appendix A)


其中 fwd 和 bwd 分别表示前向和后向模块。 然后可以很容易地获得最终输出为 $\widehat{\boldsymbol{X}}_{[t, t+T]} =\Phi\left(\widehat{\boldsymbol{Y}}_{[t, t +T]}\right)$。注意，通过构造，我们的模型可以利用所有可用的相关时空信息，因为每个节点显式屏蔽的唯一值是 $x^i_t$。同时，它 重要的是要意识到我们的模型不仅将输入重建为自动编码器，而且由于其归纳偏差，它专门为插补任务量身定制。通过最小化两个方向上所有插补阶段的重建误差来训练模型（参见 附录 A）

## 5 实证评估

In this section, we empirically evaluate our approach against state-of-the-art baselines on four datasets coming from three relevant application domains. Our approach, remarkably, achieves state-of-the-art performance on all of them.

在本节中，我们在来自三个相关应用领域的四个数据集上根据最先进的基线经验评估我们的方法。 我们的方法显着地在所有这些上实现了最先进的性能。

- Air Quality (AQI): dataset of recordings of several air quality indices from 437 monitoring stations spread across 43 Chinese cities. We consider only the PM2.5 pollutant. Prior works on imputation (Yi et al., 2016; Cao et al., 2018) consider a reduced version of this dataset, including only 36 sensors (AQI-36 in the following). We evaluate our model on both datasets. We use as adjacency matrix a thresholded Gaussian kernel (Shuman et al., 2013) computed from pairwise geographic distance.

- 空气质量 (AQI)：来自中国 43 个城市的 437 个监测站的多项空气质量指数记录数据集。 我们只考虑 PM2.5 污染物。 先前的估算工作（Yi et al., 2016; Cao et al., 2018）考虑了该数据集的简化版本，仅包括 36 个传感器（以下为 AQI-36）。 我们在两个数据集上评估我们的模型。 我们使用从成对地理距离计算的阈值高斯核 (Shuman et al., 2013) 作为邻接矩阵。

Table 1: Results on the air datasets. Performance averaged over 5 runs
表 1：空气数据集的结果。 5 次运行的平均性能
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220915124247.png)

- Traffic: we consider the PEMS-BAY and METR-LA datasets from Li et al. (2018), containing data from traffic sensors from the San Francisco Bay Area and the Los Angeles County Highway. We use the same approach of Li et al. (2018) and Wu et al. (2019) to obtain an adjacency matrix. 
- Smart grids: we consider data from the Irish Commission for Energy Regulation Smart Metering Project (CER-E; Commission for Energy Regulation, 2016). We select only the subset of the available smart meters monitoring the energy consumption of small and medium-sized enterprises (SMEs), i.e., 485 time series with samples acquired every 30 minutes. We build an adjacency matrix by extracting a k-nearest neighbor graph (with k = 10) from the similarity matrix built by computing the correntropy (Liu et al., 2007) among the time series.

- 交通：我们考虑来自 Li 等人的 PEMS-BAY 和 METR-LA 数据集。 (2018)，包含来自旧金山湾区和洛杉矶县高速公路的交通传感器的数据。 我们使用与 Li 等人相同的方法。 （2018）和吴等人。 （2019）获得邻接矩阵。
- 智能电网：我们考虑来自爱尔兰能源监管委员会智能计量项目（CER-E；能源监管委员会，2016 年）的数据。 我们仅选择监控中小型企业 (SME) 能源消耗的可用智能电表的子集，即每 30 分钟采集一次样本的 485 个时间序列。 我们通过从通过计算时间序列之间的相关熵 (Liu et al., 2007) 构建的相似性矩阵中提取 k 最近邻图（k = 10）来构建邻接矩阵。


For the air quality datasets, we adopt the same evaluation protocol of previous works (Yi et al., 2016; Cao et al., 2018) and we show results for both the in-sample and out-of-sample settings. For the traffic and energy consumption datasets, we consider only the out-of-sample scenario (except for matrix factorization which only works in-sample). We simulate the presence of missing data by considering 2 different settings: 1) Block missing, i.e, at each step, for each sensor, we randomly drop 5% of the available data and, in addition, we simulate a failure with probability pfailure = 0.15% and sample its duration uniformly in the interval [min steps, max steps], where min steps and max steps are the number of time steps corresponding respectively to 1 and 4 hours in the traffic case and 2 hours and 2 days for CER-E; 2) Point missing, i.e., we simply randomly mask out 25% of the available data. We split all the datasets into training/validation/test sets. We use as performance metrics the mean absolute error (MAE), mean squared error (MSE) and mean relative error (MRE; Cao et al., 2018) computed over the imputation window. For all the experiments, we use as messagepassing operator the diffusion convolution introduced by Atwood & Towsley (2016). We consider BRITS (Cao et al., 2018) as the principal competing alternative among non-adversarial deep autoregressive approaches, as it shares architectural similarities with our methods. As additional baselines we consider: 1) MEAN, i.e., imputation using the node-level average; 2) KNN, i.e., imputation by averaging values of the k = 10 neighboring nodes with the highest weight in the adjacency matrix Wt; 3) MICE (White et al., 2011), limiting the maximum number of iterations to 100 and the number of nearest features to 10; 4) Matrix Factorization (MF) with rank = 10; 5) VAR, i.e., a Vector Autoregressive one-step-ahead predictor; 6) rGAIN, i.e., an unsupervised version of SSGAN (Miao et al., 2021) which can be seen as GAIN (Yoon et al., 2018a) with bidirectional recurrent encoder and decoder; 7) MPGRU, a one-step-ahead GNN-based predictor similar to DCRNN (Li et al., 2018).

对于空气质量数据集，我们采用与之前工作相同的评估协议（Yi et al., 2016; Cao et al., 2018），我们展示了样本内和样本外设置的结果。对于交通和能源消耗数据集，我们仅考虑样本外场景（矩阵分解仅在样本内有效）。我们通过考虑 2 个不同的设置来模拟缺失数据的存在：1) 块缺失，即在每个步骤中，对于每个传感器，我们随机丢弃 5% 的可用数据，此外，我们以概率 pfailure = 模拟故障0.15% 并在区间 [min steps, max steps] 中均匀采样其持续时间，其中 min steps 和 max steps 是交通情况下分别对应于 1 和 4 小时以及 CER 的 2 小时和 2 天的时间步数- E; 2）点缺失，即我们只是随机屏蔽掉 25% 的可用数据。我们将所有数据集拆分为训练/验证/测试集。我们使用在插补窗口上计算的平均绝对误差 (MAE)、均方误差 (MSE) 和平均相对误差 (MRE; Cao et al., 2018) 作为性能指标。对于所有实验，我们使用 Atwood & Towsley (2016) 引入的扩散卷积作为消息传递算子。我们认为 BRITS (Cao et al., 2018) 是非对抗性深度自回归方法中的主要竞争替代方案，因为它与我们的方法具有架构相似性。作为额外的基线，我们考虑：1）MEAN，即使用节点级平均值进行插补； 2) KNN，即通过对邻接矩阵 Wt 中权重最高的 k = 10 个相邻节点的平均值进行插补； 3) MICE (White et al., 2011)，将最大迭代次数限制为 100，将最近特征数限制为 10； 4) 秩 = 10 的矩阵分解 (MF)； 5) VAR，即向量自回归单步预测器； 6) rGAIN，即 SSGAN (Miao et al., 2021) 的无监督版本，可以看作是具有双向循环编码器和解码器的 GAIN (Yoon et al., 2018a)； 7) MPGRU，一种基于 GNN 的单步预测器，类似于 DCRNN (Li et al., 2018)。


Table 2: Results on the traffic and smart grids datasets. Performance averaged over 5 runs
表 2：交通和智能电网数据集的结果。 5 次运行的平均性能
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220915124615.png)

We provide further comment and in depth details on baselines and datasets, together with additional experiments on synthetic data in the appendix.
我们提供了关于基线和数据集的进一步评论和深入细节，以及附录中关于合成数据的额外实验。


### 5.1 结果
Empirical results show that GRIN can achieve large improvements in imputation performance on several scenarios, as well as increased flexibility. In fact, differently from the other state-of-theart baselines, GRIN can handle input with a variable number of dimensions. Tab. 1 shows the experimental results on the air quality datasets. In the in-sample settings, we compute metrics using as imputation the value obtained by averaging predictions over all the overlapping windows; in the out-of-sample settings, instead, we simply report results by averaging the error over windows. GRIN largely outperforms other baselines on both settings. In particular, in the latter case, GRIN decreases MAE w.r.t. the closest baseline by more than 20% in AQI. Interestingly, GRIN consistently outperforms BRITS in imputing missing values also for sensors corresponding to isolated (disconnected) nodes, i.e., nodes corresponding to stations more than 40 km away from any other station (see B.1): this is empirical evidence of the positive regularizations encoded into GRIN. Our method achieves more accurate imputation also in the 36-dimensional dataset, where we could expect the graph representation to have a lower impact. Results for the traffic and smart grids datasets are shown in Tab. 2. In the traffic dataset, our method outperforms both BRITS and rGAIN by a wide margin in all the considered settings while using a much lower number of parameters (see A). In the traffic datasets, on average, GRIN reduces MAE by ≈ 29% w.r.t. BRITS and, in particular, in the Point missing setting of the PEMS-BAY dataset, the error is halved. In CER-E, GRIN consistently outperforms other baselines. Besides showing the effectiveness of our approach in a relevant application field, this experiment also goes to show that GRIN can be exploited in settings where relational information is not readily available.

实证结果表明，GRIN 可以在多种情况下大幅提高插补性能，并提高灵活性。事实上，与其他 state-of-theart 基线不同，GRIN 可以处理具有可变维数的输入。标签。图 1 显示了空气质量数据集的实验结果。在样本内设置中，我们使用通过对所有重叠窗口的平均预测获得的值作为插补来计算指标；相反，在样本外设置中，我们只是通过平均窗口上的误差来报告结果。 GRIN 在这两种设置上都大大优于其他基线。特别是在后一种情况下，GRIN 会降低 MAE w.r.t。 AQI 中最接近的基线超过 20%。有趣的是，对于与孤立（断开）节点相对应的传感器，GRIN 在估算缺失值方面的表现始终优于 BRITS，即，与任何其他站点相距 40 公里以上的站点对应的节点（见 B.1）：这是积极的经验证据编码为 GRIN 的正则化。我们的方法在 36 维数据集中也实现了更准确的插补，我们可以预期图形表示的影响较小。交通和智能电网数据集的结果显示在选项卡中。 2. 在流量数据集中，我们的方法在所有考虑的设置中都大大优于 BRITS 和 rGAIN，同时使用的参数数量要少得多（参见 A）。在交通数据集中，平均而言，GRIN 将 MAE 降低了 ≈ 29% w.r.t。 BRITS，特别是在 PEMS-BAY 数据集的点缺失设置中，误差减半。在 CER-E 中，GRIN 始终优于其他基线。除了展示我们的方法在相关应用领域的有效性之外，该实验还表明 GRIN 可以在关系信息不易获得的环境中被利用。

Finally, Tab. 3 show results – in terms of MAE – of an ablation study on the out-of-sample scenario in AQI, METR-LA (in the Block Missing settings), and CER-E (Point Missing setting). In particular, we compare GRIN against 3 baselines to assess the impact of the spatial decoder and of the bidirectional architecture. The first baseline is essentially a bidirectional MPGRU where values are imputed by a final MLP taking as inputs h fwd t−1 and h bwd t+1 , while the second one has an analogous architecture, but uses hidden representation and time step t (for both directions) and, thus, behaves similarly to a denoising autoencoder. As reference, we report the results of the unidirectional MPGRU. Results show that the components we introduce do contribute to significantly reduce imputation error. It is clear that spatial decoding and the bidirectional architecture are important to obtain accurate missing data reconstruction, especially in realistic settings with blocks of missing data. Interestingly, the denoising model suffers in the Block missing scenario, while, as one might expect, works well in the Point Missing setting. For additional results and discussion about scalability issues, we refer to the appendix of the paper

最后，标签。图 3 显示了 AQI、METR-LA（在块缺失设置中）和 CER-E（点缺失设置）中样本外场景的消融研究结果 - 就 MAE 而言。特别是，我们将 GRIN 与 3 个基线进行比较，以评估空间解码器和双向架构的影响。第一个基线本质上是一个双向 MPGRU，其中值由最终 MLP 估算，将 h fwd t−1 和 h bwd t+1 作为输入，而第二个基线具有类似的架构，但使用隐藏表示和时间步 t（对于两个方向），因此，其行为类似于去噪自动编码器。作为参考，我们报告了单向 MPGRU 的结果。结果表明，我们引入的组件确实有助于显着降低插补误差。很明显，空间解码和双向架构对于获得准确的缺失数据重建很重要，尤其是在具有缺失数据块的现实环境中。有趣的是，去噪模型在 Block Missing 场景中受到影响，而正如人们所预料的那样，在 Point Missing 设置中效果很好。有关可扩展性问题的其他结果和讨论，请参阅本文的附录

表 3：消融研究。 平均超过 5 次运行。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220915125053.png)


### 5.2 虚拟传感
As a final experiment, we provide a quantitative and qualitative assessment of the proposed method in virtual sensing. The idea (often studied in the context of kriging – see Section 2) is to simulate the presence of a sensor by adding a node with no available data and, then, let the model reconstruct the corresponding time series. Note that for the approach to work several assumptions are needed: 1) we have to assume that the physical quantity being monitored can be reconstructed from observations at neighboring sensors; 2) we should assume a high-degree of homogeneity of sensors (e.g., in the case of air quality stations we should assume that sensors are placed at the same height) or that the features characterizing each neighboring sensor (e.g., placement) are available to the model. In this context, it is worth noting that, due to the inductive biases embedded in the model, GRIN performs reconstruction not only by minimizing reconstruction error at the single node, but by regularizing the reconstructed value for imputation at neighboring sensors. We masked out observed values of the two nodes of AQI-36 with highest (station no. 1014) and lowest (no. 1031) connectivity, and trained GRIN on the remaining part of the data as usual. Results, in Fig. 3, qualitatively show that GRIN can infer the trend and scale for unseen sensors. In terms of MAE, GRIN scored 11.74 for sensor 1014 and 20.00 for sensor 1031 (averages over 5 independent runs).

作为最后的实验，我们对所提出的虚拟传感方法进行了定量和定性评估。这个想法（通常在克里金法的背景下研究——见第 2 节）是通过添加一个没有可用数据的节点来模拟传感器的存在，然后让模型重建相应的时间序列。请注意，该方法的工作需要几个假设：1）我们必须假设被监控的物理量可以从相邻传感器的观察中重建； 2）我们应该假设传感器的高度同质性（例如，在空气质量站的情况下，我们应该假设传感器放置在相同的高度）或表征每个相邻传感器的特征（例如，放置）是可用的到模型。在这种情况下，值得注意的是，由于模型中嵌入了归纳偏差，GRIN 不仅通过最小化单个节点的重建误差来执行重建，而且通过对相邻传感器处的插补的重建值进行正则化。我们掩盖了 AQI-36 的两个节点的观测值，具有最高（1014 号站）和最低（1031 号）连通性，并像往常一样在数据的剩余部分上训练 GRIN。结果，在图 3 中，定性地表明 GRIN 可以推断出看不见的传感器的趋势和规模。在 MAE 方面，传感器 1014 的 GRIN 得分为 11.74，传感器 1031 的得分为 20.00（5 次独立运行的平均值）。

Figure 3: Reconstruction of observations from sensors removed from the training set. Plots show that GRIN might be used for virtual sensing.
图 3：重建从训练集中移除的传感器的观察结果。 图表显示 GRIN 可能用于虚拟传感。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220915125346.png)


## 6 结论
We presented GRIN, a novel approach for MTSI exploiting modern graph neural networks. Our method imputes missing data by leveraging the relational information characterizing the underlying network of sensors and the functional dependencies among them. Compared against state-of-the-art baselines, our framework offers higher flexibility and achieves better reconstruction accuracy on all the considered scenarios. There are several possible directions for future works. From a theoretical perspective, it would be interesting to study the properties that would guarantee an accurate reconstruction. Furthermore, future work should study extensions able to deal with a non-stationary setting and further assess applications of GRIN in virtual and active sensing.

我们介绍了 GRIN，这是一种利用现代图神经网络的 MTSI 新方法。 我们的方法通过利用表征传感器底层网络的关系信息以及它们之间的功能依赖关系来估算丢失的数据。 与最先进的基线相比，我们的框架提供了更高的灵活性，并在所有考虑的场景中实现了更好的重建精度。 未来的工作有几个可能的方向。 从理论的角度来看，研究能够保证准确重建的特性会很有趣。 此外，未来的工作应该研究能够处理非静止环境的扩展，并进一步评估 GRIN 在虚拟和主动传感中的应用。


REPRODUCIBILITY STATEMENT再现性声明

Code to reproduce experiments presented in the paper is provided as supplementary material together with configuration files to replicate reported results. All datasets, except CER-E, are open and downloading links are provided in the supplementary material. The CER-E dataset can be obtained free of charge for research purposes (see appendix). For experiments where failures are simulated, we use random number generators with fixed seed for missing data generation to ensure reproducibility and consistency among experiments and baselines.

本文提供的重现实验的代码与配置文件一起作为补充材料提供，以复制报告的结果。 除 CER-E 外，所有数据集都是开放的，补充材料中提供了下载链接。 CER-E 数据集可以免费获得用于研究目的（见附录）。 对于模拟失败的实验，我们使用具有固定种子的随机数生成器来生成缺失数据，以确保实验和基线之间的可重复性和一致性。

ACKNOWLEDGMENTS
This research is funded by the Swiss National Science Foundation project 200021 172671: “ALPSFORT: A Learning graPh-baSed framework FOr cybeR-physical sysTems”. The authors wish to
thank the Institute of Computational Science at USI for granting access to computational resources.

致谢 本研究由瑞士国家科学基金会项目 200021 172671 资助：“ALPSFORT：网络物理系统的基于学习图的框架”。 作者希望感谢 USI 计算科学研究所授予对计算资源的访问权限。



## REFERENCES

[1]Gabriel Appleby, Linfeng Liu, and Li-Ping Liu. Kriging convolutional networks. In Proceedings of the AAAI Conference on Artificial Intelligence, volume 34, pp. 3187–3194, 2020.


[2]James Atwood and Don Towsley. Diffusion-convolutional neural networks. In Advances in neural information processing systems, pp. 1993–2001, 2016.


[3]Shaojie Bai, J Zico Kolter, and Vladlen Koltun. An empirical evaluation of generic convolutional and recurrent networks for sequence modeling. arXiv preprint arXiv:1803.01271, 2018.

[4]Peter W Battaglia, Jessica B Hamrick, Victor Bapst, Alvaro Sanchez-Gonzalez, Vinicius Zambaldi, Mateusz Malinowski, Andrea Tacchetti, David Raposo, Adam Santoro, Ryan Faulkner, et al. Relational inductive biases, deep learning, and graph networks. arXiv preprint arXiv:1806.01261, 2018.


[5]Lorenzo Beretta and Alessandro Santaniello. Nearest neighbor imputation algorithms: a critical evaluation. BMC medical informatics and decision making, 16(3):197–208, 2016.

[6]Michael M Bronstein, Joan Bruna, Yann LeCun, Arthur Szlam, and Pierre Vandergheynst. Geometric deep learning: going beyond euclidean data. IEEE Signal Processing Magazine, 34(4):18–42,2017.

[7]Deng Cai, Xiaofei He, Jiawei Han, and Thomas S Huang. Graph regularized nonnegative matrix factorization for data representation. IEEE transactions on pattern analysis and machine intelligence, 33(8):1548–1560, 2010.

[8]Ling Cai, Krzysztof Janowicz, Gengchen Mai, Bo Yan, and Rui Zhu. Traffic transformer: Capturing the continuity and periodicity of time series for traffic forecasting. Transactions in GIS, 24(3): 736–755, 2020.

[9]Wei Cao, Dong Wang, Jian Li, Hao Zhou, Yitan Li, and Lei Li. Brits: bidirectional recurrent imputation for time series. In Proceedings of the 32nd International Conference on Neural Information Processing Systems, pp. 6776–6786, 2018.

[10]Zhengping Che, Sanjay Purushotham, Kyunghyun Cho, David Sontag, and Yan Liu. Recurrent neural networks for multivariate time series with missing values. Scientific reports, 8(1):1–12,2018


[11]Kyunghyun Cho, Bart Van Merrienboer, Caglar Gulcehre, Dzmitry Bahdanau, Fethi Bougares, Hol- ¨ ger Schwenk, and Yoshua Bengio. Learning phrase representations using rnn encoder-decoder for statistical machine translation. arXiv preprint arXiv:1406.1078, 2014.

[12]Junyoung Chung, Caglar Gulcehre, KyungHyun Cho, and Yoshua Bengio. Empirical evaluation of gated recurrent neural networks on sequence modeling. arXiv preprint arXiv:1412.3555, 2014.

[13]Andrzej Cichocki and Anh-Huy Phan. Fast local algorithms for large scale nonnegative matrix and tensor factorizations. IEICE transactions on fundamentals of electronics, communications and computer sciences, 92(3):708–721, 2009.

[14]Commission for Energy Regulation. CER Smart Metering Project - Electricity Customer Behaviour Trial, 2009-2010 [dataset]. Irish Social Science Data Archive. SN: 0012-00, 2016. URL https: //www.ucd.ie/issda/data/commissionforenergyregulationcer.

[15]Michael Defferrard, Xavier Bresson, and Pierre Vandergheynst. Convolutional neural networks on ¨ graphs with fast localized spectral filtering. Advances in neural information processing systems, 29:3844–3852, 2016.

[16]James Durbin and Siem Jan Koopman. Time series analysis by state space methods. Oxford university press, 2012.

[17]William Fedus, Ian Goodfellow, and Andrew M Dai. Maskgan: Better text generation via filling in the . In International Conference on Learning Representations, 2018.

[18]Fabrizio Frasca, Emanuele Rossi, Davide Eynard, Benjamin Chamberlain, Michael Bronstein, and Federico Monti. Sign: Scalable inception graph neural networks. In ICML 2020 Workshop on Graph Representation Learning and Beyond, 2020.

[19]Zoubin Ghahramani and Michael Jordan. Supervised learning from incomplete data via an em approach. In J. Cowan, G. Tesauro, and J. Alspector (eds.), Advances in Neural Information Processing Systems, volume 6. Morgan-Kaufmann, 1994. Justin Gilmer, Samuel S Schoenholz, Patrick F Riley, Oriol Vinyals, and George E Dahl. Neural message passing for quantum chemistry. In International conference on machine learning, pp. 1263–1272. PMLR, 2017.

[20]Ian Goodfellow, Jean Pouget-Abadie, Mehdi Mirza, Bing Xu, David Warde-Farley, Sherjil Ozair, Aaron Courville, and Yoshua Bengio. Generative adversarial nets. Advances in neural information processing systems, 27, 2014.

[21]Ian Goodfellow, Yoshua Bengio, Aaron Courville, and Yoshua Bengio. Deep learning, volume 1. MIT Press, 2016.

[22]Clive WJ Granger. Investigating causal relations by econometric models and cross-spectral methods. Econometrica: journal of the Econometric Society, pp. 424–438, 1969. William L Hamilton, Rex Ying, and Jure Leskovec. Inductive representation learning on large graphs. In Proceedings of the 31st International Conference on Neural Information Processing Systems, pp. 1025–1035, 2017.

[23]Charles R Harris, K Jarrod Millman, Stefan J van der Walt, Ralf Gommers, Pauli Virtanen, David ´ Cournapeau, Eric Wieser, Julian Taylor, Sebastian Berg, Nathaniel J Smith, et al. Array programming with numpy. Nature, 585(7825):357–362, 2020.

[24]Sepp Hochreiter and Jurgen Schmidhuber. Long short-term memory. ¨ Neural computation, 9(8): 1735–1780, 1997. Hosagrahar V Jagadish, Johannes Gehrke, Alexandros Labrinidis, Yannis Papakonstantinou, Jignesh M Patel, Raghu Ramakrishnan, and Cyrus Shahabi. Big data and its technical challenges. Communications of the ACM, 57(7):86–94, 2014.

[25]J Kihoro, K Athiany, et al. Imputation of incomplete nonstationary seasonal time series data. Mathematical Theory and Modeling, 3(12):142–154, 2013.

[26]Thomas Kipf, Ethan Fetaya, Kuan-Chieh Wang, Max Welling, and Richard Zemel. Neural relational inference for interacting systems. In International Conference on Machine Learning, pp. 2688– 2697. PMLR, 2018.

[27]Sanmukh R. Kuppannagari, Yao Fu, Chung Ming Chueng, and Viktor K. Prasanna. Spatio-temporal missing data imputation for smart power grids. In Proceedings of the Twelfth ACM International Conference on Future Energy Systems, e-Energy ’21, pp. 458–465, New York, NY, USA, 2021.

[28]Association for Computing Machinery. ISBN 9781450383332. doi: 10.1145/3447555.3466586. URL https://doi.org/10.1145/3447555.3466586. Yann LeCun, Yoshua Bengio, and Geoffrey Hinton. Deep learning. nature, 521(7553):436–444, 2015.

[29]Yaguang Li, Rose Yu, Cyrus Shahabi, and Yan Liu. Diffusion convolutional recurrent neural network: Data-driven traffic forecasting. In International Conference on Learning Representations,2018.

### 30
[30]Yujia Li, Daniel Tarlow, Marc Brockschmidt, and Richard S. Zemel. Gated graph sequence neural networks. In International Conference on Learning Representations, 2016.

[31]Zachary C Lipton, David Kale, and Randall Wetzel. Directly modeling missing data in sequences with rnns: Improved classification of clinical time series. In Finale Doshi-Velez, Jim Fackler, David Kale, Byron Wallace, and Jenna Wiens (eds.), Proceedings of the 1st Machine Learning for Healthcare Conference, volume 56 of Proceedings of Machine Learning Research, pp. 253– 270, Northeastern University, Boston, MA, USA, 18–19 Aug 2016.

[32]PMLR. Roderick JA Little and Donald B Rubin. Statistical analysis with missing data, volume 793. John Wiley & Sons, 2019.

[33]Weifeng Liu, Puskal P Pokharel, and Jose C Principe. Correntropy: Properties and applications in non-gaussian signal processing. IEEE Transactions on signal processing, 55(11):5286–5298, 2007.

[34]Yukai Liu, Rose Yu, Stephan Zheng, Eric Zhan, and Yisong Yue. Naomi: Non-autoregressive multiresolution sequence imputation. Advances in Neural Information Processing Systems, 32: 11238–11248, 2019.

[35]Yonghong Luo, Xiangrui Cai, Ying Zhang, Jun Xu, and Yuan Xiaojie. Multivariate time series imputation with generative adversarial networks. In S. Bengio, H. Wallach, H. Larochelle, K. Grauman, N. Cesa-Bianchi, and R. Garnett (eds.), Advances in Neural Information Processing Systems, volume 31. Curran Associates, Inc., 2018.

[36]Yonghong Luo, Ying Zhang, Xiangrui Cai, and Xiaojie Yuan. E²gan: End-to-end generative adversarial network for multivariate time series imputation. In Proceedings of the Twenty-Eighth International Joint Conference on Artificial Intelligence, IJCAI-19, pp. 3094–3100. International Joint Conferences on Artificial Intelligence Organization, 7 2019.

[37]Jiali Mei, Yohann De Castro, Yannig Goude, and Georges Hebrail. Nonnegative matrix factorization ´ for time series recovery from a few temporal aggregates. In International Conference on Machine Learning, pp. 2382–2390. PMLR, 2017.

[38]Xiaoye Miao, Yangyang Wu, Jun Wang, Yunjun Gao, Xudong Mao, and Jianwei Yin. Generative semi-supervised learning for multivariate time series imputation. In Proceedings of the AAAI Conference on Artificial Intelligence, volume 35, pp. 8983–8991, 2021.

[39]Fulufhelo V Nelwamondo, Shakir Mohamed, and Tshilidzi Marwala. Missing data: A comparison of neural network and expectation maximization techniques. Current Science, pp. 1514–1521, 2007.

[40]neptune.ai. Neptune: Metadata store for mlops, built for research and production teams that run a lot of experiments, 2021. URL https://neptune.ai. Benjamin Paassen, Daniele Grattarola, Daniele Zambon, Cesare Alippi, and Barbara Eva Hammer. Graph edit networks. In International Conference on Learning Representations, 2020.

[41]Adam Paszke, Sam Gross, Francisco Massa, Adam Lerer, James Bradbury, Gregory Chanan, Trevor Killeen, Zeming Lin, Natalia Gimelshein, Luca Antiga, et al. Pytorch: An imperative style, high-performance deep learning library. Advances in neural information processing systems, 32: 8026–8037, 2019.

[42]Fabian Pedregosa, Gael Varoquaux, Alexandre Gramfort, Vincent Michel, Bertrand Thirion, Olivier ¨ Grisel, Mathieu Blondel, Peter Prettenhofer, Ron Weiss, Vincent Dubourg, et al. Scikit-learn: Machine learning in python. the Journal of machine Learning research, 12:2825–2830, 2011.

[43]Nikhil Rao, Hsiang-Fu Yu, Pradeep Ravikumar, and Inderjit S Dhillon. Collaborative filtering with graph information: Consistency and scalable methods. In NIPS, volume 2, pp. 7. Citeseer, 2015.

[44]Emanuele Rossi, Ben Chamberlain, Fabrizio Frasca, Davide Eynard, Federico Monti, and Michael Bronstein. Temporal graph networks for deep learning on dynamic graphs. arXiv preprint arXiv:2006.10637, 2020.

[45]Donald B Rubin. Inference and missing data. Biometrika, 63(3):581–592, 1976. Alex Rubinsteyn and Sergey Feldman. fancyimpute: An imputation library for python. URL https://github.com/iskandr/fancyimpute. Franco Scarselli, Marco Gori, Ah Chung Tsoi, Markus Hagenbuchner, and Gabriele Monfardini. The graph neural network model. IEEE transactions on neural networks, 20(1):61–80, 2008.

[46]Michael Schlichtkrull, Thomas N Kipf, Peter Bloem, Rianne Van Den Berg, Ivan Titov, and Max Welling. Modeling relational data with graph convolutional networks. In European semantic web conference, pp. 593–607. Springer, 2018.

[47]Jurgen Schmidhuber. Deep learning in neural networks: An overview. ¨ Neural networks, 61:85–117, 2015.

[48]Youngjoo Seo, Michael Defferrard, Pierre Vandergheynst, and Xavier Bresson. Structured sequence ¨ modeling with graph convolutional recurrent networks. In International Conference on Neural Information Processing, pp. 362–373. Springer, 2018.

[49]Chao Shang, Jie Chen, and Jinbo Bi. Discrete graph structure learning for forecasting multiple time series. In International Conference on Learning Representations, 2020.

[50]David I Shuman, Sunil K Narang, Pascal Frossard, Antonio Ortega, and Pierre Vandergheynst. The emerging field of signal processing on graphs: Extending high-dimensional data analysis to networks and other irregular domains. IEEE signal processing magazine, 30(3):83–98, 2013.

[51]Indro Spinelli, Simone Scardapane, and Aurelio Uncini. Missing data imputation with adversariallytrained graph convolutional networks. Neural Networks, 129:249–260, 2020.

[52]Michael L Stein. Interpolation of spatial data: some theory for kriging. Springer Science & Business Media, 1999. Olga Troyanskaya, Michael Cantor, Gavin Sherlock, Pat Brown, Trevor Hastie, Robert Tibshirani, David Botstein, and Russ B Altman. Missing value estimation methods for dna microarrays. Bioinformatics, 17(6):520–525, 2001.

[53]Guido Van Rossum and Fred L. Drake. Python 3 Reference Manual. CreateSpace, Scotts Valley, CA, 2009. ISBN 1441412697.

[54]Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N Gomez, Łukasz Kaiser, and Illia Polosukhin. Attention is all you need. In Advances in neural information processing systems, pp. 5998–6008, 2017.

[55]Ian R White, Patrick Royston, and Angela M Wood. Multiple imputation using chained equations: issues and guidance for practice. Statistics in medicine, 30(4):377–399, 2011.

[56]Yuankai Wu, Dingyi Zhuang, Aurelie Labbe, and Lijun Sun. Inductive graph neural networks for spatiotemporal kriging. arXiv preprint arXiv:2006.07527, 2020a.

[57]Zonghan Wu, Shirui Pan, Guodong Long, Jing Jiang, and Chengqi Zhang. Graph wavenet for deep spatial-temporal graph modeling. arXiv preprint arXiv:1906.00121, 2019.

[58]Zonghan Wu, Shirui Pan, Guodong Long, Jing Jiang, Xiaojun Chang, and Chengqi Zhang. Connecting the dots: Multivariate time series forecasting with graph neural networks. In Proceedings of the 26th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining, pp. 753–763, New York, NY, USA, 2020b. Association for Computing Machinery.

[59]Xiuwen Yi, Yu Zheng, Junbo Zhang, and Tianrui Li. St-mvl: Filling missing values in geo-sensory time series data. In Proceedings of the Twenty-Fifth International Joint Conference on Artificial Intelligence, IJCAI’16, pp. 2704–2710. AAAI Press, 2016. ISBN 9781577357704.

[60]Jinsung Yoon, James Jordon, and Mihaela Schaar. Gain: Missing data imputation using generative adversarial nets. In International Conference on Machine Learning, pp. 5689–5698. PMLR, 2018a.

[61]Jinsung Yoon, William R Zame, and Mihaela van der Schaar. Estimating missing data in temporal data streams using multi-directional recurrent neural networks. IEEE Transactions on Biomedical Engineering, 66(5):1477–1490, 2018b.

[62]Jiaxuan You, Xiaobai Ma, Daisy Yi Ding, Mykel Kochenderfer, and Jure Leskovec. Handling missing data with graph representation learning. Neural Information Processing Systems (NeurIPS, 2020.

[63]Bing Yu, Haoteng Yin, and Zhanxing Zhu. Spatio-temporal graph convolutional networks: A deep learning framework for traffic forecasting. arXiv preprint arXiv:1709.04875, 2017.

[64]Hsiang-Fu Yu, Nikhil Rao, and Inderjit S Dhillon. Temporal regularized matrix factorization for high-dimensional time series prediction. Advances in neural information processing systems, 29: 847–855, 2016.

[65]Daniele Zambon, Daniele Grattarola, Lorenzo Livi, and Cesare Alippi. Autoregressive models for sequences of graphs. In 2019 International Joint Conference on Neural Networks (IJCNN), pp. 1–8. IEEE, 2019.

[66]Jiani Zhang, Xingjian Shi, Junyuan Xie, Hao Ma, Irwin King, and Dit Yan Yeung. Gaan: Gated attention networks for learning on large and spatiotemporal graphs. In 34th Conference on Uncertainty in Artificial Intelligence 2018, UAI 2018, 2018.

[67]Yu Zheng, Licia Capra, Ouri Wolfson, and Hai Yang. Urban computing: concepts, methodologies, and applications. ACM Transactions on Intelligent Systems and Technology (TIST), 5(3):1–55, 2014.

[68]Yu Zheng, Xiuwen Yi, Ming Li, Ruiyuan Li, Zhangqing Shan, Eric Chang, and Tianrui Li. Forecasting fine-grained air quality based on big data. In Proceedings of the 21th ACM SIGKDD international conference on knowledge discovery and data mining, pp. 2267–2276, 2015.


### 中
[1] 加布里埃尔·阿普尔比、刘林峰和刘丽萍。克里金卷积网络。在 AAAI 人工智能会议论文集中，第 34 卷，第 3187-3194 页，2020 年。

[2]詹姆斯·阿特伍德和唐·托斯利。扩散卷积神经网络。在神经信息处理系统的进展中，第 1993-2001 页，2016 年。

[3]Shaojie Bai、J Zico Kolter 和 Vladlen Koltun。用于序列建模的通用卷积和循环网络的经验评估。 arXiv 预印本 arXiv:1803.01271, 2018。

[4]Peter W Battaglia、Jessica B Hamrick、Victor Bapst、Alvaro Sanchez-Gonzalez、Vinicius Zambaldi、Mateusz Malinowski、Andrea Tacchetti、David Raposo、Adam Santoro、Ryan Faulkner 等人。关系归纳偏差、深度学习和图网络。 arXiv 预印本 arXiv:1806.01261, 2018。

[5]洛伦佐·贝雷塔和亚历山德罗·桑塔尼洛。最近邻插补算法：关键评估。 BMC 医学信息学和决策，16(3):197–208, 2016。

[6]迈克尔·M·布朗斯坦、琼·布鲁纳、扬·勒昆、亚瑟·斯拉姆和皮埃尔·范德盖恩斯特。几何深度学习：超越欧几里得数据。 IEEE 信号处理杂志，34(4):18–42,2017。

[7] 邓才、何晓飞、韩家伟、Thomas S Huang。用于数据表示的图形正则化非负矩阵分解。 IEEE 模式分析和机器智能交易，33(8):1548–1560, 2010。

[8] 蔡玲，Krzysztof Janowicz，Gengchen Mai，Bo Yan，Rui Zhu。交通变换器：捕获时间序列的连续性和周期性以进行交通预测。 GIS 交易，24(3): 736–755, 2020。

[9] 曹伟，王东，李健，周浩，李一潭，李磊。英国人：时间序列的双向循环插补。在第 32 届神经信息处理系统国际会议论文集上，第 6776-6786 页，2018 年。

[10]Zhengping Che、Sanjay Purushotham、Kyunghyun Cho、David Sontag 和 Yan Liu。具有缺失值的多元时间序列的递归神经网络。科学报告，8（1）：1-12，

[11] Kyunghyun Cho、Bart Van Merrienboer、Caglar Gulcehre、Dzmitry Bahdanau、Fethi Bougares、Hol-ger Schwenk 和 Yoshua Bengio。使用 rnn 编码器-解码器学习短语表示以进行统计机器翻译。 arXiv 预印本 arXiv:1406.1078, 2014。

[12] Junyoung Chung、Caglar Gulcehre、KyungHyun Cho 和 Yoshua Bengio。门控循环神经网络对序列建模的实证评估。 arXiv 预印本 arXiv:1412.3555, 2014。

[13]Andrzej Cichocki 和 Anh-Huy Phan。用于大规模非负矩阵和张量分解的快速局部算法。 IEICE 电子、通信和计算机科学基础交易，92(3):708–721, 2009。

[14]能源监管委员会。 CER 智能计量项目 - 电力客户行为试验，2009-2010 [数据集]。爱尔兰社会科学数据档案。 SN: 0012-00, 2016. URL https://www.ucd.ie/issda/data/commissionforenergyregulationcer。

[15]迈克尔·德弗拉德、泽维尔·布列松和皮埃尔·范德盖恩斯特。图上的卷积神经网络具有快速局部谱滤波。神经信息处理系统的进展，29:3844-3852，2016。

[16]詹姆斯·德宾和西姆·扬·库普曼。通过状态空间方法进行时间序列分析。牛津大学出版社，2012 年。

[17]威廉·费杜斯、伊恩·古德费罗和安德鲁·M·戴。 Maskgan：通过填写更好的文本生成。在 2018 年国际学习代表大会上。

[18]Fabrizio Frasca、Emanuele Rossi、Davide Eynard、Benjamin Chamberlain、Michael Bronstein 和 Federico Monti。标志：可扩展的初始图神经网络。 2020 年 ICML 2020 图形表示学习及超越研讨会。

[19]佐宾·加赫拉马尼和迈克尔·乔丹。通过 em 方法从不完整的数据中进行监督学习。载于 J. Cowan、G. Tesauro 和 J. Alspector (eds.)，《神经信息处理系统进展》，第 6 卷。Morgan-Kaufmann，1994 年。Justin Gilmer、Samuel S Schoenholz、Patrick F Riley、Oriol Vinyals 和 George E达尔。量子化学的神经信息传递。在国际机器学习会议上，第 1263-1272 页。 2017 年，PMLR。

[20]Ian Goodfellow、Jean Pouget-Abadie、Mehdi Mirza、Bing Xu、David Warde-Farley、Sherjil Ozair、Aaron Courville 和 Yoshua Bengio。生成对抗网络。神经信息处理系统的进展，27，2014。

[21]伊恩·古德费罗、约书亚·本吉奥、亚伦·库维尔和约书亚·本吉奥。深度学习，第 1 卷。麻省理工学院出版社，2016 年。

[22]克莱夫·W·J·格兰杰。通过计量经济学模型和交叉光谱法研究因果关系。计量经济学：计量经济学会杂志，第 424-438 页，1969 年。William L Hamilton、Rex Ying 和 Jure Leskovec。大图上的归纳表示学习。在第 31 届神经信息处理系统国际会议论文集上，第 1025-1035 页，2017 年。

[23]查尔斯·R·哈里斯、K·贾罗德·米尔曼、斯特凡·J·范德沃尔特、拉尔夫·戈默斯、保利·维尔塔宁、大卫·库尔纳 peau、Eric Wieser、Julian Taylor、Sebastian Berg、Nathaniel J Smith 等人。使用 numpy 进行数组编程。自然，585（7825）：357–362，2020。

[24]Sepp Hochreiter 和 Jurgen Schmidhuber。长短期记忆。 ¨ 神经计算，9(8): 1735–1780, 1997。Hosagrahar V Jagadish、Johannes Gehrke、Alexandros Labrinidis、Yannis Papakonstantinou、Jignesh M Patel、Raghu Ramakrishnan 和 Cyrus Shahabi。大数据及其技术挑战。 ACM 通讯，57(7):86–94, 2014。

[25]J Kihoro、K Athiany 等人。不完整的非平稳季节性时间序列数据的插补。数学理论与建模，3（12）：142-154，2013。

[26]Thomas Kipf、Ethan Fetaya、Kuan-Chieh Wang、Max Welling 和 Richard Zemel。交互系统的神经关系推理。在机器学习国际会议上，第 2688-2697 页。PMLR，2018。

[27]Sanmukh R. Kuppannagari、Yao Fu、Chung Ming Chueng 和 Viktor K. Prasanna。智能电网时空缺失数据插补。在第十二届 ACM 未来能源系统国际会议论文集中，e-Energy '21，第 458-465 页，美国纽约州纽约市，2021 年。

[28]计算机协会。国际标准书号 9781450383332。doi：10.1145/3447555.3466586。网址 https://doi.org/10.1145/3447555.3466586。 Yann LeCun、Yoshua Bengio 和 Geoffrey Hinton。深度学习。自然，521（7553）：436–444，2015。

[29]李亚光、余蔷薇、赛勒斯·沙哈比、刘艳。扩散卷积递归神经网络：数据驱动的交通预测。在 2018 年国际学习代表大会上。
### 30
[30]李玉嘉、丹尼尔·塔洛、马克·布罗克施密特和理查德·S·泽梅尔。门控图序列神经网络。在国际学习代表大会上，2016。

[31]扎卡里·C·立顿、大卫·凯尔和兰德尔·韦策尔。使用 rnns 直接建模序列中的缺失数据：改进的临床时间序列分类。在 Finale Doshi-Velez、Jim Fackler、David Kale、Byron Wallace 和 Jenna Wiens（编辑），第一届机器学习医疗会议论文集，机器学习研究论文集第 56 卷，第 253-270 页，东北大学，美国马萨诸塞州波士顿，2016 年 8 月 18 日至 19 日。

[32]PMLR。罗德里克 JA 利特尔和唐纳德 B 鲁宾。缺失数据的统计分析，第 793 卷。John Wiley & Sons，2019 年。

[33] 刘伟峰、Puskal P Pokharel 和 Jose C Principe。 Correntropy：非高斯信号处理中的属性和应用。 IEEE 信号处理汇刊，55(11):5286–5298, 2007。

[34] 刘玉凯、于蔷薇、Stephan Zheng、Eric Zhan 和 Yisong Yue。 Naomi：非自回归多分辨率序列插补。神经信息处理系统的进展，32：11238-11248，2019。

[35]罗永红，蔡祥瑞，张颖，徐俊，袁晓杰。具有生成对抗网络的多元时间序列插补。载于 S. Bengio、H. Wallach、H. Larochelle、K. Grauman、N. Cesa-Bianchi 和 R. Garnett（编辑），神经信息处理系统进展，第 31 卷。Curran Associates, Inc.，2018 年。

[36]罗永红，张颖，蔡祥瑞，袁小杰。 E²gan：用于多元时间序列插补的端到端生成对抗网络。在第二十八届国际人工智能联合会议论文集上，IJCAI-19，第 3094-3100 页。国际人工智能组织联合会议，2019 年 7 月。

[37] 梅佳丽、约汉·德·卡斯特罗、雅尼格·古德和乔治·赫伯瑞尔。非负矩阵分解'用于从几个时间聚合中恢复时间序列。在机器学习国际会议上，第 2382-2390 页。 2017 年，PMLR。

[38] 苗小野，吴阳阳，王军，高云军，毛旭东，尹建伟。用于多元时间序列插补的生成式半监督学习。在 AAAI 人工智能会议论文集中，第 35 卷，第 8983-8991 页，2021 年。

[39]Fulufhelo V Nelwamondo、Shakir Mohamed 和 Tshilidzi Marwala。缺失数据：神经网络和期望最大化技术的比较。 《当代科学》，第 1514-1521 页，2007 年。

[40]海王星.ai。 Neptune：用于 mlops 的元数据存储，专为进行大量实验的研究和生产团队打造，2021 年。网址 https://neptune.ai。本杰明·帕森、丹尼尔·格拉塔罗拉、丹尼尔·赞邦、切萨雷·阿利皮和芭芭拉·伊娃·哈默。图形编辑网络。在 2020 年国际学习代表大会上。

[41]Adam Paszke、Sam Gross、Francisco Massa、Adam Lerer、James Bradbury、Gregory Chanan、Trevor Killeen、Zeming Lin、Natalia Gimelshein、Luca Antiga 等。 Pytorch：命令式风格的高性能深度学习库。神经信息处理系统的进展，32：8026-8037，2019。

[42]Fabian Pedregosa、Gael Varoquaux、Alexandre Gramfort、Vincent Michel、Bertrand Thirion、Olivier ¨ Grisel、Mathieu Blondel、Peter Prettenhofer、Ron Weiss、Vincent Dubourg 等。 Scikit-learn：python 中的机器学习。机器学习研究杂志，12:2825–2830，2011。

[43]尼基l Rao、Hsiang-Fu Yu、Pradeep Ravikumar 和 Inderjit S Dhillon。具有图信息的协同过滤：一致性和可扩展方法。在 NIPS，第 2 卷，第 7 页。Citeseer，2015。

[44]伊曼纽尔·罗西、本·张伯伦、法布里齐奥·弗拉斯卡、大卫·艾纳德、费德里科·蒙蒂和迈克尔·布朗斯坦。用于动态图深度学习的时间图网络。 arXiv 预印本 arXiv:2006.10637, 2020。

[45]唐纳德·B·鲁宾。推理和缺失数据。 Biometrika, 63(3):581–592, 1976. Alex Rubinsteyn 和 Sergey Feldman。 fancyimpute：python 的插补库。网址 https://github.com/iskandr/fancyimpute。 Franco Scarselli、Marco Gori、Ah Chung Tsoi、Markus Hagenbuchner 和 Gabriele Monfardini。图神经网络模型。 IEEE 神经网络交易，20(1):61–80, 2008。

[46]Michael Schlichtkrull、Thomas N Kipf、Peter Bloem、Rianne Van Den Berg、Ivan Titov 和 Max Welling。使用图卷积网络对关系数据进行建模。在欧洲语义网络会议上，第 593-607 页。斯普林格，2018。

[47]尤尔根·施米德胡伯。神经网络中的深度学习：概述。 ¨ 神经网络，61:85–117，2015。

[48]Youngjoo Seo、Michael Defferrard、Pierre Vandergheynst 和 Xavier Bresson。结构化序列 - 使用图卷积循环网络建模。在神经信息处理国际会议上，第 362-373 页。斯普林格，2018。

[49] 尚超，陈洁，毕金波。用于预测多个时间序列的离散图结构学习。在 2020 年国际学习代表大会上。

[50]大卫·舒曼、苏尼尔·K·纳朗、帕斯卡·弗罗萨德、安东尼奥·奥尔特加和皮埃尔·范德盖恩斯特。图信号处理的新兴领域：将高维数据分析扩展到网络和其他不规则领域。 IEEE 信号处理杂志，30(3):83–98，2013。

[51]因德罗·斯皮内利、西蒙娜·斯卡达潘和奥雷里奥·安奇尼。对抗训练的图卷积网络缺少数据插补。神经网络，129：249–260，2020。

[52]迈克尔·L·斯坦。空间数据插值：克里金法的一些理论。 Springer Science & Business Media，1999 年。Olga Troyanskaya、Michael Cantor、Gavin Sherlock、Pat Brown、Trevor Hastie、Robert Tibshirani、David Botstein 和 Russ B Altman。 dna 微阵列的缺失值估计方法。生物信息学，17（6）：520–525，2001。

[53]Guido Van Rossum 和 Fred L. Drake。 Python 3 参考手册。 CreateSpace，加利福尼亚州斯科茨谷，2009 年。ISBN 1441412697。

[54]Ashish Vaswani、Noam Shazeer、Niki Parmar、Jakob Uszkoreit、Llion Jones、Aidan N Gomez、Łukasz Kaiser 和 Illia Polosukhin。注意力就是你所需要的。在神经信息处理系统的进展中，第 5998-6008 页，2017 年。

[55]伊恩·R·怀特、帕特里克·罗伊斯顿和安吉拉·M·伍德。使用链式方程的多重插补：问题和实践指南。医学统计，30(4):377–399，2011。

[56] 吴元凯、庄丁一、奥蕾莉·拉贝、孙丽君。用于时空克里金法的归纳图神经网络。 arXiv 预印本 arXiv:2006.07527, 2020a。

[57] 吴宗涵、潘诗瑞、龙国栋、姜静、张承启。用于深度时空图建模的图波网。 arXiv 预印本 arXiv:1906.00121, 2019。

[58] 吴宗翰、潘诗瑞、龙国栋、姜静、常晓军、张承启。连接点：使用图神经网络进行多变量时间序列预测。在第 26 届 ACM SIGKDD 知识发现和数据挖掘国际会议论文集中，第 753-763 页，纽约，纽约，美国，2020b。计算机协会。

[59]易修文，于正，张俊波，李天瑞。 St-mvl：填充地理感知时间序列数据中的缺失值。在第二十五届人工智能国际联合会议论文集上，IJCAI'16，第 2704-2710 页。 AAAI 出版社，2016 年。国际标准书号 9781577357704。

[60]Jinsung Yoon、James Jordon 和 Mihaela Schaar。增益：使用生成对抗网络缺失数据插补。在机器学习国际会议上，第 5689-5698 页。 PMLR，2018a。

[61]Jinsung Yoon、William R Zame 和 Mihaela van der Schaar。使用多向递归神经网络估计时间数据流中的缺失数据。 IEEE 生物医学工程汇刊，66(5):1477–1490, 2018b。

[62] 尤嘉轩、马晓白、Daisy Yi Ding、Mykel Kochenderfer 和 Jure Leskovec。使用图表示学习处理缺失数据。神经信息处理系统（NeurIPS，2020。

[63] 于冰，尹浩腾，朱占兴。时空图卷积网络：用于交通预测的深度学习框架。 arXiv 预印本 arXiv:1709.04875, 2017。

[64] Hsiang-Fu Yu、Nikhil Rao 和 Inderjit S Dhillon。用于高维时间序列预测的时间正则化矩阵分解。神经信息处理系统的进展，29：847-855，2016。

[65]Daniele Zambon、Daniele Grattarola、Lorenzo Livi 和 Cesare Alippi。图序列的自回归模型。 2019年国际神经网络联合会议 (IJCNN)，第 1-8 页。 IEEE，2019。

[66]张佳妮、施行健、谢军源、马浩、欧文·金、杨迪特·扬。 Gaan：用于学习大型时空图的门控注意网络。 在 2018 年第 34 届人工智能不确定性会议上，UAI 2018，2018。

[67]于正、莉西亚·卡普拉、欧里·沃尔夫森和海洋。 城市计算：概念、方法和应用。 ACM 智能系统和技术交易 (TIST)，5(3):1-55，2014。

[68] 于正，易修文，李明，李瑞源，张清山，Eric Chang，李天瑞。 基于大数据的细粒度空气质量预测。 在第 21 届 ACM SIGKDD 知识发现和数据挖掘国际会议论文集上，第 2267-2276 页，2015 年。


## 附录


### A DETAILED EXPERIMENTAL SETTINGS详细的实验设置

In this appendix, we give more details on the experimental settings used to evaluate our approach.
We train all the models by sampling at random 160 batches of 32 elements for each epoch, we fix the
maximum number of epochs to 300 and we use early stopping on the validation set with a patience
of 40 epochs. All methods are trained using a cosine learning rate scheduler with initial value of
0.001, decayed over the 300 training epochs. During training, we randomly mask out an additional
5% of the input data for each batch to foster robustness to noise and missing data.

在本附录中，我们提供了有关用于评估我们的方法的实验设置的更多详细信息。 我们通过对每个 epoch 随机抽样 160 批 32 个元素来训练所有模型，我们将最大 epoch 数固定为 300，并且我们在验证集上使用提前停止，耐心为 40 个 epoch。 所有方法都使用余弦学习率调度程序进行训练，初始值为 0.001，在 300 个训练时期内衰减。 在训练过程中，我们随机屏蔽了每批额外 5% 的输入数据，以增强对噪声和缺失数据的鲁棒性。

For GRIN we minimize the following loss function is
对于 GRIN，我们最小化以下损失函数是


$$
\begin{aligned}
\mathcal{L} &=\mathcal{L}\left(\boldsymbol{Y}_{[t, t+T]}, \boldsymbol{X}_{[t, t+T]}, \boldsymbol{M}_{[t, t+T]}\right) \\
&+\mathcal{L}\left(\boldsymbol{Y}_{[t, t+T]}^{(1), f w d}, \boldsymbol{X}_{[t, t+T]}, \boldsymbol{M}_{[t, t+T]}\right)+\mathcal{L}\left(\boldsymbol{Y}_{[t, t+T]}^{(2), f w d}, \boldsymbol{X}_{[t, t+T]}, \boldsymbol{M}_{[t, t+T]}\right) \\
&+\mathcal{L}\left(\boldsymbol{Y}_{[t, t+T]}^{(1), b w d}, \boldsymbol{X}_{[t, t+T]}, \boldsymbol{M}_{[t, t+T]}\right)+\mathcal{L}\left(\boldsymbol{Y}_{[t, t+T]}^{(2), b w d}, \boldsymbol{X}_{[t, t+T]}, \boldsymbol{M}_{[t, t+T]}\right),
\end{aligned}
$$


where each L( · , · , · ) is of the form of Eq. 1 and the element-wise error function is MAE. Note
that here we are using X[t,t+T] and M[t,t+T]
instead of Xc[t,t+T] and M[t,t+T]

其中每个 L( · , · , · ) 是等式的形式。 1 元素级误差函数为 MAE。 请注意，这里我们使用 X[t,t+T] 和 M[t,t+T] 而不是 ${\widehat{X}[t,t+T]}$ 和 ${\overline{M}[t,t+T]}$



For BRITS, we use the same network hyperparameters of Cao et al. (2018) for the AQI-36 dataset.
To account for the larger input dimension, for the other datasets we increase the number of hidden
neurons in the RNNs cells to 128 for AQI/METR-LA and 256 for PEMS-BAY/CER-E. The number
of neurons was tuned on the validation sets. For rGAIN we use the same number of units in the
cells of the bidirectional RNN used by BRITS, but we concatenate a random vector (sampled from
a uniform distribution) of dimension z = 4 to the input vector in order to model the sampling of
the data generating process. To obtain predictions, we average out the outputs of k = 5 forward
passes. For VAR we used an order of 5 and trained the model with SGD. Since the VAR model
needs past 5 observations to predict the next step, we pad each sequence using the mean for each
channel. Here we used a batch size to 64 and a learning rate of 0.0005. The order was selected with
a small search in the range [2, 12]: we found out a window size of 5 to be ideal for all the considered
datasets. For GRIN we use the same hyperparameters in all the datasets: a hidden dimension of
64 neurons for both the spatio-temporal encoder and the spatial decoder and of 64 neurons for the
MLP. We use diffusion convolution as message-passing operation, with a diffusion step k = 2 in
the spatio-temporal encoder and k = 1 in the temporal decoder. Note that, due to the architectural
differences, the other neural network baselines have a number of parameters that is far higher than
GRIN (depending on the considered dataset, up to ≈ 4M against ≈ 200K). For MPGRU we use
the same hyperparameters of GRIN (64 units for both the spatio-temporal encoder and the decoder)



对于 BRITS，我们使用 Cao 等人的相同网络超参数。 (2018) 用于 AQI-36 数据集。为了考虑更大的输入维度，对于其他数据集，我们将 RNN 细胞中隐藏神经元的数量增加到 AQI/METR-LA 的 128 个和 PEMS-BAY/CER-E 的 256 个。在验证集上调整神经元的数量。对于 rGAIN，我们在 BRITS 使用的双向 RNN 的单元中使用相同数量的单元，但我们将维度 z = 4 的随机向量（从均匀分布中采样）连接到输入向量，以便对数据生成过程。为了获得预测，我们平均了 k = 5 次前向传递的输出。对于 VAR，我们使用 5 阶并使用 SGD 训练模型。由于 VAR 模型需要过去 5 次观察来预测下一步，我们使用每个通道的平均值填充每个序列。在这里，我们使用了 64 的批大小和 0.0005 的学习率。该顺序是通过在 [2, 12] 范围内的小搜索选择的：我们发现窗口大小为 5 对所有考虑的数据集来说都是理想的。对于 GRIN，我们在所有数据集中使用相同的超参数：时空编码器和空间解码器的 64 个神经元的隐藏维度和 MLP 的 64 个神经元的隐藏维度。我们使用扩散卷积作为消息传递操作，在时空编码器中扩散步长 k = 2，在时间解码器中扩散步长 k = 1。请注意，由于架构差异，其他神经网络基线有许多参数远远高于 GRIN（取决于所考虑的数据集，最高 ≈ 4M 对 ≈ 200K）。对于 MPGRU，我们使用相同的 GRIN 超参数（时空编码器和解码器均为 64 个单元）



For data processing we use the same steps of Li et al. (2018), data are normalized across the feature dimension (which means graph-wise for GRIN and node-wise for BRITS/rGAIN/VAR). Data
masked out for evaluation are never used to train any model

对于数据处理，我们使用 Li 等人的相同步骤。 （2018 年），数据在特征维度上进行了标准化（这意味着 GRIN 的图形方式和 BRITS/rGAIN/VAR 的节点方式）。 为评估而屏蔽的数据永远不会用于训练任何模型



All the models were developed in Python (Van Rossum & Drake, 2009) using the following opensource libraries:

所有模型都是使用以下开源库在 Python 中开发的（Van Rossum & Drake，2009）：

- PyTorch (Paszke et al., 2019);
- numpy (Harris et al., 2020);
- Neptune1
(neptune.ai, 2021);
- scikit-learn (Pedregosa et al., 2011);
- fancyimpute (Rubinsteyn & Feldman

The implementation of the diffusion convolutional operator was adapted from the Graph-WaveNet
codebase 2
. For the implementation of BRITS, we used the code provided by the authors3
. The code
to reproduce the experiments of the paper is available online4

扩散卷积算子的实现改编自 Graph-WaveNet 代码库 2 。 对于 BRITS 的实现，我们使用了作者提供的代码3。 重现论文实验的代码可在线获取4

Table 4: Statistics on adjacency matrices used in the experiments. Self loops are excluded.
表 4：实验中使用的邻接矩阵的统计数据。 自环被排除在外。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009164910.png)

### B DATASETSB 数据集

In this appendix, we provide more details on the datasets that we used to run experiments. Tab. 4
shows detailed statistics for graph structure associated with each dataset, while Fig. 4 shows the
corresponding adjacency matrices. Tab. 5 shows missing data statistics. In the following subsections,
we go deeper into details for each dataset

在本附录中，我们提供了有关我们用于运行实验的数据集的更多详细信息。 标签。 图 4 显示了与每个数据集相关的图结构的详细统计数据，而图 4 显示了相应的邻接矩阵。 标签。 图 5 显示了缺失数据统计。 在以下小节中，我们将更深入地了解每个数据集的详细信息

Table 5: Statistics on missing data distribution. (P) and (B) indicate the Point Missing and Block
Missing settings, respectively. With block, we refer to missing data bursts longer than 2 time steps
and shorter than or equal to 48.

表 5：缺失数据分布统计。 (P) 和 (B) 分别表示点缺失和块缺失设置。 对于块，我们指的是大于 2 个时间步长且小于或等于 48 个时间步长的丢失数据突发。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009165129.png)


#### B.1 AIR QUALITY

Air pollution is nowadays a ubiquitous problem. The Urban Computing project (Zheng et al., 2014;
2015) published several datasets containing real measurements of different indices affecting human
life in urban spaces. We consider as benchmark the dataset regarding the air quality index (AQI).
The complete dataset contains hourly measurements of six pollutants from 437 air quality monitoring stations, spread over 43 cities in China, over a period of one year (from May 2014 to April
2015). Prior works on imputation (Yi et al., 2016; Cao et al., 2018) considered a reduced version of
this dataset, including only 36 sensors (AQI-36). This dataset is particularly interesting as a benchmark for imputation due to the high rate of missing values (25.7% in AQI and 13.2% in AQI-36).
Along with Yi et al. (2016), we consider as the test set the months of March, June, September and
December. We consider both the in-sample and out-of-sample scenarios. In latter case, we do not
consider windows overlapping with any of the test months. We use the same procedure of Yi et al.
(2016) to simulate the presence of missing data for evaluation.

空气污染是当今普遍存在的问题。城市计算项目 (Zheng et al., 2014; 2015) 发布了几个数据集，其中包含影响城市空间中人类生活的不同指标的真实测量结果。我们将有关空气质量指数（AQI）的数据集作为基准。完整的数据集包含在一年（2014 年 5 月至 2015 年 4 月）期间对分布在中国 43 个城市的 437 个空气质量监测站的六种污染物的每小时测量值。先前的插补工作（Yi 等人，2016；Cao 等人，2018）考虑了该数据集的简化版本，仅包括 36 个传感器（AQI-36）。由于高缺失值率（AQI 为 25.7%，AQI-36 为 13.2%），该数据集作为插补基准特别有趣。与易等人一起。 (2016)，我们将 3 月、6 月、9 月和 12 月视为测试集。我们同时考虑样本内和样本外的情况。在后一种情况下，我们不考虑与任何测试月份重叠的窗口。我们使用与 Yi 等人相同的程序。 （2016）模拟缺失数据的存在进行评估。

1https://neptune.ai
2https://github.com/nnzhan/Graph-WaveNet
3https://github.com/caow13/BRITS
4https://github.com/Graph-Machine-Learning-Group/grin

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009165400.png)

Figure 4: Adjacency matrices of the different datasets.

图 4：不同数据集的邻接矩阵。

We select windows of data of length T = 24 for AQI and T = 36 for AQI-36 (in line with Cao et al.
(2018)). To evaluate the imputation performances, we mask out from the test set and use as groundtruth the value x
i,j
t
if: (1) the value is not missing (m
i,j
t = 1) and (2) the value is missing at the same
hour and day in the following month. Besides air quality readings, the dataset provides geographic
coordinates of each monitoring station. To obtain an adjacency matrix from the geographic distances
between nodes, we use a thresholded Gaussian kernel (Shuman et al., 2013): the weight w
i,j
t = w
i,j
of the edge connecting i-th and j-th node is

我们为 AQI 选择长度为 T = 24 的数据窗口，为 AQI-36 选择长度为 T = 36 的数据窗口（与 Cao 等人（2018）一致）。 为了评估插补性能，我们从测试集中屏蔽并使用值 x i,j t 作为基本事实，如果：(1) 值没有丢失 (m i,j t = 1) 和 (2) 值同时丢失 下个月的小时和天。 除了空气质量读数，数据集还提供每个监测站的地理坐标。 为了从节点之间的地理距离获得邻接矩阵，我们使用阈值高斯核 (Shuman et al., 2013)：连接第 i 个和第 j 个节点的边的权重 w i,j t = w i,j 为


$$
w^{i, j}=\left\{\begin{array}{cl}
\exp \left(-\frac{\operatorname{dist}(i, j)^2}{\gamma}\right) & \operatorname{dist}(i, j) \leq \delta \\
0 & \text { otherwise }
\end{array}\right.
$$

where dist ( · , · ) is the geographical distance operator, γ controls the width of the kernel and δ
is the threshold. We set γ to the standard deviation of geographical distances in AQI-36 in both
datasets. We set δ so that it corresponds to a distance of ≈ 40 km.

其中 dist ( · , · ) 是地理距离算子，γ 控制内核的宽度，δ 是阈值。 我们将 γ 设置为两个数据集中 AQI-36 中地理距离的标准差。 我们设置 δ 使其对应于 ≈ 40 km 的距离。

#### B.2 TRAFFIC流量
The study of traffic networks is key for the development of intelligent transportation systems and
a relevant application field for network science. While previous works (Yu et al., 2017; Li et al.,
2018; Wu et al., 2019; Shang et al., 2020) have assessed spatio-temporal deep learning methods for
the traffic forecasting task, we focus on reconstruction. We use as benchmark the PEMS-BAY and
METR-LA datasets from Li et al. (2018). PEMS-BAY contains 6 months of data from 325 traffic
sensors in San Francisco Bay Area, while METR-LA contains 4 months of sensor readings from
207 detectors in the Los Angeles County Highway (Jagadish et al., 2014); for both datasets, the
sampling rate corresponds to 5 minutes.

交通网络研究是智能交通系统发展的关键，也是网络科学的相关应用领域。 虽然之前的工作（Yu et al., 2017; Li et al., 2018; Wu et al., 2019; Shang et al., 2020）已经评估了交通预测任务的时空深度学习方法，但我们专注于重建 . 我们使用来自 Li 等人的 PEMS-BAY 和 METR-LA 数据集作为基准。 （2018 年）。 PEMS-BAY 包含来自旧金山湾区 325 个交通传感器的 6 个月数据，而 METR-LA 包含来自洛杉矶县高速公路 207 个探测器的 4 个月传感器读数（Jagadish 等人，2014 年）； 对于这两个数据集，采样率对应于 5 分钟。

We use input sequences of 24 steps, which correspond to 2 hours of data. For adjacency, we use
a thresholded Gaussian kernel applied to geographic distances following previous works Wu et al.
(2019). We split the data into three folds, using 70% of them for training and the remaining 10%
and 20% for validation and testing, respectively.

我们使用 24 步的输入序列，对应于 2 小时的数据。 对于邻接，我们使用阈值高斯核应用于地理距离，遵循之前的工作 Wu 等人。 （2019）。 我们将数据分为三部分，其中 70% 用于训练，其余 10% 和 20% 分别用于验证和测试。


#### B.3 SMART GRIDS智能电网


We consider data from the Irish Commission for Energy Regulation (CER) Smart Metering Project5
.
We select only the subset of the available smart meters monitoring energy consumption of small and
medium-sized enterprises (SMEs), i.e., 485 time series with samples acquired every 30 minutes.
Note that access to dataset can be obtained free of charge for research purposes.

我们考虑了来自爱尔兰能源监管委员会 (CER) 智能计量项目的数据5。 我们仅选择监控中小型企业 (SME) 能耗的可用智能电表的子集，即每 30 分钟采集一次样本的 485 个时间序列。 请注意，出于研究目的，可以免费获得对数据集的访问。

We build an adjacency matrix by extracting a k-nearest neighbor graph (with k = 10) from the
similarity matrix built by computing the week-wise correntropy (Liu et al., 2007) among time series.
As in the traffic case, we use a 70%/10%/20% split for training, validation and testing and use a
window size of 24 steps. Data were normalized using standard scaling as in the previous settings,
and we did not perform additional preprocessing steps.


我们通过从通过计算时间序列之间的周相关熵 (Liu et al., 2007) 构建的相似性矩阵中提取 k 最近邻图（k = 10）来构建邻接矩阵。 与交通案例一样，我们使用 70%/10%/20% 的比例进行训练、验证和测试，并使用 24 步的窗口大小。 与之前的设置一样，使用标准缩放对数据进行标准化，我们没有执行额外的预处理步骤。

5https://www.ucd.ie/issda/data/commissionforenergyregulationcer



### C ADDITIONAL RESULTS其他结果
In this appendix, we show an additional experiment in a controlled environment, comparison against
additional baselines, additional ablation studies, and sensitivity analyses.

在本附录中，我们展示了在受控环境中的额外实验、与额外基线的比较、额外的消融研究和敏感性分析。

#### C.1 SYNTHETIC DATA合成数据

In this experiment, we test our method on the simulated particle system introduced by Kipf et al.
(2018)6
. We simulate the trajectories of N = 10 particles in a (10 × 10) box with elastic collision.
Each particle carries a either positive or negative charge q ∈ {1, −1}. Two particles attract each
other if they have opposite sign, otherwise they repel. Interaction forces between two particles are
ruled by Coulomb’s law. We collect two datasets, each containing 5000 independent simulations
of T = 36 steps each. In the first dataset, particles have the same charge in every simulation. In
the second one, we sample the charges uniformly at random at the beginning of every simulation.
In both scenarios, the initial location and velocity of the particles are drawn randomly. At each
step, we randomly remove blocks of consecutive readings with a probability pfailure = 2.5% and a
length sampled uniformly from the interval [4, 9]. Here, a reading consists of the (x, y) coordinates
of the particle’s position. We further mask out 2.5% of positions at random. The percentage of
values not masked out is ≈ 74%. For evaluation purposes, we generate another mask using the same
missing data distribution and use the masked values as ground-truth for evaluation. We split dataset
in training/validation/test folds using 70%/10%/20% splits, respectively.


在本实验中，我们在 Kipf 等人引入的模拟粒子系统上测试了我们的方法。 （2018 年）6。我们在弹性碰撞的（10×10）盒子中模拟 N = 10 个粒子的轨迹。每个粒子都带有正电荷或负电荷 q ∈ {1, -1}。两个粒子如果符号相反则相互吸引，否则相互排斥。两个粒子之间的相互作用力由库仑定律决定。我们收集了两个数据集，每个数据集包含 5000 个独立的 T = 36 步模拟。在第一个数据集中，粒子在每个模拟中都具有相同的电荷。在第二个中，我们在每次模拟开始时随机均匀地采样电荷。在这两种情况下，粒子的初始位置和速度都是随机绘制的。在每一步，我们随机删除概率 pfailure = 2.5% 且长度从区间 [4, 9] 均匀采样的连续读数块。这里，读数由粒子位置的 (x, y) 坐标组成。我们进一步随机屏蔽了 2.5% 的位置。未被屏蔽的值的百分比约为 74%。出于评估目的，我们使用相同的缺失数据分布生成另一个掩码，并将掩码值用作评估的真值。我们分别使用 70%/10%/20% 拆分将数据集拆分为训练/验证/测试折叠。


Table 6: Results on the synthetic datasets. Performance averaged over 5 runs

表 6：合成数据集的结果。 5 次运行的平均性能
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009170130.png)

We test our method (GRIN) and BRITS in both synthetic datasets. We use 32 units for the hidden
layer of BRITS (≈ 25K parameters) and 16 units for both the encoder and decoder of GRIN (≈ 10K
parameters). Results are reported in Tab. 6. Both the methods take as input only the particles’ positions, with no information about the charges. As can be seen, consistently with what observed
by Kipf et al. (2018), relational representations are impressively effective in this scenario. Our
method outperforms the baseline by more than an order of magnitude in terms of MSE. Surprisingly, BRITS is more accurate in the setting with varying charge. Our hypothesis is that the added
stochasticity acts as a regularization and forces BRITS to learn a more general model.


我们在两个合成数据集中测试我们的方法（GRIN）和 BRITS。 我们为 BRITS 的隐藏层使用 32 个单元（≈ 25K 参数），为 GRIN 的编码器和解码器使用 16 个单元（≈ 10K 参数）。 结果在表中报告。 6. 这两种方法都只输入粒子的位置，没有关于电荷的信息。 可以看出，与 Kipf 等人观察到的一致。 （2018），关系表示在这种情况下非常有效。 我们的方法在 MSE 方面优于基线超过一个数量级。 令人惊讶的是，BRITS 在不同电荷的设置中更加准确。 我们的假设是，增加的随机性充当正则化并迫使 BRITS 学习更通用的模型。

#### C.2 EMPIRICAL COMPARISON AGAINST MATRIX FACTORIZATION WITH SIDE INFORMATION C.2 与边信息的矩阵分解的经验比较

Table 7: Comparison of regularized matrix factorization methods on air quality datasets. Results averaged over 5 independent runs.

表 7：空气质量数据集上正则化矩阵分解方法的比较。 结果平均超过 5 次独立运行。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009170332.png)


As mentioned in Section 2, several matrix factorization approaches – often studied in the context
of recommender systems – can be regularized by considering priors on the spatio-temporal structure of the data. Intuitively, spatial regularization is achieved by imposing soft constraints on the
smoothness of the interpolated function w.r.t. nodes of an underlying graph (Cai et al., 2010; Rao
et al., 2015). Temporal regularization can be obtained by imposing analogous constraints modelling
temporal dependencies as – eventually weighted – edges of a graph. In temporal regularized matrix
factorization (TRMF; Yu et al., 2016), similarly, coefficients of an autoregressive model are used as
temporal regularizer
如第 2 节所述，几种矩阵分解方法——通常在推荐系统的背景下研究——可以通过考虑数据时空结构的先验来进行正则化。 直观地说，空间正则化是通过对内插函数 w.r.t. 的平滑度施加软约束来实现的。 底层图的节点（Cai et al., 2010; Rao et al., 2015）。 时间正则化可以通过施加类似的约束来获得，将时间依赖性建模为 - 最终加权 - 图的边缘。 在时间正则化矩阵分解 (TRMF; Yu et al., 2016) 中，类似地，自回归模型的系数被用作时间正则化器


Tab. 7 shows a comparison of different matrix factorization approaches on imputation in the air
quality datasets (where we considered the in-sample setting in Section 5). For TRMF we used an
implementation adapted from the Transdim repository7
, while for graph regularized matrix factorization (GMRF) we use a custom implementation of the method proposed by Cai et al. (2010). We
fixed the rank to be equal to 10 (as the one used in all the experiments for standard MF) and tuned the
regularization coefficients on a validation set. Results do show that introducing spatial and temporal
regularization improve w.r.t. vanilla MF; however, deep learning methods – and even linear VAR
predictors – achieve far superior reconstruction accuracy here. Arguably, low-rank approximation
methods might instead have an edge in a low-data regime: this type of analysis is, however, out of
the scope of this work.

图 7 显示了空气质量数据集中不同矩阵分解方法的比较（我们在第 5 节中考虑了样本内设置）。 对于 TRMF，我们使用改编自 Transdim 存储库的实现，而对于图正则化矩阵分解 (GMRF)，我们使用 Cai 等人提出的方法的自定义实现。 （2010）。 我们将等级固定为 10（作为标准 MF 的所有实验中使用的等级）并调整验证集上的正则化系数。 结果确实表明，引入空间和时间正则化可以提高 w.r.t. 香草中频； 然而，深度学习方法——甚至是线性 VAR 预测器——在这里实现了更高的重建精度。 可以说，低秩近似方法可能反而在低数据状态下具有优势：但是，这种类型的分析超出了这项工作的范围。


#### C.3 SCALABILITY可拓展性

With reference to a standard bidirectional GRU, using MPNNs to implement the cell’s gates increases the computational complexity by a factor that scales with the number of edges O(E) – if
using an efficient sparse implementation – or with the number of nodes squared O(N2
). Luckily,
this overhead can be amortized as most of the computation can be parallelized. Research on scalable
and memory efficient GNNs is a very active field (e.g., Hamilton et al., 2017; Frasca et al., 2020):
depending on the task, the designer can opt for massage passing operators that meet the application
requirements in terms of performance, time and space constraints.

参考标准的双向 GRU，使用 MPNN 来实现单元的门会增加计算复杂度一个因子，该因子与边的数量 O(E) - 如果使用有效的稀疏实现 - 或节点数量的平方 O( N2)。 幸运的是，这种开销可以分摊，因为大部分计算可以并行化。 对可扩展和内存高效的 GNN 的研究是一个非常活跃的领域（例如，Hamilton et al., 2017; Frasca et al., 2020）：根据任务，设计者可以选择满足应用需求的按摩传递算子。 性能、时间和空间的限制。

#### C.4 ABLATION STUDY消融研究

Here we provide two different ablation studies, the first one on the architecture of GRIN and the
second one on the graph structure.

在这里，我们提供了两种不同的消融研究，第一个关于 GRIN 的架构，第二个关于图结构。



##### C.4.1 ARCHITECTURAL ABLATIONS架构消融


Tab. 8 shows additional results for the ablation study presented in Section 5. Consistently with what
we already observed, the spatial decoder and bidirectional architecture improve performance and
appear particularly relevant in settings with blocks of missing data.

图 8 显示了第 5 节中介绍的消融研究的其他结果。与我们已经观察到的一致，空间解码器和双向架构提高了性能，并且在具有缺失数据块的设置中显得特别相关。

Table 8: Ablation study. MAE averaged over 5 runs. (P) and (B) indicate the Point Missing and
Block Missing settings, respectively


表 8：消融研究。 MAE 平均超过 5 次运行。 (P) 和 (B) 分别表示点缺失和块缺失设置

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009170934.png)


##### C.4.2 GRAPH STRUCTURE ABLATIONS C.4.2 图形结构消融

Here we study how exploiting the relational structure of the problem affects the accuracy of the
reconstruction. In particular, we run two additional experiments on the METR-LA dataset (Block
missing settings), where instead of using as adjacency matrix the thresholded kernel in Eq. 12, we
use (1) a fully connected graph (W = 1) and (2) a graph with no edges (W = I). To provide node
– i.e., sensor – identification, we use learnable embeddings as additional node features. Results are shown in Tab. 9, performance for BRITS are reported as reference. It is clear that the constraints
posed by the graph structure do have an impact on the accuracy of missing data imputation and, at
the same time, that spatial information is relevant for the task.
在这里，我们研究利用问题的关系结构如何影响重建的准确性。 特别是，我们在 METR-LA 数据集（块缺失设置）上运行了两个额外的实验，其中不使用方程式中的阈值内核作为邻接矩阵。 在图 12 中，我们使用 (1) 全连接图 (W = 1) 和 (2) 无边图 (W = I)。 为了提供节点（即传感器）识别，我们使用可学习嵌入作为附加节点特征。 结果显示在表中。 9、BRITS 的性能报告为参考。 很明显，图结构带来的约束确实会影响缺失数据插补的准确性，同时，空间信息与任务相关。


Table 9: Performance with different adjacency matrices. Results averaged over 5 runs. (B) indicates
the Block Missing setting.

表 9：不同邻接矩阵的性能。 结果平均超过 5 次运行。 (B) 表示 Block Missing 设置。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009171208.png)

##### C.5 SENSITIVITY ANALYSIS C.5 敏感性分析

Finally, in this subsection we carry out an assessment of performance degradation w.r.t. the amount
of missing data. Before discussing results, there are a few remarks that are worth bringing up
regarding imputation in highly sparse settings. In the first place, GRIN, as well as a large portion
of the state-of-the-art baselines, is an autoregressive model, which means that it might be subject
to error accumulation over long time horizons. Furthermore, here, consistently with Section 5, we
consider the out-of-sample setting which is particularly challenging in the sparse data regime. That
being said, GRIN achieves remarkable performance also in this benchmark.


最后，在本小节中，我们对 w.r.t 的性能下降进行了评估。 缺失数据的数量。 在讨论结果之前，有一些关于高度稀疏设置中的插补的评论值得提出。 首先，GRIN 以及大部分最先进的基线是一个自回归模型，这意味着它可能会在很长一段时间内受到误差累积的影响。 此外，在这里，与第 5 节一致，我们考虑在稀疏数据机制中特别具有挑战性的样本外设置。 话虽如此，GRIN 在这个基准测试中也取得了显着的表现。

We train one model each for GRIN and BRITS by randomly masking out 60% of input data for
each batch during training, then, we run the models on the test set by using evaluation masks with
increasing sparsity (note that this causes a distribution shift in evaluation). For each level of sparsity,
evaluation is repeated 5 times by sampling different evaluation masks. Results are reported in Tab. 10
and Fig. 5 shows that GRIN outperforms BRITS in all the considered scenarios.

我们通过在训练期间随机屏蔽每批 60% 的输入数据，为 GRIN 和 BRITS 分别训练一个模型，然后，我们通过使用具有越来越稀疏性的评估掩码在测试集上运行模型（请注意，这会导致 评估）。 对于每个稀疏级别，通过采样不同的评估掩码重复评估 5 次。 结果在表中报告。 图 10 和图 5 显示 GRIN 在所有考虑的场景中都优于 BRITS。

Table 10: Performance with different amounts of missing data. Results averaged over 5 different
evaluation masks in the out-sample setting. (P) indicates the Point Missing setting.

表 10：不同缺失数据量的性能。 结果在样本外设置中平均超过 5 个不同的评估掩码。 (P) 表示缺少点设置。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009171422.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221009171435.png)


Figure 5: The plot shows graphically the results in Tab. 10.

图 5：该图以图形方式显示 Tab10 中的结果。





## 参考论文
## 1 最近邻插补算法：关键评估。
https://bmcmedinformdecismak.biomedcentral.com/articles/10.1186/s12911-016-0318-z
[5]Lorenzo Beretta and Alessandro Santaniello. Nearest neighbor imputation algorithms: a critical evaluation. BMC medical informatics and decision making, 16(3):197–208, 2016.

出身背景
最近邻（NN）插补算法是填充缺失数据的有效方法，其中一些记录上的每个缺失值都被从整个记录集中的相关案例中获得的值替换。除了能够用尽可能接近真实值的合理值替换缺失数据外，插补算法还应保持原始数据结构，避免扭曲插补变量的分布。尽管NN算法效率很高，但人们对这些方法对数据结构的影响知之甚少。
方法
对具有不同模式和丢失程度的合成数据集进行仿真，以评估在不同学习框架下，单邻（1NN）和k邻无（kNN）或加权（wkN）的NN的性能：普通集、ReliefF滤波后的约简集、打包、属性的随机选择、，打包与属性的随机选择相结合（类似于随机森林的方法）。
后果
无论采用何种框架，就插补精度和减少推断统计误差而言，kNN通常优于1NN，但1NN是唯一能够保留数据结构的方法，即使考虑k个邻域的小值，数据也会失真；重采样模式的失真更严重。
结论
结合ReliefF使用三个邻居似乎可以在插补错误和数据结构保存之间取得最佳平衡。在完全随机引入缺失数据后，对单质子发射计算机断层扫描（SPECTF）心脏数据集进行插补实验时，可以得出相同的结论。


## 2 缺失数据：神经网络和期望最大化技术的比较

https://www.jstor.org/stable/24099079#metadata_info_tab_contents
[39]Fulufhelo V Nelwamondo, Shakir Mohamed, and Tshilidzi Marwala. Missing data: A comparison of neural network and expectation maximization techniques. Current Science, pp. 1514–1521, 2007.

最近的文献中出现了两种技术作为缺失数据插补问题的候选解决方案。 这些是期望最大化（EM）算法和自联想神经网络和遗传算法（GA）的组合。 这两种技术都已单独讨论过，并且在现有文献中详细讨论了它们的优点。 但是，它们尚未相互比较。 本文使用工业发电厂、工业缠绕过程和 HIV 血清流行率调查数据的数据集对这两种技术进行了比较。 结果表明，EM算法更适用于输入变量之间几乎没有或没有相互依赖的情况，而自联想神经网络和GA组合适用于某些给定变量之间存在固有非线性关系的情况。 .

## 3 通过状态空间方法进行时间序列分析
https://books.google.com.hk/books?hl=zh-CN&lr=&id=lGyshsfkLrIC&oi=fnd&pg=PP1&dq=Time+series+analysis+by+state+space+methods&ots=WP4NWftwhp&sig=is-tbib2NZ7zzrtpTfvxy3VaGxI&redir_esc=y#v=onepage&q=Time%20series%20analysis%20by%20state%20space%20methods&f=false
[16]James Durbin and Siem Jan Koopman. Time series analysis by state space methods. Oxford university press, 2012.



## 4 用于大规模非负矩阵和张量分解的快速局部算法
https://d.wanfangdata.com.cn/periodical/91669bdffdc7025f9faab9d298aeaa39

[13]Andrzej Cichocki and Anh-Huy Phan. Fast local algorithms for large scale nonnegative matrix and tensor factorizations. IEICE transactions on fundamentals of electronics, communications and computer sciences, 92(3):708–721, 2009.

非负矩阵分解 (NMF) 及其扩展如非负张量分解 (NTF) 已成为盲源分离 (BSS)、图像数据库分析、数据挖掘和其他信息检索和聚类应用的重要技术。在本文中，我们提出了一系列有效的 NMF/NTF 算法，以及稀疏非负编码和表示，在计算神经科学、多感官处理、压缩感知和多维数据分析中具有许多潜在的应用。我们开发了一类优化的局部算法，称为分层交替最小二乘 (HALS) 算法。出于这些目的，我们对一组平方欧几里得距离执行了顺序约束最小化。然后，我们将这种方法扩展到使用 alpha 和 beta 散度的稳健成本函数，并得出灵活的更新规则。我们的算法是局部稳定的，并且适用于基于 NMF 的盲源分离 (BSS)，不仅适用于超定情况，而且适用于欠定（过完备）情况（即，对于传感器数量少于来源）如果数据足够稀疏。 NMF 学习规则针对 N 阶非负张量分解 (NTF) 进行了扩展和推广。此外，这些算法可以通过调整单个参数来调整到不同的噪声统计数据。广泛的实验结果证实了所开发算法的准确性和计算性能，特别是在使用多层分层 NMF 方法时。



## 5 


[9]Wei Cao, Dong Wang, Jian Li, Hao Zhou, Yitan Li, and Lei Li. Brits: bidirectional recurrent imputation for time series. In Proceedings of the 32nd International Conference on Neural Information Processing Systems, pp. 6776–6786, 2018.


## 6 具有缺失值的多元时间序列的递归神经网络


[10]Zhengping Che, Sanjay Purushotham, Kyunghyun Cho, David Sontag, and Yan Liu. Recurrent neural networks for multivariate time series with missing values. Scientific reports, 8(1):1–12,2018
实际应用中的多变量时间序列数据，如医疗保健、地球科学和生物学，具有多种缺失值的特点。在时间序列预测和其他相关任务中，已经注意到缺失值及其缺失模式通常与目标标签相关，即信息缺失。在利用缺失模式进行有效插补和提高预测性能方面的工作非常有限。在本文中，我们开发了新颖的深度学习模型，即 GRU-D，作为早期尝试之一。 GRU-D 基于门控循环单元 (GRU)，这是一种最先进的循环神经网络。它采用两种缺失模式的表示，即掩码和时间间隔，并将它们有效地结合到一个深度模型架构中，这样它不仅可以捕获时间序列中的长期时间依赖性，还可以利用缺失的模式来实现更好的预测结果。在真实世界临床数据集（MIMIC-III、PhysioNet）和合成数据集上的时间序列分类任务实验表明，我们的模型实现了最先进的性能，并为更好地理解和利用时间序列中的缺失值提供了有用的见解分析。














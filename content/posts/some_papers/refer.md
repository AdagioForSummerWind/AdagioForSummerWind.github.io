---
title: "refer"
date: 2022-05-21T14:42:03+08:00
lastmod: 2022-05-21
tags: [papers]
categories: [Graduation project]
slug: TRMF for High-dimensional Time Series Prediction
draft: true
---
我们用图神经网络去做时空数据插补与BRITS相比，BRITS的弱势在哪里呢，我还不太明白……

26页倒数第二行 降 去掉



## 青基




## 1
[1] Kreindler, David M., and Charles J. Lumsden. The Efects of the Irregular Sample and Missing Data in 
Time Series Analysis. Nonlinear dynamics, psychology, and life sciences (2006).
时间序列分析中不规则样本和缺失数据的影响

人类自我报告的时间序列数据通常以采样率不规则为特征；此外，这些违规行为通常是数据生成过程的自然结果。帮助分析不规则采样数据的出版物相对较少。我们报告了一系列关于合成数据集的计算实验的结果，这些实验旨在评估处理不规则时间序列数据的技术的效用。保守准周期、耗散混沌和自组织临界动力学的行为在时间上被定期采样，并且定期采样被数据点移除或时间上的随机变化破坏。然后通过段连接、用平均数据值填充段或通过相空间中的局部插值来修补缺失的数据段。我们比较了非线性分析工具的结果，例如使用完整集和修补集的自相关和相关维数，以及功率谱与抽取集的 Lomb 周期图。相空间中的局部插值在保留原始数据的关键特征方面特别成功，但需要潜在不切实际的完整数据量作为引物。虽然其他修补方法不受对完整数据的需求的限制，但它们会扭曲相对于完整系列的结果。我们得出的结论是，具有多达 15% 缺失数据的不规则采样数据集可能会被重新采样或修复，以便使用假设定期采样而不会引入重大错误的技术进行分析。

## 2
[2] Fung, David S. Methods for the estimation of missing values in time series. (2006).
估计时间序列中缺失值的方法
https://ro.ecu.edu.au/theses/63/

时间序列是随时间测量的一组连续数据。时间序列的例子出现在从工程到经济学的各个领域。时间序列数据的分析是统计学的一个重要领域。由于数据是通过时间获取的记录，因此时间序列数据中的缺失观察非常常见。发生这种情况是因为由于设备故障、记录丢失或错误可能无法在特定时间进行观察，直到以后才能纠正。当一个或多个观测值缺失时，可能需要估计模型并获得缺失值的估计值。通过包括对缺失值的估计，可以通过更准确的预测更好地理解数据的性质。不同的系列可能需要不同的策略来估计这些缺失值。有必要有效地使用这些策略以获得最佳估计。本论文的目的是检验和比较各种技术在时间序列数据模型中估计缺失值的有效性。

## 3
[3] Azur, Melissa J., Elizabeth A. Stuart, Constantine Frangakis, and Philip J. Leaf. Multiple imputation by chained equations: what is it and how does it work?. International journal of methods in psychiatric research 20, no. 1 (2011): 40-49.
链式方程的多重插补：它是什么以及它是如何工作的？
https://onlinelibrary.wiley.com/doi/full/10.1002/mpr.329
链式方程的多元插补 (MICE) 已成为处理缺失数据的原则性方法。尽管使 MICE 特别适用于大型插补程序的特性和软件开发的进步现在使许多研究人员可以使用它，但许多精神病学研究人员尚未接受过这些方法的培训，并且很少有实用资源来指导研究人员实施这项技术。本文介绍了 MICE 方法，重点介绍了使用该方法的实际方面和挑战。还提供了对可用于实施 MICE 并分析多重估算数据的软件程序的简要回顾。

## 4

[4] Li, Lei, James McCann, Nancy S. Pollard, and Christos Faloutsos. Dynammo: Mining and summarization of coevolving sequences with missing values. In Proceedings of the 15th ACM SIGKDD international conference on Knowledge discovery and data mining, pp. (2009): 507-516. 
DynaMMo：对具有缺失值的共同进化序列进行挖掘和总结
https://dl.acm.org/doi/abs/10.1145/1557019.1557078
给定具有缺失值的多个时间序列，我们提出了 DynaMMo，它可以汇总、压缩和查找潜在变量。这个想法是发现隐藏变量并学习它们的动态，使我们的算法即使在缺失值的情况下也能正常工作。
我们对跨越数兆字节的真实和合成数据集进行了实验，包括运动捕捉序列和饮用水中的氯含量。我们证明了我们提出的 DynaMMo 方法 (a) 可以成功地学习潜在变量及其演化； (b) 可以提供高压缩，重建精度损失很小； (c) 可以提取紧凑但强大的特征用于分割、解释和预测； (d) 复杂度与序列的持续时间呈线性关系。

## 5
[5] Yu, Hsiang-Fu, Nikhil Rao, and Inderjit S. Dhillon. Temporal regularized matrix factorization for high-dimensional time series prediction. Advances in neural information processing systems 29 (2016).
用于高维时间序列预测的时态正则化矩阵因子化
https://proceedings.neurips.cc/paper/2016/hash/85422afb467e9456013a2a51d4dff702-Abstract.html

时间序列预测问题在现代应用中变得越来越高维，例如气候学和需求预测。例如，在后一个问题中，需要预测需求的项目数量可能高达 50,000 个。此外，数据通常嘈杂且充满缺失值。因此，现代应用程序需要高度可扩展的方法，并且可以处理损坏或缺失值方面的噪声数据。然而，经典的时间序列方法通常无法处理这些问题。在本文中，我们提出了一个时间正则化矩阵分解 (TRMF) 框架，该框架支持数据驱动的时间学习和预测。我们开发了新颖的正则化方案并使用了非常适合具有许多缺失值的高维时间序列数据的可扩展矩阵分解方法。我们提出的 TRMF 是高度通用的，并且包含了许多现有的时间序列分析方法。在自回归框架中学习依赖关系的背景下，我们与图正则化方法建立了有趣的联系。实验结果表明TRMF在可扩展性和预测质量方面的优越性。特别是，对于 50,000 维的问题，TRMF 比其他方法快两个数量级，并且在沃尔玛电子商务数据集等现实世界数据集上生成更好的预测。


## 6
[6] Che, Zhengping, Sanjay Purushotham, Kyunghyun Cho, David Sontag, and Yan Liu. Recurrent neural networks for multivariate time series with missing values. Scientific reports 8, no. 1 (2018): 1-12.
具有缺失值的多元时间序列的递归神经网络	
https://www.nature.com/articles/s41598-018-24271-9

实际应用中的多变量时间序列数据，如医疗保健、地球科学和生物学，具有多种缺失值的特点。在时间序列预测和其他相关任务中，已经注意到缺失值及其缺失模式通常与目标标签相关，即信息缺失。在利用缺失模式进行有效插补和提高预测性能方面的工作非常有限。在本文中，我们开发了新颖的深度学习模型，即 GRU-D，作为早期尝试之一。 GRU-D 基于门控循环单元 (GRU)，这是一种最先进的循环神经网络。它采用两种缺失模式的表示，即掩码和时间间隔，并将它们有效地结合到一个深度模型架构中，这样它不仅可以捕获时间序列中的长期时间依赖性，还可以利用缺失的模式来实现更好的预测结果。在真实世界临床数据集（MIMIC-III、PhysioNet）和合成数据集上的时间序列分类任务实验表明，我们的模型实现了最先进的性能，并为更好地理解和利用时间序列中的缺失值提供了有用的见解分析。

## 7
[7] Yoon, Jinsung, William R. Zame, and Mihaela van der Schaar. Multi-directional recurrent neural networks: A novel method for estimating missing data. In Time Series Workshop in International Conference on Machine Learning. 2017.
多向循环神经网络:一种估计缺失数据的新方法
https://www.semanticscholar.org/paper/Multi-directional-Recurrent-Neural-Networks-%3A-A-for-Yoon-Zame/77ffd0afe8748e7f241856e517aab45b59634343
大多数具有多个数据流的时间序列数据集都缺少（许多）需要估计的测量值。大多数现有方法通过在数据流内插值或跨数据流插补来解决这个估计问题；我们开发了一种新颖的方法，两者兼而有之。我们的方法基于深度学习架构，我们称之为多向递归神经网络 (M-RNN)。 M-RNN 与双向 RNN 的不同之处在于，它除了在流内之外还跨流操作，并且因为输入到隐藏层的时间既滞后又超前。为了展示我们方法的强大功能，我们将其应用于熟悉的现实世界医学数据集，并展示了显着提高的性能。

## 8
[8] Cao, Wei, Dong Wang, Jian Li, Hao Zhou, Lei Li, and Yitan Li. Brits: Bidirectional recurrent imputation for time series. Advances in neural information processing systems 31 (2018).
BRITS：时间序列的双向循环插补
https://proceedings.neurips.cc/paper/2018/hash/734e6bfcd358e25ac1db0a4241b95651-Abstract.html

时间序列在许多分类/回归任务中被广泛用作信号。时间序列包含许多缺失值是普遍存在的。给定多个相关的时间序列数据，如何填充缺失值并预测它们的类标签？现有的插补方法通常对基础数据生成过程进行强假设，例如状态空间中的线性动态。在本文中，我们提出了 BRITS，这是一种基于循环神经网络的新方法，用于时间序列数据中的缺失值插补。我们提出的方法直接学习双向循环动力系统中的缺失值，无需任何特定假设。估算值被视为 RNN 图的变量，可以在反向传播期间有效更新。 BRITS 具有三个优点：（a）它可以处理时间序列中的多个相关缺失值； (b) 它推广到具有非线性动力学基础的时间序列； (c) 它提供了一个数据驱动的插补程序，适用于缺少数据的一般环境。我们在三个真实世界的数据集上评估我们的模型，包括空气质量数据集、医疗保健数据和人类活动的定位数据。实验表明，我们的模型在插补和分类/回归精度方面都优于最先进的方法。

## 9
[9] Luo, Yonghong, Xiangrui Cai, Ying Zhang, and Jun Xu. Multivariate time series imputation with generative adversarial networks. Advances in neural information processing systems 31 (2018).
具有生成对抗网络的多元时间序列插补
https://proceedings.neurips.cc/paper/2018/hash/96b9bff013acedfb1d140579e2fbeb63-Abstract.html

多元时间序列通常包含大量缺失值，这阻碍了高级分析方法在多元时间序列数据上的应用。解决缺失值挑战的传统方法，包括均值/零插补、案例删除和基于矩阵分解的插补，都无法对多变量时间序列中的时间依赖性和复杂分布的性质进行建模。在本文中，我们将缺失值插补问题视为数据生成。受生成对抗网络 (GAN) 在图像生成方面的成功启发，我们建议使用 GAN 学习多元时间序列数据集的整体分布，进一步用于生成每个样本的缺失值。与图像数据不同，由于数据记录过程的性质，时间序列数据通常是不完整的。 GAN 中使用了一个改进的门循环单元来模拟不完整时间序列的时间不规则性。在两个多元时间序列数据集上的实验表明，所提出的模型在插补准确性方面优于基线。实验结果还表明，基于估算数据的简单模型可以在预测任务上取得最先进的结果，证明了我们的模型在下游应用中的优势。

## 10
[10] Hu, Jilin, Chenjuan Guo, Bin Yang, and Christian S. Jensen. Stochastic weight completion for road networks using graph convolutional networks. IEEE 35th International Conference on Data Engineering (ICDE), pp. (2019): 1274-1285
使用图卷积网络的道路网络的随机权重完成
https://ieeexplore.ieee.org/abstract/document/8731475

交通领域的创新，如按需移动服务和自动驾驶，需要高分辨率的路线，该路线依赖于整个基础道路网络的旅行时间的准确表示。具体来说，道路网络边缘的行驶时间被建模为时变分布，它捕捉到交通随时间的变化以及不同驾驶员可能在同一时间以不同速度穿越同一边缘的事实。这种随机权重可以从诸如GPS和环路检测器数据之类的数据源中提取。然而，即使是非常大的数据源也无法始终覆盖道路网络的所有边缘。然而，高分辨率路由需要所有边的随机权重。我们解决了填充缺失权重的问题。为了实现这一点，我们提供了能够从仅覆盖所有边的一小部分的交通数据中估计所有边的随机边权重的技术。我们提出了一个通用的学习框架，称为图卷积权重完成 (GCWC)，它利用道路网络图的拓扑结构和相邻边之间的权重相关性来估计所有边的随机权重。接下来，我们将上下文信息合并到 GCWC 中以进一步提高准确性。使用来自高速公路收费站网络的环路检测器数据和来自大城市的 GPS 数据的实证研究提供了对 GCWC 的设计特性及其有效性的洞察。




## DCRNN
https://arxiv.org/abs/1707.01926
Diffusion Convolutional Recurrent Neural Network: Data-Driven Traffic Forecasting

时空预测在神经科学、气候和交通领域有多种应用。交通预测是这种学习任务的一个典型例子。由于（1）对道路网络的复杂空间依赖性，（2）随道路条件变化的非线性时间动态以及（3）长期预测的固有困难，这项任务具有挑战性。为了应对这些挑战，我们建议将交通流建模为有向图上的扩散过程，并引入扩散卷积递归神经网络 (DCRNN)，这是一种用于交通预测的深度学习框架，它在交通流中结合了空间和时间依赖性。具体来说，DCRNN 使用图上的双向随机游走来捕获空间依赖性，并使用具有预定采样的编码器-解码器架构来捕获时间依赖性。我们在两个真实世界的大规模道路网络交通数据集上评估该框架，并观察到比最先进的基线持续改进 12% - 15%。


## 删改
提取隐含图结构的思路
现有时空图上的时间序列补全算法，或缺乏对隐含图结构的有效利用， 或无法对补全值提供置信度广泛

贝叶斯深度生成模型可以将未知图结构视为隐含随机变量从而以后验概率推断的形式对隐含图结构动态估计，且能够对建模变量学习概率分布函数，提供置信度或不确定性量化，因此可以有效解决上述问题。
本项目将从贝叶斯深度生成模型的角度出发，将未知的图结构视为隐含随机变量，将收集到的时间序列数据视为可观测随机变量，然后将问题转化为贝叶斯后验概率估计问题。
然而，后验概率的推断需要对高维隐含变量进行积分，传统的方法如马尔科夫链蒙特卡洛模拟 (Markov Chain Monte Carlo) 只能适用于较小规模的数据。另一种求解思路则是变分推断技术 (Variational Inference)，变分推断将后验概率的推断问题转化为一个数值优化问题，因此可以使用诸如随机梯度下降的方法来处理规模较大的数据集。
本项目拟采用变分自动编码器 (Variational Auto-Encoders) 进行后验概率推断。Variational Auto-Encoders 充分结合了深度神经网络的表达能力和变分推断高效的特点，通过选取神经网络来作为近似后验概率的函数簇，通过自动微分来优化参数以此选取最优近似后验概率。
具体来说：编码器主要功能就是学习隐含随机变量的后验概率分布。也就是补全值具有置信度的隐含图结构。






[35]Kreindler, David M., and Charles J. Lumsden. The Ef ects of the Irregular Sample and Missing Data in Time Series Analysis. Nonlinear dynamics, psychology, and life sciences (2006).

[36]Fung, David S. Methods for the estimation of missing values in time series. (2006).

[37]Azur, Melissa J., Elizabeth A. Stuart, Constantine Frangakis, and Philip J. Leaf. Multiple imputation by chained equations: what is it and how does it work?. International journal of methods in psychiatric research 20, no. 1 (2011): 40-49.

[38]Li, Lei, James McCann, Nancy S. Pollard, and Christos Faloutsos. Dynammo: Mining and summarization of coevolving sequences with missing values. In Proceedings of the 15th ACM SIGKDD international conference on Knowledge discovery and data mining, pp. 507-516. 2009.

[39]Yu, Hsiang-Fu, Nikhil Rao, and Inderjit S. Dhillon. Temporal regularized matrix factorization for high-dimensional time series prediction. Advances in neural information processing systems 29 (2016).

[40]Che, Zhengping, Sanjay Purushotham, Kyunghyun Cho, David Sontag, and Yan Liu. Recurrent neural networks for multivariate time series with missing values. Scientific reports 8, no. 1 (2018): 1-12.

[41]Yoon, Jinsung, William R. Zame, and Mihaela van der Schaar. Multi-directional recurrent neural networks: A novel method for estimating missing data. In Time Series Workshop in International Conference on Machine Learning. 2017

[42]Cao, Wei, Dong Wang, Jian Li, Hao Zhou, Lei Li, and Yitan Li. Brits: Bidirectional recurrent imputation for time series. Advances in neural information processing systems 31 (2018).

[43]Luo, Yonghong, Xiangrui Cai, Ying Zhang, and Jun Xu. Multivariate time series imputation with generative adversarial networks. Advances in neural information processing systems 31 (2018).

[44]Hu, Jilin, Chenjuan Guo, Bin Yang, and Christian S. Jensen. Stochastic weight completion for road networks using graph convolutional networks. IEEE 35th International Conference on Data Engineering (ICDE), pp. 1274-1285. 2019.






## DCRNN
Diffusion Convolutional Recurrent Neural Network: Data-Driven Traffic Forecasting
Yaguang Li, Rose Yu, Cyrus Shahabi, Yan Liu
https://arxiv.org/abs/1707.01926



哈尔滨工业大学深圳校区
毕业设计（论文）开题报告
题       目	基于图神经网络的多                   
元时间序列补全算法                    
姓       名	        邹啟正                       
学       号	       190110722                       
学       院	   计算机科学与技术学院                       
专       业	     计算机科学与技术                       
指 导 教 师	         李修成                       
日       期	        2022.10.26                  
 
目  录
1  课题背景及研究的目的和意义	1
1.1  课题背景	1
1.2  研究的目的和意义	1
2  国内外研究现状及分析	2
2.1  国内外研究现状	2
2.2  已有的时间序列补全模型	2
2.2.1基于插值和因子分解的补全模型	2
2.2.2基于深度学习的补全模型	2
2.2.3现有补全模型总结	2
3  主要研究内容及研究方案	3
3.1  研究内容	3
3.1.1未知结构时空图上时间序列数据补全	3
3.2  研究方案	3
3.2.1未知结构时空图上时间序列数据补全	3
3.2.2研究思路	4
4  进度安排及预期目标	7
4.1  进度安排	7
4.2  预期目标	7
5  已具备和所需的条件和经费	8
5.1  实验室条件和经费保障	8
5.2  所需条件和经费	8
6  预计困难及解决方案	9
6.1  预计困难与技术难点	9
6.2  解决方案	9
参考文献	10

   
 
1  课题背景及研究的目的和意义
1.1  课题背景
多元时间序列数据产生于人类活动的各个领域，如金融、交通、气象、医疗等。基于多元时间序列数据的预测和分类技术在科学研究和社会生产活动中具有举足轻重的地位，比如气象预测、交通预测、病情发展预测，姿势识别、活动识别，然而现有的多元时间序列预测和分类算法的开发通常都是基于原始数据是完整的这一假设。但在现实世界中，由于设备功能故障或者通信失败，多元时间序列数据经常存在随机数据丢失，因此对其补全算法的研究极其重要。
1.2  研究的目的和意义
时空数据是由若干在空间中分布的传感器或观测站在一段时间内对特定物理量进行收集的结果，它遍及科学研究、社会生产和个人日常生活的各个角落，如气象观测、道路网络交通流监测、人类出行轨迹等。而随着廉价智能传感器的大规模普及、部署和新一代通信技术（如5G）的进步，时空数据的生成速度和规模也在快速增长。从这些不断累积的海量时空数据中挖掘隐含的模式和进行知识提取，对科学研究、社会生产、智慧城市建设等都具有十分重要的意义。
时空数据是由分布在空间上的传感器或观测站在特定时间点收集而来，其收集过程决定了数据的两个显著内在属性，即1）时间维度上的动态性和2）空间维度上的关联性。其中，空间维度的关联性可以使用图来描述，其中每个传感器或观测点对应了图中的一个节点而图中的边则表示了两个节点间的关联性，由于时间维度上的动态性，每个节点的数据随时间变化，因此时空数据可以用一个时空图来描述。时空图中的边既可以是显示定义，比如承载交通流的道路交通网络（两个节点间存在路段连接），也可以是隐式定义的，比如一个区域内不同的风力发电站（两个发电站间无物理实体相连接），医疗时序数据（不同的测量指标如，血压和心率虽然处于不同维度，但二者之间存在相关性）。
在实际观测中由于设备故障或通信故障，亦或是观测成本高昂，时空数据通常是不完整的，因此相应的时空图中节点属性也是缺失的。比如在医疗时序数据中，由于设备出现异常，相应的监测指标会存在缺失；在交通流量监测中，在每条道路上都部署相应的流量监测站，代价会极为昂贵，因此只有部分道路存在监测数据。本问题重点研究如何在时空图结构缺失场景下通过观测数据推断丢失数据。




2  国内外研究现状及分析
2.1  国内外研究现状
总体而言，国内外对时间序列补全模型的研究沿着从**局部插值**向**对时间动态建模**的方向发展。
2.2  已有的时间序列补全模型
2.2.1**基于插值和因子分解的补全模型**
早期的工作包括基于平滑曲线假设的插值方法[1][2]，这些方法假设整个时间序列数据是平滑的因此主要利用局部观测进行重构丢失数据，因而忽略了数据在时间上的动态性。文献[3]首先随机初始化丢失值，然后基于链式方程对丢失值进行估计。文献[4]将丢失值视为隐含变量，通过学习其转移矩阵对隐含变量进行推断。文献[5]将多变量时间序列数据视为矩阵，并通过矩阵分解得到时间不变量因子和时间动态因子，并在时间动态因子上施加连续性约束，进而恢复丢失值。
2.2.2**基于深度学习的补全模型**
随着深度学习的广泛引用，文献[6][7][8]试图通过引入循环神经网络进行时空数据恢复，文献[9]则试图通过对抗生成网络实现对含丢失值时间序列数据的建模，进而实现丢失值重构，文献[10]试图使用图卷积神经网络在道路网络上对交通流进行补全，并通过图卷积操作建模相邻路段的空间相关性。
2.2.3现有补全模型总结	
    文献[1]到[9]都没有显式地建模不同空间对象之间隐含的相关性，而这一空间相关性对于丢失值恢复至关重要。文献[10]虽然建模了空间相关性，但它依赖于道路网络定义的图拓扑结构来捕获空间相关性，因此并不适用于图结构未知的场景。与此同时，现有方法通常只能给出对丢失值的估计而无法量化这一估计值的置信度，而这种置信度对于使用估计值进行决策制定是不可或缺的。因此本项目拟通过贝叶斯深度生成模型去动态学习隐含的图结构，并整合隐含图结构和非线性动态来实现对丢失值的补全，由于贝叶斯生成模型可以输出对估计值的概率分布，因此新的模型不但能够进行丢失值估计同时可以提供其估计值的可信度。






3  主要研究内容及研究方案
3.1  研究内容
实现未知结构时空图上时间序列数据补全算法，从收集到的部分观测数据推断缺失的图结构，并将推断的图结构用于对丢失（未观测）数据的补全上，提升补全性能。
3.1.1未知结构时空图上时间序列数据补全
时空图上的数据由每个节点（传感器或观测站）收集并通过网络发送存储，在实际运行中，设备故障或异常以及通信故障可能会导致数据丢失，这些丢失数据对很多应用有着重要的价值，因此从收集到的观测数据中去推断丢失数据成为了一个重要的研究问题。时空图上时间序列的补全问题可以从两个维度来建模：时间维度和空间维度。其关键问题在于如何建模带有丢失值的时间序列数据以及如何从这些含有丢失值的数据中动态、高效的推断未知的图结构，并使用推断的图结构来对缺失值进行补全。提出有效的算法来解决这些问题将成为本项目的研究内容。
3.2  研究方案
对于未知图结构的时间序列数据补全的关键是对隐含图结构的推断，以及如何从含有丢失值的时间序列数据中进行推断。因此，首先要解决的是含有丢失值的时间序列数据建模问题，经典的序列模型如循环神经网络或 Transformer 无法直接建模丢失值序列，本项目拟修改 GRU 网络的记忆门机制来适应丢失值序列或采用基于常微分方程的神经网络（Neural ODE），将其作为编码器提取含丢失值的时间序列数据表征，推断隐含图结构；其解码器亦可以通过修改融合卷积循环网络（适应丢失值序列）来实现。具体的研究方案和技术思路说明如下：
3.2.1未知结构时空图上时间序列数据补全
现有时空图上的补全模型多数是基于矩阵或者张量分解的因子模型，这类因子模型用隐含因子来表示每个空间节点，同时对每个时间切片赋予一个隐含因子，然后基于矩阵或张量分解的技术并结合时间上的动态性，为每个隐含因子学习表征，使得所学表征在观测数据上误差最小。也有部分模型通过修改循环神经网络或通过生成对抗网络来建模时间序列数据的。这些模型都没有显式地去推断隐含的图结构，本项目则从推断隐含图结构出发并结合图上的学习算法来创新性的解决这一问题。
3.2.2研究思路
图 3-1 展示了从含有丢失值的时空数据中推断隐含图结构并使用推断的图结构进行数据补全的整体研究思路。
 
                       图3-1：图结构推断和补全模型
首先，如何从含有丢失值的时间序列数据   中提取表征并将其用于图结构推断。循环神经网络无法直接处理含丢失值的序列数据，因为其每一步都需要观测数据更新隐含表征  ，下面通过分析 GRU 循环神经网络的更新机制来说明这一问题，以及提出可行的修改方案，为了叙述清晰，暂时忽略节点的下标，即用   来代替   。
 
（3-1）
GRU 循环神经网络通过隐含表征   来表示从开始到当前时刻的序列，并通过每 一步观测   对其动态更新，其中有两个门  、  来控制信息流的大小，而  、  以及新引入的观测信息   由当前观测   和历史表征   共同决定。对于有丢失值的时间序列来说当前观测   可能是丢失的，因此无法控制相应的信息流。本项目拟采用如下方案对 GRU 做出修改，将修改后的模型称为 D-GRU（Decayed-GRU），见式（3-2）。令   为与   有相同维度的 0/1 向量，如果当前时间步有观测其值为 1，否则为 0，   表示当前时间步距离上一步有观测值时间步的间隔： 
 
（3-2）
其核心思想是在当前时间步观测丢失时，使用时间衰减机制通过历史表征对未来观测进行预测，并通过预测值代替观测值更新 GRU。衰减幅度由指数函数   控制， 是待拟合参数，当距离上一步观测间隔越大衰减幅度越大，如果上一步有观测则无衰减；  是当前观测值和基于历史预测值的组合，其中   是线性函数，如果当前时间步有观测值，那么   完全由观测值决定，否则由预测值决定；同时，在  、 、  中额外引入了带有   的项使得模型可以区分有观测和无观测的情形。因此可以看出在当前时间步有观测值时，D-GRU 和 GRU 完全兼容。
在从编码器采样得到隐含图的结构后，解码器将以图结构和含有丢失值的时空数据为输入进行解码。为了使解码器使用图结构信息推断丢失数据，需要一种能够处理丢失值的图卷积循环神经网络。本项目拟在时间维度引入衰减机制以及在空间维度上调整边权重的方式扩展DCRNN，并将扩展后的模型称为 D-DCRNN（Decayed-DCRNN）。时间维度上的衰减机制类似于上述 D-GRU，空间维度上的处理思想如下：如果一个节点在当前时间步数据丢失，那么需要降低从该节点出发传播信息的权重。为此引入权重调整矩阵 Γ ，其定义方式见式（3-3）：
 
（3-3）
其中   是可以学习参数，  保证其值落在(0,1)区间，因此如果当前时间步节点   含丢失值则降低其权重，否则其权重保持不变。现在可以用 Γ 对图卷积操作做如下调整：
 
（3-4）
上述研究思路只是一个拟采用的方案，比如对丢失值的处理也可以考虑如何扩展基于注意力机制的 Transformer。此外，上述隐含图结构推断复杂度为图节点数的二次方   ，如何利用图结构的稀疏性和先验概率降低其计算复杂度，仍需要本项目作出进一步研究。
4  进度安排及预期目标
4.1  进度安排
本课题的研究时间为2022年9月开始至2023年6月份进行答辩，根据之前的项目经验，结合自身学习，科研攻关能力，针对课题研究计划，制定出下面的研究进度：
1. 2022年04月-2023年02月:总体设计
2. 2022年09月-2022年12月:查找资料
3. 2023年02月-2022年 05月:编写程序及模型测试
4.2  预期目标
给出未知结构时空图上时间序列数据补全方法。提出可以用于结构未知时空图上时间序列数据的补全模型，补全模型可以从带有丢失数据的时间序列数据中动态、高效的学习出最优图结构，并使用该图结构提升模型的补全能力。 新的时序补全模型，较现有方法平均绝对误差（Mean Absolute Error）降低5%以上。

5  已具备和所需的条件和经费
5.1  实验室条件和经费保障
实验室条件：校内拥有高性能云服务计算中心，课题所需的开发工具和常用软件齐全。
经费保障：实验课题经费充足
5.2  所需条件和经费
无
6  预计困难及解决方案
6.1  预计困难与技术难点
时空图上的补全问题需要对时空数据的走势进行建模，需要在含有丢失值的时间序列数据中进行，是一种内向插值（interpolation）。因此，至少存在两个技术难点：
一个是如何建模含有丢失值的时空数据并从中动态、高效的发掘隐含的图结构；另一个是如何将发掘的隐含结构整合到模型中帮助丢失值补全。
预计困难：在缺失时空数据中难以发掘出高效的图结构，新的时序补全模型，要实现较现有方法平均绝对误差降低5%以上比较困难。
6.2	 解决方案
为针对以上困难与技术难点，计划采取以下方案：
学习神经网络算法相关知识与应用，重点学习 GRU 和 DCRNN ，以便更好地对其进行修改和拓展，从而建模含有丢失值的时空数据并从中发掘出高效的图结构，并将其整合到模型中从而补全丢失值。
参考文献
[1] Kreindler, David M., and Charles J. Lumsden. The Efects of the Irregular Sample and Missing Data in Time Series Analysis. Nonlinear dynamics, psychology, and life sciences (2006).
[2] Fung, David S. Methods for the estimation of missing values in time series. (2006). 
[3] Li, Lei, James McCann, Nancy S. Pollard, and Christos Faloutsos. Dynammo: Mining and summarization of coevolving sequences with missing values. In Proceedings of the 15th ACM SIGKDD international conference on Knowledge discovery and data mining, pp. (2009): 507-516.
[4] Andrzej Cichocki and Anh-Huy Phan. Fast local algorithms for large scale
nonnegative matrix and tensor factorizations. IEICE transactions on fundamentals
of electronics, communications and computer sciences, 2009，92(3):708–721. 
[5] Wei Cao, Dong Wang, Jian Li, Hao Zhou, Yitan Li, and Lei Li. Brits:
bidirectional recurrent imputation for time series. In Proceedings of the 32nd
International Conference on Neural Information Processing Systems, 2018, pp.
6776–6786.
[6] Zhengping Che, Sanjay Purushotham, Kyunghyun Cho, David Sontag, and Yan Liu. Recurrent neural networks for multivariate time series with missing values. Scientific reports, 2018, 8(1):1–12.
[7] Yoon, Jinsung, William R. Zame, and Mihaela van der Schaar. Multi-directional
recurrent neural networks: A novel method for estimating missing data. In Time
Series Workshop in International Conference on Machine Learning. 2017. 
[8] Cao, Wei, Dong Wang, Jian Li, Hao Zhou, Lei Li, and Yitan Li. Brits:
Bidirectional recurrent imputation for time series. Advances in neural information
processing systems 31 (2018).
[9] Luo, Yonghong, Xiangrui Cai, Ying Zhang, and Jun Xu. Multivariate time
series imputation with generative adversarial networks. Advances in neural
information processing systems 31 (2018).
[10] Hu, Jilin, Chenjuan Guo, Bin Yang, and Christian S. Jensen. Stochastic weight
completion for road networks using graph convolutional networks. IEEE 35th
International Conference on Data Engineering (ICDE), pp. (2019): 1274-1285






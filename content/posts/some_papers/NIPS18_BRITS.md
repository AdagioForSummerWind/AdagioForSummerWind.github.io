---
title: "NIPS18_BRITS"
date: 2022-05-21T14:42:03+08:00
lastmod:
tags: [papers]
categories: [Graduation project]
slug: TRMF for High-dimensional Time Series Prediction
draft: true
---
> BRITS: Bidirectional Recurrent Imputation for Time Series
> https://blog.csdn.net/qq_40206371/article/details/124282752

# BRITS: Bidirectional Recurrent Imputation for Time Series

## 摘要
Time series are ubiquitous in many classification/regression applications. However, the time series data in real applications may contain many missing values. Hence, given multiple (possibly correlated) time series data, it is important to fill in missing values and at the same time to predict their class labels. Existing imputation methods often impose strong assumptions of the underlying data generating process, such as linear dynamics in the state space. In this paper, we propose a novel method, called BRITS, based on recurrent neural networks for missing value imputation in time series data. Our proposed method directly learns the missing values in a bidirectional recurrent dynamical system, without any specific assumption. The imputed values are treated as variables of RNN graph and can be effectively updated during backpropagation. BRITS has three advantages: (a) it can handle multiple correlated missing values in time series; (b) it generalizes to time series with nonlinear dynamics underlying; (c) it provides a data-driven imputation procedure and applies to general settings with missing data. We evaluate our model on three real-world datasets, including an air quality dataset, a healthcare dataset, and a localization dataset for human activity. Experiments show that our model outperforms the state-of-the-art methods in both imputation and classification/regression.


时间序列在许多分类/回归应用程序中无处不在。但是，实际应用中的时间序列数据可能包含很多缺失值。因此，给定多个（可能相关的）时间序列数据，填充缺失值并同时预测它们的类标签很重要。现有的插补方法通常对基础数据生成过程进行强假设，例如状态空间中的线性动态。在本文中，我们提出了一种名为 BRITS 的新方法，该方法基于递归神经网络，用于时间序列数据中的缺失值插补。我们提出的方法直接学习双向循环动力系统中的缺失值，无需任何特定假设。估算值被视为 RNN 图的变量，可以在反向传播期间有效更新。 BRITS 具有三个优点：（a）它可以处理时间序列中的多个相关缺失值； (b) 它推广到具有非线性动力学基础的时间序列； (c) 它提供了一个数据驱动的插补程序，适用于缺少数据的一般环境。我们在三个真实世界的数据集上评估我们的模型，包括空气质量数据集、医疗保健数据集和人类活动的定位数据集。实验表明，我们的模型在插补和分类/回归方面都优于最先进的方法。






## 1 介绍
Multivariate time series data are abundant in many application areas, such as financial marketing [5, 4], health-care [10, 22], meteorology [31, 26], and traffic engineering [29, 35]. Time series are widely used as signals for classification/regression in various applications of corresponding areas. However, missing values in time series are very common, due to unexpected accidents, such as equipment damage or communication error, and may significantly harm the performance of downstream applications.
多元时间序列数据在金融营销[5, 4]、医疗保健[10, 22]、气象学[31, 26]和交通工程[29, 35]等许多应用领域都非常丰富。 时间序列在相应领域的各种应用中被广泛用作分类/回归的信号。 但是，由于设备损坏或通信错误等意外事故，时间序列中的缺失值非常常见，并且可能会严重损害下游应用程序的性能。

Much prior work proposed to fix the missing data problem with statistics and machine learning approaches. However most of them require fairly strong assumptions on missing values. We can fill the missing values using classical statistical time series models such as ARMA or ARIMA (e.g., [1]). But these models are essentially linear (after differencing). Kreindler et al. [19] assume that the data are smoothable, i.e., there is no sudden wave in the periods of missing values, hence imputing missing values can be done by smoothing over nearby values. Matrix completion has also been used to address missing value problems (e.g., [30, 34]). But it typically applies to only static data and requires strong assumptions such as low-rankness. We can also predict missing values by fitting a parametric data-generating model with the observations [14, 2], which assumes that data of time series follow the distribution of hypothetical models. These assumptions make corresponding imputation algorithms less general, and the performance less desirable when the assumptions do not hold.

许多先前的工作建议使用统计和机器学习方法来解决丢失数据的问题。然而，它们中的大多数都需要对缺失值做出相当强的假设。我们可以使用经典的统计时间序列模型，例如 ARMA 或 ARIMA（例如 [1]）来填充缺失值。但这些模型本质上是线性的（差分后）。克林德勒等人。 [19] 假设数据是可平滑的，即在缺失值期间没有突然波动，因此可以通过平滑附近的值来估算缺失值。矩阵补全也被用于解决缺失值问题（例如，[30, 34]）。但它通常仅适用于静态数据，并且需要强假设，例如低秩。我们还可以通过使用观测值拟合参数数据生成模型来预测缺失值 [14, 2]，该模型假设时间序列的数据遵循假设模型的分布。这些假设使相应的插补算法不太通用，并且当假设不成立时性能不太理想。

In this paper, we propose BRITS, a novel method for filling the missing values for multiple correlated time series. Internally, BRITS adapts recurrent neural networks (RNN) [16, 11] for imputing missing values, without any specific assumption over the data. Much prior work uses non-linear dynamical systems for time series prediction [9, 24, 3]. In our method, we instantiate the dynamical system as a bidirectional RNN, i.e., imputing missing values with bidirectional recurrent dynamics. In particular, we make the following technical contributions:

在本文中，我们提出了 BRITS，这是一种填补多个相关时间序列缺失值的新方法。 在内部，BRITS 采用循环神经网络 (RNN) [16, 11] 来估算缺失值，而无需对数据进行任何特定假设。 许多先前的工作使用非线性动力系统进行时间序列预测[9,24,3]。 在我们的方法中，我们将动态系统实例化为双向 RNN，即用双向循环动力学来估算缺失值。 特别是，我们做出了以下技术贡献：

We design a bidirectional RNN model for imputing missing values. We directly use RNN for predicting missing values, instead of tuning weights for smoothing as in [10]. Our method does not impose specific assumption, hence works more generally than previous methods.

We regard missing values as variables of the bidirectional RNN graph, which are involved in the backpropagation process. In such case, missing values get delayed gradients in both forward and backward directions with consistency constraints, which makes the estimation of missing values more accurate.

We simultaneously perform missing value imputation and classification/regression of applications jointly in one neural graph. This alleviates the error propagation problem from imputation to classification/regression and makes the classification/regression more accurate.

We evaluate our model on three real-world datasets, including an air quality dataset, a health-care dataset and a localization dataset of human activities. Experimental results show that our model outperforms the state-of-the-art models for both imputation and classification/regression accuracies.
- 我们设计了一个双向 RNN 模型来估算缺失值。我们直接使用 RNN 来预测缺失值，而不是像 [10] 中那样调整权重以进行平滑处理。我们的方法没有强加特定假设，因此比以前的方法更普遍。

- 我们将缺失值视为双向 RNN 图的变量，参与反向传播过程。在这种情况下，缺失值在具有一致性约束的前向和后向方向上都得到延迟梯度，这使得对缺失值的估计更加准确。

- 我们同时在一个神经图中共同执行应用程序的缺失值插补和分类/回归。这缓解了从归类到分类/回归的错误传播问题，并使分类/回归更加准确。

- 我们在三个真实世界的数据集上评估我们的模型，包括空气质量数据集、医疗保健数据集和人类活动的本地化数据集。实验结果表明，我们的模型在插补和分类/回归精度方面都优于最先进的模型。



## 2 related work
There is a large body of literature on the imputation of missing values in time series. We only mention a few closely related ones. The interpolate method tries to fit a "smooth curve" to the observations and thus reconstruct the missing values by the local interpolations [19, 14]. Such method discards any relationships between the variables over time. The autoregressive method, including ARIMA, SARIMA etc., eliminates the non-stationary parts in the time series data and fit a parameterized stationary model. The state space model further combines ARIMA and Kalman Filter [14, 15], which provides more accurate results. Multivariate Imputation by Chained Equations (MICE) [2] first initializes the missing values arbitrarily and then estimates each missing variable based on the chain equations. The graphical model [20] introduces a latent variable for each missing value, and finds the latent variables by learning their transition matrix. There are also some data-driven methods for missing value imputation. Yi et al. [32] imputed the missing values in air quality data with geographical features. Wang et al. [30] imputed missing values in recommendation system with collaborative filtering. Yu et al. [34] utilized matrix factorization with temporal regularization to impute the missing values in regularly sampled time series data.

关于时间序列中缺失值的插补有大量文献。我们只提到一些密切相关的。 interpolate 方法试图将“平滑曲线”拟合到观测值，从而通过局部插值重建缺失值 [19, 14]。这种方法随着时间的推移丢弃了变量之间的任何关系。自回归方法，包括ARIMA、SARIMA等，消除了时间序列数据中的非平稳部分，拟合出参数化的平稳模型。状态空间模型进一步结合了 ARIMA 和卡尔曼滤波器 [14, 15]，提供了更准确的结果。链式方程的多元插补 (MICE) [2] 首先任意初始化缺失值，然后根据链式方程估计每个缺失变量。图模型[20]为每个缺失值引入了一个潜在变量，并通过学习它们的转移矩阵来找到潜在变量。还有一些用于缺失值插补的数据驱动方法。易等人。 [32] 用地理特征估算空气质量数据中的缺失值。王等人。 [30] 使用协同过滤在推荐系统中估算缺失值。于等人。 [34] 利用具有时间正则化的矩阵分解来估算定期采样的时间序列数据中的缺失值。

Recently, some researchers attempted to impute the missing values with recurrent neural networks [7, 10, 21, 12, 33]. The recurrent components are trained together with the classification/regression component, which significantly boosts the accuracy. Che et al. [10] proposed GRU-D, which imputes missing values in health-care data with a smooth fashion. It assumes that a missing variable can be represented as the combination of its corresponding last observed value and the global mean. GRU-D achieves remarkable performance on health-care data. However, it has many limitations on general datasets [10]. A closely related work is M-RNN proposed by Yoon et al. [33]. M-RNN also utilizes bi-directional RNN to impute missing values. Differing from our model, it drops the relationships between missing variables. The imputed values in M-RNN are treated as constants and cannot be sufficiently updated.

最近，一些研究人员试图用循环神经网络来估算缺失值 [7, 10, 21, 12, 33]。 循环组件与分类/回归组件一起训练，这显着提高了准确性。 切等人。 [10] 提出了 GRU-D，它以平滑的方式估算医疗保健数据中的缺失值。 它假设缺失变量可以表示为其对应的最后观察值和全局平均值的组合。 GRU-D 在医疗保健数据上表现出色。 然而，它对一般数据集有很多限制[10]。 Yoon 等人提出的 M-RNN 是一项密切相关的工作。 [33]。 M-RNN 还利用双向 RNN 来估算缺失值。 与我们的模型不同，它丢弃了缺失变量之间的关系。 M-RNN 中的估算值被视为常数，无法充分更新。

## 预备工作































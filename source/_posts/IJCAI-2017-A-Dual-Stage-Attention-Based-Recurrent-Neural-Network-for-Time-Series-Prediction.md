---
title: >-
  [IJCAI 2017]A Dual-Stage Attention-Based Recurrent Neural Network for Time
  Series Prediction
date: 2018-06-09 18:46:59
tags:
- time series prediction
- encoder decoder
- hierarchical
- attention
categories:
- paper notes
- IJCAI 
- 2017
mathjax: true
---

{% centerquote %}论文提出了基于注意力机制的两阶段循环神经网络（DA-RNN），在第一阶段（encoder），引入input attention mechanism对每一时刻的外部输入自适应性地提取相关性；在第二阶段（decoder），引入temporal attention mechanism捕获encoder的长期时序依赖信息。{% endcenterquote %}

<!-- more -->

#### 任务

时间序列预测，给定`1~T-1`时刻的目标序列以及`1~T-1`时刻的外部序列，预测T时刻的目标值。

#### 模型结构

![DA-RNN](https://github.com/yesic/pic/blob/master/dual/dual-1.jpg?raw=true)

##### encoder

1. 使用LSTM作为encoder和decoder，其中状态更新如下 ：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-2.jpg?raw=true)

2. 引入input attention mechanism：对每一时刻$\mathbf{X_t}$的n维变量使用attention

   ![](https://github.com/yesic/pic/blob/master/dual/dual-3.jpg?raw=true) 

3. 使用$\alpha_t^k$对$\mathbf{X_t}$加权求和，更新$\mathbf{X_t}$：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-4.jpg?raw=true)

4. 使用$\tilde{X_t}$更新`1`中的状态方程：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-5.jpg?raw=true)



##### decoder

1. 引入temporal attention mechanism：在decoder的第t时刻，对encoder所有隐藏层状态做attention 

   ![](https://github.com/yesic/pic/blob/master/dual/dual-6.jpg?raw=true)

2. 计算t时刻的上下文向量$\mathbf{c_t}$：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-8.jpg?raw=true)

3. 利用$\mathbf{c_t}$更新目标序列的输入值$\mathbf{y_{t-1}}$为$\mathbf{\tilde{y}_{t-1}}$：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-7.jpg?raw=true)

4. 更新第t时刻decoder的隐藏层状态$\mathbf{d_t}$：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-9.jpg?raw=true)

   其中$f_2$的更新状态方程如`encoder-1`：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-10.jpg?raw=true)

![](https://github.com/yesic/pic/blob/master/dual/dual-11.jpg?raw=true)

1. 则第t时刻的预测值$\mathbf{\hat{T}_t}$为：

   ![](https://github.com/yesic/pic/blob/master/dual/dual-12.jpg?raw=true)

   

#### 实验

作者在两个数据集上和ARIMA，NARX RNN，Encoder-Decoder，Attention RNN模型进行了比较，均获得了最好的结果。 

![](https://github.com/yesic/pic/blob/master/dual/dual-13.jpg?raw=true)
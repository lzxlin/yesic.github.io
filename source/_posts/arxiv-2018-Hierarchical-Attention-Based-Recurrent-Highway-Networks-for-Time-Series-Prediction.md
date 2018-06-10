---
title: >-
  [arxiv 2018]Hierarchical Attention-Based Recurrent Highway Networks for Time
  Series Prediction
date: 2018-06-09 22:21:15
tags:
- encoder decoder
- time series prediction
- hierarchical
- attention
categories:
- paper notes
- arxiv
- 2018
mathjax: true
---

{% centerquote %}论文提出了一种可以端到端学习的深度学习模型，Hierarchical Attention-Based Recurrent Highway Networks(HARNN)，可以将外部序列的时空特征提取和目标序列的时空动态建模结合到一个简单的框架。{% endcenterquote %}

<!-- more -->

> 论文模型借鉴来源：[A Dual-Stage Attention-Based Recurrent Neural Network for Time Series Prediction](https://yesic.github.io/2018/06/09/IJCAI-2017-A-Dual-Stage-Attention-Based-Recurrent-Neural-Network-for-Time-Series-Prediction/)

******



#### 模型贡献

- 利用卷积网络学习外部输入（**x**）中变量之间的空间特征。接着利用RHN（recurrent highway network）在不同层构建不同的语义，建模时序动态。
- 提出层次化注意力机制。
- 在获得高准确率的同时，可以捕获时间序列中的突然振荡或者变动。

#### 任务

时间序列预测，给定`1~T-1`时刻的目标序列以及`1~T-1`时刻的外部序列，预测T时刻的目标值。

#### 模型结构

encoder和decoder均使用多层RHN，在encoder使用卷积网络提取外部序列的时空特征，在decoder使用层次化注意力机制对时序依赖动态建模。

![](https://github.com/yesic/pic/blob/master/harnn/harnn-1.jpg?raw=true)

##### encoder

1. 将外部输入$(\mathbf{x_1,x_2,...,x_{T-1}})$通过几层卷积、池化操作后输给一个全连接层，得到一个特征向量 $(\mathbf{w_1,w_2,...,w_{T-1}})$ 

2. 利用RHN对`encoder-1`中的特征向量进行时序动态建模。$\mathbf{h}_t^{[k]}$表示第k层第t时刻的隐藏层状态 

   ![](https://github.com/yesic/pic/blob/master/harnn/harnn-2.jpg?raw=true)

   ![](https://github.com/yesic/pic/blob/master/harnn/harnn-3.jpg?raw=true)

##### decoder

1. 多层注意力机制：在t时刻，对encoder每一层的1~T-1时刻的隐藏层状态做attention。其中$\mathbf{s_{t-1}}=\mathbf{s}_{t-1}^{[k]}$ ，t-1时刻decoder第k层隐藏层状态。

   ![](https://github.com/yesic/pic/blob/master/harnn/harnn-4.jpg?raw=true)

2. 则第k层第t时刻的子上下文$\mathbf{d}_t^{[k]}$为

   ![](https://github.com/yesic/pic/blob/master/harnn/harnn-5.jpg?raw=true)

   将t时刻所有层的子上下文拼接，得到t时刻的上下文$\mathbf{d}_t$：

   ![](https://github.com/yesic/pic/blob/master/harnn/harnn-6.jpg?raw=true)

3. 更新decoder输入$\mathbf{y}_t$为$\mathbf{\tilde{y}_t}$：

   ![](https://github.com/yesic/pic/blob/master/harnn/harnn-7.jpg?raw=true)

4. 解码器RHN第k层隐藏层状态$\mathbf{s}_{t}^{[k]}$：

    ![](https://github.com/yesic/pic/blob/master/harnn/harnn-8.jpg?raw=true)

5. 第T时刻预测值$\mathbf{\hat{y}_T}$：

   ![](https://github.com/yesic/pic/blob/master/harnn/harnn-9.jpg?raw=true)



#### 实验

作者在三个数据集上和ARIMA，LSTM，GRU，DA-RNN模型比较，均获得最好的结果。 

![](https://github.com/yesic/pic/blob/master/harnn/harnn-10.jpg?raw=true)
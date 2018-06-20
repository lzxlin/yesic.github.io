---
title: '[CVPR 2018]Dual Skipping Networks'
date: 2018-06-20 23:30:58
tags:
- object classification
- layyer skipping
- coarse-to-fine
categories:
- paper notes
- CVPR 
- 2018
mathjax: true
---

{% centerquote %}Inspired by the recent study on the hemispheric specialization and coarse-to-fine perception, we proposed a novel left-right asymmetric layer skippable network for coarse-to-fine object categorization.    {% endcenterquote %}

<!-- more -->

### Introduction

在人类的大脑上，尽管关于视觉分析的具体位置和方法还有很多的争论，但是有相当多的证据证明，视觉分析主要发生在一个默认的粗到细的序列上（coarse-to-fine sequence），如图（1）。例如，快速地给某人看图片（1）时，只能接收到非常粗的视觉刺激，只能看到砂子和雨伞，通常是低空间频率的（Low spatial frequencies）。如果给定一个较长的观察时间，可以接收到一些细的视觉感知，通常时高空间频率的（high spatial frequencies）。

最近生物研究表明，大脑的两个半球功能在处理空间频率信息（spatial frequency information）时并不是完全一样的，左半球（LH）主要处理高空间频率信息，而右半球（RH）主要处理低空间频率信息，如图（2-A，2-B）。

受到大脑结构的启发，文中提出了dual skipping network（DSN），网络有左右两个分支，这两个分支不对称，并且层可以跳跃。模型可以用一个简单的框架实现粗到细的物体分类。

尽管空间频率不能等同于识别的粒度，DSN的工作原理和大脑半球形似，取决于监督信息的粒度。

**Contributions**     

* 受到最近神经科学研究的启发，提出左右分支网络结构解决粗到细的物体分类任务。
* 提出层跳跃机制（layer-skipping mechanism），在测试阶段可以跳跃一些层。
* 采用自顶向下的反馈机制，通过高层的global语义信息来指导细粒度分类。
* 构建一个新的数据集small-big MNIST，每个MNIST的global数字由local数字组成。

![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/2bmldBD0cf.png?imageslim)

### Model

模型由左右两个分支组成，每个分支由shared convolutional layer，skip-dense blocks，transition layers，pooling layer和classification layer组成。

左分支负责细粒度分类，右分支负责粗粒度分类，左右分支的结构是一样的，但是右分支有“Guide"箭头指导左分支。

![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/bbdhbLfaj0.png?imageslim)

#### *shared convolutional layer*

两个分支的共享卷积层。

#### *Left/Right subnet*

两个分支都是由skip-dense blocks和transition交替堆叠而成。

skip-dense blocks结构如上图最右所示，可以看作是一个级别的视觉感知抽象，其中gating network学习当前的块信息是否流向dense layer。

transition由$1\times1$的卷积和池化操作组成，控制下一个skip-dense block的特征图数量和空间大小。

#### *skip-dense block*

skip-dense block由一系列的gating network和dense layer堆叠而成，其中每个gating network和dense layer可以看作resNet中的残差连接。

**<u>dense layer</u>**     dense layer是由denseNet或者resNet组成。denseNet和resNet主要不同在于，残差连接的方式不一样，denseNet是通道拼接，resNet是按元素相加。

**<u>gating network</u>**     gating network负责路径的选择。对于N维的特征输入，经过一个$N\times1$的全连接层得到一个标量输出，在通过一个阈值函数（threshold function）。

**<u>threshold function of gating network</u>**      给定一个激活值或者输入，阈值函数需要判断是否要跳过接下来的dense layer。直观上，阈值函数是一个二分类，文中采用了hard sigmoid function。<u>文中的实验中显示hard sigmoid要比soft sigmoid效果要好，soft sigmoid就是常用的sigmoid函数。</u>
$$
hard \ sigm(x)=max(0,min(kx+\frac{1}{2},1))
$$
斜率参数$k$ 是一个关键参数，决定dense layer的输出缩放比例，$k$初始化为1，每个epoch按照一个固定值增加。

hard sigmoid是logistic sigmoid激活函数的分段线性近似，它更易计算，使得学习计算的速度更快，尽管首次派生值为零可能导致静默神经元过慢的学习率。

阈值函数会和卷积输出的每个单元相乘，影响层的分类重要性。

#### *Guide*

较快的coarse/global分支可以以自顶向下的方式，通过global上下文信息引导较慢的fine/local分支。模型中，选择粗分支的最后一个skip-dense block的输出特征，去引导细分支中最后一个transition layer。

粗分支的最后一个skip-dense block的输出特征通过双线性采样和细分支的最后一个transition layer的输入特征拼接，输入给细分支的最后一个transition layer。

### Experiments

**Datasets**

* sb-MNIST（不采用guide）
* CIFAR-100
* CUB-200-2011
* stanford cars

**baselines**

* Feedback Net
* DenseNet
* ResNet

**results**

* sb-MNIST result

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/F26CBdC7bE.png?imageslim)

* CIFAR-100

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/FFkLk0gF5J.png?imageslim)


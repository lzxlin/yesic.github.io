---
title: '[CVPR 2018]A Neural Multi-sequence Alignment TeCHnique (NeuMATCH)'
date: 2018-06-19 23:42:10
tags:
- sequence alignment
categories:
- paper notes
- CVPR
- 2018
---

{% centerquote %}论文提出了一种端到端训练的模型，称为NeuMATCH，用于处理多个异源（即对齐的两个目标的来源不同，例如视频和文本的对齐）序列对齐的问题。

{% endcenterquote %}

<!-- more -->

### Introduction

序列对齐主要包含三种类型：一对一，一对多，非单调。

![mark](http://pakzslacd.bkt.clouddn.com/blog/180620/aeCh1DLEf9.png?imageslim)

传统的序列对齐方法包括两个阶段：1）学习两个句子的相似性度量；2）找到最优的对齐路径。例如DTW，CTW。

基于DTW的方法属于马尔科夫假设，只考虑了局部的上下文信息，然而有利于对齐的上下文信息可能分散在整个序列，例如一些叙述性的电影。

文中采用的是视频和文本对齐的数据，尤其是包含一些叙述性的内容，例如电影。选择叙述性的内容原因在于，由于事件之间存在大量的因果和时间相互作用，是计算理解中最具挑战性的。

**Contributions**  1）论文提出了一种端到端的循环框架，用于异源端个序列对齐问题。不像之前的方法，我们在决策时考虑了更多的上下文信息；2）标注了一个新的[数据集](https://github.com/pelindogan/NeuMATCH) ，包含了电影摘要视频。

### Approach

**任务**：给定两个序列，视频序列和文本序列。视频序列包含了一系列连续的镜头（video clips），记为$\mathcal{V}={\{V_i\}}_{i=1,2...,N}$ 。文本序列包含了一系列连续的句子，记为${\mathcal{S}}={\{S_i\}}_{i,2,...,M}$ 。任务是找到一个函数$\pi$将视频段的下标映射到相应的句子：$\langle V_i,S_i \rangle$ 。

**<u>由于需要学习相似性度量的上下文信息分散在整个序列上，所以在决策时需要考虑过去和未来的信息。</u>** 

### Architecture

整个模型由四个LSTM组成，包含了输入视频序列（Video Stack），输入文本序列（Text Stack），先前对齐动作（Action Stack），先前对齐匹配（Match Stack）。

**<u>这里的stack不是堆叠的意思，而是数据结构中的stack，先入先出。这也是本文的一个亮点。</u>**

![mark](http://pakzslacd.bkt.clouddn.com/blog/180620/mKc05J5ag4.png?imageslim)  

#### *Language and Visual Encoders*

先对每个镜头（video clips）和每个句子编码，然后在通过可选择的预训练，将编码后对的镜头和句子嵌入到同一个空间。通过预训练可以得到很好的初始化。

**Video Encoder**  利用VGG-16训练镜头的每一帧，将第一层全连接层的特征提取出来，为4096维。对所有帧的特征进行mean pooling，作为每个镜头的特征。镜头特征在经过三层全连接层，最后的到video的编码信息$v_i$，表示第i个镜头（clip）的编码信息。

**Sentence Encoder**   对每个词使用GloVe嵌入，在经过两层LSTM后，得到最后一层隐藏层的特征$h_t$，在经过三层全连接层得到每个句子的编码信息$s_i$。

**Enc

####  *The NeuMATCH Alignment Network*

**LSTM Stacks** 

* *Video Stack*   对于每一时刻$t$，video stack包含了还没处理的序列$V_t,V_{t+1},...,V_N$ 。LSTM的方向是从$V_N$到$V_t$，允许信息从未来的clip流到当前的clip，将这个LSTM作为video stack，其隐藏层特征为$h_t^V$。

* *sentence stack*  处理方法和video stack一样， 对于每一时刻$t$，sentence stack包含了还没处理的序列$S_t,S_{t+1},...,S_N$，其隐藏层特征为$h_t^S$。

* *action stack*   action stack负责储存过去所有的对齐动作，动作极为$A_{t-1},...,A_1$，每个动作为one-hot编码。动作的信息流不同于video stack和sentence stack，信息是从第一个动作流向最后一个动作，其隐藏层状态记为$h_{t-1}^A$。

  文中定义了五种基本的对齐动作，它们一起处理两个序列对齐的问题，包含了不匹配（例如，一个clip没有句子和它匹配，记为null）以及一对多匹配（一个句子和多个clip匹配）。

  五个对齐动作分别为：Pop Clip (PC),Pop Sentence (PS), Match (M), Match-Retain Clip (MRC), and Match-Retain Sentence (MRS)。下面是一个操作例子。

  **<u>需要注意的是，并不是这五个动作都需要一起使用，具体问题具体分析，例如一对一的对齐，每个句子和某个clip匹配，允许某个clip和没有句子匹配，则只需要Pop Clip以及Match；一对多的对齐，允许每个句子和多个clip匹配，允许某个clip和没有句子匹配，每个句子至少和一个clip匹配，则只需要Pop Clip, Pop Sentence, and Match- Retain Sentence。</u>**

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180620/5bF8KCldig.png?imageslim)

  

* *matched stack*   match stack包含了先前已经匹配的clip和sentence，其中最后匹配的置于stack的顶部。本文考虑一个句子可以匹配多个clip。因为匹配的clip在内容上是相似的，所以一个句子匹配多个clip的，取它们的均值，即$v_i=\sum_j^Kv_j/K$。则LSTM的输入为sentence和clip的拼接，$r_i=[s_i,v_I]$ 。最后一个隐藏层状态为$h_{t-1}^M$。

**Alignment Action Prediction**

每一时刻$t$，四个stack的状态为$\Psi_t=(V_{t^+},S_{t^+},A_{(t-1)^-},R_{1^+})$ 。则第$t$时刻$A_t$的条件概率为
$$
P(A_t|\Psi_t)=P(A_t|V_{t^+},S_{t^+},A_{(t-1)^-},R_{1^+})
$$
上面的计算方式为$\psi_t$经过两层全连接层然后在softmax。

根据链式法则，
$$
P(A_1,...,A_N|\mathcal{V},\mathcal{S})=\prod_{t=1}^NP(A_t|A_{(t-1)^-},\Psi_t)
$$
将交叉熵作为损失函数。

### Experimental Evaluation

**dataset**

HM-1为在序列中随机插入一些不同电影的片段，但是具有相似的情节，HM-2随机删除一些句子，YMS是论文提出的new dataset，数来源于youtube。

![mark](http://pakzslacd.bkt.clouddn.com/blog/180620/I4giJf559F.png?imageslim)

**baselines**

* Minimum Distance (MD),
* Dynamic Time Warping (DTW)
* Canonical Time Warping (CTW)

**results**

* 一对一

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180620/ECgI7i0E11.png?imageslim)

* 一对多

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180620/7e4FiEmA4i.png?imageslim)


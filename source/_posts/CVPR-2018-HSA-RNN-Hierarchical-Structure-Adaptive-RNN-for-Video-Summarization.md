---
title: >-
  [CVPR 2018]HSA-RNN: Hierarchical Structure-Adaptive RNN for Video
  Summarization
date: 2018-06-21 16:30:38
tags:
- video summarization
- hierarchical
categories:
- paper notes
- CVPR
- 2018
mathjax: true
---

{% centerquote %}In this paper, we propose a Hierarchical Structure Adaptive RNN (HSA-RNN) for the video summarization task, which can adaptively exploit the video structure and generate the summary simultaneously. {% endcenterquote %}

<!-- more -->

### Introduction

类似文本序列，视频序列也是一种层次化的结构，由帧（frame）组成镜头（shot），镜头组成视频（video）。

视频摘要（video summarization）可以分为三个类别，镜头（shots），帧（shots）以及物体（objects）。论文中讨论的是镜头级别的视频摘要，从视频中挑选出关键的shots，因为它更好地保留了视频内容的动态信息和时空一致性。

视频摘要可以分为两个阶段：1）镜头划分（shot segmentation）；2）挑选关键镜头（key shot selection）。

通常更多的方法关注的key shot selection，而shot segmentation一般根据帧突然变化、运动幅度变化，或者固定长度划分，但是这样的方法忽略了视频的结构。

针对先前shot segmentation方法的缺点，文中提出了一种层次化自适应的网络HSA-RNN，可以联合探索视频的结构和视频摘要。**<u>更加关注shot segmentation</u>**

### Approach

HSA-RNN包含两层：第一层探索视频的结构，划分shot；第二层根据第一层划分的shot生成视频摘要，即挑选出key shot。

![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/KiEk15cJLI.png?imageslim)

####  *layer 1*

输入所有帧的特征序列$(f_1,f_2,...,f_n)$，输出所有镜头的特征序列$(s_1,s_2,...,s_m)$。n和m分别是帧和镜头的数量。

一种简单的方法就是利用LSTM应用于整个视频，每一步通过一个输出阈值判断shot的边界，如图(a)。但是这种方法有两个缺点：

1）难以应用双向LSTM到这种结构，在检测shot边界时前后的信息都是很重要的。

2）阈值难以设定。

文中使用了**<u>滑动的双向LSTM</u>**解决这个问题，也是论文的亮点，如图(b)。

![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/G207FmiI02.png?imageslim)

如图(b)，它就像一个固定长度的一维滤波器在帧序列上滑动。双向LSTM的长度$k$凭经验选择（文中k=240），LSTM每次滑动的长度等于检测出的镜头（detected shot）的长度。

步骤：

* 某一时刻双向LSTM处理的帧序列$(f_1,f_2,..,f_k)$，经过隐藏层后有
  $$
  h_t^{1,f}=LSTM(f_t,h_{t-1}^{1,f})\\
  h_t^{1,b}=LSTM(f_t,h_{t+1}^{1,b})
  $$
  $h_t^{1,f}$和$h_t^{1,b}$是双向LSTM的隐藏层状态。将这两个状态拼接用来计算每一帧的作为shot的边界的置信度$c_t$。
  $$
  c_t=softmax(Relu(W_c[h_t^{1,f},h_t^{1,b}]+b_c))
  $$
  其中，$c_t$是一个二维向量，包含了是帧作为shot的边界的置信度与否。
  $$
  t^*=max\{c_1(1),c_2(1),...,c_k(1)\}
  $$
  将$h_{t^*}^{1,f}$作为shot的边界。滑动的LSTM下一次处理的帧序列为$(f_{t^*+1},f_{t+2},...,f_{t^*+k})$。

#### *layer 2*

同样利用双向LSTM处理layer 1得到的shot序列$(s_1,s_2,...,s_m)​$。
$$
h_t^{2,f}=LSTM(s_t,h_{t-1}^{2,f})\\
h_t^{2,b}=LSTM(s_t,h_{t+1}^{2,b})
$$
<u>将隐藏层状态和$s_t$拼接</u>，计算每一shot作为key shot生成视频摘要的置信度。
$$
p_t=softmax(Relu(W_p[h_t^{2,f};h_t^{2,b};s_t]+b_p))
$$
损失函数：

![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/8A9dJ8KGik.png?imageslim)

T为视频的数量，$n^{(i)}$是视频的帧数。考虑到生成的shot的长度和人为设定的有不同的间隔差异，这里的预测是frame-level的预测$\hat{p}_t^{(i)}$，通过得到的shot的预测结果分配给shot所包含的frame。$L(\cdot)$为交叉熵。

### Experiments

**datasets**

* SumMe
* TVSum
* CoSum
* VTW

**results**

* boundary detection

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/LIj24fB2Fj.png?imageslim)

* video summatization 

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/f0B3bh76AE.png?imageslim)

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180621/mbAaFfH1LE.png?imageslim)


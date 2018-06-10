---
title: '[NAACL 2016]Hierarchical Attention Networks for Document Classification'
date: 2018-06-10 00:19:35
tags:
- document classification
- encoder decoder
- attention
- hierarchical
categories:
- paper notes
- NAACL
- 2016
mathjax: true
---

{% centerquote %}论文提出了一种层次化的注意力网络用于文本分类，模型有两个特点：1）它有一个层次化的结构，反映了文本的层次化结构（由词组成句；由句组成文本）；2）它有两个level的注意力机制，分别是word-level和sentence-level。 {% endcenterquote %}

<!-- more -->

#### 模型结构

模型由四部分组成：word encoder，word attention，sentence encoder，sentence attention

![](https://github.com/yesic/pic/blob/master/han/han-1.jpg?raw=true)

##### word encoder

1. 对每个词进行word embedding，在通过双向GRU提取隐藏层状态，将两个方向的隐藏层状态拼接， $w_{it}$为文本第i个句子的第t个词 。

   ![](https://github.com/yesic/pic/blob/master/han/han-2.jpg?raw=true)

![](https://github.com/yesic/pic/blob/master/han/han-3.jpg?raw=true)

##### word attention

1. 对每个句子的所有词做attention，然后计算每个句子向量$s_i$：

![](https://github.com/yesic/pic/blob/master/han/han-4.jpg?raw=true)

##### sentence encoder

1. 和word encoder方式一样，利用双向GRU提取每个句子向量$s_i$的信息，然后将两个方向的隐藏层状态拼接

   ![](https://github.com/yesic/pic/blob/master/han/han-5.jpg?raw=true)

   ![](https://github.com/yesic/pic/blob/master/han/han-6.jpg?raw=true)

##### sentence attention

和word attention方式一样，对文本的每个句子做attention，然后计算文本向量v，将v用softmax分类。

![](https://github.com/yesic/pic/blob/master/han/han-7.jpg?raw=true)

#### 实验

作者在6个大规模文本分类数据集上做了实验，和众多SVM，CNN，LSTM比较均获得了最好的结果。 

![](https://github.com/yesic/pic/blob/master/han/han-8.jpg?raw=true)


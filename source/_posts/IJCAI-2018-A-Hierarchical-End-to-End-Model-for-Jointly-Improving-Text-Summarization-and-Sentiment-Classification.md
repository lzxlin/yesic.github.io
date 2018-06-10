---
title: >-
  [IJCAI 2018]A Hierarchical End-to-End Model for Jointly Improving Text
  Summarization and Sentiment Classification
date: 2018-06-10 00:44:10
tags:
- encoder decoder
- text summarization
- sentiment classification
categories:
- paper notes
- IJCAI
- 2018
mathjax: true
---

{% centerquote %}论文提出了一种层次化端到端模型，整合了文本摘要生成和情感分类任务 。{% endcenterquote %}

<!-- more -->

> 模型借鉴来源：[Hierarchical Attention Networks for Document Classification](https://yesic.github.io/2018/06/10/NAACL-2016-Hierarchical-Attention-Networks-for-Document-Classification/)

*****

#### 任务

给定样本$(x^i,y^i,l^i)$，包含了文本，摘要，情感标签，同时进行摘要生成和情感分类。其中$L_i$为文本的单词数， $M_i$为摘要的单词数。

<div align=center>

![](https://github.com/yesic/pic/blob/master/man/man-1.jpg?raw=true)

</div>

#### 模型结构

模型包括三部分：text encoder，summary decoder，sentiment classifier

<div align=center>

![](https://github.com/yesic/pic/blob/master/man/man-2.jpg?raw=true)

</div>

##### text encoder

1. 使用双向LSTM作为encoder，产生上下文信息$h={h_1,h_2,...,h_L}$

<div align=center>

![](https://github.com/yesic/pic/blob/master/man/man-3.jpg?raw=true)</div>

##### summary decoder

Summary decoder包括三部分：单向LSTM，multi-view attention，word generator

1. 利用单向LSTM产生decoder的隐藏层输出$s_t$

   <div align=center>

   ![](https://github.com/yesic/pic/blob/master/man/man-4.jpg?raw=true)</div>

2. 利用multi-view attention生成摘要向量$\mathbf{v}^{(c)}$和情感向量$\mathbf{v}^{(t)}$，multi-view attention其实就是两个独立的global attention。摘要向量$\mathbf{v}^{(c)}$生成如下：

   <div align=center>

   ![](https://github.com/yesic/pic/blob/master/man/man-5.jpg?raw=true)</div>

3. 根据摘要向量 $\mathbf{v}^{(c)}$，利用生成摘要中的每个词。 

   <div align=center>

   ![](https://github.com/yesic/pic/blob/master/man/man-6.jpg?raw=true)</div>



##### sentiment classifier

1. 将所有时间步的情感向量 $\mathbf{v}^{(t)}$收集起来

   <div align=center>

   ![](https://github.com/yesic/pic/blob/master/man/man-7.jpg?raw=true)</div>

2. 为了获得原文本的上下文信息，使用highway机制，将上下文信息h作为分类器输入的一部分，r为情感分类的输入向量

   <div align=center>

   ![](https://github.com/yesic/pic/blob/master/man/man-8.jpg?raw=true)</div>



#### 实验

作者在亚马逊在线评论数据集上做了实验，在和其他模型的对比上均获得了最好的结果。
---
title: '[ICLR 2018]Hierarchical Representations for Efficient Architecture Search'
date: 2018-06-10 01:24:00
tags:
- hierarchical
categories:
- paper notes
- ICLR
- 2018
mathjax: true
---

{% centerquote %}论文结合了一种新型层次化遗传表示体系（hierarchical genetic representation scheme），可以模仿人类专家常用的模块化设计模式，支持复杂的拓扑结构。分别在CIFAR-10和ImageNet上获得了top-1错误率为3.6%和20.3%。 {% endcenterquote %}

<!-- more -->

在这项工作中，作者通过强加一个层次化的网络来限制搜索空间结构，允许每层是一些灵活的网络拓扑结构（有向无环图）。底层是一些基本操作，例如卷积，池化等，更高层的计算图，或者modif，是由低层modif作为它们的构建块来组成，最高层的modif通过堆叠形成最终的网络结构。如下图所示。

![](https://github.com/yesic/pic/blob/master/hgrs/hgrs-1.jpg?raw=true)



作者通过进化搜索或者随机搜索来发现层次化结构。基于遗传算法，作者定义了一些变异（mutation），每次选择某一层的某个motif的某条边发生变异，这可以使得modif的结构发生改变（删除，添加，修改），将验证集的准确率作为fitness。

在实验中，提出的搜索框架只学习cell的结构，而不是整个模型。原因是这样能够快速计算fitness，然后可以将对应的genotype转移到大的模型，也就是用更少的cell计算fitness，更多的cell评估模型。下图是使用框架搜索优化过的cell构建的图像分类模型。

![](https://github.com/yesic/pic/blob/master/hgrs/hgrs-2.jpg?raw=true)


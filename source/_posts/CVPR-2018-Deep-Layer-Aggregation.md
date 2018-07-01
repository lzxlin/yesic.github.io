---
title: '[CVPR 2018]Deep Layer Aggregation'
date: 2018-07-01 10:44:30
tags:
- aggregation
- hierarchical
categories:
- paper notes
- CVPR
- 2018
mathjax: true
---

{% centerquote %}Aggregation is a decisive aspect of architecture, and as the number of modules multiply their connectivity is made all the more important.By relating architectures for aggregating channels, scales, and resolutions we identified the need for deeper aggregation, and addressed it by iterative deep aggregation and hierarchical deep aggregation. {% endcenterquote %}

<!-- more -->

### Introduction

**More nonlinearity, greater capacity, and larger receptive fields generally improve accuracy but can be problematic for optimization and computation.** To overcome these barriers, different blocks or modules have been incorporated to balance and temper these quantities, such as bottlenecks for dimensionality reduction or residual, gated, and concatenative connections for feature and gradient propagation.Networks designed according to these schemes have 100+ and even 1000+ layers.    

Nevertheless, further exploration is needed on how to connect these layers and modules. Layered networks from LeNet  through AlexNet  to ResNet  stack layers and modules in sequence. Layerwise accuracy comparisons , transferability analysis , and representation visualization  show that deeper layers extract more semantic and more global features, **but these signs do not prove that the last layer is the ultimate representation for any task**. In fact, skip connections have proven effective for classification and regression and more structured tasks. Aggregation, like depth and width, is a critical dimension of architecture.    

In this work, we investigate how to aggregate layers to better fuse semantic and spatial information for recognition and localization.  **We introduce two structures for deep layer aggregation (DLA): iterative deep aggregation (IDA) and hierarchical deep aggregation (HDA).** 

**IDA focuses on fusing resolutions and scales while HDA focuses on merging features from all modules and channels. IDA follows the base hierarchy to refine resolution and aggregate scale stage-by-stage. HDA assembles its own hierarchy of tree-structured connections that cross and merge stages to aggregate different levels of representation.  **

**task:**  large-scale image classification, finegrained recognition, semantic segmentation, and boundary detection  

### Related work

**Our aggregation architectures are most closely related to leading approaches for fusing feature hierarchies. The key axes of fusion are semantic and spatial. Semantic fusion, or aggregating across channels and depths, improves inference of what. Spatial fusion, or aggregating across resolutions and scales, improves inference of where. Deep layer aggregation can be seen as the union of both forms of fusion.  **

### Deep Layer Aggregation

#### *iterative deep aggregation (IDA)*

Iterative deep aggregation follows the iterated stacking of the backbone architecture. We divide the stacked blocks of the network into stages according to feature resolution. Deeper stages are more semantic but spatially coarser. 

* conventional method

  (b)是FCN、FPN、U-net的方法，从粗粒度向细粒度融合。

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180701/emj9hkiClD.png?imageslim)

* IDA

  ![mark](http://pakzslacd.bkt.clouddn.com/blog/180701/B7HiEfe6I0.png?imageslim)

#### *Hierarchical Deep Aggregation(HDA)*

Hierarchical deep aggregation merges blocks and stages in a tree to preserve and combine feature channels. With HDA shallower and deeper layers are combined to learn richer combinations that span more of the feature hierarchy.    

![mark](http://pakzslacd.bkt.clouddn.com/blog/180701/dF3mIg69E9.png?imageslim)

#### *aggregation nodes*

特征融合：使用$1*1$的卷积

#### *blocks and stages*

The networks we instantiate in our experiments make use of three types of residual blocks.

* **Basic blocks** combine stacked convolutions with an identity skip connection.
*  **Bottleneck blocks** regularize the convolutional stack by reducing dimensionality through a 1×1 convolution. 
* **Split blocks** diversify features by grouping channels into a number of separate paths (called the cardinality of the split).



### Experiments

* ImageNet Classification    

* Fine-grained Recognition    

* Semantic Segmentation    

* Boundary Detection    

  


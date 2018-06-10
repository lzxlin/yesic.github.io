---
title: >-
  [NIPS 2017]Learning Hierarchical Information Flow with Recurrent Neural
  Modules
date: 2018-06-10 01:35:44
tags:
- hierarchical
categories:
- paper notes
- NIPS
- 2017
mathjax: true
---

{% centerquote %}论文受到新脑皮质（neocortical）通信方式的启发，提出了ThalNet。新脑皮质通信方式有两种，一种是直连，另一种是通过丘脑（thalamus）。论文受到第二种通信方式的启发，构建多个循环神经模块，将所有模块的特征发送到一个路由中心，使得模块在多个时间步能够共享特征。模型展示了模块对输入数据的链式处理，学习了层次化的路由信息，并且模型包含了前馈神经网络、跳跃连接、反馈连接等结构。{% endcenterquote %}

<!-- more -->

#### 模型结构

![](https://github.com/yesic/pic/blob/master/hif/hif-1.jpg?raw=true)

论文构建的模型包括四个模块，模块$f^1$接受input， $f^2$，$f^3$为侧模块，$f^4$负责输出，每个模块$f$可以是全连接层、GRU、LSTM等。对于每一时刻t，每个模块将其特征$\phi_t$发给路由中心 $\Phi$，其中每个模块特征由上下文$c_t$和一个可以选择的输入 $x_t$决定。对于下一时刻的上下文 $c_{t+1}$，每个模块通过阅读机制（read mechanism）选择性地读取路由中心$\Phi$的特征。对于每个模块的特征计算，上下文计算，路由中心，模型输出式子见下图。

![](https://github.com/yesic/pic/blob/master/hif/hif-2.jpg?raw=true)

阅读机制包括两种：静态阅读，动态阅读。静态阅读只取决于 $\Phi$，即$r^i(\Phi)$，而动态阅读不仅取决于 $\Phi$，还取决于当前模块的特征，即 $r^i(\Phi,\phi^i)$。

文中给出了四种阅读方法，其中weight normalization、Fast softmax比较稳定，效果比较好。 

![](https://github.com/yesic/pic/blob/master/hif/hif-3.jpg?raw=true)![](https://github.com/yesic/pic/blob/master/hif/hif-4.jpg?raw=true)



#### 实验

论文在三个任务上做了实验， Sequential Permuted MNIST、Sequential CIFAR-10、Text8 Language Modeling。其中前两个是图像的延迟预测，即将图像的像素每一行看作一个时刻，在输入最后一个时刻时预测出图像的类别，第三个任务是输入每个character，预测下一个character。 
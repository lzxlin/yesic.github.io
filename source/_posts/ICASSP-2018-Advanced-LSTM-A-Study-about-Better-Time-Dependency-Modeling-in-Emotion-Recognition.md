---
title: >-
  [ICASSP 2018]Advanced LSTM: A Study about Better Time Dependency Modeling in
  Emotion Recognition
date: 2018-07-01 10:19:17
tags:
- emotion recognition
- multi-task learning
- attention
categories:
- paper notes
- ICASSP
- 2018
mathjax: true
---

{% centerquote %}We proposed a new type of LSTM, A-LSTM, in this paper. This was a early study of A-LSTM. We applied it in the weighted pooling RNN for emotion recognition.  {% endcenterquote %}

<!-- more -->

### Introduction

LSTM每一时刻的状态依赖于上一个时刻，这种假设会约束RNN的建模容量。这篇论文，提出了一种LSTM的变体，advanced LSTM，即A-LSTM，每一时刻依赖于多个不同的时刻。

改进的方法就是利用了<u>**attention based weighted pooling layer**</u>

### Proposed approach

#### *attention based weighted pooling RNN*

依赖注意力机制学习每一个时间步的一个权重，$h_T$是过去的时间步，可以是一个固定的步长，例如3,4,5。

![El9Scq.png](https://s2.ax1x.com/2019/04/28/El9Scq.png)

#### *multi-task learning*

文中的任务是基于语音的动作识别，并且是多任务的学习，包括动作、说话者以及说话者性别的识别

[![El9PBT.png](https://s2.ax1x.com/2019/04/28/El9PBT.png)](https://imgchr.com/i/El9PBT)


#### *advanced LSTM*

* conventional LSTM

![El9CuV.png](https://s2.ax1x.com/2019/04/28/El9CuV.png)

* advanced LSTM

  注意：<u>(9)和(10)中的W是共享的</u>

![El9pj0.png](https://s2.ax1x.com/2019/04/28/El9pj0.png)
  
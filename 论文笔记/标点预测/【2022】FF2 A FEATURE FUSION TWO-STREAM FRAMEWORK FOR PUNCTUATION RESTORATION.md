# 【2022】FF2: A FEATURE FUSION TWO-STREAM FRAMEWORK FOR PUNCTUATION RESTORATION

> 本文提出了一种双流结构，其中一个流使用预训练模型捕获数据的语义特征；另一个流使用较小的随机初始化的模型捕获当前未加标点的数据特征。最后两个特征进行concat。

[(arxiv)](https://arxiv.org/pdf/2211.04699.pdf) [(论文)](D:\learning\论文\标点预测/【2022】FF2 A Feature Fusion Two-Stream Framework for Punctuation Restoration.pdf)

## 以往工作的缺陷

- 以往的工作使用预训练模型进行标点预测，然而这些预训练模型都是基于有标点的数据进行训练的，对于无标点的数据拟合可能效果不好。
- 一些工作（[[1]](https://arxiv.org/pdf/2003.02436.pdf) [[2]](http://proceedings.mlr.press/v119/bhojanapalli20a/bhojanapalli20a.pdf)）指出self-attention中存在Low-Rank Bottleneck问题，在多头自注意向量大小固定的情况下，增加多头自注意向量的头数会使每个头的向量大小减小，从而导致其理解能力的显著下降。

## 解决的问题

- 采用双流的网络结构，其中一个流使用预训练模型捕获语义特征；另一个流使用较小的模型捕获不含标点的数据特征。最后进行concat。
- 修改self-attention结构，使得多个heads之间可以进行信息交流。

## 方法

<img src="图片/FF2 A FEATURE FUSION TWO-STREAM FRAMEWORK FOR PUNCTUATION_1.png" style="zoom:75%;" />

- ITE为预训练模型，TNP为较小的随机初始化的模型
- Interaction Attention：在self-attention的各个heads之间进行信息交互

<img src="图片/FF2 A FEATURE FUSION TWO-STREAM FRAMEWORK FOR PUNCTUATION_2.png" style="zoom:75%;" />

<img src="图片/FF2 A FEATURE FUSION TWO-STREAM FRAMEWORK FOR PUNCTUATION_3.png" style="zoom:75%;" />

​	其中，$J^(k)$表示原始注意力分数，$P_{\lambda}$表示可学习的参数。通过对每个heads使用$P_{\lambda}$进行相乘， 从而在各个heads之间进行信息交互。

<img src="图片/FF2 A FEATURE FUSION TWO-STREAM FRAMEWORK FOR PUNCTUATION_4.png" style="zoom:75%;" />
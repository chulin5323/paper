# 【2023 1】A Frustratingly Easy Plug-and-Play Detection-and-Reasoning Module for Chinese Spelling Check

[arxiv](https://arxiv.org/pdf/2310.09119.pdf) [PDF](‪D:\learning\论文\GEC\【2023 EMNLP】A Frustratingly Easy Plug-and-Play Detection-and-Reasoning Module for.pdf) [code](https://github.com/THUKElab/DR-CSC) 

> 本文将CSC分解为三个流程：detection、reasoning、searching，并能更加直接和有效地利用外部知识（confusion set）。具体地，在研究中设计了一个即插即用的模块——detection-and-reasoning module（DR-CSC），该模块能和现有的非自回归（non-autoregressive）CSC模型进行结合，并提升其性能。

## 以往工作存在的缺陷

- 现有研究在结合外部知识（confusion set）方面存在一些缺陷，只是隐式地学习外部知识，缺乏可解释性和有效性。

## 解决的问题

- 将CSC分解为三个子任务：detection、reasoning、searching，三个子任务分别解释了任务中的：which（哪一个字符错误）、why（为什么该字符错误，即错误类型）、how（如何纠正该错误）。在训练过程中结合了外部知识——confusion set，从而使模型能意识到错误位置、错误类型以及来自三个子任务的外部知识。

<img src="D:\learning\论文\论文笔记\GEC\图片\DR-CSC_1.png" style="zoom:67%;" />

## 方法

<img src="D:\learning\论文\论文笔记\GEC\图片\DR-CSC_2.png" style="zoom:80%;" />

### Detection

- 检测每个字符的错误类型（二分类问题）
- 检测loss

<img src="D:\learning\论文\论文笔记\GEC\图片\DR-CSC_3.png" style="zoom:80%;" />

### Reasoning

- 检测错误字符的错误类型（形音字还是形近字错误）
- 推理loss——在计算loss时，仅保证错误字符参与loss计算

<img src="D:\learning\论文\论文笔记\GEC\图片\DR-CSC_4.png" style="zoom:80%;" />

### Searching

> 通过该方法，使得模型从confusion set中查询对应的形音字和形近字错误，比从整个词表搜索效果更好。

- 通过confusion set优化预测结果

<img src="D:\learning\论文\论文笔记\GEC\图片\DR-CSC_5.png" style="zoom:80%;" />

其中，$pc$和$vc$是01向量，维度为**词表大小**，分别表示$x_i$处的token，其所对应的形音字或者形近字，是形音字或形近字的为1，否则为0。

- 纠错预测输出概率为

<img src="D:\learning\论文\论文笔记\GEC\图片\DR-CSC_6.png" style="zoom:80%;" />

其中$P_S$为正常输出的预测概率。

- 搜索loss

​	<img src="D:\learning\论文\论文笔记\GEC\图片\DR-CSC_7.png" style="zoom:80%;" />
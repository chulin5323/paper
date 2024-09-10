# 【2023 **MuCGEC SOTA**】Improving Seq2Seq Grammatical Error Correction via Decoding Interventions

[PDF](D:\learning\论文\GEC\【2023 MuCGEC_SOTA】Improving Seq2Seq Grammatical Error Correction via Decoding Interventions.pdf) [arxiv](https://arxiv.org/pdf/2310.14534.pdf) [code](https://github.com/Jacob-Zhou/gecdi) 

> 本文在Seq2Seq GEC的基础上，提出了一种统一的解码干涉框架（unified decoding intervention framework），该框架通过额外的标准来递进式的评估所生成token的合适性，并动态的影响下一个生成token的选择。文中探究了两种干涉方式：**预训练因果语言模型（a pre-trained left-to-right language model critic）**和**递进式目标侧语法错误检测标准（an incremental target-side grammatical error detector critic）**。

## 以往工作存在的缺陷

- Seq2Seq需要在并行的数据上进行训练，但在GEC任务中，该方法往往是存在噪音（数据质量）并且数量有限（训练时一句话当作一个样本，而不是Seq2Edit那样将token当作样本，所以数据量更小）
- Seq2Seq方法在生成token的过程中，对于下一个token生成的正确性没有明确的意识（不清楚所生成的token是否正确）
- Seq2Seq方法效率较低，在大量数据上训练会耗费很多时间

## 解决的问题

- 提出一种解码干涉框架，使用额外的标准来递进式地判断下一个token生成的正确性，并动态地影响后续token的生成，该标准主要是通过减少**token分数的log概率**来惩罚token的生成；
- 提出了两种额外标准：**a pretrained left -to-right language model (LM)** 和 **an incremental target-side GED**；
- 通过解码干涉，来使模型能有效对所生成token的正确性有一定判断

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_1.png" style="zoom:80%;" />

## 方法

### 1、通常的Seq2Seq GEC model

- 递进式生成token

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_2.png" style="zoom:80%;" />

- 输出句子y的分数是所有预测token的log概率求和

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_3.png" style="zoom:80%;" />

- 训练期间使用了teacher forcing方法

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_4.png" style="zoom:80%;" />

- 使用beam search方法来找到最佳输出句子y

### 2、Decoding Intervention

> 对生成的token添加惩罚（改进方法：是否可以添加奖励？）惩罚措施有两种：LM和GED

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_5.png" style="zoom: 80%;" />

#### （1）Left-to-Right Pre-trained LM

- 语言模型生成的句子的概率

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_6.png" style="zoom:80%;" />

- 添加惩罚项——语言模型生成该token的概率越大，惩罚越小

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_7.png" style="zoom:80%;" />

#### （2）Incremental Target-side GED

-  GED将对所生成token的正确性进行判断，共有4个类别

> GED模型在判断时，一定要将原句子$x$也作为输入，不能仅接收生成的句子$y_{<t}$ 

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_8.png" style="zoom:80%;" />

- 添加惩罚项，如果模型判断为正确，那么该生成token的惩罚较小

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_9.png" style="zoom:80%;" />

#### （3）标准系数

> 基于假设：如果模型对它的预测有较高的置信度，那么认为模型是可信的

- $\lambda$的选择

<img src="D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_10.png" style="zoom:80%;" />

其中，$p_{\theta}(\cdot)$和$p_{\pi}(\cdot)$分别表示GEC模型的概率分布和语言模型的概率分布。

### 3、实验结果

![](D:\learning\论文\论文笔记\GEC\图片\Improving Seq2Seq Grammatical Error Correction via Decoding Interventions_11.png)

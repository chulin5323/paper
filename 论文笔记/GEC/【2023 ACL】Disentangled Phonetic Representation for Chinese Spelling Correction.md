# 【2023 ACL】Disentangled Phonetic Representation for Chinese Spelling Correction

[(arxiv)](https://arxiv.org/abs/2305.14783) [(PDF)](D:\learning\论文\GEC\【2023 ACL】Disentangled Phonetic Representation for Chinese Spelling Correction.pdf) [(code)](https://github.com/liangzh63/DORM-CSC) 

> 本文将文本表征和音标表征进行解耦，从而使得二者之间能直接进行信息交互；为了学习更加有效的语音表征，提出了一个pinyin-to-character的目标，使得模型能通过pinyin来预测相应的文本，并通过separation mask来屏蔽音标对文本的注意力，并使用自蒸馏模块（self-distillation）来确保文本信息在预测中发挥主要作用。

## 以往工作的缺陷

- 简单的将音标特征和文本特征进行融合，会减弱纠错时文本的表达能力
- 由于训练时将两个特征进行融合，所以拼音特征可能会占据主导或者被忽略

## 解决的问题

- 将文本特征和音标特征进行解耦，并进行直接的信息交互
- 为了学习更加有用的音标特征，提出pinyin-to-character的目标，使模型仅根据音标来预测正确的字，并使用separation mask来屏蔽从音标输入到文本输入的注意力
- 设计了自蒸馏模块，确保文本信息在训练中发挥主要作用

## 方法

### 模型结构

<img src="图片/Disentangled Phonetic Representation for Chinese Spelling Correction_1.png" style="zoom:80%;" />

> 文本和拼音拼接，并输入到预训练模型

### Separation Mask

<img src="图片/Disentangled Phonetic Representation for Chinese Spelling Correction_2.png" style="zoom: 80%;" />

#### 文本loss

<img src="图片/Disentangled Phonetic Representation for Chinese Spelling Correction_3.png" style="zoom:80%;" />

- mask思想：允许**从文本表征到音标表征的注意力**，反之则不行

### Pinyin-to-Character Objective

#### 拼音loss

<img src="图片/Disentangled Phonetic Representation for Chinese Spelling Correction_4.png" style="zoom:80%;" />

> pinyin输出的预测会在预测时被丢弃，只使用文本的预测作为最终的输出

### 自蒸馏模块

> 模型结构最左侧构建另一个网络，并使用原始文本作为输入，通过KL散度来强迫两个网络的输出分布接近

<img src="图片/Disentangled Phonetic Representation for Chinese Spelling Correction_5.png" style="zoom:80%;" />

### Joint Learning

<img src="图片/Disentangled Phonetic Representation for Chinese Spelling Correction_6.png" style="zoom: 80%;" />

> $\alpha$、$\beta$、$\gamma$都是可调整的超参数。

### Pre-training

> pinyin到文本的预训练

在两个大型语料库上进行预训练：

- [wiki2019zh ](https://github.com/brightmart/nlp_chinese_corpus) 
- [weixin-public-corpus](https://github.com/nonamestreet/weixin_public_corpus) 


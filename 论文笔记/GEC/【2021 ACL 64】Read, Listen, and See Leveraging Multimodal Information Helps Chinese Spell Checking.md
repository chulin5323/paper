# 【2021 ACL 64】Read, Listen, and See Leveraging Multimodal Information Helps Chinese Spell Checking

> 本文提出了一种多模态的中文文本纠错方法，提取输入文本的语义、音标、图形信息三个模态的信息，并通过选择融合模块，将三个模态的信息进行融合，最后进行文本纠错。

[(arxiv)](https://arxiv.org/pdf/2105.12306.pdf) [(pdf)](D:\learning\论文\GEC\【2021 ACL 64】Read, Listen, and See Leveraging Multimodal Information Helps Chinese Spell Checking.pdf) [(code)](https://github.com/DaDaMrX/ReaLiSe) 

## 以往工作的缺陷

- 中文拼写错误通常包含三种类型：语义错误、形音字错误、形近字错误。以往的工作使用启发式的方法或者人工设计混淆集（confusion set）进行纠错，但由于这种混淆集是人工定义的，所以模型通常无法预测在混淆集中没有定义的错误。

## 解决的问题

- 使用输入文本的语义、音标以及图形信息作为输入，进行多模态纠错，从而更全面的纠正文本错误
- 通过selective fusion 方法融合各个模态的信息，并设计了生意以及视觉预训练任务来提升模型的预测性能

## 方法

<img src="D:\learning\论文\论文笔记\GEC\图片\Read, Listen, and See Leveraging Multimodal Information Helps Chinese Spell Checking_1.png" style="zoom:80%;" />

### Model

#### Semantic Encoder

- 使用预训练模型提取语义信息

#### Phonetic Encoder

- 字符级
  - 将拼音表示为一个字符序列，如：$z,h,o,n,g,1$表示“中”，其中数字表示声调（共有01234五种声调）
  - 使用单向GRU对字符进行编码，并使用最后一个隐藏状态作为输入
- 句子级
  - 使用transformer encoder对每个字符的音标进行编码，获得句子级表征


#### Graphic Encoder

- 输入数据为字符的图像信息：$H\times W\times C$，其中C表示通道数，使用了三种字体来作为3通道（简体中文的黑体、繁体中文的黑体、小篆）
- 使用ResNet进行编码，并压缩大小直至大小为$1\times1$ 

<img src="D:\learning\论文\论文笔记\GEC\图片\Read, Listen, and See Leveraging Multimodal Information Helps Chinese Spell Checking_2.png" style="zoom:80%;" />

#### Selective Modality Fusion Module

<img src="D:\learning\论文\论文笔记\GEC\图片\Read, Listen, and See Leveraging Multimodal Information Helps Chinese Spell Checking_3.png" style="zoom:80%;" />

- $h^t$表示语义信息的平局，代表整个句子的语义

### Acoustic and Visual Pretraining

- phonetic encoder
  - 根据输入拼音来恢复对应文本（其中在训练数据中有一部分拼写错误）
- graphic encoder
  - 类似于OCR任务，但仅在字级别以及打印字上进行训练

### 实验结果

<img src="D:\learning\论文\论文笔记\GEC\图片\Read, Listen, and See Leveraging Multimodal Information Helps Chinese Spell Checking_4.png" style="zoom:80%;" />

#### 复现结果

##### 

|    数据集     | DET-ACC | DET-P | DET-R | DET-F1 | COR-ACC | COR-P | COR-R | COR-F1 |
| :-----------: | :-----: | :---: | :---: | :----: | :-----: | :---: | :---: | :----: |
| SIGHAN15-论文 |  84.7   | 77.3  | 81.3  |  79.3  |  84.0   | 75.9  | 79.9  |  77.8  |
| SIGHAN15-复现 |  84.09  | 76.52 | 81.33 | 78.85  |  83.09  | 74.61 | 79.30 | 76.88  |
|               |         |       |       |        |         |       |       |        |
|               |         |       |       |        |         |       |       |        |


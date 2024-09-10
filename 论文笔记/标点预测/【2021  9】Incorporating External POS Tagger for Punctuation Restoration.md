# 【2021  9】Incorporating External POS Tagger for Punctuation Restoration

> 本文将part-of-speech标记引入标点恢复任务中；并提出sequence boundary sampling在序列标注任务中学习标点的位置。

[(arxiv)](https://arxiv.org/pdf/2106.06731.pdf) [(PDF)](D:\learning\论文\标点预测/【2021 InterSpeech 8】Incorporating external POS tagger for punctuation restoration.pdf) [(github)](https://github.com/ShiningLab/POS-Tagger-for-Punctuation-Restoration)

## 以往工作的缺陷

- 以往的工作并未将part-of-speech（POS）知识引入原始文本中，而这些知识会对标点恢复工作有所帮助。
- 以往的工作在预处理阶段将语料分割成多个序列，导致在每个epoch中会重复使用训练集中相同的截断文本（truncation）；另外一些在不同的epoch之间，每次旋转训练集的一个token，会导致训练集样本数过大。

## 解决的问题

- 在训练文本中引入词性特征，从而有助于标点恢复任务。
- 提出sequence boundary sampling，更加随机的截断字符流。

## 方法

### 模型

<img src="图片/Incorporating External POS Tagger for Punctuation Restoration_1.png" style="zoom:75%;" />

- 输入$X$经过预训练语言模型，得到隐藏层embedding $H\in n\times d$。
- 输入$X$经过POS Tagger，POS tagger的隐藏层大小为$b$，Softmax权重矩阵$W\in b\times e$，其中$e$表示词性标签数量。根据$\hat{T}$中的元素是在权重矩阵中找到对应的列，并得到POS embedding$E\in n\times b$。
- $H$和$E$进行concat，并再次进行attention计算，最终得到输出。

### SBS

从语料库$S$中随机挑选长度为$L$的token序列进行训练， 采样次数为$\lfloor \frac{S}{L}\rfloor$。
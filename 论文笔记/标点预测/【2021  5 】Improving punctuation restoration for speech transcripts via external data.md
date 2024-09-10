# 【2021  5 】Improving punctuation restoration for speech transcripts via external data

> 本研究主要集中构建商业产品，并应用于商业英语对话场景。通过数据采样技术，从较大语料中采集与目标域对话数据相似的文本作为训练数据，从而提升目标域模型性能。

[(arxiv)]([2110.00560.pdf (arxiv.org)](https://arxiv.org/pdf/2110.00560.pdf)) [(PDF)](D:\learning\论文\标点预测/【2021 5】Improving punctuation restoration for speech transcripts via external data.pdf)

## 以往工作的缺陷

- 集中领域不同，过去的工作大都集中于独白文本，并没有对话形式。

## 解决的问题

- 通过[数据采样](Using similarity measures to select pretrainingdata for NER.)采集类似in-domain的数据。
- 二阶段微调，模型首次在采样的数据上进行一阶段微调，随后在in-domain数据上进行二阶段微调。

## 方法

<img src="图片/Improving punctuation restoration for speech transcripts via external data_1.png" style="zoom:75%;" />

- 通过数据采样收集与in-domain类似的数据，并微调模型。
- 人工对ASR数据进行标注，并在标注后的数据上进行二阶段微调。
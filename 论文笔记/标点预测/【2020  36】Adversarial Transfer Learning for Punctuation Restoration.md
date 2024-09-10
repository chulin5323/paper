# 【2020  36】Adversarial Transfer Learning for Punctuation Restoration

> 本文主要使用了BERT作为预训练模型，并使用POS tagging任务来作为辅助任务，训练时POS tagging和punctuation restoration任务一起训练。

[(arxiv)](https://arxiv.org/pdf/2004.00248.pdf) [(PDF)](D:\learning\论文\标点预测/【2020 36】Adversarial transfer learning for punctuation restoration.pdf) 

## 以往工作的缺陷

- 以往工作使用的word embedding都是由单一方向的语言模型进行预训练得到。
- 使用POS特征作为输入，但由于引入了额外的POS特征，所以在推理阶段的计算量会增大，并且错误的POS信息会导致标点预测错误。

## 解决的问题

- 使用BERT作为预训练模型，解决单一方向问题。
- 使用POS tagging任务帮助标点预测任务。

## 方法

<img src="图片/Adversarial Transfer Learning for Punctuation Restoration_1.png" style="zoom:75%;" />

- 输入通过预训练模型
- 预训练模型输出embedding分别作为PR和POS tagging任务的输入
- Gradient Reversal Layer：确保所有任务上的特征分布对于task discriminator来说尽可能是不可区分的，从而使模型能有效结合两个任务。
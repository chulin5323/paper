# 【2022 6】Diff-Font: Diusion Model for Robust One-Shot Font Generation

[(arxiv)](https://arxiv.org/pdf/2212.05895.pdf) [(PDF)](D:\learning\论文\AIGC\【2022 6】Diff-Font.pdf) [(code)](https://github.com/Hxyz-123/Font-diff) 

> 本文提出了Diff-Font：一种统一的one-shot字体生成框架，该方法将字体生成视为一个条件生成任务，字体的内容通过预定义的embedding tokens进行管理，期望生成的字体风格从one-shot参考文字图片进行提取。为了使生成的文字比划信息更加精确，引入了**笔划**作为更加细粒度的控制信息。

## 一、以往工作存在的缺陷

主要是与基于GAN的生成网络进行对比：

- 基于GAN的字体生成网络在使用大量数据时，往往训练不稳定且较难收敛
- 基于GAN的方法，将字体生成视为一个从源图像到目标图像的风格转换任务，通常不能独立的对内容和字体风格进行建模
- 当文字结构较为复杂时，这些方法不能保证生成字体的完整性

## 二、解决的问题

- Diffusion Model在字体生成的第一次应用，利用了Diffusion Model，使得训练更加稳定
- 将字体生成视为一个多属性条件控制结构，在扩散模型的控制条件中，引入了：风格、内容、比划信息，从而使得生成的字体样式风格可控
- 引入了笔划信息，使得生成字体更加完整

## 三、方法

### Diff-Font模型框架

![](..\..\论文笔记\AIGC\fig\Diff-Font_1.png)

- Diffusion Model负责进行文字生成
- Character attributes encoder负责添加控制信息：内容、风格、笔划

### 多属性控制条件

#### 一、style encoder

- 风格表征从预训练的风格encoder中进行提取，使用了DG-Font中的风格编码器，在训练过程中，该编码器的参数固定

    > DG-Font中的风格编码器：将输入图片映射为一个风格隐向量

#### 二、stokes/components

- 将每个字符的笔划信息编码为一个**32维的向量**，向量的每个维度表示**该字符中基本比划的数量**

![](..\..\论文笔记\AIGC\fig\Diff-Font_2.png)

#### 三、contents

- 使用模型提取输入文本的表征，将每个字符视为不同的token

## 结果

![](..\..\论文笔记\AIGC\fig\Diff-Font_3.png)
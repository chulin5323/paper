# 【2023 6 NeurIPS】GlyphControl: Glyph Conditional Control for Visual Text Generation

[arxiv]() [PDF](D:\learning\论文\AIGC\【2023 6】GlyphControl.pdf) [code](https://github.com/AIGText/GlyphControl-release) 

> 现有的扩散模型在生成连续且格式良好的视觉文字上较为困难，本文利用了额外的字形条件信息来增强扩散模型生成文字的能力，用户可以根据具体的需求来自定义生成文字的内容、位置和大小。

## 以往工作的缺陷

- 缺乏生成准确易读文字的能力
- 由于BPE tokenizer，模型缺乏充足的用于拼写的字符级别的信息（针对 英文）
- 仅依赖于文本prompt对于生成准确易读的文字渲染是不够的

## 解决的问题

- 将文本字形信息作为控制条件，字形图像作为一个明确的空间布局信息，控制扩散模型生成连贯且形式良好的文本
- 结合ControlNet，构建了一个字形条件控制的文本-图像生成模型

## 方法

### 模型结构

![](..\..\论文笔记\AIGC\fig\GlyphControl.png)

#### OCR engine

- 用于检测给定图片中的文字信息

#### Glyph render

- 将输入文本渲染为白色背景的视觉文字，位置可自由指定

#### image VAE encoder/decoder

- 将输入图片映射为潜在变量，并根据潜在变量生成图像

#### text decoder

- 将输入文本转换为text embedding

#### U-Net

- 执行去噪扩散过程

#### GlyphControlNet

- 通过处理渲染后的字形图像，来编码条件字形控制信息


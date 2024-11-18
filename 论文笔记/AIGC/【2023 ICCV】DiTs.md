# 【2023 ICCV】Scalable Diffusion Models with Transformers

[arxiv](https://arxiv.org/pdf/2212.09748.pdf)  [PDF](..\..\AIGC\文生图\【2023 ICCV】DiTs.pdf)  [code](https://github.com/facebookresearch/DiT) 

> Transformer在扩散模型上的应用，使用Transformer替换了扩散模型中的骨干网络U-Net。并通过Gflops分析了Diffusion Transformers（DiTs）的可扩展性，研究发现，通过增加Transformer的深度\宽度，或者增加输入token的数量，模型所生成的图像会有较低的FID。

## 以往工作存在的缺陷

- Transformer在许多领域取得了较好的效果，但在生成模型框架中却很少使用，现有的生成模型大多以U-Net作为backbone，而U-Net的归纳偏置对生成模型的性能而言并不是很关键的，它可以被一些标准化的设计，如Transformer，所替换

## 解决的问题

- 使用Transformer替换了扩散模型中的U-Net
- 探究了网络复杂度和生成样本质量之间的关系，研究发现，DiTs对扩散模型而言是可扩展的：在模型复杂度(GFlops衡量)和生成样本质量之间存在很强的相关性

## 方法

<img src="..\..\论文笔记\AIGC\fig\DiTs_1.png" style="zoom:80%;" />

> CFG（Classifier-free guidance）：Classifier-free guidance是扩散模型中的一种方法，旨在实现更好的生成效果，同时避免引入额外的分类器。
>
> 在扩散模型中，Classifier Guidance是一种常用的方法，它通过使用额外训练的分类器来提升生成样本的质量。具体来说，分类器引导是一种混合了扩散模型分数估计与分类器概率输入梯度的方法。然而，这种方法需要额外训练一个用于处理噪声数据的分类器，并且在采样过程中将分数估计与该分类器梯度混合，使扩散模型训练流程复杂化。
>
> 为了解决这个问题，有人提出了Classifier-free guidance。这种方法的核心思想是在扩散模型的训练过程中直接加入条件信号，而无需依赖额外的分类器。通过直接在训练过程中引入条件，模型可以学习根据这些条件生成更符合要求的样本。这种方法简化了训练流程，同时有望实现与Classifier Guidance相媲美的生成效果。
>
> 总的来说，Classifier-free guidance是扩散模型中的一种新方法，它通过直接在训练过程中引入条件信号来避免使用额外的分类器，从而实现更简单、高效的生成过程。

### DiTs模型

#### Patchify

<img src="..\..\论文笔记\AIGC\fig\DiTs_2.png" style="zoom:80%;" />

- DiTs的输入是一个空间表征$z$，对于$256\times 256\times 3$而言的图片，$z$的大小为$32\times 32 \times 4$
- 在patchify之后，对patch token使用了ViT中基于频率的位置embedding（sine-cosine）
- 这里使用**更小的patch size**，会得到**更长的token序列**，因此会对应**更多的Gflops**

#### DiT block design

<img src="..\..\论文笔记\AIGC\fig\DiTs_3.png" style="zoom:80%;" />

探究了四种可以**直接处理条件输入**的Transformer块的变体

1. cross-attention块：将条件embedding c和时间步embedding t 拼接成长度为2的序列，并在多头自注意力之后添加一个**多头交叉注意力**，用于为图像特征添加条件

2. In-context条件：在输入token中拼接t和c，并在最后一个块中，从序列中移除条件t和c

3. 自适应层归一化块（Adaptive Layer Norm block，adaLN）：使用adaLN替换了transformer中的标准层归一化。

    此外，没有直接学习$\gamma$和$\beta$​，而是**通过t和c的求和来对它们进行回归**

    > Rather than directly learn dimension wise scale and shift parameters $\gamma$ and $\beta$, we regress them from the sum of the embedding vectors of $t$ and $c$.

4. 除了对$\gamma$和$\beta$进行回归，还对维度缩放参数$\alpha$进行回归，该元素应用在DiTs块中每一个残差连接前

    > In addition to regressing $\gamma$ and $\beta$, we also regress dimensionwise scaling parameters $\alpha$ that are applied immediately prior to any residual connections within the DiT block.

5. **adaLN-Zero Block**

    > 有工作发现在有监督学习中，对每个 Block 的第一个 Batch Norm 操作的缩放因子进行 Zero-Initialization 可以加速其大规模训练。基于 U-Net 的扩散模型使用类似的初始化策略，对每个 Block 的第一个卷积进行 Zero-Initialization。
    >
    > 论文中初始化 MLP 使其输出的缩放系数$\alpha$全部为0，这样一来，DiT Block 就初始化为了**Identity Function**。

#### Transformer decoder

在最后一个DiTs块之后，需要将图像token序列解码为输出噪声预测和**输出对角协方差预测**，使用一个**标准的线性解码器**来完成此过程。

最后，将解码之后的token，重新放置在原始空间布局中，从而得到噪声和协方差预测。

### 实验

scaling DiT模型在训练阶段可以提升性能

![](..\..\论文笔记\AIGC\fig\DiTs_4.png)

Transformer的Gflops和FID存在强相关性

<img src="..\..\论文笔记\AIGC\fig\DiTs_5.png" style="zoom:67%;" />
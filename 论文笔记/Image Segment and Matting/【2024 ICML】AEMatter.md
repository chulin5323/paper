# Revisiting Context Aggregation for Image Matting

[PDF](..\..\AIGC\Image Matting\【2024 ICML】 AEMatter.pdf) [ICML](https://openreview.net/pdf?id=sjJZHPV9Id) [code](https://github.com/aipixel/AEMatter) 

## 以往工作存在的缺陷

1. **上下文尺度敏感性**：现有的基于深度学习的抠图方法中设计的上下文聚合模块，如基于池化或亲和性的聚合操作，不能很好地处理训练和推理过程中由于图像尺寸差异导致的上下文尺度变化，这限制了这些模块的通用性，并可能导致抠图性能下降。
2. **复杂性与性能不成正比**：尽管现有的方法通过设计复杂的上下文聚合模块来提高预测精度，但这些模块由于其对上下文尺度的敏感性，并没有在所有情况下都带来性能的提升。

## 解决的问题

1. **重新审视上下文聚合机制**：文章首先通过评估现有的抠图网络，发现没有上下文聚合模块的编码器-解码器网络在处理大尺寸图像时表现更好，这表明基本网络结构本身就能够学习到有效的上下文聚合。
2. **提出AEMatter网络**：基于上述发现，文章提出了AEMatter，这是一个简单但非常有效的抠图网络。AEMatter采用了混合变换器（Hybrid-Transformer）作为主干网络，并整合了外观增强的轴向学习（AEAL）块，以构建具有强大上下文聚合学习能力的基本网络。
3. **大图像训练策略**：AEMatter利用大尺寸图像训练策略（**larger patch**），帮助网络更好地学习上下文聚合，从而提高抠图性能。
4. **简化网络设计**：通过简化网络设计，避免了复杂上下文聚合模块可能带来的性能不稳定问题，同时通过扩大感受野和使用大尺寸图像训练，提高了网络的通用性和性能。

## 方法

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\AEMatter_1.png)

1. **编码器-解码器网络结构**：传统的抠图方法使用编码器-解码器网络来提取输入数据的上下文特征，并估计alpha遮罩。文章发现，即使没有特定的上下文聚合模块，基本的编码器-解码器网络也能够学习到有效的上下文聚合。

2. **混合变换器（Hybrid-Transformer）主干网络**：为了增强上下文特征的提取能力，AEMatter采用了混合变换器作为其主干网络。这种结构结合了卷积块和Swin Transformer块，以提取丰富的低级特征和高级别的上下文特征。

3. **外观增强的轴向学习（AEAL）块**：AEAL块是AEMatter中的一个创新组件，它通过外观增强（AE）块和轴向注意力机制来进一步扩大网络的感受野，从而增强对大范围上下文信息的聚合能力。

    > 1. **残差块 (Residual Blocks)** 和 **1 × 1 卷积**：使用残差块和1 × 1卷积来从主干网络的第四阶段特征 (F4) 生成紧凑的上下文特征 (Fc)。这有助于减少由主干网络产生的高维上下文特征的计算开销。
    > 2. **外观增强 (Appearance-Enhanced, AE) 块**：利用AE块来加强上下文特征的外观信息。AE块通过结合Fc和从主干网络第三阶段特征 (F3) 提取的外观特征 (Fa) 来生成外观增强的上下文特征 (Fac)。这个过程通过以下公式实现： Fac=Fc+Conv(Res(Conv(Cat(Fc,Fa))))Fac=Fc+Conv(Res(Conv(Cat(Fc,Fa)))) 其中，Cat表示连接操作，Conv表示1 × 1卷积，Res表示残差块。
    > 3. **轴向注意力 (Axis-Wise Attention)**：为了捕获大范围的上下文特征，提出了轴向注意力机制。该机制将Fac分割为轴向矩形区域，然后对每个区域应用多头自注意力。具体来说，Fac首先被零填充到宽度为W的整数倍，然后沿通道维度分割为Facpx和Facpy： (Facpx,Facpy)=SplitChannel(Pad(Fac))(Facpx,Facpy)=SplitChannel(Pad(Fac)) 然后，Facpx和Facpy被进一步分割为两组轴向特征，分别沿X轴和Y轴应用多头自注意力。处理后的特征重新组合形成精炼的上下文特征 (Frc)： Frc=Cat(MHA(SplitAxis-X(Facpx(𝑋))),MHA(SplitAxis-Y(Facpy(𝑋))))Frc=Cat(MHA(SplitAxis-X(Facpx(*X*))),MHA(SplitAxis-Y(Facpy(*X*)))) 其中，MHA表示多头自注意力操作，SplitAxis-X和SplitAxis-Y分别表示沿X轴和Y轴的分割。
    > 4. **多层感知器 (MLP) 网络**：作为前馈网络 (Feed-Forward Network, FFN)，用于特征转换，遵循传统的Transformer架构。

4. **大图像训练策略**：AEMatter采用了大图像训练策略，即使用较大尺寸的图像（1024×1024像素）进行训练。这种策略有助于网络更好地学习上下文聚合，因为更大的图像提供了更丰富的上下文信息。

    

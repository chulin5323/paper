# In-Context Matting

[PDF](..\..\AIGC\Image Matting\【2024 CVPR】In-Context Matting.pdf) [CVPR](https://openaccess.thecvf.com/content/CVPR2024/papers/Guo_In-Context_Matting_CVPR_2024_paper.pdf) [code](https://github.com/tiny-smart/in-context-matting) 

## 以往工作存在的缺陷

1. **用户交互的不便**：传统的辅助输入型抠图方法（auxiliary input-based matting）虽然精度高，但需要用户为每张图片提供辅助信息（如trimaps、scribbles或已知背景），这在实际应用中会显著降低抠图效率和用户体验。
2. **自动化与定制化的平衡**：自动化抠图（automatic matting）虽然无需任何用户干预即可预测alpha matte，但这些模型通常局限于特定对象类别（如人类、动物等），在自然场景中的泛化能力较差，且无法很好地处理一般对象类别。
3. **抠图精度与效率的矛盾**：自动化抠图虽然方便，但在精度上通常不如需要辅助输入的抠图方法。用户需要在定制化和自动化之间做出权衡。
4. **图像分割与抠图的关联性问题**：尽管图像分割技术取得了进展，但现有技术在处理半透明物体和精确对应关系方面存在限制

## 解决的问题

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\IconMatting_1.png)

- 文章提出了"In-Context Matting"任务设置，它结合了自动化抠图的便利性和辅助输入型抠图的精度。具体来说，"In-Context Matting"允许用户仅通过在参考图像上提供先验信息（如遮罩和涂鸦），就能自动对一批目标图像进行alpha matte估计，而无需为每张图像提供额外的辅助输入。
- 提出IconMatting模型，该模型基于预训练的文本到图像扩散模型，通过利用参考图像的上下文信息来生成准确的目标alpha matte。
- 文章还提出了一个新的测试数据集ICM-57，用于评估"In-Context Matting"的性能，并在量化和定性结果上展示了IconMatting与基于trimap的抠图相当，同时保持了类似自动化抠图的自动化水平。

## 方法

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\IconMatting_2.png)

<img src="D:\learning\论文\论文笔记\Image Segment and Matting\fig\IconMatting_3.png" style="zoom:80%;" />

1. **基于预训练的扩散模型**：IconMatting模型基于预训练的Stable Diffusion模型，该模型在大规模文本-图像配对数据集上训练，展现出了在几何和语义对应方面的能力。
2. **特征提取**：使用Stable Diffusion作为特征提取器，从参考图像和目标图像中提取多尺度特征和自注意力图（self-attention maps）。
3. **区域到区域的匹配**：将In-Context Matting的挑战视为一个区域到区域的匹配问题，利用Stable Diffusion的特征对应能力来关联参考图像和目标图像中的前景区域。
4. **In-Context相似性模块**：该模块是框架的核心，包括互相似性（inter-similarity）和内相似性（intra-similarity）两个子模块。互相似性利用参考图像的感兴趣区域（RoI）特征作为上下文查询，从目标特征中派生出指导图；内相似性则利用自注意力图来补充缺失的部分，通过元素级乘法将互相似性生成的指导图与多尺度自注意力图结合起来。
5. **Matting Head**：使用一个解码器来生成目标图像的alpha matte。这个解码器将原始图像与来自前面模块的输出合并，使用一系列卷积、归一化和激活层来细化特征表示。
6. **参考提示扩展（point）**：为了增强基于点和涂鸦的提示，提出了一种参考提示的扩展方法，利用参考图像的自注意力图来扩展RoI掩码，包括与提示位置相似度最高的区域。
7. **混合训练集**：结合了RM1K数据集和Open Images数据集的一个子集，以适应In-Context Matting的训练需求。
8. **ICM-57测试数据集**：构建了一个新的测试数据集，包含57组具有不同上下文的图像，用于评估In-Context Matting的性能。
9. **评估指标**：使用了SAD、MSE、Grad和Conn等四个广泛使用的抠图度量标准来评估模型性能。
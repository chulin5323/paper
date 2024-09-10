# Unifying Automatic and Interactive Matting with Pretrained ViTs

[PDF](..\..\AIGC\Image Matting\【2024 CVPR】Unifying Automatic and Interactive Matting with Pretrained ViTs.pdf) [CVPR](https://openaccess.thecvf.com/content/CVPR2024/papers/Ye_Unifying_Automatic_and_Interactive_Matting_with_Pretrained_ViTs_CVPR_2024_paper.pdf) [code](github.com/coconut/SMat) 

## 以往工作存在的缺陷

<img src="D:\learning\论文\论文笔记\Image Segment and Matting\fig\SMat_1.png" style="zoom:80%;" />

- **自动抠图与交互式抠图的局限性**：自动抠图（Automatic Matting）虽然能够处理完整的图像实例，但在细节补偿方面表现不佳；而交互式抠图（Interactive Matting）虽然能够通过用户输入（如点击、涂鸦）关注细节，但往往在全局完整性上有所欠缺。
- **模型切换的不便**：在不同的应用场景下，用户需要在自动抠图和交互式抠图之间进行切换，这不仅增加了工作量，也带来了使用上的不便。

## 解决的问题

- **引入显著性引导**：在自动抠图模式中，使用类别标记（class token）来提供显著性信息，帮助模型更好地定位和关注图像中的重要对象。
- **使用概率相似性图**：代替传统的二进制掩模（避免过度依赖trimap），使用概率相似性图作为引导，这可以提供更丰富的信息，帮助模型更准确地识别目标实例。
- **候选特征机制**：通过自适应地在类标记和用户提示特征之间切换，实现在自动和交互式模式下的统一处理。
- **轻量级框架**：提出的SMat方法具有更轻量级的框架，使得在两种模式下都能取得良好的性能，同时便于实际应用。

## 方法

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\SMat_2.png)

1. **候选特征机制 (Candidate Feature Mechanism)**:	
    - 引入候选特征作为自动和交互式抠图模式之间的枢纽，根据用户提示的存在与否自适应地切换。
    - 在自动模式中，使用预训练的视觉变换器（ViT）中的类别标记作为候选特征，它隐含地携带显著性信息。
    - 在交互式模式中，使用用户提示区域的平均特征作为候选特征。
2. **显著性引导 (Saliency Guidance)**:
    - 在自动模式下，利用类别标记来引导模型关注图像中的显著对象，从而提高对细节区域的注意力。
3. **概率相似性图 (Probabilistic Similarity Map)**:
    - 代替传统的二进制掩模，使用概率相似性图作为引导，这提供了更丰富的信息，帮助模型更准确地识别目标实例。
    - 通过计算候选特征与图像特征之间的相似性来生成相似性图。
4. **跨注意力模块 (Cross-Attention Module)**:
    - 用于增强候选特征的全局意识，通过与图像特征的交互来更新候选特征，从而提高相似性图的鲁棒性。
5. **相似性引导解码器 (Similarity-Guided Decoder)**:
    - 将相似性图转换为Alpha Matte，通过简单的解码器实现从相似性图到Alpha Matte的转换。
6. **前景复制策略 (Foreground Duplication Strategy, FD)**:
    - 通过在图像中复制前景并强化位置信息的作用，帮助模型更好地区分具有相同语义和相似外观的实例。
7. **混合训练策略 (Mixed Training Strategy)**:
    - 在训练过程中随机出现带提示和不带提示的样本，以此来适应不同的抠图场景。
8. **轻量级框架 (Lightweight Framework)**:
    - 通过简化的设计，使得模型在保持高效的同时，能够处理更复杂的抠图任务。
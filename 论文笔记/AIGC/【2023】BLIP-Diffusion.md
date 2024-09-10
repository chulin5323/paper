# 【2023】BLIP-Diffusion: Pre-trained Subject Representation for Controllable Text-to-Image Generation and Editing

[arxiv](https://arxiv.org/pdf/2305.14720.pdf) [PDF](D:\learning\论文\AIGC\【2023】BLIP-Diffusion.pdf) [code](https://github.com/salesforce/LAVIS/tree/main/projects/blip-diffusion) 

> 主体驱动（Subject-Driven）的文本-图像生成模型能基于文本prompts创作出输入主题的新颖展示。现有模型通常有较长的fine-tuning时间以及在保持主体的保真度方面有较大困难。
>
> 本文提出Blip-Diffusion——一种主体驱动的图像生成模型，它能通过主体图像和文本prompts输入来进行多模态控制。该模型包含两个预训练阶段：**多模态预训练**（使用Blip2生成与文本对齐的视觉表征）和**主题表征学习**（使diffusion模型利用视觉表征生成主体的新颖展示）。Blip-Diffusion可以和ControlNet以及prompt-to-prompt结合，进行主体驱动生成与编辑应用。

## 一、以往工作存在的缺陷

- 以往工作通过将主题视觉转化为文本嵌入空间来进行主体驱动的生成，该方法需要迭代大量的微调步骤，**效率不高**
- 大多数文本-图像预训练模型不支持多模态控制，即使用图像和文本作为控制输入。因此在学习与文本空间对齐的主体表征并且同时能**高保真度的主体视觉**有较大的挑战性

## 二、解决的问题

- 在预训练阶段，使用Blip2训练与文本对齐的视觉表征，从而获得视觉和文本的多模态表征
- 在主体表征学习阶段，使用视觉表征作为输入，让扩散模型学习生成新颖的主体展现
    - 在该阶段，对输入主体随机更换背景，以突出主体训练
    - 该阶段的多模态表征，通过将合成背景图像以及其主体对应的类标签同时作为Blip2的输入得到，作为主体表征
- Blip-Diffusion可以和ControlNet以及prompt-to-prompt结合，学习生成主体驱动的图片或进行编辑

## 三、相关工作

### 3.1 Diffusion Models for Text-to-Image Generation

#### 1、扩散模型的生成原理

通过递进式的去除来自高斯分布的随机变量来生成图片，本文关注潜在扩散模型（latent diffusion model），该模型的优化目标：

![](D:\learning\论文\论文笔记\AIGC\BlipDiffusion_1.png)

$\epsilon$为添加的噪声，$\epsilon_{\theta}$为模型预测的噪声，$c$为文本prompt（condition），在训练过程中，潜变量$z$是通过将原始图片通过预训练的编码器（如VAE）得到的。

扩散模型通过预测噪声来进行图片生成，在推理时，对输入的高斯随机变量逐渐去噪，从而生成满足条件的图像。

### 3.2 Subject-driven Text-to-Image Generation

主体驱动的文本-图像生成任务的目标是基于文本prompts生成上下文语境中的主体目标。

相关工作有：Textual Inversion、DreamBooth、SuTI等。

## 四、方法

![](D:\learning\论文\论文笔记\AIGC\BlipDiffusion_2.png)

Blip-Diffusion首先使用Blip2编码器提取多模态主体表征，随后与文本prompts一起用于指导图片生成。

Blip-Diffusion的目标是学习主体表征，该表征能捕获特定主体的视觉展现，同时能与文本prompts进行良好的对齐。因此，文中采用了两阶段的预训练：**多模态表征学习阶段（multimodal representation learning stage）**和**主体表征学习阶段（subject representation learning stage）**，前者生成与文本对齐的通用图像表征，后者使用文本和一阶段的视觉表征驱动扩散模型进行主体生成。

***

### 4.1 Multimodal Representation Learning with BLIP-2

在预训练阶段，使用参数冻结的图像编码器提取图像特征，使用多模态编码器（Q-Former）进行图像-文本对齐。

- Q-Former：多模态编码器为一个transformer，它接收一个参数有限的可学习的**query tokens**和**文本**作为输入。其中，query tokens通过self-attention进行自身的信息交互，并通过cross-attention与图像特征进行交互，从而生成与文本对齐的图像特征。
- Blip2预训练过程中，联合优化三个视觉语言预训练目标：image-text contrastive learning (ITC) loss、image-grounded text generation (ITG) loss、image-text matching (ITM) loss。

### 4.2 Subject Representation Learning with Stable Diffusion

将主体表征注入到扩散模型时，考虑两个所需的属性：

- 主体表征能很好的与文本prompts进行协调，从而更好地实现**文本指导的主体驱动生成**
- 在理想情况下，应保持扩散模型的行为，从而能更好地与 promt-to-prompt或者control net结合

#### 4.2.1 模型结构

- 多模态编码器输出的embedding与prompts embedding拼接，作为soft visual subject prompt。形式为："[text prompt], the [subject text] is [subject prompt]"
- 拼接后的embedding作为CLIP文本编码器的输入，作为扩散模型生成图像的指导。

#### 4.2.2 Subject-generic Pre-training with Prompted Context Generation

- 为了使预训练模型从输入图片中学习更加**通用主体特征**，提出了：prompted context generation，对输入图片中的主体随机更换背景，从而生成输入-目标训练数据对

    - 使用CLIPSeg对输入图片进行分割，构建分割后的三元图（trimap），其中**高置信度的为前景，低置信度的为不确定区域，剩下的为背景**
    - 使用closed-form matting提取前景（主体）

    > Closed-form Matting是一种图像处理技术，主要用于精确地将前景对象从背景中分离出来。这种技术基于一个特定的数学公式或模型，通过优化算法来求解像素的透明度，从而实现前景和背景的分离。

    - 通过alpha blending将随机背景图片嵌入主体

    > Alpha Blending是一种图形渲染技术，主要用于将图像或图形对象与背景进行混合，以产生半透明或透明的效果。

- 在训练期间，以15%的概率随机丢弃主体prompt，只保留文本prompt指导扩散模型生成

#### 4.2.3 与Control Net和prompt-to-prompt结合

![](D:\learning\论文\论文笔记\AIGC\BlipDiffusion_3.png)
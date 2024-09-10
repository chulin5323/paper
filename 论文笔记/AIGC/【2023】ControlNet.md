# 【2023 798】Adding Conditional Control to Text-to-Image Diffusion Models

[arxiv](https://arxiv.org/abs/2302.05543) [PDF](D:\learning\论文\AIGC\【2023 789】ControlNet.pdf)  [code](https://github.com/lllyasviel/ControlNet) 

> 本文提出了ControlNet，一个向预训练文本-图像扩散模型添加**空间条件控制**的网络框架。其本质是在训练时复制扩散模型encoder的参数形成控制网络，在训练过程中冻结预训练模型的参数，仅对控制网络的参数进行更新，两个网络之间通过**zero convolution**进行连接（确保不对finetuning过程中引入有害噪声）。

## 一、以往工作存在的缺陷

- 文本-图像模型在在控制图像的空间组成方面受到限制，难以仅通过文本prompts表达出空间的复杂布局、姿态、形状等空间结构
- 目前已有一些方法可以通过限制去噪扩散过程或者编辑注意力层来解决如生成图像变换或图像修补等问题，但depth-to-image，pose-to-image等仍需要端到端的学习以及数据驱动方法
- 对于文本-图像训练使用的数据而言，用于明确条件控制的数据量太小，模型难以从少量的条件数据中学会控制

## 二、解决的问题

- 提出ControlNet，在训练时复制扩散模型encoder（frozen）的参数（trainable），二者通过zero convolution进行连接，既保留了预训练扩散模型的生成能力，同时也可以根据控制条件进行训练
- 可以通过多种条件进行控制：Canny edges, Hough lines, user scribbles, human key points, segmentation maps, shape normals, depths等，推理时，仅通过一张控制图片即可进行条件控制生成（可选择性添加文本prompts）

<img src="D:\learning\论文\论文笔记\AIGC\ControlNet_2.png" style="zoom:80%;" />

## 三、方法

### 3.1 ControlNet

<img src="D:\learning\论文\论文笔记\AIGC\ControlNet_3.png" style="zoom:80%;" />

- 输入：文本prompt、输入图像、控制条件
- 使用$1\times 1$卷积连接两个网络，该卷积块权重和偏置均初始化为0。关于初始化为0，仍能进行反向传播的解释：

![](D:\learning\论文\论文笔记\AIGC\ControlNet_1.png)

### 3.2 ControlNet用于文本图像扩散

- 使用CLIP文本encoder对文本prompts进行编码，diffusion timesteps使用带有位置编码的时间编码器进行编码
- ControlNet结构用于U-Net的每个编码层
- 去噪过程可以发生在像素空间以及从训练数据编码的隐藏空间（latent space）
- 扩散模型的输入图片和控制图片大小应一致

### 3.3 训练

- 训练目标为预测噪声，loss为MSE
- 训练时随机替换50%的文本prompts为空字符串，该方法增强了ControlNet直接识别控制图像语义信息的能力，从而可以在推理时用作文本prompts的替换
- 训练中存在突然收敛现象（sudden convergence phenomenon）

<img src="D:\learning\论文\论文笔记\AIGC\ControlNet_4.png" style="zoom:80%;" />

### 3.4 推理

- Stable Diffusion依赖于Classifier-Free Guidance（CFG）技术来生成高质量图像，即

$$
\epsilon_{prd}=\epsilon_{uc}+\beta_{cfg}(\epsilon_{c}-\epsilon_{uc})
$$

其中，$\epsilon_{prd}$，$\epsilon_{uc}$，$\epsilon_{c}$，$\beta_{cfg}$分别是模型最终输出，无条件输出，有条件输出，以及超参数。

控制条件可以添加到$\epsilon_{uc}$和$\epsilon_{c}$或仅添加到$\epsilon_{c}$。

**当无文本prompts给出的情况下**，将控制条件添加到$\epsilon_{uc}$和$\epsilon_{c}$上会完全移除CFG指导（图b），仅使用$\epsilon_{c}$会使得指导性变得更强（图c）。

![](D:\learning\论文\论文笔记\AIGC\ControlNet_5.png)

- CFG Resolution Weighting：训练时，首先添加条件控制图像到$\epsilon_c$上，随后乘以权重$w_i$，并添加到扩散模型和ControlNet的连接中，其中$w_i$根据每个block的大小进行调整
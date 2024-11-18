# A Task is Worth One Word: Learning with Task Prompts for High-Quality Versatile Image Inpainting

[PDF](..\..\AIGC\inpainting\【2024 ECCV】PowerPaint.pdf) [code](https://github.com/open-mmlab/PowerPaint) 

> 这篇文章介绍了一个名为PowerPaint的图像修复模型，它能够同时在多种图像修复任务中达到最先进的结果，包括文本引导的**对象修复**、**对象移除**、**形状引导的对象修复**和**扩展画布(outpainting)**等。PowerPaint的核心特点是**使用可学习的“任务提示”（task prompts）和定制的微调策略**，使得模型能够明确地关注不同的修复目标。具体来说，PowerPaint基于一个预训练的文本到图像的扩散模型，并引入了两个可学习的提示：**Pobj用于文本引导的对象修复，Pctxt用于上下文感知的图像修复**。此外，PowerPaint还展示了任务提示的多功能性，例如通过负提示实现对象移除，以及通过**提示插值技术(prompt interpolation)**实现可控的形状引导对象修复。

## 解决的问题

这篇文章试图解决高质量多用途图像修复中的挑战，特别是在需要填充用户指定区域以实现各种意图（如背景填充和对象合成）时。具体来说，文章试图解决以下几个问题：

1. **多任务挑战**：现有的图像修复方法要么专注于上下文感知的填充，要么使用文本描述进行对象合成，但同时实现这两项任务是具有挑战性的，因为它们需要不同的训练策略。

2. **对象合成与上下文一致性**：早期的工作集中在上下文感知的图像修复上，这些模型在合成新对象时遇到困难，因为它们仅依赖于上下文来推断缺失的内容。

3. **文本引导的修复**：最近的方法转向文本引导的图像修复，这些方法在对象合成方面取得了显著成果，但它们通常假设遮罩区域内存在对象，这限制了它们在背景清理和对象移除方面的应用。

4. **对象移除的挑战**：在拥挤的图像背景中移除对象时，现有方法往往会复制周围环境中的对象，导致无法有效移除目标对象。

5. **形状引导的对象修复**：在形状引导的对象修复任务中，现有方法可能会过度拟合遮罩形状，而忽视对象的整体形状，导致生成的对象形状不自然。

## 相关的工作及缺陷

1. **早期的上下文感知图像修复模型**：
   - **缺陷**：这些模型通过随机遮盖图像区域并重建原始内容来训练，虽然能够生成与图像上下文一致的内容，但在**合成新对象时遇到挑战**，因为它们仅依赖于上下文来推断缺失的内容。

2. **文本引导的图像修复模型**：
   - **缺陷**：这些模型通过微调预训练的文本到图像（T2I）模型，并使用遮罩和文本描述来实现，虽然在对象合成方面取得了显著成果，但它们通常假设遮罩区域内存在对象，这**限制了它们在背景清理和对象移除方面的应用**。此外，这些方法可能需要复杂的提示工程或复杂的工作流程来移除不需要的对象。

3. **Smartbrush 和 Imagen Editor**：
   - **缺陷**：这些模型使用成对的对象-描述数据进行训练，以解决文本错位问题，但它们倾向于假设缺失区域总有对象存在，失去了执行上下文感知图像修复的能力。

4. **SD-Inpainting 和 ControlNet-Inpainting**：
   - **缺陷**：这些模型基于大规模预训练的文本到图像模型（如Stable Diffusion），并在随机遮罩上进行微调，但它们可能遭受文本错位问题，无法合成与文本提示对齐的对象。

5. **Adobe Firefly**：
   - **缺陷**：这是一个商业产品，可能基于大型文本到图像模型，但在拥挤的图像背景中移除对象时，由于模型的内在网络结构（例如，注意力层），它倾向于从上下文中复制对象，导致在遮罩区域内合成对象而不是移除它们。

6. **其他基于扩散模型的图像修复方法**：
   - **缺陷**：这些方法在扩展图像内容时可能会忽略图像上下文，生成随机伪影，尤其是在没有详细提示的情况下。

## 方法

<img src="D:\learning\paper\论文笔记\AIGC\fig\Powerpaint_1.png" style="zoom:80%;" />

1. **学习任务提示（Task Prompts）**：

   - 引入了可学习的“任务提示”（例如Pobj和Pctxt），这些提示与特定的训练策略一起，指导模型专注于不同的图像修复目标。Pobj用于文本引导的对象修复，而Pctxt用于上下文感知的图像修复。

2. **定制的微调策略**：

   <img src="D:\learning\paper\论文笔记\AIGC\fig\Powerpaint_2.png" style="zoom:67%;" />

   where p is randomly initialized as **an array of tokens** and then used as input to the text encoder.

   Pobj和Pctxt的优化策略相同。

   - 根据不同的任务（文本引导的对象修复和上下文感知的图像修复），设计了不同的微调策略。例如，Pobj通过使用对象边界框作为遮罩，并将其作为文本描述的后缀进行优化；Pctxt则通过随机遮罩和自身作为文本提示进行优化。

3. **负提示用于对象移除**：
   - 利用负提示（Pobj）和正提示（Pctxt）结合无分类器引导采样策略，有效抑制不需要的对象生成，从而改进对象移除的性能。
   - Pctxt和Pobj可以通过==CFG采样==策略实现更有效的目标移除

   <img src="D:\learning\paper\论文笔记\AIGC\fig\Powerpaint_3.png" style="zoom:67%;" />

   where Pctxt is considered a positive prompt, while Pobj is considered a negative prompt, and $w$ is the guidance scale.

   > Classifier-free guidance (CFG) is a sampling strategy in diffusion models that helps to enhance the quality of generated samples by balancing diversity with adherence to certain conditions or prompts. This technique is widely used in text-to-image models and other generative tasks where control over the generated content is desired without relying on an explicit classifier. Here’s a breakdown of how it works:
   >
   > 1. **Background and Purpose**:
   >    In conditional generation, models are often trained to generate data based on certain conditions (e.g., text prompts for images). Traditionally, classifiers or auxiliary networks are used to ensure the generated samples align with the conditions. However, classifier-free guidance avoids the need for such classifiers, making the process simpler and more flexible.
   >
   > 2. **Sampling Process**:
   >    During training, the model learns to generate both conditional and unconditional samples. This means the model has two modes:
   >    - **Conditional Mode**: It generates samples based on a given condition (e.g., an image prompt).
   >    - **Unconditional Mode**: It generates samples without any condition.
   >
   > 3. **Guidance Strategy**:
   >    Classifier-free guidance interpolates between the conditional and unconditional outputs. During inference, the generated sample is computed as a weighted sum of these two outputs:
   >    $x = x_{\text{uncond}} + \omega \cdot (x_{\text{cond}} - x_{\text{uncond}})$
   >    where $x_{\text{uncond}}$ is the unconditional prediction, $x_{\text{cond}}$ is the conditional prediction, and $\omega$ is a guidance scale that controls the strength of conditioning. Higher values of $\omega$ make the generation more closely follow the condition, while lower values retain more diversity.
   >    
   > 4. **Advantages**:
   >    - **Control over Adherence**: By adjusting the guidance scale, users can control how closely the output adheres to the prompt.
   >    - **Enhanced Sample Quality**: CFG helps the model focus on features relevant to the prompt, leading to sharper and more coherent samples.
   >    - **No Classifier Needed**: Unlike other guidance methods that require an additional classifier to steer the generation, CFG achieves similar effects without one, simplifying the setup.
   >
   > 5. **Applications**:
   >    Classifier-free guidance is widely used in models like DALL-E, Stable Diffusion, and other generative models that perform conditional generation, especially in image synthesis based on text prompts.
   >
   > This strategy balances prompt adherence with creativity in generation, making it particularly valuable for applications that require high-quality, controlled outputs.

4. **提示插值技术**：

   - 通过提示插值技术，实现了可控的形状引导对象修复。这涉及到在Pctxt（上下文感知提示）和Pshape（形状引导提示）之间进行插值，以平衡遮罩中心的文本引导对象修复和遮罩边缘的上下文感知背景填充。

5. **统一模型与任务特定提示的结合**：
   - PowerPaint作为一个多功能模型，通过结合任务特定的提示，能够在不牺牲性能的情况下处理多种图像修复任务。

6. **扩展的编码器和网络结构**：
   - 在CLIP文本编码器的嵌入层和基于U-Net的网络结构中微调任务提示，以适应不同的修复任务。

7. **多任务训练**：

   - 使用OpenImage v6的语义分割子集作为主要数据集进行多任务提示调整，并结合[BLIP captions](https://proceedings.mlr.press/v202/li23q/li23q.pdf)作为局部文本描述进行训练。

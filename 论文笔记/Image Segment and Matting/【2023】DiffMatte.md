# Diffusion for Natural Image Matting

[PDF](..\..\AIGC\Image Matting\【2023】DiffMatte.pdf)  [code](https://github.com/YihanHu-2022/DiffMatte) 

> 首次将扩散模型应用到Image Matting任务上，将扩散模型中的编码器和解码器进行解耦，并使用任意的matting模型作为编码器，使用一个轻量级的网络作为扩散过程的解码器，从而减少参数量；提出具有统一时间间隔的字对齐训练策略（a self-aligned training strategy with uniform time intervals），从而确保在整个时间域的训练和推理之间保持一致的噪声采样。

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\DiffMatte_1.png)

## 以往工作存在的缺陷

目标是利用扩散模型解决Matting任务，但存在以下两个问题：

- 高计算量的开销
- 训练和推理过程的噪声采样不一致
- 现有matting模型在提取特征时，仅关注于高级别特征（high-level），忽略了图像的低级别特征（low-level）

## 解决的问题

- 将扩散模型的编码器和解码器解耦，并在扩散过程的迭代中采用轻量级的解码器来减少计算量
- 提出具有统一时间间隔的字对齐训练策略，从而确保在整个时间域的训练和推理之间保持一致的噪声采样。

> **Low-Level Features**
> Low-level features are the basic, primitive elements that can be directly extracted from the image without requiring any prior knowledge or interpretation. They represent the fundamental building blocks of an image. Examples of low-level features include:
>
> - Edges: Boundaries between different regions in an image, often detected using edge detection algorithms like the Sobel or Canny edge detectors.
>
> - Corners: Points where edges intersect, which can be identified using methods like the Harris corner detector.
> - Texture: Patterns or surface properties of an image region, which can be quantified using techniques like Gabor filters or Local Binary Patterns (LBP).
> - Color: Basic color information, usually represented in color histograms or color spaces (e.g., RGB, HSV).
> - Intensity: The brightness of pixels in grayscale images.
> - Low-level features are typically straightforward to compute and provide a raw description of the image content. However, they don't carry semantic information and are often used as the first step in more complex image analysis tasks.
>
> **High-Level Features**
> High-level features, on the other hand, are more abstract and represent more complex and semantically meaningful information. These features are derived from low-level features and often involve a greater level of interpretation and understanding of the image content. Examples of high-level features include:
>
> - Objects: Recognizable entities within an image, such as cars, people, or animals. Object detection and recognition algorithms identify and classify these objects.
> - Scenes: The overall context or environment in an image, such as a beach, cityscape, or forest. Scene recognition models categorize images based on these broader contexts.
> - Shapes: Specific forms and structures, which can be identified using shape analysis techniques.
> - Gestures or Poses: The position and movement of human bodies, often analyzed using pose estimation algorithms.
> - Semantic Segmentation: Dividing an image into regions with semantic meaning, like distinguishing between road, sky, and buildings in a street scene.
> - High-level features require more sophisticated processing and often rely on machine learning models, particularly deep learning techniques like convolutional neural networks (CNNs), to extract and interpret the information. These features enable applications such as image classification, object detection, facial recognition, and image captioning.

## 方法

### 1、Constructing Diffusion Framework for Matting

$X_t$为alpha matte，条件$c$为原图Image和Trimap的拼接（经过matting模型映射为high-level features）

前向加噪

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\DiffMatte_3.png)

反向迭代预测

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\DiffMatte_4.png)

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\DiffMatte_5.png)

### 2、Decoupled texture decoder for efficient diffusion process.

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\DiffMatte_6.png)

### 3、 Self-aligned strategy with uniform time intervals for consistent noise sampling

![](D:\learning\论文\论文笔记\Image Segment and Matting\fig\DiffMatte_2.png)


# Inpainting

## Image inpainting

### deterministic image inpainting

> 仅输出一个inpainted结果

- single-shot

<img src="D:\learning\paper\论文笔记\AIGC\fig\inpainting_1.png" style="zoom:80%;" />

- two-stage

    - Coarse-to-fine methods
    - Structure-then-texture methods

    <img src="D:\learning\paper\论文笔记\AIGC\fig\inpainting_2.png" style="zoom:80%;" />

- progressive

<img src="D:\learning\paper\论文笔记\AIGC\fig\inpainting_3.png" style="zoom:80%;" />

### stochastic image inpainting

> 通过随机采样过程输出多个可能的结果

- VAE-based methods
- GAN-based methods
- Flow-based methods
- MLM-based methods（masked language model）
- Diffusion model-based methods

### Text-guided Image Inpainting

> 关键挑战：1）如何将文本和图像语义特征进行融合；2）如何集中关注文本中的有效信息

## Inpainting Mask

### Regular masks

> A square hole that blocks the center area or random location are generally easier to construct.

### Irregular masks

> Letter masks，object-shaped masks

<img src="D:\learning\paper\论文笔记\AIGC\fig\inpainting_4.png" style="zoom:80%;" />

## Datasets

### face

- CelebA
- CelebA-HQ

### real-world encountered scenes

- Places2

### street scenes

- Paris StreetView

### texture

- DTD

### objects

- ImageNet

## Evaluation Protocol

### ℓ1 error

### ℓ2 error

### PSNR

### SSIM

### MS-SSIM

### FID

### LPIPS

### P/U-IDS

### User Study
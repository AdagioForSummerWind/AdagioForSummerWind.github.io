---
title: "DIP_base_01"
date: 2022-02-24T10:43:56+08:00
lastmod: 2022-02-24
tags: [DIP]
categories: [School courses]
slug: DIP_intro
draft: true
---
> 哈工大深圳卢光明副院长授课的本科生图像处理课程，参考教材：Digital Image Processing”, Rafael C. Gonzalez & Richard E. Woods, 2011（or 4th Edition，2017）

# Intro & 
课程目的：
- 理解图像处理的基本概念、理论、方法
- 解决图像处理的问题
- 为了更深入地研究图像处理的理论和一些相关的课程，像：计算机视觉等

10%+20%+70%
## What is digital image processing?
f(x,y),x,y是坐标，函数值为强度或者称为灰度级。（灰度图像）

Finite & discrete quantities

每个像素灰度级在0~2^8

define:
- processing digital images by means of a digital computer

Digital image processing focuses on two major tasks:
- Improvement of image quality for human interpretation
- Processing of image data for storage, transmission, display and representation for automatic machine perception

图像处理（去噪、增强……人是最终解释者）->图像分析（分割、特征提取……）->计算机视觉（理解、识别……计算机是最终解释者）
- 从低层到高层
- 本课程重点在底层以及中层


## Human vision system
人的视觉系统怎么成像？

视网膜（成像的地方，分布着大量的光分离接收器（锥状体，主要分布在中央凹；杆状体）） 20度是盲点
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220224111030.png)

430~790nm人能接收到的波谱。

光子能量（电压）：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220225093349.png)

## Image acquisition
## Sampling and Quantization
## Resolution
彩色图像是指图像中的每个像素都分成R、G、B三个基色分量，每个基色分量直接决定其基色的强度，这样产生的色彩称为真彩色，因此彩色图像有3个通道。

灰色图像的二进制表示：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220225100628.png)
## Basic Relationships Between Pixels
What's $N_4(P),N_D(P),N_8(P)$ ?  (P is a pixel)

Connectivity 连通性：
- 像素之间的连通性很重要，它被用于在一个图像的组成部分之间建立界限的。
- 两个像素是连通的的条件：
    - 它们是邻居
    - 它们的灰度级满足一个相似性的特殊标准。

Adjacency 毗邻（邻接），有三种类型的毗邻：
- 4-adjacency 对应 $N_4(p)$
- 8-adjacency 对应 $N_8(p)$
- m-adjacency 对应->
    - q is in $N_4(p)$ or
    - q is in $N_D(p)$ and the set N4(p)ANDN4(q) has no pixels with values from V
        - V is the set of gray-level values used to define adjacency (邻接) (e.g. V={1} for adjacency of pixels of value 1)

    - m-adjacency 常常被用于清除8-adjacency使用时产生的多路径。是8-adjacency的一种修改。![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220226095647.png)

path（通路）:
- We can define 4-, 8-, and m-path depending on the type of adjacency. 

定义：
- S是一张图像里的一个像素集。
- 如果两个像素存在通路则被称之为是连通的（connected）
- S中与任意像素p连通的像素集被称之为S的一个连通分量（connected component）
- 如果S只有一个连通分量，则S被称之为连通集。
- 连通集通常会被称为图像的一个region
- 区域（region）的boundary就是具有至少一个不在区域里的邻居的像素的集合

distance measures:
- $D_e(p,q)=[(x-s)^2 + (y-t)^2]^{0.5}$
- $D_4(p,q) = |x-s| + |y-t|$
- $D_8(p,q) = max(|x-s|,|y-t|)$



## Key stages in digital image processing
- Problem Domain
- Image Acquisition
- Image Enhancement
- Image Restoration
- Morphological Processing
- Segmentation分割
- Object Recognition物体识别
- Representation & Description描述表示
- Image Compressive
- Colour Image Processing
---
title: "DIP_base_02"
date: 2022-02-24T10:44:08+08:00
lastmod: 2022-02-28
tags: [DIP]
categories: [School courses]
slug: Image Enhancement
draft: true
---
> 哈工大深圳卢光明副院长授课的本科生图像处理课程，参考教材：Digital Image Processing”, Rafael C. Gonzalez & Richard E. Woods, 2011（or 4th Edition，2017）
# Image Enhancement
There are two broad categories of image enhancement techniques
- Spatial domain techniques
    - Direct manipulation操作 of image pixels
        - Point processing
        - Neighbourhood operations
- Frequency domain techniques
    - Manipulation of Fourier transform傅里叶变换 or wavelet transform小波变换 of an image
## Basic Spatial Domain Image Enhancement
Most spatial domain enhancement operations can be reduced to the form:  g (x, y) = T[ f (x, y)]
- where f (x, y) is the input image, g (x, y) is the processed image and T is some operator defined over some neighbourhood of (x, y)

The simplest spatial domain operations occur when the neighbourhood is simply the pixel itself: **Point Processing**.
In this case T is referred to as a grey level transformation function or a point processing operation.
Point processing operations take the form:
s = T ( r )
where s refers to the processed image pixel value and r refers to the original image pixel value.

Point processing
- The neighborhood is of size 1×1
- Gray-level transformation

Mask processing掩码处理 or filtering
- The neighborhood is defined as a mask, filter, or window.
- Filtering

## point processing techniques:
- Thresholding阈值化
    - Thresholding transformations are particularly useful for segmentation in which we want to isolate an object of interest from a background.
    - s=1.0(r>threshold)
    - s=0.0(r<=threshold)
- Logarithmic transformation对数转化
    - The general form of the log transformation is s = c * log(1 + r)
    - Log functions are particularly useful when the input grey level values may have an extremely large range of values.
    - In Fourier transform, we usually encounter spectrum values that ranges from 0 to 106 or higher. But image display system cannot reproduce such a wide range of intensity values.
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327103421.png)



- Power law transforms幂律转化
    - Power law transformations have the following form:$s = c * r^γ$      r ∈ [0.0, 1.0]
    - Map a narrow range of dark input values into a wider range of output values or vice versa.
    - Varying γ gives a whole family of curves.
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327104518.png)

- Gamma Correction灰度矫正
    - Many of you might be familiar with gamma correction of computer monitors.
    - Problem is that display devices, print devices do not respond linearly to different intensities, and they respond according to a power law:
    - $s = r^γ$
    - For CRT display, γ =1.8~2.5.Can be corrected using a nth root transform:
    - $s = r^{1/γ}$
    - Prior Knowledge about gamma correction
        - Varying the value of gamma correction changes not only the brightness, but also the ratio of red to green to blue.
    - Applications
        - Internet
        - Millions of people and millions of monitors
        - Gamma represents an “average ”of the types of monitors and computer systems
        - Scanners, Printers have different values of gamma



- Grey level slicing灰度分割
    - Highlights a specific range of grey levels
        - Similar to thresholding
        - Other levels can be suppressed or maintained
        - Useful for highlighting features in an image
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327105621.png)

- Bit plane slicing位平面切片
    - Often by isolating particular bits of the pixel values in an image we can highlight interesting aspects of that image:
        - Higher-order bits usually contain most of the significant visual information.
        - Lower-order bits containsubtle details.

- Image subtraction
    - g(x,y) = f(x,y) - h(x,y)
    - f and h are two images. 
    - Note: most images are displayed using 8 bits (even 24-bit color images consists of three 8 bit channels). Thus we expect image values not to be outside the range from 0 to 255.
    - In subtraction, the results should be in the range -255 to 255. so some sort of scaling is required to display the results.
    1. Add 255 and then divide by 2
    2. y=x-min(x); z=y*255/max(y).
    - Change detection is another major application by using image subtraction. Such as:
        - Tracking moving vehicles
        - Tracking walking persons
        - Change detections
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327110232.png)


- Image averaging
    - A noisy image: g(x,y) = f(x,y) + n(x,y)
    - Where n(x) is the noise, and it has zero average value. Then Averaging M different noisy images can reduce the noise:
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327110523.png)
    - As M increases, the variability of the pixel values at each location decreases.
        - This means that `g(x,y) approaches f(x,y) as the number of noisy images used in the averaging process increases.
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327110659.png)
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327110825.png)
    - In average, the results of gray level should be in the range 0 to 255*M. so some sort of scaling is required to display the results.
        1. Divide by M.
        2. y=x-min(x); z=y*255/max(y).

- Image Histograms图像直方图
    - The histogram of a digital image with gray levels from 0 to L-1 is a discrete function:
        - $p_r(r_k)=n_k 0<=r_k<=1 k=0,1,2……L-1$
        - $r_k$ is the kth gray level
        - $n_k$ is the pixels in the image with that gray level
        - k = 0, 1, 2, …, L-1
    - Normalized histogram: p(rk)=nk/n
        - n is the total number of pixels in the image
        - sum of all components is 1
    - The histogram of an image shows us the distribution of grey levels in the image. It is useful in image processing, especially in enhancement and segmentation.

Properties of Image Histograms:
- The histogram only shows the distribution of grey levels in the image, and it doesn’t include the location information of pixels.
- One image has its corresponding histogram, but different images may have the same histograms. 

Note that the high contrast image has the most evenly spaced histogram

Histogram equalization直方图均衡化
- ……
- Equalisation Transformation Function平衡变换函数
        - ……
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220327121228.png)

## Spatial Filtering空间滤波
spatial filtering techniques:
- Basic of spatial filtering
- Smoothing spatial filters
    - Smoothing linear filters
    - Order-statistics filters
- Sharpening spatial filters
    - 1st derivative filters
    - 2nd derivative filters
### Basic of spatial filtering
Neighbourhood operations simply operate on a larger neighbourhood of pixels than point operations.

Neighbourhoods are mostly a rectangle around a central pixel.

Any size rectangle and any shape filter are possible.

Use of spatial masks for image processing (spatial filters)
- Correlation
- Convolution

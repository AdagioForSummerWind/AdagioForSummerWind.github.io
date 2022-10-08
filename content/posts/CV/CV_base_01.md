---
title: "CV_base_01"
date: 2022-04-16T18:54:15+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
> Harbin Institute of Technology（Shenzhen）Jingyong Su

# CV_base
## 1.Introduction (2)
### Introduction to computer vision
The goal of computer vision:
- To bridge the gap between pixels and “meaning”

### Linear algebra primer
#### Vectors and matrices
Basic Matrix Operations：
- Vectors have two main uses：
    - Vectors can represent an offset in 2D or 3D space
    - Data (pixels, gradients at an image keypoint, etc) can also be treated as a vector.Such vectors don’t have a geometric interpretation, but calculations like “distance” can still have value

Color Images:
- Grayscale images have one number per pixel, and are stored as an m × n matrix.
- Color images have 3 numbers per pixel – red, green, and blue brightness
- Stored as an m × n × 3 matrix

Basic Matrix Operations:
- Addition
- Scaling(缩放)
- Dot product(点积)
- Multiplication
- Transpose
- Inverse / pseudoinverse

- Determinant 
- trace
    - sum of diagonal elements



Special Matrices:
- Identity matrix
    - Square matrix, 1’s along diagonal, 0’s elsewhere
- Diagonal matrix
- Symmetric matrix:$A^T=A$
- Skew-symmetric matrix:$A^T=-A$

#### Transformation Matrices
Homogeneous coordinates:

Matrices can be used to transform vectors in useful ways, through multiplication: $x’= Ax$

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220509092134.png)

$$
\left[\begin{array}{l}
x^{\prime} \\
y^{\prime}
\end{array}\right]=\left[\begin{array}{cc}
\cos \theta & -\sin \theta \\
\sin \theta & \cos \theta
\end{array}\right]\left[\begin{array}{l}
x \\
y
\end{array}\right]
$$


#### Matrix inverse
skip ……
#### Matrix rank（秩）
#### Singular Value Decomposition (SVD奇异值分解)
Use for image compression
Use for Principal Component Analysis (PCA)
Computer algorithm

## 2.Pixels and Features (2.5)
### Pixels and filters 
Images as functions 

Linear systems (filters) 

Convolution and correlation

### Edge detection 
### Feature point extraction - SIFT 
## 3.Regions of Images and Segmentation (1.5)
### Clustering -  K-means and mean shift 
### Motion and Tracking
## 4.Object Recognition and Face Detection(1)
## 5.Neural Networks (2)
## 6.Recurrent Neural Networks: RNN, LSTM (1)
## 7.Attention and Transformers (1)
## 8.Generative Modeling – GAN(1)

Attendance: 24% (2% per class)

Homework Assignment: 40% （10% each）
Only email the “.ipynb” file to hitcv2022@163.com
Procedures and results are required

Final Project: 36%

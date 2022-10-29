---
title: "Programming_literacy"
date: 2022-01-02T18:57:57+08:00
lastmod: 2022-01-02
tags: [data structure]
categories: [Coding]
slug: Programming Literacy
draft: true
---
# 编程相关
## 代码风格与规范
go其实没有什么要说的。。
### 变量命名
主要以团队风格为主；

主流有如下三种变量规则：
- 小驼峰、大驼峰(帕斯卡命名法)命名法（java,go）
- 下划线命名法(python,linux下的c/c++编程)
- 匈牙利命名法
    - 该命名规范，要求前缀字母用变量类型的缩写，其余部分用变量的英文或英文的缩写，单词第一个字母大写。（很少用，在windows下的c/c++编程有时会用,没有IDE的时代挺好）
    ```
c
    int iMyAge;        //  "i": int
    char cMyName[10];  //  "c": char
    float fManHeight;  //  "f": float
    ```
### 核心代码模式和ACM模式
- 核心代码模式：
    - 把要处理的数据都已经放入容器里，可以直接写逻辑
- ACM输入模式呢：
    - 自己构造输入数据格式，把要需要处理的容器填充好，不会给你任何代码，包括include哪些函数都要自己写，最后也要自己控制返回数据的格式。

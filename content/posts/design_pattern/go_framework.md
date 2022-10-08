---
title: "Go_framework"
date: 2022-03-09T17:22:20+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
> 极客时间学习笔记
# go常用架构模式
```
An architectural pattern is a general, reusable solution to a commonly 
occurring problem in software architecture within a given context.
— wikipedia
```
## pipe-filter
适合数据处理、数据分析。

一个广告请求的过程（APP SSP）：就是一个数据的转化过程
- request parsing
- params processing
- verification
- filtering过滤
- ranking排序
- filling填充

pipe-filter pattern:
```
pump->filter->filter->sink
```
Pipe-Filter模式封装了数据处理的过程。

松耦合：Filter只跟数据（格式）耦合

Pipe用于连接Filter传递数据或者在异步处理过程中缓冲数据流
- 进程内同步调用时，pipe演变为数据在方法调用间传递 

filter也可以组合在一起形成con-filter。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220310092110.png)
## micro-kernel
我们使用的IDE其实都用的是微内核模式

特点
- 易于扩展
- 错误隔离
- 保持架构⼀致性

要点
- 内核包含公共流程或通⽤逻辑
- 将可变或可扩展部分规划为扩展点
- 抽象扩展点⾏为，定义接⼝
- 利⽤插件进⾏扩展

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220310092659.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220310093012.png)

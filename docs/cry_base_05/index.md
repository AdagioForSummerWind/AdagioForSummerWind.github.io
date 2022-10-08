# Cry_base_05

> 笔记来自2021年秋哈工大深圳密码学基础授课老师：蒋琳

# 序列密码
## 概述
- 序列密码，也称流密码，是对称密码中的一种
- 序列密码的因一次一密应运而生的
-  思想：用短的种子密钥来获取长的密钥序列，其独立于明文消息和密文消息。
- 结构：![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219091626.png)
- e. 密钥流是密钥和固定大小的以往密文位的函数
## 序列密码生成
- 密钥序列生成：安全性至关重要
- 密钥生成器一般由两部分构成：驱动子系统、非线性组合子系统
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219091710.png)
- 内部由有限状态自动机构成
## 伪随机序列
- m序列：最大长度线性反馈移位寄存器序列的简称。k位数据最大重复周期$2^k-1$（0不是有效数据）
- 线性反馈寄存器的特征多项式：以此产生m序列
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219091914.png)
- 产生m序列的充要条件：特征多项式为“本原多项式”
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219091945.png)
- 特性
    - 均衡性
    - 短游程特性
    - 自相关特性
- 应用
    - 测试噪声源
    - 加密通信
    - 加扰以及平衡





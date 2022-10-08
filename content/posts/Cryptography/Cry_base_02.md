---
title: "Cry_base_02"
date: 2021-12-16T15:23:51+08:00
lastmod: 2021-12-16
tags: [cryptography]
categories: [School courses]
slug: classical cryptography
draft: true
---
> 笔记来自2021年秋哈工大深圳密码学基础授课老师：蒋琳

# 古典密码学
## 代换密码
将明文中的每个字符代换成密文中的另一个字符，代换后的各字符位置不变。

- 单字母单表密码
    - 加法密码：caesar cipher
        - $c_i=(m_i+k)mod26$
        - $m_i=(c_i-k)mod26$
    - 加乘密码：affine cipher
        - $C_i=(aM_i+b)mod26$
        - $M_i=a^{-1}(C_i-b)mod26$
        - 利用拓展欧几里得算法求逆元
            - 𝑎在乘法群ℤ𝑛中有乘法逆的前提是gcd 𝑛, 𝑎 = 1，即𝑛与𝑎互质
        - 利用欧几里得算法（辗转相除法）求最大公约数
    - 混合密码：mixed alphabetic cipher 混合字母表密码
        - 给定每个字母之间的映射，按照该映射关系进行加密
- 单字母多表密码
    - 周期多表代换：有限个周期性重复的固定代换表
    - 周期多表代换：维吉尼亚密码
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211217100224.png)
    - 周期多表代换：转轮密码机（1870-1943）
        - 有限个周期重复的固定代换表
    - 无限多表代换：one-time pad（OTP）一次一密
        - 密钥和明文一样长，且密钥不能重复使用
- 多字母密码
    - 字母代换：playfair普莱菲尔密码
        - 1. 挑选个单词作为密钥
        - 2. 按照接下來的方式将字母一一填入5X5的矩阵
        - 3. 将明文分成两个字符一组(但若一组字母相同，则中间插入一个“x”)
        - 4. 加密规则:
            - 若两字符在同一行，则各以边字符取代。
            - 若两字符在同一列，则各以下方字符取代。
            - 其他情況，则换成此二字符对角的两字符。
        - 5. 解密规则:
            - 若两字符在同一行，则各以左边字符取代。
            - 若两字符在同一列，则各以上方字符取代。
            - 其他情况,则换成此二字符对角的两字符。
    - 字母代换：Hill密码
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211217101145.png)
- 代换密码安全性分析
    - 都无法避免统计规律
## 置换密码
将明文中的各字符位置次序重新排列，但不改变字符本身。
- 栅栏密码
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211217101452.png)
- 列移位密码
    - 按固定列数排列
    - 根据密钥按列移动
    - 按列读出
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211217101634.png)
## 小结
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211217102428.png)

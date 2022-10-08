# Cry_base_03

> 笔记来自2021年秋哈工大深圳密码学基础授课老师：蒋琳

# 分组密码
## 对称加密分类
- 分组密码/块密码（Block Cipher）
    - 一次加密一个分组（Playfair）
- 序列密码/流密码（Stream Cipher）
    - 一次加密一个比特或一个字节（一次一密Vernam Cipher）
## 分组密码设计准则
- 阻止分析出密钥和明文
- 混淆：密文和密钥之间的统计关系复杂
    - 一般只用来序列加密
    - 操作：代换
    - 密文和密钥之间的统计关系变得尽可能复杂，以使攻击者无法得到密钥
    - 即使攻击者能得到密文的一些统计关系，由于密钥和密文之间的统计关系复杂化，攻击者也无法得到密钥
    - 使用复杂的代换算法可以得到预期的混淆效果，而简单的线性代换函数得到的混淆效果则不太理想
- 扩散：明文统计特性散布到密文中
    - 序列加密和分组加密都可以使用
    - 操作：置换
    - 将明文的统计特性散布到密文中去，使明文的每位影响密文中多位的值
    - 等价于密文中每位均受明文中多位的影响，即从密文中不能获得明文的统计特性
## 分组密码设计方法
- 乘积密码
    - Shannon提出了乘积密码的思想：顺序地执行两个或多个代换和置换，使得最后结果的密码强度高于每个基本密码系统产生的结果
    - 费斯妥网络(Feistel Network)、SP网络(Substitution Permutation Net)
- 迭代
    - 每轮使用S盒和P盒以及其它一些方法，执行多轮
- 费斯妥网络(Feistel Network)
    - A very intelligent and interesting cipher that has been used for decades. A Feistel cipher can have three types of components: self-invertible,invertible, and noninvertible. 
- SP网络(Substitution Permutation Net)
    - Only invertible components. A component in the encryption cipher has the corresponding component in the decryption cipher. 
## 数据加密标准（DES）
- 简化版DES算法原理
    - 两部分：加密和密钥生成
    - 密钥生成：
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217115627.png)
    - 加密：
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217115848.png)
- DES算法产生及原理
    - 原理概述：
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217120045.png)
    - 单轮结构：
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217120201.png)
- DES算法安全性分析
    - 差分分析：CPA
    - 线性分析：KPA
    - 弱密钥问题
    - 线性B盒
## AES
- 简化AES和AES参数
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217120503.png)
- 简化AES
    - 总体流程：
        - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217120601.png)
    - 加密流程：
        - S盒代换、行置换、列混淆、轮密钥加
        - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217120719.png)
    - 密钥拓展：
        - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211217120750.png)
## 分组密码操作模式
分组密码在加密时，明文分组长度固定，而实际应用中待加密消息的数据量不固定，数据格式可能多种多样。

![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219090638.png)

- 电码本ECB
    - 加密：每次加密密钥相同
    - 适用：短消息加密
    - 容易暴露明文数据的格式和统计特征
    - 不会产生错误转播Error Propagation效应
- 密码分组链接CBC
    - 加密：输入是明文和前一次密文的异或
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219090926.png)
    - 会出现传播错误    
    - 能隐蔽明文的数据模式
    - 要注意IV的使用，不能IV+1和chained IV， 他们都不能解决CPA攻击
- 密码反馈CFB
    - 可以进一步将DES转化为流密码
    - 加密：输入是移位寄存器，加密的也是移位寄存器，最后与明文异或
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219091026.png)
    - 错误传播
    - 能检测出篡改，还能够隐蔽明文数据图样
- 输出反馈OFB
    - 可以进一步将DES转化为流密码
    - 加密：OFB直接将寄存器加密结构作为反馈（所以是输出反馈），而CFB是密文作为反馈（密码反馈）
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219091128.png)
    - 克服了错误传播问题，难以检测密文篡改
    - 不具有自同步功能，需要与系统保持严格的同步
- 计数器CTR
    - 加密：OFB移位寄存器换成计数器
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219091227.png)


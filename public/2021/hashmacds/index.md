# Cry_base_07

# Hash,MAC,DS
## 哈希函数应用
- 消息验证
- 完整性
- 口令验证
- MAC（与加密配合）
- 数字签名（与加密配合）
## 两个简易哈希函数
- 全文XOR
- 每块异或之后左移一位
## 哈希函数要求（哈希函数容易收到生日攻击）
- 不定长明文->定长哈希
- 单向性（抗原像攻击）
- 弱抗碰撞性（抗第二原像攻击）：给定原文和哈希，寻找碰撞
- 强抗碰撞性：任意寻找两个碰撞原文
## 哈希函数结构
- Merkle-Damgard 结构
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219095810.png)
- 两大流派：
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100001.png)
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100025.png)
## MD5
- 总结构：
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100118.png)
- MD5运算：
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100203.png)
## SHA512
- 总结构（与MD5基本一致）
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100240.png)
- 填充：
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100312.png)
## Message Authentication Code MAC 消息验证码
- 目的：检测消息的改动
- 一种结构:![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100348.png)
- 本质：一种与密钥有关的哈希函数
## Digital Signature DS 数字签名
- 性质
    - 身份验证（来自公私钥加密）
    - 数据完整性（来自hash）
    - 不可否认性（来自DS）
- RSA签名
    - 速度慢
    - 可伪造
- ElGamal签名
    - 密钥生成
        - 1) p大素数
        - 2) g本原根
        - 3) x密钥
        - 4) $y=g^xmod p$公钥
    - 签名
        - 1) $\operatorname{sig}(\mathrm{m}, \mathrm{k})=(\mathrm{r}, \mathrm{s}), \mathrm{k} \in \mathrm{Z}_{\mathrm{p}-1}^{*}$ is random
        - 2) $\mathrm{r}=\mathrm{g}^{\mathrm{k}} \bmod \mathrm{p}$
        - 3) $s=k^{-1}(H(m)-x r) \bmod (p-1)$
- Schnorr签名（略）
- DSS（略）
## PKI 公钥基础设施
- 结构如下:![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211219100920.png)




# Cry_base_06

> 笔记来自2021年秋哈工大深圳密码学基础授课老师：蒋琳

# 公钥加密（RSA，DH, ElGamal 与椭圆曲线）
## 公钥密码体制
- 非对称密码：免去密钥分发、密钥管理简单
- 原则
    - 单向函数
    - 陷门单向函数
## RSA
- 密钥生成
    - 选择两个大素数p,q
    - 随机选择公钥e,$\text { sub to } 1<\mathrm{e}<\phi(\mathrm{n}), \operatorname{gcd}(\mathrm{e}, \phi(\mathrm{n}))=1$
- 加密：C=$m^e mod n (m < n)$
- 解密：m=$C^d mod n (C < n)$
## RSA安全性
- 共模攻击
- 低指数攻击
- CCA选择密文攻击（因为RSA是确定性加密算法）
## DH密钥交换协议
- 目的：双方在网络中交换信息以生成双方共享的会话密钥
- 原理
    - 本原根g，双方密钥x,y
    - 公开交换$g^x,g^y$
    - 公钥$g^{xy}$
- 出现中间人攻击问题
## ElGamal加密
- 密钥生成
    - 选择大素数p，本原根$e_1$，选择整数d
    - 生成$e_2 = e^d_1 mod p$
    - 作为公钥， 作为私钥
- 公钥加密
    - $ C_1 = e^r_1 mod p$
    - $ C_2=(e^r_2*P)mod p$
- 私钥解密
    - $ P=C_2*(C^p_1)^{-1}mod p$
## 椭圆曲线加密
- 椭圆曲线定义:$E(a,b):y^2=x^3+ax+b$
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211219093940.png)
- 运算定义：
    - $P=(x,y) -p=(x,-y)$
    - $R=P+Q$
    - $P+P=2P$
    - $P+(-P)=O$
    - $P=(x_p,y_p),Q=(x_q,y_q)$
    - $R=P+Q=(x_r,y_r)$
    - $\begin{aligned}&-x_{r}=\lambda^{2}+\lambda-x_{p}-x_{q}-a \\&-y_{r}=-\lambda\left(x_{r}-x_{p}\right)-x_{r}-y_{p}\end{aligned}$
- 可以看到，对于P，nP非常复杂，也就是说已知nP求解出P是困难的，基于此对ElGamal做改进如下:
- 密钥生成
    - 选择椭圆曲线$E_p(a,b),e_1=(x,y),d$
    - 生成$e_2=de_1$
    - $e_2,e_1$作为公钥，d作为私钥
- 公钥加密：
    - $C_1=re_1$
    - $C_2=p+re_2$
- 私钥解密：
    - $P=C_2-dC_1$




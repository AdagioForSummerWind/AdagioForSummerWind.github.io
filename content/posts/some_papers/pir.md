---
title: "隐私信息检索"
date: 2022-05-13T09:33:30+08:00
lastmod: 2022-05-14
tags: [papers]
categories: [Software design B]
slug: One Server for the Price of Two:Simple and Fast Single-Server Private Information Retrieval
draft: true
---

> One Server for the Price of Two:Simple and Fast Single-Server Private Information Retrieval
> https://eprint.iacr.org/2022/949.pdf


# 两台服务器的价格：简单快速的单服务器隐私信息检索


Abstract. We present SimplePIR, the fastest private information retrieval (PIR) scheme known to date. SimplePIR
is a single-server PIR scheme, whose security holds under
the learning-with-errors assumption. To answer a client’s PIR
query, the SimplePIR server performs one 32-bit multiplication
and one 32-bit addition per database byte. SimplePIR achieves
6.5 GB/s/core server throughput, which is 7% faster than the
fastest two-server PIR schemes (which require non-colluding
servers). SimplePIR has relatively large communication costs:
to make queries to a 1 GB database, the client must download a
124 MB “hint” about the database contents; thereafter, the client
may make an unbounded number of queries, each requiring
242 KB of communication. We present a second single-server
scheme, DoublePIR, that shrinks the hint to 16 MB at the
cost of slightly higher per-query communication (345 KB) and
slightly lower throughput (5.2 GB/s/core). Finally, we apply
our PIR schemes, together with a new data structure for approximate set membership, to the problem of private auditing in
Certificate Transparency. We achieve a strictly stronger notion
of privacy than Google Chrome’s current approach with a
modest, 13× larger communication overhead.

## 摘要
我们提出了 SimplePIR，这是迄今为止已知的最快的私人信息检索 (PIR) 方案。 SimplePIR 是一种单服务器 PIR 方案，其安全性在错误学习假设下成立。为了回答客户端的 PIR 查询，SimplePIR 服务器对每个数据库字节执行一次 32 位乘法和一次 32 位加法。 SimplePIR 实现了 6.5 GB/s/核心服务器吞吐量，比最快的两台服务器 PIR 方案（需要非共谋服务器）快 7%。 SimplePIR 的通信成本比较大：要查询 1 GB 的数据库，客户端必须下载 124 MB 的关于数据库内容的“提示”；此后，客户端可以进行无限数量的查询，每个查询需要 242 KB 的通信。我们提出了第二种单服务器方案，DoublePIR，它将提示缩小到 16 MB，代价是每次查询通信稍高 (345 KB) 和吞吐量稍低 (5.2 GB/s/core)。最后，我们将我们的 PIR 方案以及用于近似集合成员的新数据结构应用于证书透明度中的私人审计问题。我们实现了比 Google Chrome 当前方法更严格的隐私概念，但通信开销却是适度的​​ 13 倍。

## 1 介绍

In a Private Information Retrieval (PIR) protocol [26, 53], a
database server holds an array of 𝑁 records. A client wants to
fetch record 𝑖 ∈ {1, · · · , 𝑁} from the server, without revealing
the index 𝑖 that it desires to the server. PIR has applications
to systems for private database search [75, 81], metadatahiding messaging [9, 10], private media consumption [48],
credential breach reporting [4,57,80,82], and private blocklist
lookups [52], among others.

在私人信息检索 (PIR) 协议 [26, 53] 中，数据库服务器保存一组 𝑁 记录。 客户端想要从服务器获取记录 𝑖 ∈ {1, · · · , 𝑁}，而不向服务器透露它想要的索引 𝑖。 PIR 可应用于私有数据库搜索 [75, 81]、元数据隐藏消息 [9, 10]、私有媒体消费 [48]、凭据泄露报告 [4,57,80,82] 和私有阻止列表查找 [52] 的系统 ，等等。


Modern PIR schemes require surprisingly little communication: with a single database server, and under modest
cryptographic assumptions [20, 44, 70], the total communication required to fetch a database record grows only polylogarithmically with the number of records, 𝑁. Unfortunately,
PIR schemes are computationally expensive: the server must
touch every bit of the database to answer even a single client
query [12], since otherwise the PIR scheme leaks information
about which database records the client is not interested in.
(Recently, there have been a number of PIR schemes that
preprocess the database to achieve a runtime sublinear in the
database size, but all known approaches either require client-specific preprocessing [29,30,52,77,85] or impractically large
server storage [12, 16, 21].) Thus, a hard limit on the performance of PIR schemes is the speed with which the PIR server
can read the database from memory—roughly 12.8 GB/s/core
on our machine.

现代 PIR 方案几乎不需要通信：使用单个数据库服务器，并且在适度的密码学假设下 [20,44,70]，获取数据库记录所需的总通信量仅随记录数量 𝑁 呈多对数增长。 不幸的是，PIR 方案的计算成本很高：服务器必须触及数据库的每一位才能回答甚至单个客户端查询 [12]，否则 PIR 方案会泄漏有关客户端不感兴趣的数据库记录的信息。（最近，有 已经有许多 PIR 方案对数据库进行预处理以实现数据库大小的运行时亚线性，但所有已知的方法要么需要特定于客户端的预处理 [29,30,52,77,85]，要么需要不切实际的大型服务器存储 [12, 16, 21]。）因此，对 PIR 方案性能的硬性限制是 PIR 服务器从内存中读取数据库的速度——在我们的机器上大约为 12.8 GB/s/core。



In the standard setting, in which the client communicates
with only a single database server, the performance of existing
PIR schemes is far from this theoretical limit: we measure that
the fastest single-server PIR schemes achieve a throughput of
259 MB/s/core, or 2% of the maximum possible [66], on a
database of hundred-byte records. It is possible to push the
performance up to 1.3 GB/s/core when the database records are
hundreds of kilobytes long, though that parameter setting is not
relevant for many PIR applications, including our application
to Certificate Transparency.

在标准设置下，客户端仅与单个数据库服务器通信，现有 PIR 方案的性能远未达到这个理论极限：我们测量到最快的单服务器 PIR 方案的吞吐量达到 259 MB/s/core ，或最大可能的 2% [66]，在百字节记录的数据库中。 当数据库记录长达数百 KB 时，可以将性能提高到 1.3 GB/s/core，尽管该参数设置与许多 PIR 应用程序无关，包括我们对证书透明度的应用程序。

When the client can communicate with multiple noncolluding database servers [26], faster PIR schemes exist.
In the two-server setting, schemes based on distributed point
functions [15, 45] can achieve 5.4 GB/s/core throughput, or
42% of the maximum possible. Here, two servers must answer
each query, which halves the throughput per core. Unfortunately, these two-server schemes are cumbersome to deploy,
since they rely on multiple coordinating yet independent infrastructure providers. In addition, their security is brittle, as
it stems from a non-collusion assumption rather than from
cryptographic hardness. Thus, existing PIR schemes either
have poor performance—in the single-server setting—or undesirable trust assumptions—in the multi-server setting.

当客户端可以与多个非共谋数据库服务器通信时[26]，存在更快的 PIR 方案。 在两台服务器的设置中，基于分布式点功能的方案 [15, 45] 可以实现 5.4 GB/s/core 吞吐量，或最大可能的 42%。 在这里，两个服务器必须回答每个查询，这将每个核心的吞吐量减半。 不幸的是，这些两台服务器方案部署起来很麻烦，因为它们依赖于多个协调但独立的基础设施提供商。 此外，它们的安全性很脆弱，因为它源于非共谋假设而不是密码学难度。 因此，现有的 PIR 方案要么在单服务器设置中性能不佳，要么在多服务器设置中具有不良的信任假设。

In this paper, we present two new single-server PIR schemes;
our faster scheme achieves the highest server throughput of
any existing PIR protocol, including multi-server ones. Our
schemes have modest per-query communication of a few hundred KB on a database of one billion short rows, and their
security is based on Regev’s learning-with-errors assumption [76]. In addition, our schemes are relatively simple to
explain and easy to implement: our complete implementation
of both schemes together requires roughly 1,200 lines of Go
code, plus 80 lines of C, and uses no external libraries.

在本文中，我们提出了两种新的单服务器 PIR 方案； 我们更快的方案实现了任何现有 PIR 协议（包括多服务器协议）的最高服务器吞吐量。 我们的方案在 10 亿个短行的数据库上具有几百 KB 的适度每次查询通信，并且它们的安全性基于 Regev 的错误学习假设 [76]。 此外，我们的方案解释起来相对简单，实现起来也很容易：两个方案的完整实现总共需要大约 1200 行 Go 代码，加上 80 行 C 代码，并且不使用外部库。

More specifically, our first scheme, SimplePIR, achieves
a server throughput of 6.5 GB/s/core, though it requires the
client to download a relatively large “hint” about the database
contents before making its queries. On a database of size 𝑁,
the hint has size roughly 1024√
𝑁 bytes . The hint is not clientspecific, and a client can reuse the hint over many queries,so the amortized communication cost per query can be small.
Our second scheme, DoublePIR, achieves slightly lower server
throughput of 5.2 GB/s/core, but shrinks the hint to roughly
16 MB for a database of one-byte records—independent of the
number of records in the database.

更具体地说，我们的第一个方案 SimplePIR 实现了 6.5 GB/s/core 的服务器吞吐量，尽管它需要客户端在进行查询之前下载关于数据库内容的相对较大的“提示”。 在大小为 𝑁 的数据库上，提示的大小约为 1024√ 𝑁 字节。 提示不是特定于客户端的，并且客户端可以在许多查询中重用提示，因此每个查询的摊销通信成本可以很小。 我们的第二个方案 DoublePIR 实现了 5.2 GB/s/核心的稍低的服务器吞吐量，但对于单字节记录的数据库，将提示缩小到大约 16 MB——与数据库中的记录数无关。

Our techniques. We now summarize the technical ideas behind
our results.
我们的技术。 我们现在总结我们的结果背后的技术思想。

### Recap: Single-server PIR

Our starting point is the singleserver PIR construction of Kushilevitz and Ostrovsky [53]. In
their scheme, the PIR server represents an 𝑁-record database
as a matrix D of dimension √
𝑁 by √
𝑁. To fetch the database
record in row 𝑖 and column 𝑗, the client sends the server
the encryption 𝐸(q) of a dimension-√
𝑁 vector that is zero
everywhere except that it has a “1” in index 𝑗. If the encryption
scheme is linearly homomorphic, the server can compute the
matrix-vector product D · 𝐸(q) = 𝐸(D · q) under encryption
and return the result to the client. The client decrypts to recover
D·q which, by construction, is the 𝑗-th column of the database,
as desired. The total communication grows as √
𝑁.

我们的出发点是 Kushilevitz 和 Ostrovsky [53] 的单服务器 PIR 构建。 在他们的方案中，PIR 服务器将 𝑁 记录数据库表示为维度为 √ 𝑁 × √ 𝑁 的矩阵 D。 为了获取行𝑖和列𝑗中的数据库记录，客户端向服务器发送维度-√𝑁向量的加密𝐸（q），除了索引𝑗中的“1”之外，它在任何地方都为零。 如果加密方案是线性同态的，服务器可以在加密下计算矩阵-向量积D·𝐸(q)=𝐸(D·q)并将结果返回给客户端。 客户端解密以恢复 D·q，根据需要，它是数据库的第 𝑗 列。 总通信量增长为 √ 𝑁。

SimplePIR from linearly homomorphic encryption with preprocessing. 
SimplePIR 来自带预处理的线性同态加密。

 The PIR server’s throughput here is limited by the
speed with which it can compute the product of the plaintext
matrix D with the encrypted vector 𝐸(q). Our observation in
SimplePIR (Section 4) is that, using Regev’s learning-witherrors-based encryption scheme [76], the server can perform
the vast majority of the work of computing the matrix-vector
product D · 𝐸(q) in advance—before the client even makes
its query. The server’s preprocessing depends only on the
database D and the public parameters of the Regev encryption
scheme, so the server can reuse this preprocessing work across
many queries from many independent clients. After this preprocessing step, to answer a client’s query, the server needs
to compute only roughly 𝑁 32-bit integer multiplications and
additions on a database of 𝑁 bytes. The only catch is that the
client must download a “hint” about the database contents after
this preprocessing step—the hint accounts for the bulk of the
communication cost in SimplePIR.

此处 PIR 服务器的吞吐量受限于它计算明文矩阵 D 与加密向量 𝐸(q) 的乘积的速度。我们在 SimplePIR（第 4 节）中的观察结果是，使用 Regev 的基于错误学习的加密方案 [76]，服务器可以提前执行绝大多数计算矩阵向量积 D·𝐸(q) 的工作——在客户端甚至进行查询之前。服务器的预处理只依赖于数据库 D 和 Regev 加密方案的公共参数，因此服务器可以在来自许多独立客户端的许多查询中重用此预处理工作。在这个预处理步骤之后，为了回答客户端的查询，服务器只需要在 𝑁 字节的数据库上计算大约 𝑁 32 位整数乘法和加法。唯一的问题是客户端必须在此预处理步骤之后下载有关数据库内容的“提示”——提示占 SimplePIR 中通信成本的大部分。


### DoublePIR from one recursive step. 
……

### 申请证书透明度
. Finally, we evaluate
our PIR schemes in the context of the application of signed certificate timestamp (SCT) auditing in Certificate Transparency.

最后，我们在证书透明度中应用签名证书时间戳(SCT)审计的背景下评估我们的PIR方案。

In this auditing application, a server holds a set 𝑆 of strings and a client (web browser) wants to test whether a particular string
𝜎, representing an SCT, appears in the set 𝑆, while hiding
𝜎 from the server. (The string 𝜎 reveals information about
which websites a client has visited.) Google Chrome currently
proposes implementing this auditing step using a solution that
provides 𝑘-anonymity for 𝑘 = 1000 [33].

在这个审计应用程序中，服务器持有一组字符串𝑆，并且客户端（Web 浏览器）想要测试表示 SCT 的特定字符串 𝜎 是否出现在集合 𝑆 中，同时对服务器隐藏 𝜎。 （字符串 𝜎 揭示了有关客户访问过哪些网站的信息。）Google Chrome 目前建议使用为 𝑘 = 1000 [33] 提供 𝑘-匿名的解决方案来实施此审计步骤。


Along the way, we construct a new data structure (Section 6)
for more efficiently solving this type of private set-membership
problem using PIR, when a constant rate of false positives is acceptable (as in our application). In this setting, standard Bloom
filters [14] and approaches based on PIR by keywords [25]
require the client to perform PIR over a database of 𝜆𝑁 bits (if
the set 𝑆 has size 𝑁 and 𝜆 ≈ 128 is a security parameter). In
contrast, our data structure requires performing PIR over only
8𝑁 bits—giving a roughly 16× speedup in our application.

在此过程中，我们构建了一个新的数据结构（第 6 节），以使用 PIR 更有效地解决这种类型的私有集合成员问题，此时可以接受恒定的误报率（如在我们的应用程序中）。 在此设置中，标准 Bloom 过滤器 [14] 和基于 PIR 通过关键字 [25] 的方法要求客户端在 𝜆𝑁 位的数据库上执行 PIR（如果集合 𝑆 的大小为 𝑁 并且 𝜆 ≈ 128 是一个安全参数）。 相比之下，我们的数据结构只需要在 8𝑁 位上执行 PIR——在我们的应用程序中提供大约 16 倍的加速。


Google’s current solution to SCT auditing, which provides 𝑘-
anonymity rather than full cryptographic privacy, requires the
client to communicate 240 B on average per TLS connection.
Our solution, which provides cryptographic privacy, requires
1.5 KB and 0.004 core-seconds of server compute on average
per TLS connection, along with 16 MB of client download
and 400 KB of client storage every week to maintain the hint.

Google 当前的 SCT 审计解决方案提供了 𝑘- 匿名性而非完整的加密隐私，它要求客户端平均每个 TLS 连接通信 240 B。 我们的解决方案提供加密隐私，每个 TLS 连接平均需要 1.5 KB 和 0.004 核心秒的服务器计算，以及每周 16 MB 的客户端下载和 400 KB 的客户端存储来维护提示。



### 不足
Our new PIR schemes come with two main downsides. First, our client must download a “hint:” on databases
gigabytes in size, the hint is tens of megabytes. If a client makes
only one query, this hint download dominates the overall communication. Second, our schemes’ online communication is
on the order of hundreds of kilobytes on databases with short
entries—which is 10× larger than in some prior work. On
databases with with larger entries, this gap increases further.
Nevertheless, we believe that SimplePIR and DoublePIR represent an exciting new point in the PIR design space: large
computation savings, along with a conceptually simple design and small, stand-alone codebase, at the cost of modest
communication and storage overheads.

我们新的 PIR 计划有两个主要缺点。 首先，我们的客户端必须下载一个“提示：”数据库大小为千兆字节，提示为数十兆字节。 如果客户端只进行一次查询，则此提示下载将主导整个通信。 其次，我们方案的在线通信在具有短条目的数据库上约为数百 KB，比之前的一些工作大 10 倍。 在具有较大条目的数据库上，这种差距进一步扩大。 尽管如此，我们相信 SimplePIR 和 DoublePIR 代表了 PIR 设计领域中一个令人兴奋的新点：大量的计算节省，以及概念上简单的设计和小型的独立代码库，但代价是适度的通信和存储开销。

In summary, our contributions are:
- two new high-throughput single-server private informationretrieval protocols (Sections 4 and 5),
- a new data structure for private set membership using PIR (Section 6) and its application to private auditing in Certificate Transparency (Section 7), and
- the evaluation of these schemes, using a new open-source implementation (Section 8).

总之，我们的贡献是：
- 两个新的高吞吐量单服务器私有信息检索协议（第 4 节和第 5 节），
- 使用 PIR（第 6 节）的私有集成员资格的新数据结构及其在证书透明度中的私有审计中的应用（第 7 节），以及
- 使用新的开源实现对这些方案进行评估（第 8 节）。


## 2 Related work and comparison

Chor, Goldreich, Kushilevitz and Sudan [26] introduced PIR
in the multi-server setting and Kushilevitz and Ostrovsky [53]
gave the first construction of single-server PIR. Their scheme
uses a linearly homomorphic encryption scheme that expands
ℓ-bit plaintexts to ℓ · 𝐹-bit ciphertexts. We call 𝐹 the expansion
factor of the encryption scheme. Then, on a database of N bits and any dimension parameter 𝑑 ∈ {1, 2, 3, . . . }, their
PIR construction has communication roughly 𝑁
1/𝑑𝐹
𝑑−1
. The
server must perform roughly 𝑁𝐹𝑑−1 homomorphic operations
in the process of answering the client’s query.

Chor、Goldreich、Kushilevitz 和苏丹 [26] 在多服务器设置中引入了 PIR，Kushilevitz 和 Ostrovsky [53] 首次构建了单服务器 PIR。 他们的方案使用线性同态加密方案，将ℓ位明文扩展为ℓ·𝐹位密文。 我们称𝐹为加密方案的扩展因子。 然后，在 N 位和任何维度参数的数据库上 𝑑 ∈ {1, 2, 3, . . . }，他们的 PIR 结构大致有通信 𝑁 1/𝑑𝐹 𝑑−1 。 服务器在回答客户端查询的过程中必须执行大致𝑁𝐹𝑑−1 同态操作。

The Damgård-Jurik [32] cryptosystem has expansion factor 𝐹 ≈ 1, which yields very communication-efficient PIR
schemes [60]. It is possible to construct PIR with similar
communication efficiency from an array of cryptographic assumptions [20, 23, 36]. However, these schemes are all costly
in computation: for each bit of the database, the server must
perform work polynomial in the security parameter.

Damgård-Jurik [32] 密码系统具有扩展因子 𝐹 ≈ 1，它产生了非常有效的通信 PIR 方案 [60]。 从一系列密码学假设 [20, 23, 36] 可以构建具有相似通信效率的 PIR。 然而，这些方案在计算上都是昂贵的：对于数据库的每一位，服务器必须在安全参数中执行工作多项式。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917110251.png)


Table 1: A comparison of PIR schemes on database size 𝑁 and
security parameter 𝑛. The memory bandwidth of our machine is 12.8
GB/s/core; as each of these schemes must read the whole database
from memory, their throughput cannot be higher than this upper bound.
XOR refers to a benchmark that performs a linear scan of XORs over
the database (see Section 8.1). The overhead column indicates whether
the server computation per database bit is at most polylogarithmic in 𝑛.
The throughput column gives the maximum throughput we measured
for any database size and record size. The database sizes we used for
these numbers are in Appendix A. The throughput is normalized by
the number of cores, i.e., divided by two for 2-server PIR schemes
(DPF PIR and XOR). ★No open-source implementation available.
This is the throughput reported in the MulPIR paper [8]. ♥This is the
XPIR throughput reported by SealPIR [9].


表 1：PIR 方案在数据库大小 𝑁 和安全参数 𝑛 上的比较。 我们机器的内存带宽是12.8 GB/s/core； 由于这些方案中的每一个都必须从内存中读取整个数据库，因此它们的吞吐量不能高于这个上限。 XOR 是指对数据库执行 XOR 线性扫描的基准（参见第 8.1 节）。 开销列指示每个数据库位的服务器计算是否最多是 𝑛 中的多对数。 吞吐量列给出了我们针对任何数据库大小和记录大小测量的最大吞吐量。 我们用于这些数字的数据库大小在附录 A 中。吞吐量由核心数量标准化，即，对于 2 台服务器 PIR 方案（DPF PIR 和 XOR）除以 2。 ★没有可用的开源实现。 这是 MulPIR 论文 [8] 中报告的吞吐量。 ♥这是 SealPIR [9] 报告的 XPIR 吞吐量。



Lattice-based PIR. 

To drive down this computational cost,
recent PIR schemes instantiate the Kushilevitz-Ostrovsky construction using encryption schemes based on the ring learningwith-errors problem (“Ring LWE”) [63]. In these schemes, for
each bit of the database, the server performs work polylogarithmic in the security parameter—rather than polynomial.
However, these savings in computation come at the cost of a
larger expansion factor (𝐹 ≈ 10), which increases the communication as the dimension parameter 𝑑 cannot be too large. For
example, XPIR [5] takes 𝑑 = 2. In addition, the client in the
Kushilevitz-Ostrovsky scheme must upload 𝑁
1/𝑑
ciphertexts,and each ring-LWE ciphertext is at least thousands of kilobytes
in size. This imposes large absolute communication costs (e.g.,
tens of MB per query, on a database of hundreds of MB).

基于格的 PIR。

为了降低这种计算成本，最近的 PIR 方案使用基于环学习错误问题（“环 LWE”）[63] 的加密方案来实例化 Kushilevitz-Ostrovsky 结构。 在这些方案中，对于数据库的每一位，服务器在安全参数中执行多项式工作，而不是多项式。 然而，这些计算上的节省是以更大的扩展因子 (𝐹 ≈ 10) 为代价的，这增加了通信，因为维度参数 𝑑 不能太大。 例如，XPIR[5]取𝑑=2。另外，Kushilevitz-Ostrovsky方案中的客户端必须上传𝑁1/𝑑密文，每个环-LWE密文的大小至少为数千字节。 这会带来很大的绝对通信成本（例如，对于数百 MB 的数据库，每次查询数十 MB）。


SealPIR [9] shows that the client can compress the ciphertexts in an XPIR-style scheme before uploading them. The
server can then expand these ciphertexts using homomorphic operations. (FastPIR [6] uses a similar idea to compress
responses.) This optimization reduces the communication
costs by orders of magnitude, though it requires the server
to store some per-client information (“key-switching hints”)—
essentially, encryptions of the client’s secret decryption key—
that is megabytes in size and that the client must upload to the
server before it makes any queries.

SealPIR [9] 表明客户端可以在上传之前以 XPIR 样式的方案压缩密文。 然后服务器可以使用同态操作扩展这些密文。 （FastPIR [6] 使用类似的想法来压缩响应。）这种优化将通信成本降低了几个数量级，尽管它需要服务器存储一些每个客户端的信息（“密钥切换提示”）——本质上，加密 客户端的秘密解密密钥——大小为兆字节，客户端在进行任何查询之前必须上传到服务器。

MulPIR [8], OnionPIR [67], and Spiral [66] additionally
use fully homomorphic encryption [42] to further reduce the
communication cost. In Spiral [66], for example, the cost grows
roughly as 𝑁
1/𝑑𝐹, where the exponent on the 𝐹 term is now 1
instead of 𝑑 − 1. Building on ideas of Gentry and Halevi [43],
Spiral shows how to achieve this decrease in communication
cost while keeping the throughput high: up to 259 MB/s on a
database of short records. (When the database records are long,
Spiral does not use the SealPIR query compression technique
and gets throughput as large as 1,314 MB/s at the cost of
increased communication.)


MulPIR [8]、OnionPIR [67] 和 Spiral [66] 还使用全同态加密 [42] 以进一步降低通信成本。 例如，在 Spiral [66] 中，成本大致增长为 𝑁 1/𝑑𝐹，其中 𝐹 项的指数现在是 1 而不是 𝑑 − 1。基于 Gentry 和 Halevi [43] 的想法，Spiral 展示了如何 在保持高吞吐量的同时降低通信成本：在短记录数据库上高达 259 MB/s。 （当数据库记录较长时，Spiral 不使用 SealPIR 查询压缩技术，并以增加通信为代价获得高达 1,314 MB/s 的吞吐量。）

Plain learning with errors. 

We base our PIR schemes on
the standard learning-with-errors (LWE) problem—not the
ring variant. The expansion factor of the standard LWE-based
encryption scheme, Regev encryption [76], is roughly 𝐹 =
𝑛 ≈ 1024, where 𝑛 is the lattice security parameter. This large
expansion factor means that a direct application of Regev
encryption to the Kushilevitz-Ostrovsky PIR scheme would be
disastrous in terms of communication and computation. Our
innovation is to show that the server can do the bulk of its work
in advance, and reuse it over multiple clients.

简单的错误学习。

我们的 PIR 方案基于标准的错误学习 (LWE) 问题，而不是环变体。 标准基于 LWE 的加密方案 Regev 加密 [76] 的扩展因子大致为 𝐹 = 𝑛 ≈ 1024，其中 𝑛 是格安全参数。 这种大的扩展因子意味着将 Regev 加密直接应用于 Kushilevitz-Ostrovsky PIR 方案在通信和计算方面将是灾难性的。 我们的创新是表明服务器可以提前完成大部分工作，并在多个客户端上重用它。

Aside from the fact that our scheme is based on a weaker
cryptographic assumption, namely plain LWE as opposed to
ring LWE, this strategy yields multiple benefits:

除了我们的方案基于较弱的加密假设（即普通 LWE 而不是环 LWE）这一事实之外，这种策略还产生了多种好处：

1. Our LWE-based schemes are simple to implement: they require no polynomial arithmetic or fast Fourier transforms.
2. Our schemes do not require the server to store any extra per-client state. In contrast, many schemes based on Ring LWE [8, 9, 66, 67] rely on optimizations that require the server to store one “key-switching hint” for each client.
3. Our schemes are faster. We avoid the costs associated with ciphertext compression and expansion. In addition, since we only need our encryption scheme to be linearly (not fully) homomorphic, we can use smaller and more efficient lattice parameters.

1. 我们基于 LWE 的方案易于实现：它们不需要多项式算术或快速傅立叶变换。
2. 我们的方案不需要服务器存储任何额外的每个客户端状态。 相比之下，许多基于 Ring LWE [8, 9, 66, 67] 的方案依赖于需要服务器为每个客户端存储一个“按键切换提示”的优化。
3. 我们的方案更快。 我们避免了与密文压缩和扩展相关的成本。 另外，由于我们只需要我们的加密方案是线性的（不是完全的）同态的，我们可以使用更小更有效的格参数。


The drawback of our schemes is that they have larger communication cost, especially when the client makes only a single
query (so the client cannot amortize the offline download cost
over multiple queries) or when the database records are long.

我们的方案的缺点是它们具有较大的通信成本，特别是当客户端只进行一次查询（因此客户端无法将离线下载成本分摊到多个查询中）或数据库记录很长时。

Preprocessing and PIR. 
The server in our PIR schemes performs some client-independent preprocessing. Prior work
uses server-side preprocessing—either one-time [12,16,21] or
per-client [29, 30, 52, 77, 85]—to build PIR where the server
online work is sublinear in the database size. Prior work also
proves strong lower bounds on the performance of any such PIR
with preprocessing schemes [12,29,30,74]. In contrast, in this
work, we use preprocessing to build PIR where the amortized
per-query server work is still linear, but it is concretely efficient.

预处理和 PIR。 
我们的 PIR 方案中的服务器执行一些独立于客户端的预处理。 先前的工作使用服务器端预处理（一次性 [12,16,21] 或每个客户端 [29,30,52,77,85]）来构建 PIR，其中服务器在线工作在数据库大小中是次线性的。 先前的工作还证明了使用预处理方案 [12,29,30,74] 对任何此类 PIR 的性能都有很强的下限。 相比之下，在这项工作中，我们使用预处理来构建 PIR，其中摊销的每个查询服务器工作仍然是线性的，但它实际上是有效的。

Hardware acceleration for PIR. 
Recent work improves the
throughput of both single-server [58] and multi-server [49] PIR
using hardware acceleration. This approach is complementary
to ours, as it may further speed up our new PIR protocols.

PIR 的硬件加速。 
最近的工作使用硬件加速提高了单服务器 [58] 和多服务器 [49] PIR 的吞吐量。 这种方法是对我们的补充，因为它可以进一步加快我们的新 PIR 协议。

Privacy and certificate transparency. Lueks and Goldberg [62] and Kales, Omolola, and Ramacher [51] propose
using multi-server PIR for auditing in certificate transparency.
We work in the single-server setting, where the client communicates with a separate audit server (e.g., Google, in the application to Chrome). Further, we introduce a new set-membership
data structure to reduce the cost of auditing (Section 6). We
discuss existing approaches to auditing in Section 7.2.

隐私和证书透明度。 Lueks 和 Goldberg [62] 以及 Kales、Omolola 和 Ramacher [51] 建议使用多服务器 PIR 来审计证书透明度。 我们在单服务器设置中工作，其中客户端与单独的审计服务器（例如，谷歌，在 Chrome 的应用程序中）进行通信。 此外，我们引入了一种新的集合成员数据结构来降低审计成本（第 6 节）。 我们将在第 7.2 节讨论现有的审计方法。

## 3 Background and definitions

Notation. For a probability distribution 𝜒, we use 𝑥 ←R
𝜒 to
indicate that 𝑥 is a uniformly random sample from 𝜒. For a finite
set 𝑆, we use 𝑥 ←R
𝑆 to denote sampling 𝑥 uniformly at random
from 𝑆. We use N to represent the natural numbers and Z𝑝 to
represent integers modulo 𝑝. All logarithms are to the base two.
For 𝑛 ∈ N, we let [𝑛] denote the set {1, . . . , 𝑛}. Throughout,
we assume that values like √
𝑁 are integral, wherever doing
so is essentially without loss of generality. Algorithms are
modeled as RAM programs and their runtime is measured in
terms of the number of RAM instructions executed. We use the
symbols MB and MiB, as well as GB and GiB, synonymously

符号。 对于概率分布 𝜒，我们使用 𝑥 ←R 𝜒 来表示 𝑥 是来自 𝜒 的均匀随机样本。 对于有限集 𝑆，我们使用 𝑥 ←R 𝑆 表示从 𝑆 均匀随机抽样 𝑥。 我们用 N 表示自然数，用 Z𝑝 表示模 𝑝 的整数。 所有对数均以二为底。 对于 𝑛 ∈ N，我们让 [𝑛] 表示集合 {1, . . . ，𝑛}。 在整个过程中，我们假设像 √ 𝑁 这样的值是完整的，只要这样做基本上不失一般性。 算法被建模为 RAM 程序，它们的运行时间是根据执行的 RAM 指令的数量来衡量的。 我们将符号 MB 和 MiB 以及 GB 和 GiB 用作同义词

### 3.1 Learning with errors (LWE)


The security of our PIR schemes relies on the decision version
of the learning-with-errors assumption [76]. The assumption
is parameterized by the dimension of the LWE secret 𝑛 ∈ N,
the number of samples 𝑚 ∈ N, the integer modulus 𝑞 ≥ 2,
and an error distribution 𝜒 over Z. The LWE assumption then
asserts that for a matrix A ←R ……
, the following
distributions are computationally indistinguishable:

我们的 PIR 方案的安全性依赖于错误学习假设的决策版本 [76]。 该假设由 LWE 秘密的维度 𝑛 ∈ N、样本数量 𝑚 ∈ N、整数模 𝑞 ≥ 2 以及 Z 上的误差分布 𝜒 参数化。LWE 假设随后断言对于矩阵 A ←R Z 𝑚×𝑛 𝑞 、秘密 s ←R Z 𝑛 𝑞 、误差向量 e ←R 𝜒 𝑚 和随机向量 r ←R Z 𝑚 𝑞 ，以下分布在计算上无法区分：

$$
\{(\mathbf{A}, \mathbf{A s}+\mathbf{e})\} \stackrel{c}{\approx}\{(\mathbf{A}, \mathbf{r})\}
$$

More specifically, the (𝑛, 𝑞, 𝜒)-LWE problem with 𝑚 samples is (𝑇, 𝜖)-hard if all adversaries running in time 𝑇 have
advantage at most 𝜖 in distinguishing the two distributions. In
Section 4.2, we give concrete values for the LWE parameters.

更具体地说，如果所有在时间 𝑇 上运行的对手在区分两个分布时最多具有优势 在 4.2 节中，我们给出了 LWE 参数的具体值。

Secret-key Regev encryption. Regev [76] gives a secret-key
encryption scheme that is secure under the LWE assumption.With LWE parameters (𝑛, 𝑞, 𝜒) and a plaintext modulus 𝑝, the
Regev secret key is a vector s ←R…… The Regev encryption of
a message 𝜇 ∈ Z𝑝 is

密钥 Regev 加密。 
Regev [76] 给出了在 LWE 假设下安全的密钥加密方案。使用 LWE 参数（𝑛，𝑞，𝜒）和明文模数𝑝，Regev 密钥是向量 s ←R Z 𝑛 𝑞。 消息 𝜇 ∈ Z𝑝 的 Regev 加密是

$$
(\mathbf{a}, c)=\left(\mathbf{a}, \mathbf{a}^{\top} \mathbf{s}+e+\lfloor q / p\rfloor \cdot \mu\right) \in \mathbb{Z}_q^n \times \mathbb{Z}_q
$$

for 𝑒 ←R
𝜒. To decrypt the ciphertext, anyone who knows the
secret s can compute 𝑐 −a
⊺
s mod 𝑞 and round the result to the
nearest multiple of ⌊𝑞/𝑝⌋. Decryption succeeds as long as the
absolute value of the error sampled from the error distribution
𝜒 is smaller than 1
2
· ⌊𝑞/𝑝⌋. We say that a setting of the Regev
parameters supports correctness error 𝛿 if the probability of a
decryption error is at most 𝛿 (over the encryption algorithm’s
randomness). Regev encryption is additively homomorphic,
since given two ciphertexts (a1, 𝑐1) and (a2, 𝑐2), their sum
(a1 +a2, 𝑐1 +𝑐2) decrypts to the sum of the plaintexts, provided
again that the error remains sufficiently small.

对于𝑒←R𝜒。 要解密密文，任何知道秘密 s 的人都可以计算 𝑐 -a ⊺ s mod 𝑞 并将结果四舍五入到最接近的 ⌊𝑞/𝑝⌋ 倍数。 只要从误差分布𝜒中采样的误差的绝对值小于 1 2 · ⌊𝑞/𝑝⌋，解密就会成功。 如果解密错误的概率最多为 𝛿（超过加密算法的随机性），我们说 Regev 参数的设置支持正确性错误 𝛿。 Regev 加密是加法同态的，因为给定两个密文 (a1, 𝑐1) 和 (a2, 𝑐2)，它们的和 (a1 +a2, 𝑐1 +𝑐2) 解密为明文的总和，前提是误差仍然足够小。

### 3.2 Private information retrieval with hints

We now give the syntax and security definitions for the type of
PIR schemes we construct. Our form of PIR is very similar to
the standard single-server PIR schemes [26, 53]. The primary
distinction is that we allow the PIR server to preprocess the
database ahead of time and to output two “hints”: one that the
server stores locally, and another that the server sends to each
client. This preprocessing allows the PIR server to push much
of its computational work into an offline phase that takes place
before the client makes its query. In our constructions, both
hints are small—they have size sublinear in the database size.
In addition, all clients use the same hint and a client can reuse
the same hint for an unbounded number of PIR queries.

我们现在给出我们构建的 PIR 方案类型的语法和安全定义。 我们的 PIR 形式与标准的单服务器 PIR 方案非常相似 [26, 53]。 主要区别在于我们允许 PIR 服务器提前预处理数据库并输出两个“提示”：一个是服务器存储在本地的，另一个是服务器发送给每个客户端的。 这种预处理允许 PIR 服务器将其大部分计算工作推入离线阶段，该阶段发生在客户端进行查询之前。 在我们的构造中，这两个提示都很小——它们的大小在数据库大小中是次线性的。 此外，所有客户端都使用相同的提示，并且客户端可以针对无限数量的 PIR 查询重复使用相同的提示。

Remark 3.1 (Handling database updates). As PIR schemes with
preprocessing perform some precomputation over the database,
the server inherently needs to repeat some of this work if
the database contents change. Related work investigates how
to minimize the amount of computation and communication
that such database updates incur, in both a black-box [52] and
a protocol-specific [64] manner. We address how to handle
updates in our schemes in Appendices C.3 and E.3.

备注 3.1（处理数据库更新）。 由于带有预处理的 PIR 方案对数据库执行一些预计算，因此如果数据库内容发生变化，服务器本质上需要重复其中的一些工作。 相关工作研究了如何以黑盒 [52] 和特定于协议的 [64] 方式最小化此类数据库更新所产生的计算和通信量。 我们在附录 C.3 和 E.3 中讨论了如何处理我们的方案中的更新。

A PIR-with-preprocessing scheme [12], over plaintext
space D and database size 𝑁 ∈ Z, consists of four routines,
which all take the security parameter as an implicit input:

一个带有预处理的 PIR 方案 [12]，在明文空间 D 和数据库大小 𝑁 ∈ Z 上，由四个例程组成，它们都将安全参数作为隐式输入：


Setup(db) → (hint𝑠, hint𝑐). Given a database db ∈ D𝑁 ,
output preprocessed hints for the server and the client.

Query(𝑖) → (st, qu). Given an index 𝑖 ∈ [𝑁], output a secret
client state st and a database query qu.

Answer(db, hint𝑠, qu) → ans. Given the database db, a server
hint hint𝑠, and a client query qu, output an answer ans.

Recover(st, hint𝑐, ans) → 𝑑. Given a secret client state st, a
client hint hint𝑐, and a query answer ans, output a database
record 𝑑 ∈ D.

Correctness. When the client and the server execute the
PIR protocol faithfully, the client should recover its desired database record with all but negligible probability in the
implicit correctness parameter. Formally, we say that a PIR
scheme has correctness error 𝛿 if, on database size 𝑁 ∈ N,
for all databases db = (𝑑1, . . . , 𝑑𝑁 ) ∈ D𝑁 and for all indices
𝑖 ∈ [𝑁], the following probability is at least 1 − 𝛿:

正确性。 当客户端和服务器忠实地执行 PIR 协议时，客户端应该在隐式正确性参数中以几乎可以忽略不计的概率恢复其所需的数据库记录。 正式地说，如果在数据库大小 𝑁 ∈ N 上，对于所有数据库 db = (𝑑1, ..., 𝑑𝑁 ) ∈ D𝑁 和所有索引 𝑖 ∈ [𝑁]，则 PIR 方案具有正确性错误 𝛿，以下概率 至少为 1 - 𝛿：
<!-- 
$$
\operatorname{Pr}\left[\begin{array}{rl}
\left(\text { hint }_s, \text { hint }_c\right) & \leftarrow \operatorname{Setup}(\mathrm{db}) \\
(\mathrm{st}, \mathrm{qu}) & \leftarrow \operatorname{Query}(i) \\
\text { ans } & \leftarrow \operatorname{Answer}\left(\mathrm{db}, \text { hint }_s, \mathrm{qu}\right) \\
\ d_i = \hat{d}_i: \quad & \leftarrow \operatorname{Recover}\left(\text { st, }_i \text { hint }_c, \text { ans }\right)
\end{array}\right]
$$


$$
d_i=\hat{d}_i
$$ -->
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917112812.png)

For the PIR scheme to be non-trivial, the total client-to-server
communication should be smaller than the bitlength of the
database. That is, it must hold that |hint𝑐 | + |qu| + |ans| ≪ |db|.

为了使 PIR 方案不平凡，总的客户端到服务器通信应该小于数据库的位长。 也就是说，它必须持有 |hint𝑐 | + |曲| + |答案| ≪ |分贝|。


Security. The client’s query should reveal no information
about its desired database record. That is, we say that a PIR
scheme is (𝑇, 𝜖)-secure if, for all adversaries A running in
time at most 𝑇, on database size 𝑁 ∈ N, and for all 𝑖, 𝑗 ∈ [𝑁],


安全。 客户端的查询不应显示有关其所需数据库记录的任何信息。 也就是说，我们说 PIR 方案是 (𝑇, 𝜖)-安全的，如果，对于所有在时间上运行的攻击者 A 最多为 𝑇，数据库大小为 𝑁 ∈ N，并且对于所有 𝑖，𝑗 ∈ [𝑁]，

$$
\begin{aligned}
&\mid \operatorname{Pr}\left[\mathcal{A}\left(1^N, \mathrm{qu}\right)=1:(\text { st, qu }) \leftarrow \operatorname{Query}(i)\right] \\
&-\operatorname{Pr}\left[\mathcal{A}\left(1^N, \mathrm{qu}\right)=1:(\text { st }, \mathrm{qu}) \leftarrow \operatorname{Query}(j)\right] \mid \leq \epsilon .
\end{aligned}
$$

Remark 3.2 (Stateless client). The client in our PIR schemes
does not hold any secret state across queries. In contrast, in
SealPIR [9] and related schemes, the client builds its queries
using persistent, long-term cryptographic secrets. We show in
Appendix B that, in certain settings, a malicious PIR server
can perform a state-recovery attack against these schemes and
thus break client privacy for both past and future queries. Our
stateless schemes are not vulnerable to such attacks.

备注 3.2（无状态客户端）。 我们的 PIR 方案中的客户端在查询中不持有任何秘密状态。 相比之下，在 SealPIR [9] 和相关方案中，客户端使用持久的长期加密秘密构建其查询。 我们在附录 B 中表明，在某些情况下，恶意 PIR 服务器可以对这些方案执行状态恢复攻击，从而破坏过去和未来查询的客户端隐私。 我们的无国籍方案不易受到此类攻击。


## 4 SimplePIR

In this section, we present our first PIR scheme, SimplePIR.
SimplePIR is the fastest PIR scheme known to date in terms of
throughput per second per core—even when compared against
two-server PIR schemes (Table 1). In particular, we prove the
following theorem:

在本节中，我们将介绍我们的第一个 PIR 方案 SimplePIR。 就每个内核每秒的吞吐量而言，SimplePIR 是迄今为止已知最快的 PIR 方案，即使与双服务器 PIR 方案相比也是如此（表 1）。 特别地，我们证明了以下定理：


Informal Theorem 4.1. On database size 𝑁, let 𝑝 ∈ N be a
suitable plaintext modulus for secret-key Regev encryption with
LWE parameters (𝑛, 𝑞, 𝜒), achieving (𝑇, 𝜖)-security for √
𝑁
LWE samples and supporting √
𝑁 homomorphic additions with
correctness error 𝛿 (cf. Section 4.2). Then, for a random LWE
matrix A ∈ Z
√
𝑁×𝑛
𝑞 , SimplePIR is a (𝑇 − 𝑂(
√
𝑁), 2𝜖)-secure
PIR scheme on database size 𝑁, over plaintext space Z𝑝, with
correctness error 𝛿.


非正式定理 4.1。 在数据库大小 𝑁 上，让 𝑝 ∈ N 是一个合适的明文模数，用于使用 LWE 参数 (𝑛, 𝑞, 𝜒) 的密钥 Regev 加密，实现 √ 𝑁 LWE 样本的 (𝑇, 𝜖)-安全性并支持 √ 𝑁 同态加法 有正确性错误𝛿（参见第 4.2 节）。 然后，对于随机 LWE 矩阵 A ∈ Z √ 𝑁×𝑛 𝑞 ，SimplePIR 是一个 (𝑇 − 𝑂( √ 𝑁), 2𝜖)-安全 PIR 方案，数据库大小为 𝑁，在明文空间 Z𝑝 上，正确性错误为 𝛿。

We give a formal description of SimplePIR in Figure 2 and
we prove its security and correctness in Appendix C.

我们在图 2 中给出了 SimplePIR 的正式描述，并在附录 C 中证明了它的安全性和正确性。

Remark 4.1 (Concrete costs of SimplePIR). Using the parameters of Informal Theorem 4.1, we give SimplePIR’s concrete
costs, with no hidden constants, in terms of operations (i.e.,
integer additions and multiplications) over Z𝑞. In a one-time
public preprocessing phase, SimplePIR requires:

备注 4.1（SimplePIR 的具体成本）。 使用非正式定理 4.1 的参数，我们在 Z𝑞 上的操作（即整数加法和乘法）方面给出了 SimplePIR 的具体成本，没有隐藏常数。 在一次性公共预处理阶段，SimplePIR 需要：


Construction: SimplePIR. The parameters of the construction are a database size 𝑁, LWE parameters (𝑛, 𝑞, 𝜒),
a plaintext modulus 𝑝 ≪ 𝑞, and a LWE matrix A ∈ Z
√
𝑁×𝑛
𝑞
(sampled in practice using a hash function). The database
consists of 𝑁 values in Z𝑝, which we represent as a matrix
in Z
√
𝑁×
√
𝑁
𝑝 . Define the scalar Δ := ⌊𝑞/𝑝⌋ ∈ Z.

构造：SimplePIR。 构造的参数是数据库大小𝑁、LWE 参数（𝑛、𝑞、𝜒）、明文模数𝑝 ≪ 𝑞 和 LWE 矩阵 A ∈ Z √ 𝑁×𝑛 𝑞（在实践中使用哈希函数采样）。 数据库由 Z𝑝 中的 𝑁 值组成，我们将其表示为 Z √ 𝑁× √ 𝑁 𝑝 中的矩阵。 定义标量 Δ := ⌊𝑞/𝑝⌋ ∈ Z。


Setup(db ∈ Z √ 𝑁× √ 𝑁 𝑝 ) → (hint𝑠, hint𝑐). • Return (hint𝑠, hint𝑐) ← (⊥, db · A ∈ Z √ 𝑁×𝑛 𝑞 ). Query(𝑖 ∈ [𝑁]) → (st, qu). • Write 𝑖 as a pair (𝑖row, 𝑖col) ∈ [√ 𝑁] 2 . • Sample s ←R Z 𝑛 𝑞 and e ←R 𝜒 √ 𝑁 . • Compute qu ← (As + e + Δ · u𝑖col) ∈ Z √ 𝑁 𝑞 , where u𝑖col is the vector of all zeros with a single ‘1’ at index 𝑖col. • Return (st, qu) ← ( (𝑖row,s), qu). Answer(db ∈ Z √ 𝑁× √ 𝑁 𝑝 , hint𝑠, qu ∈ Z √ 𝑁 𝑞 ) → ans. • Return ans ← db · qu ∈ Z √ 𝑁 𝑞 . Recover(st, hint𝑐 ∈ Z √ 𝑁×𝑛 𝑞 , ans ∈ Z √ 𝑁 𝑞 ) → 𝑑. • Parse (𝑖row ∈ [√ 𝑁],s ∈ Z 𝑛 𝑞 ) ← st. • Compute 𝑑ˆ ← (ans[𝑖row] − hint𝑐 [𝑖row, :] · s) ∈ Z𝑞, where ans[𝑖row] denotes component 𝑖row of ans and hint𝑐 [𝑖row, :] denotes row 𝑖row of hint𝑐. • Return 𝑑 ← RoundΔ(𝑑ˆ)/Δ ∈ Z𝑝, which is 𝑑ˆrounded to the nearest multiple of Δ and then divided by Δ.

设置（db ∈ Z √ 𝑁× √ 𝑁 𝑝 ）→（提示𝑠，提示𝑐）。 • 返回 (hint𝑠, hint𝑐) ← (⊥, db · A ∈ Z √ 𝑁×𝑛 𝑞 )。查询(𝑖 ∈ [𝑁]) → (st, qu)。 • 将𝑖写成一对(𝑖row, 𝑖col) ∈ [√ 𝑁] 2 。 • 样本 s ←R Z 𝑛 𝑞 和 e ←R 𝜒 √ 𝑁 。 • 计算 qu ← (As + e + Δ · u𝑖col) ∈ Z √ 𝑁 𝑞 ，其中 u𝑖col 是在索引 𝑖col 处带有单个“1”的全零向量。 • 返回 (st, qu) ← ( (𝑖row,s), qu)。答案(db ∈ Z √ 𝑁× √ 𝑁 𝑝 , hint𝑠, qu ∈ Z √ 𝑁 𝑞 ) → ans. • 返回ans ← db · qu ∈ Z √ 𝑁 𝑞。恢复(st, hint𝑐 ∈ Z √ 𝑁×𝑛 𝑞 , ans ∈ Z √ 𝑁 𝑞 ) → 𝑑。 • Parse (𝑖row ∈ [√ 𝑁],s ∈ Z 𝑛 𝑞 ) ← st。 • 计算𝑑^ ← (ans[𝑖row] − hint𝑐 [𝑖row, :] · s) ∈ Z𝑞，其中 ans[𝑖row] 表示组件 𝑖 ans 行，hint𝑐 [𝑖row, :] 表示行𝑖行提示𝑐。 • 返回𝑑 ← RoundΔ(𝑑^)/Δ ∈ Z𝑝，它是 𝑑^四舍五入到最接近的 Δ 倍数，然后除以 Δ。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917113409.png)

Figure 2: The SimplePIR protocol.

• the server to perform 2𝑛𝑁 operations in Z𝑞, and
• the client to download 𝑛
√
𝑁 elements in Z𝑞,
where our implementation takes 𝑛 = 2
10 and 𝑞 = 2
32 to achieve
128-bit security against the best known attacks [7].

- 服务器在 Z𝑞 中执行 2𝑛𝑁 操作，以及 
- 客户端在 Z𝑞 中下载 𝑛 √ 𝑁 元素，我们的实现需要 𝑛 = 2 10 和 𝑞 = 2 32 来实现 128 位安全性以抵御最知名的攻击 [7] .

On each query, SimplePIR requires
• the client to upload √
𝑁 elements in Z𝑞,
• the server to perform 2𝑁 operations in Z𝑞, and
• the client to download √
𝑁 elements in Z𝑞.

在每个查询中，SimplePIR 要求 • 客户端上传 Z𝑞 中的 √ 𝑁 元素， • 服务器在 Z𝑞 中执行 2𝑁 操作，以及 • 客户端下载 Z𝑞 中的 √ 𝑁 元素。


### 4.1 技术思路

We now discuss the SimplePIR construction in more detail.
The simplest non-trivial single-server PIR schemes [22,
53, 60] take the following “square-root” approach: given an
𝑁-element database, the server stores this database as a √
𝑁-by-
√
𝑁 square matrix. Meanwhile, a client who wishes to query
for database entry 𝑖 ∈ [𝑁] decomposes index 𝑖 into the pair
of coordinates (𝑖row, 𝑖col) ∈ [√
𝑁]
2
. Then, the client builds a
unit vector u𝑖col in Z
√
𝑁
2
(i.e., the vector of all zeros with a
single ‘1’ at index 𝑖col), element-wise encrypts it with a linearly homomorphic encryption scheme, and sends this encrypted
vector to the server. The server computes the matrix-vector
product between the database and the query vector and returns
it to the client. Finally, the client decrypts element 𝑖row of the
server’s answer vector—which corresponds exactly to the inner
product of database row 𝑖row and encrypted unit vector u𝑖col , or,
equivalently, the encrypted database entry at (𝑖row, 𝑖col). In this
scheme, the server and the client exchange 2
√
𝑁 ciphertext elements, while the server performs 𝑁 ciphertext multiplications
and additions to answer each PIR query

我们现在更详细地讨论 SimplePIR 构造。最简单的非平凡单服务器 PIR 方案 [22, 53, 60] 采用以下“平方根”方法：给定一个 𝑁 元素数据库，服务器将此数据库存储为 √ 𝑁-by- √ 𝑁 方阵.同时，希望查询数据库条目𝑖 ∈ [𝑁] 的客户端将索引 𝑖 分解为坐标对 (𝑖row, 𝑖col) ∈ [√ 𝑁] 2 。然后，客户端在 Z √ 𝑁 2 中构建一个单位向量 u𝑖col（即，在索引 𝑖col 处具有单个“1”的全零向量），使用线性同态加密方案逐元素对其进行加密，并发送此加密向量到服务器。服务器计算数据库和查询向量之间的矩阵向量乘积并将其返回给客户端。最后，客户端解密服务器答案向量的元素 𝑖row——它正好对应于数据库行 𝑖row 和加密单位向量 u𝑖col 的内积，或者等效地，在 (𝑖row, 𝑖col) 处的加密数据库条目。在该方案中，服务器和客户端交换 2 √ 𝑁 密文元素，而服务器执行 𝑁 密文乘法和加法来回答每个 PIR 查询

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917113750.png)

Figure 3: The server computation in SimplePIR. Each cell represents a
Z𝑞 element, and × denotes matrix multiplication. The server performs
the bulk of its work—namely, multiplying the database db and the
matrix A—in a one-time preprocessing phase. Thereafter, the server
can answer each client’s query with a lightweight online phase.

图 3：SimplePIR 中的服务器计算。 每个单元格代表一个 Z𝑞 元素，× 表示矩阵乘法。 服务器在一次性预处理阶段执行其大部分工作——即，将数据库 db 和矩阵 A 相乘。 此后，服务器可以通过轻量级在线阶段回答每个客户端的查询。


Our starting point is to instantiate this “square-root” approach with the secret-key version of Regev’s LWE-based
encryption scheme [76]. Let (𝑛, 𝑞, 𝜒) be LWE parameters.
Then, the Regev encryption of a vector 𝝁 ∈ Z
𝐿
𝑝
consists of a
pair of a matrix and a vector:

我们的出发点是使用 Regev 的基于 LWE 的加密方案 [76] 的密钥版本来实例化这种“平方根”方法。 让 (𝑛, 𝑞, 𝜒) 为 LWE 参数。 然后，向量 𝝁 ∈ Z 𝐿 𝑝 的 Regev 加密由一对矩阵和一个向量组成：

$$
\operatorname{Enc}(\boldsymbol{\mu})=(\mathbf{A}, \mathbf{c})=(\mathbf{A}, \mathbf{A s}+\mathbf{e}+\lfloor q / p\rfloor \cdot \boldsymbol{\mu})
$$

for some LWE matrix A ←R
Z
𝐿×𝑛
𝑞
, secret s ←R
Z
𝑛
𝑞
, and error
vector e ←R
𝜒
𝐿

对于一些 LWE 矩阵 A ←R Z 𝐿×𝑛 𝑞 ，秘密 s ←R Z 𝑛 𝑞 和误差向量 e ←R 𝜒 𝐿


我们对 Regev 加密做了三个重要的观察：


1. First, a large part of the ciphertext—namely, the matrix
A—is independent of the encrypted message. It is thus
possible to generate the matrix A ahead of time.
2. Second, Regev encryption remains secure even when many
different parties use the same matrix A to encrypt many
messages [73], provided that each ciphertext uses an independent secret vector s and error vector e.
3. Finally, we can take A to be pseudorandom (rather than
random) at a negligible loss in security, allowing us to
succinctly represent A by a short random seed.

首先，密文的很大一部分——即矩阵 A——与加密信息无关。 因此可以提前生成矩阵A。
其次，即使许多不同方使用相同的矩阵 A 来加密许多消息 [73]，Regev 加密仍然是安全的，前提是每个密文使用独立的秘密向量 s 和错误向量 e。
最后，我们可以在安全性损失可以忽略不计的情况下将 A 视为伪随机（而不是随机），从而允许我们用一个短随机种子简洁地表示 A。


In SimplePIR, we leverage these three observations as follows. Consider a client who wishes to retrieve the database
entry at (𝑖row, 𝑖col). At a conceptual level, the client’s query to
the server consists of Enc(u𝑖col) = (A, c)—the Regev encryption of the vector in Z
√
𝑁
𝑝 that is zero everywhere but with a
“1” at index 𝑖col. The server then represents the database as a
matrix D ∈ Z
√
𝑁×
√
𝑁
𝑝 and computes and returns the matrixvector product of the database with the client’s encrypted query,
i.e., (D · A, D · c). From the server’s reply, the client can use
standard Regev decryption to recover D · u𝑖col ∈ Z
√
𝑁
𝑝 , which is exactly the 𝑖col-th column of the database, as desired.

在 SimplePIR 中，我们利用这三个观察结果如下。 考虑一个希望在 (𝑖row, 𝑖col) 处检索数据库条目的客户。 在概念层面上，客户端对服务器的查询由 Enc(u𝑖col) = (A, c) 组成——Z √ 𝑁 𝑝 中向量的 Regev 加密，它处处为零，但在索引 𝑖col 处为“1”。 然后，服务器将数据库表示为矩阵 D ∈ Z √ 𝑁× √ 𝑁 𝑝，并计算并返回数据库与客户端加密查询的矩阵向量积，即 (D·A，D·c)。 根据服务器的回复，客户端可以根据需要使用标准 Regev 解密来恢复 D·u𝑖col ∈ Z √ 𝑁 𝑝，这正是数据库的第 𝑖col 列。

现在，我们进行以下修改：


1. We have the server compute the value D · A ahead of time
in a preprocessing phase. This preprocessing step requires
2𝑛𝑁 operations in Z𝑞, where 𝑛 ≈ 2
10 is the dimension of
the LWE secret and 𝑁 is the database size. Then, to answer
the client’s query, the server needs to compute the value
D · c, which requires only 2𝑁 operations in Z𝑞. So, an
𝑛/(𝑛 + 1) fraction (i.e., 99.9%) of the server’s work can
happen ahead of time—before the client even decides which
database record it wants to fetch.

我们让服务器在预处理阶段提前计算值 D·A。 此预处理步骤需要 Z𝑞 中的 2𝑛𝑁 操作，其中 𝑛 ≈ 2 10 是 LWE 秘密的维度，𝑁 是数据库大小。 然后，为了回答客户端的查询，服务器需要计算值 D·c，这只需要 Z𝑞 中的 2𝑁 操作。 因此，服务器工作的 𝑛/(𝑛 + 1) 部分（即 99.9%）可以提前发生——甚至在客户端决定要获取哪个数据库记录之前。

2. We have all clients use the same matrix A to build their
queries. Then, the server must only precompute D · A once.
The server sends this one-time “hint” to all clients. Thus, the
server amortizes the cost of computing and communicating
D · A over many clients and over many queries.

我们让所有客户使用相同的矩阵 A 来构建他们的查询。 然后，服务器必须只预计算一次 D·A。 服务器将这一一次性“提示”发送给所有客户端。 因此，服务器将计算和通信 D·A 的成本分摊给许多客户端和许多查询。

3. As an optimization, we compress A using pseudorandomness. In an initialization step before the protocol starts, the
server and the clients agree on a public random seed used to
generate A. This saves bandwidth and storage, as the server
and the clients communicate and store only a small seed.

作为优化，我们使用伪随机性压缩 A。 在协议启动之前的初始化步骤中，服务器和客户端就用于生成 A 的公共随机种子达成一致。这节省了带宽和存储，因为服务器和客户端通信并仅存储一个小种子。

The security of the SimplePIR construction follows almost
immediately from the security of Regev encryption [76] with a
reused matrix A [73], which in turn follows from the hardness
of LWE. SimplePIR’s correctness follows from the correctness
of Regev’s linearly homomorphic encryption scheme and of
Kushilevitz and Ostrovsky’s “square-root” PIR template.

SimplePIR 结构的安全性几乎紧跟 Regev 加密 [76] 的安全性和重用矩阵 A [73]，而后者又遵循 LWE 的硬度。 SimplePIR 的正确性源于 Regev 的线性同态加密方案以及 Kushilevitz 和 Ostrovsky 的“平方根”PIR 模板的正确性。


### 4.2 参数选择

Picking the LWE parameters (𝑛, 𝑞, 𝜒) and the plaintext
modulus 𝑝 requires a standard (though tedious) analysis. We
choose our parameters to have 128-bit security, according
to modern lattice-attack-cost estimates [7]. In particular, we
set the secret dimension 𝑛 = 2
10, use modulus 𝑞 = 2
32 (as
modern hardware natively supports operations with this
modulus), set the error distribution 𝜒 to be the discrete
Gaussian distribution with standard deviation 𝜎 = 6.4, and
allow correctness error 𝛿 = 2
−40. We obtain the following
trade-off between database size 𝑁 and plaintext modulus 𝑝:

选择 LWE 参数（𝑛、𝑞、𝜒）和明文模数 𝑝 需要进行标准（尽管乏味）分析。 根据现代格子攻击成本估计[7]，我们选择我们的参数具有 128 位安全性。 特别是，我们设置秘密维度𝑛 = 2 10，使用模数𝑞 = 2 32（因为现代硬件本身支持使用此模数的操作），将误差分布𝜒设置为具有标准偏差𝜎 = 6.4的离散高斯分布，以及 允许正确性错误𝛿 = 2 −40。 我们在数据库大小𝑁和明文模数𝑝之间进行了以下权衡：

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917114446.png)

我们将在附录 C.1 中进一步讨论参数选择。

### 4.3 扩展

最后，我们扩展了我们的 SimplePIR 构造以满足实际部署场景的要求：

Supporting databases with larger record sizes. The basic
SimplePIR scheme (Figure 2) supports a database in which
each record is a single Z𝑝 element—or, roughly 8-10 bits with
our parameter settings. Our main application (Section 7) uses
a database with one-bit records, though other applications of
PIR [6, 9, 10, 48, 66] use much longer records.

支持具有更大记录大小的数据库。 基本的 SimplePIR 方案（图 2）支持数据库，其中每条记录都是单个 Z𝑝 元素，或者，使用我们的参数设置大约为 8-10 位。 我们的主要应用程序（第 7 节）使用具有一位记录的数据库，尽管 PIR [6, 9, 10, 48, 66] 的其他应用程序使用更长的记录。

To handle large records, we observe that the client in SimplePIR can retrieve an entire column of the database at once.
Concretely, after executing a single online phase with the server
to query for database element (𝑖row, 𝑖col), the client can run the
Recover procedure √
𝑁 times—once for every row in [
√
𝑁]—
to reconstruct the entire database column 𝑖col. Thus, to support
large records, we encode each record as multiple elements
in the plaintext space, Z𝑝, and store these elements stacked
vertically in the same database column. By making a single
online query and reconstructing the corresponding column of
elements, the client recovers any record of its choosing.

为了处理大型记录，我们观察到 SimplePIR 中的客户端可以一次检索数据库的整个列。 具体来说，在与服务器执行单个在线阶段以查询数据库元素（𝑖row，𝑖col）后，客户端可以运行Recover过程√𝑁次——[√𝑁]中的每一行一次——以重建整个数据库列𝑖col . 因此，为了支持大记录，我们将每个记录编码为明文空间 Z𝑝 中的多个元素，并将这些元素垂直堆叠在同一个数据库列中。 通过进行单个在线查询并重建相应的元素列，客户端可以恢复其选择的任何记录。

On a database of 𝑁 records, each in Z
𝑑
𝑝
(where 𝑑 ≤ 𝑁),
with LWE secret dimension 𝑛 and modulus 𝑞, SimplePIR has:

在 𝑁 记录的数据库中，每个 Z 𝑑 𝑝（其中 𝑑 ≤ 𝑁），具有 LWE 秘密维度 𝑛 和模数 𝑞，SimplePIR 具有：

one-time (hint) download 𝑛 ·
√
𝑑𝑁 elements in Z𝑞,
• per-query upload and download √
𝑑𝑁 elements in Z𝑞, and
• per-query server computation 2𝑑𝑁 operations in Z𝑞.

- 一次性（提示）下载 Z𝑞 中的 𝑛 · √ 𝑑𝑁 元素， 
- 每次查询上传和下载 Z𝑞 中的 √ 𝑑𝑁 元素，以及 
- 每次查询服务器计算 2𝑑𝑁 Z𝑞 中的操作。

Fetching many database records at once (“Batch PIR”).
In many applications [9, 10], a client wants to fetch 𝑘 records
from the PIR server at once. If the client runs our PIR protocol
𝑘 times on a database of 𝑁 records, the total server time would
be roughly 𝑘𝑁. We can apply the “batch PIR” techniques of
Ishai et al. [50] to allow a client to fetch 𝑘 records at server-side
cost ≪ 𝑘𝑁, without increasing the hint size.

一次获取许多数据库记录（“Batch PIR”）。 在许多应用程序 [9, 10] 中，客户端希望立即从 PIR 服务器获取 𝑘 记录。 如果客户端在 𝑁 记录的数据库上运行我们的 PIR 协议 𝑘 次，则服务器总时间大约是 𝑘𝑁。 我们可以应用 Ishai 等人的“批量 PIR”技术。 [50] 允许客户端以服务器端成本获取 𝑘 记录 ≪ 𝑘𝑁，而无需增加提示大小。


The idea is to randomly partition the database of 𝑁 records
into 𝑘 chunks, each represented as a matrix of dimension
(
√
𝑁/𝑘)-by-√
𝑁. To fetch a set of 𝑘 database records that fall
into distinct chunks, the client and server then run SimplePIR
once on each database chunk. The hint size (i.e., offline client
download) remains 𝑛
√
𝑁—as in one-query SimplePIR. The
communication cost for the client is 𝑘 ·
√
𝑁—𝑘 times larger than
in one-query SimplePIR (and identical to the communication
if the client fetched all 𝑘 records sequentially). The server
performs 𝑁 operations in Z𝑞, as in one-query SimplePIR.

这个想法是将𝑁记录的数据库随机划分为𝑘块，每个块都表示为一个维度矩阵（√𝑁/𝑘）×√𝑁。 为了获取一组属于不同块的𝑘数据库记录，客户端和服务器然后在每个数据库块上运行一次 SimplePIR。 提示大小（即离线客户端下载）保持为 𝑛 √ 𝑁 — 与单查询 SimplePIR 中一样。 客户端的通信成本是𝑘 · √ 𝑁—𝑘 倍于单次查询 SimplePIR（如果客户端按顺序获取所有 𝑘 记录，则与通信相同）。 服务器在 Z𝑞 中执行 𝑁 操作，就像在单查询 SimplePIR 中一样。

However, more than one of the client’s desired records may
fall into the same chunk. There are two ways to handle this:

但是，多个客户所需的记录可能会落入同一个块中。 有两种方法可以处理这个问题：


• If the client must recover all 𝑘 records with overwhelming
probability, the client can run the entire batch-PIR protocol
𝜆 times to achieve failure probability 2
−Ω(𝜆)
[9, 50].
• If the client needs only to recover a constant fraction of
the 𝑘 database records, then the client and server run this
protocol only once. The server-side computation cost is as
in one-query SimplePIR.

- 如果客户端必须以压倒性的概率恢复所有 𝑘 记录，则客户端可以运行整个批处理 PIR 协议 𝜆 次以实现失败概率 2 −Ω(𝜆) [9, 50]。 
- 如果客户端只需要恢复一定比例的𝑘 数据库记录，那么客户端和服务器只运行一次此协议。 服务器端计算成本与单查询 SimplePIR 相同。

Additional improvements. We discuss how to further improve
the asymptotic efficiency of SimplePIR in Appendix C.3.

额外的改进。 我们在附录 C.3 中讨论了如何进一步提高 SimplePIR 的渐近效率。


### 4.4 快速线性同态加密Fast linearly homomorphic encryption

In Appendix D, we introduce the notion of linearly homomorphic encryption with preprocessing. This new primitive
abstracts out the key properties of Regev encryption that we
use in SimplePIR. We expect this new form of linearly homomorphic encryption to have further practical applications.

在附录 D 中，我们介绍了带有预处理的线性同态加密的概念。 这个新的原语抽象出了我们在 SimplePIR 中使用的 Regev 加密的关键属性。 我们期望这种新形式的线性同态加密有进一步的实际应用。


## 5 DoublePIR


## 6 Data structure for private approximate set membership6 私有近似集成员的数据结构

In this section, we introduce a new data structure for the
approximate private set membership problem. In this problem,
a client holds a private string 𝜎, a server holds a set of strings 𝑆,
and the client wants to test whether 𝜎 is included in 𝑆 without
revealing 𝜎 to the server. To rule out the trivial solution where
the server sends 𝑆 to the client, we insist on communication
sublinear in |𝑆|. This problem differs from that of private set
intersection [41] in two key ways: the server’s set 𝑆 does not
need to be kept private, and the client’s output bit 𝑏 (indicating
whether 𝜎 ∈ 𝑆) may be approximate, i.e., there is some chance
that it will be incorrect. Looking ahead, our data structure will
be at the core of our new scheme for auditing in Certificate
Transparency (Section 7).

在本节中，我们为近似私有集成员问题介绍了一种新的数据结构。 在这个问题中，客户端持有私有字符串𝜎，服务器持有一组字符串𝑆，并且客户端想要测试𝜎是否包含在𝑆中，而不向服务器透露𝜎。 为了排除服务器将𝑆发送给客户端的简单解决方案，我们坚持|𝑆|中的次线性通信。 这个问题与私有集合交集[41]的问题在两个关键方面不同：服务器的集合𝑆不需要保持私有，客户端的输出位𝑏（指示是否𝜎 ∈𝑆）可能是近似的，即有 它有可能是不正确的。 展望未来，我们的数据结构将成为我们新的证书透明度审计计划的核心（第 7 节）。

At a very high level, we have the server preprocess its set
of strings 𝑆 into a data structure. Then, the client, holding a
private string 𝜎, can test whether 𝜎 ∈ 𝑆 by privately reading
only a few bits of the server’s data structure. The client fetches
these bits from the server using PIR. Thus, the relevant cost
metrics in this setting (Table 4) are:


在非常高的层次上，我们让服务器将其字符串集𝑆预处理成数据结构。 然后，持有私有字符串 𝜎 的客户端可以通过仅读取服务器数据结构的少数位来测试是否 𝜎 ∈ 𝑆。 客户端使用 PIR 从服务器获取这些位。 因此，此设置中的相关成本指标（表 4）是：

• Number of probes. How many bits/symbols of the server’s
data structure must the client read?
• PIR database size. What is the size of the data structure
from which the client performs its private PIR read?
• Soundness error (i.e., false-positive rate). For an adversarially chosen set 𝑆 and string 𝜎, what is the probability, only
over the choice of the client’s randomness, that the client
outputs “𝜎 ∈ 𝑆” when 𝜎 ∉ 𝑆.

- 探头数量。 客户端必须读取服务器数据结构的多少位/符号？ 
- PIR 数据库大小。 客户端执行其私有 PIR 读取的数据结构的大小是多少？ 
- 稳健性错误（即误报率）。 对于对抗性选择的集合 𝑆 和字符串 𝜎，当 𝜎 ∉ 𝑆 时，客户端输出“𝜎 ∈ 𝑆”的概率是多少，仅取决于客户端随机性的选择。


Remark 6.1 (False positives versus false negatives). Some, but
not all, applications can tolerate false positives. For example,password-breach database lookups [57,72,80] could potentially
tolerate false-positive rates as large as 2
−30; in contrast, Safe
Browsing blocklist lookups [47, 52] demand a cryptographically negligible false-positive rate, as false positives would
cause a legitimate website to be flagged as malicious. For the
latter case, other data structures may be more appropriate.

备注 6.1（假阳性与假阴性）。 一些（但不是全部）应用程序可以容忍误报。 例如，密码泄露数据库查找 [57,72,80] 可能会容忍高达 2 −30 的误报率； 相比之下，安全浏览阻止列表查找 [47, 52] 需要加密可忽略的误报率，因为误报会导致合法网站被标记为恶意网站。 对于后一种情况，其他数据结构可能更合适。


### 6.1 Related approaches

Standard Bloom filters [14] are an excellent solution to this
problem, except for a major wrinkle: the soundness guarantee
they provide is too weak for our purposes. In particular, the
soundness guarantee of standard Bloom filters only holds
when the string 𝜎 being tested is chosen independently of
the randomness used to construct the filter (i.e., the hash
function). In our adversarial setting (Section 7), the data
structure (including its hash function(s)) is public, and an
adversary could choose the string 𝜎 in a way that depends on
the hash function(s) used to construct the Bloom filter. Standard
Bloom filters do not provide good soundness in this setting,
unless the number of probes is relatively large (Table 4).

标准的布隆过滤器 [14] 是解决这个问题的一个很好的解决方案，除了一个主要的问题：它们提供的健全性保证对于我们的目的来说太弱了。 特别是，标准 Bloom 过滤器的稳健性保证仅在被测试的字符串 𝜎 的选择独立于用于构造过滤器的随机性（即散列函数）时才成立。 在我们的对抗设置（第 7 节）中，数据结构（包括其散列函数）是公开的，并且攻击者可以选择字符串 𝜎，其方式取决于用于构造 Bloom 过滤器的散列函数 . 标准布隆过滤器在此设置中不能提供良好的可靠性，除非探测器的数量相对较大（表 4）。

To address this issue, prior work has constructed adversarial
Bloom filters [27, 68]. While these data structures provide
adversarial soundness as we require, they do not provide
privacy. In particular, they require the client to send its string
𝜎 to the server; the server then performs a private computation
on 𝜎 to determine the probes.

为了解决这个问题，之前的工作已经构建了对抗性布隆过滤器 [27, 68]。 虽然这些数据结构提供了我们需要的对抗性稳健性，但它们不提供隐私。 特别是，它们要求客户端将其字符串 𝜎 发送到服务器； 然后，服务器对 𝜎 执行私有计算以确定探测。

In contrast, we show how the server can build a data structure such that the client can perform an approximate setmembership test by reading a single bit from a roughly 𝜆-fold
smaller database than would be required by Bloom filters or
other standard techniques (on security parameter 𝜆 ≈ 128). If
the client reads this bit using PIR, this approach is private. The
downside is that our data structure’s soundness-error rate is
constant (i.e., 1/2) instead of negligible in 𝜆. However, since
our application (Section 7) can naturally tolerate constant
soundness error, this trade-off is profitable.

相比之下，我们展示了服务器如何构建数据结构，以便客户端可以通过从比布隆过滤器或其他标准技术（安全 参数𝜆≈128）。 如果客户端使用 PIR 读取此位，则此方法是私有的。 缺点是我们的数据结构的健全性错误率是恒定的（即 1/2），而不是在 𝜆 中可以忽略不计。 然而，由于我们的应用程序（第 7 节）可以自然地容忍恒定的稳健性误差，因此这种权衡是有利可图的。


### 6.2 Syntax and properties语法和属性

We define the syntax and security properties for our approximate private set membership data structure. The data structure
is parameterized by a universe U ⊆ {0, 1}
∗ of possible strings
and consists of two routines:

我们为我们的近似私有集成员数据结构定义语法和安全属性。 数据结构由可能字符串的全域 U ⊆ {0, 1} ∗ 参数化，并由两个例程组成：

Setup(𝑆) → 𝐷. Take as input a set of strings 𝑆 ⊆ U and
output a data structure 𝐷 ∈ {0, 1}
𝑎×𝑏
, where 𝑎, 𝑏 ∈ N can
depend on |𝑆|. This algorithm is deterministic.
Query(𝜎) → (𝑖, 𝑗). Given a candidate string 𝜎 ∈ U, output an
index (𝑖, 𝑗) ∈ [𝑎] × [𝑏] in the data structure 𝐷 to probe. If
𝜎 ∈ 𝑆, it holds that 𝐷𝑖, 𝑗 = 1. This algorithm is randomized.

设置（𝑆）→𝐷。 将一组字符串 𝑆 ⊆ U 作为输入，并输出一个数据结构 𝐷 ∈ {0, 1} 𝑎×𝑏 ，其中𝑎，𝑏 ∈ N 可以依赖于 |𝑆|。 该算法是确定性的。 查询（𝜎）→（𝑖，𝑗）。 给定一个候选字符串𝜎 ∈ U，在数据结构 𝐷 中输出一个索引 (𝑖, 𝑗) ∈ [𝑎] × [𝑏] 进行探测。 如果𝜎 ∈ 𝑆，它认为𝐷𝑖，𝑗 = 1。这个算法是随机的。

Properties. For a set 𝑆 ⊆ U and string 𝜎 ∈ U, define

特性。 对于集合 𝑆 ⊆ U 和字符串 𝜎 ∈ U，定义

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917150547.png)

where the probability is taken only over the randomness of the
Query algorithm.
其中概率仅取自 Query 算法的随机性。

Completeness. An approximate membership test is complete
if, for all 𝑆 ⊆ U and 𝜎 ∈ 𝑆, Accept(𝑆, 𝜎) = 1.

完整性。 如果对于所有 𝑆 ⊆ U 和 𝜎 ∈ 𝑆，Accept(𝑆, 𝜎) = 1，则近似成员检验完成。

Soundness. An approximate membership test has soundness
error 𝜖 if, for all 𝑆 ⊆ U and 𝜎 ∉ 𝑆, Accept(𝑆, 𝜎) ≤ 𝜖.

健全。 如果对于所有 𝑆 ⊆ U 和 𝜎 ∉ 𝑆，Accept(𝑆, 𝜎) ≤ 𝜖，则近似成员检验具有稳健性错误 𝜖。

Privacy. An approximate membership test is private if the row
of the data structure that the Query algorithm probes reveals
no information about the query string 𝜎. In particular, we say
that privacy holds if, for all strings 𝜎0, 𝜎1 ∈ U, the following
distributions of the row indices are identical:

隐私。 如果 Query 算法探测的数据结构的行没有显示有关查询字符串 𝜎 的信息，则近似成员资格测试是私有的。 特别是，如果对于所有字符串 𝜎0, 𝜎1 ∈ U，行索引的以下分布相同，则我们说隐私成立：

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917150815.png)


In a deployment, to probe the element at row 𝑖 and column 𝑗
in the data structure, the client sends 𝑖 in the clear, along with
a PIR query for 𝑗, to the server. The server executes the PIR
scheme over the 𝑖-th row, and sends the PIR answer (and, if
needed, the client hint for that row) back to the client. Thus, by
the above privacy property, the server learns nothing about 𝜎.
Performing PIR over only a row of the data structure (rather
than the whole data structure) will be the source of our 𝜆-fold
performance improvements compared to related approaches.

在部署中，为了探测数据结构中行𝑖和列𝑗的元素，客户端以明文形式向服务器发送𝑖以及针对𝑗的 PIR 查询。 服务器在第 𝑖 行上执行 PIR 方案，并将 PIR 答案（以及，如果需要，该行的客户端提示）发送回客户端。 因此，通过上述隐私属性，服务器对𝜎一无所知。 与相关方法相比，仅对数据结构的一行（而不是整个数据结构）执行 PIR 将是我们 𝜆 倍性能改进的源泉。

### 6.3 Our approximate membership test

Our construction is parameterized by integers 𝑎, 𝑘 ∈ N and
a set size 𝑁. Our data structure then maps the set of strings
𝑆 into a matrix of 𝑎-by-(𝑘𝑁) bits. The construction uses
cryptographic hash functions 𝐻1, . . . , 𝐻𝑎 : U → [𝑘𝑁], which
we model as independent random oracles [13].

我们的构造由整数 𝑎、𝑘 ∈ N 和集合大小 𝑁 参数化。 然后，我们的数据结构将字符串集合 𝑆 映射到 𝑎-by-(𝑘𝑁) 位的矩阵中。 该结构使用加密散列函数𝐻1, 。 . . , 𝐻𝑎 : U → [𝑘𝑁]，我们将其建模为独立的随机预言机 [13]。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917151010.png)
然后我们在附录 F 中证明以下命题：

Proposition 6.2. The approximate membership-test data structure (Setup, Query), on parameters 𝑎 ≥ 2 (log(|U|) + 𝜆) and 𝑘 ≥ 8, is complete, private, and has soundness error 1/2.
The construction fails with probability 2
−𝜆
, over the choice of
the hash functions modeled as independent random oracles.
Concretely, on |U| = 2
256, taking 𝑘 = 8 and 𝑎 = 768 gives
soundness error 1/2 and failure probability 2
−128
.

提案 6.2。 近似的成员资格测试数据结构（Setup，Query），参数 𝑎 ≥ 2 (log(|U|) + 𝜆) 和 𝑘 ≥ 8，是完整的、私有的，并且有 1/2 的健全性错误。 在选择建模为独立随机预言机的哈希函数时，构造失败的概率为 2 -𝜆。 具体来说，在 |U| = 2 256，取 𝑘 = 8 和 𝑎 = 768 给出稳健性误差 1/2 和失败概率 2 -128。

## 7 Application: Auditing in Certificate Transparency 7 应用：证书透明度审计

We now explain how to use our new PIR schemes to solve the
problem of privately auditing signed certificate timestamps in
deployments of Certificate Transparency [54, 55, 65].

我们现在解释如何使用我们的新 PIR 方案来解决在证书透明度部署中私下审核签名证书时间戳的问题 [54,55,65]。

### 7.1 Problem statement
Background: Certificate Transparency
背景：证书透明度

The goal of Certificate Transparency is to store every public-key certificate that
every certificate authority issues in a set of publicly accessible
logs. To this end, certificate authorities submit the certificates
they issue to log operators, who respond with a signed certificate timestamp (SCT). The SCT is a promise to include the
new certificate in the log maintained by this operator within
some bounded period of time.

证书透明度的目标是将每个证书颁发机构颁发的每个公钥证书存储在一组可公开访问的日志中。 为此，证书颁发机构将他们颁发的证书提交给日志操作员，他们以签名证书时间戳 (SCT) 进行响应。 SCT 承诺在一定时间内将新证书包含在此运营商维护的日志中。


Later on, when a TLS server sends a public-key certificate to
a client, the server attaches a number of SCTs according to the
client’s policy (e.g., Google and Apple both require SCTs from
three distinct log operators in Chrome and Safari, respectively).
By verifying the SCTs, the client can be sure that each of the
log operators has seen the new certificate and—if the operator
is honest—will eventually log it. Domain operators can then
use the logs to detect whether or not a certificate authority has
mistakenly or maliciously issued a certificate for their domain

稍后，当 TLS 服务器向客户端发送公钥证书时，服务器会根据客户端的策略附加多个 SCT（例如，Google 和 Apple 都需要分别来自 Chrome 和 Safari 中三个不同日志操作符的 SCT） . 通过验证 SCT，客户端可以确保每个日志操作员都看到了新证书，并且 - 如果操作员是诚实的 - 最终会记录它。 然后，域运营商可以使用日志来检测证书颁发机构是否错误或恶意为其域颁发了证书


SCT auditing  SCT 审计

To keep the logs honest, some party in the
system must also verify that log operators are fulfilling the
promise implicit in the SCTs that they issue. In particular, if a
client receives an SCT for some certificate 𝐶 signed by a log
operator, this party would like to verify that 𝐶 actually appears
in that operator’s public log. This process is SCT auditing.

为了保持日志的真实性，系统中的某些方还必须验证日志操作员是否履行了他们发布的 SCT 中隐含的承诺。 特别是，如果客户端收到由日志操作员签署的某些证书 𝐶 的 SCT，则该方希望验证 𝐶 确实出现在该操作员的公共日志中。 这个过程是 SCT 审计。


Clients must be involved in SCT auditing, as they are the
only participants who see SCTs “in the wild.” However, since
the set of SCTs that a client sees reveals information about the
client’s browsing history, the client should not reveal which
SCTs it has seen to the log operators or any other entity.

客户必须参与 SCT 审计，因为他们是唯一“在野外”看到 SCT 的参与者。 但是，由于客户端看到的一组 SCT 揭示了有关客户端浏览历史的信息，客户端不应向日志操作员或任何其他实体透露它看到的 SCT。

Google’s recent proposals for SCT auditing [33, 78] involve
an SCT auditor (run by Google) that is separate from the client.
In their model, the auditor maintains the entire set of SCTs
for non-expired certificates from all Certificate Transparency
logs. Every SCT that a client sees for a live website should
appear in the auditor’s set. To determine whether an SCT is
valid, a client can check whether it (or really, its SHA256 hash)
appears in the set of valid SCTs maintained by the auditor:

谷歌最近提出的 SCT 审计建议 [33, 78] 涉及一名独立于客户的 SCT 审计员（由谷歌运营）。 在他们的模型中，审核员维护所有证书透明度日志中未过期证书的整套 SCT。 客户在实时网站上看到的每个 SCT 都应该出现在审核员的集合中。 为了确定 SCT 是否有效，客户端可以检查它（或者实际上，它的 SHA256 哈希）是否出现在审核员维护的有效 SCT 集中：

• If the client’s SCT appears in the auditor’s set, then the log
server that issued the SCT correctly fulfilled its promise.

• 如果客户的 SCT 出现在审核员的集合中，则日志
发出 SCT 的服务器正确地履行了它的承诺。

• If not, the client can report the problematic SCT to the
auditor (i.e., Google) to investigate further.

• 如果没有，客户可以将有问题的 SCT 报告给审核员（即 Google）以进一步调查。

To protect the client’s privacy, the client should not reveal
its SCT to the auditor directly. (Again, the fact that a client has
seen an SCT for example.com reveals that the client has visited
example.com.) A privacy-protecting solution for SCT auditing
must thus allow the client to test whether or not its SCT appears
in the auditor’s set of valid SCTs, without revealing its SCT to
the auditor. This is a private set-membership problem [80].

为了保护客户的隐私，客户不应直接向审计师透露其 SCT。 （同样，客户已经看到了 example.com 的 SCT 的事实表明客户已经访问了 example.com。）因此，用于 SCT 审计的隐私保护解决方案必须允许客户测试其 SCT 是否出现在 审计员的有效 SCT 集，而不向审计员透露其 SCT。 这是一个私有集合成员问题[80]。

To summarize, we need the following properties to hold,
which we state only informally:

总而言之，我们需要以下属性来保持，我们只是非正式地声明：

• Correctness with false negatives. When an honest client
holding string 𝜎, chosen independently of the client’s secret
randomness, interacts with an honest audit server holding
strings 𝑆 = {𝜎1, 𝜎2, . . . }:

• 误报的正确性。 当一个诚实的客户持有字符串𝜎，独立于客户的秘密随机性选择，与持有字符串𝑆 = {𝜎1, 𝜎2, . . . }：

– if 𝜎 ∈ 𝑆, then the client always outputs “valid”, and
– if 𝜎 ∉ 𝑆, then the client outputs “invalid” with probability
at least 1/2, over the choice of the client’s randomness.

– 如果 𝜎 ∈ 𝑆，那么客户端总是输出“有效”，并且 – 如果 𝜎 ∉ 𝑆，那么客户端输出“无效”的概率至少为 1/2，超过了客户端随机性的选择。

• Privacy for the client. When an honest client interacts with
a malicious audit server, the server learns nothing about the
client’s private input string 𝜎.

• 客户的隐私。 当诚实的客户端与恶意审计服务器交互时，服务器不会了解客户端的私人输入字符串𝜎。

We do not require correctness to hold against a malicious
audit server. (If the audit server wants to break correctness, it
can trivially do so by lying about its set of SCTs.)

我们不需要正确性来抵御恶意审计服务器。 （如果审计服务器想要破坏正确性，它可以通过对其 SCT 集撒谎来轻松做到这一点。）

System parameters. There are roughly five billion active SCTs
in the web today [33]. Roughly six million of these are added
or removed each day as certificate authorities issue certificates
and as certificates expire [2]. Google’s current proposal for
SCT auditing requires the Chrome client to randomly sample
0.1% of the SCTs associated with its TLS connections, and
to audit only this small fraction of all SCTs [33]. Random
sampling in this way reduces the amortized cost of auditing by
1000×, but also reduces the chance that a single auditing client
catches a cheating log. Still, across many auditing clients this
randomized SCT auditing will—with high probability—catch
invalid SCTs that many clients see. (After 1000 clients observe
an invalid SCT, one will audit it in expectation.)

系统参数。 今天，网络上大约有 50 亿个活跃的 SCT [33]。 随着证书颁发机构颁发证书和证书到期 [2]，每天大约有 600 万个证书被添加或删除。 谷歌当前的 SCT 审计提议要求 Chrome 客户端随机抽样 0.1% 的与其 TLS 连接相关的 SCT，并且只审计所有 SCT [33] 中的一小部分。 以这种方式随机抽样将审计的摊销成本降低了 1000 倍，但也减少了单个审计客户捕获作弊日志的机会。 尽管如此，在许多审计客户中，这种随机 SCT 审计将很有可能捕获许多客户看到的无效 SCT。 （在 1000 个客户观察到无效的 SCT 后，会有一个期望对其进行审核。）


### 7.2 Existing approaches to SCT auditing

There are two existing proposals we consider for SCT auditing,
which could be used separately or combined.

我们考虑了两个现有的 SCT 审计建议，可以单独使用或组合使用。

Opt-out SCT auditing. Google Chrome’s current proposal [33]
has the client reveal the first 20 bits of the hash of its SCT to the
audit server. The audit server replies with all ≈ 1000 SCTs in
its set that begin with this 20-bit prefix. This method is simple
to implement, but achieves only 𝑘-anonymity for 𝑘 = 1000;
i.e., Google’s audit server learns that the client visited one in a
set of 1000 websites.

选择退出 SCT 审计。 谷歌浏览器当前的提议 [33] 让客户端向审计服务器显示其 SCT 哈希的前 20 位。 审计服务器在其集合中以该 20 位前缀开头的所有 ≈ 1000 个 SCT 进行回复。 这种方法实现起来很简单，但是对于 𝑘 = 1000 只实现了 𝑘-匿名； 即，谷歌的审计服务器获悉客户访问了一组 1000 个网站中的一个。

Anonymizing proxy. The client could use one or multiple proxy
servers, such as in Tor [34], to send its SCT to the auditor
anonymously [31], and the auditor could reply with the bit indicating whether or not the SCT appears in its set. This
mechanism is susceptible to timing attacks and it reveals the
entire distribution of clients’ SCTs to the audit server, which
could allow the auditor to deanonymize particular clients. As
a practical matter, browser vendors may not want to outsource
this security-critical task to the Tor network.

匿名代理。 客户端可以使用一个或多个代理服务器，例如在 Tor [34] 中，将其 SCT 匿名发送给审计员 [31]，审计员可以回复指示 SCT 是否出现在其集合中的位。 这种机制容易受到定时攻击，它会揭示客户 SCT 到审计服务器的整个分布，这可能允许审计师对特定客户进行去匿名化。 实际上，浏览器供应商可能不希望将这一安全关键任务外包给 Tor 网络。

### 7.3 Our approach

We propose a new scheme for SCT auditing that uses our PIR
schemes (Sections 4 and 5) along with our new approximate
set-membership data structure (Section 6). As in Chrome’s
proposal for SCT auditing [33], we would expect to run this
protocol on a small fraction of the SCTs sampled from the
client’s TLS connections. The deployment is as follows:

我们提出了一个新的 SCT 审计方案，它使用我们的 PIR 方案（第 4 和 5 节）以及我们新的近似集合成员数据结构（第 6 节）。 正如 Chrome 对 SCT 审计的提议 [33] 一样，我们希望在从客户端 TLS 连接中采样的一小部分 SCT 上运行该协议。 部署如下：

1. Audit server: Data-set construction. The SCT audit server
prepares an approximate set-membership data structure for
the set of all SHA256 hashes of all 𝑁 active SCTs. This data
structure consists of 𝑎 ≈ 768 arrays, each 8𝑁 bits in length.
The server runs our PIR scheme’s Setup algorithm on each of
these 𝑎 arrays, to produce 𝑎 PIR hints.

审计服务器：数据集构建。 SCT 审计服务器为所有 𝑁 活动 SCT 的所有 SHA256 哈希集合准备一个近似的集合成员数据结构。 该数据结构由𝑎 ≈ 768 个数组组成，每个数组长度为 8𝑁 位。 服务器在每个 𝑎 数组上运行我们的 PIR 方案的设置算法，以产生 𝑎 PIR 提示。

2. Client: Hint download. The client chooses a secret random
value 𝑖
∗ ←R
[𝑎] and downloads the 𝑖
∗
-th hint from the audit
server (revealing 𝑖
∗
to the server in the process). To test whether
a particular SCT hash appears in the server’s set, the client
now only needs to privately read a single bit from the 𝑖
∗
-th
array that the server holds. A malicious TLS server can trick
the client into accepting an invalid SCT with probability at
most 𝜖 ≈ 1/2. (The value 𝜖 is a parameter of the underlying
approximate set-membership data structure.) If the client audits
an 𝑓 -fraction of all TLS connections, the false-negative rate
is (1 − 𝑓 ) + 𝑓 · 𝜖. In our deployment, 𝑓 = 1/500 and 𝜖 = 1/2,
which yields an overall false-negative rate of 1/1000, matching
that of Chrome’s current proposal [33].

客户：提示下载。 客户端选择一个秘密随机值 𝑖 ∗ ←R [𝑎] 并从审计服务器下载 𝑖 ∗ -th 提示（在此过程中将 𝑖 ∗ 显示给服务器）。 为了测试特定的 SCT 哈希是否出现在服务器的集合中，客户端现在只需要从服务器持有的 𝑖 ∗ -th 数组中私下读取一个位。 恶意 TLS 服务器可以欺骗客户端以最多 𝜖 ≈ 1/2 的概率接受无效的 SCT。 （值 𝜖 是底层近似集合成员数据结构的参数。）如果客户端审核所有 TLS 连接的 𝑓 部分，则误报率为 (1 - 𝑓 ) + 𝑓 · 𝜖。 在我们的部署中，𝑓 = 1/500 和 𝜖 = 1/2，这产生了 1/1000 的整体假阴性率，与 Chrome 目前的提议 [33] 相匹配。


3. Client and audit server: SCT lookup via PIR. Once the client
receives an SCT whose validity it wants to verify, the client
hashes the SCT, computes the bit of the server’s 𝑖
∗
-th array
that the client needs to read to verify the validity of the SCT,
and uses our PIR protocol, along with its offline hint, to read
this bit. The audit server answers the client’s PIR query with
respect to the 𝑖
∗
-th array, and the client reconstructs the bit.

客户端和审计服务器：通过 PIR 进行 SCT 查找。 一旦客户端接收到它想要验证其有效性的 SCT，客户端就会对 SCT 进行哈希处理，计算客户端需要读取的服务器的 𝑖 ∗ -th 数组的位以验证 SCT 的有效性，并使用我们的 PIR 协议， 连同它的离线提示，阅读这一位。 审计服务器回答客户端关于 𝑖 ∗ -th 数组的 PIR 查询，客户端重建该位。

### 7.4 我们如何处理数据库更新
The client holds a PIR “hint” that depends on the contents of
the audit server’s database. Whenever the set of active SCTs
changes—which happens continuously as certificate authorities
issue new certificates and certificates expire—the audit server
must update its database state. Without extra engineering, the
client would have to download a fresh hint from the audit
server. Since the hints are relatively large (tens of megabytes
for our parameter settings), we would like the client to have to
download a hint only rarely.

客户端持有一个取决于审计服务器数据库内容的 PIR “提示”。 每当活动 SCT 集发生变化时——随着证书颁发机构颁发新证书和证书到期而不断发生——审计服务器必须更新其数据库状态。 如果没有额外的工程，客户端将不得不从审计服务器下载新的提示。 由于提示相对较大（我们的参数设置为数十兆字节），我们希望客户端只需很少下载提示。

Our approach is to have the client only download a hint
periodically—once per week, for example. When the audit
server sends the hint to the client, the server specifies the range
of certificate issue dates that the hint covers. When the client
wants to audit an SCT, the client checks whether its current
hint covers the issue date of the SCT in question. If so, the
client makes a PIR query to test the validity of the SCT. If not,
the client caches the hash of the SCT so that it can test the
validity of this SCT the next time it downloads a hint from the
audit server. In this way, the client eventually audits its full
random sample of SCTs, but the client needs only to download
a hint from the audit server periodically

我们的方法是让客户端只定期下载一个提示——例如每周一次。 当审计服务器向客户端发送提示时，服务器会指定提示涵盖的证书颁发日期范围。 当客户想要审计 SCT 时，客户会检查其当前提示是否涵盖相关 SCT 的发布日期。 如果是这样，客户端会进行 PIR 查询以测试 SCT 的有效性。 如果没有，客户端会缓存 SCT 的哈希，以便在下次从审计服务器下载提示时测试此 SCT 的有效性。 这样，客户端最终会审计其 SCT 的全随机样本，但客户端只需要定期从审计服务器下载一个提示

The server must now store a few copies of the database (e.g.,
one per day) but server storage is relatively inexpensive. In a
large-scale deployment, it would be readily possible to keep
all of these database copies in memory by having one or more
physical servers responsible for each version of the database.

服务器现在必须存储一些数据库副本（例如，每天一份），但服务器存储相对便宜。 在大规模部署中，通过让一个或多个物理服务器负责数据库的每个版本，很容易将所有这些数据库副本保存在内存中。


## 8 评估   

Implementation. We implement SimplePIR in 1,000 lines of
Go code, along with 80 lines of C (for the matrix-multiplication
routines). Implementing DoublePIR requires 170 additional
lines of Go code. Our code does not rely on any external libraries and is published under the MIT open-source license [1].
A code listing for the core of SimplePIR appears in Appendix G

执行。 我们在 1000 行 Go 代码和 80 行 C 代码（用于矩阵乘法例程）中实现了 SimplePIR。 实现 DoublePIR 需要 170 行额外的 Go 代码。 我们的代码不依赖任何外部库，并根据 MIT 开源许可 [1] 发布。 SimplePIR 核心的代码清单出现在附录 G 中

We store the database in memory in packed form and
decompress it into Z𝑝 elements on-the-fly, as otherwise the
Answer routine is memory-bandwidth-bound. In DoublePIR,
we represent the database as a rectangular (rather than square)
matrix, so that the first level of PIR dominates the computation.

我们将数据库以打包形式存储在内存中，并在运行中将其解压缩为 Z𝑝 元素，否则 Answer 例程受内存带宽限制。 在 DoublePIR 中，我们将数据库表示为矩形（而不是正方形）矩阵，因此第一级 PIR 主导计算。

We run all experiments using a single thread of execution,
on an AWS c5n.metal instance running Ubuntu 22.04. To
collect the throughput numbers for tables, we run each scheme
five times and report the average. All standard deviations in
throughput are smaller than 5% of the throughput measured.

我们使用单个执行线程在运行 Ubuntu 22.04 的 AWS c5n.metal 实例上运行所有实验。 为了收集表的吞吐量数字，我们将每个方案运行五次并报告平均值。 吞吐量的所有标准偏差均小于所测吞吐量的 5%。


### 8.1 Microbenchmarks微基准

Throughput. We first measure the maximal throughput of
each PIR scheme, on the database dimensions that suit it best.
In Table 1, we report the throughput measured for each PIR
scheme, on a database consisting of roughly 2
33 bits (or, 1
GB), where we take the entry size to be that for which the
highest throughput was reported in the corresponding paper
(or in a related paper, if it is not made explicit). These entry
sizes appear in Table 9. We confirm that these throughputs are
indeed the best achievable for each scheme by measuring each
scheme’s throughput on each entry size in Figure 6.

吞吐量。 我们首先在最适合它的数据库维度上测量每个 PIR 方案的最大吞吐量。 在表 1 中，我们报告了针对每个 PIR 方案测量的吞吐量，该数据库由大约 2 33 位（或 1 GB）组成，其中我们将条目大小设为相应论文中报告的最高吞吐量 （或在相关论文中，如果没有明确说明）。 这些条目大小出现在表 9 中。我们通过在图 6 中测量每个方案在每个条目大小上的吞吐量来确认这些吞吐量确实是每个方案可实现的最佳值。

SimplePIR and DoublePIR achieve throughputs of 6.5 GB/s
and 5.2 GB/s respectively, which is roughly 5× better than
the fastest prior single-server PIR scheme designed for the
streaming setting (SpiralStreamPack) and 18× better than the
fastest prior single-server PIR scheme designed for databases with short entries (Spiral). SimplePIR and DoublePIR also
achieve higher per-server throughput1 than prior two-server
PIR schemes: two-server PIR from DPFs achieves a per-server
throughput of 5.4 GB/s. Finally, we benchmark the throughput
of performing XORs in a linear scan over the database to
provide a hard upper bound on the performance of linearwork, two-server PIR. This benchmark achieves a per-server
throughput of 6.1 GB/s, also slower than SimplePIR.

SimplePIR 和 DoublePIR 分别实现了 6.5 GB/s 和 5.2 GB/s 的吞吐量，这比之前为流设置 (SpiralStreamPack) 设计的最快的单服务器 PIR 方案好大约 5 倍，比之前最快的单服务器 PIR 方案好 18 倍 专为具有短条目（螺旋）的数据库设计的服务器 PIR 方案。 SimplePIR 和 DoublePIR 还实现了比之前的两服务器 PIR 方案更高的每服务器吞吐量1：来自 DPF 的两服务器 PIR 实现了每服务器 5.4 GB/s 的吞吐量。 最后，我们对在数据库的线性扫描中执行 XOR 的吞吐量进行基准测试，以提供线性工作、两台服务器 PIR 性能的硬上限。 该基准测试实现了每台服务器 6.1 GB/s 的吞吐量，也比 SimplePIR 慢。

Communication. In Figure 6, we give each scheme’s total
communication, amortized over 100 queries, for increasing
entry sizes. On databases with short entries, DoublePIR’s
amortized communication is comparable to that of the most
communication-efficient schemes (Spiral, SpiralPack, SealPIR,
and OnionPIR). With larger entries, DoublePIR’s ammortized
communication costs increase, as the client must download
many hints. The two schemes with the closest throughput to
ours (SpiralStream and SpiralStreamPack), as well as FastPIR, have much larger amortized communication than both
DoublePIR and SimplePIR on entry sizes less than a kilobit.

沟通。 在图 6 中，我们给出了每个方案的总通信量，分摊了 100 多个查询，以增加条目大小。 在具有短条目的数据库上，DoublePIR 的摊销通信与最高效的通信方案（Spiral、SpiralPack、SealPIR 和 OnionPIR）相当。 随着条目的增加，DoublePIR 的摊销通信成本会增加，因为客户端必须下载许多提示。 与我们的吞吐量最接近的两种方案（SpiralStream 和 SpiralStreamPack）以及 FastPIR 在入口大小小于 1 kbit 时具有比 DoublePIR 和 SimplePIR 大得多的分摊通信。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917153320.png)

Figure 5: Throughput vs. per-query communication, on a database of
total size 2
33 bits (or, 1 GiB). For each PIR scheme, we display the
communication and the corresponding throughput for two choices
of entry size: one that maximizes the throughput, and another that
minimizes the communication. (For schemes with only one point
displayed, both entry sizes are the same.) The per-query communication cost given is the total (i.e., offline and online) communication,
amortized over 100 queries.

图 5：吞吐量与每次查询通信，在总大小为 2 33 位（或 1 GiB）的数据库上。 对于每个 PIR 方案，我们显示了两种条目大小选择的通信和相应的吞吐量：一种使吞吐量最大化，另一种使通信最小化。 （对于只显示一个点的方案，两个条目大小相同。）给出的每次查询通信成本是总（即离线和在线）通信，摊销超过 100 个查询。


Throughput vs. communication trade-off. We summarize these findings in Figure 5, which displays the throughput/communication trade-off achieved by each PIR scheme.
Concretely, we run each scheme on a database of 2
33 bits with
increasing entry sizes (as also done in Figure 6). Then, for each
scheme, we display the per-query communication (amortized
over 100 queries) and the corresponding throughput for two
choices of the entry size: one that maximizes the throughput, and another that minimizes the communication. Figure 5
demonstrates that our new PIR schemes achieve a novel point in the design space: SimplePIR and DoublePIR have substantially
higher throughput than all prior single-server PIR schemes;
DoublePIR further has a per-query communication cost that is
competitive with the most communication-efficient schemes.

吞吐量与通信的权衡。 我们在图 5 中总结了这些发现，该图显示了每个 PIR 方案实现的吞吐量/通信权衡。 具体来说，我们在 2 33 位的数据库上运行每个方案，并增加条目大小（如图 6 所示）。 然后，对于每个方案，我们显示每个查询的通信（摊销超过 100 个查询）和两个条目大小选择的相应吞吐量：一个最大化吞吐量，另一个最小化通信。 图 5 展示了我们的新 PIR 方案在设计空间中实现了一个新点：SimplePIR 和 DoublePIR 具有比所有先前的单服务器 PIR 方案更高的吞吐量； DoublePIR 还具有每次查询的通信成本，与最具通信效率的方案相比具有竞争力。


Comparison on a database of 2
33 × 1-bit entries. In Table 7,
we give a fine-grained comparison of the performance of each
scheme on a database relevant to our application, consisting
of 2
33 1-bit entries. On this database, SimplePIR and DoublePIR again achieve much higher throughput than all other
schemes (6 GB/s and 5 GB/s respectively). SimplePIR has
high offline download and thus also client-side storage costs.
However, DoublePIR’s offline download is comparable to the
offline communication of other PIR schemes, and its online
communication is on the order of kilobytes.

在 2 个 33 × 1 位条目的数据库上进行比较。 在表 7 中，我们对每个方案在与我们的应用程序相关的数据库上的性能进行了细粒度比较，该数据库由 2 33 个 1 位条目组成。 在这个数据库上，SimplePIR 和 DoublePIR 再次实现了比所有其他方案高得多的吞吐量（分别为 6 GB/s 和 5 GB/s）。 SimplePIR 具有较高的离线下载，因此也具有客户端存储成本。 但是，DoublePIR 的离线下载与其他 PIR 方案的离线通信不相上下，其在线通信量为千字节量级。

For each scheme, we additionally compute its cost per
query, when the client makes 100 database queries, using the
AWS costs for compute ($1.5 · 10−5 per core second) and
data transfer out of Amazon EC2 ($0.09 per GB). SimplePIR
achieves a per-query cost of $1 · 10−4
, while DoublePIR and
the cheapest scheme from related work (SpiralStreamPack)
each achieve a per-query cost of $2 · 10−5
. We note, however,
that SpiralStreamPack has a very large online upload (on the
order of megabytes), which is not reflected in its per-query
cost, as AWS only charges for outgoing communication.

对于每个方案，我们额外计算其每次查询的成本，当客户端进行 100 次数据库查询时，使用 AWS 的计算成本（每核每秒 1.5 美元·10−5 美元）和从 Amazon EC2 传出的数据（每 GB 0.09 美元）。 SimplePIR 的每次查询成本为 1 · 10−4 美元，而 DoublePIR 和相关工作中最便宜的方案 (SpiralStreamPack) 的每次查询成本为 2 · 10−5 美元。 然而，我们注意到 SpiralStreamPack 具有非常大的在线上传量（以兆字节为量级），这并未反映在其每次查询成本中，因为 AWS 仅对传出通信收费。

Batching queries. Finally, we evaluate how SimplePIR and
DoublePIR’s effective throughput scales when the client makes
a batch of queries for 𝑘 records at once, assuming the client
only needs to recover a constant fraction of the 𝑘 records. For
increasing values of 𝑘, we compute the expected number of
“successful” queries (i.e., the expected number of queries that fall into a distinct database chunk, as discussed in Section 4.3)
and we derive the expected “successful” throughput, i.e. the
throughput measured when the server answers that number of
queries at once, with a single pass over the database.

批处理查询。 最后，我们评估 SimplePIR 和 DoublePIR 的有效吞吐量在客户端一次对 𝑘 记录进行批量查询时如何扩展，假设客户端只需要恢复固定比例的 𝑘 记录。 对于增加的 𝑘 值，我们计算“成功”查询的预期数量（即，落入不同数据库块的预期查询数量，如 4.3 节所述），我们得出预期“成功”吞吐量，即 当服务器一次回答该数量的查询时测量的吞吐量，一次通过数据库。

Figure 8 shows that both SimplePIR and DoublePIR’s
throughput increases when the client makes a batch of queries
at once. SimplePIR’s throughput scales linearly, achieving a
value of over 100 GB/s on batch size 𝑘 ≥ 32 and roughly 1000
GB/s on batch size 𝑘 ≥ 256. DoublePIR achieves a throughput
of roughly 50 GB/s for 𝑘 ≥ 64; at this point, the throughput
plateaus, as the second level of PIR becomes a bottleneck.

批处理查询。 最后，我们评估 SimplePIR 和 DoublePIR 的有效吞吐量在客户端一次对 𝑘 记录进行批量查询时如何扩展，假设客户端只需要恢复固定比例的 𝑘 记录。 对于增加的 𝑘 值，我们计算“成功”查询的预期数量（即，落入不同数据库块的预期查询数量，如 4.3 节所述），我们得出预期“成功”吞吐量，即 当服务器一次回答该数量的查询时测量的吞吐量，一次通过数据库。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917154142.png)
Figure 6: Throughput and
per-query communication
for each PIR scheme, on
a database of total size
2
33 bits (or, 1 GB), with
entries of increasing size.
The per-query communication cost given is the
total (i.e., offline and online) per-query communication, amortized over
100 queries.
图 6：每个 PIR 方案的吞吐量和每次查询通信，在总大小为 2 33 位（或 1 GB）的数据库上，条目大小增加。 给出的每次查询通信成本是每次查询通信的总（即离线和在线），分摊超过 100 个查询。




![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220917154213.png)

Figure 8: Effective PIR
throughput (database size ×
queries per second), with increasing batch sizes and a
fixed-size hint, on a database
consisting of 2
33×1-bit entries. The shading displays
the standard deviation.

图 8：在由 2 个 33×1 位条目组成的数据库上，有效的 PIR 吞吐量（数据库大小 × 每秒查询数），随着批量大小的增加和固定大小的提示。 阴影显示标准偏差。



### 8.2 Certificate Transparency benchmark证书透明度基准

We propose using DoublePIR for the SCT auditing application.
With our new data structure for private set membership, the
task of SCT auditing requires a single round of PIR over a
database with 1-bit entries. For such a database, our evaluation
in Section 8.1 shows that DoublePIR achieves both high server
throughput and small client storage and communication. SCT
auditing occurs in the background, and is not on the critical
path to web browsing. Thus, while using PIR may increase the
latency of auditing, we believe this is a desirable trade-off in
exchange for cryptographic privacy, as long as the computation
remains modest and the communication remains comparable.

我们建议将 DoublePIR 用于 SCT 审计应用程序。 使用我们用于私有集成员的新数据结构，SCT 审计任务需要对具有 1 位条目的数据库进行单轮 PIR。 对于这样的数据库，我们在第 8.1 节中的评估表明，DoublePIR 实现了高服务器吞吐量和小型客户端存储和通信。 SCT 审计发生在后台，并不在 Web 浏览的关键路径上。 因此，虽然使用 PIR 可能会增加审计的延迟，但我们认为这是交换加密隐私的理想权衡，只要计算保持适度并且通信保持可比性。

To benchmark DoublePIR in this application context, we
evaluate the scheme on a database consisting of 2
36 × 1-
bit entries, which is the size of a row in our approximate
set membership data structure (Proposition 6.2) when we
instantiate it with all 5 billion active SCTs. (In our evaluation,
each entry is a random bit.) On this database size, we measure
that DoublePIR has a “hint” of size 16 MB, an online upload of 724 KB, and an online download of 32 KB. The server can
answer each query in fewer than 1.9 core-seconds (and this
work is fully parallelizable). As our client must audit one in
every 500 TLS connections, our proposal for SCT auditing
then requires: (1) 16 MB of client storage and download every
week (to keep the client hint), and (2) per TLS connection, an
amortized overhead of 0.004 core-seconds of server compute
and 1.5 KB of communication. Using the AWS costs for
compute ($1.5 · 10−5 per core second) and data transfer ($0.09
per outgoing GB), for each client, this amounts to a fixed cost
of $0.001 per week, along with $6 · 10−8 per TLS connection.
For a typical client making 104 TLS connections per week [3],
we expect this cost to be roughly $0.1 per year. Since (after
sampling its TLS connections) a typical client makes only
roughly 20 queries to the audit server using each week’s hint,
we can reduce the client’s storage to less than 400 KB using
the optimization from Appendix E.3.

为了在此应用程序上下文中对 DoublePIR 进行基准测试，我们在由 2 个 36 × 1 位条目组成的数据库上评估该方案，当我们用所有 5亿活跃的 SCT。 （在我们的评估中，每个条目都是一个随机位。）在这个数据库大小上，我们测量 DoublePIR 的“提示”大小为 16 MB，在线上传大小为 724 KB，在线下载大小为 32 KB。服务器可以在不到 1.9 个核心秒的时间内回答每个查询（并且这项工作是完全可并行化的）。由于我们的客户端必须每 500 个 TLS 连接审计一个，因此我们的 SCT 审计建议需要：(1) 每周 16 MB 的客户端存储和下载（以保留客户端提示），以及 (2) 每个 TLS 连接，摊销0.004 核心秒的服务器计算开销和 1.5 KB 的通信开销。使用 AWS 的计算成本（每核每秒 1.5 美元·10−5 美元）和数据传输（每传出 GB 0.09 美元），对于每个客户端，这相当于每周 0.001 美元的固定成本，以及每个 TLS 6 美元·10−8 美元联系。对于每周进行 104 次 TLS 连接的典型客户端 [3]，我们预计此成本约为每年 0.1 美元。由于（在对其 TLS 连接进行采样后）典型客户端使用每周的提示仅对审计服务器进行大约 20 次查询，因此我们可以使用附录 E.3 中的优化将客户端的存储空间减少到 400 KB 以下。

By comparison, Google Chrome’s current SCT auditing
scheme has the client audit one in every 1000 TLS connections
and provides only 𝑘-anonymity (for 𝑘 = 1000) [33]. That is,
the server learns that a client visited one of a set of 1000
domains. Auditing incurs an amortized overhead of 240 B
of communication per connection [33], negligible server-side
computation, and no client-side storage (unless the client
caches popular SCTs). Again assuming a client making 104
TLS connections per week [3], we expect this scheme to
cost roughly $0.01/client/year; our approach using DoublePIR
incurs only 13× more communication and achieves the goal of
cryptographic privacy.

相比之下，谷歌浏览器当前的 SCT 审计方案每 1000 个 TLS 连接中就有一个客户端审计，并且仅提供 𝑘-匿名性（对于 𝑘 = 1000）[33]。 也就是说，服务器获悉客户端访问了一组 1000 个域中的一个。 审计会产生每个连接 240 B 通信的摊销开销 [33]、可忽略的服务器端计算和没有客户端存储（除非客户端缓存流行的 SCT）。 再次假设客户端每周进行 104 次 TLS 连接 [3]，我们预计此方案的成本约为 0.01 美元/客户端/年； 我们使用 DoublePIR 的方法只增加了 13 倍的通信量，并实现了加密隐私的目标。

## 9 Conclusion

We show that the server-side, per-core throughput of singleserver PIR can approach the memory bandwidth of the machine.
One of our new schemes, SimplePIR, is faster than two-server
PIR without two-server PIR’s undesirable trust assumptions.
Two exciting directions remain open: one is to reduce our
schemes’ communication; and another is to explore combining
our ideas with those of sublinear-time PIR [29, 30] to reduce
the computation cost beyond the linear-server-time barrier.

我们展示了单服务器 PIR 的服务器端每核吞吐量可以接近机器的内存带宽。 我们的一个新方案 SimplePIR 比两台服务器 PIR 更快，没有两台服务器 PIR 的不良信任假设。 两个令人兴奋的方向仍然开放：一个是减少我们计划的沟通； 另一个是探索将我们的想法与亚线性时间 PIR [29, 30] 的想法相结合，以降低超出线性服务器时间障碍的计算成本。

Acknowledgements. We thank Martin Albrecht for answering
questions about LWE hardness estimates, Vadim Lyubashevsky
for advice on discrete gaussian sampling, and Adam Belay
and Zhenyuan Ruan for discussions about AVX performance.
We are grateful to Anish Athalye and Derek Leung for reviewing a draft of this work, and to Dima Kogan, David
Wu, Jean-Philippe Bossuat, and Samir Menon for helpful
conversations and feedback. We thank Sebastian Angel for
constructive comments on the discussion of malicious security in an earlier version of this work, and for suggestions on
how to improve the presentation. This work was supported
in part by the National Science Foundation (Award CNS2054869), a gift from Google, a Facebook Research Award,
and MIT’s Fintech@CSAIL Initiative. Alexandra Henzinger
was supported by the National Science Foundation Graduate
Research Fellowship under Grant No. 2141064 and an EECS
Great Educators Fellowship. Matthew M. Hong was funded by
NIH R01 HG010959. Vinod Vaikuntanathan was supported by
DARPA under Agreement No. HR00112020023, NSF CNS2154149, MIT-IBM Watson AI, Analog Devices, a Microsoft
Trustworthy AI grant and a Thornton Family Faculty Research
Innovation Fellowship. Any opinions, findings and conclusions
or recommendations expressed in this material are those of the
author(s) and do not necessarily reflect the views of the United
States Government or DARPA.

致谢。我们感谢 Martin Albrecht 回答有关 LWE 硬度估计的问题，感谢 Vadim Lyubashevsky 就离散高斯采样提供建议，感谢 Adam Belay 和 Jinyuan Ruan 讨论 AVX 性能。我们感谢 Anish Athalye 和 Derek Leung 审阅了这项工作的草稿，并感谢 Dima Kogan、David Wu、Jean-Philippe Bossuat 和 Samir Menon 的有益对话和反馈。我们感谢 Sebastian Angel 对本工作早期版本中恶意安全性的讨论提出建设性意见，并就如何改进演示文稿提出建议。这项工作得到了美国国家科学基金会（CNS2054869 奖）、谷歌的礼物、Facebook 研究奖和麻省理工学院的 Fintech@CSAIL 倡议的部分支持。 Alexandra Henzinger 得到了美国国家科学基金会研究生研究奖学金的支持，资助号为 2141064 和 EECS 伟大的教育者奖学金。 Matthew M. Hong 由 NIH R01 HG010959 资助。 Vinod Vaikuntanathan 得到了 DARPA 根据协议号 HR00112020023、NSF CNS2154149、MIT-IBM Watson AI、Analog Devices、Microsoft 值得信赖的 AI 资助和 Thornton 家庭教师研究创新奖学金的支持。本材料中表达的任何意见、调查结果和结论或建议均为作者的观点，不一定反映美国政府或 DARPA 的观点。


























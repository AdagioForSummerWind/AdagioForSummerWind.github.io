---
title: "OpenResty"
date: 2022-07-21T10:29:27+08:00
lastmod: 2022-07-21
tags: [web server]
categories: []
slug: openResty
draft: true
---
# OpenResty从入门到实战

## 开篇词 | OpenResty，为你打开高性能开发的大门
温铭 2019-05-22

你好，我是温铭，OpenResty 软件基金会主席，曾任某开源商业公司合伙人，前 360 开源技术委员会委员，在互联网安全公司工作了 10 年，负责开发过云查杀、反钓鱼和企业安全产品。接下来的几个月，我会带着你系统地学习一下 OpenResty。

### 为什么学习 OpenResty

为什么学习 OpenResty，这是开篇的第一个问题。我们正身处技术日新月异的时代，经常听到周围的工程师开玩笑说，学不动了。人的精力有限，选择学习某个技术都会有机会成本。最好的选择，是从你工作中涉及到的部分出发，学以致用。对于服务端工程师来说，如果你的工作中涉及到 NGINX、高性能、高并发、动态控制、性能测试和分析等，那么不管开发语言和平台是什么，这门 OpenResty 课程都会让你有所裨益。如果你之前没有接触过 OpenResty，我确信它会给你打开另外一个服务端世界的大门。OpenResty 是一个兼具开发效率和性能的服务端开发平台，虽然它基于 NGINX 实现，但其适用范围，早已远远超出反向代理和负载均衡。

它的核心是基于 NGINX 的一个 C 模块（lua-nginx-module），该模块将 LuaJIT 嵌入到 NGINX 服务器中，并对外提供一套完整的 Lua API，透明地支持非阻塞 I/O，提供了轻量级线程、定时器等高级抽象。同时，围绕这个模块，OpenResty 构建了一套完备的测试框架、调试技术以及由 Lua 实现的周边功能库。你可以用 Lua 语言来进行字符串和数值运算、查询数据库、发送 HTTP 请求、执行定时任务、调用外部命令等，还可以用 FFI 的方式调用外部 C 函数。这基本上可以满足服务端开发需要的所有功能。掌握好了 OpenResty，你就可以同时拥有脚本语言的开发效率和迭代速度，以及 NGINX C 模块的高并发和高性能优势。

### 我与 OpenResty 的渊源
说了这么多 OpenResty 的特点，我又是怎样与它结缘的呢？其实，我是在 2012 年开始接触 OpenResty 的，那会儿我正在为一个新的系统做技术选型，作为一个 Python 的忠实粉丝，我不喜欢 NGINX C 模块的艰涩，却希望得到它的高性能，鱼与熊掌想兼得。该怎么办呢？经过一番搜寻后，我发现了 Python 社区“大妈” ZQ 的一篇介绍 OpenResty 的文章，可以说是如获至宝。不过，兴奋只持续了很短的时间，因为之后的我，就像是无头苍蝇一样，开始在黑暗中摸索着缓慢前行。踩了数不清的坑后，我才真正拿下了 OpenResty。和很多工程师不同的是，我喜欢写文章，在大学期间就一直维护着自己的技术博客。有一天晚上加班时，我发现身边一位工程师在用 GitHub 记录 ELK 的使用心得，并发布到了 GitBook 上。原来 GitHub 还可以开源书籍，而不只是代码！我一下子就被点燃了，当晚就列出了《OpenResty 最佳实践》的目录，并开始“鼓动”周围的工程师加入。我们从未宣传过这个开源项目，但它慢慢变成了 OpenResty 入门者的最佳伙伴。

不过，在加入 OpenResty Inc. 后，我才逐渐发现，能写出正确的 OpenResty 代码并避免常见的坑，和写出高性能、优质的 OpenResty 代码之间，还相差了十万八千里。**而跨越这个巨大鸿沟的法宝零件，散落在 OpenResty 开源项目的源码、文档、issue、PR、幻灯片、邮件列表中，需要你把它们串联成真正的法宝——一个完整的学习体系和知识图谱。**

那如何才能体系化学习 OpenResty 呢？在 OpenResty 的技术交流群里面，很多工程师都曾经有过这样的困惑。事实上，OpenResty 的学习资料还比较少，官方只有 API 文档，并没有提供入门和进阶的文档，而网上能找到的资料也不够系统。可以说，绝大部分的 OpenResty 使用者都是在摸着石头过河，过程很痛苦。因此，我与极客时间合作了这个专栏，目的很明确，就是让你轻松快速地入门，并给你描绘出 OpenResty 的全貌，帮你建立知识体系，带你真正掌握 OpenResty 这款开发利器。


### 学习这个专栏需要什么基础？
OpenResty 是在 NGINX 和 LuaJIT 的基础上搭建的，所以我们肯定需要 NGINX 和 LuaJIT 的基础知识。但你只需要很少的 NGINX 知识，就足够开始 OpenResty 之旅了。少到什么程度呢？涉及到的 NGINX 知识，我只用一节课就介绍完了。即使你完全没有接触过 NGINX，也可以跟着课程的节奏，逐步学习 OpenResty。要知道，OpenResty 并不等同于 NGINX，OpenResty 这个项目的目的之一，就是让你感知不到 NGINX 的存在。而从编程语言来看，Lua 是一种很容易理解的语言，你只要能够看懂它的代码，就可以完成本专栏的学习，并不需要能够独立写出复杂的 Lua 代码。同样的，我也会花少数几节课的时间，带你入门 Lua，达到 OpenResty 的使用水准。

### 从实战中来，到实战中去
实践出真知，这句话用在互联网技术的学习上很恰当。和理论偏多的书籍不同，专栏的形式本身更偏重于实战。专栏中出现的不少代码，都源自开源 OpenResty 的测试案例，以及实际的开源项目。引用这些实际案例，就是希望你在入门之初，就能接触到最优秀的代码，了解到最真实的使用场景。同时，我还会在专栏文章中，穿插多个视频课程。视频课的内容，都取自真实开源项目的功能点和 PR。通过视频，你会亲眼看到，刚刚学到的知识是如何在实际中使用的。专栏最后的实战部分，则是我们的真实“战场”。我会带你一起，用 OpenResty 从零搭建一个微服务 API 网关。根据我们在社区中的统计，接近一半的 OpenResty 使用者，都把 OpenResty 用在 API 网关的开发上，Kong 和 orange 则是 OpenResty 领域中最流行的两个开源网关项目。你想自己从头搭建一个更简单、更高性能的 API 网关吗？一起来吧。从实际的开源项目中学习，再到实际的开源项目中去实战，将实战融入完整的知识体系，这便是我的教学理念，希望你喜欢这种方式。



## 入门

## 01 | 初探OpenResty的三大特性

开篇词里我们说过，OpenResty 的优势显而易见。不过，在具体学习之前，让我们先简单回顾下 OpenResty 的发展过程，这有助于你对后面内容有更好的理解。


### OpenResty 的发展
OpenResty 并不像其他的开发语言一样从零开始搭建，而是基于成熟的开源组件——NGINX 和 LuaJIT。OpenResty 诞生于 2007 年，不过，它的第一个版本并没有选择 Lua，而是用了 Perl，这跟作者章亦春的技术偏好有很大关系。但 Perl 的性能远远不能达到要求，于是，在第二个版本中，Perl 就被 Lua 给替换了。 不过，**在 OpenResty 官方的项目中，Perl 依然占据着重要的角色，OpenResty 工程化方面都是用 Perl 来构建，比如测试框架、Linter、CLI 等**，后面我们也会逐步介绍。

后来，章亦春离开了淘宝，加入了美国的 CDN 公司 Cloudflare。因为 OpenResty 高性能和动态的优势很适合 CDN 的业务需求，很快， OpenResty 就成为 CDN 的技术标准。 通过丰富的 lua-resty 库，OpenResty 开始逐渐摆脱 NGINX 的影子，形成自己的生态体系，在 API 网关、软 WAF 等领域被广泛使用。

其实，我经常说，OpenResty 是一个被广泛使用的技术，但它并不能算得上是热门技术，这听上去有点矛盾，到底什么意思呢？说它应用广，是因为 OpenResty 现在是全球排名第五的 Web 服务器。我们经常用到的 12306 的余票查询功能，或者是京东的商品详情页，这些高流量的背后，其实都是 OpenResty 在默默地提供服务。说它并不热门，那是因为使用 OpenResty 来构建业务系统的比例并不高。使用者大都用 OpenResty 来处理入口流量，并没有深入到业务里面去，自然，对于 OpenResty 的使用也是浅尝辄止，满足当前的需求就可以了。这当然也与 OpenResty 没有像 Java、Python 那样有成熟的 Web 框架和生态有关。说了这么多，接下来，我重点来介绍下，OpenResty 这个开源项目值得称道和学习的几个地方。

### OpenResty 的三大特性
#### 详尽的文档和测试用例

没错，文档和测试是判断开源项目是否靠谱的关键指标，甚至是排在代码质量和性能之前的。OpenResty 的文档非常详细，作者把每一个需要注意的点都写在了文档中。绝大部分时候，我们只需要仔细查看文档，就能解决遇到的问题，而不用谷歌搜索或者是跟踪到源码中。为了方便起见，OpenResty 还自带了一个命令行工具restydoc，专门用来帮助你通过 shell 查看文档，避免编码过程被打断。不过，文档中只会有一两个通用的代码片段，并没有完整和复杂的示例，到哪里可以找到这样的例子呢？对于 OpenResty 来说，自然是/t目录，它里面就是所有的测试案例。每一个测试案例都包含完整的 NGINX 配置和 Lua 代码，以及测试的输入数据和预期的输出数据。不过，OpenResty 使用的测试框架，与其他断言风格的测试框架完全不同，后面我会用专门章节来做介绍。

#### 同步非阻塞
协程，是很多脚本语言为了提升性能，在近几年新增的特性。但它们实现得并不完美，有些是语法糖，有些还需要显式的关键字声明。OpenResty 则没有历史包袱，在诞生之初就支持了协程，并基于此实现了同步非阻塞的编程模式。这一点是很重要的，毕竟，程序员也是人，代码应该更符合人的思维习惯。显式的回调和异步关键字会打断思路，也给调试带来了困难。这里我解释一下，什么是同步非阻塞。先说同步，这个很简单，就是按照代码来顺序执行。比如下面这段伪码：


```
local res, err  = query-mysql(sql)
local value, err = query-redis(key)
```
在同一请求连接中，如果要等 MySQL 的查询结果返回后，才能继续去查询 Redis，那就是同步；如果不用等 MySQL 的返回，就能继续往下走，去查询 Redis，那就是异步。对于 OpenResty 来说，绝大部分都是同步操作，只有 ngx.timer 这种后台定时器相关的 API，才是异步操作。再来说说非阻塞，这是一个很容易和“异步”混淆的概念。这里我们说的“阻塞”，特指阻塞操作系统线程。我们继续看上面的例子，假设查询 MySQL 需要 1s 的时间，如果在这 1s 内，操作系统的资源（CPU）是空闲着并傻傻地等待返回，那就是阻塞；如果 CPU 趁机去处理其他连接的请求，那就是非阻塞。非阻塞也是 C10K、C100K 这些高并发能够实现的关键。同步非阻塞这个概念很重要，建议你仔细琢磨一下。我认为，这一概念最好不要通过类比来理解，因为不恰当的类比，很可能把你搞得更糊涂。在 OpenResty 中，上面的伪码就可以直接实现同步非阻塞，而不用任何显式的关键字。这里也再次体现了，让开发者用起来更简单，是 OpenResty 的理念之一。


#### 动态
OpenResty 有一个非常大的优势，并且还没有被充分挖掘，就是它的动态。

传统的 Web 服务器，比如 NGINX，如果发生任何的变动，都需要你去修改磁盘上的配置文件，然后重新加载才能生效，这也是因为它们并没有提供 API，来控制运行时的行为。所以，在需要频繁变动的微服务领域，NGINX 虽然有多次尝试，但毫无建树。而异军突起的 Envoy， 正是凭着 xDS 这种动态控制的 API，大有对 NGINX 造成降维攻击的威胁。和 NGINX 、 Envoy 不同的是，OpenResty 是由脚本语言 Lua 来控制逻辑的，而动态，便是 Lua 天生的优势。通过 OpenResty 中 lua-nginx-module 模块中提供的 Lua API，我们可以动态地控制路由、上游、SSL 证书、请求、响应等。甚至更进一步，你可以在不重启 OpenResty 的前提下，修改业务的处理逻辑，并不局限于 OpenResty 提供的 Lua API。这里有一个很合适的类比，可以帮你理解上面关于动态的说明。你可以把 Web 服务器当做是一个正在高速公路上飞驰的汽车，NGINX 需要停车才能更换轮胎，更换车漆颜色；Envoy 可以一边跑一边换轮胎和颜色；而 OpenResty 除了具备前者能力外，还可以在不停车的情况下，直接把汽车从 SUV 变成跑车。显然，掌握这种“逆天”的能力后，OpenResty 的能力圈和想象力就扩展到了其他领域，比如  Serverless 和边缘计算等。

### 你学习的重点在哪里？
讲了这么多 OpenResty 的重点特性，你又该怎么学呢？我认为，学习需要抓重点，围绕主线来展开，而不是眉毛胡子一把抓，这样，你才能构建出脉络清晰的知识体系。要知道，不管多么全面的课程，都不可能覆盖所有问题，更不能直接帮你解决线上的每个 bug 和异常。回到 OpenResty 的学习，在我看来，想要学好 OpenResty，你必须理解下面 8 个重点：

同步非阻塞的编程模式；不同阶段的作用；LuaJIT 和 Lua 的不同之处；OpenResty API 和周边库；协程和 cosocket；单元测试框架和性能测试工具；火焰图和周边工具链；性能优化。

这些内容正是我们学习的重点，在专栏的各个模块中我都会分别讲到。在学习的过程中，我希望你能举一反三，并且根据自己的兴趣点和背景，有针对性地深入阅读某些章节。如果你是 OpenResty 的初学者，那么你可以完全跟着专栏的进度，在自己的环境中安装 OpenResty，运行并修改示例代码。要记住，你的重点在于构建 OpenResty 的全貌，而非死磕某个知识点。当然，如果你有疑问的地方，随时可以在留言区提出，我会解答你的困惑。如果你正在项目中使用 OpenResty，那就太棒了，相信你在阅读 LuaJIT 和性能优化章节时，一定会有更多的共鸣，更能应用到实际，在你的项目中看到优化前后的性能指标变化。另外，如果你想要给 OpenResty 以及周边库贡献代码，那么最大的门槛，并不是对 OpenResty 原理的理解，或者是如何编写 NGINX C 模块的问题，而是测试案例和代码规范。我见过太多 OpenResty 的代码贡献者（也包括我自己），在一个 PR 上反复修改测试案例和代码风格，这其中有太多鲜为人知的潜规则。所以，专栏的代码规范和单元测试部分，就是为你准备的。而如果你是测试工程师，即使你不使用 OpenResty，OpenResty 的测试框架和性能分析工具集，也必能给你非常多的启发。毕竟，OpenResty 在测试上面的投入和积累是相当深厚的。




## 02 | 如何写出你的“hello world”？

每当我们开始学习一个新的开发语言或者平台，都会从最简单的hello world开始，OpenResty 也不例外。让我们先跳过安装的步骤，直接看下，最简单的 OpenResty 程序是怎么编写和运行的：

```
$ resty -e "ngx.say('hello world')"
hello world
```
这应该是你见过的最简单的那种 hello world 代码写法，和 Python 类似：

```
$ python -c 'print("hello world")'
hello world
```
这背后其实是 OpenResty 哲学的一种体现，代码要足够简洁，也好让你打消“从入门到放弃“的念头。我们今天的内容，就专门围绕着这行代码来展开聊一聊。上一节我们讲过，OpenResty 是基于 NGINX 的。那你现在是不是有一个疑问：为什么这里看不到 NGINX 的影子？别着急，我们加一行代码，看看 resty背后真正运行的是什么：

```
resty -e "ngx.say('hello world'); ngx.sleep(10)" &
```
我们加了一行 sleep 休眠的代码，让 resty 运行的程序打印出字符串后，并不退出。这样，我们就有机会一探究竟：
```
$ ps -ef | grep nginx
501 25468 25462   0  7:24下午 ttys000    0:00.01 /usr/local/Cellar/openresty/''1.13.6.2/nginx/sbin/nginx -p /tmp/resty_AfNwigQVOB/ -c conf/nginx.conf
```
终于看了熟悉的 NGINX 进程。看来，resty 本质上是启动了一个 NGINX 服务，那么resty 又是一个什么程序呢？我先卖个关子，咱后面再讲。你的机器上可能还没有安装 OpenResty，所以，接下来，我们先回到开头跳过的安装步骤，把 OpenResty 安装完成后再继续。
### OpenResty 的安装
和其他的开源软件一样，OpenResty 的安装有多种方法，比如使用操作系统的包管理器、源码编译或者 docker 镜像。我推荐你优先使用 yum、apt-get、brew 这类包管理系统，来安装 OpenResty。这里我们使用 Mac 系统来做示例：

```
brew tap openresty/brew
brew install openresty
```
使用其他操作系统也是类似的，先要在包管理器中添加 OpenResty 的仓库地址，然后用包管理工具来安装。具体步骤，你可以参考**官方文档**。

不过，这看似简单的安装背后，其实有两个问题：为什么我不推荐使用源码来安装呢？为什么不能直接从操作系统的官方仓库安装，而是需要先设置另外一个仓库地址？

对于这两个问题，你不妨先自己想一想。


这里我想补充一句。在这门课程里面，我会在表象背后提出很多的“为什么”，希望你可以一边学新东西一边思考，结果是否正确并不重要。独立思考在技术领域也是稀缺的，由于每个人技术领域和深度的不同，在任何课程中老师都会不可避免地带有个人观点以及知识的错漏。只有在学习过程中多问几个为什么，融会贯通，才能逐渐形成自己的技术体系。很多工程师都有源码的情节，多年前的我也是一样。在使用一个开源项目的时候，我总是希望能够自己手工从源码开始 configure 和 make，并修改一些编译参数，感觉这样做才能最适合这台机器的环境，才能把性能发挥到极致。但现实并非如此，每次源码编译，我都会遇到各种诡异的环境问题，磕磕绊绊才能安装好。现在我想明白了，我们的最初目的其实是用开源项目来解决业务需求，不应该浪费时间和环境鏖战，更何况包管理器和容器技术，正是为了帮我们解决这些问题。言归正传，给你说说我的看法。使用 OpenResty 源码安装，不仅仅步骤繁琐，需要自行解决 PCRE、OpenSSL 等外部依赖，而且还需要手工对 OpenSSL 打上对应版本的补丁。不然就会在处理 SSL session 时，带来功能上的缺失，比如像ngx.sleep这类会导致 yield 的 Lua API 就没法使用。这部分内容如果你还想深入了解，可以参考[官方文档](https://github.com/openresty/lua-nginx-module#ssl_session_fetch_by_lua_block)来获取更详细的信息。


从 OpenResty 自己维护的 OpenSSL [打包脚本](https://github.com/openresty/openresty-packaging/blob/master/rpm/SPECS/openresty-openssl.spec)中，就可以看到这些补丁。而在 OpenResty 升级 OpenSSL 版本时，都需要重新生成对应的补丁，并进行完整的回归测试。


```
Source0: https://www.openssl.org/source/openssl-%{version}.tar.gz

Patch0: https://raw.githubusercontent.com/openresty/openresty/master/patches/openssl-1.1.0d-sess_set_get_cb_yield.patch
Patch1: https://raw.githubusercontent.com/openresty/openresty/master/patches/openssl-1.1.0j-parallel_build_fix.patch
```
同时，我们可以看下 OpenResty 在 CentOS 中的[打包脚本](https://github.com/openresty/openresty-packaging/blob/master/rpm/SPECS/openresty.spec)，看看是否还有其他隐藏的点：

```
BuildRequires: perl-File-Temp
BuildRequires: gcc, make, perl, systemtap-sdt-devel
BuildRequires: openresty-zlib-devel >= 1.2.11-3
BuildRequires: openresty-openssl-devel >= 1.1.0h-1
BuildRequires: openresty-pcre-devel >= 8.42-1
Requires: openresty-zlib >= 1.2.11-3
Requires: openresty-openssl >= 1.1.0h-1
Requires: openresty-pcre >= 8.42-1
```
从这里可以看出，OpenResty 不仅维护了自己的 OpenSSL 版本，还维护了自己的 zlib 和 PCRE 版本。不过后面两个只是调整了编译参数，并没有维护自己的补丁。所以，综合这些因素，我不推荐你自行源码编译 OpenResty，除非你已经很清楚这些细节。为什么不推荐源码安装，你现在应该已经很清楚了。其实我们在回答第一个问题时，也顺带回答了第二个问题：为什么不能直接从操作系统的官方仓库安装，而是需要先设置另外一个仓库地址？这是因为，官方仓库不愿意接受第三方维护的 OpenSSL、PCRE 和 zlib 包，这会导致其他使用者的困惑，不知道选用哪一个合适。另一方面，OpenResty 又需要指定版本的 OpenSSL、PCRE 库才能正常运行，而系统默认自带的版本都比较旧。


### OpenResty CLI
安装完 OpenResty 后，默认就已经把 OpenResty 的 CLI：resty 安装好了。resty是个 1000 多行的 Perl 脚本，之前我们提到过，OpenResty 的周边工具都是 Perl 编写的，这个是由 OpenResty 作者的技术偏好决定的。

```
$ which resty
/usr/local/bin/resty
$ head -n 1 /usr/local/bin/resty
 #!/usr/bin/env perl
```
resty 的功能很强大，想了解完整的列表，你可以查看resty -h或者[官方文档](https://github.com/openresty/resty-cli)。下面，我挑两个有意思的功能介绍一下。

```
$ resty --shdict='dogs 1m' -e 'local dict = ngx.shared.dogs
                               dict:set("Tom", 56)
                               print(dict:get("Tom"))'
56
```
先来看第一个例子。这个示例结合了 NGINX 配置和 Lua 代码，一起完成了一个共享内存字典的设置和查询。dogs 1m 是 NGINX 的一段配置，声明了一个共享内存空间，名字是 dogs，大小是 1m；在 Lua 代码中用字典的方式使用共享内存。另外还有--http-include 和 --main-include来设置 NGINX 配置文件。所以，上面的例子也可以写为：

```
resty --http-conf 'lua_shared_dict dogs 1m;' -e 'local dict = ngx.shared.dogs
                               dict:set("Tom", 56)
                               print(dict:get("Tom"))'
```
OpenResty 世界中常用的调试工具，比如gdb、valgrind、sysetmtap和Mozilla rr ，也可以和 resty 一起配合使用，方便你平时的开发和测试。它们分别对应着 resty 不同的指令，内部的实现其实很简单，就是多套了一层命令行调用。我们以 valgrind 为例：


```
$ resty --valgrind  -e "ngx.say('hello world'); "
ERROR: failed to run command "valgrind /usr/local/Cellar/openresty/1.13.6.2/nginx/sbin/nginx -p /tmp/resty_hTFRsFBhVl/ -c conf/nginx.conf": No such file or directory
```
在后面调试、测试和性能分析的章节，会涉及到这些工具的使用。它们不仅适用于 OpenResty 世界，也是服务端的通用工具，让我们循序渐进地来学习吧。


### 更正式的 hello world
最开始我们使用resty写的第一个 OpenResty 程序，没有 master 进程，也不会监听端口。下面，让我们写一个更正式的 hello world。写出这样的 OpenResty 程序并不简单，你至少需要三步才能完成：

创建工作目录；修改 NGINX 的配置文件，把 Lua 代码嵌入其中；启动 OpenResty 服务。

我们先来创建工作目录。

```
mkdir geektime
cd geektime
mkdir logs/ conf/
```
下面是一个最简化的 nginx.conf，在根目录下新增 OpenResty 的content_by_lua指令，里面嵌入了ngx.say的代码：

```
events {
    worker_connections 1024;
}

http {
    server {
        listen 8080;
        location / {
            content_by_lua '
                ngx.say("hello, world")
            ';
        }
    }
}
```
请先确认下，是否已经把openresty加入到PATH环境中；然后，启动 OpenResty 服务就可以了：

```
openresty -p `pwd` -c conf/nginx.conf
```
没有报错的话，OpenResty 的服务就已经成功启动了。你可以打开浏览器，或者使用 curl 命令，来查看结果的返回：

```
$ curl -i 127.0.0.1:8080
HTTP/1.1 200 OK
Server: openresty/1.13.6.2
Content-Type: text/plain
Transfer-Encoding: chunked
Connection: keep-alive

hello, world
```
到这里，恭喜你，一个真正的 OpenResty 程序就完成了。

### 总结
让我们回顾下今天讲的内容。我们通过一行简单的 hello, world 代码，延展到 OpenResty 的安装和 CLI，并在最后启动了 OpenResty 进程，运行了一个真正的后端程序。其中， resty 是我们后面会频繁使用到的命令行工具，课程中的演示代码都是用它来运行的，而不是启动后台的 OpenResty 服务。更为重要的是，OpenResty 的背后隐藏了非常多的文化和技术细节，它就像漂浮在海面上的一座冰山。我希望能够通过这门课程，给你展示更全面、更立体的 OpenResty，而不仅仅是它对外暴露出来的 API。


## 03 | 揪出隐藏在背后的那些子项目

我们先来揭晓上一节最后留下的思考题，如何把 Lua 代码从 nginx.conf 里面抽取出来，保持代码的可读性和可维护性呢？操作其实很简单。我们先在 geektime 的工作目录下，创建一个名为 lua 的目录，专门用来存放代码：


```
$ mkdir lua
$ cat lua/hello.lua
ngx.say("hello, world")
```
然后修改 nginx.conf 的配置，把 content_by_lua_block 改为 content_by_lua_file：

```
pid logs/nginx.pid;
events {
  worker_connections 1024;
}

http {
  server {
    listen 8080;
    location / {
      content_by_lua_file lua/hello.lua;
      }
    }
  }
```
最后，重启 OpenResty 的服务就可以了：

```
$ sudo kill -HUP `cat logs/nginx.pid`
```
你可以使用 curl ，验证是否返回了预期的结果。至于后面 Lua 代码的变更，你就可以直接修改 hello.lua 这个文件，而不是 nginx.conf 了。其实，在上面这个小例子里面，也有几个有趣的地方：

content_by_lua_file lua/hello.lua; 里面写的是相对路径，那么 OpenResty 是如何找到这个 Lua 文件的？Lua 代码内容的变更，需要重启 OpenResty 服务才会生效，这样显然不方便调试，那么有没有什么即时生效的方法呢？如何把 lua 代码所在的文件夹，加入到 OpenResty 的查找路径中呢？

这几个问题，我鼓励你先自己思考一下，它们都可以在官方文档里面找到[答案](https://github.com/openresty/lua-nginx-module#content_by_lua_file)。这也是为什么，我一直强调文档的重要性。


接下来我们一起来解答。先看第一个问题。如果原本给出的是相对路径，那么 OpenResty 在启动时，会把 OpenResty 启动的命令行参数中的 -p PATH 作为前缀，将相对路径拼接为绝对路径。这样，自然就可以顺利找到 Lua 文件。再来看第二个问题。Lua 代码在第一个请求时会被加载，并默认缓存起来。所以在你每次修改 Lua 源文件后，都必须重新加载 OpenResty 才会生效。其实，在 nginx.conf 中关闭 lua_code_cache 就能避免重新加载，这一点你可以自己试试看。不过，特别需要注意的是，这种方法只能临时用于开发和调试，如果是线上部署，一定要记得打开缓存，否则会非常影响性能。

最后一个问题，OpenResty 提供了 lua_package_path 指令，可以设置 Lua 模块的查找路径。针对上面的例子，我们可以把 lua_package_path 设置为 $prefix/lua/?.lua;;，其中，

 $prefix就是启动参数中的 -p PATH；/lua/?.lua表示 lua 目录下所有以 .lua 作为后缀的文件；最后的两个分号，则代表内置的代码搜索路径。

## OpenResty 安装后的目录结构

了解完第一个 hello world 程序后，我们继续追根究底，来看下 OpenResty 自身安装完成后，它的目录结构是怎样的，以及里面包含哪些文件。我们先通过 -V 选项，查看 OpenResty 安装到了哪一个目录。下面的这个结果，我省略了很多模块的编译参数，这些我们稍后再来补上：

```
$ openresty -V
nginx version: openresty/1.13.6.2
built by clang 10.0.0 (clang-1000.10.44.4)
built with OpenSSL 1.1.0h  27 Mar 2018
TLS SNI support enabled
configure arguments: --prefix=/usr/local/Cellar/openresty/1.13.6.2/nginx ...
```
我本地是通过 brew 安装的，所以目录是/usr/local/Cellar/openresty/1.13.6.2/nginx ，和你的本地环境很可能不同。这其中主要包含了 bin、luajit、lualib、nginx、pod 这几个子目录。理解这几个文件夹的含义很重要，可以帮我们更好地学习 OpenResty。接下来，我们逐个来看一下。首先是最重要的 bin 目录：
```
$ ll /usr/local/Cellar/openresty/1.13.6.2/bin
total 320
-r-xr-xr-x  1 ming  admin    19K  3 27 12:54 md2pod.pl
-r-xr-xr-x  1 ming  admin    15K  3 27 12:54 nginx-xml2pod
lrwxr-xr-x  1 ming  admin    19B  3 27 12:54 openresty -> ../nginx/sbin/nginx
-r-xr-xr-x  1 ming  admin    62K  3 27 12:54 opm
-r-xr-xr-x  1 ming  admin    29K  3 27 12:54 resty
-r-xr-xr-x  1 ming  admin    15K  3 27 12:54 restydoc
-r-xr-xr-x  1 ming  admin   8.3K  3 27 12:54 restydoc-index
```
这里面既有我们上一节中提到的 OpenResty CLI resty，也有最核心的可执行文件 openresty，它其实是 nginx 的一个软链接。至于目录里面其他的一些工具，没有任何悬念，它们和 resty 一样，都是 Perl 脚本。在这其中，opm 是包管理工具，可以通过它来管理各类第三方包，后面会有一节内容专门来讲；而 restydoc，则是我们第一节提到过的“老朋友”了，它是 OpenResty 提供的文档查看工具，你可以通过它来查看 OpenResty 和 NGINX 的使用文档：


```
$ restydoc -s ngx.say
$ restydoc -s proxy_pass
```
这段代码中的两个例子，分别查询了 OpenResty 的 API 和 NGINX 的指令。restydoc 这个工具，对服务端工程师的专注开发有很大帮助。浏览完了 bin 目录，我们接着看下 pod 目录。先强调一点，这里的“pod”，和 k8s 里“pod”的概念完全没有关系。pod 是 Perl 里面的一种标记语言，用于给 Perl 的模块编写文档。pod 目录中存放的就是 OpenResty、 NGINX、lua-resty-*、LuaJIT 的文档， 这些就和刚才提到的 restydoc 联系在一起了。接下来是熟悉的 nginx 和 luajit 这两个目录。这两个很好理解，主要存放  NGINX 和 LuaJIT 的可执行文件和依赖，是 OpenResty 的基石。很多人说 OpenResty 基于 Lua，这个说法其实并不准确，从上面我们可以看出， OpenResty 其实是基于 LuaJIT 的。事实上，早期的 OpenResty 同时带有 Lua 和 LuaJIT，你可以通过编译选项，来决定使用 Lua 还是 LuaJIT。不过到了现在，Lua 逐渐被淘汰，就只支持更高性能的 LuaJIT 了。最后，我们看下 lualib 目录。它里面存放的是 OpenResty 中使用到的 Lua 库，主要分为 ngx 和 resty 两个子目录。


前者存放的是 [lua-resty-core](https://github.com/openresty/lua-resty-core/tree/master/lib/ngx) 这个官方项目中的 Lua 代码，里面都是基于 FFI 重新实现的 OpenResty API，后面我会用专门的文章来解释为什么要重新实现，这里你有个大概印象即可，不必深究。而 resty 目录中存放的则是各种 lua-resty-* 项目包含的 Lua 代码，接下来我们会接触到。

按照我讲课的惯例，到这一步我会给出这些目录源头的出处。这也是开源项目的乐趣之一，如果你喜欢打破砂锅问到底，那你总发现更多好玩的东西。下面是 OpenResty 在 CentOS 中的[打包脚本](https://github.com/openresty/openresty-packaging/blob/master/rpm/SPECS/openresty.spec#L218)，里面包含了上面提到的所有目录，你可以自己了解一下。

```
%files
%defattr(-,root,root,-)

/etc/init.d/%{name}
/usr/bin/%{name}
%{orprefix}/bin/openresty
%{orprefix}/site/lualib/
%{orprefix}/luajit/*
%{orprefix}/lualib/*
%{orprefix}/nginx/html/*
%{orprefix}/nginx/logs/
%{orprefix}/nginx/sbin/*
%{orprefix}/nginx/tapset/*
%config(noreplace) %{orprefix}/nginx/conf/*
%{orprefix}/COPYRIGHT
```

### OpenResty 项目概览提到

 OpenResty，你应该会想到 lua-nginx-module。没错，这个 NGINX 的 C 模块确实是 OpenResty 的核心，但它并不等价于 OpenResty。很多工程师都会把 OpenResty 叫做 ngx lua，有不少技术大会的分享和出版的书籍中也是用的这个叫法，这其实是不严谨的，也是 OpenResty 社区不提倡的。下面我来讲讲为什么，以及 OpenResty 中除了 lua-nginx-module ，还有哪些其他的关联项目。打开 OpenResty 在 GitHub 的  [项目主页](https://github.com/openresty/)，你可以看到 OpenResty 包含了 68 个公开的项目，大概分为以下 7 类, 下面我来分别简单介绍下，让你有个初步的印象，这样你后面学习起来也轻松一些。

### NGINX C 模块

OpenResty 的项目命名都是有规范的，以 *-nginx-module命名的就是 NGINX 的 C 模块。OpenResty 中一共包含了 20 多个 C 模块，我们在本节最开始使用的 openresty -V 中，也可以看到这些 C 模块：

```
$ openresty -V
nginx version: openresty/1.13.6.2
built by clang 10.0.0 (clang-1000.10.44.4)
built with OpenSSL 1.1.0h  27 Mar 2018
TLS SNI support enabled
configure arguments: --prefix=/usr/local/Cellar/openresty/1.13.6.2/nginx --with-cc-opt='-O2 -I/usr/local/include -I/usr/local/opt/pcre/include -I/usr/local/opt/openresty-openssl/include' --add-module=../ngx_devel_kit-0.3.0 --add-module=../echo-nginx-module-0.61 --add-module=../xss-nginx-module-0.06 --add-module=../ngx_coolkit-0.2rc3 --add-module=../set-misc-nginx-module-0.32 --add-module=../form-input-nginx-module-0.12 --add-module=../encrypted-session-nginx-module-0.08 --add-module=../srcache-nginx-module-0.31 --add-module=../ngx_lua-0.10.13 --add-module=../ngx_lua_upstream-0.07 --add-module=../headers-more-nginx-module-0.33 --add-module=../array-var-nginx-module-0.05 --add-module=../memc-nginx-module-0.19 --add-module=../redis2-nginx-module-0.15 --add-module=../redis-nginx-module-0.3.7 --add-module=../ngx_stream_lua-0.0.5 --with-ld-opt='-Wl,-rpath,/usr/local/Cellar/openresty/1.13.6.2/luajit/lib -L/usr/local/lib -L/usr/local/opt/pcre/lib -L/usr/local/opt/openresty-openssl/lib' --pid-path=/usr/local/var/run/openresty.pid --lock-path=/usr/local/var/run/openresty.lock --conf-path=/usr/local/etc/openresty/nginx.conf --http-log-path=/usr/local/var/log/nginx/access.log --error-log-path=/usr/local/var/log/nginx/error.log --with-pcre-jit --with-ipv6 --with-stream --with-stream_ssl_module --with-stream_ssl_preread_module --with-http_v2_module --without-mail_pop3_module --without-mail_imap_module --without-mail_smtp_module --with-http_stub_status_module --with-http_realip_module --with-http_addition_module --with-http_auth_request_module --with-http_secure_link_module --with-http_random_index_module --with-http_geoip_module --with-http_gzip_static_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-threads --with-dtrace-probes --with-stream --with-stream_ssl_module --with-http_ssl_module
```
这里--add-module=后面跟着的，就是 OpenResty 的 C 模块。其中，最核心的就是 lua-nginx-module 和 stream-lua-nginx-module，前者用来处理七层流量，后者用来处理四层流量。

**这些 C 模块中，有些是需要特别注意的，虽然默认编译进入了 OpenResty，但并不推荐使用**。 比如 redis2-nginx-module、redis-nginx-module 和 memc-nginx-module，它们是用来和 redis 以及 memcached 交互使用的。这些 C 库是 OpenResty 早期推荐使用的，但在 cosocket 功能加入之后，它们都已经被 lua-resty-redis 和 lua-resty-memcached 替代，处于疏于维护的状态。

OpenResty 后面也不会开发更多的 NGINX C 库，而是专注在基于 cosocket 的 Lua 库上，后者才是未来。

### lua-resty- 周边库

OpenResty 官方仓库中包含 18 个 lua-resty-* 库，涵盖 Redis、MySQL、memcached、websocket、dns、流量控制、字符串处理、进程内缓存等常用库。除了官方自带的之外，还有更多的第三方库。它们非常重要，所以下一章节，我们会花更多的篇幅来专门介绍这些周边库。

### 自己维护的 LuaJIT 分支

OpenResty 除了维护自己的 OpenSSL patch 外，还维护了自己的 [LuaJIT 分支](https://github.com/openresty/luajit2)。在 2015 年，LuaJIT 的作者 Mike Pall 宣布退休，寻找新的 LuaJIT 维护者，但 Mike 并没有找到合适的维护者，他现在主要是做 bugfix 的维护工作，新功能的开发也已经暂停，所以 OpenResty 维护着自己的 LuaJIT 分支。

相对于 Lua，LuaJIT 增加了不少独有的函数，这些函数非常重要，但知道的工程师并不多，算是 _ 半隐藏技能 _，后面我也会专门介绍。



### 测试框架

OpenResty 的测试框架是[test-nginx](https://github.com/openresty/test-nginx)，同样也是用 Perl 语言来开发的，从名字上就能看出来，它是专门用来测试 NGINX 相关的项目。OpenResty 官方的所有 C 模块和 lua-resty 库的测试案例，都是由 test-nginx 驱动的。这个框架和常见的基于断言的框架不同，是一套更强大和独立的系统，我们后面会花几节课来专门学习。事实上，有些 OpenResty 的代码贡献者也没有搞清楚这个测试框架，有时候提交的 PR 中包含了不少复杂的 C 和 Lua 代码，但对编写对应的测试案例一事，还是经常发怵。所以，如果你已经查看过一些 OpenResty 项目中/t目录里面的测试案例，却仍然一头雾水，先别急着怀疑自己，大部分人都是一样的。除了 test-nginx 之外，[mockeagain](https://github.com/openresty/mockeagain) 这个项目可以模拟慢速的网络，让程序每次只读写一个字节。对于 web 服务器来说，这是一个很有用的工具。

### 调试工具链

OpenResty 项目在如何科学和动态地调试代码上，花费了大量的精力，可以说是达到了极致。OpenResty 的作者章亦春专门写了[一篇文章](https://blog.openresty.com.cn/cn/dynamic-tracing/)，来介绍动态追踪技术。我强烈推荐给你，看完也有助于理解对应的工具链。[openresty-systemtap-toolkit](https://github.com/openresty/openresty-systemtap-toolkit) 和 [stapxx](https://github.com/openresty/stapxx) 这两个 OpenResty 的项目，都基于 systemtap 这个动态调试和追踪工具。使用 systemtap 最大的优势，便是实现活体分析，同时对目标程序完全无侵入。打个比方，systemtap，就像是我们去医院照了个 CT，无痛无感知。更棒的是，systemtap 可以生成直观的火焰图来做性能分析，后面我也会专门介绍，这里先放一个火焰图，让你直观上有个感性的认识：

![img](https://static001.geekbang.org/resource/image/dc/7f/dcc1340a7622ba1643e8d8b9347a417f.png?wh=736*492)

### 打包相关

OpenResty 在不同发行操作系统（比如 CentOS、Ubuntu、MacOS 等）版本中的打包脚本，出于更细可控力度的目的，都是手工编写的。我们在介绍安装后目录结构的时候，就已经涉及到了这些打包相关的项目：[openresty-packaging](https://github.com/openresty/openresty-packaging)  和 [home-brew](https://github.com/openresty/homebrew-brew)。如果你对此有兴趣，可以自行学习，这里我就不再赘述了。

### 工程化工具

除了上面这些比较大块儿的项目之外，OpenResty 还有一些负责工程化的工具，大都也是“深藏闺中”。比如 [openresty-devel-utils](https://github.com/openresty/openresty-devel-utils) 就是开发 OpenResty 和 NGINX 的工具集。它们也都使用 Perl 开发，其中大部分的工具都是没有文档的。但对于 OpenResty 的开发者来说，这些工具又是非常有用的。这里我先挑几个简单介绍一下。

[lj-releng](https://github.com/openresty/openresty-devel-utils/blob/master/lj-releng) 是一个简单有效的 LuaJIT 代码检测工具，类似 luacheck，可以找出全局变量等潜在的问题。reindex 从名字来看是重建索引的意思，它其实是格式化 test-nginx 测试案例的工具，可以重新排列测试案例的编号，以及去除多余的空白符。[reindex](https://github.com/openresty/openresty-devel-utils/blob/master/reindex) 可以说是 OpenResty 开发者每天都会用到的工具之一。[opsboy](https://github.com/openresty/opsboy) 也是一个深藏不露的项目，主要用于自动化部署。OpenResty 每次发布版本前，都会在 AWS EC2 集群上做完整的回归测试，详细的文档你可以参考官方文档，而这个回归测试正是由 opsboy 来部署和驱动的。

### 写在最后

今天，我们主要学习了 OpenResty 安装后的目录结构，以及背后的一些子项目。希望你学完今天的内容后，能够了解更多 OpenResty 的项目。OpenResty 已经远远超出了 NGINX 负载均衡和反向代理的范畴，实现了自己的生态，下一次我们会详细聊聊这方面。

## 04 | 如何管理第三方包？从包管理工具luarocks和opm说起

在上一节中，我们大概了解了下 OpenResty 官方的一些项目。不过，如果我们把 OpenResty 用于生产环境，显然，OpenResty 安装包自带的这些库是远远不够的，比如没有 lua-resty 库来发起 HTTP 请求，也没有办法和 Kafka 交互。那么应该怎么办呢？本节我们就来一起了解下，应该从什么渠道来找到这些第三方库。这里，我再次强调下，OpenResty 并不是 NGINX 的 fork，也不是在 NGINX 的基础上加了一些常用库重新打包，而只是把 NGINX 当作底层的网络库来使用。



当你使用 NGINX 的时候，是不会想着如何发起自定义的 HTTP 请求，以及如何与 Kafka 交互的。而在 OpenResty 的世界中，由于 cosocket 的存在，开发者可以轻松地写出 lua-resty-http 和 lua-resty-kafka ，来处理这类需求，就像你用 Python、PHP 这类的开发语言一样。另外，还有一个建议告诉你：你不应该使用任何 Lua 世界的库来解决上述问题，而是应该使用 cosocket 的 lua-resty-* 库。**Lua 世界的库很可能会带来阻塞**，让原本高性能的服务，直接下降几个数量级。这是 OpenResty 初学者的常见错误，而且并不容易觉察到。那我们怎么找到这些非阻塞的 lua-resty-* 库呢？接下来，我来为你介绍下面几种途径。

### OPM

OPM（OpenResty Package Manager）是 OpenResty 自带的包管理器，在你安装好 OpenResty 之后，就可以直接使用。我们可以试着去找找发送 http 请求的库 $ opm search http第一次查询可能会比较慢，需要几秒钟的时间。opm.openresty.org 会从 PostgreSQL 数据库中做一次查询，并把结果缓存一段时间。search 具体的返回结果比较长，我们这里只看下第一条返回值：

```
openresty/lua-resty-upload          Streaming reader and parser for HTTP file uploading based on ngx_lua cosocket
```
呃，看到这个结果，你可能会疑惑：这个 lua-resty-upload 包和发送 http 有什么关系呢？原来，OPM 做搜索的时候，是用后面的关键字同时搜索了包的名字和包的简介。这也是为什么上面的搜索会持续几秒，因为它在 PostgreSQL 里面做了字符串的全文搜索。不过，不管怎么说，这个返回并不友好。让我们修改下关键字，重新搜索下：

```
$ opm search lua-resty-http
ledgetech/lua-resty-http                          Lua HTTP client cosocket driver for OpenResty/ngx_lua
pintsized/lua-resty-http                          Lua HTTP client cosocket driver for OpenResty/ngx_lua
agentzh/lua-resty-http                            Lua HTTP client cosocket driver for OpenResty/ngx_lua
```
其实，在 OpenResty 世界中，如果你使用 cosocket 实现了一个包，那么就要使用 lua-resty- 这个前缀，算是一个不成文的规定。回过头来看刚刚的搜索结果，OPM 使用了贡献者的 GitHub 仓库地址作为包名，即 GitHub ID / repo name。上面返回了三个 lua-resty-http 第三方库，我们应该选择哪一个呢？眼尖的你，可能已经发现了 agentzh 这个 ID，没错，这就是 OpenResty 作者春哥本人。在选择这个包之前，我们看下它的 star 数和最后更新时间：只有十几个 star，最后一次更新是在 2016 年。很明显，这是个被放弃的坑。更深入地看下，pintsized/lua-resty-http 和 ledgetech/lua-resty-http 其实指向了同一个仓库。所以，不管你选哪个都是一样的。同时 [OPM 的网站](https://opm.openresty.org/) 也相对简单，没有提供包的下载次数，也没有这个包的依赖关系。你需要花费更多的时间，来甄别出到底使用哪些 lua-resty 库才是正确的选择，而这些本应该是维护者的事情。

### LUAROCKS

LuaRocks 是 OpenResty 世界的另一个包管理器，诞生在 OPM 之前。不同于 OPM 里只包含 OpenResty 相关的包，LuaRocks 里面还包含 Lua 世界的库。举个例子，LuaRocks 里面的 LuaSQL-MySQL，就是 Lua 世界中连接 MySQL 的包，并不能用在 OpenResty 中。还是以 HTTP 库为例，我们尝试用 LuaRocks 来试一试查找：

```
$ luarocks search http
```
你可以看到，也是返回了一大堆包。我们不妨再换个关键字：

```
$ luarocks search lua-resty-http
```
这次只返回了一个包。我们可以到 LuaRocks 的网站上，去看看[这个包的详细信息](https://luarocks.org/modules/pintsized/lua-resty-http)，下面是网站页面的截图：

![img](https://static001.geekbang.org/resource/image/ba/95/ba5cbaae9a7a9ab1fbd05099dc7e9695.jpg?wh=1170*838)

这里面包含了作者、License、GitHub 地址、下载次数、功能简介、历史版本、依赖等。和 OPM 不同的是，LuaRocks 并没有直接使用 GitHub 的用户信息，而是需要开发者单独在 LuaRocks 上进行注册。其实，开源的 API 网关项目 Kong，就是使用 LuaRocks 来进行包的管理，并且还把 LuaRocks 的作者收归麾下。我们接着就来简单看下，Kong 的包管理配置是怎么写的。目前 Kong 的最新版本是 1.1.1， 你可以在 https://github.com/Kong/kong 的项目下找到最新的 .rockspec 后缀的文件。

```
package = "kong"
version = "1.1.1-0"
supported_platforms = {"linux", "macosx"}
source = {
  url = "git://github.com/Kong/kong",
  tag = "1.1.1"
}
description = {
  summary = "Kong is a scalable and customizable API Management Layer built on top of Nginx.",
  homepage = "https://konghq.com",
  license = "Apache 2.0"
}
dependencies = {
  "inspect == 3.1.1",
  "luasec == 0.7",
  "luasocket == 3.0-rc1",
  "penlight == 1.5.4",
  "lua-resty-http == 0.13",
  "lua-resty-jit-uuid == 0.0.7",
  "multipart == 0.5.5",
  "version == 1.0.1",
  "kong-lapis == 1.6.0.1",
  "lua-cassandra == 1.3.4",
  "pgmoon == 1.9.0",
  "luatz == 0.3",
  "http == 0.3",
  "lua_system_constants == 0.1.3",
  "lyaml == 6.2.3",
  "lua-resty-iputils == 0.3.0",
  "luaossl == 20181207",
  "luasyslog == 1.0.0",
  "lua_pack == 1.0.5",
  "lua-resty-dns-client == 3.0.2",
  "lua-resty-worker-events == 0.3.3",
  "lua-resty-mediador == 0.1.2",
  "lua-resty-healthcheck == 0.6.0",
  "lua-resty-cookie == 0.1.0",
  "lua-resty-mlcache == 2.3.0",
......
```
通过文件你可以看到，依赖项里面掺杂了 lua-resty 库和纯 Lua 世界的库，使用 OPM 只能部分安装这些依赖项。写好配置后，使用 luarocks 的 upload 命令把这个配置上传，用户就可以用 LuaRocks 来下载并安装 Kong 了。另外，在 OpenResty 中，除了 Lua 代码外，我们还经常会调用 C 代码，这时候就需要编译才能使用。LuaRocks 是支持这么做的，你可以在 rockspec 文件中，指定 C 源码的路径和名称，这样 LuaRocks 就会帮你本地编译。而 OPM 暂时还不支持这种特性。不过，需要注意的是，OPM 和 LuaRocks 都不支持私有包。

### AWESOME-RESTY

讲了这么多包管理的内容，其实呢，即使有了 OPM 和 LuaRocks，对于 OpenResty 的 lua-resty 包，我们还是管中窥豹的状态。到底有没有地方可以让我们一览全貌呢？当然是有的，awesome-resty 这个项目，就维护了几乎所有 OpenResty 可用的包，并且都分门别类地整理好了。当你不确定是否存在适合的第三方包时，来这里“按图索骥”，可以说是最好的办法。还是以 HTTP 库为例， 在 awesome-resty 中，它自然是属于 networking 分类：

```
lua-resty-http by @pintsized — Lua HTTP client cosocket driver for OpenResty / ngx_lua
lua-resty-http by @liseen — Lua http client driver for the ngx_lua based on the cosocket API
lua-resty-http by @DorianGray — Lua HTTP client driver for ngx_lua based on the cosocket API
lua-resty-http-simple — Simple Lua HTTP client driver for ngx_lua
lua-resty-httpipe — Lua HTTP client cosocket driver for OpenResty / ngx_lua
lua-resty-httpclient — Nonblocking Lua HTTP Client library for aLiLua & ngx_lua
lua-httpcli-resty — Lua HTTP client module for OpenResty
lua-resty-requests — Yet Another HTTP Library for OpenResty
```
我们看到，这里有 8 个 lua-resty-http 的第三方库。对比一下前面的结果，我们使用 OPM 只找到 2 个，而 LuaRocks 里面更是只有 1 个。不过，如果你是选择困难症，请直接使用第一个，它和 LuaRocks 中的是同一个。而对于愿意尝试的工程师，我更推荐你用最后一个库： [lua-resty-requests](https://github.com/tokers/lua-resty-requests)，它是人类更友好的 HTTP 访问库，接口风格与 Python 中大名鼎鼎的 [Requests](http://docs.python-requests.org/en/master/) 一致。如果你跟我一样是一个 Python 爱好者，一定会喜欢上 lua-resty-requests。这个库的作者是 OpenResty 社区中活跃的 tokers，因此你可以放心使用。必须要承认，OpenResty 现有的第三方库并不完善，所以，如果你在 awesome-resty 中没有找到你需要的库，那就需要你自己来实现，比如 OpenResty 一直没有访问 Oracle  或者 SQLServer 的 lua-rsety 库。

### 写在最后

一个开源项目想要健康地发展壮大，不仅需要有硬核的技术、完善的文档和完整的测试，还需要带动更多的开发者和公司一起加入进来，形成一个生态。正如 Apache 基金会的名言：社区胜于代码。还是那句话，想把 OpenResty 代码写好，一点儿也不简单。OpenResty 还没有系统的学习资料，也没有官方的代码指南，很多的优化点的确已经写在了开源项目中，但大多数开发者却是知其然而不知其所以然。这也是我这个专栏的目的所在，希望你学习完之后，可以写出更高效的 OpenResty 代码，也可以更容易地参与到 OpenResty 相关的开源项目中来。



## 05 | [视频]opm项目导读

今天的内容，我特意安排成了视频的形式来讲解。不过，在你看视频之前，我想先问你这么几个问题：在真实的项目中，你会配置 nginx.conf，以便和 Lua 代码联动吗？你清楚 OpenResty 的代码结构该如何组织吗？

这两个问题，也是今天视频课要解决的核心内容，希望你可以先自己思考一下，并带着问题来学习今天的视频内容。同时，我会给出相应的文字介绍，方便你在听完视频内容后，及时总结与复习。下面是今天这节课的文字介绍部分。

### 今日核心

[opm](https://github.com/openresty/opm/) 是 OpenResty 中为数不多的网站类项目，而里面的代码，基本上是由 OpenResty 的作者亲自操刀完成的。很多 OpenResty 的使用者并不清楚，如何在真实的项目中去配置 nginx.conf， 以及如何组织 Lua 的代码结构。确实，在这方面可以参考的开源项目并不多，给学习使用带了不小的阻力。不过，借助今天的这个项目，你就可以克服这一点了。你将会熟悉一个 OpenResty 项目的结构和开发流程，还能看到 OpenResty 的作者是如何编写业务类 Lua 代码的。opm 还涉及到数据库的操作，它后台数据的储存，使用的是 PostgreSQL ，你可以顺便了解下 OpenResty 和数据库是如何交互的。除此之外，这个项目还涉及到一些简单的性能优化，也是为了后面专门设立的性能优化内容做个铺垫。最后，浏览完 opm 这个项目后，你可以自行看下另外一个类似的项目，那就是 OpenResty 的官方网站：https://github.com/openresty/openresty.org。

今天的课件已经上传到了我的 GitHub 上，你可以自己下载学习。链接如下：https://github.com/iresty/geektime-slides

## 06 | OpenResty 中用到的 NGINX 知识

通过前面几篇文章的介绍，相信你对 OpenResty 的轮廓已经有了一个大概的认知。下面几节课里，我会带你熟悉下 OpenResty 的两个基石：NGINX 和 LuaJIT。万丈高楼平地起，掌握些这些基础的知识，才能更好地去学习 OpenResty。今天我先来讲 NGINX。这里我只会介绍下，OpenResty 中可能会用到的一些 NGINX 基础知识，这些仅仅是 NGINX 很小的一个子集。如果你需要系统和深入学习 NGINX，可以参考陶辉老师的《NGINX 核心知识 100 讲》，这也是极客时间上评价非常高的一门课程。说到配置，其实，在 OpenResty 的开发中，我们需要注意下面几点：

要尽可能少地配置 nginx.conf；避免使用 if、set 、rewrite 等多个指令的配合；能通过 Lua 代码解决的，就别用 NGINX 的配置、变量和模块来解决。

这样可以最大限度地提高可读性、可维护性和可扩展性。下面这段 NGINX 配置，就是一个典型的反例，可以说是把配置项当成了代码来使用：

```
location ~ ^/mobile/(web/app.htm) {
            set $type $1;
            set $orig_args $args;
            if ( $http_user_Agent ~ "(iPhone|iPad|Android)" ) {
                rewrite  ^/mobile/(.*) http://touch.foo.com/mobile/$1 last;
            }
            proxy_pass http://foo.com/$type?$orig_args;
}
```

这是我们在使用 OpenResty 进行开发时需要避免的。



### NGINX 配置

我们首先来看下 NGINX 的配置文件。NGINX 通过配置文件来控制自身行为，它的配置可以看作是一个简单的 DSL。NGINX 在进程启动的时候读取配置，并加载到内存中。如果修改了配置文件，需要你重启或者重载 NGINX，再次读取后才能生效。只有 NGINX 的商业版本，才会在运行时, 以 API 的形式提供部分动态的能力。我们先来看下面这段配置，里面的内容非常简单，我相信大部分工程师都能看懂：

```
worker_processes auto;

pid logs/nginx.pid;
error_log logs/error.log notice;

worker_rlimit_nofile 65535;

events {
    worker_connections 16384;
}

http {
    server {
  listen 80;
  listen 443 ssl;

        location / {
      proxy_pass https://foo.com;
      }
    }
}

stream {
    server {
        listen 53 udp;
    }
}
```
不过，即使是简单的配置，背后也涉及到了一些很重要的基础概念。



第一，每个指令都有自己适用的上下文（Context），也就是 NGINX 配置文件中指令的作用域。最上层的是 main，里面是和具体业务无关的一些指令，比如上面出现的 worker_processes、pid 和 error_log，都属于 main 这个上下文。另外，上下文是有层级关系的，比如 location 的上下文是 server，server 的上下文是 http，http 的上下文是 main。指令不能运行在错误的上下文中，NGINX 在启动时会检测 nginx.conf 是否合法。比如我们把listen 80;  从 server 上下文换到 main 上下文，然后启动 NGINX 服务，会看到类似这样的报错：

```
"listen" directive is not allowed here ......
```
第二，NGINX 不仅可以处理 HTTP 请求 和 HTTPS 流量，还可以处理 UDP 和 TCP 流量。其中，七层的放在 HTTP 中，四层的放在 stream 中。在 OpenResty 里面， lua-nginx-module 和 stream-lua-nginx-module 分别和这俩对应。

这里有一点需要注意，**NGINX 支持的功能，OpenResty 并不一定支持，需要看 OpenResty 的版本号**。OpenResty 的版本号是和 NGINX 保持一致的，所以很容易识别。比如 NGINX 在 2018 年 3 月份发布的 1.13.10 版本中，增加了对 gRPC 的支持，但 OpenResty 在 2019 年 4 月份时的最新版本是 1.13.6.2，由此可以推断 OpenResty 还不支持 gRPC。上面 nginx.conf 涉及到的配置指令，都在 NGINX 的核心模块 [ngx_core_module](http://nginx.org/en/docs/ngx_core_module.html)、[ngx_http_core_module_](http://nginx.org/en/docs/http/ngx_http_core_module.html) 和 [ngx_stream_core_module_](http://nginx.org/en/docs/stream/ngx_stream_core_module.html) 中，你可以点击这几个链接去查看具体的文档说明。

### MASTER-WORKER 模式

了解完配置文件，我们再来看下 NGINX 的多进程模式。这里我放了一张图来表示，你可以看到，NGINX 启动后，会有一个 Master 进程和多个 Worker 进程（也可以只有一个 Worker 进程，看你如何配置）。

![img](https://static001.geekbang.org/resource/image/a7/92/a7304c2c8af0e1e6c54819c97611b992.jpg?wh=1164*916)

先来说 Master 进程，一如其名，扮演“管理者”的角色，并不负责处理终端的请求。它是用来管理 Worker 进程的，包括接受管理员发送的信号量、监控 Worker 的运行状态。当 Worker 进程异常退出时，Master 进程会重新启动一个新的 Worker 进程。Worker 进程则是“一线员工”，用来处理终端用户的请求。它是从 Master 进程 fork 出来的，彼此之间相互独立，互不影响。多进程的模式比 Apache 多线程的模式要先进很多，没有线程间加锁，也方便调试。即使某个进程崩溃退出了，也不会影响其他 Worker 进程正常工作。而 OpenResty 在 NGINX Master-Worker 模式的前提下，又增加了独有的特权进程（privileged agent）。这个进程并不监听任何端口，和 NGINX 的 Master 进程拥有同样的权限，所以可以做一些需要高权限才能完成的任务，比如对本地磁盘文件的一些写操作等。如果特权进程与 NGINX 二进制热升级的机制互相配合，OpenResty 就可以实现自我二进制热升级的整个流程，而不依赖任何外部的程序。减少对外部程序的依赖，尽量在 OpenResty 进程内解决问题，不仅方便部署、降低运维成本，也可以降低程序出错的概率。可以说，OpenResty 中的特权进程、ngx.pipe 等功能，都是出于这个目的。

### 执行阶段

执行阶段也是 NGINX 重要的特性，与 OpenResty 的具体实现密切相关。NGINX 有 11 个执行阶段，我们可以从 ngx_http_core_module.h 的源码中看到：

```
typedef enum {
    NGX_HTTP_POST_READ_PHASE = 0,

    NGX_HTTP_SERVER_REWRITE_PHASE,

    NGX_HTTP_FIND_CONFIG_PHASE,
    NGX_HTTP_REWRITE_PHASE,
    NGX_HTTP_POST_REWRITE_PHASE,

    NGX_HTTP_PREACCESS_PHASE,

    NGX_HTTP_ACCESS_PHASE,
    NGX_HTTP_POST_ACCESS_PHASE,

    NGX_HTTP_PRECONTENT_PHASE,

    NGX_HTTP_CONTENT_PHASE,

    NGX_HTTP_LOG_PHASE
} ngx_http_phases;
```
如果你想详细了解这 11 个阶段的作用，可以学习陶辉老师的视频课程，或者 NGINX 文档，这里我就不再赘述。不过，巧合的是，OpenResty 也有 11 个 *_by_lua指令，它们和 NGINX 阶段的关系如下图所示（图片来自 lua-nginx-module 文档）：

![img](https://static001.geekbang.org/resource/image/2a/73/2a05cb2a679bd1c81b44508666e70273.png?wh=1005*910)

其中，  init_by_lua 只会在 Master 进程被创建时执行，init_worker_by_lua 只会在每个 Worker 进程被创建时执行。其他的 *_by_lua 指令则是由终端请求触发，会被反复执行。所以在 init_by_lua 阶段，我们可以预先加载 Lua 模块和公共的只读数据，这样可以利用操作系统的 COW（copy on write）特性，来节省一些内存。对于业务代码来说，其实大部分的操作都可以在 content_by_lua 里面完成，但我更推荐的做法，是根据不同的功能来进行拆分，比如下面这样：

set_by_lua：设置变量；rewrite_by_lua：转发、重定向等；access_by_lua：准入、权限等；content_by_lua：生成返回内容；header_filter_by_lua：应答头过滤处理；body_filter_by_lua：应答体过滤处理；log_by_lua：日志记录。



我举一个例子来说明这样拆分的好处。我们假设，你对外提供了很多明文 API，现在需要增加自定义的加密和解密逻辑。那么请问，你需要修改所有 API 的代码吗？

```
# 明文协议版本
location /mixed {
    content_by_lua '...';       # 处理请求
}
```
当然不用。事实上，利用阶段的特性，我们只需要简单地在 access 阶段解密，在 body filter 阶段加密就可以了，原来 content 阶段的代码是不用做任何修改的：

```
# 加密协议版本
location /mixed {
    access_by_lua '...';        # 请求体解密
    content_by_lua '...';       # 处理请求，不需要关心通信协议
    body_filter_by_lua '...';   # 应答体加密
}
```

### 二进制热升级

最后，我来简单说一下 NGINX 的二进制热升级。我们知道，在你修改完 NGINX 的配置文件后，还需要重启才能生效。但在 NGINX 升级自身版本的时候，却可以做到热升级。这看上去有点儿本末倒置，不过，考虑到 NGINX 是从传统静态的负载均衡、反向代理、文件缓存起家的，这倒也可以理解。热升级通过向旧的 Master 进程发送 USR2  和 WINCH 信号量来完成。对于这两步，前者的作用，是启动新的 Master 进程；后者的作用，是逐步关闭 Worker 进程。执行完这两步后，新的 Master 和新的 Worker 就已经启动了。不过此时，旧的 Master 并没有退出。不退出的原因也很简单，如果你需要回退，依旧可以给旧的 Master 发送 HUP 信号量。当然，如果你已经确定不需要回退，就可以给旧 Master 发送 KILL 信号量来退出。

至此，大功告成，二进制的热升级就完成了。关于二进制升级，我主要就讲这些。如果你想了解这方面更详细的资料，可以查阅[官方文档](http://nginx.org/en/docs/control.html#upgrade)继续学习。

### 课外延伸

OpenResty 的作者多年前写过一个 NGINX 教程，如果你对此感兴趣，可以自己学习下。这里面的内容比较多，即使看不懂也没有关系，并不会影响你学习 OpenResty。

### 写在最后

总的来说，在 OpenResty 中用到的都是 Nginx 的基础知识，主要涉及到配置、主从进程、执行阶段等。而其他能用 Lua 代码解决的，尽量用代码来解决，而非使用 Nginx 的模块和配置，这是在学习 OpenResty 中的一个思路转变。

## 07 | 带你快速上手 Lua

在大概了解 NGINX 的基础知识后，接下来，我们就要来进一步学习 Lua 了。它是 OpenResty 中使用的编程语言，掌握它的基本语法还是很有必要的。Lua 是一个小巧精妙的脚本语言，诞生于巴西的大学实验室，这个名字在葡萄牙语里的含义是“美丽的月亮”。从作者所在的国家来看，NGINX 诞生于俄罗斯，Lua 诞生于巴西，OpenResty 诞生于中国，这三门同样精巧的开源技术都出自金砖国家，而不是欧美，也是挺有趣的一件事。回到 Lua 语言上。事实上，Lua 在设计之初，就把自己定位为一个简单、轻量、可嵌入的胶水语言，没有走大而全的路线。虽然你平常工作中可能没有直接编写 Lua 代码，但 Lua 的使用其实非常广泛。很多的网游，比如魔兽世界，都会采用 Lua 来编写插件；而键值数据库 Redis 则是内置了 Lua 来控制逻辑。另一方面，虽然 Lua 自身的库比较简单，但它可以方便地调用 C 库，大量成熟的 C 代码都可以为其所用。比如在 OpenResty 中，很多时候都需要你调用 NGINX 和 OpenSSL 的 C 函数，而这都得益于 Lua 和 LuaJIT 这种方便调用 C 库的能力。下面，我带你来快速熟悉下 Lua 的数据类型和语法，以便你后面更顺畅地学习 OpenResty。

### 环境和 hello world

我们不用专门去安装标准 Lua 5.1 之类的环境，因为 OpenResty 已经不再支持标准 Lua，而只支持 LuaJIT。这里我介绍的 Lua 语法，也是和 LuaJIT 兼容的部分，而不是基于最新的 Lua 5.3，这一点需要你特别注意。在 OpenResty 的安装目录下，你可以找到 LuaJIT 的目录和可执行文件。我这里是 Mac 环境，使用 brew 安装 OpenResty，所以你本地的路径很可能和下面的不同：

```
$ ll /usr/local/Cellar/openresty/1.13.6.2/luajit/bin/luajit
 lrwxr-xr-x  1 ming  admin    18B  4  2 14:54 /usr/local/Cellar/openresty/1.13.6.2/luajit/bin/luajit -> luajit-2.1.0-beta3
```
你也可以在系统的可执行文件目录中找到它：

```
$ which luajit
 /usr/local/bin/luajit
```
并查看 LuaJIT 的版本号：

```
$ luajit -v
 LuaJIT 2.1.0-beta2 -- Copyright (C) 2005-2017 Mike Pall. http://luajit.org/
```
查清楚这些信息后，你可以新建一个 1.lua 文件，并用 luajit 来运行其中的 hello world 代码：

```
$ cat 1.lua
print("hello world")

$ luajit 1.lua
 hello world
```
当然，你还可以使用 resty 来直接运行，要知道，它最终也是用 LuaJIT 来执行的：

```
$ resty -e 'print("hello world")'
 hello world
```
上述两种运行 hello world 的方式都是可行的。不顾对我来说，我更喜欢 resty 这种方式，因为后面很多 OpenResty 的代码，也都是通过 resty 来运行的。

### 数据类型

Lua 中的数据类型不多，你可以通过 type 函数来返回一个值的类型，比如下面这样的操作：

```
$ resty -e 'print(type("hello world")) 
 print(type(print)) 
 print(type(true)) 
 print(type(360.0))
 print(type({}))
 print(type(nil))
 '
```
会打印出如下内容：

```
 string
 function
 boolean
 number
 table
 nil
```
这几种就是 Lua 中的基本数据类型了。下面我们来简单介绍一下它们。

### 字符串

在 Lua 中，字符串是不可变的值，如果你要修改某个字符串，就等于创建了一个新的字符串。这种做法显然有利有弊：好处是即使同一个字符串出现了很多次，在内存中也只有一份；但劣势也很明显，如果你想修改、拼接字符串，会额外地创建很多不必要的字符串。我们举一个例子，来说明这个弊端。下面这段代码，是把 1 到 10 这些数字当作字符串拼接起来。对了，在 Lua 中，我们使用两个点号来表示字符串的相加：

```
$ resty -e 'local s  = ""
 for i = 1, 10 do
     s = s .. tostring(i)
 end
 print(s)'
```
这里我们循环了 10 次，但只有最后一次是我们想要的，而中间新建的 9 个字符串都是无用的。它们不仅占用了额外的空间，也消耗了不必要的 CPU 运算。当然，在后面的性能优化章节，我们会有对应的方法来解决它。另外，在 Lua 中，你有三种方式可以表达一个字符串：单引号、双引号，以及长括号（[[]]）。前面两种都比较好理解，别的语言一般也这么用，那么长括号有什么用处呢？我们看一个具体的示例：

```
$ resty -e 'print([[string has \n and \r]])'
 string has \n and \r
```
你可以看到，长括号中的字符串不会做任何的转义处理。你也许会问另外一个问题：如果上面那段字符串中包括了长括号本身，又该怎么处理呢？答案很简单，就是在长括号中间增加一个或者多个 = 符号：

```
$ resty -e 'print([=[ string has a [[]]. ]=])'
  string has a [[]].
```

### 布尔值

这个很简单，true 和 false。但在 Lua 中，只有 nil 和 false 为假，其他都为真，包括 0 和空字符串也为真。我们可以用下面的代码印证一下：

```
$ resty -e 'local a = 0
 if a then
   print("true")
 end
 a = ""
 if a then
   print("true")
 end'
```
这种判断方式和很多常见的开发语言并不一致，所以，为了避免在这种问题上出错，你可以显式地写明比较的对象，比如下面这样：

```
$ resty -e 'local a = 0
 if a == false then
   print("true")
 end
 '

```

### 数字

Lua 的 number 类型，是用双精度浮点数来实现的。值得一提的是，LuaJIT 支持 dual-number（双数）模式，也就是说， LuaJIT 会根据上下文来用整型来存储整数，而用双精度浮点数来存放浮点数。此外，LuaJIT 还支持长长整型的大整数，比如下面的例子：

```
$ resty -e 'print(9223372036854775807LL - 1)'
9223372036854775806LL
```
### 函数

函数在 Lua 中是一等公民，你可以把函数存放在一个变量中，也可以当作另外一个函数的入参和出参。比如，下面两个函数的声明是完全等价的：

```
function foo()
 end
```
```
foo = function ()
 end
```

### table

table 是 Lua 中唯一的数据结构，自然非常重要，所以后面我会用专门的章节来介绍它。我们可以先来看一个简单的示例代码：

```
$ resty -e 'local color = {first = "red"}
print(color["first"])'
 red
```
### 空值

在 Lua 中，空值就是 nil。如果你定义了一个变量，但没有赋值，它的默认值就是 nil：

```
$ resty -e 'local a
 print(type(a))'
 nil
```
当你真正进入 OpenResty 体系中后，会发现很多种空值，比如 ngx.null 等等，我们后面再细聊。Lua 的数据类型，我主要就介绍这么多，先给你打个基础。一些需要重点掌握的内容，后面的文章中我们都会继续学习。在练习、使用中学习，永远是吸收新知识最便捷的方式。

### 常用标准库

很多时候，我们学习一门语言，其实就是在学习它的标准库。Lua 比较小巧，内置的标准库并不多。而且，在 OpenResty 的环境中，Lua 标准库的优先级是很低的。对于同一个功能，我更推荐你优先使用 OpenResty 的 API 来解决，然后是 LuaJIT 的库函数，最后才是标准 Lua 的函数。OpenResty的API > LuaJIT的库函数 > 标准Lua的函数，这个优先级后面会被反复提及，它不仅关系到是否好用这一点，更会对性能产生非常大的影响。不过，尽管如此，在实际的项目开发中，我们还是不可避免会用到一些 Lua 库。这里，我挑选了几个比较常用的标准库做下介绍，如果你想要了解更多内容，可以查阅 Lua 的官方文档。

### string 库

字符串操作是我们最常用到的，也是坑最多的地方。有一个简单的原则，那就是如果涉及到正则表达式的，请一定要使用 OpenResty 提供的 ngx.re.* 来解决，不要用 Lua 的 string.* 处理。这是因为，Lua 的正则独树一帜，不符合 PCRE 的规范，我相信绝大部分工程师是玩不转的。其中 string.byte(s [, i [, j ]])，是比较常用到的一个 string 库函数，它返回字符 s[i]、s[i + 1]、s[i + 2]、······、s[j] 所对应的 ASCII 码。i 的默认值为 1，即第一个字节，j 的默认值为 i。下面我们来看一段示例代码：

```
$ resty -e 'print(string.byte("abc", 1, 3))
 print(string.byte("abc", 3)) -- 缺少第三个参数，第三个参数默认与第二个相同，此时为 3
 print(string.byte("abc"))    -- 缺少第二个和第三个参数，此时这两个参数都默认为 1
 '
```
它的输出为：

```
 979899
 99
 97
```

### table 库

在 OpenResty 的上下文中，对于 Lua 自带的 table 库，除了 table.concat 、table.sort 等少数几个函数，大部分我都不推荐使用。至于它们的细节，我们留在 LuaJIT 章节中专门来讲。这里我简单提一下table.concat 。table.concat一般用在字符串拼接的场景下，比如下面这个例子。它可以避免生成很多无用的字符串。

```
$ resty -e 'local a = {"A", "b", "C"}
 print(table.concat(a))'
```
### math 库

Lua math 库由一组标准的数学函数构成。数学库的引入，既丰富了 Lua 编程语言的功能，同时也方便了程序的编写。在 OpenResty 的实际项目中，我们很少用 Lua 去做数学方面的运算，不过其中和随机数相关的 math.random() 和 math.randomseed() 两个函数，倒是比较常用，比如下面的这段代码，它可以在指定的范围内，随机地生成两个数字。

```
$ resty -e 'math.randomseed (os.time()) 
print(math.random())
 print(math.random(100))'
```
### 虚变量

了解了这些常见的标准库，接下来，我们再来学习一个新的概念——虚变量。设想这么一个场景，当一个函数返回多个值的时候，有些返回值我们并不需要，这时候，应该怎么接收这些值呢？不知道你是怎么看待这件事的，起码对我来说，要想法设法给这些用不到的变量，去赋予有意义的名字，着实是一件很折磨人的事情。还好， Lua 中可以完美地解决这一点。Lua 提供了一个虚变量（dummy variable）的概念， 按照惯例以一个下划线来命名，用来表示丢弃不需要的数值，仅仅起到占位的作用。下面我们以 string.find 这个标准库函数为例，来看虚变量的用法。这个标准库函数会返回两个值，分别代表开始和结束的下标。如果我们只需要获取开始的下标，那么很简单，只声明一个变量来接收 string.find 的返回值即可：

```
$ resty -e 'local start = string.find("hello", "he")
 print(start)'
 1
```
但如果你只想获取结束的下标，那就必须使用虚变量了：



```
$ resty -e 'local  _, end_pos = string.find("hello", "he")
 print(end_pos)'
 2
```
除了在返回值里使用，虚变量还经常用于循环中，比如下面这个例子：

```
$ resty -e 'for _, v in ipairs({4,5,6}) do
     print(v)
 end'
 4
 5
 6
```
而当有多个返回值需要忽略时，你可以重复使用同一个虚变量。这里我就不举例子了，你可以试着自己写一个这样的示例代码吗？欢迎你把代码贴在留言区里和我分享、交流。

### 写在最后

今天，我们一起快速地学习了标准 Lua 的数据结构和语法，相信你对这门简单精巧的语言已经有了初步的了解。下节课，我会带你了解  Lua 和 LuaJIT 的关系，LuaJIT 更是 OpenResty 中的重头戏，值得我们深入挖掘。最后，我想再为你留下一道思考题。还记得这节课讲 math 库时，学过的这段代码吗？它可以在指定范围内，随机生成两个数字。

```
$ resty -e 'math.randomseed (os.time()) 
print(math.random())
 print(math.random(100))'
```
不过，你可能注意到了，这段代码是用当前时间戳作为种子的，那么这种方法是否有问题呢？又该如何生成好的种子呢？要知道，很多时候我们生成的随机数其实并不随机，并且有很大的安全隐患。

## 08 | LuaJIT分支和标准Lua有什么不同？

这节课，我们来学习下 OpenResty 的另一块基石：LuaJIT。今天主要的篇幅，我会留给 Lua 和 LuaJIT 中重要和鲜为人知的一些知识点。而更多 Lua 语言的基础知识，你可以通过搜索引擎或者 Lua 的书籍自己来学习，这里我推荐 Lua 作者编写的《Lua 程序设计》这本书。当然，在 OpenResty 中，写出正确的 LuaJIT 代码的门槛并不高，但要写出高效的 LuaJIT 代码绝非易事，这里的关键内容，我会在后面 OpenResty 性能优化部分详细介绍。我们先来看下 LuaJIT 在 OpenResty 整体架构中的位置：

![img](https://static001.geekbang.org/resource/image/cd/ef/cdef970a60810548b9c297e6959671ef.png?wh=1180*874)

前面我们提到过，OpenResty 的 worker 进程都是 fork master 进程而得到的， 其实， master 进程中的 LuaJIT 虚拟机也会一起 fork 过来。在同一个 worker 内的所有协程，都会共享这个 LuaJIT 虚拟机，Lua 代码的执行也是在这个虚拟机中完成的。这可以算是 OpenResty 的基本原理，后面课程我们再详细聊聊。今天我们先来理顺 Lua 和 LuaJIT 的关系。

### 标准 Lua 和 LuaJIT 的关系

先把重要的事情放在前面说：标准 Lua 和 LuaJIT 是两回事儿，LuaJIT 只是兼容了 Lua 5.1 的语法。

标准 Lua 现在的最新版本是 5.3，LuaJIT 的最新版本则是 2.1.0-beta3。在 OpenResty 几年前的老版本中，编译的时候，你可以选择使用标准 Lua VM ，或者 LuaJIT VM 来作为执行环境，不过，现在已经去掉了对标准 Lua 的支持，只支持 LuaJIT。LuaJIT 的语法兼容 Lua 5.1，并对 Lua 5.2 和 5.3 做了选择性支持。所以我们应该先学习 Lua 5.1 的语法，并在此基础上学习 LuaJIT 的特性。上节课我已经带你入门了 Lua 的基础语法，今天只提及 Lua 的一些特别之处。值得注意的是，OpenResty 并没有直接使用 LuaJIT 官方提供的 2.1.0-beta3 版本，而是在此基础上，扩展了自己的 fork: [openresty-luajit2]：OpenResty 维护了自己的 LuaJIT 分支，并扩展了很多独有的 API。

这些独有的 API，都是在实际开发 OpenResty 的过程中，出于性能方面的考虑而增加的。所以，我们后面提到的 LuaJIT，特指 OpenResty 自己维护的 LuaJIT 分支。

### 为什么选择 LuaJIT？

说了这么多 LuaJIT 和 Lua 的关系，你可能会纳闷儿，为什么不直接使用 Lua，而是要用自己维护的 LuaJIT 呢？其实，最主要的原因，还是 LuaJIT 的性能优势。其实标准 Lua 出于性能考虑，也内置了虚拟机，所以 Lua 代码并不是直接被解释执行的，而是先由 Lua 编译器编译为字节码（Byte Code），然后再由 Lua 虚拟机执行。而 LuaJIT 的运行时环境，除了一个汇编实现的 Lua 解释器外，还有一个可以直接生成机器代码的 JIT 编译器。开始的时候，LuaJIT 和标准 Lua 一样，Lua 代码被编译为字节码，字节码被 LuaJIT 的解释器解释执行。但不同的是，LuaJIT 的解释器会在执行字节码的同时，记录一些运行时的统计信息，比如每个 Lua 函数调用入口的实际运行次数，还有每个 Lua 循环的实际执行次数。当这些次数超过某个随机的阈值时，便认为对应的 Lua 函数入口或者对应的 Lua 循环足够热，这时便会触发 JIT 编译器开始工作。JIT 编译器会从热函数的入口或者热循环的某个位置开始，尝试编译对应的 Lua 代码路径。编译的过程，是把 LuaJIT 字节码先转换成 LuaJIT 自己定义的中间码（IR），然后再生成针对目标体系结构的机器码。

所以，**所谓 LuaJIT 的性能优化，本质上就是让尽可能多的 Lua 代码可以被 JIT 编译器生成机器码，而不是回退到 Lua 解释器的解释执行模式**。明白了这个道理，你才能理解后面学到的 OpenResty 性能优化的本质。

### Lua 特别之处

正如我们上节课介绍的一样，Lua 语言相对简单。对于有其他开发语言背景的工程师来说，注意 到 Lua 中一些独特的地方后，你就能很容易的看懂代码逻辑。接下来，我们一起来看 Lua 语言比较特别的几个地方。

#### 1. Lua 的下标从 1 开始

Lua 是我知道的唯一一个下标从 1 开始的编程语言。这一点，虽然对于非程序员背景的人来说更好理解，但却容易导致程序的 bug。下面是一个例子：

```
$ resty -e 't={100}; ngx.say(t[0])'
```
你自然期望打印出 100，或者报错说下标 0 不存在。但结果出乎意料，什么都没有打印出来，也没有报错。既然如此，让我们加上 type 命令，来看下输出到底是什么：



```
$ resty -e 't={100};ngx.say(type(t[0]))'
nil
```
原来是空值。事实上，在 OpenResty 中，对于空值的判断和处理也是一个容易让人迷惑的点，后面我们讲到 OpenResty 的时候再细聊。

### 2. 使用 .. 来拼接字符串

这一点，上节课我也提到过。和大部分语言使用 + 不同，Lua 中使用两个点号来拼接字符串：

```
$ resty -e "ngx.say('hello' .. ', world')"
hello, world
```
在实际的项目开发中，我们一般都会使用多种开发语言，而 Lua 这种不走寻常路的设计，总是会让开发者的思维，在字符串拼接的时候卡顿一下，也是让人哭笑不得。



#### 3. 只有 table 这一种数据结构

不同于 Python 这种内置数据结构丰富的语言，Lua 中只有一种数据结构，那就是 table，它里面可以包括数组和哈希表：

```
local color = {first = "red", "blue", third = "green", "yellow"}
print(color["first"])                 --> output: red
print(color[1])                         --> output: blue
print(color["third"])                --> output: green
print(color[2])                         --> output: yellow
print(color[3])                         --> output: nil
```
如果不显式地用_键值对_的方式赋值，table 就会默认用数字作为下标，从 1 开始。所以 color[1] 就是 blue。另外，想在 table 中获取到正确长度，也是一件不容易的事情，我们来看下面这些例子：

```
local t1 = { 1, 2, 3 }
print("Test1 " .. table.getn(t1))

local t2 = { 1, a = 2, 3 }
print("Test2 " .. table.getn(t2))

local t3 = { 1, nil }
print("Test3 " .. table.getn(t3))

local t4 = { 1, nil, 2 }
print("Test4 " .. table.getn(t4))
```
使用 resty 运行的结果如下：

```
Test1 3
Test2 2
Test3 1
Test4 1
```
你可以看到，除了第一个返回长度为 3 的测试案例外，后面的测试都是我们预期之外的结果。事实上，想要在 Lua 中获取 table 长度，必须注意到，只有在 table 是 _序列_ 的时候，才能返回正确的值。那什么是序列呢？首先序列是数组（array）的子集，也就是说，table 中的元素都可以用正整数下标访问到，不存在键值对的情况。对应到上面的代码中，除了 t2 外，其他的 table 都是 array。其次，序列中不包含空洞（hole），即 nil。综合这两点来看，上面的 table 中， t1 是一个序列，而 t3 和 t4 是 array，却不是序列（sequence）。到这里，你可能还有一个疑问，为什么 t4 的长度会是 1 呢？其实这是因为，在遇到 nil 时，获取长度的逻辑就不继续往下运行，而是直接返回了。不知道你完全看懂了吗？这部分确实相当复杂。那么有没有什么办法可以获取到我们想要的 table 长度呢？自然是有的，OpenResty 在这方面做了扩展，在后面专门的 table 章节我会讲到，这里先留一个悬念。

#### 4. 默认是全局变量

我想先强调一点，除非你相当确定，否则在 Lua 中声明变量时，前面都要加上 local：

```
local s = 'hello'
```
这是因为在 Lua 中，变量默认是全局的，会被放到名为 _G 的 table 中。不加 local 的变量会在全局表中查找，这是昂贵的操作。如果再加上一些变量名的拼写错误，就会造成难以定位的 bug。所以，在 OpenResty 编程中，我强烈建议你总是使用 local 来声明变量，即使在 require module 的时候也是一样：

```
-- Recommended 
local xxx = require('xxx')

-- Avoid
require('xxx')
```

#### LuaJIT

明白了 Lua 这四点特别之处，我们继续来说 LuaJIT。除了兼容 Lua 5.1 的语法并支持 JIT 外，LuaJIT 还紧密结合了 FFI（Foreign Function Interface），可以让你直接在 Lua 代码中调用外部的 C 函数和使用 C 的数据结构。下面是一个最简单的例子：

```
local ffi = require("ffi")
ffi.cdef[[
int printf(const char *fmt, ...);
]]
ffi.C.printf("Hello %s!", "world")
```
短短这几行代码，就可以直接在 Lua 中调用 C 的 printf 函数，打印出 Hello world!。你可以使用 resty 命令来运行它，看下是否成功。类似的，我们可以用 FFI 来调用 NGINX、OpenSSL 的 C 函数，来完成更多的功能。实际上，FFI 方式比传统的 Lua/C API 方式的性能更优，这也是 lua-resty-core 项目存在的意义。下一节我们就来专门讲讲 FFI 和 lua-resty-core。此外，出于性能方面的考虑，LuaJIT 还扩展了 table 的相关函数：table.new 和 table.clear。这是两个在性能优化方面非常重要的函数，在 OpenResty 的 lua-resty 库中会被频繁使用。不过，由于相关文档藏得非常深，而且没有示例代码，所以熟悉它们的开发者并不多。我们留到性能优化章节专门来讲它们。

### 写在最后

让我们来回顾下今天的内容。OpenResty 出于性能的考虑，选择了 LuaJIT 而不是标准 Lua，并且维护了自己的 LuaJIT 分支。而 LuaJIT 基于 Lua 5.1 的语法，并选择性地兼容了部分 Lua5.2 和 Lua5.3 的语法，形成了自己的体系。至于你需要掌握的 Lua 语法，在下标、字符串拼接、数据结构和变量上，都有自己鲜明的特点，在写代码的时候你应该特别留意。

## 09 | 为什么 lua-resty-core 性能更高一些？

前面两节课我们说了，Lua 是一种嵌入式开发语言，核心保持了短小精悍，你可以在 Redis、NGINX 中嵌入 Lua，来帮助你更灵活地完成业务逻辑。同时，Lua 也可以调用已有的 C 函数和数据结构，避免重复造轮子。在 Lua 中，你可以用 Lua C API 来调用 C 函数，而在 LuaJIT 中还可以使用 FFI。对 OpenResty 而言：在核心的 lua-nginx-module 中，调用 C 函数的 API，都是使用 Lua C API 来完成的；而在 lua-resty-core 中，则是把 lua-nginx-module 已有的部分 API，使用 FFI 的模式重新实现了一遍。

看到这里你估计纳闷了：为什么要用 FFI 重新实现一遍？别着急，让我们以 ngx.base64_decode 这个很简单的 API 为例，一起看下 Lua C API 和 FFI 的实现有何不同之处，这样你也可以对它们的性能有个直观的认识。

### Lua CFunction
我们先来看下， lua-nginx-module 中用 Lua C API 是如何实现的。我们在项目的代码中搜索 decode_base64，可以找到它的代码实现在 ngx_http_lua_string.c 中：


```
lua_pushcfunction(L, ngx_http_lua_ngx_decode_base64);
lua_setfield(L, -2, "decode_base64");
```
上面的代码看着就头大，不过还好，我们不用深究那两个 lua_ 开头的函数，以及它们参数的具体作用，只需要知道一点——这里注册了一个 CFunction：ngx_http_lua_ngx_decode_base64， 而它与 ngx.base64_decode 这个对外暴露的 API 是对应关系。我们继续“按图索骥”，在这个 C 文件中搜索 ngx_http_lua_ngx_decode_base64，它定义在文件的开始位置：

```
static int ngx_http_lua_ngx_decode_base64(lua_State *L);
```
对于那些能够被 Lua 调用的 C 函数来说，它的接口必须遵循 Lua 要求的形式，也就是 typedef int (*lua_CFunction)(lua_State* L)。它包含的参数是 lua_State 类型的指针 L ；它的返回值类型是一个整型，表示返回值的数量，而非返回值自身。它的实现如下（这里我已经去掉了错误处理的代码）：
```
static int
 ngx_http_lua_ngx_decode_base64(lua_State *L)
 {
     ngx_str_t p, src;
 
    src.data = (u_char *) luaL_checklstring(L, 1, &src.len);
 
     p.len = ngx_base64_decoded_length(src.len);
 
     p.data = lua_newuserdata(L, p.len);
 
     if (ngx_decode_base64(&p, &src) == NGX_OK) {
         lua_pushlstring(L, (char *) p.data, p.len);
 
     } else {
         lua_pushnil(L);
     }
 
     return 1;
 }
```
这段代码中，最主要的是 ngx_base64_decoded_length 和 ngx_decode_base64， 它们都是 NGINX 自身提供的 C 函数。我们知道，用 C 编写的函数，无法把返回值传给 Lua 代码，而是需要通过栈，来传递 Lua 和 C 之间的调用参数和返回值。这也是为什么，会有很多我们一眼无法看懂的代码。同时，这些代码也不能被 JIT 跟踪到，所以对于 LuaJIT 而言，这些操作是处于黑盒中的，没法进行优化。

### LuaJIT FFI
而 FFI 则不同。FFI 的交互部分是用 Lua 实现的，这部分代码可以被 JIT 跟踪到，并进行优化；当然，代码也会更加简洁易懂。我们还是以 base64_decode为例，它的 FFI 实现分散在两个仓库中： lua-resty-core 和 lua-nginx-module。我们先来看下前者里面[实现的代码](https://github.com/openresty/lua-resty-core/blob/master/lib/resty/core/base64.lua#L72)：

```
ngx.decode_base64 = function (s)
     local slen = #s
     local dlen = base64_decoded_length(slen)
     
     local dst = get_string_buf(dlen)
     local pdlen = get_size_ptr()
     local ok = C.ngx_http_lua_ffi_decode_base64(s, slen, dst, pdlen)
     if ok == 0 then
         return nil
     end
     return ffi_string(dst, pdlen[0])
 end
```
你会发现，相比 CFunction，FFI 实现的代码清爽了很多，它具体的实现是 lua-nginx-module 仓库中的ngx_http_lua_ffi_decode_base64，如果你对这里感兴趣，可以自己去查看这个函数的实现，特别简单，这里我就不贴代码了。不过，细心的你，是否从上面的代码片段中，发现函数命名的一些规律了呢？没错，OpenResty 中的函数都是有命名规范的，你可以通过命名推测出它的用处。比如：

没错，OpenResty 中的函数都是有命名规范的，你可以通过命名推测出它的用处。比如：ngx_http_lua_ffi_ ，是用 FFI 来处理 NGINX HTTP 请求的 Lua 函数；ngx_http_lua_ngx_ ，是用 Cfunction 来处理 NGINX HTTP 请求的 Lua 函数；其他 ngx_ 和 lua_ 开头的函数，则分别属于 NGINX 和 Lua 的内置函数。

更进一步，OpenResty 中的 C 代码，也有着严格的代码规范，这里我推荐阅读[官方的 C 代码风格指南](https://openresty.org/cn/c-coding-style-guide.html)。对于有意学习 OpenResty 的 C 代码并提交 PR 的开发者来说，这是必备的一篇文档。否则，即使你的 PR 写得再好，也会因为代码风格问题被反复评论并要求修改。关于 FFI 更多的 API 和细节，推荐你阅读 LuaJIT [官方的教程](http://luajit.org/ext_ffi_tutorial.html) 和 [文档](http://luajit.org/ext_ffi_api.html)。技术专栏并不能代替官方文档，我也只能在有限的时间内帮你指出学习的路径，少走一些弯路，硬骨头还是需要你自己去啃的。


### LuaJIT FFI GC
使用 FFI 的时候，我们可能会迷惑：在 FFI 中申请的内存，到底由谁来管理呢？是应该我们在 C 里面手动释放，还是 LuaJIT 自动回收呢？这里有个简单的原则：LuaJIT 只负责由自己分配的资源；而 ffi.C 是 C 库的命名空间，所以，使用 ffi.C 分配的空间不由 LuaJIT 负责，需要你自己手动释放。举个例子，比如你使用 ffi.C.malloc 申请了一块内存，那你就需要用配对的 ffi.C.free 来释放。LuaJIT 的官方文档中有一个对应的示例：

```
local p = ffi.gc(ffi.C.malloc(n), ffi.C.free)
 ...
 p = nil -- Last reference to p is gone.
 -- GC will eventually run finalizer: ffi.C.free(p)
```
这段代码中，ffi.C.malloc(n) 申请了一段内存，同时 ffi.gc 就给它注册了一个析构的回调函数 ffi.C.free。这样一来，p 这个 cdata 在被 LuaJIT GC 的时候，就会自动调用 ffi.C.free，来释放 C 级别的内存。而 cdata 是由 LuaJIT 负责 GC 的 ，所以上述代码中的 p 会被 LuaJIT 自动释放。这里要注意，如果你要在 OpenResty 中申请大块的内存，我更推荐你用 ffi.C.malloc 而不是 ffi.new。原因也很明显：

ffi.new 返回的是一个 cdata，这部分内存由 LuaJIT 管理；LuaJIT GC 的管理内存是有上限的，OpenResty 中的 LuaJIT 并未开启 GC64 选项，所以单个 worker 内存的上限只有 2G。一旦超过 LuaJIT 的内存管理上限，就会导致报错。


在使用 FFI 的时候，我们还需要特别注意内存泄漏的问题。不过，凡人皆会犯错，只要是人写的代码，百密一疏，总会出现 bug。那么，有没有什么工具可以检测内存泄漏呢？这时候，OpenResty 强大的周边测试和调试工具链就派上用场了。我们先来说说测试。在 OpenResty 体系中，我们使用 Valgrind 来检测内存泄漏问题。前面课程我们提到过的测试框架 test::nginx，有专门的内存泄漏检测模式去运行单元测试案例集，你只需要设置环境变量 TEST_NGINX_USE_VALGRIND=1 即可。OpenResty 的官方项目在发版本之前，都会在这个模式下完整回归，后面的测试章节中我们再详细介绍。而 OpenResty 的 CLI resty 也有 --valgrind 选项，方便你单独运行某段 Lua 代码，即使你没有写测试案例也是没问题的。再来看调试工具。

OpenResty 提供基于 [systemtap](https://github.com/openresty/stapxx) 的扩展，来对 OpenResty 程序进行活体的动态分析。你可以在这个项目的工具集中，搜索 gc 这个关键字，会看到 lj-gc 和 lj-gc-objs 这两个工具。而对于 core dump 这种离线分析，OpenResty 提供了 [GDB 的工具集](https://github.com/openresty/openresty-gdb-utils)，同样你可以在里面搜索 gc，找到 lgc、lgcstat 和 lgcpath 三个工具。这些调试工具的具体用法，我们会在后面的调试章节中详细介绍，你先有个印象即可。这样，你遇到内存问题就不会“病急乱投医“，毕竟 OpenResty 有专门的工具集，帮你定位和解决这些问题。


### lua-resty-core
从上面的比较中，我们可以看到，FFI 的方式不仅代码更简洁，而且可以被 LuaJIT 优化，显然是更优的选择。其实现实也是如此，实际上，CFunction 的实现方式已经被 OpenResty 废弃，相关的实现也从代码库中移除了。现在新的 API，都通过 FFI 的方式，在 lua-resty-core 仓库中实现。在 OpenResty 2019 年 5 月份发布的 1.15.8.1 版本前，lua-resty-core 默认是不开启的，而这不仅会带来性能损失，更严重的是会造成潜在的 bug。所以，我强烈推荐还在使用历史版本的用户，都手动开启 lua-resty-core。你只需要在 init_by_lua 阶段，增加一行代码就可以了：


```
require "resty.core"
```
当然，姗姗来迟的 1.15.8.1 版本中，已经增加了 lua_load_resty_core 指令，默认开启了 lua-resty-core。我个人感觉，OpenResty 对于 lua-resty-core 的开启还是过于谨慎了，开源项目应该尽早把类似的功能设置为默认开启。lua-resty-core 中不仅重新实现了部分 lua-nginx-module 项目中的 API，比如 ngx.re.match、ngx.md5 等，还实现了不少新的 API，比如 ngx.ssl、ngx.base64、ngx.errlog、ngx.process、ngx.re.split、ngx.resp.add_header、ngx.balancer、ngx.semaphore 等等，我们在后面的 OpenResty API 章节中会介绍到。

### 写在最后
讲了这么多内容，最后我还是想说，FFI 虽然好，却也并不是性能银弹。它之所以高效，主要原因就是可以被 JIT 追踪并优化。如果你写的 Lua 代码不能被 JIT，而是需要在解释模式下执行，那么 FFI 的效率反而会更低。那么到底有哪些操作可以被 JIT，哪些不能呢？怎样才可以避免写出不能被 JIT 的代码呢？下一节我来揭晓这个问题。最后，给你留一个需要动手的作业题：你可以找一两个 lua-nginx-module 和 lua-resty-core 中都存在的 API，然后性能测试比较一下两者的差异吗？你可以看下 FFI 的性能提升到底有多大。

## 10 | JIT编译器的死穴：为什么要避免使用 NYI ？

上一节，我们一起了解了 LuaJIT 中的 FFI。如果你的项目中只用到了 OpenResty 提供的 API，没有自己调用 C 函数的需求，那么 FFI 对你而言并没有那么重要，你只需要确保开启了 lua-resty-core 即可。但我们今天要讲的 LuaJIT 中 NYI，却是每一个使用 OpenResty 的工程师都逃避不了的关键问题，它对于性能的影响举足轻重。

**你可以很快使用 OpenResty 写出逻辑正确的代码，但不明白 NYI，你就不能写出高效的代码，无法发挥 OpenResty 真正的威力。这两者的性能差距，至少是一个数量级的。**

### 什么是 NYI？
那究竟什么是 NYI 呢？先回顾下我们之前提到过的一个知识点：**LuaJIT 的运行时环境，除了一个汇编实现的 Lua 解释器外，还有一个可以直接生成机器代码的 JIT 编译器**。LuaJIT 中 JIT 编译器的实现还不完善，有一些原语它还无法编译，因为这些原语实现起来比较困难，再加上 LuaJIT 的作者目前处于半退休状态。这些原语包括常见的 pairs() 函数、unpack() 函数、基于 Lua CFunction 实现的 Lua C 模块等。这样一来，当 JIT 编译器在当前代码路径上遇到它不支持的操作时，便会退回到解释器模式。而 JIT 编译器不支持的这些原语，其实就是我们今天要讲的 NYI，全称为 Not Yet Implemented。LuaJIT 的官网上有[这些 NYI 的完整列表](http://wiki.luajit.org/NYI)，建议你仔细浏览一遍。当然，目的不是让你背下这个列表的内容，而是让你要在写代码的时候有意识地提醒自己。下面，我截取了 NYI 列表中 string 库的几个函数：

![img](https://static001.geekbang.org/resource/image/1b/91/1b15183f8282ce235379281a961bd991.png?wh=1020*372)

其中，string.byte 对应的能否被编译的状态是 yes，表明可以被 JIT，你可以放心大胆地在代码中使用。string.char 对应的编译状态是 2.1，表明从 LuaJIT 2.1 开始支持。我们知道，OpenResty 中的 LuaJIT 是基于 LuaJIT 2.1 的，所以你也可以放心使用。string.dump 对应的编译状态是 never，即不会被 JIT，会退回到解释器模式。目前来看，未来也没有计划支持这个原语。string.find 对应的编译状态是 2.1 partial，意思是从 LuaJIT 2.1 开始部分支持，后面的备注中写的是 只支持搜索固定的字符串，不支持模式匹配。所以对于固定字符串的查找，你使用 string.find 是可以被 JIT 的。我们自然应该避免使用 NYI，让更多的代码可以被 JIT 编译，这样性能才能得到保证。但在现实环境中，我们有时候不可避免要用到一些 NYI 函数的功能，这时又该怎么办呢？

### NYI 的替代方案
其实，不用担心，大部分 NYI 函数我们都可以敬而远之，通过其他方式来实现它们的功能。接下来，我挑选了几个典型的 NYI 来讲解，带你了解不同类型的 NYI 替代方案。这样，其他的 NYI 你也可以自己触类旁通。


#### 1.string.gsub() 函数
第一个我们来看 string.gsub() 函数。它是 Lua 内置的字符串操作函数，作用是做全局的字符串替换，比如下面这个例子：

```
$ resty -e 'local new = string.gsub("banana", "a", "A"); print(new)'
bAnAnA
```
这个函数是一个 NYI 原语，无法被 JIT 编译。我们可以尝试在 OpenResty 自己的 API 中寻找替代函数，但对于大多数人来说，记住所有的 API 和用法是不现实的。所以在平时开发中，我都会打开 lua-nginx-module 的 [GitHub 文档页面](https://github.com/openresty/lua-nginx-module)。

比如，针对刚刚的这个例子，我们可以用 gsub 作为关键字，在文档页面中搜索，这时ngx.re.gsub 就会映入眼帘。细心的同学可能会问，这里为什么不用之前推荐的 restydoc 工具，来搜索 OpenResty API 呢？你可以尝试下用它来搜索 gsub：


```
$ restydoc -s gsub
```
看到了吧，这里并没有返回我们期望的 ngx.re.gsub，而是显示了 Lua 自带的函数。事实上，现阶段而言， restydoc 返回的是唯一的精准匹配的结果，所以它更适合在你明确知道 API 名字的前提下使用。至于模糊的搜索，还是要自己手动在文档中进行。回到刚刚的搜索结果，我们看到，ngx.re.gsub 的函数定义如下：


newstr, n, err = ngx.re.gsub(subject, regex, replace, options?)

这里，函数参数和返回值的命名都带有具体的含义。其实，在 OpenResty 中，我并不推荐你写很多注释，大多数时候，一个好的命名胜过好几行注释。对于不熟悉 OpenResty 正则体系的工程师而言，看到最后的变参 options ，你可能会比较困惑。不过，这个变参的解释，并不在此函数中，而是在 ngx.re.match 函数的文档中。通过查看参数 options 的文档，你会发现，只要我们把它设置为 jo，就开启了 PCRE 的 JIT。这样，使用 ngx.re.gsub 的代码，既可以被 LuaJIT 进行 JIT 编译，也可以被 PCRE JIT 进行 JIT 编译。具体的文档内容，我就不再赘述了。不过这里我想强调一点——在翻看文档时，我们一定要有打破砂锅问到底的精神。OpenResty 的文档其实非常完善，仔细阅读文档，就可以解决你大部分的问题。


#### 2.string.find() 函数
和 string.gsub 不同的是，string.find 在 plain 模式（即固定字符串的查找）下，是可以被 JIT 的；而带有正则这种的字符串查找，string.find 并不能被 JIT ，这时就要换用 OpenResty 自己的 API，也就是 ngx.re.find 来完成。所以，当你在 OpenResty 中做字符串查找时，首先一定要明确区分，你要查找的是固定的字符串，还是正则表达式。如果是前者，就要用 string.find，并且记得把最后的 plain 设置为 true：

```
string.find("foo bar", "foo", 1, true)
```
如果是后者，你应该用 OpenResty 自己的 API，并开启 PCRE 的 JIT 选项：
```
ngx.re.find("foo bar", "^foo", "jo")
```
其实，**这里更适合做一层封装，并把优化选项默认打开，不要让最终的使用者知道这么多细节**。这样，对外就是统一的字符串查找函数了。你可以感受到，有时候选择太多、太灵活并不是一件好事。


#### 3.unpack() 函数
第三个我们来看 unpack() 函数。unpack() 也是要避免使用的函数，特别是不要在循环体中使用。你可以改用数组的下标去访问，比如下面代码的这个例子：

```
$ resty -e '
 local a = {100, 200, 300, 400}
 for i = 1, 2 do
    print(unpack(a))
 end'

$ resty -e 'local a = {100, 200, 300, 400}
 for i = 1, 2 do
    print(a[1], a[2], a[3], a[4])
 end'
```
让我们再深究一下 unpack，这次我们可以用restydoc 来搜索一下：

```
$ restydoc -s unpack
```
从 unpack 的文档中，你可以看出，unpack (list [, i [, j]]) 和 return list[i], list[i+1], , list[j] 是等价的，你可以把 unpack 看成一个语法糖。这样，你完全可以用数组下标的方式来访问，以免打断 LuaJIT 的 JIT 编译。


#### 4.pairs() 函数
最后我们来看遍历哈希表的 pairs() 函数，它也不能被 JIT 编译。不过非常遗憾，这个并没有等价的替代方案，你只能尽量避免使用，或者改用数字下标访问的数组，特别是在热代码路径上不要遍历哈希表。这里我解释一下代码热路径，它的意思是，这段代码会被返回执行很多次，比如在一个很大的循环里面。

说完这四个例子，我们来总结一下，要想规避 NYI 原语的使用，你需要注意下面这两点：请优先使用 OpenResty 提供的 API，而不是 Lua 的标准库函数。这里要牢记， Lua 是嵌入式语言，我们实际上是在 OpenResty 中编程，而不是 Lua。如果万不得已要使用 NYI 原语，请一定确保它没有在代码热路径上。

### 如何检测 NYI？
讲了这么多 NYI 的规避方案，都是在教你该怎么做。不过，如果到这里戛然而止，那就不太符合 OpenResty 奉行的一个哲学：

**能让机器自动完成的，就不要人工参与。**

人不是机器，总会有疏漏，能够自动化地检测代码中使用到的 NYI，才是工程师价值的一个重要体现。这里我推荐，LuaJIT 自带的 jit.dump 和 jit.v 模块。它们都可以打印出 JIT 编译器工作的过程。前者会输出非常详细的信息，可以用来调试 LuaJIT 本身，你可以参考[它的源码](https://github.com/openresty/luajit2/blob/v2.1-agentzh/src/jit/dump.lua)来做更深入的了解；后者的输出比较简单，每行对应一个 trace，通常用来检测是否可以被 JIT。具体应该怎么操作呢？我们可以先在 init_by_lua 中，添加以下两行代码：

```
local v = require "jit.v"
v.on("/tmp/jit.log")
```
然后，运行你自己的压力测试工具，或者跑几百个单元测试集，让 LuaJIT 足够热，触发 JIT 编译。这些都完成后，再来检查 /tmp/jit.log 的结果。当然，这个方法相对比较繁琐，如果你想要简单验证的话， 使用 resty 就足够了，这个 OpenResty 的 CLI 带有相关选项：
```
$resty -j v -e 'for i=1, 1000 do
      local newstr, n, err = ngx.re.gsub("hello, world", "([a-z])[a-z]+", "[$0,$1]", "i")
 end'
 [TRACE   1 (command line -e):1 stitch C:107bc91fd]
 [TRACE   2 (1/stitch) (command line -e):2 -> 1]
```
其中，resty 的 -j 就是和 LuaJIT 相关的选项；后面的值为 dump 和 v，就对应着开启 jit.dump 和 jit.v 模式。在 jit.v 模块的输出中，每一行都是一个成功编译的 trace 对象。刚刚是一个能够被 JIT 的例子，而如果遇到 NYI 原语，输出里面就会指明 NYI，比如下面这个 pairs 的例子：

```
$resty -j v -e 'local t = {}
 for i=1,100 do
     t[i] = i
 end
 
 for i=1, 1000 do
     for j=1,1000 do
         for k,v in pairs(t) do
             --
         end
     end
 end'
```
它就不能被 JIT，所以结果里，指明了第 8 行中有 NYI 原语。

```
 [TRACE   1 (command line -e):2 loop]
 [TRACE --- (command line -e):7 -- NYI: bytecode 72 at (command line -e):8]
```
### 写在最后
这是我们第一次用比较多的篇幅来谈及 OpenResty 的性能问题。看完这些关于 NYI 的优化，不知道你有什么感想呢？可以留言说说你的看法。最后，给你留一道思考题。在讲 string.find() 函数的替代方案时，我有提到过，那里其实更适合做一层封装，并默认打开优化选项。那么，这个任务就交给你来小试牛刀了。


## 11 | 剖析Lua唯一的数据结构table和metatable特性

今天我们一起学习下 LuaJIT 中唯一的数据结构：table。和其他具有丰富数据结构的脚本语言不同，LuaJIT 中只有 table 这一个数据结构，并没有区分开数组、哈希、集合等概念，而是揉在了一起。让我们先温习下之前提到过的一个例子：

```
local color = {first = "red", "blue", third = "green", "yellow"}
print(color["first"])                 --> output: red
print(color[1])                         --> output: blue
print(color["third"])                --> output: green
print(color[2])                         --> output: yellow
print(color[3])                         --> output: nil
```
这个例子中， color 这个 table 包含了数组和哈希，并且可以互不干扰地进行访问。比如，你可以用 ipairs 函数，只遍历数组部分的内容：

```
$ resty -e 'local color = {first = "red", "blue", third = "green", "yellow"}
for k, v in ipairs(color) do
     print(k)
end
'
```
table 的操作是如此重要，以至于 LuaJIT 对标准 Lua 5.1 的 table 库做了扩展，而 OpenResty 又对 LuaJIT 的 table 库做了更进一步的扩展。下面，我们就一起来分别看下这些库函数。


### table 库函数
先来看标准 table 库函数。Lua 5.1 中自带的 table 库函数并不多，我们可以大概浏览一遍。


#### table.getn 获取元素个数
我们在 标准 Lua 和 LuaJIT 章节中曾经提到过，想正确地获取到 table 所有元素的个数，在 LuaJIT 中是一个老大难问题。对于序列，你用table.getn 或者一元操作符 # ，就可以正确返回元素的个数。比如下面这个例子，就会返回我们预期中的 3。
```
$ resty -e 'local t = { 1, 2, 3 }
print(table.getn(t)) '
```
而对于不是序列的 table，就无法返回正确的值。比如第二个例子，返回的就是 1。
```
$ resty -e 'local t = { 1, a = 2 }
print(#t) '
```
不过，幸运的是，这种难以理解的函数，已经被 LuaJIT 的扩展替代，后面我们会提到。所以在 OpenResty 的环境下，除非你明确知道，你正在获取序列的长度，否则请不要使用函数 table.getn 和一元操作符 # 。另外，table.getn 和一元操作符 # 并不是 O(1) 的时间复杂度，而是 O(n)，这也是尽量避免使用它们的另外一个理由。

#### table.remove 删除指定元素
第二个我们来看table.remove 函数，它的作用是在 table 中根据下标来删除元素，也就是说只能删除 table 中数组部分的元素。我们还是来看color的例子：

```
$ resty -e 'local color = {first = "red", "blue", third = "green", "yellow"}
  table.remove(color, 1)
  for k, v in pairs(color) do
      print(v)
  end'
```
这段代码会把下标为 1 的 blue 删除掉。你可能会问，那该如何删除 table 中的哈希部分呢？也很简单，把 key 对应的 value 设置为 nil 即可。这样，color这个例子中，third 对应的green就被删除了。
```
$ resty -e 'local color = {first = "red", "blue", third = "green", "yellow"}
  color.third = nil
  for k, v in pairs(color) do
      print(v)
  end'
```
#### table.concat 元素拼接函数
第三个我们来看table.concat 元素拼接函数。它可以按照下标，把 table 中的元素拼接起来。既然这里又是根据下标来操作的，那么显然还是针对 table 的数组部分。同样还是color这个例子：
```
$ resty -e 'local color = {first = "red", "blue", third = "green", "yellow"}
print(table.concat(color, ", "))'
```
使用table.concat函数后，它输出的是 blue, yellow，哈希的部分被跳过了。另外，这个函数还可以指定下标的起始位置来做拼接，比如下面这样的写法：

```
$ resty -e 'local color = {first = "red", "blue", third = "green", "yellow", "orange"}
print(table.concat(color, ", ", 2, 3))'
```
这次输出是 yellow, orange，跳过了 blue。你可能觉得这些操作还挺简单的，不过，我要说的是，函数不可貌相，海水不可。千万不要小看这个看上去没有太大用处的函数，在做性能优化时，它却会有意想不到的作用，也是我们后面性能优化章节中的主角之一。

#### table.insert 插入一个元素
最后我们来看table.insert 函数。它可以下标插入一个新的元素，自然，影响的还是 table 的数组部分。还是用color例子来说明：

```
$ resty -e 'local color = {first = "red", "blue", third = "green", "yellow"}
table.insert(color, 1,  "orange")
print(color[1])
'
```
你可以看到， color 的第一个元素变为了 orange。当然，你也可以不指定下标，这样就会默认插入队尾。这里我必须说明的是，table.insert 虽然是一个很常见的操作，但性能并不乐观。如果你不是根据指定下标来插入元素，那么每次都需要调用 LuaJIT 的 lj_tab_len 来获取数组的长度，以便插入队尾。正如我们在 table.getn 中提到的，获取 table 长度的时间复杂度为 O(n) 。所以，对于table.insert 操作，我们应该尽量避免在热代码中使用，比如：

```
local t = {}
for i = 1, 10000 do
     table.insert(t, i)
end
```
### LuaJIT 的 table 扩展函数
接下来我们来看 LuaJIT 的 table 扩展函数。LuaJIT 在标准 Lua 的基础上，扩展了两个很有用的 table 函数，分别用来新建和清空一个 table，下面我具体来介绍一下。

#### table.new(narray, nhash) 新建 table
第一个是table.new(narray, nhash) 函数。这个函数，会预先分配好指定的数组和哈希的空间大小，而不是在插入元素时自增长，这也是它的两个参数 narray 和 nhash 的含义。自增长是一个代价比较高的操作，会涉及到空间分配、resize 和 rehash 等，我们应该尽量避免。这里注意，table.new 的文档并没有出现在 LuaJIT 的官网，而是深藏在 GitHub 项目的[扩展文档](https://github.com/openresty/luajit2/blob/v2.1-agentzh/doc/extensions.html)中，即使你用谷歌也难觅其踪迹，所以知道的工程师并不多。下面是一个简单的例子，我来带你看下它该怎么用。首先要说明，这个函数是扩展出来的，所以在使用它之前，你需要先 require 一下：

```
local new_tab = require "table.new"
local t = new_tab(100, 0)
for i = 1, 100 do
   t[i] = i
end
```
你可以看到，这段代码新建了一个 table，里面包含 100 个数组元素和 0 个哈希元素。当然，你也可以根据实际需要，新建一个同时包含 100 个数组元素和 50 个 哈希元素的 table，这都是合法的：


```
local t = new_tab(100, 50)
```
另外，超出预设的空间大小，也可以正常使用，只不过性能会退化，也就失去了使用 table.new 的意义。比如下面这个例子，我们预设大小为 100，而实际上却使用了 200：

```
local new_tab = require "table.new"
local t = new_tab(100, 0)
for i = 1, 200 do
   t[i] = i
end
```
所以，你需要根据实际场景，来预设好 table.new 中数组和哈希空间的大小，这样才能在性能和内存占用上找到一个平衡点。


### table.clear() 清空 table
第二个我们来看清空函数table.clear() 。它用来清空某个 table 里的所有数据，但并不会释放数组和哈希部分占用的内存。所以，它在循环利用 Lua table 时非常有用，可以避免反复创建和销毁 table 的开销。


```
$ resty -e 'local clear_tab =require "table.clear"
local color = {first = "red", "blue", third = "green", "yellow"}
clear_tab(color)
for k, v in pairs(color) do
     print(k)
end'
```
不过，事实上，能使用这个函数的场景并不算多，大多数情况下，我们还是应该把这个任务交给 LuaJIT GC 去完成。


### OpenResty 的 table 扩展函数
开头我提到过，OpenResty 自己维护的 LuaJIT 分支，也对 table 做了扩展，它[新增了几个 API](https://github.com/openresty/luajit2/#new-api)：table.isempty、table.isarray、 table.nkeys 和 table.clone。

需要注意的是，在使用这几个新增的 API 前，请记住检查你使用的 OpenResty 的版本，这些 API 大都只能在 OpenResty 1.15.8.1 之后的版本中使用。这是因为， OpenResty 在 1.15.8.1 版本之前，已经有一年左右没有发布新版本了，而这些 API 是在这个发布间隔中新增的。文章中我已经附上了链接，这里我就只用 table.nkeys 来举例说明下，其他的三个 API 从命名上来说都非常容易理解，你自己翻阅 GitHub 上的文档就可以明白了。不得不说，OpenResty 的文档质量非常高，其中包含了代码示例、能否被 JIT、需要注意的事项等，比起 Lua 和 LuaJIT 的文档，着实高了好几个数量级。好的，回到table.nkeys函数上，它的命名可能会让你迷惑，不过，它实际上是获取 table 长度的函数，返回的是 table 的元素个数，包括数组和哈希部分的元素。因此，我们可以用它来替代 table.getn，比如下面这样来用：


```
local nkeys = require "table.nkeys"

print(nkeys({}))  -- 0
print(nkeys({ "a", nil, "b" }))  -- 2
print(nkeys({ dog = 3, cat = 4, bird = nil }))  -- 2
print(nkeys({ "a", dog = 3, cat = 4 }))  -- 3
```
### 元表
讲完了 table 函数，我们再来看下由 table 引申出来的 元表（metatable）。元表是 Lua 中独有的概念，在实际项目中的使用非常广泛。不夸张地说，在几乎所有的 lua-resty-* 库中，你都能看到它的身影。元表的表现行为类似于操作符重载，比如我们可以重载 __add，来计算两个 Lua 数组的并集；或者重载 __tostring，来定义转换为字符串的函数。而 Lua 提供了两个处理元表的函数：

第一个是setmetatable(table, metatable), 用于为一个 table 设置元表；第二个是getmetatable(table)，用于获取 table 的元表。

介绍了这么半天，你可能更关心它的作用，我们接着就来看下元表具体有什么用处。下面是一段真实项目里的代码：

```
$ resty -e ' local version = {
  major = 1,
  minor = 1,
  patch = 1
  }
version = setmetatable(version, {
    __tostring = function(t)
      return string.format("%d.%d.%d", t.major, t.minor, t.patch)
    end
  })
  print(tostring(version))
'
```
我们首先定义了一个 名为 version的 table ，你可以看到，这段代码的目的，是想把 version 中的版本号打印出来。但是，我们并不能直接打印 version，你可以试着操作一下，就会发现，直接打印的话，只会输出这个 table 的地址。

```
print(tostring(version))
```
所以，我们需要自定义这个 table 的字符串转换函数，也就是 __tostring，到这一步也就是元表的用武之地了。我们用 setmetatable ，重新设置 version 这个 table 的 __tostring 方法，就可以打印出版本号: 1.1.1。其实，除了 __tostring 之外，在实际项目中，我们还经常重载元表中的以下两个元方法（metamethod）。**其中一个是__index**。我们在 table 中查找一个元素时，首先会直接从 table 中查询，如果没有找到，就继续到元表的 __index 中查询。比如下面这个例子，我们把 patch 从 version 这个 table 中去掉：


```
$ resty -e ' local version = {
  major = 1,
  minor = 1
  }
version = setmetatable(version, {
     __index = function(t, key)
         if key == "patch" then
             return 2
         end
     end,
     __tostring = function(t)
      return string.format("%d.%d.%d", t.major, t.minor, t.patch)
    end
  })
  print(tostring(version))
'
```
这样的话，t.patch 其实获取不到值，那么就会走到 __index 这个函数中，结果就会打印出 1.1.2。事实上，__index 不仅可以是一个函数，也可以是一个 table。你试着运行下面这段代码，就会看到，它们实现的效果是一样的。


```
$ resty -e ' local version = {
  major = 1,
  minor = 1
  }
version = setmetatable(version, {
     __index = {patch = 2},
     __tostring = function(t)
      return string.format("%d.%d.%d", t.major, t.minor, t.patch)
    end
  })
  print(tostring(version))
'
```
另一个元方法则是__call。它类似于仿函数，可以让 table 被调用。我们还是基于上面打印版本号的代码来做修改，看看如何调用一个 table：


```
$ resty -e '
local version = {
  major = 1,
  minor = 1,
  patch = 1
  }

local function print_version(t)
     print(string.format("%d.%d.%d", t.major, t.minor, t.patch))
end

version = setmetatable(version,
     {__call = print_version})

  version()
'
```
这段代码中，我们使用 setmetatable，给 version 这个 table 增加了元表，而里面的 __call 元方法指向了函数 print_version 。那么，如果我们尝试把 version 当作函数调用，这里就会执行函数 print_version。而 getmetatable 是和 setmetatable 配对的操作，可以获取到已经设置的元表，比如下面这段代码：
```
$ resty -e ' local version = {
  major = 1,
  minor = 1
  }
version = setmetatable(version, {
     __index = {patch = 2},
     __tostring = function(t)
      return string.format("%d.%d.%d", t.major, t.minor, t.patch)
    end
  })
  print(getmetatable(version).__index.patch)
'
```
自然，除了今天讲到的这三个元方法外，还有一些不经常使用的元方法，你可以在遇到的时候再去查阅[文档](http://lua-users.org/wiki/MetamethodsTutorial)了解。

### 面向对象
最后我们来聊聊面向对象。你可能知道，Lua 并不是一个面向对象（Object Orientation）的语言，但我们可以使用 metatable 来实现 OO。我们来看一个实际的例子。[lua-resty-mysql](https://github.com/openresty/lua-resty-mysql/blob/master/lib/resty/mysql.lua)  是 OpenResty 官方的 MySQL 客户端，里面就使用元表模拟了类和类方法，它的使用方式如下所示：

```
$ resty -e 'local mysql = require "resty.mysql" -- 先引用 lua-resty 库
local db, err = mysql:new() -- 新建一个类的实例
db:set_timeout(1000) -- 调用类的方法'
```
你可以直接用 resty 命令行来执行上述代码。这几行代码很好理解，唯一可能给你造成困扰的是：

**在调用类方法的时候，为什么是冒号而不是点号呢？**


其实，在这里冒号和点号都是可以的，db:set_timeout(1000) 和 db.set_timeout(db, 1000) 是完全等价的。冒号是 Lua 中的一个语法糖，可以省略掉函数的第一个参数 self。众所周知，源码面前没有秘密，让我们来看看上述几行代码所对应的具体实现，以便你更好理解，如何用元表来模拟面向对象：
```
local _M = { _VERSION = '0.21' } -- 使用 table 模拟类
local mt = { __index = _M } -- mt 即 metatable 的缩写，__index 指向类自身

-- 类的构造函数
function _M.new(self) 
     local sock, err = tcp()
     if not sock then
         return nil, err
     end
     return setmetatable({ sock = sock }, mt) -- 使用 table 和 metatable 模拟类的实例
end
 
-- 类的成员函数
 function _M.set_timeout(self, timeout) -- 使用 self 参数，获取要操作的类的实例
     local sock = self.sock
     if not sock then
        return nil, "not initialized"
     end

    return sock:settimeout(timeout)
end
```
你可以看到，_M 这个 table 模拟了一个类，初始化时，它只有 _VERSION 这一个成员变量，并在随后定义了 _M.set_timeout 等成员函数。在 _M.new(self) 这个构造函数中，我们返回了一个 table，这个 table 的元表就是 mt，而 mt 的 __index 元方法指向了 _M，这样，返回的这个 table 就模拟了类 _M 的实例。


### 写在最后
好的，到这里，今天的主要内容就结束了。事实上，table 和 metatable 会大量地用在 OpenResty 的 lua-resty-* 库以及基于 OpenResty 的开源项目中，我希望通过这节课的学习，可以让你更容易地读懂这些源代码。自然，除了 table 外，Lua 中还有其他一些常用的函数，我们下节课再一起来学习。

## 12 | 高手秘诀：识别Lua的独有概念和坑

上一节中，我们一起了解了 LuaJIT 中 table 相关的库函数。除了这些常用的函数外，今天我再为你介绍一些 Lua 独有的或不太常用的概念，以及 OpenResty 中常见的 Lua 的坑。

### 弱表
首先是 弱表（weak table），它是 Lua 中很独特的一个概念，和垃圾回收相关。和其他高级语言一样，Lua 是自动垃圾回收的，你不用关心具体的实现，也不用显式 GC。没有被引用到的空间，会被垃圾收集器自动完成回收。但简单的引用计数还不太够用，有时候我们需要一种更灵活的机制。举个例子，我们把一个 Lua 的对象 Foo（table 或者函数）插入到 table tb 中，这就会产生对这个对象 Foo 的引用。即使没有其他地方引用 Foo，tb 对它的引用也还一直存在，那么 GC 就没有办法回收 Foo 所占用的内存。这时候，我们就只有两种选择：

一是手工释放 Foo；二是让它常驻内存。

比如下面这段代码：

```
$ resty -e 'local tb = {}
tb[1] = {red}
tb[2] = function() print("func") end
print(#tb) -- 2

collectgarbage()
print(#tb) -- 2

table.remove(tb, 1)
print(#tb) -- 1
```
不过，你肯定不希望，内存一直被用不到的对象占用着吧，特别是 LuaJIT 中还有 2G 内存的上限。而手工释放的时机并不好把握，也会增加代码的复杂度。那么这时候，就轮到弱表来大显身手了。看它的名字，弱表，首先它是一个表，然后这个表里面的所有元素都是弱引用。概念总是抽象的，让我们先来看一段稍加修改后的代码：

```
$ resty -e 'local tb = {}
tb[1] = {red}
tb[2] = function() print("func") end
setmetatable(tb, {__mode = "v"})
print(#tb)  -- 2

collectgarbage()
print(#tb) -- 0
'
```
可以看到，没有被使用的对象都被 GC 了。这其中，最重要的就是下面这一行代码：
```
setmetatable(tb, {__mode = "v"})
```
是不是似曾相识？这不就是元表的操作吗！没错，当一个 table 的元表中存在 __mode 字段时，这个 table 就是弱表（weak table）了。如果 __mode 的值是 k，那就意味着这个 table 的 键 是弱引用。如果 __mode 的值是 v，那就意味着这个 table 的 值 是弱引用。当然，你也可以设置为 kv，表明这个表的键和值都是弱引用。

这三者中的任意一种弱表，只要它的 键 或者 值 被回收了，那么对应的整个键值 对象都会被回收。在上面的代码示例中，__mode 的值 v，而tb 是一个数组，数组的 value 则是 table 和函数对象，所以可以被自动回收。不过，如果你把__mode 的值改为 k，就不会 GC 了，比如看下面这段代码：

```
$ resty -e 'local tb = {}
tb[1] = {red}
tb[2] = function() print("func") end
setmetatable(tb, {__mode = "k"})
print(#tb)  -- 2

collectgarbage()
print(#tb) -- 2
'
```
请注意，这里我们只演示了 value 为弱引用的弱表，也就是数组类型的弱表。自然，你同样可以把对象作为 key，来构建哈希表类型的弱表，比如下面这样写：

```
$ resty -e 'local tb = {}
tb[{color = red}] = "red"
local fc = function() print("func") end
tb[fc] = "func"
fc = nil

setmetatable(tb, {__mode = "k"})
for k,v in pairs(tb) do
     print(v)
end

collectgarbage()
print("----------")
for k,v in pairs(tb) do
     print(v)
end
'
```
在手动调用 collectgarbage() 进行强制 GC 后，tb 整个 table 里面的元素，就已经全部被回收了。当然，在实际的代码中，我们大可不必手动调用 collectgarbage()，它会在后台自动运行，无须我们担心。不过，既然提到了 collectgarbage() 这个函数，我就再多说几句。这个函数其实可以传入多个不同的选项，且默认是 collect，即完整的 GC。另一个比较有用的是 count，它可以返回 Lua 占用的内存空间大小。这个统计数据很有用，可以让你看出是否存在内存泄漏，也可以提醒我们不要接近 2G 的上限值。弱表相关的代码，在实际应用中会写得比较复杂，不太容易理解，相对应的，也会隐藏更多的 bug。具体有哪些呢？不必着急，后面内容，我会专门介绍一个开源项目中，使用弱表带来的内存泄漏问题。


### 闭包和 upvalue
再来看闭包和 upvalue。前面我强调过，在 Lua 中，所有的值都是一等公民，包含函数也是。这就意味着函数可以保存在变量中，当作参数传递，以及作为另一个函数的返回值。比如在上面弱表中出现的这段示例代码：

```
tb[2] = function() print("func") end
```
其实就是把一个匿名函数，作为 table 的值给存储了起来。在 Lua 中，下面这段代码中动两个函数的定义是完全等价的。不过注意，后者是把函数赋值给一个变量，这也是我们经常会用到的一种方式：
```
local function foo() print("foo") end
local foo = fuction() print("foo") end
```
另外，Lua 支持把一个函数写在另外一个函数里面，即嵌套函数，比如下面的示例代码：

```
$ resty -e '
local function foo()
     local i = 1
     local function bar()
         i = i + 1
         print(i)
     end
     return bar
end

local fn = foo()
print(fn()) -- 2
'
```
你可以看到， bar 这个函数可以读取函数 foo 里面的局部变量 i，并修改它的值，即使这个变量并不在 bar 里面定义。这个特性叫做词法作用域（lexical scoping）。事实上，Lua 的这些特性正是闭包的基础。所谓闭包 ，简单地理解，它其实是一个函数，不过它访问了另外一个函数词法作用域中的变量。如果按照闭包的定义来看，Lua 的所有函数实际上都是闭包，即使你没有嵌套。这是因为 Lua 编译器会把 Lua 脚本外面，再包装一层主函数。比如下面这几行简单的代码段：

```
local foo, bar
local function fn()
     foo = 1
     bar = 2
end
```
在编译后，就会变为下面的样子：
```
function main(...)
     local foo, bar
     local function fn()
         foo = 1
         bar = 2
     end
end
```
而函数 fn 捕获了主函数的两个局部变量，因此也是闭包。当然，我们知道，很多语言中都有闭包的概念，它并非 Lua 独有，你也可以对比着来加深理解。只有理解了闭包，你才能明白我们接下来要讲的 upvalue。upvalue 就是 Lua 中独有的概念了。从字面意思来看，可以翻译成 上面的值。实际上，upvalue 就是闭包中捕获的自己词法作用域外的那个变量。还是继续看上面那段代码：

```
local foo, bar
local function fn()
     foo = 1
     bar = 2
end
```
你可以看到，函数 fn 捕获了两个不在自己词法作用域的局部变量 foo 和 bar，而这两个变量，实际上就是函数 fn 的 upvalue。

### 常见的坑
介绍了 Lua 中的几个概念后，我再来说说，在 OpenResty 开发中遇到的那些和 Lua 相关的坑。在前面内容中，我们提到了一些 Lua 和其他开发语言不同的点，比如下标从 1 开始、默认全局变量等等。在 OpenResty 实际的代码开发中，我们还会遇到更多和 Lua、 LuaJIT 相关的问题点， 下面我会讲其中一些比较常见的。这里要先提醒一下，即使你知道了所有的 坑，但不可避免的，估计还是要自己踩过之后才能印象深刻。当然，不同的是，你能够更块地从坑里面爬出来，并找到症结所在。

#### 下标从 0 开始还是从 1 开始
第一个坑，Lua 的下标是从 1 开始的，这点我们之前反复提及过。但我不得不说，这并非事实的全部。因为在 LuaJIT 中，使用 ffi.new 创建的数组，下标又是从 0 开始的:

```
local buf = ffi_new("char[?]", 128)
```
所以，如果你要访问上面这段代码中 buf 这个 cdata，请记得下标从 0 开始，而不是 1。在使用 FFI 和 C 交互的时候，一定要特别注意这个地方。

#### 正则模式匹配
第二个坑，正则模式匹配问题。OpenResty 中并行着两套字符串匹配方法：Lua 自带的 sting 库，以及 OpenResty 提供的 ngx.re.* API。其中， Lua 正则模式匹配是自己独有的格式，和 PCRE 的写法不同。下面是一个简单的示例：

```
resty -e 'print(string.match("foo 123 bar", "%d%d%d"))'  — 123
```
这段代码从字符串中提取了数字部分，你会发现，它和我们的熟悉的正则表达式完全不同。Lua 自带的正则匹配库，不仅代码维护成本高，而且性能低——不能被 JIT，而且被编译过一次的模式也不会被缓存。所以，在你使用 Lua 内置的 string 库去做 find、match 等操作时，如果有类似正则这样的需求，不用犹豫，请直接使用 OpenResty 提供的 ngx.re 来替代。只有在查找固定字符串的时候，我们才考虑使用 plain 模式来调用 string 库。

**这里我有一个建议：在 OpenResty 中，我们总是优先使用 OpenResty 的 API，然后是 LuaJIT 的 API，使用 Lua 库则需要慎之又慎。**

#### json 编码时无法区分 array 和 dict
第三个坑，json 编码时无法区分 array 和 dict。由于 Lua 中只有 table 这一个数据结构，所以在 json 对空 table 编码的时候，自然就无法确定编码为数组还是字典：

```
resty -e 'local cjson = require "cjson"
local t = {}
print(cjson.encode(t))
'
```
比如上面这段代码，它的输出是 {}，由此可见， OpenResty 的 cjson 库，默认把空 table 当做字典来编码。当然，我们可以通过 encode_empty_table_as_object 这个函数，来修改这个全局的默认值：


```
resty -e 'local cjson = require "cjson"
cjson.encode_empty_table_as_object(false)
local t = {}
print(cjson.encode(t))
'
```
这次，空 table 就被编码为了数组：[]。不过，全局这种设置的影响面比较大，那能不能指定某个 table 的编码规则呢？答案自然是可以的，我们有两种方法可以做到。第一种方法，把 cjson.empty_array 这个 userdata 赋值给指定 table。这样，在 json 编码的时候，它就会被当做空数组来处理：

```
$ resty -e 'local cjson = require "cjson"
local t = cjson.empty_array
print(cjson.encode(t))
'
```
不过，有时候我们并不确定，这个指定的 table 是否一直为空。我们希望当它为空的时候编码为数组，那么就要用到 cjson.empty_array_mt 这个函数，也就是我们的第二个方法。它会标记好指定的 table，当 table 为空时编码为数组。从cjson.empty_array_mt 这个命名你也可以看出，它是通过 metatable 的方式进行设置的，比如下面这段代码操作：

```
$ resty -e 'local cjson = require "cjson"
local t = {}
setmetatable(t, cjson.empty_array_mt)
print(cjson.encode(t))
t = {123}
print(cjson.encode(t))
'
```
你可以在本地执行一下这段代码，看看输出和你预期的是否一致。

#### 变量的个数限制
再来看第四个坑，变量的个数限制问题。 Lua 中，一个函数的局部变量的个数，和 upvalue 的个数都是有上限的，你可以从 Lua 的源码中得到印证：


```
/*
@@ LUAI_MAXVARS is the maximum number of local variables per function
@* (must be smaller than 250).
*/
#define LUAI_MAXVARS            200


/*
@@ LUAI_MAXUPVALUES is the maximum number of upvalues per function
@* (must be smaller than 250).
*/
#define LUAI_MAXUPVALUES        60
```
这两个阈值，分别被硬编码为 200 和 60。虽说你可以手动修改源码来调整这两个值，不过最大也只能设置为 250。一般情况下，我们不会超过这个阈值，但写 OpenResty 代码的时候，你还是要留意这个事情，不要过多地使用局部变量和 upvalue，而是要尽可能地使用 do .. end 做一层封装，来减少局部变量和 upvalue 的个数。比如我们来看下面这段伪码：

```
local re_find = ngx.re.find
  function foo() ... end
function bar() ... end
function fn() ... end
```
如果只有函数 foo 使用到了 re_find， 那么我们可以这样改造下：

```
do
     local re_find = ngx.re.find
     function foo() ... end
end
function bar() ... end
function fn() ... end
```
这样一来，在 main 函数的层面上，就少了 re_find 这个局部变量。这在单个的大的 Lua 文件中，算是一个优化技巧。


## 13 | [视频]实战：基于FFI实现的lua-resty-lrucache

今天的内容，我同样会以视频的形式来讲解。不过，在你进行视频学习之前，我想先问你这么几个问题：lua-resty-lrucache 内部最重要的数据结构是什么？lua-resty-lrucache 有两种 FFI 的实现，我们今天讲的这一种更适合什么场景？这几个问题，也是今天视频课要解决的核心内容，希望你可以先自己思考一下，并带着问题来学习今天的视频内容。同时，我会给出相应的文字介绍，方便你在听完视频内容后，及时总结与复习。下面是今天这节课的文字介绍部分。


### 今日核心
lua-resty-lrucache 是一个使用 LuaJIT FFI 实现的 LRU 缓存库，可以在 worker 内缓存各种类型的数据。功能与之类似的是 shared dict，但 shared dict 只能存储字符串类型的数据。在大多数实际情况下，这两种缓存是配合在一起使用的——lrucache 作为一级缓存，shared dict 作为二级缓存。lrucache 的实现，并没有涉及到 OpenResty 的 Lua API。所以，即使你以前没有用过 OpenResty，也可以通过这个项目来学习如何使用 LuaJIT 的 FFI。lrucache 仓库中包含了两种实现方案，一种是使用 Lua table 来实现缓存，另外一种则是使用 hash 表来实现。前者更适合命中率高的情况，后者适合命中率低的情况。两个方案没有哪个更好，要看你的线上环境更适合哪一个。通过今天这个项目，你可以弄清楚要如何使用 FFI，并了解一个完整的 lua-resty 库应该包括哪些必要的内容。当然，我顺道也会介绍下 travis 的使用。最后，还是想强调一点，在你面对一个陌生的开源项目时，文档和测试案例永远是最好的上手方式。而你后期如果要阅读源码，也不要先去抠细节，而是应该先去看主要的数据结构，围绕重点逐层深入。

### 课件参考
今天的课件已经上传到了我的 GitHub 上，你可以自己下载学习。链接如下：https://github.com/iresty/geektime-slides


## 14 | 答疑（一）：Lua 规则和 NGINX 配置文件产生冲突怎么办？

第一问，OpenResty 的名字和语言Q：看到现在，我还没看懂 OpenResty 这个名字的来历。另外，OpenResty 借助 Lua 语言，插上翅膀，那么为什么不借助其他脚本语言呢？比如 Shell 等。

A：事实上，OpenResty 最早是雅虎中国的一个公司项目，起步于 2007 年 10 月。当时兴起了 OpenAPI 的热潮，于是春哥想做一个类似的东西，可以支持各种 Web Service 的需求。Open 这个名字取自 OpenAPI， Resty 则是取自 rest API。最初 OpenResty 的目的，并非是做 web 服务器和开发平台，而是做类似网站这样的应用。OpenResty 在十几年前开源的时候，支持同步非阻塞的语言凤毛麟角。即使是到了现在，后端语言可以达到 OpenResty 这种性能级别的也不多。当前，更多的开发者把 OpenResty 用在 API 网关和软 WAF 领域，这也算是开发者的自然选择了。至于语言方面，OpenResty 并不是唯一一个把其他开发语言嵌入 NGINX 的项目。比如，NGINX 官方就把 JS 嵌入了进来；同时也有一些开源项目，把 PHP 嵌入 NGINX。通常来说，选择借助哪一门语言，会综合考虑协程、JIT 和语言普及度等多种因素。对于 OpenResty，在 2007 年时，Lua 确实是最佳的选择。实际上，OpenResty 在最早的版本中选择了 perl 而不是 Lua，也可以说是走了一段弯路。

第二问，配置文件的规则优先级Q：当 OpenResty 中的 Lua 规则和 NGINX 配置文件产生冲突时，比如 NGINX 配置了 rewrite 规则，又同时引用了 rewrite_by_lua_file，那么这两条规则的优先级是什么？

A：其实，这个具体要看 NGINX 配置的 rewrite 规则是怎么写的了，是 break 还是 last。这一点，在 OpenResty 的官方文档中有注明，并且配了一个示例代码：


```
 location /foo {
     rewrite ^ /bar;
     rewrite_by_lua 'ngx.exit(503)';
 }
 location /bar {
     ...
 }
```
在示例代码的这个配置中，ngx.exit(503) 是不会被执行的。但是，如果你改成下面这样的写法，ngx.exit(503) 就可以被执行。


```
rewrite ^ /bar break；
```
不过，为了避免这种歧义，我还是建议都使用 OpenResty 来处理 rewrite，而不是 NGINX 的配置。说实话，NGINX 的很多配置是比较晦涩的，需要你反复查阅文档才能读懂。


第三问，我的代码为什么报错？Q：在 LuaJIT 扩展的 table 函数中，为什么下面这两行代码用 LuaJIT 去执行，都会报错“找不到 moudule”呢？我用的 LuaJIT 为 2.0.5 版本。

```
local new_tab = require('table.new')
# 或者
require('table.clear')

# 执行后会报错
luajit: table_luajit.lua:1: module 'table.new' not found:
```
A：这个问题要注意，这两行代码，需要 LuaJIT 2.1 的版本才能运行, 文档在这里：https://github.com/LuaJIT/LuaJIT/blob/v2.1/doc/extensions.html#L218，可以了解一下。其实，这也是你在使用 OpenResty 时需要特别留意的。OpenResty 需要特定版本的 LuaJIT 才能正常运行，前面我们也讲过，因为 OpenResty 基于 LuaJIT 2.1 的分支，并且对 LuaJIT 做了不少自己的扩展。所以，在运行本专栏的代码时，请记得使用 OpenResty 官方的安装方式，如果你在 NGINX 的基础上添加 lua-nginx-module 来编译，还是会踩不少坑的。


第四问，关于空值的困惑Q：我遇到一些让人困惑的地方是ngx.null、nil、null和""。在网上搜索的时候，看到有人说null是ngx.null的一个定义。Redis 返回的时候，经常会判断返回结果是否为空，那么，判断的时候是和哪个值进行比较呢？关于这些值，有没有其他一些使用上的坑呢？一直以来我都没有一个明确的认识，想和老师确认一下。


A：在回答你的问题之前，我建议你在 lua-resty-redis 里，使用下面的代码去查找一个 key：

```
local res, err = red:get("dog")
```
如果返回值 res 是 nil，就说明函调用失败了；如果 res 是 ngx.null ，就说明 redis 中不存在 dog 这个 key。这是因为， Lua 的 nil 无法作为 table 的 value，所以 OpenResty 引入了 ngx.null，作为 table 中的空值。我们可以用下面的代码，打印出 ngx.null 和它的类型：
```
# 打印ngx.null
$ resty -e  'print(ngx.null)'
null

# 打印类型
$ resty -e 'print(type(ngx.null))'
userdata
```
你可以看到， ngx.null 并非nil，而是 userdata 类型。更进一步，在 OpenResty 中有很多种空值，比如 cjson.null、cdata:NULL 等等，后面我都会专门讲到。总的来说，在 OpenResty 中只有 nil 和 false 是假值。所以，在你写类似 if not res then这种代码的时候，一定要慎之又慎，最好改成明确的 if res ~= nil and res ~= false then，用类似这样的写法，并要有对应的测试案例覆盖。


第五问：API 网关到底是什么？Q：文中一直说的 API 网关是指什么？和 NGINX、Tomcat、Apache 这种 Web 服务器，又有什么区别呢？


A：API 网关其实是用来统一管理服务的网关。举个例子，像是支付、用户登录等，都是 API 形式对外提供的服务，它们都需要一个网关来做统一的安全和身份认证。API 网关可以替代传统的 NGINX、Apache 来处理南北向流量，也可以在微服务环境下处理东西向的流量，是更加贴近业务的一种中间件，而非底层的 Web 服务器。所以，在专栏的最后几篇文章中，我会带着你一起来看下，如何实现一个 API 网关，这是 OpenResty 当前最热门的使用场景之一。学习是一个需要反复和刻意练习的过程，就像你高中、大学读书的时候一样，能提出问题、敢于提出问题，是吸收知识的重要步骤。希望你能够体会“把书读厚再读薄”的这个学习过程。


## API篇

## 15 | OpenResty 和别的开发平台有什么不同？

上一模块中， 你已经学习了 OpenResty 的两个基石：NGINX 和 LuaJIT，相信你已经摩拳擦掌，准备开始学习 OpenResty 提供的 API 了吧？不过，别着急，在这之前，你还需要再花一点儿时间，来熟悉下 OpenResty 的原理和基本概念。

### 原理
在前面的 LuaJIT 内容中，你已经见过下面这个架构图：

![img](https://static001.geekbang.org/resource/image/14/f0/14ab2f0c81c170234ab739cb700a62f0.png?wh=1146*828)

这里我再详细解释一下。OpenResty 的 master 和 worker 进程中，都包含一个 LuaJIT VM。在同一个进程内的所有协程，都会共享这个 VM，并在这个 VM 中运行 Lua 代码。而在同一个时间点上，每个 worker 进程只能处理一个用户的请求，也就是只有一个协程在运行。看到这里，你可能会有一个疑问：NGINX 既然能够支持 C10K （上万并发），不是需要同时处理一万个请求吗？当然不是，NGINX 实际上是通过 epoll 的事件驱动，来减少等待和空转，才尽可能地让 CPU 资源都用于处理用户的请求。毕竟，只有单个的请求被足够快地处理完，整体才能达到高性能的目的。如果采用的是多线程模式，让一个请求对应一个线程，那么在 C10K 的情况下，资源很容易就会被耗尽的。在 OpenResty 层面，Lua 的协程会与 NGINX 的事件机制相互配合。如果 Lua 代码中出现类似查询 MySQL 数据库这样的 I/O 操作，就会先调用 Lua 协程的 yield 把自己挂起，然后在 NGINX 中注册回调；在 I/O 操作完成（也可能是超时或者出错）后，再由 NGINX 回调 resume 来唤醒 Lua 协程。这样就完成了 Lua 协程和 NGINX 事件驱动的配合，避免在 Lua 代码中写回调。我们可以来看下面这张图，描述了这整个流程。其中，lua_yield 和 lua_resume 都属于 Lua 提供的 lua_CFunction。

![img](https://static001.geekbang.org/resource/image/fa/34/fae1008edb43c7476cf2f20da9928234.png?wh=1728*764)

另外一个方面，如果 Lua 代码中没有 I/O 或者 sleep 操作，比如全是密集的加解密运算，那么 Lua 协程就会一直占用 LuaJIT VM，直到处理完整个请求。下面我提供了 ngx.sleep 的一段源码，可以帮你更清晰理解这一点。 这段代码位于 ngx_http_lua_sleep.c 中，你可以在 lua-nginx-module 项目的  [src 目录](https://github.com/openresty/lua-nginx-module/tree/master/src)中找到它。在ngx_http_lua_sleep.c 中，我们可以看到 sleep 函数的具体实现。你需要先通过 C 函数 ngx_http_lua_ngx_sleep，来注册 ngx.sleep 这个 Lua API：

```
void
ngx_http_lua_inject_sleep_api(lua_State *L)
{
     lua_pushcfunction(L, ngx_http_lua_ngx_sleep);
     lua_setfield(L, -2, "sleep");
}
```
下面便是 sleep 的主函数，这里我只摘取了几行主要的代码：
```
static int ngx_http_lua_ngx_sleep(lua_State *L)
{
    coctx->sleep.handler = ngx_http_lua_sleep_handler;
    ngx_add_timer(&coctx->sleep, (ngx_msec_t) delay);
    return lua_yield(L, 0);
}
```
你可以看到：这里先增加了 ngx_http_lua_sleep_handler 这个回调函数；然后调用 ngx_add_timer 这个 NGINX 提供的接口，向 NGINX 的事件循环中增加一个定时器；最后使用 lua_yield 把 Lua 协程挂起，把控制权交给 NGINX 的事件循环。


当 sleep 操作完成后， ngx_http_lua_sleep_handler 这个回调函数就被触发了。它里面调用了 ngx_http_lua_sleep_resume, 并最终使用 lua_resume 唤醒了 Lua 协程。更具体的调用过程，你可以自己去代码里面检索，这里我就不展开描述了。ngx.sleep 只是最简单的一个示例，不过通过对它的剖析，你可以看出 lua-nginx-module 模块的基本原理。


### 基本概念
分析完原理之后，让我们一起温故而知新，回忆下 OpenResty 中阶段和非阻塞这两个重要的概念。OpenResty 和 NGINX 一样，都有阶段的概念，并且每个阶段都有自己不同的作用：

set_by_lua，用于设置变量；rewrite_by_lua，用于转发、重定向等；access_by_lua，用于准入、权限等；content_by_lua，用于生成返回内容；header_filter_by_lua，用于应答头过滤处理；body_filter_by_lua，用于应答体过滤处理；log_by_lua，用于日志记录。

当然，如果你的代码逻辑并不复杂，都放在 rewrite 或者 content 阶段执行，也是可以的。不过需要注意，OpenResty 的 API 是有阶段使用限制的。每一个 API 都有一个与之对应的使用阶段列表，如果你超范围使用就会报错。这与其他的开发语言有很大的不同。举个例子，这里我还是以 ngx.sleep 为例。通过查阅文档，我知道它只能用于下面列出的上下文中，并不包括 log 阶段：

```
context: rewrite_by_lua*, access_by_lua*, content_by_lua*, ngx.timer.*, ssl_certificate_by_lua*, ssl_session_fetch_by_lua*_
```
而如果你不知道这一点，在它不支持的 log 阶段使用 sleep 的话:


```
location / {
    log_by_lua_block {
        ngx.sleep(1)
     }
}
```
在 NGINX 的错误日志中，就会出现 error 级别的提示：


```
[error] 62666#0: *6 failed to run log_by_lua*: log_by_lua(nginx.conf:14):2: API disabled in the context of log_by_lua*
stack traceback:
    [C]: in function 'sleep'
```
所以，在你使用 API 之前，一定记得要先查阅文档，确定其能否在代码的上下文中使用。复习了阶段的概念后，我们再来回顾下非阻塞。首先明确一点，由 OpenResty 提供的所有 API，都是非阻塞的。我继续以 sleep 1 秒这个需求为例来说明。如果你要在 Lua 中实现它，你需要这样做：

```
function sleep(s)
   local ntime = os.time() + s
   repeat until os.time() > ntime
end
```
因为标准 Lua 没有直接的 sleep 函数，所以这里我用一个循环，来不停地判断是否达到指定的时间。这个实现就是阻塞的，在 sleep 的这一秒钟时间内，Lua 正在做无用功，而其他需要处理的请求，只能在一边傻傻地等待。不过，要是换成 ngx.sleep(1) 来实现的话，根据上面我们分析过的源码，在这一秒钟的时间内，OpenResty 依然可以去处理其他请求（比如 B 请求），当前请求（我们叫它 A 请求）的上下文会被保存起来，并由 NGINX 的事件机制来唤醒，再回到 A 请求，这样 CPU 就一直处于真正的工作状态。

### 变量和生命周期
除了这两个重要概念外，变量的生命周期，也是 OpenResty 开发中容易出错的地方。前面说过，在 OpenResty 中，我推荐你把所有变量都声明为局部变量，并用 luacheck 和 lua-releng 这样的工具来检测全局变量。这其实对于模块来说也是一样的，比如下面这样的写法：

```
local ngx_re = require "ngx.re"
```
其实，在 OpenResty 中，除了 init_by_lua 和 init_worker_by_lua 这两个阶段外，其余阶段都会设置一个隔离的全局变量表，以免在处理过程中污染了其他请求。即使在这两个可以定义全局变量的阶段，你也应该尽量避免去定义全局变量。通常来说，试图用全局变量来解决的问题，其实更应该用模块的变量来解决，而且还会更加清晰。下面是一个模块中变量的示例：
```
local _M = {}

_M.color = {
      red = 1,
      blue = 2,
      green = 3
  }

  return _M
```
我在一个名为 hello.lua 的文件中定义了一个模块，模块包含了 color 这个 table。然后，我又在 nginx.conf 中增加了对应的配置：

```
location / {
    content_by_lua_block {
        local hello = require "hello"
        ngx.say(hello.color.green)
     }
}
```
这段配置会在 content 阶段中 require 这个模块，并把 green 的值作为 http 请求返回体打印出来。你可能会好奇，模块变量为什么这么神奇呢？实际上，在同一 worker 进程中，模块只会被加载一次；之后这个 worker 处理的所有请求，就可以共享模块中的数据了。我们说“全局”的数据很适合封装在模块内，是因为 OpenResty 的 worker 之间完全隔离，所以每个 worker 都会独立地对模块进行加载，而模块的数据也不能跨越 worker。至于应该如何处理 worker 之间需要共享的数据，我会留到后面的章节来讲解，这里你先不必深究。


不过，这里也有一个很容易出错的地方，那就是访问模块变量的时候，你最好保持只读，而不要尝试去修改，不然在高并发的情况下会出现 race。这种 bug 依靠单元测试是无法发现的，它在线上偶尔会出现，并且很难定位。举个例子，模块变量 green 当前的值是 3，而你在代码中做了加 1 的操作，那么现在 green 的值是 4 吗？不一定，它可能是 4，也可能是 5 或者是 6。因为在对模块变量进行写操作的时候，OpenResty 并不会加锁，这时就会产生竞争，模块变量的值就会被多个请求同时更新。


说完了全局变量、局部变量和模块变量，最后我们再来讲讲跨阶段的变量。有些情况下，我们需要的是跨越阶段的、可以读写的变量。而像我们熟悉的 NGINX 中 $host、$scheme 等变量，虽然满足跨越阶段的条件，但却无法做到动态创建，你必须先在配置文件中定义才能使用它们。比如下面这样的写法：

```
location /foo {
      set $my_var ; # 需要先创建 $my_var 变量
      content_by_lua_block {
          ngx.var.my_var = 123
      }
  }
```
OpenResty 提供了 ngx.ctx，来解决这类问题。它是一个 Lua table，可以用来存储基于请求的 Lua 数据，且生存周期与当前请求相同。我们来看下官方文档中的这个示例：

```
location /test {
      rewrite_by_lua_block {
          ngx.ctx.foo = 76
      }
      access_by_lua_block {
          ngx.ctx.foo = ngx.ctx.foo + 3
      }
      content_by_lua_block {
          ngx.say(ngx.ctx.foo)
      }
  }
```
你可以看到，我们定义了一个变量 foo，存放在 ngx.ctx 中。这个变量跨越了 rewrite、access 和 content 三个阶段，最终在 content 阶段打印出了值，并且是我们预期的 79。当然，ngx.ctx 也有自己的局限性：


比如说，使用 ngx.location.capture 创建的子请求，会有自己独立的 ngx.ctx 数据，和父请求的 ngx.ctx 互不影响；再如，使用 ngx.exec 创建的内部重定向，会销毁原始请求的 ngx.ctx，重新生成空白的 ngx.ctx。

这两个局限，在官方文档中都有详细的[代码示例](https://github.com/openresty/lua-nginx-module#ngxctx)，如果你有兴趣可以自行查阅。



### 写在最后
最后，我再多说几句。这节课，我们学习的是 OpenResty 的原理和几个重要的概念，不过，你并不需要背得滚瓜烂熟，毕竟，这些概念总是在和实际需求以及代码结合在一起时，才会变得有意义并生动起来。


## 16 | 秒杀大多数开发问题的两个利器：文档和测试案例


在学习了 OpenResty 的原理和几个重要概念后，我们终于要开始 API 的学习了。从我个人的经验来看，学习 OpenResty 的 API 是相对容易的，所以并没有占用本专栏太多的篇幅。你可以会疑惑：API 不是最常用、最重要的部分吗，为什么花的笔墨不多？其实，这主要是出于两个方面的考虑。

第一，OpenResty 提供了非常详尽的文档。和很多其他的开发语言或者平台相比，OpenResty 除了会提供 API 的参数、返回值定义，还会提供完整的、可运行的代码示例，清楚地告诉你 API 是如何处理各种边界条件的。这种在 API 定义下面紧跟着示例代码和注意事项的做法，就是 OpenResty 文档的一贯风格。这样一来，在看完 API 描述后，你就可以立即在自己的环境下运行示例代码，并修改参数来和文档互相印证，加深记忆和理解。


第二，在文档之外，OpenResty 还提供了高覆盖度的测试案例集。刚刚我提到过，OpenResty 文档中提供了 API 的代码示例，但终究篇幅有限，多个 API 之间如何配合使用、各种异常情况下的报错和处理等，在文档中并没有呈现。

不过，不用担心，这些内容你大都可以在测试案例集里找到。对于 OpenResty 的开发者来说，最好的 API 学习资料就是官方文档和测试案例，它们足够专业和友好。在这个前提下，如果我单纯地把文档翻译成中文再放在专栏中来讲，就没有太大意义了。授人以鱼不如授之以渔，我更希望教给你的是通用的方法和经验。让我们用一个真实的例子来体验下，在 OpenResty 的开发中，如何让文档和测试案例集发挥更大的威力。


### shdict get API
shared dict（共享字典）是基于 NGINX 共享内存区的 Lua 字典对象，它可以跨多个 worker 来存取数据，一般用来存放限流、限速、缓存等数据。shared dict 相关的 API 有 20 多个，是 OpenResty 中最常用也是最重要的一组 API。我们以最简单的 get 操作为例，你可以点开  [文档链接](https://github.com/openresty/lua-nginx-module/#ngxshareddictget)  做为对照。下面的最小化的代码示例，正是由官方文档改编而来：

```
  http {
      lua_shared_dict dogs 10m;
      server {
          location /demo {
              content_by_lua_block {
                  local dogs = ngx.shared.dogs
         dogs:set("Jim", 8)
         local v = dogs:get("Jim")
                  ngx.say(v)
              }
          }
      }
  }
```
简单说明一下，在 Lua 代码中使用 shared dict 之前，我们需要在 nginx.conf 中用 lua_shared_dict 指令增加一块内存空间，它的名字是 dogs，大小为 10M。修改完 nginx.conf 后，你还需要重启进程，用浏览器或者 curl 访问才能看到结果。这步骤看起来是不是有些繁琐呢？让我们用一种更直接的方式改造一下。你可以看到，使用 resty CLI 的这种方式，和在 nginx.conf 中嵌入代码的效果是一致的。

```
$ resty --shdict 'dogs 10m' -e 'local dogs = ngx.shared.dogs
 dogs:set("Jim", 8)
 local v = dogs:get("Jim")
 ngx.say(v)
 '
```
你现在已经知道 nginx.conf 和 Lua 代码是如何配合的，也成功运行了 shared dict 的 set 和 get 方法。一般来说，大部分开发者也就此止步，不再深究了。事实上，这里还是有几个值得注意的地方，比如：

哪些阶段不能使用共享内存相关的 API 呢？我们在示例代码中看到 get 函数只有一个返回值，那什么情况下会有多个返回值呢？get 函数的入参是什么类型？是否有长度限制？


不要小看这几个问题，窥一斑而见全豹，它们可以帮助我们更好的深入 OpenResty。接下来我就带你一一解读。


### 哪些阶段不能使用共享内存相关的 API ？
先来看第一个问题，答案很直接，文档中专门有一个 context （即上下文部分），里面列出了在什么环境下可以使用这个 API：

```
context: set_by_lua*, rewrite_by_lua*, access_by_lua*, content_by_lua*, header_filter_by_lua*, body_filter_by_lua*, log_by_lua*, ngx.timer.*, balancer_by_lua*, ssl_certificate_by_lua*, ssl_session_fetch_by_lua*, ssl_session_store_by_lua*
```
可以看出， init 和 init_worker 两个阶段不在其中，也就是说，共享内存的 get API 不能在这两个阶段使用。需要注意的是，每个共享内存的 API 可以使用的阶段并不完全相同，比如 set API 就可以在 init 阶段使用。所以，千万不要想当然，还是那句话，使用时多翻阅文档。当然了，尽信书不如无书，OpenResty 的文档有时候也会出现错漏，这时候你就需要用实际的测试来验证了。接下来，让我们修改下测试案例集，来确定下 init 阶段是否可以运行 shared dict 的 get API。

那该如何找到和共享内存相关的测试案例集呢？事实上，OpenResty 的测试案例都放在 /t 目录下，并且命名也是有规律的，即自增数字-功能名.t。搜索shdict，你可以找到 043-shdict.t，而这就是共享内存的测试案例集了，它里面有接近 100 个测试案例，包含各种正常和异常情况的测试。

我们来试着修改下第一个测试案例。

你可以把 content 阶段改为 init 阶段，并精简掉无关代码，看看 get 接口能否运行。这里我需要提醒一点，在现阶段，你不用非得搞明白测试案例是如何编写、组织和运行的，你只要知道它是在测试 get 接口就可以了：

```
=== TEST 1: string key, int value
 --- http_config
     lua_shared_dict dogs 1m;
 --- config
     location = /test {
         init_by_lua '
             local dogs = ngx.shared.dogs
             local val = dogs:get("foo")
             ngx.say(val)
         ';
     }
 --- request
 GET /test
 --- response_body
 32
 --- no_error_log
 [error]
 --- ONLY
```
你应该注意到了，在测试案例的最后，我加了 --ONLY 标记，这表示忽略其他所有测试案例，只运行这一个测试案例，以提高运行速度。后面在测试部分中，我会专门讲解各种各样的标记，你先记住这里就可以了。修改完以后，我们用 prove 命令，就可以运行这个测试案例：
```
$ prove t/043-shdict.t
```
然后，你会得到一个报错，这也就印证了文档中描述的阶段限制。

```
nginx: [emerg] "init_by_lua" directive is not allowed here
```
### get 函数何时会有多个返回值？
我们再来看第二个问题，它可以从官方文档中总结出来。文档最开始就是这个接口的syntax 语法描述部分：


```
value, flags = ngx.shared.DICT:get(key)
```
正常情况下，第一个参数value 返回的是字典中 key 对应的值；但当 key 不存在或者过期时，value 的值为 nil。第二个参数 flags 就稍微复杂一些了，如果 set 接口设置了 flags，就返回，否则不返回。

一旦 API 调用出错，value 返回 nil，flags 返回具体的错误信息。从文档总结的信息我们可以看出，local v = dogs:get("Jim") 这种只有一个接收参数的写法并不完善，因为它只覆盖了普通的使用场景，没有接收第二个参数，也没有做异常处理。我们可以把它修改为下面这样：

```
local data, err = dogs:get("Jim")
if data == nil and err then
    ngx.say("get not ok: ", err)
    return
end
```
和第一个问题一样，我们可以到测试案例集里搜索一下，印证下我们对文档的理解：

```
=== TEST 65: get nil key
 --- http_config
     lua_shared_dict dogs 1m;
 --- config
     location = /test {
         content_by_lua '
             local dogs = ngx.shared.dogs
             local ok, err = dogs:get(nil)
             if not ok then
                 ngx.say("not ok: ", err)
                 return
             end
             ngx.say("ok")
         ';
     }
 --- request
 GET /test
 --- response_body
 not ok: nil key
 --- no_error_log
 [error]
```
在这个测试案例中，get 接口的入参为 nil，返回的 err 信息是 nil key。这一方面验证了我们对文档的分析是正确的，另一方面，也为第三个问题提供了部分答案——起码，get 的入参不能是 nil。

### get 函数的入参是什么类型？
至于第三个问题， get 的入参可以是什么类型的呢？我们按照老规矩先查看文档，不过很可惜，你会发现，文档里并没有注明 key 的合法类型有哪些。这时该怎么办呢？别着急，至少我们知道 key 可以是字符串类型，并且不能为 nil。不知道你还记得 Lua 中的数据类型吗？除了字符串和 nil，还有数字、数组、布尔类型和函数。后面两个显然没有作为 key 的必要性，我们只需要验证前两个。不妨先去测试文件中搜索一下，是否有数字作为 key 的案例：

```
=== TEST 4: number keys, string values
```
通过这个测试案例，你可以清楚看到，数字也可以作为 key ，内部会将数字转为字符串。那么数组呢？很遗憾，测试案例并没有覆盖到，我们需要自己动手试一下：

```
$ resty --shdict 'dogs 10m' -e 'local dogs = ngx.shared.dogs
 dogs:get({})
 '
```
不出意料，果然报错了：
```
ERROR: (command line -e):2: bad argument #1 to 'get' (string expected, got table)
```
综上，我们可以得出结论：get API 接受的 key 类型为字符串和数字。那么入参 key 的长度是否有限制呢？这里其实也有一个对应的测试案例，我们一起来看一下：

```
=== TEST 67: get a too-long key
 --- http_config
     lua_shared_dict dogs 1m;
 --- config
     location = /test {
         content_by_lua '
             local dogs = ngx.shared.dogs
             local ok, err = dogs:get(string.rep("a", 65536))
             if not ok then
                 ngx.say("not ok: ", err)
                 return
             end
             ngx.say("ok")
         ';
     }
 --- request
 GET /test
 --- response_body
 not ok: key too long
 --- no_error_log
 [error]

```
很显然，字符串长度为 65536 的时候，就会被提示 key 太长了。你可以试下把长度改为 65535，虽然只少了 1 个字节，却不会再报错了。这就说明，key 的最大长度正是 65535。

### 写在最后
OpenResty 现在的官方文档只有英文版本，国内工程师在阅读时，难免会因为语言问题，抓不住重点，甚至误解其中的内容。但越是这样，越没有捷径可走，你更应该仔细地把文档从头到尾读完，并在有疑问时，结合测试案例集和自己的尝试，去确定出答案。这才是辅助我们学习 OpenResty 的正确途径。

最后，我想提醒一下，在 OpenResty 的 API 中，凡是返回值中带有错误信息的，都必须有变量来接收并做错误处理，否则前方一定会有坑等你跳进去。比如把出错的连接放入了连接池，或者在 API 调用失败的情况下继续后面的逻辑，总之一定让人叫苦不迭。


## 17 | 为什么能成为更好的Web服务器？动态处理请求和响应是关键

今天这节课，我们来看一下 OpenResty 如何处理终端请求和响应。虽然 OpenResty 是基于 NGINX 的 Web 服务器，但它与 NGINX 却有本质的不同：NGINX 由静态的配置文件驱动，而 OpenResty 是由 Lua API 驱动的，所以能提供更多的灵活性和可编程性。下面，就让我来带你领略 Lua API 带来的好处吧。


### API 分类
首先我们要知道，OpenResty 的 API 主要分为下面几个大类：

处理请求和响应；SSL 相关；shared dict；cosocket；处理四层流量；process 和 worker；获取 NGINX 变量和配置；字符串、时间、编解码等通用功能。

这里，我建议你同时打开 OpenResty 的 Lua API 文档，对照着其中的 [API 列表](https://github.com/openresty/lua-nginx-module/#nginx-api-for-lua)  ，看看是否能和这个分类联系起来。

OpenResty 的 API 不仅仅存在于 lua-nginx-module 项目中，也存在于 lua-resty-core 项目中，比如 ngx.ssl、ngx.base64、ngx.errlog、ngx.process、ngx.re.split、ngx.resp.add_header、ngx.balancer、ngx.semaphore、ngx.ocsp 这些 API 。而对于不在 lua-nginx-module 项目中的 API，你需要单独 require 才能使用。举个例子，比如你想使用 split 这个字符串分割函数，就需要按照下面的方法来调用：

```
$ resty -e 'local ngx_re = require "ngx.re"
 local res, err = ngx_re.split("a,b,c,d", ",", nil, {pos = 5})
 print(res)
 '
```
当然，这可能会给你带来一个困惑：在 lua-nginx-module 项目中，明明有 ngx.re.sub、ngx.re.find 等好几个 ngx.re 开头的 API，为什么单单是 ngx.re.split 这个 API ，需要 require 后才能使用呢？事实上，在前面 lua-resty-core 章节中，我们也提到过，OpenResty 新的 API 都是通过 FFI 的方式在 lua-rety-core 仓库中实现的，所以难免就会存在这种割裂感。自然，我也很期待 lua-nginx-module 和 lua-resty-core 这两个项目以后可以合并，彻底解决此类问题。


### 请求
接下来，我们具体了解下 OpenResty 是如何处理终端请求和响应的。先来看下处理请求的 API，不过，以 ngx.req 开头的 API 有 20 多个，该怎么下手呢？我们知道，HTTP 请求报文由三部分组成：请求行、请求头和请求体，所以下面我就按照这三部分来对 API 做介绍。

### 请求行
首先是请求行，HTTP 的请求行中包含请求方法、URI 和 HTTP 协议版本。在 NGINX 中，你可以通过内置变量的方式，来获取其中的值；而在 OpenResty 中对应的则是 ngx.var.* 这个 API。我们来看两个例子。

 $scheme 这个内置变量，在 NGINX 中代表协议的名字，是 “http” 或者 “https”；而在 OpenResty 中，你可以通过 ngx.var.scheme 来返回同样的值。

 $request_method 代表的是请求的方法，“GET”、“POST” 等；而在 OpenResty 中，你可以通过 ngx.var. request_method 来返回同样的值。

至于完整的 NGINX 内置变量列表，你可以访问 NGINX 的官方文档来获取：http://nginx.org/en/docs/http/ngx_http_core_module.html#variables。

那么问题就来了：既然可以通过ngx.var.* 这种返回变量值的方法，来得到请求行中的数据，为什么 OpenResty 还要单独提供针对请求行的 API 呢？这其实是很多方面因素的综合考虑结果：

首先是对性能的考虑。ngx.var 的效率不高，不建议反复读取；也有对程序友好的考虑，ngx.var 返回的是字符串，而非 Lua 对象，遇到获取 args 这种可能返回多个值的情况，就不好处理了；另外是对灵活性的考虑，绝大部分的 ngx.var 是只读的，只有很少数的变量是可写的，比如 $args 和 limit_rate，可很多时候，我们会有修改 method、URI 和 args 的需求。

所以， OpenResty 提供了多个专门操作请求行的 API，它们可以对请求行进行改写，以便后续的重定向等操作。我们先来看下，如何通过 API 来获取 HTTP 协议版本号。OpenResty 的 API ngx.req.http_version 和 NGINX 的 $server_protocol 变量的作用一样，都是返回 HTTP 协议的版本号。不过这个 API 的返回值是数字格式，而非字符串，可能的值是 2.0、1.0、1.1 和 0.9，如果结果不在这几个值的范围内，就会返回 nil。再来看下获取请求行中的请求方法。刚才我们提到过，ngx.req.get_method 和 NGINX 的 $request_method 变量的作用、返回值一样，都是字符串格式的方法名。但是，改写当前 HTTP 请求方法的 API，也就是 ngx.req.set_method，它接受的参数格式却并非字符串，而是内置的数字常量。比如，下面的代码，把请求方法改写为 POST：

```
ngx.req.set_method(ngx.HTTP_POST)
```
为了验证 ngx.HTTP_POST 这个内置常量，确实是数字而非字符串，你可以打印出它的值，看输出是否为 8：
```
$ resty -e 'print(ngx.HTTP_POST)'
```
这样一来，get 方法的返回值为字符串，而 set 方法的输入值却是数字，就很容易让你在写代码的时候想当然了。如果是 set 时候传值混淆的情况还好，API 会崩溃报出 500 的错误；但如果是下面这种判断逻辑的代码：

```
if (ngx.req.get_method() == ngx.HTTP_POST) then
    -- do something
 end
```
这种代码是可以正常运行的，不会报出任何错误，甚至在 code review 时也很难发现。不幸的是，我就犯过类似的错误，对此记忆犹新：当时已经经过了两轮 code review，还有不完整的测试案例尝试覆盖，然而，最终还是因为线上环境异常才追踪到了这里。碰到这类情况，除了自己多小心，或者再多一层封装外，并没有什么有效的方法来解决。平常你在设计自己的业务 API 时，也可以多做一些这方面的考虑，尽量保持 get、set 方法的参数格式一致，即使这会牺牲一些性能。另外，在改写请求行的方法中，还有 ngx.req.set_uri 和 ngx.req.set_uri_args 这两个 API，可以用来改写 uri 和 args。我们来看下这个 NGINX 配置：


```
 rewrite ^ /foo?a=3? break;
```
那么，如何用等价的 Lua API 来解决呢？答案就是下面这两行代码。
```
ngx.req.set_uri_args("a=3")
ngx.req.set_uri("/foo")

```
其实，如果你看过官方文档，就会发现 ngx.req.set_uri 还有第二个参数：jump，默认是 false。如果设置为 true，就等同于把 rewrite 指令的 flag 设置为 last，而非上面示例中的 break。不过，我个人并不喜欢 rewrite 指令的 flag 配置，看不懂也记不住，远没有代码来的直观和好维护。

### 请求头
再来看下和请求头有关的 API。我们知道，HTTP 的请求头是 key : value 格式的，比如：

```
Accept: text/css,*/*;q=0.1
Accept-Encoding: gzip, deflate, br
```
在 OpenResty 中，你可以使用 ngx.req.get_headers 来解析和获取请求头，返回值的类型则是 table：

```
local h, err = ngx.req.get_headers()
 
  if err == "truncated" then
      -- one can choose to ignore or reject the current request here
  end
 
  for k, v in pairs(h) do
      ...
  end
```
这里默认返回前 100 个 header，如果请求头超过了 100 个，就会返回 truncated 的错误信息，由开发者自己决定如何处理。你可能会好奇为什么会有这样的处理，这一点先留个悬念，在后面安全漏洞的章节中我会提到。不过，需要注意的是，OpenResty 并没有提供获取某一个指定请求头的 API，也就是没有 ngx.req.header['host'] 这种形式。如果你有这样的需求，那就需要借助 NGINX 的变量 $http_xxx 来实现了，那么在 OpenResty 中，就是 ngx.var.http_xxx 这样的获取方式。看完了获取请求头，我们再来看看应该如何改写和删除请求头，这两种操作的 API 其实都很直观：

```
ngx.req.set_header("Content-Type", "text/css")
ngx.req.clear_header("Content-Type")
```
当然，官方文档中也提到了其他方法来删除请求头，比如把 header 的值设置为 nil 等，但为了代码更加清晰的考虑，我还是推荐统一用 clear_header 来操作。

### 请求体
最后来看请求体。出于性能考虑，OpenResty 不会主动读取请求体的内容，除非你在 nginx.conf 中强制开启了 lua_need_request_body 指令。此外，对于比较大的请求体，OpenResty 会把内容保存在磁盘的临时文件中，所以读取请求体的完整流程是下面这样的：
```
ngx.req.read_body()
local data = ngx.req.get_body_data()
if not data then
    local tmp_file = ngx.req.get_body_file()
     -- io.open(tmp_file)
     -- ...
 end
```
这段代码中有读取磁盘文件的 IO 阻塞操作。你应该根据实际情况来调整 client_body_buffer_size 配置的大小（64 位系统下默认是 16 KB），尽量减少阻塞的操作；你也可以把 client_body_buffer_size 和 client_max_body_size 配置成一样的，完全在内存中来处理，当然，这取决于你内存的大小和处理的并发请求数。另外，请求体也可以被改写，ngx.req.set_body_data 和 ngx.req.set_body_file 这两个 API，分别接受字符串和本地磁盘文件做为输入参数，来完成请求体的改写。不过，这类操作并不常见，你可以查看文档来获取更详细的内容。

### 响应
处理完请求后，我们就需要发送响应返回给客户端了。和请求报文一样，响应报文也由几个部分组成，即状态行、响应头和响应体。同样的，接下来我会按照这三部分来介绍相应的 API。

### 状态行
状态行中，我们主要关注的是状态码。在默认情况下，返回的 HTTP 状态码是 200，也就是 OpenResty 中内置的常量 ngx.HTTP_OK。但在代码的世界中，处理异常情况的代码总是占比最多的。如果你检测了请求报文，发现这是一个恶意的请求，那么你需要终止请求:

```
 ngx.exit(ngx.HTTP_BAD_REQUEST)
```
不过，OpenResty 的 HTTP 状态码中，有一个特别的常量：ngx.OK。当 ngx.exit(ngx.OK) 时，请求会退出当前处理阶段，进入下一个阶段，而不是直接返回给客户端。当然，你也可以选择不退出，只使用 ngx.status 来改写状态码，比如下面这样的写法：
```
ngx.status = ngx.HTTP_FORBIDDEN
```
如果你想了解更多的状态码常量，可以从[文档](https://github.com/openresty/lua-nginx-module/#http-status-constants)中查询到。


### 响应头
说到响应头，其实，你有两种方法来设置它。第一种是最简单的：

```
ngx.header.content_type = 'text/plain'
ngx.header["X-My-Header"] = 'blah blah'
ngx.header["X-My-Header"] = nil -- 删除
```
这里的 ngx.header 保存了响应头的信息，可以读取、修改和删除。第二种设置响应头的方法是 ngx_resp.add_header ，来自 lua-resty-core 仓库，它可以增加一个头信息，用下面的方法来调用：

```
local ngx_resp = require "ngx.resp"
ngx_resp.add_header("Foo", "bar")

```
与第一种方法的不同之处在于，add header 不会覆盖已经存在的同名字段。

### 响应体
最后看下响应体，在 OpenResty 中，你可以使用 ngx.say 和 ngx.print 来输出响应体：

```
ngx.say('hello, world')
```
这两个 API 的功能是一致的，唯一的不同在于， ngx.say 会在最后多一个换行符。为了避免字符串拼接的低效，ngx.say / ngx.print 不仅支持字符串作为参数，也支持数组格式：

```
$ resty -e 'ngx.say({"hello", ", ", "world"})'
 hello, world
```
这样在 Lua 层面就跳过了字符串的拼接，把这个它不擅长的事情丢给了 C 函数去处理。


### 写在最后
到此，让我们回顾下今天的内容。我们按照请求报文和响应报文的内容，依次介绍了与之相关的 OpenResty API。你可以看得出来，和 NGINX 的指令相比，OpenResty API 更加灵活和强大。

## 18 | worker间的通信法宝：最重要的数据结构之shared dict

前面我们讲过，在 Lua 中， table 是唯一的数据结构。与之对应的一个事实是，共享内存字典 shared dict，是你在 OpenResty 编程中最为重要的数据结构。它不仅支持数据的存放和读取，还支持原子计数和队列操作。基于 shared dict，你可以实现多个 worker 之间的缓存和通信，以及限流限速、流量统计等功能。你可以把 shared dict 当作简单的 Redis 来使用，只不过 shared dict 中的数据不能持久化，所以你存放在其中的数据，一定要考虑到丢失的情况。

### 数据共享的几种方式
在编写 OpenResty Lua 代码的过程中，你不可避免地会遇到，在一个请求的不同阶段、不同 worker 之间共享数据的情况，还可能需要在 Lua 和 C 代码之间共享数据。所以，在正式介绍 shared dict 的 API 之前，先让我们了解一下，OpenResty 中常见的几种数据共享的方法；并学会根据实际情况，选择较为合适的数据共享方式。

第一种是 Nginx 中的变量。它可以在 Nginx C 模块之间共享数据，自然的，也可以在 C 模块和 OpenResty 提供的 lua-nginx-module 之间共享数据，比如下面这段代码：

```
location /foo {
     set $my_var ''; # this line is required to create $my_var at config time
     content_by_lua_block {
         ngx.var.my_var = 123;
         ...
     }
 }
```
不过，使用 Nginx 变量这种方式来共享数据是比较慢的，因为它涉及到 hash 查找和内存分配。同时，这种方法有其局限性，只能用来存储字符串，不能支持复杂的 Lua 类型。

第二种是ngx.ctx，可以在同一个请求的不同阶段之间共享数据。它其实就是一个普通的 Lua 的 table，所以速度很快，还可以存储各种 Lua 的对象。它的生命周期是请求级别的，当一个请求结束的时候，ngx.ctx 也会跟着被销毁掉。下面是一个典型的使用场景，我们用 ngx.ctx 来缓存 Nginx 变量 这种昂贵的调用，并在不同阶段都可以使用到它：


```
location /test {
     rewrite_by_lua_block {
         ngx.ctx.host = ngx.var.host
     }
     access_by_lua_block {
        if (ngx.ctx.host == 'openresty.org') then
            ngx.ctx.host = 'test.com'
        end
     }
     content_by_lua_block {
         ngx.say(ngx.ctx.host)
     }
 }
```
这时，如果你使用 curl 访问的话：


```
curl -i 127.0.0.1:8080/test -H 'host:openresty.org'
```
就会打印出 test.com，可以表明 ngx.ctx 的确是在不同阶段共享了数据。当然，你还可以自己动手修改上面的例子，保存 table 等更复杂的对象，而非简单的字符串，看看它是否满足你的预期。不过，这里需要特别注意的是，正因为 ngx.ctx 的生命周期是请求级别的，所以它并不能在模块级别进行缓存。比如，我在 foo.lua 文件中这样使用就是错误的：

```
local ngx_ctx = ngx.ctx

local function bar()
    ngx_ctx.host =  'test.com'
end
```
我们应该在函数级别进行调用和缓存：


```
local ngx = ngx

local function bar()
    ngx_ctx.host =  'test.com'
end

```
ngx.ctx 还有很多的细节，后面的性能优化部分，我们再继续探讨。


接着往下看，第三种方法是使用模块级别的变量，在同一个 worker 内的所有请求之间共享数据。跟前面的 Nginx 变量和 ngx.ctx 不一样，这种方法有些不太好理解。不过别着急，概念抽象，代码先行，让我们先来看个例子，弄明白什么是 模块级别的变量：


```
-- mydata.lua
 local _M = {}

 local data = {
     dog = 3,
     cat = 4,
     pig = 5,
 }

 function _M.get_age(name)
     return data[name]
 end

 return _M
```
在 nginx.conf 的配置如下：



```
location /lua {
     content_by_lua_block {
         local mydata = require "mydata"
         ngx.say(mydata.get_age("dog"))
     }
 }
```
在这个示例中，mydata 就是一个模块，它只会被 worker 进程加载一次，之后，这个 worker 处理的所有请求，都会共享 mydata 模块的代码和数据。自然，mydata 模块中的 data 这个变量，就是 模块级别的变量，它位于模块的 top level，也就是模块最开始的位置，所有函数都可以访问到它。所以，你可以把需要在请求间共享的数据，放在模块的 top level 变量中。不过，需要特别注意的是，一般我们只用这种方式来保存只读的数据。如果涉及到写操作，你就要非常小心了，因为可能会有 race condition，这是非常难以定位的 bug。


我们可以通过下面这个最简化的例子来体会下：
```
-- mydata.lua
 local _M = {}

 local data = {
     dog = 3,
     cat = 4,
     pig = 5,
 }

 function _M.incr_age(name)
     data[name]  = data[name] + 1
    return data[name]
 end

 return _M
```
在模块中，我们增加了 incr_age 这个函数，它会对 data 这个表的数据进行修改。然后，在调用的代码中，我们增加了最关键的一行 ngx.sleep(5)，这个 sleep 是一个 yield 操作：

```
location /lua {
     content_by_lua_block {
         local mydata = require "mydata"
         ngx.say(mydata. incr_age("dog"))
         ngx.sleep(5) -- yield API
         ngx.say(mydata. incr_age("dog"))
     }
 }
```
如果没有这行 sleep 代码（也可以是其他的非阻塞 IO 操作，比如访问 Redis 等），就不会有 yield 操作，也就不会产生竞争，那么，最后输出的数字就是顺序的。但当我们加了这行代码后，哪怕只是在 sleep 的 5 秒钟内，也很可能就有其他请求调用了mydata. incr_age 函数，修改了变量的值，从而导致最后输出的数字不连续。要知道，在实际的代码中，逻辑不会这么简单，bug 的定位也一定会困难得多。所以，除非你很确定这中间没有 yield 操作，不会把控制权交给 Nginx 事件循环，否则，我建议你还是保持对模块级别变量的只读。

第四种，也是最后一种方法，用 shared dict 来共享数据，这些数据可以在多个 worker 之间共享。这种方法是基于红黑树实现的，性能很好，但也有自己的局限性——你必须事先在 Nginx 的配置文件中，声明共享内存的大小，并且这不能在运行期更改：

```
lua_shared_dict dogs 10m;
```
shared dict 同样只能缓存字符串类型的数据，不支持复杂的 Lua 数据类型。这也就意味着，当我需要存放 table 等复杂的数据类型时，我将不得不使用 json 或者其他的方法，来序列化和反序列化，这自然会带来不小的性能损耗。总之，还是那句话，这里并没有银弹，不存在一种完美的数据共享方式，你需要根据需求和场景，来组合多个方法来使用。


### 共享字典
上面数据共享的部分，我们花了很多的篇幅来学，有的人可能纳闷儿：它们看上去和 shared dict 没有直接关系，是不是有些文不对题呢？事实并非如此，你可以自己想一下，为什么 OpenResty 中要有 shared dict 的存在呢？回忆一下刚刚讲的几种方法，前面三种数据共享的范围都是在请求级别，或者单个 worker 级别。所以，在当前的 OpenResty 的实现中，只有 shared dict 可以完成 worker 间的数据共享，并借此实现 worker 之间的通信，这也是它存在的价值。在我看来，明白一个技术为何存在，并弄清楚它和别的类似技术之间的差异和优势，远比你只会熟练调用它提供的 API 更为重要。这种技术视野，会给你带来一定程度的远见和洞察力，这也可以说是工程师和架构师的一个重要区别。回到共享字典本身，它对外提供了 20 多个 Lua API，不过所有的这些 API 都是原子操作，你不用担心多个 worker 和高并发的情况下的竞争问题。

这些 API 都有官方详细的[文档](https://github.com/openresty/lua-nginx-module#ngxshareddict)，我就不再一一赘述了。这里我想再强调一下，任何技术课程的学习，都不能代替对官方文档的仔细研读。这些耗时的笨功夫，每个人都省不掉的。继续看 shared dict 的 API，这些 API 可以分为下面三个大类，也就是字典读写类、队列操作类和管理类这三种。

### 字典读写类
首先来看字典读写类。在最初的版本中，只有字典读写类的 API，它们也是共享字典最常用的功能。下面是一个最简单的示例：


```
$ resty --shdict='dogs 1m' -e 'local dict = ngx.shared.dogs
                               dict:set("Tom", 56)
                               print(dict:get("Tom"))'
```
除了 set 外，OpenResty 还提供了 safe_set、add、safe_add、replace 这四种写入的方法。这里safe 前缀的含义是，在内存占满的情况下，不根据 LRU 淘汰旧的数据，而是写入失败并返回 no memory 的错误信息。除了 get 外，OpenResty 还提供了 get_stale 的读取数据的方法，相比 get 方法，它多了一个过期数据的返回值：

```
value, flags, stale = ngx.shared.DICT:get_stale(key)
```
你还可以调用 delete 方法来删除指定的 key，它和 set(key, nil) 是等价的。


### 队列操作类
再来看队列操作，它是 OpenResty 后续新增的功能，提供了和 Redis 类似的接口。队列中的每一个元素，都用 ngx_http_lua_shdict_list_node_t 来描述：


```
typedef struct { 
    ngx_queue_t queue; 
    uint32_t value_len; 
    uint8_t value_type; 
    u_char data[1]; 
} ngx_http_lua_shdict_list_node_t;
```
我把这些队列操作 API 的 [PR](https://github.com/openresty/lua-nginx-module/pull/586/files)  贴在了文章中，如果你对此感兴趣，可以跟着文档、测试案例和源码，来分析具体的实现。不过，下面这 5 个队列 API，在文档中并没有对应的代码示例，这里我简单介绍一下：

lpush/rpush，表示在队列两端增加元素；lpop/rpop，表示在队列两端弹出元素；llen，表示返回队列的元素数量。

别忘了我们上节课讲过的另一个利器——测试案例。如果文档中没有，我们通常可以在测试案例中找到对应的代码。队列相关的测试，正是在 145-shdict-list.t 这个文件中：


```
=== TEST 1: lpush & lpop
--- http_config
    lua_shared_dict dogs 1m;
--- config
    location = /test {
        content_by_lua_block {
            local dogs = ngx.shared.dogs

            local len, err = dogs:lpush("foo", "bar")
            if len then
                ngx.say("push success")
            else
                ngx.say("push err: ", err)
            end

            local val, err = dogs:llen("foo")
            ngx.say(val, " ", err)

            local val, err = dogs:lpop("foo")
            ngx.say(val, " ", err)

            local val, err = dogs:llen("foo")
            ngx.say(val, " ", err)

            local val, err = dogs:lpop("foo")
            ngx.say(val, " ", err)
        }
    }
--- request
GET /test
--- response_body
push success
1 nil
bar nil
0 nil
nil nil
--- no_error_log
[error]
```
### 管理类
最后要说的管理类 API 也是后续新增的，属于社区呼声比较高的需求。其中，共享内存的使用情况就是最典型的例子。比如，用户申请了 100M 的空间作为 shared dict，那么这 100M 是否够用呢？里面存放了多少 key？具体是哪些 key 呢？这几个都是非常现实的问题。对于这类问题，OpenResty 的官方态度，是希望用户使用火焰图来解决，即非侵入式，保持代码基的高效和整洁，而不是提供侵入式的 API 来直接返回结果。但站在使用者友好角度来考虑，这些管理类 API 还是非常有必要的。毕竟开源项目是用来解决产品需求的，并不是展示技术本身的。所以，下面我们就来了解一下，这几个后续增加的管理类 API。首先是 get_keys(max_count?)，它默认也只返回前 1024 个 key；如果你把 max_count 设置为 0，那就返回所有 key。然后是 capacity 和 free_space，这两个 API 都属于 lua-resty-core 仓库，所以需要你 require 后才能使用：

```
require "resty.core.shdict"

 local cats = ngx.shared.cats
 local capacity_bytes = cats:capacity()
 local free_page_bytes = cats:free_space()
```
它们分别返回的，是共享内存的大小（也就是 lua_shared_dict 中配置的大小）和空闲页的字节数。因为 shared dict 是按照页来分配的，即使 free_space 返回为 0，在已经分配的页面中也可能存在空间，所以它的返回值并不能代表共享内存实际被占用的情况。

### 写在最后
在实际的开发中，我们经常会用到多级缓存，OpenResty 的官方项目中也有对缓存的封装。你能找出来是哪几个项目吗？或者你知道一些其他缓存封装的 lua-resty 库吗？


## 19 | OpenResty 的核心和精髓：cosocket

今天我们来学习下 OpenResty 中的核心技术：cosocket。其实在前面的课程中，我们就已经多次提到过它了，cosocket 是各种 lua-resty-* 非阻塞库的基础，没有 cosocket，开发者就无法用 Lua 来快速连接各种外部的网络服务。在早期的 OpenResty 版本中，如果你想要去与 Redis、memcached 这些服务交互的话，需要使用 redis2-nginx-module、redis-nginx-module 和 memc-nginx-module这些 C 模块. 这些模块至今仍然在 OpenResty 的发行包中。不过，cosocket 功能加入以后，它们都已经被 lua-resty-redis 和 lua-resty-memcached 替代，基本上没人再去使用 C 模块连接外部服务了。

### 什么是 cosocket？
那究竟什么是 cosocket 呢？事实上，cosocket 是 OpenResty 中的专有名词，是把协程和网络套接字的英文拼在一起形成的，即 cosocket = coroutine + socket。所以，你可以把 cosocket 翻译为“协程套接字”。cosocket 不仅需要 Lua 协程特性的支持，也需要 Nginx 中非常重要的事件机制的支持，这两者结合在一起，最终实现了非阻塞网络 I/O。另外，cosocket 支持 TCP、UDP 和 Unix Domain Socket。如果我们在 OpenResty 中调用一个 cosocket 相关函数，内部实现便是下面这张图的样子：

![img](https://static001.geekbang.org/resource/image/80/06/80d16e11d2750d6e4127445c126c9f06.png?wh=1692*756)

记性比较好的同学应该发现了，在前面 OpenResty 原理和基本概念的那节课里，我也用过这张图。从图中你可以看到，用户的 Lua 脚本每触发一个网络操作，都会有协程的 yield 以及 resume。遇到网络 I/O 时，它会交出控制权（yield），把网络事件注册到 Nginx 监听列表中，并把权限交给 Nginx；当有 Nginx 事件达到触发条件时，便唤醒对应的协程继续处理（resume）。OpenResty 正是以此为蓝图，封装实现 connect、send、receive 等操作，形成了我们如今见到的 cosocket API。下面，我就以处理 TCP 的 API 为例来介绍一下。处理 UDP 和 Unix Domain Socket ，与 TCP 的接口基本是一样的。


### cosocket API 和指令简介
TCP 相关的 cosocket API 可以分为下面这几类。

创建对象：ngx.socket.tcp。设置超时：tcpsock:settimeout 和 tcpsock:settimeouts。建立连接：tcpsock:connect。发送数据：tcpsock:send。接受数据：tcpsock:receive、tcpsock:receiveany 和 tcpsock:receiveuntil。连接池：tcpsock:setkeepalive。关闭连接：tcpsock:close。

我们还要特别注意下，这些 API 可以使用的上下文：

```
rewrite_by_lua*, access_by_lua*, content_by_lua*, ngx.timer.*, ssl_certificate_by_lua*, ssl_session_fetch_by_lua*_
```
这里我还要强调一点，归咎于 Nginx 内核的各种限制，cosocket API 在 set_by_lua*， log_by_lua*， header_filter_by_lua* 和 body_filter_by_lua* 中是无法使用的。而在 init_by_lua* 和 init_worker_by_lua* 中暂时也不能用，不过 Nginx 内核对这两个阶段并没有限制，后面可以增加对这它们的支持。此外，与这些 API 相关的，还有 8 个 lua_socket_ 开头的 Nginx 指令，我们简单来看一下。


lua_socket_connect_timeout：连接超时，默认 60 秒。lua_socket_send_timeout：发送超时，默认 60 秒。lua_socket_send_lowat：发送阈值（low water），默认为 0。lua_socket_read_timeout： 读取超时，默认 60 秒。lua_socket_buffer_size：读取数据的缓存区大小，默认 4k/8k。lua_socket_pool_size：连接池大小，默认 30。lua_socket_keepalive_timeout：连接池 cosocket 对象的空闲时间，默认 60 秒。lua_socket_log_errors：cosocket 发生错误时，是否记录日志，默认为 on。

这里你也可以看到，有些指令和 API 的功能一样的，比如设置超时时间和连接池大小等。不过，如果两者有冲突的话，API 的优先级高于指令，会覆盖指令设置的值。所以，一般来说，我们都推荐使用 API 来做设置，这样也会更加灵活。接下来，我们一起来看一个具体的例子，弄明白到底如何使用这些 cosocket API。下面这段代码的功能很简单，是发送 TCP 请求到一个网站，并把返回的内容打印出来：


```
$ resty -e 'local sock = ngx.socket.tcp()
        sock:settimeout(1000)  -- one second timeout
        local ok, err = sock:connect("www.baidu.com", 80)
        if not ok then
            ngx.say("failed to connect: ", err)
            return
        end

        local req_data = "GET / HTTP/1.1\r\nHost: www.baidu.com\r\n\r\n"
        local bytes, err = sock:send(req_data)
        if err then
            ngx.say("failed to send: ", err)
            return
        end

        local data, err, partial = sock:receive()
        if err then
            ngx.say("failed to receive: ", err)
            return
        end

        sock:close()
        ngx.say("response is: ", data)'
```
我们来具体分析下这段代码。首先，通过 ngx.socket.tcp() ，创建  TCP 的 cosocket 对象，名字是 sock。然后，使用 settimeout() ，把超时时间设置为 1 秒。注意这里的超时没有区分 connect、receive，是统一的设置。接着，使用 connect() 去连接指定网站的 80 端口，如果失败就直接退出。连接成功的话，就使用 send() 来发送构造好的数据，如果发送失败就退出。发送数据成功的话，就使用 receive() 来接收网站返回的数据。这里 receive() 的默认参数值是 *l，也就是只返回第一行的数据；如果参数设置为了*a，就是持续接收数据，直到连接关闭；最后，调用 close() ，主动关闭 socket 连接。

你看，短短几步就可以完成，使用 cosocket API 来做网络通信，就是这么简单。不过，不能满足于此，接下来，我们对这个示例再做一些调整。

**第一个动作，对 socket 连接、发送和读取这三个动作，分别设置超时时间。**

我们刚刚用的settimeout() ，作用是把超时时间统一设置为一个值。如果要想分开设置，就需要使用 settimeouts() 函数，比如下面这样的写法：

```
sock:settimeouts(1000, 2000, 3000) 
```
这行代码表示连接超时为 1 秒，发送超时为 2 秒，读取超时为 3 秒。在 OpenResty 和 lua-resty 库中，大部分和时间相关的 API 的参数，都以毫秒为单位，但也有例外，需要你在调用的时候特别注意下。


**第二个动作，receive 接收指定大小的内容**。刚刚说了，receive() 接口可以接收一行数据，也可以持续接收数据。不过，如果你只想接收 10K 大小的数据，应该怎么设置呢？这时，receiveany() 闪亮登场。它就是专为满足这种需求而设计的，一起来看下面这行代码：

```
local data, err, partial = sock:receiveany(10240)
```
这段代码就表示，最多只接收 10K 的数据。当然，关于 receive，还有另一个很常见的用户需求，那就是一直获取数据，直到遇到指定字符串才停止。receiveuntil() 专门用来解决这类问题，它不会像 receive() 和 receiveany() 一样返回字符串，而会返回一个迭代器。这样，你就可以在循环中调用它来分段读取匹配到的数据，当读取完毕时，就会返回 nil。下面就是一个例子：

```
 local reader = sock:receiveuntil("\r\n")

 while true do
     local data, err, partial = reader(4)
     if not data then
         if err then
             ngx.say("failed to read the data stream: ", err)
             break
         end

         ngx.say("read done")
         break
     end
     ngx.say("read chunk: [", data, "]")
 end
```
这段代码中的 receiveuntil 会返回 \r\n 之前的数据，并通过迭代器每次读取其中的 4 个字节，也就实现了我们想要的功能。

**第三个动作，不直接关闭 socket，而是放入连接池中。**

我们知道，没有连接池的话，每次请求进来都要新建一个连接，就会导致 cosocket 对象被频繁地创建和销毁，造成不必要的性能损耗。为了避免这个问题，在你使用完一个 cosocket 后，可以调用 setkeepalive() 放到连接池中，比如下面这样的写法：

```
local ok, err = sock:setkeepalive(2 * 1000, 100)
if not ok then
    ngx.say("failed to set reusable: ", err)
end
```
这段代码设置了连接的空闲时间为 2 秒，连接池的大小为 100。这样，在调用 connect() 函数时，就会优先从连接池中获取 cosocket 对象。不过，关于连接池的使用，有两点需要我们注意一下。

第一，不能把发生错误的连接放入连接池，否则下次使用时，就会导致收发数据失败。这也是为什么我们需要判断每一个 API 调用是否成功的一个原因。第二，要搞清楚连接的数量。连接池是 worker 级别的，每个 worker 都有自己的连接池。所以，如果你有 10 个 worker，连接池大小设置为 30，那么对于后端的服务来讲，就等于有 300 个连接。

### 写在最后
总结一下，今天我们学习了 cosocket 的基本概念，以及相关的指令和 API，并通过一个实际的例子，熟悉了 TCP 相关的 API 应该如何使用。而 UDP 和 Unix Domain Socket 的使用类似于 TCP，弄明白今天所学，你基本上都能迎刃而解了。从中你应该也能感受到，cosocket 用起来还是比较容易上手的，而且用好它，你就可以去连接各种外部的服务了，可以说是给 OpenResty 插上了想象的翅膀。


## 20 | 超越 Web 服务器：特权进程和定时任务

前面我们介绍了 OpenResty API、共享字典缓存和 cosocket。它们实现的功能，都还在 Nginx 和 Web 服务器的范畴之内，算是提供了开发成本更低、更容易维护的一种实现，提供了可编程的 Web 服务器。不过，OpenResty 并不满足于此。我们今天就挑选几个，OpenResty 中超越 Web 服务器的功能来介绍一下。它们分别是定时任务、特权进程和非阻塞的 ngx.pipe。


### 定时任务
在 OpenResty 中，我们有时候需要在后台定期地执行某些任务，比如同步数据、清理日志等。如果让你来设计，你会怎么做呢？最容易想到的方法，便是对外提供一个 API 接口，在接口中完成这些任务；然后用系统的 crontab 定时调用 curl，来访问这个接口，进而曲线地实现这个需求。不过，这样一来不仅会有割裂感，也会给运维带来更高的复杂度。所以， OpenResty 提供了 ngx.timer 来解决这类需求。你可以把ngx.timer ，看作是 OpenResty 模拟的客户端请求，用以触发对应的回调函数。

其实，OpenResty 的定时任务可以分为下面两种：ngx.timer.at，用来执行一次性的定时任务；ngx.time.every，用来执行固定周期的定时任务。

还记得上节课最后我留下的思考题吗？问题是如何突破 init_worker_by_lua 中不能使用 cosocket 的限制，这个答案其实就是 ngx.timer。下面这段代码，就是启动了一个延时为 0 的定时任务。它启动了回调函数 handler，并在这个函数中，用 cosocket 去访问一个网站：

```
init_worker_by_lua_block {
        local function handler()
            local sock = ngx.socket.tcp()
            local ok, err = sock:connect(“www.baidu.com", 80)
        end

        local ok, err = ngx.timer.at(0, handler)
    }
```
这样，我们就绕过了 cosocket 在这个阶段不能使用的限制。再回到这部分开头时我们提到的的用户需求，ngx.timer.at 并没有解决周期性运行这个需求，在上面的代码示例中，它是一个一次性的任务。那么，又该如何做到周期性运行呢？表面上来看，基于 ngx.timer.at 这个 API 的话，你有两个选择：

你可以在回调函数中，使用一个 while true 的死循环，执行完任务后 sleep 一段时间，自己来实现周期任务；你还可以在回调函数的最后，再创建另外一个新的 timer。

不过，在做出选择之前，有一点我们需要先明确下：timer 的本质是一个请求，虽然这个请求不是终端发起的；而对于请求来讲，在完成自己的任务后它就要退出，不能一直常驻，否则很容易造成各种资源的泄漏。所以，第一种使用 while true 来自行实现周期任务的方案并不靠谱。第二种方案虽然是可行的，但递归地创建 timer ，并不容易让人理解。那么，是否有更好的方案呢？其实，OpenResty 后面新增的 ngx.time.every API，就是专门为了解决这个问题而出现的，它是更加接近 crontab 的解决方案。但美中不足的是，在启动了一个 timer 之后，你就再也没有机会来取消这个定时任务了，毕竟ngx.timer.cancel 还是一个 todo 的功能。这时候，你就会面临一个问题：定时任务是在后台运行的，并且无法取消；如果定时任务的数量很多，就很容易耗尽系统资源。所以，OpenResty 提供了 lua_max_pending_timers 和 lua_max_running_timers 这两个指令，来对其进行限制。前者代表等待执行的定时任务的最大值，后者代表当前正在运行的定时任务的最大值。你也可以通过 Lua API，来获取当前等待执行和正在执行的定时任务的值，下面是两个示例：

```
content_by_lua_block {
            ngx.timer.at(3, function() end)
            ngx.say(ngx.timer.pending_count())
        }
```
这段代码会打印出 1，表示有 1 个计划任务正在等待被执行。

```
content_by_lua_block {
            ngx.timer.at(0.1, function() ngx.sleep(0.3) end)
            ngx.sleep(0.2)
            ngx.say(ngx.timer.running_count())
        }
```
这段代码会打印出 1，表示有 1 个计划任务正在运行中。

### 特权进程
接着来看特权进程。我们都知道 Nginx 主要分为 master 进程和 worker 进程，其中，真正处理用户请求的是 worker 进程。我们可以通过 lua-resty-core 中提供的 process.type API ，获取到进程的类型。比如，你可以用 resty 运行下面这个函数：


```
$ resty -e 'local process = require "ngx.process"
ngx.say("process type:", process.type())'
```
你会看到，它返回的结果不是 worker， 而是 single。这意味 resty 启动的 Nginx 只有 worker 进程，没有 master 进程。其实，事实也是如此。在 resty 的实现中，你可以看到，下面这样的一行配置， 关闭了 master 进程：

```
master_process off;
```
而 OpenResty 在 Nginx 的基础上进行了扩展，增加了特权进程：privileged agent。特权进程很特别：

它不监听任何端口，这就意味着不会对外提供任何服务；它拥有和 master 进程一样的权限，一般来说是 root 用户的权限，这就让它可以做很多 worker 进程不可能完成的任务；特权进程只能在 init_by_lua 上下文中开启；另外，特权进程只有运行在 init_worker_by_lua 上下文中才有意义，因为没有请求触发，也就不会走到content、access 等上下文去。

下面，我们来看一个开启特权进程的示例：

```
init_by_lua_block {
    local process = require "ngx.process"

    local ok, err = process.enable_privileged_agent()
    if not ok then
        ngx.log(ngx.ERR, "enables privileged agent failed error:", err)
    end
}
```
通过这段代码开启特权进程后，再去启动 OpenResty 服务，我们就可以看到，Nginx 的进程中多了特权进程的身影：

```
nginx: master process
nginx: worker process
nginx: privileged agent process
```
不过，如果特权只在 init_worker_by_lua 阶段运行一次，显然不是一个好主意，那我们应该怎么来触发特权进程呢？没错，答案就藏在刚刚讲过的知识里。既然它不监听端口，也就是不能被终端请求触发，那就只有使用我们刚才介绍的 ngx.timer ，来周期性地触发了：

```
init_worker_by_lua_block {
    local process = require "ngx.process"

    local function reload(premature)
        local f, err = io.open(ngx.config.prefix() .. "/logs/nginx.pid", "r")
        if not f then
            return
        end
        local pid = f:read()
        f:close()
        os.execute("kill -HUP " .. pid)
    end

    if process.type() == "privileged agent" then
         local ok, err = ngx.timer.every(5, reload)
        if not ok then
            ngx.log(ngx.ERR, err)
        end
    end
}
```
上面这段代码，实现了每 5 秒给 master 进程发送 HUP 信号量的功能。自然，你也可以在此基础上实现更多有趣的功能，比如轮询数据库，看是否有特权进程的任务并执行。因为特权进程是 root 权限，这显然就有点儿“后门”程序的意味了。


### 非阻塞的 ngx.pipe
最后我们来看非阻塞的 ngx.pipe。刚刚讲过的这个代码示例中，我们使用了 Lua 的标准库，来执行外部命令行，把信号发送给了 master 进程：

```
os.execute("kill -HUP " .. pid) 
```
这种操作自然是会阻塞的。那么，在 OpenResty 中，是否有非阻塞的方法来调用外部程序呢？毕竟，要知道，如果你是把 OpenResty 当做一个完整的开发平台，而非 Web 服务器来使用的话，这就是你的刚需了。为此，lua-resty-shell 库应运而生，使用它来调用命令行就是非阻塞的：

```
$ resty -e 'local shell = require "resty.shell"
local ok, stdout, stderr, reason, status =
    shell.run([[echo "hello, world"]])
    ngx.say(stdout)
```
这段代码可以算是 hello world 的另外一种写法了，它调用系统的 echo 命令来完成输出。类似的，你可以用 resty.shell ，来替代 Lua 中的 os.execute 调用。我们知道，lua-resty-shell 的底层实现，依赖了 lua-resty-core 中的 [ngx.pipe] API，所以，这个使用 lua-resty-shell 打印出 hello wrold 的示例，改用 ngx.pipe ，可以写成下面这样：

```
$ resty -e 'local ngx_pipe = require "ngx.pipe"
local proc = ngx_pipe.spawn({"echo", "hello world"})
local data, err = proc:stdout_read_line()
ngx.say(data)'
```
这其实也就是 lua-resty-shell 底层的实现代码了。你可以去查看 ngx.pipe 的文档和测试案例，来获取更多的使用方法，这里我就不再赘述了。

### 写在最后
到此，今天的主要内容我就讲完了。从上面的几个功能，我们可以看出，OpenResty 在做一个更好用的 Nginx 的前提下，也在尝试往通用平台的方向上靠拢，希望开发者能够尽量统一技术栈，都用 OpenResty 来解决开发需求。这对于运维来说是相当友好的，因为只要部署一个 OpenResty 就可以了，维护成本更低。


## 21 | 带你玩转时间、正则表达式等常用API
在前面几节课中，你已经熟悉了不少 OpenResty 中重要的 Lua API 了，今天我们再来了解下其他一些通用的 API，主要和正则表达式、时间、进程等相关。


### 正则
先来看下最常用，也是最重要的正则。在 OpenResty 中，我们应该一直使用 ngx.re.* 提供的一系列 API，来处理和正则表达式相关的逻辑，而不是用 Lua 自带的模式匹配。这不仅是出于性能方面的考虑，还因为 Lua 自带的正则是自成体系的，并非 PCRE 规范，这对于绝大部分开发者来说都是徒增烦恼。在前面的课程中，你已经多多少少接触过一些 ngx.re.* 的 API 了，文档也写得非常详细，我就不再一一列举了。这里，我再单独强调两个内容。

#### ngx.re.split
第一个是ngx.re.split。字符串切割是很常见的功能，OpenResty 也提供了对应的 API，但在社区的 QQ 交流群中，很多开发者都找不到这样的函数，只能选择自己手写。为什么呢？其实， ngx.re.split 这个 API 并不在 lua-nginx-module 中，而是在 lua-resty-core 里面；并且它也不在 lua-resty-core 首页的文档中，而是在 lua-resty-core/lib/ngx/re.md 这个第三级目录的文档中出现的。多种原因，导致很多开发者完全不知道这个 API 的存在。类似这种“藏在深闺无人识“的 API，还有我们前面提到过的 ngx_resp.add_header、enable_privileged_agent 等等。那么怎么来最快地解决这种问题呢？除了阅读 lua-resty-core 首页文档外，你还需要把 lua-resty-core/lib/ngx/ 这个目录下的 .md 格式的文档也通读一遍才行。我们前面夸了很多 OpenResty 文档做得好的地方，不过，这一点上，也就是在一个页面能够查询到完整的 API 列表，确实还有很大的改进空间。



#### lua_regex_match_limit
第二个，我想介绍一下lua_regex_match_limit。我们之前并没有花专门的篇幅，来讲 OpenResty 提供的 Nginx 指令，因为大部分情况下我们使用默认值就足够了，它们也没有在运行时去修改的必要性。不过，我们今天要讲的这个，和正则表达式相关的lua_regex_match_limit 指令，却是一个例外。我们知道，如果我使用的正则引擎是基于回溯的 NFA 来实现的，那么就有可能出现灾难性回溯（Catastrophic Backtracking），即正则在匹配的时候回溯过多，造成 CPU 100%，正常服务被阻塞。一旦发生灾难性回溯，我们就需要用 gdb 分析 dump，或者 systemtap 分析线上环境才能定位，而且事先也不容易发现，因为只有特别的请求才会触发。这显然就给攻击者带来了可趁之机，ReDoS（RegEx Denial of Service）就是指的这类攻击。如果你对如何自动化发现和彻底解决这个问题感兴趣，可以参考我之前在公众号写的一篇文章：[如何彻底避免正则表达式的灾难性回溯](https://mp.weixin.qq.com/s/K9d60kjDdFn6ZwIdsLjqOw)？今天在这里，我主要给你介绍下，如何在 OpenResty 中简单有效地规避，也就是使用下面这行代码：

```
lua_regex_match_limit 100000;
```
lua_regex_match_limit ，就是用来限制 PCRE 正则引擎的回溯次数的。这样，即使出现了灾难性回溯，后果也会被限制在一个范围内，不会导致你的 CPU 满载。这里我简单说一下，这个指令的默认值是 0，也就是不做限制。如果你没有替换 OpenResty 自带的正则引擎，并且还涉及到了比较多的复杂的正则表达式，你可以考虑重新设置这个 Nginx 指令的值。

### 时间 API
接下来我们说说时间 API。OpenResty 提供了 10 个左右和时间相关的 API，从这个数量你也可见它的重要性。一般来说，最常用的时间 API 就是 ngx.now，它可以打印出当前的时间戳，比如下面这行代码：

```
resty -e 'ngx.say(ngx.now())'
```
从打印的结果可以看出，ngx.now 包括了小数部分，所以更加精准。而与之相关的 ngx.time 则只返回了整数部分的值。至于其他的 ngx.localtime、ngx.utctime、ngx.cookie_time 和 ngx.http_time ，主要是返回和处理时间的不同格式。具体用到的话，你可以查阅文档，本身并不难理解，我就没有必要专门来讲了。不过，值得一提的是，**这些返回当前时间的 API，如果没有非阻塞网络 IO 操作来触发，便会一直返回缓存的值，而不是像我们想的那样，能够返回当前的实时时间**。可以看看下面这个示例代码：

```
$ resty -e 'ngx.say(ngx.now())
os.execute("sleep 1")
ngx.say(ngx.now())'
```
在两次调用 ngx.now 之间，我们使用 Lua 的阻塞函数 sleep 了 1 秒钟，但从打印的结果来看，这两次返回的时间戳却是一模一样的。那么，如果换成是非阻塞的 sleep 函数呢？比如下面这段新的代码：


```
$ resty -e 'ngx.say(ngx.now())
ngx.sleep(1)
ngx.say(ngx.now())'
```
显然，它就会打印出不同的时间戳了。这里顺带引出了 ngx.sleep ，这个非阻塞的 sleep 函数。这个函数除了可以休眠指定的时间外，还有另外一个特别的用处。举个例子，比如你有一段正在做密集运算的代码，需要花费比较多的时间，那么在这段时间内，这段代码对应的请求就会一直占用着 worker 和 CPU 资源，导致其他请求需要排队，无法得到及时的响应。这时，我们就可以在其中穿插 ngx.sleep(0)，使这段代码让出控制权，让其他请求也可以得到处理。

### worker 和进程 API
再来看 worker 和进程相关的 API。OpenResty 提供了 ngx.worker.* 和 ngx.process.* 这些 API， 来获取 worker 和进程相关的信息。其中，前者和 Nginx worker 进程有关，后者则是泛指所有的 Nginx 进程，不仅有 worker 进程，还有 master 进程和特权进程等等。事实上，ngx.worker.* 由 lua-nginx-module 提供，而ngx.process.* 则是由 lua-resty-core 提供。还记得上节课我们留的作业题吗，如何保证在多 worker 的情况下，只启动一个 timer？其实，这就需要用到 ngx.worker.id 这个 API 了。你可以在启动 timer 之前，先做一个简单的判断：

```
if ngx.worker.id == 0 then
    start_timer()
end
```
这样，我们就能实现只启动一个 timer 的目的了。这里注意，worker id 是从 0 开始返回的，这和 Lua 中数组下标从 1 开始并不相同，千万不要混淆了。至于其他 worker 和 process 相关的 API，并没有什么特别需要注意的地方，就交给你自己去学习和练习了。

### 真值和空值
最后我们来看看，真值和空值的问题。在 OpenResty 中，真值与空值的判断，一直是个让人头痛、也比较混乱的点。我们先看来下 Lua 中真值的定义：除了 nil 和 false 之外，都是真值。所以，真值也就包括了：0、空字符串、空表等等。再来看下 Lua 中的空值（nil），它是未定义的意思，比如你申明了一个变量，但还没有初始化，它的值就是 nil：
```
$ resty -e 'local a
ngx.say(type(a))'
```
而 nil 也是 Lua 中的一种数据类型。明白了这两点后，我们现在就来具体看看，基于这两个定义，衍生出来的其他坑。

#### ngx.null
第一个坑是ngx.null。因为 Lua 的 nil 无法作为 table 的 value，所以 OpenResty 引入了 ngx.null，作为 table 中的空值：


```
$ resty -e  'print(ngx.null)'
null
```
```
$ resty -e 'print(type(ngx.null))'
userdata
```
从上面两段代码你可以看出，ngx.null 被打印出来是 null，而它的类型是 userdata。那么，可以把它当作假值吗？当然不行，事实上，ngx.null 的布尔值为真：
```
$ resty -e 'if ngx.null then
ngx.say("true")
end'
```
所以，要谨记，只有 nil 和 false 是假值。如果你遗漏了这一点，就很容易踩坑，比如你在使用 lua-resty-redis 的时候，做了下面这个判断：

```
local res, err = red:get("dog")
if not res then
    res = res + "test"
end 
```
如果返回值 res 是 nil，就说明函数调用失败了；如果 res 是 ngx.null，就说明 redis 中不存在 dog 这个 key。那么，在 dog 这个 key 不存在的情况下，这段代码就 500 崩溃了。

#### cdata:NULL
第二个坑是cdata:NULL。当你通过 LuaJIT FFI 接口去调用 C 函数，而这个函数返回一个 NULL 指针，那么你就会遇到另外一种空值，即cdata:NULL 。

```
$ resty -e 'local ffi = require "ffi"
local cdata_null = ffi.new("void*", nil)
if cdata_null then
    ngx.say("true")
end'
```
和 ngx.null 一样，cdata:NULL 也是真值。但更让人匪夷所思的是，下面这段代码，会打印出 true，也就是说cdata:NULL 是和 nil 相等的：

```
$ resty -e 'local ffi = require "ffi"
local cdata_null = ffi.new("void*", nil)
ngx.say(cdata_null == nil)'
```
那么我们应该如何处理 ngx.null 和 cdata:NULL 呢？显然，让应用层来关心这些闹心事儿是不现实的，最好是做一个二层封装，不要让调用者知道这些细节即可。


#### cjson.null
最后，我们再来看下 cjson 中出现的空值。cjson 库会把 json 中的 NULL，解码为 Lua 的 lightuserdata，并用 cjson.null 来表示：

```
$ resty -e 'local cjson = require "cjson"
local data = cjson.encode(nil)
local decode_null = cjson.decode(data)
ngx.say(decode_null == cjson.null)'
```
Lua 中的 nil，被 json encode 和 decode 一圈儿之后，就变成了 cjson.null。你可以想得到，它引入的原因和 ngx.null 是一样的，因为 nil 无法在 table 中作为 value。到现在为止，看了这么多 OpenResty 中的空值，不知道你蒙圈儿了没？不要慌张，这部分内容多看几遍，自己梳理一下，就不至于晕头转向分不清了。当然，你以后在写类似 if not foo then 的时候，就要多想想，这个条件到底能不能成立了。


## 22 | [视频]从一个安全漏洞说起，探寻API性能和安全的平衡

今天的内容，我同样会以视频的形式来讲解。老规矩，在你进行视频学习之前，我想先问你这么几个问题：你在使用 OpenResty 的时候，是否注意到有 API 存在安全隐患呢？在安全和性能之间，如何去平衡它们的关系呢？这几个问题，也是今天视频课要解决的核心内容，希望你可以先自己思考一下，并带着问题来学习今天的视频内容。同时，我会给出相应的文字介绍，方便你在听完视频内容后，及时总结与复习。下面是今天这节课的文字介绍部分。


### 今日核心
安全，是一个永恒的话题，不管你是写开发业务代码，还是做底层的架构，都离不开安全方面的考虑。CVE-2018-9230 是与 OpenResty 相关的一个安全漏洞，但它并非 OpenResty 自身的安全漏洞。这听起来是不是有些拗口呢？没关系，接下来让我们具体看下，攻击者是如何构造请求的。OpenResty 中的 ngx.req.get_uri_args、ngx.req.get_post_args 和 ngx.req.get_headers接口，默认只返回前 100 个参数。如果 WAF 的开发者没有注意到这个细节，就会被参数溢出的方式攻击。攻击者可以填入 100 个无用参数，把 payload 放在第 101 个参数中，借此绕过 WAF 的检测。那么，应该如何处理这个 CVE 呢？显然，OpenResty 的维护者需要考虑到向下兼容、不引入更多安全风险和不影响性能这么几个因素，并要在其中做出一个平衡的选择。最终，OpenResty 维护者选择新增一个 err 的返回值来解决这个问题。如果输入参数超过 100 个，err 的提示信息就是 truncated。这样一来，这些 API 的调用者就必须要处理错误信息，自行判断拒绝请求还是放行。其实，归根到底，安全是一种平衡。究竟是选择基于规则的黑名单方式，还是选择基于身份的白名单方式，抑或是两种方式兼用，都取决于你的实际业务场景。


### 课件参考
今天的课件已经上传到了我的 GitHub 上，你可以自己下载学习。链接如下：https://github.com/iresty/geektime-slides



## 23 | [视频]导读lua-resty-requests：优秀的lua-resty-*是如何编写的？
今天的内容，我同样会以视频的形式来讲解。老规矩，在你进行视频学习之前，先问你这么几个问题：面对多个相同功能的 lua-resty 库，我们应该从哪些方面来选择？如何来组织一个 lua-resty 的结构？这几个问题，也是今天视频课要解决的核心内容，希望你可以先自己思考一下，并带着问题来学习今天的视频内容。同时，我会给出相应的文字介绍，方便你在听完视频内容后，及时总结与复习。下面是今天这节课的文字介绍部分。



### 今日核心
前面我们介绍过的 lua-resty 库都是官方自带的，但在 HTTP client 这个最常用的库上，官方并没有。这时候，我们就得自己来选择一个优秀的第三方库了。那么，如何在众多的 lua-resty HTTP client 中，选择一个最好、最适合自己的第三方库呢？这时候，你就需要综合考虑活跃度、作者、测试覆盖度、接口封装等各方面的因素了。我最后选择的是 lua-resty-requests（https://github.com/tokers/lua-resty-requests），它是由又拍云的工程师 tokers 贡献的，我个人很喜欢它的接口风格，也推荐给你。在视频中我会从最简单的 get 接口入手，结合文档、测试案例和源码，来逐步展开。你可以看到一个优秀的 lua-resty 库是如何编写的，有哪些可以借鉴的地方。


### 课件参考
今天的课件已经上传到了我的 GitHub 上，你可以自己下载学习。链接如下：https://github.com/iresty/geektime-slides


## 24 | 实战：处理四层流量，实现Memcached Server

在前面几节课中，我们介绍了不少处理请求的 Lua API ，不过它们都是和七层相关的。除此之外，OpenResty 其实还提供了 stream-lua-nginx-module 模块来处理四层的流量。它提供的指令和 API ，与 lua-nginx-module 基本一致。今天，我就带你一起用 OpenResty 来实现一个 memcached server，而且大概只需要 100 多行代码就可以完成。在这个小的实战中，我们会用到不少前面学过的内容，也会带入一些后面测试和性能优化章节的内容。所以，我希望你能够明确一点，我们这节课的重点，不在于你必须读懂每一行代码的具体作用，而是你要从需求、测试、开发等角度，把 OpenResty 如何从零开发一个项目的全貌了然于心。

### 原始需求和技术方案
在开发之前，我们都需要明白需求是什么，到底是用来解决什么问题的，否则就会在迷失在技术选择中。比如看到我们今天的主题，你就应该先反问一下自己，为什么要实现一个 memcached server 呢？直接安装一个原版的 memcached 或者 redis 不就行了吗？我们知道，HTTPS 流量逐渐成为主流，但一些比较老的浏览器并不支持 session ticket，那么我们就需要在服务端把 session ID 存下来。如果本地存储空间不够，就需要一个集群进行存放，而这个数据又是可以丢弃的，所以选用 memcached 就比较合适。这时候，直接引入 memcached ，应该是最简单直接的方案。但出于以下几个方面的考虑，我还是选择使用 OpenResty 来造一个轮子：

第一，直接引入会多引入一个进程，增加部署和维护成本；第二，这个需求足够简单，只需要 get 和 set 操作，并且支持过期即可；第三，OpenResty 有 stream 模块，可以很快地实现这个需求。

既然要实现 memcached server，我们就需要先弄明白它的协议。memcached 的协议可以支持 TCP 和 UDP，这里我选择 TCP，下面是 get 和 set 命令的具体协议：

```
Get
根据 key 获取 value
Telnet command: get <key>*\r\n

示例：
get key
VALUE key 0 4 data END
```
```
Set
存储键值对到 memcached 中
Telnet command：set <key> <flags> <exptime> <bytes> [noreply]\r\n<value>\r\n

示例：
set key 0 900 4 data
STORED
```
除了 get 和 set 外，我们还需要知道 memcached 的协议的“错误处理”是怎么样做的。“错误处理”对于服务端的程序是非常重要的，我们在编写程序时，除了要处理正常的请求，也要考虑到各种异常。比如下面这样的场景：memcached 发送了一个 get、set 之外的请求，我要怎么处理呢？服务端出错，我要给 memcached 的客户端一个什么样的反馈呢？

同时，我们希望写出能够兼容 memcached 的客户端程序。这样，使用者就不用区分这是 memcached 官方的版本，还是 OpenResty 实现的版本了。下面这张图出自 memcached 的文档，描述了出错的时候，应该返回什么内容和具体的格式，你可以用做参考：

![img](https://static001.geekbang.org/resource/image/37/b0/3767ed0047e34aabaa7bf7d568438ab0.png?wh=1974*1446)


现在，再来确定下技术方案。我们知道，OpenResty 的 shared dict 可以跨各个 worker 来使用，把数据放在 shared dict 里面，和放在 memcached 里面非常类似——它们都支持 get 和 set 操作，并且在进程重启后数据就丢失了。所以，使用 shared dict 来模拟 memcached 是非常合适的，它们的原理和行为都是一致的。


### 测试驱动开发
接下来就要开始动工了。不过，基于测试驱动开发的思想，在写具体的代码之前，让我们先来构造一个最简单的测试案例。这里我们不用 test::nginx 框架，毕竟它的上手难度也不低，我们不妨先用熟悉的 resty 来手动测试下：

```
$ resty -e 'local memcached = require "resty.memcached"
    local memc, err = memcached:new()

    memc:set_timeout(1000) -- 1 sec
    local ok, err = memc:connect("127.0.0.1", 11212)
    local ok, err = memc:set("dog", 32)
    if not ok then
        ngx.say("failed to set dog: ", err)
        return
    end

    local res, flags, err = memc:get("dog")
    ngx.say("dog: ", res)'
```
这段测试代码，使用 lua-rety-memcached 客户端库发起 connect 和 set 操作，并假设 memcached 的服务端监听本机的 11212 端口。看起来应该没有问题了吧。你可以在自己的机器上执行一下这段代码，不出意外的话，会返回 failed to set dog: closed 这样的错误提示，因为此时服务并没有启动。到现在为止，你的技术方案就已经明确了，那就是使用 stream 模块来接收和发送数据，同时使用 shared dict 来存储数据。衡量需求是否完成的指标也很明确，那就是跑通上面这段代码，并把 dog 的实际值给打印出来。

### 搭建框架
那还等什么，开始动手写代码吧！我个人的习惯，是先搭建一个最小的可以运行的代码框架，然后再逐步地去填充代码。这样的好处是，在编码过程中，你可以给自己设置很多小目标；而且在完成一个小目标后，测试案例也会给你正反馈。让我们先来设置好 Nginx 的配置文件，因为 stream 和 shared dict 要在其中预设。下面是我设置的配置文件：

```
stream {
    lua_shared_dict memcached 100m;
    lua_package_path 'lib/?.lua;;';
    server {
        listen 11212;
        content_by_lua_block {
            local m = require("resty.memcached.server")
            m.run()
        }
    }
}
```
你可以看到，这段配置文件中有几个关键的信息：首先，代码运行在 Nginx 的 stream 上下文中，而非 HTTP 上下文中，并且监听了 11212 端口；其次，shared dict 的名字为 memcached，大小是 100M，这些在运行期是不可以修改的；另外，代码所在目录为 lib/resty/memcached, 文件名为 server.lua, 入口函数为 run()，这些信息你都可以从lua_package_path 和 content_by_lua_block 中找到。

接着，就该搭建代码框架了。你可以自己先动手试试，然后我们一起来看下我的框架代码：

```
local new_tab = require "table.new"
local str_sub = string.sub
local re_find = ngx.re.find
local mc_shdict = ngx.shared.memcached

local _M = { _VERSION = '0.01' }

local function parse_args(s, start)
end

function _M.get(tcpsock, keys)
end

function _M.set(tcpsock, res)
end

function _M.run()
    local tcpsock = assert(ngx.req.socket(true))

    while true do
        tcpsock:settimeout(60000) -- 60 seconds
        local data, err = tcpsock:receive("*l")

        local command, args
        if data then
            local from, to, err = re_find(data, [[(\S+)]], "jo")
            if from then
                command = str_sub(data, from, to)
                args = parse_args(data, to + 1)
            end
        end

        if args then
            local args_len = #args
            if command == 'get' and args_len > 0 then
                _M.get(tcpsock, args)
            elseif command == "set" and args_len == 4 then
                _M.set(tcpsock, args)
            end
        end
    end
end

return _M
```
这段代码，便实现了入口函数 run() 的主要逻辑。虽然我还没有做异常处理，依赖的 parse_args、get 和 set 也都是空函数，但这个框架已经完整表达了 memcached server 的逻辑。

### 填充代码
接下来，让我们按照代码的执行顺序，逐个实现这几个空函数。首先，我们可以根据 memcached 的[协议](https://github.com/memcached/memcached/blob/master/doc/protocol.txt)[文档](https://github.com/memcached/memcached/blob/master/doc/protocol.txt)，解析 memcached 命令的参数：

```
local function parse_args(s, start)
    local arr = {}

    while true do
        local from, to = re_find(s, [[\S+]], "jo", {pos = start})
        if not from then
            break
        end

        table.insert(arr, str_sub(s, from, to))

        start = to + 1
    end

    return arr
end
```
这里，我的建议是，先用最直观的方式来实现一个版本，不用考虑任何性能的优化。毕竟，完成总是比完美更重要，而且，基于完成的逐步优化才可以趋近完美。接下来，我们就来实现下 get 函数。它可以一次查询多个键，所以下面代码中我用了一个 for 循环：

```
function _M.get(tcpsock, keys)
    local reply = ""

    for i = 1, #keys do
        local key = keys[i]
        local value, flags = mc_shdict:get(key)
        if value then
            local flags  = flags or 0
            reply = reply .. "VALUE" .. key .. " " .. flags .. " " .. #value .. "\r\n" .. value .. "\r\n"
        end
    end
    reply = reply ..  "END\r\n"

    tcpsock:settimeout(1000)  -- one second timeout
    local bytes, err = tcpsock:send(reply)
end

```
其实，这里最核心的代码只有一行：local value, flags = mc_shdict:get(key)，也就是从 shared dict 中查询到数据；至于其余的代码，都在按照 memcached 的协议拼接字符串，并最终 send 到客户端。最后，我们再来看下 set 函数。它将接收到的参数转换为 shared dict API 的格式，把数据储存了起来；并在出错的时候，按照 memcached 的协议做出处理：


```
function _M.set(tcpsock, res)
    local reply =  ""

    local key = res[1]
    local flags = res[2]
    local exptime = res[3]
    local bytes = res[4]

    local value, err = tcpsock:receive(tonumber(bytes) + 2)

    if str_sub(value, -2, -1) == "\r\n" then
        local succ, err, forcible = mc_shdict:set(key, str_sub(value, 1, bytes), exptime, flags)
        if succ then
            reply = reply .. “STORED\r\n"
        else
            reply = reply .. "SERVER_ERROR " .. err .. “\r\n”
        end
    else
        reply = reply .. "ERROR\r\n"
    end

    tcpsock:settimeout(1000)  -- one second timeout
    local bytes, err = tcpsock:send(reply)
end
```
另外，在填充上面这几个函数的过程中，你可以用测试案例来做检验，并用 ngx.log 来做 debug。比较遗憾的是，OpenResty 中并没有断点调试的工具，所以我们都是使用 ngx.say 和 ngx.log 来调试的，在这方面可以说是还处于刀耕火种的时代。

### 写在最后
这个实战项目到现在就接近尾声了，最后，我想留一个动手作业。你可以把上面 memcached server 的实现代码，完整地运行起来，并通过测试案例吗？


## 25 | 答疑（二）：特权进程的权限到底是什么？

专栏更新到现在，OpenResty 第二版块 OpenResty API 篇，我们就已经学完了。恭喜你没有掉队，仍然在积极学习和实践操作，并且热情地留下了你的思考。很多留言提出的问题很有价值，大部分我都已经在 App 里回复过，一些手机上不方便回复的或者比较典型、有趣的问题，我专门摘了出来，作为今天的答疑内容，集中回复。另一方面，也是为了保证所有人都不漏掉任何一个重点。下面我们来看今天的这 6 个问题。

第一问，特权进程的权限Q：我想请问下，特权进程是怎么回事，如果启动 OpenResty 的本身就是普通用户，如何获取 root 权限呢？另外，老师可以介绍下，特权进程的使用场景有哪些吗？

A：其实，特权进程的权限和 master 进程的权限保持一样。如果你用普通用户身份启动 OpenResty，那么 master 就是普通用户的权限，这时候特权进程也就没有什么特权了。这一点应该还是很好理解的，普通用户启动的进程，无论如何也不会有 root 权限。至于特权进程的使用场景，我们一般用特权进程来处理的是清理日志、重启 OpenResty 自身等需要高权限的任务。你需要注意的是，不要把 worker 进程的任务交给特权进程来处理。这并非因为特权进程不能做到，而是其存在安全隐患。我见到过一个开发者，他把定时器的任务都交给了特权进程来处理。他为什么这么做呢？因为特权进程只有一个，这样 timer 就不会重复启动。是不是觉得这看上去很聪明呀，不用 worker.id 这种笨方法就做到了。但是，别忘了，如果定时器的任务和用户的输入有关，这不就等于留了一个后门吗？显然是非常危险的。

第二问，阶段和调试Q：老师，是不是无论在哪个阶段运行ngx.say('hello')，OpenResty 都会在执行完本阶段的剩余代码后，直接响应给客户端，而不会继续执行其他阶段了呢？我测试出来是这样的。

A：事实上并非如此，我们可以来看下它的执行阶段的[顺序图](https://github.com/moonbingbing/openresty-best-practices/blob/master/images/openresty_phases.png)：

![img](https://static001.geekbang.org/resource/image/71/bf/71b24c95f042f0bf79ac34211e2dd0bf.png?wh=1005*910)

你可以做个测试，先在 content 里面 ngx.say；然后，在 log 或者 body filter 阶段使用 ngx.log 来打印下日志试试。在专栏中，我并没有专门提到在 OpenResty 中做代码调试的问题，这也是开发者经常困惑的地方，我正好顺着这个问题在答疑中聊一下。其实，OpenResty 中的代码调试，并没有断点这些高级功能（相应有一些付费的插件，但我并没有使用过），只能用  ngx.say 和ngx.log 来看输出。我知道的开发者，包括 OpenResty 的作者和贡献者们，都是这样来做 debug 的。所以，你需要有强有力的测试案例和调试日志来作为保证。


第三问，ngx.exit 和动手实验Q：老师，文中的这句话，“OpenResty 的 HTTP 状态码中，有一个特别的常量：ngx.OK。当 ngx.exit(ngx.OK) 时，请求会退出当前处理阶段，进入下一个阶段，而不是直接返回给客户端。”

我记得，ngx.OK应该不能算是 HTTP 状态码，它对应的值是 0。我的理解是：ngx.exit(ngx.OK)、ngx.exit(ngx.ERROR)和ngx.exit(ngx.DECLINED)时，请求会退出当前处理阶段，进入下一个阶段；而当ngx.exit(ngx.HTTP_*)以ngx.HTTP_*的各种 HTTP 状态码作为参数时，会直接响应给客户端。不知道这样想对不对呢？

A：关于你的第一个问题，ngx.ok 确实不是 http 状态码，它是 OpenResty 中的一个常量，值是 0。至于第二个问题，ngx.exit 的官方文档其实正好可以解答：

```
When status >= 200 (i.e., ngx.HTTP_OK and above), it will interrupt the execution of the current request and return status code to nginx.

When status == 0 (i.e., ngx.OK), it will only quit the current phase handler (or the content handler if the content_by_lua* directive is used) and continue to run later phases (if any) for the current request.
```
不过，文档里并没有提到， OpenResty 对于ngx.exit(ngx.ERROR)和ngx.exit(ngx.DECLINED)是如何处理的，我们可以自己来做个测试，比如下面这样：


```
location /lua {
        rewrite_by_lua "ngx.exit(ngx.ERROR)";
        echo hello;
    }
```
显然，访问这个 location，你可以看到 http 响应码为空，响应体也是空，并没有进入下一个执行阶段。其实，还是那句话，在 OpenResty 的学习过程中，随着你逐步深入，一定会在某个阶段发现，文档和测试案例都无法回答你的问题。这时候，就需要你自己构建测试案例来验证你的想法了。你可以手动测试，也可以添加在 test::nginx 搭建的测试案例集里面。


第四问，变量和竞争Q：老师，你好，我有下面几个问题想请教一下。前面讲过，ngx.var变量的作用域在 nginx C 和 lua-nginx-module 模块之间。这个我不太理解，从请求的角度来看，是指一个工作进程中的单个请求吗？我的理解是，在我们操作模块内的变量时，如果两个操作之间有阻塞操作，可能会出现竞争。那么，如果两个操作之间没有阻塞操作，恰好 CPU 时间到了后，当前进程进入就绪队列，这样可能产生竞争吗？


A：我们依次来看这几个问题。第一，关于ngx.var 变量的问题，你的理解是正确的。实际上，ngx.var 的生命周期和请求一致，请求结束它也就消失了。但它的优势，是数据可以在 C 模块和 Lua 代码中传递。这是其他几种方式都无法做到的。第二，关于变量竞争的问题，其实，只要两个操作之间有 yield 操作，就可能出现竞争，而不是阻塞操作；有阻塞操作时是不会出现竞争的。换句话说，只要你不把主动权交给 Nginx 的事件循环，就不会有竞争。


第五问，共享字典操作是否需要加锁呢？Q：老师，如果多个 worker 并发存储数据，是不是需要加锁呢？比如下面这个例子：

```
resty --shdict 'dogs 10m' -e 'local dogs = ngx.shared.dogs
local lock= ngx.xxxx.lock
lock.lock()
 dogs:set("Jim", 8)
lock.unlock()
 local v = dogs:get("Jim")
 ngx.say(v)
 '
```
A：其实这里不用你自己加锁，共享字典（shared dict）的操作都是原子性的，不管是 get 还是 set。这种类似加锁的处理，OpenResty 已经帮你考虑到了。


第六问，OpenResty 中如何更新时间？Q：ngx.now()取时间，是发生在 resume 函数恢复堆栈阶段吗？

A：Nginx 是以性能优先作为设计理念的，它会把时间缓存下来。这一点，我们从 ngx.now 的源码中就可以得到印证：

```
static int
ngx_http_lua_ngx_now(lua_State *L)
{
    ngx_time_t              *tp;

    tp = ngx_timeofday();

    lua_pushnumber(L, (lua_Number) (tp->sec + tp->msec / 1000.0L));

    return 1;
}
```
可以看出，ngx.now()这个获取当前时间函数的背后，隐藏的其实是 Nginx 的 ngx_timeofday 函数。而ngx_timeofday 函数，其实是一个宏定义：
```
#define ngx_timeofday()      (ngx_time_t *) ngx_cached_time
```
这里ngx_cached_time 的值，只在函数 ngx_time_update 中会更新。所以，这个问题就简化成了，  ngx_time_update什么时候会被调用？如果你在 Nginx 的源码中去跟踪它的话，就会发现，  ngx_time_update 的调用都出现在事件循环中，这个问题也就明白了吧。


## 测试篇
## 26 | 代码贡献者的拦路虎：test::nginx 简介

测试，是软件开发中必不可少的一个重要环节。测试驱动开发（TDD）的理念已经深入人心，几乎每家软件公司都有 QA 团队来负责测试的工作。测试也是 OpenResty 质量稳定和好口碑的基石，不过同时，它也是 OpenResty 众多开源项目中最被人忽视的部分。很多开发者每天都在使用 lua-nginx-module，偶尔跑一跑火焰图，但有几个人会去运行测试案例呢？甚至很多基于 OpenResty 的开源项目，都是没有测试案例的。但没有测试案例和持续集成的开源项目，显然是不值得信赖的。不过，和商业公司不同的是，大部分的开源项目都没有专职的测试工程师，那么它们是如何来保证代码质量的呢？答案很简单，就是“自动化测试”和“持续集成”，关键点在于自动和持续，而 OpenResty 在这两个方面都做到了极致。OpenResty 有 70 个开源项目，它们的单元测试、集成测试、性能测试、mock 测试、fuzz 测试等工作量，是无法靠社区的人力解决的。所以，OpenResty 一开始在自动化测试上的投入就比较大。这样做短期看起来会拖慢项目进度，但可以说是一劳永逸，长期来看在这方面的投入是非常划算的。因此，每当我和其他工程师聊起 OpenResty 在测试方面的思路和工具集时，他们都会惊叹不已。下面，我们就先来说说 OpenResty 的测试理念。


### 理念
test::nginx 是 OpenResty 测试体系中的核心，OpenResty 本身和周边的 lua-rety 库，都是使用它来组织和编写测试集的。虽然它一个是测试框架，但它的门槛非常高。这是因为， test::nginx 和一般的测试框架不同，并非基于断言，也不使用 Lua 语言，这就要求开发者从零开始学习和使用 test::nginx，并得扭转自身对测试框架固有的认知。我认识几个 OpenResty 的贡献者，他们可以流畅地给 OpenResty 提交 C 和 Lua 代码，但在使用 test::nginx 编写测试用例时都卡壳了，要么不知道怎么写，要么遇到测试跑不过时不知道如何解决。所以，我把 test::nginx 称为代码贡献者的拦路虎。test::nginx **糅合了 Perl、数据驱动以及 DSL（领域小语言）**。对于同一份测试案例集，通过对参数和环境变量的控制，可以实现乱序执行、多次重复、内存泄漏检测、压力测试等不同的效果。


### 安装和示例
说了这么多概念，让我们来对 test::nginx 有一个直观的认识吧。在使用前，我们先来看下如何安装。关于 OpenResty 体系内软件的安装，只有官方 CI 中的安装方法才是最及时和有效的，其他方式的安装总是会遇到各种各样的问题。所以，我总是推荐你去参考它在 travis 中的[方法](https://github.com/openresty/lua-resty-core/blob/master/.travis.yml)。test::nginx 的安装和使用也不例外，在 travis 中，它可以分为 4 步。

1. 先安装 Perl 的包管理器 cpanminus。2. 然后，通过 cpanm 来安装 test::nginx：


```
sudo cpanm --notest Test::Nginx IPC::Run > build.log 2>&1 || (cat build.log && exit 1)
```
3. 再接着， clone 最新的源码：

```
git clone https://github.com/openresty/test-nginx.git
```
4. 最后，通过 Perl 的 prove 命令来加载 test-nginx 的库，并运行 /t 目录下的测试案例集：


```
prove -Itest-nginx/lib -r t
```
安装完以后，让我们看下 test::nginx 中最简单的测试案例。下面这段代码改编自[官方文档](https://metacpan.org/pod/Test::Nginx::Socket)，我已经把个性化的控制参数都去掉了：

```
use Test::Nginx::Socket 'no_plan';


run_tests();

__DATA__

=== TEST 1: set Server
--- config
    location /foo {
        echo hi;
        more_set_headers 'Server: Foo';
    }
--- request
    GET /foo
--- response_headers
Server: Foo
--- response_body
hi
```
虽然 test::nginx 是用 Perl 编写的，并且是其中的一个模块，但从上面的测试中，你是不是完全看不到，Perl 或者其他任何其他语言的影子呀？有这个感觉这就对了。因为，test::nginx 本身就是作者自己用 Perl 实现的 DSL（小语言），是专门针对 Nginx 和 OpenResty 的测试而抽象出来的。所以，当你第一次看到这种测试的时候，大概率是看不懂的。不过不用着急，让我来为“你庖丁解牛”，分析以下上面的测试案例吧。首先是 use Test::Nginx::Socket;，这是 Perl 里面引用库的方式，就像 Lua 里面 require 一样。这也在提醒我们，test::nginx  是一个 Perl 程序。

第二行的run_tests(); ，是 test::nginx  中的一个 Perl 函数，它是测试框架的入口函数。如果你还想调用 test::nginx  中其他的 Perl 函数，都要放在 run_tests 之前才有效。第三行的 __DATA__ 是一个标记，表示它下面的都是测试数据。Perl 函数都应该在这个标记之前完成。


接下来的 === TEST 1: set Server，是测试案例的标题，是为了注明这个测试的目的，它里面的数字编号有工具可以自动排列。--- config 是 Nginx 配置段。在上面的案例中，我们用的都是 Nginx 的指令，没有涉及到 Lua。如果你要添加 Lua 代码，也是在这里用类似 content_by_lua 的指令完成的。--- request 用于模拟终端来发送一个请求，下面紧跟的 GET /foo ，则指明了请求的方法和 URI。--- response_headers，是用来检测响应头的。下面的 Server: Foo 表示在响应头中必须出现的 header 和 value，如果没有出现，测试就会失败。最后的--- response_body，是用来检测相应体的。下面的 hi 则是响应体中必须出现的字符串，如果没有出现，测试就会失败；

最后的--- response_body，是用来检测相应体的。下面的 hi 则是响应体中必须出现的字符串，如果没有出现，测试就会失败；好了，到这里，最简单的测试案例就分析完了，你看明白了吗？如果哪里还不清楚，一定要及时留言提问暴露出来，毕竟，能够看懂测试案例，是完成 OpenResty 相关开发工作的前提。

### 编写自己的测试案例
光说不练假把式，接下来，我们就该进入动手试验环节了。还记得上节课中，我们是如何测试 memcached server 的吗？没错，我们是用 resty 来手动发送请求的，也就是用下面这段代码表示：

```
$ resty -e 'local memcached = require "resty.memcached"
    local memc, err = memcached:new()

    memc:set_timeout(1000) -- 1 sec
    local ok, err = memc:connect("127.0.0.1", 11212)
    local ok, err = memc:set("dog", 32)
    if not ok then
        ngx.say("failed to set dog: ", err)
        return
    end

    local res, flags, err = memc:get("dog")
    ngx.say("dog: ", res)'
```
不过，是不是觉得手动发送还不够智能呢？没关系，在学习完 test::nginx  之后，我们就可以尝试把手动的测试变为自动化的了，比如下面这段代码：

```
use Test::Nginx::Socket::Lua::Stream;

run_tests();

__DATA__
  
=== TEST 1: basic get and set
--- config
        location /test {
            content_by_lua_block {
                local memcached = require "resty.memcached"
                local memc, err = memcached:new()
                if not memc then
                    ngx.say("failed to instantiate memc: ", err)
                    return
                end

                memc:set_timeout(1000) -- 1 sec
                local ok, err = memc:connect("127.0.0.1", 11212)

                local ok, err = memc:set("dog", 32)
                if not ok then
                    ngx.say("failed to set dog: ", err)
                    return
                end

                local res, flags, err = memc:get("dog")
                ngx.say("dog: ", res)
            }
        }

--- stream_config
    lua_shared_dict memcached 100m;

--- stream_server_config
    listen 11212;
    content_by_lua_block {
        local m = require("memcached-server")
        m.go()
    }

--- request
GET /test
--- response_body
dog: 32
--- no_error_log
[error]
```
在这个测试案例中，我新增了 --- stream_config、--- stream_server_config、--- no_error_log 这些配置项，但它们的本质上都是一样的，即：


**通过抽象好的原语（也可以看做配置），把测试的数据和检测进行剥离，让可读性和扩展性变得更好。**

这就是 test::nginx 和其他测试框架的根本不同之处。这种 DSL 是一把双刃剑，它可以让测试逻辑变得清晰和方便扩展，但同时也提高了学习的门槛，你需要重新学习新的语法和配置才能开始编写测试案例。

### 写在最后
不得不说，test::nginx  虽然强大，但很多时候，它可能不一定适合你的场景。杀鸡焉用宰牛刀？在 OpenResty 中，你也选择使用断言风格的测试框架 busted。busted结合 resty 这个命令行工具，也可以满足不少测试的需求。


## 27 | test::nginx 包罗万象的测试方法

通过上节课的学习，你已经对 test::nginx 有了一个初步的认识，并运行了最简单的示例。不过，在实际的开源项目中，test::nginx 编写的测试案例显然要比示例代码复杂得多，也更加难以掌握，不然它也就称不上是拦路虎了。在本节课中，我会带你来熟悉下 test::nginx 中经常用到的指令和测试方法，目的是让你可以看明白 OpenResty 项目中大部分的测试案例集，并有能力来编写更真实的测试案例。即使你还没有给 OpenResty 贡献过代码，但熟悉了 OpenResty 的测试框架，对于你平时工作中设计和编写测试案例，还是会有不少启发的。test::nginx 的测试，本质上是根据每一个测试案例的配置，先去生成 nginx.conf，并启动一个 Nginx 进程；然后，模拟客户端发起请求，其中包含指定的请求体和请求头；紧接着，测试案例中的 Lua 代码会处理请求并作出响应，这时，test::nginx 解析响应体、响应头、错误日志等关键信息，并和测试配置做对比。如果发现不符，就报错退出，测试失败；否则就算成功。test::nginx 中提供了很多 DSL 的原语，我按照 Nginx 配置、发送请求、处理响应、检查日志这个流程，做了一个简单的分类。这  20% 的功能可以覆盖 80% 的应用场景，所以你一定要牢牢掌握。至于其他更高级的原语和使用方法，我们留到下一节再来介绍。

### Nginx 配置
我们首先来看下 Nginx 配置。test::nginx 的原语中带有 config 这个关键字的，就和 Nginx 配置相关，比如上一节中提到的 config、stream_config、http_config 等。它们的作用都是一样的，即在 Nginx 的不同上下文中，插入指定的 Nginx 配置。这些配置可以是 Nginx 指令，也可以是 content_by_lua_block 封装起来的 Lua 代码。在做单元测试的时候，config 是最常用的原语，我们会在其中加载 Lua 库，并调用函数来做白盒测试。下面是节选的一段测试代码，并不能完整运行。它来自一个真实的开源项目，如果你对此有兴趣，可以点击[链接](https://github.com/apache/apisix/blob/master/t/plugin/key-auth.t#L11)查看完整的测试，也可以尝试在本机运行。

```
=== TEST 1: sanity
--- config
    location /t {
        content_by_lua_block {
            local plugin = require("apisix.plugins.key-auth")
            local ok, err = plugin.check_schema({key = 'test-key'})
            if not ok then
                ngx.say(err)
            end
            ngx.say("done")
        }
    }
```
这个测试案例的目的，是为了测试代码文件 plugins.key-auth 中， check_schema 这个函数能否正常工作。它在location /t 中使用 content_by_lua_block 这个 Nginx 指令，require 需要测试的模块，并直接调用需要检查的函数。这就是在 test::nginx 进行白盒测试的通用手段。不过，只有这段配置自然是无法完成测试的，下面我们继续看下，如何发起客户端的请求。


### 发送请求
模拟客户端发送请求，会涉及到不少的细节，所以，我们就先从最简单的发送单个请求入手吧。

#### request
还是继续上面的测试案例，如果你想要单元测试的代码被运行，那就要发起一个 HTTP 请求，访问的地址是 config 中注明的 /t，正如下面的测试代码所示：


```
--- request
GET /t
```
这段代码在 request 原语中，发起了一个 GET 请求，地址是 /t。这里，我们并没有注明访问的 ip 地址、域名和端口，也没有指定是 HTTP 1.0 还是 HTTP 1.1，这些细节都被 test::nginx 隐藏了，你不用去关心。这就是 DSL 的好处之一——你只需要关心业务逻辑，不用被各种细节所打扰。同时，这也提供了部分的灵活性。比如默认是 HTTP 1.1 的协议，如果你想测试 HTTP 1.0，也可以单独指定：

```
--- request
GET /t  HTTP/1.0
```
除了 GET 方法之外，POST 方法也是需要支持的。下面这个示例，可以 POST hello world 这个字符串到指定的地址：

```
--- request
POST /t  
hello world
```
同样的， test::nginx 在这里为你自动计算了请求体长度，并自动增加了 host 和 connection 这两个请求头，以保证这是一个正常的请求。当然，出于可读性的考虑，你可以在其中增加注释。以 # 开头的，就会被识别为代码注释：


```
--- request
   # post request
POST /t  
hello world
```
request 还支持更为复杂和灵活的模式，那就是配合 eval 这个 filter，直接嵌入 perl 代码，毕竟 test::nginx 就是 perl 编写的。这种做法，类似于在 DSL 之外开了一个后门，如果当前的 DSL 原语都不能满足你的需求，那么 eval 这种直接执行 perl 代码的方法，就可以说是“终极武器”了。关于 eval的用法，这里我们先看几个简单的例子，其他更复杂的，我们下节课继续介绍：

```
--- request eval
"POST /t
hello\x00\x01\x02
world\x03\x04\xff"
```
第一个例子中，我们用 eval 来指定不可打印的字符，这也是它的用处之一。双引号之间的内容，会被当做 perl 的字符串来处理后，再传给 request 来作为参数。下面是一个更有趣的例子：

```
--- request eval
"POST /t\n" . "a" x 1024
```
不过，要看懂这个例子，需要懂一些 perl 的字符串知识，这里我简单提两句：在 perl 中，我们用一个点号来表示字符串拼接，这是不是和 Lua 的两个点号有些类似呢？用小写的 x 来表示字符的重复次数。比如上面的 "a" x 1024，就表示字符 a 重复 1024 次。

所以，第二个例子的含义是，用 POST 方法，向 /t 地址，发送包含 1024 个字符 a 的请求。


#### pipelined_requests
了解完如何发送单个请求后，我们再来看下如何发送多个请求。在 test::nginx 中，你可以使用 pipelined_requests 这个原语，在同一个 keep-alive 的连接里面，依次发送多个请求：

```
--- pipelined_requests eval
["GET /hello", "GET /world", "GET /foo", "GET /bar"]
```
比如这个示例就会在同一个连接中，依次访问这 4 个接口。这样做会有两个好处：第一是可以省去不少重复的测试代码，把 4 个测试案例压缩到一个测试案例中完成；第二也是最重要的原因，你可以用流水线的请求，来检测代码逻辑在多次访问的情况下，是否会有异常。

你可能会奇怪，我依次写多个测试案例，那么执行的时候，代码也会被多次执行，不也可以覆盖上面的第二个问题吗？其实，这就涉及到 test::nginx 的执行模式了，它并非像你想象中的那样去运转。事实上，在执行完每一个测试案例后， test::nginx 都会关闭当前的 Nginx 进程，自然的，内存中所有数据也都随之消失了。当运行下一个测试案例时，又会重新生成 nginx.conf，并启动新的 Nginx worker。这种机制是为了保证测试案例之间不会互相影响。所以，当你要测试多个请求时，就需要用到 pipelined_requests 这个原语了。基于它，你可以模拟出限流、限速、限并发等多种情况，用更真实和复杂的场景来检测你的系统是否正常。这一点，我们也留在下节课继续拆解，因为它会涉及到多个指令和原语的配合。

#### repeat_each
刚才我们提到了测试多个请求的情况，那么应该如何对同一个测试执行多次呢？针对这个问题，test::nginx 提供了一个全局的设置：repeat_each。它其实是一个 perl 函数，默认情况下是 repeat_each(1)，表示测试案例只运行一次。所以之前的测试案例中，我们都没有去单独设置它。自然，你可以在 run_test() 函数之前来设置它，比如将参数改为 2：


```
repeat_each(2);
run_tests();
```
那么，每个测试案例就都会被运行两次，以此类推。


#### more_headers
聊完了请求体，我们再来看下请求头。上面我们提到，test::nginx 在发送请求的时候，默认会带上 host 和 connection 这两个请求头。那么其他的请求头如何设置呢？其实，more_headers 就是专门做这件事儿的：

```
--- more_headers
X-Foo: blah
```
你可以用它来设置各种自定义的头。如果想设置多个头，那设置多行就可以了：

```
--- more_headers
X-Foo: 3
User-Agent: openresty
```
### 处理响应
发送完请求后，test::nginx 中最重要的部分就来了，那就是处理响应，我们会在这里判断响应是否符合预期。这里我们分为 4 个部分依次介绍，分别是响应体、响应头、响应码和日志。

#### response_body
与 request 原语对应的就是 response_body，下面是它们两个配置使用的例子：

```
=== TEST 1: sanity
--- config
    location /t {
        content_by_lua_block {
            ngx.say("hello")
        }
    }
--- request
GET /t
--- response_body
hello
```
这个测试案例，在响应体是 hello 的情况下会通过，其他情况就会报错。但如何返回体很长，我们怎么检测才合适呢？别着急，test::nginx 已经为你考虑好了，它支持用用正则表达式来检测响应体，比如下面这样的写法：

```
--- response_body_like
^he\w+$
```
这样你就可以对响应体进行非常灵活的检测了。不仅如此，test::nginx 还支持 unlike 的操作：

```
--- response_body_unlike
^he\w+$
```
这时候，如果响应体是hello，测试就不能通过了。同样的思路，了解完单个请求的检测后，我们再来看下多个请求的检测。下面是配合 pipelined_requests 一起使用的示例：

```
--- pipelined_requests eval
["GET /hello", "GET /world", "GET /foo", "GET /bar"]
--- response_body eval
["hello", "world", "oo", "bar"]
```
当然，这里需要注意的是，你发送了多少个请求，就需要有多少个响应来对应。


#### response_headers
第二个我们来说说响应头。响应头和请求头类似，每一行对应一个 header 的 key 和 value：

```
--- response_headers
X-RateLimit-Limit: 2
X-RateLimit-Remaining: 1
```
和响应体的检测一样，响应头也支持正则表达式和 unlike 操作，分别是 response_headers_like 、raw_response_headers_like 和 raw_response_headers_unlike。


#### error_code
第三个来看响应码。响应码的检测支持直接的比较，同时也支持 like 操作，比如下面两个示例：

```
--- error_code: 302
```
```
--- error_code_like: ^(?:500)?$
```
而对于多个请求的情况，error_code 自然也需要检测多次：

```
--- pipelined_requests eval
["GET /hello", "GET /hello", "GET /hello", "GET /hello"]
--- error_code eval
[200, 200, 503, 503]
```
#### error_log
最后一个检测项，就是错误日志了。在大部分的测试案例中，都不会产生错误日志。我们可以用 no_error_log 来检测：

```
--- no_error_log
[error]
```
在上面的例子中，如果 Nginx 的错误日志 error.log 中，出现 [error] 这个字符串，测试就会失败。这是一个很常用的功能，建议在你正常的测试中，都加上对错误日志的检测。自然，另一方面，我们也需要编写很多异常的测试案例，以便验证在出错的情况下，我们的代码是否正常处理。这种情况下，我们就需要错误日志中出现指定的字符串，这就是 error_log 的用武之地了：

```
--- error_log
hello world
```
上面这段配置，其实就在检测 error.log 中是否出现了 hello world。当然，你可以在其中，用 eval 嵌入 perl 代码的方式，来实现正则表达式的检测，比如下面这样的写法：
```
--- error_log eval
qr/\[notice\] .*?  \d+ hello world/
```
### 写在最后
今天，我们学习的是如何在 test::nginx 中发送请求和检测响应，包含了 body、header、响应码和错误日志等。通过这些原语的组合，你可以实现比较完整的测试案例集。


## 28 | test::nginx 还可以这样用？

在前面两个章节中，你已经掌握了 test::nginx 的大部分使用方法，我相信你已经能够看明白 OpenResty 项目中大部分的测试案例集了。这对于学习 OpenResty 和它的周边库而言，已经足够了。但如果你有志于成为 OpenResty 的代码贡献者，或者你正在自己的项目中使用 test::nginx 来编写测试案例，那么你还需要来学习一些更高级、更复杂的用法。今天的内容，可能会是这个专栏中最“高冷”的部分，因为这都是从来没有人分享过的内容。 以 lua-nginx-module 这个 OpenResty 中最核心的模块为例，全球一共有 70 多个贡献者，但并非每个贡献者都写过测试案例。所以，如果学完今天的课程，你在 test::nginx 上的理解，绝对可以进入全球 Top 100。

### 测试中的调试
首先，我们来看几个最简单、也是开发者最常用到的原语，它们在平时的调试中会被使用到。下面，我们就来依次介绍下，这几个调试相关的原语的使用场景。

#### ONLY
很多时候，我们都是在原有的测试案例集基础上，新增了一个测试案例。如果这个测试文件包含了很多的测试案例，那么从头到尾跑一遍显然是比较耗时的，这在你需要反复修改测试案例的时候尤为明显。那么，有没有什么方法只运行你指定的某一个测试案例呢？ ONLY 这个标记可以轻松实现这一点：

```
=== TEST 1: sanity
=== TEST 2: get
--- ONLY
```
上面这段伪码就展示了如何使用这个原语。把 --- ONLY 放在需要单独运行的测试案例的最后一行，那么使用 prove 来运行这个测试案例文件的时候，就会忽略其他所有的测试案例，只运行这一个测试了。不过，这只适合在你做调试的时候使用。所以， prove 命令发现 ONLY 标记的时候，也会给出提示，告诉你不要忘记在提交代码时把它去掉。


#### SKIP
与只执行一个测试案例对应的需求，就是忽略掉某一个测试案例。SKIP 这个标记，一般用于测试尚未实现的功能：

```
=== TEST 1: sanity
=== TEST 2: get
--- SKIP
```
从这段伪码你可以看到，它的用法和 ONLY 类似。因为我们是测试驱动开发，需要先编写测试案例；而在集体编码实现时，可能由于实现难度或者优先级的关系，导致某个功能需要延后实现。那么这时候，你就可以先跳过对应的测试案例集，等实现完成后，再把 SKIP 标记去掉即可。


#### LAST
还有一个常用的标记是 LAST，它的用法也很简单，在它之前的测试案例集都会被执行，后面的就会被忽略掉：

```
=== TEST 1: sanity
=== TEST 2: get
--- LAST
=== TEST 3: set
```
你可能疑惑，ONLY 和 SKIP 我能理解，但 LAST 这个功能有什么用呢？实际上，有时候你的测试案例是有依赖关系的，需要你执行完前面几个测试案例后，之后的测试才有意义。那么，在这种情况下去调试的话，LAST 就非常有用了。


### 测试计划 plan
在 test::nginx 所有的原语中，plan 是最容易让人抓狂、也是最难理解的一个。它源自于 perl 的 Test::Plan 模块，所以文档并不在 test::nginx中，找到它的解释并不容易，所以我把它放在靠前的位置来介绍。我见过好几个 OpenResty 的代码贡献者，都在这个坑里面跌倒，甚至爬不出来。下面是一个示例，在 OpenResty 官方测试集的每一个文件的开始部分，你都能看到类似的配置：


```
plan tests => repeat_each() * (3 * blocks());
```
这里 plan 的含义是，在整个测试文件中，按照计划应该会做多少次检测项。如果最终运行的结果和计划不符，整个测试就会失败。拿这个示例来说，如果 repeat_each 的值是 2，一共有 10 个测试案例，那么 plan 的值就应该是 2 x 3 x 10 = 60。这里估计你唯一搞不清楚的，就是数字 3 的含义吧，看上去完全是一个 magic number！别着急，我们继续看示例，一会儿你就能搞懂了。先来说说，你能算清楚下面这个测试案例中，plan 的正确值是多少吗？

```
=== TEST 1: sanity
--- config
    location /t {
        content_by_lua_block {
            ngx.say("hello")
        }
    }
--- request
GET /t
--- response_body
hello
```
我相信所有人都会得出 plan = 1 的结论，因为测试中只对 response_body 进行了校验。但，事实并非如此！正确的答案是， plan = 2。为什么呢？因为 test::nginx 中隐含了一个校验，也就是--- error_code: 200，它默认检测  HTTP 的 response code 是否为 200。所以，上面的 magic number 3，真实含义是在每一个测试中都显式地检测了两次，比如 body 和 error log；同时，隐式地检测了 response code。由于这个地方太容易出错，所以，我的建议是，推荐你用下面的方法，直接关闭掉 plan：

```
use Test::Nginx::Socket 'no_plan';
```
如果无法关闭，比如在 OpenResty 的官方测试集中遇到 plan 不准确的情况，建议你也不要去深究原因，直接在 plan 的表达式中增加或者减少数字即可：

```
plan tests => repeat_each() * (3 * blocks()) + 2;
```
这也是官方会使用到的方法。


### 预处理器
我们知道，在同一个测试文件的不同测试案例之间，可能会有一些共同的设置。如果在每一个测试案例中都重复设置，就会让代码显得冗余，后面修改起来也比较麻烦。这时候，你就可以使用 add_block_preprocessor 指令，来增加一段 perl 代码，比如下面这样来写：

```
add_block_preprocessor(sub {
    my $block = shift;

    if (!defined $block->config) {
        $block->set_value("config", <<'_END_');
    location = /t {
        echo $arg_a;
    }
    _END_
    }
});
```
这个预处理器，就会为所有的测试案例，都增加一段 config 的配置，而里面的内容就是 location /t。这样，在你后面的测试案例里，就都可以省略掉 config，直接访问即可：

```
=== TEST 1:
--- request
    GET /t?a=3
--- response_body
3

=== TEST 2:
--- request
    GET /t?a=blah
--- response_body
blah
```
### 自定义函数
除了在预处理器中增加 perl 代码之外，你还可以在 run_tests 原语之前，随意地增加 perl 函数，也就是我们所说的自定义函数。下面是一个示例，它增加了一个读取文件的函数，并结合 eval 指令，一起实现了 POST 文件的功能：

```
sub read_file {
    my $infile = shift;
    open my $in, $infile
        or die "cannot open $infile for reading: $!";
    my $content = do { local $/; <$in> };
    close $in;
    $content;
}

our $CONTENT = read_file("t/test.jpg");

run_tests;

__DATA__

=== TEST 1: sanity
--- request eval
"POST /\n$::CONTENT"
```
### 乱序
除了上面几点外，test::nginx 还有一个鲜为人知的坑：默认乱序、随机来执行测试案例，而非按照测试案例的前后顺序和编号来执行。它的初衷是想测试出更多的问题。毕竟，每一个测试案例运行完后，都会关闭 Nginx 进程，并启动新的 Nginx 来执行，结果不应该和顺序相关才对。对于底层的项目而言，确实如此。但是，对于应用层的项目来说，外部存在数据库等持久化存储。这时候的乱序执行，就会导致错误的结果。由于每次都是随机的，所以可能报错，也可能不报错，每次的报错还可能不同。这显然会给开发者带来困惑，就连我都在这里跌倒过好多次。所以，我的忠告就是：请关闭掉这个特性。你可以用下面这两行代码来关闭：
```
no_shuffle();
run_tests;
```
其中，no_shuffle 原语就是用来禁用随机，让测试严格按照测试案例的前后顺序来运行。

### reindex
最后，让我们聊一个不烧脑的、轻松一点儿的话题。OpenResty 的测试案例集，对格式有着严格的要求。每个测试案例之间都需要有 3 个换行来分割，测试案例的编号也要严格保持自增长。幸好，我们有对应的自动化工具 reindex 来做这些繁琐的事情，它隐藏在 [[ openresty-devel-utils]](https://github.com/openresty/openresty-devel-utils)  项目中，因为没有文档来介绍，知道的人很少。有兴趣的同学，可以尝试着把测试案例的编号打乱，或者增删分割的换行个数，然后用这个工具来整理下，看看是否可以还原。

### 写在最后
关于 test::nginx 的介绍就到此结束了。当然，它的功能其实还有更多，我们只讲了最核心最重要的一些。授人以鱼不如授人以渔，学习测试的基本方法和注意点我都已经教给你了，剩下的就需要你自己去官方的测试案例集中去挖掘了。


## 29 | 最容易失准的性能测试？你需要压测工具界的“悍马”wrk
在测试章节的最后一节课，我和你来聊聊性能测试。这部分内容并非 OpenResty 独有，对于其他的后端服务来说，都是一样适用的。性能测试很常见，在我们交付产品的时候，都会带有性能指标的要求，比如 QPS、TPS 达到多少，延时要低于多少毫秒，可以并发支持多少用户的连接等等。对于开源项目而言，我们发版本之前也会做一次性能测试，和上一个版本对比，看是否有明显的衰退。也有一些中立的网站，会发布同类产品的性能对比数据。不得不说，性能测试离我们真的很近。在我的十几年的工作中，针对不同的产品做过很多次性能测试，中间也踩过不少坑。后来，我逐渐地发现，性能测试做起来简单，但做对却并不容易，甚至可以说，很多性能测试的结果都是失准的。那么，如何做一个科学严谨的性能测试呢？今天这节课，且听我娓娓道来。

### 性能测试工具
工欲善其事，必先利其器。选择一个趁手的性能测试工具，是成功的一半。ab 这个 Apache Benchmark 工具你应该很熟悉，可以说是最简单的性能测试工具，但可惜的是并不好用。这是因为，当前服务端基本都基于协程和异步 I/O 来开发，性能不差；而 ab 利用不到机器的多核，生成的请求压力不够大。这种情况下，ab 测试得到的结果，并不真实，反而变成了 ab 自身的性能测试。

**所以，我们可以明确选择压测工具的一个标准，那就是：工具自身的性能非常强悍，可以生成足够大的压力，压垮服务端程序。**

当然，你也可以有钱任性，启动很多压测客户端，变为分布式压测系统。这自然是可行的，但不要忘记，与此同时的复杂度也跟着上去了。回到 OpenResty 的实践，我们推荐使用的性能测试工具是 wrk。先来说说，为什么选择它呢？

首先， wrk 满足工具选型的标准。单机的 wrk 产生的压力，可以轻松让 Nginx 跑满 CPU，其他服务端程序更是不在话下。其次， wrk 和 OpenResty 有很多类似的地方。wrk 也不是从零开始编写的一个开源项目，它站在 LuaJIT 和 Redis 这两个巨人的肩膀上，充分利用了系统的多核资源来生成请求。除此之外，wrk 还暴露了 Lua API，你可以嵌入自己的 Lua 脚本，来自定义请求的头和内容，使用非常灵活。那么该如何使用 wrk 呢？也很简单，看下面这段代码的内容：

```
wrk -t12 -c400 -d30s http://127.0.0.1:8080/index.html
```
这意味着 wrk 会使用 12 个线程，保持 400 个长连接，持续 30 秒钟，来给指定的 API 接口发送 HTTP 请求。当然，如果你不指定参数的话，wrk 会默认启动 2 个线程和 10 个长连接。


### 测试环境
找好测试工具后，我们还不能直接开始压力测试，还需要把测试环境给检查一遍，测试环境需要检查的主要有四项，下面我分别来详细讲讲。


#### 检查项一：关闭 SELinux
如果你是 CentOS/RedHat 系列的操作系统，建议你关闭 SELinux，不然可能会遇到不少诡异的权限问题。我们通过下面这个命令，查看 SELinux 是否开启：

```
$ sestatus
SELinux status: disabled
```
如果显示是开启的（enforcing），你可以通过$ setenforce 0来临时关闭；同时修改 /etc/selinux/config 文件来永久关闭，将 SELINUX=enforcing 改为 SELINUX=disabled。

#### 检查项二：最大打开文件数
然后，你需要用下面的命令，查看下当前系统的全局最大打开文件数：

```
$ cat /proc/sys/fs/file-nr
3984 0 3255296
```
这里的最后一个数字，就是最大打开文件数。如果你的机器中这个数字比较小，那就需要修改 /etc/sysctl.conf 文件来增大：

```
fs.file-max = 1020000
net.ipv4.ip_conntrack_max = 1020000
net.ipv4.netfilter.ip_conntrack_max = 1020000
```
修改完以后，还需要重启系统服务来生效：
```
sudo sysctl -p /etc/sysctl.conf
```
#### 检查项三：进程限制
除了系统的全局最大打开文件数，一个进程可以打开的文件数也是有限制的，你可以通过命令 ulimit 来查看：


```
$ ulimit -n
1024
```
你会发现，这个值默认是 1024，是一个很低的数值。因为每一个用户请求都会对应着一个文件句柄，而压力测试会产生大量的请求，所以我们需要增大这个数值，把它改为百万级别，你可以用下面的命令来临时修改：

```
$ ulimit -n 1024000
```
也可以修改配置文件 /etc/security/limits.conf 来永久生效：

```
* hard nofile 1024000
* soft nofile 1024000
```
#### 检查项四：Nginx 配置
最后，你还需要对 Nginx 的配置，做一个小的修改，也就是下面这两行代码的操作：


```
events {
    worker_connections 10240;
}
```
这样，我们就可以把每个 worker 的连接数增大了。因为它的默认值只有 512，这在大压力的测试下显然是不够的。

### 压测前检查
到此为止，测试环境已经准备好了。一定有人蠢蠢欲动想要上手测试了吧？且慢，在使用 wrk 发起测试之前，让我们最后再来检测一次。毕竟，人总会犯错，换个角度来做一次交叉测试，是非常重要的。最后的这次检测，可以分为两步。


#### 第一步，使用自动化工具 c1000k
它来自 SSDB 的作者：https://github.com/ideawu/c1000k。从名字你就能看出来，这个工具的目的，就是用来检测你的环境是否可以满足 100 万并发连接的要求。这个工具的使用也很简单。我们分别启动一个 server 和 client，对应着监听 7000 端口的服务端程序，以及发起压力测试的客户端程序，目的是为了模拟真实环境下的压力测试：

```
./server 7000
./client 127.0.0.1 7000
```
紧接着，client 会向 server 发送请求，检测当前的系统环境能否支持 100 万并发连接。你可以自己去运行一下，看看结果。



#### 第二步，检测服务端程序是否正常运行
如果服务端的程序不正常，那么压力测试可能就成了错误日志刷新测试，或者是 404 响应测试。
**所以，测试环境检测的最后一步，也是最重要的一步，就是跑一遍服务端的单元测试集，或者手动调用几个主要的接口，来保证 wrk 测试的所有接口、返回的内容和 http 响应码都正常，并且在 logs/error.log 中没有出现任何错误级别的信息。**

### 发送请求
好了，到现在，万事俱备，只欠东风了。让我们开始用 wrk 来做压力测试吧！

```
$ wrk -d 30 http://127.0.0.2:9080/hello
Running 30s test @ http://127.0.0.2:9080/hello
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   595.39us  178.51us  22.24ms   90.63%
    Req/Sec     8.33k   642.91     9.46k    59.80%
  499149 requests in 30.10s, 124.22MB read
Requests/sec:  16582.76
Transfer/sec:      4.13MB
```
这里，我并没有指定参数，所以 wrk 会默认启动 2 个线程和 10 个长连接。其实，你也并不需要把 wrk 的线程数和连接数调整得很大，只要能够让目标程序跑满 CPU 就达到要求了。但压测的时间一定不能太短，几秒钟的压测是没有意义的，不然很有可能服务端的程序还没加载完热数据，压测就已经结束了。同时，在压测期间，你需要使用 top 或者 htop 这样的监控工具，来确认服务端目标程序是否跑满 CPU。从现象上来看，如果 CPU 满载，而且压测停止后，CPU 和内存占用迅速降低，那么恭喜你，这次压测顺利完成。但如果有下面这样的异常，作为服务端开发的你就得特别留意了。

CPU 不能满载。这不会是 wrk 的问题，可能是网络的限制，更可能是你的代码中有阻塞的操作。你可以通过 review 代码来确定，也可以使用 off CPU 火焰图来确定。CPU 一直满载，即使压测停止仍然如此。这说明在代码中存在热循环，可能是正则表达式引起的，也可能是 LuaJIT 的 bug 引起的，这两点都是我在真实的环境中遇到过的问题。这时，你就需要用 on CPU 火焰图来确定了。

最后再来一起看下 wrk 的统计结果。关于这个结果，我们一般会关注两个值：第一个是 QPS，也就是 Requests/sec: 16582.76，这个数据很直接，表示服务端每秒钟处理了多少请求。第二个是延时 Latency 595.39us 178.51us 22.24ms 90.63%，这个数据和 QPS 一样重要，它体现了系统的响应速度。比如对于网关的应用来讲，我们就希望能够把延时控制在 1 毫秒以内。另外， wrk 还提供了 latency 参数，可以把延时的分布百分比详细地打印出来，比如：
```
Latency Distribution
     50%  134.00us
     75%  180.00us
     90%  247.00us
     99%  552.00us
```
不过，wrk 的延时分布数据并不准确，因为它人为地加入了网络和工具的扰动，放大了延时，这一点需要你特别注意。关于 wrk Latency Distribution，你可以通过我以前写的[这篇文章](https://mp.weixin.qq.com/s/n8a4wzmf6I8kUc-T47PylA)来了解详细内容。

### 写在最后
性能测试是个技术活儿，能做对、做好的人不多。希望今天这节课，能让你对性能测试有一个更全面的认识。

## 30 | 答疑（三）如何搭建测试的网络结构？

专栏更新到现在，OpenResty 第三版块 OpenResty 测试篇，我们就已经学完了。恭喜你没有掉队，仍然在积极学习和实践操作，并且热情地留下了你的思考。很多留言提出的问题很有价值，大部分我都已经在 App 里回复过，一些手机上不方便回复的或者比较典型、有趣的问题，我专门摘了出来，作为今天的答疑内容，集中回复。另一方面，也是为了保证所有人都不漏掉任何一个重点。下面我们来看今天的这 5 个问题。

问题一：如何搭建测试的网络结构？Q：跑 wrk 的客户端，是应该放在外网上的机器上，还是和服务端同一局域网内的机器上呢？这两者，哪个更有性能测试意义？

A：其实，对于测试 web 相关的服务来说，选择正确的测试工具，只能算得上是一个好的开端，如何搭建测试的网络结构，也是后续的重要一环。一般来说，我们肯定希望排除所有网络的干扰，单独测试出服务的性能极限来。出于这个目的，我们可以有两种搭建网络的方法来做压测。第一种方法，把 wrk 和服务端程序都部署在同一台性能比较好的机器上。比如， 我们在 Nginx 中开启 8 个 worker，剩下的几个 CPU 资源分给 wrk。这样一来，就只有本地的网络通信，可以把网络的影响降到最低。第二种方法，用专门的路由器搭建一个局域网，把 wrk 所在的机器和服务端所在的机器连在一起。

之所以不推荐你在已有的网络中直接测试，是因为大部分的网络中都存在交换机和防火墙，它们可能会对大流量的压测进行限制，造成测试结果的不准确。另外，关于性能测试工具，我还想再多提几句。性能测试工具可能存在 Coordinated Omission 问题，在分析工具的延时数据的时候，你一定要特别留意。简单地说，Coordinated Omission（协调遗漏） 是指，在做压力测试时，对于响应来说，只统计发送和收到回复之间的时间是不够的，这只是服务时间，这样统计会遗漏很多潜在的问题。因此，我们还需要把测试请求的等待时间也计算在内，这个整体才算是用户关心的响应时间。当然，如果你的服务端程序可能会出现阻塞，一定需要考虑这个问题，否则就可以忽略掉了。

问题二：test::nginx 可以测试 ssl 相关功能吗？Q：ssl 相关功能，用test::nginx是不是测不了？

A：事实显然不是这样的，test::nginx 可以测试 ssl 的相关功能，你可以参考 https://github.com/iresty/apisix/blob/master/t/node/ssl.t，这个测试案例文件测试了 ssl 证书的全过程。你可以看到，测试案例使用 Lua 代码，来读取本地证书的公钥和私钥；然后，再通过 http API 设置好证书；最后，用 cosocket 来 ssl 握手和访问，验证证书是否生效。其实，不仅仅是 ssl 这个功能，只要是 OpenResty 中包含的功能，使用 test::nginx 都是可以覆盖的。当你不确定某个功能用 test::nginx能不能实现时，可以先去 lua-nginx-module 和其他的 OpenResty 开源项目的测试案例集中搜索，一般都能找到对应的示例。我也是用这种方法来解决这类问题的，毕竟，test::nginx的可玩性和变化性比较大，总有一些意想不到的使用组合和奇技淫巧在等着你发掘。

问题三：DSL 究竟是什么？Q：DSL 的翻译是领域专用语言吗？文中讲了它是领域小语言，但我搜这个词没有搜到，只搜到了领域专用语言 DSL（Domain Specific Language）。

A：DSL 确实是领域专用语言的缩写，而小语言是 DSL 的俗称。之所以在前面加了一个“小”字，是因为  DSL 的目的和常用的开发语言不同，它不是为了解决通用领域的需求，而是要解决某个领域的需求。最著名的 DSL 就是 SQL，结构化查询语言，用在数据库领域。至于test::nginx，它其实是为了解决 Nginx 和 OpenResty 的测试需求而创造出来的 DSL。实际上，OpenResty 的作者发明了很多小语言，这种 DSL 的思路，也将会给 OpenResty 社区带来不少新的尝试和解决方案。不过，正如之前文章中提到的一样，DSL 是把双刃剑，能否给最终使用者带来生产力的提升，才是衡量 DSL 是否有价值的主要标准。

问题四：test::nginx的安装问题Q：在执行完git clone后，是否需要执行下面的命令，才能安装test::nginx呢?

```
cd test-nginx
perl Makefile.PL
make
sudo make install
```
A：事实上并非如此，这里其实你可以参考一些开源项目中 travis 的做法。第一步，先通过包管理器安装 https://github.com/iresty/apisix/blob/master/.travis/linux_runner.sh#L20：


```
sudo cpanm --notest Test::Nginx >build.log 2>&1 || (cat build.log && exit 1)
```
第二步，git clone 最新的 test::nginx  https://github.com/iresty/apisix/blob/master/.travis/linux_runner.sh#L35：

```
    git clone https://github.com/openresty/test-nginx.git test-nginx
```
第三步，用 prove 命令的时候，把 test nginx 的目录包含进去：

```
prove -Itest-nginx/lib -r t
```
前面我也提到过，OpenResty 以及周边的项目，安装的最佳指南都存在于 travis CI 中，而不是文档中。这一点可能与其他项目的做法不同，主要是因为， OpenResty 自己维护了一些周边项目的 fork 或者特定版本；同时， OpenResty 也是强依赖 travis CI 的。所以，你应该按照 travis CI 中构建的方法来使用和测试 OpenResty，才能保证和官方一致。

问题五：ab 测试工具到底好不好用？Q：我怎么记得春哥在 Google Groups 里，多次提到 ab 是当前最佳测试工具呢？

A：文章中我也提到过了，单从工具特性来说，ab 并不是一个好的性能测试工具。因为它不能够产生足够大的请求压力，而现在的服务端程序性能却已经非常强悍了。我们在 test::nginx 中确实会用到 ab，而不是 wrk，这是因为在 TEST_NGINX_BENCHMARK 模式下，test::nginx 会根据 HTTP 协议版本，选择使用 ab 或者 weighttp ，来作为压力测试的工具。另外，希望你注意到的是，互联网技术的更新换代非常快，我们身在其中的每个人，都需要及时更新自己的知识和技能数。比如说test::nginx 的这个选择，在我看来现在已经需要更新了，而春哥当时可能还不知道 wrk 的存在。当然，也许再过一段时间，会有比 wrk 更好的性能测试工具出现，我们自然也应该抱着积极开放的心态去学习和选择。


## 性能优化篇
## 31 | 性能下降10倍的真凶：阻塞函数

通过前面几个章节的学习，相信你已经对 LuaJIT、OpenResty 的架构，以及 Lua API 和测试等方面有了全面的了解。下面，我们就要进入本专栏内容最多，也是最容易被忽视的性能优化章节了。在性能优化章节中，我会带你熟悉 OpenResty 中性能优化的方方面面，并把前面章节中提到的零散内容，总结为全面的 OpenResty 的编码指南，以便你编写出更高质量的 OpenResty 代码。要知道，提升性能并不容易，你需要考虑到系统架构优化、数据库优化、代码优化、性能测试、火焰图分析等不少步骤。但相反，降低性能却很容易，就像今天这节课的标题一样，你只需要加几行代码，就可以让性能下降 10 倍甚至更多。如果你使用了 OpenResty 来编写代码，但性能却一直提不上去，那么很可能就是因为使用了阻塞函数。所以，在介绍性能优化的具体方法之前，让我们先来了解下 OpenResty 编程中的一个重要原则：避免使用阻塞函数。

我们从小就被家长和老师教育，不要玩火，不要触碰插头，这些都是危险的行为。同样的，在 OpenResty 中也存有这种危险的行为。如果你的代码中存在阻塞的操作，就会导致性能的急剧下降，那么我们使用 OpenResty 来搭建高性能服务端的初衷，也将会落空。

### 为什么不要用阻塞操作？
了解哪些行为是危险的，并避免使用它们，是性能优化的第一步。让我们先来回顾下，为什么阻塞操作会影响 OpenResty 的性能。OpenResty 之所以可以保持很高的性能，简单来说，是因为它借用了 Nginx 的事件处理和 Lua 的协程机制，所以：

在遇到网络 I/O 等需要等待返回才能继续的操作时，就会先调用 Lua 协程的 yield 把自己挂起，然后在 Nginx 中注册回调；在 I/O 操作完成（也可能是超时或者出错）后，由 Nginx 回调 resume，来唤醒 Lua 协程。

这样的流程，保证了 OpenResty 可以一直高效地使用 CPU 资源，来处理所有的请求。在这个处理流程中，如果没有使用 cosocket 这种非阻塞的方式，而是用阻塞的函数来处理 I/O，那么 LuaJIT 就不会把控制权交给 Nginx 的事件循环。这就会导致，其他的请求要一直排队等待阻塞的事件处理完，才会得到响应。综上所述，在 OpenResty 的编程中，对于可能出现阻塞的函数调用，我们要特别谨慎；否则，一行阻塞的代码，就会把整个服务的性能拖垮。

下面，我再来介绍几个常见的坑，也就是一些经常会被误用的阻塞函数；我们也一起来体会下，如何用最简单的方式“搞破坏“，快速让你的服务性能下降 10 倍。


### 执行外部命令
在很多的场景下，开发者并不只是把 OpenResty 当作 web 服务器，而是会赋予更多业务的逻辑在其中。这种情况下，就有可能需要调用外部的命令和工具，来辅助完成一些操作了。比如杀掉某个进程：

```
os.execute("kill -HUP " .. pid) 
```
或者是拷贝文件、使用 OpenSSL 生成密钥等耗时更久的一些操作：
```
os.execute(" cp test.exe /tmp ")


os.execute(" openssl genrsa -des3 -out private.pem 2048 ")
```
表面上看， os.execute 是 Lua 的内置函数，而在 Lua 世界中，也确实是用这种方式来调用外部命令的。但是，我们要记住，Lua 是一种嵌入式语言，它在不同的上下文环境中，会有完全不同的推荐用法。在 OpenResty 的环境中，os.execute 会阻塞当前请求。所以，如果这个命令的执行时间特别短，那么影响还不是很大；可如果这个命令，需要执行几百毫秒甚至几秒钟的时间，那么性能就会有急剧的下降。问题我们明白了，那么应该如何解决呢？一般来讲，有两个解决方案。

### 方案一：如果有 FFI 库可以使用，那么我们就优先使用 FFI 的方式来调用。
比如，上面我们是用 OpenSSL 的命令行来生成密钥，就可以改为，用 FFI 调用 OpenSSL 的 C 函数的方式来绕过。而对于杀掉某个进程的示例，你可以使用 lua-resty-signal 这个 OpenResty 自带的库，来非阻塞地解决。代码实现如下，当然，这里的lua-resty-signal ，其实也是用 FFI 去调用系统函数来解决的。


```
local resty_signal = require "resty.signal"
local pid = 12345


local ok, err = resty_signal.kill(pid, "KILL")
```
另外，在 LuaJIT 的官方网站上，专门有一个[页面](http://wiki.luajit.org/FFI-Bindings)，里面分门别类地介绍了各种 FFI 的绑定库。当你在处理图片、加解密等 CPU 密集运算的时候，可以先去里面看看，是否有已经封装好的库，可以拿来直接使用。

### 方案二：使用基于 ngx.pipe 的 lua-resty-shell 库。
正如之前介绍过的一样，你可以在 shell.run 中运行你自己的命令，它就是一个非阻塞的操作：

```
$ resty -e 'local shell = require "resty.shell"
local ok, stdout, stderr, reason, status =
    shell.run([[echo "hello, world"]])
    ngx.say(stdout) '
```
### 磁盘 I/O
我们再来看下，处理磁盘 I/O 的场景。在一个服务端程序中，读取本地的配置文件是一个很常见的操作，比如下面这段代码：

```
local path = "/conf/apisix.conf"
local file = io.open(path, "rb")
local content = file:read("*a") 
file:close()
```
这段代码使用 io.open ，来获取某个文件中的所有内容。不过，虽然它是一个阻塞的操作，但别忘了，事情都要在实际场景下来考虑。如果你在 init 和 init worker 中调用，那么它其实是个一次性的动作，并没有影响任何终端用户的请求，是完全可以被接受的。当然，如果每一个用户的请求，都会触发磁盘的读写，那就变得不可接受了。这时，你就需要认真地考虑解决方案了。第一种方式，我们可以使用 lua-io-nginx-module 这个第三方的 C 模块。它为 OpenResty 提供了“非阻塞”的 Lua API，不过，这里的非阻塞是加了引号的，你不能像 cosocket 一样，随心所欲地去使用它。因为磁盘的 I/O 消耗并不会平白无故地消失，只不过是换了一种方式而已。这种方式的原理是，lua-io-nginx-module 利用了 Nginx 的线程池，把磁盘 I/O 操作从主线程转移到另外一个线程中处理，这样，主线程就不会因为磁盘 I/O 操作而被阻塞。不过，使用这个库时，你需要重新编译 Nginx，因为它是一个 C 模块。它的使用方法如下，和 Lua 的 I/O 库基本是一致的：

```
local ngx_io = require "ngx.io"
local path = "/conf/apisix.conf"
 local file, err = ngx_io.open(path, "rb")
 local data, err = file: read("*a") 
file:close()
```
第二种方式，则是尝试架构上的调整。对于这类磁盘 I/O，我们是否可以换种方式，不再读写本地磁盘呢？这里我举一个例子，你可以举一反三去思考。在多年之前，我经手的一个项目中，需要在本地磁盘中记录日志，以便统计和排除问题。当时的开发者，是用 ngx.log 来写这些日志的，就像下面这样：

```
ngx.log(ngx.WARN, "info")
```
这行代码调用的是 OpenResty 提供的 Lua API，看上去没有任何问题。但是，缺点在于，你不能频繁地去调用它。首先， ngx.log 本身就是一个代价不小的函数调用；其次，即使有缓冲区，大量而频繁的磁盘写入，也会严重地影响性能。那该如何解决呢？让我们回到原始的需求——统计和排错，而写入本地磁盘，本就只是达成目的的手段之一。所以，你还可以把日志发送到远端的日志服务器上，这样就可以用 cosocket 来完成非阻塞的网络通信了，也就是把阻塞的磁盘 I/O 丢给日志服务，不要阻塞对外的服务。你可以使用 lua-resty-logger-socket ，来完成这样的工作：

```
local logger = require "resty.logger.socket"
if not logger.initted() then
    local ok, err = logger.init{
        host = 'xxx',
        port = 1234,
        flush_limit = 1234,
        drop_limit = 5678,
    }
local msg = "foo"
local bytes, err = logger.log(msg)
```
其实，你应该也发现了，上面两个方法的本质都是一样的：如果阻塞不可避免，那就不要阻塞主要的工作线程，丢给外部的其他线程或者服务就可以了。

### luasocket
最后，我们来说说 luasocket ，它也是容易被开发者用到的一个 Lua 内置库，经常有人分不清 luasocket 和 OpenResty 提供的 cosocket。luasocket 也可以完成网络通信的功能，但它并没有非阻塞的优势。如果你使用了 luasocket，那么性能也会急剧下降。但是，luasocket 同样有它独特的使用场景。不知道你还记得吗？前面我们讲过，cosocket 在不少阶段是无法使用的，我们一般可以用 ngx.timer 的方式来绕过。同时，你也可以在 init_by_lua* 和 init_worker_by_lua* 这种一次性的阶段中，使用 luasocket 来完成 cosocket 的功能。越熟悉 OpenResty 和 Lua 的异同，你就越能找到类似这样的有趣的解决方案。另外，lua-resty-socket 其实就是一个二次封装的开源库，它做到了 luasocket 和 cosocket 的兼容。这个内容也值得进一步研究，如果你学有余力，这里我给你准备了继续学习的[资料](https://github.com/thibaultcha/lua-resty-socket/)。


## 32 | 让人又恨又爱的字符串操作

上节课里，我带你熟悉了 OpenResty 中常见的阻塞函数，它们都是初学者经常犯错的地方。从今天开始，我们就要进入性能优化的核心部分了，这其中会涉及到很多优化的技巧，可以帮助你快速提升 OpenResty 代码的性能，所以千万不要掉以轻心。在这个过程中，你需要多写一些测试代码，来体会这些优化技巧如何使用，并验证它们的有效性，做到心中有数，拿来即用。

### 性能优化技巧的背后
优化技巧都是属于“术”的部分，在此之前，我们不妨先来聊一下优化之“道”。性能优化的技巧，会随着 LuaJIT 和 OpenResty 的版本迭代而发生变化，一些技巧可能直接被底层技术优化，不再需要我们掌握；同时，也另会有一些新的优化技巧产生。所以，掌握这些优化技巧背后的不变的理念，才是最为重要的。下面，让我们先来看下，在 OpenResty 编程中，有关性能方面的几个重要理念。

#### 理念一：处理请求要短、平、快
OpenResty 是一个 Web 服务器，所以经常会同时处理几千、几万甚至几十万的终端请求。想要在整体上达到最高性能，我们就一定要保证单个请求被快速地处理完成，并回收内存等各种资源。这里提到的“短”，是指请求的生命周期要短，不要长时间占用资源而不释放；即使是长连接，也要设定一个时间或者请求次数的阈值，来定期地释放资源。第二个字“平”，则是指在一个 API 中只做一件事情。要把复杂的业务逻辑拆散为多个 API，保持代码的简洁。最后的“快”，是指不要阻塞主线程，不要有大量 CPU 运算。即使是不得不有这样的逻辑，也别忘了咱们上节课介绍的方法，要配合其他的服务去完成。

其实，这种架构上的考虑，不仅适合 OpenResty，在其他的开发语言和平台上也都是适用的，希望你能认真理解和思考。

#### 理念二：避免产生中间数据
避免中间的无用数据，可以说是 OpenResty 编程中最为主要的优化理念。这里，我先给你举一个小例子，来讲解下什么是中间的无用数据。我们来看下面这段代码：
```
$ resty -e 'local s= "hello"
s = s .. " world"
s = s .. "!"
print(s)
'
```
这段代码，我们对s 这个变量做了多次拼接操作，才得到了hello world! 对结果。但很显然，只有 s 的最终状态，也就是 hello world! 这个状态是有用的。而 s 的初始值和中间的赋值，都属于中间数据，应该尽量少生成。因为这些临时数据，会带来初始化和 GC 的性能损耗。不要小看这些损耗，如果这出现在循环等热代码中，就会带来非常明显的性能下降了。稍后我也会用字符串的示例来讲解这一点。


### 字符串是不可变的！
现在，回到本节课的主题——字符串。这里，我着重强调，在 Lua 中，字符串是不可变的。当然，这并不是说字符串不能做拼接、修改等操作，而是想告诉你，在你修改一个字符串的时候，其实并没有改变原来的字符串，而是产生了一个新的字符串对象，并改变了对字符串的引用。自然，如果原有字符串没有其他的任何引用，就会给 Lua 的 GC 给回收掉。字符串不可变的好处显而易见，那就是节省内存。这样一来，同样内容的字符串在内存中就只有一份了，不同的变量都会指向同一个内存地址。至于这样设计的缺点，那就是涉及到字符串的新增和 GC 时，每当你新增一个字符串，LuaJIT 都得调用 lj_str_new，去查询这个字符串是否已经存在；没有的话，便需要再创建新的字符串。如果操作很频繁，自然就会对性能有非常大的影响。我们来看一个具体的例子，类似这个例子中的字符串拼接操作，在很多 OpenResty 的开源项目中都会出现：

```
$ resty -e 'local begin = ngx.now()
local s = ""
-- for 循环，使用 .. 进行字符串拼接
for i = 1, 100000 do
    s = s .. "a"
end
ngx.update_time()
print(ngx.now() - begin)
'
```
这段示例代码的作用，是对s 变量做十万次字符串拼接，并把运行时间打印出来。虽然例子有些极端，但却能很好地体现出性能优化前后的差异。未经优化时，这段代码在我的笔记本上跑了 0.4 秒钟，还是比较慢的。那么应该如何优化呢？在前面的课程里，我其实已经给出了答案，那就是使用 table 做一层封装，去掉所有临时的中间字符串，只保留原始数据和最终结果。我们来看下具体的代码实现：

```
$ resty -e 'local begin = ngx.now()
local t = {}
-- for 循环，使用数组来保存字符串，每次都计算数组长度
for i = 1, 100000 do
    t[#t + 1] = "a"
end
-- 使用数组的 concat 方法拼接字符串
local s =  table.concat(t, "")
ngx.update_time()
print(ngx.now() - begin)
'
```
你可以看到，我用 table 依次保存了每一个字符串，下标由 #t + 1 来决定，也就是用 table 的当前长度加 1；最后，使用 table.concat 函数，把数组的每一个元素进行拼接，直接得到最终结果。这样自然就跳过了所有的临时字符串，避免了 10 万次 lj_str_new 和 GC。刚刚是我们对于代码的分析，那么优化的具体效果如何呢？很明显，优化后的代码耗时只有 0.007 秒，也就是说，性能提升了五十多倍。事实上，在实际的项目中，性能提升可能会更加明显，因为在这个示例中，我们每次只新增了一个字符 a。如果新增的字符串，是 10 个 a 的长度，性能差异会有多大呢？这是留给你的一个作业题，欢迎在留言中分享你运行的结果。回到我们的优化工作上，刚刚这段 0.007 秒的代码，是否就已经足够好了呢？其实不然，它还有继续优化的空间。我们不妨再来修改一行代码，然后来看下效果：


```
$ resty -e 'local begin = ngx.now()
local t = {}
-- for 循环，使用数组来保存字符串，自己维护数组的长度
for i = 1, 100000 do
    t[i] = "a"
end
local s =  table.concat(t, "")
ngx.update_time()
print(ngx.now() - begin)
'
```
这次，我把 t[#t + 1] = "a" ，改为了 t[i] = "a"，只修改了这么一行代码，却就可以避免十万次获取数组长度的函数调用。还记得我们之前在 table 章节中，提到的获取数组长度的操作吗？它的时间复杂度是 O(n)，显然是一个比较昂贵的操作。所以，这里我们干脆自己维护数组下标，绕过了这个获取数组长度的操作。正所谓，惹不起就躲着走呗。当然，这是比较简化的写法。我写的下面这段代码，则更加清楚地说明了，如何自己来维护数组下标，你可以参照理解：

```
$ resty -e 'local begin = ngx.now()
local t = {}
local index = 1
for i = 1, 100000 do
    t[index] = "a"
    index = index + 1
end
local s = table.concat(t, "")
ngx.update_time()
print(ngx.now() - begin)
'
```
### 减少其他临时字符串
刚刚我们所讲的，字符串拼接造成的临时字符串，还是显而易见的，通过上面几个示例代码的提醒，相信你就不会再犯类似的错误了。但是，OpenResty 中还存在着一些更隐蔽的临时字符串的产生，它们就更不容易被发现了。比如下面我将讲到的这个字符串处理函数，是经常被用到的，你能想到它也会生成临时的字符串吗？我们知道，string.sub 函数的作用是截取字符串的指定部分。正如我们前面所提到的，Lua 中的字符串是不可变的，那么截取出来的新字符串，就会涉及到 lj_str_new 和后续的 GC 操作。

```
resty -e 'print(string.sub("abcd", 1, 1))'
```
上面这段代码的作用，是获取字符串的第一个字符，并打印出来。自然，它不可避免会生成临时字符串。要完成同样的效果，还有别的更好的办法吗？

```
resty -e 'print(string.char(string.byte("abcd")))'
```
自然如此。看第二段代码，我们先用 string.byte 获取到第一个字符的数字编码，再用 string.char 把数字转为对应的字符。这个过程中并没有生成任何临时的字符串。因此，使用 string.byte 来完成字符串相关的扫描和分析，是效率最高的。


### 利用 SDK 对 table 类型的支持
学会了减少临时字符串的方法后，你是不是跃跃欲试了呢？我们可以把上面示例代码的结果，作为响应体的内容输出给客户端。到这里，你可以暂停一下，先自己动手试着写写这段代码。
```
$ resty -e 'local begin = ngx.now()
local t = {}
local index = 1
for i = 1, 100000 do
    t[index] = "a"
    index = index + 1
end
local response = table.concat(t, "")
ngx.say(response)
'
```
能写出这段代码，你就已经超越了绝大部分 OpenResty 的开发者了。不过，不要骄傲，你依然有进步的空间。OpenResty 的 Lua API ，已经考虑到了这种利用 table 来做字符串拼接的情况，所以，在 ngx.say、ngx.print 、ngx.log、cosocket:send 等这些可能接受大量字符串的 API 中，它不仅接受 string 作为参数，也同时接受 table 作为参数：

```
resty -e 'local begin = ngx.now()
local t = {}
local index = 1
for i = 1, 100000 do
    t[index] = "a"
    index = index + 1
end
ngx.say(t)
'
```
在最后这段代码中，我们省略掉了 local response = table.concat(t, "")， 这个字符串拼接的步骤，直接把 table 传给了 ngx.say。这样，就把字符串拼接的任务，从 Lua 层面转移到了 C 层面，又避免了一次字符串的查找、生成和 GC。对于比较长的字符串而言，这又是一次不小的性能提升。

### 写在最后
学完这节课，你应该也发现了，OpenResty 的性能优化，很多都是在抠各种细节。所以，你需要对 LuaJIT 和 OpenResty 的 Lua API 了如指掌，才能达到最优的性能。这也提醒你，前面的内容如果有遗忘了，一定要及时复习巩固了。


## 33 | 性能提升10倍的秘诀：必须用好 table

在 OpenResty 中，除了字符串经常出现性能问题外，table 也是性能的拦路虎。在之前的章节中，我们零零散散地介绍过 table 相关的函数，但并没有专门提到它对性能方面的提升。今天，我就带你专门来聊聊，table 操作对性能的影响。不同于对字符串的熟悉，开发者对于 table 相关的性能优化知之甚少，这主要有两个方面的原因。

其一，OpenResty 中使用的是 Lua ，是自己的 LuaJIT 分支，不是标准的 LuaJIT，也不是标准的 Lua。而大部分开发者并不知道它们之间的区别，倾向于使用标准 Lua 的 table 库来写 OpenResty 代码。其二，在标准 LuaJIT 和 OpenResty 自己的 LuaJIT 分支中，table 操作相关的文档都藏得非常深，开发者很难找到；而且文档中也没有示例代码，需要开发者自己去开源项目中寻找示例。


这就形成了比较高的认知壁垒，导致了两极分化的结果——资深的 OpenResty 开发者能够写出很高性能的代码，而刚入门的则会怀疑 OpenResty 的高性能是不是一个泡沫。当然，等你学习完这节课的内容，你就可以轻松地戳破这层窗户纸，让性能提升 10 倍不是梦。在详细介绍 table 优化之前，我想先强调的一点是，table 相关的优化，有一个自己的简单原则：

**尽量复用，避免不必要的 table 创建。**

你先记住这一点，下面，我们就从 table 的创建、元素的插入、清空、循环使用等方面，分别来介绍相关的优化。



### 预先生成数组
第一步，自然是创建数组。在 Lua 中，我们创建数组的方式很简单：

```
local t = {}
```
上面这行代码，就创建了一个空数组；当然，你也可以在创建的时候，就加上初始化的数据：


```
local color = {first = "red", "blue", third = "green", "yellow"}
```
不过，第二种写法对于性能的损失比较大，原因在于每次新增和删除数组元素的时候，都会涉及到数组的空间分配、resize 和 rehash。那么应该如何优化呢？空间换时间，是一种常见的优化思路。既然这里的性能瓶颈是动态分配数组空间，那么优化的方向，就可以是预先生成一个指定大小的数组。这样做虽然可能会浪费一部分的内存空间，但多次的空间分配、resize 和 rehash 等动作，就可以合并为一次完成了，效率高了不少。

事实上，LuaJIT 中的 table.new(narray, nhash) 函数，就是因此而新增的。这个函数，会预先分配好指定的数组和哈希的空间大小，而不是在插入元素时自增长，这也是它的两个参数 narray 和 nhash 的含义。下面我们通过一个简单的例子，来看下具体的使用。因为这个函数是 LuaJIT 扩展出来的，所以，在使用它之前，我们需要先 require 一下：

```
local new_tab = require "table.new"
 local t = new_tab(100, 0)
 for i = 1, 100 do
   t[i] = i
 end
```
另外，因为之前的 OpenResty 并没有完全绑定 LuaJIT，还支持标准 Lua，所以有些旧的代码会做这方面的兼容。如果没有找到 table.new 这个函数，就会模拟出来一个空的函数，来保证调用方的统一。
```
local ok, new_tab = pcall(require, "table.new")
  if not ok then
    new_tab = function (narr, nrec) return {} end
  end
```
### 自己计算 table 下标
有了 table 对象之后，下一步就是向它里面增加元素了。最直接的方法，就是调用 table.insert 这个函数来插入元素：

```
local new_tab = require "table.new"
 local t = new_tab(100, 0)
 for i = 1, 100 do
   table.insert(t, i)
 end
```
或者是先获取当前数组的长度，通过下标的方式来插入元素：
```
local new_tab = require "table.new"
 local t = new_tab(100, 0)
 for i = 1, 100 do
   t[#t + 1] = i
 end
```
不过，这两种方式都需要先计算数组的长度，然后再新增元素。显然，这个操作是 O(n) 的时间复杂度。就拿上面代码的例子来说，for 循环会计算 100 次数组的长度，这样下来性能自然不乐观，并且数组越大时，性能也会越低。这一点又该如何解决呢？让我们看下 lua-resty-redis 这个官方的库是如何做的吧：

```
local function _gen_req(args)
    local nargs = #args


    local req = new_tab(nargs * 5 + 1, 0)
    req[1] = "*" .. nargs .. "\r\n"
    local nbits = 2


    for i = 1, nargs do
        local arg = args[i]
        req[nbits] = "$"
        req[nbits + 1] = #arg
        req[nbits + 2] = "\r\n"
        req[nbits + 3] = arg
        req[nbits + 4] = "\r\n"
        nbits = nbits + 5
    end
    return req
en
```
这个函数预先生成了数组 req，它的大小由函数的入参来决定，这样就可以保证尽量不浪费空间。然后，它使用 nbits 这个变量，来自己维护 req 的下标，自然就抛弃了 Lua 内置的 table.insert 函数和获取长度的操作符 #。你可以看到，在 for 循环中，nbits + 1 等一些运算，就是直接用下标的方式插入元素；并在最后用 nbits = nbits + 5 ，让下标保持一个正确的值。这种的好处很明显，它省略了获取数组大小这个 O(n) 的操作，而是直接用下标访问，时间复杂度也变成了 O(1) 。当然，缺点也一样明显，那就是降低了代码的可读性，并且出错概率大大提高，可以说，这是一把双刃剑。

### 循环使用单个 table
既然 table 这么来之不易，我们自然要好好珍惜，尽量做到重复使用。不过，循环利用也是有条件的。我们先要把 table 中原有的数据清理干净，以免对下一个使用者造成污染。这时，table.clear 函数就派上用场了。从它的名字你就能看出它的作用，它会把数组中的所有数据清空，但数组的大小不会变。也就是说，你用 table.new(narray, nhash) 生了一个长度为 100 的数组，clear 后，长度还是 100。为了让你能够更清楚它的实现，下面我给出了一个代码示例，它兼容了标准 Lua：

```
local ok, clear_tab = pcall(require, "table.clear")
  if not ok then
    clear_tab = function (tab)
      for k, _ in pairs(tab) do
        tab[k] = nil
      end
    end
  end
```
可以看到，clear 函数实际上就是把每一个元素都置为了 nil。一般来说，我们会把这种循环使用的 table，放在一个模块的 top level 中。这样，在你使用模块中的函数的时候，就可以根据自己的实际情况来决定，到底是直接使用，还是 clear 后再使用。比如我们来看一个实际应用的例子。下面这段  [伪代码](https://github.com/iresty/apisix/blob/master/lua/apisix/plugin.lua)  取自开源的微服务 API 网关 APISIX，这是它在加载插件时候的逻辑：


```
local local_plugins = {}


function load()
    core.table.clear(local_plugins)


    local local_conf = core.config.local_conf()
    local plugin_names = local_conf.plugins


    local processed = {}
    for _, name in ipairs(plugin_names) do
        if processed[name] == nil then
            processed[name] = true
            insert_tab(local_plugins, name)
        end
    end


    return local_plugins
```
你可以看到，local_plugins 这个数组，是 plugin 这个模块的 top level 变量。在 load 这个加载插件函数的开始位置， table 就会被清空，然后根据当前的情况生成新的插件列表。

### table 池
到现在，你就掌握了对单个 table 循环使用的优化方法了。那么更进一步，你还可以用缓存池的方式来保存多个 table，以便随用随取，官方提供的 lua-tablepool 正是出于这个目的。下面这段代码，展示了 table 池的基本使用方法。我们可以从指定的池子中获取一个 table，使用完以后再释放回去：

```
local tablepool = require "tablepool"
 local tablepool_fetch = tablepool.fetch
 local tablepool_release = tablepool.release


local pool_name = "some_tag" 
local function do_sth()
     local t = tablepool_fetch(pool_name, 10, 0)
     -- -- using t for some purposes
    tablepool_release(pool_name, t) 
end
```
显然，tablepool 中会用到前面我们介绍过的几个方法，而且它的代码只有不到一百行，所以，如果你学有余力，我十分推荐你可以自己搜索并研究一下。这里，我主要介绍下它的两个 API。第一个是 fetch 方法，它的参数和 table.new 基本一样，只是多了一个 pool_name。如果池子中没有空闲的数组，fetch 方法就会调用 table.new 来新建一个数组。
```
tablepool.fetch(pool_name, narr, nrec)
```
第二个是 release 这个把 table 放回池子的函数。在它的参数中，最后的 no_clear ，用来配置是否要调用 table.clear 把数组清空。

```
tablepool.release(pool_name, tb, [no_clear])
```
你看，我们前面介绍到的方法，到这里是不是就全部串联起来了？不过，注意不要因此滥用 tablepool。tablepool 在实际项目中的使用并不多，比如 Kong 中就没有用到，APISIX 也只有少数几个调用。大多数情况下，不用 tablepool 的这层封装，也是足够我们使用的。

### 写在最后
性能优化，是 OpenResty 中的硬骨头，也是我们大家关注的热点。今天我介绍了 table 相关的性能优化技巧，希望能对你的实际项目有所帮助。


## 34 | 特别放送：OpenResty编码指南

很多开发语言都有自己的编码规范，来告诉开发者这个领域内一些约定俗成的东西，让大家写的代码风格保持一致，并且避免一些常见的陷阱。这对于新手来说是非常友好的，可以让初学者快速准确地上手。比如 Python 的 PEP 80，就是其中的典范，几乎所有的 Python 开发者都阅读过这份 Python 作者执笔的编码规范。

让开发者统一思想，按照规范来写代码，是一件非常重要的事情。OpenResty 还没有自己的编码规范，有些开发者在提交 PR 后，会在代码风格上被反复 review 和要求修改，消耗了大量本可避免的时间和精力。

其实，在 OpenResty 中，也有两个可以帮你自动化检测代码风格的工具：luacheck 和 lj-releng。前者是 Lua 和 OpenResty 世界通用的检测工具，后者则是 OpenResty 自己用 perl 写的代码检测工具。对我自己来说，我会在 VS Code 编辑器中安装 luacheck 的插件，这样在我写代码的时候就有工具来自动提示；而在项目的 CI 中，则是会把这两个工具都运行一遍，比如：

```
luacheck -q lua

./utils/lj-releng lua/*.lua lua/apisix/*.lua 
```
毕竟，多一个工具的检测总不是坏事。但是，这两个工具更多的是检测全局变量、每行长度等这些最基础的代码风格，离 Python PEP 80 的详细程度还有遥远的距离，并且也没有文档给你参考。所以今天，我就根据自己在 OpenResty 相关开源项目中的经验，总结了一下 OpenResty 的编码风格文档，这个规范也和一些常见的 API 网关比如 Kong、APISIX 的代码风格是一致的。


### 缩进
在 OpenResty 中，我们使用 4 个空格作为缩进的标记，虽然 Lua 并没有这样的语法要求。下面是错误和正确的两段代码示例：


```
--No
if a then
ngx.say("hello")
end
```
```
--yes
if a then
    ngx.say("hello")
end
```
为了方便，你可以在使用的编辑器中，把 tab 改为 4 个空格，来简化操作。

### 空格
在操作符的两边，都需要用一个空格来做分隔。下面是错误和正确的两段代码示例：

```
--No
local i=1
local s    =    "apisix"
```
```
--Yes
local i = 1
local s = "apisix"
```
### 空行
不少开发者会把其他语言的开发习惯带到 OpenResty 中来，比如在行尾增加一个分号：

```
--No
if a then
    ngx.say("hello");
end;
```
但事实上，增加分号会让 Lua 代码显得非常丑陋，也是没有必要的。同时，你也不要为了节省代码的行数，追求所谓的“简洁”，而把多行代码变为一行。这样做会让你在定位错误的时候，不知道到底是哪一段代码出了问题：

```
--No
if a then ngx.say("hello") end
```
```
--yes
if a then
    ngx.say("hello")
end
```
另外，函数之间需要用两个空行来做分隔：
```
--No
local function foo()
end
 local function bar()
end
```
```
--Yes
local function foo()
end


 local function bar()
end

```
如果有多个 if elseif 的分支，它们之间也需要一个空行来做分隔：


```
--No
if a == 1 then
    foo()    
elseif a== 2 then
    bar()    
elseif a == 3 then
    run()    
else
    error()
end
```
```
--Yes
if a == 1 then
    foo()

elseif a== 2 then
    bar()

elseif a == 3 then
    run()

else
    error()
end
```
### 每行最大长度
每行不能超过 80 个字符，如果超过的话，需要你换行并对齐。并且，在换行对齐的时候，我们要体现出上下两行的对应关系。就下面的示例而言，第二行函数的参数，要在第一行左括号的右边。

```
--No 
return limit_conn_new("plugin-limit-conn", conf.conn, conf.burst, conf.default_conn_delay)
```
```
--Yes
return limit_conn_new("plugin-limit-conn", conf.conn, conf.burst,
                    conf.default_conn_delay)
```
如果是字符串拼接问题的对齐，则需要把 .. 放到下一行中：


```
--No 
return limit_conn_new("plugin-limit-conn" ..  "plugin-limit-conn" ..
                    "plugin-limit-conn")
```
```
--Yes
return limit_conn_new("plugin-limit-conn" .. "plugin-limit-conn"
                    .. "plugin-limit-conn")
```
### 变量
这一点我前面也多次强调过，我们应该永远使用局部变量，不要使用全局变量：

```
--No
i = 1
s = "apisix"
```
```
--Yes
local i = 1
local s = "apisix"
```
至于变量的命名，应该使用 snake_case 风格：

```
--No
local IndexArr = 1
local str_Name = "apisix"
```
```
--Yes
local index_arr = 1
local str_name = "apisix"
```
而对于常量，则是要使用全部大写的形式：

```
--No
local max_int = 65535
local server_name = "apisix"
```
```
--Yes
local MAX_INT = 65535
local SERVER_NAME = "apisix"
```
### 数组
在 OpenResty 中，我们使用table.new 来预先分配数组：

```
--No
local t = {}
for i = 1, 100 do
   t[i] = i
 end
```
```
--Yes 
local new_tab = require "table.new"
 local t = new_tab(100, 0)
 for i = 1, 100 do
   t[i] = i
 end
```
另外注意，一定不要在数组中使用 nil：

```
--No
local t = {1, 2, nil, 3}
```
如果一定要使用空值，请用 ngx.null 来表示：

```
--Yes
local t = {1, 2, ngx.null, 3}
```
### 字符串
千万不要在热代码路径上拼接字符串：

```
--No
local s = ""
for i = 1, 100000 do
    s = s .. "a"
end
```
```
--Yes
local t = {}
for i = 1, 100000 do
    t[i] = "a"
end
local s =  table.concat(t, "")
```
### 函数
函数的命名也同样遵循 snake_case：


```
--No
local function testNginx()
end
```
```
--Yes
local function test_nginx()
end
```
并且，函数应该尽可能早地返回

```
--No
local function check(age, name)
    local ret = true
    if age < 20 then
        ret = false
    end

    if name == "a" then
        ret = false
    end
    -- do something else 
    return ret 
```
```
--Yes
local function check(age, name)
    if age < 20 then
        return false
    end

    if name == "a" then
        return false
    end
    -- do something else 
    return true 
```
### 模块
所有 require 的库都要 local 化：

```
--No
local function foo()
    local ok, err = ngx.timer.at(delay, handler)
end
```
```
--Yes
local timer_at = ngx.timer.at

local function foo()
    local ok, err = timer_at(delay, handler)
end
```
为了风格的统一，require 和 ngx 也需要 local 化：

```
--No
local core = require("apisix.core")
local timer_at = ngx.timer.at

local function foo()
    local ok, err = timer_at(delay, handler)
end
```
```
--Yes
local ngx = ngx
local require = require
local core = require("apisix.core")
local timer_at = ngx.timer.at

local function foo()
    local ok, err = timer_at(delay, handler)
end
```
### 错误处理
对于有错误信息返回的函数，我们必须对错误信息进行判断和处理：

```
--No
local sock = ngx.socket.tcp()
 local ok = sock:connect("www.google.com", 80)
 ngx.say("successfully connected to google!")
```
```
--Yes
local sock = ngx.socket.tcp()
 local ok, err = sock:connect("www.google.com", 80)
 if not ok then
     ngx.say("failed to connect to google: ", err)
     return
 end
 ngx.say("successfully connected to google!")
```
而如果是自己编写的函数，错误信息要作为第二个参数，用字符串的格式返回：

```
--No
local function foo()
    local ok, err = func()
    if not ok then
        return false
    end
    return true
end
```
```
--No
local function foo()
    local ok, err = func()
    if not ok then
        return false, {msg = err}
    end
    return true
end
```
```
--Yes
local function foo()
    local ok, err = func()
    if not ok then
        return false, "failed to call func(): " .. err
    end
    return true
end
```
### 写在最后
这个编程规范算是一个最初版本，我会公开到 [GitHub](https://github.com/apache/incubator-apisix/blob/v1.3/CODE_STYLE.md) 中来持续更新和维护。如果文中没有包含到你想知道的规范，非常欢迎你留言提问，我来给你解答。也欢迎你把这篇规范分享出去，让更多的 OpenResty 使用者参与进来。

## 35 | [视频]实际项目中的性能优化：ingress-nginx中的几个PR解读

今天的内容，我同样会以视频的形式来讲解。老规矩，在你进行视频学习之前，先问你这么几个问题：如何在开源项目中找到可能存在的性能问题？在 Github 上，如何与其他开发者正确地交流？

这几个问题，也是今天视频课要解决的核心内容，希望你可以先自己思考一下，并带着问题来学习今天的视频内容。同时，我会给出相应的文字介绍，方便你在听完视频内容后，及时总结与复习。下面是今天这节课的文字介绍部分。


### 今日核心
[ingress-nginx](https://github.com/kubernetes/ingress-nginx) 是 k8s 官方的一个项目，主要使用 Go、 Nginx 和 lua-nginx-module 来处理入口流量。在今天的视频中，我会为你清楚介绍，如何运用我们刚刚学习的性能优化方面的知识，来发现开源项目的性能问题。要知道，在我们给开源项目贡献 PR 时，跑通测试案例集以及与项目维护者积极沟通，都是非常重要的。下面是 ingress-nginx 中，和 OpenResty 性能相关的两个 PR：

https://github.com/kubernetes/ingress-nginx/pull/3673      
https://github.com/kubernetes/ingress-nginx/pull/3674

从中你也可以发现，即使是资深的开发者，对 LuaJIT 相关的优化，可能也并不是很熟悉。一方面是因为，这两个 PR 涉及到的代码，并不会对整体系统造成严重的性能下降；另一个方面，这方面的优化知识，没有人系统地总结过，开发者即使想优化也找不到方向。事实上，很多时候，我们站在代码可读性和可维护性的角度来看，可有可无的优化是不必要的，你只要去优化那些被频繁执行的代码片段就可以了，过度优化是万恶之源。那么，学完今天这节课后，你是否可以在其他的开源项目中，找到类似的性能优化点呢？

### 课件参考
今天的课件已经上传到了我的 GitHub 上，你可以自己下载学习。链接如下：https://github.com/iresty/geektime-slides



## 36 | 盘点OpenResty的各种调试手段
在 OpenResty 的交流群里面，经常会有开发者提出这样的疑问：OpenResty 里面怎么调试呢？据我所知，OpenResty 中有一些支持断点调试的工具，包括 VSCode 中的插件，但至今使用并不广泛。包括作者 agentzh 以及我认识的几个贡献者在内，大家都是使用最简单的 ngx.log 和 ngx.say 来做调试。显然，这对于大部分的新手来说并不友好。难道说众多 OpenResty 的核心维护者们，在遇到疑难杂症的时候，手里就只有打印日志这个原始的方法了吗？当然不是，在 OpenResty 的世界中，SystemTap 和火焰图，才是处理棘手问题和性能问题的标准利器。如果你在邮件列表或者 issue 里面有这方面的提问，项目的维护者肯定会让你上传火焰图，要求用图说话而不是文字描述。接下来的两节课，我就和你聊聊调试，以及 OpenResty 专门为调试而创造的工具集。今天我们先来看下，有哪些调试程序的方法。


### 断点和打印日志
在我工作的很长一段时间里面，我都是依赖编辑器的高级调试功能来跟踪程序的，这个看上去也是理所当然的。对于能在测试环境中重现的问题，不管有多复杂，我都有信心可以找到问题的根源，这是因为，这个 bug 可以被不停地重复制造出来。只要通过设置断点和增加日志，问题的根源就会慢慢浮出水面，你所需要的，只是耐心罢了。从这个角度来看，解决测试环境中稳定复现的 bug，实际上是一个体力活。我工作中解决的绝大部分 bug 都属于这一类。不过要注意，这里有两个前提：测试环境，以及稳定复现。现实总没有那么理想，如果是线上环境才会复现的 bug，是否有调试的方法呢？这里我推荐一个工具——Mozilla RR，你可以把它当作是一个复读机，可以把程序的行为录制下来，然后反复地重放。说白了，不管线上环境还是测试环境，只要你能够把 bug 的“罪证”录制下来，那就可以作为“呈堂证供”慢慢地分析了。


### 二分查找和注释
不过，对于一些大型的项目，或者涉及面比较多的系统，比如 bug 可能来自多个服务中的某一个，也可能是查询数据库的 SQL 语句有问题，在这种情况下，即使 bug 能够稳定重现，你也并不能确定 bug 出现在哪一个环节。所以，Mozilla RR 这类录制的工具就失效了。这时候，你可能会回忆起“二分查找”这个经典的算法。我们先在代码中注释掉一半的逻辑，如果问题依旧，那么就说明 bug 出在没有被注释的代码中，这时再注释掉剩下的一半逻辑，继续上面的循环。用不了几次，问题就被缩小到一个完全可控的范围了。这个方法虽然听着有些笨，但在很多场景下确实见效很快。当然，随着技术的进步和系统复杂性的增加，现在我们更推荐使用 OpenTracing 这样的标准，来进行分布式追踪。OpenTracing 可以在系统的各处埋点，通过 Trace ID 把多个 Span 组成的调用链和埋点数据上报到服务端，进行分析和图形化的展现。这样就可以发现很多隐藏的问题，而且历史数据都会保存下来，方便我们随时对比和查看。另外，如果你的系统比较复杂，比如是在微服务的环境下，那么 Zipkin、Apache SkyWalking 都是不错的选择。


### 动态调试
上面我讲的这些调试方法，基本上已经可以解决大部分的问题了。但是，如果你遇到的是只在线上才会偶然出现的故障，那么通过增加日志、埋点的方式来追踪的话，就会耗费相当多的时间。我就曾经遇到过这样的一个 bug。多年前，我负责的一个系统在每天凌晨 1 点钟左右时，数据库资源就会被耗尽，并导致整个系统雪崩。当时，我们白天排查代码中的计划任务，到了晚上，团队的同学们就蹲守在公司等 bug 复现，复现的时候再去查看各自子模块的运行状态。这样下来，直到第三个晚上才找到了 bug 的元凶。我的这个经历，和 Solaris 几个系统工程师创造 Dtrace 的背景很类似。当时 Solaris 的工程师们，也是花了几天几夜的时间排查一个诡异的线上问题，最后才发现是因为一个配置写错了。但和我不同的是，Solaris 的工程师决定彻底避免这种问题，于是发明了 Dtrace，专门用于动态调试。动态调试，也叫做活体调试。和 GDB 这种静态调试工具不同，动态调试可以调试线上的服务，而对调试的程序而言，整个调试过程是无感知、无侵入的，不用你修改代码，更不用重启。打一个比方，动态调试就像 X 光，可以在病人无感知的情况下检查身体，而不需要抽血和胃镜。Dtrace 便是最早的动态追踪框架，受到它的影响，其他系统中也逐渐出现了类似的动态调试工具。比如，Red Hat 的工程师，就在 Linux 平台上创造了 Systemtap，也就是我接下来要讲的主角。


### Systemtap
Systemtap 有自己的 DSL，也就是小语言，可以用来设置探测点。在介绍更多的内容之前，为了不仅仅停留在抽象的概念上，让我们先来安装下 Systemtap 吧。这里，用系统的包管理器来安装就可以了：

```
sudo apt install systemtap
```
我们再来看下，用 Systemtap 写的 hello world 程序是什么样子的：

```
# cat hello-world.stp
probe begin
{
  print("hello world!")
  exit()
}
```
是不是很简单？不过，你需要使用 sudo 权限才可以运行：

```
sudo stap hello-world.stp
```
它会打印出我们想要的 hello world! 。在大部分场景下，我们都不需要自己写 stap 脚本来进行分析，因为 OpenResty 已经有了很多现成的 stap 脚本来做常规的分析，下节课我就会为你介绍这些脚本。所以，今天我们只用对 stap 脚本有一个简单的认识就行了。操作了几下后，回到我们的概念上来。Systemtap 的工作原理，是将上述 stap 脚本转换为 C，运行系统 C 编译器来创建 kernel 模块。当模块被加载的时候，它会通过 hook 内核的方式，来激活所有的探测事件。比如，刚刚这个示例代码中的 probe 就是一个探针。begin 会在探测的最开始运行，与之对应的是 end，所以上面的 hello world 程序也可以写成下面的这种方式：


```
probe begin
{
  print("hello ")
  exit()
}

probe end
{
print("world!") 
```
这里，我只对 Systemtap 进行了非常粗浅的介绍。其实，Systemtap 的作者 Frank Ch. Eigler 写了一本电子书《Systemtap tutorial》，详细地介绍了 Systemtap。如果你想进一步地学习和深入了解 Systemtap，那么我建议，从这本书开始入手，就是最好的学习路径。

### 其他动态追踪框架
当然，对于内核和性能分析工程师来说，只有 Systemtap 还是不够用的。首先， Systemtap 并没有默认进入系统内核；其次，它的工作原理决定了它的启动速度比较慢，而且有可能对系统的正常运行造成影响。eBPF（extended BPF）则是最近几年 Linux 内核中新增的特性。相比 Systemtap，eBPF 有内核直接支持、不会死机、启动速度快等优点；同时，它并没有使用 DSL，而是直接使用了 C 语言的语法，所以也大大降低了它的上手难度。除了开源的解决方案外，Intel 出品的 VTune 也是神兵利器之一。它直观的界面操作和数据展示，可以让你不写代码也能分析出性能的瓶颈。


### 火焰图
最后，让我们再来回忆下前面课程中提到过的火焰图。前面我们也提到过，perf 和 Systemtap 等工具产生的数据，都可以通过火焰图的方式，来进行更加直观的展示。下面这张图就是火焰图的示例：

![img](https://static001.geekbang.org/resource/image/6e/32/6e72452ac3b97d46a44234d41993c832.png?wh=1200*674)

在火焰图中，色块的颜色和深浅都是没有意义的，只是为了对不同的色块儿做出简单的区分。火焰图其实是把每次采样的数据进行叠加，所以，真正有意义的是色块的宽度和长度。对于 on CPU 火焰图来说，色块的宽度是函数占用的 CPU 时间百分比，色块越宽，则说明性能消耗越大。如果出现一个平顶的山峰，那它就是性能的瓶颈所在。而色块的长度，代表的是函数调用的深度，最顶端的框显示正在运行的函数，在它之下的都是这个函数的调用者。所以，在下面的函数是上面函数的父函数，山峰越高，则说明调用的函数层级越深。为了让你更透彻掌握火焰图这个利器，在后面的视频课中，我会用一个真实的代码案例，给你演示，如何使用火焰图来找出性能的瓶颈并解决它。


### 最后
要知道，哪怕是动态跟踪这种无侵入的技术，也并不是完美的。它只能检测某一个单独的进程，而且一般情况下，我们只短暂开启它，以使用这段时间内的采样数据。所以，如果你需要跨越多个服务，或者是进行长时间的检测，还是需要 opentracing 这样的分布式追踪技术。


## 37 | systemtap-toolkit和stapxx：如何用数据搞定“疑难杂症”？


正如上节课介绍过的，作为服务端开发工程师，我们并不会对动态调试的工具集做深入的学习，大都是停留在使用的这个层面上，最多去编写一些简单的 stap 脚本。更底层的，比如 CPU 缓存、体系结构、编译器等，那就是性能工程师的领域了。在 OpenResty 中有两个开源项目：openresty-systemtap-toolkit 和 stapxx 。它们是基于 Systemtap 封装好的工具集，用于 Nginx 和 OpenResty 的实时分析和诊断。它们可以覆盖 on CPU、off CPU、共享字典、垃圾回收、请求延迟、内存池、连接池、文件访问等常用的功能和调试场景。在今天这节课中，我会带你浏览下这些工具和对应的使用方法，目的是帮你在遇到 Nginx 和 OpenResty 的疑难杂症时，可以快速找到定位问题的工具。在 OpenResty 的世界中，学会使用这些工具是你进阶的必经之路，也是和其他开发者沟通的非常有效的方式——毕竟，工具产生的数据，会比你用文字描述更加准确和详尽。不过，需要特别注意的是，OpenResty 的最新版本 1.15.8 默认开启了 LuaJIT GC64 模式，但是 openresty-systemtap-toolkit 和 stapxx 并没有跟着做对应的修改，这就会导致里面的工具都无法正常使用。所以，你最好在 OpenResty 旧的 1.13 版本中来使用这些工具。开源项目的贡献者大都是兼职身份，他们并没有义务来保证这些工具可以一直正常使用，这也是你在使用开源项目时候需要意识到的一点。


### 以共享字典为例
按照惯例，我先用一个你最熟悉的、也是上手最简单的工具 ngx-lua-shdict，来作为今天开篇的示例。ngx-lua-shdict 这个工具，可以分析 Nginx 的共享内存字典，并且追踪字典的操作。你可以用 -f 选项指定 dict 和 key，来获取共享内存字典里面的数据。 --raw 选项可以导出指定 key 的原始值。下面是一个从共享内存字典中获取数据的命令行示例：

```
# 假设 nginx worker pid 是 5050
$ ./ngx-lua-shdict -p 5050 -f --dict dogs --key Jim --luajit20
Tracing 5050 (/opt/nginx/sbin/nginx)...

type: LUA_TBOOLEAN
value: true
expires: 1372719243270
flags: 0xa
```
类似的，你可以用 -w选项，来追踪指定 key 的字典写操作：

```
$./ngx-lua-shdict -p 5050 -w --key Jim --luajit20
Tracing 5050 (/opt/nginx/sbin/nginx)...

Hit Ctrl-C to end

set Jim exptime=4626322717216342016
replace Jim exptime=4626322717216342016
^C
```
让我们看看这个工具是怎么实现的吧。ngx-lua-shdict 是一个 perl 的脚本，但具体的实现和 perl 并没有关系，perl 只是被用来生成了 stap 脚本并运行起来：
```
open my $in, "|stap $stap_args -x $pid -" or die "Cannot run stap: $!\n";
```
你完全可以用 Python、PHP、Go 或者你喜欢的任何语言来编写。stap 脚本中，比较关键的地方是下面这行代码：

```
probe process("$nginx_path").function("ngx_http_lua_shdict_set_helper")
```
这就是我们在上节课中提到的探针probe，探测的是 ngx_http_lua_shdict_set_helper 这个函数。而这个函数的调用，都是在 lua-nginx-module 模块的 lua-nginx-module/src/ngx_http_lua_shdict.c 文件中：

```
static int
ngx_http_lua_shdict_add(lua_State *L)
{
return ngx_http_lua_shdict_set_helper(L, NGX_HTTP_LUA_SHDICT_ADD);
}

static int
ngx_http_lua_shdict_safe_add(lua_State *L)
{
return ngx_http_lua_shdict_set_helper(L, NGX_HTTP_LUA_SHDICT_ADD
|NGX_HTTP_LUA_SHDICT_SAFE_STORE);
}

static int
ngx_http_lua_shdict_replace(lua_State *L)
{
return ngx_http_lua_shdict_set_helper(L, NGX_HTTP_LUA_SHDICT_REPLACE);
}
```
这样，我们只要探测这个函数，就可以追踪到共享字典的所有操作了。


### on CPU 和 off CPU
在使用 OpenResty 的过程中，你最常遇到的应该就是性能问题了把。性能比较差，也就是 QPS 很低的表现主要有两类，CPU 占用过高和 CPU 占用过低。前者的瓶颈，可能是没有使用我们之前介绍过的性能优化的方法；而后者可能是因为使用了阻塞函数。相对应的，on CPU 和 off CPU 火焰图，可以帮助我们确认最终的根源所在。要生成 C 级别的 on CPU 火焰图，你需要使用 systemtap-toolkit 中的sample-bt；而 Lua 级别的 on CPU 火焰图，则是由 stapxx 中的 lj-lua-stacks 来生成的。我们以 sample-bt 为例来介绍下如何使用。sample-bt 这个脚本，可以对你指定的任意用户进程（不仅限于 Nginx 和 OpenResty 进程），来进行调用栈的采样。例如，我们可以用下列代码，对一个正在运行的 Nginx worker 进程（PID 是 8736）采样 5 秒钟：

```
$ ./sample-bt -p 8736 -t 5 -u > a.bt
WARNING: Tracing 8736 (/opt/nginx/sbin/nginx) in user-space only...
WARNING: Missing unwind data for module, rerun with 'stap -d stap_df60590ce8827444bfebaf5ea938b5a_11577'
WARNING: Time's up. Quitting now...(it may take a while)
WARNING: Number of errors: 0, skipped probes: 24
```
它输出的结果文件 a.bt， 可以使用 FlameGraph 工具集来生成火焰图:

```
stackcollapse-stap.pl a.bt > a.cbt
flamegraph.pl a.cbt > a.svg
```
这里的a.svg ，就是生成的火焰图，你可以用浏览器打开查看。不过要注意，在采样期间，我们需要保持一定的请求压力，否则采样数为 0 的话，就没办法生成火焰图了。接着我们再来看下如何采样 off CPU，你需要使用的脚本是 systemtap-toolkit 中的 sample-bt-off-cpu。它的使用方法和 sample-bt 类似，我也写在了下面的代码中：


```
$ ./sample-bt-off-cpu -p 10901 -t 5 > a.bt
WARNING: Tracing 10901 (/opt/nginx/sbin/nginx)...
WARNING: _stp_read_address failed to access memory location
WARNING: Time's up. Quitting now...(it may take a while)
WARNING: Number of errors: 0, skipped probes: 23
```
在 stapxx 中，分析延迟的工具是epoll-loop-blocking-distr，它会对指定的用户进程进行采样，并输出连续的 epoll_wait 系统调用之间的延迟分布：

```
$ ./samples/epoll-loop-blocking-distr.sxx -x 19647 --arg time=60
Start tracing 19647...
Please wait for 60 seconds.
Distribution of epoll loop blocking latencies (in milliseconds)
max/avg/min: 1097/0/0
value |-------------------------------------------------- count
    0 |@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@  18471
    1 |@@@@@@@@                                            3273
    2 |@                                                    473
    4 |                                                     119
    8 |                                                      67
   16 |                                                      51
   32 |                                                      35
   64 |                                                      20
  128 |                                                      23
  256 |                                                       9
  512 |                                                       2
 1024 |                                                       2
 2048 |                                                       0
 4096 |                                                       0
```
你可以看到，这个输出结果显示，绝大部分延迟都小于 1 毫秒，但也有少数是在 200 毫秒以上的，这些就是需要关注的。


### 上游和阶段跟踪
除了 OpenResty 的代码本身可能出现性能问题外，当 OpenResty 通过 cosocket 或者 proxy_pass 这样的上游模块，与上游服务进行通信时，如果上游服务自身的延时比较大，也会对整体的性能带来很大的影响。这个时候，你可以使用 ngx-lua-tcp-recv-time、ngx-lua-udp-recv-time 和 ngx-single-req-latency 这几个工具来进行分析，这里我以 ngx-single-req-latency 为例解释下。这个工具和工具集里面的大部分工具并不太一样。其他工具，多是基于大量的采样和统计分析，得出一个数学上的分布结论。而 ngx-single-req-latency 分析的却是单个的请求，跟踪出单个请求在 OpenResty 中各个阶段的耗时，比如 rewrite、access、content 阶段以及上游的耗时。我们可以来看一个具体的示例代码：


```
# making the ./stap++ tool visible in PATH:
    $ export PATH=$PWD:$PATH

    # assuming an nginx worker process's pid is 27327
    $ ./samples/ngx-single-req-latency.sxx -x 27327
    Start tracing process 27327 (/opt/nginx/sbin/nginx)...

    POST /api_json
        total: 143596us, accept() ~ header-read: 43048us, rewrite: 8us, pre-access: 7us, access: 6us, content: 100507us
        upstream: connect=29us, time-to-first-byte=99157us, read=103us

    $ ./samples/ngx-single-req-latency.sxx -x 27327
    Start tracing process 27327 (/opt/nginx/sbin/nginx)...

    GET /robots.txt
        total: 61198us, accept() ~ header-read: 33410us, rewrite: 7us, pre-access: 7us, access: 5us, content: 27750us
        upstream: connect=30us, time-to-first-byte=18955us, read=96us
```
这个工具会跟踪它启动后遇到的第一个请求。输出的内容和 opentracing 非常类似，你甚至可以把 systemtap-toolkit 和 stapxx ，当作是 OpenResty 中 APM（应用性能管理）的非侵入版本。

### 写在最后
除了今天我讲到的这些常用工具，OpenResty 自然还提供了更多的工具，它们就交给你自己去探索和学习了。其实，在追踪技术方面，OpenResty 和其他的开发语言、平台相比，还有一个比较大的不同之处，希望你可以慢慢体会：保持代码基的简洁和稳定，不要在其中增加探针，而是通过外部动态跟踪的技术来进行采样。


## 38 | [视频]巧用wrk和火焰图，科学定位性能瓶颈

今天是我们专栏中的最后一节视频课了，后面内容仍然以图文形式呈现。老规矩，为了更有针对性地学习，在你进行视频学习之前，我想先问你这么几个问题：你测试过 OpenResty 程序的性能吗？如何才能科学地找到性能瓶颈？如何看懂火焰图的信息，并与 Lua 代码相对应呢？这几个问题，也是今天视频课要解决的核心内容，希望你可以先自己思考一下，并带着问题来学习今天的视频内容。同时，我会给出相应的文字介绍，方便你在听完视频内容后，及时总结与复习。下面是今天这节课的文字介绍部分。


### 今日核心
今天的视频课，我会用一个开源的小项目来演示一下，如何通过 wrk 和火焰图来优化代码，这个项目地址为：https://github.com/iresty/lua-performance-demo。视频中的环境是 Ubuntu 16.04，其中的 systemtap 和 wrk 工具，都是使用 apt-get 来安装的，不推荐你用源码来安装。这里的 demo 有几个不同的版本，我会用 wrk 来压测每一个版本的 qps。同时，在压测过程中，我都会使用 stapxx 来生成火焰图，并用火焰图来指导我们去优化哪一个函数和代码块。最后的结果是，我们会看到一个性能提升 10 倍以上的版本，当然，这其中的优化方式，都是在专栏前面课程中提到过的。建议你可以 clone 这个 demo 项目，来复现我在视频中的操作，加深对 wrk、火焰图和性能优化的理解。要知道，性能优化并不是感性和直觉的判断，而是需要科学的数据来做指导的。这里的数据，不仅仅是指 qps 等最终的性能指标，也包括了用数据来定位具体的瓶颈。


### 课件参考
今天的课件已经上传到了我的 GitHub 上，你可以自己下载学习。链接如下：https://github.com/iresty/geektime-slides

## 39 | 高性能的关键：shared dict 缓存和 lru 缓存

在前面几节课中，我已经把 OpenResty 自身的优化技巧和性能调优的工具都介绍过了，分别涉及到字符串、table、Lua API、LuaJIT、SystemTap、火焰图等。这些都是系统优化的基石，需要你好好掌握。但是，只懂得它们，还是不足以面对真实的业务场景。在一个稍微复杂一些的业务中，保持高性能是一个系统性的工作，并不仅仅是代码和网关层面的优化。它会涉及到数据库、网络、协议、缓存、磁盘等各个方面，这也正是架构师存在的意义。今天这节课，就让我们一起来看下，性能优化中扮演非常关键角色的组件——缓存，看看它在 OpenResty 中是如何使用和进行优化的。

### 缓存
在硬件层面，大部分的计算机硬件都会用缓存来提高速度，比如 CPU 会有多级缓存，RAID 卡也有读写缓存。而在软件层面，我们用的数据库就是一个缓存设计非常好的例子。在 SQL 语句的优化、索引设计以及磁盘读写的各个地方，都有缓存。这里，我也建议你在设计自己的缓存之前，先去了解下 MySQL 里面的各种缓存机制。我给你推荐的资料是《High Performance MySQL》 这本非常棒的书。我在多年前负责数据库的时候，从这本书中获益良多，而且后来不少其他的优化场景，也借鉴了 MySQL 的设计。回到缓存上来说，我们知道，一个生产环境的缓存系统，需要根据自己的业务场景和系统瓶颈，来找出最好的方案。这是一门平衡的艺术。一般来说，缓存有两个原则。

一是越靠近用户的请求越好。比如，能用本地缓存的就不要发送 HTTP 请求，能用 CDN 缓存的就不要打到源站，能用 OpenResty 缓存的就不要打到数据库。二是尽量使用本进程和本机的缓存解决。因为跨了进程和机器甚至机房，缓存的网络开销就会非常大，这一点在高并发的时候会非常明显。

自然，在 OpenResty 中，缓存的设计和使用也遵循这两个原则。OpenResty 中有两个缓存的组件：shared dict 缓存和 lru 缓存。前者只能缓存字符串对象，缓存的数据有且只有一份，每一个 worker 都可以进行访问，所以常用于 worker 之间的数据通信。后者则可以缓存所有的 Lua 对象，但只能在单个 worker 进程内访问，有多少个 worker，就会有多少份缓存数据。下面这两个简单的表格，可以说明 shared dict 和 lru 缓存的区别：

![img](https://static001.geekbang.org/resource/image/ba/cd/baa08571c1ca48585d14a6141e2f00cd.png?wh=973*174)

shared dict 和 lru 缓存，并没有哪一个更好的说法，而是应该根据你的场景来配合使用。如果你没有 worker 之间共享数据的需求，那么 lru 可以缓存数组、函数等复杂的数据类型，并且性能最高，自然是首选。但如果你需要在 worker 之间共享数据，那就可以在 lru 缓存的基础上，加上 shared dict 的缓存，构成两级缓存的架构。

接下来，我们具体来看看这两种缓存方式。


### 共享字典缓存
在 Lua 章节中，我们已经对 shared dict 做了具体的介绍，这里先简单回顾下它的使用方法：

```
$ resty --shdict='dogs 1m' -e 'local dict = ngx.shared.dogs
                               dict:set("Tom", 56)
                               print(dict:get("Tom"))'
```
你需要事先在 Nginx 的配置文件中，声明一个内存区 dogs，然后在 Lua 代码中才可以使用。如果你在使用的过程中，发现给 dogs 分配的空间不够用，那么是需要先修改 Nginx 配置文件，然后重新加载 Nginx 才能生效的。因为我们并不能在运行时进行扩容和缩容。下面，我们重点聊下，在共享字典缓存中，和性能相关的几个问题。

### 缓存数据的序列化
第一个问题，缓存数据的序列化。由于共享字典中只能缓存字符串对象，所以，如果你想要缓存数组，就少不了要在 set 的时候要做一次序列化，在 get 的时候做一次反序列化：
```
resty --shdict='dogs 1m' -e 'local dict = ngx.shared.dogs
                        dict:set("Tom", require("cjson").encode({a=111}))
                        print(require("cjson").decode(dict:get("Tom")).a)'
```
不过，这类序列化和反序列化操作是非常消耗 CPU 资源的。如果每个请求都有那么几次这种操作，那么，在火焰图上你就能很明显地看到它们的消耗。所以，如何在共享字典里避免这种消耗呢？其实这里并没有什么好的办法，要么在业务层面避免把数组放到共享字典里面；要么自己去手工拼接字符串为 JSON 格式，当然，这也会带来字符串拼接的性能消耗，以及可能会隐藏更多的 bug 在其中。大部分的序列化都是可以在业务层面进行拆解的。你可以把数组的内容打散，分别用字符串的形式存储在共享字典中。如果还不行的话，那么也可以把数组缓存在 lru 中，用内存空间来换取程序的便捷性和性能。此外，缓存中的 key 也应该尽量选择短和有意义的，这样不仅可以节省空间，也方便后续的调试。

### stale 数据
共享字典中还有一个 get_stale 的读取数据的方法，相比 get 方法，多了一个过期数据的返回值：

```
resty --shdict='dogs 1m' -e 'local dict = ngx.shared.dogs
                            dict:set("Tom", 56, 0.01)
                            ngx.sleep(0.02)
                             local val, flags, stale = dict:get_stale("Tom")
                            print(val)'
```
在上面的这个示例中，数据只在共享字典中缓存了 0.01 秒，在 set 后的 0.02 秒后，数据就已经超时了。这时候，通过 get 接口就不会获取到数据了，但通过 get_stale 还可能获取到过期的数据。这里我之所以用“可能”两个字，是因为过期数据所占用的空间，是有一定几率被回收，再给其他数据使用的，这也就是 LRU 算法。看到这里，你可能会有疑惑吗：获取已经过期的数据有什么用呢？不要忘记了，我们在 shared dict 中存放的是缓存数据，即使缓存数据过期了，也并不意味着源数据就一定有更新。举个例子，数据源存储在 MySQL 中，我们从 MySQL 中获取到数据后，在 shared dict 中设置了 5 秒超时，那么，当这个数据过期后，我们就会有两个选择：


当这个数据不存在时，重新去 MySQL 中再查询一次，把结果放到缓存中；判断 MySQL 的数据是否发生了变化，如果没有变化，就把缓存中过期的数据读取出来，修改它的过期时间，让它继续生效。

很明显，后者是更优化的方案，这样可以尽可能少地去和 MySQL 交互，让终端的请求都从最快的缓存中获取数据。这时候，如何判断数据源中的数据是否发生了变化，就成为了我们需要考虑和解决的问题。接下来，让我们以 lru 缓存为例，看看一个实际的项目是如何来解决这个问题的。

### lru 缓存
lru 缓存的接口只有 5 个：new、set、get、delete 和 flush_all。和上面问题相关的就只有 get 接口，让我们先来了解下这个接口是如何使用的：

```
resty -e 'local lrucache = require "resty.lrucache"
local cache, err = lrucache.new(200)
cache:set("dog", 32, 0.01)
ngx.sleep(0.02)
local data, stale_data = cache:get("dog")
print(stale_data)'
```
你可以看到，在 lru 缓存中， get 接口的第二个返回值直接就是 stale_data，而不是像 shared dict 那样分为了 get 和 get_stale 两个不同的 API。这样的接口封装，对于使用过期数据来说显然更加友好。在实际的项目中，我们一般推荐使用版本号来区分不同的数据，这样，在数据发声变化后，它的版本号也就跟着变了。比如，在 etcd 中的 modifiedIndex ，就可以拿来当作版本号，来标记数据是否发生了变化。有了版本号的概念后，我们就可以对 lru 缓存做一个简单的二次封装，比如来看下面的伪码，摘自https://github.com/iresty/apisix/blob/master/lua/apisix/core/lrucache.lua ：

```
local function (key, version, create_obj_fun, ...)
    local obj, stale_obj = lru_obj:get(key)
    -- 如果数据没有过期，并且版本没有变化，就直接返回缓存数据
    if obj and obj._cache_ver == version then
        return obj
    end

    -- 如果数据已经过期，但还能获取到，并且版本没有变化，就直接返回缓存中的过期数据
    if stale_obj and stale_obj._cache_ver == version then
        lru_obj:set(key, obj, item_ttl)
        return stale_obj
    end

    -- 如果找不到过期数据，或者版本号有变化，就从数据源获取数据
    local obj, err = create_obj_fun(...)
    obj._cache_ver = version
    lru_obj:set(key, obj, item_ttl)
    return obj, err
end
```
从这段代码中你可以看到，我们通过引入版本号的概念，在版本号没有变化的情况下，充分利用了过期数据来减少对数据源的压力，达到了性能的最优。除此之外，在上面的方案中，其实还有一个潜在的很大优化点，那就是我们把 key 和版本号做了分离，把版本号作为 value 的一个属性。我们知道，更常规的做法是把版本号写入 key 中。比如 key 的值是 key_1234，这种做法非常普遍，但在 OpenResty 的环境下，这样其实是存在浪费的。为什么这么说呢？举个例子你就明白了。假如版本号每分钟变化一次，那么key_1234 过一分钟就变为了 key_1235，一个小时就会重新生成 60 个不同的 key，以及 60 个 value。这也就意味着， Lua GC 需要回收 59 个键值对背后的 Lua 对象。如果你的更新更加频繁，那么对象的新建和 GC 显然会消耗更多的资源。当然，这些消耗也可以很简单地避免，那就是把版本号从 key 挪到 value 中。这样，一个 key 不管更新地多么频繁，也只有固定的两个 Lua 对象。可以看出，这样的优化技巧非常巧妙，不过，简单巧妙的技巧背后，其实需要你对 OpenResty 的 API 以及缓存的机制都有很深入的了解才可以。

### 写在最后
诚然，OpenResty 的文档比较详细，但如何和业务组合以产生最大的优化效果，就需要你自己来来体会和领悟了。很多时候，文档中只有一两句的地方，比如 stale data 这样的，却会产生巨大的性能差异。

## 40 | 缓存与风暴并存，谁说缓存风暴不可避免？
在前面缓存的那节课中，我为你介绍了，共享字典和 lru 缓存在高性能方面的一些优化技巧。其实，我们还遗留了一个非常重要的问题，也值得我们今天用单独的一节课来介绍，那就是“缓存风暴”。


### 什么是缓存风暴？
什么是缓存风暴呢？让我们先来设想下面这么一个场景。数据源在 MySQL 数据库中，缓存的数据放在共享字典中，超时时间为 60 秒。在这 60 秒内的时间里，所有的请求都从缓存中获取数据，MySQL 没有任何的压力。但是，一旦到达 60 秒，也就是缓存数据失效的那一刻，如果正好有大量的并发请求进来，在缓存中没有查询到结果，就要触发查询数据源的函数，那么这些请求全部都将去查询 MySQL 数据库，直接造成数据库服务器卡顿，甚至卡死。这种现象就叫做“缓存风暴”，它也有一个对应的英文名字Dog-Pile。很明显，我们之前出现的缓存相关的代码，都没有做过对应的处理。比如下面这段代码，就是有缓存风暴隐患的伪代码：

```
local value = get_from_cache(key)
if not value then
    value = query_db(sql)
    set_to_cache(value, timeout ＝ 60)
end
return value
```
这段伪代码看上去逻辑都是正常的，你使用单元测试或者端对端测试，都不会触发缓存风暴。只有长时间的压力测试才会发现这个问题，每隔 60 秒的时间，数据库就会出现一次查询的峰值，非常有规律。不过，如果你这里的缓存失效时间设置得比较长，那么缓存风暴问题被发现的几率就会降低。

### 如何避免缓存风暴？
现在明白了什么是缓存风暴，我们的下一步就是要搞清楚如何避免它了。下面，让我们分为几个不同的情况来讨论一下。

#### 主动更新缓存
在上面的伪代码中，缓存是被动更新的。只有在终端请求发现缓存失效时，它才会去数据库查询新的数据。那么，如果我们把缓存的更新，从被动改为主动，也就可以直接绕开缓存风暴的问题了。在 OpenResty 中，我们可以这样来实现。首先，使用 ngx.timer.every 来创建一个定时任务，每分钟运行一次，去 MySQL 数据库中获取最新的数据，并放入共享字典中:

```
local function query_db(premature, sql)
    local value = query_db(sql)
    set_to_cache(value, timeout ＝ 60)
end

local ok, err = ngx.timer.every(60, query_db, sql)
```
然后，在终端请求的代码逻辑中，去掉查询 MySQL 的部分，只保留获取共享字典缓存的代码：

```
local value = get_from_cache(key)
return value
```
通过这样两段伪码的操作，缓存风暴的问题就被绕过去了。但这种方式也并非完美，因为这样的每一个缓存都要对应一个周期性的任务（OpenResty 中 timer 是有上限的，不能太多）；而且缓存过期时间和计划任务的周期时间还要对应好，如果这中间出现了什么纰漏，终端就可能一直获取到的都是空数据。所以，在实际的项目中，我们一般都是使用加锁的方式来解决缓存风暴问题。接下来，我将为你讲解几种不同的加锁方式，你可以根据需要自行选择。

#### lua-resty-lock
我知道，一提到加锁，很多人可能会眉头一皱，觉得这是一个比较重的操作。而且，如果发生死锁了该怎么办呢？这显然还要处理不少异常情况。但是，使用 OpenResty 中的 lua-resty-lock 这个库来加锁，这样的担心就大可不必了。lua-resty-lock 是 OpenResty 自带的 resty 库，它底层是基于共享字典，提供非阻塞的 lock API。我们先来看一个简单的示例：

```
resty --shdict='locks 1m' -e 'local resty_lock = require "resty.lock"
                            local lock, err = resty_lock:new("locks")
                            local elapsed, err = lock:lock("my_key")
                            -- query db and update cache
                            local ok, err = lock:unlock()
                            ngx.say("unlock: ", ok)'
```
因为 lua-resty-lock 是基于共享字典来实现的，所以我们需要事先声明 shdict 的名字和大小；然后，再使用 new 方法来新建 lock 对象。你可以看到，这段代码中，我们只传了第一个参数 shdict 的名字。其实， new 方法还有第二个参数，可以用来指定锁的过期时间、等待锁的超时时间等多个参数。不过这里，我们使用的是默认值，它们就是用来避免死锁等各种异常问题的。接着，我们就可以调用 lock 方法尝试获取锁。如果成功获取到锁的话，那就可以保证只有一个请求去数据源更新数据；而如果因为锁已经被抢占、超时等导致加锁失败，那就需要从陈旧的缓存中获取数据，返回给终端。这个过程是不是听起来很熟悉？没错，这里就正好用到了我们上节课介绍过的的 get_stale API：

```
local elapsed, err = lock:lock("my_key")
# elapsed 为 nil 表示加锁失败，err的返回值是 timeout、 locked 中的一个
if not elapsed and err then 
    dict:get_stale("my_key")
end
```
如果 lock 成功，那么就可以安全地去查询数据库，并把结果更新到缓存中。最后，我们再调用 unlock 接口，把锁释放掉就可以了。结合 lua-resty-lock 和 get_stale，我们就完美地解决了缓存风暴的问题。在 lua-resty-lock 的文档中，给出了非常完整的处理代码，推荐你可以点击[链接](https://github.com/openresty/lua-resty-lock#for-cache-locks)查看。不过，每当遇到一些有趣的实现，我们总是希望能够看看它的源码是如何实现的，这也是开源的好处之一。这里，我们再深入一步，看看 lock 这个接口是如何加锁的，下面便是它的源码：

```
local ok, err = dict:add(key, true, exptime)
if ok then
    cdata.key_id = ref_obj(key)
    self.key = key
    return 0
end
```
在共享字典章节中，我曾经提到过，shared dict 的所有 API 都是原子操作，不用担心出现竞争，所以用 shared dict 来标记锁的状态是个不错的主意。这里 lock 接口的实现，便使用了 dict:add 接口来尝试设置 key。如果 key 在共享内存中不存在，add 接口就会返回成功，表示加锁成功；其他并发的请求走到 dict:add 这一行的代码逻辑时，就会返回失败，然后根据返回的 err 信息，选择是直接返回，还是多次重试。



### lua-resty-shcache
不过，在上面 lua-resty-lock 的实现中，你需要自己来处理加锁、解锁、获取过期数据、重试、异常处理等各种问题，还是相当繁琐的。所以，这里我再给你介绍一个简单的封装：lua-resty-shcache。lua-resty-shcache是 CloudFlare 开源的一个 lua-resty 库，它在共享字典和外部存储之上，做了一层封装；并且额外提供了序列化和反序列化的接口，让你不用去关心上述的各种细节:

```
local shcache = require("shcache")

local my_cache_table = shcache:new(
        ngx.shared.cache_dict
        { external_lookup = lookup,
          encode = cmsgpack.pack,
          decode = cmsgpack.decode,
        },
        { positive_ttl = 10,           -- cache good data for 10s
          negative_ttl = 3,            -- cache failed lookup for 3s
          name = 'my_cache',     -- "named" cache, useful for debug / report
        }
    )

    local my_table, from_cache = my_cache_table:load(key)
```
这段示例代码摘自官方的示例，不过，我已经把细节都隐藏了，方便你更好地把握重点。事实上，这个缓存封装的库并非是我们的最佳选择，但比较适合初学者去学习，所以我首先介绍的是它。在下一节课中，我会给你介绍其他的几个更好、更常用的封装，方便我们选择最合适的来使用。



### Nginx 配置指令
另外，即使你没有使用 OpenResty 的 lua-resty 库，你也可以用 Nginx 的配置指令，来实现加锁和获取过期数据——即proxy_cache_lock 和 proxy_cache_use_stale。不过，这里我并不推荐使用 Nginx 指令这种方式，它显然不够灵活，性能也比不上 Lua 代码。


### 写在最后
这节课，我主要为你介绍了缓存风暴和相应的几种应对方式。不得不说，缓存风暴，和之前我们反复提到的 race 问题一样，通过 code review 和测试都很难被发现，最好的方法还是提升我们本身的编码水平，或者使用封装好的类库来解决这类问题。



## 41 | lua-resty-* 封装，让你远离多级缓存之痛

前面两节课中，我们已经学习了 OpenResty 中的缓存，以及容易出错的缓存风暴问题，这些都是属于偏基础的一些知识。在实际的项目开发中，开发者自然更希望有一个已经把各种细节处理好并隐藏起来的开箱即用的库，可以拿来直接开发业务代码。这其实就是分工的一个好处，基础组件的开发者，重心在于架构灵活、性能极致、代码稳定，并不需要关心上层业务逻辑；而应用层的工程师，更关心的是业务实现和快速迭代，并不希望被底层的各种技术细节分心。这中间的鸿沟，就需要有一些封装库来填平了。OpenResty 中的缓存，也面临一样的问题。共享字典和 lru 缓存足够稳定和高效，但需要处理太多的细节。如果没有一些好用的封装，那么到达应用开发工程师的“最后一公里”，就会变得比较痛苦。这个时候，就要体现社区的重要性了。一个活跃的社区，会主动去发现鸿沟，并迅速地填平。



### lua-resty-memcached-shdict
让我们回到缓存的封装上来。lua-resty-memcached-shdict 是 OpenResty 官方的一个项目，它使用 shared dict 为 memcached 做了一层封装，处理了缓存风暴和过期数据等细节。如果你的缓存数据正好存储在后端的 memcached 中，那么你可以尝试使用这个库。它虽然是 OpenResty 官方开发的库，但默认并没有打进 OpenResty 的包中。如果你想在本地测试，需要先把它的[源码](https://github.com/openresty/lua-resty-memcached-shdict)下载到本地 OpenResty 的查找路径下。这个封装库，其实和我们上节课中提到的解决方案是一样的。它使用 lua-resty-lock 来做到互斥，在缓存失效的情况下，只有一个请求去 memcached 中获取数据，避免缓存风暴。如果没有获取到最新数据，则使用 stale 数据返回给终端。不过，这个 lua-resty 库虽说是 OpenResty 官方的项目，但也并不完美。首先，它没有测试案例覆盖，这就意味着代码质量无法得到持续的保证；其次，它暴露的接口参数过多，有 11 个必填参数和 7 个选填参数：

```
local memc_fetch, memc_store =
    shdict_memc.gen_memc_methods{
        tag = "my memcached server tag",
        debug_logger = dlog,
        warn_logger = warn,
        error_logger = error_log,

        locks_shdict_name = "some_lua_shared_dict_name",

        shdict_set = meta_shdict_set,  
        shdict_get = meta_shdict_get,  

        disable_shdict = false,  -- optional, default false

        memc_host = "127.0.0.1",
        memc_port = 11211,
        memc_timeout = 200,  -- in ms
        memc_conn_pool_size = 5,
        memc_fetch_retries = 2,  -- optional, default 1
        memc_fetch_retry_delay = 100, -- in ms, optional, default to 100 (ms)

        memc_conn_max_idle_time = 10 * 1000,  -- in ms, for in-pool connections,optional, default to nil

        memc_store_retries = 2,  -- optional, default to 1
        memc_store_retry_delay = 100,  -- in ms, optional, default to 100 (ms)

        store_ttl = 1,  -- in seconds, optional, default to 0 (i.e., never expires)
    }
```
这其中暴露的绝大部分参数，其实可以通过“新建一个 memcached 的处理函数”的方式来简化。当前这种把所有参数一股脑儿地丢给用户来填写的封装方式并不友好，所以，我也很欢迎有兴趣的开发者，贡献 PR 来做这方面的优化。另外，在这个封装库的文档中，其实也提到了进一步的优化方向：

一是使用 lua-resty-lrucache ，来增加 worker 层的缓存，而不仅仅是 server 级别的 shared dict 缓存；二是使用 ngx.timer ，来做异步的缓存更新操作。

第一个方向其实是很不错的建议，因为 worker 内的缓存性能自然会更好；而第二个建议，就需要你根据自己的实际场景来考量了。不过，一般我并不推荐使用，这不仅是因为 timer 的数量是有限制的，而且如果这里的更新逻辑出错，就再也不会去更新缓存了，影响面比较大。


### lua-resty-mlcache
接下来，我们再来介绍下，在 OpenResty 中被普遍使用的缓存封装： lua-resty-mlcache。它使用 shared dict 和 lua-resty-lrucache ，实现了多层缓存机制。我们下面就通过两段代码示例，来看看这个库如何使用：

```
local mlcache = require "resty.mlcache"

local cache, err = mlcache.new("cache_name", "cache_dict", {
    lru_size = 500,    -- size of the L1 (Lua VM) cache
    ttl = 3600,   -- 1h ttl for hits
    neg_ttl  = 30,     -- 30s ttl for misses
})
if not cache then
    error("failed to create mlcache: " .. err)
end
```
先来看第一段代码。这段代码的开头引入了 mlcache 库，并设置了初始化的参数。我们一般会把这段代码放到 init 阶段，只需要做一次就可以了。除了缓冲名和字典名这两个必填的参数外，第三个参数是一个字典，里面 12 个选项都是选填的，不填的话就使用默认值。这种方式显然就比 lua-resty-memcached-shdict 要优雅很多。其实，我们自己来设计接口的话，也最好采用 mlcache 这样的做法——让接口尽可能地简单，同时还保留足够的灵活性。下面再来看第二段代码，这是请求处理时的逻辑代码：


```
local function fetch_user(id)
    return db:query_user(id)
end

local id = 123
local user , err = cache:get(id , nil , fetch_user , id)
if err then
    ngx.log(ngx.ERR , "failed to fetch user: ", err)
    return
end

if user then
    print(user.id) -- 123
end
```
你可以看到，这里已经把多层缓存都给隐藏了，你只需要使用 mlcache 的对象去获取缓存，并同时设置好缓存失效后的回调函数就可以了。这背后复杂的逻辑，就可以被完全地隐藏了。说到这里，你可能好奇，这个库内部究竟是怎么实现的呢？接下来，再让我们来看下这个库的架构和实现。下面这张图，来自 mlcache 的作者 thibault 在 2018 年 OpenResty 大会上演讲的幻灯片：

![img](https://static001.geekbang.org/resource/image/19/97/19a701636a95e931e6a9a8d0127e4f97.png?wh=1709*1121)



从图中你可以看到，mlcache 把数据分为了三层，即 L1、L2 和 L3。L1 缓存就是 lua-resty-lrucache。每一个 worker 中都有自己独立的一份，有 N 个 worker，就会有 N 份数据，自然也就存在数据冗余。由于在单 worker 内操作 lrucache 不会触发锁，所以它的性能更高，适合作为第一级缓存。L2 缓存是 shared dict。所有的 worker 共用一份缓存数据，在 L1 缓存没有命中的情况下，就会来查询 L2 缓存。ngx.shared.DICT 提供的 API，使用了自旋锁来保证操作的原子性，所以这里我们并不用担心竞争的问题；L3 则是在 L2 缓存也没有命中的情况下，需要执行回调函数去外部数据库等数据源查询后，再缓存到 L2 中。在这里，为了避免缓存风暴，它会使用 lua-resty-lock ，来保证只有一个 worker 去数据源获取数据。


整体而言，从请求的角度来看，首先会去查询 worker 内的 L1 缓存，如果 L1 命中就直接返回。如果 L1 没有命中或者缓存失效，就会去查询 worker 间的 L2 缓存。如果 L2 命中就返回，并把结果缓存到 L1 中。如果 L2 也没有命中或者缓存失效，就会调用回调函数，从数据源中查到数据，并写入到 L2 缓存中，这也就是 L3 数据层的功能。


从这个过程你也可以看出，缓存的更新是由终端请求来被动触发的。即使某个请求获取缓存失败了，后续的请求依然可以触发更新的逻辑，以便最大程度地保证缓存的安全性。不过，虽然 mlcache 已经实现得比较完美了，但在现实使用中，其实还有一个痛点——数据的序列化和反序列化。这个其实并不是 mlcache 的问题，而是我们之前反复提到的  lrucache 和 shared dict 之间的差异造成的。在 lrucache 中，我们可以存储 Lua 的各种数据类型，包括 table；但 shared dict 中，我们只能存储字符串。L1 也就是 lrucache 缓存，是用户真正接触到的那一层数据，我们自然希望在其中可以缓存各种数据，包括字符串、table、cdata 等。可是，问题在于， L2 中只能存储字符串。那么，当数据从 L2 提升到 L1 的时候，我们就需要做一层转换，也就是从字符串转成我们可以直接给用户的数据类型。还好，mlcache 已经考虑到了这种情况，并在 new 和 get 接口中，提供了可选的函数 l1_serializer，专门用于处理 L2 提升到 L1 时对数据的处理。我们可以来看下面的示例代码，它是我从测试案例集中摘选出来的：


```
local mlcache = require "resty.mlcache"

local cache, err = mlcache.new("my_mlcache", "cache_shm", {
l1_serializer = function(i)
    return i + 2
end,
})

local function callback()
    return 123456
end

local data = assert(cache:get("number", nil, callback))
assert(data == 123458)
```
简单解释一下。在这个案例中，回调函数返回数字 123456；而在 new 中，我们设置的 l1_serializer 函数会在设置 L1 缓存前，把传入的数字加 2，也就是变成 123458。通过这样的序列化函数，数据在 L1 和 L2 之间转换的时候，就可以更加灵活了。可以说，mlcache 是一个功能很强大的缓存库，而且[文档](https://github.com/thibaultcha/lua-resty-mlcache)也写得非常详尽。今天这节课我只提到了核心的一些原理，更多的使用方法，建议你一定要自己花时间去探索和实践。

### 写在最后
有了多层缓存，服务端的性能才能得到最大限度的保证，而这中间又隐藏了很多的细节。这时候，有一个稳定、高效的封装库，我们就能省心很多。我也希望通过今天介绍的这两个封装库，帮助你更好地理解缓存。


## 42 | 如何应对突发流量：漏桶和令牌桶的概念

在前面几节课中，我们学习了代码的优化和缓存的设计，这两者都和应用的整体性能息息相关，自然值得我们重视。不过，在真实的业务场景下，我们还需要考虑到突发流量对性能的影响。这里的突发流量，可能是正常的，比如突发新闻、促销活动等带来的流量；也可能是不正常的流量，比如 DDoS 攻击等。OpenResty 现在主要被用于作为接入层的 Web 应用，比如 WAF 和 API 网关，这些都要应对刚刚提到的正常和不正常的突发流量。毕竟，如果不能处理好突发流量，后端的服务就很容易被打垮，业务也就无法正常响应了。所以今天，我们就专门来看下，应对突发流量的方法。

### 流量控制
流量控制是 WAF 和 API 网关都必备的功能之一，它通过一些算法对入口流量进行疏导和控制，来保证上游的服务能够正常运行，从而让系统整体保持健康。因为后端的处理能力是有限的，我们需要从成本、用户体验、系统稳定性等多个方面来综合考虑。不管使用哪一种算法，都不可避免地会造成正常用户请求变慢甚至被拒绝，牺牲部分的用户体验。所以，流量控制是需要在业务稳定和用户体验之间做平衡的。

其实，在现实的生活中，也经常会有流量控制的情况。比如春运等高峰期的地铁站、火车站、机场等交通枢纽，这些交通工具的处理能力是有上限的，那么，为了保证交通安全运转，就需要乘客排队等候、分批次进站。这自然会影响乘客的体验，但从整体上看，却保证了系统的高效和安全运行。如果没有排队和分批次，而是让大家一窝蜂地进站，最后的结局估计就是整个系统瘫痪了。回到技术上来说，举个例子，比如我们假定一个上游服务的设计上限是每分钟处理 1 万条请求。在高峰期的时候，如果入口处没有限流的控制，每分钟堆积的任务达到了 2 万条，那么这个上游服务的处理性能就会下降，可能只有每分钟 5000 条的处理速度，并且持续恶化，最终或许会导致服务不可用。这显然不是我们希望看到的结果。应对这种突发的流量，我们常用的流量控制算法，便是漏桶和令牌桶。

### 漏桶算法
让我们先来看下漏桶算法，它的目的是让请求的速率保持恒定，把突发的流量变得平滑。不过，它是怎么做到的呢？我们来看下面这张概念抽象图，来自维基百科中对于漏桶算法的介绍：

![img](https://static001.geekbang.org/resource/image/6e/a9/6e36e9d5fff0aa58d8a9b4d34671fba9.jpg?wh=634*688)

我们可以把客户端的流量想象成是从水管中流出来的水，水的流速不确定，忽快忽慢；而外层的流量处理模块，就是接水的桶子，并且这个水桶的底部有一个漏水用的洞眼。这其实也就是漏桶算法名字的由来，很明显，这种算法有下面几个好处。第一，不管流入水桶的是涓涓细流还是滔天洪水，都可以保证，水桶中流出来的水速是恒定的。这种稳定的流量对于上游服务是很友好的，这也是流量整形的意义。第二，水桶本身有一定容积，可以积累一定的水来等待流出水桶。这对于终端的请求来说，相当于是如果不能被立即处理，可以排队等待。第三，超过水桶容积的水，不会被水桶接纳，而是会直接流走。这里对应的是，终端的请求如果太多，超过了排队的长度，就直接返回给客户端失败信息。这时候的服务端已经处理不过来了，自然，请求连排队的必要也就没有了。

说了这么多的优点，那么，这个算法应该如何来实现呢？我们以 OpenResty 中自带的 [resty.limit.req](https://github.com/openresty/lua-resty-limit-traffic/blob/master/lib/resty/limit/req.lua#L73) 库为例来看，它就是按照漏桶算法实现的限速模块，下节课我还会介绍更多内容。今天我们先来简单了解下，下面是它关键的几行代码：

```
local elapsed = now - tonumber(rec.last)
excess = max(tonumber(rec.excess) - rate * abs(elapsed) / 1000 + 1000,0)
if excess > self.burst then
    return nil, "rejected"
end
-- return the delay in seconds, as well as excess
return excess / rate, excess / 1000
```
我来简单解释一下这几行代码。其中， elapsed 是当前请求和上一次请求之间的毫秒数，rate 则是我们设定的每秒的速率。因为rate的最小单位是 0.001 s/r，所以在上述实现的代码中，都需要乘以 1000  以便计算。excess 表示还在排队的请求数量，它为 0 表示水桶是空的，没有请求在排队，而burst 是指整个水桶的容积。如果 excess 已经大于 burst，也就意味着水桶已经满了，这时候再进来的流量就会被直接丢弃；如果 excess 大于 0 、小于 burst，就进入了排队来等待处理，这里最后返回的 excess / rate ，也就是要等待的时间。这样，在后端服务处理能力不变的情况下，我们就可以通过调节 burst 的大小，来控制突发流量的排队时长了。是直接告诉用户现在请求量太大，稍后再重试，还是让用户多等待一段时间，这就要看你的业务场景了。

### 令牌桶算法
令牌桶算法和漏桶算法的目的都是一样的，用来保证后端服务不被突发流量打垮，不过这两者的实现方式并不相同。在漏桶算法中，我们一般会使用终端 IP 作为 key ，来做限流限速的依据。这样，对于每一个终端用户而言，漏桶算法的出口速率就是固定的。不过，这就会存在一个问题：如果 A 用户的请求频率很高，而其他用户的请求频率很低，即使此时的整体服务压力并不大，但漏桶算法就会把 A 的部分请求变慢或者拒绝掉，虽然这时候服务其实是可以处理的。


这时候就有令牌桶的用武之地了。

漏桶算法关注的是流量的平滑，而令牌桶则可以允许突发流量进入后端服务。令牌桶的原理，是以一个固定的速度向水桶内放入令牌，只要桶没有满就一直往里面放。这样，终端过来的请求都需要先到令牌桶中获取到令牌，才可以被后端处理；如果桶里面没有令牌，那么请求就会被拒绝。不过，OpenResty 自带的限流限速的库中没有实现令牌桶，所以，这里我用又拍云开源的、基于令牌桶的限速模块 lua-resty-limit-rate  的[代码](https://github.com/upyun/lua-resty-limit-rate)为例，为你做一个简单的介绍：
```
local limit_rate = require "resty.limit.rate"

-- global 20r/s 6000r/5m
local lim_global = limit_rate.new("my_limit_rate_store", 100, 6000, 2)

-- single 2r/s 600r/5m
local lim_single = limit_rate.new("my_limit_rate_store", 500, 600, 1)

local t0, err = lim_global:take_available("__global__", 1)
local t1, err = lim_single:take_available(ngx.var.arg_userid, 1)

if t0 == 1 then
    return -- global bucket is not hungry
else
    if t1 == 1 then
        return -- single bucket is not hungry
    else
        return ngx.exit(503)
    end
end
```
在这段代码中，我们设置了两个令牌桶：一个是全局的令牌桶，一个是以 b ngx.var.arg_userid 为 key，按照用户来划分的令牌桶。这里用两个令牌桶做了一个组合，主要有这么一个好处：在全局令牌桶还有令牌的情况下，不用去判断用户的令牌桶，如果后端服务能够正常运行，就尽可能多地去服务用户的突发请求；在全局令牌桶没有令牌的情况下，不能无差别地拒绝请求，这时候就需要判断下单个用户的令牌桶，把突发请求比较多的用户请求给拒绝掉。这样一来，就可以保证其他用户的请求不会受到影响。


显然，令牌桶和漏桶相比，更具有弹性，允许出现突发流量传递到后端服务的情况。当然，它们都各有利弊，你可以根据自己的情况来选择使用。

### Nginx 的限速模块
说完这两个算法，我们最后再来看下，在熟悉的 Nginx 中是如何来实现限流限速的。在 Nginx 中，[limit_req 模块](http://nginx.org/en/docs/http/ngx_http_limit_req_module.html)是最常用的限速模块，下面是一个简单的配置：


```
limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

server {
    location /search/ {
        limit_req zone=one burst=5;
    }
}
```
这段代码是把终端的 IP 地址作为 key，申请了一块名为 one 的 10M 的内存空间地址，并把速率限制为每秒 1 个请求。在 server 的 location 中，还引用了 one 这个限速规则，并把 brust 设置为 5。这就表示在超过速率 1r/s 的情况下，同时允许有 5 个请求排队等待被处理，给出了一定的缓存区。要注意，如果没有设置 brust ，超过速率的请求是会被直接拒绝的。Nginx 的这个模块是基于漏桶来实现的，所以和我们上面介绍过的 OpenResty 中的 resty.limit.req ，本质都是一样的。


### 写在最后
事实上，Nginx 中设置限流限速的最大问题是，无法动态地修改。毕竟，修改完配置文件后，还需要重启才能生效，这在快速变化的环境下显然是无法接受的。下节课，我们就来看下，在 OpenResty 中如何动态地实现限流限速。



## 43 | 灵活实现动态限流限速，其实没有那么难

前面的课程中，我为你介绍了漏桶和令牌桶算法，它们都是应对突发流量的常用手段。同时，我们也学习了如何通过 Nginx 配置文件的方式，来实现对请求的限流限速。不过很显然，使用 Nginx 配置文件的方式，仅仅停留在可用的层面，距离好用还是有不小的距离的。第一个问题便是，限速的 key 被限制在 Nginx 的变量范围内，不能灵活地设置。比如，根据不同的省份和不同的客户端渠道，来设置不同的限速阈值，这种常见的需求用 Nginx 就没有办法实现。另外一个更大的问题是，不能动态地调整速率，每次修改都需要重载 Nginx 服务，这一点我们在上节课的最后也提到过。这样一来，根据不同的时间段限速这种需求，就只能通过外置的脚本来蹩脚地实现了。要知道，技术是为业务服务的，同时，业务也在驱动着技术的进步。在 Nginx 诞生的时代，并没有什么动态调整配置的需求，更多的是反向代理、负载均衡、低内存占用等类似的需求，在驱动着 Nginx 的成长。在技术的架构和实现上，并没有人能够预料到，在移动互联网、IoT、微服务等场景下，对于动态和精细控制的需求会大量爆发。而 OpenResty 使用 Lua 脚本的方式，恰好能够弥补 Nginx 在这方面的缺失，形成了有效的互补。这也是 OpenResty 被广泛地用于替换 Nginx 的根源所在。在后面几节课中，我会为你继续介绍更多 OpenResty 中动态的场景和示例。今天，就让我们先来看下，如何使用 OpenResty 来实现动态限流和限速。在 OpenResty 中，我们推荐使用 [lua-resty-limit-traffic](https://github.com/openresty/lua-resty-limit-traffic) 来做流量的限制。它里面包含了 limit-req（限制请求速率）、 limit-count（限制请求数） 和 limit-conn （限制并发连接数）这三种不同的限制方式；并且提供了limit.traffic ，可以把这三种方式进行聚合使用。


### 限制请求速率
让我们先来看下 limit-req，它使用的是漏桶算法来限制请求的速率。在上一节中，我们已经简要介绍了这个 resty 库中漏桶算法的关键实现代码，现在我们就来学习如何使用这个库。我们来看下面这段示例代码：

```
resty --shdict='my_limit_req_store 100m' -e 'local limit_req = require "resty.limit.req"
local lim, err = limit_req.new("my_limit_req_store", 200, 100)
local delay, err = lim:incoming("key", true)
if not delay then
    if err == "rejected" then
        return ngx.exit(503)
    end
    return ngx.exit(500)
end

 if delay >= 0.001 then
    ngx.sleep(delay)
end'
```
我们知道，lua-resty-limit-traffic 是使用共享字典来对 key 进行保存和计数的，所以在使用 limit-req 前，我们需要先声明 my_limit_req_store 这个 100m 的空间。这一点对于 limit-conn 和 limit-count 也是类似的，它们都需要自己单独的共享字典空间，以便区分开。


```
limit_req.new("my_limit_req_store", 200, 100)

```
上面这行代码，便是其中最关键的一行代码。它的含义，是使用名为 my_limit_req_store 的共享字典来存放统计数据，并把每秒的速率设置为 200。这样，如果超过 200 但小于 300（这个值是 200 + 100 计算得到的） 的话，就需要排队等候；如果超过 300 的话，就会直接拒绝。在设置完成后，我们就要对终端的请求进行处理了，lim: incoming("key", true) 就是来做这件事情的。incoming这个函数有两个参数，我们需要详细解读一下。第一个参数，是用户指定的限速的 key。在上面的示例中它是一个字符串常量，这就意味着要对所有终端都统一限速。如果要实现根据不同省份和渠道来限速，其实也很简单，把这两个信息都作为 key 即可，下面是实现这一需求的伪代码：

```
local  province = get_ province(ngx.var.binary_remote_addr)
local channel = ngx.req.get_headers()["channel"]
local key = province .. channel
lim:incoming(key, true)
```
当然，你也可以举一反三，自定义 key 的含义以及调用 incoming 的条件，这样你就能收到非常灵活的限流限速效果了。我们再来看incoming 函数的第二个参数，它是一个布尔值，默认是 false，意味着这个请求不会被记录到共享字典中做统计，这只是一次 演习。如果设置为 true，就会产生实际的效果了。因此，在大多数情况下，你都需要显式地把它设置为 true。你可能会纳闷儿，为什么会有这个参数的存在呢？我们不妨考虑一下这样的一个场景，你设置了两个不同的 limit-req 实例，针对不同的 key，一个 key 是主机名，另外一个 key 是客户端的 IP 地址。那么，当一个终端请求被处理的时候，会按照先后顺序调用这两个实例的 incoming 方法，就像下面这段伪码表示的一样：

```
local limiter_one, err = limit_req.new("my_limit_req_store", 200, 100)
local limiter_two, err = limit_req.new("my_limit_req_store", 20, 10)

limiter_one :incoming(ngx.var.host, true)
limiter_two:incoming(ngx.var.binary_remote_addr, true)
```
如果用户的请求通过了 limiter_one 的阈值检测，但被 limiter_two 的检测拒绝，那么 limiter_one:incoming 这次函数调用就应该被认为是一次 演习，不应该真的去计数。这样一来，上述的代码逻辑就不够严谨了。我们需要事先对所有的 limiter 做一次演习，如果有 limiter 的阈值被触发，可以 rejected 终端请求，就可以直接返回：


```
for i = 1, n do
    local lim = limiters[i]
    local delay, err = lim:incoming(keys[i], i == n)
    if not delay then
        return nil, err
    end
end
```
这其实就是 incoming 函数第二个参数的意义所在。刚刚这段代码就是 limit.traffic 模块最核心的一段代码，专门用作多个限流器的组合所用。

### 限制请求数
再来看下 limit.count 这个限制请求数的库，它的效果和 GitHub API 的 Rate Limiting 一样，可以限制固定时间窗口内有多少次用户请求。老规矩，我们先来看一段示例代码：

```
local limit_count = require "resty.limit.count"

local lim, err = limit_count.new("my_limit_count_store", 5000, 3600)

local key = ngx.req.get_headers()["Authorization"]
local delay, remaining = lim:incoming(key, true)
```
你可以看到，limit.count 和 limit.req 的使用方法是类似的，我们先在 Nginx.conf 中定义一个字典：
```
lua_shared_dict my_limit_count_store 100m;
```
然后 new 一个 limiter 对象，最后用 incoming 函数来判断和处理。不过，不同的是，limit-count 中的incoming 函数的第二个返回值，代表着还剩余的调用次数，我们可以据此在响应头中增加字段，给终端更好的提示：

```
ngx.header["X-RateLimit-Limit"] = "5000"
ngx.header["X-RateLimit-Remaining"] = remaining
```
### 限制并发连接数
第三种方式，也就是limit.conn ，是用来限制并发连接数的库。它和前面提到的两个库有所不同，有一个特别的 leaving API，这里我来简单介绍下。前面所讲的限制请求速率和限制请求数，都是可以直接在 access 这一个阶段内完成的。而限制并发连接数则不同，它不仅需要在 access 阶段判断是否超过阈值，而且需要在 log 阶段调用 leaving 接口：

```
log_by_lua_block {
    local latency = tonumber(ngx.var.request_time) - ctx.limit_conn_delay
    local key = ctx.limit_conn_key

    local conn, err = lim:leaving(key, latency)
}
```
不过，这个接口的核心代码其实也很简单，也就是下面这一行代码，实际上就是把连接数减一的操作。如果你没有在 log 阶段做这个清理的动作，那么连接数就会一直上涨，很快就会达到并发的阈值。

```
local conn, err = dict:incr(key, -1)
```
### 限速器的组合
到这里，这三种方式我们就分别介绍完了。最后，我们再来看看，怎么把 limit.rate、limit.conn 和 limit.count 组合起来使用。这就需要用到 limit.traffic 中的 combine 函数了：


```
local lim1, err = limit_req.new("my_req_store", 300, 200)
local lim2, err = limit_req.new("my_req_store", 200, 100)
local lim3, err = limit_conn.new("my_conn_store", 1000, 1000, 0.5)

local limiters = {lim1, lim2, lim3}
local host = ngx.var.host
local client = ngx.var.binary_remote_addr
local keys = {host, client, client}

local delay, err = limit_traffic.combine(limiters, keys, states)
```
有了刚刚的知识基础，这段代码你应该很容易看明白。combine 函数的核心代码，在我们上面分析 limit.rate 的时候已经提到了一部分，它主要是借助了演习功能和 uncommit 函数来实现。这样组合以后，你就可以为多个限流器设置不同的阈值和 key，实现更复杂的业务需求了。

### 写在最后
limit.traffic 不仅支持今天所讲的这三种限速器，实际上，只要某个限速器有 incoming 和 uncommit 接口，都可以被 limit.traffic 的 combine 函数管理。


## 44 | OpenResty 的杀手锏：动态

到目前为止，和 OpenResty 性能相关的内容，我就差不多快要介绍完了。我相信，掌握并灵活运用好这些优化技巧，一定可以让你的代码性能提升一个数量级。今天，在性能优化的最后一个部分，我来讲一讲 OpenResty 中被普遍低估的一种能力：动态。让我们先来看下什么是动态，以及它和性能之间有什么样的关系。这里的动态，指的是程序可以在运行时、在不重新加载的情况下，去修改参数、配置，乃至修改自身的代码。具体到 Nginx 和 OpenResty 领域，你去修改上游、SSL 证书、限流限速阈值，而不用重启服务，就属于实现了动态。至于动态和性能之间的关系，很显然，如果这几类操作不能动态地完成，那么频繁的 reload Nginx 服务，自然就会带来性能的损耗。不过，我们知道，开源版本的 Nginx 并不支持动态特性，所以，你要对上游、SSL 证书做变更，就必须通过修改配置文件、重启服务的方式才能生效。而商业版本的 Nginx Plus 提供了部分动态的能力，你可以用 REST API 来完成更新，但这最多算是一个不够彻底的改良。但是，在 OpenResty 中，这些桎梏都是不存在的，动态可以说就是 OpenResty 的杀手锏。你可能纳闷儿，为什么基于 Nginx 的 OpenResty 却可以支持动态呢？原因也很简单，Nginx 的逻辑是通过 C 模块来完成的，而 OpenResty 是通过脚本语言 Lua 来完成的——脚本语言的一大优势，便是运行时可以去做动态地改变。


### 动态加载代码
下面我们就来看看，如何在 OpenResty 中动态地加载 Lua 代码：


```
resty -e 'local s = [[ngx.say("hello world")]]
local func, err = loadstring(s)
func()'
```
你没有看错，只要短短的两三行代码，就可以把一个字符串变为一个 Lua 函数，并运行起来。我们进一步仔细看下这几行代码，我来简单解读一下：首先，我们声明了一个字符串，它的内容是一段合法的 Lua 代码，把 hello world 打印出来；然后，使用 Lua 中的 loadstring 函数，把字符串对象转为函数对象func；最后，在函数名的后面加上括号，把 func 执行起来，打印出 hello world 来。

当然，在这段代码的基础之上，我们还可以扩展出更多好玩和实用的功能。接下来，我就带你一起来“尝尝鲜”。


### 功能一：FaaS
首先是函数即服务，这是近年来很热门的技术方向，我们看下在 OpenResty 中如何实现。在刚刚的代码中，字符串是一段 Lua 代码，我们还可以把它改成一个 Lua 函数：

```
local s = [[
 return function()
     ngx.say("hello world")
end
]]
```
我们讲过，函数在 Lua 中是一等公民，这段代码便是返回了一个匿名函数。在执行这个匿名函数时，我们使用 pcall 做了一层保护。pcall 会在保护模式下运行函数，并捕获其中的异常，如果正常就返回 true 和执行的结果，如果失败就返回 false 和错误信息，也就是下面这段代码：


```
local func1, err = loadstring(s)
local ret, func = pcall(func1)
```
自然，把上面的两部分结合起来，就会得到完整的、可运行的示例：

```
resty -e 'local s = [[
 return function()
    ngx.say("hello world")
end
]]
local  func1 = loadstring(s)
local ret, func = pcall(func1)
func()'
```
更深入一步，我们还可以把 s 这个包含函数的字符串，改成可以由用户指定的形式，并加上执行它的条件，这样其实就是 FaaS 的原型了。这里，我提供了一个完整的[实现](https://github.com/apache/apisix/blob/master/apisix/plugins/serverless.lua)，如果你对 FaaS 感兴趣，想要继续研究，推荐你通过这个链接深入学习。

### 功能二：边缘计算
OpenResty 的动态不仅可以用于 FaaS，让脚本语言的动态细化到函数级别，还可以在边缘计算上发挥动态的优势。得益于 Nginx 和 LuaJIT 良好的多平台支持特性，OpenResty 不仅能运行在 X86 架构下，对于 ARM 的支持也很完善。同时， OpenResty 支持七层和四层的代理，这样一来，常见的各种协议都可以被 OpenResty 解析和代理，这其中也包括了 IoT 中的几种协议。因为这些优势，我们便可以把 OpenResty 的触角，从 API 网关、WAF、web 服务器等服务端的领域，伸展到物联网设备、CDN 边缘节点、路由器等最靠近用户的边缘节点上去。这并非只是一种畅想，事实上，OpenResty 已经在上述领域中被大量使用了。以 CDN 的边缘节点为例，OpenResty 的最大使用者 CloudFlare 很早就借助 OpenResty 的动态特性，实现了对于 CDN 边缘节点的动态控制。

CloudFlare 的做法和上面动态加载代码的原理是类似的，大概可以分为下面几个步骤：首先，从键值数据库集群中获取到有变化的代码文件，获取的方式可以是后台 timer 轮询，也可以是用“发布 - 订阅”的模式来监听；然后，用更新的代码文件替换本地磁盘的旧文件，然后使用 loadstring 和 pcall的方式，来更新内存中加载的缓存；

这样，下一个被处理的终端请求，就会走更新后的代码逻辑。当然，实际的应用要比上面的步骤考虑更多的细节，比如版本的控制和回退、异常的处理、网络的中断、边缘节点的重启等，但整体的流程是不变的。如果把 CloudFlare 的这套做法，从 CDN 边缘节点挪移到其他边缘的场景下，那我们就可以把很多计算能力动态地赋予边缘节点的设备。这不仅可以充分利用边缘节点的计算能力，也可以让用户请求得到更快速的响应。因为边缘节点会把原始数据处理过后，再汇总给远端的服务器，这就大大减少了数据的传输量。不过，要把 FaaS 和边缘计算做好，OpenResty 的动态只是一个良好的基础，你还需要考虑自身周边生态的完善和厂商的加入，这就不仅仅是技术的范畴了。


### 动态上游
现在，让我们把思绪拉回到 OpenResty 上来，一起来看如何实现动态上游。lua-resty-core 提供了 ngx.balancer 这个库来设置上游，它需要放到 OpenResty 的 balancer 阶段来运行：

```
balancer_by_lua_block {
    local balancer = require "ngx.balancer"
    local host = "127.0.0.2"
    local port = 8080

    local ok, err = balancer.set_current_peer(host, port)
    if not ok then
        ngx.log(ngx.ERR, "failed to set the current peer: ", err)
        return ngx.exit(500)
    end
}
```
我来简单解释一下。set_current_peer 函数，就是用来设置上游的 IP 地址和端口的。不过要注意，这里并不支持域名，你需要使用 lua-resty-dns 库来为域名和 IP 做一层解析。不过，ngx.balancer 还比较底层，虽然它有设置上游的能力，但动态上游的实现远非如此简单。所以，在 ngx.balancer 前面还需要两个功能：

一是上游的选择算法，究竟是一致性哈希，还是 roundrobin；二是上游的健康检查机制，这个机制需要剔除掉不健康的上游，并且需要在不健康的上游变健康的时候，重新把它加入进来。

而 OpenResty 官方的 lua-resty-balancer [这个库](https://github.com/openresty/lua-resty-balancer)中，则包含了 resty.chash 和 resty.roundrobin 两类算法来完成第一个功能，并且有 lua-resty-upstream-healthcheck 来尝试完成第二个功能。


不过，这其中还是有两个问题。第一点，缺少最后一公里的完整实现。把 ngx.balancer、lua-resty-balancer 和 lua-resty-upstream-healthcheck 整合并实现动态上游的功能，还是需要一些工作量的，这就拦住了大部分的开发者。第二点，lua-resty-upstream-healthcheck 的实现并不完整，只有被动的健康检查，而没有主动的健康检查。

简单解释一下，这里的被动健康检查，是指由终端的请求触发，进而分析上游的返回值来作为健康与否的判断条件。如果没有终端请求，那么上游是否健康就无从得知了。而主动健康检查就可以弥补这个缺陷，它使用 ngx.timer 定时去轮询指定的上游接口，来检测健康状态。所以，在实际的实践中，我们通常推荐使用 lua-resty-healthcheck 这个[库](https://github.com/Kong/lua-resty-healthcheck)，来完成上游的健康检查。它的优点是包含了主动和被动的健康检查，而且在多个项目中都经过了验证，可靠性更高。

更进一步，新兴的微服务 API 网关 APISIX，在 lua-resty-healthcheck 的基础之上，对动态上游做了完整的实现。我们可以参考它的[实现](https://github.com/apache/apisix/blob/master/lua/apisix/http/balancer.lua)，总共只有 200 多行代码，你可以很轻松地把它剥离出来，放到你的自己的项目中使用。

## 45 | 不得不提的能力外延：OpenResty常用的第三方库

对于开发语言和平台来讲，很多时候的学习，其实是对标准库和第三方库的学习，语法本身并不用花费很多的时间。这对 OpenResty 来说也是一样的，学完它自己的 API 和性能优化技巧后，就需要各种 lua-resty 库，来帮助我们把 OpenResty 的能力外延，以应用到更多的场景中去。

### 去哪里找 lua-resty 库？
和 PHP、Python、JavaScript 相比，当前 OpenResty 的标准库和第三方库还比较贫瘠，找出合适的 lua-resty 库还不是一件容易的事情。不过，这里仍然有两个推荐的渠道，可以帮你更快地找到它们。我首先推荐的是由 Aapo 维护的 awesome-resty [仓库](https://github.com/bungle/awesome-resty)，这个仓库分门别类地整理了和 OpenResty 相关的库，可以说是包罗万象，包括了 Nginx 的 C 模块、lua-resty 库、Web 框架、路由库、模板、测试框架等，是你寻找 OpenResty 资源的首选。当然，如果你在 Aapo 的仓库中没有找到合适的库，那么还可以去 luarocks、opm 和 GitHub 碰碰运气。有一些开源时间不长的、或者关注不多的库，可能就藏在其中。在前面的课程中，我们已经接触了不少有用的库，比如 lua-resty-mlcache、lua-resty-traffic、lua-resty-shell 等。今天，在 OpenResty 性能优化部分的最后一节课，我们再来认识 3 个独具特色的周边库，它们都是由社区的开发者贡献的。


### ngx.var 的性能提升
首先让我们来看一个 C 模块：[lua-var-nginx-module](https://github.com/iresty/lua-var-nginx-module)。前面我曾经提到过，ngx.var 是一个性能损耗比较大的操作，在实际使用时，我们需要用 ngx.ctx 来做一层缓存。那有没有什么方法，可以彻底解决 ngx.var 的性能问题呢？这个 C 模块，就在这个方面做了一些尝试，效果也很显著，性能比起ngx.var 提升了 5 倍。它采用的是 FFI 的方式，所以，你需要在编译 OpenResty 的时候，先加上编译选项：

```
./configure --prefix=/opt/openresty \
         --add-module=/path/to/lua-var-nginx-module
```
然后，使用 luarocks 的方式来安装 lua 库：

```
luarocks install lua-resty-ngxvar
```
这里调用的方法也很简单，只需要一行 fetch 函数的调用就可以了。它的效果完全等价于原有的 ngx.var.remote_addr，来获取到终端的 IP 地址：


```
content_by_lua_block {
    local var = require("resty.ngxvar")
    ngx.say(var.fetch("remote_addr"))
}
```
知道了这些基本操作后，你可能更好奇的是，这个模块到底是怎么做到性能大幅度提升的呢？还是那句老话，源码面前无秘密，就让我们来看看 remote_addr 这个变量在其中是如何获取的吧：

```
ngx_int_t 
ngx_http_lua_var_ffi_remote_addr(ngx_http_request_t *r, ngx_str_t *remote_addr) 
{ 
    remote_addr->len = r->connection->addr_text.len; 
    remote_addr->data = r->connection->addr_text.data; 

    return NGX_OK; 
}
```
阅读这段代码后，你会发现，这种 Lua FFI 的方式和 lua-resty-core 的做法如出一辙。它的优点很明显，使用 FFI 的方式来直接获取变量，绕过了 ngx.var 原有的查找逻辑；同时，缺点也很明显，那就是要为每一个希望获取的变量，都增加对应的 C 函数和 FFI 调用，这其实是一个体力活。有人可能会问，我为什么会说这是体力活呢？上面的 C 代码看上去不是还挺有含量的吗？我们不妨来看看这几行代码的源头，它们出自  Nginx 代码中的 src/http/ngx_http_variables.c：


```
static ngx_int_t
ngx_http_variable_remote_addr(ngx_http_request_t *r,
ngx_http_variable_value_t *v, uintptr_t data)
{
    v->len = r->connection->addr_text.len;
    v->valid = 1;
    v->no_cacheable = 0;
    v->not_found = 0;
    v->data = r->connection->addr_text.data;

    return NGX_OK;
}
```
看到源码后，谜底揭开了！lua-var-nginx-module 其实是 Nginx 变量代码的搬运工，并在外层做了 FFI 的封装，用这种方式达到了性能优化的目的。这其实也是一个很好的思路和优化方向。这里我再多说几句，我们学习某个库或者某个工具，一定不要仅仅停留在操作使用的层面，还应该多问问为什么，多看看源码，在底层原理的层面上，我们才能学到更多的设计思想和解决思路。当然，我也非常鼓励你去贡献代码，以支持更多的 Nginx 变量。


### JSON Schema
下面我介绍的是一个 lua-resty 库：[lua-rapidjson](https://github.com/xpol/lua-rapidjson) 。它是对 rapidjson 这个腾讯开源的 JSON 库的封装，以性能见长。这里，我们着重介绍下它和 cjson 的不同之处，也就是支持 JSON Schema。JSON Schema 是一个通用的标准，借助这个标准，我们就可以精确地描述接口中参数的格式，以及如何校验的问题。下面是一个简单的示例：

```
"stringArray": {
    "type": "array",
    "items": { "type": "string" },
    "minItems": 1,
    "uniqueItems": true
}
```
这段 JSON 准确地描述了 stringArray 这个参数的类型是字符串数组，并且数组不能为空，数组元素也不能重复。而lua-rapidjson，则是可以让我们在 OpenResty 中来使用 JSON Schema，这能给接口的校验带来极大的便利。举个例子，比如对于前面介绍过的 limit count 限流接口，我们就可以用下面的 schema 来描述：


```
local schema = {
    type = "object",
    properties = {
        count = {type = "integer", minimum = 0},
        time_window = {type = "integer",  minimum = 0},
        key = {type = "string", enum = {"remote_addr", "server_addr"}},
        rejected_code = {type = "integer", minimum = 200, maximum = 600},
    },
    additionalProperties = false,
    required = {"count", "time_window", "key", "rejected_code"},
}
```
你会发现，这可以带来两个十分明显的收益：对前端来说，前端可以直接复用这个 schema 描述，用于前端页面的开发和参数校验，而不用再去关心后端；而对后端来说，后端直接使用 lua-rapidjson 的 schema 校验函数 SchemaValidator 就能完成接口合法性的判断，更是无须编写多余的代码。
### worker 间通信
最后，我要讲的是可以实现 OpenResty 中 worker 间通信的 lua-resty 库。OpenResty 的 worker 之间，并没有机制可以直接通信，这显然会带来不少的问题。让我们设想这么一个场景：

一个 OpenResty 服务有 24 个 worker 进程，管理员通过 REST HTTP 接口更新了系统的某项配置，这时候只有一个 worker 收到了管理员的更新操作，并把结果写入了数据库，更新了共享字典和自己 worker 内的 lru 缓存。那么，其他 23 个 worker 怎么才能被通知去更新这项配置呢？

显然，多个 worker 之间需要一个通知的机制，才能完成上面的这个任务。在 OpenResty 自身不支持的情况下，我们就只能通过共享字典这个跨 worker 可以访问的空间，来曲线救国了。lua-resty-worker-events 便是这个思路的具体实现。它在共享字典中维护了一个版本号，在有新消息需要发布的时候，给这个版本号加一，并把消息内容放到以版本号为 key 的字典中：

```
event_id, err = _dict:incr(KEY_LAST_ID, 1)
success, err = _dict:add(KEY_DATA .. tostring(event_id), json)
```
同时，在后台使用 ngx.timer 创建了一个默认间隔为 1 秒的 polling 循环，来不断地检测版本号是否有变化：

```
local event_id, err = get_event_id()
if event_id == _last_event then
    return "done"
end
```
这样，一旦发现有新的事件通知需要处理时，就根据版本号从共享字典中获取消息内容：

```
while _last_event < event_id do
    count = count + 1
    _last_event = _last_event + 1
    data, err = _dict:get(KEY_DATA..tostring(_last_event))
end
```
总的来说，虽然 lua-resty-worker-events 会有 1 秒钟的延时，但还是实现了 worker 之间的事件通知机制，瑕不掩瑜。不过，在一些实时性要求比较高的场景下，比如消息推送，OpenResty 缺少 worker 进程间直接通信的这个问题，就可能会给你带来一些困扰了。这一点目前没有更好的解决方案，如果你有好的想法，欢迎在 Github 或者 OpenResty 的邮件列表中来一起探讨。OpenResty 的很多功能都是由社区用户来驱动的，这样才能构造一个良性的生态循环。

### 写在最后
今天我们介绍的这三个库，都各具特色，也都为 OpenResty 的应用带来了更多的可能性。


## 46 | 答疑（四）：共享字典的缓存是必须的吗？

专栏更新到现在，OpenResty 第四版块 OpenResty 性能优化篇，我们就已经学完了。恭喜你没有掉队，仍然在积极学习和实践操作，并且热情地留下了你的思考。很多留言提出的问题很有价值，大部分我都已经在 App 里回复过，一些手机上不方便回复的或者比较典型、有趣的问题，我专门摘了出来，作为今天的答疑内容，集中回复。另一方面，也是为了保证所有人都不漏掉任何一个重点。下面我们来看今天的这 5 个问题。

问题一：如何完成 Lua 模块的动态加载？
Q：关于 OpenResty 实现的动态加载，我有个疑问：在完成新文件替换后，如何用 loadstring 函数完成新文件的加载呢 ？我了解到，loadstring 只能加载字符串，如果要重新加载一个 lua 文件 / 模块，在 OpenResty 中要如何做到呢？A：我们知道，loadstring 是加载字符串使用的，而 loadfile 可以加载指定的文件，比如： loadfile("foo.lua")。事实上，这两个命令达到的效果是一样的。至于如何加载 Lua 模块，下面是一个具体的示例：

```
resty -e 'local s = [[
local ngx = ngx
local _M = {}
function _M.f()
    ngx.say("hello world")
end
return _M
]]
local lua = loadstring(s)
local ret, func = pcall(lua)
func.f()'
```
这里的字符串 s，它的内容就是一个完整的 Lua 模块。所以，在发现这个模块的代码有变化时，你可以用 loadstring 或者 loadfile 来重启加载。这样，其中的函数和变量都会随之更新。更进一步，你也把可以把获取变化和重新加载，用名为 code_loader 函数做一层包装：

```
local func = code_loader(name)
```
这样一来，代码更新就会变得更为简洁；同时， code_loader 中我们一般会用 lru cache 对 s 做一层缓存，避免每一次都去调用 loadstring。这差不多就是一个完整的实现了。



问题二：OpenResty 为什么不禁用阻塞操作？Q：这些年来，我一直有个疑虑，既然这些阻塞调用是官方极力不鼓励的，为什么不直接禁用呢？或者加一个 flag 让用户选择禁用呢？

A：这里说一下我个人的看法。首先是因为 OpenResty 的周边生态还不够完善，有时候我们不得不调用阻塞的库来实现一些功能。比如 ，在 1.15.8 版本之前，调用外部的命令行还需要走 Lua 库的 os.execute，而不是 lua-resty-shell；再如，在 OpenResty 中，读写文件至今还是只能走 Lua 的 I/O 库，并没有非阻塞的方式来替代。其次，OpenResty 在这种优化上的态度是很谨慎的。比如， lua-resty-core 已经开发完成很长时间了，但一直都没有默认开启，需要你手工来调用 require 'resty.core'。直到最新的 1.15.8 版本，它才得以转正。最后，OpenResty 的维护者更希望，通过编译器和 DSL 自动生成高度优化过的 Lua 代码，这种方式来规范阻塞方式的调用。所以，大家并没有在 OpenResty 平台本身上，去做类似 flag 选项的努力。当然，这种方向是否能够解决实际的问题，我是保留态度的。站在外部开发者的角度，如何避免这种阻塞，才是更为实际的问题。我们可以扩展 Lua 代码的检测工具，比如 luacheck 等，发现并对常见的阻塞操作进行告警；也可以直接通过改写 _G 的方式，来侵入式地禁止或者改写某些函数，比如：

```
resty -e '_G.ngx.print = function()
ngx.say("hello")
end
ngx.print()'
hello
```
这样的示例代码，就可以直接改写 ngx.print 函数了。

问题三：LuaJIT 的 NYI 的操作，是否会对性能有很大影响？Q：loadstring 在 LuaJIT 的 NYI 列表是 never，会不会对性能有很大影响？

A：关于 LuaJIT 的 NYI，我们不用矫枉过正。对于可以 JIT 的操作，自然是 JIT 的方式最好；但对于还不能 JIT 的操作，我们也不是不能使用。对于性能优化，我们需要用基于统计的科学方法来看待，这也就是火焰图采样的意义。过早优化是万恶之源。对于那些调用次数频繁、消耗 CPU 很高的热代码，我们才有优化的必要。回到 loadstring 的问题，我们只会在代码发生变化的时候，才会调用它重新加载，和请求多少无关，所以它并不是一个频繁的操作。这个时候，我们就不用担心它对系统整体性能的影响。结合第二个阻塞的问题，在 OpenResty 中，我们有些时候也会在 init 和 init worker 阶段，去调用阻塞的文件 I/O 操作。这种操作比 NYI 更加影响性能，但因为它只在服务启动的时候执行一次，所以也是可以被我们接受的。还是那句话，性能优化要从宏观的视角来看待，这是你特别需要注意的一个点。否则，纠结于某一细节，就很有可能优化了半天，却并没有起到很好的效果。

问题四：动态上游可以自己来实现吗？Q：动态上游这块，我的做法是为一个服务设置 2 个 upstream，然后根据路由条件选择不同的 upstream，当机器 IP 有变化时，直接修改 upstream 中的 IP 即可。这样的做法，和直接使用 balancer_by_lua 相比，有什么劣势或坑吗？

A：单独看这个案例。balancer_by_lua 的优势，是可以让用户选择负载均衡的算法，比如是用 roundrobin 还是 chash，又或者是用户自己实现的其他算法都可以，灵活而且性能很高。如果按照路由规则的方式来做，从最终结果上来看是一样的。但上游健康检查需要你自己来实现，增加了不少额外的工作量。我们也可以扩展下这个提问，对于 abtest 这种需要不同上游的场景，我们应该如何去实现呢？你可以在 balancer_by_lua 阶段中，根据 uri、host、参数等来决定使用哪一个上游。你也可以使用 API 网关，把这些判断变为路由的规则，在最开始的 access 阶段，通过判断决定使用哪一个路由，再通过路由和上游的绑定关系找到指定的上游。这就是 API 网关的常见做法，后面在实战章节中，我们会更具体地聊到。

问题五：共享字典的缓存是必须的吗？Q：在实际的生产应用中，我认为 shared dict 这一层缓存是必须的。貌似大家都只记得 lruca  che 的好，数据格式没限制、不需要反序列化、不需要根据 k/v 体积算内存空间、worker 间独立不相互争抢、没有读写锁、性能高云云。但是，却忘记了它最致命的一个弱点，就是 lru  cache 的生命周期是跟着 worker 走的。每当 Nginx reload 时，这部分缓存会全部丢失，这时候，如果没有 shared dict，那 L3 的数据源分分钟被打挂。当然，这是并发比较高的情况下，但是既然用到了缓存，就说明业务体量肯定不会小，也就是刚刚的分析仍然适用。不知道我的这个观点对吗？

A：大部分情况下，确实如你所说，共享字典在 reload 的时候不会丢失，所以它有存在的必要性。但也有一种特例，那就是，如果在 init 阶段或者 init_worker 阶段，就能从 L3 也就是数据源主动获取到所有数据，那么只有 lru cache 也是可以接受的。举例来说，比如开源 API 网关 [APISIX](https://github.com/iresty/apisix) 的数据源在 etcd 中，它只在 init_worker 阶段，从 etcd 中获取数据并缓存在 lru cache 中，后面的缓存更新，都是通过 etcd 的 watch 机制来主动获取的。这样一来，即使 Nginx reload ，也不会有缓存风暴产生。所以，对待技术的选择，我们可以有倾向，但还是不要一概而论绝对化，因为并没有一个可以适合所有缓存场景的银弹。根据实际场景的需要，构建一个最小化可用的方案，然后逐步地增加，是一个不错的法子。

## 47 | 微服务API网关搭建三步曲（一）
今天这节课，OpenResty 专栏就要进入实战的章节了。我会用三节课的内容，来为你介绍如何实现一个微服务 API 网关。在这个过程中，我们不仅会涉及到前面学过的 OpenResty 知识，我也会从行业、产品、技术选型等多个维度，为你展示下，如何从头做一个新的产品和开源项目。

### 微服务 API 网关有什么用？
让我们先来看下微服务 API 网关的作用。下面这张图，是一个简要的说明：

![img](https://static001.geekbang.org/resource/image/de/ee/dea3e12608301d1a6b7fffce8bafafee.png?wh=3264*1816)



众所周知，API 网关并非一个新兴的概念，在十几年前就已经存在了，它的作用主要是作为流量的入口，统一处理和业务相关的请求，让请求更加安全、快速和准确地得到处理。它有以下几个传统功能：反向代理和负载均衡，这和 Nginx 的定位和功能是一致的；动态上游、动态 SSL 证书和动态限流限速等运行时的动态功能，这是开源版本 Nginx 并不具备的功能；上游的主动和被动健康检查，以及服务熔断功能；在 API 网关的基础上进行扩展，成为全生命周期的 API 管理平台。

在最近几年，业务相关的流量，不再仅仅由 PC 客户端和浏览器发起，更多的来自手机、IoT 设备等，未来随着 5G 的普及，这些流量会越来越多。同时，随着微服务架构的结构变迁，服务之间的流量也开始爆发性地增长。在这种新的业务场景下，自然也催生了 API 网关更多、更高级的功能：

云原生友好，架构要变得轻巧，便于容器化；对接 Prometheus、Zipkin、SkyWalking 等统计、监控组件；支持 gRPC 代理，以及 HTTP 到 gRPC 之间的协议转换，把用户的 HTTP 请求转为内部服务的 gRPC 请求；承担 OpenID Relying Party 的角色，对接 Auth0、Okta 等身份认证提供商的服务，把流量安全作为头等大事来对待；通过运行时动态执行用户函数的方式来实现 Serverless，让网关的边缘节点更加灵活；不锁定用户，支持混合云的部署架构；最后，网关节点要状态无关，可以随意地扩容和缩容。

当一个微服务 API 网关具备了上述十几项功能时，就可以让用户的服务只关心业务本身；而和业务实现无关的功能，比如服务发现、服务熔断、身份认证、限流限速、统计、性能分析等，就可以在独立的网关层面来解决。从这个角度来看，API 网关既可以替代 Nginx 的所有功能，处理南北向的流量；也可以完成 Istio 控制面和 Envoy 数据面的角色，处理东西向的流量。



### 为什么要新造轮子？
正因为微服务 API 网关的地位如此重要，所以它一直处于兵家必争之地，传统的 IT 巨头在这个领域很早就都有布局。根据 2018 年 Gartner 发布的 API 全生命周期报告，谷歌、CA、IBM、红帽、Salesforce 都是处于领导地位的厂商，开发者更熟悉的 Kong 则处于远见者的区间内。那么，问题就来了，为什么我们还要新造一个轮子呢？

简单来说，这是因为当前的微服务 API 网关都不足以满足我们的需求。我们首先来看闭源的商业产品，它们的功能都很完善，覆盖了 API 的设计、多语言 SDK、文档、测试和发布等全生命周期管理，并且提供 SaaS 服务，有些还与公有云做了集成，使用起来非常方便。但同时，它们也带来了两个痛点。


第一个痛点，平台锁定问题。API 网关是业务流量的入口，它不像图片、视频等 CDN 加速的这种非业务流量可以随意迁移，API 网关上会绑定不少业务相关的逻辑。你一旦使用了闭源的方案，就很难平滑和低成本地迁移到其他平台。第二个痛点，无法二次开发的问题。一般的大中型企业都会有自己独特的需求，需要定制开发，但这时候你只能依靠厂商，而不能自己动手去做二次开发。


这也是为什么开源的 API 网关方案开始流行的一个原因。不过，现有的开源产品也不是万能的，自身也有很多不足。


第一，依赖 PostgreSQL、MySQL 等关系型数据库。这样，在配置发生变化的时候，网关节点只能轮询数据库。这不仅造成配置生效慢，也给代码增加了复杂度，让人难以理解；同时，数据库也会成为系统的单点和性能瓶颈，无法保证整体的高可用。如果你把 API 网关用于 Kubernetes 环境下，关系型数据库会显得更加笨重，不利于快速伸缩。第二，插件不能热加载。当你新增一个插件或者修改现有插件的代码后，必须要重载服务才能生效，这和修改 Nginx 配置后需要重载是一样的，显然会影响用户的请求。第三，代码结构复杂， 难以掌握。有些开源项目做了多层面向对象的封装，一些简单的逻辑也变得雾里看花。但其实，对于 API 网关这种场景，直来直去的表达会更加清晰和高效，也更有利于二次开发。

所以，我们需要一个更轻巧、对云原生和开发友好的 API 网关。当然，我们也不能闭门造车，需要先深入了解已有 API 网关各自的特点，这时候，云原生软件基金会（CNCF）的全景图就是一个很好的参考：

![img](https://static001.geekbang.org/resource/image/19/f7/19328e6e516ed8ed6f723dd32fef58f7.png?wh=3654*1761)


这张图筛选出了业界常见的 API 网关，以开源的方案为主，可以为我们下面的技术选型提供不少有价值的内容。


### API 网关的核心组件和概念
当然，在具体实现之前，我们还需要了解 API 网关有哪些核心组件。根据我们前面提到的 API 网关具备的功能点，它至少需要下面几个组件才能开始运行。首先是路由。它通过定义一些规则来匹配客户端的请求，然后根据匹配结果，加载、执行相应的插件，并把请求转发给到指定的上游。这些路由匹配规则可以由 host、uri、请求头等组成，我们熟悉的 Nginx 中的 location，就是路由的一种实现。其次是插件。这是 API 网关的灵魂所在，身份认证、限流限速、IP 黑白名单、Prometheus、Zipkin 等这些功能，都是通过插件的方式来实现的。既然是插件，那就需要做到即插即用；并且，插件之间不能互相影响，就像我们搭建乐高积木一样，需要用统一规则的、约定好的开发接口，来和底层进行交互。接着是 schema。既然是处理 API 的网关，那么少不了要对 API 的格式做校验，比如数据类型、允许的字段内容、必须上传的字段等，这时候就需要有一层 schema 来做统一、独立的定义和检查。最后是存储。它用于存放用户的各种配置，并在有变更时负责推送到所有的网关节点。这是底层非常关键的基础组件，它的选型决定了上层的插件如何编写、系统能否保持高可用和可扩展性等，所以需要我们审慎地决定。另外，在这些核心组件之上，我们还需要抽象出几个 API 网关的常用概念，它们在不同的 API 网关之间都是通用的。先来说说 Route。路由会包含三部分内容，即匹配的条件、绑定的插件和上游，如下图所示：

![img](https://static001.geekbang.org/resource/image/6a/0d/6a86c854ec54b07347ff517114482c0d.png?wh=1462*322)

我们可以直接在 Route 中完成所有的配置，这样最简单。但在 API 和上游很多的情况下，这样做就会有很多重复的配置。这时候，我们就需要 Service 和 Upstream 这两个概念来做一层抽象。我们接着来看 Service。它是某类 API 的抽象，也可以理解为一组 Route 的抽象，它通常与上游服务是一一对应的，而 Route 与 Service 之间通常是 N:1 的关系。我也用了一张图来表示：

![img](https://static001.geekbang.org/resource/image/09/db/0954bddf3828fa26f26a1ba2003c7edb.png?wh=1286*1338)



通过 Service 的这层抽象，我们就可以把重复的插件和上游剥离出来。这样，在插件和上游发生变更的时候，我们只需要修改 Service 就可以了，而不用去修改多个 Route 上绑定的数据。最后说说 Upstream。还是继续上面的示例，如果两个 Route 中的上游是一样的，但是绑定的插件各自不同，那么我们就可以把上游单独抽象出来，如下图所示：

![img](https://static001.geekbang.org/resource/image/eb/8e/ebedcfafc5aeafb970097e480f663d8e.png?wh=1643*1608)

这样，在上游节点发生变更时，Route 是完全无感知的，它们都在 Upstream 内部进行了处理。其实，从这三个主要概念的衍生过程中，我们也可以看到，这几个抽象都基于用户的实际场景，而不是生造出来的。自然，它们适用于所有的 API 网关，和具体的技术方案无关。


### 写在最后
今天这节课，我们介绍了微服务 API 网关的作用、功能、核心组件和抽象概念，它们都是 API 网关的基础。


## 48 | 微服务API网关搭建三步曲（二）

在明白了微服务 API 网关的核心组件和抽象概念后，我们就要开始技术选型，并动手去实现它了。今天，我们就分别来看下，路由、插件、schema 和存储这四个核心组件的技术选型问题。

### 存储
上节课我提到过，存储是底层非常关键的基础组件，它会影响到配置如何同步、集群如何伸缩、高可用如何保证等核心的问题，所以，我们把它放在最开始的位置来选型。我们先来看看，已有的 API 网关是把数据存储在哪里的。Kong 是把数据储存在 PostgreSQL 或者 Cassandra 中，而同样基于 OpenResty 的 Orange，则是存储在 MySQL 中。不过，这种选择还是有很多缺陷的。


第一，储存需要单独做高可用方案。PostgreSQL、MySQL 数据库虽然有自己的高可用方案，但你还需要 DBA 和机器资源，在发生故障时也很难做到快速切换。第二，只能轮询数据库来获取配置变更，无法做到推送。这不仅会增加数据库资源的消耗，同时变更的实时性也会大打折扣。第三，需要自己维护历史版本，并考虑回退和升级。如果用户发布了一个变更，后续可能会有回滚操作，这时候你就需要在代码层面，自己做两个版本之间的 diff，以便配置的回滚。同时，在系统自身升级的时候，还可能会修改数据库的表结构，所以代码层面就需要考虑到新旧版本的兼容和数据升级。第四，提高了代码的复杂度。在实现网关的功能之外，你还需要为了前面 3 个缺陷，在代码层面去打上补丁，这显然会让代码的可读性降低不少。第五，增加了部署和运维的难度。部署和维护一个关系型数据库并不是一件简单的事情，如果是一个数据库集群那就更加复杂了，并且我们也无法做到快速扩容和缩容。


针对这样的情况，我们应该如何选择呢？我们不妨回到 API 网关的原始需求上来，这里存储的都是简单的配置信息，uri、插件参数、上游地址等，并没有涉及到复杂的联表操作，也不需要严格的事务保证。显然，这种情况下使用关系型数据库，可不就是“杀鸡焉用宰牛刀”吗？事实上，本着最小化够用并且更贴近 K8s 的原则，etcd 就是一个恰到好处的选型了：

API 网关的配置数据每秒钟的变化次数不会很多，etcd 在性能上是足够的；集群和动态伸缩方面，更是 etcd 天生的优势；etcd 还具备 watch 的接口，不用轮询去获取变更。

其实还有一点，可以让我们更加放心地选择 etcd——它已经是 K8s 体系中保存配置的默认选型了，显然已经经过了很多比 API 网关更加复杂的场景的验证。


### 路由
路由也是非常重要的技术选型，所有的请求都由路由筛选出需要加载的插件列表，逐个运行后，再转发给指定的上游。不过，考虑到路由规则可能会比较多，所以路由这里的技术选型，我们需要着重从算法的时间复杂度上去考量。我们先来看下，在 OpenResty 下有哪些现成的路由可以拿来使用。老规矩，让我们在 awesome-resty 的项目中逐个查找一遍，这其中就有专门的 Routing Libraries：

```
    •    lua-resty-route — A URL routing library for OpenResty supporting multiple route matchers, middleware, and HTTP and WebSockets handlers to mention a few of its features
    •    router.lua — A barebones router for Lua, it matches URLs and executes Lua functions
    •    lua-resty-r3 — libr3 OpenResty implementation, libr3 is a high-performance path dispatching library. It compiles your route paths into a prefix tree (trie). By using the constructed prefix trie in the start-up time, you may dispatch your routes with efficiency
    •    lua-resty-libr3 — High-performance path dispatching library base on libr3 for OpenResty
```
你可以看到，这里面包含了四个路由库的实现。前面两个路由都是纯 Lua 实现，相对比较简单，所以有不少功能的欠缺，还不能达到生成的要求。后面两个库，其实都是基于 libr3 这个 C 库，并使用 FFI 的方式做了一层封装，而 libr3 自身使用的是前缀树。这种算法和存储了多少条规则的数目 N 无关，只和匹配数据的长度 K 有关，所以时间复杂度为 O(K)。但是， libr3 也是有缺点的，它的匹配规则和我们熟悉的 Nginx location 的规则不同，而且不支持回调。这样，我们就没有办法根据请求头、cookie、Nginx 变量来设置路由的条件，对于 API 网关的场景来说显然不够灵活。不过，虽说我们尝试从 awesome-resty 中找到可用路由库的努力没有成功，但 libr3 的实现，还是给我们指引了一个新的方向：用 C 来实现前缀树以及 FFI 封装，这样应该可以接近时间复杂度和代码性能上的最优方案。

正好， Redis 的作者开源了一个基数树，也就是压缩前缀树的 [C 实现](https://github.com/antirez/rax)。顺藤摸瓜，我们还可以找到 rax 在 OpenResty 中可用的 [FFI 封装库](https://github.com/api7/lua-resty-radixtree)，它的示例代码如下：


```
local radix = require("resty.radixtree")
local rx = radix.new({
    {
        path = "/aa",
        host = "foo.com",
        method = {"GET", "POST"},
        remote_addr = "127.0.0.1",
    },
    {
        path = "/bb*",
        host = {"*.bar.com", "gloo.com"},
        method = {"GET", "POST", "PUT"},
        remote_addr = "fe80:fe80::/64",
        vars = {"arg_name", "jack"},
    }
})

ngx.say(rx:match("/aa", {host = "foo.com",
                     method = "GET",
                     remote_addr = "127.0.0.1"
                    }))
```
从中你也可以看出， lua-resty-radixtree 支持根据 uri、host、http method、http header、Nginx 变量、IP 地址等多个维度，作为路由查找的条件；同时，基数树的时间复杂度为 O(K)，性能远比现有 API 网关常用的“遍历 +hash 缓存”的方式，来得更为高效。


### schema
schema 的选择其实要容易得多，我们在前面介绍过的 lua-rapidjson ，就是非常好的一个选择。这部分你完全没有必要自己去写一个，json schema 已经足够强大了。下面就是一个简单的示例：

```
local schema = {
    type = "object",
    properties = {
        count = {type = "integer", minimum = 0},
        time_window = {type = "integer",  minimum = 0},
        key = {type = "string", enum = {"remote_addr", "server_addr"}},
        rejected_code = {type = "integer", minimum = 200, maximum = 600},
    },
    additionalProperties = false,
    required = {"count", "time_window", "key", "rejected_code"},
}
```
### 插件
有了上面存储、路由和 schema 的基础，上层的插件应该如何实现，其实就清晰多了。插件并没有现成的开源库可以使用，需要我们自己来实现。插件在设计的时候，主要有三个方面需要我们考虑清楚。首先是如何挂载。我们希望插件可以挂载到 rewrite、access、header filer、body filter 和 log阶段，甚至在 balancer 阶段也可以设置自己的负载均衡算法。所以，我们应该在 Nginx 的配置文件中暴露这些阶段，并在对插件的实现中预留好接口。其次是如何获取配置的变更。由于没有关系型数据库的束缚，插件参数的变更可以通过 etcd 的 watch 来实现，这会让整体框架的代码逻辑变得更加明了易懂。最后是插件的优先级。具体来说，比如，身份认证和限流限速的插件，应该先执行哪一个呢？绑定在 route 和绑定在 service 上的插件发生冲突时，又应该以哪一个为准呢？这些都是我们需要考虑到位的。在梳理清楚插件的这三个问题后，我们就可以得到插件内部的一个流程图了：

![img](https://static001.geekbang.org/resource/image/d1/13/d18243966a4973ff8409dd45bf83dc13.png?wh=974*1182)


### 架构
自然，当微服务 API 网关的这些关键组件都确定了之后，用户请求的处理流程，也就随之尘埃落定了。这里我画了一张图来表示这个流程：

![img](https://static001.geekbang.org/resource/image/7f/89/7f2b50689a86d382a4c9340b4edb9489.png?wh=2843*1674)

从这个图中我们可以看出，当一个用户请求进入 API 网关时，首先，会根据请求的方法、uri、host、请求头等条件，去路由规则中进行匹配。如果命中了某条路由规则，就会从 etcd 中获取对应的插件列表。然后，和本地开启的插件列表进行交集，得到最终可以运行的插件列表。再接着，根据插件的优先级，逐个运行插件。最后，根据上游的健康检查和负载均衡算法，把这个请求发送给上游。


当架构设计完成后，我们就胸有成竹，可以去编写具体的代码了。这其实就像盖房子一样，只有在你拥有设计的蓝图和坚实的地基之后，才能去做砖瓦堆砌的具体工作。

### 写在最后
其实，通过这两节课的学习，我们已经做好了产品定位和技术选型这两件最重要的事情，它们都比具体的编码实现更为关键，也希望你可以更用心地去考虑和选择。



## 49 | 微服务API网关搭建三步曲（三）

今天这节课，微服务 API 网关搭建就到了最后的环节了。让我们用一个最小的示例来把之前选型的组件，按照设计的蓝图，拼装运行起来吧！



### Nginx 配置和初始化
我们知道，API 网关是用来处理流量入口的，所以我们首先需要在 Nginx.conf 中做简单的配置，让所有的流量都通过网关的 Lua 代码来处理。

```
server {
    listen 9080;

    init_worker_by_lua_block {
        apisix.http_init_worker()
    }

    location / {
        access_by_lua_block {
            apisix.http_access_phase()
        }
        header_filter_by_lua_block {
            apisix.http_header_filter_phase()
        }
        body_filter_by_lua_block {
            apisix.http_body_filter_phase()
        }
        log_by_lua_block {
            apisix.http_log_phase()
        }
    }
}
```
这里我们使用开源 API 网关 [APISIX](https://github.com/apache/apisix)  为例，所以上面的代码示例中带有 apisix 的关键字。在这个示例中，我们监听了 9080 端口，并通过 location / 的方式，把这个端口的所有请求都拦截下来，并依次通过 access、rewrite、header filter、body filter 和 log 这几个阶段进行处理，在每个阶段中都会去调用对应的插件函数。其中， rewrite 阶段便是在 apisix.http_access_phase 函数中合并处理的。而对于系统初始化的工作，我们放在了 init_worker 阶段来处理，这其中包含了读取各项配置参数、预制 etcd 中的目录、从 etcd 中获取插件列表、对于插件按照优先级进行排序等。我这里列出了关键部分的代码并进行讲解，当然，你可以在 GitHub 上看到更完整的[初始化函数](https://github.com/apache/apisix/blob/master/lua/apisix.lua#L47)。

```
function _M.http_init_worker()
-- 分别初始化路由、服务和插件这三个最重要的部分
    router.init_worker()
    require("apisix.http.service").init_worker()
    require("apisix.plugin").init_worker()
end
```
通过阅读这段代码，你可以发现，router 和 plugin 这两部分的初始化相对复杂一些，主要涉及到读取配置参数，并根据参数的不同做一些选择。因为这里会涉及到从 etcd 中读取数据，所以我们使用的是 ngx.timer 的方式，来绕过“不能在 init_worker 阶段使用 cosocket”的这个限制。如果你对这部分很感兴趣并且学有余力，建议一定要去读读源码，加深理解。


### 匹配路由
在最开始的 access 阶段里面，我们首先需要做的就是匹配路由，根据请求中携带 uri、host、args、cookie 等，来和已经设置好的路由规则进行匹配：

```
router.router_http.match(api_ctx)
```
对外暴露的，其实只有上面一行代码，这里的api_ctx 中存放的就是 uri、host、args、cookie 这些请求的信息。而具体的 match 函数的[实现](https://github.com/apache/apisix/blob/master/apisix/http/router/radixtree_uri.lua)，就用到了我们前面提到过的 lua-resty-radixtree。如果没有命中，就说明这个请求并没有设置与之对应的上游，就会直接返回 404。


```
local router = require("resty.radixtree")

local match_opts = {}

function _M.match(api_ctx)
    -- 从 ctx 中获取请求的参数，作为路由的判断条件
    match_opts.method = api_ctx.var.method
    match_opts.host = api_ctx.var.host
    match_opts.remote_addr = api_ctx.var.remote_addr
    match_opts.vars = api_ctx.var
    -- 调用路由的判断函数 
    local ok = uri_router:dispatch(api_ctx.var.uri, match_opts, api_ctx)
    -- 没有命中路由就直接返回 404 
    if not ok then
        core.log.info("not find any matched route")
        return core.response.exit(404)
    end

    return true
end

```
### 加载插件
当然，如果路由可以命中，就会走到过滤插件和加载插件的步骤，这也是 API 网关的核心所在。我们先来看下面这段代码：


```
local plugins = core.tablepool.fetch("plugins", 32, 0)
-- etcd 中的插件列表和本地配置文件中的插件列表进行交集运算 
api_ctx.plugins = plugin.filter(route, plugins)

-- 依次运行插件在 rewrite 和 access 阶段挂载的函数 
run_plugin("rewrite", plugins, api_ctx)
run_plugin("access", plugins, api_ctx)
```
在这段代码中，我们首先通过 table pool 的方式，申请了一个长度为 32 的 table，这是我们之前介绍过的性能优化技巧。然后便是插件的过滤函数。你可能疑惑，为什么需要这一步呢？在插件的 init worker 阶段，我们不是已经从 etcd 中获取插件列表并完成排序了吗？事实上，这里的过滤是和本地配置文件来做对比的，主要有下面两个原因。

第一，新开发的插件需要灰度来发布，这时候新插件在 etcd 的列表中存在，但只在部分网关节点中处于开启状态。所以，我们需要额外做一次交集的运算。第二，为了支持 debug 模式。终端的请求经过了哪些插件的处理？这些插件的加载顺序是什么？这些信息在调试的时候会很有用，所以在过滤函数中也会判断其是否处于 debug 模式，并在响应头中记录下这些信息。

因此，在 access 阶段的最后，我们会把这些过滤好的插件，按照优先级逐个运行，如下面这段代码所示：


```
local function run_plugin(phase, plugins, api_ctx)
    for i = 1, #plugins, 2 do
        local phase_fun = plugins[i][phase]
        if phase_fun then
            -- 最核心的调用代码 
            phase_fun(plugins[i + 1], api_ctx)
        end
    end

    return api_ctx
end
```
你可以看到，在遍历插件的时候，我们是以 2 为间隔进行的，这是因为每个插件都会有两个部分组成：插件对象和插件的配置参数。现在，我们来看上面示例代码中最核心的那一行代码：

```
phase_fun(plugins[i + 1], api_ctx)
```
单独看这行代码会有些抽象，我们用一个具体的 limit_count 插件来替换一下，就会清楚很多：

```
limit_count_plugin_rewrite_function(conf_of_plugin, api_ctx)
```
到这里，API 网关的整体流程，我们就实现得差不多了。这些代码都在同一个代码[文件](https://github.com/apache/apisix/blob/master/apisix/init.lua)中，它里面有 400 多行代码，但核心的代码就是我们上面所介绍的这短短几十行。


### 编写插件
现在，距离一个完整的 demo 还差一件事情，那就是编写一个插件，让它可以跑起来。我们以 limit-count 这个限制请求数的插件为例，它的[完整实现](https://github.com/apache/apisix/blob/master/apisix/plugins/limit-count.lua)只有 60 多行代码，你可以点击链接查看。下面，我来详细讲解下其中的关键代码。首先，我们要引入 lua-resty-limit-traffic ，作为限制请求数的基础库：

```
local limit_count_new = require("resty.limit.count").new
```
然后，使用 rapidjson 中的 json schema ，来定义这个插件的参数有哪些：

```
local schema = {
    type = "object",
    properties = {
        count = {type = "integer", minimum = 0},
        time_window = {type = "integer", minimum = 0},
        key = {type = "string",
        enum = {"remote_addr", "server_addr"},
        },
        rejected_code = {type = "integer", minimum = 200, maximum = 600},
    },
    additionalProperties = false,
    required = {"count", "time_window", "key", "rejected_code"},
}
```
插件的这些参数，和大部分 resty.limit.count 的参数是对应的，其中包含了限制的 key、时间窗口的大小、限制的请求数。另外，插件中增加了一个参数: rejected_code，在请求被限速的时候返回指定的状态码。最后一步，我们把插件的处理函数挂载到 rewrite 阶段：


```
function _M.rewrite(conf, ctx)
    -- 从缓存中获取 limit count 的对象，如果没有就使用 `create_limit_obj` 函数新建并缓存 
    local lim, err = core.lrucache.plugin_ctx(plugin_name, ctx,  create_limit_obj, conf)

    -- 从 ctx.var 中获取 key 的值，并和配置类型和配置版本号一起组成新的 key 
    local key = (ctx.var[conf.key] or "") .. ctx.conf_type .. ctx.conf_version

    --  进入限制的判断函数
    local delay, remaining = lim:incoming(key, true)
    if not delay then
        local err = remaining
        -- 如果超过阈值，就返回指定的状态码 
        if err == "rejected" then
            return conf.rejected_code
        end

        core.log.error("failed to limit req: ", err)
        return 500
    end

    -- 如果没有超过阈值，就放行，并设置对应响应头 
    core.response.set_header("X-RateLimit-Limit", conf.count,
                             "X-RateLimit-Remaining", remaining)
end
```
上面的代码中，进行限制判断的逻辑只有一行，其他的都是来做准备工作和设置响应头的。如果没有超过阈值，就会继续按照优先级运行下一个插件。


### 写在最后
今天这节课，通过整体框架和插件的编写，我们就完成了一个 API 网关的 Demo。更进一步，利用本专栏学到的 OpenResty 知识，你可以在上面继续添砖加瓦，搭建更丰富的功能。

## 50 | 答疑（五）：如何在工作中引入 OpenResty？
几个月的时间转瞬即逝，到现在，OpenResty 专栏的最后一个版块微服务 API 网关篇，我们就已经学完了。恭喜你没有掉队，始终在积极学习和实践操作，并且热情地留下了你的思考。很多留言提出的问题很有价值，大部分我都已经在 App 里回复过，一些手机上不方便回复的或者比较典型、有趣的问题，我专门摘了出来，作为今天的答疑内容，集中回复。另一方面，也是为了保证所有人都不漏掉任何一个重点。下面我们来看今天的这 5 个问题。

问题一：OpenResty 在工作中的使用Q：快结课了，我也基本上跟下来了，但自己的实践还是偏少（工作中目前未用）。不过，这确实是很强大的一门课。感谢温老师的持续分享，后期工作中我也会择机引入。

A：感谢这位同学的认可，关于这条留言，我想聊一聊，如何在工作中引入 OpenResty，这确实是一个值得一谈的话题。OpenResty 基于 Nginx，并在它的基础之上加了 lua-nginx-module 的 C 模块和众多 lua-resty 库，所以 OpenResty 是可以无痛替换 Nginx 的，这是成本最低的开始使用 OpenResty 的方法。当然，这个替换过程也是有风险的，你需要注意下面这三点。


第一，确认线上 Nginx 的版本。OpenResty 的主版本号与 Nginx 保持一致，比如 OpenResty 1.15.8.1 使用的就是 Nginx 1.15.8 的内核。如果目前线上 Nginx 的版本号比 OpenResty 的最新版高，那么你最好谨慎换用 OpenResty，毕竟，OpenResty 升级的速度还是比较慢的，离 Nginx 的主线版本要落后半年到一年的时间。如果线上 Nginx 的版本和 OpenResty 的一致或者比 OpenResty 的低，那就具备了升级的前提条件。第二，测试。测试是最主要的一个环节，使用 OpenResty 替换 Nginx 的风险很低，但肯定也存在一些风险。比如，是否有自定义的 C 模块需要编译，OpenResty 依赖的 openssl 版本，以及 OpenResty 给 Nginx 打的 patch 是否对业务会造成影响等。你需要复制一些业务的流量过来做验证。第三，流量切换。基本的验证通过后，你还需要线上真实流量的灰度来验证，这时候为了能够快速的回滚，我们可以新开几台服务器来部署 OpenResty，而不是直接替换原有的 Nginx 服务。如果没有问题，我们可以选择二进制文件热升级的方式，或者是从 LB 中逐步摘掉和替换 Nginx 的方式来升级。

OpenResty 除了可以替代 Nginx 外，另外两个比较容易的切入点是 WAF 和 API 网关，它们都是对性能和动态有比较高要求的场景，也有对应的开源项目可以开箱即用，我在专栏中也有部分涉及到。再继续把 OpenResty 深入到业务层面的话，就需要考虑比较多技术之外的因素了，比如是否容易招聘到 OpenResty 相关的工程师？是否能够和公司原有的技术系统进行融合等等。总的来说，从替代 Nginx 的角度来切入，然后慢慢扩散来使用 OpenResty ，是一个不错的注意。

问题二：OpenResty 的数据库封装Q：根据你的指点，要尽量少用 ..字符串拼接，特别是在代码热区。但是我在处理数据库访问时，需要动态构建 SQL 语句（在语句中插入变量），这应该是非常常见的使用场景。可是对于这个需求，我目前感觉，只有字符串拼接是最简单的办法，其他真的想不到既简单又高性能的办法。


A：你可以先用我们前面课程介绍过的 SystemTap 或者其他工具分析下，看 SQL 语句的拼接是否是系统的瓶颈。如果不是，自然就没有优化的必要性，毕竟，过早的优化是万恶之源。如果瓶颈确实是  SQL 语句的拼接，那么我们可以利用数据库的 prepare 语句来做优化，也可以用数组的方式来做拼接。但 lua-resrty-mysql 对 prepare 的支持一直处于 TODO 状态，所以只剩下数组拼接的方式了。这也是一些 lua-resty 库的通病，实现了大部分的功能，处于能用的状态，但更新得并不够及时。除了数据库的 prepare 语句外，lua-resty-redis 对 cluster 也一直没有支持。字符串拼接，包括 lua-resty 库的这类问题，OpenResty 是希望用 DSL 来彻底解决的——使用编译器的技术自动生成数组来拼接字符串，把这些细节隐藏起来，上层的用户不用感知；使用小语言 wirelang 来自动生成各种 lua-resty 网络通信库，不再需要手写。这听上去很美好吧？但有一个问题必须正视，那就是自动生成的代码对人类是不友好的。如果你要学习或者修改生成的代码，就必须再学习编译器技术以及一门可能不会开源的 DSL，这会让参与社区的门槛越来越高。

问题三：OpenResty 的 Web 框架Q：我现在想用 OpenResty 做一个 Web 项目，但做起来很痛苦，主要是没找到成熟的框架，需要自己造很多轮子，就比如说上面的数据库操作问题（没找到可以动态构建 SQL 语句、连贯操作的类库）。所以想问下老师，在 Web 框架上有什么好的可以推荐吗？

A：在 awesome-resty 这个仓库中，我们可以看到有专门的 [Web 框架分类](https://github.com/bungle/awesome-resty#web-frameworks)，有 20 个 开源项目，不过大部分项目都处于停滞的状态。其中，Lapis、lor 和香草这三个项目你可以尝试下，看看哪一个更适合。确实，由于没有强大的 Web 框架作为支撑，OpenResty 在处理大项目的时候就会力不从心，这也是很少有人用 OpenResty 做业务系统的原因之一。

问题四：修改了响应体，怎么修改响应头中的 content-length？Q：如果需要修改 respones body 的内容，就只能在 body filter 里做修改，但这样会引起 body 长度与 content-length 长度不一致，应该如何处理呢？

A：在这种情况下，我们需要在 body filter 之前的 header filter 阶段中，把 content length 这个响应头置为 nil，不再返回，改为流式输出。下面是一段示例代码：

```
server {
    listen 8080;

    location /test {
            proxy_pass http://www.baidu.com;
            header_filter_by_lua_block {
                     ngx.header.content_length = nil
            }
            body_filter_by_lua_block {
                    ngx.arg[1] = ngx.arg[1] .. "abc"
            }
     }
}
```
通过这段代码你可以看到，在 body filter 阶段中，ngx.arg[1] 代表的就是响应体。如果我们在它后面增加了字符串 abc，响应头 content length 就不准确了，所以，我们在 header filter 阶段直接把它禁用掉就可以了。另外，从这个示例中，我们还可以看到 OpenResty 的各个阶段之间是如何来配合工作的，这一点也希望你注意并思考。

问题五：Lua 代码的查找路径Q：lua_package_path 似乎配置的是 Lua 依赖的搜索路径。对于content_by_lua_file，我试验发现，它只在 prefix 下根据指令提供的文件相对路径去搜索，而不会到 lua_package_path 下搜索。不知道我的理解对不对？

A：这位同学自己动手试验和思考的精神非常值得肯定，并且这个理解也是对的。lua_package_path 这个指令是用来加载 Lua 模块而使用的，比如我们在调用 require 'cjson' 时，就会到lua_package_path 中的指定目录中，去查找 cjson 这个模块。而 content_by_lua_file 则不同，它后面跟随的是磁盘中的一个文件路径：

```
location /test {
     content_by_lua_file /path/test.lua;
 }
```
而且，如果这里不是绝对路径而是相对路径：

```
 content_by_lua_file path/test.lua;
```
那么就会使用 OpenResty 启动时指定的 -p 目录，来做一个拼接，从而得到绝对路径。































































































































































































































































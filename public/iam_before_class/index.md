# iam_before_class


## 1 | IAM系统概述
### 项目背景：为什么选择 IAM 系统作为实战项目？
我们在做 Go 项目开发时，绕不开的一个话题是安全，如何保证 Go 应用的安全，是每个开发者都要解决的问题。虽然 Go 应用的安全包含很多方面，但大体可分为如下 2 类：
- 服务自身的安全：为了保证服务的安全，需要禁止非法用户访问服务。这可以通过服务器层面和软件层面来解决。服务器层面可以通过物理隔离、网络隔离、防火墙等技术从底层保证服务的安全性，软件层面可以通过 HTTPS、用户认证等手段来加强服务的安全性。服务器层面一般由运维团队来保障，软件层面则需要开发者来保障。
- 服务资源的安全：服务内有很多资源，为了避免非法访问，开发者要避免 UserA 访问到 UserB 的资源，也即需要对资源进行授权。通常，我们可以通过资源授权系统来对资源进行授权。

总的来说，为了保障 Go 应用的安全，我们需要对访问进行认证，对资源进行授权。那么，我们要如何实现访问认证和资源授权呢？认证功能不复杂，我们可以通过 JWT （JSON Web Token）认证来实现。授权功能比较复杂，授权功能的复杂性使得它可以囊括很多 Go 开发技能点。因此，在这个专栏中，我将认证和授权的功能实现升级为 IAM 系统，通过讲解它的构建过程，给你讲清楚 Go 项目开发的全部流程。

### IAM 系统是什么？
IAM（Identity and Access Management，身份识别与访问管理）系统是用 Go 语言编写的一个 Web 服务，用于给第三方用户提供访问控制服务。IAM 系统可以帮用户解决的问题是：在特定的条件下，谁能够 / 不能够对哪些资源做哪些操作（Who is able to do what on something given some context），也即完成资源授权功能。那么，IAM 系统是如何进行资源授权的呢？下面，我们通过 IAM 系统的资源授权的流程，来看下它是如何工作的，整个过程可以分为 4 步。![img](https://static001.geekbang.org/resource/image/ee/50/eed75fcd91d6e726ca74315d65193150.jpg?wh=2513*1134)

用户需要提供昵称、密码、邮箱、电话等信息注册并登录到 IAM 系统，这里是以用户名和密码作为唯一的身份标识来访问 IAM 系统，并且完成认证。因为访问 IAM 的资源授权接口是通过密钥（secretID/secretKey）的方式进行认证的，所以用户需要在 IAM 中创建属于自己的密钥资源。因为 IAM 通过授权策略完成授权，所以用户需要在 IAM 中创建授权策略。请求 IAM 提供的授权接口，IAM 会根据用户的请求内容和授权策略来决定一个授权请求是否被允许。

我们可以看到，在上面的流程中，IAM 使用到了 3 种系统资源：用户（User）、密钥（Secret）和策略（Policy），它们映射到程序设计中就是 3 种 RESTful 资源：用户（User）：实现对用户的增、删、改、查、修改密码、批量修改等操作。密钥（Secret）：实现对密钥的增、删、改、查操作。策略（Policy）：实现对策略的增、删、改、查、批量删除操作。

### IAM 系统的架构长啥样？
知道了 IAM 的功能之后，我们再来详细说说 IAM 系统的架构，架构图如下：

![img](https://static001.geekbang.org/resource/image/0a/42/0a5f6fd67af1eda1c690c8216dc5e042.jpg?wh=3197*2063)

总的来说，IAM 架构中包括 9 大组件和 3 大数据库。我将这些组件和功能都总结在下面的表格中。这里面，我们主要记住 5 个核心组件，包括 iam-apiserver、iam-authz-server、iam-pump、marmotedu-sdk-go 和 iamctl 的功能，还有 3 个数据库 Redis、MySQL 和 MongoDB 的功能。

![img](https://static001.geekbang.org/resource/image/6c/71/6cdbde36255c7fb2d4f2e718c9077a71.jpeg?wh=1920*1043)

此外，IAM 系统为存储数据使用到的 3 种数据库的说明如下所示。

![img](https://static001.geekbang.org/resource/image/e6/f2/e68c21e1991c74becc4b8a6a8bf5a8f2.jpeg?wh=1818*496)

### IAM 使用流程
第 1 步，创建平台资源。用户通过 iam-webconsole（RESTful API）或 iamctl（sdk marmotedu-sdk-go）客户端请求 iam-apiserver 提供的 RESTful API 接口完成用户、密钥、授权策略的增删改查，iam-apiserver 会将这些资源数据持久化存储在 MySQL 数据库中。而且，为了确保通信安全，客户端访问服务端都是通过 HTTPS 协议来访问的。

第 2 步，请求 API 完成资源授权。用户可以通过请求 iam-authz-server 提供的 /v1/authz 接口进行资源授权，请求 /v1/authz 接口需要通过密钥认证，认证通过后 /v1/authz 接口会查询授权策略，从而决定资源请求是否被允许。为了提高 /v1/authz 接口的性能，iam-authz-server 将密钥和策略信息缓存在内存中，以便实现快速查询。那密钥和策略信息是如何实现缓存的呢？首先，iam-authz-server 通过调用 iam-apiserver 提供的 gRPC 接口，将密钥和授权策略信息缓存到内存中。同时，为了使内存中的缓存信息和 iam-apiserver 中的信息保持一致，当 iam-apiserver 中有密钥或策略被更新时，iam-apiserver 会往特定的 Redis Channel（iam-authz-server 也会订阅该 Channel）中发送 PolicyChanged 和 SecretChanged 消息。这样一来，当 iam-authz-server 监听到有新消息时就会获取并解析消息，根据消息内容判断是否需要重新调用 gRPC 接来获取密钥和授权策略信息，再更新到内存中。

第 3 步，授权日志数据分析。iam-authz-server 会将授权日志上报到 Redis 高速缓存中，然后 iam-pump 组件会异步消费这些授权日志，再把清理后的数据保存在 MongoDB 中，供运营系统 iam-operating-system 查询。这里还有一点你要**注意**：iam-authz-server 将授权日志保存在 Redis 高性能 key-value 数据库中，可以最大化减少写入延时。不保存在内存中是因为授权日志量我们没法预测，当授权日志量很大时，很可能会将内存耗尽，造成服务中断。

第 4 步，运营平台授权数据展示。iam-operating-system 是 IAM 的运营系统，它可以通过查询 MongoDB 获取并展示运营数据，比如某个用户的授权 / 失败次数、授权失败时的授权信息等。此外，我们也可以通过 iam-operating-system 调用 iam-apiserver 服务来做些运营管理工作。比如，以上帝视角查看某个用户的授权策略供排障使用，或者调整用户可创建密钥的最大个数，再或者通过白名单的方式，让某个用户不受密钥个数限制的影响等等。

### IAM 软件架构模式
在设计软件时，我们首先要做的就是选择一种软件架构模式，它对软件后续的开发方式、软件维护成本都有比较大的影响。因此，这里我也会和你简单聊聊 2 种最常用的软件架构模式，分别是前后端分离架构和 MVC 架构。

#### 前后端分离架构
因为 IAM 系统采用的就是前后端分离的架构，所以我们就以 IAM 的运营系统 iam-operating-system 为例来详细说说这个架构。一般来说，运营系统的功能可多可少，对于一些具有复杂功能的运营系统，我们可以采用前后端分离的架构。其中，前端负责页面的展示以及数据的加载和渲染，后端只负责返回前端需要的数据。iam-operating-system 前后端分离架构如下图所示。

![img](https://static001.geekbang.org/resource/image/a2/76/a2e1f1cc135debd86611yya1f221c476.jpg?wh=2519*1447)

采用了前后端分离架构之后，当你通过浏览器请求前端 ops-webconsole 时，ops-webconsole 会先请求静态文件服务器加载静态文件，比如 HTML、CSS 和 JavaScript，然后它会执行 JavaScript，通过负载均衡请求后端数据，最后把后端返回的数据渲染到前端页面中。采用前后端分离的架构，让前后端通过 RESTful API 通信，会带来以下 5 点好处：可以让前、后端人员各自专注在自己业务的功能开发上，让专业的人做专业的事，来提高代码质量和开发效率前后端可并行开发和发布，这也能提高开发和发布效率，加快产品迭代速度前后端组件、代码分开，职责分明，可以增加代码的维护性和可读性，减少代码改动引起的 Bug 概率，同时也能快速定位 Bug前端 JavaScript 可以处理后台的数据，减少对后台服务器的压力可根据需要选择性水平扩容前端或者后端来节约成本

#### MVC 架构
但是，如果运营系统功能比较少，采用前后端分离框架的弊反而大于利，比如前后端分离要同时维护 2 个组件会导致部署更复杂，并且前后端分离将人员也分开了，这会增加一定程度的沟通成本。同时，因为代码中也需要实现前后端交互的逻辑，所以会引入一定的开发量。这个时候，我们可以尝试直接采用 MVC 软件架构，MVC 架构如下图所示。

![img](https://static001.geekbang.org/resource/image/a2/eb/a23b8ba92705710c694fd7cb99812feb.jpg?wh=1753*869)
MVC 的全名是 Model View Controller，它是一种架构模式，分为 Model、View、Controller 三层，每一层的功能如下：View（视图）：提供给用户的操作界面，用来处理数据的显示。Controller（控制器）：根据用户从 View 层输入的指令，选取 Model 层中的数据，然后对其进行相应的操作，产生最终结果。Model（模型）：应用程序中用于处理数据逻辑的部分。

MVC 架构的好处是通过控制器层将视图层和模型层分离之后，当更改视图层代码后时，我们就不需要重新编译控制器层和模型层的代码了。同样，如果业务流程发生改变也只需要变更模型层的代码就可以。在实际开发中为了更好的 UI 效果，视图层需要经常变更，但是通过 MVC 架构，在变更视图层时，我们根本不需要对业务逻辑层的代码做任何变化，这不仅减少了风险还能提高代码变更和发布的效率。

除此之外，还有一种跟 MVC 比较相似的软件开发架构叫三层架构，它包括 UI 层、BLL 层和 DAL 层。其中，UI 层表示用户界面，BLL 层表示业务逻辑，DAL 层表示数据访问。在实际开发中很多人将 MVC 当成三层架构在用，比如说，很多人喜欢把软件的业务逻辑放在 Controller 层里，将数据库访问操作的代码放在 Model 层里，软件最终的代码放在 View 层里，就这样硬生生将 MVC 架构变成了伪三层架构。这种代码不仅不伦不类，同时也失去了三层架构和 MVC 架构的核心优势，也就是：通过 Controller 层将 Model 层和 View 层解耦，从而使代码更容易维护和扩展。因此在实际开发中，我们也要**注意**遵循 MVC 架构的开发规范，发挥 MVC 的核心价值。

## 2 | 配置go开发环境

![img](https://static001.geekbang.org/resource/image/28/8e/28e52b697e735ecd58770c5fede7e58e.jpg?wh=2588*1894)

### Linux 服务器申请和配置
毫无疑问，要安装一个 Go 开发环境，你首先需要有一个 Linux 服务器。Linux 服务器有很多操作系统可供选择，例如：CentOS、Ubuntu、RHEL、Debian 等，但目前生产环境用得最多的还是 CentOS 系统，为了跟生产环境保持一致，我们选择当前最新的 CentOS 版本：CentOS 8.2。因为本专栏的所有操作都是在 CentOS 8.2 系统上进行的，为了避免环境不一致导致的操作失败，我**建议**你也使用 CentOS 8.2。安装一个 Linux 服务器需要两步：服务器申请和配置。

Linux 服务器申请：
我们可以通过以下 3 种方式来安装一个 CentOS 8.2 系统。在物理机上安装一个 CentOS 8.2 系统。在 Windows/MacBook 上安装虚拟机管理软件，用虚拟机管理软件创建 CentOS 8.2 虚拟机。其中，Windows **建议**用 VMWare Workstation 来创建虚拟机，MacBook **建议**用 VirtualBox 来创建虚拟机。在诸如腾讯云、阿里云、华为云等平台上购买一个虚拟机，并预装 CentOS 8.2 系统。

Linux 服务器配置：
申请完 Linux 服务之后，我们需要通过 SecureCRT 或 Xshell 等工具登录 Linux 服务器，并对服务器做一些简单必要的配置，包括创建普通用户、添加 sudoers、配置 $HOME/.bashrc 文件。接下来，我们一一来说。

第一步，用 Root 用户登录 Linux 系统，并创建普通用户。一般来说，一个项目会由多个开发人员协作完成，为了节省企业成本，公司不会给每个开发人员都配备一台服务器，而是让所有开发人员共用一个开发机，通过普通用户登录开发机进行开发。因此，为了模拟真实的企业开发环境，我们也通过一个普通用户的身份来进行项目的开发，创建方法如下：

```bash
# useradd going # 创建 going 用户，通过 going 用户登录开发机进行开发
# passwd going # 设置密码
Changing password for user going.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```
不仅如此，使用普通用户登录和操作开发机也可以保证系统的安全性，这是一个比较好的习惯，所以我们在日常开发中也要尽量避免使用 Root 用户。

第二步，添加 sudoers。我们知道很多时候，普通用户也要用到 Root 的一些权限，但 Root 用户的密码一般是由系统管理员维护并定期更改的，每次都向管理员询问密码又很麻烦。因此，我**建议**你将普通用户加入到 sudoers 中，这样普通用户就可以通过 sudo 命令来暂时获取 Root 的权限。具体来说，你可以执行如下命令添加：

```bash
sed -i '/^root.*ALL=(ALL).*ALL/a\going\tALL=(ALL) \tALL' /etc/sudoers
```
替换 CentOS 8.4 系统中自带的 Yum 源
由于 Red Hat 提前宣布 CentOS 8 于 2021 年 12 月 31 日停止维护，官方的 Yum 源已不可使用，所以需要切换官方的 Yum 源，这里选择阿里提供的 Yum 源。切换命令如下：
```bash
mv /etc/yum.repos.d /etc/yum.repos.d.bak # 先备份原有的 Yum 源
mkdir /etc/yum.repos.d 
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
yum clean all && yum makecache
```

第三步，用新的用户名（going）和密码登录 Linux 服务器。这一步也可以验证普通用户是否创建成功。第四步，配置 $HOME/.bashrc 文件。我们登录新服务器后的第一步就是配置 $HOME/.bashrc 文件，以使 Linux 登录 shell 更加易用，例如配置 LANG 解决中文乱码，配置 PS1 可以避免整行都是文件路径，并将 $HOME/bin 加入到 PATH 路径中。配置后的内容如下：

```
mkdir workspace
```

```sh
# .bashrc
 
# User specific aliases and functions
 
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
 
# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
 
# User specific environment
# Basic envs
export LANG="en_US.UTF-8" # 设置系统语言为 en_US.UTF-8，避免终端出现中文乱码
export PS1='[\u@dev \W]\$ ' # 默认的 PS1 设置会展示全部的路径，为了防止过长，这里只展示："用户名@dev 最后的目录名"
export WORKSPACE="$HOME/workspace" # 设置工作目录
export PATH=$HOME/bin:$PATH # 将 $HOME/bin 目录加入到 PATH 变量中
 
# Default entry folder
cd $WORKSPACE # 登录系统，默认进入 workspace 目录
```
有一点需要我们**注意**，在 export PATH 时，最好把 $PATH 放到最后，因为我们添加到目录中的命令是期望被优先搜索并使用的。配置完 $HOME/.bashrc 后，我们还需要创建工作目录 workspace。将工作文件统一放在 $HOME/workspace 目录中，有几点好处。可以使我们的$HOME目录保持整洁，便于以后的文件查找和分类。如果哪一天 /分区空间不足，可以将整个 workspace 目录 mv 到另一个分区中，并在 /分区中保留软连接，例如：/home/going/workspace -> /data/workspace/。如果哪天想备份所有的工作文件，可以直接备份 workspace。

具体的操作指令是$ mkdir -p $HOME/workspace。配置好 $HOME/.bashrc 文件后，我们就可以执行 bash 命令将配置加载到当前 shell 中了。



### 依赖安装和配置
在 Linux 系统上安装 IAM 系统会依赖一些 RPM 包和工具，有些是直接依赖，有些是间接依赖。为了避免后续的操作出现依赖错误，例如，因为包不存在而导致的编译、命令执行错误等，我们先统一依赖安装和配置。安装和配置步骤如下。

第一步，安装依赖。首先，我们在 CentOS 系统上通过 yum 命令来安装所需工具的依赖，安装命令如下：

```bash
sudo yum -y install make autoconf automake cmake perl-CPAN libcurl-devel libtool gcc gcc-c++ glibc-headers zlib-devel git-lfs telnet ctags lrzsz jq expat-devel openssl-devel
```

第二步，安装 Git。因为安装 IAM 系统、执行 go get 命令、安装 protobuf 工具等都是通过 Git 来操作的，所以接下来我们还需要安装 Git。由于低版本的 Git 不支持--unshallow 参数，而 go get 在安装 Go 包时会用到 git fetch --unshallow 命令，因此我们要确保安装一个高版本的 Git，具体的安装方法如下：

```bash
cd /tmp
wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.30.2.tar.gz
tar -xvzf git-2.30.2.tar.gz
cd git-2.30.2/
./configure
make
sudo make install
git --version          # 输出 git 版本号，说明安装成功
```
把 Git 的二进制目录添加到 PATH 路径中

```bash
tee -a $HOME/.bashrc <<'EOF'
# Configure for git
export PATH=/usr/local/libexec/git-core:$PATH
EOF
```
第三步，配置 Git。我们直接执行如下命令配置 Git：

```bash
git config --global user.name "QizhengZou"    # 用户名改成自己的
git config --global user.email "2838264218@qq.com"    # 邮箱改成自己的
git config --global credential.helper store    # 设置 Git，保存用户名和密码
git config --global core.longpaths true # 解决 Git 中 'Filename too long' 的错误
```
在 Git 中，我们会把非 ASCII 字符叫做 Unusual 字符。这类字符在 Git 输出到终端的时候默认是用 8 进制转义字符输出的（以防乱码），但现在的终端多数都支持直接显示非 ASCII 字符，所以我们可以关闭掉这个特性，具体的命令如下：

```bash
git config --global core.quotepath off
```
GitHub 限制最大只能克隆 100M 的单个文件，为了能够克隆大于 100M 的文件，我们还需要安装 Git Large File Storage，安装方式如下：

```bash
git lfs install --skip-repo
```



### Go 编译环境安装和配置
我们知道，Go 是一门编译型语言，所以在部署 IAM 系统之前，我们需要将代码编译成可执行的二进制文件。因此我们需要安装 Go 编译环境。除了 Go，我们也会用 gRPC 框架展示 RPC 通信协议的用法，所以我们也需要将 ProtoBuf 的.proto 文件编译成 Go 语言的接口。因此，我们也需要安装 ProtoBuf 的编译环境。



安装 Go 语言相对来说比较简单，我们只需要下载源码包、设置相应的环境变量即可。首先，我们从 Go 语言官方网站下载对应的 Go 安装包以及源码包，这里我下载的是 go1.17.2 版本：

```bash
wget https://golang.google.cn/dl/go1.17.2.linux-amd64.tar.gz -O /tmp/go1.17.2.linux-amd64.tar.gz
# 解压安装
mkdir -p $HOME/go
tar -xvzf /tmp/go1.17.2.linux-amd64.tar.gz -C $HOME/go
mv $HOME/go/go $HOME/go/go1.17.2
# 将下列环境变量追加到$HOME/.bashrc 文件中
tee -a $HOME/.bashrc <<'EOF'
# Go envs
export GOVERSION=go1.17.2 # Go 版本设置
export GO_INSTALL_DIR=$HOME/go # Go 安装目录
export GOROOT=$GO_INSTALL_DIR/$GOVERSION # GOROOT 设置
export GOPATH=$WORKSPACE/golang # GOPATH 设置
export PATH=$GOROOT/bin:$GOPATH/bin:$PATH # 将 Go 语言自带的和通过 go install 安装的二进制文件加入到 PATH 路径中
export GO111MODULE="on" # 开启 Go moudles 特性
export GOPROXY=https://goproxy.cn,direct # 安装 Go 模块时，代理服务器设置
export GOPRIVATE=
export GOSUMDB=off # 关闭校验 Go 依赖包的哈希值
EOF
```
Go 语言是通过一系列的环境变量来控制 Go 编译器行为的。因此，我们一定要理解每一个环境变量的含义。

![img](https://static001.geekbang.org/resource/image/4b/c1/4bde380dc05cd9900ec56dc7027c15c1.jpeg?wh=1920*1080)

因为 Go 以后会用 Go modules 来管理依赖，所以我**建议**你将 GO111MODULE 设置为 on。在使用模块的时候，$GOPATH 是无意义的，不过它还是会把下载的依赖储存在 $GOPATH/pkg/mod 目录中，也会把 go install 的二进制文件存放在 $GOPATH/bin 目录中。另外，我们还要将$GOPATH/bin、$GOROOT/bin 加入到 Linux 可执行文件搜索路径中。这样一来，我们就可以直接在 bash shell 中执行 go 自带的命令，以及通过 go install 安装的命令。最后就是进行测试了，如果我们执行 go version 命令可以成功输出 Go 的版本，就说明 Go 编译环境安装成功。具体的命令如下：

```bash
bash
go version
```

#### ProtoBuf 编译环境安装
接着，我们再来安装 protobuf 的编译器 protoc。protoc 需要 protoc-gen-go 来完成 Go 语言的代码转换，因此我们需要安装 protoc 和 protoc-gen-go 这 2 个工具。它们的安装方法比较简单，你直接看我下面给出的代码和操作注释就可以了。

```bash
# 第一步：安装 protobuf
cd /tmp/
git clone --depth=1 https://github.com/protocolbuffers/protobuf
cd protobuf
./autogen.sh
./configure
make
sudo make install
protoc --version # 查看 protoc 版本，成功输出版本号，说明安装成功
libprotoc 3.15.6

# 第二步：安装 protoc-gen-go
$ go get -u github.com/golang/protobuf/protoc-gen-go
```
### Go 开发 IDE 安装和配置
编译环境准备完之后，我们还需要一个代码编辑器才能开始 Go 项目开发，并且为了提高开发效率，我们需要将这个编辑器配置成 Go IDE。目前，GoLand、VSCode 这些 IDE 都很优秀，我们使用的也很多，但它们都是 Windows 系统下的 IDE。因此，在 Linux 环境下我们可以选择将 Vim 配置成 Go IDE，熟悉 Vim IDE 的操作之后，它的开发效率不输 GoLand 和 VSCode。比如说，我们可以通过 SpaceVim 将 Vim 配置成一个 Go IDE。SpaceVim 是一个社区驱动的模块化的 Vim IDE，以模块的方式组织管理插件以及相关配置， 为不同的语言开发量身定制了相关的开发模块，该模块提供代码自动补全、 语法检查、格式化、调试、REPL 等特性。我们只需要载入相关语言的模块就能得到一个开箱即用的 Vim IDE 了。Vim 可以选择 NeoVim，NeoVim 是基于 Vim 的一个 fork 分支，它主要解决了 Vim8 之前版本中的异步执行、开发模式等问题，对 Vim 的兼容性很好。同时对 vim 的代码进行了大量地清理和重构，去掉了对老旧系统的支持，添加了新的特性。

虽然 Vim8 后来也新增了异步执行等特性，在使用层面两者差异不大，但是**NeoVim 开发更激进，新特性更多，架构也相对更合理，所以我选择了 NeoVim**，你也可以根据个人爱好来选择（都是很优秀的编辑器，这里不做优缺点比较）。Vim IDE 的安装和配置主要分五步：

第一步，安装 NeoVim。我们直接执行 pip3 和 yum 命令安装即可，安装方法如下：

```bash
sudo pip3 install pynvim
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum -y install neovim
```
第二步，配置 $HOME/.bashrc。先配置 nvim 的别名为 vi，这样当我们执行 vi 时，Linux 系统就会默认调用 nvim。同时，配置 EDITOR 环境变量可以使一些工具，例如 Git 默认使用 nvim。配置方法如下：

```bash
tee -a $HOME/.bashrc <<'EOF'
# Configure for nvim
export EDITOR=nvim # 默认的编辑器（git 会用到）
alias vi="nvim"
EOF
```
第三步，检查 nvim 是否安装成功。我们可以通过查看 NeoVim 版本来确认是否成功安装，如果成功输出版本号，说明 NeoVim 安装成功。


```bash
bash
vi --version # 输出 NVIM v0.3.8 说明安装成功
NVIM v0.3.8
Build type: RelWithDebInfo
...
```
第四步，离线安装 SpaceVim。安装 SpaceVim 步骤稍微有点复杂，为了简化你的安装，同时消除网络的影响，我将安装和配置 SpaceVim 的步骤做成了一个离线安装包 [marmotVim](https://github.com/marmotedu/marmotVim) 。marmotVim 可以进行 SpaceVim 的安装、卸载、打包等操作，安装步骤如下：

```bash
cd /tmp
wget https://marmotedu-1254073058.cos.ap-beijing.myqcloud.com/tools/marmotVim.tar.gz
tar -xvzf marmotVim.tar.gz
cd marmotVim
./marmotVimCtl install
```
SpaceVim 配置文件为：$HOME/.SpaceVim.d/init.toml 和$HOME/.SpaceVim.d/autoload/custom_init.vim，你可自行配置（配置文件中有配置说明）：init.toml：SpaceVim 的配置文件custom_init.vim：兼容 vimrc，用户自定义的配置文件SpaceVim Go IDE 常用操作的按键映射如下表所示：

![img](https://static001.geekbang.org/resource/image/f1/d9/f1ec06569a411be4369byy7b8c7469d9.jpeg?wh=1920*1080)

第五步，Go 工具安装。SpaceVim 会用到一些 Go 工具，比如在函数跳转时会用到 guru、godef 工具，在格式化时会用到 goimports，所以我们也需要安装这些工具。安装方法有 2 种：Vim 底线命令安装：vi test.go，然后执行：:GoInstallBinaries 安装。拷贝工具：直接将整理好的工具文件拷贝到$GOPATH/bin 目录下。为了方便，你可以直接拷贝我已经打包好的 Go 工具到指定目录下：

```bash
cd /tmp
wget https://marmotedu-1254073058.cos.ap-beijing.myqcloud.com/tools/gotools-for-spacevim.tgz
mkdir -p $GOPATH/bin
tar -xvzf gotools-for-spacevim.tgz -C $GOPATH/bin
```

## 3 | 快速部署
总的来说，我把部署过程分成 2 大步。安装和配置数据库：我们需要安装和配置 MariaDB、Redis 和 MongoDB。安装和配置 IAM 服务：我们需要安装和配置 iam-apiserver、iam-authz-server、iam-pump、iamctl 和 man 文件。

[一键部署（卸载） IAM 系统](https://github.com/marmotedu/iam/blob/master/docs/guide/zh-CN/installation/README.md)：

```bash

$ mkdir -p $WORKSPACE/golang/src/github.com/marmotedu
$ cd $WORKSPACE/golang/src/github.com/marmotedu
$ git clone --depth=1 https://github.com/marmotedu/iam

# 善用 alias，将常用操作配置成 alias，方便以后操作

$ tee -a $HOME/.bashrc << 'EOF'
# Alias for quick access
export GOWORK="$WORKSPACE/golang/src"
export IAM_ROOT="$GOWORK/github.com/marmotedu/iam"
export LINUX_PASSWORD='iam59!z$'
alias mm="cd $GOWORK/github.com/marmotedu"
alias i="cd $GOWORK/github.com/marmotedu/iam"
EOF
$ bash
```

### 安装和配置数据库
因为 IAM 系统用到了 MariaDB、Redis、MongoDB 数据库来存储数据，而 IAM 服务在启动时会先尝试连接这些数据库，所以为了避免启动时连接数据库失败，这里我们先来安装需要的数据库。

安装和配置 MariaDBIAM：
IAM 会把 REST 资源的定义信息存储在关系型数据库中，关系型数据库我选择了 MariaDB。为啥选择 MariaDB，而不是 MySQL 呢？。选择 MariaDB 一方面是因为它是发展最快的 MySQL 分支，相比 MySQL，它加入了很多新的特性，并且它能够完全兼容 MySQL，包括 API 和命令行。另一方面是因为 MariaDB 是开源的，而且迭代速度很快。首先，我们可以通过以下命令安装和配置 MariaDB，并将 Root 密码设置为 iam59!z$：

```bash
$ cd $IAM_ROOT
$ ./scripts/install/mariadb.sh iam::mariadb::install
$ mysql -h127.0.0.1 -uroot -p'iam59!z$'
MariaDB [(none)]>
```


安装和配置 Redis：
在 IAM 系统中，由于 iam-authz-server 是从 iam-apiserver 拉取并缓存用户的密钥 / 策略信息的，因此同一份密钥 / 策略数据会分别存在 2 个服务中，这可能会出现数据不一致的情况。数据不一致会带来一些问题，例如当我们通过 iam-apiserver 创建了一对密钥，但是这对密钥还没有被 iam-authz-server 缓存，这时候通过这对密钥访问 iam-authz-server 就会访问失败。为了保证数据的一致性，我们可以使用 Redis 的发布订阅 (pub/sub) 功能进行消息通知。同时，iam-authz-server 也会将授权审计日志缓存到 Redis 中，所以也需要安装 Redis key-value 数据库。我们可以通过以下命令来安装和配置 Redis，并将 Redis 的初始密码设置为 iam59!z$ ：

```bash
$ cd $IAM_ROOT
$ ./scripts/install/redis.sh iam::redis::install
$ redis-cli -h 127.0.0.1 -p 6379 -a 'iam59!z$' # 连接 Redis，-h 指定主机，-p 指定监听端口，-a 指定登录密码
```
安装和配置 MongoDB因为 iam-pump 会将 iam-authz-server 产生的数据处理后存储在 MongoDB 中，所以我们也需要安装 MongoDB 数据库。主要分两步安装：首先安装 MongoDB，然后再创建 MongoDB 账号。

第 1 步，安装 MongoDB
首先，我们可以通过以下 4 步来安装 MongoDB。配置 MongoDB yum 源，并安装 MongoDB。CentOS8.x 系统默认没有配置安装 MongoDB 需要的 yum 源，所以我们需要先配置好 yum 源再安装：

```bash
$ sudo tee /etc/yum.repos.d/mongodb-org-4.4.repo<<'EOF'
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
EOF

$ sudo yum install -y mongodb-org
```

关闭 SELinux。在安装的过程中，SELinux 有可能会阻止 MongoDB 访问 /sys/fs/cgroup，所以我们还需要关闭 SELinux：

```bash
$ sudo setenforce 0
$ sudo sed -i 's/^SELINUX=.*$/SELINUX=disabled/' /etc/selinux/config # 永久关闭 SELINUX
```

开启外网访问权限和登录验证。MongoDB 安装完之后，默认情况下是不会开启外网访问权限和登录验证，为了方便使用，我**建议**你先开启这些功能，执行如下命令开启：

```bash
$ sudo sed -i '/bindIp/{s/127.0.0.1/0.0.0.0/}' /etc/mongod.conf
$ sudo sed -i '/^#security/a\security:\n  authorization: enabled' /etc/mongod.conf
```
配置完 MongoDB 之后，我们就可以启动它了，具体的命令如下：

```bash
$ sudo systemctl start mongod
$ sudo systemctl enable mongod # 设置开机启动
$ sudo systemctl status mongod # 查看 mongod 运行状态，如果输出中包含 active (running)字样说明 mongod 成功启动
# 登陆
$ mongo --quiet "mongodb://127.0.0.1:27017"
>
```

第 2 步，创建 MongoDB 账号安装完 MongoDB 之后，默认是没有用户账号的，为了方便 IAM 服务使用，我们需要先创建好管理员账号，通过管理员账户登录 MongoDB，我们可以执行创建普通用户、数据库等操作。创建管理员账户。首先，我们通过 use admin 指令切换到 admin 数据库，再通过 db.auth("用户名"，"用户密码") 验证用户登录权限。如果返回 1 表示验证成功；如果返回 0 表示验证失败。具体的命令如下：

```bash
$ mongo --quiet "mongodb://127.0.0.1:27017"
> use admin
switched to db admin
> db.createUser({user:"root",pwd:"iam59!z$",roles:["root"]})
Successfully added user: { "user" : "root", "roles" : [ "root" ] }
> db.auth("root", "iam59!z$")
1
```
此外，如果想删除用户，可以使用 db.dropUser("用户名") 命令。db.createUser 用到了以下 3 个参数。user: 用户名。pwd: 用户密码。roles: 用来设置用户的权限，比如读、读写、写等。

因为 admin 用户具有 MongoDB 的 Root 权限，权限过大安全性会降低。为了提高安全性，我们还需要创建一个 iam 普通用户来连接和操作 MongoDB。

```bash
$ mongo --quiet mongodb://root:'iam59!z$'@127.0.0.1:27017/iam_analytics?authSource=admin # 用管理员账户连接 MongoDB
> use iam_analytics
switched to db iam_analytics
> db.createUser({user:"iam",pwd:"iam59!z$",roles:["dbOwner"]})
Successfully added user: { "user" : "iam", "roles" : [ "dbOwner" ] }
> db.auth("iam", "iam59!z$")
1

# 用iam用户登陆
$ mongo --quiet mongodb://iam:'iam59!z$'@127.0.0.1:27017/iam_analytics?authSource=iam_analytics
```
### 安装和配置 IAM 系统

![img](https://static001.geekbang.org/resource/image/05/ae/05de7498aaba1fddd1ae6ec3cb5b2fae.jpg?wh=2778*1741)

要想完成 IAM 系统的安装，我们还需要安装和配置 iam-apiserver、iam-authz-server、iam-pump 和 iamctl。


#### 准备工作
5步：初始化 MariaDB 数据库，创建 iam 数据库。配置 scripts/install/environment.sh。创建需要的目录。创建 CA 根证书和密钥。配置 hosts。


第 1 步，初始化 MariaDB 数据库，创建 iam 数据库。安装完 MariaDB 数据库之后，我们需要在 MariaDB 数据库中创建 IAM 系统需要的数据库、表和存储过程，以及创建 SQL 语句保存在 IAM 代码仓库中的 configs/iam.sql 文件中。具体的创建步骤如下。登录数据库并创建 iam 用户。

```bash
$ cd $IAM_ROOT
$ mysql -h127.0.0.1 -P3306 -uroot -p'iam59!z$' # 连接 MariaDB，-h 指定主机，-P 指定监听端口，-u 指定登录用户，-p 指定登录密码
MariaDB [(none)]> grant all on iam.* TO iam@127.0.0.1 identified by 'iam59!z$';
Query OK, 0 rows affected (0.000 sec)
MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.000 sec)
```
用 iam 用户登录 MariaDB，执行 iam.sql 文件，创建 iam 数据库。

```bash
$ mysql -h127.0.0.1 -P3306 -uiam -p'iam59!z$'
MariaDB [(none)]> source configs/iam.sql;
MariaDB [iam]> show databases;
+--------------------+
| Database           |
+--------------------+
| iam                |
| information_schema |
+--------------------+
2 rows in set (0.000 sec)
```
上面的命令会创建 iam 数据库，并创建以下数据库资源。表：user 是用户表，用来存放用户信息；secret 是密钥表，用来存放密钥信息；policy 是策略表，用来存放授权策略信息；policy_audit 是策略历史表，被删除的策略会被转存到该表。admin 用户：在 user 表中，我们需要创建一个管理员用户，用户名是 admin，密码是 Admin@2021。存储过程：删除用户时会自动删除该用户所属的密钥和策略信息。

第 2 步，配置 scripts/install/environment.sh。IAM 组件的安装配置都是通过环境变量文件 scripts/install/environment.sh 进行配置的，所以我们要先配置好 scripts/install/environment.sh 文件。这里，你可以直接使用默认值，提高你的安装效率。

第 3 步，创建需要的目录。在安装和运行 IAM 系统的时候，我们需要将配置、二进制文件和数据文件存放到指定的目录。所以我们需要先创建好这些目录，创建步骤如下。

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ sudo mkdir -p ${IAM_DATA_DIR}/{iam-apiserver,iam-authz-server,iam-pump} # 创建 Systemd WorkingDirectory 目录
$ sudo mkdir -p ${IAM_INSTALL_DIR}/bin #创建 IAM 系统安装目录
$ sudo mkdir -p ${IAM_CONFIG_DIR}/cert # 创建 IAM 系统配置文件存放目录
$ sudo mkdir -p ${IAM_LOG_DIR} # 创建 IAM 日志文件存放目录
```


第 4 步， 创建 CA 根证书和密钥。为了确保安全，IAM 系统各组件需要使用 x509 证书对通信进行加密和认证。所以，这里我们需要先创建 CA 证书。CA 根证书是所有组件共享的，只需要创建一个 CA 证书，后续创建的所有证书都由它签名。我们可以使用 CloudFlare 的 PKI 工具集 cfssl 来创建所有的证书。安装 cfssl 工具集。我们可以直接安装 cfssl 已经编译好的二进制文件，cfssl 工具集中包含很多工具，这里我们需要安装 cfssl、cfssljson、cfssl-certinfo，功能如下。cfssl：证书签发工具。cfssljson：将 cfssl 生成的证书（json 格式）变为文件承载式证书。这两个工具的安装方法如下：

```bash
$ cd $IAM_ROOT
$ ./scripts/install/install.sh iam::install::install_cfssl
```
创建配置文件。CA 配置文件是用来配置根证书的使用场景 (profile) 和具体参数 (usage、过期时间、服务端认证、客户端认证、加密等)，可以在签名其它证书时用来指定特定场景：

```bash
$ cd $IAM_ROOT
$ tee ca-config.json << EOF
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "iam": {
        "usages": [
          "signing",
          "key encipherment",
          "server auth",
          "client auth"
        ],
        "expiry": "876000h"
      }
    }
  }
}
EOF
```

上面的 JSON 配置中，有一些字段解释如下。signing：表示该证书可用于签名其它证书（生成的 ca.pem 证书中 CA=TRUE）。server auth：表示 client 可以用该证书对 server 提供的证书进行验证。client auth：表示 server 可以用该证书对 client 提供的证书进行验证。expiry：876000h，证书有效期设置为 100 年。

创建证书签名请求文件。我们创建用来生成 CA 证书签名请求（CSR）的 JSON 配置文件：

```bash
$ cd $IAM_ROOT
$ tee ca-csr.json << EOF
{
  "CN": "iam-ca",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "BeiJing",
      "L": "BeiJing",
      "O": "marmotedu",
      "OU": "iam"
    }
  ],
  "ca": {
    "expiry": "876000h"
  }
}
EOF
```
上面的 JSON 配置中，有一些字段解释如下。C：Country，国家。ST：State，省份。L：Locality (L) or City，城市。CN：Common Name，iam-apiserver 从证书中提取该字段作为请求的用户名 (User Name) ，浏览器使用该字段验证网站是否合法。O：Organization，iam-apiserver 从证书中提取该字段作为请求用户所属的组 (Group)。OU：Company division (or Organization Unit – OU)，部门 / 单位。

除此之外，还有两点需要我们**注意**。不同证书 csr 文件的 CN、C、ST、L、O、OU 组合必须不同，否则可能出现 PEER'S CERTIFICATE HAS AN INVALID SIGNATURE 错误。后续创建证书的 csr 文件时，CN、OU 都不相同（C、ST、L、O 相同），以达到区分的目的。

创建 CA 证书和私钥 首先，我们通过 cfssl gencert 命令来创建：

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ cfssl gencert -initca ca-csr.json | cfssljson -bare ca
$ ls ca*
ca-config.json  ca.csr  ca-csr.json  ca-key.pem  ca.pem
$ sudo mv ca* ${IAM_CONFIG_DIR}/cert # 需要将证书文件拷贝到指定文件夹下（分发证书），方便各组件引用
```
上述命令会创建运行 CA 所必需的文件 ca-key.pem（私钥）和 ca.pem（证书），还会生成 ca.csr（证书签名请求），用于交叉签名或重新签名。创建完之后，我们可以通过 cfssl certinfo 命名查看 cert 和 csr 信息：

```bash
$ cfssl certinfo -cert ${IAM_CONFIG_DIR}/cert/ca.pem # 查看 cert(证书信息)
$ cfssl certinfo -csr ${IAM_CONFIG_DIR}/cert/ca.csr # 查看 CSR(证书签名请求)信息
```
第 5 步，配置 hosts。iam 通过域名访问 API 接口，因为这些域名没有注册过，还不能在互联网上解析，所以需要配置 hosts，具体的操作如下：

```bash
$ sudo tee -a /etc/hosts <<EOF
127.0.0.1 iam.api.marmotedu.com
127.0.0.1 iam.authz.marmotedu.com
EOF
```
#### 安装和配置 iam-apiserver
完成了准备工作之后，我们就可以安装 IAM 系统的各个组件了。首先我们通过以下 3 步来安装 iam-apiserver 服务。

第 1 步，创建 iam-apiserver 证书和私钥。其它服务为了安全都是通过 HTTPS 协议访问 iam-apiserver，所以我们要先创建 iam-apiserver 证书和私钥。创建证书签名请求：

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ tee iam-apiserver-csr.json <<EOF
{
  "CN": "iam-apiserver",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "BeiJing",
      "L": "BeiJing",
      "O": "marmotedu",
      "OU": "iam-apiserver"
    }
  ],
  "hosts": [
    "127.0.0.1",
    "localhost",
    "iam.api.marmotedu.com"
  ]
}
EOF
```
代码中的 hosts 字段是用来指定授权使用该证书的 IP 和域名列表，上面的 hosts 列出了 iam-apiserver 服务的 IP 和域名。

生成证书和私钥：

```bash
$ cfssl gencert -ca=${IAM_CONFIG_DIR}/cert/ca.pem \
  -ca-key=${IAM_CONFIG_DIR}/cert/ca-key.pem \
  -config=${IAM_CONFIG_DIR}/cert/ca-config.json \
  -profile=iam iam-apiserver-csr.json | cfssljson -bare iam-apiserver
$ sudo mv iam-apiserver*pem ${IAM_CONFIG_DIR}/cert # 将生成的证书和私钥文件拷贝到配置文件目录
```

第 2 步，安装并运行 iam-apiserver。iam-apiserver 作为 iam 系统的核心组件，需要第一个安装。安装 iam-apiserver 可执行程序：

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ make build BINS=iam-apiserver
$ sudo cp _output/platforms/linux/amd64/iam-apiserver ${IAM_INSTALL_DIR}/bin

# 生成并安装 iam-apiserver 的配置文件（iam-apiserver.yaml）
$ ./scripts/genconfig.sh scripts/install/environment.sh configs/iam-apiserver.yaml > iam-apiserver.yaml
$ sudo mv iam-apiserver.yaml ${IAM_CONFIG_DIR}
# 创建并安装 iam-apiserver systemd unit 文件：
$ ./scripts/genconfig.sh scripts/install/environment.sh init/iam-apiserver.service > iam-apiserver.service
$ sudo mv iam-apiserver.service /etc/systemd/system/
# 启动 iam-apiserver 服务：
$ sudo systemctl daemon-reload
$ sudo systemctl enable iam-apiserver
$ sudo systemctl restart iam-apiserver
$ systemctl status iam-apiserver # 查看 iam-apiserver 运行状态，如果输出中包含 active (running)字样说明 iam-apiserver 成功启动
```

第 3 步，测试 iam-apiserver 是否成功安装。测试 iam-apiserver 主要是测试 RESTful 资源的 CURD：用户 CURD、密钥 CURD、授权策略 CURD。首先，我们需要获取访问 iam-apiserver 的 Token，请求如下 API 访问：

```bash
$ curl -s -XPOST -H'Content-Type: application/json' -d'{"username":"admin","password":"Admin@2021"}' http://127.0.0.1:8080/login | jq -r .token
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA
```
代码中下面的 HTTP 请求通过-H'Authorization: Bearer ' 指定认证头信息，将上面请求的 Token 替换 。

用户 CURD  创建用户、列出用户、获取用户详细信息、修改用户、删除单个用户、批量删除用户，请求方法如下：

```bash
# 创建用户
$ curl -s -XPOST -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' -d'{"password":"User@2021","metadata":{"name":"colin"},"nickname":"colin","email":"colin@foxmail.com","phone":"1812884xxxx"}' http://127.0.0.1:8080/v1/users

# 列出用户
$ curl -s -XGET -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' 'http://127.0.0.1:8080/v1/users?offset=0&limit=10'

# 获取 colin 用户的详细信息
$ curl -s -XGET -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/users/colin

# 修改 colin 用户
$ curl -s -XPUT -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' -d'{"nickname":"colin","email":"colin_modified@foxmail.com","phone":"1812884xxxx"}' http://127.0.0.1:8080/v1/users/colin

# 删除 colin 用户
$ curl -s -XDELETE -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/users/colin

# 批量删除用户
$ curl -s -XDELETE -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' 'http://127.0.0.1:8080/v1/users?name=colin&name=mark&name=john'
```

密钥 CURD创建密钥、列出密钥、获取密钥详细信息、修改密钥、删除密钥请求方法如下：

```bash
# 创建 secret0 密钥
$ curl -s -XPOST -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' -d'{"metadata":{"name":"secret0"},"expires":0,"description":"admin secret"}' http://127.0.0.1:8080/v1/secrets

# 列出所有密钥
$ curl -s -XGET -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/secrets

# 获取 secret0 密钥的详细信息
$ curl -s -XGET -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/secrets/secret0

# 修改 secret0 密钥
$ curl -s -XPUT -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' -d'{"metadata":{"name":"secret0"},"expires":0,"description":"admin secret(modified)"}' http://127.0.0.1:8080/v1/secrets/secret0

# 删除 secret0 密钥
$ curl -s -XDELETE -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/secrets/secret0
```
这里我们要**注意**，因为密钥属于重要资源，被删除会导致所有的访问请求失败，所以密钥不支持批量删除。

授权策略 CURD创建策略、列出策略、获取策略详细信息、修改策略、删除策略请求方法如下：

```bash
# 创建策略
$ curl -s -XPOST -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' -d'{"metadata":{"name":"policy0"},"policy":{"description":"One policy to rule them all.","subjects":["users:<peter|ken>","users:maria","groups:admins"],"actions":["delete","<create|update>"],"effect":"allow","resources":["resources:articles:<.*>","resources:printer"],"conditions":{"remoteIPAddress":{"type":"CIDRCondition","options":{"cidr":"192.168.0.1/16"}}}}}' http://127.0.0.1:8080/v1/policies

# 列出所有策略
$ curl -s -XGET -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/policies

# 获取 policy0 策略的详细信息
$ curl -s -XGET -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/policies/policy0

# 修改 policy0 策略
$ curl -s -XPUT -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' -d'{"metadata":{"name":"policy0"},"policy":{"description":"One policy to rule them all(modified).","subjects":["users:<peter|ken>","users:maria","groups:admins"],"actions":["delete","<create|update>"],"effect":"allow","resources":["resources:articles:<.*>","resources:printer"],"conditions":{"remoteIPAddress":{"type":"CIDRCondition","options":{"cidr":"192.168.0.1/16"}}}}}' http://127.0.0.1:8080/v1/policies/policy0

# 删除 policy0 策略
$ curl -s -XDELETE -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MTc5MjI4OTQsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MTc4MzY0OTQsInN1YiI6ImFkbWluIn0.9qztVJseQ9XwqOFVUHNOtG96-KUovndz0SSr_QBsxAA' http://127.0.0.1:8080/v1/policies/policy0

```


#### 安装 iamctl
上面，我们安装了 iam 系统的 API 服务。但是想要访问 iam 服务，我们还需要安装客户端工具 iamctl。具体来说，我们可以通过 3 步完成 iamctl 的安装和配置。

第 1 步，创建 iamctl 证书和私钥。iamctl 使用 https 协议与 iam-apiserver 进行安全通信，iam-apiserver 对 iamctl 请求包含的证书进行认证和授权。iamctl 后续用于 iam 系统访问和管理，所以这里创建具有最高权限的 admin 证书。创建证书签名请求。下面创建的证书只会被 iamctl 当作 client 证书使用，所以 hosts 字段为空。代码如下：

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ cat > admin-csr.json <<EOF
{
  "CN": "admin",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "BeiJing",
      "L": "BeiJing",
      "O": "marmotedu",
      "OU": "iamctl"
    }
  ],
  "hosts": []
}
EOF
# 生成证书和私钥：
$ cfssl gencert -ca=${IAM_CONFIG_DIR}/cert/ca.pem \
  -ca-key=${IAM_CONFIG_DIR}/cert/ca-key.pem \
  -config=${IAM_CONFIG_DIR}/cert/ca-config.json \
  -profile=iam admin-csr.json | cfssljson -bare admin
$ mkdir -p $(dirname ${CONFIG_USER_CLIENT_CERTIFICATE}) $(dirname ${CONFIG_USER_CLIENT_KEY}) # 创建客户端证书存放的目录
$ mv admin.pem ${CONFIG_USER_CLIENT_CERTIFICATE} # 安装 TLS 的客户端证书
$ mv admin-key.pem ${CONFIG_USER_CLIENT_KEY} # 安装 TLS 的客户端私钥文件
```

第 2 步，安装 iamctl。iamctl 是 IAM 系统的客户端工具，其安装位置和 iam-apiserver、iam-authz-server、iam-pump 位置不同，为了能够在 shell 下直接运行 iamctl 命令，我们需要将 iamctl 安装到$HOME/bin 下，同时将 iamctl 的配置存放在默认加载的目录下：$HOME/.iam。主要分 2 步进行。安装 iamctl 可执行程序：

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ make build BINS=iamctl
$ cp _output/platforms/linux/amd64/iamctl $HOME/bin
# 生成并安装 iamctl 的配置文件（iamctl.yaml）：
$ ./scripts/genconfig.sh scripts/install/environment.sh configs/iamctl.yaml> iamctl.yaml
$ mkdir -p $HOME/.iam
$ mv iamctl.yaml $HOME/.iam
```
因为 iamctl 是一个客户端工具，可能会在多台机器上运行。为了简化部署 iamctl 工具的复杂度，我们可以把 config 配置文件中跟 CA 认证相关的 CA 文件内容用 base64 加密后，放置在 config 配置文件中。具体的思路就是把 config 文件中的配置项 client-certificate、client-key、certificate-authority 分别用如下配置项替换 client-certificate-data、client-key-data、certificate-authority-data。这些配置项的值可以通过对 CA 文件使用 base64 加密获得。假如，certificate-authority 值为/etc/iam/cert/ca.pem，则 certificate-authority-data 的值为 cat "/etc/iam/cert/ca.pem" | base64 | tr -d '\r\n'，其它-data 变量的值类似。这样当我们再部署 iamctl 工具时，只需要拷贝 iamctl 和配置文件，而不用再拷贝 CA 文件了。

第 3 步，测试 iamctl 是否成功安装。执行 iamctl user list 可以列出预创建的 admin 用户，如下所示：
```bash
[going@dev workspace]$ iamctl user list
NAME    NICKNAME   EMAIL               PHONE         CREATED               UPDATED
admin  admin     admin@foxmail.com  1812884xxxx  2021-05-27 18:01:40  2022-07-07 16:32:18
```
#### 安装和配置 iam-authz-server
接下来，我们需要安装另外一个核心组件：iam-authz-server，可以通过以下 3 步来安装。

第 1 步，创建 iam-authz-server 证书和私钥。创建证书签名请求：

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ tee iam-authz-server-csr.json <<EOF
{
  "CN": "iam-authz-server",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "BeiJing",
      "L": "BeiJing",
      "O": "marmotedu",
      "OU": "iam-authz-server"
    }
  ],
  "hosts": [
    "127.0.0.1",
    "localhost",
    "iam.authz.marmotedu.com"
  ]
}
EOF
```
代码中的 hosts 字段指定授权使用该证书的 IP 和域名列表，上面的 hosts 列出了 iam-authz-server 服务的 IP 和域名。生成证书和私钥：

```bash
$ cfssl gencert -ca=${IAM_CONFIG_DIR}/cert/ca.pem \
  -ca-key=${IAM_CONFIG_DIR}/cert/ca-key.pem \
  -config=${IAM_CONFIG_DIR}/cert/ca-config.json \
  -profile=iam iam-authz-server-csr.json | cfssljson -bare iam-authz-server
$ sudo mv iam-authz-server*pem ${IAM_CONFIG_DIR}/cert # 将生成的证书和私钥文件拷贝到配置文件目录
```
第 2 步，安装并运行 iam-authz-server。安装 iam-authz-server 步骤和安装 iam-apiserver 步骤基本一样，也需要 4 步。安装 iam-authz-server 可执行程序：

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ make build BINS=iam-authz-server
$ sudo cp _output/platforms/linux/amd64/iam-authz-server ${IAM_INSTALL_DIR}/bin
# 生成并安装 iam-authz-server 的配置文件（iam-authz-server.yaml）：
$ ./scripts/genconfig.sh scripts/install/environment.sh configs/iam-authz-server.yaml > iam-authz-server.yaml
$ sudo mv iam-authz-server.yaml ${IAM_CONFIG_DIR}
# 创建并安装 iam-authz-server systemd unit 文件：
$ ./scripts/genconfig.sh scripts/install/environment.sh init/iam-authz-server.service > iam-authz-server.service
$ sudo mv iam-authz-server.service /etc/systemd/system/
# 启动 iam-authz-server 服务：
$ sudo systemctl daemon-reload
$ sudo systemctl enable iam-authz-server
$ sudo systemctl restart iam-authz-server
$ systemctl status iam-authz-server # 查看 iam-authz-server 运行状态，如果输出中包含 active (running)字样说明 iam-authz-server 成功启动。
```

第 3 步，测试 iam-authz-server 是否成功安装。重新登陆系统，并获取访问令牌

```bash
$ token=`curl -s -XPOST -H'Content-Type: application/json' -d'{"username":"admin","password":"Admin@2021"}' http://127.0.0.1:8080/login | jq -r .token`

# 创建授权策略
$ curl -s -XPOST -H"Content-Type: application/json" -H"Authorization: Bearer $token" -d'{"metadata":{"name":"authztest"},"policy":{"description":"One policy to rule them all.","subjects":["users:<peter|ken>","users:maria","groups:admins"],"actions":["delete","<create|update>"],"effect":"allow","resources":["resources:articles:<.*>","resources:printer"],"conditions":{"remoteIPAddress":{"type":"CIDRCondition","options":{"cidr":"192.168.0.1/16"}}}}}' http://127.0.0.1:8080/v1/policies

# 创建密钥，并从命令的输出中提取 secretID 和 secretKey
$ curl -s -XPOST -H"Content-Type: application/json" -H"Authorization: Bearer $token" -d'{"metadata":{"name":"authztest"},"expires":0,"description":"admin secret"}' http://127.0.0.1:8080/v1/secrets
{"metadata":{"id":23,"name":"authztest","createdAt":"2021-04-08T07:24:50.071671422+08:00","updatedAt":"2021-04-08T07:24:50.071671422+08:00"},"username":"admin","secretID":"ZuxvXNfG08BdEMqkTaP41L2DLArlE6Jpqoox","secretKey":"7Sfa5EfAPIwcTLGCfSvqLf0zZGCjF3l8","expires":0,"description":"admin secret"}

# 生成访问 iam-authz-server 的 token  iamctl 提供了 jwt sigin 命令，可以根据 secretID 和 secretKey 签发 Token，方便你使用。
$ iamctl jwt sign ZuxvXNfG08BdEMqkTaP41L2DLArlE6Jpqoox 7Sfa5EfAPIwcTLGCfSvqLf0zZGCjF3l8 # iamctl jwt sign $secretID $secretKey
eyJhbGciOiJIUzI1NiIsImtpZCI6Ilp1eHZYTmZHMDhCZEVNcWtUYVA0MUwyRExBcmxFNkpwcW9veCIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYxNzg0NTE5NSwiaWF0IjoxNjE3ODM3OTk1LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MTc4Mzc5OTV9.za9yLM7lHVabPAlVQLCqXEaf8sTU6sodAsMXnmpXjMQ

# 如果你的开发过程中有些重复性的操作，为了方便使用，也可以将这些操作以 iamctl 子命令的方式集成到 iamctl 命令行中。

# 测试资源授权是否通过我们可以通过请求 /v1/authz 来完成资源授权：如果授权通过会返回：{"allowed":true} 。
$ curl -s -XPOST -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsImtpZCI6Ilp1eHZYTmZHMDhCZEVNcWtUYVA0MUwyRExBcmxFNkpwcW9veCIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYxNzg0NTE5NSwiaWF0IjoxNjE3ODM3OTk1LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MTc4Mzc5OTV9.za9yLM7lHVabPAlVQLCqXEaf8sTU6sodAsMXnmpXjMQ' -d'{"subject":"users:maria","action":"delete","resource":"resources:articles:ladon-introduction","context":{"remoteIPAddress":"192.168.0.5"}}' http://127.0.0.1:9090/v1/authz
{"allowed":true}
```
#### 安装和配置 iam-pump
安装 iam-pump 步骤和安装 iam-apiserver、iam-authz-server 步骤基本一样，具体步骤如下。第 1 步，安装 iam-pump 可执行程序。

```bash
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ make build BINS=iam-pump
$ sudo cp _output/platforms/linux/amd64/iam-pump ${IAM_INSTALL_DIR}/bin

# 生成并安装 iam-pump 的配置文件（iam-pump.yaml）。
$ ./scripts/genconfig.sh scripts/install/environment.sh configs/iam-pump.yaml > iam-pump.yaml
$ sudo mv iam-pump.yaml ${IAM_CONFIG_DIR}

# 创建并安装 iam-pump systemd unit 文件。
$ ./scripts/genconfig.sh scripts/install/environment.sh init/iam-pump.service > iam-pump.service
$ sudo mv iam-pump.service /etc/systemd/system/

# 启动 iam-pump 服务。
$ sudo systemctl daemon-reload
$ sudo systemctl enable iam-pump
$ sudo systemctl restart iam-pump
$ systemctl status iam-pump # 查看 iam-pump 运行状态，如果输出中包含 active (running)字样说明 iam-pump 成功启动。

# 测试 iam-pump 是否成功安装。
$ curl http://127.0.0.1:7070/healthz
{"status": "ok"}
```
#### 安装 man 文件
IAM 系统通过组合调用包：github.com/cpuguy83/go-md2man/v2/md2man 和 github.com/spf13/cobra 的相关函数生成了各个组件的 man1 文件，主要分 3 步实现。
```bash
# 第 1 步，生成各个组件的 man1 文件。
$ cd $IAM_ROOT
$ ./scripts/update-generated-docs.sh
# 第 2 步，安装生成的 man1 文件。
$ sudo cp docs/man/man1/* /usr/share/man/man1/
# 第 3 步，检查是否成功安装 man1 文件。
$ man iam-apiserver
```
执行 man iam-apiserver 命令后，会弹出 man 文档界面



IAM 系统所有组件都已经安装成功了，你可以通过 iamctl version 查看客户端和服务端版本，代码如下：

```bash
$ iamctl version -o yaml
clientVersion:
  buildDate: "2021-04-08T01:56:20Z"
  compiler: gc
  gitCommit: 1d682b0317396347b568a3ef366c1c54b3b0186b
  gitTreeState: dirty
  gitVersion: v0.6.1-5-g1d682b0
  goVersion: go1.16.2
  platform: linux/amd64
serverVersion:
  buildDate: "2021-04-07T22:30:53Z"
  compiler: gc
  gitCommit: bde163964b8c004ebb20ca4abd8a2ac0cd1f71ad
  gitTreeState: dirty
  gitVersion: bde1639
  goVersion: go1.16.2
  platform: linux/amd64
```




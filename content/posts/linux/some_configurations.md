---
title: "Some_configurations"
date: 2022-07-02T14:15:21+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---

## 1
申请完 Linux 服务之后，我们需要通过 SecureCRT 或 Xshell 等工具登录 Linux 服务器，并对服务器做一些简单必要的配置，包括创建普通用户、添加 sudoers、配置 $HOME/.bashrc 文件

第一步，用 Root 用户登录 Linux 系统，并创建普通用户。

第二步，添加 sudoers。

```bash
sed -i '/^root.*ALL=(ALL).*ALL/a\going\tALL=(ALL) \tALL' /etc/sudoers
```
第三步，用新的用户名（going）和密码登录 Linux 服务器

第四步，配置 $HOME/.bashrc 文件。

我们登录新服务器后的第一步就是配置 $HOME/.bashrc 文件，以使 Linux 登录 shell 更加易用，例如配置 LANG 解决中文乱码，配置 PS1 可以避免整行都是文件路径，并将 $HOME/bin 加入到 PATH 路径中。配置后的内容如下：

```bash
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
有一点需要我们**注意**，在 export PATH 时，最好把 $PATH 放到最后，因为我们添加到目录中的命令是期望被优先搜索并使用的。配置完 $HOME/.bashrc 后，我们还需要创建工作目录 workspace。将工作文件统一放在 $HOME/workspace 目录中，有几点好处。

可以使我们的$HOME目录保持整洁，便于以后的文件查找和分类。如果哪一天 /分区空间不足，可以将整个 workspace 目录 mv 到另一个分区中，并在 /分区中保留软连接，例如：/home/going/workspace -> /data/workspace/。如果哪天想备份所有的工作文件，可以直接备份 workspace。具体的操作指令是$ mkdir -p $HOME/workspace。配置好 $HOME/.bashrc 文件后，我们就可以执行 bash 命令将配置加载到当前 shell 中了。至此，我们就完成了 Linux 开发机环境的申请及初步配置。

## 安装一些依赖
```bash
sudo yum -y install make autoconf automake cmake perl-CPAN libcurl-devel libtool gcc gcc-c++ glibc-headers zlib-devel git-lfs telnet ctags lrzsz jq expat-devel openssl-devel
```
## 安装 Git
由于低版本的 Git 不支持--unshallow 参数，而 go get 在安装 Go 包时会用到 git fetch --unshallow 命令，因此我们要确保安装一个高版本的 Git，具体的安装方法如下：
```bash
$ cd /tmp
$ wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.30.2.tar.gz
$ tar -xvzf git-2.30.2.tar.gz
$ cd git-2.30.2/
$ ./configure
$ make
$ sudo make install
$ git --version          # 输出 git 版本号，说明安装成功
git version 2.30.2
```
把 Git 的二进制目录添加到 PATH 路径中，不然 Git 可能会因为找不到一些命令而报错。你可以通过执行以下命令添加目录：
```bash
tee -a $HOME/.bashrc <<'EOF'
# Configure for git
export PATH=/usr/local/libexec/git-core:$PATH
EOF
```
git 配置：
```
$ git config --global user.name "Lingfei Kong"    # 用户名改成自己的
$ git config --global user.email "colin404@foxmail.com"    # 邮箱改成自己的
$ git config --global credential.helper store    # 设置 Git，保存用户名和密码
$ git config --global core.longpaths true # 解决 Git 中 'Filename too long' 的错误
```
在 Git 中，我们会把非 ASCII 字符叫做 Unusual 字符。这类字符在 Git 输出到终端的时候默认是用 8 进制转义字符输出的（以防乱码），但现在的终端多数都支持直接显示非 ASCII 字符，所以我们可以关闭掉这个特性，具体的命令如下：
```
$ git config --global core.quotepath off
```
替换镜像：
```bash
$ git config --global url."https://github.com.cnpmjs.org/".insteadOf "https://github.com/"
```
GitHub 限制最大只能克隆 100M 的单个文件，为了能够克隆大于 100M 的文件，我们还需要安装 Git Large File Storage，安装方式如下：
```
$ git lfs install --skip-repo
```
## go配置
```
$ wget https://golang.google.cn/dl/go1.17.2.linux-amd64.tar.gz -O /tmp/go1.17.2.linux-amd64.tar.gz
$ mkdir -p $HOME/go
$ tar -xvzf /tmp/go1.17.2.linux-amd64.tar.gz -C $HOME/go
$ mv $HOME/go/go $HOME/go/go1.17.2
```
将环境变量追加到$HOME/.bashrc 文件中。
```
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
![img](D:\Blog\localImages\4bde380dc05cd9900ec56dc7027c15c1.jpeg)
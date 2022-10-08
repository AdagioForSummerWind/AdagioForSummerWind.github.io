# Gb_20100420

# 第三方库：goprotobuf 及其他

安德鲁·杰朗  
2010 年 4 月 20 日

3 月 24 日，Rob Pike 发布[了 goprotobuf](http://code.google.com/p/goprotobuf/)，即 Google 数据交换格式[Protocol Buffers](http://code.google.com/apis/protocolbuffers/docs/overview.html)的 Go 绑定，简称 protobufs。在此公告中，Go 加入了 C++、Java 和 Python 作为提供官方 protobuf 实现的语言。这标志着实现现有系统与 Go 内置系统之间的互操作性的重要里程碑。

goprotobuf 项目由两部分组成：一个“协议编译器插件”，它生成 Go 源文件，一旦编译，就可以访问和管理协议缓冲区；和一个 Go 包，它实现了对编码（编组）、解码（解组）和访问协议缓冲区的运行时支持。

要使用 goprotobuf，首先需要安装 Go 和[protobuf](http://code.google.com/p/protobuf/)。然后，您可以使用[goinstall](https://go.dev/cmd/goinstall/)安装“proto”包：

```
goinstall goprotobuf.googlecode.com/hg/proto
```

然后安装 protobuf 编译器插件：

```
cd $GOROOT/src/pkg/goprotobuf.googlecode.com/hg/compiler
make install
```

有关更多详细信息，请参阅项目的[README](http://code.google.com/p/goprotobuf/source/browse/README)文件。

这是越来越多的第三方[Go 项目之一](http://godashboard.appspot.com/package)。自 goprotobuf 发布以来，X Go 绑定已经从标准库分离到[x-go-binding](http://code.google.com/p/x-go-binding/)项目，并且已经开始在[Freetype](http://www.freetype.org/)端口 [freetype-go](http://code.google.com/p/freetype-go/)上工作。其他流行的第三方项目包括轻量级 Web 框架 [web.go](http://github.com/hoisie/web.go)和 Go GTK 绑定[gtk-go](http://github.com/mattn/go-gtk)。

我们希望鼓励开源社区开发其他有用的软件包。如果您正在做某事，请不要一个人独享 - 通过我们的邮件列表[golang-nuts](http://groups.google.com/group/golang-nuts)让我们知道。

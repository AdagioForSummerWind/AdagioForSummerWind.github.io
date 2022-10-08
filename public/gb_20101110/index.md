# Gb_20101110

# 去：一年前的今天

安德鲁·杰朗  
2010 年 11 月 10 日

2009 年 11 月 10 日，我们启动了 Go 项目：一种专注于简单性和效率的开源编程语言。在过去的一年里，Go 项目本身及其社区都取得了许多进展。

我们着手构建一种用于系统编程的语言——人们通常可以用 C 或 C++ 编写的那种程序——我们对 Go 作为通用语言的实用性感到惊讶。我们曾预料到 C、C++ 和 Java 程序员会对此感兴趣，但 Python 和 JavaScript 等动态类型语言用户的兴趣却是出乎意料的。Go 的原生编译、静态类型、内存管理和轻量级语法的组合似乎引起了编程社区的广泛关注。

该横截面发展成为一个由热情的 Go 程序员组成的专门社区。我们的[邮件列表](http://groups.google.com/group/golang-nuts)有 3,800 多名成员，每个月大约有 1,500 个帖子。该项目有超过 130 名贡献者（提交代码或文档的人），自启动以来的 2,800 次提交中，近三分之一是由核心团队以外的程序员贡献的。为了使所有代码成型，我们的[开发邮件列表](http://groups.google.com/group/golang-dev)上交换了近 14,000 封电子邮件。

这些数字反映了其成果在项目代码库中显而易见的劳动。编译器得到了显着改进，代码生成速度更快、效率更高，修复了一百多个报告的错误，并支持更广泛的操作系统和架构。由于有一群专门的贡献者（其中一个成为我们第一个非谷歌项目的提交者），Windows 移植即将完成。ARM 移植也取得了长足的进步，最近达到了通过所有测试的里程碑。

Go 工具集得到了扩展和改进。Go 文档工具[godoc](https://go.dev/cmd/godoc/)现在支持其他源代码树的文档（您可以浏览和搜索自己的代码）并提供[“代码漫游”](https://go.dev/doc/codewalk/) 界面来展示教程材料（以及更多改进）。 [Goinstall](https://go.dev/cmd/goinstall/)是一个新的包管理工具，允许用户使用单个命令安装和更新外部包。 [Go 漂亮的打印机Gofmt](https://go.dev/cmd/gofmt/)现在尽可能简化语法。 [Goplay](https://go.dev/misc/goplay/)是一种基于 Web 的“即用型编译”工具，在您无法访问[Go Playground](https://go.dev/doc/play/)的时候，它是一种便捷的 Go 实验方式。

标准库增加了超过 42,000 行代码，包括 20 个新[包](https://go.dev/pkg/)。新增内容包括[jpeg](https://go.dev/pkg/image/jpeg/)、 [jsonrpc](https://go.dev/pkg/rpc/jsonrpc/)、 [mime](https://go.dev/pkg/mime/)、[netchan](https://go.dev/pkg/netchan/)和[smtp](https://go.dev/pkg/smtp/)包，以及大量新的[加密](https://go.dev/pkg/crypto/)包。更一般地说，随着我们对 Go 习语理解的加深，标准库一直在不断完善和修订。

调试故事也变得更好了。最近对 gc 编译器的 DWARF 输出的改进使 GNU 调试器 GDB 对 Go 二进制文件有用，我们正在积极努力使调试信息更加完整。（有关详细信息，请参阅[最近的博客文章](https://blog.golang.org/2010/11/debugging-go-code-status-report.html)。）

现在比以往任何时候都更容易链接到用 Go 以外的语言编写的现有库。Go 支持在最新的[SWIG](http://www.swig.org/)版本 2.0.1 中，使得链接 C 和 C++ 代码变得更加容易，我们的[cgo](https://go.dev/cmd/cgo/)工具已经看到了许多修复和改进。

[Gccgo](https://go.dev/doc/install/gccgo)是 GNU C 编译器的 Go 前端，作为并行 Go 实现与 gc 编译器保持同步。它现在有一个工作垃圾收集器，并已被 GCC 核心接受。我们现在正致力于将[gofrontend](http://code.google.com/p/gofrontend/) 作为 BSD 许可的 Go 编译器前端提供，与 GCC 完全分离。

在 Go 项目本身之外，Go 开始被用于构建真正的软件。我们的[项目仪表板上列出了 200 多个 Go 程序和库，](http://godashboard.appspot.com/project) [Google Code](http://code.google.com/hosting/search?q=label:Go) 和[Github](https://github.com/search?q=language:Go)上还有数百个。在我们的邮件列表和 IRC 频道上，您可以找到来自世界各地使用 Go 进行编程项目的程序员。（有关 真实示例，请参阅我们上个月的[客座博客文章。）在 Google 内部，有几个团队选择 Go 来构建生产软件，我们收到了来自其他正在使用 Go 开发大型系统的公司的报告。](https://blog.golang.org/2010/10/real-go-projects-smarttwitter-and-webgo.html)我们还与几位使用 Go 作为教学语言的教育工作者取得了联系。

语言本身也已经成长和成熟。在过去的一年中，我们收到了许多功能请求。但是 Go 是一门小型语言，我们一直在努力确保任何新功能都能在简单性和实用性之间取得正确的折衷。自发布以来，我们已经进行了许多语言更改，其中许多更改是由社区的反馈推动的。

- 分号现在在几乎所有情况下都是可选的。[规格](https://go.dev/doc/go_spec.html#Semicolons)
- 新的内置功能`copy`，`append`使切片的管理更加高效和直接。 [规格](https://go.dev/doc/go_spec.html#Appending_and_copying_slices)
- 制作子切片时可以省略上限和下限。这意味着 that`s[:]`是 的简写`s[0:len(s)]`。 [规格](https://go.dev/doc/go_spec.html#Slices)
- 新的内置函数`recover`补充`panic`并`defer`作为错误处理机制。 [博客](https://blog.golang.org/2010/08/defer-panic-and-recover.html), [规范](https://go.dev/doc/go_spec.html#Handling_panics)
- 新的复数类型（`complex`、 `complex64`和`complex128`）简化了某些数学运算。 [规范](https://go.dev/doc/go_spec.html#Complex_numbers), [规范](https://go.dev/doc/go_spec.html#Imaginary_literals)
- 复合文字语法允许省略冗余类型信息（例如，在指定二维数组时）。 [release.2010-10-27](https://go.dev/doc/devel/release.html#2010-10-27) , [规范](https://go.dev/doc/go_spec.html#Composite_literals)
- 现在指定 了可变函数参数 ( `...T`) 及其传播 ( ) 的一般语法。[规格](https://go.dev/doc/go_spec.html#Function_Types), [规格](https://go.dev/doc/go_spec.html#Passing_arguments_to_..._parameters), [发布.2010-09-29](https://go.dev/doc/devel/release.html#2010-09-29)`v...`[](https://go.dev/doc/go_spec.html#Function_Types)[](https://go.dev/doc/go_spec.html#Passing_arguments_to_..._parameters)[](https://go.dev/doc/devel/release.html#2010-09-29)

Go 肯定已准备好用于生产环境，但仍有改进的空间。我们近期的重点是让 Go 程序在高性能系统的上下文中更快、更高效。这意味着改进垃圾收集器、优化生成的代码和改进核心库。我们还在探索对类型系统的一些进一步添加，以使泛型编程更容易。一年发生了很多事；这既令人兴奋又令人满意。我们希望来年比去年更富有成效。

_如果你一直想要 \[回到\] Go，那么现在是这样做的好时机！查看_ [_文档_](https://go.dev/doc/docs.html) _和_ [_入门页面以获取更多信息，或者只是在_](https://go.dev/doc/install.html) [_Go Playground_](https://go.dev/doc/play/)_中发疯_ 。


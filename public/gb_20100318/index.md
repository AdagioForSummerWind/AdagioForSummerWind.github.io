# Gb_20100318

# Go：2010 年 3 月的新增功能

安德鲁·杰朗  
2010 年 3 月 18 日

欢迎来到官方 Go 博客。我们 Go 团队希望使用这个博客让世界了解 Go 编程语言的发展以及围绕它的库和应用程序不断增长的生态系统。

自我们推出（去年 11 月）以来已经过去了几个月，所以让我们来谈谈从那时起 Go World 中发生了什么。

Google 的核心团队继续开发语言、编译器、包、工具和文档。编译器现在生成的代码在某些情况下比发布时快 2 倍到一个数量级。我们整理了一些[基准测试](http://godashboard.appspot.com/benchmarks)图表，[构建状态](http://godashboard.appspot.com/)页面跟踪提交到存储库的每个变更集的可靠性。

我们对语法进行了更改，以使语言更加简洁、规则和灵活。分号[几乎已从](http://groups.google.com/group/golang-nuts/t/5ee32b588d10f2e9)语言中完全删除。[…T 语法](https://go.dev/doc/go_spec.html#Function_types) 使处理任意数量的类型化函数参数变得更简单。语法 x\[lo:\] 现在是 x\[lo:len(x)\] 的简写。Go 现在也原生支持复数。有关更多信息，请参阅[发行说明](https://go.dev/doc/devel/release.html)。

[Godoc](https://go.dev/cmd/godoc/)现在为第三方库提供了更好的支持，并且发布了一个新工具 - [goinstall](https://go.dev/cmd/goinstall) - 以便于安装它们。此外，我们已经开始开发包裹跟踪系统，以便更轻松地找到您需要的东西。[您可以在Packages 页面](http://godashboard.appspot.com/package)上查看此内容的开头。

超过 40,000 行代码已添加到[标准库](https://go.dev/pkg/)中，其中包括许多全新的包，其中相当一部分是由外部贡献者编写的。

说到第三方，自从我们的[邮件列表](http://groups.google.com/group/golang-nuts/)和 irc 频道（freenode 上的#go-nuts）推出以来，一个充满活力的社区蓬勃发展。我们已经正式为该项目增加了 50 多人。他们的贡献范围从错误修复和文档更正到核心包和对其他操作系统的支持（Go 现在在 FreeBSD 下得到支持，并且[Windows 移植](http://code.google.com/p/go/wiki/WindowsPort)正在进行中）。我们将这些社区贡献视为迄今为止我们取得的最大成功。

我们也收到了一些好评。[PC World 最近的这篇文章](http://www.pcworld.idg.com.au/article/337773/google_go_captures_developers_imaginations/)总结 了围绕该项目的热情。几位博主已经开始记录他们的语言体验（例如，请参见[此处](http://golang.tumblr.com/)、 [此处](http://www.infi.nl/blog/view/id/47)和[此处](http://freecella.blogspot.com/2010/01/gospecify-basic-setup-of-projects.html) ）。我们用户的普遍反应非常积极；一位初来乍到的人说：[“我离开时印象非常深刻。Go 在简单和强大之间走一条优雅的线。”](https://groups.google.com/group/golang-nuts/browse_thread/thread/5fabdd59f8562ed2)

至于未来：我们听取了无数声音告诉我们他们需要什么，现在正专注于让 Go 为黄金时段做好准备。我们正在改进垃圾收集器、运行时调度程序、工具和标准库，并探索新的语言特性。2010 年对于 Go 来说将是激动人心的一年，我们期待与社区合作，使其取得成功。

# Gb_20100804

# 推迟、恐慌和恢复

安德鲁·杰朗  
2010 年 8 月 4 日

Go 具有通常的控制流机制：if、for、switch、goto。它还具有在单独的 goroutine 中运行代码的 go 语句。在这里，我想讨论一些不太常见的问题：延迟、恐慌和恢复。

**defer 语句**将函数调用推送到列表中。保存的调用列表在周围函数返回后执行。Defer 通常用于简化执行各种清理操作的函数。

例如，让我们看一个打开两个文件并将一个文件的内容复制到另一个文件的函数：

```
func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }

    written, err = io.Copy(dst, src)
    dst.Close()
    src.Close()
    return
}
```

这有效，但有一个错误。如果调用 os.Create 失败，函数将返回而不关闭源文件。这可以通过在第二个 return 语句之前调用 src.Close 来轻松解决，但如果函数更复杂，问题可能不会那么容易被注意到和解决。通过引入 defer 语句，我们可以确保文件始终关闭：

```
func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }
    defer src.Close()

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }
    defer dst.Close()

    return io.Copy(dst, src)
}
```

Defer 语句允许我们考虑在打开每个文件后立即关闭它，从而保证无论函数中返回语句的数量如何，文件_都将_被关闭。

defer 语句的行为是直接且可预测的。有三个简单的规则：

1. _评估 defer 语句时评估延迟函数的参数。_

在这个例子中，当 Println 调用被延迟时，表达式“i”被计算。函数返回后，延迟调用将打印“0”。

```
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
```

2. _延迟函数调用在周围函数返回后按后进先出顺序执行。_

此函数打印“3210”：

```
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}
```

3. _延迟函数可以读取并分配给返回函数的命名返回值。_

在此示例中，延迟函数 在周围函数返回_后递增返回值 i。_因此，此函数返回 2：

```
func c() (i int) {
    defer func() { i++ }()
    return 1
}
```

这样方便修改函数的错误返回值；我们很快就会看到一个例子。

**Panic**是一个内置函数，可以停止普通的控制流程并开始_恐慌_。当函数 F 调用 panic 时，F 的执行停止，F 中的所有延迟函数都正常执行，然后 F 返回其调用者。对调用者来说，F 的行为就像是对恐慌的调用。该进程继续向上堆栈，直到当前 goroutine 中的所有函数都返回，此时程序崩溃。恐慌可以通过直接调用恐慌来启动。它们也可能是由运行时错误引起的，例如越界数组访问。

**Recover**是一个内置函数，可以重新控制恐慌的 goroutine。恢复仅在延迟函数中有用。在正常执行期间，recover 调用将返回 nil 并且没有其他效果。如果当前的 goroutine 正在恐慌，对 recovery 的调用将捕获为恐慌提供的值并恢复正常执行。

这是一个演示恐慌和延迟机制的示例程序：

```
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}
```

函数 g 接受 int i，如果 i 大于 3 则恐慌，否则它使用参数 i+1 调用自身。函数 f 延迟了一个调用 recover 并打印恢复值的函数（如果它不是 nil）。在继续阅读之前，试着想象一下这个程序的输出可能是什么。

该程序将输出：

```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
Recovered in f 4
Returned normally from f.
```

如果我们从 f 中删除延迟函数，则恐慌不会恢复并到达 goroutine 调用堆栈的顶部，从而终止程序。这个修改后的程序将输出：

```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
panic: 4

panic PC=0x2a9cd8
[stack trace omitted]
```

有关**panic**和**recover**的真实示例，请参阅 Go 标准库中的[json 包](https://go.dev/pkg/encoding/json/)。它使用一组递归函数对接口进行编码。如果在遍历该值时发生错误，则会调用panic将堆栈展开到顶层函数调用，该函数调用会从panic中恢复并返回适当的错误值（参见encodeState类型的'error'和'marshal'方法在[encode.go](https://go.dev/src/pkg/encoding/json/encode.go)中）。

Go 库中的约定是，即使包在内部使用了 panic，它的外部 API 仍然会显示显式的错误返回值。

**defer**的其他用途（除了前面给出的 file.Close 示例）包括释放互斥锁：

```
mu.Lock()
defer mu.Unlock()
```

打印页脚：

```
printHeader()
defer printFooter()
```

和更多。

总之，defer 语句（有或没有恐慌和恢复）为控制流提供了一种不同寻常且强大的机制。它可用于对其他编程语言中由专用结构实现的许多功能进行建模。试试看。


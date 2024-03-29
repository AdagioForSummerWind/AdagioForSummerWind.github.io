---
title: "Go_base_03"
date: 2022-01-06T09:16:34+08:00
lastmod: 2022-01-06
tags: [go grammar]
categories: [Go]
slug: Go_Function
draft: true
---
> 参考学习go语言中文网、C语言中文网、golang官方文档等

# 函数
## 函数定义
**go的函数特点：**
- 无需声明原型。
- 支持不定参、变参。
- 支持**多返回值**。
- 支持命名返回参数。 
- 支持匿名函数和闭包。（闭包详见后文）
- 函数也是一种类型，一个函数可以赋值给变量。
- 有返回值的函数，必须有明确的终止语句，否则会引发编译错误。
- 不支持 嵌套 (nested) 一个包不能有两个名字一样的函数。
- 不支持 重载 (overload) （**区别于重写**，重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。最常用的地方就是构造器的重载。）
- 不支持 默认参数 (default parameter)。 
- 没有函数体的函数声明，表示该函数不是以Go实现的。这样的声明定义了函数标识符。
- **所有参数都是值传递：slice，map，channel 会有传引⽤的错觉**
    - string、切片、map 这些类型它们的内存表示对应的是它们数据内容的“描述符”。当这些类型作为实参类型时，值传递拷贝的也是它们数据内容的“描述符”，不包括数据内容本身，所以这些类型传递的开销是固定的，与数据内容大小无关。这种只拷贝“描述符”，不拷贝实际数据内容的拷贝过程，也被称为“浅拷贝”。
    - **当函数的形参为接口类型，或者形参是变长参数时，简单的值传递就不能满足要求了**，这时 Go 编译器会介入：对于类型为接口类型的形参，Go 编译器会把传递的实参赋值给对应的接口类型形参；对于为变长参数的形参，Go 编译器会将零个或多个实参按一定形式转换为对应的变长形参。
        - 在 Go 中，变长参数实际上是通过切片来实现的。所以，我们在函数体中，就可以使用切片支持的所有操作来操作变长参数
- 关于函数的返回值：
    - Go 标准库以及大多数项目代码中的函数，都选择了使用普通的非具名返回值形式。但在一些特定场景下，具名返回值也会得到应用。比如，当函数使用 defer，而且还在 defer 函数中修改外部函数返回值时，具名返回值可以让代码显得更优雅清晰。当函数的返回值个数较多时，每次显式使用 return 语句时都会接一长串返回值，这时，我们用具名返回值可以让函数实现的可读性更好一些
```go
// $GOROOT/src/time/format.go
func parseNanoseconds(value string, nbytes int) (ns int, rangeErrString string, err error) {
    if !commaOrPeriod(value[0]) {
        err = errBad
        return
    }
    if ns, err = atoi(value[1:nbytes]); err != nil {
        return
    }
    if ns < 0 || 1e9 <= ns {
        rangeErrString = "fractional second"
        return
    }

    scaleDigits := 10 - nbytes
    for i := 0; i < scaleDigits; i++ {
        ns *= 10
    }
    return
}
```
**函数是第一类对象，可作为参数传递。建议将复杂签名定义为函数类型，以便于阅读。**
```go
import "fmt"

func test(fn func() int) int {
    return fn()
}
// 定义函数类型。
type FormatFunc func(s string, x, y int) string 

func format(fn FormatFunc, s string, x, y int) string {
    return fn(s, x, y)
}

func main() {
    s1 := test(func() int { return 100 }) // 直接将匿名函数当参数。

    s2 := format(func(s string, x, y int) string {
        return fmt.Sprintf(s, x, y)
    }, "%d, %d", 10, 20)

    println(s1, s2)
}

output:
100 10,20
```
fmt里的一些格式化输入输出函数：
```go
func Printf(format string, a ...interface{}) (n int, err error)
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error)
func Sprintf(format string, a ...interface{}) string
func Print(a ...interface{}) (n int, err error)
func Fprint(w io.Writer, a ...interface{}) (n int, err error)
func Sprint(a ...interface{}) string
func Println(a ...interface{}) (n int, err error)
func Fprintln(w io.Writer, a ...interface{}) (n int, err error)
func Sprintln(a ...interface{}) string
func Errorf(format string, a ...interface{}) error
func Scanf(format string, a ...interface{}) (n int, err error)
func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error)
func Sscanf(str string, format string, a ...interface{}) (n int, err error)
func Scan(a ...interface{}) (n int, err error)
func Fscan(r io.Reader, a ...interface{}) (n int, err error)
func Sscan(str string, a ...interface{}) (n int, err error)
func Scanln(a ...interface{}) (n int, err error)
func Fscanln(r io.Reader, a ...interface{}) (n int, err error)
func Sscanln(str string, a ...interface{}) (n int, err error)
```
## 参数
map、slice、chan、指针、interface默认以引用的方式传递，其他的在默认情况下，使用的是值传递.

无论是值传递，还是引用传递，传递给函数的都是变量的副本，不过，值传递是值的拷贝。引用传递是地址的拷贝，一般来说，地址拷贝更为高效。而值拷贝取决于拷贝的对象大小，对象越大，则性能越低。
### 不定参
就是函数的参数不是固定的，后面的类型是固定的。（可变参数）

Golang 可变参数本质上就是 slice。只能有一个，且必须是最后一个。

在参数赋值时可以不用用一个一个的赋值，可以直接传递一个数组或者切片，特别**注意**的是在参数后加上“…”即可。
```go
  func myfunc(args ...int) {    //0个或多个参数
  }

  func add(a int, args…int) int {    //1个或多个参数
  }

  func add(a int, b int, args…int) int {    //2个或多个参数
  }
```
**注意**：其中args是一个slice，我们可以通过arg[index]依次访问所有参数,通过len(arg)来判断传递参数的个数.

任意类型的不定参数：就是函数的参数和每个参数的类型都不是固定的。

**用interface{}传递任意类型数据是Go语言的惯例用法，而且interface{}是类型安全的。**
```go
func myfunc(args ...interface{}) {
  }
```
```go
func test(s string, n ...int) string {
    var x int
    for _, i := range n {
        x += i
    }
    return fmt.Sprintf(s, x)
}
func main() {
    s := []int{1, 2, 3}
    res := test("sum: %d", s...)    // slice... 展开slice,而不是只写变量名
    println(res)
}
```
## 返回值
Go 的返回值可以被命名，并且就像在函数体开头声明的变量那样使用。

返回值的名称应当具有一定的意义，可以作为文档使用。

没有参数的 return 语句返回各个返回变量的当前值。这种用法被称作 **“裸”返回**。
```go
package main

import (
    "fmt"
)

func add(a, b int) (c int) {
    c = a + b
    return
}

func calc(a, b int) (sum int, avg int) {
    sum = a + b
    avg = (a + b) / 2

    return
}

func main() {
    var a, b int = 1, 2
    c := add(a, b)
    sum, avg := calc(a, b)
    fmt.Println(a, b, c, sum, avg)
}
```
命名返回参数可被同名局部变量遮蔽，此时需要显式返回。


**Golang返回值不能用容器对象接收多返回值。只能用多个变量，或 "_" 忽略。或者多返回值可直接作为其他函数调用实参。**
```go
func test() (int, int) {
    return 1, 2
}

func add(x, y int) int {
    return x + y
}

func sum(n ...int) int {
    var x int
    for _, i := range n {
        x += i
    }

    return x
}

func main() {
    println(add(test()))
    println(sum(test()))
}
```
**命名返回参数允许 defer 延迟调用通过闭包读取和修改。**
```go
package main

func add(x, y int) (z int) {
    defer func() {
        z += 100
    }()

    z = x + y
    return
}

func main() {
    println(add(1, 2)) 
}

output:103
```
利用多返回值进行错误处理：
- 



## 匿名函数
**匿名函数的优越性在于可以直接使用函数内的变量，不必申明。Golang匿名函数可赋值给变量，做为结构字段，或者在 channel 里传送。**
```go
package main

func main() {
    // --- function variable ---
    fn := func() { println("Hello, World!") }
    fn()

    // --- function collection ---
    fns := [](func(x int) int){
        func(x int) int { return x + 1 },
        func(x int) int { return x + 2 },
    }
    println(fns[0](100))

    // --- function as field ---
    d := struct {
        fn func() string
    }{
        fn: func() string { return "Hello, World!" },
    }
    println(d.fn())

    // --- channel of function ---
    fc := make(chan func() string, 2)
    fc <- func() string { return "Hello, World!" }
    println((<-fc)())

output:
Hello, World!
101
Hello, World!
Hello, World!
}
```
## 闭包、递归
### 闭包
**闭包**是由函数及其相关引用环境组合而成的实体。

“官方”的解释是：所谓“闭包”，指的是一个拥有许多变量和绑定了这些变量的环境的表达式（通常是一个函数），因而这些变量也是该表达式的一部分。

维基百科讲，闭包（Closure），是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。

目前在JavaScript、Go、PHP、Scala、Scheme、Common Lisp、Smalltalk、Groovy、Ruby、 Python、Lua、objective c、Swift 以及Java8以上等语言中都能找到对闭包不同程度的支持。

通过支持闭包的语法可以发现一个特点，他们都有垃圾回收(GC)机制。

go的闭包：
```go
package main

import (
    "fmt"
)

func a() func() int {
    i := 0
    b := func() int {
        i++
        fmt.Println(i)
        return i
    }
    return b
}

func main() {
    c := a()
    c()
    c()
    c()

    a() //不会输出i
}

output:
1
2
3
```
**当函数a()的内部函数b()被函数a()外的一个变量引用的时候，就创建了一个闭包。**

闭包复制的是原对象指针，这就很容易解释延迟引用现象。（延迟引用，引用的只是某个变量的“最终值”，延迟闭包里引用的变量是原变量指针，这解释了后面为什么derfer碰上闭包的输出都是同一值）

```go
package main

import "fmt"

func test() func() {
    x := 100
    fmt.Printf("x (%p) = %d\n", &x, x)

    return func() {
        fmt.Printf("x (%p) = %d\n", &x, x)
    }
}

func main() {
    f := test()
    f()
}

output:
x (0xc42007c008) = 100
x (0xc42007c008) = 100
```
**在汇编层 ，test 实际返回的是 FuncVal 对象，其中包含了匿名函数地址、闭包对象指针。当调用匿名函数时，只需以某个寄存器传递该对象即可。**

Funcval对象：
```
 FuncVal { func_address, closure_var_pointer ... }
```
外部引用函数参数局部变量:
```go
package main

import "fmt"

// 外部引用函数参数局部变量
func add(base int) func(int) int {
    return func(i int) int {
        base += i
        return base
    }
}

func main() {
    tmp1 := add(10)
    fmt.Println(tmp1(1), tmp1(2))
    // 此时tmp1和tmp2不是一个实体了
    tmp2 := add(100)
    fmt.Println(tmp2(1), tmp2(2))
}
```
**返回两个闭包**：
```go
package main

import "fmt"

// 返回2个函数类型的返回值
func test01(base int) (func(int) int, func(int) int) {
    // 定义2个函数，并返回
    // 相加
    add := func(i int) int {
        base += i
        return base
    }
    // 相减
    sub := func(i int) int {
        base -= i
        return base
    }
    // 返回
    return add, sub
}

func main() {
    f1, f2 := test01(10)
    // base一直是没有消
    fmt.Println(f1(1), f2(2))
    // 此时base是9
    fmt.Println(f1(3), f2(4))
}
```
### go递归函数
构成递归的两个条件：
1. 子问题须与原始问题为同样的事，且更为简单。
2. 不能无限制地调用本身，须有个出口，化简为非递归状况处理。

go的递归和其他语言基本无差别。
## 延迟调用（defer）
特性：
1. 关键字 defer 用于注册延迟调用。
2. 这些调用直到 return 前才被执行。因此，可以用来做资源清理。
3. 多个defer语句，按**先进后出**的方式执行。（因为后面的defer可能会用到前面的资源）
4. defer语句中的变量，在defer声明时就决定了。
5. 发生panic依然会执行defer，但不是任何情况都会执行，比如：os.Exit()不会调用defer。os.Exit()退出时不输出当前调用栈信息
6. Go1.13前的版本defer的开销还是非常大的，在后续团队优化后现在的开销比较小可以放心使用

用途：
1. 关闭文件句柄、锁资源释放、数据库连接释放
2. **使用 defer 可以跟踪函数的执行过程**
```go
// trace.go
package main
  
func Trace(name string) func() {
    println("enter:", name)
    return func() {
        println("exit:", name)
    }
}

func foo() {
    defer Trace("foo")()
    bar()
}

func bar() {
    defer Trace("bar")()
}

func main() {
    defer Trace("main")()
    foo()
}
```
不足：调用 Trace 时需手动显式传入要跟踪的函数名；如果是并发应用，不同 Goroutine 中函数链跟踪混在一起无法分辨；输出的跟踪结果缺少层次感，调用关系不易识别；对要跟踪的函数，需手动调用 Trace 函数。

**实现一个自动注入跟踪代码，并输出有层次感的函数调用链跟踪命令行工具**：
- 自动获取所跟踪函数的函数名
3. 充当“断言”，提示潜在bug
4. 
5. 

defer功能强大，对于资源管理非常方便，但是如果没用好，也会有陷阱。

**defer碰上闭包：**
```go
package main

import "fmt"

func main() {
    var whatever [5]struct{}
    for i := range whatever {
        defer func() { fmt.Println(i) }()
    }
} 

output:
4
4
4
4
4
```
延迟引用，闭包里的i是原变量指针。

**defer.f.Close**:
```go
package main

import "fmt"

type Test struct {
    name string
}

func (t *Test) Close() {
    fmt.Println(t.name, " closed")
}
func main() {
    ts := []Test{"a", "b", "c"}
    for _, t := range ts {
        defer t.Close()
    }
} 

output:
c  closed
c  closed
c  closed
```
```go
package main

import "fmt"

type Test struct {
    name string
}

func (t *Test) Close() {
    fmt.Println(t.name, " closed")
}
func Close(t Test) {
    t.Close()
}
func main() {
    ts := []Test"a", "b", "c"}
    for _, t := range ts {
        defer Close(t)
    }
    //或者for _, t := range ts {
    //     t2 := t
    //     defer t2.Close()
    // }
} 

output:
c  closed
b  closed
a  closed
```
结论：
defer后面的语句在执行的时候，函数调用的参数会被保存起来，但是不执行。也就是复制了一份。但是并没有说struct这里的this指针如何处理，通过这个例子可以看出go语言并没有把这个明确写出来的this指针当作参数来看待。

多个 defer 注册，按 FILO 次序执行 ( 先进后出 )。哪怕函数或某个延迟调用发生错误，比如发生panic，这些defer调用依旧会被执行。

```go
package main

func test(x int) {
    defer println("a")
    defer println("b")

    defer func() {
        println(100 / x) // div0 异常未被捕获，逐步往外传递，最终终止进程。
    }()

    defer println("c")
}

func main() {
    test(0)
} 

output:
c
b
a
panic: runtime error: integer divide by zero
```
延迟调用参数在注册时求值或复制，可用指针或闭包 “延迟” 读取。

```go
package main

func test() {
    x, y := 10, 20

    defer func(i int) {
        println("defer:", i, y) // y 闭包引用
    }(x) // x 被复制

    x += 10
    y += 100
    println("x =", x, "y =", y)
}

func main() {
    test()
}  

output:
x = 20 y = 120
defer: 10 120
```
滥用 defer 可能会导致性能问题，尤其是在一个 “大循环” 里。
```go
package main

import (
    "fmt"
    "sync"
    "time"
)

var lock sync.Mutex

func test() {
    lock.Lock()
    lock.Unlock()
}

func testdefer() {
    lock.Lock()
    defer lock.Unlock()
}

func main() {
    func() {
        t1 := time.Now()

        for i := 0; i < 10000; i++ {
            test()
        }
        elapsed := time.Since(t1)
        fmt.Println("test elapsed: ", elapsed)
    }()
    func() {
        t1 := time.Now()

        for i := 0; i < 10000; i++ {
            testdefer()
        }
        elapsed := time.Since(t1)
        fmt.Println("testdefer elapsed: ", elapsed)
    }()

}

output:
test elapsed:  223.162µs
testdefer elapsed:  781.304µs
```
### defer陷阱
defer 与 closure：
```go
package main

import (
    "errors"
    "fmt"
)

func foo(a, b int) (i int, err error) {
    defer fmt.Printf("first defer err %v\n", err)
    defer func(err error) { fmt.Printf("second defer err %v\n", err) }(err)
    defer func() { fmt.Printf("third defer err %v\n", err) }()
    if b == 0 {
        err = errors.New("divided by zero!")
        return
    }

    i = a / b
    return
}

func main() {
    foo(2, 0)
}  

output：
third defer err divided by zero!
second defer err <nil>
first defer err <nil>
```
解释：如果 defer 后面跟的不是一个 closure 最后执行的时候我们得到的并不是最新的值。

defer 与 return:
```go
package main

import "fmt"

func foo() (i int) {

    i = 0
    defer func() {
        fmt.Println(i)
    }()

    return 2
}

func main() {
    foo()
}

output：
    2
```
解释：在有具名返回值的函数中（这里具名返回值为 i），执行 return 2 的时候实际上已经将 i 的值重新赋值为 2。所以defer closure 输出结果为 2 而不是 1。

defer nil 函数:
```go
package main

import (
    "fmt"
)

func test() {
    var run func() = nil
    defer run()
    fmt.Println("runs")
}

func main() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println(err)
        }
    }()
    test()
} 

output：
runs
runtime error: invalid memory address or nil pointer dereference
```
解释：名为 test 的函数一直运行至结束，然后 defer 函数会被执行且会因为值为 nil 而产生 panic 异常。然而值得**注意**的是，run() 的声明是没有问题，因为在test函数运行完成后它才会被调用。

**在错误的位置使用 defer:**
当 http.Get 失败时会抛出异常。
```go
package main

import "net/http"

func do() error {
    res, err := http.Get("http://www.google.com")
    defer res.Body.Close()
    if err != nil {
        return err
    }

    // ..code...

    return nil
}

func main() {
    do()
} 

output：
panic: runtime error: invalid memory address or nil pointer dereference
```
因为在这里我们并没有检查我们的请求是否成功执行，当它失败的时候，我们访问了 Body 中的空变量 res ，因此会抛出异常

**解决方案**：
总是在一次成功的资源分配下面使用 defer ，对于这种情况来说意味着：当且仅当 http.Get 成功执行时才使用 defer

```go
package main

import "net/http"

func do() error {
    res, err := http.Get("http://xxxxxxxxxx")
    if res != nil {
        defer res.Body.Close()
    }

    if err != nil {
        return err
    }

    // ..code...

    return nil
}

func main() {
    do()
} 
```
在上述的代码中，当有错误的时候，err 会被返回，否则当整个函数返回的时候，会关闭 res.Body 。

解释：在这里，你同样需要检查 res 的值是否为 nil ，这是 http.Get 中的一个警告。通常情况下，出错的时候，返回的内容应为空并且错误会被返回，可当你获得的是一个重定向 error 时， res 的值并不会为 nil ，但其又会将错误返回。上面的代码保证了无论如何 Body 都会被关闭，如果你没有打算使用其中的数据，那么你还需要丢弃已经接收的数据。

**不检查错误**:
在这里，f.Close() 可能会返回一个错误，可这个错误会被我们忽略掉。
```go
package main

import "os"

func do() error {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }

    if f != nil {
        defer f.Close()
    }

    // ..code...

    return nil
}

func main() {
    do()
}  
```
改进一下：
```go
package main

import "os"

func do() error {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }

    if f != nil {
        defer func() {
            if err := f.Close(); err != nil {
                // log etc
            }
        }()
    }

    // ..code...

    return nil
}

func main() {
    do()
} 
```
再改进一下：通过命名的返回变量来返回defer内的错误。
```go
package main

import "os"

func do() (err error) {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }

    if f != nil {
        defer func() {
            if ferr := f.Close(); ferr != nil {
                err = ferr
            }
        }()
    }

    // ..code...

    return nil
}

func main() {
    do()
} 
```
释放相同的资源

如果你尝试使用相同的变量释放不同的资源，那么这个操作可能无法正常执行。

```go
package main

import (
    "fmt"
    "os"
)

func do() error {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }
    if f != nil {
        defer func() {
            if err := f.Close(); err != nil {
                fmt.Printf("defer close book.txt err %v\n", err)
            }
        }()
    }

    // ..code...

    f, err = os.Open("another-book.txt")
    if err != nil {
        return err
    }
    if f != nil {
        defer func() {
            if err := f.Close(); err != nil {
                fmt.Printf("defer close another-book.txt err %v\n", err)
            }
        }()
    }

    return nil
}

func main() {
    do()
} 
```
输出结果：
defer close book.txt err close ./another-book.txt: file already closed

当延迟函数执行时，只有最后一个变量会被用到，因此，f 变量 会成为最后那个资源 (another-book.txt)。而且两个 defer 都会将这个资源作为最后的资源来关闭

解决方案：
```go
package main

import (
    "fmt"
    "io"
    "os"
)

func do() error {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }
    if f != nil {
        defer func(f io.Closer) {
            if err := f.Close(); err != nil {
                fmt.Printf("defer close book.txt err %v\n", err)
            }
        }(f)
    }

    // ..code...

    f, err = os.Open("another-book.txt")
    if err != nil {
        return err
    }
    if f != nil {
        defer func(f io.Closer) {
            if err := f.Close(); err != nil {
                fmt.Printf("defer close another-book.txt err %v\n", err)
            }
        }(f)
    }

    return nil
}

func main() {
    do()
} 
```
## 异常处理，错误处理
提倡**及早失败，避免嵌套**

Golang 没有结构化异常，使用 panic 抛出错误，recover 捕获错误。

异常的使用场景简单描述：Go中可以抛出一个panic的异常，然后在defer中通过recover捕获这个异常，然后正常处理。

panic:
1. 内置函数
2. 假如函数F中书写了panic语句，会终止其后要执行的代码，在panic所在函数F内如果存在要执行的defer函数列表，按照defer的逆序执行
3. 返回函数F的调用者G，在G中，调用函数F语句之后的代码不会执行，假如函数G中存在要执行的defer函数列表，按照defer的逆序执行
4. 直到goroutine整个退出，并报告错误

recover:
1. 内置函数
2. 用来控制一个goroutine的panicking行为，捕获panic，从而影响应用的行为
3. 一般的调用建议
    - 只能用在defer函数中，通过recever来终止一个goroutine的panicking过程，从而恢复正常代码的执行
    - 可以获取通过panic传递的error

**注意**：
1. 利用recover处理panic指令，defer 必须放在 panic 之前定义，另外 recover 只有在 defer 调用的函数中才有效。否则当panic时，recover无法捕获到panic，无法防止panic扩散。
2. recover 处理异常后，逻辑并不会恢复到 panic 那个点去，函数跑到 defer 之后的那个点。
3. 多个 defer 会形成 defer 栈，后定义的 defer 语句会被最先调用。
4. **当心recoer成为恶魔**，因为recover并不会检测发生了什么错误。可能是系统的某些核心资源消耗完了，强制恢复之后系统依然无法正常工作的。还会导致一些健康检查程序无法检测出错误，health check无法检测出错误（很多health check程序只是检查这个进程在还是不在），形成僵尸服务进程（存在着但不能提供服务）。不如采用一种可恢复的设计模式，“Let it Crash”，干脆让进程crash掉，然后就会帮我们重新把服务进程提起来，如同重启。（重启是恢复不确定性最好的方法）
```go
package main

func main() {
    test()
}

func test() {
    defer func() {
        if err := recover(); err != nil {
            println(err.(string)) // 将 interface{} 转型为具体类型。
        }
    }()

    panic("panic error!")
}  

output:
panic error! 
```
由于 panic、recover 参数类型为 interface{}，因此可抛出任何类型对象。
```go
func panic(v interface{})
func recover() interface{}
```
向已关闭的通道发送数据会引发panic
```go
package main

import (
    "fmt"
)

func main() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println(err)
        }
    }()

    var ch chan int = make(chan int, 10)
    close(ch)
    ch <- 1
}  

output:
send on closed channel
```
延迟调用中引发的错误，可被后续延迟调用捕获，但仅最后一个错误可被捕获。
```go
package main

import "fmt"

func test() {
    defer func() {
        fmt.Println(recover())
    }()

    defer func() {
        panic("defer panic")
    }()

    panic("test panic")
}

func main() {
    test()
} 

output:
defer panic 
```
捕获函数 recover 只有在延迟调用内直接调用才会终止错误，否则总是返回 nil。任何未捕获的错误都会沿调用堆栈向外传递。
```go
package main

import "fmt"

func test() {
    defer func() {
        fmt.Println(recover()) //有效
    }()
    defer recover()              //无效！
    defer fmt.Println(recover()) //无效！
    defer func() {
        func() {
            println("defer inner")
            recover() //无效！
        }()
    }()

    panic("test panic")
}

func main() {
    test()
}

output:
defer inner
<nil>
test panic
```
使用延迟匿名函数或下面这样都是有效的。
```go
package main

import (
    "fmt"
)

func except() {
    fmt.Println(recover())
}

func test() {
    defer except()
    panic("test panic")
}

func main() {
    test()
}
output：
test panic
```
如果需要保护代码段，可将代码块重构成匿名函数，如此可确保后续代码被执行。
```go
package main

import "fmt"

func test(x, y int) {
    var z int

    func() {
        defer func() {
            if recover() != nil {
                z = 0
            }
        }()
        panic("test panic")
        z = x / y
        return
    }()

    fmt.Printf("x / y = %d\n", z)
}

func main() {
    test(2, 1)
}

output：
x / y = 0
```
除用 panic 引发中断性错误外，还可返回 error 类型错误对象来表示函数调用状态。（error类型实现了error接口）
```go
type error interface {
    Error() string
} 
```
**标准库 errors.New 和 fmt.Errorf 函数用于创建实现 error 接口的错误对象。通过判断错误对象实例来确定具体错误类型。**

```go
package main

import (
    "errors"
    "fmt"
)
//定义预置错误
var ErrDivByZero = errors.New("division by zero")

func div(x, y int) (int, error) {
    if y == 0 {
        return 0, ErrDivByZero
    }
    return x / y, nil
}

func main() {
    defer func() {
        fmt.Println(recover())
    }()
    switch z, err := div(10, 0); err {
    case nil:
        println(z)
    case ErrDivByZero:
        panic(err)
    }
} 

output:
division by zero
```
go实现类似 try catch 的异常处理。```
go`
go
package main

import "fmt"

func Try(fun func(), handler func(interface{})) {
    defer func() {
        if err := recover(); err != nil {
            handler(err)
        }
    }()
    fun()
}

func main() {
    Try(func() {
        panic("test panic")
    }, func(err interface{}) {
        fmt.Println(err)
    })
} 
output：
test panic
```
如何区别使用 panic 和 error 两种方式?

惯例是:导致关键流程出现不可修复性错误的使用 panic，其他使用 error。

几种错误处理策略：
1. 透明错误处理策略
```go
err := doSomething()
if err != nil {
    // 不关心err变量底层错误值所携带的具体上下文信息
    // 执行简单错误处理逻辑并返回
    ... ...
    return err
}
```
2. “哨兵”
    - 当错误处理方不能只根据“透明的错误值”就做出错误处理路径选取的情况下，错误处理方会尝试对返回的错误值进行检视，于是就有可能出现下面代码中的反模式：
    - 反模式就是，错误处理方以透明错误值所能提供的唯一上下文信息（描述错误的字符串），作为错误处理路径选择的依据。但这种“反模式”会造成严重的隐式耦合。这也就意味着，错误值构造方不经意间的一次错误描述字符串的改动，都会造成错误处理方处理行为的变化，并且这种通过字符串比较的方式，对错误值进行检视的性能也很差。
```go
data, err := b.Peek(1)
if err != nil {
    switch err.Error() {
    case "bufio: negative count":
        // ... ...
        return
    case "bufio: buffer full":
        // ... ...
        return
    case "bufio: invalid use of UnreadByte":
        // ... ...
        return
    default:
        // ... ...
        return
    }
}
```
go 标准库采用了定义导出的（Exported）“哨兵”错误值的方式，来辅助错误处理方检视（inspect）错误值并做出错误处理分支的决策，比如下面的 bufio 包中定义的“哨兵错误”：```
go`
go

// $GOROOT/src/bufio/bufio.go
var (
    ErrInvalidUnreadByte = errors.New("bufio: invalid use of UnreadByte")
    ErrInvalidUnreadRune = errors.New("bufio: invalid use of UnreadRune")
    ErrBufferFull        = errors.New("bufio: buffer full")
    ErrNegativeCount     = errors.New("bufio: negative count")
)
```
下面的代码片段利用了上面的哨兵错误，进行错误处理分支的决策：
```go

data, err := b.Peek(1)
if err != nil {
    switch err {
    case bufio.ErrNegativeCount:
        // ... ...
        return
    case bufio.ErrBufferFull:
        // ... ...
        return
    case bufio.ErrInvalidUnreadByte:
        // ... ...
        return
    default:
        // ... ...
        return
    }
}
```
一般“哨兵”错误值变量以 ErrXXX 格式命名。和透明错误策略相比，“哨兵”策略让错误处理方在有检视错误值的需求时候，可以“有的放矢”。不过，对于 API 的开发者而言，暴露“哨兵”错误值也意味着这些错误值和包的公共函数 / 方法一起成为了 API 的一部分。一旦发布出去，开发者就要对它进行很好的维护。而“哨兵”错误值也让使用这些值的错误处理方对它产生了依赖。

从 Go 1.13 版本开始，标准库 errors 包提供了 Is 函数用于错误处理方对错误值的检视。Is 函数类似于把一个 error 类型变量与“哨兵”错误值进行比较，比如下面代码：
```go
// 类似 if err == ErrOutOfBounds{ … }
if errors.Is(err, ErrOutOfBounds) {
    // 越界的错误处理
}
```
不同的是，如果 error 类型变量的底层错误值是一个包装错误（Wrapped Error），errors.Is 方法会沿着该包装错误所在错误链（Error Chain)，与链上所有被包装的错误（Wrapped Error）进行比较，直至找到一个匹配的错误为止。下面是 Is 函数应用的一个例子：
```go
var ErrSentinel = errors.New("the underlying sentinel error")

func main() {
  err1 := fmt.Errorf("wrap sentinel: %w", ErrSentinel)
  err2 := fmt.Errorf("wrap err1: %w", err1)
    println(err2 == ErrSentinel) //false
  if errors.Is(err2, ErrSentinel) {
    println("err2 is ErrSentinel")
    return
  }

  println("err2 is not ErrSentinel")
}
```
通过 fmt.Errorf 函数，并且使用 %w 创建包装错误变量 err1 和 err2，其中 err1 实现了对 ErrSentinel 这个“哨兵错误值”的包装，而 err2 又对 err1 进行了包装，这样就形成了一条错误链。位于错误链最上层的是 err2，位于最底层的是 ErrSentinel。之后，我们再分别通过值比较和 errors.Is 这两种方法，判断 err2 与 ErrSentinel 的关系。

运行上述代码，我们会看到如下结果：
```
false
err2 is ErrSentinel
```
通过比较操作符对 err2 与 ErrSentinel 进行比较后，我们发现这二者并不相同。而 errors.Is 函数则会沿着 err2 所在错误链，向下找到被包装到最底层的“哨兵”错误值ErrSentinel。

Go 1.13 及后续版本，建议尽量使用errors.Is方法去检视某个错误值是否就是某个预期错误值，或者包装了某个特定的“哨兵”错误值。
3. 错误值类型检视策略
- 基于 Go 标准库提供的错误值构造方法构造的“哨兵”错误值，除了让错误处理方可以“有的放矢”的进行值比较之外，并没有提供其他有效的错误上下文信息。那如果遇到错误处理方需要错误值提供更多的“错误上下文”的情况，上面这些错误处理策略和错误值构造方式都无法满足。
- 这种情况下，我们需要通过自定义错误类型的构造错误值的方式，来提供更多的“错误上下文”信息。并且，由于错误值都通过 error 接口变量统一呈现，要得到底层错误类型携带的错误上下文信息，错误处理方需要使用 Go 提供的类型断言机制（Type Assertion）或类型选择机制（Type Switch），这种错误处理方式，我称之为错误值类型检视策略
```go

// $GOROOT/src/encoding/json/decode.go
type UnmarshalTypeError struct {
    Value  string       
    Type   reflect.Type 
    Offset int64        
    Struct string       
    Field  string      
}
```
错误处理方可以通过错误类型检视策略，获得更多错误值的错误上下文信息，下面就是利用这一策略的 json 包的一个方法的实现：
```go

// $GOROOT/src/encoding/json/decode.go
func (d *decodeState) addErrorContext(err error) error {
    if d.errorContext.Struct != nil || len(d.errorContext.FieldStack) > 0 {
        switch err := err.(type) {
        case *UnmarshalTypeError:
            err.Struct = d.errorContext.Struct.Name()
            err.Field = strings.Join(d.errorContext.FieldStack, ".")
            return err
        }
    }
    return err
}
```
我们看到，这段代码通过类型 switch 语句得到了 err 变量代表的动态类型和值，然后在匹配的 case 分支中利用错误上下文信息进行处理。这里，一般自定义导出的错误类型以XXXError的形式命名。和“哨兵”错误处理策略一样，错误值类型检视策略，由于暴露了自定义的错误类型给错误处理方，因此这些错误类型也和包的公共函数 / 方法一起，成为了 API 的一部分。一旦发布出去，开发者就要对它们进行很好的维护。而它们也让使用这些类型进行检视的错误处理方对其产生了依赖。

从 Go 1.13 版本开始，标准库 errors 包提供了As函数给错误处理方检视错误值。As函数类似于通过类型断言判断一个 error 类型变量是否为特定的自定义错误类型，如下面代码所示：
```go

// 类似 if e, ok := err.(*MyError); ok { … }
var e *MyError
if errors.As(err, &e) {
    // 如果err类型为*MyError，变量e将被设置为对应的错误值
}
```
不同的是，如果 error 类型变量的动态错误值是一个包装错误，errors.As函数会沿着该包装错误所在错误链，与链上所有被包装的错误的类型进行比较，直至找到一个匹配的错误类型，就像 errors.Is 函数那样。下面是As函数应用的一个例子：
```go

type MyError struct {
    e string
}

func (e *MyError) Error() string {
    return e.e
}

func main() {
    var err = &MyError{"MyError error demo"}
    err1 := fmt.Errorf("wrap err: %w", err)
    err2 := fmt.Errorf("wrap err1: %w", err1)
    var e *MyError
    if errors.As(err2, &e) {
        println("MyError is on the chain of err2")
        println(e == err)                  
        return                             
    }                                      
    println("MyError is not on the chain of err2")
} 
```
```
MyError is on the chain of err2
true
```
errors.As函数沿着 err2 所在错误链向下找到了被包装到最深处的错误值，并将 err2 与其类型 * MyError成功匹配。匹配成功后，errors.As 会将匹配到的错误值存储到 As 函数的第二个参数中，这也是为什么println(e == err)输出 true 的原因。所以，如果使用的是 Go 1.13 及后续版本，尽量使用errors.As方法去检视某个错误值是否是某自定义错误类型的实例。
4. 错误行为特征检视策略
- “透明错误处理策略”，有效降低了错误的构造方与错误处理方两者之间的耦合，策略二和策略三，都是我们实际编码中有效的错误处理策略，但其实使用这两种策略的代码，依然在错误的构造方与错误处理方两者之间建立了耦合。
- **将某个包中的错误类型归类，统一提取出一些公共的错误行为特征，并将这些错误行为特征放入一个公开的接口类型中。这种方式也被叫做错误行为特征检视策略。**

以标准库中的net包为例，它将包内的所有错误类型的公共行为特征抽象并放入net.Error这个接口中，如下面代码：
```go

// $GOROOT/src/net/net.go
type Error interface {
    error
    Timeout() bool  
    Temporary() bool
}
```
net.Error 接口包含两个用于判断错误行为特征的方法：Timeout 用来判断是否是超时（Timeout）错误，Temporary 用于判断是否是临时（Temporary）错误。而错误处理方只需要依赖这个公共接口，就可以检视具体错误值的错误行为特征信息，并根据这些信息做出后续错误处理分支选择的决策。

http 包使用错误行为特征检视策略进行错误处理的例子：
```go

// $GOROOT/src/net/http/server.go
func (srv *Server) Serve(l net.Listener) error {
    ... ...
    for {
        rw, e := l.Accept()
        if e != nil {
            select {
            case <-srv.getDoneChan():
                return ErrServerClosed
            default:
            }
            if ne, ok := e.(net.Error); ok && ne.Temporary() {
                // 注：这里对临时性(temporary)错误进行处理
                ... ...
                time.Sleep(tempDelay)
                continue
            }
            return e
        }
        ...
    }
    ... ...
}
```
Accept 方法实际上返回的错误类型为*OpError，它是 net 包中的一个自定义错误类型，它实现了错误公共特征接口net.Error，如下代码所示：
```go

// $GOROOT/src/net/net.go
type OpError struct {
    ... ...
    // Err is the error that occurred during the operation.
    Err error
}

type temporary interface {
    Temporary() bool
}

func (e *OpError) Temporary() bool {
  if ne, ok := e.Err.(*os.SyscallError); ok {
      t, ok := ne.Err.(temporary)
      return ok && t.Temporary()
  }
  t, ok := e.Err.(temporary)
  return ok && t.Temporary()
}
```
因此，OpError 实例可以被错误处理方通过net.Error接口的方法，判断它的行为是否满足 Temporary 或 Timeout 特征。

错误处理策略建议：
- 请尽量使用“透明错误”处理策略，降低错误处理方与错误值构造方之间的耦合；
- 如果可以通过错误值类型的特征进行错误检视，那么请尽量使用“错误行为特征检视策略”;
- 在上述两种策略无法实施的情况下，再使用“哨兵”策略和“错误值类型检视”策略；
- Go 1.13 及后续版本中，尽量用errors.Is和errors.As函数替换原先的错误检视比较语句。
## **为啥说函数是Go语言的一等公民**：
- 引用一下 wiki 发明人、C2 站点作者沃德·坎宁安 (Ward Cunningham)对“一等公民”的解释：
    - 如果一门编程语言对某种语言元素的创建和使用没有限制，我们可以像对待值（value）一样对待这种语法元素，那么我们就称这种语法元素是这门编程语言的“一等公民”。拥有“一等公民”待遇的语法元素可以存储在变量中，可以作为参数传递给函数，可以在函数内部创建并可以作为返回值从函数返回
1. Go 函数可以存储在变量中。
2. 支持在函数内创建并通过返回值返回。
3. 作为参数传入函数。
4. 拥有自己的类型。
- 应用go函数的这些灵活性：
1. 函数类型的妙用
    - 函数也可以被显式转型，见下面web server的例子。
```go

func greeting(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Welcome, Gopher!\n")
}                    

func main() {
    //greeting这个函数被显示转化为HandleFunc类型，ListenAndServe的第二个参数是个需要实现ServeHTTP方法（即需要实现Handle接口）的类型
    http.ListenAndServe(":8080", http.HandlerFunc(greeting))
}

……

// $GOROOT/src/net/http/server.go

type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(w, r).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
        f(w, r)
}
```
2. 利用闭包简化函数调用。
```go

func partialTimes(x int) func(int) int {
  return func(y int) int {
    return times(x, y)
  }
}

func main() {
  timesTwo := partialTimes(2)   // 以高频乘数2为固定乘数的乘法函数
  timesThree := partialTimes(3) // 以高频乘数3为固定乘数的乘法函数
  fmt.Println(timesTwo(5))   // 10，等价于times(2, 5)
  fmt.Println(timesTwo(6))   // 12，等价于times(2, 6)
  fmt.Println(timesThree(5)) // 15，等价于times(3, 5)
  fmt.Println(timesThree(6)) // 18，等价于times(3, 6)
}
```
## 单元测试
单元测试还是挺重要的。
### go test工具
Go语言中的测试依赖go test命令。编写测试代码和编写普通的Go代码过程是类似的，并不需要学习新的语法、规则或工具。

go test命令是一个按照一定约定和组织的测试代码的驱动程序。在包目录内，所有以_test.go为后缀名的源代码文件都是go test测试的一部分，不会被go build编译到最终的可执行文件中。

在*_test.go文件中有三种类型的函数，单元测试函数、基准测试函数和示例函数。

|类型|格式|作用|
|:--|:--|:--|
测试函数	|函数名前缀为Test	|测试程序的一些逻辑行为是否正确
基准函数	|函数名前缀为Benchmark	|测试函数的性能
示例函数	|函数名前缀为Example	|为文档提供示例文档

go test命令会遍历所有的*_test.go文件中符合上述命名规则的函数，然后生成一个临时的main包用于调用相应的测试函数，然后构建并运行、报告测试结果，最后清理测试中生成的临时文件。

Golang单元测试对文件名和方法名，参数都有很严格的要求。
1. 文件名必须以xx_test.go命名
2. 方法必须是Test[^a-z]开头
3. 方法参数必须 t *testing.T
4. 使用go test执行单元测试

go test的参数解读：

go test是go语言自带的测试工具，其中包含的是两类，单元测试和性能测试

通过go help test可以看到go test的使用说明：

格式形如：
go test [-c] [-i] [build flags] [packages] [flags for test binary]

参数解读：

-c : 编译go test成为可执行的二进制文件，但是不运行测试。

-i : 安装测试包依赖的package，但是不运行测试。

关于build flags，调用go help build，这些是编译运行过程中需要使用到的参数，一般设置为空

关于packages，调用go help packages，这些是关于包的管理，一般设置为空

关于flags for test binary，调用go help testflag，这些是go test过程中经常使用到的参数

-test.v : 是否输出全部的单元测试用例（不管成功或者失败），默认没有加上，所以只输出失败的单元测试用例。

-test.run pattern: 只跑哪些单元测试用例

-test.bench patten: 只跑那些性能测试用例

-test.benchmem : 是否在性能测试的时候输出内存情况

-test.benchtime t : 性能测试运行的时间，默认是1s

-test.cpuprofile cpu.out : 是否输出cpu性能分析文件

-test.memprofile mem.out : 是否输出内存性能分析文件

-test.blockprofile block.out : 是否输出内部goroutine阻塞的性能分析文件

-test.memprofilerate n : 内存性能分析的时候有一个分配了多少的时候才打点记录的问题。这个参数就是设置打点的内存分配间隔，也就是profile中一个sample代表的内存大小。默认是设置为512 * 1024的。如果你将它设置为1，则每分配一个内存块就会在profile中有个打点，那么生成的profile的sample就会非常多。如果你设置为0，那就是不做打点了。

你可以通过设置memprofilerate=1和GOGC=off来关闭内存回收，并且对每个内存块的分配进行观察。

-test.blockprofilerate n: 基本同上，控制的是goroutine阻塞时候打点的纳秒数。默认不设置就相当于-test.blockprofilerate=1，每一纳秒都打点记录一下

-test.parallel n : 性能测试的程序并行cpu数，默认等于GOMAXPROCS。

-test.timeout t : 如果测试用例运行时间超过t，则抛出panic

-test.cpu 1,2,4 : 程序运行在哪些CPU上面，使用二进制的1所在位代表，和nginx的nginx_worker_cpu_affinity是一个道理

-test.short : 将那些运行时间较长的测试用例运行时间缩短

目录结构：
```
test
      |
       —— calc.go
      |
       —— calc_test.go
```
### 性能分析工具
可以安装go-torch
```
通过⽂件⽅式输出 Profile
- 灵活性⾼，适⽤于特定代码段的分析
- 通过⼿动调⽤ runtime/pprof 的 API
- API 相关⽂档 https://studygolang.com/static/pkgdoc/pkg/runtime_pprof.htm
- go tool pprof [binary] [binary.prof]

通过 HTTP ⽅式输出 Profile
- 简单，适合于持续性运⾏的应⽤
- 在应⽤程序中导⼊ import _ "net/http/pprof"，并启动 http server 即可
- http://<host>:<port>/debug/pprof/
- go tool pprof http://<host>:<port>/debug/pprof/profile?seconds=10 （默认值为30秒）
- go-torch -seconds 10 http://<host>:<port>/debug/pprof/profile

Go ⽀持的多种 Profile
go help testflag
https://golang.org/src/runtime/pprof/pprof.go
```
#### 性能调优
性能调优过程：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220316154210.png)

常⻅分析指标
- Wall Time
- CPU Time
- Block Time
- Memory allocation
- GC times/time spent

ch47

### 测试函数
#### 测试函数的格式
每个测试函数必须导入testing包，测试函数的基本格式（签名）如下：
```go
func TestName(t *testing.T){
    // ...
} 
```
测试函数的名字必须以Test开头，可选的后缀名必须以大写字母开头，举几个例子：\
```go
func TestAdd(t *testing.T){ ... }
func TestSum(t *testing.T){ ... }
func TestLog(t *testing.T){ ... }
```
其中参数t用于报告测试失败和附加的日志信息。 testing.T的拥有的方法如下：
```go
func (c *T) Error(args ...interface{})
func (c *T) Errorf(format string, args ...interface{})
func (c *T) Fail()
func (c *T) FailNow()
func (c *T) Failed() bool
func (c *T) Fatal(args ...interface{})
func (c *T) Fatalf(format string, args ...interface{})
func (c *T) Log(args ...interface{})
func (c *T) Logf(format string, args ...interface{})
func (c *T) Name() string
func (t *T) Parallel()
func (t *T) Run(name string, f func(t *T)) bool
func (c *T) Skip(args ...interface{})
func (c *T) SkipNow()
func (c *T) Skipf(format string, args ...interface{})
func (c *T) Skipped() bool
```
#### 测试函数示例
就像细胞是构成我们身体的基本单位，一个软件程序也是由很多单元组件构成的。单元组件可以是函数、结构体、方法和最终用户可能依赖的任意东西。总之我们需要确保这些组件是能够正常运行的。单元测试是一些利用各种方法测试单元组件的程序，它会将结果与预期输出进行比较。

接下来，我们定义一个split的包，包中定义了一个Split函数，具体实现如下：
```go
// split/split.go

package split

import "strings"

// split package with a single split function.

// Split slices s into all substrings separated by sep and
// returns a slice of the substrings between those separators.
func Split(s, sep string) (result []string) {
    i := strings.Index(s, sep)

    for i > -1 {
        result = append(result, s[:i])
        s = s[i+1:]
        i = strings.Index(s, sep)
    }
    result = append(result, s)
    return
}
```
在当前目录下，我们创建一个split_test.go的测试文件，并定义一个测试函数如下：（表格测试法）
```go
// split/split_test.go

package split

import (
    "reflect"
    "testing"
)

func TestSplit(t *testing.T) { // 测试函数名必须以Test开头，必须接收一个*testing.T类型参数
    got := Split("a:b:c", ":")         // 程序输出的结果
    want := []string{"a", "b", "c"}    // 期望的结果
    if !reflect.DeepEqual(want, got) { // 因为slice不能比较直接，借助反射包中的方法比较
        t.Errorf("excepted:%v, got:%v", want, got) // 测试失败输出错误提示
    }
}
```
此时split这个包中的文件如下：
```
    split $ ls -l
    total 16
    -rw-r--r--  1 pprof staff  408  4 29 15:50 split.go
    -rw-r--r--  1 pprof  staff  466  4 29 16:04 split_test.go
```
在split包路径下，执行go test命令，可以看到输出结果如下：
```
split $ go test
PASS
ok      github.com/pprof/studygo/code_demo/test_demo/split       0.005s
```
一个测试用例有点单薄，我们再编写一个测试使用多个字符切割字符串的例子，在split_test.go中添加如下测试函数：
```go
func TestMoreSplit(t *testing.T) {
    got := Split("abcd", "bc")
    want := []string{"a", "d"}
    if !reflect.DeepEqual(want, got) {
        t.Errorf("excepted:%v, got:%v", want, got)
    }
}
```
再次运行go test命令，输出结果如下：
```
    split $ go test
    --- FAIL: TestMultiSplit (0.00s)
        split_test.go:20: excepted:[a d], got:[a cd]
    FAIL
    exit status 1
    FAIL    github.com/pprof/studygo/code_demo/test_demo/split       0.006s
```
 
这一次，我们的测试失败了。我们可以为go test命令添加-v参数，查看测试函数名称和运行时间：
```
    split $ go test -v
    === RUN   TestSplit
    --- PASS: TestSplit (0.00s)
    === RUN   TestMoreSplit
    --- FAIL: TestMoreSplit (0.00s)
        split_test.go:21: excepted:[a d], got:[a cd]
    FAIL
    exit status 1
    FAIL    github.com/pprof/studygo/code_demo/test_demo/split       0.005s
```
这一次我们能清楚的看到是TestMoreSplit这个测试没有成功。 还可以在go test命令后添加-run参数，它对应一个正则表达式，只有函数名匹配上的测试函数才会被go test命令执行。
```
    split $ go test -v -run="More"
    === RUN   TestMoreSplit
    --- FAIL: TestMoreSplit (0.00s)
        split_test.go:21: excepted:[a d], got:[a cd]
    FAIL
    exit status 1
    FAIL    github.com/pprof/studygo/code_demo/test_demo/split       0.006s
```
现在我们回过头来解决我们程序中的问题。很显然我们最初的split函数并没有考虑到sep为多个字符的情况，我们来修复下这个Bug：
```go
package split

import "strings"

// split package with a single split function.

// Split slices s into all substrings separated by sep and
// returns a slice of the substrings between those separators.
func Split(s, sep string) (result []string) {
    i := strings.Index(s, sep)

    for i > -1 {
        result = append(result, s[:i])
        s = s[i+len(sep):] // 这里使用len(sep)获取sep的长度
        i = strings.Index(s, sep)
    }
    result = append(result, s)
    return
} 
```
这一次我们再来测试一下，我们的程序。**注意**，当我们修改了我们的代码之后不要仅仅执行那些失败的测试函数，我们应该完整的运行所有的测试，保证不会因为修改代码而引入了新的问题。
```
 split $ go test -v
    === RUN   TestSplit
    --- PASS: TestSplit (0.00s)
    === RUN   TestMoreSplit
    --- PASS: TestMoreSplit (0.00s)
    PASS
    ok      github.com/pprof/studygo/code_demo/test_demo/split       0.006s
```
这一次我们的测试都通过了
### 测试组
我们现在还想要测试一下split函数对中文字符串的支持，这个时候我们可以再编写一个TestChineseSplit测试函数，但是我们也可以使用如下更友好的一种方式来添加更多的测试用例。
```go
func TestSplit(t *testing.T) {
   // 定义一个测试用例类型
    type test struct {
        input string
        sep   string
        want  []string
    }
    // 定义一个存储测试用例的切片
    tests := []test{
        {input: "a:b:c", sep: ":", want: []string{"a", "b", "c"} },
        {input: "a:b:c", sep: ",", want: []string{"a:b:c"} },
        {input: "abcd", sep: "bc", want: []string{"a", "d"} },
        {input: "枯藤老树昏鸦", sep: "老", want: []string{"枯藤", "树昏鸦"} },
    }
    // 遍历切片，逐一执行测试用例
    for _, tc := range tests {
        got := Split(tc.input, tc.sep)
        if !reflect.DeepEqual(got, tc.want) {
            t.Errorf("excepted:%v, got:%v", tc.want, got)
        }
    }
} 
```
我们通过上面的代码把多个测试用例合到一起，再次执行go test命令。
```
 split $ go test -v
    === RUN   TestSplit
    --- FAIL: TestSplit (0.00s)
        split_test.go:42: excepted:[枯藤 树昏鸦], got:[ 枯藤 树昏鸦]
    FAIL
    exit status 1
    FAIL    github.com/pprof/studygo/code_demo/test_demo/split       0.006s
```
我们的测试出现了问题，仔细看打印的测试失败提示信息：excepted:[枯藤 树昏鸦], got:[ 枯藤 树昏鸦]，你会发现[ 枯藤 树昏鸦]中有个不明显的空串，这种情况下十分推荐使用%#v的格式化方式。

我们修改下测试用例的格式化输出错误提示部分：
```go
func TestSplit(t *testing.T) {
   ...

    for _, tc := range tests {
        got := Split(tc.input, tc.sep)
        if !reflect.DeepEqual(got, tc.want) {
            t.Errorf("excepted:%#v, got:%#v", tc.want, got)
        }
    }
} 
```
此时运行go test命令后就能看到比较明显的提示信息了：
```
 split $ go test -v
    === RUN   TestSplit
    --- FAIL: TestSplit (0.00s)
        split_test.go:42: excepted:[]string{"枯藤", "树昏鸦"}, got:[]string{"", "枯藤", "树昏鸦"}
    FAIL
    exit status 1
    FAIL    github.com/Q1mi/studygo/code_demo/test_demo/split       0.006s
```
### 子测试
看起来都挺不错的，但是如果测试用例比较多的时候，我们是没办法一眼看出来具体是哪个测试用例失败了。我们可能会想到下面的解决办法
```go
func TestSplit(t *testing.T) {
    type test struct { // 定义test结构体
        input string
        sep   string
        want  []string
    }
    tests := map[string]test{ // 测试用例使用map存储
        "simple":      {input: "a:b:c", sep: ":", want: []string{"a", "b", "c"} },
        "wrong sep":   {input: "a:b:c", sep: ",", want: []string{"a:b:c"} },
        "more sep":    {input: "abcd", sep: "bc", want: []string{"a", "d"} },
        "leading sep": {input: "枯藤老树昏鸦", sep: "老", want: []string{"枯藤", "树昏鸦"} },
    }
    for name, tc := range tests {
        got := Split(tc.input, tc.sep)
        if !reflect.DeepEqual(got, tc.want) {
            t.Errorf("name:%s excepted:%#v, got:%#v", name, tc.want, got) // 将测试用例的name格式化输出
        }
    }
} 
```
上面的做法是能够解决问题的。同时Go1.7+中新增了子测试，我们可以按照如下方式使用t.Run执行子测试：
```go
func TestSplit(t *testing.T) {
    type test struct { // 定义test结构体
        input string
        sep   string
        want  []string
    }
    tests := map[string]test{ // 测试用例使用map存储
        "simple":      {input: "a:b:c", sep: ":", want: []string{"a", "b", "c"} },
        "wrong sep":   {input: "a:b:c", sep: ",", want: []string{"a:b:c"} },
        "more sep":    {input: "abcd", sep: "bc", want: []string{"a", "d"} },
        "leading sep": {input: "枯藤老树昏鸦", sep: "老", want: []string{"枯藤", "树昏鸦"} },
    }
    for name, tc := range tests {
        t.Run(name, func(t *testing.T) { // 使用t.Run()执行子测试
            got := Split(tc.input, tc.sep)
            if !reflect.DeepEqual(got, tc.want) {
                t.Errorf("excepted:%#v, got:%#v", tc.want, got)
            }
        })
    }
}
```
此时我们再执行go test命令就能够看到更清晰的输出内容了：
```
    split $ go test -v
    === RUN   TestSplit
    === RUN   TestSplit/leading_sep
    === RUN   TestSplit/simple
    === RUN   TestSplit/wrong_sep
    === RUN   TestSplit/more_sep
    --- FAIL: TestSplit (0.00s)
        --- FAIL: TestSplit/leading_sep (0.00s)
            split_test.go:83: excepted:[]string{"枯藤", "树昏鸦"}, got:[]string{"", "枯藤", "树昏鸦"}
        --- PASS: TestSplit/simple (0.00s)
        --- PASS: TestSplit/wrong_sep (0.00s)
        --- PASS: TestSplit/more_sep (0.00s)
    FAIL
    exit status 1
    FAIL    github.com/pprof/studygo/code_demo/test_demo/split       0.006s 
```
这个时候我们要把测试用例中的错误修改回来：
```go
func TestSplit(t *testing.T) {
    ...
    tests := map[string]test{ // 测试用例使用map存储
        "simple":      {input: "a:b:c", sep: ":", want: []string{"a", "b", "c"} },
        "wrong sep":   {input: "a:b:c", sep: ",", want: []string{"a:b:c"} },
        "more sep":    {input: "abcd", sep: "bc", want: []string{"a", "d"} },
        "leading sep": {input: "枯藤老树昏鸦", sep: "老", want: []string{"", "枯藤", "树昏鸦"} },
    }
    ...
}
```
我们都知道可以通过-run=RegExp来指定运行的测试用例，还可以通过/来指定要运行的子测试用例，例如：go test -v -run=Split/simple只会运行simple对应的子测试用例。
### 测试覆盖率
测试覆盖率是你的代码被测试套件覆盖的百分比。通常我们使用的都是语句的覆盖率，也就是在测试中至少被运行一次的代码占总代码的比例。

Go提供内置功能来检查你的代码覆盖率。我们可以使用go test -cover来查看测试覆盖率。例如：
```
    split $ go test -cover
    PASS
    coverage: 100.0% of statements
    ok      github.com/pprof/studygo/code_demo/test_demo/split       0.005s 
```
从上面的结果可以看到我们的测试用例覆盖了100%的代码。

Go还提供了一个额外的-coverprofile参数，用来将覆盖率相关的记录信息输出到一个文件。例如：
```
    split $ go test -cover -coverprofile=c.out
    PASS
    coverage: 100.0% of statements
    ok      github.com/pprof/studygo/code_demo/test_demo/split       0.005s 
```
上面的命令会将覆盖率相关的信息输出到当前文件夹下面的c.out文件中，然后我们执行go tool cover -html=c.out，使用cover工具来处理生成的记录信息，该命令会打开本地的浏览器窗口生成一个HTML报告。
### 基准测试
#### 基准测试函数格式
基准测试就是在一定的工作负载之下检测程序性能的一种方法。基准测试的基本格式如下：
```go
func BenchmarkName(b *testing.B){
    // ...
} 
```
基准测试以Benchmark为前缀，需要一个*testing.B类型的参数b，基准测试必须要执行b.N次，这样的测试才有对照性，b.N的值是系统根据实际情况去调整的，从而保证测试的稳定性。 testing.B拥有的方法如下：
```go
func (c *B) Error(args ...interface{})
func (c *B) Errorf(format string, args ...interface{})
func (c *B) Fail()
func (c *B) FailNow()
func (c *B) Failed() bool
func (c *B) Fatal(args ...interface{})
func (c *B) Fatalf(format string, args ...interface{})
func (c *B) Log(args ...interface{})
func (c *B) Logf(format string, args ...interface{})
func (c *B) Name() string
func (b *B) ReportAllocs()
func (b *B) ResetTimer()
func (b *B) Run(name string, f func(b *B)) bool
func (b *B) RunParallel(body func(*PB))
func (b *B) SetBytes(n int64)
func (b *B) SetParallelism(p int)
func (c *B) Skip(args ...interface{})
func (c *B) SkipNow()
func (c *B) Skipf(format string, args ...interface{})
func (c *B) Skipped() bool
func (b *B) StartTimer()
func (b *B) StopTimer()  
```
#### 基准测试示例
我们为split包中的Split函数编写基准测试如下：
```go
func BenchmarkSplit(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Split("枯藤老树昏鸦", "老")
    }
}
```
基准测试并不会默认执行，需要增加-bench参数，所以我们通过执行go test -bench=Split命令执行基准测试，输出结果如下：
```
    split $ go test -bench=Split
    goos: darwin
    goarch: amd64
    pkg: github.com/pprof/studygo/code_demo/test_demo/split
    BenchmarkSplit-8        10000000               203 ns/op
    PASS
    ok      github.com/pprof/studygo/code_demo/test_demo/split       2.255s
```
其中BenchmarkSplit-8表示对Split函数进行基准测试，数字8表示GOMAXPROCS的值，这个对于并发基准测试很重要。10000000和203ns/op表示每次调用Split函数耗时203ns，这个结果是10000000次调用的平均值。

我们还可以为基准测试添加-benchmem参数，来获得内存分配的统计数据。
```
    split $ go test -bench=Split -benchmem
    goos: darwin
    goarch: amd64
    pkg: github.com/pprof/studygo/code_demo/test_demo/split
    BenchmarkSplit-8        10000000               215 ns/op             112 B/op          3 allocs/op
    PASS
    ok      github.com/pprof/studygo/code_demo/test_demo/split       2.394s
```
其中，112 B/op表示每次操作内存分配了112字节，3 allocs/op则表示每次操作进行了3次内存分配。 我们将我们的Split函数优化如下：
```go
func Split(s, sep string) (result []string) {
    result = make([]string, 0, strings.Count(s, sep)+1)
    i := strings.Index(s, sep)
    for i > -1 {
        result = append(result, s[:i])
        s = s[i+len(sep):] // 这里使用len(sep)获取sep的长度
        i = strings.Index(s, sep)
    }
    result = append(result, s)
    return
}
```
这一次我们提前使用make函数将result初始化为一个容量足够大的切片，而不再像之前一样通过调用append函数来追加。我们来看一下这个改进会带来多大的性能提升：
```
    split $ go test -bench=Split -benchmem
    goos: darwin
    goarch: amd64
    pkg: github.com/pprof/studygo/code_demo/test_demo/split
    BenchmarkSplit-8        10000000               127 ns/op              48 B/op          1 allocs/op
    PASS
    ok      github.com/pprof/studygo/code_demo/test_demo/split       1.423s
```
这个使用make函数提前分配内存的改动，减少了2/3的内存分配次数，并且减少了一半的内存分配。
### x性能比较函数
上面的基准测试只能得到给定操作的绝对耗时，但是在很多性能问题是发生在两个不同操作之间的相对耗时，比如同一个函数处理1000个元素的耗时与处理1万甚至100万个元素的耗时的差别是多少？再或者对于同一个任务究竟使用哪种算法性能最佳？我们通常需要对两个不同算法的实现使用相同的输入来进行基准比较测试。

性能比较函数通常是一个带有参数的函数，被多个不同的Benchmark函数传入不同的值来调用。举个例子如下：
```go
func benchmark(b *testing.B, size int){/* ... */}
func Benchmark10(b *testing.B){ benchmark(b, 10) }
func Benchmark100(b *testing.B){ benchmark(b, 100) }
func Benchmark1000(b *testing.B){ benchmark(b, 1000) }  
```
例如我们编写了一个计算斐波那契数列的函数如下：
```go
// fib.go

// Fib 是一个计算第n个斐波那契数的函数
func Fib(n int) int {
    if n < 2 {
        return n
    }
    return Fib(n-1) + Fib(n-2)
} 
```
我们编写的性能比较函数如下：
```go
// fib_test.go

func benchmarkFib(b *testing.B, n int) {
    for i := 0; i < b.N; i++ {
        Fib(n)
    }
}

func BenchmarkFib1(b *testing.B)  { benchmarkFib(b, 1) }
func BenchmarkFib2(b *testing.B)  { benchmarkFib(b, 2) }
func BenchmarkFib3(b *testing.B)  { benchmarkFib(b, 3) }
func BenchmarkFib10(b *testing.B) { benchmarkFib(b, 10) }
func BenchmarkFib20(b *testing.B) { benchmarkFib(b, 20) }
func BenchmarkFib40(b *testing.B) { benchmarkFib(b, 40) }  
```
运行基准测试：
```
    split $ go test -bench=.
    goos: darwin
    goarch: amd64
    pkg: github.com/pprof/studygo/code_demo/test_demo/fib
    BenchmarkFib1-8         1000000000               2.03 ns/op
    BenchmarkFib2-8         300000000                5.39 ns/op
    BenchmarkFib3-8         200000000                9.71 ns/op
    BenchmarkFib10-8         5000000               325 ns/op
    BenchmarkFib20-8           30000             42460 ns/op
    BenchmarkFib40-8               2         638524980 ns/op
    PASS
    ok      github.com/pprof/studygo/code_demo/test_demo/fib 12.944s 
```
这里需要**注意**的是，默认情况下，每个基准测试至少运行1秒。如果在Benchmark函数返回时没有到1秒，则b.N的值会按1,2,5,10,20,50，…增加，并且函数再次运行。

最终的BenchmarkFib40只运行了两次，每次运行的平均值只有不到一秒。像这种情况下我们应该可以使用-benchtime标志增加最小基准时间，以产生更准确的结果。例如：
```
    split $ go test -bench=Fib40 -benchtime=20s
    goos: darwin
    goarch: amd64
    pkg: github.com/pprof/studygo/code_demo/test_demo/fib
    BenchmarkFib40-8              50         663205114 ns/op
    PASS
    ok      github.com/pprof/studygo/code_demo/test_demo/fib 33.849s 
```
这一次BenchmarkFib40函数运行了50次，结果就会更准确一些了。

使用性能比较函数做测试的时候一个容易犯的错误就是把b.N作为输入的大小，例如以下两个例子都是错误的示范：
```go
// 错误示范1
func BenchmarkFibWrong(b *testing.B) {
    for n := 0; n < b.N; n++ {
        Fib(n)
    }
}

// 错误示范2
func BenchmarkFibWrong2(b *testing.B) {
    Fib(b.N)
}  
```
### 重置时间
b.ResetTimer之前的处理不会放到执行时间里，也不会输出到报告中，所以可以在之前做一些不计划作为测试报告的操作。例如：
```go
func BenchmarkSplit(b *testing.B) {
    time.Sleep(5 * time.Second) // 假设需要做一些耗时的无关操作
    b.ResetTimer()              // 重置计时器
    for i := 0; i < b.N; i++ {
        Split("枯藤老树昏鸦", "老")
    }
} 
```
### 并行测试
func (b B) RunParallel(body func(PB))会以并行的方式执行给定的基准测试。

RunParallel会创建出多个goroutine，并将b.N分配给这些goroutine执行， 其中goroutine数量的默认值为GOMAXPROCS。用户如果想要增加非CPU受限（non-CPU-bound）基准测试的并行性， 那么可以在RunParallel之前调用SetParallelism 。RunParallel通常会与-cpu标志一同使用。
```go
func BenchmarkSplitParallel(b *testing.B) {
    // b.SetParallelism(1) // 设置使用的CPU数
    b.RunParallel(func(pb *testing.PB) {
        for pb.Next() {
            Split("枯藤老树昏鸦", "老")
        }
    })
}  
```
执行一下基准测试：
```
split $ go test -bench=.
goos: darwin
goarch: amd64
pkg: github.com/pprof/studygo/code_demo/test_demo/split
BenchmarkSplit-8                10000000               131 ns/op
BenchmarkSplitParallel-8        50000000                36.1 ns/op
PASS
ok      github.com/pprof/studygo/code_demo/test_demo/split       3.308s
```
还可以通过在测试命令后添加-cpu参数如go test -bench=. -cpu 1来指定使用的CPU数量。
### Setup与TearDown
测试程序有时需要在测试之前进行额外的设置（setup）或在测试之后进行拆卸（teardown）。

#### TestMain
通过在*_test.go文件中定义TestMain函数来可以在测试之前进行额外的设置（setup）或在测试之后进行拆卸（teardown）操作。

如果测试文件包含函数:func TestMain(m *testing.M)那么生成的测试会先调用 TestMain(m)，然后再运行具体测试。TestMain运行在主goroutine中, 可以在调用 m.Run前后做任何设置（setup）和拆卸（teardown）。退出测试的时候应该使用m.Run的返回值作为参数调用os.Exit。

一个使用TestMain来设置Setup和TearDown的示例如下：
```go
func TestMain(m *testing.M) {
    fmt.Println("write setup code here...") // 测试之前的做一些设置
    // 如果 TestMain 使用了 flags，这里应该加上flag.Parse()
    retCode := m.Run()                         // 执行测试
    fmt.Println("write teardown code here...") // 测试之后做一些拆卸工作
    os.Exit(retCode)                           // 退出测试
}  
```
需要**注意**的是：在调用TestMain时, flag.Parse并没有被调用。所以如果TestMain 依赖于command-line标志 (包括 testing 包的标记), 则应该显示的调用flag.Parse。

子测试的Setup与Teardown
有时候我们可能需要为每个测试集设置Setup与Teardown，也有可能需要为每个子测试设置Setup与Teardown。下面我们定义两个函数工具函数如下：
```go
// 测试集的Setup与Teardown
func setupTestCase(t *testing.T) func(t *testing.T) {
    t.Log("如有需要在此执行:测试之前的setup")
    return func(t *testing.T) {
        t.Log("如有需要在此执行:测试之后的teardown")
    }
}

// 子测试的Setup与Teardown
func setupSubTest(t *testing.T) func(t *testing.T) {
    t.Log("如有需要在此执行:子测试之前的setup")
    return func(t *testing.T) {
        t.Log("如有需要在此执行:子测试之后的teardown")
    }
}  
```
使用方式如下：
```go
func TestSplit(t *testing.T) {
    type test struct { // 定义test结构体
        input string
        sep   string
        want  []string
    }
    tests := map[string]test{ // 测试用例使用map存储
        "simple":      {input: "a:b:c", sep: ":", want: []string{"a", "b", "c"} },
        "wrong sep":   {input: "a:b:c", sep: ",", want: []string{"a:b:c"} },
        "more sep":    {input: "abcd", sep: "bc", want: []string{"a", "d"} },
        "leading sep": {input: "枯藤老树昏鸦", sep: "老", want: []string{"", "枯藤", "树昏鸦"} },
    }
    teardownTestCase := setupTestCase(t) // 测试之前执行setup操作
    defer teardownTestCase(t)            // 测试之后执行testdoen操作

    for name, tc := range tests {
        t.Run(name, func(t *testing.T) { // 使用t.Run()执行子测试
            teardownSubTest := setupSubTest(t) // 子测试之前执行setup操作
            defer teardownSubTest(t)           // 测试之后执行testdoen操作
            got := Split(tc.input, tc.sep)
            if !reflect.DeepEqual(got, tc.want) {
                t.Errorf("excepted:%#v, got:%#v", tc.want, got)
            }
        })
    }
} 
```
测试结果如下：
```
    split $ go test -v
    === RUN   TestSplit
    === RUN   TestSplit/simple
    === RUN   TestSplit/wrong_sep
    === RUN   TestSplit/more_sep
    === RUN   TestSplit/leading_sep
    --- PASS: TestSplit (0.00s)
        split_test.go:71: 如有需要在此执行:测试之前的setup
        --- PASS: TestSplit/simple (0.00s)
            split_test.go:79: 如有需要在此执行:子测试之前的setup
            split_test.go:81: 如有需要在此执行:子测试之后的teardown
        --- PASS: TestSplit/wrong_sep (0.00s)
            split_test.go:79: 如有需要在此执行:子测试之前的setup
            split_test.go:81: 如有需要在此执行:子测试之后的teardown
        --- PASS: TestSplit/more_sep (0.00s)
            split_test.go:79: 如有需要在此执行:子测试之前的setup
            split_test.go:81: 如有需要在此执行:子测试之后的teardown
        --- PASS: TestSplit/leading_sep (0.00s)
            split_test.go:79: 如有需要在此执行:子测试之前的setup
            split_test.go:81: 如有需要在此执行:子测试之后的teardown
        split_test.go:73: 如有需要在此执行:测试之后的teardown
    === RUN   ExampleSplit
    --- PASS: ExampleSplit (0.00s)
    PASS
    ok      github.com/Q1mi/studygo/code_demo/test_demo/split       0.006s
```
 
### 示例函数
#### 示例函数的格式

被go test特殊对待的第三种函数就是示例函数，它们的函数名以Example为前缀。它们既没有参数也没有返回值。标准格式如下：
```go
func ExampleName() {
    // ...
} 
```
#### 示例函数示例
下面的代码是我们为Split函数编写的一个示例函数：
```go
func ExampleSplit() {
    fmt.Println(split.Split("a:b:c", ":"))
    fmt.Println(split.Split("枯藤老树昏鸦", "老"))
    // Output:
    // [a b c]
    // [ 枯藤 树昏鸦]
} 
```
为你的代码编写示例代码有如下三个用处：
```
    示例函数能够作为文档直接使用，例如基于web的godoc中能把示例函数与对应的函数或包相关联。

    示例函数只要包含了// Output:也是可以通过go test运行的可执行测试。

        split $ go test -run Example
        PASS
        ok      github.com/pprof/studygo/code_demo/test_demo/split       0.006s
    示例函数提供了可以直接运行的示例代码，可以直接在golang.org的godoc文档服务器上使用Go Playground运行示例代码。下图为strings.ToUpper函数在Playground的示例函数效果
```
### func ToUpper
```go
func ToUpper(s string) string
```
ToUpper returms a copy of the sring s with all Unicode ltters mapped to their upper case.

Example:
```
 go
package main
import (
    "fnt"
    "strings"
)

func main() {
    fmt. Println(strings . ToUpper("Gopher"))
}
```
## 压力测试
### Go怎么写测试用例
开发程序其中很重要的一点是测试，我们如何保证代码的质量，如何保证每个函数是可运行，运行结果是正确的，又如何保证写出来的代码性能是好的，我们知道单元测试的重点在于发现程序设计或实现的逻辑错误，使问题及早暴露，便于问题的定位解决，而性能测试的重点在于发现程序设计上的一些问题，让线上的程序能够在高并发的情况下还能保持稳定。本小节将带着这一连串的问题来讲解Go语言中如何来实现单元测试和性能测试。

Go语言中自带有一个轻量级的测试框架testing和自带的go test命令来实现单元测试和性能测试，testing框架和其他语言中的测试框架类似，你可以基于这个框架写针对相应函数的测试用例，也可以基于该框架写相应的压力测试用例，那么接下来让我们一一来看一下怎么写。

另外建议安装gotests插件自动生成测试代码:
```
 go get -u -v github.com/cweill/gotests/...   
```
### 如何编写测试用例
由于go test命令只能在一个相应的目录下执行所有文件，所以我们接下来新建一个项目目录gotest,这样我们所有的代码和测试代码都在这个目录下。

接下来我们在该目录下面创建两个文件：gotest.go和gotest_test.go

gotest.go:这个文件里面我们是创建了一个包，里面有一个函数实现了除法运算:
```go
package gotest

    import (
        "errors"
    )

    func Division(a, b float64) (float64, error) {
        if b == 0 {
            return 0, errors.New("除数不能为0")
        }

        return a / b, nil
    }
```
  
gotest_test.go:这是我们的单元测试文件，但是记住下面的这些原则：

文件名必须是_test.go结尾的，这样在执行go test的时候才会执行到相应的代码

你必须import testing这个包

所有的测试用例函数必须是Test开头

测试用例会按照源代码中写的顺序依次执行

测试函数TestXxx()的参数是testing.T，我们可以使用该类型来记录错误或者是测试状态

测试格式：func TestXxx (t *testing.T),Xxx部分可以为任意的字母数字的组合，但是首字母不能是小写字母[a-z]，例如Testintdiv是错误的函数名。

函数中通过调用testing.T的Error, Errorf, FailNow, Fatal, FatalIf方法，说明测试不通过，调用Log方法用来记录测试的信息。

下面是我们的测试用例的代码：
```go
    package gotest

    import (
        "testing"
    )

    func Test_Division_1(t *testing.T) {
        if i, e := Division(6, 2); i != 3 || e != nil { //try a unit test on function
            t.Error("除法函数测试没通过") // 如果不是如预期的那么就报错
        } else {
            t.Log("第一个测试通过了") //记录一些你期望记录的信息
        }
    }

    func Test_Division_2(t *testing.T) {
        t.Error("就是不通过")
    }  
```
我们在项目目录下面执行go test,就会显示如下信息：
```
    --- FAIL: Test_Division_2 (0.00 seconds)
        gotest_test.go:16: 就是不通过
    FAIL
    exit status 1
    FAIL    gotest    0.013s   
```
从这个结果显示测试没有通过，因为在第二个测试函数中我们写死了测试不通过的代码t.Error，那么我们的第一个函数执行的情况怎么样呢？默认情况下执行go test是不会显示测试通过的信息的，我们需要带上参数go test -v，这样就会显示如下信息：
```
    === RUN Test_Division_1
    --- PASS: Test_Division_1 (0.00 seconds)
        gotest_test.go:11: 第一个测试通过了
    === RUN Test_Division_2
    --- FAIL: Test_Division_2 (0.00 seconds)
        gotest_test.go:16: 就是不通过
    FAIL
    exit status 1
    FAIL    gotest    0.012s  
```
 
上面的输出详细的展示了这个测试的过程，我们看到测试函数1Test_Division_1测试通过，而测试函数2Test_Division_2测试失败了，最后得出结论测试不通过。接下来我们把测试函数2修改成如下代码：
```go
func Test_Division_2(t *testing.T) {
        if _, e := Division(6, 0); e == nil { //try a unit test on function
            t.Error("Division did not work as expected.") // 如果不是如预期的那么就报错
        } else {
            t.Log("one test passed.", e) //记录一些你期望记录的信息
        }
    }  
```
   
然后我们执行go test -v，就显示如下信息，测试通过了：
```
    === RUN Test_Division_1
    --- PASS: Test_Division_1 (0.00 seconds)
        gotest_test.go:11: 第一个测试通过了
    === RUN Test_Division_2
    --- PASS: Test_Division_2 (0.00 seconds)
        gotest_test.go:20: one test passed. 除数不能为0
    PASS
    ok      gotest    0.013s   
```
```
//一般
go test -v -run funcName fileName.go
go test -v -cover -run funcName fileName.go
```
### 如何编写压力测试
压力测试用来检测函数(方法）的性能，和编写单元功能测试的方法类似,此处不再赘述，但需要**注意**以下几点：

压力测试用例必须遵循如下格式，其中XXX可以是任意字母数字的组合，但是首字母不能是小写字母
```go
    func BenchmarkXXX(b *testing.B) { ... }  
```
go test不会默认执行压力测试的函数，如果要执行压力测试需要带上参数-test.bench，语法:-test.bench=”test_name_regex”,例如go test -test.bench=".*"表示测试全部的压力测试函数

在压力测试用例中,请记得在循环体内使用testing.B.N,以使测试可以正常的运行
文件名也必须以_test.go结尾

下面我们新建一个压力测试文件webbench_test.go，代码如下所示：
```go
import (
    "testing"
)

func Benchmark_Division(b *testing.B) {
    for i := 0; i < b.N; i++ { //use b.N for looping 
        Division(4, 5)
    }
}

func Benchmark_TimeConsumingFunction(b *testing.B) {
    b.StopTimer() //调用该函数停止压力测试的时间计数

    //做一些初始化的工作,例如读取文件数据,数据库连接之类的,
    //这样这些时间不影响我们测试函数本身的性能

    b.StartTimer() //重新开始时间
    for i := 0; i < b.N; i++ {
        Division(4, 5)
    }
}  
```
我们执行命令go test webbench_test.go -test.bench=".*"，可以看到如下结果：
```
    Benchmark_Division-4                            500000000          7.76 ns/op         456 B/op          14 allocs/op
    Benchmark_TimeConsumingFunction-4            500000000          7.80 ns/op         224 B/op           4 allocs/op
    PASS
    ok      gotest    9.364s   
```
上面的结果显示我们没有执行任何TestXXX的单元测试函数，显示的结果只执行了压力测试函数，第一条显示了Benchmark_Division执行了500000000次，每次的执行平均时间是7.76纳秒，第二条显示了Benchmark_TimeConsumingFunction执行了500000000，每次的平均执行时间是7.80纳秒。最后一条显示总共的执行时间。
## BDD
Behavior Driven Development行为驱动开发

行为驱动开发（Behavior-Driven Development）（简写BDD），在软件工程中，BDD是一种敏捷软件开发的技术。行为驱动开发(BDD)是测试驱动开发的延伸，开发使用简单的，特定于领域的脚本语言。这些DSL将结构化自然语言语句转换为可执行测试。结果是与给定功能的验收标准以及用于验证该功能的测试之间的关系更密切。因此，它一般是测试驱动开发(TDD)测试的自然延伸。（摘自[百度百科](https://baike.baidu.com/item/%E8%A1%8C%E4%B8%BA%E9%A9%B1%E5%8A%A8%E5%BC%80%E5%8F%91/9424963)）


BDD in Go 一个测试框架
```
项⽬⽹站
https://github.com/smartystreets/goconvey
安装
go get -u github.com/smartystreets/goconvey/convey
启动 WEB UI 
$GOPATH/bin/goconvey
```
```go
package testing

import (
	"testing"

	. "github.com/smartystreets/goconvey/convey"
)

func TestSpec(t *testing.T) {

	// Only pass t into top-level Convey calls
	Convey("Given 2 even numbers", t, func() {
		a := 3
		b := 4

		Convey("When add the two numbers", func() {
			c := a + b

			Convey("Then the result is still even", func() {
				So(c%2, ShouldEqual, 0)
			})
		})
	})
}
```
convey这个pkg还提供了一个web界面，在gopath下的bin目录里面有二进制程序goconvey。
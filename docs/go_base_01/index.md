# Go_base_01

> 参考学习go语言中文网、C语言中文网、golang官方文档等

## 前言

计算机里的数据都是以字节形式进行存储和处理，从而需要编码来表达信息。ASCII是**简单字符集编码模型**，定义了这个字符集里包含的字符以及其映射成的8位比特值。
关于**现代编码模型**：
- 一个字符如何映射成有限长度的比特值？
    - 需要表示字符的范围--**字符表**（character repertoire）
    - CR映射到一个整数集，称映射为**编码字符集**（coded character set），也就是Unicode的概念，那些整数称为码点（code point）
    - 将CCS里的整数映射成有限长度的比特值，这个对应关系称为字符编码方式或**字符编码表**（character encoding form），比如UTF-8，UTF-16。（Unicode Transformation Format，8或者16是指码元的大小，码元是一个已编码文本中具有最短的比特组合的单元，即最小单位是一个字节或者两个字节）
- UTF-8是完全兼容ASCII的，多字节表示一个字符时Unicode码点范围以及对应的bit组合：
    - 一字节：U+00~U+7F--------------UTF-8字节流（二进制）：0xxxxxxx
    - 二字节：U+80~U+7FF-------------UTF-8字节流（二进制）：110xxxxx 10xxxxxx
    - 三字节：U+800~U+7FFF-----------UTF-8字节流（二进制）：1110xxxx 10xxxxxx 10xxxxxx
    - 四字节：U+10000~U+10FFFF-------UTF-8字节流（二进制）：11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
    - 汉字大多是三字节

关于Unicode和UTF-8：

```go
func TestString(t *testing.T) {
	var s string
	t.Log(s) //初始化为默认零值“”
	s = "hello"
	t.Log(len(s))
	//s[1] = '3' //string是不可变的byte slice
	//s = "\xE4\xB8\xA5" //可以存储任何二进制数据
	s = "\xE4\xBA\xBB\xFF"
	t.Log(s)
	t.Log(len(s))
	s = "中"
	t.Log(len(s)) //是byte数

	c := []rune(s)
	t.Log(len(c))
	//	t.Log("rune size:", unsafe.Sizeof(c[0]))
	t.Logf("中 unicode %x", c[0])
	t.Logf("中 UTF8 %x", s)
}
```
```
Running tool: D:\go\bin\go.exe test -timeout 30s -run ^TestString$ code/code/ch9/string

=== RUN   TestString
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:9:
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:11: 5
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:15: 亻�
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:16: 4
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:18: 3
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:21: 1
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:23: 中 unicode 4e2d
    d:\Go\Go_WorkSpace\go_learning-master\code\ch9\string\string_test.go:24: 中 UTF8 e4b8ad
--- PASS: TestString (0.00s)
PASS
ok      code/code/ch9/string    0.514s
```
## go起源
07年 三位大牛 解决三个困难：多核硬件架构、超大规模的分布式计算集群、如今使用的web开发模式导致的前所未有的开发规模和更新速度

Go 官方下载站点是 golang.org/dl，但我们可以用针对中国大陆的镜像站点 golang.google.cn/dl 来下载
## 主要特征
1. 自动立即回收。(自带GC)
2. 更丰富的内置类型。
3. 函数多返回值。
4. 错误处理。
5. 匿名函数和闭包。
6. 类型和接口。
7. 并发编程。
8. 反射。
9. 有复合，无继承（因为复合大于继承，干脆不要继承）

Go的函数、变量、常量、自定义类型、包(package)的命名方式遵循以下规则：
- 1）首字符可以是任意的Unicode字符或者下划线
- 2）剩余字符可以是Unicode字符、下划线、数字
- 3）字符长度不限

25关键字：
```go
    break        default      func         interface    select
    case         defer        go           map          struct
    chan         else         goto         package      switch
    const        fallthrough  if           range        type
    continue     for          import       return       var
```
- select和switch:select只能应用于channel的操作，既可以用于channel的数据接收，也可以用于channel的数据发送。如果select的多个分支都满足条件，则会随机的选取其中一个满足条件的分支。而switch用于一般的分支判断，顺序执行。
- continue
    - 如果循环体中的代码执行到一半，要中断当前迭代，忽略此迭代循环体中的后续代码（循环后置语句eg:x++不会被忽略），并回到 for 循环条件判断，尝试开启下一次迭代,使用continue关键字。
    - 看上去好像和C没区别，其实Go的continue增加了对lable的支持，label 语句的作用，是标记跳转的目标。在中断内层 for 循环，回到外层 for 循环继续执行的场景应用比较合适
```go
func main() {
    var sum int
    var sl = []int{1, 2, 3, 4, 5, 6}

loop:
    for i := 0; i < len(sl); i++ {
        if sl[i]%2 == 0 {
            // 忽略切片中值为偶数的元素
            continue loop
        }
        sum += sl[i]
    }
    println(sum) // 9
}
```
- **注意**与goto的区别：
    - 一旦使用 goto 跳转，那么不管是内层循环还是外层循环都会被终结，代码将会从 outerloop 这个 label 处，开始重新执行我们的嵌套循环语句，这与带 label 的 continue 的跳转语义是完全不同的。

goto 是一种公认的、难于驾驭的语法元素，应用 goto 的代码可读性差、代码难于维护还易错。而 Go 语言保留了 goto，具体我不得而知

- break
    - Go 语言规范中明确规定，不带 label 的 break 语句中断执行并跳出的，是同一函数内 break 语句所在的最内层的 for、switch 或 select


```go
package main
import "time"
import "fmt"
func main() {
    c1 := make(chan string)
    c2 := make(chan string)
    go func() {
        time.Sleep(time.Second * 1)
        c1 <- "one"
    }()
    go func() {
        time.Sleep(time.Second * 2)
        c2 <- "two"
    }()
    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-c1:
            fmt.Println("received", msg1)
        case msg2 := <-c2:
            fmt.Println("received", msg2)
        }
    }
}
```
- fallthrough:可以使用fallthrough强制执行该case执行完下一条case代码，fallthrough不会判断下一条case的判断结果是否为true。
37个保留字：
```go
    Constants:    true  false  iota  nil

    Types:    int  int8  int16  int32  int64  
              uint  uint8  uint16  uint32  uint64  uintptr
              float32  float64  complex128  complex64
              bool  byte  rune  string  error

    Functions:   make  len  cap  new  append  copy  close  delete
                 complex  real  imag
                 panic  recover
```
- new&make
    - func new(Type) *Type
    - func make(t Type, size ...IntegerType) Type

go声明：var,const,type,func

## 内置类型与函数
值类型：
```go
bool
int(32 or 64), int8, int16, int32, int64
uint(32 or 64), uint8(byte), uint16, uint32, uint64
float32, float64
string
complex64, complex128
array
```
**引用类型**（声明的同时需要分配内存空间，不然会引发panic，分配内存可以用new或者make）：
```go
slice
map
chan
……
```
内置函数（无需导入即可使用）:
```go
append          -- 用来追加元素到数组、slice中,返回修改后的数组、slice
close           -- 主要用来关闭channel
delete            -- 从map中删除key对应的value
panic            -- 停止常规的goroutine  （panic和recover：用来做错误处理）
recover         -- 允许程序定义goroutine的panic动作
real            -- 返回complex的实部   （complex、real imag：用于创建和操作复数）
imag            -- 返回complex的虚部
make            -- 用来分配内存，返回Type本身(只能应用于slice, map, channel) make 函数允许在运行期动态指定数组长度，绕开了数组类型必须使用编译期常量的限制。
new                -- 用来分配内存，主要用来分配值类型，比如int、struct。返回指向Type的指针
cap                -- capacity用于返回某个类型的最大容量（只能用于切片和 map）
copy            -- 用于复制和连接slice，返回复制的数目，copy(a,b) 只有 min(len(a),len(b))个元素会被成功拷贝。
len                -- 用来求长度，比如string、array、slice、map、channel ，返回长度
print、println     -- 底层打印函数，在部署环境中建议使用 fmt 包
```
内置接口error：
```go
type error interface { //只要实现了Error()函数，返回值为String的都实现了err接口（鸭子类型）
    Error()    String
}
```
## init & main 以及Go包的初始化顺序
go语言中init函数用于包(package)的初始化，该函数是go语言的一个重要特性。有下面的特征：
1. init函数是用于程序执行前做包的初始化的函数，比如初始化包里的变量等
2. 每个包可以拥有多个init函数
3. 包的每个源文件也可以拥有多个init函数
4. 同一个包中多个init函数的执行顺序go语言没有明确的定义(说明)
5. 不同包的init函数按照包导入的依赖关系决定该初始化函数的执行顺序
6. init函数不能被其他函数调用，而是在main函数执行之前，自动被调用

init()函数的用途：
- 重置包级变量值，对包内部以及暴露到外部的包级数据（主要是包级变量）的初始状态进行检查
- 实现对包级变量的复杂初始化，有些包级变量需要一个比较复杂的初始化过程，使用init()比较合适
- 在 init 函数中实现“注册模式”，如下
```go

import (
    "database/sql"
    _ "github.com/lib/pq"
)

func main() {
    db, err := sql.Open("postgres", "user=pqgotest dbname=pqgotest sslmode=verify-full")
    if err != nil {
        log.Fatal(err)
    }
    
    age := 21
    rows, err := db.Query("SELECT name FROM users WHERE age = $1", age)
    ...
}
```
```go
//pq包的init()
func init() {
    sql.Register("postgres", &Driver{})
}
```
pq 包将自己实现的 sql 驱动注册到了 sql 包中。这样只要应用层代码在 Open 数据库的时候，传入驱动的名字（这里是“postgres”)，那么通过 sql.Open 函数，返回的数据库实例句柄对数据库进行的操作，实际上调用的都是 pq 包中相应的驱动实现。

从标准库 database/sql 包的角度来看，这种“注册模式”实质是一种工厂设计模式的实现，sql.Open 函数就是这个模式中的工厂方法，**它根据外部传入的驱动名称“生产”出不同类别的数据库实例句柄。**

这种“注册模式”在标准库的其他包中也有广泛应用，比如说，使用标准库 image 包获取各种格式图片的宽和高：
```go

package main

import (
    "fmt"
    "image"
    _ "image/gif" // 以空导入方式注入gif图片格式驱动
    _ "image/jpeg" // 以空导入方式注入jpeg图片格式驱动
    _ "image/png" // 以空导入方式注入png图片格式驱动
    "os"
)

func main() {
    // 支持png, jpeg, gif
    width, height, err := imageSize(os.Args[1]) // 获取传入的图片文件的宽与高
    if err != nil {
        fmt.Println("get image size error:", err)
        return
    }
    fmt.Printf("image size: [%d, %d]\n", width, height)
}

func imageSize(imageFile string) (int, int, error) {
    f, _ := os.Open(imageFile) // 打开图文文件
    defer f.Close()

    img, _, err := image.Decode(f) // 对文件进行解码，得到图片实例
    if err != nil {
        return 0, 0, err
    }

    b := img.Bounds() // 返回图片区域
    return b.Max.X, b.Max.Y, nil
}
```
```go
// $GOROOT/src/image/png/reader.go
func init() {
    image.RegisterFormat("png", pngHeader, Decode, DecodeConfig)
}

// $GOROOT/src/image/jpeg/reader.go
func init() {
    image.RegisterFormat("jpeg", "\xff\xd8", Decode, DecodeConfig)
}

// $GOROOT/src/image/gif/reader.go
func init() {
    image.RegisterFormat("gif", "GIF8?a", Decode, DecodeConfig)
}  
```

Go语言程序的默认入口函数(主函数)：
```go
func main(){
    ……
    //通过os.Args获取参数eg:os.Args[0]
    //不支持返回值，可以通过os.Exit()来返回状态
}
```
在启动了多个 Goroutine 的 Go 应用中，main.main 函数将在 Go 应用的主 Goroutine 中执行。

init函数和main函数的异同：
- 同
    - 两个函数在定义时不能有任何的参数和返回值，且Go程序自动调用。
- 异
    - init可以应用于任意包中，且可以重复定义多个。
    - main函数只能用于main包中，且只能定义一个。

init()执行顺序：
- 对同一个go文件的init()调用顺序是从上到下的。
- 对同一个package中不同文件是按**文件名字符串比较“从小到大”顺序**调用各文件中的init()函数。
- 对于不同的package，如果不相互依赖的话，按照main包中”先import的后调用”的顺序调用其包中的init()，如果package存在依赖，则先调用最早被依赖的package中的init()，最后调用main函数。
- 同一个包被多次调用只会执行一次init()函数
- 如果init函数中使用了println()或者print()你会发现在执行过程中这两个不会按照你想象中的顺序执行。这两个函数官方只推荐在测试环境中使用，对于正式环境不要使用。

Go包初始化：**从main包开始按照深度优先初始化main包的依赖包，初始化一个包时的顺序是初始化依赖包、常量、变量、init()，回到main包时同样初始化常量、变量、init()，再执行main()函数。**

## go命令
```
PS D:\Blog\JF-011101.github.io-blog\content\posts> go
Go is a tool for managing Go source code.

Usage:

        go <command> [arguments]

The commands are:

        bug         start a bug report
        build       compile packages and dependencies       
        clean       remove object files and cached files    
        doc         show documentation for package or symbol
        env         print Go environment information        
        fix         update packages to use new APIs
        fmt         gofmt (reformat) package sources        
        generate    generate Go files by processing source  
        get         add dependencies to current module and install them
        install     compile and install packages and dependencies
        list        list packages or modules
        mod         module maintenance
        run         compile and run Go program
        test        test packages
        tool        run specified go tool
        version     print Go version
        vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.

Additional help topics:

        buildconstraint build constraints
        buildmode       build modes
        c               calling between Go and C
        cache           build and test caching
        environment     environment variables
        filetype        file types
        go.mod          the go.mod file
        gopath          GOPATH environment variable
        gopath-get      legacy GOPATH go get
        goproxy         module proxy protocol
        importpath      import path syntax
        modules         modules, module versions, and more
        module-get      module-aware go get
        module-auth     module authentication using go.sum
        packages        package lists and patterns
        private         configuration for downloading non-public code
        testflag        testing flags
        testfunc        testing functions
        vcs             controlling version control with GOVCS

Use "go help <topic>" for more information about that topic.
```
### go env
用于打印Go语言的环境信息。

### go run命令
可以编译并运行命令源码文件。

### go get
可以根据要求和实际情况从互联网上下载或更新指定的代码包及其依赖包，并对它们进行编译和安装。

### go build命令
用于编译我们指定的源码文件或代码包以及它们的依赖包。

### go install
用于编译并安装指定的代码包及它们的依赖包。

### go clean命令
会删除掉执行其它命令时产生的一些文件和目录。

### go doc命令
可以打印附于Go语言程序实体上的文档。我们可以通过把程序实体的标识符作为该命令的参数来达到查看其文档的目的。

### go test命令
用于对Go语言编写的程序进行测试。
在命名文件时需要让文件必须以_test结尾。默认的情况下，go test命令不需要任何的参数，它会自动把你源码包下面所有 test 文件测试完毕，当然你也可以带上参数。

这里介绍几个常用的参数：
```
-bench regexp 执行相应的 benchmarks，例如 -bench=.；
-cover 开启测试覆盖率；
-run regexp 只运行 regexp 匹配的函数，例如 -run=Array 那么就执行包含有 Array 开头的函数；
-v 显示测试的详细命令。
```
单元测试源码文件可以由多个测试用例组成，每个测试用例函数需要以Test为前缀。**测试用例文件不会参与正常源码编译，不会被包含到可执行文件中。**测试用例文件使用go test指令来执行，没有也不需要 main() 作为函数入口。所有在以_test结尾的源码内以Test开头的函数会自动被执行。测试用例可以不传入 *testing.T 参数。单元（功能）测试以testing.T为参数，性能（压力）测试以testing.B为参数。

运行指定示例：
```
PS D:\Go\Go_WorkSpace\go_learning-master\code\ch2\constant_test> go test -v -run TestTest test_test.go
=== RUN   TestTest
    test_test.go:6: kk
--- PASS: TestTest (0.00s)
PASS
ok      command-line-arguments  0.425s
```
t.FailNow()-----------标记错误并终止当前测试用例
t.Fail()--------------仅标记错误

每个测试用例可能并发执行，使用 testing.T 提供的日志输出可以保证日志跟随这个测试上下文一起打印输出。testing.T 提供了几种日志输出方法：
```
Log	    打印日志
Logf	格式化打印日志
Error	打印错误日志
Errorf	格式化打印错误日志
Fatal	打印致命日志
Fatalf	格式化打印致命日志
```
单元测试使用示例：
```go
//demo.go
package demo
// 冒泡排序
func BubbleSort(list []int) []int {
	n := len(list)
	for i := n - 1; i > 0; i-- {
		for j := 0; j < i; j++ {
			if list[j] > list[j+1] {
				list[j], list[j+1] = list[j+1], list[j]
			}
		}
	}
	return list
}
```
```go
//demo_test.go
package demo
import "testing"
func TestBubbleSort(t *testing.T) {
    area := BubbleSort([]list{2,1,3,4,65,13,22})
    if area != {1,2,3,4,13,22,65} {
        t.Error("测试失败")
    }
}
```
执行：
```
PS D:\code> go test -v
=== RUN   TestGetArea
--- PASS: TestGetArea (0.00s)
PASS
ok      _/D_/code       0.435s
```
#### 基准测试——获得代码内存占用和运行效率的性能数据
使用者无须准备高精度的计时器和各种分析工具，基准测试本身即可以打印出非常标准的测试报告。
```go
package code
import "testing"
func Benchmark_Add(b *testing.B) {
    var n int
    for i := 0; i < b.N; i++ {
        n++
    }
}
```
这段代码使用基准测试框架测试加法性能。第 7 行中的 b.N 由基准测试框架提供。测试代码需要保证函数可重入性及无状态，也就是说，测试代码不使用全局变量等带有记忆性质的数据结构。避免多次运行同一段代码时的环境不一致，不能假设 N 值范围。
```
//-bench=.相当于-run。在windows下使用-bench="."
$ go test -v -bench=. benchmark_test.go
goos: linux
goarch: amd64
Benchmark_Add-4           20000000         0.33 ns/op
// 20000000指的是测试执行次数
PASS
ok          command-line-arguments        0.700s
```
基准测试原理：基准测试框架对一个测试用例的默认测试时间是 1 秒。开始测试时，当以 Benchmark 开头的基准测试用例函数返回时还不到 1 秒，那么 testing.B 中的 N 值将按 1、2、5、10、20、50……递增，同时以递增后的值重新调用基准测试用例函数。

通过-benchtime参数可以自定义测试时间，例如：
```
$ go test -v -bench=. -benchtime=5s benchmark_test.go
goos: linux
goarch: amd64
Benchmark_Add-4           10000000000                 0.33 ns/op
PASS
ok          command-line-arguments        3.380s
```
基准测试可以对一段代码可能存在的内存分配进行统计，下面是一段使用字符串格式化的函数，内部会进行一些分配操作。
```go
func Benchmark_Alloc(b *testing.B) {
    for i := 0; i < b.N; i++ {
        fmt.Sprintf("%d", i)
    }
}
```
```
$ go test -v -bench=Alloc -benchmem benchmark_test.go
goos: linux
goarch: amd64
Benchmark_Alloc-4 20000000 109 ns/op 16 B/op 2 allocs/op
PASS
ok          command-line-arguments        2.311s
```
- 第 1 行的代码中-bench后添加了 Alloc，指定只测试 Benchmark_Alloc() 函数。
- 第 4 行代码的“16 B/op”表示每一次调用需要分配 16 个字节，“2 allocs/op”表示每一次调用有两次分配
- 开发者根据这些信息可以迅速找到可能的分配点，进行优化和调整。

**控制计时器**：有些测试需要一定的启动和初始化时间，如果从 Benchmark() 函数开始计时会很大程度上影响测试结果的精准性。testing.B 提供了一系列的方法可以方便地控制计时器，从而让计时器只在需要的区间进行测试。我们通过下面的代码来了解计时器的控制。
```go
func Benchmark_Add_TimerControl(b *testing.B) {
    // 重置计时器
    b.ResetTimer()
    // 停止计时器
    b.StopTimer()
    // 开始计时器
    b.StartTimer()
    var n int
    for i := 0; i < b.N; i++ {
        n++
    }
}
```
从 Benchmark() 函数开始，Timer 就开始计数。计数器内部不仅包含耗时数据，还包括内存分配的数据。
### go list命令
作用是列出指定的代码包的信息。

### go fix
会把指定代码包的所有Go语言源码文件中的旧版本代码修正为新版本的代码。

### go vet
是一个用于检查Go语言源码中静态错误的简单工具。比如是否存在变量遮蔽。
```
$go install golang.org/x/tools/go/analysis/passes/shadow/cmd/shadow@latest
go: downloading golang.org/x/tools v0.1.5
go: downloading golang.org/x/mod v0.4.2
```
```
$go vet -vettool=$(which shadow) -strict complex.go 
./complex.go:13:12: declaration of "err" shadows declaration at line 11
```
### go tool pprof命令
交互式的访问概要文件的内容。监测进程的运行数据，用于监控程序的性能，对内存使用和CPU使用的情况统信息进行分析。官方提供了两个包**：runtime/pprof和net/http/pprof**，前者用于普通代码的性能分析，后者用于web服务器的性能分析。
#### runtime/pprof
```
示例
PS D:\Go\Go_WorkSpace\go_learning-master\code\ch46\tools\file> go tool pprof prof cpu.prof
prof: open prof: The system cannot find the file specified.
Fetched 1 source profiles out of 2
Type: cpu
Time: Mar 10, 2022 at 10:41am (CST)
Duration: 2.77s, Total samples = 1.70s (61.39%)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top
Showing nodes accounting for 1.67s, 98.24% of 1.70s total
Showing top 10 nodes out of 34
      flat  flat%   sum%        cum   cum%
     0.49s 28.82% 28.82%      1.62s 95.29%  main.fillMatrix
     0.35s 20.59% 49.41%      1.13s 66.47%  math/rand.(*Rand).Intn
     0.33s 19.41% 68.82%      0.78s 45.88%  math/rand.(*Rand).Int31n
     0.14s  8.24% 77.06%      0.14s  8.24%  math/rand.(*rngSource).Uint64 (inline)
     0.13s  7.65% 84.71%      0.45s 26.47%  math/rand.(*Rand).Int31 (inline)
     0.10s  5.88% 90.59%      0.24s 14.12%  math/rand.(*rngSource).Int63
     0.08s  4.71% 95.29%      0.32s 18.82%  math/rand.(*Rand).Int63
     0.02s  1.18% 96.47%      0.02s  1.18%  main.calculate
     0.02s  1.18% 97.65%      0.02s  1.18%  runtime.stdcall1
     0.01s  0.59% 98.24%      0.01s  0.59%  runtime.lock2
(pprof) list fillMatrix
Total: 1.70s
ROUTINE ======================== main.fillMatrix in D:\go\Go_WorkSpace\go_learning-master\code\ch46\tools\file\prof.go
     490ms      1.62s (flat, cum) 95.29% of Total
         .          .     16:
         .          .     17:func fillMatrix(m *[row][col]int) {
         .          .     18:   s := rand.New(rand.NewSource(time.Now().UnixNano()))
         .          .     19:
         .          .     20:   for i := 0; i < row; i++ {
      20ms       20ms     21:           for j := 0; j < col; j++ {
     470ms      1.60s     22:                   m[i][j] = s.Intn(100000)
         .          .     23:           }
         .          .     24:   }
         .          .     25:}
         .          .     26:
         .          .     27:func calculate(m *[row][col]int) {
(pprof) 
```
top/tree/web
- top [n]，查看排名前n个数据，默认为10。（这个函数本身占用的时间，以及这个函数包括调用其他函数的时间）
- tree [n]，以树状图形式显示，默认显示10个。
#### net/http/pprof

### go modules
go modules 是 golang 1.11 新加的特性
- go mod
    - download
        - download modules to local cache(下载依赖包)
    - edit
        - edit go.mod from tools or scripts（编辑go.mod）
    - graph
        - print module requirement graph (打印模块依赖图)
    - init
        - initialize new module in current directory（在当前目录初始化mod）
    - tidy
        - add missing and remove unused modules(拉取缺少的模块，移除不用的模块,常用)
    - vendor
        - make vendored copy of dependencies(将依赖复制到vendor下)
    - verify
        - verify dependencies have expected content (验证依赖是否正确
    - why
        - explain why packages or modules are needed(解释为什么需要依赖)
- go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。go toolchain会在各类命令执行时，比如go get、go build、go mod等修改和维护go.mod文件。
## 运算符
Go 语言内置的运算符有：
- 算术运算符
    - ++ 和 -- **只有后置的，没有前置的，只作为语句，不作为表达式**
- 关系运算符
- 逻辑运算符
- 位运算符
    - **按位置零运算符 x &^ y ------如果y非零，则z为0；如果y为零，则z为x（先非再与）**，**注意**按位运算
- 赋值运算符（**一个赋值语句可以给多个变量进行赋值，多重赋值时，变量的左值和右值按从左到右的顺序赋值。多重赋值在 Go 语言的错误处理和函数返回值中会大量地使用。**）
    - =	简单的赋值运算符，将一个表达式的值赋给一个左值
    - +=	相加后再赋值
    - -=	相减后再赋值
    - *=	相乘后再赋值
    - /=	相除后再赋值
    - %=	求余后再赋值
    - <<=	左移后赋值
    - \>\>=	右移后赋值
    - &=	按位与后赋值
    - l=	按位或后赋值
    - ^=	按位异或后赋值
## 下划线
“_”是特殊标识符，用来忽略结果。
- 下划线在import中
    -  import 下划线（如：import _ hello/imp）的作用：当导入一个包时，该包下的文件里所有init()函数都会被执行，然而，有些时候我们并不需要把整个包都导入进来，仅仅是是希望它执行init()函数而已。这个时候就可以使用 import _ 引用该包。即使用【import _ 包路径】只是引用该包，仅仅是为了调用init()函数，所以无法通过包名来调用包中的其他函数。
    - **注意**区别于.在import中，它是指import进来的package里的所有的方法是在当前的名字空间的，使用其方法时直接使用即可。（请勿使用 import . 记法，它可以简化必须在被测试包外运行的测试， 除此之外应尽量避免使用。----effective go）
- 下划线在代码中
    - 作为占位符
## 格式占位符%…
```go
普通占位符
占位符     说明                           举例                   输出
%v      相应值的默认格式。            Printf("%v", people)   {zhangsan}，
%+v     打印结构体时，会添加字段名     Printf("%+v", people)  {Name:zhangsan}
%#v     相应值的Go语法表示            Printf("#v", people)   main.Human{Name:"zhangsan"}
%T      相应值的类型的Go语法表示       Printf("%T", people)   main.Human
%%      字面上的百分号，并非值的占位符  Printf("%%")            %

布尔占位符
占位符       说明                举例                     输出
%t          true 或 false。     Printf("%t", true)       true

整数占位符
占位符     说明                                  举例                       输出
%b      二进制表示                             Printf("%b", 5)             101
%c      相应Unicode码点所表示的字符              Printf("%c", 0x4E2D)        中
%d      十进制表示                             Printf("%d", 0x12)          18
%o      八进制表示                             Printf("%o", 10)            12
%q      单引号围绕的字符字面值，由Go语法安全地转义 Printf("%q", 0x4E2D)        '中'
%x      十六进制表示，字母形式为小写 a-f         Printf("%x", 13)             d
%X      十六进制表示，字母形式为大写 A-F         Printf("%x", 13)             D
%U      Unicode格式：U+1234，等同于 "U+%04X"   
Printf("%U", 0x4E2D)         U+4E2D

浮点数和复数的组成部分（实部和虚部）
占位符     说明                              举例            输出
%b      无小数部分的，指数为二的幂的科学计数法，
        与 strconv.FormatFloat 的 'b' 转换格式一致。例如 -123456p-78
%e      科学计数法，例如 -1234.456e+78        Printf("%e", 10.2)     1.020000e+01
%E      科学计数法，例如 -1234.456E+78        Printf("%e", 10.2)     1.020000E+01
%f      有小数点而无指数，例如 123.456        Printf("%f", 10.2)     10.200000
%g      根据情况选择 %e 或 %f 以产生更紧凑的（无末尾的0）输出 Printf("%g", 10.20)   10.2
%G      根据情况选择 %E 或 %f 以产生更紧凑的（无末尾的0）输出 Printf("%G", 10.20+2i) (10.2+2i)

字符串与字节切片
占位符     说明                              举例                           输出
%s      输出字符串表示（string类型或[]byte)   Printf("%s", []byte("Go语言"))  Go语言
%q      双引号围绕的字符串，由Go语法安全地转义  Printf("%q", "Go语言")         "Go语言"
%x      十六进制，小写字母，每字节两个字符      Printf("%x", "golang")         676f6c616e67
%X      十六进制，大写字母，每字节两个字符      Printf("%X", "golang")         676F6C616E67

指针
占位符         说明                      举例                             输出
%p      十六进制表示，前缀 0x          Printf("%p", &people)             0x4f57f0

其它标记
占位符      说明                             举例          输出
+      总打印数值的正负号；对于%q（%+q）保证只输出ASCII编码的字符。 
                                           Printf("%+q", "中文")  "\u4e2d\u6587"
-      在右侧而非左侧填充空格（左对齐该区域）
#      备用格式：为八进制添加前导 0（%#o）      Printf("%#U", '中')      U+4E2D
       为十六进制添加前导 0x（%#x）或 0X（%#X），为 %p（%#p）去掉前导 0x；
       如果可能的话，%q（%#q）会打印原始 （即反引号围绕的）字符串；
       如果是可打印字符，%U（%#U）会写出该字符的
       Unicode 编码形式（如字符 x 会被打印成 U+0078 'x'）。
' '    (空格)为数值中省略的正负号留出空白（% d）；
       以十六进制（% x, % X）打印字符串或切片时，在字节之间用空格隔开
0      填充前导的0而非空格；对于数字，这会将填充移到正负号之后
```
golang没有 '%u' 点位符，若整数为无符号类型，默认就会被打印成无符号的。

宽度与精度的控制格式以Unicode码点为单位。宽度为该数值占用区域的最小宽度；精度为小数点之后的位数。

操作数的类型为int时，宽度与精度都可用字符 '*' 表示。

对于 %g/%G 而言，精度为所有数字的总数，例如：123.45，%.4g 会打印123.5，（而 %6.2f 会打印123.45）。

%e 和 %f 的默认精度为6

对大多数的数值类型而言，宽度为输出的最小字符数，如果必要的话会为已格式化的形式填充空格。

而以字符串类型，精度为输出的最大字符数，如果必要的话会直接截断。
## 变量和常量
变量：
- 为什么要有变量：程序运行过程中的数据都是保存在内存中，我们想要在代码中操作某个数据时就需要去内存上找到这个变量，但是如果我们直接在代码中通过内存地址去操作变量的话，代码的可读性会非常差而且还容易出错，所以我们就利用变量将这个数据的内存地址保存起来，以后直接通过这个变量就能找到内存上对应的数据了。
- Go语言中的变量需要声明后才能使用，同一作用域内不支持重复声明。并且Go语言的变量声明后必须使用。
- 批量声明变量：（“声明聚类”）
```go
var (
        a string
        b int
        c bool
        d float32
    )
```
- 建议将延迟初始化的变量声明放在一个 var 声明块，将声明且显式初始化的变量放在另一个 var 块中
- 变量声明咱们一般采用就近原则，以实现变量的作用域最小化
- 在函数内部，可以使用更简略的 := 方式声明并初始化变量。
- 匿名变量_

常量:
```
const (
        pi = 3.1415
        e = 2.7182
    )
```
- 如果省略了值则表示和上面一行的值相同
- **iota**是go语言的常量计数器，只能在常量的表达式中使用。iota在const关键字出现时将被重置为0。const中每新增一行常量声明将使iota计数一次(iota可理解为const语句块中的行索引)。 使用iota能简化定义，在定义枚举时很有用。
    - 可以使用_跳过某些值
```go
 const (
            _  = iota
            KB = 1 << (10 * iota)
            MB = 1 << (10 * iota)
            GB = 1 << (10 * iota)
            TB = 1 << (10 * iota)
            PB = 1 << (10 * iota)
        )
 const (
            a, b = iota + 1, iota + 2 //1,2
            c, d                      //2,3
            e, f                      //3,4
        )
```
go在常量上还是有一定创新的：
- 与C对比：
    - C 语言中，原生不支持常量，字面值担负着常量的角色，C 语言的常用实践是使用宏（macro）定义记号来指代这些字面值，但它是一种仅在预编译阶段进行替换的字面值，继承了宏替换的复杂性和易错性，而且还有类型不安全、无法在调试时通过宏名字输出常量的值，等等问题。后续 C 标准中提供的 const 关键字修饰的标识符也不够完美，因为 const 关键字修饰的标识符本质上依旧是变量，它甚至无法用作数组变量声明中的初始长度（除非用 GNU 扩展 C）。
    - Go 原生提供的用 const 关键字定义的常量，整合了 C 语言中宏定义常量、const 修饰的“只读变量”，以及枚举常量这三种形式，并消除了每种形式的不足，使得 Go 常量是类型安全的，而且对编译器优化友好。
- 支持无类型常量；
    - eg: const n = 13
    - 常量 n 在声明时并没有显式地被赋予类型（但并非真的无类型，由初始值给予默认类型），在 Go 中，这样的常量就被称为无类型常量（Untyped Constant）
    - 但下面的例子里边为啥编译器不报错？
```go
type myInt int
const n = 13

func main() {
    var a myInt = 5
    fmt.Println(a + n)  // 输出：18
}
```
- 支持常量隐式自动转型；
    - 对于无类型常量参与的表达式求值，Go 编译器会根据上下文中的类型信息，把无类型常量自动转换为相应的类型后，再参与求值计算，这一转型动作是隐式进行的。但由于转型的对象是一个常量，所以这并不会引发类型安全问题，Go 编译器会保证这一转型的安全性。
    - 这就很好地解释了上面的问题。
    - **注意**：如果 Go 编译器在做隐式转型时，发现无法将常量转换为目标类型，Go 编译器也会报错，比如转型后溢出了。
- 以及前面有提到的可用于实现枚举。

## 基本类型
|类型|长度(字节)|默认值|说明|
|:--|:--|:--|:--|
bool   |1  |**false**
byte	|1	|0	|uint8，一个ASCII字符
rune	|4	|0	|Unicode Code Point，int32，一个utf-8字符,**c:=[]rune(s)//指将字符串s转化为rune的切片**
int, uint	|4或8	|0	|由操作系统位数(32/64)决定
int8, uint8 |1	|0	|-128 ~ 127, 0 ~ 255，**byte是uint8 的别名**
int16, uint16	|2	|0	|-32768 ~ 32767, 0 ~ 65535
int32, uint32	|4	|0	|-21亿~ 21亿, 0 ~ 42亿，**rune是int32 的别名**
int64, uint64	|8	|0	
float32	|4	|0.0	
float64	|8	|0.0	
complex64	|8		
complex128	|16		
uintptr	|4或8	|	|以存储指针的 uint32 或 uint64 整数
array	|	|	|值类型
struct	|	|	|值类型
string	|	|“”	|UTF-8 字符串
slice	|	|nil	|引用类型
map	|	|nil	|引用类型
channel	|	|nil	|引用类型
interface	|	|nil	|接口
function	|	|nil	|函数
- uintptr 实际上就是一个 uint 用来表示地址，go 的指针和 c 不一样不能进行偏移操作，如果非要偏移的话就需要 unsafe.Pointer 和 uintptr 配合来实现。uintptr 不是一个指针 所以 GC 时也不会处理 uintptr 的引用。如果不涉及地址偏移时没有必要使用 uintptr 。------[来自知乎回答](https://www.zhihu.com/question/439659823)
- 标准库 math 定义了各数字类型取值范围。
- 空指针值 nil，而非C/C++ NULL。golang中有多种引用类型：pointer、interface、slice、map、channel、function。go作为一个强类型语言（类型是定义好的无法改变，不像c，你定义一个short可以当成char用，因为可以直接操作内存），不同引用类型的判空（nil）规则是不同的；比如：interface的判空规则是，需要判断类型和值是否都为nil(interface的底层是有类型和值构成的)slice的判空，需要判断slice引用底层数组的指针为空，容量和size均为0。
- 不允许将整型强制转换为布尔型。
- 字符串的内部实现使用UTF-8编码。（UTF-8是Unicode的存储实现，转化为有限长度比特组合的规则）
- 只有显示类型转化。
## string
值类型，空值是空字符串而不是nil。本质就是个只读的（不可变的）byte切片。

```go
// $GOROOT/src/reflect/value.go

// StringHeader是一个string的运行时表示
type StringHeader struct {
    Data uintptr
    Len  int
}
```
string 类型其实是一个“描述符”，它本身并不真正存储字符串数据，而仅是由一个指向底层存储的指针和字符串的长度字段组成的

- 多行字符串，用反引号`
```go
s1 := `第一行
    第二行
    第三行
    `
    fmt.Println(s1)
```
方法   |介绍
:--|:--
len(str)	 |求长度
+或fmt.Sprintf	|拼接字符串
strings.Split	|分割
strings.Contains	|判断是否包含
strings.HasPrefix,strings.HasSuffix	|前缀/后缀判断
strings.Index(),strings.LastIndex()	|子串出现的位置
strings.Join(a[]string, sep string)	|join操作

遍历字符串：
```go
 // Traversal strings
    func traversalString() {
        s := "JeFo的博客"
        for i := 0; i < len(s); i++ { //Traversal by byte
            fmt.Printf("%v(%c) ", s[i], s[i])
        }
        fmt.Println()
        for _, r := range s { //Traversal by rune
            fmt.Printf("%v(%c) ", r, r)
        }
        fmt.Println()
    }
```
```
74(J) 101(e) 70(F) 111(o) 231(ç) 154() 132(
                                           ) 229(å) 141() 154() 229(å) 174(®) 162(¢) 
74(J) 101(e) 70(F) 111(o) 30340(的) 21338(博) 23458(客)
```
可以调用标准库 UTF-8 包中的 RuneCountInString 函数获取字符串字符个数。（len只能获得字节个数）

Go 原生支持通过 +/+= 操作符进行字符串连接。以及，Go 还提供了 strings.Builder、strings.Join、fmt.Sprintf 等函数来进行字符串连接操作。
-  如果能知道拼接字符串的个数，那么使用bytes.Buffer和strings.Builder的Grows申请空间后，性能是最好的；如果不能确定长度，那么bytes.Buffer和strings.Builder也比“+”和fmt.Sprintf性能好很多。
- bytes.Buffer与strings.Builder，strings.Builder更合适，因为bytes.Buffer 转化为字符串时重新申请了一块空间，存放生成的字符串变量，而 strings.Builder 直接将底层的 []byte 转换成了字符串类型返回了回来。

字符串比较：
- Go 字符串类型支持各种比较关系操作符，包括 = =、!= 、>=、<=、> 和 <。在字符串的比较上，Go 采用字典序的比较策略，分别从每个字符串的起始处，开始逐个字节地对两个字符串类型变量进行比较。

**Go 支持字符串与字节切片、字符串与 rune 切片的双向转换，并且这种转换无需调用任何函数，只需使用显式类型转换就可以了**

修改字符串：
- 要修改字符串，需要先将其转换成[]rune或[]byte，完成后再转换为string。无论哪种转换，**都会重新分配内存，并复制字节数组。**

为什么go要原生支持字符串类型？
- c语言并没有内置字符串类型
    - 不是原生类型，编译器不会对它进行类型校验，导致类型安全性差；
    - 字符串操作时要时刻考虑结尾的’\0’，防止缓冲区溢出；
    - 以字符数组形式定义的“字符串”，它的值是可变的，在并发场景中需要考虑同步问题；
    - 获取一个字符串的长度代价较大，通常是 O(n) 时间复杂度；
    - C 语言没有内置对非 ASCII 字符（如中文字符）的支持。
- go内置了字符串类型的好处
    - string 类型的数据是不可变的，提高了字符串的并发安全性和存储利用率。
    - 没有结尾’\0’，而且获取长度的时间复杂度是常数时间，消除了获取字符串长度的开销。
    - 原生支持“所见即所得”的原始字符串，大大降低构造多行字符串时的心智负担。
        - **在 C 语言中构造多行字符串，一般就是两个方法：要么使用多个字符串的自然拼接，要么需要结合续行符""。但因为有转义字符的存在，我们很难控制好格式。Go 语言就简单多了，通过一对反引号原生支持构造“所见即所得”的原始字符串（Raw String）。而且，Go 语言原始字符串中的任意转义字符都不会起到转义的作用**
    - 对非 ASCII 字符提供原生支持，消除了源码在不同环境下显示乱码的可能。






## 数组
- 数组可以通过下标进行访问，下标是从0开始，最后一个元素下标是：len-1
- 支持 "=="、"!=" 操作符，因为内存总是被初始化过的。
    - 相同类型的数组之间可以使用 == 或 != 进行比较，但不可以使用 < 或 >，也可以相互赋值。
    - 长度不同类型也不同。
- 指针数组 [n]*T，数组指针 *[n]T。
- 多维数组除了第一维，初始化时都不能用[...]省略长度声明。
- 值拷贝行为会造成性能问题，通常会建议使用 slice，或数组指针。
## 列表list
初始化：
- 通过 container/list 包的 New() 函数初始化 list。变量名 := list.New()
- 通过 var 关键字声明初始化 list 。var 变量名 list.List
列表与切片和 map 不同的是，列表并没有具体元素类型的限制，因此，列表的元素可以是任意类型，这既带来了便利，也引来一些问题，例如给列表中放入了一个 interface{} 类型的值，取出值后，如果要将 interface{} 转换为其他类型将会发生宕机。

源码数据结构：
```go
type Element struct {
	// Next and previous pointers in the doubly-linked list of elements.
	// To simplify the implementation, internally a list l is implemented
	// as a ring, such that &l.root is both the next element of the last
	// list element (l.Back()) and the previous element of the first list
	// element (l.Front()).
	next, prev *Element

	// The list to which this element belongs.
	list *List

	// The value stored with this element.
	Value interface{}
}
type List struct {
	root Element // sentinel list element, only &root, root.prev, and root.next are used
	len  int     // current list length excluding (this) sentinel element
}
```
其他源码列出稍冗长，root.prev可以视作尾节点，root.next相当于头节点，不过这些是透明的，被封装好的。


在列表中插入元素删除元素都有简便方法。
## 切片
只能和nil进行比较。
```go
//用make()初始化
var s3 []int = make([]int, 0, 10)//len=0,cap=10
var s4 []int = make([]int, 5)//len=5
//先初始化一个数组，再截取相应部分得到切片
arr := [5]int{1, 2, 3, 4, 5}
var s6 []int
//数组的切片化
s6 = arr[1:4]  // 左闭右开
s7 = arr[1:3:5]//arr[low,high,max]，len=high-low cap=max-low
```
切片追加append（内置函数）：
```go
// The append built-in function appends elements to the end of a slice. If
// it has sufficient capacity, the destination is resliced to accommodate the
// new elements. If it does not, a new underlying array will be allocated.
// Append returns the updated slice. It is therefore necessary to store the
// result of append, often in the variable holding the slice itself:
//	slice = append(slice, elem1, elem2)
//	slice = append(slice, anotherSlice...)
// As a special case, it is legal to append a string to a byte slice, like this:
//	slice = append([]byte("hello "), "world"...)
var a = []int{1, 2, 3}
fmt.Printf("slice a : %v\n", a)
var b = []int{4, 5, 6}
fmt.Printf("slice b : %v\n", b)
c := append(a, b...)
fmt.Printf("slice c : %v\n", c)
d := append(c, 7)
fmt.Printf("slice d : %v\n", d)
e := append(d, 8, 9, 10)
fmt.Printf("slice e : %v\n", e)
slice := append([]byte("hello "), "world"...)//**注意**是字节数组和字符串。
fmt.Printf("slice slice : %v\n", slice)
```
```go
func TestOne(t *testing.T) {
	q := make([]int, 3, 10)
	w := append(q, 1)
	t.Log(len(w), len(q))
	e := append(q, 2)
	//append是加在该切片的len后面，但不是最后一个元素后面，因为底层数组会被改变，而切片变量的结构体所记录的信息是固定的。
	t.Log(q, w, e)
}
```
- 超出原 slice.cap 限制，就会重新分配底层数组，即便原数组并未填满。
- 通常以 2 倍容量重新分配底层数组。在大批量添加数据时，建议一次性分配足够大的空间，以减少内存分配和数据复制开销。或初始化足够长的 len 属性，改用索引号进行操作。
- 及时释放不再使用的 slice 对象，避免持有过期数组，造成 GC 无法回收。

切片resize:
```go
package main

import (
    "fmt"
)

func main() {
    var a = []int{1, 3, 4, 5}
    fmt.Printf("slice a : %v , len(a) : %v\n", a, len(a))
    b := a[1:2]
    fmt.Printf("slice b : %v , len(b) : %v\n", b, len(b))
    c := b[1:3]
    fmt.Printf("slice c : %v , len(c) : %v\n", c, len(c))
}
```
```
slice a : [1 3 4 5] , len(a) : 4
slice b : [3] , len(b) : 1
slice c : [4 5] , len(c) : 2
```
string & slice :
string底层就是一个byte的数组，因此，也可以进行切片操作。

二维切片：
```go
……
 a := make([][]int,dy)
 for i = range a {
     a[i] = make([]int, dx)
 }
```
### slice切片底层实现
切片的设计想法是由动态数组概念而来，为了开发者可以更加方便的使一个数据结构可以自动增加和减少。但是切片本身并不是动态数据或者数组指针。切片常见的操作有   **reslice、append、copy**。与此同时，切片还具有**可索引，可迭代**的优秀特性。
```go
func main() {
    arrayA := []int{1, 2}
    testArrayPoint(&arrayA)   // 1.传数组指针
    arrayB := arrayA[:]
    testArrayPoint(&arrayB)   // 2.传切片
    fmt.Printf("arrayA : %p , %v\n", &arrayA, arrayA)
}

func testArrayPoint(x *[]int) {
    fmt.Printf("func Array : %p , %v\n", x, *x)
    (*x)[1] += 1
}
```
```
func Array : 0xc4200b0140 , [1 2]
func Array : 0xc4200b0180 , [1 3]
arrayA : 0xc4200b0140 , [1 4]
```
- 传指针会有一个弊端，从打印结果可以看到，第一行和第三行指针地址都是同一个，万一原数组的指针指向更改了，那么函数里面的指针指向都会跟着更改。
- 用切片传数组参数，既可以达到节约内存的目的，也可以达到合理处理好共享内存的问题。打印结果第二行就是切片，**切片的指针和原来数组的指针是不同的**。
slice数据结构源码：
```go
// runtime/slice.go
type slice struct {
	array unsafe.Pointer // 指向一个数组的指针
	len   int // 切片长度 
	cap   int // 切片容量
}
```
**注意**：Golang 语言是没有操作原始内存的指针的，所以 **unsafe 包**提供相关的对内存指针的操作，一般情况下非专业人员勿用

如果想从 slice 中得到一块内存地址，可以这样做：
```go
s := make([]byte, 200)
ptr := unsafe.Pointer(&s[0])
```
自己构造一个slice:
```go
var ptr unsafe.Pointer
var s1 = struct {
    addr uintptr
    len int
    cap int
}{ptr, length, length}
s := *(*[]byte)(unsafe.Pointer(&s1))
```
在 Go 的反射中就存在一个与之对应的数据结构 SliceHeader，我们可以用它来构造一个 slice：
```go
var o []byte
sliceHeader := (*reflect.SliceHeader)((unsafe.Pointer(&o)))
sliceHeader.Cap = length
sliceHeader.Len = length
sliceHeader.Data = uintptr(ptr)
```
此外，unsafe的Sizeof函数：
```go
var a, b = int(5), uint(6)
var p uintptr = 0x12345678
fmt.Println("signed integer a's length is", unsafe.Sizeof(a)) // 8
fmt.Println("unsigned integer b's length is", unsafe.Sizeof(b)) // 8
fmt.Println("uintptr's length is", unsafe.Sizeof(p)) // 8
```
并非所有时候都适合用切片代替数组：因为切片底层数组可能会在堆上分配内存，而且小数组在栈上拷贝的消耗也未必比make 消耗大。
### 创建切片
**make 函数允许在运行期动态指定数组长度，绕开了数组类型必须使用编译期常量的限制。**
创建切片有两种形式，make 创建切片，字面量创建切片（既可以初始化一个新的，也可以截取一个数组,截取一个数组的时候cap未声明时为数组容量）。
#### 空切片和nil切片
nil切片：| nil (Pointer) | Len(int) | Cap(int) |
-  var slice []int
nil 切片被用在很多标准库和内置函数中，描述一个不存在的切片的时候，就需要用到 nil 切片。比如函数在发生异常的时候，返回的切片就是 nil 切片。nil 切片的指针指向 nil。

空切片： | Array (Pointer) | Len(int) | Cap(int) |
- silce := make( []int , 0 )
- slice := []int{ }
空切片一般会用来表示一个空的集合。比如数据库查询，一条结果也没有查到，那么就可以返回一个空切片。

空切片和 nil 切片的区别在于，空切片指向的地址不是nil，指向的是一个内存地址，但是它没有分配任何内存空间，即底层元素包含0个元素。

不管是使用 nil 切片还是空切片，对其调用内置函数 append，len 和 cap 的效果都是一样的。
#### 扩容策略
如果切片的容量小于 1024 个元素，于是扩容的时候就翻倍增加容量。

一旦元素个数超过 1024 个元素，那么增长因子就变成 1.25 ，即每次增加原来容量的四分之一。

**注意**：扩容扩大的容量都是针对原来的容量而言的，而不是针对原来数组的长度而言的。

扩容后的数组是新数组还是老数组？
- 如果如果切片扩容后容量比原来数组的容量最大值还大，扩容切片需要另开一片内存区域，把原来的值拷贝过来，再执行append()操作。
- 否则，不会开辟新数组，这种情况很危险，因为这种情况下，扩容以后的数组还是指向原来的数组,多个原来的数组上的切片会受新切片所影响！
### 切片拷贝
slicecopy 方法会把源切片值(即 fm Slice )中的元素复制到目标切片(即 to Slice )中，并返回被复制的元素个数，copy 的两个类型必须一致。slicecopy 方法最终的复制结果取决于较短的那个切片，当较短的切片复制完成，整个复制过程就全部完成了。

如果用 range 的方式去遍历一个切片，拿到的 Value 其实是切片里面的值拷贝。所以每次打印 Value 的地址都不变。由于 Value 是值拷贝的，并非引用传递，所以直接改 Value 是达不到更改原切片值的目的的，需要通过 &slice[index] 获取真实的地址。
## 指针
区别于C/C++中的指针，Go语言中的指针不能进行偏移和运算，是安全指针。

首先需要知道指针地址、指针类型和指针取值。

指针地址和指针类型：
- Go语言中的值类型（int、float、bool、string、array、struct）都有对应的指针类型

指针取值：*

**空指针**：
- 当一个指针被定义后没有分配到任何变量时，它的值为 nil

**new和make**:
- 在Go语言中对于引用类型的变量，我们在使用的时候不仅要声明它，还要为它分配内存空间，否则我们的值就没办法存储。
- new
    - 函数签名和举例
    ```go
    func new(Type) *Type
    
    func main() {
    var a *int
    a = new(int)
    *a = 10
    fmt.Println(*a)
    }
    ```
    - new函数不太常用，使用new函数得到的是一个类型的指针，并且该指针对应的值为该类型的零值。
- make
    - **只用于slice、map以及chan的内存创建，而且它返回的类型就是这三个类型本身**,因为这三种类型就是引用类型，所以就没有必要返回他们的指针了。
    - 以map举例：
    ```go
    func main() {
    var b map[string]int
    b = make(map[string]int, 10)
    b["测试"] = 100
    fmt.Println(b)
    }
    ```
## map
```go
    map[KeyType]ValueType
```
初始化时用make申请内存（或者直接填充元素）：
```go
    make(map[KeyType]ValueType, [cap])//cap不是必须的，但最好一开始就申请一个合适的容量
    //
    // 初始化 + 赋值一体化
    m3 := map[string]string{
        "a": "aa",
        "b": "bb",
    }
```
**判断某个key是否存在**：
```go
//map[key]会返回两个值，第二个是该键是否存在
    value, ok := map[key]   
```
map的遍历还是正常用for range，但有一点需要**注意**：**遍历map时元素顺序与添加键值对的顺序无关。**

按照指定顺序遍历map:思路是将map的key取出另存为切片再排序，再按照切片的顺序进行遍历即可。

**因为 map 类型要保证 key 的唯一性。key 的类型必须支持“==”和“!=”两种比较操作符，比如函数类型、map 类型自身，以及切片类型是不能作为 map 的 key 类型的。value类型则没有限制。**

**map & 切片**：
- 元素为map的切片：
```go
func main() {
    var mapSlice = make([]map[string]string, 3)
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }
    fmt.Println("after init")
    // 对切片中的map元素进行初始化
    mapSlice[0] = make(map[string]string, 10)
    mapSlice[0]["name"] = "王五"
    mapSlice[0]["password"] = "123456"
    mapSlice[0]["address"] = "红旗大街"
}
```
- value为切片的map:
```go
func main() {
    var sliceMap = make(map[string][]string, 3)
    fmt.Println(sliceMap)
    fmt.Println("after init")
    key := "中国"
    value, ok := sliceMap[key]
    if !ok {
        value = make([]string, 0, 2)
    }
    value = append(value, "北京", "上海")
    sliceMap[key] = value
    fmt.Println(sliceMap)
}
```
获取键值对数量：
```go
m := map[string]int {
  "key1" : 1,
  "key2" : 2,
}

fmt.Println(len(m)) // 2
m["key3"] = 3  
fmt.Println(len(m)) // 3
```
**注意**：不能对 map 类型变量调用 cap，来获取当前容量

map删除键值对：（即便传给 delete 的键在 map 中并不存在，delete 函数的执行也不会失败，更不会抛出运行时的异常。）
```go
delete(map,key)
```
### map实现原理
map底层存储方式为（结构体）数组，在存储时key不能重复，当key重复时，value进行覆盖，**我们通过key进行hash运算（可以简单理解为把key转化为一个整形数字）然后对数组的长度取余，得到key存储在数组的哪个下标位置，最后将key和value组装为一个结构体，放入数组下标处**。

哈希冲突：即不同key经哈希映射后得到相同的数组下标。
- 解决办法：开放定址法：
    - 发现hashkey(key)的下标已经被别key占用的时候，在这个数组中空间中重新找一个没被占用的存储这个冲突的key。寻找方式有很多。常见的有线性探测法，线性补偿探测法，随机探测法。
        - 线性探测法：
            - 从冲突的下标处开始往后探测，到达数组末尾时，从数组开始处探测，直到找到一个空位置存储这个key，当数组都找不到的情况下会扩容（事实上当数组容量快满的时候就会扩容了）
            - 查找某一个key的时候，找到key对应的下标，比较key是否相等，如果相等直接取出来，否则按照顺序探测直到碰到一个空位置，说明key不存在。
        - 拉链法：
            - 当key的hash冲突时，我们在冲突位置的元素上形成一个链表，通过指针互连接。
            - 当查找时，发现key冲突，顺着链表一直往下找，直到链表的尾节点，找不到则返回空
    - 开放定址法的优缺点：
        - 由上面可以看出拉链法比线性探测处理简单
        - 线性探测查找是会被拉链法会更消耗时间
        - 线性探测会更加容易导致扩容，而拉链不会
        - 拉链存储了指针，所以空间上会比线性探测占用多一点
        - 拉链是动态申请存储空间的，所以更适合链长不确定的

### go中map的实现原理
go里面map并不是线程安全的，在1.9版本之前用map加互斥锁来解决此问题，之后加了sync.map性能稍微高了一些，因为它有一块只读的buffer，相当于由两个buffer组成，一块只读，一块读写。sync.map更适合于读非常多，能够占到90%以上的情况。如果读写差不多或者说写更多的话，sync.map的性能就比较差了。
在读写对半的情况下，可以考虑引入concurrent_map包。（go get -u +包地址）

在go1.16中，map也是数组存储的的，每个数组下标处存储的是一个bucket,这个bucket的类型见下面代码，每个bucket中可以存储8个kv键值对，当每个bucket存储的kv对到达8个之后，会通过overflow指针指向一个新的bucket，从而形成一个链表,看bmap和hmap的结构
```go
//go 1.18
// A bucket for a Go map.
type bmap struct {
	// tophash generally contains the top byte of the hash value
	// for each key in this bucket. If tophash[0] < minTopHash,
	// tophash[0] is a bucket evacuation state instead.即桶疏散状态
	tophash [bucketCnt]uint8
	// Followed by bucketCnt keys and then bucketCnt elems.
	// NOTE: packing all the keys together and then all the elems together makes the
	// code a bit more complicated than alternating key/elem/key/elem/... but it allows
	// us to eliminate padding which would be needed for, e.g., map[int64]int8.
	// Followed by an overflow pointer.
}


// A header for a Go map.
type hmap struct {
	// Note: the format of the hmap is also encoded in cmd/compile/internal/reflectdata/reflect.go.
	// Make sure this stays in sync with the compiler's definition.
	count     int // # live cells == size of map.  Must be first (used by len() builtin)
	flags     uint8
	B         uint8  // log_2 of # of buckets (can hold up to loadFactor * 2^B items)
	noverflow uint16 // approximate number of overflow buckets; see incrnoverflow for details
	hash0     uint32 // hash seed

	buckets    unsafe.Pointer // array of 2^B Buckets. may be nil if count==0.
	oldbuckets unsafe.Pointer // previous bucket array of half the size, non-nil only when growing
	nevacuate  uintptr        // progress counter for evacuation (buckets less than this have been evacuated)

	extra *mapextra // optional fields
}
```
- `B` 是 buckets 数组的长度的对数，也就是说 buckets 数组的长度就是 2^B。
- tophash用来快速查找key值是否在该bucket中，而不同每次都通过真值进行比较。
- **`map[int64]int8`,key是int64（8个字节），value是int8（一个字节），kv的长度不同，如果按照kv格式存放，则考虑内存对齐v也会占用int64，而按照后者存储时，8个v刚好占用一个int64**
- **当往map中存储一个kv对时，通过k获取hash值，hash值的低八位和bucket数组长度取余，定位到在数组中的那个下标，hash值的高八位存储在bucket中的tophash中，用来快速判断key是否存在，当一个bucket满时，通过overfolw指针链接到下一个bucket。**
- map的存储源码：
```go
// Like mapaccess, but allocates a slot for the key if it is not present in the map.如果key不在map里为其分配一个插槽（狭槽）
func mapassign(t *maptype, h *hmap, key unsafe.Pointer) unsafe.Pointer {
	if h == nil {
		panic(plainError("assignment to entry in nil map"))
	}
	if raceenabled {
		callerpc := getcallerpc()
		pc := funcPC(mapassign)
		racewritepc(unsafe.Pointer(h), callerpc, pc)
		raceReadObjectPC(t.key, key, callerpc, pc)
	}
	if msanenabled {
        //获取hash算法
		msanread(key, t.key.size)
	}
	if h.flags&hashWriting != 0 {
		throw("concurrent map writes")
	}
    //计算哈希值
	hash := t.hasher(key, uintptr(h.hash0))

	// Set hashWriting after calling t.hasher, since t.hasher may panic,
	// in which case we have not actually done a write.
	h.flags ^= hashWriting

    //如果bucket数组一开始为空，则初始化
	if h.buckets == nil {
		h.buckets = newobject(t.bucket) // newarray(t.bucket, 1)
	}

again:
    //定位在哪一个bucket中
	bucket := hash & bucketMask(h.B)
	if h.growing() {
		growWork(t, h, bucket)
	}
    //得到bucket的结构体
	b := (*bmap)(add(h.buckets, bucket*uintptr(t.bucketsize)))
    //获取高八位的哈希值
	top := tophash(hash)

	var inserti *uint8
	var insertk unsafe.Pointer
	var elem unsafe.Pointer
bucketloop:
    //死循环
	for {
        //循环bucket中的tophash数组
		for i := uintptr(0); i < bucketCnt; i++ {
            //如果hash不相等
			if b.tophash[i] != top {
                //判断是否为空，为空则插入
				if isEmpty(b.tophash[i]) && inserti == nil {
					inserti = &b.tophash[i]
					insertk = add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
					elem = add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.elemsize))
				}
                //插入成功，终止外层循环
				if b.tophash[i] == emptyRest {
					break bucketloop
				}
				continue
			}
            //高八位哈希值一样，获取已存在的kay
			k := add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
			if t.indirectkey() {
				k = *((*unsafe.Pointer)(k))
			}
            //判断两个key是否相等，不相等就循环下一个
			if !t.key.equal(key, k) {
				continue
			}
			// already have a mapping for key. Update it.
			if t.needkeyupdate() {
				typedmemmove(t.key, k, key)
			}
            //获取已存在的value
			elem = add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.elemsize))
			goto done
		}
        //如果上一个bucket没能找到插入，则通过overflow获取链表上的下一个bucket
		ovf := b.overflow(t)
		if ovf == nil {
			break
		}
		b = ovf
	}

	// Did not find mapping for key. Allocate new cell & add entry.

	// If we hit the max load factor or we have too many overflow buckets,
	// and we're not already in the middle of growing, start growing.
	if !h.growing() && (overLoadFactor(h.count+1, h.B) || tooManyOverflowBuckets(h.noverflow, h.B)) {
		hashGrow(t, h)
		goto again // Growing the table invalidates everything, so try again
	}

	if inserti == nil {
		// The current bucket and all the overflow buckets connected to it are full, allocate a new one.
		newb := h.newoverflow(t, b)
		inserti = &newb.tophash[0]
		insertk = add(unsafe.Pointer(newb), dataOffset)
		elem = add(insertk, bucketCnt*uintptr(t.keysize))
	}

	// store new key/elem at insert position
	if t.indirectkey() {
		kmem := newobject(t.key)
		*(*unsafe.Pointer)(insertk) = kmem
		insertk = kmem
	}
	if t.indirectelem() {
		vmem := newobject(t.elem)
		*(*unsafe.Pointer)(elem) = vmem
	}
	typedmemmove(t.key, insertk, key)
    //将高八位hash值存储
	*inserti = top
	h.count++

done:
	if h.flags&hashWriting == 0 {
		throw("concurrent map writes")
	}
	h.flags &^= hashWriting
	if t.indirectelem() {
		elem = *((*unsafe.Pointer)(elem))
	}
	return elem
}
```
 map 可以自动扩容，map 中数据元素的 value 位置可能在这一过程中发生变化，所以 Go **不允许获取 map 中 value 的地址，这个约束是在编译期间就生效的**


### map与工厂模式
map的value可以是一个方法。

与 Go 的 Dock type 接⼝⽅式⼀起，可以⽅便的实现单⼀⽅法对象的⼯⼚模式
```go
func TestMapWithFunValue(t *testing.T) {
	m := map[int]func(op int) int{}
	m[1] = func(op int) int { return op }
	m[2] = func(op int) int { return op * op }
	m[3] = func(op int) int { return op * op * op }
	t.Log(m[1](2), m[2](2), m[3](2))
}
```
## set
Go 的内置集合中没有 Set 实现， 可以 map[type]bool。

1. map可以保证添加元素的唯⼀性，方便判断唯一元素的个数
2. 基本操作
    - 1) 添加元素
    - 2) 判断元素是否存在
    - 3) 删除元素
    - 4) 元素个数
```go
func TestMapForSet(t *testing.T) {
	mySet := map[int]bool{}
	mySet[1] = true
	n := 3
	if mySet[n] {
		t.Logf("%d is existing", n)
	} else {
		t.Logf("%d is not existing", n)
	}
	mySet[3] = true
	t.Log(len(mySet))
	delete(mySet, 1)
	n = 1
	if mySet[n] {
		t.Logf("%d is existing", n)
	} else {
		t.Logf("%d is not existing", n)
	}
}
```
## 结构体
**Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。**

### 类型别名和自定义类型
自定义类型（新类型）：
```go
 //将MyInt定义为int类型
    type MyInt int 
```
- 可以基于内置的基本类型定义，也可以通过struct定义。

类型别名（Go1.9添加的新功能，**注意**编译后是原来的类型）：
```go
//将MyInt作为为int类型的昵称
    type MyInt = int 
```
### 结构体
本质上是一种聚合型的数据类型。

通过struct可以实现面向对象。

定义的时候，同样类型的字段也可以写在一行。

只有结构体被实例化时，才会真正被分配内存。

匿名结构体：定义临时数据结构时可能会用到。

语法糖：Go语言中支持对结构体指针直接使用.来访问结构体的成员，在使用new分配内存后得到的便是结构体指针。

对于包含结构体类型字段的结构体类型来说,可以无需提供字段的名字:
- 以这种方式定义的结构体字段，我们叫做嵌入字段（Embedded Field）。我们也可以将这种字段称为匿名字段，或者把类型名看作是这个字段的名字
```go
type Book struct {
    Title string
    Person
    ... ...
}

func main(){
    var book Book 
    println(book.Person.Phone) // 将类型名当作嵌入字段的名字
    println(book.Phone)        // 支持直接访问嵌入字段所属类型中字段
}
```
**注意**：
- 结构体类型 T 定义中，不能有以自身类型 T 定义的字段，但却可以拥有自身类型的指针类型、以自身类型为元素类型的切片类型，以及以自身类型作为 value 类型的 map 类型的字段，因为指针、map、切片的变量元数据的内存占用大小是固定的。
```go
type T struct {
    a  T            // wrong
    t  *T           // ok
    st []T          // ok
    m  map[string]T // ok
}     
```
sync.Mutex和bytes.Buffer的“零值可用”：
```go
var mu sync.Mutex
mu.Lock()
mu.Unlock()
var b bytes.Buffer
b.Write([]byte("Hello, Go"))
fmt.Println(b.String()) // 输出：Hello, Go
```
**使用&对结构体进行取地址操作相当于对该结构体类型进行了一次new实例化操作。**
```go
p := &person{}
```
初始化（没有指定初始值的字段的值就是该字段类型的零值）：
```go
p := person{
    a: "1a",
    b: "2b",
}
//结构体指针
q := &ss{
    a: "1a",
    b: "2b",
}
//简写需**注意**三点：1.必须初始化结构体的所有字段。2.初始值的填充顺序必须与字段在结构体中的声明顺序一致。3.该方式不能和键值初始化方式混用。  
s := &d{
    "aq",
    "sw",
}
```
go 语言并不推荐我们按字段顺序对一个结构体类型变量进行显式初始化，甚至 Go 官方还在提供的 go vet 工具中专门内置了一条检查规则：“composites”，用来静态检查代码中结构体变量初始化是否使用了这种方法，一旦发现，就会给出警告。

**Go 推荐我们用“field:value”形式的复合字面值，对结构体类型变量进行显式初始化，这种方式可以降低结构体类型使用者和结构体类型设计者之间的耦合**
```go
var t = T{
    F2: "hello",
    F1: 11,
    F4: 14,
}
```


空结构体的作用：
- 空结构体类型变量内存占用为0
- **使用空结构体类型元素，作为一种“事件”信息进行 Goroutine 之间的通信**
```go
var c = make(chan Empty) // 声明一个元素类型为Empty的channel
c<-Empty{}               // 向channel写入一个“事件”
```
- 这种以空结构体为元素类建立的 channel，是目前能实现的、内存占用最小的 Goroutine 间通信方式。

**空标识符“_”作为结构体类型定义中的字段名称的作用**：



自己实现一个结构体构造函数：
```go
//值拷贝开销太大，返回结构体指针
func newPerson(name, city string, age int8) *person {
    return &person{
        name: name,
        city: city,
        age:  age,
    }
}
```
如果一个结构体类型中**包含未导出字段，并且这个字段的零值还不可用**时,又或是一个结构体类型中的某些字段，需要一个复杂的初始化逻辑时：需要使用一个特定的构造函数，来创建并初始化结构体变量了。

结构体类型的内存布局：
- 在真实情况下，虽然 Go 编译器没有在结构体变量占用的内存空间中插入额外字段，但结构体字段实际上可能并不是紧密相连的，中间可能存在“缝隙”。这些“缝隙”同样是结构体变量占用的内存空间的一部分，它们是 Go 编译器插入的“填充物（Padding）”
    - 这是为了**内存对齐**。



#### 方法和接收者
Go语言中的方法（Method）是一种作用于特定类型变量的函数。这种特定类型变量叫做接收者（Receiver）。**接收者的概念就类似于其他语言中的this或者 self。**
```go
//接收者变量：接收者中的参数变量名在命名时，官方建议使用接收者类型名的第一个小写字母，而不是self、this之类的命名。例如，Person类型的接收者变量应该命名为 p，Connector类型的接收者变量应该命名为c等。
func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {
        函数体
}
```
**指针类型的接收者：**
- 指针类型的接收者由一个结构体的指针组成，由于指针的特性，调用方法时修改接收者指针的任意成员变量，在方法结束后，修改都是有效的。这种方式就十分接近于其他语言中面向对象中的this或者self。 
- 例如我们为Person添加一个SetAge方法，来修改实例变量的年龄。
```go
// SetAge 设置p的年龄
// 使用指针接收者
func (p *Person) SetAge(newAge int8) {
    p.age = newAge
}
//调用该方法
func main() {
    p1 := NewPerson("测试", 25)
    fmt.Println(p1.age) // 25
    p1.SetAge(30)
    fmt.Println(p1.age) // 30
} 
```
值类型的接收者：
```go
// SetAge2 设置p的年龄
// 使用值接收者
func (p Person) SetAge2(newAge int8) {
    p.age = newAge
}

func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()
    fmt.Println(p1.age) // 25
    p1.SetAge2(30) // (*p1).SetAge2(30)
    fmt.Println(p1.age) // 25
} 
```
**什么时候应该使用指针类型接收者：**
1. 需要修改接收者中的值
2. 接收者是拷贝代价比较大的大对象
3. 保证一致性，如果有某个方法使用了指针接收者，那么其他的方法也应该使用指针接收者。

为任意类型添加方法：
- 在Go语言中，接收者的类型可以是任何类型，不仅仅是结构体，任何类型都可以拥有方法。 举个例子，我们基于内置的int类型使用type关键字可以定义新的自定义类型，然后为我们的自定义类型添加方法。
- **注意**事项：非本地类型不能定义方法，也就是说我们不能给别的包的类型定义方法。

结构体的匿名字段：
- 结构体允许其成员字段在声明时没有字段名而只有类型，这种没有名字的字段就称为匿名字段。
- 匿名字段默认采用类型名作为字段名，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个。

**嵌套结构体**
一个结构体中可以嵌套包含另一个结构体或结构体指针。

嵌套结构体内部可能存在相同的字段名。这个时候为了避免歧义需要指定具体的内嵌结构体的字段。

**类型的“继承”**：
即通过类型嵌入，包括接口类型的类型嵌入和结构体类型的类型嵌入。
- 结构体类型中嵌入接口类型：结构体类型的方法集合，包含嵌入的接口类型的方法集合。
    - 结构体类型嵌入接口类型在日常编码中有一个妙用，就是可以简化单元测试的编写：见下面例子：
        - 由于嵌入某接口类型的结构体类型的方法集合包含了这个接口类型的方法集合，这就意味着，这个结构体类型也是它嵌入的接口类型的一个实现
```go

package employee
  
type Result struct {
    Count int
}

func (r Result) Int() int { return r.Count }

type Rows []struct{}

type Stmt interface {
    Close() error
    NumInput() int
    Exec(stmt string, args ...string) (Result, error)
    Query(args []string) (Rows, error)
}

// 返回男性员工总数
func MaleCount(s Stmt) (int, error) {
    result, err := s.Exec("select count(*) from employee_tab where gender=?", "1")
    if err != nil {
        return 0, err
    }

    return result.Int(), nil
}
```
```go

package employee
  
import "testing"

type fakeStmtForMaleCount struct {
    Stmt
}

func (fakeStmtForMaleCount) Exec(stmt string, args ...string) (Result, error) {
    return Result{Count: 5}, nil
}

func TestEmployeeMaleCount(t *testing.T) {
    f := fakeStmtForMaleCount{}
    c, _ := MaleCount(f)
    if c != 5 {
        t.Errorf("want: %d, actual: %d", 5, c)
        return
    }
}
```
我们为 TestEmployeeMaleCount 测试用例建立了一个 fakeStmtForMaleCount 的伪对象类型，然后在这个类型中嵌入了 Stmt 接口类型。这样 fakeStmtForMaleCount 就实现了 Stmt 接口，我们也实现了快速建立伪对象的目的。接下来我们只需要为 fakeStmtForMaleCount 实现 MaleCount 所需的 Exec 方法，就可以满足这个测试的要求了。



结构体类型中嵌入结构体类型：
```go
//Animal 动物
type Animal struct {
    name string
}

func (a *Animal) move() {
    fmt.Printf("%s会动！\n", a.name)
}

//Dog 狗
type Dog struct {
    Feet    int8
    *Animal //通过嵌套匿名结构体实现继承
}

func (d *Dog) wang() {
    fmt.Printf("%s会汪\n", d.name)
}

func main() {
    d1 := &Dog{
        Feet: 4,
        Animal: &Animal{ //**注意**嵌套的是结构体指针
            name: "旺财",
        },
    }
    d1.wang() //旺财会汪
    d1.move() //旺财会动！
}
```
结构体中字段大写开头表示可公开访问，小写表示私有（**仅在定义当前结构体的包中可访问**）。

无论原类型是接口类型还是非接口类型，类型别名都与原类型拥有完全相同的方法集合。

**结构体与JSON序列化**：
- JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。易于人阅读和编写。同时也易于机器解析和生成。JSON键值对是用来保存JS对象的一种方式，键/值对组合中的键名写在前面并用双引号””包裹，使用冒号:分隔，然后紧接着值；多个键值之间使用英文,分隔。
- 应用于远程的过程调用、webservice、程序配置等
```go
//Student 学生
type Student struct {
    ID     int
    Gender string
    Name   string
}

//Class 班级
type Class struct {
    Title    string
    Students []*Student
}

func main() {
    c := &Class{
        Title:    "101",
        Students: make([]*Student, 0, 200),
    }
    for i := 0; i < 10; i++ {
        stu := &Student{
            Name:   fmt.Sprintf("stu%02d", i),
            Gender: "男",
            ID:     i,
        }
        c.Students = append(c.Students, stu)
    }
    //JSON序列化：结构体-->JSON格式的字符串
    data, err := json.Marshal(c)
    if err != nil {
        fmt.Println("json marshal failed")
        return
    }
    fmt.Printf("json:%s\n", data)
    //JSON反序列化：JSON格式的字符串-->结构体
    str := `{"Title":"101","Students":[{"ID":0,"Gender":"男","Name":"stu00"},{"ID":1,"Gender":"男","Name":"stu01"},{"ID":2,"Gender":"男","Name":"stu02"},{"ID":3,"Gender":"男","Name":"stu03"},{"ID":4,"Gender":"男","Name":"stu04"},{"ID":5,"Gender":"男","Name":"stu05"},{"ID":6,"Gender":"男","Name":"stu06"},{"ID":7,"Gender":"男","Name":"stu07"},{"ID":8,"Gender":"男","Name":"stu08"},{"ID":9,"Gender":"男","Name":"stu09"}]}`
    c1 := &Class{}
    err = json.Unmarshal([]byte(str), c1)
    if err != nil {
        fmt.Println("json unmarshal failed!")
        return
    }
    fmt.Printf("%#v\n", c1)
} 
```
**结构体标签Tag:**
Tag是结构体的元信息，可以在运行的时候通过反射的机制读取出来。

Tag在结构体字段的后方定义，由一对反引号包裹起来，具体的格式如下：
```go
    `key1:"value1" key2:"value2"`  
```
- 结构体标签由一个或多个键值对组成。键与值使用冒号分隔，值用双引号括起来。键值对之间使用一个空格分隔。 **注意**事项： 为结构体编写Tag时，必须严格遵守键值对的规则。结构体标签的解析代码的容错能力很差，一旦格式写错，编译和运行时都不会提示任何错误，通过反射也无法正确取值。例如不要在key和value之间添加空格。
- 例如我们为Student结构体的每个字段定义json序列化时使用的Tag：
```go
//Student 学生
type Student struct {
    ID     int    `json:"id"` //通过指定tag实现json序列化该字段时的key
    Gender string //json序列化是默认使用字段名作为key
    name   string //私有不能被json包访问
}

func main() {
    s1 := Student{
        ID:     1,
        Gender: "女",
        name:   "pprof",
    }
    data, err := json.Marshal(s1)
    if err != nil {
        fmt.Println("json marshal failed!")
        return
    }
    fmt.Printf("json str:%s\n", data) //json str:{"id":1,"Gender":"女"}
} 
```
**内置JSON解析**：
- 利⽤反射实现，通过FeildTag来标识对应的json 值（性能较低）

```go
package jsontest

type BasicInfo struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}
type JobInfo struct {
	Skills []string `json:"skills"`
}
type Employee struct {
	BasicInfo BasicInfo `json:"basic_info"`
	JobInfo   JobInfo   `json:"job_info"`
}
```
```go
package jsontest

import (
	"encoding/json"
	"fmt"
	"testing"
)

var jsonStr = `{
	"basic_info":{
	  	"name":"Mike",
		"age":30
	},
	"job_info":{
		"skills":["Java","Go","C"]
	}
}	`

func TestEmbeddedJson(t *testing.T) {
	e := new(Employee)
	err := json.Unmarshal([]byte(jsonStr), e)
	if err != nil {
		t.Error(err)
	}
	fmt.Println(*e)
	if v, err := json.Marshal(e); err == nil {
		fmt.Println(string(v))
	} else {
		t.Error(err)
	}

}
```
**easyjson**:
- 更快的JSON解析
- EasyJSON 采⽤代码⽣成⽽⾮反射
- 见ch43


## go embed
go1.1推出的能将静态资源嵌入程序的指令。
https://zhuanlan.zhihu.com/p/351931501

## go项目的标准布局演进
官方并没有给标准布局，但社区还是有的。随着go版本的不断更新，Go源码比例不断增大，Go 1.0时还占比32%的C语言现在也只不过占比不到1%。而项目布局一直保持了下来。

Go 1.3 src 目录下面的结构：
- 以 all.bash 为代表的代码构建的脚本源文件放在了 src 下面的顶层目录下。
- src 下的二级目录 cmd 下面存放着 Go 相关可执行文件的相关目录
    - 每个子目录都是一个 Go 工具链命令或子命令对应的可执行文件
- src 下的二级目录 pkg 下面存放着运行时实现、标准库包实现，这些包既可以被上面 cmd 下各程序所导入，也可以被 Go 语言项目之外的 Go 程序依赖并导入。

Go 1.4 版本删除 pkg 这一中间层目录并引入 internal 目录。“src/pkg/xxx”->“src/xxx”
- 根据 internal 机制的定义，一个 Go 项目里的 internal 目录下的 Go 包，只可以被本项目内部的包导入。项目外部是无法导入这个 internal 目录下面的包的。
    - internal 目录的引入，让一个 Go 项目中 Go 包的分类与用途变得更加清晰

Go 1.6 版本增加 vendor 目录
- 为了解决 Go 包依赖版本管理的问题，Go 核心团队在 Go 1.5 版本中做了第一次改进。增加了 vendor 构建机制，也就是 Go 源码的编译可以不在 GOPATH 环境变量下面搜索依赖包的路径，而在 vendor 目录下查找对应的依赖包
    - Go 1.7 版本，Go 在 vendor 下缓存了其依赖的外部包。这些依赖包主要是 golang.org/x 下面的包
- vendor 机制与目录的引入，让 Go 项目第一次具有了**可重现构建**（Reproducible Build）的能力。

Go 1.13 版本引入 go.mod 和 go.sum
- 在 Go 1.11 版本中，Go 核心团队做出了第二次改进尝试：引入了 Go Module 构建机制，也就是在项目引入 go.mod 以及在 go.mod 中明确项目所依赖的第三方包和版本，项目的构建就将摆脱 GOPATH 的束缚，实现精准的可重现构建。
- Go 语言项目自身在 Go 1.13 版本引入 go.mod 和 go.sum 以支持 Go Module 构建机制

**Go 可执行程序项目的典型结构布局**：
```
├── cmd/
│   ├── app1/
│   │   └── main.go
│   └── app2/
│       └── main.go
├── go.mod
├── go.sum
├── internal/
│   ├── pkga/
│   │   └── pkg_a.go
│   └── pkgb/
│       └── pkg_b.go
├── pkg1/
│   └── pkg1.go
├── pkg2/
│   └── pkg2.go
└── vendor/
```
- cmd（也可以是app或者其他名字）
    - 存放项目要编译构建的可执行文件对应的 main 包的源文件。如果你的项目中有多个可执行文件需要构建，每个可执行文件的 main 包单独放在一个子目录中，cmd 目录下的各 app 的 main 包将整个项目的依赖连接在一起
    - 通常来说，main 包应该很简洁。我们在 main 包中会做一些**命令行参数解析、资源初始化、日志设施初始化、数据库连接初始化等**工作，之后就会将程序的执行权限交给更高级的执行控制对象
- pkgN
    - 一个存放项目自身要使用、同样也是可执行文件对应 main 包所要依赖的库文件，同时这些目录下的包还可以被外部项目引用。
- go.mod和go.sum
    - 包依赖管理的配置文件
- vendor（可选）
    - 前面有说，vendor 是 Go 1.5 版本引入的用于在项目本地缓存特定版本依赖包的机制
    - Go Module 机制也保留了 vendor 目录（通过 go mod vendor 可以生成 vendor 下的依赖包，通过 go build -mod=vendor 可以实现基于 vendor 的构建）。一般我们仅保留项目根目录下的 vendor 目录，否则会造成不必要的依赖选择的复杂性。
- etc
    - 如若喜欢借助一些第三方的构建工具辅助构建，比如：make、bazel 等。你可以将这类外部辅助构建工具涉及的诸多脚本文件（比如 Makefile）放置在项目的顶层目录下，就像 Go 1.3中的 all.bash 那样。

如果app1，app2的发布版本不总是同步的，建议将每个项目单独作为一个 module 进行单独的版本管理和演进，避免版本管理的“分歧”带来更大的复杂性。当然新版Go命令较好地解决了这一点，可以采用如下结构：
```
├── go.mod // mainmodule
├── module1
│   └── go.mod // module1
└── module2
    └── go.mod // module2
```
- 可以通过 git tag 名字来区分不同 module 的版本。其中 vX.Y.Z 形式的 tag 名字用于代码仓库下的 mainmodule；而 module1/vX.Y.Z 形式的 tag 名字用于指示 module1 的版本。

**Go 库项目的典型结构布局**：Go 库项目主要作用还是对外暴露 API。
```
├── go.mod
├── internal/
│   ├── pkga/
│   │   └── pkg_a.go
│   └── pkgb/
│       └── pkg_b.go
├── pkg1/
│   └── pkg1.go
└── pkg2/
    └── pkg2.go
```
- 不需要构建可执行程序
- 仅限项目内部使用而不想暴露到外部的包，可以放在项目顶层的 internal 目录下面。当然 internal 也可以有多个并存在于项目结构中的任一目录层级中，关键是项目结构设计人员要明确各级 internal 包的应用层次和范围。
## go应用构建模式的演进
Go 程序的构建过程就是确定包版本、编译包以及将编译后得到的目标文件链接在一起的过程。

包依赖管理演进：
- GOPATH模式。
    - Go 编译器可以在本地 GOPATH 环境变量配置的路径下，搜寻 Go 程序依赖的第三方包。如果存在，就使用这个本地包进行编译；如果不存在，就会报编译错误
    - 如果你没有显式设置 GOPATH 环境变量，Go 会将 GOPATH 设置为默认值，不同操作系统下默认值的路径不同
    - 可以通过 go get 命令将本地缺失的第三方依赖包下载到本地。不仅能将包下载到 GOPATH 环境变量配置的目录下，它还会检查该包的依赖包在本地是否存在，如果不存在，go get 也会一并将它们下载到本地。**但是，go get只能得到最新主线版本的依赖包，不能保证Reproduceable Build**
    - 总之，在 GOPATH 构建模式下，Go 编译器实质上并没有关注 Go 项目所依赖的第三方包的版本。从而有了Vendor机制控制依赖包版本。
- Vendor机制
    - 本质上就是在 Go 项目的某个特定目录下，将项目的所有依赖包缓存起来，这个特定目录名就是 vendor。
    - Go 编译器会优先感知和使用 vendor 目录下缓存的第三方包版本，而不是 GOPATH 环境变量所配置的路径下的第三方包版本。
    - 目录示例：
    ```
    ├── main.go
    └── vendor/
        ├── github.com/
        │   └── sirupsen/
        │       └── logrus/
        └── golang.org/
            └── x/
                └── sys/
                    └── unix/
    ```
    - 开启 vendor 机制，你的 Go 项目**必须**位于 GOPATH 环境变量配置的某个路径的 src 目录下面
    - 不足之处主要在于需要手工管理 vendor 下面的 Go 依赖包（Go 社区先后开发了诸如 gb、glide、dep 等工具，都是用来进行依赖分析管理的，自身却都存在某些问题）、庞大的vendor目录也得提交到代码仓库，以及项目路径的限制。
- Go Module
    - 如何创建？
        - go mod init 
        - go mod tidy
        - go build
    - go.sum是由 go mod 相关命令维护的一个文件，它存放了特定版本 module 内容的哈希值。这是 Go Module 的一个安全措施。当将来这里的某个 module 的特定版本被再次下载的时候，go 命令会使用 go.sum 文件中对应的哈希值，和新下载的内容的哈希值进行比对，只有哈希值比对一致才是合法的，这样可以确保你的项目所依赖的 module 内容，不会被恶意或意外篡改。
    - 项目所依赖的包有很多版本，Go Module 是如何选出最适合的那个版本？
        - Go Module 的语义导入版本机制
            -  go.mod 的 require 段中依赖的版本号，都符合 vX.Y.Z 的格式。语义版本号分成 3 部分：主版本号 (major)、次版本号 (minor) 和补丁版本号 (patch)。分别对应XYZ。
            - 按照语义版本规范，主版本号不同的两个版本是相互不兼容的。而且，在主版本号相同的情况下，次版本号大都是向后兼容次版本号小的版本。补丁版本号也不影响兼容性
            - Go Module 规定：如果同一个包的新旧版本是兼容的，那么它们的包导入路径应该是相同的。
                - 如果不兼容，Go Module 创新性地给出了一个方法：将包主版本号引入到包导入路径中，我们可以像下面这样导入 logrus v2.0.0 版本依赖包：
                ```
                import "github.com/sirupsen/logrus/v2"
                ```
            - 关于主版本号为0时，按照语义版本规范的说法，v0.y.z 这样的版本号是用于项目初始开发阶段的版本号。在这个阶段任何事情都有可能发生，其 API 也不应该被认为是稳定的。Go Module 将这样的版本 (v0) 与主版本号 v1 做同等对待，也就是采用不带主版本号的包导入路径
            - 总之，通过在包导入路径中引入主版本号的方式，来区别同一个包的不兼容版本。
        - 最小版本选择原则
            - 包依赖关系比较复杂时，一个包A的两个依赖包BC可能依赖于不同版本的某个包D，那A依赖于D的哪个版本？
            - **Go 会在该项目依赖项的所有版本中，选出符合项目整体要求的“最小版本”。**
    - 明确具体版本下 Go Module 的实际表现行为还是比较重要的，方便应用时选择切换。
    - Go 各版本构建模式机制和切换：
        - Go 1.11后一段时间GOPATH 构建模式与 Go Modules 构建模式各自独立工作，我们可以通过设置环境变量 GO111MODULE 的值在两种构建模式间切换。
        - Go 1.16 版本，Go Module 构建模式成为了默认模式。
        - 目前我觉得GOPATH似乎可以抛弃了

**Go Module常规操作**：
- 为当前 module 添加一个依赖：
    - go get 命令将我们新增的依赖包下载到了本地 module 缓存里，并在 go.mod 文件的 require 段中新增相关内容。
    - 使用 go mod tidy 命令，在执行构建前自动分析源码中的依赖变化，识别新增依赖项并下载它们
    - 两种方法都行，复杂的项目用go mod tidy/go get .（**注意**二者还是有区别的,看到后面你就知道了）
- 升级、降级依赖版本：
    - 查询某个依赖包的版本
    ```
    PS D:\Blog\JF-011101.github.io-blog\content\posts> go list -m -versions github.com/sirupsen/logrus
    github.com/sirupsen/logrus v0.1.0 v0.1.1 v0.2.0 v0.3.0 v0.4.0 v0.4.1 v0.5.0 v0.5.1 v0.6.0 v0.6.1 v0.6.2 v0.6.3 v0.6.4 v0.6.5 v0.6.6 v0.7.0 v0.7.1 v0.7.2 v0.7.3 v0.8.0 v0.8.1 v0.8.2 v0.8.3 v0.8.4 v0.8.5 v0.8.6 v0.8.7 v0.9.0 v0.10.0 v0.11.0 v0.11.1 v0.11.2 v0.11.3 v0.11.4 v0.11.5 v1.0.0 v1.0.1 v1.0.3 v1.0.4 v1.0.5 v1.0.6 v1.1.0 v1.1.1 v1.2.0 v1.3.0 v1.4.0 v1.4.1 v1.4.2 v1.5.0 v1.6.0 v1.7.0 v1.7.1 v1.8.0 v1.8.1
    //go mod tidy帮我们选择了v1.8.1
    ```
    - 我们可以在项目的 module 根目录下，执行带有版本号的 go get 命令eg: go get github.com/sirupsen/logrus@v1.7.0
    - 或者，用 go mod edit 命令，明确告知我们要依赖 v1.7.0 版本，而不是 v1.8.1。执行go mod edit -require=github.com/sirupsen/logrus@v1.7.0再go mod tidy。
- 添加一个主版本号大于 1 的依赖：
    - 在声明它的导入路径的基础上，加上版本号信息
- 升级依赖版本到一个不兼容版本：
    - 需要**注意**一点，可能需要移除对某个包的依赖
- 移除一个依赖
    - 要想彻底从项目中移除 go.mod 中的依赖项，仅从源码中删除对依赖项的导入语句还不够。
    - 还得用 go mod tidy 命令，将这个依赖项彻底从 Go Module 构建上下文中清除掉。go mod tidy 会自动分析源码依赖，而且将不再使用的依赖从 go.mod 和 go.sum 中移除。
- **特殊情况**：借用Vendor
    - Vendor机制其实可以作为Go Module的一个很好的补充。
    - **在一些不方便访问外部网络，并且对 Go 应用构建性能敏感的环境，比如在一些内部的持续集成或持续交付环境（CI/CD）中，使用 vendor 机制可以实现与 Go Module 等价的构建。**
    - Go 提供了可以快速建立和更新 vendor 的命令：
        - go mod vendor
            - make vendored copy of dependencies(将依赖复制到vendor下)
    - 在 go build 后面加上 -mod=vendor 参数，可以快速基于 vendor 构建项目。
    - **在 Go 1.14 及以后版本中，如果 Go 项目的顶层目录下存在 vendor 目录，那么 go build 默认也会优先基于 vendor 构建，除非你给 go build 传入 -mod=mod 的参数**
## 参考
- https://cloud.tencent.com/developer/article/1526095
- https://www.topgoer.cn/docs/golang/chapter02
- https://time.geekbang.org/column/article/429143

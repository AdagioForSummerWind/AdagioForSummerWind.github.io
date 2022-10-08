# iam_service_test


# 服务测试


## 36 | 代码测试（上）：如何编写 Go 语言单元测试和性能测试用例？

在 Go 项目开发中，我们不仅要开发功能，更重要的是确保这些功能稳定可靠，并且拥有一个不错的性能。要确保这些，就要对代码进行测试。开发人员通常会进行单元测试和性能测试，分别用来测试代码的功能是否正常和代码的性能是否满足需求。每种语言通常都有自己的测试包 / 模块，Go 语言也不例外。在 Go 中，我们可以通过testing包对代码进行单元测试和性能测试。这一讲，我会用一些示例来讲解如何编写单元测试和性能测试用例，下一讲则会介绍如何编写其他的测试类型，并介绍 IAM 项目的测试用例。


### 如何测试 Go 代码？
Go 语言有自带的测试框架testing，可以用来实现单元测试（T 类型）和性能测试（B 类型），通过go test命令来执行单元测试和性能测试。



go test 执行测试用例时，是以 go 包为单位进行测试的。执行时需要指定包名，比如go test 包名，如果没有指定包名，默认会选择执行命令时所在的包。go test 在执行时，会遍历以_test.go结尾的源码文件，执行其中以Test、Benchmark、Example开头的测试函数。为了演示如何编写测试用例，我预先编写了 4 个函数。假设这些函数保存在 test 目录下的math.go文件中，包名为test，math.go 代码如下：



```go
package test

import (
  "fmt"
  "math"
  "math/rand"
)

// Abs returns the absolute value of x.
func Abs(x float64) float64 {
  return math.Abs(x)
}

// Max returns the larger of x or y.
func Max(x, y float64) float64 {
  return math.Max(x, y)
}

// Min returns the smaller of x or y.
func Min(x, y float64) float64 {
  return math.Min(x, y)
}

// RandInt returns a non-negative pseudo-random int from the default Source.
func RandInt() int {
  return rand.Int()
}
```
在这一讲后面的内容中，我会演示如何编写测试用例，来对这些函数进行单元测试和性能测试。下面让我们先来看下测试命名规范。


### 测试命名规范
在我们对 Go 代码进行测试时，需要编写测试文件、测试函数、测试变量，它们都需要遵循一定的规范。这些规范有些来自于官方，有些则来自于社区。这里，我分别来介绍下测试文件、包、测试函数和测试变量的命名规范。


#### 测试文件的命名规范
Go 的测试文件名必须以_test.go结尾。例如，如果我们有一个名为person.go的文件，那它的测试文件必须命名为person_test.go。这样做是因为，Go 需要区分哪些文件是测试文件。这些测试文件可以被 go test 命令行工具加载，用来测试我们编写的代码，但会被 Go 的构建程序忽略掉，因为 Go 程序的运行不需要这些测试代码。


#### 包的命名规范
Go 的测试可以分为白盒测试和黑盒测试。


白盒测试：将测试和生产代码放在同一个 Go 包中，这使我们可以同时测试 Go 包中可导出和不可导出的标识符。当我们编写的单元测试需要访问 Go 包中不可导出的变量、函数和方法时，就需要编写白盒测试用例。

黑盒测试：将测试和生产代码放在不同的 Go 包中。这时，我们仅可以测试 Go 包的可导出标识符。这意味着我们的测试包将无法访问生产代码中的任何内部函数、变量或常量。


在白盒测试中，Go 的测试包名称需要跟被测试的包名保持一致，例如：person.go定义了一个person包，则person_test.go的包名也要为person，这也意味着person.go和person_test.go都要在同一个目录中。

在黑盒测试中，Go 的测试包名称需要跟被测试的包名不同，但仍然可以存放在同一个目录下。比如，person.go定义了一个person包，则person_test.go的包名需要跟person不同，通常我们命名为person_test。



如果不是需要使用黑盒测试，我们在做单元测试时要尽量使用白盒测试。一方面，这是 go test 工具的默认行为；另一方面，使用白盒测试，我们可以测试和使用不可导出的标识符。测试文件和包的命名规范，由 Go 语言及 go test 工具来强制约束。


#### 函数的命名规范
测试用例函数必须以Test、Benchmark、Example开头，例如TestXxx、BenchmarkXxx、ExampleXxx，Xxx部分为任意字母数字的组合，首字母大写。这是由 Go 语言和 go test 工具来进行约束的，Xxx一般是需要测试的函数名。

除此之外，还有一些社区的约束，这些约束不是强制的，但是遵循这些约束会让我们的测试函数名更加易懂。例如，我们有以下函数：


```go
package main

type Person struct {
  age  int64
}

func (p *Person) older(other *Person) bool {
  return p.age > other.age
}
```

很显然，我们可以把测试函数命名为TestOlder，这个名称可以很清晰地说明它是Older函数的测试用例。但是，如果我们想用多个测试用例来测试TestOlder函数，这些测试用例该如何命名呢？也许你会说，我们命名为TestOlder1、TestOlder2不就行了？


其实，还有其他更好的命名方法。比如，这种情况下，我们可以将函数命名为TestOlderXxx，其中Xxx代表Older函数的某个场景描述。例如，strings.Compare函数有如下测试函数：TestCompare、TestCompareIdenticalString、TestCompareStrings。



#### 变量的命名规范
Go 语言和 go test 没有对变量的命名做任何约束。但是，在编写单元测试用例时，还是有一些规范值得我们去遵守。

单元测试用例通常会有一个实际的输出，在单元测试中，我们会将预期的输出跟实际的输出进行对比，来判断单元测试是否通过。为了清晰地表达函数的实际输出和预期输出，可以将这两类输出命名为expected/actual，或者got/want。例如：



```go
if c.expected != actual {
  t.Fatalf("Expected User-Agent '%s' does not match '%s'", c.expected, actual)
}
```
或者：


```go
if got, want := diags[3].Description().Summary, undeclPlural; got != want {
  t.Errorf("wrong summary for diagnostic 3\ngot:  %s\nwant: %s", got, want)
}
```
其他的变量命名，我们可以遵循 Go 语言推荐的变量命名方法，例如：

- Go 中的变量名应该短而不是长，对于范围有限的局部变量来说尤其如此。
- 变量离声明越远，对名称的描述性要求越高。
- 像循环、索引之类的变量，名称可以是单个字母（i）。如果是不常见的变量和全局变量，变量名就需要具有更多的描述性。


上面，我介绍了 Go 测试的一些基础知识。接下来，我们来看看如何编写单元测试用例和性能测试用例。


### 单元测试
单元测试用例函数以 Test 开头，例如 TestXxx 或 Test_xxx （ Xxx 部分为任意字母数字组合，首字母大写）。函数参数必须是 *testing.T，可以使用该类型来记录错误或测试状态。


我们可以调用 testing.T 的 Error 、Errorf 、FailNow 、Fatal 、FatalIf 方法，来说明测试不通过；调用 Log 、Logf 方法来记录测试信息。函数列表和相关描述如下表所示：


![img](https://static001.geekbang.org/resource/image/b3/ab/b374d392abfe62459d2c22e6ff76c0ab.jpg?wh=1920x1570)

下面的代码是两个简单的单元测试函数（函数位于文件math_test.go中）：


```go
func TestAbs(t *testing.T) {
    got := Abs(-1)
    if got != 1 {
        t.Errorf("Abs(-1) = %f; want 1", got)
    }
}

func TestMax(t *testing.T) {
    got := Max(1, 2)
    if got != 2 {
        t.Errorf("Max(1, 2) = %f; want 2", got)
    }
}
```

执行go test命令来执行如上单元测试用例：


```
$ go test
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    0.002s
```

go test命令自动搜集所有的测试文件，也就是格式为*_test.go的文件，从中提取全部测试函数并执行。go test 还支持下面三个参数。


-v，显示所有测试函数的运行细节：


```
$ go test -v
=== RUN   TestAbs
--- PASS: TestAbs (0.00s)
=== RUN   TestMax
--- PASS: TestMax (0.00s)
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    0.002s
```

-run < regexp>，指定要执行的测试函数：


```
$ go test -v -run='TestA.*'
=== RUN   TestAbs
--- PASS: TestAbs (0.00s)
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    0.001s
```
上面的例子中，我们只运行了以TestA开头的测试函数。

-count N，指定执行测试函数的次数：


```
$ go test -v -run='TestA.*' -count=2
=== RUN   TestAbs
--- PASS: TestAbs (0.00s)
=== RUN   TestAbs
--- PASS: TestAbs (0.00s)
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    0.002s
```
#### 多个输入的测试用例
前面介绍的单元测试用例只有一个输入，但是很多时候，我们需要测试一个函数在多种不同输入下是否能正常返回。这时候，我们可以编写一个稍微复杂点的测试用例，用来支持多输入下的用例测试。例如，我们可以将TestAbs改造成如下函数：


```go
func TestAbs_2(t *testing.T) {
    tests := []struct {
        x    float64
        want float64
    }{
        {-0.3, 0.3},
        {-2, 2},
        {-3.1, 3.1},
        {5, 5},
    }

    for _, tt := range tests {
        if got := Abs(tt.x); got != tt.want {
            t.Errorf("Abs() = %f, want %v", got, tt.want)
        }
    }
}
```
上述测试用例函数中，我们定义了一个结构体数组，数组中的每一个元素代表一次测试用例。数组元素的的值包含输入和预期的返回值：

```go
tests := []struct {
    x    float64
    want float64
}{
    {-0.3, 0.3},
    {-2, 2},
    {-3.1, 3.1},
    {5, 5},
}
```
上述测试用例，将被测函数放在 for 循环中执行：


```go
   for _, tt := range tests {
        if got := Abs(tt.x); got != tt.want {
            t.Errorf("Abs() = %f, want %v", got, tt.want)
        }
    }
```
上面的代码将输入传递给被测函数，并将被测函数的返回值跟预期的返回值进行比较。如果相等，则说明此次测试通过，如果不相等则说明此次测试不通过。通过这种方式，我们就可以在一个测试用例中，测试不同的输入和输出，也就是不同的测试用例。如果要新增一个测试用例，根据需要添加输入和预期的返回值就可以了，这些测试用例都共享其余的测试代码。


上面的测试用例中，我们通过got != tt.want来对比实际返回结果和预期返回结果。我们也可以使用github.com/stretchr/testify/assert包中提供的函数来做结果对比，例如：


```go
func TestAbs_3(t *testing.T) {
    tests := []struct {
        x    float64
        want float64
    }{
        {-0.3, 0.3},
        {-2, 2},
        {-3.1, 3.1},
        {5, 5},
    }

    for _, tt := range tests {
        got := Abs(tt.x)
        assert.Equal(t, got, tt.want)
    }
}
```
使用assert来对比结果，有下面这些好处：
- 友好的输出结果，易于阅读。
- 因为少了if got := Xxx(); got != tt.wang {}的判断，代码变得更加简洁。
- 可以针对每次断言，添加额外的消息说明，例如assert.Equal(t, got, tt.want, "Abs test")。


assert 包还提供了很多其他函数，供开发者进行结果对比，例如Zero、NotZero、Equal、NotEqual、Less、True、Nil、NotNil等。如果想了解更多函数，你可以参考go doc github.com/stretchr/testify/assert。


#### 自动生成单元测试用例
通过上面的学习，你也许可以发现，测试用例其实可以抽象成下面的模型：


![img](https://static001.geekbang.org/resource/image/8f/fa/8f06e0a1bf2638a9255467a29e6dfcfa.jpg?wh=1920x688)

用代码可表示为：



```go
func TestXxx(t *testing.T) {
    type args struct {
        // TODO: Add function input parameter definition.
    }

    type want struct {
         // TODO: Add function return parameter definition.
    }
    tests := []struct {
        name string
        args args
        want want
    }{
        // TODO: Add test cases.
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            if got := Xxx(tt.args); got != tt.want {
                t.Errorf("Xxx() = %v, want %v", got, tt.want)
            }
        })
    }
}
```

既然测试用例可以抽象成一些模型，那么我们就可以基于这些模型来自动生成测试代码。Go 社区中有一些优秀的工具可以自动生成测试代码，我推荐你使用gotests工具。


下面，我来讲讲 gotests 工具的使用方法，可以分成三个步骤。第一步，安装 gotests 工具：


```
$ go get -u github.com/cweill/gotests/...
```

gotests 命令执行格式为：gotests [options] [PATH] [FILE] ...。gotests 可以为PATH下的所有 Go 源码文件中的函数生成测试代码，也可以只为某个FILE中的函数生成测试代码。


第二步，进入测试代码目录，执行 gotests 生成测试用例：


```
$ gotests -all -w .
```
上面的命令会为当前目录下所有 Go 源码文件中的函数生成测试代码。


第三步，添加测试用例：生成完测试用例，你只需要添加需要测试的输入和预期的输出就可以了。下面的测试用例是通过 gotests 生成的：


```go
func TestUnpointer(t *testing.T) {
    type args struct {
        offset *int64
        limit  *int64
    }
    tests := []struct {
        name string
        args args
        want *LimitAndOffset
    }{
        // TODO: Add test cases.
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            if got := Unpointer(tt.args.offset, tt.args.limit); !reflect.DeepEqual(got, tt.want) {
                t.Errorf("Unpointer() = %v, want %v", got, tt.want)
            }
        })
    }
}
```
我们只需要补全TODO位置的测试数据即可，补全后的测试用例见gorm_test.go文件。


### 性能测试
上面，我讲了用来测试代码的功能是否正常的单元测试，接下来我们来看下性能测试，它是用来测试代码的性能是否满足需求的。


性能测试的用例函数必须以Benchmark开头，例如BenchmarkXxx或Benchmark_Xxx（ Xxx 部分为任意字母数字组合，首字母大写）。


函数参数必须是*testing.B，函数内以b.N作为循环次数，其中N会在运行时动态调整，直到性能测试函数可以持续足够长的时间，以便能够可靠地计时。下面的代码是一个简单的性能测试函数（函数位于文件math_test.go中）：


```go
func BenchmarkRandInt(b *testing.B) {
    for i := 0; i < b.N; i++ {
        RandInt()
    }
}
```
go test命令默认不会执行性能测试函数，需要通过指定参数-bench 来运行性能测试函数。-bench后可以跟正则表达式，选择需要执行的性能测试函数，例如go test -bench=".*"表示执行所有的压力测试函数。执行go test -bench=".*"后输出如下：


```
$ go test -bench=".*"
goos: linux
goarch: amd64
pkg: github.com/marmotedu/gopractise-demo/31/test
BenchmarkRandInt-4      97384827                12.4 ns/op
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    1.223s
```
上面的结果只显示了性能测试函数的执行结果。BenchmarkRandInt性能测试函数的执行结果如下：



```
BenchmarkRandInt-4     90848414          12.8 ns/op
```

每个函数的性能执行结果一共有 3 列，分别代表不同的意思，这里用上面的函数举例子：


- BenchmarkRandInt-4，BenchmarkRandInt表示所测试的测试函数名，4 表示有 4 个 CPU 线程参与了此次测试，默认是GOMAXPROCS的值。
- 90848414 ，说明函数中的循环执行了90848414次。
- 12.8 ns/op，说明每次循环的执行平均耗时是 12.8 纳秒，该值越小，说明代码性能越高。



如果我们的性能测试函数在执行循环前，需要做一些耗时的准备工作，我们就需要重置性能测试时间计数，例如：


```go
func BenchmarkBigLen(b *testing.B) {
    big := NewBig()
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        big.Len()
    }
}
```
当然，我们也可以先停止性能测试的时间计数，然后再开始时间计数，例如：


```go
func BenchmarkBigLen(b *testing.B) {
  b.StopTimer() // 调用该函数停止压力测试的时间计数
  big := NewBig()
  b.StartTimer() // 重新开始时间
  for i := 0; i < b.N; i++ {
    big.Len()
  }
}
```

B 类型的性能测试还支持下面 4 个参数。

benchmem，输出内存分配统计：


```
$ go test -bench=".*" -benchmem
goos: linux
goarch: amd64
pkg: github.com/marmotedu/gopractise-demo/31/test
BenchmarkRandInt-4      96776823                12.8 ns/op             0 B/op          0 allocs/op
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    1.255s
```
指定了-benchmem参数后，执行结果中又多了两列： 0 B/op，表示每次执行分配了多少内存（字节），该值越小，说明代码内存占用越小；0 allocs/op，表示每次执行分配了多少次内存，该值越小，说明分配内存次数越少，意味着代码性能越高。


benchtime，指定测试时间和循环执行次数（格式需要为 Nx，例如 100x）：


```
$ go test -bench=".*" -benchtime=10s # 指定测试时间
goos: linux
goarch: amd64
pkg: github.com/marmotedu/gopractise-demo/31/test
BenchmarkRandInt-4      910328618               13.1 ns/op
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    13.260s
$ go test -bench=".*" -benchtime=100x # 指定循环执行次数
goos: linux
goarch: amd64
pkg: github.com/marmotedu/gopractise-demo/31/test
BenchmarkRandInt-4           100                16.9 ns/op
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    0.003s
```

cpu，指定 GOMAXPROCS。
timeout，指定测试函数执行的超时时间：


```
$ go test -bench=".*" -timeout=10s
goos: linux
goarch: amd64
pkg: github.com/marmotedu/gopractise-demo/31/test
BenchmarkRandInt-4      97375881                12.4 ns/op
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    1.224s
```

### 总结
代码开发完成之后，我们需要为代码编写单元测试用例，并根据需要，给一些函数编写性能测试用例。Go 语言提供了 testing 包，供我们编写测试用例，并通过 go test 命令来执行这些测试用例。

go test 在执行测试用例时，会查找具有固定格式的 Go 源码文件名，并执行其中具有固定格式的函数，这些函数就是测试用例。这就要求我们的测试文件名、函数名要符合 go test 工具的要求：Go 的测试文件名必须以 _test.go 结尾；测试用例函数必须以 Test 、 Benchmark 、 Example 开头。此外，我们在编写测试用例时，还要注意包和变量的命名规范。

Go 项目开发中，编写得最多的是单元测试用例。单元测试用例函数以 Test 开头，例如 TestXxx 或 Test_xxx （Xxx 部分为任意字母数字组合，首字母大写）。函数参数必须是 *testing.T ，可以使用该类型来记录错误或测试状态。我们可以调用 testing.T 的 Error 、Errorf 、FailNow 、Fatal 、FatalIf 方法，来说明测试不通过；调用 Log 、Logf 方法来记录测试信息。


下面是一个简单的单元测试函数：



```go
func TestAbs(t *testing.T) {
    got := Abs(-1)
    if got != 1 {
        t.Errorf("Abs(-1) = %f; want 1", got)
    }
}
```
编写完测试用例之后，可以使用 go test 命令行工具来执行这些测试用例。此外，我们还可以使用gotests工具，来自动地生成单元测试用例，从而减少编写测试用例的工作量。

我们在 Go 项目开发中，还经常需要编写性能测试用例。性能测试用例函数必须以Benchmark开头，以*testing.B 作为函数入参，通过 go test -bench 运行。

## 37 | 代码测试（下）：Go 语言其他测试类型及 IAM 测试介绍
上一讲，我介绍了 Go 中的两类测试：单元测试和性能测试。在 Go 中，还有一些其他的测试类型和测试方法，值得我们去了解和掌握。此外，IAM 项目也编写了大量测试用例，这些测试用例使用了不同的编写方法，你可以通过学习 IAM 的测试用例来验证你学到的测试知识。今天，我就来介绍下 Go 语言中的其他测试类型：示例测试、TestMain 函数、Mock 测试、Fake 测试等，并且介绍下 IAM 项目是如何编写和运行测试用例的。

### 示例测试
示例测试以Example开头，没有输入和返回参数，通常保存在example_test.go文件中。示例测试可能包含以Output:或者Unordered output:开头的注释，这些注释放在函数的结尾部分。Unordered output:开头的注释会忽略输出行的顺序。

执行go test命令时，会执行这些示例测试，并且 go test 会将示例测试输出到标准输出的内容，跟注释作对比（比较时将忽略行前后的空格）。如果相等，则示例测试通过测试；如果不相等，则示例测试不通过测试。下面是一个示例测试（位于 example_test.go 文件中）：


```go
func ExampleMax() {
    fmt.Println(Max(1, 2))
    // Output:
    // 2
}
```
执行 go test 命令，测试ExampleMax示例测试：


```
$ go test -v -run='Example.*'
=== RUN   ExampleMax
--- PASS: ExampleMax (0.00s)
PASS
ok      github.com/marmotedu/gopractise-demo/31/test    0.004s
```
可以看到ExampleMax测试通过。这里测试通过是因为fmt.Println(Max(1, 2))向标准输出输出了2，跟// Output:后面的2一致。当示例测试不包含Output:或者Unordered output:注释时，执行go test只会编译这些函数，但不会执行这些函数。


#### 示例测试命名规范
示例测试需要遵循一些命名规范，因为只有这样，Godoc 才能将示例测试和包级别的标识符进行关联。例如，有以下示例测试（位于 example_test.go 文件中）：


```go
package stringutil_test

import (
    "fmt"

    "github.com/golang/example/stringutil"
)

func ExampleReverse() {
    fmt.Println(stringutil.Reverse("hello"))
    // Output: olleh
}
```
Godoc 将在Reverse函数的文档旁边提供此示例，如下图所示：


![img](https://static001.geekbang.org/resource/image/d8/93/d8ae5e99fe1d159e9b3ba1f815b24693.png?wh=540x374)

示例测试名以Example开头，后面可以不跟任何字符串，也可以跟函数名、类型名或者类型_方法名，中间用下划线_连接，例如：



```go
func Example() { ... } // 代表了整个包的示例
func ExampleF() { ... } // 函数F的示例
func ExampleT() { ... } // 类型T的示例
func ExampleT_M() { ... } // 方法T_M的示例
```
当某个函数 / 类型 / 方法有多个示例测试时，可以通过后缀来区分，后缀必须以小写字母开头，例如：


```go
func ExampleReverse()
func ExampleReverse_second()
func ExampleReverse_third()
```


#### 大型示例
有时候，我们需要编写一个大型的示例测试，这时候我们可以编写一个整文件的示例（whole file example），它有这几个特点：文件名以_test.go结尾；只包含一个示例测试，文件中没有单元测试函数和性能测试函数；至少包含一个包级别的声明；当展示这类示例测试时，godoc 会直接展示整个文件。例如：


```go
package sort_test

import (
    "fmt"
    "sort"
)

type Person struct {
    Name string
    Age  int
}

func (p Person) String() string {
    return fmt.Sprintf("%s: %d", p.Name, p.Age)
}

// ByAge implements sort.Interface for []Person based on
// the Age field.
type ByAge []Person

func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }

func Example() {
    people := []Person{
        {"Bob", 31},
        {"John", 42},
        {"Michael", 17},
        {"Jenny", 26},
    }

    fmt.Println(people)
    sort.Sort(ByAge(people))
    fmt.Println(people)

    // Output:
    // [Bob: 31 John: 42 Michael: 17 Jenny: 26]
    // [Michael: 17 Jenny: 26 Bob: 31 John: 42]
}
```
一个包可以包含多个 whole file example，一个示例一个文件，例如example_interface_test.go、example_keys_test.go、example_search_test.go等。


#### TestMain 函数
有时候，我们在做测试的时候，可能会在测试之前做些准备工作，例如创建数据库连接等；在测试之后做些清理工作，例如关闭数据库连接、清理测试文件等。这时，我们可以在_test.go文件中添加TestMain函数，其入参为*testing.M。



TestMain是一个特殊的函数（相当于 main 函数），测试用例在执行时，会先执行TestMain函数，然后可以在TestMain中调用m.Run()函数执行普通的测试函数。在m.Run()函数前面我们可以编写准备逻辑，在m.Run()后面我们可以编写清理逻辑。我们在示例测试文件[math_test.go](https://github.com/marmotedu/gopractise-demo/blob/master/test/math_test.go)中添加如下 TestMain 函数：


```go
func TestMain(m *testing.M) {
    fmt.Println("do some setup")
    m.Run()
    fmt.Println("do some cleanup")
}
```
执行 go test，输出如下：

```
$ go test -v
do some setup
=== RUN   TestAbs
--- PASS: TestAbs (0.00s)
...
=== RUN   ExampleMax
--- PASS: ExampleMax (0.00s)
PASS
do some cleanup
ok    github.com/marmotedu/gopractise-demo/31/test  0.006s
```
在执行测试用例之前，打印了do some setup，在测试用例运行完成之后，打印了do some cleanup。IAM 项目的测试用例中，使用 TestMain 函数在执行测试用例前连接了一个 fake 数据库，代码如下（位于internal/apiserver/service/v1/user_test.go文件中）：


```go
func TestMain(m *testing.M) {
    fakeStore, _ := fake.NewFakeStore()
    store.SetClient(fakeStore)
    os.Exit(m.Run())
}
```
单元测试、性能测试、示例测试、TestMain 函数是 go test 支持的测试类型。此外，为了测试在函数内使用了 Go Interface 的函数，我们还延伸出了 Mock 测试和 Fake 测试两种测试类型。



### Mock 测试
一般来说，单元测试中是不允许有外部依赖的，那么也就是说，这些外部依赖都需要被模拟。在 Go 中，一般会借助各类 Mock 工具来模拟一些依赖。


GoMock 是由 Golang 官方开发维护的测试框架，实现了较为完整的基于 interface 的 Mock 功能，能够与 Golang 内置的 testing 包良好集成，也能用于其他的测试环境中。GoMock 测试框架包含了 GoMock 包和 mockgen 工具两部分，其中 GoMock 包用来完成对象生命周期的管理，mockgen 工具用来生成 interface 对应的 Mock 类源文件。下面，我来分别详细介绍下 GoMock 包和 mockgen 工具，以及它们的使用方法。


#### 安装 GoMock
要使用 GoMock，首先需要安装 GoMock 包和 mockgen 工具，安装方法如下:


```
$ go get github.com/golang/mock/gomock
$ go install github.com/golang/mock/mockgen
```

下面，我通过一个获取当前 Golang 最新版本的例子，来给你演示下如何使用 GoMock。示例代码目录结构如下（目录下的代码见gomock）：


```
tree .
.
├── go_version.go
├── main.go
└── spider
    └── spider.go
```
spider.go文件中定义了一个Spider接口，spider.go代码如下：


```go
package spider

type Spider interface {
    GetBody() string
}
```
Spider接口中的 GetBody 方法可以抓取https://golang.org首页的Build version字段，来获取 Golang 的最新版本。我们在go_version.go文件中，调用Spider接口的GetBody方法，go_version.go代码如下：

```go
package gomock

import (
    "github.com/marmotedu/gopractise-demo/gomock/spider"
)

func GetGoVersion(s spider.Spider) string {
    body := s.GetBody()
    return body
}

```
GetGoVersion函数直接返回表示版本的字符串。正常情况下，我们会写出如下的单元测试代码：


```go
func TestGetGoVersion(t *testing.T) {
    v := GetGoVersion(spider.CreateGoVersionSpider())
    if v != "go1.8.3" {
        t.Error("Get wrong version %s", v)
    }
}
```

上面的测试代码，依赖spider.CreateGoVersionSpider()返回一个实现了Spider接口的实例（爬虫）。但很多时候，spider.CreateGoVersionSpider()爬虫可能还没有实现，或者在单元测试环境下不能运行（比如，在单元测试环境中连接数据库），这时候TestGetGoVersion测试用例就无法执行。


那么，如何才能在这种情况下运行TestGetGoVersion测试用例呢？这时候，我们就可以通过 Mock 工具，Mock 一个爬虫实例。接下来我讲讲具体操作。首先，用 GoMock 提供的 mockgen 工具，生成要 Mock 的接口的实现，我们在 gomock 目录下执行以下命令：


```
$ mockgen -destination spider/mock/mock_spider.go -package spider github.com/marmotedu/gopractise-demo/gomock/spider Spider
```

上面的命令会在spider/mock目录下生成mock_spider.go文件：



```
$ tree .
.
├── go_version.go
├── go_version_test.go
├── go_version_test_traditional_method.go~
└── spider
    ├── mock
    │   └── mock_spider.go
    └── spider.go
```

mock_spider.go文件中，定义了一些函数 / 方法，可以支持我们编写TestGetGoVersion测试函数。这时候，我们的单元测试代码如下（见go_version_test.go文件）：


```go
package gomock

import (
  "testing"

  "github.com/golang/mock/gomock"

  spider "github.com/marmotedu/gopractise-demo/gomock/spider/mock"
)

func TestGetGoVersion(t *testing.T) {
  ctrl := gomock.NewController(t)
  defer ctrl.Finish()

  mockSpider := spider.NewMockSpider(ctrl)
  mockSpider.EXPECT().GetBody().Return("go1.8.3")
  goVer := GetGoVersion(mockSpider)

  if goVer != "go1.8.3" {
    t.Errorf("Get wrong version %s", goVer)
  }
}
```

这一版本的TestGetGoVersion通过 GoMock， Mock 了一个Spider接口，而不用去实现一个Spider接口。这就大大降低了单元测试用例编写的复杂度。通过 Mock，很多不能测试的函数也变得可测试了。通过上面的测试用例，我们可以看到，GoMock 和上一讲介绍的 testing 单元测试框架可以紧密地结合起来工作。



#### mockgen 工具介绍
上面，我介绍了如何使用 GoMock 编写单元测试用例。其中，我们使用到了mockgen工具来生成 Mock 代码，mockgen工具提供了很多有用的功能，这里我来详细介绍下。


mockgen工具是 GoMock 提供的，用来 Mock 一个 Go 接口。它可以根据给定的接口，来自动生成 Mock 代码。这里，有两种模式可以生成 Mock 代码，**分别是源码模式和反射模式。**



源码模式
如果有接口文件，则可以通过以下命令来生成 Mock 代码：


```
$ mockgen -destination spider/mock/mock_spider.go -package spider -source spider/spider.go
```
上面的命令，Mock 了spider/spider.go文件中定义的Spider接口，并将 Mock 代码保存在spider/mock/mock_spider.go文件中，文件的包名为spider。mockgen 工具的参数说明见下表：


![img](https://static001.geekbang.org/resource/image/e7/9c/e72102362e2ae3225e868f125654689c.jpg?wh=1920x1210)

反射模式
此外，mockgen 工具还支持通过使用反射程序来生成 Mock 代码。它通过传递两个非标志参数，即导入路径和逗号分隔的接口列表来启用，其他参数和源码模式共用，例如：

```
$ mockgen -destination spider/mock/mock_spider.go -package spider github.com/marmotedu/gopractise-demo/gomock/spider Spider
```

#### 通过注释使用 mockgen
如果有多个文件，并且分散在不同的位置，那么我们要生成 Mock 文件的时候，需要对每个文件执行多次 mockgen 命令（这里假设包名不相同）。这种操作还是比较繁琐的，mockgen 还提供了一种通过注释生成 Mock 文件的方式，此时需要借助go generate工具。


在接口文件的代码中，添加以下注释（具体代码见spider.go文件）：


```
//go:generate mockgen -destination mock_spider.go -package spider github.com/cz-it/blog/blog/Go/testing/gomock/example/spider Spider
```

这时候，我们只需要在gomock目录下，执行以下命令，就可以自动生成 Mock 代码：


```
$ go generate ./...
```

#### 使用 Mock 代码编写单元测试用例
生成了 Mock 代码之后，我们就可以使用它们了。这里我们结合testing来编写一个使用了 Mock 代码的单元测试用例。


首先，需要在单元测试代码里创建一个 Mock 控制器：


```
ctrl := gomock.NewController(t)
```
将*testing.T传递给 GoMock ，生成一个Controller对象，该对象控制了整个 Mock 的过程。在操作完后，还需要进行回收，所以一般会在NewController后面 defer 一个 Finish，代码如下：


```
defer ctrl.Finish()
```
然后，就可以调用 Mock 的对象了：


```
mockSpider := spider.NewMockSpider(ctrl)
```
这里的spider是 mockgen 命令里面传递的包名，后面是NewMockXxxx格式的对象创建函数，Xxx是接口名。这里，我们需要传递控制器对象进去，返回一个 Mock 实例。接着，有了 Mock 实例，我们就可以调用其断言方法EXPECT()了。

gomock 采用了链式调用法，通过.连接函数调用，可以像链条一样连接下去。例如：


```
mockSpider.EXPECT().GetBody().Return("go1.8.3")
```

Mock 一个接口的方法，我们需要 Mock 该方法的入参和返回值。我们可以通过参数匹配来 Mock 入参，通过 Mock 实例的 Return 方法来 Mock 返回值。下面，我们来分别看下如何指定入参和返回值。先来看如何指定入参。如果函数有参数，我们可以使用参数匹配来指代函数的参数，例如：


```
mockSpider.EXPECT().GetBody(gomock.Any(), gomock.Eq("admin")).Return("go1.8.3")
```
gomock 支持以下参数匹配：
- gomock.Any()，可以用来表示任意的入参。
- gomock.Eq(value)，用来表示与 value 等价的值。
- gomock.Not(value)，用来表示非 value 以外的值。
- gomock.Nil()，用来表示 None 值。

接下来，我们看如何指定返回值。EXPECT()得到 Mock 的实例，然后调用 Mock 实例的方法，该方法返回第一个Call对象，然后可以对其进行条件约束，比如使用 Mock 实例的 Return 方法约束其返回值。Call对象还提供了以下方法来约束 Mock 实例：

```go
func (c *Call) After(preReq *Call) *Call // After声明调用在preReq完成后执行
func (c *Call) AnyTimes() *Call // 允许调用次数为 0 次或更多次
func (c *Call) Do(f interface{}) *Call // 声明在匹配时要运行的操作
func (c *Call) MaxTimes(n int) *Call // 设置最大的调用次数为 n 次
func (c *Call) MinTimes(n int) *Call // 设置最小的调用次数为 n 次
func (c *Call) Return(rets ...interface{}) *Call //  // 声明模拟函数调用返回的值
func (c *Call) SetArg(n int, value interface{}) *Call // 声明使用指针设置第 n 个参数的值
func (c *Call) Times(n int) *Call // 设置调用次数为 n 次
```
上面列出了多个 Call 对象提供的约束方法，接下来我会介绍 3 个常用的约束方法：指定返回值、指定执行次数和指定执行顺序。


指定返回值
我们可以提供调用Call的Return函数，来指定接口的返回值，例如：



```
mockSpider.EXPECT().GetBody().Return("go1.8.3")
```

指定执行次数
有时候，我们需要指定函数执行多少次，例如：对于接受网络请求的函数，计算其执行了多少次。我们可以通过Call的Times函数来指定执行次数：


```
mockSpider.EXPECT().Recv().Return(nil).Times(3)
```

上述代码，执行了三次 Recv 函数，这里 gomock 还支持其他的执行次数限制：
- AnyTimes()，表示执行 0 到多次。
- MaxTimes(n int)，表示如果没有设置，最多执行 n 次。
- MinTimes(n int)，表示如果没有设置，最少执行 n 次。


指定执行顺序
有时候，我们还要指定执行顺序，比如要先执行 Init 操作，然后才能执行 Recv 操作：


```
initCall := mockSpider.EXPECT().Init()
mockSpider.EXPECT().Recv().After(initCall)
```
最后，我们可以使用go test来测试使用了 Mock 代码的单元测试代码：


```
$ go test -v
=== RUN   TestGetGoVersion
--- PASS: TestGetGoVersion (0.00s)
PASS
ok    github.com/marmotedu/gopractise-demo/gomock  0.002s
```


### Fake 测试
在 Go 项目开发中，对于比较复杂的接口，我们还可以 Fake 一个接口实现，来进行测试。所谓 Fake 测试，其实就是针对接口实现一个假（fake）的实例。至于如何实现 Fake 实例，需要你根据业务自行实现。例如：IAM 项目中 iam-apiserver 组件就实现了一个 fake store，代码见fake目录。因为这一讲后面的 IAM 项目测试实战部分有介绍，所以这里不再展开讲解。


### 何时编写和执行单元测试用例？
上面，我介绍了 Go 代码测试的基础知识，这里我再来分享下在做测试时一个比较重要的知识点：何时编写和执行单元测试用例。


#### 编码前：TDD


![img](https://static001.geekbang.org/resource/image/48/b2/4830b21b55d194eccf1ec74637ee3eb2.png?wh=538x516)

Test-Driven Development，也就是测试驱动开发，是敏捷开发的⼀项核心实践和技术，也是⼀种设计方法论。简单来说，TDD 原理就是：开发功能代码之前，先编写测试用例代码，然后针对测试用例编写功能代码，使其能够通过。这样做的好处在于，通过测试的执行代码肯定满足需求，而且有助于面向接口编程，降低代码耦合，也极大降低了 bug 的出现几率。

然而，TDD 的坏处也显而易见：由于测试用例是在进行代码设计之前写的，很有可能限制开发者对代码的整体设计；并且，由于 TDD 对开发⼈员要求非常高，体现的思想跟传统开发思维也不⼀样，因此实施起来比较困难；此外，因为要先编写测试用例，TDD 也可能会影响项目的研发进度。所以，在客观情况不满足的情况下，不应该盲目追求对业务代码使用 TDD 的开发模式。


#### 与编码同步进行：增量
及时为增量代码写单测是一种良好的习惯。一方面是因为，此时我们对需求有一定的理解，能够更好地写出单元测试来验证正确性。并且，在单测阶段就发现问题，而不是等到联调测试中才发现，修复的成本也是最小的。


另一方面，在写单测的过程中，我们也能够反思业务代码的正确性、合理性，推动我们在实现的过程中更好地反思代码的设计，并及时调整。


#### 编码后：存量
在完成业务需求后，我们可能会遇到这种情况：因为上线时间比较紧张、没有单测相关规划，开发阶段只手动测试了代码是否符合功能。

如果这部分存量代码出现较大的新需求，或者维护已经成为问题，需要大规模重构，这正是推动补全单测的好时机。为存量代码补充上单测，一方面能够推进重构者进一步理解原先的逻辑，另一方面也能够增强重构者重构代码后的信心，降低风险。

但是，补充存量单测可能需要再次回忆理解需求和逻辑设计等细节，而有时写单测的人并不是原编码的设计者，所以编码后编写和执行单元测试用例也有一定的不足。


#### 测试覆盖率
我们写单元测试的时候应该想得很全面，能够覆盖到所有的测试用例，但有时也会漏过一些 case，Go 提供了 cover 工具来统计测试覆盖率。具体可以分为两大步骤。

第一步，生成测试覆盖率数据：

```
$ go test -coverprofile=coverage.out
do some setup
PASS
coverage: 40.0% of statements
do some cleanup
ok    github.com/marmotedu/gopractise-demo/test  0.003s

```

上面的命令在当前目录下生成了coverage.out覆盖率数据文件。


![img](https://static001.geekbang.org/resource/image/3c/01/3c11a0d41d6ed736f364c1693a2eff01.png?wh=1920x366)
第二步，分析覆盖率文件：


```
$ go tool cover -func=coverage.out
do some setup
PASS
coverage: 40.0% of statements
do some cleanup
ok    github.com/marmotedu/gopractise-demo/test  0.003s
[colin@dev test]$ go tool cover -func=coverage.out
github.com/marmotedu/gopractise-demo/test/math.go:9:  Abs    100.0%
github.com/marmotedu/gopractise-demo/test/math.go:14:  Max    100.0%
github.com/marmotedu/gopractise-demo/test/math.go:19:  Min    0.0%
github.com/marmotedu/gopractise-demo/test/math.go:24:  RandInt    0.0%
github.com/marmotedu/gopractise-demo/test/math.go:29:  Floor    0.0%
total:              (statements)  40.0%
```
在上述命令的输出中，我们可以查看到哪些函数没有测试，哪些函数内部的分支没有测试完全。cover 工具会根据被执行代码的行数与总行数的比例计算出覆盖率。可以看到，Abs 和 Max 函数的测试覆盖率为 100%，Min 和 RandInt 的测试覆盖率为 0。


我们还可以使用go tool cover -html生成HTML格式的分析文件，可以更加清晰地展示代码的测试情况：


```
$ go tool cover -html=coverage.out -o coverage.html
```
上述命令会在当前目录下生成一个coverage.html文件，用浏览器打开coverage.html文件，可以更加清晰地看到代码的测试情况，如下图所示：


![img](https://static001.geekbang.org/resource/image/f0/5e/f089f5d44ba06f052c1c46c858c2b75e.png?wh=1524x1075)

通过上图，我们可以知道红色部分的代码没有被测试到，可以让我们接下来有针对性地添加测试用例，而不是一头雾水，不知道需要为哪些代码编写测试用例。



在 Go 项目开发中，我们往往会把测试覆盖率作为代码合并的一个强制要求，所以需要在进行代码测试时，同时生成代码覆盖率数据文件。在进行代码测试时，可以通过分析该文件，来判断我们的代码测试覆盖率是否满足要求，如果不满足则代码测试失败。


### IAM 项目测试实战
接下来，我来介绍下 IAM 项目是如何编写和运行测试用例的，你可以通过 IAM 项目的测试用例，加深对上面内容的理解。



#### IAM 项目是如何运行测试用例的？
首先，我们来看下 IAM 项目是如何执行测试用例的。在 IAM 项目的源码根目录下，可以通过运行make test执行测试用例，make test会执行iam/scripts/make-rules/golang.mk文件中的go.test伪目标，规则如下：


```
.PHONY: go.test
go.test: tools.verify.go-junit-report
  @echo "===========> Run unit test"
  @set -o pipefail;$(GO) test -race -cover -coverprofile=$(OUTPUT_DIR)/coverage.out \\
    -timeout=10m -short -v `go list ./...|\
    egrep -v $(subst $(SPACE),'|',$(sort $(EXCLUDE_TESTS)))` 2>&1 | \\
    tee >(go-junit-report --set-exit-code >$(OUTPUT_DIR)/report.xml)
  @sed -i '/mock_.*.go/d' $(OUTPUT_DIR)/coverage.out # remove mock_.*.go files from test coverage
  @$(GO) tool cover -html=$(OUTPUT_DIR)/coverage.out -o $(OUTPUT_DIR)/coverage.html
```
在上述规则中，我们执行go test时设置了超时时间、竞态检查，开启了代码覆盖率检查，覆盖率测试数据保存在了coverage.out文件中。在 Go 项目开发中，并不是所有的包都需要单元测试，所以上面的命令还过滤掉了一些不需要测试的包，这些包配置在EXCLUDE_TESTS变量中：


```
EXCLUDE_TESTS=github.com/marmotedu/iam/test github.com/marmotedu/iam/pkg/log github.com/marmotedu/iam/third_party github.com/marmotedu/iam/internal/pump/storage github.com/marmotedu/iam/internal/pump github.com/marmotedu/iam/internal/pkg/logger
```
同时，也调用了go-junit-report将 go test 的结果转化成了 xml 格式的报告文件，该报告文件会被一些 CI 系统，例如 Jenkins 拿来解析并展示结果。上述代码也同时生成了 coverage.html 文件，该文件可以存放在制品库中，供我们后期分析查看。


这里需要注意，Mock 的代码是不需要编写测试用例的，为了避免影响项目的单元测试覆盖率，需要将 Mock 代码的单元测试覆盖率数据从coverage.out文件中删除掉，go.test规则通过以下命令删除这些无用的数据：


```
sed -i '/mock_.*.go/d' $(OUTPUT_DIR)/coverage.out # remove mock_.*.go files from test coverage
```
另外，还可以通过make cover来进行单元测试覆盖率测试，make cover会执行iam/scripts/make-rules/golang.mk文件中的go.test.cover伪目标，规则如下：


```
.PHONY: go.test.cover
go.test.cover: go.test
  @$(GO) tool cover -func=$(OUTPUT_DIR)/coverage.out | \\
    awk -v target=$(COVERAGE) -f $(ROOT_DIR)/scripts/coverage.awk
```

上述目标依赖go.test，也就是说执行单元测试覆盖率目标之前，会先进行单元测试，然后使用单元测试产生的覆盖率数据coverage.out计算出总的单元测试覆盖率，这里是通过coverage.awk脚本来计算的。如果单元测试覆盖率不达标，Makefile 会报错并退出。可以通过 Makefile 的COVERAGE变量来设置单元测试覆盖率阈值。


COVERAGE 的默认值为 60，我们也可以在命令行手动指定，例如：


```
$ make cover COVERAGE=80
```
为了确保项目的单元测试覆盖率达标，需要设置单元测试覆盖率质量红线。一般来说，这些红线很难靠开发者的自觉性去保障，所以好的方法是将质量红线加入到 CICD 流程中。所以，在Makefile文件中，我将cover放在all目标的依赖中，并且位于 build 之前，也就是all: gen add-copyright format lint cover build。这样每次当我们执行 make 时，会自动进行代码测试，并计算单元测试覆盖率，如果覆盖率不达标，则停止构建；如果达标，继续进入下一步的构建流程。

#### IAM 项目测试案例分享
接下来，我会给你展示一些 IAM 项目的测试案例，因为这些测试案例的实现方法，我在36 讲 和这一讲的前半部分已有详细介绍，所以这里，我只列出具体的实现代码，不会再介绍这些代码的实现方法。


单元测试案例
我们可以手动编写单元测试代码，也可以使用 gotests 工具生成单元测试代码。先来看手动编写测试代码的案例。这里单元测试代码见Test_Option，代码如下：

```go
func Test_Option(t *testing.T) {
    fs := pflag.NewFlagSet("test", pflag.ExitOnError)
    opt := log.NewOptions()
    opt.AddFlags(fs)

    args := []string{"--log.level=debug"}
    err := fs.Parse(args)
    assert.Nil(t, err)

    assert.Equal(t, "debug", opt.Level)
}

```
上述代码中，使用了github.com/stretchr/testify/assert包来对比结果。再来看使用 gotests 工具生成单元测试代码的案例（Table-Driven 的测试模式）。出于效率上的考虑，IAM 项目的单元测试用例，基本都是使用 gotests 工具生成测试用例模板代码，并基于这些模板代码填充测试 Case 的。代码见service_test.go文件。



性能测试案例
IAM 项目的性能测试用例，见BenchmarkListUser测试函数。代码如下：

```go
func BenchmarkListUser(b *testing.B) {
  opts := metav1.ListOptions{
    Offset: pointer.ToInt64(0),
    Limit:  pointer.ToInt64(50),
  }
  storeIns, _ := fake.GetFakeFactoryOr()
  u := &userService{
    store: storeIns,
  }

  for i := 0; i < b.N; i++ {
    _, _ = u.List(context.TODO(), opts)
  }
}
```

示例测试案例
IAM 项目的示例测试用例见example_test.go文件。example_test.go中的一个示例测试代码如下：


```go
func ExampleNew() {
  err := New("whoops")
  fmt.Println(err)

  // Output: whoops
}
```

TestMain 测试案例
IAM 项目的 TestMain 测试案例，见user_test.go文件中的TestMain函数：


```go
func TestMain(m *testing.M) {
    _, _ = fake.GetFakeFactoryOr()
    os.Exit(m.Run())
}
```
TestMain函数初始化了 fake Factory，然后调用m.Run执行测试用例。


Mock 测试案例
Mock 代码见internal/apiserver/service/v1/mock_service.go，使用 Mock 的测试用例见internal/apiserver/controller/v1/user/create_test.go文件。因为代码比较多，这里建议你打开链接，查看测试用例的具体实现。我们可以在 IAM 项目的根目录下执行以下命令，来自动生成所有的 Mock 文件：


```
$ go generate ./...
```
Fake 测试案例
fake store 代码实现位于internal/apiserver/store/fake目录下。fake store 的使用方式，见user_test.go文件：


```go
func TestMain(m *testing.M) {
    _, _ = fake.GetFakeFactoryOr()
    os.Exit(m.Run())
}

func BenchmarkListUser(b *testing.B) {
    opts := metav1.ListOptions{
        Offset: pointer.ToInt64(0),
        Limit:  pointer.ToInt64(50),
    }
    storeIns, _ := fake.GetFakeFactoryOr()
    u := &userService{
        store: storeIns,
    }

    for i := 0; i < b.N; i++ {
        _, _ = u.List(context.TODO(), opts)
    }
}
```
上述代码通过TestMain初始化 fake 实例（store.Factory接口类型）：


```go
func GetFakeFactoryOr() (store.Factory, error) {
    once.Do(func() {
        fakeFactory = &datastore{
            users:    FakeUsers(ResourceCount),
            secrets:  FakeSecrets(ResourceCount),
            policies: FakePolicies(ResourceCount),
        }
    })

    if fakeFactory == nil {
        return nil, fmt.Errorf("failed to get mysql store fatory, mysqlFactory: %+v", fakeFactory)
    }

    return fakeFactory, nil
}
```
GetFakeFactoryOr函数，创建了一些 fake users、secrets、policies，并保存在了fakeFactory变量中，供后面的测试用例使用，例如 BenchmarkListUser、Test_newUsers 等。



### 其他测试工具 / 包
最后，我再来分享下 Go 项目测试中常用的工具 / 包，因为内容较多，我就不详细介绍了，如果感兴趣你可以点进链接自行学习。我将这些测试工具 / 包分为了两类，分别是测试框架和 Mock 工具。


#### 测试框架
Testify 框架：Testify 是 Go test 的预判工具，它能让你的测试代码变得更优雅和高效，测试结果也变得更详细。

GoConvey 框架：GoConvey 是一款针对 Golang 的测试框架，可以管理和运行测试用例，同时提供了丰富的断言函数，并支持很多 Web 界面特性。


#### Mock 工具
这一讲里，我介绍了 Go 官方提供的 Mock 框架 GoMock，不过还有一些其他的优秀 Mock 工具可供我们使用。这些 Mock 工具分别用在不同的 Mock 场景中，我在 10 讲中已经介绍过。不过，为了使我们这一讲的测试知识体系更加完整，这里我还是再提一次，你可以复习一遍。


- sqlmock：可以用来模拟数据库连接。数据库是项目中比较常见的依赖，在遇到数据库依赖时都可以用它。
- httpmock：可以用来 Mock HTTP 请求。
- bouk/monkey：猴子补丁，能够通过替换函数指针的方式来修改任意函数的实现。如果 golang/mock、sqlmock 和 httpmock 这几种方法都不能满足我们的需求，我们可以尝试用猴子补丁的方式来 Mock 依赖。可以这么说，猴子补丁提供了单元测试 Mock 依赖的最终解决方案。


### 总结
这一讲，我介绍了除单元测试和性能测试之外的另一些测试方法。

除了示例测试和 TestMain 函数，我还详细介绍了 Mock 测试，也就是如何使用 GoMock 来测试一些在单元测试环境下不好实现的接口。绝大部分情况下，可以使用 GoMock 来 Mock 接口，但是对于一些业务逻辑比较复杂的接口，我们可以通过 Fake 一个接口实现，来对代码进行测试，这也称为 Fake 测试。此外，我还介绍了何时编写和执行测试用例。我们可以根据需要，选择在编写代码前、编写代码中、编写代码后编写测试用例。

为了保证单元测试覆盖率，我们还应该为整个项目设置单元测试覆盖率质量红线，并将该质量红线加入到 CICD 流程中。我们可以通过 go test -coverprofile=coverage.out 命令来生成测试覆盖率数据，通过go tool cover -func=coverage.out 命令来分析覆盖率文件。


IAM 项目中使用了大量的测试方法和技巧来测试代码，为了加深你对测试知识的理解，我也列举了一些测试案例，供你参考、学习和验证。具体的测试案例，你可以返回前面查看下。除此之外，我们还可以使用其他一些测试框架，例如 Testify 框架和 GoConvey 框架。在 Go 代码测试中，我们最常使用的是 Go 官方提供的 Mock 框架 GoMock，但仍然有其他优秀的 Mock 工具，可供我们在不同场景下使用，例如 sqlmock、httpmock、bouk/monkey 等。

## 38｜性能分析（上）：如何分析 Go 语言代码的性能？

作为开发人员，我们一般都局限在功能上的单元测试中，对一些性能上的细节往往不会太关注。但是，如果我们在上线的时候对项目的整体性能没有一个全面的了解，随着请求量越来越大，可能会出现各种各样的问题，比如 CPU 占用高、内存使用率高、请求延时高等。为了避免这些性能瓶颈，我们在开发的过程中需要通过一定的手段，来对程序进行性能分析。


Go 语言已经为开发者内置了很多性能调优、监控的工具和方法，这大大提升了我们 profile 分析的效率，借助这些工具，我们可以很方便地对 Go 程序进行性能分析。在 Go 语言开发中，开发者基本都是通过内置的pprof工具包来进行性能分析的。

在进行性能分析时，我们会先借助一些工具和包，生成性能数据文件，然后再通过pprof工具分析性能数据文件，从而分析代码的性能。那么接下来，我们就分别来看下如何执行这两步操作。


### 生成性能数据文件
要查看性能数据，需要先生成性能数据文件。生成性能数据文件有三种方法，分别是通过命令行、通过代码和通过net/http/pprof包。这些工具和包会分别生成 CPU 和内存性能数据。接下来，我们就来看下这三种方法分别是如何生成性能数据文件的。

#### 通过命令行生成性能数据文件
我们可以使用go test -cpuprofile来生成性能测试数据。进入internal/apiserver/service/v1目录，执行以下命令：


```
$ go test -bench=".*" -cpuprofile cpu.profile -memprofile mem.profile
goos: linux
goarch: amd64
pkg: github.com/marmotedu/iam/internal/apiserver/service/v1
cpu: AMD EPYC Processor
BenchmarkListUser-8          280     4283077 ns/op
PASS
ok    github.com/marmotedu/iam/internal/apiserver/service/v1  1.798s
```

上面的命令会在当前目录下生成 3 个文件：
- v1.test，测试生成的二进制文件，进行性能分析时可以用来解析各种符号。
- cpu.profile，CPU 性能数据文件。
- mem.profile，内存性能数据文件。


#### 通过代码生成性能数据文件
我们还可以使用代码来生成性能数据文件，例如pprof.go文件：


```go
package main

import (
  "os"
  "runtime/pprof"
)

func main() {
  cpuOut, _ := os.Create("cpu.out")
  defer cpuOut.Close()
  pprof.StartCPUProfile(cpuOut)
  defer pprof.StopCPUProfile()

  memOut, _ := os.Create("mem.out")
  defer memOut.Close()
  defer pprof.WriteHeapProfile(memOut)

  Sum(3, 5)

}

func Sum(a, b int) int {
  return a + b
}

```

运行pprof.go文件：


```
$ go run pprof.go
```
运行pprof.go文件后，会在当前目录生成cpu.profile和mem.profile性能数据文件。



#### 通过net/http/pprof生成性能数据文件
如果要分析 HTTP Server 的性能，我们可以使用net/http/pprof包来生成性能数据文件。IAM 项目使用 Gin 框架作为 HTTP 引擎，所以 IAM 项目使用了github.com/gin-contrib/pprof包来启用 HTTP 性能分析。github.com/gin-contrib/pprof包是net/http/pprof的一个简单封装，通过封装使 pprof 的功能变成了一个 Gin 中间件，这样可以根据需要加载 pprof 中间件。


github.com/gin-contrib/pprof包中的pprof.go文件中有以下代码：


```go
func Register(r *gin.Engine, prefixOptions ...string) {
    prefix := getPrefix(prefixOptions...)

    prefixRouter := r.Group(prefix)
    {
        ...
        prefixRouter.GET("/profile", pprofHandler(pprof.Profile))
        ...
    }
}

func pprofHandler(h http.HandlerFunc) gin.HandlerFunc {
    handler := http.HandlerFunc(h)
    return func(c *gin.Context) {
        handler.ServeHTTP(c.Writer, c.Request)
    }
}
```

通过上面的代码，你可以看到github.com/gin-contrib/pprof包将net/http/pprof.Profile转换成了gin.HandlerFunc，也就是 Gin 中间件。

要开启 HTTP 性能分析，只需要在代码中注册 pprof 提供的 HTTP Handler 即可（位于internal/pkg/server/genericapiserver.go文件中）：


```go
// install pprof handler
if s.enableProfiling {
    pprof.Register(s.Engine)
}
```
上面的代码根据配置--feature.profiling来判断是否开启 HTTP 性能分析功能。我们开启完 HTTP 性能分析，启动 HTTP 服务 iam-apiserver 后，即可访问http:// x.x.x.x:8080/debug/pprof（x.x.x.x是 Linux 服务器的地址）来查看 profiles 信息。profiles 信息如下图所示：


![img](https://static001.geekbang.org/resource/image/6a/6b/6a5fc33b87b6322162c39e9209b6396b.png?wh=1520x1170)
我们可以通过以下命令，来获取 CPU 性能数据文件：

```
$ curl http://127.0.0.1:8080/debug/pprof/profile -o cpu.profile
```
执行完上面的命令后，需要等待 30s，pprof 会采集这 30s 内的性能数据，我们需要在这段时间内向服务器连续发送多次请求，请求的频度可以根据我们的场景来决定。30s 之后，/debug/pprof/profile接口会生成 CPU profile 文件，被 curl 命令保存在当前目录下的 cpu.profile 文件中。


同样的，我们可以执行以下命令来生成内存性能数据文件：


```
$ curl http://127.0.0.1:8080/debug/pprof/heap -o mem.profile
```

上面的命令会自动下载 heap 文件，并被 curl 命令保存在当前目录下的 mem.profile 文件中。


我们可以使用go tool pprof [mem|cpu].profile命令来分析 HTTP 接口的 CPU 和内存性能。我们也可以使用命令go tool pprof http://127.0.0.1:8080/debug/pprof/profile，或者go tool pprof http://127.0.0.1:8080/debug/pprof/heap，来直接进入 pprof 工具的交互 Shell 中。go tool pprof会首先下载并保存 CPU 和内存性能数据文件，然后再分析这些文件。


通过上面的三种方法，我们生成了 cpu.profile 和 mem.profile，接下来我们就可以使用go tool pprof来分析这两个性能数据文件，进而分析我们程序的 CPU 和内存性能了。下面，我来具体讲讲性能分析的过程。


#### 性能分析
使用go tool pprof，来对性能进行分析的流程，你可以参考下图：


![img](https://static001.geekbang.org/resource/image/d4/da/d41d03c41283ea00308682a9yy0400da.jpg?wh=1920x665)

接下来，我先给你介绍下 pprof 工具，再介绍下如何生成性能数据，最后再分别介绍下 CPU 和内存性能分析方法。



#### pprof 工具介绍
pprof是一个 Go 程序性能分析工具，用它可以访问并分析性能数据文件，它还会根据我们的要求，提供高可读性的输出信息。Go 在语言层面上集成了 profile 采样工具，只需在代码中简单地引入runtime/pprof或者net/http/pprof包，即可获取程序的 profile 文件，并通过 profile 文件来进行性能分析。


net/http/pprof基于runtime/pprof包进行封装，并在 HTTP 端口上暴露出来。



#### 生成性能数据
我们在做性能分析时，主要是对内存和 CPU 性能进行分析。为了分析内存和 CPU 的性能，我们需要先生成性能数据文件。在 IAM 源码中，也有包含性能测试的用例，下面我会借助 IAM 源码中的性能测试用例，来介绍如何分析程序的性能。


进入internal/apiserver/service/v1目录，user_test.go 文件包含了性能测试函数 BenchmarkListUser，执行以下命令来生成性能数据文件：



```
$ go test -benchtime=30s -benchmem -bench=".*" -cpuprofile cpu.profile -memprofile mem.profile
goos: linux
goarch: amd64
pkg: github.com/marmotedu/iam/internal/apiserver/service/v1
cpu: AMD EPYC Processor
BenchmarkListUser-8          175   204523677 ns/op     15331 B/op       268 allocs/op
PASS
ok    github.com/marmotedu/iam/internal/apiserver/service/v1  56.514s
```
上面的命令会在当前目录下产生cpu.profile、mem.profile性能数据文件，以及v1.test二进制文件。接下来，我们基于cpu.profile、mem.profile、v1.test文件来分析代码的 CPU 和内存性能。为了获取足够的采样数据，我们将 benchmark 时间设置为30s。


在做性能分析时，我们可以采取不同的手段来分析性能，比如分析采样图、分析火焰图，还可以使用go tool pprof交互模式，查看函数 CPU 和内存消耗数据。下面我会运用这些方法，来分析 CPU 性能和内存性能。



### CPU 性能分析
在默认情况下，Go 语言的运行时系统会以 100 Hz 的的频率对 CPU 使用情况进行采样，也就是说每秒采样 100 次，每 10 毫秒采样一次。每次采样时，会记录正在运行的函数，并统计其运行时间，从而生成 CPU 性能数据。


上面我们已经生成了 CPU 性能数据文件cpu.profile，接下来会运用上面提到的三种方法来分析该性能文件，优化性能。

#### 方法一：分析采样图
要分析性能，最直观的方式当然是看图，所以首先我们需要生成采样图，生成过程可以分为两个步骤。



第一步，确保系统安装了graphviz：

```
$ sudo yum -y install graphviz.x86_64
```
第二步，执行go tool pprof生成调用图：


```
$ go tool pprof -svg cpu.profile > cpu.svg  # svg 格式
$ go tool pprof -pdf cpu.profile > cpu.pdf # pdf 格式
$ go tool pprof -png cpu.profile > cpu.png # png 格式
```

以上命令会生成cpu.pdf、cpu.svg和cpu.png文件，文件中绘制了函数调用关系以及其他采样数据。如下图所示：


![img](https://static001.geekbang.org/resource/image/a7/0c/a737e4d5f25775150545558872aa9e0c.png?wh=438x1259)


这张图片由有向线段和矩形组成。**我们先来看有向线段的含义。**

有向线段描述了函数的调用关系，矩形包含了 CPU 采样数据。从图中，我们看到没箭头的一端调用了有箭头的一端，可以知道v1.(*userService).List函数调用了fake.(*policies).List。线段旁边的数字90ms则说明，v1.(*userService).List调用fake.(*policies).List函数，在采样周期内，一共耗用了90ms。通过函数调用关系，我们可以知道某个函数调用了哪些函数，并且调用这些函数耗时多久。

这里，我们再次解读下图中调用关系中的重要信息：


![img](https://static001.geekbang.org/resource/image/70/32/70e964bc6d8f0b28d434cce47c4e1132.png?wh=835x818)

runtime.schedule的累积采样时间（140ms）中，有 10ms 来自于runtime.goschedImpl函数的直接调用，有 70ms 来自于runtime.park_m函数的直接调用。这些数据可以说明runtime.schedule函数分别被哪些函数调用，并且调用频率有多大。也因为这个原因，函数runtime.goschedImpl对函数runtime.schedule的调用时间必定小于等于函数runtime.schedule的累积采样时间。



我们再来看下矩形里的采样数据。这些矩形基本都包含了 3 类信息：
- 函数名 / 方法名，该类信息包含了包名、结构体名、函数名 / 方法名，方便我们快速定位到函数 / 方法，例如fake(*policies)List说明是 fake 包，policies 结构体的 List 方法。
- 本地采样时间，以及它在采样总数中所占的比例。本地采样时间是指采样点落在该函数中的总时间。
- 累积采样时间，以及它在采样总数中所占的比例。累积采样时间是指采样点落在该函数，以及被它直接或者间接调用的函数中的总时间。

我们可以通过OutDir函数来解释本地采样时间和累积采样时间这两个概念。OutDir函数如下图所示：


![img](https://static001.geekbang.org/resource/image/f5/a5/f55c738c09471094a9a8498e9b73faa5.png?wh=1020x558)

整个函数的执行耗时，我们可以认为是累积采样时间，包含了白色部分的代码耗时和红色部分的函数调用耗时。白色部分的代码耗时，可以认为是本地采样时间。

通过累积采样时间，我们可以知道函数的总调用时间，累积采样时间越大，说明调用它所花费的 CPU 时间越多。但你要注意，这并不一定说明这个函数本身是有问题的，也有可能是函数所调用的函数性能有瓶颈，这时候我们应该根据函数调用关系顺藤摸瓜，去寻找这个函数直接或间接调用的函数中最耗费 CPU 时间的那些。


如果函数的本地采样时间很大，就说明这个函数自身耗时（除去调用其他函数的耗时）很大，这时候需要我们分析这个函数自身的代码，而不是这个函数直接或者间接调用函数的代码。采样图中，矩形框面积越大，说明这个函数的累积采样时间越大。那么，如果一个函数分析采样图中的矩形框面积很大，这时候我们就要认真分析了，因为很可能这个函数就有需要优化性能的地方。


#### 方法二：分析火焰图
上面介绍的采样图，其实在分析性能的时候还不太直观，这里我们可以通过生成火焰图，来更直观地查看性能瓶颈。火焰图是由 Brendan Gregg 大师发明的专门用来把采样到的堆栈轨迹（Stack Trace）转化为直观图片显示的工具，因整张图看起来像一团跳动的火焰而得名。


go tool pprof提供了-http参数，可以使我们通过浏览器浏览采样图和火焰图。执行以下命令：


```
$ go tool pprof -http="0.0.0.0:8081" v1.test cpu.profile
```

然后访问http://x.x.x.x:8081/（x.x.x.x是执行go tool pprof命令所在服务器的 IP 地址），则会在浏览器显示各类采样视图数据，如下图所示：


![img](https://static001.geekbang.org/resource/image/91/9d/91dab469d3d61dd4302d3ef5d483609d.png?wh=1920x679)


上面的 UI 页面提供了不同的采样数据视图：
- Top，类似于 linux top 的形式，从高到低排序。
- Graph，默认弹出来的就是该模式，也就是上一个图的那种带有调用关系的图。
- Flame Graph：pprof 火焰图。
- Peek：类似于 Top 也是从高到底的排序。
- Source：和交互命令式的那种一样，带有源码标注。
- Disassemble：显示所有的总量。

接下来，我们主要来分析火焰图。在 UI 界面选择 Flame Graph（VIEW -> Flame Graph），就会展示火焰图，如下图所示：


![img](https://static001.geekbang.org/resource/image/33/e9/33e427f1a2419e0420e9ef8e9ddd69e9.png?wh=1920x609)

火焰图主要有下面这几个特征：
- 每一列代表一个调用栈，每一个格子代表一个函数。
- 纵轴展示了栈的深度，按照调用关系从上到下排列。最下面的格子代表采样时，正在占用 CPU 的函数。
- 调用栈在横向会按照字母排序，并且同样的调用栈会做合并，所以一个格子的宽度越大，说明这个函数越可能是瓶颈。
- 火焰图格子的颜色是随机的暖色调，方便区分各个调用信息。

查看火焰图时，格子越宽的函数，就越可能存在性能问题，这时候，我们就可以分析该函数的代码，找出问题所在。



#### 方法三：用go tool pprof交互模式查看详细数据
我们可以执行go tool pprof命令，来查看 CPU 的性能数据文件：


```
$ go tool pprof v1.test cpu.profile
File: v1.test
Type: cpu
Time: Aug 17, 2021 at 2:17pm (CST)
Duration: 56.48s, Total samples = 440ms ( 0.78%)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof)
```

go tool pprof输出了很多信息：
- File，二进制可执行文件名称。
- Type，采样文件的类型，例如 cpu、mem 等。
- Time，生成采样文件的时间。
- Duration，程序执行时间。上面的例子中，程序总执行时间为37.43s，采样时间为42.37s。采样程序在采样时，会自动分配采样任务给多个核心，所以总采样时间可能会大于总执行时间。
- (pprof)，命令行提示，表示当前在go tool的pprof工具命令行中，go tool还包括cgo、doc、pprof、trace等多种命令。


执行go tool pprof命令后，会进入一个交互 shell。在这个交互 shell 中，我们可以执行多个命令，最常用的命令有三个，如下表所示：


![img](https://static001.geekbang.org/resource/image/d1/98/d10a2c6cbfa4e35fc4efc9a3760d1b98.jpg?wh=1920x1196)
我们在交互界面中执行top命令，可以查看性能样本数据：


```
(pprof) top
Showing nodes accounting for 350ms, 79.55% of 440ms total
Showing top 10 nodes out of 47
      flat  flat%   sum%        cum   cum%
     110ms 25.00% 25.00%      110ms 25.00%  runtime.futex
      70ms 15.91% 40.91%       90ms 20.45%  github.com/marmotedu/iam/internal/apiserver/store/fake.(*policies).List
      40ms  9.09% 50.00%       40ms  9.09%  runtime.epollwait
      40ms  9.09% 59.09%      180ms 40.91%  runtime.findrunnable
      30ms  6.82% 65.91%       30ms  6.82%  runtime.write1
      20ms  4.55% 70.45%       30ms  6.82%  runtime.notesleep
      10ms  2.27% 72.73%      100ms 22.73%  github.com/marmotedu/iam/internal/apiserver/service/v1.(*userService).List
      10ms  2.27% 75.00%       10ms  2.27%  runtime.checkTimers
      10ms  2.27% 77.27%       10ms  2.27%  runtime.doaddtimer
      10ms  2.27% 79.55%       10ms  2.27%  runtime.mallocgc
```

上面的输出中，每一行表示一个函数的信息。pprof 程序中最重要的命令就是 topN，这个命令用来显示 profile 文件中最靠前的 N 个样本（sample），top 命令会输出多行信息，每一行代表一个函数的采样数据，默认按flat%排序。输出中，各列含义如下：

- flat：采样点落在该函数中的总时间。
- flat%：采样点落在该函数中时间的百分比。
- sum%：前面所有行的 flat% 的累加值，也就是上一项的累积百分比。
- cum：采样点落在该函数中的，以及被它调用的函数中的总时间。
- cum%：采样点落在该函数中的，以及被它调用的函数中的总次数百分比。
- 函数名。


上面这些信息，可以告诉我们函数执行的时间和耗时排名，我们可以根据这些信息，来判断哪些函数可能有性能问题，或者哪些函数的性能可以进一步优化。这里想提示下，如果执行的是go tool pprof mem.profile，那么上面的各字段意义是类似的，只不过这次不是时间而是内存分配大小（字节）。


执行top命令默认是按flat%排序的，在做性能分析时，我们需要先按照cum来排序，通过cum，我们可以直观地看到哪个函数总耗时最多，然后再参考该函数的本地采样时间和调用关系，来判断是该函数性能耗时多，还是它调用的函数耗时多。执行top -cum输出如下：


```
(pprof) top20 -cum
Showing nodes accounting for 280ms, 63.64% of 440ms total
Showing top 20 nodes out of 47
      flat  flat%   sum%        cum   cum%
         0     0%     0%      320ms 72.73%  runtime.mcall
         0     0%     0%      320ms 72.73%  runtime.park_m
         0     0%     0%      280ms 63.64%  runtime.schedule
      40ms  9.09%  9.09%      180ms 40.91%  runtime.findrunnable
     110ms 25.00% 34.09%      110ms 25.00%  runtime.futex
      10ms  2.27% 36.36%      100ms 22.73%  github.com/marmotedu/iam/internal/apiserver/service/v1.(*userService).List
         0     0% 36.36%      100ms 22.73%  github.com/marmotedu/iam/internal/apiserver/service/v1.BenchmarkListUser
         0     0% 36.36%      100ms 22.73%  runtime.futexwakeup
         0     0% 36.36%      100ms 22.73%  runtime.notewakeup
         0     0% 36.36%      100ms 22.73%  runtime.resetspinning
         0     0% 36.36%      100ms 22.73%  runtime.startm
         0     0% 36.36%      100ms 22.73%  runtime.wakep
         0     0% 36.36%      100ms 22.73%  testing.(*B).launch
         0     0% 36.36%      100ms 22.73%  testing.(*B).runN
      70ms 15.91% 52.27%       90ms 20.45%  github.com/marmotedu/iam/internal/apiserver/store/fake.(*policies).List
      10ms  2.27% 54.55%       50ms 11.36%  runtime.netpoll
      40ms  9.09% 63.64%       40ms  9.09%  runtime.epollwait
         0     0% 63.64%       40ms  9.09%  runtime.modtimer
         0     0% 63.64%       40ms  9.09%  runtime.resetForSleep
         0     0% 63.64%       40ms  9.09%  runtime.resettimer (inline)

```

从上面的输出可知，v1.BenchmarkListUser、testing.(*B).launch、testing.(*B).runN的本地采样时间占比分别为0%、0%、0%，但是三者的累积采样时间占比却比较高，分别为22.73%、22.73%、22.73%。



本地采样时间占比很小，但是累积采样时间占比很高，说明这 3 个函数耗时多是因为调用了其他函数，它们自身几乎没有耗时。根据采样图，我们可以看到函数的调用关系，具体如下图所示：


![img](https://static001.geekbang.org/resource/image/b0/4c/b0b7624a7922cea801de63b865f6ed4c.jpg?wh=1920x437)

从采样图中，可以知道最终v1.BenchmarkListUser调用了v1.(*userService).List函数。v1.(*userService).List函数是我们编写的函数，该函数的本地采样时间占比为2.27%，但是累积采样时间占比却高达22.73%，说明v1.(*userService).List调用其他函数耗用了大量的 CPU 时间。

再观察采样图，可以看出v1.(*userService).List耗时久是因为调用了fake.(*policies).List函数。我们也可以通过list命令查看函数内部的耗时情况：


![img](https://static001.geekbang.org/resource/image/81/23/81765c7e56cb45d03a0a61de5835d823.png?wh=1920x576)

list userService.*List会列出userService结构体List方法内部代码的耗时情况，从上图也可以看到，u.store.Policies().List耗时最多。fake.(*policies).List的本地采样时间占比为15.91%，说明fake.(*policies).List函数本身可能存在瓶颈。走读fake.(*policies).List代码可知，该函数是查询数据库的函数，查询数据库会有延时。继续查看v1.(*userService).List代码，我们可以发现以下调用逻辑：


```go
func (u *userService) ListWithBadPerformance(ctx context.Context, opts metav1.ListOptions) (*v1.UserList, error) {
    ...
    for _, user := range users.Items {
        policies, err := u.store.Policies().List(ctx, user.Name, metav1.ListOptions{})
        ...
        })
    }
    ...
}
```
我们在for循环中，串行调用了fake.(*policies).List函数，每一次循环都会调用有延时的fake.(*policies).List函数。多次调用，v1.(*userService).List函数的耗时自然会累加起来。


现在问题找到了，那我们怎么优化呢？你可以利用 CPU 多核特性，开启多个 goroutine，这样我们的查询耗时就不是串行累加的，而是取决于最慢一次的fake.(*policies).List调用。优化后的v1.(*userService).List函数代码见internal/apiserver/service/v1/user.go。用同样的性能测试用例，测试优化后的函数，结果如下：



```
$ go test -benchtime=30s -benchmem -bench=".*" -cpuprofile cpu.profile -memprofile mem.profile
goos: linux
goarch: amd64
pkg: github.com/marmotedu/iam/internal/apiserver/service/v1
cpu: AMD EPYC Processor
BenchmarkListUser-8         8330     4271131 ns/op     26390 B/op       484 allocs/op
PASS
ok    github.com/marmotedu/iam/internal/apiserver/service/v1  36.179s
```
上面的代码中，ns/op 为4271131 ns/op，可以看到和第一次的测试结果204523677 ns/op相比，性能提升了97.91%。这里注意下，为了方便你对照，我将优化前的v1.(*userService).List函数重命名为v1.(*userService).ListWithBadPerformance。


#### 内存性能分析
Go 语言运行时，系统会对程序运行期间的所有堆内存分配进行记录。不管在采样的哪一时刻，也不管堆内存已用字节数是否有增长，只要有字节被分配且数量足够，分析器就会对它进行采样。



内存性能分析方法和 CPU 性能分析方法比较类似，这里就不再重复介绍了。你可以借助前面生成的内存性能数据文件mem.profile自行分析。接下来，给你展示下内存优化前和优化后的效果。在v1.(*userService).List函数（位于internal/apiserver/service/v1/user.go文件中）中，有以下代码：


```go
infos := make([]*v1.User, 0)
for _, user := range users.Items {
    info, _ := m.Load(user.ID)
    infos = append(infos, info.(*v1.User))
}

```
此时，我们运行go test命令，测试下内存性能，作为优化后的性能数据，进行对比：

```
$ go test -benchmem -bench=".*" -cpuprofile cpu.profile -memprofile mem.profile
goos: linux
goarch: amd64
pkg: github.com/marmotedu/iam/internal/apiserver/service/v1
cpu: AMD EPYC Processor
BenchmarkListUser-8          278     4284660 ns/op     27101 B/op       491 allocs/op
PASS
ok    github.com/marmotedu/iam/internal/apiserver/service/v1  1.779s
```
B/op和allocs/op分别为27101 B/op和491 allocs/op。我们通过分析代码，发现可以将infos := make([]*v1.User, 0)优化为infos := make([]*v1.User, 0, len(users.Items))，来减少 Go 切片的内存重新分配的次数。优化后的代码为：

```go
//infos := make([]*v1.User, 0)
infos := make([]*v1.User, 0, len(users.Items))
for _, user := range users.Items {
    info, _ := m.Load(user.ID)
    infos = append(infos, info.(*v1.User))
}
```
再执行go test测试下性能：


```
$ go test -benchmem -bench=".*" -cpuprofile cpu.profile -memprofile mem.profile
goos: linux
goarch: amd64
pkg: github.com/marmotedu/iam/internal/apiserver/service/v1
cpu: AMD EPYC Processor
BenchmarkListUser-8          276     4318472 ns/op     26457 B/op       484 allocs/op
PASS
ok    github.com/marmotedu/iam/internal/apiserver/service/v1  1.856s
```

优化后的B/op和allocs/op分别为26457 B/op和484 allocs/op。跟第一次的27101 B/op和491 allocs/op相比，内存分配次数更少，每次分配的内存也更少。我们可以执行go tool pprof命令，来查看 CPU 的性能数据文件：



```
$ go tool pprof v1.test mem.profile
File: v1.test
Type: alloc_space
Time: Aug 17, 2021 at 8:33pm (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof)
```
该命令会进入一个交互界面，在交互界面中执行 top 命令，可以查看性能样本数据，例如：


```
(pprof) top
Showing nodes accounting for 10347.32kB, 95.28% of 10859.34kB total
Showing top 10 nodes out of 52
      flat  flat%   sum%        cum   cum%
 3072.56kB 28.29% 28.29%  4096.64kB 37.72%  github.com/marmotedu/iam/internal/apiserver/service/v1.(*userService).List.func1
 1762.94kB 16.23% 44.53%  1762.94kB 16.23%  runtime/pprof.StartCPUProfile
 1024.52kB  9.43% 53.96%  1024.52kB  9.43%  go.uber.org/zap/buffer.NewPool.func1
 1024.08kB  9.43% 63.39%  1024.08kB  9.43%  time.Sleep
  902.59kB  8.31% 71.70%   902.59kB  8.31%  compress/flate.NewWriter
  512.20kB  4.72% 76.42%  1536.72kB 14.15%  github.com/marmotedu/iam/internal/apiserver/service/v1.(*userService).List
  512.19kB  4.72% 81.14%   512.19kB  4.72%  runtime.malg
  512.12kB  4.72% 85.85%   512.12kB  4.72%  regexp.makeOnePass
  512.09kB  4.72% 90.57%   512.09kB  4.72%  github.com/marmotedu/iam/internal/apiserver/store/fake.FakeUsers
  512.04kB  4.72% 95.28%   512.04kB  4.72%  runtime/pprof.allFrames
```

上面的内存性能数据，各字段的含义依次是：
- flat，采样点落在该函数中的总内存消耗。
- flat% ，采样点落在该函数中的百分比。
- sum% ，上一项的累积百分比。
- cum ，采样点落在该函数，以及被它调用的函数中的总内存消耗。
- cum%，采样点落在该函数，以及被它调用的函数中的总次数百分比。
- 函数名。



### 总结
在 Go 项目开发中，程序性能低下时，我们需要分析出问题所在的代码。Go 语言提供的 go tool pprof 工具可以支持我们分析代码的性能。我们可以通过两步来分析代码的性能，分别是生成性能数据文件和分析性能数据文件。Go 中可以用来生成性能数据文件的方式有三种：通过命令行生成性能数据文件、通过代码生成性能数据文件、通过 net/http/pprof 生成性能数据文件。


生成性能数据文件之后，就可以使用 go tool pprof 工具来分析性能数据文件了。我们可以分别获取到 CPU 和内存的性能数据，通过分析就可以找到性能瓶颈。有 3 种分析性能数据文件的方式，分别是分析采样图、分析火焰图和用 go tool pprof 交互模式查看详细数据。因为火焰图直观高效，所以我建议你多使用火焰图来分析性能。

## 39｜性能分析（下）：API Server性能测试和调优实战

上一讲，我们学习了如何分析 Go 代码的性能。掌握了性能分析的基本知识之后，这一讲，我们再来看下如何分析 API 接口的性能。在 API 上线之前，我们需要知道 API 的性能，以便知道 API 服务器所能承载的最大请求量、性能瓶颈，再根据业务对性能的要求，来对 API 进行性能调优或者扩缩容。通过这些，可以使 API 稳定地对外提供服务，并且让请求在合理的时间内返回。这一讲，我就介绍如何用 wrk 工具来测试 API Server 接口的性能，并给出分析方法和结果。



### API 性能测试指标
API 性能测试，往大了说其实包括 API 框架的性能和指定 API 的性能。不过，因为指定 API 的性能跟该 API 具体的实现（比如有无数据库连接，有无复杂的逻辑处理等）有关，我认为脱离了具体实现来探讨单个 API 的性能是毫无意义的，所以这一讲只探讨 API 框架的性能。


用来衡量 API 性能的指标主要有 3 个：
- 并发数（Concurrent）：并发数是指某个时间范围内，同时在使用系统的用户个数。广义上的并发数是指同时使用系统的用户个数，这些用户可能调用不同的 API；严格意义上的并发数是指同时请求同一个 API 的用户个数。这一讲我们讨论的并发数是严格意义上的并发数。
- 每秒查询数（QPS）：每秒查询数 QPS 是对一个特定的查询服务器在规定时间内所处理流量多少的衡量标准。QPS = 并发数 / 平均请求响应时间。
- 请求响应时间（TTLB）：请求响应时间指的是从客户端发出请求到得到响应的整个时间。这个过程从客户端发起的一个请求开始，到客户端收到服务器端的响应结束。在一些工具中，请求响应时间通常会被称为 TTLB（Time to last byte，意思是从发送一个请求开始，到客户端收到最后一个字节的响应为止所消费的时间）。请求响应时间的单位一般为“秒”或“毫秒”。

这三个指标中，衡量 API 性能的最主要指标是 QPS，但是在说明 QPS 时，需要指明是多少并发数下的 QPS，否则毫无意义，因为不同并发数下的 QPS 是不同的。举个例子，单用户 100 QPS 和 100 用户 100 QPS 是两个不同的概念，前者说明 API 可以在一秒内串行执行 100 个请求，而后者说明在并发数为 100 的情况下，API 可以在一秒内处理 100 个请求。当 QPS 相同时，并发数越大，说明 API 性能越好，并发处理能力越强。


在并发数设置过大时，API 同时要处理很多请求，会频繁切换上下文，而真正用于处理请求的时间变少，反而使得 QPS 会降低。并发数设置过大时，请求响应时间也会变长。API 会有一个合适的并发数，在该并发数下，API 的 QPS 可以达到最大，但该并发数不一定是最佳并发数，还要参考该并发数下的平均请求响应时间。


此外，在有些 API 接口中，也会测试 API 接口的 TPS（Transactions Per Second，每秒事务数）。一个事务是指客户端向服务器发送请求，然后服务器做出反应的过程。客户端在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数。


那么，TPS 和 QPS 有什么区别呢？如果是对一个查询接口（单场景）压测，且这个接口内部不会再去请求其他接口，那么 TPS=QPS，否则，TPS≠QPS。如果是对多个接口（混合场景）压测，假设 N 个接口都是查询接口，且这个接口内部不会再去请求其他接口，QPS=N*TPS。

### API 性能测试方法
Linux 下有很多 Web 性能测试工具，常用的有 Jmeter、AB、Webbench 和 wrk。每个工具都有自己的特点，IAM 项目使用 wrk 来对 API 进行性能测试。wrk 非常简单，安装方便，测试结果也相对专业，并且可以支持 Lua 脚本来创建更复杂的测试场景。下面，我来介绍下 wrk 的安装方法和使用方法。


#### wrk 安装方法
wrk 的安装很简单，一共可分为两步。第一步，Clone wrk repo：


```
$ git clone https://github.com/wg/wrk
```
第二步，编译并安装：


```
$ cd wrk
$ make
$ sudo cp ./wrk /usr/bin
```

#### wrk 使用简介
这里我们来看下 wrk 的使用方法。wrk 使用起来不复杂，执行wrk --help可以看到 wrk 的所有运行参数：


```
$ wrk --help
Usage: wrk <options> <url>
  Options:
    -c, --connections <N>  Connections to keep open
    -d, --duration    <T>  Duration of test
    -t, --threads     <N>  Number of threads to use

    -s, --script      <S>  Load Lua script file
    -H, --header      <H>  Add header to request
        --latency          Print latency statistics
        --timeout     <T>  Socket/request timeout
    -v, --version          Print version details

  Numeric arguments may include a SI unit (1k, 1M, 1G)
  Time arguments may include a time unit (2s, 2m, 2h)

```

常用的参数有下面这些：
- -t，线程数（线程数不要太多，是核数的 2 到 4 倍就行，多了反而会因为线程切换过多造成效率降低）。
- -c，并发数。
- -d，测试的持续时间，默认为 10s。
- -T，请求超时时间。
- -H，指定请求的 HTTP Header，有些 API 需要传入一些 Header，可通过 wrk 的 -H 参数来传入。
- –latency，打印响应时间分布。
- -s，指定 Lua 脚本，Lua 脚本可以实现更复杂的请求。


然后，我们来看一个 wrk 的测试结果，并对结果进行解析。一个简单的测试如下（确保 iam-apiserver 已经启动，并且开启了健康检查）：


```
$ wrk -t144 -c30000 -d30s -T30s --latency http://10.0.4.57:8080/healthz
Running 30s test @ http://10.0.4.57:8080/healthz
  144 threads and 30000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   508.77ms  604.01ms   9.27s    81.59%
    Req/Sec   772.48      0.94k   10.45k    86.82%
  Latency Distribution
     50%  413.35ms
     75%  948.99ms
     90%    1.33s
     99%    2.44s
  2276265 requests in 30.10s, 412.45MB read
  Socket errors: connect 1754, read 40, write 0, timeout 0
Requests/sec:  75613.16
Transfer/sec:     13.70MB
```
下面是对测试结果的解析。
- 144 threads and 30000 connections：用 144 个线程模拟 20000 个连接，分别对应 -t 和 -c 参数。
- Thread Stats 是线程统计，包括 Latency 和 Req/Sec。
  - Latency：响应时间，有平均值、标准偏差、最大值、正负一个标准差占比。
  - Req/Sec：每个线程每秒完成的请求数, 同样有平均值、标准偏差、最大值、正负一个标准差占比。
- Latency Distribution 是响应时间分布。
  - 50%：50% 的响应时间为 413.35ms。
  - 75%：75% 的响应时间为 948.99ms。
  - 90%：90% 的响应时间为 1.33s。
  - 99%：99% 的响应时间为 2.44s。

- 2276265 requests in 30.10s, 412.45MB read：30.10s 完成的总请求数（2276265）和数据读取量（412.45MB）。
- Socket errors: connect 1754, read 40, write 0, timeout 0：错误统计，会统计 connect 连接失败请求个数（1754）、读失败请求个数、写失败请求个数、超时请求个数。
- Requests/sec：QPS。
- Transfer/sec：平均每秒读取 13.70MB 数据（吞吐量）。


### API Server 性能测试实践
接下来，我们就来测试下 API Server 的性能。影响 API Server 性能的因素有很多，除了 iam-apiserver 自身的原因之外，服务器的硬件和配置、测试方法、网络环境等都会影响。为了方便你对照性能测试结果，我给出了我的测试环境配置，你可以参考下。

- 客户端硬件配置：1 核 4G。
- 客户端软件配置：干净的CentOS Linux release 8.2.2004 (Core)。
- 服务端硬件配置：2 核 8G。
- 服务端软件配置：干净的CentOS Linux release 8.2.2004 (Core)。
- 测试网络环境：腾讯云 VPC 内访问，除了性能测试程序外，没有其他资源消耗型业务程序。


测试架构如下图所示：


![img](https://static001.geekbang.org/resource/image/7d/c4/7df51487bc7b761d79247a5d547745c4.jpg?wh=2248x575)

#### 性能测试脚本介绍
在做 API Server 的性能测试时，需要先执行 wrk，生成性能测试数据。为了能够更直观地查看性能数据，我们还需要以图表的方式展示这些性能数据。这一讲，我使用 gnuplot 工具来自动化地绘制这些性能图，为此我们需要确保 Linux 服务器已经安装了 gnuplot 工具。你可以通过以下方式安装：



```
$ sudo yum -y install gnuplot
```

在这一讲的测试中，我会绘制下面这两张图，通过它们来观测和分析 API Server 的性能。
- QPS & TTLB 图：X轴为并发数（Concurrent），Y轴为每秒查询数（QPS）和请求响应时间（TTLB）。
- 成功率图：X轴为并发数（Concurrent），Y轴为请求成功率。


为了方便你测试 API 接口性能，我将性能测试和绘图逻辑封装在[scripts/wrktest.sh](https://github.com/marmotedu/iam/blob/v1.0.8/scripts/wrktest.sh)脚本中，你可以在 iam 源码根目录下执行如下命令，生成性能测试数据和性能图表：


```
$ scripts/wrktest.sh http://10.0.4.57:8080/healthz
```
上面的命令会执行性能测试，记录性能测试数据，并根据这些性能测试数据绘制出 QPS 和成功率图。接下来，我再来介绍下 wrktest.sh 性能测试脚本，并给出一个使用示例。

wrktest.sh 性能测试脚本，用来测试 API Server 的性能，记录测试的性能数据，并根据性能数据使用 gnuplot 绘制性能图。wrktest.sh 也可以对比前后两次的性能测试结果，并将对比结果通过图表展示出来。wrktest.sh 会根据 CPU 的核数自动计算出适合的 wrk 启动线程数（-t）：CPU核数 * 3。


wrktest.sh 默认会测试多个并发下的 API 性能，默认测试的并发数为200 500 1000 3000 5000 10000 15000 20000 25000 50000。你需要根据自己的服务器配置选择测试的最大并发数，我因为服务器配置不高（主要是8G内存在高并发下，很容易就耗尽），最大并发数选择了50000。如果你的服务器配置够高，可以再依次尝试下测试 100000 、200000 、500000 、1000000 并发下的 API 性能。


wrktest.sh 的使用方法如下：


```
$ scripts/wrktest.sh -h

Usage: scripts/wrktest.sh [OPTION] [diff] URL
Performance automation test script.

  URL                    HTTP request url, like: http://10.0.4.57:8080/healthz
  diff                   Compare two performance test results

OPTIONS:
  -h                     Usage information
  -n                     Performance test task name, default: apiserver
  -d                     Directory used to store performance data and gnuplot graphic, default: _output/wrk

Reprot bugs to <colin404@foxmail.com>.
```
wrktest.sh 提供的命令行参数介绍如下。
- URL：需要测试的 API 接口。
- diff：如果比较两次测试的结果，需要执行 wrktest.sh diff。
- -n：本次测试的任务名，wrktest.sh 会根据任务名命名生成的文件。
- -d：输出文件存放目录。
- -h：打印帮助信息。

下面，我来展示一个 wrktest.sh 使用示例。wrktest.sh 的主要功能有两个，分别是运行性能测试并获取结果和对比性能测试结果。下面我就分别介绍下它们的具体使用方法。


运行性能测试并获取结果执行如下命令：

```
$ scripts/wrktest.sh http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 200 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 500 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 1000 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 3000 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 5000 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 10000 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 15000 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 20000 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 25000 http://10.0.4.57:8080/healthz
Running wrk command: wrk -t3 -d300s -T30s --latency -c 50000 http://10.0.4.57:8080/healthz

Now plot according to /home/colin/_output/wrk/apiserver.dat
QPS graphic file is: /home/colin/_output/wrk/apiserver_qps_ttlb.png
Success rate graphic file is: /home/colin/_output/wrk/apiserver_successrate.pngz
```
上面的命令默认会在_output/wrk/目录下生成 3 个文件：
- apiserver.dat，wrk 性能测试结果，每列含义分别为并发数、QPS 平均响应时间、成功率。
- apiserver_qps_ttlb.png，QPS&TTLB 图。
- apiserver_successrate.png，成功率图。

这里要注意，请求 URL 中的 IP 地址应该是腾讯云 VPC 内网地址，因为通过内网访问，不仅网络延时最低，而且还最安全，所以真实的业务通常都是内网访问的。


对比性能测试结果
假设我们还有另外一次 API 性能测试，测试数据保存在 _output/wrk/http.dat 文件中。执行如下命令，对比两次测试结果：

```
$ scripts/wrktest.sh diff _output/wrk/apiserver.dat _output/wrk/http.dat
```
apiserver.dat和http.dat是两个需要对比的 Wrk 性能数据文件。上述命令默认会在_output/wrk目录下生成下面这两个文件：
- apiserver_http.qps.ttlb.diff.png，QPS & TTLB 对比图。
- apiserver_http.success_rate.diff.png，成功率对比图。


#### 关闭 Debug 配置选项
在测试之前，我们需要关闭一些 Debug 选项，以免影响性能测试。执行下面这两步操作，修改 iam-apiserver 的配置文件：

- 将server.mode设置为 release，server.middlewares去掉 dump、logger 中间件。
- 将log.level设置为 info，log.output-paths去掉 stdout。


因为我们要在执行压力测试时分析程序的性能，所以需要设置feature.profiling为 true，以开启性能分析。修改完之后，重新启动 iam-apiserver。


#### 使用 wrktest.sh 测试 IAM API 接口性能
关闭 Debug 配置选项之后，就可以执行wrktest.sh命令测试 API 性能了（默认测试的并发数为200 500 1000 3000 5000 10000 15000 20000 25000 50000）:


```
$ scripts/wrktest.sh http://10.0.4.57:8080/healthz
```
生成的 QPS & TTLB 图和成功率图分别如下图所示：


![img](https://static001.geekbang.org/resource/image/0a/0f/0aca3648e72974c5a32c2fbcfea8670f.png?wh=640x480)

上图中，X轴为并发数（Concurrent），Y轴为每秒查询数（QPS）和请求响应时间（TTLB）。


![img](https://static001.geekbang.org/resource/image/a2/5d/a2d7866536ee96327a10614dd332475d.png?wh=640x480)

上图中，X轴为并发数（Concurrent），Y轴为请求成功率。通过上面两张图，你可以看到，API Server 在并发数为200时，QPS 最大；并发数为500，平均响应时间为56.33ms，成功率为 100.00% 。在并发数达到1000时，成功率开始下降。一些详细数据从图里看不到，你可以直接查看apiserver.dat文件，里面记录了每个并发下具体的 QPS、TTLB 和成功率数据。



现在我们有了 API Server 的性能数据，那么该 API Server 的 QPS 处于什么水平呢？一方面，你可以根据自己的业务需要来对比；另一方面，可以和性能更好的 Web 框架进行对比，总之需要有个参照。这里用 net/http 构建最简单的 HTTP 服务器，使用相同的测试工具和测试服务器，测试性能并作对比。HTTP 服务源码为（位于文件tools/httptest/main.go中）：



```go
package main

import (
  "fmt"
  "log"
  "net/http"
)

func main() {
  http.HandleFunc("/healthz", func(w http.ResponseWriter, r *http.Request) {
    message := `{"status":"ok"}`
    fmt.Fprint(w, message)
  })

  addr := ":6667"
  fmt.Printf("Serving http service on %s\n", addr)
  log.Fatal(http.ListenAndServe(addr, nil))
}

```

我们将上述 HTTP 服务的请求路径设置为/healthz，并且返回{"status":"ok"}，跟 API Server 的接口返回数据完全一样。通过这种方式，你可以排除因为返回数据大小不同而造成的性能差异。可以看到，该 HTTP 服务器很简单，只是利用net/http包最原生的功能，在 Go 中几乎所有的 Web 框架都是基于net/http包封装的。既然是封装，肯定比不上原生的性能，所以我们要把它跟用net/http直接启动的 HTTP 服务接口的性能进行对比，来衡量我们的 API Server 性能。


我们需要执行相同的 wrk 测试，并将结果跟 API Server 的测试结果进行对比，将对比结果绘制成对比图。具体对比过程可以分为 3 步。


第一步，启动 HTTP 服务。在 iam 源码根目录下执行如下命令：


```
$ go run tools/httptest/main.go
```
第二步，执行wrktest.sh脚本，测试该 HTTP 服务的性能：

```
$ scripts/wrktest.sh -n http http://10.0.4.57:6667/healthz
```
上述命令会生成 _output/wrk/http.dat 文件。第三步，对比两次性能测试数据：

```
$ scripts/wrktest.sh diff _output/wrk/apiserver.dat _output/wrk/http.dat
```
生成的两张对比图表，如下所示：


![img](https://static001.geekbang.org/resource/image/51/f4/51eacf20d190080bf8b42e2f43yy00f4.png?wh=640x480)



![img](https://static001.geekbang.org/resource/image/e1/99/e1cc646da40036a44e5300ed2bef8999.png?wh=640x480)

通过上面两张对比图，我们可以看出，API Server 在 QPS、响应时间和成功率上都不如原生的 HTTP Server，特别是 QPS，最大 QPS 只有原生 HTTP Server 最大 QPS 的13.68%，性能需要调优。

### API Server 性能分析
上面，我们测试了 API 接口的性能，如果性能不合预期，我们还需要分析性能数据，并优化性能。


在分析前我们需要对 API Server 加压，在加压的情况下，API 接口的性能才更可能暴露出来，所以继续执行如下命令：


```
$ scripts/wrktest.sh http://10.0.4.57:8080/healthz
```

在上述命令执行压力测试期间，可以打开另外一个 Linux 终端，使用go tool pprof工具分析 HTTP 的 profile 文件：


```
$ go tool pprof http://10.0.4.57:8080/debug/pprof/profile
```
执行完go tool pprof后，因为需要采集性能数据，所以该命令会阻塞 30s。在 pprof 交互 shell 中，执行top -cum查看累积采样时间，我们执行top30 -cum，多观察一些函数：


```
(pprof) top20 -cum
Showing nodes accounting for 32.12s, 39.62% of 81.07s total
Dropped 473 nodes (cum <= 0.41s)
Showing top 20 nodes out of 167
(pprof) top30 -cum
Showing nodes accounting for 11.82s, 20.32% of 58.16s total
Dropped 632 nodes (cum <= 0.29s)
Showing top 30 nodes out of 239
      flat  flat%   sum%        cum   cum%
     0.10s  0.17%  0.17%     51.59s 88.70%  net/http.(*conn).serve
     0.01s 0.017%  0.19%     42.86s 73.69%  net/http.serverHandler.ServeHTTP
     0.04s 0.069%  0.26%     42.83s 73.64%  github.com/gin-gonic/gin.(*Engine).ServeHTTP
     0.01s 0.017%  0.28%     42.67s 73.37%  github.com/gin-gonic/gin.(*Engine).handleHTTPRequest
     0.08s  0.14%  0.41%     42.59s 73.23%  github.com/gin-gonic/gin.(*Context).Next (inline)
     0.03s 0.052%  0.46%     42.58s 73.21%  .../internal/pkg/middleware.RequestID.func1
         0     0%  0.46%     41.02s 70.53%  .../internal/pkg/middleware.Context.func1
     0.01s 0.017%  0.48%     40.97s 70.44%  github.com/gin-gonic/gin.CustomRecoveryWithWriter.func1
     0.03s 0.052%  0.53%     40.95s 70.41%  .../internal/pkg/middleware.LoggerWithConfig.func1
     0.01s 0.017%  0.55%     33.46s 57.53%  .../internal/pkg/middleware.NoCache
     0.08s  0.14%  0.69%     32.58s 56.02%  github.com/tpkeeper/gin-dump.DumpWithOptions.func1
     0.03s 0.052%  0.74%     24.73s 42.52%  github.com/tpkeeper/gin-dump.FormatToBeautifulJson
     0.02s 0.034%  0.77%     22.73s 39.08%  github.com/tpkeeper/gin-dump.BeautifyJsonBytes
     0.08s  0.14%  0.91%     16.39s 28.18%  github.com/tpkeeper/gin-dump.format
     0.21s  0.36%  1.27%     16.38s 28.16%  github.com/tpkeeper/gin-dump.formatMap
     3.75s  6.45%  7.72%     13.71s 23.57%  runtime.mallocgc
     ...
```
因为top30内容过多，这里只粘贴了耗时最多的一些关联函数。从上面的列表中，可以看到有 ServeHTTP 类的函数，这些函数是 gin/http 自带的函数，我们无需对此进行优化。还有这样一些函数：

```
.../gin.(*Context).Next (inline)
.../internal/pkg/middleware.RequestID.func1
.../internal/pkg/middleware.Context.func1
github.com/gin-gonic/gin.CustomRecoveryWithWriter.func1
.../internal/pkg/middleware.LoggerWithConfig.func1
.../internal/pkg/middleware.NoCache
github.com/tpkeeper/gin-dump.DumpWithOptions.func1

```
可以看到，middleware.RequestID.func1、middleware.Context.func1、gin.CustomRecoveryWithWriter.func1、middleware.LoggerWithConfig.func1等，这些耗时较久的函数都是我们加载的 Gin 中间件。这些中间件消耗了大量的 CPU 时间，所以我们可以选择性加载这些中间件，删除一些不需要的中间件，来优化 API Server 的性能。

假如我们暂时不需要这些中间件，也可以通过配置 iam-apiserver 的配置文件，将server.middlewares设置为空或者注释掉，然后重启 iam-apiserver。重启后，再次执行wrktest.sh测试性能，并跟原生的 HTTP Server 性能进行对比，对比结果如下面 2 张图所示：

![img](https://static001.geekbang.org/resource/image/7b/c2/7bc94be0d44a5ac54cd0e199d2612ec2.png?wh=640x480)

![img](https://static001.geekbang.org/resource/image/88/a7/88e9fdfe7ba14061e979d0195b45cca7.png?wh=640x480)

可以看到，删除无用的 Gin 中间件后，API Server 的性能有了很大的提升，并发数为200时性能最好，此时 QPS 为47812，响应时间为4.33``ms，成功率为100.00``%。在并发数为50000的时候，其 QPS 是原生 HTTP Server 的75.02%。

### API 接口性能参考
不同团队对 API 接口的性能要求不同，同一团队对每个 API 接口的性能要求也不同，所以并没有一个统一的数值标准来衡量 API 接口的性能，但可以肯定的是，性能越高越好。我根据自己的研发经验，在这里给出一个参考值（并发数可根据需要选择），如下表所示：

![img](https://static001.geekbang.org/resource/image/58/7c/581fc922afedaf36379c5a5d723ebd7c.jpg?wh=2248x585)

### API Server 性能测试注意事项
在进行 API Server 性能测试时，要考虑到 API Server 的性能影响因素。影响 API Server 性能的因素很多，大致可以分为两类，分别是 Web 框架的性能和 API 接口的性能。另外，在做性能测试时，还需要确保测试环境是一致的，最好是一个干净的测试环境。
### Web 框架性能
Web 框架的性能至关重要，因为它会影响我们的每一个 API 接口的性能。在设计阶段，我们会确定所使用的 Web 框架，这时候我们需要对 Web 框架有个初步的测试，确保我们选择的 Web 框架在性能和稳定性上都足够优秀。当整个 Go 后端服务开发完成之后，在上线之前，我们还需要对 Web 框架再次进行测试，确保按照我们最终的使用方式，Web 框架仍然能够保持优秀的性能和稳定性。

我们通常会通过 API 接口来测试 Web 框架的性能，例如健康检查接口/healthz。我们需要保证该 API 接口足够简单，API 接口里面不应该掺杂任何逻辑，只需要象征性地返回一个很小的返回内容即可。比如，这一讲中我们通过/healthz接口来测试 Web 框架的性能：

```go
s.GET("/healthz", func(c *gin.Context) {
    core.WriteResponse(c, nil, map[string]string{"status": "ok"})
})
```
接口中只调用了core.WriteResponse函数，返回了{"status":"ok"}。这里使用core.WriteResponse函数返回请求数据，而不是直接返回ok字符串，这样做是为了保持 API 接口返回格式统一。
### API 接口性能

除了测试 Web 框架的性能，我们还可能需要测试某些重要的 API 接口，甚至所有 API 接口的性能。为了测试 API 接口在真实场景下的接口性能，我们会使用 wrk 这类 HTTP 压力测试工具，来模拟多个 API 请求，进而分析 API 的性能。

因为会模拟大量的请求，这时候测试写类接口，例如Create、Update、Delete等会存在一些问题，比如可能在数据库中插入了很多数据，导致磁盘空间被写满或者数据库被压爆。所以，针对写类接口，我们可以借助单元测试，来测试其性能。根据我的开发经验，写类接口通常不会有性能问题，反而读类接口更可能遇到性能问题。针对读类接口，我们可以使用 wrk 这类 HTTP 压力测试工具来进行测试。


### 测试环境
在做性能 / 压力测试时，为了不影响生产环境，要确保在测试环境进行压测，并且测试环境的网络不能影响到生产环境的网络。另外，为了更好地进行性能对比和分析，也要保证我们的测试方法和测试环境是一致的。这就要求我们最好将性能测试自动化，并且每次在同一个测试环境进行测试。
### 总结
在项目上线前，我们需要对 API 接口进行性能测试。通常 API 接口的性能延时要小于 500ms ，如果大于这个值，需要考虑优化性能。在进行性能测试时，需要确保每次测试都有一个一致的测试环境，这样不同测试之间的数据才具有可对比性。这一讲中，我推荐了一个比较优秀的性能测试工具 wrk ，我们可以编写 shell 脚本，将 wrk 的性能测试数据自动绘制成图，方便我们查看、对比性能。

总结在项目上线前，我们需要对 API 接口进行性能测试。通常 API 接口的性能延时要小于 500ms ，如果大于这个值，需要考虑优化性能。在进行性能测试时，需要确保每次测试都有一个一致的测试环境，这样不同测试之间的数据才具有可对比性。这一讲中，我推荐了一个比较优秀的性能测试工具 wrk ，我们可以编写 shell 脚本，将 wrk 的性能测试数据自动绘制成图，方便我们查看、对比性能。





































































































































































































































































































































































































































































































































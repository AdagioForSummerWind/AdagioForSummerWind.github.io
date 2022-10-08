# Iam_extra_meal

## 特别放送 | 给你一份清晰、可直接套用的Go编码规范
我们在上一讲学习了“写出优雅 Go 项目的方法论”，那一讲内容很丰富，是我多年 Go 项目开发的经验沉淀，需要你多花一些时间好好消化吸收。吃完大餐之后，咱们今天来一期特别放送，就是上一讲我提到过的编码规范。这一讲里，为了帮你节省时间和精力，我会给你一份清晰、可直接套用的 Go 编码规范，帮助你编写一个高质量的 Go 应用。这份规范，是我参考了 Go 官方提供的编码规范，以及 Go 社区沉淀的一些比较合理的规范之后，加入自己的理解总结出的，它比很多公司内部的规范更全面，你掌握了，以后在面试大厂的时候，或者在大厂里写代码的时候，都会让人高看你一眼，觉得你 code 很专业。这份编码规范中包含代码风格、命名规范、注释规范、类型、控制结构、函数、GOPATH 设置规范、依赖管理和最佳实践九类规范。如果你觉得这些规范内容太多了，看完一遍也记不住，这完全没关系。你可以多看几遍，也可以在用到时把它翻出来，在实际应用中掌握。这篇特别放送的内容，更多是作为写代码时候的一个参考手册。

### 1. 代码风格
#### 1.1 代码格式
- 代码都必须用 gofmt 进行格式化。
- 运算符和操作数之间要留空格。
- 建议一行代码不超过 120 个字符，超过部分，请采用合适的换行方式换行。
- 但也有些例外场景，例如 import 行、工具自动生成的代码、带 tag 的 struct 字段。
- 文件长度不能超过 800 行。
- 函数长度不能超过 80 行。
- import 规范
  - 代码都必须用 goimports 进行格式化（建议将代码 Go 代码编辑器设置为：保存时运行 goimports）。
    - 不要使用相对路径引入包，例如 import …/util/net 。
    - 包名称与导入路径的最后一个目录名不匹配时，或者多个相同包名冲突时，则必须使用导入别名。

```go
// bad
  "github.com/dgrijalva/jwt-go/v4"

  //good
  jwt "github.com/dgrijalva/jwt-go/v4"
```
  - 导入的包建议进行分组，匿名包的引用使用一个新的分组，并对匿名包引用进行说明。 

```go
  import (
    // go 标准包
    "fmt"

    // 第三方包
      "github.com/jinzhu/gorm"
      "github.com/spf13/cobra"
      "github.com/spf13/viper"

    // 匿名包单独分组，并对匿名包引用进行说明
      // import mysql driver
      _ "github.com/jinzhu/gorm/dialects/mysql"

    // 内部包
      v1 "github.com/marmotedu/api/apiserver/v1"
      metav1 "github.com/marmotedu/apimachinery/pkg/meta/v1"
      "github.com/marmotedu/iam/pkg/cli/genericclioptions"
  )

```

#### 1.2 声明、初始化和定义
当函数中需要使用到多个变量时，可以在函数开始处使用 var 声明。在函数外部声明必须使用 var ，不要采用 := ，容易踩到变量的作用域的问题。

```go
var (
  Width  int
  Height int
)
```
在初始化结构引用时，请使用 &T{}代替 new(T)，以使其与结构体初始化一致。

```go
// bad
sptr := new(T)
sptr.Name = "bar"

// good
sptr := &T{Name: "bar"}
```
struct 声明和初始化格式采用多行，定义如下。

```go
type User struct{
    Username  string
    Email     string
}

user := User{
  Username: "colin",
  Email: "colin404@foxmail.com",
}
```
相似的声明放在一组，同样适用于常量、变量和类型声明。

```go
// bad
import "a"
import "b"

// good
import (
  "a"
  "b"
)
```
尽可能指定容器容量，以便为容器预先分配内存，例如：

```go
v := make(map[int]string, 4)
v := make([]string, 0, 4)
```
在顶层，使用标准 var 关键字。请勿指定类型，除非它与表达式的类型不同。

```go
// bad
var _s string = F()

func F() string { return "A" }

// good
var _s = F()
// 由于 F 已经明确了返回一个字符串类型，因此我们没有必要显式指定_s 的类型
// 还是那种类型

func F() string { return "A" }
```
对于未导出的顶层常量和变量，使用 _ 作为前缀。

```go
// bad
const (
  defaultHost = "127.0.0.1"
  defaultPort = 8080
)

// good
const (
  _defaultHost = "127.0.0.1"
  _defaultPort = 8080
)
```
嵌入式类型（例如 mutex）应位于结构体内的字段列表的顶部，并且必须有一个空行将嵌入式字段与常规字段分隔开。

```go
// bad
type Client struct {
  version int
  http.Client
}

// good
type Client struct {
  http.Client

  version int
}

```
#### 1.3 错误处理
error作为函数的值返回，必须对error进行处理，或将返回值赋值给明确忽略。对于defer xx.Close()可以不用显式处理。

```go
func load() error {
  // normal code
}

// bad
load()

// good
 _ = load()
```
尽早进行错误处理，并尽早返回，减少嵌套。

```go
// bad
if err != nil {
  // error code
} else {
  // normal code
}

// good
if err != nil {
  // error handling
  return err
}
// normal code
```
如果需要在 if 之外使用函数调用的结果，则应采用下面的方式。

```go
// bad
if v, err := foo(); err != nil {
  // error handling
}

// good
v, err := foo()
if err != nil {
  // error handling
}
```
错误要单独判断，不与其他逻辑组合判断。

```go
// bad
v, err := foo()
if err != nil || v  == nil {
  // error handling
  return err
}

// good
v, err := foo()
if err != nil {
  // error handling
  return err
}

if v == nil {
  // error handling
  return errors.New("invalid value v")
}
```
如果返回值需要初始化，则采用下面的方式。

```go
v, err := f()
if err != nil {
    // error handling
    return // or continue.
}
// use v

```

错误描述建议
- 告诉用户他们可以做什么，而不是告诉他们不能做什么。
- 当声明一个需求时，用 must 而不是 should。例如，must be greater than 0、must match regex ‘[a-z]+’。
- 当声明一个格式不对时，用 must not。例如，must not contain。
- 当声明一个动作时用 may not。例如，may not be specified when otherField is empty、only name may be specified。
- 引用文字字符串值时，请在单引号中指示文字。例如，ust not contain ‘…’。
- 当引用另一个字段名称时，请在反引号中指定该名称。例如，must be greater than request。
- 指定不等时，请使用单词而不是符号。例如，must be less than 256、must be greater than or equal to 0 (不要用 larger than、bigger than、more than、higher than)。
- 指定数字范围时，请尽可能使用包含范围。
- 建议 Go 1.13 以上，error 生成方式为 fmt.Errorf("module xxx: %w", err)。
- 错误描述用小写字母开头，结尾不要加标点符号，例如：

```go
  // bad
  errors.New("Redis connection failed")
  errors.New("redis connection failed.")

  // good
  errors.New("redis connection failed")
```

#### 1.4 panic 处理
- 在业务逻辑处理中禁止使用 panic。
- 在 main 包中，只有当程序完全不可运行时使用 panic，例如无法打开文件、无法连接数据库导致程序无法正常运行。
- 在 main 包中，使用 log.Fatal 来记录错误，这样就可以由 log 来结束程序，或者将 panic 抛出的异常记录到日志文件中，方便排查问题。
- 可导出的接口一定不能有 panic。
- 包内建议采用 error 而不是 panic 来传递错误。

#### 1.5 单元测试
- 单元测试文件名命名规范为 example_test.go。
- 每个重要的可导出函数都要编写测试用例。
- 因为单元测试文件内的函数都是不对外的，所以可导出的结构体、函数等可以不带注释。
- 如果存在 func (b *Bar) Foo ，单测函数可以为 func TestBar_Foo。

#### 1.6 类型断言失败处理
type assertion 的单个返回值针对不正确的类型将产生 panic。请始终使用 “comma ok”的惯用法。

```go
// bad
t := n.(int)

// good
t, ok := n.(int)
if !ok {
  // error handling
}
// normal code
```
### 2. 命名规范
命名规范是代码规范中非常重要的一部分，一个统一的、短小的、精确的命名规范可以大大提高代码的可读性，也可以借此规避一些不必要的 Bug。

#### 2.1 包命名
- 包名必须和目录名一致，尽量采取有意义、简短的包名，不要和标准库冲突。
- 包名全部小写，没有大写或下划线，使用多级目录来划分层级。
- 项目名可以通过中划线来连接多个单词。
- 包名以及包所在的目录名，不要使用复数，例如，是net/url，而不是net/urls。
- 不要用 common、util、shared 或者 lib 这类宽泛的、无意义的包名。
- 包名要简单明了，例如 net、time、log。

#### 2.2 函数命名
- 函数名采用驼峰式，首字母根据访问控制决定使用大写或小写，例如：MixedCaps 或者 mixedCaps。
- 代码生成工具自动生成的代码 (如 xxxx.pb.go) 和为了对相关测试用例进行分组，而采用的下划线 (如 TestMyFunction_WhatIsBeingTested) 排除此规则。
#### 2.3 文件命名
- 文件名要简短有意义。
- **文件名应小写，并使用下划线分割单词**。

#### 2.4 结构体命名
- 采用驼峰命名方式，首字母根据访问控制决定使用大写或小写，例如 MixedCaps 或者 mixedCaps。
- 结构体名不应该是动词，应该是名词，比如 Node、NodeSpec。
- 避免使用 Data、Info 这类无意义的结构体名。
- 结构体的声明和初始化应采用多行，例如：

```go
// User 多行声明
type User struct {
    Name  string
    Email string
}

// 多行初始化
u := User{
    UserName: "colin",
    Email:    "colin404@foxmail.com",
}
```

#### 2.5 接口命名
- 接口命名的规则，基本和结构体命名规则保持一致：
  - 单个函数的接口名以 “er"”作为后缀（例如 Reader，Writer），有时候可能导致蹩脚的英文，但是没关系。
  - 两个函数的接口名以两个函数名命名，例如 ReadWriter。
  - 三个以上函数的接口名，类似于结构体名。

例如：
```go
  // Seeking to an offset before the start of the file is an error.
  // Seeking to any positive offset is legal, but the behavior of subsequent
  // I/O operations on the underlying object is implementation-dependent.
  type Seeker interface {
      Seek(offset int64, whence int) (int64, error)
  }

  // ReadWriter is the interface that groups the basic Read and Write methods.
  type ReadWriter interface {
      Reader
      Writer
  }
```

#### 2.6 变量命名
- 变量名必须遵循驼峰式，首字母根据访问控制决定使用大写或小写。
- 在相对简单（对象数量少、针对性强）的环境中，可以将一些名称由完整单词简写为单个字母，例如：
  - user 可以简写为 u；
  - userID 可以简写 uid。
- 特有名词时，需要遵循以下规则：
  - 如果变量为私有，且特有名词为首个单词，则使用小写，如 apiClient。
  - 其他情况都应当使用该名词原有的写法，如 APIClient、repoID、UserID。

下面列举了一些常见的特有名词。

```go
// A GonicMapper that contains a list of common initialisms taken from golang/lint
var LintGonicMapper = GonicMapper{
    "API":   true,
    "ASCII": true,
    "CPU":   true,
    "CSS":   true,
    "DNS":   true,
    "EOF":   true,
    "GUID":  true,
    "HTML":  true,
    "HTTP":  true,
    "HTTPS": true,
    "ID":    true,
    "IP":    true,
    "JSON":  true,
    "LHS":   true,
    "QPS":   true,
    "RAM":   true,
    "RHS":   true,
    "RPC":   true,
    "SLA":   true,
    "SMTP":  true,
    "SSH":   true,
    "TLS":   true,
    "TTL":   true,
    "UI":    true,
    "UID":   true,
    "UUID":  true,
    "URI":   true,
    "URL":   true,
    "UTF8":  true,
    "VM":    true,
    "XML":   true,
    "XSRF":  true,
    "XSS":   true,
}
```
若变量类型为 bool 类型，则名称应以 Has，Is，Can 或 Allow 开头，例如：

```go
var hasConflict bool
var isExist bool
var canManage bool
var allowGitHook bool
```

局部变量应当尽可能短小，比如使用 buf 指代 buffer，使用 i 指代 index。

代码生成工具自动生成的代码可排除此规则 (如 xxx.pb.go 里面的 Id)

#### 2.7 常量命名
常量名必须遵循驼峰式，首字母根据访问控制决定使用大写或小写。如果是枚举类型的常量，需要先创建相应类型：

```go
// Code defines an error code type.
type Code int

// Internal errors.
const (
    // ErrUnknown - 0: An unknown error occurred.
    ErrUnknown Code = iota
    // ErrFatal - 1: An fatal error occurred.
    ErrFatal
)
```

#### 2.8 Error 的命名
Error 类型应该写成 FooError 的形式。

```go
type ExitError struct {
  // ....
}
```
Error 变量写成 ErrFoo 的形式。
```go
var ErrFormat = errors.New("unknown format")
```

### 3. 注释规范
- 每个可导出的名字都要有注释，该注释对导出的变量、函数、结构体、接口等进行简要介绍。
- 全部使用单行注释，禁止使用多行注释。
- 和代码的规范一样，单行注释不要过长，禁止超过 120 字符，超过的请使用换行展示，尽量保持格式优雅。
- 注释必须是完整的句子，以需要注释的内容作为开头，句点作为结尾，格式为 // 名称 描述. 。例如：

```go
// bad
// logs the flags in the flagset.
func PrintFlags(flags *pflag.FlagSet) {
  // normal code
}

// good
// PrintFlags logs the flags in the flagset.
func PrintFlags(flags *pflag.FlagSet) {
  // normal code
}
```

所有注释掉的代码在提交 code review 前都应该被删除，否则应该说明为什么不删除，并给出后续处理建议。在多段注释之间可以使用空行分隔加以区分，如下所示：

```go
// Package superman implements methods for saving the world.
//
// Experience has shown that a small number of procedures can prove
// helpful when attempting to save the world.
package superman
```

#### 3.1 包注释
每个包都有且仅有一个包级别的注释。包注释统一用 // 进行注释，格式为 // Package 包名 包描述 ，例如：
```go
// Package genericclioptions contains flags which can be added to you command, bound, completed, and produce
// useful helper functions.
package genericclioptions
```
#### 3.2 变量 / 常量注释
每个可导出的变量 / 常量都必须有注释说明，格式为// 变量名 变量描述，例如：

```go
// ErrSigningMethod defines invalid signing method error.
var ErrSigningMethod = errors.New("Invalid signing method")
```
出现大块常量或变量定义时，可在前面注释一个总的说明，然后在每一行常量的前一行或末尾详细注释该常量的定义，例如：

```go
// Code must start with 1xxxxx.    
const (                         
    // ErrSuccess - 200: OK.          
    ErrSuccess int = iota + 100001    
                                                   
    // ErrUnknown - 500: Internal server error.    
    ErrUnknown    

    // ErrBind - 400: Error occurred while binding the request body to the struct.    
    ErrBind    
                                                  
    // ErrValidation - 400: Validation failed.    
    ErrValidation 
)
```
#### 3.3 结构体注释
- 每个需要导出的结构体或者接口都必须有注释说明，格式为 // 结构体名 结构体描述.。
- 结构体内的可导出成员变量名，如果意义不明确，必须要给出注释，放在成员变量的前一行或同一行的末尾。例如：

```go
// User represents a user restful resource. It is also used as gorm model.
type User struct {
    // Standard object's metadata.
    metav1.ObjectMeta `json:"metadata,omitempty"`

    Nickname string `json:"nickname" gorm:"column:nickname"`
    Password string `json:"password" gorm:"column:password"`
    Email    string `json:"email" gorm:"column:email"`
    Phone    string `json:"phone" gorm:"column:phone"`
    IsAdmin  int    `json:"isAdmin,omitempty" gorm:"column:isAdmin"`
}
```
#### 3.4 方法注释
每个需要导出的函数或者方法都必须有注释，格式为// 函数名 函数描述.，例如：

```go
// BeforeUpdate run before update database record.
func (p *Policy) BeforeUpdate() (err error) {
  // normal code
  return nil
}
```
#### 3.5 类型注释
每个需要导出的类型定义和类型别名都必须有注释说明，格式为 // 类型名 类型描述. ，例如：

```go
// Code defines an error code type.
type Code int
```

### 4. 类型
#### 4.1 字符串
空字符串判断。

```go
// bad
if s == "" {
    // normal code
}

// good
if len(s) == 0 {
    // normal code
}
```
[]byte/string 相等比较。

```go
// bad
var s1 []byte
var s2 []byte
...
bytes.Equal(s1, s2) == 0
bytes.Equal(s1, s2) != 0

// good
var s1 []byte
var s2 []byte
...
bytes.Compare(s1, s2) == 0
bytes.Compare(s1, s2) != 0
```

复杂字符串使用 raw 字符串避免字符转义。

```go
// bad
regexp.MustCompile("\\.")

// good
regexp.MustCompile(`\.`)

```

#### 4.2 切片
空 slice 判断。

```go
// bad
if len(slice) = 0 {
    // normal code
}

// good
if slice != nil && len(slice) == 0 {
    // normal code
}
```
上面判断同样适用于 map、channel。
声明 slice。

```go
// bad
s := []string{}
s := make([]string, 0)

// good
var s []string
```
slice 复制。

```go
// bad
var b1, b2 []byte
for i, v := range b1 {
   b2[i] = v
}
for i := range b1 {
   b2[i] = b1[i]
}

// good
copy(b2, b1)
```
slice 新增。

```go
// bad
var a, b []int
for _, v := range a {
    b = append(b, v)
}

// good
var a, b []int
b = append(b, a...)
```

#### 4.3 结构体
struct 初始化。
struct 以多行格式初始化。

```go
type user struct {
  Id   int64
  Name string
}

u1 := user{100, "Colin"}

u2 := user{
    Id:   200,
    Name: "Lex",
}
```
### 5. 控制结构
#### 5.1 if
if 接受初始化语句，约定如下方式建立局部变量。

```go
if err := loadConfig(); err != nil {
  // error handling
  return err
}
```
if 对于 bool 类型的变量，应直接进行真假判断。

```go
var isAllow bool
if isAllow {
  // normal code
}
```

#### 5.2 for
采用短声明建立局部变量。

```go
sum := 0
for i := 0; i < 10; i++ {
    sum += 1
}
```
不要在 for 循环里面使用 defer，defer 只有在函数退出时才会执行。

```go
// bad
for file := range files {
  fd, err := os.Open(file)
  if err != nil {
    return err
  }
  defer fd.Close()
  // normal code
}

// good
for file := range files {
  func() {
    fd, err := os.Open(file)
    if err != nil {
      return err
    }
    defer fd.Close()
    // normal code
  }()
}
```
#### 5.3 range
如果只需要第一项（key），就丢弃第二个。

```go
for key := range keys {
// normal code
}
```
如果只需要第二项，则把第一项置为下划线。

```go
sum := 0
for _, value := range array {
    sum += value
}
```
#### 5.4 switch
必须要有 default。

```go
switch os := runtime.GOOS; os {
    case "linux":
        fmt.Println("Linux.")
    case "darwin":
        fmt.Println("OS X.")
    default:
        fmt.Printf("%s.\n", os)
}
```
#### 5.5 goto
业务代码禁止使用 goto 。
框架或其他底层源码尽量不用。

### 6. 函数
- 传入变量和返回变量以小写字母开头。
- 函数参数个数不能超过 5 个。
- 函数分组与顺序
  - 函数应按粗略的调用顺序排序。
  - 同一文件中的函数应按接收者分组。
- 尽量采用值传递，而非指针传递。
- 传入参数是 map、slice、chan、interface ，不要传递指针。


#### 6.1 函数参数
如果函数返回相同类型的两个或三个参数，或者如果从上下文中不清楚结果的含义，使用命名返回，其他情况不建议使用命名返回，例如：

```go
func coordinate() (x, y float64, err error) {
  // normal code
}
```

传入变量和返回变量都以小写字母开头。尽量用值传递，非指针传递。参数数量均不能超过 5 个。多返回值最多返回三个，超过三个请使用 struct。


#### 6.2 defer
当存在资源创建时，应紧跟 defer 释放资源 (可以大胆使用 defer，defer 在 Go1.14 版本中，性能大幅提升，defer 的性能损耗即使在性能敏感型的业务中，也可以忽略)。

先判断是否错误，再 defer 释放资源，例如：

```go
rep, err := http.Get(url)
if err != nil {
    return err
}

defer resp.Body.Close()
```

#### 6.3 方法的接收器
- 推荐以类名第一个英文首字母的小写作为接收器的命名。
- 接收器的命名在函数超过 20 行的时候不要用单字符。
- 接收器的命名不能采用 me、this、self 这类易混淆名称。

#### 6.4 嵌套
嵌套深度不能超过 4 层。

#### 6.5 变量命名
变量声明尽量放在变量第一次使用的前面，遵循就近原则。

如果魔法数字出现超过两次，则禁止使用，改用一个常量代替，例如：

```go
// PI ...
const Prise = 3.14

func getAppleCost(n float64) float64 {
  return Prise * n
}

func getOrangeCost(n float64) float64 {
  return Prise * n
}
```
### 7.   GOPATH 设置规范
Go 1.11 之后，弱化了 GOPATH 规则，已有代码（很多库肯定是在 1.11 之前建立的）肯定符合这个规则，建议保留 GOPATH 规则，便于维护代码。建议只使用一个 GOPATH，不建议使用多个 GOPATH。如果使用多个 GOPATH，编译生效的 bin 目录是在第一个 GOPATH 下。

### 8. 依赖管理
Go 1.11 以上必须使用 Go Modules。使用 Go Modules 作为依赖管理的项目时，不建议提交 vendor 目录。使用 Go Modules 作为依赖管理的项目时，必须提交 go.sum 文件。

### 9. 最佳实践
尽量少用全局变量，而是通过参数传递，使每个函数都是“无状态”的。这样可以减少耦合，也方便分工和单元测试。在编译时验证接口的符合性，例如：

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}
var _ http.Handler = LogHandler{}
```
服务器处理请求时，应该创建一个 context，保存该请求的相关信息（如 requestID），并在函数调用链中传递。

#### 9.1 性能
string 表示的是不可变的字符串变量，对 string 的修改是比较重的操作，基本上都需要重新申请内存。所以，如果没有特殊需要，需要修改时多使用 []byte。

优先使用 strconv 而不是 fmt。

#### 9.2 注意事项
append 要小心自动分配内存，append 返回的可能是新分配的地址。

如果要直接修改 map 的 value 值，则 value 只能是指针，否则要覆盖原来的值。

map 在并发中需要加锁。

编译过程无法检查 interface{} 的转换，只能在运行时检查，小心引起 panic。

### 总结
这一讲，我向你介绍了九类常用的编码规范。但今天的最后，我要在这里提醒你一句：规范是人定的，你也可以根据需要，制定符合你项目的规范。这也是我在之前的课程里一直强调的思路。但同时我也建议你采纳这些业界沉淀下来的规范，并通过工具来确保规范的执行。

## 特别放送 | 给你一份Go项目中最常用的Makefile核心语法

在第 14 讲  里，我强调了熟练掌握 Makefile 语法的重要性，还推荐你去学习陈皓老师编写的《跟我一起写 Makefile》 (PDF 重制版)。也许你已经点开了链接，看到那么多 Makefile 语法，是不是有点被“劝退”的感觉？

其实在我看来，虽然 Makefile 有很多语法，但不是所有的语法都需要你熟练掌握，有些语法在 Go 项目中是很少用到的。要编写一个高质量的 Makefile，首先应该掌握一些核心的、最常用的语法知识。这一讲我就来具体介绍下 Go 项目中常用的 Makefile 语法和规则，帮助你快速打好最重要的基础。

Makefile 文件由三个部分组成，分别是 Makefile 规则、Makefile 语法和 Makefile 命令（这些命令可以是 Linux 命令，也可以是可执行的脚本文件）。在这一讲里，我会介绍下 Makefile 规则和 Makefile 语法里的一些核心语法知识。在介绍这些语法知识之前，我们先来看下如何使用 Makefile 脚本。

### Makefile 的使用方法
在实际使用过程中，我们一般是先编写一个 Makefile 文件，指定整个项目的编译规则，然后通过 Linux make 命令来解析该 Makefile 文件，实现项目编译、管理的自动化。默认情况下，make 命令会在当前目录下，按照 GNUmakefile、makefile、Makefile 文件的顺序查找 Makefile 文件，一旦找到，就开始读取这个文件并执行。大多数的 make 都支持“makefile”和“Makefile”这两种文件名，但我建议使用“Makefile”。因为这个文件名第一个字符大写，会很明显，容易辨别。make 也支持 -f 和 --file 参数来指定其他文件名，比如 make -f golang.mk 或者 make --file golang.mk 。

### Makefile 规则介绍
学习 Makefile，最核心的就是学习 Makefile 的规则。规则是 Makefile 中的重要概念，它一般由目标、依赖和命令组成，用来指定源文件编译的先后顺序。Makefile 之所以受欢迎，核心原因就是 Makefile 规则，因为 Makefile 规则可以自动判断是否需要重新编译某个目标，从而确保目标仅在需要时编译。

这一讲我们主要来看 Makefile 规则里的规则语法、伪目标和 order-only 依赖。

#### 规则语法
Makefile 的规则语法，主要包括 target、prerequisites 和 command，示例如下：

```
target ...: prerequisites ...
    command
    ...
    ...
```

target，可以是一个 object file（目标文件），也可以是一个执行文件，还可以是一个标签（label）。target 可使用通配符，当有多个目标时，目标之间用空格分隔。

prerequisites，代表生成该 target 所需要的依赖项。当有多个依赖项时，依赖项之间用空格分隔。

command，代表该 target 要执行的命令（可以是任意的 shell 命令）。
- 在执行 command 之前，默认会先打印出该命令，然后再输出命令的结果；如果不想打印出命令，可在各个 command 前加上@。
- command 可以为多条，也可以分行写，但每行都要以 tab 键开始。另外，如果后一条命令依赖前一条命令，则这两条命令需要写在同一行，并用分号进行分隔。
- 如果要忽略命令的出错，需要在各个 command 之前加上减号-。


**只要 targets 不存在，或 prerequisites 中有一个以上的文件比 targets 文件新，那么 command 所定义的命令就会被执行，从而产生我们需要的文件，或执行我们期望的操作。**

我们直接通过一个例子来理解下 Makefile 的规则吧。第一步，先编写一个 hello.c 文件。

```go
#include <stdio.h>
int main()
{
  printf("Hello World!\n");
  return 0;
}
```
第二步，在当前目录下，编写 Makefile 文件。

```
hello: hello.o
  gcc -o hello hello.o

hello.o: hello.c
  gcc -c hello.c

clean:
  rm hello.o
```
第三步，执行 make，产生可执行文件。

```
$ make
gcc -c hello.c
gcc -o hello hello.o
$ ls
hello  hello.c  hello.o  Makefile
```
上面的示例 Makefile 文件有两个 target，分别是 hello 和 hello.o，每个 target 都指定了构建 command。当执行 make 命令时，发现 hello、hello.o 文件不存在，就会执行 command 命令生成 target。

第四步，不更新任何文件，再次执行 make。

```
$ make
make: 'hello' is up to date.
```
当 target 存在，并且 prerequisites 都不比 target 新时，不会执行对应的 command。
第五步，更新 hello.c，并再次执行 make。

```
$ touch hello.c
$ make
gcc -c hello.c
gcc -o hello hello.o
```
当 target 存在，但 prerequisites 比 target 新时，会重新执行对应的 command。
第六步，清理编译中间文件。Makefile 一般都会有一个 clean 伪目标，用来清理编译中间产物，或者对源码目录做一些定制化的清理：

```
$ make clean
rm hello.o
```
我们可以在规则中使用通配符，make 支持三个通配符：*，? 和~，例如：
```
objects = *.o
print: *.c
    rm *.c
```
### 伪目标
接下来我们介绍下 Makefile 中的伪目标。Makefile 的管理能力基本上都是通过伪目标来实现的。

在上面的 Makefile 示例中，我们定义了一个 clean 目标，这其实是一个伪目标，也就是说我们不会为该目标生成任何文件。因为伪目标不是文件，make 无法生成它的依赖关系，也无法决定是否要执行它。通常情况下，我们需要显式地标识这个目标为伪目标。在 Makefile 中可以使用.PHONY来标识一个目标为伪目标：

```
.PHONY: clean
clean:
    rm hello.o
```
伪目标可以有依赖文件，也可以作为“默认目标”，例如：


```
.PHONY: all
all: lint test build
```
因为伪目标总是会被执行，所以其依赖总是会被决议。通过这种方式，可以达到同时执行所有依赖项的目的。

### order-only 依赖
在上面介绍的规则中，只要 prerequisites 中有任何文件发生改变，就会重新构造 target。但是有时候，我们希望只有**当 prerequisites 中的部分文件改变时，才重新构造 target。**这时，你可以通过 order-only prerequisites 实现。

order-only prerequisites 的形式如下：


```
targets : normal-prerequisites | order-only-prerequisites
    command
    ...
    ...
```
在上面的规则中，只有第一次构造 targets 时，才会使用 order-only-prerequisites。后面即使 order-only-prerequisites 发生改变，也不会重新构造 targets。只有 normal-prerequisites 中的文件发生改变时，才会重新构造 targets。这里，符号“ | ”后面的 prerequisites 就是 order-only-prerequisites。

到这里，我们就介绍了 Makefile 的规则。接下来，我们再来看下 Makefile 中的一些核心语法知识。

### Makefile 语法概览
因为 Makefile 的语法比较多，这一讲只介绍 Makefile 的核心语法，以及 IAM 项目的 Makefile 用到的语法，包括命令、变量、条件语句和函数。因为 Makefile 没有太多复杂的语法，你掌握了这些知识点之后，再在实践中多加运用，融会贯通，就可以写出非常复杂、功能强大的 Makefile 文件了。

#### 命令
Makefile 支持 Linux 命令，调用方式跟在 Linux 系统下调用命令的方式基本一致。默认情况下，make 会把正在执行的命令输出到当前屏幕上。但我们可以通过在命令前加@符号的方式，禁止 make 输出当前正在执行的命令。我们看一个例子。现在有这么一个 Makefile：

```
.PHONY: test
test:
    echo "hello world"
```
执行 make 命令：

```
$ make test
echo "hello world"
hello world
```
可以看到，make 输出了执行的命令。很多时候，我们不需要这样的提示，因为我们更想看的是命令产生的日志，而不是执行的命令。这时就可以在命令行前加@，禁止 make 输出所执行的命令：

```
.PHONY: test
test:
    @echo "hello world"
```

再次执行 make 命令：

```
$ make test
hello world
```
可以看到，make 只是执行了命令，而没有打印命令本身。这样 make 输出就清晰了很多。

这里，我建议在命令前都加@符号，禁止打印命令本身，以保证你的 Makefile 输出易于阅读的、有用的信息。

默认情况下，每条命令执行完 make 就会检查其返回码。如果返回成功（返回码为 0），make 就执行下一条指令；如果返回失败（返回码非 0），make 就会终止当前命令。很多时候，命令出错（比如删除了一个不存在的文件）时，我们并不想终止，这时就可以在命令行前加 - 符号，来让 make 忽略命令的出错，以继续执行下一条命令，比如：

```
clean:
    -rm hello.o
```

#### 变量
变量，可能是 Makefile 中使用最频繁的语法了，Makefile 支持变量赋值、多行变量和环境变量。另外，Makefile 还内置了一些特殊变量和自动化变量。我们先来看下最基本的变量赋值功能。


Makefile 也可以像其他语言一样支持变量。在使用变量时，会像 shell 变量一样原地展开，然后再执行替换后的内容。Makefile 可以通过变量声明来声明一个变量，变量在声明时需要赋予一个初值，比如ROOT_PACKAGE=github.com/marmotedu/iam。引用变量时可以通过$()或者${}方式引用。我的建议是，用$()方式引用变量，例如$(ROOT_PACKAGE)，也建议整个 makefile 的变量引用方式保持一致。


变量会像 bash 变量一样，在使用它的地方展开。比如：

```
GO=go
build:
    $(GO) build -v .
```
展开后为：

```
GO=go
build:
    go build -v .
```

接下来，我给你介绍下 Makefile 中的 4 种变量赋值方法。

= 最基本的赋值方法。例如：
```
BASE_IMAGE = alpine:3.10
```

使用 = 进行赋值时，要注意下面这样的情况：

```
A = a
B = $(A) b
A = c
```
B 最后的值为 c b，而不是 a b。也就是说，在用变量给变量赋值时，右边变量的取值，取的是最终的变量值。


:=直接赋值，赋予当前位置的值。例如：

```
A = a
B := $(A) b
A = c
```

B 最后的值为 a b。通过 := 的赋值方式，可以避免 = 赋值带来的潜在的不一致。

?= 表示如果该变量没有被赋值，则赋予等号后的值。例如：

```
PLATFORMS ?= linux_amd64 linux_arm64
```
+=表示将等号后面的值添加到前面的变量上。例如：

```
MAKEFLAGS += --no-print-directory
```

Makefile 还支持多行变量。可以通过 define 关键字设置多行变量，变量中允许换行。定义方式为：
```
define 变量名
变量内容
...
endef
```
变量的内容可以包含函数、命令、文字或是其他变量。例如，我们可以定义一个 USAGE_OPTIONS 变量：

```
define USAGE_OPTIONS

Options:
  DEBUG        Whether to generate debug symbols. Default is 0.
  BINS         The binaries to build. Default is all of cmd.
  ...
  V            Set to 1 enable verbose build. Default is 0.
endef
```
Makefile 还支持环境变量。在 Makefile 中，有两种环境变量，分别是 Makefile 预定义的环境变量和自定义的环境变量。其中，自定义的环境变量可以覆盖 Makefile 预定义的环境变量。默认情况下，Makefile 中定义的环境变量只在当前 Makefile 有效，如果想向下层传递（Makefile 中调用另一个 Makefile），需要使用 export 关键字来声明。

下面的例子声明了一个环境变量，并可以在下层 Makefile 中使用：

```
...
export USAGE_OPTIONS
...
```
此外，Makefile 还支持两种内置的变量：特殊变量和自动化变量。特殊变量是 make 提前定义好的，可以在 makefile 中直接引用。特殊变量列表如下：

![img](https://static001.geekbang.org/resource/image/c1/1d/c1cba21aaed2eb0117yyb0470byy641d.png?wh=1052x978)

Makefile 还支持自动化变量。自动化变量可以提高我们编写 Makefile 的效率和质量。在 Makefile 的模式规则中，目标和依赖文件都是一系列的文件，那么我们如何书写一个命令，来完成从不同的依赖文件生成相对应的目标呢？这时就可以用到自动化变量。所谓自动化变量，就是这种变量会把模式中所定义的一系列的文件自动地挨个取出，一直到所有符合模式的文件都取完为止。这种自动化变量只应出现在规则的命令中。Makefile 中支持的自动化变量见下表。

![img](https://static001.geekbang.org/resource/image/13/12/13ec33008eaff973c0dd854a795ff712.png?wh=1263x1303)

上面这些自动化变量中，$*是用得最多的。$* 对于构造有关联的文件名是比较有效的。如果目标中没有模式的定义，那么 $* 也就不能被推导出。但是，如果目标文件的后缀是 make 所识别的，那么 $* 就是除了后缀的那一部分。例如：如果目标是 foo.c ，因为.c 是 make 所能识别的后缀名，所以 $* 的值就是 foo。

#### 条件语句
Makefile 也支持条件语句。这里先看一个示例。下面的例子判断变量ROOT_PACKAGE是否为空，如果为空，则输出错误信息，不为空则打印变量值：

```
ifeq ($(ROOT_PACKAGE),)
$(error the variable ROOT_PACKAGE must be set prior to including golang.mk)
else
$(info the value of ROOT_PACKAGE is $(ROOT_PACKAGE))
endif
```
条件语句的语法为：

```
# if ...
<conditional-directive>
<text-if-true>
endif
# if ... else ...
<conditional-directive>
<text-if-true>
else
<text-if-false>
endif
```
例如，判断两个值是否相等：

```
ifeq 条件表达式
...
else
...
endif
```
- ifeq 表示条件语句的开始，并指定一个条件表达式。表达式包含两个参数，参数之间用逗号分隔，并且表达式用圆括号括起来。
- else 表示条件表达式为假的情况。
- endif 表示一个条件语句的结束，任何一个条件表达式都应该以 endif 结束。
- 表示条件关键字，有 4 个关键字：ifeq、ifneq、ifdef、ifndef。


为了加深你的理解，我们分别来看下这 4 个关键字的例子。

ifeq：条件判断，判断是否相等。例如：

```
ifeq (<arg1>, <arg2>)
ifeq '<arg1>' '<arg2>'
ifeq "<arg1>" "<arg2>"
ifeq "<arg1>" '<arg2>'
ifeq '<arg1>' "<arg2>"
```
比较 arg1 和 arg2 的值是否相同，如果相同则为真。也可以用 make 函数 / 变量替代 arg1 或 arg2，例如 ifeq ($(origin ROOT_DIR),undefined) 或 ifeq ($(ROOT_PACKAGE),) 。origin 函数会在之后专门讲函数的一讲中介绍到。

ifneq：条件判断，判断是否不相等。

```
ifneq (<arg1>, <arg2>)
ifneq '<arg1>' '<arg2>'
ifneq "<arg1>" "<arg2>"
ifneq "<arg1>" '<arg2>'
ifneq '<arg1>' "<arg2>"
```
比较 arg1 和 arg2 的值是否不同，如果不同则为真。

ifdef：条件判断，判断变量是否已定义。

```
ifdef <variable-name>
```
如果值非空，则表达式为真，否则为假。也可以是函数的返回值。

ifndef：条件判断，判断变量是否未定义。

```
ifndef <variable-name>
```
如果值为空，则表达式为真，否则为假。也可以是函数的返回值。


#### 函数
Makefile 同样也支持函数，函数语法包括定义语法和调用语法。我们先来看下自定义函数。 make 解释器提供了一系列的函数供 Makefile 调用，这些函数是 Makefile 的预定义函数。我们可以通过 define 关键字来自定义一个函数。自定义函数的语法为：
```
define 函数名
函数体
endef
```
例如，下面这个自定义函数：

```
define Foo
    @echo "my name is $(0)"
    @echo "param is $(1)"
endef
```
define 本质上是定义一个多行变量，可以在 call 的作用下当作函数来使用，在其他位置使用只能作为多行变量来使用，例如：

```
var := $(call Foo)
new := $(Foo)
```
自定义函数是一种过程调用，没有任何的返回值。可以使用自定义函数来定义命令的集合，并应用在规则中。

再来看下预定义函数。 刚才提到，make 编译器也定义了很多函数，这些函数叫作预定义函数，调用语法和变量类似，语法为：

```
$(<function> <arguments>)
```
或者
```
${<function> <arguments>}
```
< function>是函数名，< arguments>是函数参数，参数间用逗号分割。函数的参数也可以是变量。我们来看一个例子：
```
PLATFORM = linux_amd64
GOOS := $(word 1, $(subst _, ,$(PLATFORM)))
```
上面的例子用到了两个函数：word 和 subst。word 函数有两个参数，1 和 subst 函数的输出。subst 函数将 PLATFORM 变量值中的 _ 替换成空格（替换后的 PLATFORM 值为 linux amd64）。word 函数取 linux amd64 字符串中的第一个单词。所以最后 GOOS 的值为 linux。

Makefile 预定义函数能够帮助我们实现很多强大的功能，在编写 Makefile 的过程中，如果有功能需求，可以优先使用这些函数。如果你想使用这些函数，那就需要知道有哪些函数，以及它们实现的功能。常用的函数包括下面这些，你需要先有个印象，以后用到时再来查看。

![img](https://static001.geekbang.org/resource/image/96/5f/96da0853e8225a656d2c0489e544865f.jpg?wh=2248x3692)


### 引入其他 Makefile
除了 Makefile 规则、Makefile 语法之外，Makefile 还有很多特性，比如可以引入其他 Makefile、自动生成依赖关系、文件搜索等等。这里我再介绍一个 IAM 项目的 Makefile 用到的重点特性：引入其他 Makefile。

在 14 讲 中，我们介绍过 Makefile 要结构化、层次化，这一点可以通过**在项目根目录下的 Makefile 中引入其他 Makefile 来实现。**

在 Makefile 中，我们可以通过关键字 include，把别的 makefile 包含进来，类似于 C 语言的#include，被包含的文件会插入在当前的位置。include 用法为include < filename>，示例如下：


```
include scripts/make-rules/common.mk
include scripts/make-rules/golang.mk
```

include 也可以包含通配符include scripts/make-rules/*。make 命令会按下面的顺序查找 makefile 文件：
1. 如果是绝对或相对路径，就直接根据路径 include 进来。
2. 如果 make 执行时，有-I或--include-dir参数，那么 make 就会在这个参数所指定的目录下去找。
3. 如果目录< prefix>/include（一般是/usr/local/bin或/usr/include）存在的话，make 也会去找。

如果有文件没有找到，make 会生成一条警告信息，但不会马上出现致命错误，而是继续载入其他的文件。一旦完成 makefile 的读取，make 会再重试这些没有找到或是不能读取的文件。如果还是不行，make 才会出现一条致命错误信息。如果你想让 make 忽略那些无法读取的文件继续执行，可以在 include 前加一个减号-，如-include <filename>。

### 总结
在这一讲里，为了帮助你编写一个高质量的 Makefile，我重点介绍了 Makefile 规则和 Makefile 语法里的一些核心语法知识。在讲 Makefile 规则时，我们主要学习了规则语法、伪目标和 order-only 依赖。掌握了这些 Makefile 规则，你就掌握了 Makefile 中最核心的内容。在介绍 Makefile 的语法时，我只介绍了 Makefile 的核心语法，以及 IAM 项目的 Makefile 用到的语法，包括命令、变量、条件语句和函数。你可能会觉得这些语法学习起来比较枯燥，但还是那句话，工欲善其事，必先利其器。希望你能熟练掌握 Makefile 的核心语法，为编写高质量的 Makefile 打好基础。

## 特别放送 | Go Modules依赖包管理全讲

在 Go 项目开发中，依赖包管理是一个非常重要的内容，依赖包处理不好，就会导致编译失败。而且 Go 的依赖包管理有一定的复杂度，所以，我们有必要系统学习下 Go 的依赖包管理工具。这一讲，我会首先介绍下 Go 依赖包管理工具的历史，并详细介绍下目前官方推荐的依赖包管理方案 Go Modules。Go Modules 主要包括了 go mod 命令行工具、模块下载机制，以及两个核心文件 go.mod 和 go.sum。另外，Go Modules 也提供了一些环境变量，用来控制 Go Modules 的行为。这一讲，我会分别介绍下这些内容。在正式开始讲解这些内容之前，我们先来对 Go Modules 有个基本的了解。


### Go Modules 简介
Go Modules 是 Go 官方推出的一个 Go 包管理方案，基于 vgo 演进而来，具有下面这几个特性：

- 可以使包的管理更加简单。
- 支持版本管理。
- 允许同一个模块多个版本共存。
- 可以校验依赖包的哈希值，确保包的一致性，增加安全性。
- 内置在几乎所有的 go 命令中，包括go get、go build、go install、go run、go test、go list等命令。
- 具有 Global Caching 特性，不同项目的相同模块版本，只会在服务器上缓存一份。

在 Go1.14 版本以及之后的版本，Go 官方建议在生产环境中使用 Go Modules。因此，以后的 Go 包管理方案会逐渐统一到 Go Modules。与 Go Modules 相关的概念很多，我在这里把它们总结为“6-2-2-1-1”，这一讲后面还会详细介绍每个概念。

- 六个环境变量：GO111MODULE、GOPROXY、GONOPROXY、GOSUMDB、GONOSUMDB、GOPRIVATE。
- 两个概念：Go module proxy 和 Go checksum database。
- 两个主要文件：go.mod 和 go.sum。
- 一个主要管理命令：go mod。
- 一个 build flag。


### Go 包管理的历史
在具体讲解 Go Modules 之前，我们先看一下 Go 包管理的历史。从 Go 推出之后，因为没有一个统一的官方方案，所以出现了很多种 Go 包管理方案，比较混乱，也没有彻底解决 Go 包管理的一些问题。Go 包管理的历史如下图所示：

![img](https://static001.geekbang.org/resource/image/34/e5/348d772b26940f721c6fb907f6833be5.jpg?wh=2248x739)
这张图展示了 Go 依赖包管理工具经历的几个发展阶段，接下来我会按时间顺序重点介绍下其中的五个阶段。

#### Go1.5 版本前：GOPATH
在 Go1.5 版本之前，没有版本控制，所有的依赖包都放在 GOPATH 下。采用这种方式，无法实现包的多版本管理，并且包的位置只能局限在 GOPATH 目录下。如果 A 项目和 B 项目用到了同一个 Go 包的不同版本，这时候只能给每个项目设置一个 GOPATH，将对应版本的包放在各自的 GOPATH 目录下，切换项目目录时也需要切换 GOPATH。这些都增加了开发和实现的复杂度。

#### Go1.5 版本：Vendoring
Go1.5 推出了 vendor 机制，并在 Go1.6 中默认启用。在这个机制中，每个项目的根目录都可以有一个 vendor 目录，里面存放了该项目的 Go 依赖包。在编译 Go 源码时，Go 优先从项目根目录的 vendor 目录查找依赖；如果没有找到，再去 GOPATH 下的 vendor 目录下找；如果还没有找到，就去 GOPATH 下找。这种方式解决了多 GOPATH 的问题，但是随着项目依赖的增多，vendor 目录会越来越大，造成整个项目仓库越来越大。在 vendor 机制下，一个中型项目的 vendor 目录有几百 M 的大小一点也不奇怪。

#### “百花齐放”：多种 Go 依赖包管理工具出现
这个阶段，社区也出现了很多 Go 依赖包管理的工具，这里我介绍三个比较有名的。- Godep：解决包依赖的管理工具，Docker、Kubernetes、CoreOS 等 Go 项目都曾用过 godep 来管理其依赖。
Govendor：它的功能比 Godep 多一些，通过 vendor 目录下的vendor.json文件来记录依赖包的版本。
Glide：相对完善的包管理工具，通过glide.yaml记录依赖信息，通过glide.lock追踪每个包的具体修改。

Govendor、Glide 都是在 Go 支持 vendor 之后推出的工具，Godep 在 Go 支持 vendor 之前也可以使用。Go 支持 vendor 之后，Godep 也改用了 vendor 模式。


#### Go1.9 版本：Dep
对于从 0 构建项目的新用户来说，Glide 功能足够，是个不错的选择。不过，Golang 依赖管理工具混乱的局面最终由官方来终结了：Golang 官方接纳了由社区组织合作开发的 Dep，作为 official experiment。在相当长的一段时间里，Dep 作为标准，成为了事实上的官方包管理工具。因为 Dep 已经成为了 official experiment 的过去时，现在我们就不必再去深究了，让我们直接去了解谁才是未来的 official experiment 吧。


#### Go1.11 版本之后：Go Modules
Go1.11 版本推出了 Go Modules 机制，Go Modules 基于 vgo 演变而来，是 Golang 官方的包管理工具。在 Go1.13 版本，Go 语言将 Go Modules 设置为默认的 Go 管理工具；在 Go1.14 版本，Go 语言官方正式推荐在生产环境使用 Go Modules，并且鼓励所有用户从其他的依赖管理工具迁移过来。至此，Go 终于有了一个稳定的、官方的 Go 包管理工具。到这里，我介绍了 Go 依赖包管理工具的历史，下面再来介绍下 Go Modules 的使用方法。


#### 包（package）和模块（module）
Go 程序被组织到 Go 包中，Go 包是同一目录中一起编译的 Go 源文件的集合。在一个源文件中定义的函数、类型、变量和常量，对于同一包中的所有其他源文件可见。模块是存储在文件树中的 Go 包的集合，并且文件树根目录有 go.mod 文件。go.mod 文件定义了模块的名称及其依赖包，每个依赖包都需要指定导入路径和语义化版本（Semantic Versioning），通过导入路径和语义化版本准确地描述一个依赖。这里要注意，"module" != "package"，模块和包的关系更像是集合和元素的关系，包属于模块，一个模块是零个或者多个包的集合。下面的代码段，引用了一些包：

```go
import (
    // Go 标准包
    "fmt"

    // 第三方包
    "github.com/spf13/pflag"

    // 匿名包
     _ "github.com/jinzhu/gorm/dialects/mysql"

     // 内部包
    "github.com/marmotedu/iam/internal/apiserver"
)
```
这里的fmt、github.com/spf13/pflag和github.com/marmotedu/iam/internal/apiserver都是 Go 包。Go 中有 4 种类型的包，下面我来分别介绍下。
- Go 标准包：在 Go 源码目录下，随 Go 一起发布的包。
- 第三方包：第三方提供的包，比如来自于 github.com 的包。
- 匿名包：只导入而不使用的包。通常情况下，我们只是想使用导入包产生的副作用，即引用包级别的变量、常量、结构体、接口等，以及执行导入包的init()函数。
- 内部包：项目内部的包，位于项目目录下。

下面的目录定义了一个模块：

```
$ ls hello/
go.mod  go.sum  hello.go  hello_test.go  world
```
hello 目录下有一个 go.mod 文件，说明了这是一个模块，该模块包含了 hello 包和一个子包 world。该目录中也包含了一个 go.sum 文件，该文件供 Go 命令在构建时判断依赖包是否合法。这里你先简单了解下，我会在下面讲 go.sum 文件的时候详细介绍。
### Go Modules 命令
Go Modules 的管理命令为go mod，go mod有很多子命令，你可以通过go help mod来获取所有的命令。下面我来具体介绍下这些命令。

- download：下载 go.mod 文件中记录的所有依赖包。
- edit：编辑 go.mod 文件。
- graph：查看现有的依赖结构。
- init：把当前目录初始化为一个新模块。
- tidy：添加丢失的模块，并移除无用的模块。默认情况下，Go 不会移除 go.mod 文件中的无用依赖。当依赖包不再使用了，可以使用go mod tidy命令来清除它。
- vendor：将所有依赖包存到当前目录下的 vendor 目录下。
- verify：检查当前模块的依赖是否已经存储在本地下载的源代码缓存中，以及检查下载后是否有修改。
- why：查看为什么需要依赖某模块。


### Go Modules 开关
如果要使用 Go Modules，在 Go1.14 中仍然需要确保 Go Modules 特性处在打开状态。你可以通过环境变量 GO111MODULE 来打开或者关闭。GO111MODULE 有 3 个值，我来分别介绍下。
- auto：在 Go1.14 版本中是默认值，在$GOPATH/src下，且没有包含 go.mod 时则关闭 Go Modules，其他情况下都开启 Go Modules。
- on：启用 Go Modules，Go1.14 版本推荐打开，未来版本会设为默认值。
- off：关闭 Go Modules，不推荐。

所以，如果要打开 Go Modules，可以设置环境变量export GO111MODULE=on或者export GO111MODULE=auto，建议直接设置export GO111MODULE=on。Go Modules 使用语义化的版本号，我们开发的模块在发布版本打 tag 的时候，要注意遵循语义化的版本要求，不遵循语义化版本规范的版本号都是无法拉取的。


### 模块下载
在执行 go get 等命令时，会自动下载模块。接下来，我会介绍下 go 命令是如何下载模块的。主要有三种下载方式：通过代理下载；指定版本号下载；按最小版本下载。

#### 通过代理来下载模块
默认情况下，Go 命令从 VCS（Version Control System，版本控制系统）直接下载模块，例如 GitHub、Bitbucket、Bazaar、Mercurial 或者 SVN。在 Go 1.13 版本，引入了一个新的环境变量 GOPROXY，用于设置 Go 模块代理（Go module proxy）。模块代理可以使 Go 命令直接从代理服务器下载模块。GOPROXY 默认值为https://proxy.golang.org,direct，代理服务器可以指定多个，中间用逗号隔开，例如GOPROXY=https://proxy.golang.org,https://goproxy.cn,direct。当下载模块时，会优先从指定的代理服务器上下载。如果下载失败，比如代理服务器不可访问，或者 HTTP 返回码为404或410，Go 命令会尝试从下一个代理服务器下载。

direct 是一个特殊指示符，用来指示 Go 回源到模块的源地址 (比如 GitHub 等) 去抓取 ，当值列表中上一个 Go module proxy 返回 404 或 410，Go 会自动尝试列表中的下一个，遇见 direct 时回源，遇见 EOF 时终止，并抛出类似invalid version: unknown revision...的错误。 如果GOPROXY=off，则 Go 命令不会尝试从代理服务器下载模块。引入 Go module proxy 会带来很多好处，比如：

- 国内开发者无法访问像 golang.org、gopkg.in、go.uber.org 这类域名，可以设置 GOPROXY 为国内可以访问的代理服务器，解决依赖包下载失败的问题。
- Go 模块代理会永久缓存和存储所有的依赖，并且这些依赖一经缓存，不可更改，这也意味着我们不需要再维护一个 vendor 目录，也可以避免因为维护 vendor 目录所带来的存储空间占用。
- 因为依赖永久存在于代理服务器，这样即使模块从互联网上被删除，也仍然可以通过代理服务器获取到。
- 一旦将 Go 模块存储在 Go 代理服务器中，就无法覆盖或删除它，这可以保护开发者免受可能注入相同版本恶意代码所带来的攻击。
- 我们不再需要 VCS 工具来下载依赖，因为所有的依赖都是通过 HTTP 的方式从代理服务器下载。
- 因为 Go 代理通过 HTTP 独立提供了源代码（.zip 存档）和 go.mod，所以下载和构建 Go 模块的速度更快。因为可以独立获取 go.mod（而之前必须获取整个仓库），所以解决依赖也更快。
- 当然，开发者也可以设置自己的 Go 模块代理，这样开发者可以对依赖包有更多的控制，并可以预防 VCS 停机所带来的下载失败。

在实际开发中，我们的很多模块可能需要从私有仓库拉取，通过代理服务器访问会报错，这时候我们需要将这些模块添加到环境变量 GONOPROXY 中，这些私有模块的哈希值也不会在 checksum database 中存在，需要将这些模块添加到 GONOSUMDB 中。一般来说，我建议直接设置 GOPRIVATE 环境变量，它的值将作为 GONOPROXY 和 GONOSUMDB 的默认值。

GONOPROXY、GONOSUMDB 和 GOPRIVATE 都支持通配符，多个域名用逗号隔开，例如*.example.com,github.com。对于国内的 Go 开发者来说，目前有 3 个常用的 GOPROXY 可供选择，分别是官方、七牛和阿里云。官方的 GOPROXY，国内用户可能访问不到，所以我更推荐使用七牛的goproxy.cn，goproxy.cn是七牛云推出的非营利性项目，它的目标是为中国和世界上其他地方的 Go 开发者提供一个免费、可靠、持续在线，且经过 CDN 加速的模块代理。

#### 指定版本号下载
通常，我们通过go get来下载模块，下载命令格式为go get <package[@version]>，如下表所示：

![img](https://static001.geekbang.org/resource/image/63/92/63fbbf7cf8b67af85aa4e7ce76a99392.jpg?wh=2248x2048)

你可以使用go get -u更新 package 到 latest 版本，也可以使用go get -u=patch只更新小版本，例如从v1.2.4到v1.2.5。

#### 按最小版本下载
一个模块往往会依赖许多其他模块，并且不同的模块也可能会依赖同一个模块的不同版本，如下图所示：

![img](https://static001.geekbang.org/resource/image/00/46/00794e3487e63d9d3302bfe977af6d46.jpg?wh=2248x1212)
在上述依赖中，模块 A 依赖了模块 B 和模块 C，模块 B 依赖了模块 D，模块 C 依赖了模块 D 和模块 F，模块 D 又依赖了模块 E。并且，同模块的不同版本还依赖了对应模块的不同版本。那么 Go Modules 是如何选择版本的呢？Go Modules 会把每个模块的依赖版本清单都整理出来，最终得到一个构建清单，如下图所示：

![img](https://static001.geekbang.org/resource/image/4f/f2/4f83ffe8125d75764c5f8069a73966f2.jpg?wh=2248x1148)

上图中，rough list 和 final list 的区别在于重复引用的模块 D（v1.3、v1.4），最终清单选用了 D 的v1.4版本。这样做的主要原因有两个。第一个是语义化版本的控制。因为模块 D 的v1.3和v1.4版本变更都属于次版本号的变更，而在语义化版本的约束下，v1.4必须要向下兼容v1.3，因此我们要选择高版本的v1.4。第二个是模块导入路径的规范。主版本号不同，模块的导入路径就不一样。所以，如果出现不兼容的情况，主版本号会改变，例如从 v1 变为 v2，模块的导入路径也就改变了，因此不会影响 v1 版本。

### go.mod 和 go.sum 介绍
在 Go Modules 中，go.mod 和 go.sum 是两个非常重要的文件，下面我就来详细介绍这两个文件。

#### go.mod 文件介绍
go.mod 文件是 Go Modules 的核心文件。下面是一个 go.mod 文件示例：

```
module github.com/marmotedu/iam

go 1.14

require (
  github.com/AlekSi/pointer v1.1.0
  github.com/appleboy/gin-jwt/v2 v2.6.3
  github.com/asaskevich/govalidator v0.0.0-20200428143746-21a406dcc535
  github.com/gin-gonic/gin v1.6.3
  github.com/golangci/golangci-lint v1.30.0 // indirect
  github.com/google/uuid v1.0.0
    github.com/blang/semver v3.5.0+incompatible
    golang.org/x/text v0.3.2
)

replace (
    github.com/gin-gonic/gin => /home/colin/gin
    golang.org/x/text v0.3.2 => github.com/golang/text v0.3.2
)

exclude (
    github.com/google/uuid v1.1.0
)
```

**接下来，我会从 go.mod 语句、go.mod 版本号、go.mod 文件修改方法三个方面来介绍 go.mod。**

go.mod 语句

go.mod 文件中包含了 4 个语句，分别是 module、require、replace 和 exclude。下面我来介绍下它们的功能。

- module：用来定义当前项目的模块路径。
- go：用来设置预期的 Go 版本，目前只是起标识作用。
- require：用来设置一个特定的模块版本，格式为<导入包路径> <版本> [// indirect]。
- exclude：用来从使用中排除一个特定的模块版本，如果我们知道模块的某个版本有严重的问题，就可以使用 exclude 将该版本排除掉。
- replace：用来将一个模块版本替换为另外一个模块版本。格式为 $module => $newmodule ，$newmodule可以是本地磁盘的相对路径，例如github.com/gin-gonic/gin => ./gin。也可以是本地磁盘的绝对路径，例如github.com/gin-gonic/gin => /home/lk/gin。还可以是网络路径，例如golang.org/x/text v0.3.2 => github.com/golang/text v0.3.2。

这里需要注意，虽然我们用$newmodule替换了$module，但是在代码中的导入路径仍然为$module。replace 在实际开发中经常用到，下面的场景可能需要用到 replace：
- 在开启 Go Modules 后，缓存的依赖包是只读的，但在日常开发调试中，我们可能需要修改依赖包的代码来进行调试，这时可以将依赖包另存到一个新的位置，并在 go.mod 中替换这个包。
- 如果一些依赖包在 Go 命令运行时无法下载，就可以通过其他途径下载该依赖包，上传到开发构建机，并在 go.mod 中替换为这个包。
- 在项目开发初期，A 项目依赖 B 项目的包，但 B 项目因为种种原因没有 push 到仓库，这时也可以在 go.mod 中把依赖包替换为 B 项目的本地磁盘路径。
- 在国内访问 golang.org/x 的各个包都需要翻墙，可以在 go.mod 中使用 replace，替换成 GitHub 上对应的库，例如golang.org/x/text v0.3.0 => github.com/golang/text v0.3.0。

有一点要注意，exclude 和 replace 只作用于当前主模块，不影响主模块所依赖的其他模块。

go.mod 版本号
go.mod 文件中有很多版本号格式，我知道在平时使用中，有很多开发者对此感到困惑。这里，我来详细说明一下。
- 如果模块具有符合语义化版本格式的 tag，会直接展示 tag 的值，例如 github.com/AlekSi/pointer v1.1.0 。
- 除了 v0 和 v1 外，主版本号必须显试地出现在模块路径的尾部，例如github.com/appleboy/gin-jwt/v2 v2.6.3。
- 对于没有 tag 的模块，Go 命令会选择 master 分支上最新的 commit，并根据 commit 时间和哈希值生成一个符合语义化版本的版本号，例如github.com/asaskevich/govalidator v0.0.0-20200428143746-21a406dcc535。
- 如果模块名字跟版本不符合规范，例如模块的名字为github.com/blang/semver，但是版本为 v3.5.0（正常应该是github.com/blang/semver/v3），go 会在 go.mod 的版本号后加+incompatible表示。
- 如果 go.mod 中的包是间接依赖，则会添加// indirect注释，例如github.com/golangci/golangci-lint v1.30.0 // indirect。

这里要注意，Go Modules 要求模块的版本号格式为v< major>.< minor>.< patch>，如果< major>版本号大于 1，它的版本号还要体现在模块名字中，例如模块github.com/blang/semver版本号增长到v3.x.x，则模块名应为github.com/blang/semver/v3。

这里再详细介绍下出现// indirect的情况。原则上 go.mod 中出现的都是直接依赖，但是下面的两种情况只要出现一种，就会在 go.mod 中添加间接依赖。
- 直接依赖未启用 Go Modules：如果模块 A 依赖模块 B，模块 B 依赖 B1 和 B2，但是 B 没有 go.mod 文件，则 B1 和 B2 会记录到 A 的 go.mod 文件中，并在最后加上// indirect。
- 直接依赖 go.mod 文件中缺失部分依赖：如果模块 A 依赖模块 B，模块 B 依赖 B1 和 B2，B 有 go.mod 文件，但是只有 B1 被记录在 B 的 go.mod 文件中，这时候 B2 会被记录到 A 的 go.mod 文件中，并在最后加上// indirect。


go.mod 文件修改方法
要修改 go.mod 文件，我们可以采用下面这三种方法：

- Go 命令在运行时自动修改。
- 手动编辑 go.mod 文件，编辑之后可以执行go mod edit -fmt格式化 go.mod 文件。
- 执行 go mod 子命令修改。

在实际使用中，我建议你采用第三种修改方法，和其他两种相比不太容易出错。使用方式如下：

```
go mod edit -fmt  # go.mod 格式化
go mod edit -require=golang.org/x/text@v0.3.3  # 添加一个依赖
go mod edit -droprequire=golang.org/x/text # require的反向操作，移除一个依赖
go mod edit -replace=github.com/gin-gonic/gin=/home/colin/gin # 替换模块版本
go mod edit -dropreplace=github.com/gin-gonic/gin # replace的反向操作
go mod edit -exclude=golang.org/x/text@v0.3.1 # 排除一个特定的模块版本
go mod edit -dropexclude=golang.org/x/text@v0.3.1 # exclude的反向操作
```

#### go.sum 文件介绍
Go 会根据 go.mod 文件中记载的依赖包及其版本下载包源码，但是下载的包可能被篡改，缓存在本地的包也可能被篡改。单单一个 go.mod 文件，不能保证包的一致性。为了解决这个潜在的安全问题，Go Modules 引入了 go.sum 文件。

go.sum 文件用来记录每个依赖包的 hash 值，在构建时，如果本地的依赖包 hash 值与go.sum文件中记录的不一致，则会拒绝构建。go.sum 中记录的依赖包是所有的依赖包，包括间接和直接的依赖包。这里提示下，为了避免已缓存的模块被更改，$GOPATH/pkg/mod下缓存的包是只读的，不允许修改。

接下来我从 go.sum 文件内容、go.sum 文件生成、校验三个方面来介绍 go.sum。

go.sum 文件内容
下面是一个 go.sum 文件的内容：
```
golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c h1:qgOY6WgZOaTkIIMiVjBQcw93ERBE4m30iBm00nkL0i8=
golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c/go.mod h1:NqM8EUOU14njkJ3fqMW+pc6Ldnwhi/IjpwHt7yyuwOQ=
rsc.io/quote v1.5.2 h1:w5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
rsc.io/quote v1.5.2/go.mod h1:LzX7hefJvL54yjefDEDHNONDjII0t9xZLPXsUe+TKr0=
rsc.io/sampler v1.3.0 h1:7uVkIFmeBqHfdjD+gZwtXXI+RODJ2Wc4O7MPEh/QiW4=
rsc.io/sampler v1.3.0/go.mod h1:T1hPZKmBbMNahiBKFy5HrXp6adAjACjK9JXDnKaTXpA=
```
go.sum 文件中，每行记录由模块名、版本、哈希算法和哈希值组成，如< module> < version>[/go.mod] <algorithm>:<hash>。目前，从 Go1.11 到 Go1.14 版本，只有一个算法 SHA-256，用 h1 表示。

正常情况下，每个依赖包会包含两条记录，分别是依赖包所有文件的哈希值和该依赖包 go.mod 的哈希值，例如：


```
rsc.io/quote v1.5.2 h1:w5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
rsc.io/quote v1.5.2/go.mod h1:LzX7hefJvL54yjefDEDHNONDjII0t9xZLPXsUe+TKr0=
```
但是，如果一个依赖包没有 go.mod 文件，就只记录依赖包所有文件的哈希值，也就是只有第一条记录。额外记录 go.mod 的哈希值，主要是为了在计算依赖树时不必下载完整的依赖包版本，只根据 go.mod 即可计算依赖树。

go.sum 文件生成
在 Go Modules 开启时，如果我们的项目需要引入一个新的包，通常会执行go get命令，例如：

```
$ go get rsc.io/quote
```

当执行go get rsc.io/quote命令后，go get命令会先将依赖包下载到$GOPATH/pkg/mod/cache/download，下载的依赖包文件名格式为$version.zip，例如v1.5.2.zip。

下载完成之后，go get会对该 zip 包做哈希运算，并将结果存在$version.ziphash文件中，例如v1.5.2.ziphash。如果在项目根目录下执行go get命令，则go get会同时更新 go.mod 和 go.sum 文件。例如，go.mod 新增一行require rsc.io/quote v1.5.2，go.sum 新增两行：

```
rsc.io/quote v1.5.2 h1:w5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
rsc.io/quote v1.5.2/go.mod h1:LzX7hefJvL54yjefDEDHNONDjII0t9xZLPXsUe+TKr0=
```
校验
在我们执行构建时，go 命令会从本地缓存中查找所有的依赖包，并计算这些依赖包的哈希值，然后与 go.sum 中记录的哈希值进行对比。如果哈希值不一致，则校验失败，停止构建。

校验失败可能是因为本地指定版本的依赖包被修改过，也可能是 go.sum 中记录的哈希值是错误的。但是 Go 命令倾向于相信依赖包被修改过，因为当我们在 go get 依赖包时，包的哈希值会经过校验和数据库（checksum database）进行校验，校验通过才会被加入到 go.sum 文件中。也就是说，go.sum 文件中记录的哈希值是可信的。

校验和数据库可以通过环境变量GOSUMDB指定，GOSUMDB的值是一个 web 服务器，默认值是sum.golang.org。该服务可以用来查询依赖包指定版本的哈希值，保证拉取到的模块版本数据没有经过篡改。

如果设置GOSUMDB为off，或者使用go get的时候启用了-insecure参数，Go 就不会去对下载的依赖包做安全校验，这存在一定的安全隐患，所以我建议你开启校验和数据库。如果对安全性要求很高，同时又访问不了sum.golang.org，你也可以搭建自己的校验和数据库。

值得注意的是，Go checksum database 可以被 Go module proxy 代理，所以当我们设置了GOPROXY后，通常情况下不用再设置GOSUMDB。还要注意的是，go.sum 文件也应该提交到你的 Git 仓库中去。


### 模块下载流程
上面，我介绍了模块下载的整体流程，还介绍了 go.mod 和 go.sum 这两个文件。因为内容比较多，这里用一张图片来做个总结：

![img](https://static001.geekbang.org/resource/image/8b/8d/8b92e53cebd4373f8c41fdbe9328ba8d.jpg?wh=2248x1077)

最后还想介绍下 Go modules 的全局缓存。Go modules 中，相同版本的模块只会缓存一份，其他所有模块公用。目前，所有模块版本数据都缓存在 $GOPATH/pkg/mod 和 $GOPATH/pkg/sum 下，未来有可能移到 $GOCACHE/mod 和 $GOCACHE/sum 下，我认为这可能发生在 GOPATH 被淘汰后。你可以使用 go clean -modcache 清除所有的缓存。

### 总结
Go 依赖包管理是 Go 语言中一个重点的功能。在 Go1.11 版本之前，并没有官方的依赖包管理工具，业界虽然存在多个 Go 依赖包管理方案，但效果都不理想。直到 Go1.11 版本，Go 才推出了官方的依赖包管理工具，Go Modules。这也是我建议你在进行 Go 项目开发时选择的依赖包管理工具。


Go Modules 提供了 go mod 命令，来管理 Go 的依赖包。 go mod 有很多子命令，这些子命令可以完成不同的功能。例如，初始化当前目录为一个新模块，添加丢失的模块，移除无用的模块，等等。

在 Go Modules 中，有两个非常重要的文件：go.mod 和 go.sum。go.mod 文件是 Go Modules 的核心文件，Go 会根据 go.mod 文件中记载的依赖包及其版本下载包源码。go.sum 文件用来记录每个依赖包的 hash 值，在构建时，如果本地的依赖包 hash 值与 go.sum 文件中记录的不一致，就会拒绝构建。

Go 在下载依赖包时，可以通过代理来下载，也可以指定版本号下载。如果不指定版本号，Go Modules 会根据自定义的规则，选择最小版本来下载。


## 特别放送 | IAM排障指南

今天我们更新一期特别放送作为加餐。在部署和使用 IAM 的过程中，难免会出现一些异常 (也称为故障、问题)。这时候，就需要我们能够定位故障，并修复故障。这里，我总结了一些 IAM 的排障方法，以及一些常见故障的解决方法，供你参考。

### 如何排障？
首先，我们需要发现问题，然后定位问题。我们可能需要经过多轮分析排查才能定位到问题的根因，最后去解决问题。排障流程如下图所示：

![img](https://static001.geekbang.org/resource/image/73/0f/7330d836e7c4b5052c79bbd365abdd0f.jpg?wh=2248x535)


如果想排查问题并解决问题，你还需要具备这两个基本能力：**能够理解错误日志的内容；根据错误日志，找出解决方案**。我们举个例子来说吧。有以下错误：

```
[going@dev iam]$ mysql -h127.0.0.1 -uroot -p'iam59!z$'
bash: /usr/bin/mysql: 没有那个文件或目录
[going@dev iam]$
```
对于这个错误，我们首先来理解错误内容：mysql 命令没有找到，说明没有安装 mysql，或者安装 mysql 失败。那么，我们的解决方案就是重新执行 03 讲 中安装 MariaDB 的步骤：

```
$ cd $IAM_ROOT
$ ./scripts/install/mariadb.sh iam::mariadb::install
```
接下来，我会以iam-apiserver服务为例，给你演示下具体如何排障并解决问题。


#### 发现问题
要排障，首先我们需要发现问题。我们通常用下面这几种方式来发现问题。

- 检查服务状态：启动 iam-apiserver 服务后，执行systemctl status iam-apiserver 发现 iam-apiserver 启动失败，即Active的值不为active (running)。
- 功能异常：访问 iam-apiserver 服务，功能异常或者报错，例如接口返回值跟预期不一样等。
- 日志报错：在 iam-apiserver 的日志中发现一些WARN、ERROR、PANIC、FATAL等级别的错误日志。

#### 定位问题
发现问题之后，就需要我们定位出问题的根本原因。我们可以通过下面这三种方式来定位问题。

- 查看日志，它是最简单的排障方式。
- 使用 Go 调试工具 Delve 来定位问题。
- 添加 Debug 日志，从程序入口处跟读代码，在关键位置处打印 Debug 日志，来定位问题。

在定位问题的过程中，我们可以采用“顺藤摸瓜”的思路去排查问题。比如，我们的程序执行流程是：A -> B -> … -> N。其中 A、B、N 都可以理解为一个排查点。所谓的排查点，就是需要在该处定位问题的点，这些点可能是导致问题的根因所在。

在排障过程中，你可以根据最上层的日志报错，找到下一个排查点 B。如果经过定位，发现 B 没有问题，那继续根据程序执行流程，找下一个排查点排查问题。如此反复，直到找到最终的排查点，也就是出问题的根因 N，N 即为 Bug 点。执行流程如下图所示：

![img](https://static001.geekbang.org/resource/image/cc/6d/cc26b83cb2177106695e1a9f7f09ae6d.jpg?wh=2248x931)


下面，我们来具体看看这三种定位问题的方法。

##### 查看日志定位问题
我们首先应该通过日志来定位问题，这是最简单高效的方式。要通过日志来定位问题，你不仅要会看日志，还要能读懂日志，也就是理解日志报错的原因。下面我来具体讲解用这种方法定位问题的步骤。

第一步，确保服务运行正常。你可以通过执行 systemctl status 命令来查看服务的运行状况：

```
$ systemctl status iam-apiserver
● iam-apiserver.service - IAM APIServer
   Loaded: loaded (/etc/systemd/system/iam-apiserver.service; enabled; vendor preset: disabled)
   Active: activating (auto-restart) (Result: exit-code) since Thu 2021-09-09 13:47:56 CST; 2s ago
     Docs: https://github.com/marmotedu/iam/blob/master/init/README.md
  Process: 119463 ExecStart=/opt/iam/bin/iam-apiserver --config=/etc/iam/iam-apiserver.yaml (code=exited, status=1/FAILURE)
  Process: 119461 ExecStartPre=/usr/bin/mkdir -p /var/log/iam (code=exited, status=0/SUCCESS)
  Process: 119460 ExecStartPre=/usr/bin/mkdir -p /data/iam/iam-apiserver (code=exited, status=0/SUCCESS)
 Main PID: 119463 (code=exited, status=1/FAILURE)
```

可以看到，Active不是active (running)，说明 iam-apiserver 服务没有正常运行。从上面输出中的Process: 119463 ExecStart=/opt/iam/bin/iam-apiserver --config=/etc/iam/iam-apiserver.yaml (code=exited, status=1/FAILURE)信息中，我们可以获取以下信息：
- iam-apiserver 服务启动命令为/opt/iam/bin/iam-apiserver --config=/etc/iam/iam-apiserver.yaml。
- /opt/iam/bin/iam-apiserver加载的配置文件为/etc/iam/iam-apiserver.yaml。
- /opt/iam/bin/iam-apiserver命令执行失败，退出码为 1，其进程 ID 为119463。

这里注意，systemctl status会将超过一定长度的行的后半部分用省略号替代，如果想查看完整的信息，可以追加-l参数，也就是systemctl status -l来查看。既然 iam-apiserver 命令启动失败，那我们就需要查看 iam-apiserver 启动时的日志，看看有没有一些报错日志。


接下来，就进入第二步，查看iam-apiserver运行日志。
这里提一句，如果你对 systemd 不了解，也可以趁机恶补一波。你可以参考阮一峰大佬的两篇博客：[Systemd 入门教程：命令篇](https://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)和[Systemd 入门教程：实战篇](https://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)。

那么如何查看呢？我们有 3 种查看方式，我在下面按优先级顺序排列了下。你在定位问题和查看日志时，按优先级 3 选 1 即可，1 > 2 > 3。
1. 通过journalctl -u iam-apiserver查看。
2. 通过 iam-apiserver 日志文件查看。
3. 通过 console 查看。

下面我来分别介绍下这三种查看方式。先来看优先级最高的方式，通过journalctl -u iam-apiserver查看。

systemd 提供了自己的日志系统，称为 journal。我们可以使用journalctl命令来读取 journal 日志。journalctl提供了-u选项来查看某个 Unit 的日志，提供了_PID来查看指定进程 ID 的日志。在第一步中，我们知道服务启动失败的进程 ID 为119463。执行以下命令来查看这次启动的日志：

```
$ sudo journalctl _PID=119463
-- Logs begin at Thu 2021-09-09 09:12:25 CST, end at Thu 2021-09-09 14:40:48 CST. --
...
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: 2021-09-09 13:47:56.727        INFO        apiserver        gorm@v1.21.12/gorm.go:202        mysql/mysql.go:75[error] faile>
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: 2021-09-09 13:47:56.727        FATAL        apiserver        apiserver/server.go:139        Failed to get cache instance: g>
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/marmotedu/iam/internal/apiserver.(*completedExtraConfig).New
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/server.go:139
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/marmotedu/iam/internal/apiserver.createAPIServer
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/server.go:66
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/marmotedu/iam/internal/apiserver.Run
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/run.go:11
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/marmotedu/iam/internal/apiserver.run.func1
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/app.go:46
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/marmotedu/iam/pkg/app.(*App).runCommand
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/src/github.com/marmotedu/iam/pkg/app/app.go:278
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/spf13/cobra.(*Command).execute
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:856
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/spf13/cobra.(*Command).ExecuteC
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:974
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/spf13/cobra.(*Command).Execute
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:902
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: github.com/marmotedu/iam/pkg/app.(*App).Run
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/src/github.com/marmotedu/iam/pkg/app/app.go:233
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: main.main
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/workspace/golang/src/github.com/marmotedu/iam/cmd/iam-apiserver/apiserver.go:24
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]: runtime.main
Sep 09 13:47:56 VM-200-70-centos iam-apiserver[119463]:         /home/going/go/go1.16.2/src/runtime/proc.go:225
lines 10-54/54 (END)

```

从上面的日志中，我们找到了服务启动失败的原因：iam-apiserver启动时，发生了FATAL级别的错误。到这里，你已经初步定位到问题原因了。

我们再来看通过 iam-apiserver 日志文件查看的方式。

作为一个企业级的实战项目，iam-apiserver 的日志当然是会记录到日志文件中的。在第一步中，我们通过systemctl status iam-apiserver输出的信息，知道了 iam-apiserver 启动时加载的配置文件为/etc/iam/iam-apiserver.yaml。所以，我们可以通过 iam-apiserver 的配置文件 iam-apiserver.yaml 中的log.output-paths配置项，查看记录日志文件的位置：

```
log:
    name: apiserver # Logger的名字
    development: true # 是否是开发模式。如果是开发模式，会对DPanicLevel进行堆栈跟踪。
    level: debug # 日志级别，优先级从低到高依次为：debug, info, warn, error, dpanic, panic, fatal。
    format: console # 支持的日志输出格式，目前支持console和json两种。console其实就是text格式。
    enable-color: true # 是否开启颜色输出，true:是，false:否
    disable-caller: false # 是否开启 caller，如果开启会在日志中显示调用日志所在的文件、函数和行号
    disable-stacktrace: false # 是否在panic及以上级别禁止打印堆栈信息
    output-paths: /var/log/iam/iam-apiserver.log,stdout # 支持输出到多个输出，逗号分开。支持输出到标准输出（stdout）和文件。
    error-output-paths: /var/log/iam/iam-apiserver.error.log # zap内部(非业务)错误日志输出路径，多个输出，逗号分开
```

可以看到，iam-apiserver 将日志分别记录到了/var/log/iam/iam-apiserver.log和stdout中。所以，我们可以通过查看/var/log/iam/iam-apiserver.log日志文件，来查看报错信息：

```
$ tail -25 /var/log/iam/iam-apiserver.log
...
2021-09-09 15:42:35.231  INFO  apiserver  server/genericapiserver.go:88  GET    /version --> github.com/marmotedu/iam/internal/pkg/server.(*GenericAPIServer).InstallAPIs.func2 (10 handlers)
2021-09-09 15:42:35.232  INFO  apiserver  gorm@v1.21.12/gorm.go:202  mysql/mysql.go:75[error] failed to initialize database, got error dial tcp 127.0.0.1:3309: connect: connection refused
2021-09-09 15:42:35.232  FATAL  apiserver  apiserver/server.go:139  Failed to get cache instance: got nil cache server
github.com/marmotedu/iam/internal/apiserver.(*completedExtraConfig).New
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/server.go:139
github.com/marmotedu/iam/internal/apiserver.createAPIServer
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/server.go:66
github.com/marmotedu/iam/internal/apiserver.Run
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/run.go:11
github.com/marmotedu/iam/internal/apiserver.run.func1
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/app.go:46
github.com/marmotedu/iam/pkg/app.(*App).runCommand
  /home/going/workspace/golang/src/github.com/marmotedu/iam/pkg/app/app.go:278
github.com/spf13/cobra.(*Command).execute
  /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:856
github.com/spf13/cobra.(*Command).ExecuteC
  /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:974
github.com/spf13/cobra.(*Command).Execute
  /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:902
github.com/marmotedu/iam/pkg/app.(*App).Run
  /home/going/workspace/golang/src/github.com/marmotedu/iam/pkg/app/app.go:233
main.main
  /home/going/workspace/golang/src/github.com/marmotedu/iam/cmd/iam-apiserver/apiserver.go:24
runtime.main
  /home/going/go/go1.16.2/src/runtime/proc.go:225
```

我们再来看最后一种查看方式，通过 console 查看。当然，我们也可以直接通过 console 来看日志，这就需要我们在 Linux 终端前台运行 iam-apiserver（在第一步中，我们已经知道了启动命令）：

```
$ sudo /opt/iam/bin/iam-apiserver --config=/etc/iam/iam-apiserver.yaml
...
2021-09-09 15:47:00.660  INFO  apiserver  server/genericapiserver.go:88  GET    /debug/pprof/mutex --> github.com/gin-contrib/pprof.pprofHandler.func1 (10 handlers)
2021-09-09 15:47:00.660  INFO  apiserver  server/genericapiserver.go:88  GET    /debug/pprof/threadcreate --> github.com/gin-contrib/pprof.pprofHandler.func1 (10 handlers)
2021-09-09 15:47:00.660  INFO  apiserver  server/genericapiserver.go:88  GET    /version --> github.com/marmotedu/iam/internal/pkg/server.(*GenericAPIServer).InstallAPIs.func2 (10 handlers)
2021-09-09 15:47:00.661  INFO  apiserver  gorm@v1.21.12/gorm.go:202  mysql/mysql.go:75[error] failed to initialize database, got error dial tcp 127.0.0.1:3309: connect: connection refused
2021-09-09 15:47:00.661  FATAL  apiserver  apiserver/server.go:139  Failed to get cache instance: got nil cache server
github.com/marmotedu/iam/internal/apiserver.(*completedExtraConfig).New
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/server.go:139
github.com/marmotedu/iam/internal/apiserver.createAPIServer
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/server.go:66
github.com/marmotedu/iam/internal/apiserver.Run
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/run.go:11
github.com/marmotedu/iam/internal/apiserver.run.func1
  /home/going/workspace/golang/src/github.com/marmotedu/iam/internal/apiserver/app.go:46
github.com/marmotedu/iam/pkg/app.(*App).runCommand
  /home/going/workspace/golang/src/github.com/marmotedu/iam/pkg/app/app.go:278
github.com/spf13/cobra.(*Command).execute
  /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:856
github.com/spf13/cobra.(*Command).ExecuteC
  /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:974
github.com/spf13/cobra.(*Command).Execute
  /home/going/workspace/golang/pkg/mod/github.com/spf13/cobra@v1.2.1/command.go:902
github.com/marmotedu/iam/pkg/app.(*App).Run
  /home/going/workspace/golang/src/github.com/marmotedu/iam/pkg/app/app.go:233
main.main
  /home/going/workspace/golang/src/github.com/marmotedu/iam/cmd/iam-apiserver/apiserver.go:24
runtime.main
  /home/going/go/go1.16.2/src/runtime/proc.go:225

```
通过上面这 3 种查看方式，我们均能初步定位到服务异常的原因。

##### 使用 Go 调试工具 Delve 来定位问题
查看日志是最简单的排障方式，通过查看日志，我们可能定位出问题的根本原因，这种情况下问题就能得到快速的解决。但有些情况下，我们通过日志并不一定能定位出问题，例如：

- 程序异常，但是没有错误日志。
- 日志有报错，但只能判断问题的面，还不能精准找到问题的根因。

遇到上面这两种情况，我们都需要再进一步地定位问题。这时候，我们可以使用 Delve 调试工具来尝试定位问题。Delve 工具的用法你可以参考 [Delve 使用详解。](https://github.com/marmotedu/geekbang-go/blob/master/Delve%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.md)


##### 添加 Debug 日志定位问题
如果使用 Delve 工具仍然没有定位出问题，接下来你可以尝试最原始的方法：添加 Debug 日志来定位问题。这种方法具体可以分为两个步骤。


**第一步，在关键代码段添加 Debug 日志。**你需要根据自己对代码的理解来决定关键代码段。如果不确定哪段代码出问题，可以从请求入口处添加 Debug 日志，然后跟着代码流程一步步往下排查，并在需要的地方添加 Debug 日志。

例如，通过排查日志，我们定位到internal/apiserver/server.go:139位置的代码导致程序 FATAL，FATAL 原因是Failed to get cache instance: got nil cache server。cache server是nil，说明cache server没有被初始化。查看cache server初始化函数：

```go
func GetCacheInsOr(store store.Factory) (*Cache, error) {
    if store != nil {
        once.Do(func() {
            cacheServer = &Cache{store}
        })
    }

    if cacheServer == nil {
        return nil, fmt.Errorf("got nil cache server")
    }

    return cacheServer, nil
}
```
我们不难分析出，是store == nil导致cacheServer没有被初始化。再来看下 store 的初始化代码，并加一些 Debug 日志，如下图所示：

![img](https://static001.geekbang.org/resource/image/cc/15/cc50c340e4ff0e5401b3d89430456b15.png?wh=1669x1025)

我们添加完 Debug 代码后，就可以重新编译并运行程序了。这里有个小技巧：可以在错误返回的位置添加 Debug 日志，这样能大概率帮助你定位到出错的位置，例如：

```go
if err != nil {
  log.Debugf("DEBUG POINT - 1: %v", err)
  return err
}
```

**第二步，重新编译源码，并启动**。这里为了调试、看日志方便，我们直接在 Linux 终端的前端运行 iam-apiserver：

```
$ sudo /opt/iam/bin/iam-apiserver --config=/etc/iam/iam-apiserver.yaml
```
查看我们添加的 Debug 日志打印的内容，如下图所示：

![img](https://static001.geekbang.org/resource/image/61/03/61f31a4b9e45ed9079470e928f7d3b03.png?wh=1920x265)

从 Debug 日志中，可以看到用来创建 MySQL 实例的端口是错误的，正确的端口应该是3306，而不是3309。MySQL 服务器的端口是在 iam-apiserver.yaml 中配置的。修改 iam-apiserver.yaml 为正确的配置，并启动：

```
$ sudo /opt/iam/bin/iam-apiserver --config=/etc/iam/iam-apiserver.yaml

```
再次查看 console 日志，如下图所示：

![img](https://static001.geekbang.org/resource/image/f7/9d/f72b766b7504016259bef04eb03dac9d.png?wh=1920x271)

可以看到问题已经修复，dbIns不为nil，程序正常运行：

```
$ systemctl status iam-apiserver
● iam-apiserver.service - IAM APIServer
   Loaded: loaded (/etc/systemd/system/iam-apiserver.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2021-09-09 20:48:18 CST; 17s ago
     Docs: https://github.com/marmotedu/iam/blob/master/init/README.md
  Process: 255648 ExecStartPre=/usr/bin/mkdir -p /var/log/iam (code=exited, status=0/SUCCESS)
  Process: 255647 ExecStartPre=/usr/bin/mkdir -p /data/iam/iam-apiserver (code=exited, status=0/SUCCESS)
 Main PID: 255650 (iam-apiserver)
    Tasks: 5 (limit: 23724)
   Memory: 7.3M
   CGroup: /system.slice/iam-apiserver.service
           └─255650 /opt/iam/bin/iam-apiserver --config=/etc/iam/iam-apiserver.yaml
```
在这里，Active为active (running)状态。因为这些 Debug 日志能够协助你定位问题，从侧面说明这些日志是有用的，所以你可以保留这些 Debug 日志调用代码。


##### 解决问题
在定位问题阶段，我们已经找到了问题的原因，接下来就可以根据自己对业务、底层代码实现的掌握和理解，修复这个问题了。至于怎么修复，你需要结合具体情况来判断，并没有统一的流程和方法论，这里就不多介绍了。上面，我介绍了排查问题的思路和方法。接下来，我来向你展示 9 个在部署和使用 IAM 系统时容易遇到的问题，并提供解决方法。这些问题基本上都是由服务器环境引起的。

### IAM 常见故障及解决办法
问题一：安装 neovim，报 No match for argument: neovim 错误。
解决方法是安装 EPEL 源：

```
$ sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```

问题二：安装 protoc-gen-go 失败（超时、报错等）。这个故障出现，可能是因为你当前服务器所在的网络环境无法访问github.com，或者访问github.com速度太慢。解决方法是手动编译安装，方法如下：

```
$ git clone --depth 1 https://github.com/golang/protobuf $GOPATH/src/github.com/golang/protobuf
$ cd $GOPATH/src/github.com/golang/protobuf/protoc-gen-go
$ go install -v .
```

问题三：遇到xxx: permission denied这类的错误。出现这种错误，是因为你没有权限执行当前的操作。解决方法是排查自己是否有权限执行当前操作。如果没有权限，需要你切换到有权限的用户，或者放弃执行当前操作。为了说明问题，这里我举一个错误例子，并给出排查思路。例子的错误日志如下：

```
[going@VM-8-9-centos /]$ go get -u github.com/golang/protobuf/protoc-gen-go
go: could not create module cache: mkdir /golang: permission denied
[going@VM-8-9-centos /]$ sudo go get -u github.com/golang/protobuf/protoc-gen-go
sudo: go: command not found
```
上述错误中， 一共报了两个错误，分别是mkdir /golang: permission denied和sudo: go: command not found。我们先来看mkdir /golang: permission denied错误。

通过命令行提示符$可以知道，当前登陆用户是普通用户；通过报错mkdir /golang: permission denied可以知道go get -u github.com/golang/protobuf/protoc-gen-go命令底层执行了mkdir /golang，因为普通用户没有写/ 目录的权限，所以会报权限错误。解决方法是切换到用户的目录下，执行go get -u命令。

我们再来看下sudo: go: command not found错误。sudo命令会将命令执行的环境切换到root用户，root用户显然是没有安装go命令的，所以会导致command not found错误。解决方式是去掉 sudo ，直接执行 $ go get -u xxx 。

问题四：VimIDE 使用过程中，报各类错误。这里的报错原因跟环境有关系，安装 VimIDE 时的系统环境、包的版本等等，都可能会导致使用 VimIDE 报错。因为错误类型太多，没法一一说明，所以我建议你忽略这些错误，其实完全不影响后面的学习。

问题五：访问 iam-authz-server 的/v1/authz接口报{"code":100202,"message":"Signature is invalid"}。这时可能是签发的 Token 有问题，建议重新执行以下 5 个步骤：

重新登陆系统，并获取访问令牌：
```
$ token=`curl -s -XPOST -H'Content-Type: application/json' -d'{"username":"admin","password":"Admin@2021"}' http://127.0.0.1:8080/login | jq -r .token`
```
如果没有安装jq命令，可以执行sudo yum -y install jq命令来安装。

创建授权策略：

```
$ curl -s -XPOST -H"Content-Type: application/json" -H"Authorization: Bearer $token" -d'{"metadata":{"name":"authztest"},"policy":{"description":"One policy to rule them all.","subjects":["users:<peter|ken>","users:maria","groups:admins"],"actions":["delete","<create|update>"],"effect":"allow","resources":["resources:articles:<.*>","resources:printer"],"conditions":{"remoteIPAddress":{"type":"CIDRCondition","options":{"cidr":"192.168.0.1/16"}}}}}' http://127.0.0.1:8080/v1/policies
```
创建密钥，并从命令的输出中提取 secretID 和 secretKey：

```
$ curl -s -XPOST -H"Content-Type: application/json" -H"Authorization: Bearer $token" -d'{"metadata":{"name":"authztest"},"expires":0,"description":"admin secret"}' http://127.0.0.1:8080/v1/secrets
{"metadata":{"id":23,"name":"authztest","createdAt":"2021-04-08T07:24:50.071671422+08:00","updatedAt":"2021-04-08T07:24:50.071671422+08:00"},"username":"admin","secretID":"ZuxvXNfG08BdEMqkTaP41L2DLArlE6Jpqoox","secretKey":"7Sfa5EfAPIwcTLGCfSvqLf0zZGCjF3l8","expires":0,"description":"admin secret"}
```

生成访问 iam-authz-server 的 Token
iamctl 提供了 jwt sigin 命令，你可以根据 secretID 和 secretKey 签发 Token，方便你使用。签发 Token 的具体命令如下：

```
$ iamctl jwt sign ZuxvXNfG08BdEMqkTaP41L2DLArlE6Jpqoox 7Sfa5EfAPIwcTLGCfSvqLf0zZGCjF3l8 # iamctl jwt sign $secretID $secretKey
eyJhbGciOiJIUzI1NiIsImtpZCI6Ilp1eHZYTmZHMDhCZEVNcWtUYVA0MUwyRExBcmxFNkpwcW9veCIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYxNzg0NTE5NSwiaWF0IjoxNjE3ODM3OTk1LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MTc4Mzc5OTV9.za9yLM7lHVabPAlVQLCqXEaf8sTU6sodAsMXnmpXjMQ
```
测试资源授权是否通过：


```
$ curl -s -XPOST -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsImtpZCI6Ilp1eHZYTmZHMDhCZEVNcWtUYVA0MUwyRExBcmxFNkpwcW9veCIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYxNzg0NTE5NSwiaWF0IjoxNjE3ODM3OTk1LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MTc4Mzc5OTV9.za9yLM7lHVabPAlVQLCqXEaf8sTU6sodAsMXnmpXjMQ' -d'{"subject":"users:maria","action":"delete","resource":"resources:articles:ladon-introduction","context":{"remoteIPAddress":"192.168.0.5"}}' http://127.0.0.1:9090/v1/authz
{"allowed":true}
```


问题六：执行iamctl user list报error: {"code":100207,"message":"Permission denied"}。出现这种情况，可能是密码没有配置正确。你可以看下$HOME/.iam/iamctl.yaml配置文件中的用户名和密码配置的是不是 admin，以及 admin 的密码是否是Admin@2021。

问题七：在创建用户时报{"code":100101,"message":"Database error"}错误。出现这种情况，可能是用户名重了，建议换个新的用户名再次创建。


问题八：报No such file or directory、command not found、permission denied错误。遇到这类错误，要根据提示排查和解决问题。

- No such file or directory：确认文件是否存在，不存在的原因是什么。
- command not found：确认命令是否存在，如果不存在，可以重新安装命令。
- permission denied：确认是否有操作权限，如果没有，要切换到有权限的用户或者目录。

问题九：报iam-apiserver.service、/opt/iam/bin/iam-apiserver、/etc/iam/iam-apiserver.yaml文件不存在。我来介绍下这些文件的作用。

- /etc/systemd/system/iam-apiserver.service：iam-apiserver 的 sysmted Unit 文件。
- /opt/iam/bin/iam-apiserver：iam-apiserver 的二进制启动命令。
- /etc/iam/iam-apiserver.yaml：iam-apiserver 的配置文件。

如果某个文件不存在，那就需要你重新安装这些文件。我来分别介绍这三个文件的安装方法。

/etc/systemd/system/iam-apiserver.service安装方法：

```
$ cd $IAM_ROOT
$ ./scripts/genconfig.sh scripts/install/environment.sh init/iam-apiserver.service > iam-apiserver.service
$ sudo mv iam-apiserver.service /etc/systemd/system/
```
/opt/iam/bin/iam-apiserver安装方法：

```
$ cd $IAM_ROOT
$ source scripts/install/environment.sh
$ make build BINS=iam-apiserver
$ sudo cp _output/platforms/linux/amd64/iam-apiserver ${IAM_INSTALL_DIR}/bin
```

/etc/iam/iam-apiserver.yaml安装方法：

```
$ cd $IAM_ROOT
$ ./scripts/genconfig.sh scripts/install/environment.sh configs/iam-apiserver.yaml > iam-apiserver.yaml
$ sudo mv iam-apiserver.yaml ${IAM_CONFIG_DIR}
```

### 总结
这一讲，我以iam-apiserver服务为例，向你介绍了排障的基本流程：发现问题 -> 定位问题 -> 解决问题。你可以通过三种方式来发现问题。

- 检查服务状态：启动 iam-apiserver 服务后，执行systemctl status iam-apiserver 发现 iam-apiserver 启动失败，即Active的值不为active (running)。
- 功能异常：访问 iam-apiserver 服务，功能异常或者报错，例如接口返回值跟预期不一样；接口报错。
- 日志报错：在 iam-apiserver 的日志中发现一些WARN、ERROR、PANIC、FATAL等高级别的错误日志。

发现问题之后，你可以通过查看日志、使用 Go 调试工具 Delve 和添加 Debug 日志这三种方式来定位问题。
- 查看日志：查看日志是最简单的排障方式。
- 使用 Go 调试工具 Delve 来定位问题。
- 添加 Debug 日志：从程序入口处跟读代码，在关键位置处打印 Debug 日志，来定位问题。

找到问题根因之后，就要解决问题。你需要根据自己对业务、底层代码实现的掌握和理解，解决这个问题。最后，我向你展示了 9 个在部署和使用 IAM 系统时容易遇到的问题，并提供了解决方法，希望能给你一些切实的帮助。

## 特别放送 | Go Modules实战

今天我们更新一期特别放送作为加餐。在 特别放送 | Go Modules 依赖包管理全讲中，我介绍了 Go Modules 的知识，里面内容比较多，你可能还不知道具体怎么使用 Go Modules 来为你的项目管理 Go 依赖包。

这一讲，我就通过一个具体的案例，带你一步步学习 Go Modules 的常见用法以及操作方法，具体包含以下内容：

- 准备一个演示项目。
- 配置 Go Modules。
- 初始化 Go 包为 Go 模块。
- Go 包依赖管理。

### 准备一个演示项目
为了演示 Go Modules 的用法，我们首先需要一个 Demo 项目。假设我们有一个 hello 的项目，里面有两个文件，分别是 hello.go 和 hello_test.go，所在目录为/home/lk/workspace/golang/src/github.com/marmotedu/gopractise-demo/modules/hello。

hello.go 文件内容为：

```go
package hello

func Hello() string {
  return "Hello, world."
}
```

hello_test.go 文件内容为：

```go
package hello

import "testing"

func TestHello(t *testing.T) {
  want := "Hello, world."
  if got := Hello(); got != want {
    t.Errorf("Hello() = %q, want %q", got, want)
  }
}
```

这时候，该目录包含了一个 Go 包，但还不是 Go 模块，因为没有 go.mod 件。接下来，我就给你演示下，如何将这个包变成一个 Go 模块，并执行 Go 依赖包的管理操作。这些操作共有 10 个步骤，下面我们来一步步看下。

### 配置 Go Modules
打开 Go Modules
确保 Go 版本>=go1.11，并开启 Go Modules，可以通过设置环境变量export GO111MODULE=on开启。如果你觉得每次都设置比较繁琐，可以将export GO111MODULE=on追加到文件$HOME/.bashrc中，并执行 bash 命令加载到当前 shell 环境中。

设置环境变量
对于国内的开发者来说，需要设置export GOPROXY=https://goproxy.cn,direct，这样一些被墙的包可以通过国内的镜像源安装。如果我们有一些模块存放在私有仓库中，也需要设置 GOPRIVATE 环境变量。


因为 Go Modules 会请求 Go Checksum Database，Checksum Database 国内也可能会访问失败，可以设置export GOSUMDB=off来关闭 Checksum 校验。对于一些模块，如果你希望不通过代理服务器，或者不校验checksum，也可以根据需要设置 GONOPROXY 和 GONOSUMDB。


### 初始化 Go 包为 Go 模块
创建一个新模块

你可以通过go mod init命令，初始化项目为 Go Modules。 init 命令会在当前目录初始化并创建一个新的 go.mod 文件，也代表着创建了一个以项目根目录为根的 Go Modules。如果当前目录已经存在 go.mod 文件，则会初始化失败。

在初始化 Go Modules 时，需要告知go mod init要初始化的模块名，可以指定模块名，例如go mod init github.com/marmotedu/gopractise-demo/modules/hello。也可以不指定模块名，让init自己推导。下面我来介绍下推导规则。


如果有导入路径注释，则使用注释作为模块名，比如：

```
package hello // import "github.com/marmotedu/gopractise-demo/modules/hello"
```
则模块名为github.com/marmotedu/gopractise-demo/modules/hello。

如果没有导入路径注释，并且项目位于 GOPATH 路径下，则模块名为绝对路径去掉$GOPATH/src后的路径名，例如GOPATH=/home/lk/workspace/golang，项目绝对路径为/home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/modules/hello，则模块名为github.com/marmotedu/gopractise-demo/modules/hello。

初始化完成之后，会在当前目录生成一个 go.mod 文件：

```
$ cat go.mod
module github.com/marmotedu/gopractise-demo/modules/hello

go 1.14
```
文件内容表明，当前模块的导入路径为github.com/marmotedu/gopractise-demo/modules/hello，使用的 Go 版本是go 1.14。

如果要新增子目录创建新的 package，则 package 的导入路径自动为 模块名/子目录名 ：github.com/marmotedu/gopractise-demo/modules/hello/< sub-package-name>，不需要在子目录中再次执行go mod init。

比如，我们在 hello 目录下又创建了一个 world 包world/world.go，则 world 包的导入路径为github.com/marmotedu/gopractise-demo/modules/hello/world。

### Go 包依赖管理
增加一个依赖
Go Modules 主要是用来对包依赖进行管理的，所以这里我们来给 hello 包增加一个依赖rsc.io/quote：

```go
package hello

import "rsc.io/quote"

func Hello() string {
  return quote.Hello()
}
```
运行go test：

```
$ go test
go: finding module for package rsc.io/quote
go: downloading rsc.io/quote v1.5.2
go: found rsc.io/quote in rsc.io/quote v1.5.2
go: downloading rsc.io/sampler v1.3.0
PASS
ok    github.com/google/addlicense/golang/src/github.com/marmotedu/gopractise-demo/modules/hello  0.003s
```
当 go 命令在解析源码时，遇到需要导入一个模块的情况，就会去 go.mod 文件中查询该模块的版本，如果有指定版本，就导入指定的版本。

如果没有查询到该模块，go 命令会自动根据模块的导入路径安装模块，并将模块和其最新的版本写入 go.mod 文件中。在我们的示例中，go test将模块rsc.io/quote解析为rsc.io/quote v1.5.2，并且同时还下载了rsc.io/quote模块的两个依赖模块：rsc.io/quote和rsc.io/sampler。只有直接依赖才会被记录到 go.mod 文件中。

查看 go.mod 文件：

```
module github.com/marmotedu/gopractise-demo/modules/hello

go 1.14

require rsc.io/quote v1.5.2
```
再次执行go test：

```
$ go test
PASS
ok    github.com/marmotedu/gopractise-demo/modules/hello  0.003s
```

当我们再次执行go test时，不会再下载并记录需要的模块，因为 go.mod 目前是最新的，并且需要的模块已经缓存到了本地的$GOPATH/pkg/mod目录下。可以看到，在当前目录还新生成了一个 go.sum 文件：

```
$ cat go.sum
golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c h1:qgOY6WgZOaTkIIMiVjBQcw93ERBE4m30iBm00nkL0i8=
golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c/go.mod h1:NqM8EUOU14njkJ3fqMW+pc6Ldnwhi/IjpwHt7yyuwOQ=
rsc.io/quote v1.5.2 h1:w5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
rsc.io/quote v1.5.2/go.mod h1:LzX7hefJvL54yjefDEDHNONDjII0t9xZLPXsUe+TKr0=
rsc.io/sampler v1.3.0 h1:7uVkIFmeBqHfdjD+gZwtXXI+RODJ2Wc4O7MPEh/QiW4=
rsc.io/sampler v1.3.0/go.mod h1:T1hPZKmBbMNahiBKFy5HrXp6adAjACjK9JXDnKaTXpA=
```

go test在执行时，还可以添加-mod选项，比如go test -mod=vendor。-mod有 3 个值，我来分别介绍下。
- readonly：不更新 go.mod，任何可能会导致 go.mod 变更的操作都会失败。通常用来检查 go.mod 文件是否需要更新，例如用在 CI 或者测试场景。
- vendor：从项目顶层目录下的 vendor 中导入包，而不是从模块缓存中导入包，需要确保 vendor 包完整准确。
- mod：从模块缓存中导入包，即使项目根目录下有 vendor 目录。

如果go test执行时没有-mod选项，并且项目根目录存在 vendor 目录，go.mod 中记录的 go 版本大于等于1.14，此时go test执行效果等效于go test -mod=vendor。-mod标志同样适用于 go build、go install、go run、go test、go list、go vet 命令。

查看所有依赖模块
我们可以通过go list -m all命令查看所有依赖模块：

```
$ go list -m all
github.com/marmotedu/gopractise-demo/modules/hello
golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c
rsc.io/quote v1.5.2
rsc.io/sampler v1.3.0
```
可以看出，除了rsc.io/quote v1.5.2外，还间接依赖了其他模块。


更新依赖
通过go list -m all，我们可以看到模块依赖的golang.org/x/text模块版本是v0.0.0，我们可以通过go get命令，将其更新到最新版本，并观察测试是否通过：

```
$ go get golang.org/x/text
go: golang.org/x/text upgrade => v0.3.3
$ go test
PASS
ok    github.com/marmotedu/gopractise-demo/modules/hello  0.003s
```

go test命令执行后输出 PASS 说明升级成功，再次看下go list -m all和 go.mod 文件：
```
$ go list -m all
github.com/marmotedu/gopractise-demo/modules/hello
golang.org/x/text v0.3.3
golang.org/x/tools v0.0.0-20180917221912-90fa682c2a6e
rsc.io/quote v1.5.2
rsc.io/sampler v1.3.0
$ cat go.mod
module github.com/marmotedu/gopractise-demo/modules/hello

go 1.14

require (
  golang.org/x/text v0.3.3 // indirect
  rsc.io/quote v1.5.2
)
```
可以看到，golang.org/x/text包被更新到最新的 tag 版本 (v0.3.3)，并且同时更新了 go.mod 文件。// indirect说明golang.org/x/text是间接依赖。现在我们再尝试更新rsc.io/sampler并测试：

```
$ go get rsc.io/sampler
go: rsc.io/sampler upgrade => v1.99.99
go: downloading rsc.io/sampler v1.99.99
$ go test
--- FAIL: TestHello (0.00s)
    hello_test.go:8: Hello() = "99 bottles of beer on the wall, 99 bottles of beer, ...", want "Hello, world."
FAIL
exit status 1
FAIL  github.com/marmotedu/gopractise-demo/modules/hello  0.004s
```
测试失败，说明最新的版本v1.99.99与我们当前的模块不兼容，我们可以列出rsc.io/sampler所有可用的版本，并尝试更新到其他版本：

```
$ go list -m -versions rsc.io/sampler
rsc.io/sampler v1.0.0 v1.2.0 v1.2.1 v1.3.0 v1.3.1 v1.99.99

# 我们尝试选择一个次新的版本v1.3.1
$ go get rsc.io/sampler@v1.3.1
go: downloading rsc.io/sampler v1.3.1
$ go test
PASS
ok    github.com/marmotedu/gopractise-demo/modules/hello  0.004s
```
可以看到，更新到v1.3.1版本，测试是通过的。go get还支持多种参数，如下表所示：

![img](https://static001.geekbang.org/resource/image/2b/f6/2b80e94c1e91bb18dea9c20695b25bf6.jpg?wh=2248x1496)

添加一个新的 major 版本依赖
我们尝试添加一个新的函数func Proverb，该函数通过调用rsc.io/quote/v3的quote.Concurrency函数实现。首先，我们在 hello.go 文件中添加新函数：


```go
package hello

import (
  "rsc.io/quote"
  quoteV3 "rsc.io/quote/v3"
)

func Hello() string {
  return quote.Hello()
}

func Proverb() string {
  return quoteV3.Concurrency()
}
```
在 hello_test.go 中添加该函数的测试用例：

```go
func TestProverb(t *testing.T) {
    want := "Concurrency is not parallelism."
    if got := Proverb(); got != want {
        t.Errorf("Proverb() = %q, want %q", got, want)
    }
}
```
然后执行测试：

```
$ go test
go: finding module for package rsc.io/quote/v3
go: found rsc.io/quote/v3 in rsc.io/quote/v3 v3.1.0
PASS
ok    github.com/marmotedu/gopractise-demo/modules/hello  0.003s
```
测试通过，可以看到当前模块同时依赖了同一个模块的不同版本rsc.io/quote和rsc.io/quote/v3：

```
$ go list -m rsc.io/q...
rsc.io/quote v1.5.2
rsc.io/quote/v3 v3.1.0
```
升级到不兼容的版本
在上一步中，我们使用rsc.io/quote v1版本的Hello()函数。按照语义化版本规则，如果我们想升级major版本，可能面临接口不兼容的问题，需要我们变更代码。我们来看下rsc.io/quote/v3的函数：

```
$ go doc rsc.io/quote/v3
package quote // import "github.com/google/addlicense/golang/pkg/mod/rsc.io/quote/v3@v3.1.0"

Package quote collects pithy sayings.

func Concurrency() string
func GlassV3() string
func GoV3() string
func HelloV3() string
func OptV3() string
```
可以看到，Hello()函数变成了HelloV3()，这就需要我们变更代码做适配。因为我们都统一模块到一个版本了，这时候就不需要再为了避免重名而重命名模块，所以此时 hello.go 内容为：
```go
package hello

import (
  "rsc.io/quote/v3"
)

func Hello() string {
  return quote.HelloV3()
}

func Proverb() string {
  return quote.Concurrency()
}
```
执行go test：

```
$ go test
PASS
ok    github.com/marmotedu/gopractise-demo/modules/hello  0.003s
```
可以看到测试成功。

删除不使用的依赖
在上一步中，我们移除了rsc.io/quote包，但是它仍然存在于go list -m all和 go.mod 中，这时候我们要执行go mod tidy清理不再使用的依赖：

```
$ go mod tidy
[colin@dev hello]$ cat go.mod
module github.com/marmotedu/gopractise-demo/modules/hello

go 1.14

require (
  golang.org/x/text v0.3.3 // indirect
  rsc.io/quote/v3 v3.1.0
  rsc.io/sampler v1.3.1 // indirect
)
```
使用 vendor

如果我们想把所有依赖都保存起来，在 Go 命令执行时不再下载，可以执行go mod vendor，该命令会把当前项目的所有依赖都保存在项目根目录的 vendor 目录下，也会创建vendor/modules.txt文件，来记录包和模块的版本信息：

```
$ go mod vendor
$ ls
go.mod  go.sum  hello.go  hello_test.go  vendor  world
```

到这里，我就讲完了 Go 依赖包管理常用的 10 个操作。


### 总结
这一讲中，我详细介绍了如何使用 Go Modules 来管理依赖，它包括以下 Go Modules 操作：

- 打开 Go Modules；
- 设置环境变量；
- 创建一个新模块；
- 增加一个依赖；
- 查看所有依赖模块；
- 更新依赖；
- 添加一个新的 major 版本依赖；
- 升级到不兼容的版本；
- 删除不使用的依赖。
- 使用 vendor。


## 特别放送 | 分布式作业系统设计和实现


今天这一讲，我们来聊聊如何设计分布式作业系统。在实际的 Go 项目开发中，我们经常会遇到下面这两个功能需求：
- 想定时执行某个任务，例如在每天上午 10:00 清理数据库中的无用数据。
- 轮训数据库表的某个字段，根据字段的状态，进行一些异步的业务逻辑处理。比如，监听到 table_xxx.status = 'pending' 时，执行异步的初始化流程，完成之后设置 table_xxx.status='normal' 。

这两个在 Go 项目开发中非常常见、基础的功能需求，通常可以通过作业系统来实现。IAM 为了解决这种常见的功能需求，也开发了自己的作业系统。今天这一讲，我们就来看下 IAM 是如何实现作业系统的。

### 任务分类
在介绍作业系统之前，这里先来看下任务的分类。理解任务的分类，有助于我们理解作业系统执行的任务类型，进而有助于我们设计作业系统。

在我看来，任务可以分为下面 3 类。
- 定时任务：定时任务会在指定的时间点固定执行。只要到达执行任务的时间点，就会执行任务，而不管上一次任务是否完成。
- 间隔任务：上一次任务执行完，间隔一段时间（如 5 秒、5 分钟），再继续执行下一次任务。
- 间隔性定时任务：间隔任务的变种，从上一次任务开始执行时计时，只要间隔时间一到，便执行下一次任务，而不管上一次任务是否完成。

定时任务好理解，但间隔任务和间隔性定时任务不太好区分，它们的区别是：间隔任务会等待上一次任务执行完，间隔一段时间再执行下一次任务。而间隔性定时任务不会等待上一次任务执行完，只要间隔时间一到，便执行下一次任务。

三者的区别如下图所示：

![img](https://static001.geekbang.org/resource/image/cf/dd/cf323871d6946c31a82de6679c1178dd.jpg?wh=1920x1266)

在实际的项目开发中，我们经常会遇到这 3 类任务的需求。

### 作业系统的常见实现
在开始介绍 IAM 作业系统实现之前，有必要先介绍一下如何执行一个间隔 / 定时任务。只有了解了这些，才能更好地设计 IAM 的作业系统。通常来说，我们可以通过以下 4 种方式，来执行一个间隔 / 定时任务：

- 基于time 包提供的方法（例如time.Sleep、time.Ticker等 ）自己开发执行间隔 / 定时任务的服务。
- 一些 Go 包支持执行间隔 / 定时任务，可以直接使用这些 Go 包来执行间隔 / 定时任务，免去了自己开发作业调度部分的代码，例如github.com/robfig/cron 。
- 借助 Linux 的 crontab 执行定时任务。
- 使用开源的作业系统，并通过作业系统来执行间隔 / 定时任务，例如 distribworks/dkron。

上述 4 种方法，每一种都有自己的优缺点。采用第一种方法的话，因为一切都要从 0 开始实现，开发工作量大、开发效率低。我认为，因为已经有很多优秀的 cron 包可供使用了，没必要自己从 0 开发，可以直接使用这些 cron 包来执行周期 / 定时任务。IAM 项目便采用了这种方法。

接下来，我先介绍下第三种和第四种方法：使用 Linux crontab 和使用开源的 Go 作业系统。然后，我们再来重点看看 IAM 项目采用的第二种方法。

#### Linux crontab
crontab 是 Linux 系统自带的定时执行工具，可以在无需人工干预的情况下运行作业。crontab 通过 crond 进程来提供服务，crond 进程每分钟会定期检查是否有要执行的任务，如果有，则自动执行该任务。crond 进程通过读取 crontab 配置，来判断是否有任务执行，以及何时执行。

crond 进程会在下面这 3 个位置查找 crontab 配置文件。
- /var/spool/cron/：该目录存放用户（包括 root）的 crontab 任务，每个任务以登录名命名，比如 colin 用户创建的 crontab 任务对应的文件就是/var/spool/cron/colin。
- /etc/crontab：该目录存放由系统管理员创建并维护的 crontab 任务。
- /etc/cron.d/：该目录存放任何要执行的 crontab 任务。cron 进程执行时，会自动扫描该目录下的所有文件，按照文件中的时间设定执行后面的命令。

可以看到，如果想执行一个 crontab 任务，就需要确保 crond 运行，并配置 crontab 任务。具体分为以下两步：

第一步，确保 crond 进程正在运行。执行以下命令，查看 crond 进程运行状态：

```
$ systemctl status crond
● crond.service - Command Scheduler
   Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2021-11-17 07:11:27 CST; 2 days ago
 Main PID: 9182 (crond)
    Tasks: 1
   Memory: 728.0K
   CGroup: /system.slice/crond.service
           └─9182 /usr/sbin/crond -n
```

Active: active (running)说明 crond 进程正在运行，否则可以执行systemctl start crond启动 crond 进程。


第二步，配置 crontab 任务。可以通过crontab -e来编辑配置文件，例如执行crontab -e后进入 vi 交互界面，并配置以下 crontab 任务：

```
# 每分钟输出时间到文件 /tmp/test.txt
*  *  *  *  * echo `date` >> /tmp/test.txt

# 每隔 2 分钟同步一次互联网时间
*/2 * * * * /usr/bin/ntpstat time.windows.com > /dev/null 2>&1
```
编辑后的配置文件保存在/var/spool/cron/$USER文件中。你可以通过crontab -l或者sudo cat /var/spool/cron/$USER来查看，例如：

```
$ crontab -l
# 每分钟输出时间到文件/tmp/test.txt
*  *  *  *  * echo `date` >> /tmp/test.txt

# 每隔 2 分钟同步一次互联网时间
*/2 * * * * /usr/bin/ntpstat time.windows.com > /dev/null 2>&1
```
如果想删除所有的 crontab 任务，你可以执行crontab -r命令。配置的 crontab 任务需要遵循 crontab 的时间格式，格式如下：

```
.---------------- minute (0 - 59)    
|  .------------- hour (0 - 23)    
|  |  .---------- day of month (1 - 31)    
|  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...    
|  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat    
|  |  |  |  |    
*  *  *  *  * <command to be executed>
```
可以看到，crontab 只能精确到分钟，不能精确到秒。下面是一些常用的 crontab 时间格式，你可以参考，来加深理解：

```
# 每分钟执行一次 <command>            
* * * * * <command> # * 代表所有可能的值

# 每隔一小时执行一次 <command>
* */1 * * * <command> # / 表示频率

# 每小时的 15 和 30 分各执行一次 <command>
15,45 * * * * <command> # , 表示并列

# 在每天上午 8- 11 时中间每小时 15，45 分各执行一次 <command>
15,45 8-11 * * * <command> # - 表示范围

# 每个星期一的上午 8 点到 11 点的第 3 和第 15 分钟执行一次 <command>
3,15 8-11 * * 1 <command>

# 每隔两天的上午 8 点到 11 点的第 3 和第 15 分钟执行一次 <command>
3,15 8-11 */2 * * <command>
```

使用 crontab 执行周期 / 定时任务的优点是不用做任何开发，只需要配置 crontab 任务即可。至于缺点也很明显，主要有下面这几个：
- 不能精确到秒。
- 需要手动编写可执行命令。这些可执行命令跟项目分离，没办法复用项目提供的包、函数等能力。如果想执行跟项目关系紧密的作业，开发起来不方便。
- 单点，如果 crond 进程异常，周期 / 定时任务就没法继续执行。你可能想说：可以在两台机器上配置并执行相同的周期 / 定时任务。但是这样做会有问题，因为两台机器同时执行相同的任务，可能会彼此造成冲突或状态不一致。
- 没办法实现间隔任务和间隔性定时任务。

### 使用开源的作业系统
除了使用 Linux 系统自带的 crontab 之外，我们还可以使用一些业界优秀的开源作业系统。这里，我列出了一些比较受欢迎的 Go 语言开发的作业系统。之所以只选择 Go 语言开发的项目，一方面是想丰富你的 Go 语言生态，另一方面，同种语言也有助于你学习、改造这些项目。

distribworks/dkron。dkron 是一个分布式、启动迅速、带容错机制的定时作业系统，支持 crontab 表达式。它具有下面这些核心特性。
- 易用：可以通过易操作、漂亮的 Web 界面来管理作业。
- 可靠：具备容错机制，一个节点不可用，其他节点可继续执行作业。
- 高可扩展性：能够处理大量的计划作业和数千个节点。

ouqiang/gocron。gocron 是国人开发的轻量级定时任务集中调度和管理系统, 用于替代 Linux-crontab。它具有下面这些核心特性。
- 具有 Web 界面管理定时任务。
- 支持 crontab 时间格式，并精确到秒。
- 支持 shell 命令和 HTTP 请求两种任务格式。
- 具有任务超时机制、任务依赖机制、任务执行失败可重试机制。
- 支持查看任务执行日志，并支持用邮件、Slack、Webhook 等方式通知任务执行结果。

shunfei/cronsun。cronsun 是一个分布式作业系统，单个节点同 crontab 近似。它具有下面这些核心特性。
- 具有 Web 界面，方便对多台服务器上的定时任务进行集中式管理。
- 任务调度时间粒度支持到秒级别。
- 任务执行失败可重试。
- 任务可靠性保障（从 N 个节点里面挑一个可用节点来执行任务）。
- 任务日志查看。
- 任务失败邮件告警（也支持自定义 http 告警接口）。

那么，这么多的开源项目该如何选择呢？这里建议你选择 distribworks/dkron 。原因是 distribworks/dkron  Star 数很多，而且功能齐全易用、文档丰富。当然，在实际开发中，你最好也对其他开源项目进行调研，根据需要选择一个最适合自己的开源项目。

使用这些作业系统的优点是不用开发、功能比 crontab 更强大，有些还是分布式的作业系统，具备容灾能力。但缺点也很明显：
- 这些作业系统支持的任务种类有限，比如一般会支持通过 shell 脚本及发送 HTTP 请求的方式来执行任务。不管哪种方式，实现都跟项目分离，在开发跟项目结合紧密的任务插件时不是很简单、高效。
- 很多时候我们只会使用其中一部分能力，或者仅有一到两个项目会使用到这类系统，但我们还要部署并维护这些作业系统，工作量大，收益小。
- 没办法实现间隔任务。

使用 Linux 的 crontab 和使用开源的 Go 作业系统，这两种方法的缺点都很明显。鉴于这些缺点，IAM 系统选择使用现有的 cron 库封装自己的任务框架，并基于这个框架开发任务。IAM 项目选择了robfig/cron库，原因是 cron 库 Star 数最多，且功能丰富、使用简单。另外 IAM 还使用github.com/go-redsync/redsync实现了基于 Redis 的分布式互斥锁。所以，在开始介绍 IAM 作业系统实现前，我先来简单介绍下如何使用这两个包。


#### github.com/robfig/cron使用介绍
github.com/robfig/cron是一个可以实现类似 Linux crontab 定时任务的 cron 包，但是 cron 包支持到秒。


**cron 包支持的时间格式**
cron 包支持 crontab 格式和固定间隔格式这两种时间格式，下面我来分别介绍下。crontab 格式的时间格式，支持的匹配符跟 crontab 保持一致。时间格式如下：

```
 ┌─────────────second 范围 (0 - 60)
 │ ┌───────────── min (0 - 59)
 │ │ ┌────────────── hour (0 - 23)
 │ │ │ ┌─────────────── day of month (1 - 31)
 │ │ │ │ ┌──────────────── month (1 - 12)
 │ │ │ │ │ ┌───────────────── day of week (0 - 6) (0 to 6 are Sunday to
 │ │ │ │ │ │                  Saturday)
 │ │ │ │ │ │
 │ │ │ │ │ │
 * * * * * *   
```
第二种是固定间隔格式，例如@every <duration>。duration是一个可以被time.ParseDuration解析的字符串，例如@every 1h30m10s表示任务每隔 1 小时 30 分 10 秒会被执行。这里要注意，间隔不考虑任务的运行时间。例如，如果任务需要 3 分钟运行，并且计划每 5 分钟运行一次，则每次运行之间只有 2 分钟的空闲时间。

**cron 包使用示例**
cron 包的使用方法也很简单，下面是一个简单的使用示例：

```go
package main

import (
  "fmt"

  "github.com/robfig/cron/v3"
)

func helloCron() {
  fmt.Println("hello cron")
}

func main() {
  fmt.Println("starting go cron...")

  // 创建一个cron实例
  cron := cron.New(cron.WithSeconds(), cron.WithChain(cron.SkipIfStillRunning(nil), cron.Recover(nil)))

  // 添加一个定时任务
  cron.AddFunc("*  *  *  *  *  *", helloCron)

  // 启动计划任务
  cron.Start()

  // 关闭着计划任务, 但是不能关闭已经在执行中的任务.
  defer cron.Stop()

  select {} // 查询语句，保持程序运行，在这里等同于for{}
}
```
在上面的代码中，通过 cron.New 函数调用创建了一个 cron 实例；接下来通过 cron 实例的 AddFunc 方法，给 cron 实例添加了一个定时任务：每分钟执行一次 helloCron 函数；最后通过 cron 实例的 Start 方法启动定时任务。在程序退出时，还执行了 cron.Stop() 关闭定时任务。


**拦截器**
cron 包还支持安装一些拦截器，这些拦截器可以实现以下功能：

- 从任务的 panic 中恢复（cron.Recover()）。
- 如果上一次任务尚未完成，则延迟下一次任务的执行（cron.DelayIfStillRunning()）。
- 如果上一次任务尚未完成，则跳过下一次任务的执行（cron.SkipIfStillRunning()）。
- 记录每个任务的调用（cron.WithLogger()）。
- 任务完成时通知。

如果想使用这些拦截器，只需要在创建 cron 实例时，传入相应的 Option 即可，例如：

```
cron := cron.New(cron.WithSeconds(), cron.WithChain(cron.SkipIfStillRunning(nil), cron.Recover(nil)))
```

#### github.com/go-redsync/redsync使用介绍
redsync 可以实现基于 Redis 的分布式锁，使用起来也比较简单，我们直接来看一个使用示例：

```go
package main

import (
  goredislib "github.com/go-redis/redis/v8"
  "github.com/go-redsync/redsync/v4"
  "github.com/go-redsync/redsync/v4/redis/goredis/v8"
)

func main() {
  // Create a pool with go-redis (or redigo) which is the pool redisync will
  // use while communicating with Redis. This can also be any pool that
  // implements the `redis.Pool` interface.
  client := goredislib.NewClient(&goredislib.Options{
    Addr: "localhost:6379",
  })
  pool := goredis.NewPool(client) // or, pool := redigo.NewPool(...)

  // Create an instance of redisync to be used to obtain a mutual exclusion
  // lock.
  rs := redsync.New(pool)

  // Obtain a new mutex by using the same name for all instances wanting the
  // same lock.
  mutexname := "my-global-mutex"
  mutex := rs.NewMutex(mutexname)

  // Obtain a lock for our given mutex. After this is successful, no one else
  // can obtain the same lock (the same mutex name) until we unlock it.
  if err := mutex.Lock(); err != nil {
    panic(err)
  }

  // Do your work that requires the lock.

  // Release the lock so other processes or threads can obtain a lock.
  if ok, err := mutex.Unlock(); !ok || err != nil {
    panic("unlock failed")
  }
}
```
上面的代码，创建了一个 redsync.Redsync 实例，并使用 redsync.Redsync 提供的 NewMutex 方法，创建了一个分布式锁实例 mutex。通过 mutex.Lock() 加锁，通过 mutex.Unlock() 释放锁。


### IAM 作业系统特点
在开发 IAM 的作业系统之前，我们需要先梳理好 IAM 要实现的任务。IAM 需要实现以下两个间隔任务：
- 每隔一段时间从 policy_audit 表中清理超过指定天数的授权策略。
- 每隔一段时间禁用超过指定天数没有登录的用户。

结合上面提到的作业系统的缺点，这里将我们需要设计的作业系统的特点总结如下：
- 分布式的作业系统，当有多个实例时，确保同一时刻只有 1 个实例在工作。
- 跟项目契合紧密，能够方便地复用项目提供的包、函数等能力，提高开发效率。
- 能够执行定时任务、间隔任务、间隔性定时任务这 3 种类型的任务。
- 可插件化地加入新的周期 / 定时任务。

### IAM 作业系统实现
介绍完 IAM 作业系统使用到的两个 Go 包和 IAM 作业系统的特点，下面我来正式讲解 IAM 作业系统的实现。

IAM 的作业系统服务名叫 iam-watcher。watcher 是观察者的意思，里面的任务主要是感知一些状态，并执行相应的任务，所以叫 watcher。iam-watcher main 函数位于[cmd/iam-watcher/watcher.go](https://github.com/marmotedu/iam/blob/v1.2.0/cmd/iam-watcher/watcher.go)文件中。应用框架跟 iam-apiserver、iam-authz-server、iam-pump 保持高度一致，这里就不再介绍了。

整个 iam-watcher 服务的核心实现位于internal/watcher/server.go文件中，在 server.go 文件中调用了newWatchJob，创建了一个github.com/robfig/cron.Cron类型的 cron 实例，newWatchJob 代码如下：

```go
func newWatchJob(redisOptions *genericoptions.RedisOptions, watcherOptions *options.WatcherOptions) *watchJob {    
    logger := cronlog.NewLogger(log.SugaredLogger())                                                               

    client := goredislib.NewClient(&goredislib.Options{                     
        Addr:     fmt.Sprintf("%s:%d", redisOptions.Host, redisOptions.Port),    
        Username: redisOptions.Username,                                         
        Password: redisOptions.Password,    
    })                                                                  

    pool := goredis.NewPool(client)                                                                            
    rs := redsync.New(pool)                                                

    cron := cron.New(                                                             
        cron.WithSeconds(),                     
        cron.WithChain(cron.SkipIfStillRunning(logger), cron.Recover(logger)),                                      
    )                                                                             

    return &watchJob{                                             
        Cron:   cron,                                                            
        config: watcherOptions,                                                   
        rs:     rs,                             
    }                                                             
}
```

上述代码创建了以下两种类型的实例。
- github.com/robfig/cron.Cron：基于github.com/robfig/cron包实现的作业系统，可以支持定时任务、间隔任务、间隔性定时任务 3 种类型的任务。
- github.com/go-redsync/redsync.Redsync：基于 Redis 的分布式互斥锁。

这里需要注意，创建 cron 实例时需要增加cron.SkipIfStillRunning() Option，SkipIfStillRunning可以使 cron 任务在上一个任务还没执行完时，跳过下一个任务的执行，以此实现间隔任务的效果。

创建实例后，通过[addWatchers()](https://github.com/marmotedu/iam/blob/v1.2.0/internal/watcher/watcher.go)来注册 cron 任务。addWatchers 函数代码如下：

```go
func (w *watchJob) addWatchers() *watchJob {                            
    for name, watcher := range watcher.ListWatchers() {
        // log with `{"watcher": "counter"}` key-value to distinguish which watcher the log comes from.
        ctx := context.WithValue(context.Background(), log.KeyWatcherName, name)

        if err := watcher.Init(ctx, w.rs.NewMutex(name, redsync.WithExpiry(2*time.Hour)), w.config); err != nil {
            log.Panicf("construct watcher %s failed: %s", name, err.Error())    
        }                                                              

        _, _ = w.AddJob(watcher.Spec(), watcher)                            
    }           

    return w                                    
}  

```
上述函数会调用watcher.ListWatchers()列出所有的 watcher，并在 for 循环中将这些 watcher 添加到 cron 调度引擎中。watcher 定义如下：

```go
type IWatcher interface {                                               
    Init(ctx context.Context, rs *redsync.Mutex, config interface{}) error
    Spec() string                                                                                      
    cron.Job                                                                    
}

type Job interface {                                                    
    Run()                                                                 
}
```

也就是说，一个 watcher 是实现了以下 3 个方法的结构体：Init()，用来初始化 wacther。Spec()，用来返回 Cron 实例的时间格式，支持 Linux crontab 时间格式和@every 1d类型的时间格式。Run()，用来运行任务。


IAM 实现了两个 watcher：[task](https://github.com/marmotedu/iam/blob/v1.2.0/internal/watcher/watcher/task/watcher.go)：禁用超过X天还没有登录过的用户，X可由 iam-watcher.yaml 配置文件中的watcher.task.max-inactive-days配置项来配置。[clean](https://github.com/marmotedu/iam/blob/v1.2.0/internal/watcher/watcher/clean/watcher.go)：清除policy_audit表中超过X天数后的授权策略，X可由 iam-watcher.yaml 配置文件中的watcher.clean.max-reserve-days配置项来配置。

创建完 cron 实例后，就可以在Run 函数中启动 cron 任务。Run 函数代码如下：


```go
func (s preparedWatcherServer) Run() error {
  stopCh := make(chan struct{})
  s.gs.AddShutdownCallback(shutdown.ShutdownFunc(func(string) error {
    // wait for running jobs to complete.
    ctx := s.cron.Stop()
    select {
    case <-ctx.Done():
      log.Info("cron jobs stopped.")
    case <-time.After(3 * time.Minute):
      log.Error("context was not done after 3 minutes.")
    }
    stopCh <- struct{}{}

    return nil
  }))

  // start shutdown managers
  if err := s.gs.Start(); err != nil {
    log.Fatalf("start shutdown manager failed: %s", err.Error())
  }

  log.Info("star to run cron jobs.")
  s.cron.Start()

  // blocking here via channel to prevents the process exit.
  <-stopCh

  return nil
}
```
上述代码，通过s.cron.Start()代码调用来启动 cron 实例，执行 cron 任务。

这里需要注意，我们还需要实现优雅关停功能，也就是当程序结束时，等待正在执行的作业都结束后，再终止进程。s.cron.Stop()会返回context.Context类型的变量，用来告知调用者 cron 任务何时结束，以使调用者终止进程。在 cron 任务都执行完毕或者超时 3 分钟后，会往 stopCh 通道中写入一条 message，<-stopCh 会结束阻塞状态，进而退出 iam-watcher 进程。


#### task watcher 实现解读
task watcher 的实现位于internal/watcher/watcher/task/watcher.go文件中，该文件定义了一个taskWatcher结构体：

```go
type taskWatcher struct {    
    ctx             context.Context    
    mutex           *redsync.Mutex    
    maxInactiveDays int          
}
```

taskWatcher实现了IWatcher接口。在程序启动时，通过 init 函数将taskWatcher注册到internal/watcher/watcher/registry.go中定义的全局变量registry中，通过func ListWatchers() map[string]IWatcher函数返回所有注册的 watcher。

这里需要注意，所有的 watcher 在internal/watcher/watcher/all/all.go文件中以匿名包的形式被导入，从而触发 watcher 所在包的 init 函数的执行。init 函数通过调用watcher.Register("clean", &cleanWatcher{})将 watcher 注册到registry变量中。all.go文件中导入匿名包代码如下：

```go
import (                                                           
    _ "github.com/marmotedu/iam/internal/watcher/watcher/clean"    
    _ "github.com/marmotedu/iam/internal/watcher/watcher/task"    
)  
```
这样做的好处是，不需要修改任何 iam-watcher 的框架代码，就可以插件化地注册一个新的 watcher。不改动 iam-watcher 的主体代码，能够使我们以最小的改动添加一个新的 watcher。例如，我们需要新增一个 cleansecret  watcher，只需要执行以下两步即可：
- 在internal/watcher/watcher目录下新建一个cleansecret目录，并实现cleanSecretWatcher。
- 在internal/watcher/watcher/all/all.go文件中以匿名的形式导入github.com/marmotedu/iam/internal/watcher/watcher/cleansecret包。在taskWatcher的Run()方法中，我们通过以下代码，来确保即使有多个 iam-watcher 实例，也只有一个 task watcher 在执行：

```go
    if err := tw.mutex.Lock(); err != nil {               
        log.L(tw.ctx).Info("taskWatcher already run.")    

        return    
    }                 
    defer func() {                                      
        if _, err := tw.mutex.Unlock(); err != nil {    
            log.L(tw.ctx).Errorf("could not release taskWatcher lock. err: %v", err)    

            return    
        }    
    }()
```

我们在taskWatcher的Run()方法中，查询出所有的用户，并对比loginedAt字段中记录的时间和当前时间，来判断是否需要禁止用户。loginedAt字段记录了用户最后一次登录的时间。

通过 task watcher 的实现，可以看到：在 task watcher 中，我们使用了 IAM 项目提供的mysql.GetMySQLFactoryOr函数、log 包，以及 Options 配置，这使我们可以很方便地开发一个跟项目紧密相关的任务。

### 总结
在 Go 项目开发中，我们经常会需要执行一些间隔 / 定时任务，这时我们就需要一个作业系统。我们可以使用 Linux 提供的 crontab 执行定时任务，还可以自己搭建一个作业系统，并在上面执行我们的间隔 / 定时任务。但这些方法都有一些缺点，比如跟项目独立、无法执行间隔任务等。所以，这时候比较好的方式是基于开源的优秀 cron 包，来实现一个作业系统，并基于这个作业系统开发任务插件。

IAM 基于github.com/robfig/cron包和github.com/go-redsync/redsync包，实现了自己的分布式作业系统 iam-watcher。iam-watcher 可以插件化地添加定时任务、间隔任务、间隔性定时任务。至于它的具体实现，你可以跟读 iam-watcher 服务的代码，其 main 函数位于cmd/iam-watcher/watcher.go文件中。





















































































































































































































































































































































































































































































































































































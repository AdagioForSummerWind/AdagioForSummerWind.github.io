---
title: "Go_base_04"
date: 2022-01-06T09:16:37+08:00
lastmod: 2022-01-06
tags: [go grammar]
categories: [Go]
slug: Go_Method
draft: true
---
> 参考学习go语言中文网、C语言中文网、golang官方文档等

# 方法
## 方法本质
一个以方法的 receiver 参数作为第一个参数的普通函数。
```go

package main

import (
    "fmt"
    "time"
)

type field struct {
    name string
}

func (p *field) print() {
    fmt.Println(p.name)
}

func main() {
    data1 := []*field{ {"one"}, {"two"}, {"three"} }
    for _, v := range data1 {
        go v.print()
    }

    data2 := []field{ {"four"}, {"five"}, {"six"} }
    for _, v := range data2 {
        go v.print()
    }

    time.Sleep(3 * time.Second)
}
```
```go

one
two
three
six
six
six
```
为什么会是这样的输出？

根据 Go 方法的本质，也就是一个以方法的 receiver 参数作为第一个参数的普通函数，对这个程序做个等价变换

```go

type field struct {
    name string
}

func (p *field) print() {
    fmt.Println(p.name)
}

func main() {
    data1 := []*field{ {"one"}, {"two"}, {"three"} }
    for _, v := range data1 {
        go (*field).print(v)
    }

    data2 := []field{ {"four"}, {"five"}, {"six"} }
    for _, v := range data2 {
        go (*field).print(&v)
    }

    time.Sleep(3 * time.Second)
}
```
我们把对 field 的方法 print 的调用，替换为 Method Expression 形式，替换前后的程序输出结果是一致的

使用 go 关键字启动一个新 Goroutine 时，method expression 形式的 print 函数是如何绑定参数的：
- 迭代 data1 时，由于 data1 中的元素类型是 field 指针 (*field)，因此赋值后 v 就是元素地址，与 print 的 receiver 参数类型相同，每次调用 (*field).print 函数时直接传入的 v 即可，实际上传入的也是各个 field 元素的地址；
- 迭代 data2 时，由于 data2 中的元素类型是 field（非指针），与 print 的 receiver 参数类型不同，因此需要将其取地址后再传入 (*field).print 函数。这样每次传入的 &v 实际上是变量 v 的地址，而不是切片 data2 中各元素的地址。
## 方法定义
Golang 方法总是绑定对象实例，并隐式将实例作为第一实参 (receiver)。
- 只能为当前包内命名类型定义方法。
- 参数 receiver 可任意命名。如方法中未曾使用 ，可省略参数名。
- 参数 receiver 类型可以是 T 或 *T。基类型 T 不能是接口或指针。 
- 不支持方法重载，receiver 只是参数签名的组成部分。
- 可用实例 value 或 pointer 调用全部方法，编译器自动转换。   

方法表达式（Method Expression）:
- 直接以类型名 T 调用方法的表达方式，被称为 Method Expression。通过 Method Expression 这种形式，类型 T 只能调用 T 的方法集合（Method Set）中的方法，同理类型 *T 也只能调用 *T 的方法集合中的方法

一个方法就是一个包含了接受者的函数，接受者可以是命名类型或者结构体类型的一个值或者是一个指针。

所有给定类型的方法属于该类型的方法集。

```go
 func (recevier type) methodName(参数列表)(返回值列表){}
//参数和返回值可以省略 

package main

type Test struct{}

// 无参数、无返回值
func (t Test) method0() {

}

// 单参数、无返回值
func (t Test) method1(i int) {

}

// 多参数、无返回值
func (t Test) method2(x, y int) {

}

// 无参数、单返回值
func (t Test) method3() (i int) {
    return
}

// 多参数、多返回值
func (t Test) method4(x, y int) (z int, err error) {
    return
}

// 无参数、无返回值
func (t *Test) method5() {

}

// 单参数、无返回值
func (t *Test) method6(i int) {

}

// 多参数、无返回值
func (t *Test) method7(x, y int) {

}

// 无参数、单返回值
func (t *Test) method8() (i int) {
    return
}

// 多参数、多返回值
func (t *Test) method9(x, y int) (z int, err error) {
    return
}

func main() {}  
```
下面定义一个结构体类型和该类型的一个方法：
```go
package main

import (
    "fmt"
)

//结构体
type User struct {
    Name  string
    Email string
}

//方法
func (u User) Notify() {
    fmt.Printf("%v : %v \n", u.Name, u.Email)
}
func main() {
    // 值类型调用方法
    u1 := User{"golang", "golang@golang.com"}
    u1.Notify()
    // 指针类型调用方法
    u2 := User{"go", "go@go.com"}
    u3 := &u2
    u3.Notify()
} 
output：
    golang : golang@golang.com 
    go : go@go.com    
```
解释：
首先我们定义了一个叫做 User 的结构体类型，然后定义了一个该类型的方法叫做 Notify，该方法的接受者是一个 User 类型的值。要调用 Notify 方法我们需要一个 User 类型的值或者指针。

在这个例子中当我们使用指针时，Go 调整和解引用指针使得调用可以被执行。**注意**，当接受者不是一个指针时，该方法操作对应接受者的值的副本(意思就是即使你使用了指针调用函数，但是函数的接受者是值类型，所以函数内部操作还是对副本的操作，而不是指针操作。

我们修改 Notify 方法，让它的接受者使用指针类型：
```go
package main

import (
    "fmt"
)

//结构体
type User struct {
    Name  string
    Email string
}

//方法
func (u *User) Notify() {
    fmt.Printf("%v : %v \n", u.Name, u.Email)
}
func main() {
    // 值类型调用方法
    u1 := User{"golang", "golang@golang.com"}
    u1.Notify()
    // 指针类型调用方法
    u2 := User{"go", "go@go.com"}
    u3 := &u2
    u3.Notify()
}  
output：

    golang : golang@golang.com 
    go : go@go.com  
```
 
**注意**：当接受者是指针时，即使用值类型调用那么函数内部也是对指针的操作。

方法不过是一种特殊的函数，只需将其还原，就知道 receiver T 和 *T 的差别。
```go
package main

import "fmt"

type Data struct {
    x int
}

func (self Data) ValueTest() { // func ValueTest(self Data);
    fmt.Printf("Value: %p\n", &self)
}

func (self *Data) PointerTest() { // func PointerTest(self *Data);
    fmt.Printf("Pointer: %p\n", self)
}

func main() {
    d := Data{}
    p := &d
    fmt.Printf("Data: %p\n", p)

    d.ValueTest()   // ValueTest(d)
    d.PointerTest() // PointerTest(&d)

    p.ValueTest()   // ValueTest(*p)
    p.PointerTest() // PointerTest(p)
}  
output:
    Data: 0xc42007c008
    Value: 0xc42007c018
    Pointer: 0xc42007c008
    Value: 0xc42007c020
    Pointer: 0xc42007c008  
```
普通函数与方法的区别
1.对于普通函数，接收者为值类型时，不能将指针类型的数据直接传递，反之亦然。

2.对于方法（如struct的方法），接收者为值类型时，可以直接用指针类型的变量调用方法，反过来同样也可以。

```go
package main

//普通函数与方法的区别（在接收者分别为值类型和指针类型的时候）

import (
    "fmt"
)

//1.普通函数
//接收值类型参数的函数
func valueIntTest(a int) int {
    return a + 10
}

//接收指针类型参数的函数
func pointerIntTest(a *int) int {
    return *a + 10
}

func structTestValue() {
    a := 2
    fmt.Println("valueIntTest:", valueIntTest(a))
    //函数的参数为值类型，则不能直接将指针作为参数传递
    //fmt.Println("valueIntTest:", valueIntTest(&a))
    //compile error: cannot use &a (type *int) as type int in function argument

    b := 5
    fmt.Println("pointerIntTest:", pointerIntTest(&b))
    //同样，当函数的参数为指针类型时，也不能直接将值类型作为参数传递
    //fmt.Println("pointerIntTest:", pointerIntTest(b))
    //compile error:cannot use b (type int) as type *int in function argument
}

//2.方法
type PersonD struct {
    id   int
    name string
}

//接收者为值类型
func (p PersonD) valueShowName() {
    fmt.Println(p.name)
}

//接收者为指针类型
func (p *PersonD) pointShowName() {
    fmt.Println(p.name)
}

func structTestFunc() {
    //值类型调用方法
    personValue := PersonD{101, "hello world"}
    personValue.valueShowName()
    personValue.pointShowName()

    //指针类型调用方法
    personPointer := &PersonD{102, "hello golang"}
    personPointer.valueShowName()
    personPointer.pointShowName()

    //与普通函数不同，接收者为指针类型和值类型的方法，指针类型和值类型的变量均可相互调用
}

func main() {
    structTestValue()
    structTestFunc()
}  
output：
    valueIntTest: 12
    pointerIntTest: 15
    hello world
    hello world
    hello golang
    hello golang  
```
## 匿名字段
**Golang匿名字段 ：可以像字段成员那样访问匿名字段方法，编译器负责查找。**
```go
package main

import "fmt"

type User struct {
    id   int
    name string
}

type Manager struct {
    User
}

func (self *User) ToString() string { // receiver = &(Manager.User)
    return fmt.Sprintf("User: %p, %v", self, self)
}

func main() {
    m := Manager{User{1, "Tom"} }
    fmt.Printf("Manager: %p\n", &m)
    fmt.Println(m.ToString())
}  
output:
    Manager: 0xc42000a060
    User: 0xc42000a060, &{1 Tom}
```
通过匿名字段，可获得和继承类似的复用能力。依据编译器查找次序，只需在外层定义同名方法，就可以实现 “override”。
```go
package main

import "fmt"

type User struct {
    id   int
    name string
}

type Manager struct {
    User
    title string
}

func (self *User) ToString() string {
    return fmt.Sprintf("User: %p, %v", self, self)
}

func (self *Manager) ToString() string {
    return fmt.Sprintf("Manager: %p, %v", self, self)
}

func main() {
    m := Manager{User{1, "Tom"}, "Administrator"}

    fmt.Println(m.ToString())

    fmt.Println(m.User.ToString())
}  
output:
Manager: 0xc420074180, &\{\{1 Tom} Administrator}
    User: 0xc420074180, &{1 Tom} 
```
## 方法集以及如何选择 receiver 参数的类型。
Golang方法集 ：每个类型都有与之关联的方法集，这会影响到接口实现规则。
- 类型 T 方法集包含全部 receiver T 方法。
- 类型 *T 方法集包含全部 receiver T + *T 方法。
- 如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。 
- 如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T + *T 方法。 
- 不管嵌入 T 或 *T，*S 方法集总是包含 T + *T 方法。
用实例 value 和 pointer 调用方法 (含匿名字段) 不受方法集约束，编译器总是查找全部方法，并自动转换 receiver 实参。

Go 语言中内部类型方法集提升的规则：

类型 T 方法集包含全部 receiver T 方法。
```go
package main

import (
    "fmt"
)

type T struct {
    int
}

func (t T) test() {
    fmt.Println("类型 T 方法集包含全部 receiver T 方法。")
}

func main() {
    t1 := T{1}
    fmt.Printf("t1 is : %v\n", t1)
    t1.test()
} 
output：
    t1 is : {1}
    类型 T 方法集包含全部 receiver T 方法。 
```
类型 *T 方法集包含全部 receiver T + *T 方法。
```go
package main

import (
    "fmt"
)

type T struct {
    int
}

func (t T) testT() {
    fmt.Println("类型 *T 方法集包含全部 receiver T 方法。")
}

func (t *T) testP() {
    fmt.Println("类型 *T 方法集包含全部 receiver *T 方法。")
}

func main() {
    t1 := T{1}
    t2 := &t1
    fmt.Printf("t2 is : %v\n", t2)
    t2.testT()
    t2.testP()
}
output：
    t2 is : &{1}
    类型 *T 方法集包含全部 receiver T 方法。
    类型 *T 方法集包含全部 receiver *T 方法。 
```
给定一个结构体类型 S 和一个命名为 T 的类型，方法提升像下面规定的这样被包含在结构体方法集中：

如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。

这条规则说的是当我们嵌入一个类型，嵌入类型的接受者为值类型的方法将被提升，可以被外部类型的值和指针调用。
```go
package main

import (
    "fmt"
)

type S struct {
    T
}

type T struct {
    int
}

func (t T) testT() {
    fmt.Println("如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。")
}

func main() {
    s1 := S{T{1} }
    s2 := &s1
    fmt.Printf("s1 is : %v\n", s1)
    s1.testT()
    fmt.Printf("s2 is : %v\n", s2)
    s2.testT()
}
output：
    s1 is : { {1} }
    如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。
    s2 is : &{ {1} }
    如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。 
```
如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T + *T 方法。

这条规则说的是当我们嵌入一个类型的指针，嵌入类型的接受者为值类型或指针类型的方法将被提升，可以被外部类型的值或者指针调用。
```go
package main

import (
    "fmt"
)

type S struct {
    T
}

type T struct {
    int
}

func (t T) testT() {
    fmt.Println("如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T 方法")
}
func (t *T) testP() {
    fmt.Println("如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 *T 方法")
}

func main() {
    s1 := S{T{1} }
    s2 := &s1
    fmt.Printf("s1 is : %v\n", s1)
    s1.testT()
    s1.testP()
    fmt.Printf("s2 is : %v\n", s2)
    s2.testT()
    s2.testP()
}
output：
s1 is : { {1} }
如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T 方法
如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 *T 方法
s2 is : &{ {1} }
如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T 方法
如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 *T 方法 
```
```go
func (t T) M1() <=> F1(t T)
func (t *T) M2() <=> F2(t *T)
```
- 当 receiver 参数的类型为 T 时：
    - 当我们的方法 M1 采用类型为 T 的 receiver 参数时，代表 T 类型实例的 receiver 参数以值传递方式传递到 M1 方法体中的，实际上是 T 类型实例的副本，M1 方法体中对副本的任何修改操作，都不会影响到原 T 类型实例。
- 当 receiver 参数的类型为 *T 时：
    - 方法 M2 采用类型为 *T 的 receiver 参数时，代表 *T 类型实例的 receiver 参数以值传递方式传递到 M2 方法体中的，实际上是 T 类型实例的地址，M2 方法体通过该地址可以对原 T 类型实例进行任何修改操作。

选择 receiver 参数类型的第一个原则：**如果 Go 方法要把对 receiver 参数代表的类型实例的修改，反映到原类型实例上，那么我们应该选择 *T 作为 receiver 参数的类型。**

无论是 T 类型实例，还是 *T 类型实例，都既可以调用 receiver 为 T 类型的方法，也可以调用 receiver 为 *T 类型的方法。

选择 receiver 参数类型的第二个原则：
- 一般情况下，我们通常会为 receiver 参数选择 T 类型，因为这样可以缩窄外部修改类型实例内部状态的“接触面”，也就是尽量少暴露可以修改类型内部状态的方法。
- 考虑到 Go 方法调用时，receiver 参数是以值拷贝的形式传入方法中的。那么，如果 receiver 参数类型的 size 较大，以值拷贝形式传入就会导致较大的性能开销，这时我们选择 *T 作为 receiver 类型可能更好些。


选择 receiver 参数类型的第三个原则：（**其实是应该首先考虑的一点**）
- 如果 T 类型需要实现某个接口，那我们就要使用 T 作为 receiver 参数的类型，来满足接口类型方法集合中的所有方法。
- 如果 T 不需要实现某一接口，但 \*T 需要实现该接口，那么根据方法集合概念，*T 的方法集合是包含 T 的方法集合的，这样我们在确定 Go 方法的 receiver 的类型时，参考原则一和原则二就可以了。

## 表达式
Golang 表达式 ：根据调用者不同，方法分为两种表现形式:
```go
    instance.method(args...) ---> <type>.func(instance, args...)  
```
前者称为 method value，后者 method expression。

两者都可像普通函数那样赋值和传参，区别在于 method value 绑定实例，而 method expression 则须显式传参。
```go
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self *User) Test() {
    fmt.Printf("%p, %v\n", self, self)
}

func main() {
    u := User{1, "Tom"}
    u.Test()

    mValue := u.Test
    mValue() // 隐式传递 receiver

    mExpression := (*User).Test
    mExpression(&u) // 显式传递 receiver
}   
output:

    0xc42000a060, &{1 Tom}
    0xc42000a060, &{1 Tom}
    0xc42000a060, &{1 Tom}  
```
需要**注意**，method value 会复制 receiver。
```go
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self User) Test() {
    fmt.Println(self)
}

func main() {
    u := User{1, "Tom"}
    mValue := u.Test // 立即复制 receiver，因为不是指针类型，不受后续修改影响。

    u.id, u.name = 2, "Jack"
    u.Test()

    mValue()
} 
output:
    {2 Jack}
    {1 Tom}  
```
在汇编层面，method value 和闭包的实现方式相同，实际返回 FuncVal 类型对象。
```
    FuncVal { method_address, receiver_copy }  
```
可依据方法集转换 method expression，**注意** receiver 类型的差异。
```go
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self *User) TestPointer() {
    fmt.Printf("TestPointer: %p, %v\n", self, self)
}

func (self User) TestValue() {
    fmt.Printf("TestValue: %p, %v\n", &self, self)
}

func main() {
    u := User{1, "Tom"}
    fmt.Printf("User: %p, %v\n", &u, u)

    mv := User.TestValue
    mv(u)

    mp := (*User).TestPointer
    mp(&u)

    mp2 := (*User).TestValue // *User 方法集包含 TestValue。签名变为 func TestValue(self *User)。实际依然是 receiver value copy。
    mp2(&u)
} 
output:
    User: 0xc42000a060, {1 Tom}
    TestValue: 0xc42000a0a0, {1 Tom}
    TestPointer: 0xc42000a060, &{1 Tom}
    TestValue: 0xc42000a100, {1 Tom}
```
将方法 “还原” 成函数，就容易理解下面的代码了。
```go
package main

type Data struct{}

func (Data) TestValue() {}

func (*Data) TestPointer() {}

func main() {
    var p *Data = nil
    p.TestPointer()

    (*Data)(nil).TestPointer() // method value
    (*Data).TestPointer(nil)   // method expression

    // p.TestValue()            // invalid memory address or nil pointer dereference

    // (Data)(nil).TestValue()  // cannot convert nil to type Data
    // Data.TestValue(nil)      // cannot use nil as type Data in function argument
}   
```
## 自定义error
### 抛异常和处理异常
#### 系统抛
```go
package main

import "fmt"

// 系统抛
func test01() {
   a := [5]int{0, 1, 2, 3, 4}
   a[1] = 123
   fmt.Println(a)
   //a[10] = 11
   index := 10
   a[index] = 10
   fmt.Println(a)
}

func getCircleArea(radius float32) (area float32) {
   if radius < 0 {
      // 自己抛
      panic("半径不能为负")
   }
   return 3.14 * radius * radius
}

func test02() {
   getCircleArea(-5)
}

//
func test03() {
   // 延时执行匿名函数
   // 延时到何时？（1）程序正常结束   （2）发生异常时
   defer func() {
      // recover() 恢复
      // 会返回程序为什么挂了
      if err := recover(); err != nil {
         fmt.Println(err)
      }
   }()
   getCircleArea(-5)
   fmt.Println("这里有没有执行")
}

func test04()  {
   test03()
   fmt.Println("test04")
}

func main() {
   test04()
}
```
#### 返回异常
```go
package main

import (
   "errors"
   "fmt"
)

func getCircleArea(radius float32) (area float32, err error) {
   if radius < 0 {
      // 构建个异常对象
      err = errors.New("半径不能为负")
      return
   }
   area = 3.14 * radius * radius
   return
}

func main() {
   area, err := getCircleArea(-5)
   if err != nil {
      fmt.Println(err)
   } else {
      fmt.Println(area)
   }
}
```
#### 自定义error：
```go
package main

import (
    "fmt"
    "os"
    "time"
)

type PathError struct {
    path       string
    op         string
    createTime string
    message    string
}

func (p *PathError) Error() string {
    return fmt.Sprintf("path=%s \nop=%s \ncreateTime=%s \nmessage=%s", p.path,
        p.op, p.createTime, p.message)
}

func Open(filename string) error {

    file, err := os.Open(filename)
    if err != nil {
        return &PathError{
            path:       filename,
            op:         "read",
            message:    err.Error(),
            createTime: fmt.Sprintf("%v", time.Now()),
        }
    }

    defer file.Close()
    return nil
}

func main() {
    err := Open("/Users/5lmh/Desktop/go/src/test.txt")
    switch v := err.(type) {
    case *PathError:
        fmt.Println("get path error,", v)
    default:

    }

}
output：
    get path error, path=/Users/pprof/Desktop/go/src/test.txt 
    op=read 
    createTime=2018-04-05 11:25:17.331915 +0800 CST m=+0.000441790 
    message=open /Users/pprof/Desktop/go/src/test.txt: no such file or directory
```

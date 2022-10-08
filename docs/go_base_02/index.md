# Go_base_02

> 参考学习go语言中文网、C语言中文网、golang官方文档等

# 流程控制
## if
Go 编程语言中 if 语句的语法如下：
```go
 可省略条件表达式括号。
 持初始化语句，可定义代码块局部变量，也叫做if语句的自用变量，而这自用变量作用域在其声明所在的代码块，但不能在该if语句块之外。
 代码块左花括号必须在条件表达式尾部。
 可嵌套    

    if 布尔表达式 {
    /* 在布尔表达式为 true 时执行 */
    }   
```
if分支有三种：单分支、二分支、多分支，要减少多分支结构，甚至是二分支结构的使用。这样的代码更优雅、简洁、易读、易维护。单分支结构的使用被称为符合“快乐路径”原则。

**Go不支持三元操作符(三目运算符) "a > b ? a : b"**


## switch
**Golang switch 分支表达式可以是任意类型，不限于常量。可省略 break，默认自动终止。**

可以同时测试多个可能符合条件的值，使用逗号分割它们，例如：case val1, val2, val3。

switch 语句支持声明临时变量。

如：
```go
switch {
      case grade == "A" :
         fmt.Printf("优秀!\n" )     
      case grade == "B", grade == "C" :
         fmt.Printf("良好\n" )      
      case grade == "D" :
         fmt.Printf("及格\n" )      
      case grade == "F":
         fmt.Printf("不及格\n" )
      default:
         fmt.Printf("差\n" )
   }
```
### Type Switch
switch 语句还可以被用于 type-switch 来**判断某个 interface 变量中实际存储的变量类型。**

```go
switch x.(type){
    case type1:
       statement(s)      
    case type2:
       statement(s)
    /* 你可以定义任意个数的case */
    default: /* 可选 */
       statement(s)
}   
```
## select
**select 语句类似于 switch 语句，但是select会随机执行一个可运行的case。如果没有case可运行，且没有default语句，它将阻塞，直到有case可运行。**
```
每个case都必须是一个通信
所有channel表达式都会被求值
所有被发送的表达式都会被求值
如果任意某个通信可以进行，它就执行；其他被忽略。
如果有多个case都可以运行，Select会随机公平地选出一个执行。其他不会执行。
否则：
如果有default子句，则执行该语句。
如果没有default字句，select将阻塞，直到某个通信可以运行；Go不会重新对channel或值进行求值。
```
select可以监听channel的数据流动

select的用法与switch语法非常类似，由select开始的一个新的选择块，每个选择条件由case语句来描述

与switch语句可以选择任何使用相等比较的条件相比，select有比较多的限制，其中最大的一条限制就是**每个case语句里必须是一个IO操作**
```go
select { //不停的在这里检测
    case <-chanl : //检测有没有数据可以读
    //如果chanl成功读取到数据，则进行该case处理语句
    case chan2 <- 1 : //检测有没有可以写
    //如果成功向chan2写入数据，则进行该case处理语句


    //假如没有default，那么在以上两个条件都不成立的情况下，就会在此阻塞//一般default会不写在里面，select中的default子句总是可运行的，因为会很消耗CPU资源
    default:
    //如果以上都没有符合条件，那么则进行default处理流程
 }
 ```
 

 在一个select语句中，Go会按顺序从头到尾评估每一个发送和接收的语句。

- 如果其中的任意一个语句可以继续执行（即没有被阻塞），那么就从那些可以执行的语句中任意选择一条来使用。
- 如果没有任意一条语句可以执行（即所有的通道都被阻塞），那么有两种可能的情况：
    - ①如果给出了default语句，那么就会执行default的流程，同时程序的执行会从select语句后的语句中恢复。
    - ②如果没有default语句，那么select语句将被阻塞，直到至少有一个case可以进行下去。
### 基本使用
select是Go中的一个控制结构，类似于switch语句，用于处理异步IO操作。select会监听case语句中channel的读写操作，当case中channel读写操作为非阻塞状态（即能读写）时，将会触发相应的动作。

select中的case语句必须是一个channel操作

select中的default子句总是可运行的。

如果有多个case都可以运行，select会随机公平地选出一个执行，其他不会执行。

如果没有可运行的case语句，且有default语句，那么就会执行default的动作。

如果没有可运行的case语句，且没有default语句，select将阻塞，直到某个case通信可以运行

例如：
```go
package main

import "fmt"

func main() {
   var c1, c2, c3 chan int
   var i1, i2 int
   select {
      case i1 = <-c1:
         fmt.Printf("received ", i1, " from c1\n")
      case c2 <- i2:
         fmt.Printf("sent ", i2, " to c2\n")
      case i3, ok := (<-c3):  // same as: i3, ok := <-c3
         if ok {
            fmt.Printf("received ", i3, " from c3\n")
         } else {
            fmt.Printf("c3 is closed\n")
         }
      default:
         fmt.Printf("no communication\n")
   }    
}
//输出：no communication 
```
### 典型用法
1. 超时判断：
```go
//比如在下面的场景中，使用全局resChan来接受response，如果时间超过3S,resChan中还没有数据返回，则第二条case将执行
var resChan = make(chan int)
// do request
func test() {
    select {
    case data := <-resChan:
        doData(data)
    case <-time.After(time.Second * 3):
        fmt.Println("request time out")
    }
}

func doData(data int) {
    //...
}
```
2. 退出
```go
//主线程（协程）中如下：
var shouldQuit=make(chan struct{})
fun main(){
    {
        //loop
    }
    //...out of the loop
    select {
        case <-c.shouldQuit:
            cleanUp()
            return
        default:
        }
    //...
}

//再另外一个协程中，如果运行遇到非法操作或不可处理的错误，就向shouldQuit发送数据通知程序停止运行
close(shouldQuit)
```
3. 判断channel是否阻塞
```go
//在某些情况下是存在不希望channel缓存满了的需求的，可以用如下方法判断
ch := make (chan int, 5)
//...
data：=0
select {
case ch <- data:
default:
    //做相应操作，比如丢弃data。视需求而定
} 
```
## for
**Go语言的For循环有3中形式，只有其中的一种使用分号。**
```go
    for init; condition; post { }
    for condition { }
    for { }

    init： 一般为赋值表达式，给控制变量赋初值；
    condition： 关系表达式或逻辑表达式，循环控制条件；
    post： 一般为赋值表达式，给控制变量增量或减量。

    for语句执行过程如下：
    ①先对表达式 init 赋初值；
    ②判别赋值表达式 init 是否满足给定 condition 条件，若其值为真，满足循环条件，则执行循环体内语句，然后执行 post，进入第二次循环，再判别 condition；否则判断 condition 的值为假，不满足条件，就终止for循环，执行循环体外语句。 
```
可嵌套，可无限循环。
## range
Golang range类似迭代器操作，返回 (索引, 值) 或 (键, 值)。

for 循环的 range 格式可以对 slice、map、数组、字符串等进行迭代循环。格式如下：
```go
for key, value := range oldMap {
    newMap[key] = value
}   
```
****注意**range会复制对象**：
```go
package main

import "fmt"

func main() {
    a := [3]int{0, 1, 2}

    for i, v := range a { // index、value 都是从复制品中取出。

        if i == 0 { // 在修改前，我们先修改原数组。
            a[1], a[2] = 999, 999
            fmt.Println(a) // 确认修改有效，输出 [0, 999, 999]。
        }

        a[i] = v + 100 // 使用复制品中取出的 value 修改原数组。

    }

    fmt.Println(a) // 输出 [100, 101, 102]。
}   

output:
[0 999 999]
[100 101 102]   
```
这说明开始执行range时就已经保存了当下的数组，所以在循环里修改数组也不会改动遍历时的使用的数组的值。

**改用引用类型，其底层数据不会被复制：**
```go
package main

func main() {
    s := []int{1, 2, 3, 4, 5}

    for i, v := range s { // 复制 struct slice { pointer, len, cap }。

        if i == 0 {
            s = s[:3]  // 对 slice 的修改，不会影响 range。
            s[2] = 100 // 对底层数据的修改。
        }

        println(i, v)
    }
}   

output:
0 1
1 2
2 100
3 4
4 5
```
另外两种引用类型 map、channel 也能应用于for range遍历上，是指针包装，而不像 slice 是 struct。

for 和 for range有什么区别?

主要是使用场景不同

for可以
- 遍历array和slice
- 遍历key为整型递增的map
- 遍历string

for range可以完成所有for可以做的事情，却能做到for不能做的，包括
- 遍历key为string类型的map并同时获取key和value
- 遍历channel
## Goto Break Continue 
循环控制语句：
Goto、Break、Continue
1. 三个语句都可以配合标签(label)使用
2. 标签名区分大小写，定以后若不使用会造成编译错误
3. continue、break配合标签(label)可用于多层循环跳出
4. goto是调整执行位置，与continue、break配合标签(label)的结果并不相同  

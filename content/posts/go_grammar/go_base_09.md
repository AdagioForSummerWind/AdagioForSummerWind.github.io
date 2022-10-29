---
title: "Go_base_09"
date: 2022-01-12T22:17:07+08:00
lastmod: 2022-01-06
tags: [go grammar]
categories: [Go]
slug: Go_Common_Standard_Library
draft: true
---
# 常用标准库
## 前言
### 包复用
package:
1. 基本复⽤模块单元：以⾸字⺟⼤写来表明可被包外代码访问
2. 代码的 package 可以和所在的⽬录不⼀致
3. 同⼀⽬录⾥的 Go 代码的 package 要保持⼀致

### 依赖管理
Go 未解决的依赖问题：
1. 同⼀环境下，不同项⽬使⽤同⼀包的不同版本
2. ⽆法管理对包的特定版本的依赖

vendor 路径
- 随着 Go 1.5 release 版本的发布，vendor ⽬录被添加到除了 GOPATH 和GOROOT 之外的依赖⽬录查找的解决⽅案。在 Go 1.6 之前，你需要⼿动的设置环境变量查找依赖包路径的解决⽅案如下：
1. 当前包下的 vendor ⽬录
2. 向上级⽬录查找，直到找到 src 下的 vendor ⽬录
3. 在 GOPATH 下⾯查找依赖包
4. 在 GOROOT ⽬录下查找

常用依赖管理工具：
- [godep](https://github.com/tools/godep)
- [glide](https://github.com/Masterminds/glide)
- [dep](https://github.com/golang/dep)


## fmt
## Time
## Flag
## Log
## IO操作
## Strconv
## Template
## Http
## Context
## 数据格式
## 反射和unsafe
### 反射编程
反射是指在程序运行期对程序本身进行访问和修改的能力

#### 变量的内在机制
- 变量包含类型信息和值信息 var arr [10]int arr[0] = 10
- 类型信息：是静态的元信息，是预先定义好的
- 值信息：是程序运行过程中动态改变的
#### 反射的使用
- reflect包封装了反射相关的方法
- 获取类型信息：reflect.TypeOf，是静态的
- 获取值信息：reflect.ValueOf，是动态的
#### 空接口与反射
- 反射可以在运行时动态获取程序的各种详细信息
- 反射获取interface类型信息

```go
package main

import (
   "fmt"
   "reflect"
)

//反射获取interface类型信息

func reflect_type(a interface{}) {
   t := reflect.TypeOf(a)
   fmt.Println("类型是：", t)
   // kind()可以获取具体类型
   k := t.Kind()
   fmt.Println(k)
   switch k {
   case reflect.Float64:
      fmt.Printf("a is float64\n")
   case reflect.String:
      fmt.Println("string")
   }
}

func main() {
   var x float64 = 3.4
   reflect_type(x)
}
```
反射获取interface值信息
```go
package main

import (
    "fmt"
    "reflect"
)

//反射获取interface值信息

func reflect_value(a interface{}) {
    v := reflect.ValueOf(a)
    fmt.Println(v)
    k := v.Kind()
    fmt.Println(k)
    switch k {
    case reflect.Float64:
        fmt.Println("a是：", v.Float())
    }
}

func main() {
    var x float64 = 3.4
    reflect_value(x)
}
```
反射修改值信息
```go
package main

import (
    "fmt"
    "reflect"
)

//反射修改值
func reflect_set_value(a interface{}) {
    v := reflect.ValueOf(a)
    k := v.Kind()
    switch k {
    case reflect.Float64:
        // 反射修改值
        v.SetFloat(6.9)
        fmt.Println("a is ", v.Float())
    case reflect.Ptr:
        // Elem()获取地址指向的值
        v.Elem().SetFloat(7.9)
        fmt.Println("case:", v.Elem().Float())
        // 地址
        fmt.Println(v.Pointer())
    }
}

func main() {
    var x float64 = 3.4
    // 反射认为下面是指针类型，不是float类型
    reflect_set_value(&x)
    fmt.Println("main:", x)
}
```
#### 结构体与反射
查看类型字段和方法
```go
package main

import (
    "fmt"
    "reflect"
)

// 定义结构体
type User struct {
    Id   int
    Name string
    Age  int
}

// 绑方法
func (u User) Hello() {
    fmt.Println("Hello")
}

// 传入interface{}
func Poni(o interface{}) {
    t := reflect.TypeOf(o)
    fmt.Println("类型：", t)
    fmt.Println("字符串类型：", t.Name())
    // 获取值
    v := reflect.ValueOf(o)
    fmt.Println(v)
    // 可以获取所有属性
    // 获取结构体字段个数：t.NumField()
    for i := 0; i < t.NumField(); i++ {
        // 取每个字段
        f := t.Field(i)
        fmt.Printf("%s : %v", f.Name, f.Type)
        // 获取字段的值信息
        // Interface()：获取字段对应的值
        val := v.Field(i).Interface()
        fmt.Println("val :", val)
    }
    fmt.Println("=================方法====================")
    for i := 0; i < t.NumMethod(); i++ {
        m := t.Method(i)
        fmt.Println(m.Name)
        fmt.Println(m.Type)
    }

}

func main() {
    u := User{1, "zs", 20}
    Poni(u)
}
```
查看匿名字段
```go
package main

import (
    "fmt"
    "reflect"
)

// 定义结构体
type User struct {
    Id   int
    Name string
    Age  int
}

// 匿名字段
type Boy struct {
    User
    Addr string
}

func main() {
    m := Boy{User{1, "zs", 20}, "bj"}
    t := reflect.TypeOf(m)
    fmt.Println(t)
    // Anonymous：匿名
    fmt.Printf("%#v\n", t.Field(0))
    // 值信息
    fmt.Printf("%#v\n", reflect.ValueOf(m).Field(0))
}
```
修改结构体的值：
```go
package main

import (
    "fmt"
    "reflect"
)

// 定义结构体
type User struct {
    Id   int
    Name string
    Age  int
}

// 修改结构体值
func SetValue(o interface{}) {
    v := reflect.ValueOf(o)
    // 获取指针指向的元素
    v = v.Elem()
    // 取字段
    f := v.FieldByName("Name")
    if f.Kind() == reflect.String {
        f.SetString("kuteng")
    }
}

func main() {
    u := User{1, "5lmh.com", 20}
    SetValue(&u)
    fmt.Println(u)
}
```
调用方法
```go
package main

import (
    "fmt"
    "reflect"
)

// 定义结构体
type User struct {
    Id   int
    Name string
    Age  int
}

func (u User) Hello(name string) {
    fmt.Println("Hello：", name)
}

func main() {
    u := User{1, "5lmh.com", 20}
    v := reflect.ValueOf(u)
    // 获取方法
    m := v.MethodByName("Hello")
    // 构建一些参数
    args := []reflect.Value{reflect.ValueOf("6666")}
    // 没参数的情况下：var args2 []reflect.Value
    // 调用方法，需要传入方法的参数
    m.Call(args)
}
```
获取字段的tag
```go
package main

import (
    "fmt"
    "reflect"
)

type Student struct {
    Name string `json:"name1" db:"name2"`
}

func main() {
    var s Student
    v := reflect.ValueOf(&s)
    // 类型
    t := v.Type()
    // 获取字段
    f := t.Elem().Field(0)
    fmt.Println(f.Tag.Get("json"))
    fmt.Println(f.Tag.Get("db"))
}
```
#### 万能程序
DeepEqual比较切片和map
```go
func TestDeepEqual(t *testing.T) {
	a := map[int]string{1: "one", 2: "two", 3: "three"}
	b := map[int]string{1: "one", 2: "two", 3: "three"}
	//	t.Log(a == b)
	t.Log("a==b?", reflect.DeepEqual(a, b))

	s1 := []int{1, 2, 3}
	s2 := []int{1, 2, 3}
	s3 := []int{2, 3, 1}

	t.Log("s1 == s2?", reflect.DeepEqual(s1, s2))
	t.Log("s1 == s3?", reflect.DeepEqual(s1, s3))

	c1 := Customer{"1", "Mike", 40}
	c2 := Customer{"1", "Mike", 40}
	fmt.Println(c1 == c2)
	fmt.Println(reflect.DeepEqual(c1, c2))
}
```
用公用方法解决不同结构体的填充
```go
type Employee struct {
	EmployeeID string
	Name       string `format:"normal"`
	Age        int
}

func (e *Employee) UpdateAge(newVal int) {
	e.Age = newVal
}

type Customer struct {
	CookieID string
	Name     string
	Age      int
}

func fillBySettings(st interface{}, settings map[string]interface{}) error {

	// func (v Value) Elem() Value
	// Elem returns the value that the interface v contains or that the pointer v points to.
	// It panics if v's Kind is not Interface or Ptr.
	// It returns the zero Value if v is nil.

	if reflect.TypeOf(st).Kind() != reflect.Ptr {
		return errors.New("the first param should be a pointer to the struct type.")
	}
	// Elem() 获取指针指向的值
	if reflect.TypeOf(st).Elem().Kind() != reflect.Struct {
		return errors.New("the first param should be a pointer to the struct type.")
	}

	if settings == nil {
		return errors.New("settings is nil.")
	}

	var (
		field reflect.StructField
		ok    bool
	)

	for k, v := range settings {
		if field, ok = (reflect.ValueOf(st)).Elem().Type().FieldByName(k); !ok {
			continue
		}
		if field.Type == reflect.TypeOf(v) {
			vstr := reflect.ValueOf(st)
			vstr = vstr.Elem()
			vstr.FieldByName(k).Set(reflect.ValueOf(v))
		}

	}
	return nil
}

func TestFillNameAndAge(t *testing.T) {
	settings := map[string]interface{}{"Name": "Mike", "Age": 30}
	e := Employee{}
	if err := fillBySettings(&e, settings); err != nil {
		t.Fatal(err)
	}
	t.Log(e)
	c := new(Customer)
	if err := fillBySettings(c, settings); err != nil {
		t.Fatal(err)
	}
	t.Log(*c)
}
```
灵活性虽然更强，但使代码可读性降低，并降低性能。
#### 不安全编程
unsafe pkg  比如拟合外部的c程序实现的高效的库，交互的时候可能会用到unsafe，其他使用比较少。
```go
//  强转
i := 10
f := *(*float64)(unsafe.Pointer(&i))
```
```go
package unsafe_test

import (
	"fmt"
	"sync"
	"sync/atomic"
	"testing"
	"time"
	"unsafe"
)

type Customer struct {
	Name string
	Age  int
}
//危险的类型转换
func TestUnsafe(t *testing.T) {
	i := 10
	f := *(*float64)(unsafe.Pointer(&i))
	t.Log(unsafe.Pointer(&i))
	t.Log(f)
}

// The cases is suitable for unsafe
type MyInt int

//合理的类型转换
func TestConvert(t *testing.T) {
	a := []int{1, 2, 3, 4}
	b := *(*[]MyInt)(unsafe.Pointer(&a))
	t.Log(b)
}

//原子类型操作 atomic包提供的指针原子操作
func TestAtomic(t *testing.T) {
    //并发读写共享内存
	var shareBufPtr unsafe.Pointer
	writeDataFn := func() {
		data := []int{}
		for i := 0; i < 100; i++ {
			data = append(data, i)
		}
        //写在另外一块内存，写完后重新将所写的数据指向那个共享内存（原子操作）
		atomic.StorePointer(&shareBufPtr, unsafe.Pointer(&data))
	}
	readDataFn := func() {
		data := atomic.LoadPointer(&shareBufPtr)
		fmt.Println(data, *(*[]int)(data))
	}
	var wg sync.WaitGroup
	writeDataFn()
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			for i := 0; i < 10; i++ {
				writeDataFn()
				time.Sleep(time.Microsecond * 100)
			}
			wg.Done()
		}()
		wg.Add(1)
		go func() {
			for i := 0; i < 10; i++ {
				readDataFn()
				time.Sleep(time.Microsecond * 100)
			}
			wg.Done()
		}()
	}
	wg.Wait()
}

```
```go
PS D:\Go\Go_WorkSpace\go_learning-master\code\ch39\unsafe_programming> go test -v -run TestUnsafe unsafe_test.go
=== RUN   TestUnsafe
    unsafe_test.go:20: 0xc00009e1c8
    unsafe_test.go:21: 5e-323
--- PASS: TestUnsafe (0.00s)
PASS
ok      command-line-arguments  0.656s
```
## 文件操作
## go module
## String


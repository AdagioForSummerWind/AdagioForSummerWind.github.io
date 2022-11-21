---
title: "Programming_literacy"
date: 2022-01-02T18:57:57+08:00
lastmod: 2022-01-02
tags: [data structure]
categories: [Coding]
slug: Programming Literacy
draft: true
---
# 编程相关
## 代码风格与规范
go其实没有什么要说的。。
### 变量命名
主要以团队风格为主；

主流有如下三种变量规则：
- 小驼峰、大驼峰(帕斯卡命名法)命名法（java,go）
- 下划线命名法(python,linux下的c/c++编程)
- 匈牙利命名法
    - 该命名规范，要求前缀字母用变量类型的缩写，其余部分用变量的英文或英文的缩写，单词第一个字母大写。（很少用，在windows下的c/c++编程有时会用,没有IDE的时代挺好）
    ```
c
    int iMyAge;        //  "i": int
    char cMyName[10];  //  "c": char
    float fManHeight;  //  "f": float
    ```
### 核心代码模式和ACM模式
- 核心代码模式：
    - 把要处理的数据都已经放入容器里，可以直接写逻辑
- ACM输入模式呢：
    - 自己构造输入数据格式，把要需要处理的容器填充好，不会给你任何代码，包括include哪些函数都要自己写，最后也要自己控制返回数据的格式。



### acm模式

#### 输入输出
1. 单独读一行：先用bufio.NewReader(os.Stdin)从标准输入中读取缓冲，再调用ReadLine()读取一行，有三个返回值，第二个是指是否是前缀，也就是说缓冲区可能装不下整行字符串。
2. 多行：bufio.NewScanner(os.Stdin)从缓冲区中读取多行，每次调用 input.Scan()，即 读入下一行 ，并移除行末的换行符。读取的内容可以调用 input.Text() 得到。Scan函数在读到一行时返回true，不再有输入时返回false。如果读入一行text为多个以空格相隔的字符串可以调用 strings.Split() 进行分割返回字符串切片 eg:
```
……
s:=input.Text()
li:=strings.Split(s," ")
```


fmt.Scan()
Scan 从标准输入扫描文本，读取由空白符分隔的值保存到传递给本函数的参数中，换行符视为空白符。本函数返回成功扫描的数据个数和遇到的任何错误。如果读取的数据个数比提供的参数少，会返回一个错误报告原因。

fmt.Scanf()
Scanf从标准输入扫描文本，根据format参数指定的格式去读取由空白符分隔的值保存到传递给本函数的参数中。本函数返回成功扫描的数据个数和遇到的任何错误。

fmt.Scanf和fmt.Scan不同于以简单的空格符作为输入数据的分隔符，fmt.Scanf为输入数据指定了具体的输入内容格式，只有按照格式输入数据才会被扫描并存入对应变量。

fmt.Scanln
Scanln类似于Scan，它遇到换行才停止扫描。最后一个数据后面必须有换行或者到达结束位置。本函数返回成功扫描的数据个数和遇到的任何错误。

#### HJ1 字符串最后一个单词的长度
知识点
字符串
warning 校招时部分企业笔试将禁止编程题跳出页面，为提前适应，练习时请使用在线自测，而非本地IDE。
描述
计算字符串最后一个单词的长度，单词以空格隔开，字符串长度小于5000。（注：字符串末尾不以空格为结尾）
输入描述：
输入一行，代表要计算的字符串，非空，长度小于5000。

输出描述：
输出一个整数，表示输入字符串最后一个单词的长度。

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
)

func LastStringLength(str *string) int {
    newStr := strings.Fields(*str)  // 库函数，以空格将字符串分割为字符串切片
    return len(newStr[len(newStr)-1])
}

// 填fmt.scanf的坑
func Scanf(input *string) {
    reader := bufio.NewReader(os.Stdin)
    data, _, _ := reader.ReadLine()//后面是：是否后缀，err
    *input = string(data)
}

func main() {
    var input string
    Scanf(&input)
    fmt.Printf("%d", LastStringLength(&input)) 
}
```


#### HJ2 计算某字符出现次数

描述
写出一个程序，接受一个由字母、数字和空格组成的字符串，和一个字符，然后输出输入字符串中该字符的出现次数。（不区分大小写字母）

数据范围： 1 \le n \le 1000 \1≤n≤1000 
输入描述：
第一行输入一个由字母和数字以及空格组成的字符串，第二行输入一个字符。

输出描述：
输出输入字符串中含有该字符的个数。（不区分大小写字母）

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strings"
)

func main() {
	map1 := make(map[byte]int)
	// NewScanner returns a new Scanner to read from r.
	// The split function defaults to ScanLines.
	input := bufio.NewScanner(os.Stdin)

	for input.Scan() {

		s := input.Text()

		input.Scan()

		s1 := input.Text()
		s = strings.ToLower(s)
		s1 = strings.ToLower(s1)
		b1 := s1[0]

		for i := 0; i < len(s); i++ {
			map1[s[i]]++
		}
		fmt.Println(map1[b1])

	}

}
```


#### HJ3 明明的随机数

描述
明明生成了N个1到500之间的随机整数。请你删去其中重复的数字，即相同的数字只保留一个，把其余相同的数去掉，然后再把这些数从小到大排序，按照排好的顺序输出。

数据范围： 1≤n≤1000  ，输入的数字大小满足1≤val≤500 
输入描述：
第一行先输入随机整数的个数 N 。 接下来的 N 行每行输入一个整数，代表明明生成的随机数。 具体格式可以参考下面的"示例"。
输出描述：
输出多行，表示输入数据处理后的结果

```go
package main

import (
	"fmt"
)

func main() {
	N := 0
	// Scan scans text read from standard input, storing successive
	// space-separated values into successive arguments. Newlines count
	// as space. It returns the number of items successfully scanned.
	// If that is less than the number of arguments, err will report why.
	fmt.Scan(&N)
	narr := make([]int, 501)
	n := 0
	for i := 0; i < N; i++ {
		fmt.Scan(&n)
		if narr[n] > 0 {
			continue
		}
		narr[n] = 1
	}
	for i := range narr {
		if narr[i] > 0 {
			fmt.Println(i)
		}
	}

}
```

#### HJ4 字符串分隔

描述
•输入一个字符串，请按长度为8拆分每个输入字符串并进行输出；

•长度不是8整数倍的字符串请在后面补数字0，空字符串不处理。
输入描述：
连续输入字符串(每个字符串长度小于等于100)

输出描述：
依次输出所有分割后的长度为8的新字符串

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

// 连续输入字符串，请按长度为8拆分每个输入字符串并进行输出；
//
//•长度不是8整数倍的字符串请在后面补数字0，空字符串不处理。

func main() {
	scanner := bufio.NewScanner(os.Stdin)
	if !scanner.Scan() {
		panic("error")
	}
	str := []rune(scanner.Text())
	m, n := len(str)/8, len(str)%8
	for i := 0; i < m; i++ {
		fmt.Println(string(str[i*8 : i*8+8]))
	}
	if n != 0 {
		fmt.Print(string(str[len(str)-n:]))
		for j := 0; j < 8-n; j++ {
			fmt.Print(0)
		}
		fmt.Println()
	}
}
```
#### HJ5 进制转换

描述
写出一个程序，接受一个十六进制的数，输出该数值的十进制表示。

数据范围：保证结果在1≤n≤2^31−1 
输入描述：
输入一个十六进制的数值字符串。

输出描述：
输出该数值的十进制字符串。不同组的测试用例用\n隔开。
```go
package main
 
import "fmt"
 
// 写出一个程序，接受一个十六进制的数，输出该数值的十进制表示。
//31  −1
 
func main() {
    var num int
    for {
        _, err := fmt.Scanf("0x%x", &num)
        if err != nil {
            return
        }
        fmt.Println(num)
    }
}
```

#### HJ6 质数因子

描述
功能:输入一个正整数，按照从小到大的顺序输出它的所有质因子（重复的也要列举）（如180的质因子为2 2 3 3 5 ）


数据范围： 1≤n≤2×10^9+14 
输入描述：
输入一个整数

输出描述：
按照从小到大的顺序输出它的所有质数的因子，以空格隔开。


一个合数必然只有一个大于其开方的质数因子。
```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	var n int
	in := bufio.NewReader(os.Stdin)
	//Fscan scans text read from r, storing successive space-separated values
	//into successive arguments. Newlines count as space.
	//It returns the number of items successfully scanned. If that is less
	//than the number of arguments, err will report why.
	//func fmt.Fscan(r io.Reader, a ...any) (n int, err error)
	fmt.Fscan(in, &n)

	for i := 2; i*i <= n; i++ {
		for n%i == 0 {
			n /= i
			fmt.Printf("%d ", i)
		}
	}
	if n != 1 {
		fmt.Printf("S%d ", n)
	}
}
```



#### HJ7 取近似值

描述
写出一个程序，接受一个正浮点数值，输出该数值的近似整数值。如果小数点后数值大于等于 0.5 ,向上取整；小于 0.5 ，则向下取整。

数据范围：保证输入的数字在 32 位浮点数范围内
输入描述：
输入一个正浮点数值

输出描述：
输出该数值的近似整数值


```go
package main

import (
	"fmt"
)

func main() {
	var value float32
	fmt.Scanf("%f", &value)
	run(value)
}

func run(value float32) int {
    v := int(value+0.5)
    fmt.Printf("%d",v)
    return v
}
```

#### HJ8 合并表记录

描述
数据表记录包含表索引index和数值value（int范围的正整数），请对表索引相同的记录进行合并，即将相同索引的数值进行求和运算，输出按照index值升序进行输出。


提示:
0 <= index <= 11111111
1 <= value <= 100000

输入描述：
先输入键值对的个数n（1 <= n <= 500）
接下来n行每行输入成对的index和value值，以空格隔开

输出描述：
输出合并后的键值对（多行）
```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	var n int
	var key []int
	map1 := make(map[int]int)
	// Scan scans text read from standard input, storing successive
	// space-separated values into successive arguments. Newlines count
	// as space. It returns the number of items successfully scanned.
	// If that is less than the number of arguments, err will report why.
	//func fmt.Scan(a ...any) (n int, err error)
	fmt.Scan(&n)
	for i := 0; i < n; i++ {
		var key int
		var value int
		fmt.Scan(&key, &value)
		map1[key] += value
	}
	for k := range map1 {
		key = append(key, k)
	}
	sort.Ints(key)
	for _, v := range key {
		fmt.Println(v, map1[v])
	}

}

```











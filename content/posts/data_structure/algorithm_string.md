---
title: "Algorithm_string"
date: 2022-01-06T08:18:50+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: data structure string
draft: true
---
> 学习[代码随想录](https://programmercarl.com/)笔记
# 字符串
> 库函数是工具，基础更重要。

## 输入输出
1. 单独读一行：先用bufio.NewReader(os.Stdin)从标准输入中读取缓冲，再调用ReadLine()读取一行，有三个返回值，第二个是指是否是前缀，也就是说缓冲区可能装不下整行字符串。
2. 多行：bufio.NewScanner(os.Stdin)从缓冲区中读取多行，每次调用 input.Scan()，即 读入下一行 ，并移除行末的换行符。读取的内容可以调用 input.Text() 得到。Scan函数在读到一行时返回true，不再有输入时返回false。

fmt.Scan()
Scan 从标准输入扫描文本，读取由空白符分隔的值保存到传递给本函数的参数中，换行符视为空白符。本函数返回成功扫描的数据个数和遇到的任何错误。如果读取的数据个数比提供的参数少，会返回一个错误报告原因。

fmt.Scanf()
Scanf从标准输入扫描文本，根据format参数指定的格式去读取由空白符分隔的值保存到传递给本函数的参数中。本函数返回成功扫描的数据个数和遇到的任何错误。

fmt.Scanf和fmt.Scan不同于以简单的空格符作为输入数据的分隔符，fmt.Scanf为输入数据指定了具体的输入内容格式，只有按照格式输入数据才会被扫描并存入对应变量。

fmt.Scanln
Scanln类似于Scan，它遇到换行才停止扫描。最后一个数据后面必须有换行或者到达结束位置。本函数返回成功扫描的数据个数和遇到的任何错误。
## 反转字符串
### I
和反转链表相同，都用双指针法。
```go
func reverseString(s []byte)  {
    left:=0
    right:=len(s)-1
    for left<right{
        s[left],s[right]=s[right],s[left]
        left++
        right--
    }
}
```
### II
给定一个字符串 s 和一个整数 k，你需要对从字符串开头算起的每隔 2k 个字符的前 k 个字符进行反转。

如果剩余字符少于 k 个，则将剩余字符全部反转。

如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。

示例:
```
输入: s = "abcdefg", k = 2
输出: "bacdfeg"
```
```go
func reverseStr(s string, k int) string {
    ss := []byte(s)
    length := len(s)
    for i := 0; i < length; i += 2 * k {
        if i + k <= length {
            reverse(ss[i:i+k])
        } else {
            reverse(ss[i:length])
        }
    }
    return string(ss)
}

func reverse(b []byte) {
    left := 0
    right := len(b) - 1
    for left < right {
        b[left], b[right] = b[right], b[left]
        left++
        right--
    }
}
```
## 替换空格
```go
// 遍历添加
func replaceSpace(s string) string {
    b := []byte(s)
    result := make([]byte, 0)
    for i := 0; i < len(b); i++ {
        if b[i] == ' ' {
            result = append(result, []byte("%20")...)
        } else {
            result = append(result, b[i])
        }
    }
    return string(result)
}

// 原地修改
func replaceSpace(s string) string {
    b := []byte(s)
    length := len(b)
    spaceCount := 0
    // 计算空格数量
    for _, v := range b {
        if v == ' ' {
            spaceCount++
        }
    }
    // 扩展原有切片
    resizeCount := spaceCount * 2
    tmp := make([]byte, resizeCount)
    b = append(b, tmp...)
    i := length - 1
    j := len(b) - 1
    for i >= 0 {
        if b[i] != ' ' {
            b[j] = b[i]
            i--
            j--
        } else {
            b[j] = '0'
            b[j-1] = '2'
            b[j-2] = '%'
            i--
            j = j - 3
        }
    }
    return string(b)
}
```
## 翻转字符串里的单词
给定一个字符串，逐个翻转字符串中的每个单词。
```
输入: "the sky is blue"
输出: "blue is sky the"
```
```go
import (
	"fmt"
)

func reverseWords(s string) string {
	//1.使用双指针删除冗余的空格
	slowIndex, fastIndex := 0, 0
	b := []byte(s)
	//删除头部冗余空格
	for len(b) > 0 && fastIndex < len(b) && b[fastIndex] == ' ' {
		fastIndex++
	}
    //删除单词间冗余空格
	for ; fastIndex < len(b); fastIndex++ {
		if fastIndex-1 > 0 && b[fastIndex-1] == b[fastIndex] && b[fastIndex] == ' ' {
			continue
		}
		b[slowIndex] = b[fastIndex]
		slowIndex++
	}
	//删除尾部冗余空格
	if slowIndex-1 > 0 && b[slowIndex-1] == ' ' {
		b = b[:slowIndex-1]
	} else {
		b = b[:slowIndex]
	}
	//2.反转整个字符串
	reverse(&b, 0, len(b)-1)
	//3.反转单个单词  i单词开始位置，j单词结束位置
	i := 0
	for i < len(b) {
		j := i
		for ; j < len(b) && b[j] != ' '; j++ {
		}
		reverse(&b, i, j-1)
		i = j
		i++
	}
	return string(b)
}

func reverse(b *[]byte, left, right int) {
	for left < right {
		(*b)[left], (*b)[right] = (*b)[right], (*b)[left]
		left++
		right--
	}
}
```
## 左旋转字符串
```
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```
```go
func reverseLeftWords(s string, n int) string {
    b := []byte(s)
    // 1. 反转前n个字符
    // 2. 反转第n到end字符
    // 3. 反转整个字符
    reverse(b, 0, n-1)
    reverse(b, n, len(b)-1)
    reverse(b, 0, len(b)-1)
    return string(b)
}
// 切片是引用传递
func reverse(b []byte, left, right int){
    for left < right{
        b[left], b[right] = b[right],b[left]
        left++
        right--
    }
}
```
## 实现strStr()
给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1: 输入: haystack = "hello", needle = "ll" 输出: 2

示例 2: 输入: haystack = "aaaaa", needle = "bba" 输出: -1

说明: 当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。 对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。
### 什么是KMP
KMP算法是一种改进的字符串匹配算法，由D.E.Knuth，J.H.Morris和V.R.Pratt提出的，因此人们称它为克努特—莫里斯—普拉特操作（简称KMP算法）。KMP算法的核心是利用匹配失败后的信息，尽量减少模式串与主串的匹配次数以达到快速匹配的目的。具体实现就是通过一个next()函数实现，函数本身包含了模式串的局部匹配信息。KMP算法的时间复杂度O(m+n) [1]  。（来自百度百科）

KMP的经典思想就是:当出现字符串不匹配时，可以记录一部分之前已经匹配的文本内容，利用这些信息避免从头再去做匹配。

如何记录已经匹配的文本内容，是KMP的重点，也是next数组的任务。
### 什么是前缀表与next数组
next数组就是一个前缀表。

前缀表是用来回退的，它记录了模式串与主串(文本串)不匹配的时候，模式串应该从哪里开始重新匹配。

什么是前缀表：记录下标i之前（包括i）的字符串中，有多大长度的相同前缀后缀。

**最长公共前后缀**：字符串aa的最长相等前后缀为1。 字符串aaa的最长相等前后缀为2。 等等.....

前缀表要求的就是最长相同前后缀的长度。它能告诉我们上次匹配的位置。
- **下标5之前这部分的字符串（也就是字符串aabaa）的最长相等的前缀和后缀字符串是子字符串aa ，因为找到了最长相等的前缀和后缀，匹配失败的位置是后缀子串的后面，那么我们找到与其相同的前缀的后面从新匹配就可以了。**

前缀表与next数组有什么关系：
- next数组即可以就是前缀表，也可以是前缀表统一减一（右移一位，初始位置为-1）。

### 前缀表统一减一之后的next数组：
- 时间复杂度分析：
    - 其中n为文本串长度，m为模式串长度，因为在匹配的过程中，根据前缀表不断调整匹配的位置，可以看出匹配的过程是$O(n)$，之前还要单独生成next数组，时间复杂度是$O(m)$。所以整个KMP算法的时间复杂度是$O(n+m)$的。
    - 暴力的解法显而易见是$O(n × m)$，所以KMP在字符串匹配中极大的提高的搜索的效率。
    - 为了和力扣题目28.实现strStr保持一致，方便大家理解，以下文章统称haystack为文本串, needle为模式串。
    - 都知道使用KMP算法，一定要构造next数组
- 构造next数组：
    - 我们定义一个函数getNext来构建next数组，函数参数为指向next数组的指针，和一个字符串。 代码如下：
    ```c++
       void getNext(int* next, const string& s)
    ```
    - 构造next数组其实就是计算模式串s，前缀表的过程。 主要有如下三步：
        - 初始化
            - 定义两个指针i和j，j指向前缀起始位置，i指向后缀起始位置。
            - 然后还要对next数组进行初始化赋值，如下：
            ```c++
               int j = -1;
            next[0] = j;
            ```
            - j 为什么要初始化为 -1呢，因为之前说过 前缀表要统一减一的操作仅仅是其中的一种实现，我们这里选择j初始化为-1，下文我还会给出j不初始化为-1的实现代码。
            - next[i] 表示 i（包括i）之前最长相等的前后缀长度（其实就是j）
            - 所以初始化next[0] = j 。
        - 处理前后缀不相同的情况
            - 因为j初始化为-1，那么i就从1开始，进行s[i] 与 s[j+1]的比较。
            - 所以遍历模式串s的循环下标i 要从 1开始，代码如下：
            ```c++
               for(int i = 1; i < s.size(); i++) {
            ```
            - 如果 s[i] 与 s[j+1]不相同，也就是遇到 前后缀末尾不相同的情况，就要向前回退。
            - 怎么回退呢？
            - next[j]就是记录着j（包括j）之前的子串的相同前后缀的长度。
            - 那么 s[i] 与 s[j+1] 不相同，就要找 j+1前一个元素在next数组里的值（就是next[j]）。
            - 所以，处理前后缀不相同的情况代码如下：
            ```c++
               while (j >= 0 && s[i] != s[j + 1]) { // 前后缀不相同了
                j = next[j]; // 向前回退
            }
            ```
        - 处理前后缀相同的情况
            - 如果s[i] 与 s[j + 1] 相同，那么就同时向后移动i 和j 说明找到了相同的前后缀，同时还要将j（前缀的长度）赋给next[i], 因为next[i]要记录相同前后缀的长度。
            - 代码如下：
            ```c++
               if (s[i] == s[j + 1]) { // 找到相同的前后缀
                j++;
            }
            next[i] = j;
            ```
            - 最后整体构建next数组的函数代码如下：
            ```c++
               void getNext(int* next, const string& s){
                int j = -1;
                next[0] = j;
                for(int i = 1; i < s.size(); i++) { // **注意**i从1开始
                    while (j >= 0 && s[i] != s[j + 1]) { // 前后缀不相同了
                        j = next[j]; // 向前回退
                    }
                    if (s[i] == s[j + 1]) { // 找到相同的前后缀
                        j++;
                    }
                    next[i] = j; // 将j（前缀的长度）赋给next[i]
                }
            }
            ```
- 使用next数组进行匹配
    - 在文本串s里 找是否出现过模式串t。
    - 定义两个下标j 指向模式串起始位置，i指向文本串起始位置。
    - 那么j初始值依然为-1，为什么呢？ 依然因为next数组里记录的起始位置为-1。
    - i就从0开始，遍历文本串，代码如下：
    ```c++
       for (int i = 0; i < s.size(); i++) 
    ```
    - 接下来就是 s[i] 与 t[j + 1] （因为j从-1开始的） 进行比较。
    - 如果 s[i] 与 t[j + 1] 不相同，j就要从next数组里寻找下一个匹配的位置。
    - 代码如下：
    ```c++
       while(j >= 0 && s[i] != t[j + 1]) {
        j = next[j];
    }
    ```
    如果 s[i] 与 t[j + 1] 相同，那么i 和 j 同时向后移动， 代码如下：
    ```c++
       if (s[i] == t[j + 1]) {
        j++; // i的增加在for循环里
    }
    ```
    - 如何判断在文本串s里出现了模式串t呢，如果j指向了模式串t的末尾，那么就说明模式串t完全匹配文本串s里的某个子串了。
    - 本题要在文本串字符串中找出模式串出现的第一个位置 (从0开始)，所以返回当前在文本串匹配模式串的位置i 减去 模式串的长度，就是文本串字符串中出现模式串的第一个位置。
    - 代码如下：
    ```c++
       if (j == (t.size() - 1) ) {
        return (i - t.size() + 1);
    }
    ```
    - 那么使用next数组，用模式串匹配文本串的整体代码如下：
    ```c++
       int j = -1; // 因为next数组里记录的起始位置为-1
    for (int i = 0; i < s.size(); i++) { // **注意**i就从0开始
        while(j >= 0 && s[i] != t[j + 1]) { // 不匹配
            j = next[j]; // j 寻找之前匹配的位置
        }
        if (s[i] == t[j + 1]) { // 匹配，j和i同时向后移动
            j++; // i的增加在for循环里
        }
        if (j == (t.size() - 1) ) { // 文本串s里出现了模式串t
            return (i - t.size() + 1);
        }
    }
    ```
    此时所有逻辑的代码都已经写出来了，力扣 28.实现strStr 题目的整体代码如下：
    ```c++
       class Solution {
    public:
        void getNext(int* next, const string& s) {
            int j = -1;
            next[0] = j;
            for(int i = 1; i < s.size(); i++) { // **注意**i从1开始
                while (j >= 0 && s[i] != s[j + 1]) { // 前后缀不相同了
                    j = next[j]; // 向前回退
                }
                if (s[i] == s[j + 1]) { // 找到相同的前后缀
                    j++;
                }
                next[i] = j; // 将j（前缀的长度）赋给next[i]
            }
        }
        int strStr(string haystack, string needle) {
            if (needle.size() == 0) {
                return 0;
            }
            int next[needle.size()];
            getNext(next, needle);
            int j = -1; // // 因为next数组里记录的起始位置为-1
            for (int i = 0; i < haystack.size(); i++) { // **注意**i就从0开始
                while(j >= 0 && haystack[i] != needle[j + 1]) { // 不匹配
                    j = next[j]; // j 寻找之前匹配的位置
                }
                if (haystack[i] == needle[j + 1]) { // 匹配，j和i同时向后移动
                    j++; // i的增加在for循环里
                }
                if (j == (needle.size() - 1) ) { // 文本串s里出现了模式串t
                    return (i - needle.size() + 1);
                }
            }
            return -1;
        }
    };
    ```
### 前缀表不减一的next数组：
直接使用前缀表可以换一种回退方式，找j=next[j-1] 来进行回退。

主要就是j=next[x]这一步最为关键！

构建next数组：
```c++
void getNext(int* next, const string& s) {
    int j = 0;
    next[0] = 0;
    for(int i = 1; i < s.size(); i++) {
        while (j > 0 && s[i] != s[j]) { // j要保证大于0，因为下面有取j-1作为数组下标的操作
            j = next[j - 1]; // **注意**这里，是要找前一位的对应的回退位置了
        }
        if (s[i] == s[j]) {
            j++;
        }
        next[i] = j;
    }
}
```
使用next数组进行匹配：
```c++
class Solution {
public:
    void getNext(int* next, const string& s) {
        int j = 0;
        next[0] = 0;
        for(int i = 1; i < s.size(); i++) {
            while (j > 0 && s[i] != s[j]) {
                j = next[j - 1];
            }
            if (s[i] == s[j]) {
                j++;
            }
            next[i] = j;
        }
    }
    int strStr(string haystack, string needle) {
        if (needle.size() == 0) {
            return 0;
        }
        int next[needle.size()];
        getNext(next, needle);
        int j = 0;
        for (int i = 0; i < haystack.size(); i++) {
            while(j > 0 && haystack[i] != needle[j]) {
                j = next[j - 1];
            }
            if (haystack[i] == needle[j]) {
                j++;
            }
            if (j == needle.size() ) {
                return (i - needle.size() + 1);
            }
        }
        return -1;
    }
};
```
### kmp总结
介绍了什么是KMP，KMP可以解决什么问题，然后分析KMP算法里的next数组，知道了next数组就是前缀表，再分析为什么要是前缀表而不是什么其他表。

接着从给出的模式串中，我们一步一步的推导出了前缀表，得出前缀表无论是统一减一还是不减一得到的next数组仅仅是kmp的实现方式的不同。

其中还分析了KMP算法的时间复杂度，并且和暴力方法做了对比。

然后先用前缀表统一减一得到的next数组，求得文本串s里是否出现过模式串t，并给出了具体分析代码。

又给出了直接用前缀表作为next数组，来做匹配的实现代码。

可以说把KMP的每一个细微的细节都扣了出来，毫无遮掩的展示给大家了

**go实现**：
```go
// 方法一:前缀表使用减1实现

// getNext 构造前缀表next
// params:
//		  next 前缀表数组
//		  s 模式串
func getNext(next []int, s string) {
	j := -1 // j表示 最长相等前后缀长度
	next[0] = j

	for i := 1; i < len(s); i++ {
		for j >= 0 && s[i] != s[j+1] {
			j = next[j] // 回退前一位
		}
		if s[i] == s[j+1] {
			j++
		}
		next[i] = j // next[i]是i（包括i）之前的最长相等前后缀长度
	}
}
func strStr(haystack string, needle string) int {
	if len(needle) == 0 {
		return 0
	}
	next := make([]int, len(needle))
	getNext(next, needle)
	j := -1 // 模式串的起始位置 next为-1 因此也为-1
	for i := 0; i < len(haystack); i++ {
		for j >= 0 && haystack[i] != needle[j+1] {
			j = next[j] // 寻找下一个匹配点
		}
		if haystack[i] == needle[j+1] {
			j++
		}
		if j == len(needle)-1 { // j指向了模式串的末尾
			return i - len(needle) + 1
		}
	}
	return -1
}
// 方法二: 前缀表无减一或者右移

// getNext 构造前缀表next
// params:
//		  next 前缀表数组
//		  s 模式串
func getNext(next []int, s string) {
	j := 0
	next[0] = j
	for i := 1; i < len(s); i++ {
		for j > 0 && s[i] != s[j] {
			j = next[j-1]
		}
		if s[i] == s[j] {
			j++
		}
		next[i] = j
	}
}
func strStr(haystack string, needle string) int {
	n := len(needle)
	if n == 0 {
		return 0
	}
	j := 0
	next := make([]int, n)
	getNext(next, needle)
	for i := 0; i < len(haystack); i++ {
		for j > 0 && haystack[i] != needle[j] {
			j = next[j-1] // 回退到j的前一位
		}
		if haystack[i] == needle[j] {
			j++
		}
		if j == n {
			return i - n + 1
		}
	}
	return -1
}
```
## 重复的子字符串 
给定一个非空的字符串，判断它是否可以由它的一个子串重复多次构成。给定的字符串只含有小写英文字母，并且长度不超过10000。
```
示例 1:
输入: "abab"
输出: True
解释: 可由子字符串 "ab" 重复两次构成。

示例 2:
输入: "aba"
输出: False

示例 3:
输入: "abcabcabcabc"
输出: True
解释: 可由子字符串 "abc" 重复四次构成。 (或者子字符串 "abcabc" 重复两次构成。)
```
标准的KMP题目~

数组长度减去最长相同前后缀的长度相当于是第一个周期的长度，也就是一个周期的长度，如果这个周期可以被整除，就说明整个数组就是这个周期的循环。

强烈建议大家把next数组打印出来，看看next数组里的规律，有助于理解KMP算法

代码实现：
```go
//前缀表统一减一的实现
func repeatedSubstringPattern(s string) bool {
	n := len(s)
	if n == 0 {
		return false
	}
	next := make([]int, n)
	j := -1
	next[0] = j
	for i := 1; i < n; i++ {
		for j >= 0 && s[i] != s[j+1] {
			j = next[j]
		}
		if s[i] == s[j+1] {
			j++
		}
		next[i] = j
	}
	// next[n-1]+1 最长相同前后缀的长度
	if next[n-1] != -1 && n%(n-(next[n-1]+1)) == 0 {
		return true
	}
	return false
}
//前缀表不减一
func repeatedSubstringPattern(s string) bool {
	n := len(s)
	if n == 0 {
		return false
	}
	j := 0
	next := make([]int, n)
	next[0] = j
	for i := 1; i < n; i++ {
		for j > 0 && s[i] != s[j] {
			j = next[j-1]
		}
		if s[i] == s[j] {
			j++
		}
		next[i] = j
	}
	// next[n-1]  最长相同前后缀的长度
	if next[n-1] != 0 && n%(n-next[n-1]) == 0 {
		return true
	}
	return false
}
```

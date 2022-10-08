# Algorithm_stackAndQueue

> 学习[代码随想录](https://programmercarl.com/)笔记
# 栈和队列
需要知道栈和队列的底层实现，不同编程语言不同STL的实现原理都是不尽相同的。
## 理论基础(c++)
栈其实就是递归的一种实现结构。

**栈（本身可以说是一个容器适配器）是以底层容器（数组或者链表）完成其所有的工作，对外提供统一的接口，底层容器是可插拔的（也就是说我们可以控制使用哪种容器来实现栈的功能）。**
```
SGI -- Silicon Graphics [Computer System] Inc. 硅图[计算机系统] 公司.
STL -- Standard Template Library 标准模板库。
SGI STL -- SGI的标准模板库。
SGI的全称 -- 硅图[计算机系统] 公司。
```
我们常用的SGI STL，如果没有指定底层实现的话，默认是以deque为缺省情况下栈的低层结构。

deque是一个双向队列，只要封住一段，只开通另一端就可以实现栈的逻辑了。

SGI STL中 队列底层实现缺省情况下一样使用deque实现的。

我们也可以指定vector为栈的底层实现，初始化语句如下：
```
c++
std::stack<int, std::vector<int> > third;  // 使用vector为底层容器的栈
```
对应的队列的情况是一样的。

队列中先进先出的数据结构，同样不允许有遍历行为，不提供迭代器, SGI STL中队列一样是以deque为缺省情况下的底部结构。

也可以指定list 为起底层实现，初始化queue的语句如下：
```
c++
std::queue<int, std::list<int>> third; // 定义以list为底层容器的队列
```
所以STL 队列也不被归类为容器，而被归类为container adapter（ 容器适配器）。
## 用栈实现队列
使用栈实现队列的下列操作：

push(x) -- 将一个元素放入队列的尾部。
pop() -- 从队列首部移除元素。
peek() -- 返回队列首部的元素。
empty() -- 返回队列是否为空。

示例:
```
c++
MyQueue queue = new MyQueue();
queue.push(1);
queue.push(2);
queue.peek();  // 返回 1
queue.pop();   // 返回 1
queue.empty(); // 返回 false
```
说明:
- 你只能使用标准的栈操作 -- 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。
- 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。
- 假设所有操作都是有效的 （例如，一个空的队列不会调用 pop 或者 peek 操作）
```go
type MyQueue struct {
    stack []int
    back  []int
}

/** Initialize your data structure here. */
func Constructor() MyQueue {
    return MyQueue{
        stack: make([]int, 0),
        back:  make([]int, 0),
    }
}

/** Push element x to the back of queue. */
func (this *MyQueue) Push(x int) {
    for len(this.back) != 0 {
        val := this.back[len(this.back)-1]
        this.back = this.back[:len(this.back)-1]
        this.stack = append(this.stack, val)
    }
    this.stack = append(this.stack, x)
}

/** Removes the element from in front of queue and returns that element. */
func (this *MyQueue) Pop() int {
    for len(this.stack) != 0 {
        val := this.stack[len(this.stack)-1]
        this.stack = this.stack[:len(this.stack)-1]
        this.back = append(this.back, val)
    }
    if len(this.back) == 0 {
        return 0
    }
    val := this.back[len(this.back)-1]
    this.back = this.back[:len(this.back)-1]
    return val
}

/** Get the front element. */
func (this *MyQueue) Peek() int {
    for len(this.stack) != 0 {
        val := this.stack[len(this.stack)-1]
        this.stack = this.stack[:len(this.stack)-1]
        this.back = append(this.back, val)
    }
    if len(this.back) == 0 {
        return 0
    }
    val := this.back[len(this.back)-1]
    return val
}

/** Returns whether the queue is empty. */
func (this *MyQueue) Empty() bool {
    return len(this.stack) == 0 && len(this.back) == 0
}
```
## 用队列实现栈
使用队列实现栈的下列操作：
```
push(x) -- 元素 x 入栈
pop() -- 移除栈顶元素
top() -- 获取栈顶元素
empty() -- 返回栈是否为空
```
**注意**:
- 你只能使用队列的基本操作-- 也就是 push to back, peek/pop from front, size, 和 is empty 这些操作是合法的。
- 你所使用的语言也许不支持队列。 你可以使用 list 或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。
- 你可以假设所有操作都是有效的（例如, 对一个空的栈不会调用 pop 或者 top 操作）

用两个队列实现：
```go
type MyStack struct {
    //创建两个队列
    queue1 []int
    queue2 []int
}


func Constructor() MyStack {
    return MyStack{	//初始化
        queue1:make([]int,0),
        queue2:make([]int,0),
    }
}


func (this *MyStack) Push(x int)  {
     //先将数据存在queue2中
    this.queue2 = append(this.queue2,x)	
   //将queue1中所有元素移到queue2中，再将两个队列进行交换
    this.Move() 
}


func (this *MyStack) Move(){    
    if len(this.queue1) == 0{
        //交换，queue1置为queue2,queue2置为空
        this.queue1,this.queue2 = this.queue2,this.queue1
    }else{
        //queue1元素从头开始一个一个追加到queue2中
            this.queue2 = append(this.queue2,this.queue1[0])  
            this.queue1 = this.queue1[1:]	//去除第一个元素
            this.Move()     //重复
    }
}

func (this *MyStack) Pop() int {
    val := this.queue1[0]
    this.queue1 = this.queue1[1:]	//去除第一个元素
    return val

}


func (this *MyStack) Top() int {
    return this.queue1[0]	//直接返回
}


func (this *MyStack) Empty() bool {
return len(this.queue1) == 0
}


/**
 * Your MyStack object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Top();
 * param_4 := obj.Empty();
 */
 ```
 用一个队列实现：
 ```go
 type MyStack struct {
    queue []int//创建一个队列
}


/** Initialize your data structure here. */
func Constructor() MyStack {
    return MyStack{   //初始化
        queue:make([]int,0),
    }
}


/** Push element x onto stack. */
func (this *MyStack) Push(x int)  {
    //添加元素
    this.queue=append(this.queue,x)
}


/** Removes the element on top of the stack and returns that element. */
func (this *MyStack) Pop() int {
    n:=len(this.queue)-1//判断长度
    for n!=0{ //除了最后一个，其余的都重新添加到队列里
        val:=this.queue[0]
        this.queue=this.queue[1:]
        this.queue=append(this.queue,val)
        n--
    }
    //弹出元素
    val:=this.queue[0]
    this.queue=this.queue[1:]
    return val
    
}


/** Get the top element. */
func (this *MyStack) Top() int {
    //利用Pop函数，弹出来的元素重新添加
    val:=this.Pop()
    this.queue=append(this.queue,val)
    return val
}


/** Returns whether the stack is empty. */
func (this *MyStack) Empty() bool {
    return len(this.queue)==0
}


/**
 * Your MyStack object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Top();
 * param_4 := obj.Empty();
 */
 ```
## 有效的括号
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：
- 左括号必须用相同类型的右括号闭合。
- 左括号必须以正确的顺序闭合。
- **注意**空字符串可被认为是有效字符串。

栈的经典问题。
```go
func isValid(s string) bool {
    hash := map[byte]byte{')':'(', ']':'[', '}':'{'}
    stack := make([]byte, 0)
    if s == "" {
        return true
    }

    for i := 0; i < len(s); i++ {
        if s[i] == '(' || s[i] == '[' || s[i] == '{' {
            stack = append(stack, s[i])
        } else if len(stack) > 0 && stack[len(stack)-1] == hash[s[i]] {
            stack = stack[:len(stack)-1]
        } else {
            return false
        }
    }
    return len(stack) == 0
}
```
## 删除字符串中的所有相邻重复项
给出由小写字母组成的字符串 S，重复项删除操作会选择两个相邻且相同的字母，并删除它们。

在 S 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

示例：
```
输入："abbaca"
输出："ca"
解释：例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
```
提示：
- 1 <= S.length <= 20000
- S 仅由小写英文字母组成
```go
func removeDuplicates(s string) string {
    var stack []byte
    for i := 0; i < len(s);i++ {
        // 栈不空 且 与栈顶元素不等
        if len(stack) > 0 && stack[len(stack)-1] == s[i] {
            // 弹出栈顶元素 并 忽略当前元素(s[i])
            stack = stack[:len(stack)-1]
        }else{
            // 入栈
            stack = append(stack, s[i])
        }
    }
    return string(stack)
}
```
## 逆波兰表达式求值
根据 逆波兰表示法，求表达式的值。

有效的运算符包括 + ,  - ,  * ,  / 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

说明：

整数除法只保留整数部分。 给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。

示例 1：
```
输入: ["2", "1", "+", "3", " * "]
输出: 9
解释: 该算式转化为常见的中缀算术表达式为：((2 + 1) * 3) = 9
```
逆波兰表达式：是一种后缀表达式，所谓后缀就是指算符写在后面。

平常使用的算式则是一种中缀表达式，如 ( 1 + 2 ) * ( 3 + 4 ) 。

该算式的逆波兰表达式写法为 ( ( 1 2 + ) ( 3 4 + ) * ) 。

逆波兰表达式主要有以下两个优点：
- 去掉括号后表达式无歧义，上式即便写成 1 2 + 3 4 + * 也可以依据次序计算出正确结果。
- **适合用栈操作运算：遇到数字则入栈；遇到算符则取出栈顶两个数字进行计算，并将结果压入栈中**
```go
func evalRPN(tokens []string) int {
	stack := []int{}
	for _, token := range tokens {
		val, err := strconv.Atoi(token)
		if err == nil {
			stack = append(stack, val)
		} else {
			num1, num2 := stack[len(stack)-2], stack[(len(stack))-1]
			stack = stack[:len(stack)-2]
			switch token {
			case "+":
				stack = append(stack, num1+num2)
			case "-":
				stack = append(stack, num1-num2)
			case "*":
				stack = append(stack, num1*num2)
			case "/":
				stack = append(stack, num1/num2)
			}
		}
	}
	return stack[0]
}
```
## 滑动窗口最大值
给定一个数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。

每个窗口都有一个最大值，返回滑动窗口中的最大值数组。

进阶：你能在线性时间复杂度内解决此题吗？
```go
// 封装单调队列的方式解题
type MyQueue struct {
    queue []int
}

func NewMyQueue() *MyQueue {
    return &MyQueue{
        queue: make([]int, 0),
    }
}

func (m *MyQueue) Front() int {
    return m.queue[0]
}

func (m *MyQueue) Back() int {
    return m.queue[len(m.queue)-1]
}

func (m *MyQueue) Empty() bool {
    return len(m.queue) == 0
}

func (m *MyQueue) Push(val int) {
    for !m.Empty() && val > m.Back() {
        m.queue = m.queue[:len(m.queue)-1]
    }
    m.queue = append(m.queue, val)
}

func (m *MyQueue) Pop(val int) {
    if !m.Empty() && val == m.Front() {
        m.queue = m.queue[1:]
    }
}

func maxSlidingWindow(nums []int, k int) []int {
    queue := NewMyQueue()
    length := len(nums)
    res := make([]int, 0)
    // 先将前k个元素放入队列
    for i := 0; i < k; i++ {
        queue.Push(nums[i])
    }
    // 记录前k个元素的最大值
    res = append(res, queue.Front())

    for i := k; i < length; i++ {
        // 滑动窗口移除最前面的元素
        queue.Pop(nums[i-k])
        // 滑动窗口添加最后面的元素
        queue.Push(nums[i])
        // 记录最大值
        res = append(res, queue.Front())
    }
    return res
}
```
## 前K个高频元素
给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

示例 1:
```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```
```go
//方法一：小顶堆
func topKFrequent(nums []int, k int) []int {
    map_num:=map[int]int{}
    //记录每个元素出现的次数
    for _,item:=range nums{
        map_num[item]++
    }
    h:=&IHeap{}
    heap.Init(h)
    //所有元素入堆，堆的长度为k
    for key,value:=range map_num{
        heap.Push(h,[2]int{key,value})
        if h.Len()>k{
            heap.Pop(h)
        }
    }
    res:=make([]int,k)
    //按顺序返回堆中的元素
    for i:=0;i<k;i++{
        res[k-i-1]=heap.Pop(h).([2]int)[0]
    }
    return res
}

//构建小顶堆
type IHeap [][2]int

func (h IHeap) Len()int {
    return len(h)
}

func (h IHeap) Less (i,j int) bool {
    return h[i][1]<h[j][1]
}

func (h IHeap) Swap(i,j int) {
    h[i],h[j]=h[j],h[i]
}

func (h *IHeap) Push(x interface{}){
    *h=append(*h,x.([2]int))
}
func (h *IHeap) Pop() interface{}{
    old:=*h
    n:=len(old)
    x:=old[n-1]
    *h=old[0:n-1]
    return x
}


//方法二:利用O(logn)排序
func topKFrequent(nums []int, k int) []int {
    ans:=[]int{}
    map_num:=map[int]int{}
    for _,item:=range nums {
        map_num[item]++
    }
    for key,_:=range map_num{
        ans=append(ans,key)
    }
    //核心思想：排序
    //可以不用包函数，自己实现快排
    sort.Slice(ans,func (a,b int)bool{
        return map_num[ans[a]]>map_num[ans[b]]
    })
    return ans[:k]
}
```


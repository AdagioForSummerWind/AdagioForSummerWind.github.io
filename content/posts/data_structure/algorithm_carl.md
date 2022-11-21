---
title: "Algorithm_carl"
date: 2022-01-06T08:17:33+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: algorithm-carl 
draft: false
---
> 学习[代码随想录](https://programmercarl.com/)笔记

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

## 数组
### 数组基础
数组是存放在连续内存空间上的相同类型数据的集合。 

因为数组的在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。

数组元素无法删除，只能覆盖。
### 二分查找
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。



有序数组、无重复元素便可想到用二分法查找。**注意**区间左闭右闭还是左闭右开。
简单实现：
```go

package main



func search(nums []int, target int) int {
    left := 0
    right := len(nums) - 1
    for left <= right {
        middle := left + (right - left)/2
        if nums[middle] > target {
            right = middle - 1
        }else if nums[middle] < target {
            left = middle + 1
        }else{
            return middle;
        }
    }
    return -1
}


func search(nums []int,t int)int{
    left,middle:=0
    right:=len(nums)
    for left<=right{
        num=(left+right)/2
        if nums[num]==t{
            return num
        }
        if nums[num]>t{
            right=num-1
        }else{
            left=num+1
        }
    }
}

func search(nums []int,t int)int{
    if middle:=len(nums)/2;nums[middle]==t{
        return middle
    }
    if len(nums)==1{
        return -1
    }
    if nums[middle]>t{
        return search(nums[:middle],t)
    }else{
        return search(nums[middle+1:])
    }
    
}
```
### 移除元素

给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并原地修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

示例 1: 给定 nums = [3,2,2,3], val = 3, 函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。 你不需要考虑数组中超出新长度后面的元素。

示例 2: 给定 nums = [0,1,2,2,3,0,4,2], val = 2, 函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

你不需要考虑数组中超出新长度后面的元素

双指针法（快慢指针法）： 通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。
```go
func removeElement(nums []int, val int) int {
    length:=len(nums)
    res:=0
    for i:=0;i<length;i++{
        if nums[i]!=val {
            nums[res]=nums[i]
            res++
        }
    }
    return res
}

func removeElement(nums []int, val int) int {
   
    num := 0

    for i, v := range nums {
        if v == val {
            num++
        } else {
            nums[i-num] = v
        }
    }
    return len(nums)-num
}
```
### 有序数组的平方
给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。

示例 1： 输入：nums = [-4,-1,0,3,10] 输出：[0,1,9,16,100] 解释：平方后，数组变为 [16,1,0,9,100]，排序后，数组变为 [0,1,9,16,100]

示例 2： 输入：nums = [-7,-3,2,3,11] 输出：[4,9,9,49,121]



双指针法：
```go
func sortedSquares(nums []int) []int {
	n := len(nums)
	i, j, k := 0, n-1, n-1
	ans := make([]int, n)
	for i <= j {
		lm, rm := nums[i]*nums[i], nums[j]*nums[j]
		if lm > rm {
			ans[k] = lm
			i++
		} else {
			ans[k] = rm
			j--
		}
		k--
	}
	return ans
}
```
### 长度最小的子数组
给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。

示例：

输入：s = 7, nums = [2,3,1,2,4,3] 输出：2 解释：子数组 [4,3] 是该条件下的长度最小的子数组

**滑动窗口**
```go
func minSubArrayLen(target int, nums []int) int {
    i := 0
    l := len(nums)  // 数组长度
    sum := 0        // 子数组之和
    result := l + 1 // 初始化返回长度为l+1，目的是为了判断“不存在符合条件的子数组，返回0”的情况
    for j := 0; j < l; j++ {
        sum += nums[j]
        for sum >= target {
            subLength := j - i + 1
            if subLength < result {
                result = subLength
            }
            sum -= nums[i]
            i++
        }
    }
    if result == l+1 {
        return 0
    } else {
        return result
    }
}
```
### 螺旋矩阵II
给定一个正整数 n，生成一个包含 1 到 n^2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

示例:

输入: 3 输出: [ [ 1, 2, 3 ], [ 8, 9, 4 ], [ 7, 6, 5 ] ]

#思路

模拟行为：
```go
func generateMatrix(n int) [][]int {
    top, bottom := 0, n-1
    left, right := 0, n-1
    num := 1
    tar := n * n
    matrix := make([][]int, n)
    for i := 0; i < n; i++ {
        matrix[i] = make([]int, n)
    }
    for num <= tar {
        for i := left; i <= right; i++ {
            matrix[top][i] = num
            num++
        }
        top++
        for i := top; i <= bottom; i++ {
            matrix[i][right] = num
            num++
        }
        right--
        for i := right; i >= left; i-- {
            matrix[bottom][i] = num
            num++
        }
        bottom--
        for i := bottom; i >= top; i-- {
            matrix[i][left] = num
            num++
        }
        left++
    }
    return matrix
}
```
### 总结

## 链表
### 链表理论基础
 循环链表可以用来解决约瑟夫环问题。

 链表和数组对比：
 - 数组
    - 插入删除时间复杂度：$O(n)$
    - 查询时间复杂度：$O(1)$
    - 适用场景：数据量固定，频繁查询，较少增删
- 链表
    - 插入删除时间复杂度：$O(1)$
    - 查询时间复杂度：$O(n)$
    - 适用场景：数据量不固定，频繁增删，较少查询
### 移除链表元素

题意：删除链表中等于给定值 val 的所有节点。

示例 1：
输入：head = [1,2,6,3,4,5,6], val = 6
输出：[1,2,3,4,5]

示例 2：
输入：head = [], val = 1
输出：[]

示例 3：
输入：head = [7,7,7,7], val = 7
输出：[]
```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeElements(head *ListNode, val int) *ListNode {
    dummyHead := &ListNode{}
    dummyHead.Next = head
    cur := dummyHead
    for cur != nil && cur.Next != nil {
        if cur.Next.Val == val {
            cur.Next = cur.Next.Next
        } else {
            cur = cur.Next
        }
    }
    return dummyHead.Next
}
//如果是c或者c++要在删除后free或者delete掉节点释放内存，java,python,go都会自动回收
```
另外，移除头节点的话，如果没有虚拟头节点，将头节点往后移一个节点，有虚拟头节点就一般删除节点即可。
### 设计链表
设计五个接口：
- 获取链表第index个节点的数值
- 在链表的最前面插入一个节点
- 在链表的最后面插入一个节点
- 在链表第index个节点前面插入一个节点
- 删除链表的第index个节点

链表操作的两种方式：
- 直接使用原来的链表来进行操作。
- 设置一个虚拟头结点在进行操作。（更方便一点）


题意：

在链表类中实现这些功能：

get(index)：获取链表中第 index 个节点的值。如果索引无效，则返回-1。
addAtHead(val)：在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。
addAtTail(val)：将值为 val 的节点追加到链表的最后一个元素。
addAtIndex(index,val)：在链表中的第 index 个节点之前添加值为 val  的节点。如果 index 等于链表的长度，则该节点将附加到链表的末尾。如果 index 大于链表长度，则不会插入节点。如果index小于0，则在头部插入节点。
deleteAtIndex(index)：如果索引 index 有效，则删除链表中的第 index 个节点。
```go
//循环双链表
type MyLinkedList struct {
	dummy *Node
}

type Node struct {
	Val  int
	Next *Node
	Pre  *Node
}

//仅保存哑节点，pre-> rear, next-> head
/** Initialize your data structure here. */
func Constructor() MyLinkedList {
	rear := &Node{
		Val:  -1,
		Next: nil,
		Pre:  nil,
	}
	rear.Next = rear
	rear.Pre = rear
	return MyLinkedList{rear}
}

/** Get the value of the index-th node in the linked list. If the index is invalid, return -1. */
func (this *MyLinkedList) Get(index int) int {
	head := this.dummy.Next
	//head == this, 遍历完全
	for head != this.dummy && index > 0 {
		index--
		head = head.Next
	}
	//否则, head == this, 索引无效
	if 0 != index {
		return -1
	}
	return head.Val
}

/** Add a node of value val before the first element of the linked list. After the insertion, the new node will be the first node of the linked list. */
func (this *MyLinkedList) AddAtHead(val int) {
	dummy := this.dummy
	node := &Node{
		Val: val,
		//head.Next指向原头节点
		Next: dummy.Next,
		//head.Pre 指向哑节点
		Pre: dummy,
	}

	//更新原头节点
	dummy.Next.Pre = node
	//更新哑节点
	dummy.Next = node
	//以上两步不能反
}

/** Append a node of value val to the last element of the linked list. */
func (this *MyLinkedList) AddAtTail(val int) {
	dummy := this.dummy
	rear := &Node{
		Val: val,
		//rear.Next = dummy(哑节点)
		Next: dummy,
		//rear.Pre = ori_rear
		Pre: dummy.Pre,
	}

	//ori_rear.Next = rear
	dummy.Pre.Next = rear
	//update dummy
	dummy.Pre = rear
	//以上两步不能反
}

/** Add a node of value val before the index-th node in the linked list. If index equals to the length of linked list, the node will be appended to the end of linked list. If index is greater than the length, the node will not be inserted. */
func (this *MyLinkedList) AddAtIndex(index int, val int) {
	head := this.dummy.Next
	//head = MyLinkedList[index]
	for head != this.dummy && index > 0 {
		head = head.Next
		index--
	}
	if index > 0 {
		return
	}
	node := &Node{
		Val: val,
		//node.Next = MyLinkedList[index]
		Next: head,
		//node.Pre = MyLinkedList[index-1]
		Pre: head.Pre,
	}
	//MyLinkedList[index-1].Next = node
	head.Pre.Next = node
	//MyLinkedList[index].Pre = node
	head.Pre = node
	//以上两步不能反
}

/** Delete the index-th node in the linked list, if the index is valid. */
func (this *MyLinkedList) DeleteAtIndex(index int) {
	//链表为空
	if this.dummy.Next == this.dummy {
		return
	}
	head := this.dummy.Next
	//head = MyLinkedList[index]
	for head.Next != this.dummy && index > 0 {
		head = head.Next
		index--
	}
	//验证index有效
	if index == 0 {
		//MyLinkedList[index].Pre = index[index-2]
		head.Next.Pre = head.Pre
		//MyLinedList[index-2].Next = index[index]
		head.Pre.Next = head.Next
		//以上两步顺序无所谓
	}
}
```
### 翻转链表
```go
//双指针
func reverseList(head *ListNode) *ListNode {
    var pre *ListNode
    cur := head
    for cur != nil {
        next := cur.Next
        cur.Next = pre
        pre = cur
        cur = next
    }
    return pre
}

//递归
func reverseList(head *ListNode) *ListNode {
    return help(nil, head)
}

func help(pre, head *ListNode)*ListNode{
    if head == nil {
        return pre
    }
    next := head.Next
    head.Next = pre
    return help(head, next)
}
```
### 两两交换链表中的节点

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

正常模拟，使用虚拟头节点。
```go
func swapPairs(head *ListNode) *ListNode {
    dummy := &ListNode{
        Next: head,
    }
    //head=list[i]
    //pre=list[i-1]
    pre := dummy 
    for head != nil && head.Next != nil {
        pre.Next = head.Next
        next := head.Next.Next
        head.Next.Next = head
        head.Next = next
        //pre=list[(i+2)-1]
        pre = head 
        //head=list[(i+2)]
        head = next
    }
    return dummy.Next
}
```
```go
// 递归版本
func swapPairs(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }
    next := head.Next
    head.Next = swapPairs(next.Next)
    next.Next = head
    return next
}
```
### 删除链表的倒数第N个节点
给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

进阶：你能尝试使用一趟扫描实现吗？

双指针很好做：
```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    dummyHead := &ListNode{}
    dummyHead.Next = head
    cur := head
    prev := dummyHead
    i := 1
    for cur != nil {
        cur = cur.Next
        if i > n {
            prev = prev.Next
        }
        i++
    }
    prev.Next = prev.Next.Next
    return dummyHead.Next
}
```
### 链表相交
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 null 。

图示两个链表在节点 c1 开始相交：



题目数据 保证 整个链式结构中不存在环。

注意，函数返回结果后，链表必须 保持其原始结构 。

```go
func getIntersectionNode(headA, headB *ListNode) *ListNode {
    curA := headA
    curB := headB
    lenA, lenB := 0, 0
    // 求A，B的长度
    for curA != nil {
        curA = curA.Next
        lenA++
    }
    for curB != nil {
        curB = curB.Next
        lenB++
    }
    var step int
    var fast, slow *ListNode
    // 请求长度差，并且让更长的链表先走相差的长度
    if lenA > lenB {
        step = lenA - lenB
        fast, slow = headA, headB
    } else {
        step = lenB - lenA
        fast, slow = headB, headA
    }
    for i:=0; i < step; i++ {
        fast = fast.Next
    }
    // 遍历两个链表遇到相同则跳出遍历
    for fast != slow {
        fast = fast.Next
        slow = slow.Next
    }
    return fast
}
```
### 环形链表II
题意： 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

说明：不允许修改给定的链表。

- 判断是否有环：快慢指针法
- 如何确定环的入口：一个简单的数学题
```go
func detectCycle(head *ListNode) *ListNode {
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            for slow != head {
                slow = slow.Next
                head = head.Next
            }
            return head
        }
    }
    return nil
}
```
### 如何实现LRU缓存淘汰算法?

缓存是一种提高数据读取性能的技术，在硬件设计、软件开发中都有着非常广泛的应用，比如常见的 CPU 缓存、数据库缓存、浏览器缓存等等。


缓存的大小有限，当缓存被用满时，哪些数据应该被清理出去，哪些数据应该被保留？这就需要缓存淘汰策略来决定。常见的策略有三种：先进先出策略 FIFO（First In，First Out）、最少使用策略 LFU（Least Frequently Used）、最近最少使用策略 LRU（Least Recently Used）。


### 链表结构

我们先从底层的存储结构上来看一看。从图中我们看到，数组需要一块连续的内存空间来存储，对内存的要求比较高。如果我们申请一个 100MB 大小的数组，当内存中没有连续的、足够大的存储空间时，即便内存的剩余总可用空间大于 100MB，仍然会申请失败。而链表恰恰相反，它并不需要一块连续的内存空间，它通过“指针”将一组零散的内存块串联起来使用，所以如果我们申请的是 100MB 大小的链表，根本不会有问题。

链表结构五花八门，今天我重点给你介绍三种最常见的链表结构，它们分别是：单链表、双向链表和循环链表。我们首先来看最简单、最常用的单链表。

其中有两个结点是比较特殊的，它们分别是第一个结点和最后一个结点。我们习惯性地把第一个结点叫作头结点，把最后一个结点叫作尾结点。其中，头结点用来记录链表的基地址。有了它，我们就可以遍历得到整条链表。而尾结点特殊的地方是：指针不是指向下一个结点，而是指向一个空地址 NULL，表示这是链表上最后一个结点。


循环链表是一种特殊的单链表。实际上，循环链表也很简单。它跟单链表唯一的区别就在尾结点。我们知道，单链表的尾结点指针指向空地址，表示这就是最后的结点了。而循环链表的尾结点指针是指向链表的头结点。从我画的循环链表图中，你应该可以看出来，它像一个环一样首尾相连，所以叫作“循环”链表。




接下来我们再来看一个稍微复杂的，在实际的软件开发中，也更加常用的链表结构：双向链表。

相比单链表，双向链表适合解决哪种问题呢？

从结构上来看，双向链表可以支持 O(1) 时间复杂度的情况下找到前驱结点，正是这样的特点，也使双向链表在某些情况下的插入、删除等操作都要比单链表简单、高效。


对于执行较慢的程序，可以通过消耗更多的内存（空间换时间）来进行优化；而消耗过多内存的程序，可以通过消耗更多的时间（时间换空间）来降低内存的消耗

如何基于链表实现 LRU 缓存淘汰算法？我的思路是这样的：我们维护一个有序单链表，越靠近链表尾部的结点是越早之前访问的。当有一个新的数据被访问时，我们从链表头开始顺序遍历链表。
1. 如果此数据之前已经被缓存在链表中了，我们遍历得到这个数据对应的结点，并将其从原来的位置删除，然后再插入到链表的头部。
2. 如果此数据没有在缓存链表中，又可以分为两种情况：

如果此时缓存未满，则将此结点直接插入到链表的头部；如果此时缓存已满，则链表尾结点删除，将新的数据结点插入链表的头部。

现在我们来看下缓存访问的时间复杂度是多少。因为不管缓存有没有满，我们都需要遍历一遍链表，所以这种基于链表的实现思路，缓存访问的时间复杂度为 O(n)。实际上，我们可以继续优化这个实现思路，比如引入散列表（Hash table）来记录每个数据的位置，将缓存访问的时间复杂度降到 O(1)。因为要涉及我们还没有讲到的数据结构，所以这个优化方案，我现在就不详细说了，等讲到散列表的时候，我会再拿出来讲。

除了基于链表的实现思路，实际上还可以用数组来实现 LRU 缓存淘汰策略。如何利用数组实现 LRU 缓存淘汰策略呢？

## 哈希表
### 哈希表理论基础
哈希表是根据关键码的值而直接进行访问的数据结构，比如数组、map（映射）、set（集合）。

一般用来快速判断一个元素是否出现在集合里。

比如把字符串映射为索引的例子：
- 通过哈希函数/hashCode将字符串转化为数值
- 如果得到的数值大于哈希表的大小了，取模，保证所有字符串映射到哈希表上。
- 如果字符串数量都大于哈希表的大小了，会出现同一索引不同字符串的情况。也称，**哈希碰撞**。

解决哈希碰撞：
1. 拉链法
    - 发生冲突的元素用链表存储
    - 要选择适当的哈希表的大小，这样既不会因为数组空值而浪费大量内存，也不会因为链表太长而在查找上浪费太多时间。
2. 线性探测法
    - 保证tableSize大于dataSize，避免碰撞。

### 有效的字母异位词
给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

示例 1: 输入: s = "anagram", t = "nagaram" 输出: true

示例 2: 输入: s = "rat", t = "car" 输出: false

说明: 你可以假设字符串只包含小写字母。

```go
func isAnagram(s string, t string) bool {
    if len(s)!=len(t){
        return false
    }
    exists := make(map[byte]int)
    for i:=0;i<len(s);i++{
        if v,ok:=exists[s[i]];v>=0&&ok{
            exists[s[i]]=v+1
        }else{
            exists[s[i]]=1
        }
    }
    for i:=0;i<len(t);i++{
        if v,ok:=exists[t[i]];v>=1&&ok{
            exists[t[i]]=v-1
        }else{
            return false
        }
    }
    return true
}
```
### 两个数组的交集
使用数组来做哈希的题目，是因为题目都限制了数值的大小。

而这道题目没有限制数值的大小，就无法使用数组来做哈希表了。
```go
func intersection(nums1 []int, nums2 []int) []int {
    m := make(map[int]int)
    for _, v := range nums1 {
        m[v] = 1
    }
    var res []int
    // 利用count>0，实现重复值只拿一次放入返回结果中
    for _, v := range nums2 {
        if count, ok := m[v]; ok && count > 0 {
            res = append(res, v)
            m[v]--
        }
    }
    return res
}
//优化版，利用set，减少count统计
func intersection(nums1 []int, nums2 []int) []int {
    set:=make(map[int]struct{},0)
    res:=make([]int,0)
    for _,v:=range nums1{
        if _,ok:=set[v];!ok{
            set[v]=struct{}{}
        }
    }
    for _,v:=range nums2{
        //如果存在于上一个数组中，则加入结果集，并清空该set值
        if _,ok:=set[v];ok{
            res=append(res,v)
            delete(set, v)
        }
    }
    return res
}
```
### 快乐数
编写一个算法来判断一个数 n 是不是快乐数。

「快乐数」定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是 **无限循环** 但始终变不到 1。如果 可以变为  1，那么这个数就是快乐数。

如果 n 是快乐数就返回 True ；不是，则返回 False 。

示例：
```
输入：19
输出：true
解释：
1^2 + 9^2 = 82
8^2 + 2^2 = 68
6^2 + 8^2 = 100
1^2 + 0^2 + 0^2 = 1
```
```go
func isHappy(n int) bool {
    m := make(map[int]bool)
    for n != 1 && !m[n] {
        n, m[n] = getSum(n), true
    }
    return n == 1
}

func getSum(n int) int {
    sum := 0
    for n > 0 {
        sum += (n % 10) * (n % 10)
        n = n / 10
    }
    return sum
}
```
### 两数之和
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

示例:

给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9

所以返回 [0, 1]
```go
func twoSum(nums []int, target int) []int {
    for k1, _ := range nums {
        for k2 := k1 + 1; k2 < len(nums); k2++ {
            if target == nums[k1] + nums[k2] {
                return []int{k1, k2}
            }
        }
    }
    return []int{}
}
// 使用map方式解题，降低时间复杂度
func twoSum(nums []int, target int) []int {
    m := make(map[int]int)
    for index, val := range nums {
        if preIndex, ok := m[target-val]; ok {
            return []int{preIndex, index}
        } else {
            m[val] = index
        }
    }
    return []int{}
}
```
### 四数相加II
给定四个包含整数的数组列表 A , B , C , D ,计算有多少个元组 (i, j, k, l) ，使得 A[i] + B[j] + C[k] + D[l] = 0。

为了使问题简单化，所有的 A, B, C, D 具有相同的长度 N，且 0 ≤ N ≤ 500 。所有整数的范围在 -2^28 到 2^28 - 1 之间，最终结果不会超过 2^31 - 1 。

例如:

输入: A = [ 1, 2] B = [-2,-1] C = [-1, 2] D = [ 0, 2] 输出: 2 解释: 两个元组如下:

1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
```go
func fourSumCount(nums1 []int, nums2 []int, nums3 []int, nums4 []int) int {
    m := make(map[int]int)
    count := 0
    for _, v1 := range nums1 {
        for _, v2 := range nums2 {
            m[v1+v2]++
        }
    }
    for _, v3 := range nums3 {
        for _, v4 := range nums4 {
            count += m[-v3-v4]
        }
    }
    return count
}
```
### 赎金信
给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串 ransom 能不能由第二个字符串 magazines 里面的字符构成。如果可以构成，返回 true ；否则返回 false。

(题目说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。杂志字符串中的每个字符只能在赎金信字符串中使用一次。)

**注意**：

你可以假设两个字符串均只含有小写字母。

canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true

```go
func canConstruct(ransomNote string, magazine string) bool {
    record := make([]int, 26)
    for _, v := range magazine {
        record[v-'a']++
    }
    for _, v := range ransomNote {
        record[v-'a']--
        if record[v-'a'] < 0 {
            return false
        }
    }
    return true
}
```
### 三数之和
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。

**注意**： 答案中不可以包含重复的三元组。

示例：

给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为： [ [-1, 0, 1], [-1, -1, 2] ]

解法：哈希、双指针（更高效）
```go
func threeSum(nums []int)[][]int{
	sort.Ints(nums)
	res:=[][]int{}
	
	for i:=0;i<len(nums)-2;i++{
		n1:=nums[i]
		if n1>0{
			break
		}
		if i>0&&n1==nums[i-1]{
			continue
		}
		l,r:=i+1,len(nums)-1
		for l<r{
			n2,n3:=nums[l],nums[r]
			if n1+n2+n3==0{
				res=append(res,[]int{n1,n2,n3})
				for l<r&&nums[l]==n2{
					l++
				}
				for l<r&&nums[r]==n3{
					r--
				}
			}else if n1+n2+n3<0{
				l++
			}else {
				r--
			}
		}
	}
	return res
}
```
### 四数之和
题意：给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。

**注意**：

答案中不可以包含重复的四元组。

示例： 给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。 满足要求的四元组集合为： [ [-1, 0, 0, 1], [-2, -1, 1, 2], [-2, 0, 0, 2] ]
```go
func fourSum(nums []int, target int) [][]int {
	if len(nums) < 4 {
		return nil
	}
	sort.Ints(nums)
	var res [][]int
	for i := 0; i < len(nums)-3; i++ {
		n1 := nums[i]
		// if n1 > target { // 不能这样写,因为可能是负数
		// 	break
		// }
		if i > 0 && n1 == nums[i-1] {
			continue
		}
		for j := i + 1; j < len(nums)-2; j++ {
			n2 := nums[j]
			if j > i+1 && n2 == nums[j-1] {
				continue
			}
			l := j + 1
			r := len(nums) - 1
			for l < r {
				n3 := nums[l]
				n4 := nums[r]
				sum := n1 + n2 + n3 + n4
				if sum < target {
					l++
				} else if sum > target {
					r--
				} else {
					res = append(res, []int{n1, n2, n3, n4})
					for l < r && n3 == nums[l+1] { // 去重
						l++
					}
					for l < r && n4 == nums[r-1] { // 去重
						r--
					}
					// 找到答案时,双指针同时靠近
					r--
					l++
				}
			}
		}
	}
	return res
}
```

## 字符串
> 库函数是工具，基础更重要。
### 反转字符串
#### I
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
#### II
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
### 替换空格
请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

示例 1： 输入：s = "We are happy."
输出："We%20are%20happy."



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
### 翻转字符串里的单词
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
### 左旋转字符串
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
### 实现strStr()
给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1: 输入: haystack = "hello", needle = "ll" 输出: 2

示例 2: 输入: haystack = "aaaaa", needle = "bba" 输出: -1

说明: 当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。 对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。
#### 什么是KMP
KMP算法是一种改进的字符串匹配算法，由D.E.Knuth，J.H.Morris和V.R.Pratt提出的，因此人们称它为克努特—莫里斯—普拉特操作（简称KMP算法）。KMP算法的核心是利用匹配失败后的信息，尽量减少模式串与主串的匹配次数以达到快速匹配的目的。具体实现就是通过一个next()函数实现，函数本身包含了模式串的局部匹配信息。KMP算法的时间复杂度O(m+n) [1]  。（来自百度百科）

KMP的经典思想就是:当出现字符串不匹配时，可以记录一部分之前已经匹配的文本内容，利用这些信息避免从头再去做匹配。

如何记录已经匹配的文本内容，是KMP的重点，也是next数组的任务。
#### 什么是前缀表与next数组
next数组就是一个前缀表。

前缀表是用来回退的，它记录了模式串与主串(文本串)不匹配的时候，模式串应该从哪里开始重新匹配。

什么是前缀表：记录下标i之前（包括i）的字符串中，有多大长度的相同前缀后缀。

**最长公共前后缀**：字符串aa的最长相等前后缀为1。 字符串aaa的最长相等前后缀为2。 等等.....

前缀表要求的就是最长相同前后缀的长度。它能告诉我们上次匹配的位置。
- **下标5之前这部分的字符串（也就是字符串aabaa）的最长相等的前缀和后缀字符串是子字符串aa ，因为找到了最长相等的前缀和后缀，匹配失败的位置是后缀子串的后面，那么我们找到与其相同的前缀的后面从新匹配就可以了。**

前缀表与next数组有什么关系：
- next数组即可以就是前缀表，也可以是前缀表统一减一（右移一位，初始位置为-1）。

#### 前缀表统一减一之后的next数组：
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
#### 前缀表不减一的next数组：
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
#### kmp总结
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
### 重复的子字符串 
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

## 双指针法
双指针法（快慢指针法）在数组和链表的操作中是非常常见的，很多考察数组、链表、字符串等操作的面试题，都使用双指针法。
### 接雨水
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6

```go
func trap(height []int) (ans int) {
    left, right := 0, len(height)-1
    leftMax, rightMax := 0, 0
    for left < right {
        leftMax = max(leftMax, height[left])
        rightMax = max(rightMax, height[right])
        if height[left] < height[right] {
            ans += leftMax - height[left]
            left++
        } else {
            ans += rightMax - height[right]
            right--
        }
    }
    return
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
也可以用动态规划。


### 移除元素

给你一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 $O(1)$ 额外空间并原地修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

示例 1: 给定 nums = [3,2,2,3], val = 3, 函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。 你不需要考虑数组中超出新长度后面的元素。

示例 2: 给定 nums = [0,1,2,2,3,0,4,2], val = 2, 函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

你不需要考虑数组中超出新长度后面的元素。
```go
func removeElement(nums []int, val int) int {
    length:=len(nums)
    res:=0
    for i:=0;i<length;i++{
        if nums[i]!=val {
            nums[res]=nums[i]
            res++
        }
    }
    return res
}
```
### 反转字符串
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
### 替换空格
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
### 翻转字符串里的单词
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
### 翻转链表
```go
//双指针
func reverseList(head *ListNode) *ListNode {
    var pre *ListNode
    cur := head
    for cur != nil {
        next := cur.Next
        cur.Next = pre
        pre = cur
        cur = next
    }
    return pre
}

//递归
func reverseList(head *ListNode) *ListNode {
    return help(nil, head)
}

func help(pre, head *ListNode)*ListNode{
    if head == nil {
        return pre
    }
    next := head.Next
    head.Next = pre
    return help(head, next)
}

```
### 删除链表的倒数第N个节点
```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    dummyHead := &ListNode{}
    dummyHead.Next = head
    cur := head
    prev := dummyHead
    i := 1
    for cur != nil {
        cur = cur.Next
        if i > n {
            prev = prev.Next
        }
        i++
    }
    prev.Next = prev.Next.Next
    return dummyHead.Next
}
```
### 链表相交
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 null 。
```go
func getIntersectionNode(headA, headB *ListNode) *ListNode {
    curA := headA
    curB := headB
    lenA, lenB := 0, 0
    // 求A，B的长度
    for curA != nil {
        curA = curA.Next
        lenA++
    }
    for curB != nil {
        curB = curB.Next
        lenB++
    }
    var step int
    var fast, slow *ListNode
    // 请求长度差，并且让更长的链表先走相差的长度
    if lenA > lenB {
        step = lenA - lenB
        fast, slow = headA, headB
    } else {
        step = lenB - lenA
        fast, slow = headB, headA
    }
    for i:=0; i < step; i++ {
        fast = fast.Next
    }
    // 遍历两个链表遇到相同则跳出遍历
    for fast != slow {
        fast = fast.Next
        slow = slow.Next
    }
    return fast
}
```
### 环形链表II
题意： 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
```go
func detectCycle(head *ListNode) *ListNode {
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            for slow != head {
                slow = slow.Next
                head = head.Next
            }
            return head
        }
    }
    return nil
}
```
### 三数之和
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。

**注意**： 答案中不可以包含重复的三元组。
```
示例：
给定数组 nums = [-1, 0, 1, 2, -1, -4]，
满足要求的三元组集合为： [ [-1, 0, 1], [-1, -1, 2] ]
```
```go
func threeSum(nums []int)[][]int{
	sort.Ints(nums)
	res:=[][]int{}
	
	for i:=0;i<len(nums)-2;i++{
		n1:=nums[i]
		if n1>0{
			break
		}
		if i>0&&n1==nums[i-1]{
			continue
		}
		l,r:=i+1,len(nums)-1
		for l<r{
			n2,n3:=nums[l],nums[r]
			if n1+n2+n3==0{
				res=append(res,[]int{n1,n2,n3})
				for l<r&&nums[l]==n2{
					l++
				}
				for l<r&&nums[r]==n3{
					r--
				}
			}else if n1+n2+n3<0{
				l++
			}else {
				r--
			}
		}
	}
	return res
}
```
### 四数之和
题意：给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。

**注意**：

答案中不可以包含重复的四元组。

示例： 给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。 满足要求的四元组集合为： [ [-1, 0, 0, 1], [-2, -1, 1, 2], [-2, 0, 0, 2] ]
```go
func fourSum(nums []int, target int) [][]int {
	if len(nums) < 4 {
		return nil
	}
	sort.Ints(nums)
	var res [][]int
	for i := 0; i < len(nums)-3; i++ {
		n1 := nums[i]
		// if n1 > target { // 不能这样写,因为可能是负数
		// 	break
		// }
		if i > 0 && n1 == nums[i-1] {
			continue
		}
		for j := i + 1; j < len(nums)-2; j++ {
			n2 := nums[j]
			if j > i+1 && n2 == nums[j-1] {
				continue
			}
			l := j + 1
			r := len(nums) - 1
			for l < r {
				n3 := nums[l]
				n4 := nums[r]
				sum := n1 + n2 + n3 + n4
				if sum < target {
					l++
				} else if sum > target {
					r--
				} else {
					res = append(res, []int{n1, n2, n3, n4})
					for l < r && n3 == nums[l+1] { // 去重
						l++
					}
					for l < r && n4 == nums[r-1] { // 去重
						r--
					}
					// 找到答案时,双指针同时靠近
					r--
					l++
				}
			}
		}
	}
	return res
}
```
### 双指针总结
[总结](https://programmercarl.com/%E5%8F%8C%E6%8C%87%E9%92%88%E6%80%BB%E7%BB%93.html)


## 栈和队列
需要知道栈和队列的底层实现，不同编程语言不同STL的实现原理都是不尽相同的。
### 理论基础(c++)
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
```c++
std::stack<int, std::vector<int> > third;  // 使用vector为底层容器的栈
```
对应的队列的情况是一样的。

队列中先进先出的数据结构，同样不允许有遍历行为，不提供迭代器, SGI STL中队列一样是以deque为缺省情况下的底部结构。

也可以指定list 为起底层实现，初始化queue的语句如下：
```c++
std::queue<int, std::list<int>> third; // 定义以list为底层容器的队列
```
所以STL 队列也不被归类为容器，而被归类为container adapter（ 容器适配器）。
### 用栈实现队列
使用栈实现队列的下列操作：

push(x) -- 将一个元素放入队列的尾部。
pop() -- 从队列首部移除元素。
peek() -- 返回队列首部的元素。
empty() -- 返回队列是否为空。

示例:
```c++
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
### 用队列实现栈
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
### 有效的括号
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
### 删除字符串中的所有相邻重复项
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
### 逆波兰表达式求值
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
### 滑动窗口最大值
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
### 前K个高频元素
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

## 二叉树
### 理论基础
一般主要会碰到满二叉树以及完全二叉树。

完全二叉树的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在**该层最左边**的若干位置。若最底层为第 h 层，则该层包含 1~ 2^h -1  个节点。

优先级队列其实是一个堆，堆就是一棵完全二叉树，同时保证父子节点的顺序关系。

二叉搜索树：
- 与前面两个树不同，该树有节点权值。
- 有序树，左节点 < 中节点 < 右节点

平衡二叉搜索树：又被称为AVL（Adelson-Velsky and Landis）树，且具有以下性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。

二叉树可以链式存储，也可以顺序存储。

深度优先遍历
- 前序遍历（递归法，迭代法）
- 中序遍历（递归法，迭代法）
- 后序遍历（递归法，迭代法）

广度优先遍历
- 层次遍历（迭代法）
```go
type TreeNode struct {
    Val int
    Left *TreeNode
    Right *TreeNode
}
```
### 递归遍历
递归的实现就是：每一次递归调用都会把函数的局部变量、参数值和返回地址等压入调用栈中，然后递归返回的时候，从栈顶弹出上一次递归的各项参数，所以这就是递归为什么可以返回上一层位置的原因。

写递归还是得有**方法论**。
- 确定递归函数的参数和返回值： 确定哪些参数是递归的过程中需要处理的，那么就在递归函数里加上这个参数， 并且还要明确每次递归的返回值是什么进而确定递归函数的返回类型。
- 确定终止条件： 写完了递归算法, 运行的时候，经常会遇到栈溢出的错误，就是没写终止条件或者终止条件写的不对，操作系统也是用一个栈的结构来保存每一层递归的信息，如果递归没有终止，操作系统的内存栈必然就会溢出。
- 确定单层递归的逻辑： 确定每一层递归需要处理的信息。在这里也就会重复调用自己来实现递归的过程。

前序遍历：
```go
func preorderTraversal(root *TreeNode) (res []int) {
    var traversal func(node *TreeNode)
    traversal = func(node *TreeNode) {
        if node == nil {
                return
        }
        res = append(res,node.Val)
        traversal(node.Left)
        traversal(node.Right)
    }
    traversal(root)
    return res
}

func pre(r *TreeNode)(res []int){
    
}
```
中序遍历：
```go
func inorderTraversal(root *TreeNode) (res []int) {
    var traversal func(node *TreeNode)
    traversal = func(node *TreeNode) {
        if node == nil {
            return
        }
        traversal(node.Left)
        res = append(res,node.Val)
        traversal(node.Right)
    }
    traversal(root)
    return res
}
```
后序遍历:
```go
func postorderTraversal(root *TreeNode) (res []int) {
    var traversal func(node *TreeNode)
    traversal = func(node *TreeNode) {
        if node == nil {
            return
        }
        traversal(node.Left)
        traversal(node.Right)
            res = append(res,node.Val)
    }
    traversal(root)
    return res
}
```
### 迭代遍历
迭代法前序遍历
```go
func preorderTraversal(root *TreeNode) []int {
    ans := []int{}

	if root == nil {
		return ans
	}

	st := list.New()
    st.PushBack(root)

    for st.Len() > 0 {
        node := st.Remove(st.Back()).(*TreeNode)

        ans = append(ans, node.Val)
        if node.Right != nil {
            st.PushBack(node.Right)
        }
        if node.Left != nil {
            st.PushBack(node.Left)
        }
    }
    return ans
}


func preorderTraver(root *TreeNode) []int {
    
}
```
迭代法后序遍历
```go
func postorderTraversal(root *TreeNode) []int {
    ans := []int{}

	if root == nil {
		return ans
	}

	st := list.New()
    st.PushBack(root)

    for st.Len() > 0 {
        node := st.Remove(st.Back()).(*TreeNode)

        ans = append(ans, node.Val)
        if node.Left != nil {
            st.PushBack(node.Left)
        }
        if node.Right != nil {
            st.PushBack(node.Right)
        }
    }
    reverse(ans)
    return ans
}

func reverse(a []int) {
    l, r := 0, len(a) - 1
    for l < r {
        a[l], a[r] = a[r], a[l]
        l, r = l+1, r-1
    }
}
```
迭代法中序遍历
```go
func inorderTraversal(root *TreeNode) []int {
    ans := []int{}
    if root == nil {
        return ans
    }

    st := list.New()
    cur := root

    for cur != nil || st.Len() > 0 {
        if cur != nil {
            st.PushBack(cur)
            cur = cur.Left
        } else {
            cur = st.Remove(st.Back()).(*TreeNode)
            ans = append(ans, cur.Val)
            cur = cur.Right
        }
    }

    return ans
}
```
### 统一迭代
迭代法实现的先中后序，其实风格也不是那么统一，除了先序和后序，有关联，中序完全就是另一个风格了，一会用栈遍历，一会又用指针来遍历。

使用迭代法实现先中后序遍历，很难写出统一的代码，不像是递归法，实现了其中的一种遍历方式，其他两种只要稍稍改一下节点顺序就可以了。

**要处理的节点放入栈之后，紧接着放入一个空指针作为标记。 这种方法也可以叫做标记法**

前序遍历统一迭代法
```go
 /**
 type Element struct {
    // 元素保管的值
    Value interface{}
    // 内含隐藏或非导出字段
}

func (l *List) Back() *Element 
前序遍历：中左右
压栈顺序：右左中
 **/
func preorderTraversal(root *TreeNode) []int {
	if root == nil {
		return nil
	}
	var stack = list.New()//栈
    res:=[]int{}//结果集
    stack.PushBack(root)
    var node *TreeNode
    for stack.Len()>0{
        e := stack.Back()
        stack.Remove(e)//弹出元素
        if e.Value==nil{// 如果为空，则表明是需要处理中间节点
            e=stack.Back()//弹出元素（即中间节点）
            stack.Remove(e)//删除中间节点
            node=e.Value.(*TreeNode)
            res=append(res,node.Val)//将中间节点加入到结果集中
            continue//继续弹出栈中下一个节点
        }
        node = e.Value.(*TreeNode)
        //压栈顺序：右左中
        if node.Right!=nil{
            stack.PushBack(node.Right)
        }
        if node.Left!=nil{
            stack.PushBack(node.Left)
        }
        stack.PushBack(node)//中间节点压栈后再压入nil作为中间节点的标志符
        stack.PushBack(nil)
    }
    return res

}
```
中序遍历统一迭代法
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
 //中序遍历：左中右
 //压栈顺序：右中左
func inorderTraversal(root *TreeNode) []int {
    if root==nil{
       return nil
    }
    stack:=list.New()//栈
    res:=[]int{}//结果集
    stack.PushBack(root)
    var node *TreeNode
    for stack.Len()>0{
        e := stack.Back()
        stack.Remove(e)
        if e.Value==nil{// 如果为空，则表明是需要处理中间节点
            e=stack.Back()//弹出元素（即中间节点）
            stack.Remove(e)//删除中间节点
            node=e.Value.(*TreeNode)
            res=append(res,node.Val)//将中间节点加入到结果集中
            continue//继续弹出栈中下一个节点
        }
        node = e.Value.(*TreeNode)
        //压栈顺序：右中左
        if node.Right!=nil{
            stack.PushBack(node.Right)
        }
        stack.PushBack(node)//中间节点压栈后再压入nil作为中间节点的标志符
        stack.PushBack(nil)
        if node.Left!=nil{
            stack.PushBack(node.Left)
        }
    }
    return res
}
```
后序遍历统一迭代法
```go
//后续遍历：左右中
//压栈顺序：中右左
func postorderTraversal(root *TreeNode) []int {
	if root == nil {
		return nil
	}
	var stack = list.New()//栈
    res:=[]int{}//结果集
    stack.PushBack(root)
    var node *TreeNode
    for stack.Len()>0{
        e := stack.Back()
        stack.Remove(e)
        if e.Value==nil{// 如果为空，则表明是需要处理中间节点
            e=stack.Back()//弹出元素（即中间节点）
            stack.Remove(e)//删除中间节点
            node=e.Value.(*TreeNode)
            res=append(res,node.Val)//将中间节点加入到结果集中
            continue//继续弹出栈中下一个节点
        }
        node = e.Value.(*TreeNode)
        //压栈顺序：中右左
        stack.PushBack(node)//中间节点压栈后再压入nil作为中间节点的标志符
        stack.PushBack(nil)
        if node.Right!=nil{
            stack.PushBack(node.Right)
        }
        if node.Left!=nil{
            stack.PushBack(node.Left)
        }
    }
    return res
}
```
### 层序遍历
### 102.二叉树的层序遍历
https://leetcode-cn.com/problems/binary-tree-level-order-traversal/

```go
/**
102. 二叉树的层序遍历
 */
func levelOrder(root *TreeNode) [][]int {
    res:=[][]int{}
    if root==nil{//防止为空
        return res
    }
    queue:=list.New()
    queue.PushBack(root)
    var tmpArr []int
    for queue.Len()>0 {
        length:=queue.Len()//保存当前层的长度，然后处理当前层（十分重要，防止添加下层元素影响判断层中元素的个数）
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)//出队列
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
            tmpArr=append(tmpArr,node.Val)//将值加入本层切片中
        }
        res=append(res,tmpArr)//放入结果集
        tmpArr=[]int{}//清空层的数据
    }
    return res
}
```
### 107.二叉树的层次遍历II
https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/

```go
/**
107. 二叉树的层序遍历 II
 */
func levelOrderBottom(root *TreeNode) [][]int {
    queue:=list.New()
    res:=[][]int{}
    if root==nil{
        return res
    }
    queue.PushBack(root)
    for queue.Len()>0{
        length:=queue.Len()
        tmp:=[]int{}
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
            tmp=append(tmp,node.Val)
        }
        res=append(res,tmp)
    }
    //反转结果集
    for i:=0;i<len(res)/2;i++{
        res[i],res[len(res)-i-1]=res[len(res)-i-1],res[i]
    }
    return res
}
```
### 199.二叉树的右视图
https://leetcode-cn.com/problems/binary-tree-right-side-view/

```go
func rightSideView(root *TreeNode) []int {
    queue:=list.New()
    res:=[][]int{}
    var finaRes []int
    if root==nil{
        return finaRes
    }
    queue.PushBack(root)
    for queue.Len()>0{
        length:=queue.Len()
        tmp:=[]int{}
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
            tmp=append(tmp,node.Val)
        }
        res=append(res,tmp)
    }
    //取每一层的最后一个元素
    for i:=0;i<len(res);i++{
        finaRes=append(finaRes,res[i][len(res[i])-1])
    }
    return finaRes
}
```
### 637.二叉树的层平均值
https://leetcode-cn.com/problems/average-of-levels-in-binary-tree/

```go

/**
637. 二叉树的层平均值
 */
func averageOfLevels(root *TreeNode) []float64 {
    res:=[][]int{}
    var finRes []float64
    if root==nil{//防止为空
        return finRes
    }
    queue:=list.New()
    queue.PushBack(root)
    var tmpArr []int
    for queue.Len()>0 {
        length:=queue.Len()//保存当前层的长度，然后处理当前层（十分重要，防止添加下层元素影响判断层中元素的个数）
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)//出队列
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
            tmpArr=append(tmpArr,node.Val)//将值加入本层切片中
        }
        res=append(res,tmpArr)//放入结果集
        tmpArr=[]int{}//清空层的数据
    }
    //计算每层的平均值
    length:=len(res)
    for i:=0;i<length;i++{
        var sum int
        for j:=0;j<len(res[i]);j++{
            sum+=res[i][j]
        }
        tmp:=float64(sum)/float64(len(res[i]))
        finRes=append(finRes,tmp)//将平均值放入结果集合
    }
    return finRes
}
```
### 429.N叉树的层序遍历
https://leetcode-cn.com/problems/n-ary-tree-level-order-traversal/
```go
func levelOrder(root *Node) [][]int {
    queue:=list.New()
    res:=[][]int{}//结果集
    if root==nil{
        return res
    }
    queue.PushBack(root)
    for queue.Len()>0{
        length:=queue.Len()//记录当前层的数量
        var tmp []int
        for T:=0;T<length;T++{//该层的每个元素：一添加到该层的结果集中；二找到该元素的下层元素加入到队列中，方便下次使用
            myNode:=queue.Remove(queue.Front()).(*Node)
            tmp=append(tmp,myNode.Val)
            for i:=0;i<len(myNode.Children);i++{
                queue.PushBack(myNode.Children[i])
            }
        }
        res=append(res,tmp)
    }
    return res
}
```
### 515.在每个树行中找最大值
https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/
```go

/**
515. 在每个树行中找最大值
 */
func largestValues(root *TreeNode) []int {
    res:=[][]int{}
    var finRes []int
    if root==nil{//防止为空
        return finRes
    }
    queue:=list.New()
    queue.PushBack(root)
    var tmpArr []int
    //层次遍历
    for queue.Len()>0 {
        length:=queue.Len()//保存当前层的长度，然后处理当前层（十分重要，防止添加下层元素影响判断层中元素的个数）
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)//出队列
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
            tmpArr=append(tmpArr,node.Val)//将值加入本层切片中
        }
        res=append(res,tmpArr)//放入结果集
        tmpArr=[]int{}//清空层的数据
    }
    //找到每层的最大值
    for i:=0;i<len(res);i++{
        finRes=append(finRes,max(res[i]...))
    }
    return finRes
}
func max(vals...int) int {
    max:=int(math.Inf(-1))//负无穷
    for _, val := range vals {
        if val > max {
            max = val
        }
    }
    return max
}
```
### 116.填充每个节点的下一个右侧节点指针
https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/

```go

/**
116. 填充每个节点的下一个右侧节点指针
117. 填充每个节点的下一个右侧节点指针 II
 */

func connect(root *Node) *Node {
   res:=[][]*Node{}
    if root==nil{//防止为空
        return root
    }
    queue:=list.New()
    queue.PushBack(root)
    var tmpArr []*Node
    for queue.Len()>0 {
        length:=queue.Len()//保存当前层的长度，然后处理当前层（十分重要，防止添加下层元素影响判断层中元素的个数）
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*Node)//出队列
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
            tmpArr=append(tmpArr,node)//将值加入本层切片中
        }
        res=append(res,tmpArr)//放入结果集
        tmpArr=[]*Node{}//清空层的数据
    }
    //遍历每层元素,指定next
    for i:=0;i<len(res);i++{
        for j:=0;j<len(res[i])-1;j++{
            res[i][j].Next=res[i][j+1]
        }
    }
    return root
}
```
### 117.填充每个节点的下一个右侧节点指针II
https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/
```go

/**
116. 填充每个节点的下一个右侧节点指针
117. 填充每个节点的下一个右侧节点指针 II
 */

func connect(root *Node) *Node {
   res:=[][]*Node{}
    if root==nil{//防止为空
        return root
    }
    queue:=list.New()
    queue.PushBack(root)
    var tmpArr []*Node
    for queue.Len()>0 {
        length:=queue.Len()//保存当前层的长度，然后处理当前层（十分重要，防止添加下层元素影响判断层中元素的个数）
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*Node)//出队列
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
            tmpArr=append(tmpArr,node)//将值加入本层切片中
        }
        res=append(res,tmpArr)//放入结果集
        tmpArr=[]*Node{}//清空层的数据
    }
    //遍历每层元素,指定next
    for i:=0;i<len(res);i++{
        for j:=0;j<len(res[i])-1;j++{
            res[i][j].Next=res[i][j+1]
        }
    }
    return root
}
```
### 104.二叉树的最大深度
https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/
```go

/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func maxDepth(root *TreeNode) int {
    ans:=0
    if root==nil{
        return 0
    }
    queue:=list.New()
    queue.PushBack(root)
    for queue.Len()>0{
        length:=queue.Len()
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
        }
        ans++//记录深度，其他的是层序遍历的板子
    }
    return ans
}
```
### 111.二叉树的最小深度
https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/
```
go：

/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func minDepth(root *TreeNode) int {
    ans:=0
    if root==nil{
        return 0
    }
    queue:=list.New()
    queue.PushBack(root)
    for queue.Len()>0{
        length:=queue.Len()
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)
            if node.Left==nil&&node.Right==nil{//当前节点没有左右节点，则代表此层是最小层
                return ans+1//返回当前层 ans代表是上一层
            }
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
        }
        ans++//记录层数
        

    }
    return ans+1
}
```
### 翻转二叉树
二叉树，当然是左右翻转。

递归版本的前序遍历
```go
func invertTree(root *TreeNode) *TreeNode {
    if root ==nil{
        return nil
    }
    temp:=root.Left
    root.Left=root.Right
    root.Right=temp
    
    invertTree(root.Left)
    invertTree(root.Right)

    return root
}
```
递归版本的后序遍历
```go
func invertTree(root *TreeNode) *TreeNode {
    if root==nil{
        return root
    }
    invertTree(root.Left)//遍历左节点
    invertTree(root.Right)//遍历右节点
    root.Left,root.Right=root.Right,root.Left//交换
    return root
}
```
迭代版本的前序遍历
```go
func invertTree(root *TreeNode) *TreeNode {
    stack:=[]*TreeNode{}
    node:=root
    for node!=nil||len(stack)>0{
        for node!=nil{
            node.Left,node.Right=node.Right,node.Left//交换
            stack=append(stack,node)
            node=node.Left
        }
        node=stack[len(stack)-1]
        stack=stack[:len(stack)-1]
        node=node.Right
    }
    return root
}
```
迭代版本的后序遍历
```go
func invertTree(root *TreeNode) *TreeNode {
    stack:=[]*TreeNode{}
    node:=root
    var prev *TreeNode
    for node!=nil||len(stack)>0{
        for node!=nil{
            stack=append(stack,node)
            node=node.Left
        }
        node=stack[len(stack)-1]
        stack=stack[:len(stack)-1]
        if node.Right==nil||node.Right==prev{
            node.Left,node.Right=node.Right,node.Left//交换
            prev=node
            node=nil
        }else {
            stack=append(stack,node)
            node=node.Right
        }
    }
    return root
}
```
层序遍历
```go
func invertTree(root *TreeNode) *TreeNode {
    if root==nil{
        return root
    }
    queue:=list.New()
    node:=root
    queue.PushBack(node)
    for queue.Len()>0{
        length:=queue.Len()
        for i:=0;i<length;i++{
            e:=queue.Remove(queue.Front()).(*TreeNode)
            e.Left,e.Right=e.Right,e.Left//交换
            if e.Left!=nil{
                queue.PushBack(e.Left)
            }
            if e.Right!=nil{
                queue.PushBack(e.Right)
            }
        }
    }
    return root
}
```
### 对称二叉树
检查二叉树是否镜像对称。

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
// 递归
func defs(left *TreeNode, right *TreeNode) bool {
    if left == nil && right == nil {
        return true;
    };
    if left == nil || right == nil {
        return false;
    };
    if left.Val != right.Val {
        return false;
    }
    return defs(left.Left, right.Right) && defs(right.Left, left.Right);
}
func isSymmetric(root *TreeNode) bool {
    return defs(root.Left, root.Right);
}

// 迭代
func isSymmetric(root *TreeNode) bool {
    var queue []*TreeNode;
    if root != nil {
        queue = append(queue, root.Left, root.Right);
    }
    for len(queue) > 0 {
        left := queue[0];
        right := queue[1];
        queue = queue[2:];
        if left == nil && right == nil {
            continue;
        }
        if left == nil || right == nil || left.Val != right.Val {
            return false;
        };
        queue = append(queue, left.Left, right.Right, right.Left, left.Right);
    }
    return true;
}
```
### 最大深度
```go
/**
 * definition for a binary tree node.
 * type treenode struct {
 *     val int
 *     left *treenode
 *     right *treenode
 * }
 */
func max (a, b int) int {
    if a > b {
        return a;
    }
    return b;
}
// 递归
func maxdepth(root *treenode) int {
    if root == nil {
        return 0;
    }
    return max(maxdepth(root.left), maxdepth(root.right)) + 1;
}

// 遍历
func maxdepth(root *treenode) int {
    levl := 0;
    queue := make([]*treenode, 0);
    if root != nil {
        queue = append(queue, root);
    }
    for l := len(queue); l > 0; {
        for ;l > 0;l-- {
            node := queue[0];
            if node.left != nil {
                queue = append(queue, node.left);
            }
            if node.right != nil {
                queue = append(queue, node.right);
            }
            queue = queue[1:];
        }
        levl++;
        l = len(queue);
    }
    return levl;
}
```
### 最小深度
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func min(a, b int) int {
    if a < b {
        return a;
    }
    return b;
}
// 递归
func minDepth(root *TreeNode) int {
    if root == nil {
        return 0;
    }
    if root.Left == nil && root.Right != nil {
        return 1 + minDepth(root.Right);
    }
    if root.Right == nil && root.Left != nil {
        return 1 + minDepth(root.Left);
    }
    return min(minDepth(root.Left), minDepth(root.Right)) + 1;
}

// 迭代

func minDepth(root *TreeNode) int {
    dep := 0;
    queue := make([]*TreeNode, 0);
    if root != nil {
        queue = append(queue, root);
    }
    for l := len(queue); l > 0; {
        dep++;
        for ; l > 0; l-- {
            node := queue[0];
            if node.Left == nil && node.Right == nil {
                return dep;
            }
            if node.Left != nil {
                queue = append(queue, node.Left);
            }
            if node.Right != nil {
                queue = append(queue, node.Right);
            }
            queue = queue[1:];
        }
        l = len(queue);
    } 
    return dep;
}
```
### 完全二叉树的节点个数
递归版本
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
//本题直接就是求有多少个节点，无脑存进数组算长度就行了。
func countNodes(root *TreeNode) int {
    if root == nil {
        return 0
    }
    res := 1
    if root.Right != nil {
        res += countNodes(root.Right)
    }
    if root.Left != nil {
        res += countNodes(root.Left)
    }
    return res
}
```
利用完全二叉树特性的递归解法
```go
func countNodes(root *TreeNode) int {
    if root == nil {
        return 0
    }
    leftH, rightH := 0, 0
    leftNode := root.Left
    rightNode := root.Right
    for leftNode != nil {
        leftNode = leftNode.Left
        leftH++
    }
    for rightNode != nil {
        rightNode = rightNode.Right
        rightH++
    }
    if leftH == rightH {
        return (2 << leftH) - 1
    }
    return countNodes(root.Left) + countNodes(root.Right) + 1
}
```
### 平衡二叉树
给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过1。
```go
func isBalanced(root *TreeNode) bool {
    if root==nil{
        return true
    }
    if !isBalanced(root.Left) || !isBalanced(root.Right){
        return false
    }
    LeftH:=maxdepth(root.Left)+1
    RightH:=maxdepth(root.Right)+1
    if abs(LeftH-RightH)>1{
        return false
    }
    return true
}
func maxdepth(root *TreeNode)int{
    if root==nil{
        return 0
    }
    return max(maxdepth(root.Left),maxdepth(root.Right))+1
}
func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
func abs(a int)int{
    if a<0{
        return -a
    }
    return a 
}
```
### 二叉树的所有路径

递归法：
```go
func binaryTreePaths(root *TreeNode) []string {
    res := make([]string, 0)
    var travel func(node *TreeNode, s string)
    travel = func(node *TreeNode, s string) {
        if node.Left == nil && node.Right == nil {
            v := s + strconv.Itoa(node.Val)
            res = append(res, v)
            return
        }
        s = s + strconv.Itoa(node.Val) + "->"
        if node.Left != nil {
            travel(node.Left, s)
        }
        if node.Right != nil {
            travel(node.Right, s)
        }
    }
    travel(root, "")
    return res
}
```
迭代法：
```go
func binaryTreePaths(root *TreeNode) []string {
	stack := []*TreeNode{}
	paths := make([]string, 0)
	res := make([]string, 0)
	if root != nil {
		stack = append(stack, root)
		paths = append(paths, "")
	}
	for len(stack) > 0 {
		l := len(stack)
		node := stack[l-1]
		path := paths[l-1]
		stack = stack[:l-1]
		paths = paths[:l-1]
		if node.Left == nil && node.Right == nil {
			res = append(res, path+strconv.Itoa(node.Val))
			continue
		}
		if node.Right != nil {
			stack = append(stack, node.Right)
			paths = append(paths, path+strconv.Itoa(node.Val)+"->")
		}
		if node.Left != nil {
			stack = append(stack, node.Left)
			paths = append(paths, path+strconv.Itoa(node.Val)+"->")
		}
	}
	return res
}
```
### 二叉树的递归+回溯
100.相同的树
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isSameTree(p *TreeNode, q *TreeNode) bool {
   	switch {
	case p == nil && q == nil:
		return true
	case p == nil || q == nil:
		fallthrough
	case p.Val != q.Val:
		return false
	}
	return isSameTree(p.Left, q.Left) && isSameTree(p.Right, q.Right)
}
```
257.二叉的所有路径

递归法
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func binaryTreePaths(root *TreeNode) []string {
    var result []string
    traversal(root,&result,"")
    return result
}
func traversal(root *TreeNode,result *[]string,pathStr string){
    //判断是否为第一个元素
    if len(pathStr)!=0{
        pathStr=pathStr+"->"+strconv.Itoa(root.Val)
    }else{
        pathStr=strconv.Itoa(root.Val)
    }
    //判断是否为叶子节点
    if root.Left==nil&&root.Right==nil{
        *result=append(*result,pathStr)
        return 
    }
    //左右
    if root.Left!=nil{
        traversal(root.Left,result,pathStr)
    }
    if root.Right!=nil{
        traversal(root.Right,result,pathStr)
    }
}
```
回溯法
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func binaryTreePaths(root *TreeNode) []string {
    var result []string
    var path   []int
    traversal(root,&result,&path)
    return result
}
func traversal(root *TreeNode,result *[]string,path *[]int){
    *path=append(*path,root.Val)
    //判断是否为叶子节点
    if root.Left==nil&&root.Right==nil{
        pathStr:=strconv.Itoa((*path)[0])
        for i:=1;i<len(*path);i++{
            pathStr=pathStr+"->"+strconv.Itoa((*path)[i])
        }
        *result=append(*result,pathStr)
        return 
    }
    //左右
    if root.Left!=nil{
        traversal(root.Left,result,path)
        *path=(*path)[:len(*path)-1]//回溯到上一个节点（因为traversal会加下一个节点值到path中）
    }
    if root.Right!=nil{
        traversal(root.Right,result,path)
        *path=(*path)[:len(*path)-1]//回溯
    }
}
```
### 左叶子之和
递归法
```go
func sumOfLeftLeaves(root *TreeNode) int {
    var  res int
    findLeft(root,&res)
    return res
}
func findLeft(root *TreeNode,res *int){
    //左节点
    if root.Left!=nil&&root.Left.Left==nil&&root.Left.Right==nil{
        *res=*res+root.Left.Val
    }
    if root.Left!=nil{
        findLeft(root.Left,res)
    }
    if root.Right!=nil{
        findLeft(root.Right,res)
    }
}
```
迭代法
```go
func sumOfLeftLeaves(root *TreeNode) int {
    var  res int
    queue:=list.New()
    queue.PushBack(root)
    for queue.Len()>0{
        length:=queue.Len()
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)
            if node.Left!=nil&&node.Left.Left==nil&&node.Left.Right==nil{
                res=res+node.Left.Val
            }
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
        }
    }
    return res
}
```
### 找树左下角的值
给定一个二叉树，在树的最后一行找到最左边的值。

递归法：
```go
 var maxDeep int // 全局变量 深度
 var  value  int //全局变量 最终值
func findBottomLeftValue(root *TreeNode) int {
     if root.Left==nil&&root.Right==nil{//需要提前判断一下（不要这个if的话提交结果会出错，但执行代码不会。防止这种情况出现，故先判断是否只有一个节点）
         return root.Val
     }
    findLeftValue (root,maxDeep)
    return value
}
func findLeftValue (root *TreeNode,deep int){
     //最左边的值在左边
     if root.Left==nil&&root.Right==nil{
         if deep>maxDeep{
             value=root.Val
             maxDeep=deep
         }
     }
    //递归
    if root.Left!=nil{
        deep++
        findLeftValue(root.Left,deep)
        deep--//回溯
    }
    if root.Right!=nil{
        deep++
        findLeftValue(root.Right,deep)
        deep--//回溯
    }
}
```
迭代法：
```go
func findBottomLeftValue(root *TreeNode) int {
    queue:=list.New()
    var gradation int
    queue.PushBack(root)
    for queue.Len()>0{
        length:=queue.Len()
        for i:=0;i<length;i++{
            node:=queue.Remove(queue.Front()).(*TreeNode)
            if i==0{gradation=node.Val}
            if node.Left!=nil{
                queue.PushBack(node.Left)
            }
            if node.Right!=nil{
                queue.PushBack(node.Right)
            }
        }
    }
    return gradation
}
```
### 路径总和
给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和

路径总和
```go
//递归法
/**
 * definition for a binary tree node.
 * type treenode struct {
 *     val int
 *     left *treenode
 *     right *treenode
 * }
 */
func haspathsum(root *treenode, targetsum int) bool {
    var flage bool //找没找到的标志
    if root==nil{
        return flage
    }
    pathsum(root,0,targetsum,&flage)
    return flage
}
func pathsum(root *treenode, sum int,targetsum int,flage *bool){
    sum+=root.val
    if root.left==nil&&root.right==nil&&sum==targetsum{
        *flage=true
        return
    }
    if root.left!=nil&&!(*flage){//左节点不为空且还没找到
        pathsum(root.left,sum,targetsum,flage)
    } 
    if root.right!=nil&&!(*flage){//右节点不为空且没找到
        pathsum(root.right,sum,targetsum,flage)
    }
}
```
113 递归法
```go
/**
 * definition for a binary tree node.
 * type treenode struct {
 *     val int
 *     left *treenode
 *     right *treenode
 * }
 */
func pathsum(root *treenode, targetsum int) [][]int {
    var result [][]int//最终结果
    if root==nil{
        return result
    }
    var sumnodes []int//经过路径的节点集合
    haspathsum(root,&sumnodes,targetsum,&result)
    return result
}
func haspathsum(root *treenode,sumnodes *[]int,targetsum int,result *[][]int){
    *sumnodes=append(*sumnodes,root.val)
    if root.left==nil&&root.right==nil{//叶子节点
        fmt.println(*sumnodes)
        var sum int
        var number int
        for k,v:=range *sumnodes{//求该路径节点的和
            sum+=v
            number=k
        }
        tempnodes:=make([]int,number+1)//新的nodes接受指针里的值，防止最终指针里的值发生变动，导致最后的结果都是最后一个sumnodes的值
        for k,v:=range *sumnodes{
            tempnodes[k]=v
        }
        if sum==targetsum{
            *result=append(*result,tempnodes)
        }
    }
    if root.left!=nil{
        haspathsum(root.left,sumnodes,targetsum,result)
        *sumnodes=(*sumnodes)[:len(*sumnodes)-1]//回溯
    }
    if root.right!=nil{
        haspathsum(root.right,sumnodes,targetsum,result)
        *sumnodes=(*sumnodes)[:len(*sumnodes)-1]//回溯
    }
}
```
### 从中序、后序遍历序列构造二叉树
106 从中序与后序遍历序列构造二叉树
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func buildTree(inorder []int, postorder []int) *TreeNode {
    if len(inorder)<1||len(postorder)<1{return nil}
    //先找到根节点（后续遍历的最后一个就是根节点）
    nodeValue:=postorder[len(postorder)-1]
    //从中序遍历中找到一分为二的点，左边为左子树，右边为右子树
    left:=findRootIndex(inorder,nodeValue)
    //构造root
    root:=&TreeNode{Val: nodeValue,
                Left: buildTree(inorder[:left],postorder[:left]),//将后续遍历一分为二，左边为左子树，右边为右子树
                Right: buildTree(inorder[left+1:],postorder[left:len(postorder)-1])}
    return root
}
func findRootIndex(inorder []int,target int) (index int){
    for i:=0;i<len(inorder);i++{
        if target==inorder[i]{
            return i
        }
    }
    return -1
}
```
105 从前序与中序遍历序列构造二叉树
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func buildTree(preorder []int, inorder []int) *TreeNode {
    if len(preorder)<1||len(inorder)<1{return nil}
    left:=findRootIndex(preorder[0],inorder)
    root:=&TreeNode{
        Val: preorder[0],
        Left: buildTree(preorder[1:left+1],inorder[:left]),
        Right: buildTree(preorder[left+1:],inorder[left+1:])}
    return root
}
func findRootIndex(target int,inorder []int) int{
    for i:=0;i<len(inorder);i++{
        if target==inorder[i]{
            return i
        }
    }
    return -1
}
```
### 最大二叉树
给定一个不含重复元素的整数数组。一个以此数组构建的最大二叉树定义如下：
- 二叉树的根是数组中的最大元素。
- 左子树是通过数组中最大值左边部分构造出的最大二叉树。
- 右子树是通过数组中最大值右边部分构造出的最大二叉树。
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func constructMaximumBinaryTree(nums []int) *TreeNode {
    if len(nums)<1{return nil}
    //首选找到最大值
    index:=findMax(nums)
    //其次构造二叉树
    root:=&TreeNode{
        Val: nums[index],
        Left:constructMaximumBinaryTree(nums[:index]),//左半边
        Right:constructMaximumBinaryTree(nums[index+1:]),//右半边
        }
    return root
}
func findMax(nums []int) (index int){
    for i:=0;i<len(nums);i++{
        if nums[i]>nums[index]{
            index=i
        }
    }
    return 
}
```
### 合并二叉树
```go

/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
 //前序遍历（递归遍历，跟105 106差不多的思路）
func mergeTrees(t1 *TreeNode, t2 *TreeNode) *TreeNode {
    var value int 
    var nullNode *TreeNode//空node，便于遍历
    nullNode=&TreeNode{
        Val:0,
        Left:nil,
        Right:nil}
    switch {
        case t1==nil&&t2==nil: return nil//终止条件
        default : //如果其中一个节点为空，则将该节点置为nullNode，方便下次遍历
        if t1==nil{
            value=t2.Val
            t1=nullNode
        }else if t2==nil{
            value=t1.Val
            t2=nullNode
        }else {
            value=t1.Val+t2.Val
        }
    }
    root:=&TreeNode{//构造新的二叉树
        Val: value,
        Left: mergeTrees(t1.Left,t2.Left),
        Right: mergeTrees(t1.Right,t2.Right)}
    return root
}

// 前序遍历简洁版
func mergeTrees(root1 *TreeNode, root2 *TreeNode) *TreeNode {
    if root1 == nil {
        return root2
    }
    if root2 == nil {
        return root1
    }
    root1.Val += root2.Val
    root1.Left = mergeTrees(root1.Left, root2.Left)
    root1.Right = mergeTrees(root1.Right, root2.Right)
    return root1
}

// 迭代版本
func mergeTrees(root1 *TreeNode, root2 *TreeNode) *TreeNode {
    queue := make([]*TreeNode,0)
    if root1 == nil{
        return root2
    }
    if root2 == nil{
        return root1
    }
    queue = append(queue,root1)
    queue = append(queue,root2)

    for size:=len(queue);size>0;size=len(queue){
        node1 := queue[0]
        queue = queue[1:]
        node2 := queue[0]
        queue = queue[1:]
        node1.Val += node2.Val
        // 左子树都不为空
        if node1.Left != nil && node2.Left != nil{
            queue = append(queue,node1.Left)
            queue = append(queue,node2.Left)
        }
        // 右子树都不为空
        if node1.Right !=nil && node2.Right !=nil{
            queue = append(queue,node1.Right)
            queue = append(queue,node2.Right)
        }
        // 树 1 的左子树为 nil，直接接上树 2 的左子树
        if node1.Left == nil{
            node1.Left = node2.Left
        }
        // 树 1 的右子树为 nil，直接接上树 2 的右子树
        if node1.Right == nil{
            node1.Right = node2.Right
        }
    }
    return root1
}
```
### 二叉搜索树
### 搜索
给定二叉搜索树（BST）的根节点和一个值。 你需要在BST中找到节点值等于给定值的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 NULL。

递归法：
```go
 //递归法
func searchBST(root *TreeNode, val int) *TreeNode {
    if root==nil||root.Val==val{
        return root
    }
    if root.Val>val{
        return searchBST(root.Left,val)
    }
    return searchBST(root.Right,val)
}
```
迭代法：
```go
 //迭代法
func searchBST(root *TreeNode, val int) *TreeNode {
    for root!=nil{
        if root.Val>val{
            root=root.Left
        }else if root.Val<val{
            root=root.Right
        }else{
            break
        }
    }
    return root
}
```
### 验证
给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：
- 节点的左子树只包含小于当前节点的数。
- 节点的右子树只包含大于当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

```go

import "math"

func isValidBST(root *TreeNode) bool {
	// 二叉搜索树也可以是空树
    if root == nil {
        return true
    }
    // 由题目中的数据限制可以得出min和max
    return check(root,math.MinInt64,math.MaxInt64)
}

func check(node *TreeNode,min,max int64) bool {
    if node == nil {
        return true
    }

    if min >= int64(node.Val) || max <= int64(node.Val) {
        return false
    }
    // 分别对左子树和右子树递归判断，如果左子树和右子树都符合则返回true
    return check(node.Right,int64(node.Val),max) && check(node.Left,min,int64(node.Val))
}


// 中序遍历解法
func isValidBST(root *TreeNode) bool {
    // 保存上一个指针
    var prev *TreeNode
    var travel func(node *TreeNode) bool
    travel = func(node *TreeNode) bool {
        if node == nil {
            return true
        }
        leftRes := travel(node.Left)
        // 当前值小于等于前一个节点的值，返回false
        if prev != nil && node.Val <= prev.Val {
            return false
        }
        prev = node
        rightRes := travel(node.Right)
        return leftRes && rightRes
    }
    return travel(root)
}
```
### 最小绝对差
给你一棵所有节点为非负值的二叉搜索树，请你计算树中任意两节点的差的绝对值的最小值。

中序遍历，然后计算最小差值
```go
func getMinimumDifference(root *TreeNode) int {
    var res []int
    findMIn(root,&res)
    min:=1000000//一个比较大的值
    for i:=1;i<len(res);i++{
        tempValue:=res[i]-res[i-1]
        if tempValue<min{
            min=tempValue
        }
    }
    return min
}
//中序遍历
func findMIn(root *TreeNode,res *[]int){
    if root==nil{return}
    findMIn(root.Left,res)
    *res=append(*res,root.Val)
    findMIn(root.Right,res)
}
```
```go
// 中序遍历的同时计算最小值
func getMinimumDifference(root *TreeNode) int {
    // 保留前一个节点的指针
    var prev *TreeNode
    // 定义一个比较大的值
    min := math.MaxInt64
    var travel func(node *TreeNode)
    travel = func(node *TreeNode) {
        if node == nil {
            return 
        }
        travel(node.Left)
        if prev != nil && node.Val - prev.Val < min {
            min = node.Val - prev.Val
        }
        prev = node
        travel(node.Right)
    }
    travel(root)
    return min
}
```
### 众数
给定一个有相同值的二叉搜索树（BST），找出 BST 中的所有众数（出现频率最高的元素）。

暴力法
```go
func findMode(root *TreeNode) []int {
    var history map[int]int
    var maxValue int
    var maxIndex int
    var result []int
    history=make(map[int]int)
    traversal(root,history)
    for k,value:=range history{
        if value>maxValue{
            maxValue=value
            maxIndex=k
        }
    }
    for k,value:=range history{
        if value==history[maxIndex]{
            result=append(result,k)
        }
    }
    return result
}
func traversal(root *TreeNode,history map[int]int){
    if root.Left!=nil{
        traversal(root.Left,history)
    }
    if value,ok:=history[root.Val];ok{
        history[root.Val]=value+1
    }else{
        history[root.Val]=1
    }
    if root.Right!=nil{
        traversal(root.Right,history)
    } 
}
```
计数法，不使用额外空间，利用二叉树性质，中序遍历
```go
func findMode(root *TreeNode) []int {
    res := make([]int, 0)
    count := 1
    max := 1
    var prev *TreeNode
    var travel func(node *TreeNode) 
    travel = func(node *TreeNode) {
        if node == nil {
            return
        }
        travel(node.Left)
        if prev != nil && prev.Val == node.Val {
            count++
        } else {
            count = 1
        }
        if count >= max {
            if count > max && len(res) > 0 {
                res = []int{node.Val}
            } else {
                res = append(res, node.Val)
            }
            max = count
        }
        prev = node
        travel(node.Right)
    }
    travel(root)
    return res
}
```
### 二叉树最近公共祖先
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先（可以是自己）
```go

func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    // check
    if root == nil {
        return root
    }
    // 相等 直接返回root节点即可
    if root == p || root == q {
        return root
    }
    // Divide
    left := lowestCommonAncestor(root.Left, p, q)
    right := lowestCommonAncestor(root.Right, p, q)

    // Conquer
    // 左右两边都不为空，则根节点为祖先
    if left != nil && right != nil {
        return root
    }
    if left != nil {
        return left
    }
    if right != nil {
        return right
    }
    return nil
}
```
### 二叉搜索树的最近公共祖先
```go
递归法：

//利用BSL的性质（前序遍历有序）
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    if root==nil{return nil}
    if root.Val>p.Val&&root.Val>q.Val{//当前节点的值大于给定的值，则说明满足条件的在左边
        return lowestCommonAncestor(root.Left,p,q)
    }else if root.Val<p.Val&&root.Val<q.Val{//当前节点的值小于各点的值，则说明满足条件的在右边
        return lowestCommonAncestor(root.Right,p,q)
    }else {return root}//当前节点的值在给定值的中间（或者等于），即为最深的祖先
}
```
### 插入操作
给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据保证，新值和原始二叉搜索树中的任意节点值都不同。

```go
递归法

func insertIntoBST(root *TreeNode, val int) *TreeNode {
    if root == nil {
        root = &TreeNode{Val: val}
        return root
    }
    if root.Val > val {
        root.Left = insertIntoBST(root.Left, val)
    } else {
        root.Right = insertIntoBST(root.Right, val)
    }
    return root
}
```
迭代法
```go
func insertIntoBST(root *TreeNode, val int) *TreeNode {
    if root == nil {
        return &TreeNode{Val:val}
    }
    node := root
    var pnode *TreeNode
    for node != nil {
        if val > node.Val {
            pnode = node
            node = node.Right
        } else {
            pnode = node
            node = node.Left
        }
    }
    if val > pnode.Val {
        pnode.Right = &TreeNode{Val: val}
    } else {
        pnode.Left = &TreeNode{Val: val}
    }
    return root
}
```
### 删除节点
搜索树的节点删除要比节点增加复杂的多。

```go

// 递归版本
func deleteNode(root *TreeNode, key int) *TreeNode {
    if root==nil{
        return nil
    }
    if key<root.Val{
        root.Left=deleteNode(root.Left,key)
        return root
    }
    if key>root.Val{
        root.Right=deleteNode(root.Right,key)
        return root
    }
    if root.Right==nil{
        return root.Left
    }
    if root.Left==nil{
        return root.Right
    }
    minnode:=root.Right
    for minnode.Left!=nil{
        minnode=minnode.Left
    }
    root.Val=minnode.Val
    root.Right=deleteNode1(root.Right)
    return root
}

func deleteNode1(root *TreeNode)*TreeNode{
    if root.Left==nil{
        pRight:=root.Right
        root.Right=nil
        return pRight
    }
    root.Left=deleteNode1(root.Left)
    return root
}
// 迭代版本
func deleteOneNode(target *TreeNode) *TreeNode {
	if target == nil {
		return target
	}
	if target.Right == nil {
		return target.Left
	}
	cur := target.Right
	for cur.Left != nil {
		cur = cur.Left
	}
	cur.Left = target.Left
	return target.Right
}
func deleteNode(root *TreeNode, key int) *TreeNode {
	// 特殊情况处理
	if root == nil {
		return root
	}
	cur := root
	var pre *TreeNode
	for cur != nil {
		if cur.Val == key {
			break
		}
		pre = cur
		if cur.Val > key {
			cur = cur.Left
		} else {
			cur = cur.Right
		}
	}
	if pre == nil {
		return deleteOneNode(cur)
	}
	// pre 要知道是删除左孩子还有右孩子
	if pre.Left != nil && pre.Left.Val == key {
		pre.Left = deleteOneNode(cur)
	}
	if pre.Right != nil && pre.Right.Val == key {
		pre.Right = deleteOneNode(cur)
	}
	return root
}
```
### 修剪
给定一个二叉搜索树，同时给定最小边界L 和最大边界 R。通过修剪二叉搜索树，使得所有节点的值在[L, R]中 (R>=L) 。你可能需要改变树的根节点，所以结果应当返回修剪好的二叉搜索树的新的根节点。
```go
// 递归
func trimBST(root *TreeNode, low int, high int) *TreeNode {
    if root==nil{
        return nil
    }
    if root.Val<low{//如果该节点值小于最小值，则该节点更换为该节点的右节点值，继续遍历
        right:=trimBST(root.Right,low,high)
        return right
    }
    if root.Val>high{//如果该节点的值大于最大值，则该节点更换为该节点的左节点值，继续遍历
        left:=trimBST(root.Left,low,high)
        return left
    }
    root.Left=trimBST(root.Left,low,high)
    root.Right=trimBST(root.Right,low,high)
    return root
}

// 迭代
func trimBST(root *TreeNode, low int, high int) *TreeNode {
    if root == nil {
        return nil
    }
    // 处理 root，让 root 移动到[low, high] 范围内，**注意**是左闭右闭
    for root != nil && (root.Val<low||root.Val>high){
        if root.Val < low{
            root = root.Right
        }else{
            root = root.Left
        }
    }
    cur := root
    // 此时 root 已经在[low, high] 范围内，处理左孩子元素小于 low 的情况（左节点是一定小于 root.Val，因此天然小于 high）
    for cur != nil{
        for cur.Left!=nil && cur.Left.Val < low{
            cur.Left = cur.Left.Right
        }
        cur = cur.Left
    }
    cur = root
    // 此时 root 已经在[low, high] 范围内，处理右孩子大于 high 的情况
    for cur != nil{
        for cur.Right!=nil && cur.Right.Val > high{
            cur.Right = cur.Right.Left
        }
        cur = cur.Right
    }
    return root
}
```
### 将有序数组转化为二叉搜索树
将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。
```go
递归（隐含回溯）

func sortedArrayToBST(nums []int) *TreeNode {
    if len(nums)==0{return nil}//终止条件，最后数组为空则可以返回
    root:=&TreeNode{nums[len(nums)/2],nil,nil}//按照BSL的特点，从中间构造节点
    root.Left=sortedArrayToBST(nums[:len(nums)/2])//数组的左边为左子树
    root.Right=sortedArrayToBST(nums[len(nums)/2+1:])//数字的右边为右子树
    return root
}
```
### 把二叉搜索树转化为累加树
给出二叉 搜索 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 node 的新值等于原树中大于或等于 node.val 的值之和。

弄一个sum暂存其和值
```go
 //右中左
func bstToGst(root *TreeNode) *TreeNode {
     var sum int
     RightMLeft(root,&sum)
     return root
}
func RightMLeft(root *TreeNode,sum *int) *TreeNode {
    if root==nil{return nil}//终止条件，遇到空节点就返回
    RightMLeft(root.Right,sum)//先遍历右边
    temp:=*sum//暂存总和值
    *sum+=root.Val//将总和值变更
    root.Val+=temp//更新节点值
    RightMLeft(root.Left,sum)//遍历左节点
    return root
}
```

## 回溯算法
### 理论基础
也叫回溯搜索算法。

回溯是递归的副产品，只要有递归就会有回溯

回溯的本质是穷举，穷举所有可能，然后选出我们想要的答案，并不算高效。加一些剪枝操作或许会高效一点。

一般用来解决除了暴力搜索无可奈何的情况。

回溯法，一般可以解决如下几种问题：
- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等等

**回溯法解决的问题都可以抽象为树形结构**
- 因为回溯法解决的都是在集合中递归查找子集，集合的大小就构成了树的宽度，递归的深度，都构成的树的深度。

**回溯模板**：
```
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```
### *组合问题及其优化
给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

**回溯法三部曲**：函数参数、终止条件和单层搜索

剪枝优化：
- 可以剪枝的地方就在递归中每一层的for循环所选择的起始位置。
- 如果for循环选择的起始位置之后的元素个数已经不足我们需要的元素个数了，那么就没有必要搜索了。

```go
var res [][]int 
func combine(n int, k int) [][]int {
   res=[][]int{}
   if n <= 0 || k <= 0 || k > n {
		return res
	}
    backtrack(n, k, 1, []int{})
	return res
}
func backtrack(n,k,start int,track []int){
    if len(track)==k{
        temp:=make([]int,k)
        copy(temp,track)
        res=append(res,temp)
    }
    if len(track)+n-start+1 < k {
			return
		}
    for i:=start;i<=n;i++{
        track=append(track,i)
        backtrack(n,k,i+1,track)
        track=track[:len(track)-1]
    }
}
```
剪枝：**go语言的剪枝优化会爆内存溢出，不知道是为啥……**
```go
var res [][]int 
func combine(n int, k int) [][]int {
   res=[][]int{}
   if n <= 0 || k <= 0 || k > n {
		return res
	}
    backtrack(n, k, 1, []int{})
	return res
}
func backtrack(n,k,start int,track []int){
    if len(track)==k{
        temp:=make([]int,k)
        copy(temp,track)
        res=append(res,temp)
    }
    if len(track)+n-start+1 < k {
			return
		}
    for i:=start;i<=(n-k+len(track)+1);i++{ 
        track=append(track,i)
        backtrack(n,k,i+1,track)
        track=track[:len(track)-1]
    }
}
```
### 组合总和III
找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。

说明：
- 所有数字都是正整数。
- 解集不能包含重复的组合。


回溯+减枝
```go
func combinationSum3(k int, n int) [][]int {
    var track []int// 遍历路径
    var result [][]int// 存放结果集
    backTree(n,k,1,&track,&result)
    return result
}
func backTree(n,k,startIndex int,track *[]int,result *[][]int){
    if len(*track)==k{
        var sum int
        tmp:=make([]int,k)
        for k,v:=range *track{
            sum+=v
            tmp[k]=v
        }
        if sum==n{
            *result=append(*result,tmp)
        }
        return
    }
    for i:=startIndex;i<=9-(k-len(*track))+1;i++{//减枝（k-len(*track)表示还剩多少个可填充的元素）
        *track=append(*track,i)//记录路径
        backTree(n,k,i+1,track,result)//递归
        *track=(*track)[:len(*track)-1]//回溯
    }
}
```
### 电话号码的字母组合
给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如电话按键。**注意** 1 不对应任何字母。


主要在于递归中传递下一个数字
```go
func letterCombinations(digits string) []string {
    lenth:=len(digits)
    if lenth==0 ||lenth>4{
       return nil
    }
    digitsMap:= [10]string{
         "", // 0
        "", // 1
        "abc", // 2
        "def", // 3
        "ghi", // 4
        "jkl", // 5
        "mno", // 6
        "pqrs", // 7
        "tuv", // 8
        "wxyz", // 9
    }
    res:=make([]string,0)
    recursion("",digits,0,digitsMap,&res)
     return res
}
func recursion(tempString ,digits string, Index int,digitsMap [10]string, res *[]string) {//index表示第几个数字
    if len(tempString)==len(digits){//终止条件，字符串长度等于digits的长度
        *res=append(*res,tempString)
        return
    }
    tmpK:=digits[Index]-'0' // 将index指向的数字转为int（确定下一个数字）
    letter:=digitsMap[tmpK]// 取数字对应的字符集
    for i:=0;i<len(letter);i++{
        tempString=tempString+string(letter[i])//拼接结果
        recursion(tempString,digits,Index+1,digitsMap,res)
        tempString=tempString[:len(tempString)-1]//回溯
    }
}
```
### 组合总和
给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的数字可以无限制重复被选取。

主要在于递归中传递下一个数字
```go
func combinationSum(candidates []int, target int) [][]int {
    var trcak []int
    var res [][]int
    backtracking(0,0,target,candidates,trcak,&res)
    return res
}
func backtracking(startIndex,sum,target int,candidates,trcak []int,res *[][]int){
    //终止条件
    if sum==target{
        tmp:=make([]int,len(trcak))
        copy(tmp,trcak)//拷贝
        *res=append(*res,tmp)//放入结果集
        return
    }
    if sum>target{return}
    //回溯
    for i:=startIndex;i<len(candidates);i++{
        //更新路径集合和sum
        trcak=append(trcak,candidates[i])
        sum+=candidates[i]
        //递归
        backtracking(i,sum,target,candidates,trcak,res)
        //回溯
        trcak=trcak[:len(trcak)-1]
        sum-=candidates[i]
    }

}
```
### 组合总和II
给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个数字在每个组合中只能使用一次。

主要在于如何在回溯中去重

使用used数组
```go
func combinationSum2(candidates []int, target int) [][]int {
    var trcak []int
    var res [][]int
    var history map[int]bool
    history=make(map[int]bool)
    sort.Ints(candidates)
    backtracking(0,0,target,candidates,trcak,&res,history)
    return res
}
func backtracking(startIndex,sum,target int,candidates,trcak []int,res *[][]int,history map[int]bool){
    //终止条件
    if sum==target{
        tmp:=make([]int,len(trcak))
        copy(tmp,trcak)//拷贝
        *res=append(*res,tmp)//放入结果集
        return
    }
    if sum>target{return}
    //回溯
    // used[i - 1] == true，说明同一树枝candidates[i - 1]使用过
    // used[i - 1] == false，说明同一树层candidates[i - 1]使用过
    for i:=startIndex;i<len(candidates);i++{
        if i>0&&candidates[i]==candidates[i-1]&&history[i-1]==false{
                continue
        }
        //更新路径集合和sum
        trcak=append(trcak,candidates[i])
        sum+=candidates[i]
        history[i]=true
        //递归
        backtracking(i+1,sum,target,candidates,trcak,res,history)
        //回溯
        trcak=trcak[:len(trcak)-1]
        sum-=candidates[i]
        history[i]=false
    }
}
```
不使用used数组
```go
func combinationSum2(candidates []int, target int) [][]int {
    var trcak []int
    var res [][]int
    sort.Ints(candidates)
    backtracking(0,0,target,candidates,trcak,&res)
    return res
}
func backtracking(startIndex,sum,target int,candidates,trcak []int,res *[][]int){
    //终止条件
    if sum==target{
        tmp:=make([]int,len(trcak))
        //拷贝
        copy(tmp,trcak)
        //放入结果集
        *res=append(*res,tmp)
        return
    }
    //回溯
    for i:=startIndex;i<len(candidates) && sum+candidates[i]<=target;i++{
        // 若当前树层有使用过相同的元素，则跳过
        if i>startIndex&&candidates[i]==candidates[i-1]{
                continue
        }
        //更新路径集合和sum
        trcak=append(trcak,candidates[i])
        sum+=candidates[i]
        backtracking(i+1,sum,target,candidates,trcak,res)
        //回溯
        trcak=trcak[:len(trcak)-1]
        sum-=candidates[i]
    }
}
```
### 分割回文串
给定一个字符串 s，将 s 分割成一些子串，使每个子串都是回文串。

返回 s 所有可能的分割方案。
```
示例: 输入: "aab" 输出: [ ["aa","b"], ["a","a","b"] ]
```
**注意**切片（go切片是披着值类型外衣的引用类型）
```go
func partition(s string) [][]string {
    var tmpString []string//切割字符串集合
    var res [][]string//结果集合
    backTracking(s,tmpString,0,&res)
    return res
}
func backTracking(s string,tmpString []string,startIndex int,res *[][]string){
    if startIndex==len(s){//到达字符串末尾了
        //进行一次切片拷贝，怕之后的操作影响tmpString切片内的值
        t := make([]string, len(tmpString))
		copy(t, tmpString)
        *res=append(*res,t)
    }
    for i:=startIndex;i<len(s);i++{
        //处理（首先通过startIndex和i判断切割的区间，进而判断该区间的字符串是否为回文，若为回文，则加入到tmpString，否则继续后移，找到回文区间）（这里为一层处理）
        if isPartition(s,startIndex,i){
            tmpString=append(tmpString,s[startIndex:i+1])
        }else{
            continue
        }
        //递归
        backTracking(s,tmpString,i+1,res)
        //回溯
        tmpString=tmpString[:len(tmpString)-1]
    }
}
//判断是否为回文
func isPartition(s string,startIndex,end int)bool{
    left:=startIndex
    right:=end
    for ;left<right;{
        if s[left]!=s[right]{
            return false
        }
        //移动左右指针
        left++
        right--
    }
    return true
}
```
### 复原IP地址
给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。

有效的 IP 地址 正好由四个整数（每个整数位于 0 到 255 之间组成，且不能含有前导 0），整数之间用 '.' 分隔。

例如："0.1.2.201" 和 "192.168.1.1" 是 有效的 IP 地址，但是 "0.011.255.245"、"192.168.1.312" 和 "192.168@1.1" 是 无效的 IP 地址。
```
示例 1：

输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]
示例 2：

输入：s = "0000"
输出：["0.0.0.0"]
```
回溯（对于前导 0的IP（特别**注意**s[startIndex]=='0'的判断，不应该写成s[startIndex]==0，因为s截取出来不是数字））
```go
func restoreIpAddresses(s string) []string {
	var res,path []string
	backTracking(s,path,0,&res)
	return res
}
func backTracking(s string,path []string,startIndex int,res *[]string){
	//终止条件
	if startIndex==len(s)&&len(path)==4{
		tmpIpString:=path[0]+"."+path[1]+"."+path[2]+"."+path[3]
		*res=append(*res,tmpIpString)
	}
	for i:=startIndex;i<len(s);i++{
		//处理
		path:=append(path,s[startIndex:i+1])
		if i-startIndex+1<=3&&len(path)<=4&&isNormalIp(s,startIndex,i){
			//递归
			backTracking(s,path,i+1,res)
		}else {//如果首尾超过了3个，或路径多余4个，或前导为0，或大于255，直接回退
			return
		}
        //回溯
		path=path[:len(path)-1]
	}
}
func isNormalIp(s string,startIndex,end int)bool{
	checkInt,_:=strconv.Atoi(s[startIndex:end+1])
	if end-startIndex+1>1&&s[startIndex]=='0'{//对于前导 0的IP（特别**注意**s[startIndex]=='0'的判断，不应该写成s[startIndex]==0，因为s截取出来不是数字）
		return false
	}
	if checkInt>255{
		return false
	}
	return true
}
```
### 子集问题
给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

```go
var res [][]int
func subset(nums []int) [][]int {
	res = make([][]int, 0)
	sort.Ints(nums)
	Dfs([]int{}, nums, 0)
	return res
}
func Dfs(temp, nums []int, start int){
	tmp := make([]int, len(temp))
	copy(tmp, temp)
	res = append(res, tmp)
	for i := start; i < len(nums); i++{
		//if i>start&&nums[i]==nums[i-1]{
		//	continue
		//}
		temp = append(temp, nums[i])
		Dfs(temp, nums, i+1)
		temp = temp[:len(temp)-1]
	}
}
```
### 子集II
给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

```go
var res[][]int
func subsetsWithDup(nums []int)[][]int {
	res=make([][]int,0)
	 sort.Ints(nums)
	dfs([]int{},nums,0)
	return res
}
func dfs(temp, num []int, start int)  {
	tmp:=make([]int,len(temp))
	copy(tmp,temp)

	res=append(res,tmp)
	for i:=start;i<len(num);i++{
		if i>start&&num[i]==num[i-1]{
			continue
		}
		temp=append(temp,num[i])
		dfs(temp,num,i+1)
		temp=temp[:len(temp)-1]
	}
}
```
### 递增子序列
给定一个整型数组, 你的任务是找到所有该数组的递增子序列，递增子序列的长度至少是2。给定数组中可能包含重复数字，相等的数字应该被视为递增的一种情况

```go
func findSubsequences(nums []int) [][]int {
    var subRes []int
    var res [][]int
    backTring(0,nums,subRes,&res)
    return res
}
func backTring(startIndex int,nums,subRes []int,res *[][]int){
    if len(subRes)>1{
    tmp:=make([]int,len(subRes))
    copy(tmp,subRes)
    *res=append(*res,tmp)
    }
    history:=[201]int{}//记录本层元素使用记录
    for i:=startIndex;i<len(nums);i++{
        //分两种情况判断：一，当前取的元素小于子集的最后一个元素，则继续寻找下一个适合的元素
        //                或者二，当前取的元素在本层已经出现过了，所以跳过该元素，继续寻找
        if len(subRes)>0&&nums[i]<subRes[len(subRes)-1]||history[nums[i] + 100]==1{
            continue
        }
        history[nums[i] + 100]=1//表示本层该元素使用过了
        subRes=append(subRes,nums[i])
        backTring(i+1,nums,subRes,res)
        subRes=subRes[:len(subRes)-1]
    }
}
```
### 全排列
给定一个 没有重复 数字的序列，返回其所有可能的全排列。
```go
var res [][]int
func permute(nums []int) [][]int {
	res = [][]int{}
	backTrack(nums,len(nums),[]int{})
	return res
}
func backTrack(nums []int,numsLen int,path []int)  {
	if len(nums)==0{
		p:=make([]int,len(path))
		copy(p,path)
		res = append(res,p)
	}
	for i:=0;i<numsLen;i++{
		cur:=nums[i]
		path = append(path,cur)
		nums = append(nums[:i],nums[i+1:]...)//直接使用切片
		backTrack(nums,len(nums),path)
		nums = append(nums[:i],append([]int{cur},nums[i:]...)...)//回溯的时候切片也要复原，元素位置不能变
		path = path[:len(path)-1]

	}
}
```
### 全排列II
给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。
```go
var res [][]int
func permute(nums []int) [][]int {
	res = [][]int{}
	backTrack(nums,len(nums),[]int{})
	return res
}
func backTrack(nums []int,numsLen int,path []int)  {
	if len(nums)==0{
		p:=make([]int,len(path))
		copy(p,path)
		res = append(res,p)
	}
	used := [21]int{}//跟前一题唯一的区别，同一层不使用重复的数。关于used的思想carl在递增子序列那一题中提到过
	for i:=0;i<numsLen;i++{
		if used[nums[i]+10]==1{
			continue
		}
		cur:=nums[i]
		path = append(path,cur)
		used[nums[i]+10]=1
		nums = append(nums[:i],nums[i+1:]...)
		backTrack(nums,len(nums),path)
		nums = append(nums[:i],append([]int{cur},nums[i:]...)...)
		path = path[:len(path)-1]

	}
}
```
### 回溯算法去重问题的另一种写法
https://programmercarl.com/%E5%9B%9E%E6%BA%AF%E7%AE%97%E6%B3%95%E5%8E%BB%E9%87%8D%E9%97%AE%E9%A2%98%E7%9A%84%E5%8F%A6%E4%B8%80%E7%A7%8D%E5%86%99%E6%B3%95.html#_90-%E5%AD%90%E9%9B%86ii

看c++版的
### 重新安排行程
深搜和回溯也是相辅相成的，都用了递归。

给定一个机票的字符串二维数组 [from, to]，子数组中的两个成员分别表示飞机出发和降落的机场地点，对该行程进行重新规划排序。所有这些机票都属于一个从 JFK（肯尼迪国际机场）出发的先生，所以该行程必须从 JFK 开始。

提示：
- 如果存在多种有效的行程，请你按字符自然排序返回最小的行程组合。例如，行程 ["JFK", "LGA"] 与 ["JFK", "LGB"] 相比就更小，排序更靠前
- 所有的机场都用三个大写字母表示（机场代码）。
- 假定所有机票至少存在一种合理的行程。
- 所有的机票必须都用一次 且 只能用一次。

```
示例 1：

输入：[["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]
输出：["JFK", "MUC", "LHR", "SFO", "SJC"]
示例 2：

输入：[["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
输出：["JFK","ATL","JFK","SFO","ATL","SFO"]
解释：另一种有效的行程是 ["JFK","SFO","ATL","JFK","ATL","SFO"]。但是它自然排序更大更靠后
```
```c++
class Solution {
private:
// unordered_map<出发机场, map<到达机场, 航班次数>> targets
unordered_map<string, map<string, int>> targets;
bool backtracking(int ticketNum, vector<string>& result) {
    if (result.size() == ticketNum + 1) {
        return true;
    }
    for (pair<const string, int>& target : targets[result[result.size() - 1]]) {
        if (target.second > 0 ) { // 记录到达机场是否飞过了
            result.push_back(target.first);
            target.second--;
            if (backtracking(ticketNum, result)) return true;
            result.pop_back();
            target.second++;
        }
    }
    return false;
}
public:
    vector<string> findItinerary(vector<vector<string>>& tickets) {
        targets.clear();
        vector<string> result;
        for (const vector<string>& vec : tickets) {
            targets[vec[0]][vec[1]]++; // 记录映射关系
        }
        result.push_back("JFK"); // 起始机场
        backtracking(tickets.size(), result);
        return result;
    }
};
```
### N皇后
n 皇后问题 研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 n ，返回所有不同的 n 皇后问题 的解决方案。

每一种解法包含一个不同的 n 皇后问题 的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。
```go

import "strings"
var res [][]string

func isValid(board [][]string, row, col int) (res bool){
    n := len(board)
    for i:=0; i < row; i++ {
        if board[i][col] == "Q" {
            return false
        }
    }
    for i := 0; i < n; i++{
        if board[row][i] == "Q" {
            return false
        }
    }

    for i ,j := row, col; i >= 0 && j >=0 ; i, j = i - 1, j- 1{
        if board[i][j] == "Q"{
            return false
        }
    }
    for i, j := row, col; i >=0 && j < n; i,j = i-1, j+1 {
        if board[i][j] == "Q" {
            return false
        }
    }
    return true
}

func backtrack(board [][]string, row int) {
    size := len(board)
    if row == size{
        temp := make([]string, size)
        for i := 0; i<size;i++{
            temp[i] = strings.Join(board[i],"")
        }
        res =append(res,temp)
        return 
    }
    for col := 0; col < size; col++ {
        if !isValid(board, row, col){
            continue
        }
        board[row][col] = "Q"
        backtrack(board, row+1)
        board[row][col] = "."
    }
}

func solveNQueens(n int) [][]string {
    res = [][]string{}
    board := make([][]string, n)
    for i := 0; i < n; i++{
        board[i] = make([]string, n)
    }
    for i := 0; i < n; i++{
        for j := 0; j<n;j++{
            board[i][j] = "."
        }
    }
    backtrack(board, 0)

    return res
}
```
### 解数独
编写一个程序，通过填充空格来解决数独问题。
```go
func solveSudoku(board [][]byte)  {
    var backtracking func(board [][]byte) bool
    backtracking=func(board [][]byte) bool{
        for i:=0;i<9;i++{
            for j:=0;j<9;j++{
                //判断此位置是否适合填数字
                if board[i][j]!='.'{
                    continue
                }
                //尝试填1-9
                for k:='1';k<='9';k++{
                    if isvalid(i,j,byte(k),board)==true{//如果满足要求就填
                        board[i][j]=byte(k)
                       if  backtracking(board)==true{
                           return true
                       }
                        board[i][j]='.'
                    }
                }
                return false
            }
        }
        return true
    }
    backtracking(board)
}
//判断填入数字是否满足要求
func isvalid(row,col int,k byte,board [][]byte)bool{
    for i:=0;i<9;i++{//行
        if board[row][i]==k{
            return false
        }
    }
    for i:=0;i<9;i++{//列
        if board[i][col]==k{
            return false
        }
    }
    //方格
    startrow:=(row/3)*3
    startcol:=(col/3)*3
    for i:=startrow;i<startrow+3;i++{
        for j:=startcol;j<startcol+3;j++{
            if board[i][j]==k{
                return false
            }
        }
    }
    return true
}
```

## 贪心算法
### 理论基础
贪心算法一般分为如下四步：
- 将问题分解为若干个子问题
- 找出适合的贪心策略
- 求解每一个子问题的最优解
- 将局部最优解堆叠成全局最优解
### 分发饼干
假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。

对每个孩子 i，都有一个胃口值 g[i]，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 j，都有一个尺寸 s[j] 。如果 s[j] >= g[i]，我们可以将这个饼干 j 分配给孩子 i ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。
```
示例 1:

输入: g = [1,2,3], s = [1,1]
输出: 1 解释:你有三个孩子和两块小饼干，3个孩子的胃口值分别是：1,2,3。虽然你有两块小饼干，由于他们的尺寸都是1，你只能让胃口值是1的孩子满足。所以你应该输出1。
```
```go
//排序后，局部最优
func findContentChildren(g []int, s []int) int {
  sort.Ints(g)
  sort.Ints(s)

  // 从小到大
  child := 0
  for sIdx := 0; child < len(g) && sIdx < len(s); sIdx++ {
    if s[sIdx] >= g[child] {//如果饼干的大小大于或等于孩子的为空则给与，否则不给予，继续寻找选一个饼干是否符合
      child++
    }
  }

  return child
}
```
### 摆动序列
如果连续数字之间的差严格地在正数和负数之间交替，则数字序列称为摆动序列。第一个差（如果存在的话）可能是正数或负数。少于两个元素的序列也是摆动序列。

例如， [1,7,4,9,2,5] 是一个摆动序列，因为差值 (6,-3,5,-7,3) 是正负交替出现的。相反, [1,4,7,2,5] 和 [1,7,4,5,5] 不是摆动序列，第一个序列是因为它的前两个差值都是正数，第二个序列是因为它的最后一个差值为零。

给定一个整数序列，返回作为摆动序列的最长子序列的长度。 通过从原始序列中删除一些（也可以不删除）元素来获得子序列，剩下的元素保持其原始顺序。
```
示例 1:

输入: [1,7,4,9,2,5]
输出: 6
解释: 整个序列均为摆动序列。
示例 2:

输入: [1,17,5,10,13,15,10,5,16,8]
输出: 7
解释: 这个序列包含几个长度为 7 摆动序列，其中一个可为[1,17,10,13,10,16,8]。
```
贪心或者dp

```go
func wiggleMaxLength(nums []int) int {
    var count,preDiff,curDiff  int
    count=1
    if len(nums)<2{
        return count
    }
    for i:=0;i<len(nums)-1;i++{
        curDiff=nums[i+1]-nums[i]
        //如果有正有负则更新下标值||或者只有前一个元素为0（针对两个不等元素的序列也视作摆动序列，且摆动长度为2）
        if (curDiff > 0 && preDiff <= 0) || (preDiff >= 0 && curDiff < 0){
            preDiff=curDiff
            count++
        }
    }
    return count
}
```
### 最大子序和
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例: 输入: [-2,1,-3,4,-1,2,1,-5,4] 输出: 6 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6

贪心或者dp

```go
func maxSubArray(nums []int) int {
    maxSum := nums[0]
    for i := 1; i < len(nums); i++ {
        if nums[i] + nums[i-1] > nums[i] {
            nums[i] += nums[i-1]
        }
        if nums[i] > maxSum {
            maxSum = nums[i]
        }
    }
    return maxSum
}
```
### 买卖股票的最佳时机II
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
```
示例 1:

输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4。随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
示例 2:

输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。**注意**你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
示例 3:

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0
```
贪心或者dp

```go
//贪心算法
func maxProfit(prices []int) int {
    var sum int
    for i := 1; i < len(prices); i++ {
        // 累加每次大于0的交易
        if prices[i]-prices[i-1] > 0 {
            sum += prices[i]-prices[i-1]
        }
    }
    return sum
}
```
```go
//确定售卖点
func maxProfit(prices []int) int {
    var result,buy int
    prices=append(prices,0)//在price末尾加个0，防止price一直递增
    /**
    思路：检查后一个元素是否大于当前元素，如果小于，则表明这是一个售卖点，当前元素的值减去购买时候的值
            如果不小于，说明后面有更好的售卖点，
    **/
    for i:=0;i<len(prices)-1;i++{
        if prices[i]>prices[i+1]{
            result+=prices[i]-prices[buy]
            buy=i+1
        }else if prices[buy]>prices[i]{//更改最低购买点
            buy=i
        }
    }
    return result
}
```
### 跳跃游戏
#### I
给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。
```go
func canJUmp(nums []int) bool {
	if len(nums)<=1{
		return true
	}
	dp:=make([]bool,len(nums))
	dp[0]=true
	for i:=1;i<len(nums);i++{
		for j:=i-1;j>=0;j--{
			if dp[j]&&nums[j]+j>=i{
				dp[i]=true
				break
			}
		}
	}
	return dp[len(nums)-1]
}
```
#### II
给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

你的目标是使用最少的跳跃次数到达数组的最后一个位置。
```go
func jump(nums []int) int {
	dp:=make([]int ,len(nums))
	dp[0]=0

	for i:=1;i<len(nums);i++{
		dp[i]=i
		for j:=0;j<i;j++{
			if nums[j]+j>i{
				dp[i]=min(dp[j]+1,dp[i])
			}
		}
	}
	return dp[len(nums)-1]
}
```
### K次取反后最大化的数组和
给定一个整数数组 A，我们只能用以下方法修改该数组：我们选择某个索引 i 并将 A[i] 替换为 -A[i]，然后总共重复这个过程 K 次。（我们可以多次选择同一个索引 i。）

以这种方式修改数组后，返回数组可能的最大和。
```go
func largestSumAfterKNegations(nums []int, K int) int {
	sort.Slice(nums, func(i, j int) bool {
		return math.Abs(float64(nums[i])) > math.Abs(float64(nums[j]))
	})
  
	for i := 0; i < len(nums); i++ {
		if K > 0 && nums[i] < 0 {
			nums[i] = -nums[i]
			K--
		}
	}

	if K%2 == 1 {
		nums[len(nums)-1] = -nums[len(nums)-1]
	}

	result := 0
	for i := 0; i < len(nums); i++ {
		result += nums[i]
	}
	return result
}
```
### 加油站
在一条环路上有 N 个加油站，其中第 i 个加油站有汽油 gas[i] 升。

你有一辆油箱容量无限的的汽车，从第 i 个加油站开往第 i+1 个加油站需要消耗汽油 cost[i] 升。你从其中的一个加油站出发，开始时油箱为空。

如果你可以绕环路行驶一周，则返回出发时加油站的编号，否则返回 -1。
- 如果题目有解，该答案即为唯一答案。
- 输入数组均为非空数组，且长度相同。
- 输入数组中的元素均为非负数。

```go
func canCompleteCircuit(gas []int, cost []int) int {
	curSum := 0
	totalSum := 0
	start := 0
	for i := 0; i < len(gas); i++ {
		curSum += gas[i] - cost[i]
		totalSum += gas[i] - cost[i]
		if curSum < 0 {
			start = i+1
			curSum = 0
		}
	}
	if totalSum < 0 {
		return -1
	}
	return start
}
```
### 分发糖果
老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。

你需要按照以下要求，帮助老师给这些孩子分发糖果：
- 每个孩子至少分配到 1 个糖果。
- 相邻的孩子中，评分高的孩子必须获得更多的糖果。

那么这样下来，老师至少需要准备多少颗糖果呢？
```
示例 1:

输入: [1,0,2]
输出: 5
解释: 你可以分别给这三个孩子分发 2、1、2 颗糖果。
示例 2:

输入: [1,2,2]
输出: 4
解释: 你可以分别给这三个孩子分发 1、2、1 颗糖果。第三个孩子只得到 1 颗糖果，这已满足上述两个条件。
```
```go

func candy(ratings []int) int {
    /**先确定一边，再确定另外一边
        1.先从左到右，当右边的大于左边的就加1
        2.再从右到左，当左边的大于右边的就再加1
    **/
    need:=make([]int,len(ratings))
    sum:=0
    //初始化(每个人至少一个糖果)
     for i:=0;i<len(ratings);i++{
         need[i]=1
     }
     //1.先从左到右，当右边的大于左边的就加1
    for i:=0;i<len(ratings)-1;i++{
        if ratings[i]<ratings[i+1]{
            need[i+1]=need[i]+1
        }
    }
    //2.再从右到左，当左边的大于右边的就右边加1，但要花费糖果最少，所以需要做下判断
    for i:=len(ratings)-1;i>0;i--{
        if ratings[i-1]>ratings[i]{
            need[i-1]=findMax(need[i-1],need[i]+1)
        }
    }
    //计算总共糖果
    for i:=0;i<len(ratings);i++{
        sum+=need[i]
    }
    return sum
}
func findMax(num1 int ,num2 int) int{
    if num1>num2{
        return num1
    }
    return num2
}
```
### 柠檬水找零
在柠檬水摊上，每一杯柠檬水的售价为 5 美元。

顾客排队购买你的产品，（按账单 bills 支付的顺序）一次购买一杯。

每位顾客只买一杯柠檬水，然后向你付 5 美元、10 美元或 20 美元。你必须给每个顾客正确找零，也就是说净交易是每位顾客向你支付 5 美元。

**注意**，一开始你手头没有任何零钱。

如果你能给每位顾客正确找零，返回 true ，否则返回 false 。
```
示例 1：

输入：[5,5,5,10,20]
输出：true
解释：
前 3 位顾客那里，我们按顺序收取 3 张 5 美元的钞票。
第 4 位顾客那里，我们收取一张 10 美元的钞票，并返还 5 美元。
第 5 位顾客那里，我们找还一张 10 美元的钞票和一张 5 美元的钞票。
由于所有客户都得到了正确的找零，所以我们输出 true。
示例 2：

输入：[5,5,10]
输出：true
```
```go

func lemonadeChange(bills []int) bool {
    //left表示还剩多少 下标0位5元的个数 ，下标1为10元的个数
    left:=[2]int{0,0}
    //第一个元素不为5，直接退出
    if bills[0]!=5{
        return false
    }
    for i:=0;i<len(bills);i++{
        //先统计5元和10元的个数
        if bills[i]==5{
            left[0]+=1
        }
        if bills[i]==10{
            left[1]+=1
        }
        //接着处理找零的
        tmp:=bills[i]-5
        if tmp==5{
            if left[0]>0{
                left[0]-=1
            }else {
                return false
            }
        }
        if tmp==15{
            if left[1]>0&&left[0]>0{
                left[0]-=1
                left[1]-=1
            }else if left[1]==0&&left[0]>2{
                left[0]-=3
            }else{
                return false
            }
        }
    }
    return true
}
```
### 根据身高重建队列
假设有打乱顺序的一群人站成一个队列，数组 people 表示队列中一些人的属性（不一定按顺序）。每个 people[i] = [hi, ki] 表示第 i 个人的身高为 hi ，前面 正好 有 ki 个身高大于或等于 hi 的人。

请你重新构造并返回输入数组 people 所表示的队列。返回的队列应该格式化为数组 queue ，其中 queue[j] = [hj, kj] 是队列中第 j 个人的属性（queue[0] 是排在队列前面的人）。

示例 1：
```
输入：people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
输出：[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
解释：
编号为 0 的人身高为 5 ，没有身高更高或者相同的人排在他前面。
编号为 1 的人身高为 7 ，没有身高更高或者相同的人排在他前面。
编号为 2 的人身高为 5 ，有 2 个身高更高或者相同的人排在他前面，即编号为 0 和 1 的人。
编号为 3 的人身高为 6 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
编号为 4 的人身高为 4 ，有 4 个身高更高或者相同的人排在他前面，即编号为 0、1、2、3 的人。
编号为 5 的人身高为 7 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
因此 [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]] 是重新构造后的队列。
```
```go
func reconstructQueue(people [][]int) [][]int {
    //先将身高从大到小排序，确定最大个子的相对位置
    sort.Slice(people,func(i,j int)bool{
        if people[i][0]==people[j][0]{
            return people[i][1]<people[j][1]//这个才是当身高相同时，将K按照从小到大排序
        }
        return people[i][0]>people[j][0]//这个只是确保身高按照由大到小的顺序来排，并不确定K是按照从小到大排序的
    })
    //再按照K进行插入排序，优先插入K小的
    result := make([][]int, 0)
	for _, info := range people {
		result = append(result, info)
		copy(result[info[1] +1:], result[info[1]:])//将插入位置之后的元素后移动一位（意思是腾出空间）
		result[info[1]] = info//将插入元素位置插入元素
	}
	return result
}
//链表法
func reconstructQueue(people [][]int) [][]int {
     sort.Slice(people,func (i,j int) bool {
        if people[i][0]==people[j][0]{
            return people[i][1]<people[j][1]//当身高相同时，将K按照从小到大排序
        }
         //先将身高从大到小排序，确定最大个子的相对位置
        return people[i][0]>people[j][0]
    })
    l:=list.New()//创建链表
    for i:=0;i<len(people);i++{
        position:=people[i][1]
        mark:=l.PushBack(people[i])//插入元素
        e:=l.Front()
        for position!=0{//获取相对位置
            position--
            e=e.Next()
        }
        l.MoveBefore(mark,e)//移动位置
        
    }
    res:=[][]int{}
    for e:=l.Front();e!=nil;e=e.Next(){
        res=append(res,e.Value.([]int))
    }
    return res
}
```
### 用最少数量的箭引爆气球
在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以纵坐标并不重要，因此只要知道开始和结束的横坐标就足够了。开始坐标总是小于结束坐标。

一支弓箭可以沿着 x 轴从不同点完全垂直地射出。在坐标 x 处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。

给你一个数组 points ，其中 points [i] = [xstart,xend] ，返回引爆所有气球所必须射出的最小弓箭数。
```
示例 1：

输入：points = [[10,16],[2,8],[1,6],[7,12]]
输出：2
解释：对于该样例，x = 6 可以射爆 [2,8],[1,6] 两个气球，以及 x = 11 射爆另外两个气球
示例 2：

输入：points = [[1,2],[3,4],[5,6],[7,8]]
输出：4
示例 3：

输入：points = [[1,2],[2,3],[3,4],[4,5]]
输出：2
示例 4：

输入：points = [[1,2]]
输出：1
示例 5：

输入：points = [[2,3],[2,3]]
输出：1
提示：

0 <= points.length <= 10^4
points[i].length == 2
-2^31 <= xstart < xend <= 2^31 - 1
```
```go
func findMinArrowShots(points [][]int) int {
    var res int =1//弓箭数
    //先按照第一位排序
    sort.Slice(points,func (i,j int) bool{
        return points[i][0]<points[j][0]
    })

    for i:=1;i<len(points);i++{
        if points[i-1][1]<points[i][0]{//如果前一位的右边界小于后一位的左边界，则一定不重合
            res++
        }else{
            points[i][1] = min(points[i - 1][1], points[i][1]); // 更新重叠气球最小右边界,覆盖该位置的值，留到下一步使用
        }
    }
    return res
}
func min(a,b int) int{
    if a>b{
        return b
    }
    return a
}
```
### 无重叠区间
给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

**注意**: 可以认为区间的终点总是大于它的起点。 区间 [1,2] 和 [2,3] 的边界相互“接触”，但没有相互重叠。

示例 1:

输入: [ [1,2], [2,3], [3,4], [1,3] ]
输出: 1
解释: 移除 [1,3] 后，剩下的区间没有重叠。

```go
func eraseOverlapIntervals(intervals [][]int) int {
    var flag int
    //先排序
    sort.Slice(intervals,func(i,j int)bool{
        return intervals[i][0]<intervals[j][0]
    })
    fmt.Println(intervals)
    for i:=1;i<len(intervals);i++{
        if intervals[i-1][1]>intervals[i][0]{
            flag++
            intervals[i][1]=min(intervals[i-1][1],intervals[i][1])//由于是先排序的，所以，第一位是递增顺序，故只需要将临近两个元素的第二个值最小值更新到该元素的第二个值即可作之后的判断
        }
    }
    return flag
}
func min(a,b int)int{
    if a>b{
        return b
    }
    return a
}
```
### 划分字母区间
字符串 S 由小写字母组成。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。返回一个表示每个字符串片段的长度的列表。

示例：

输入：S = "ababcbacadefegdehijhklij"
输出：[9,7,8] 解释： 划分结果为 "ababcbaca", "defegde", "hijhklij"。 每个字母最多出现在一个片段中。 像 "ababcbacadefegde", "hijhklij" 的划分是错误的，因为划分的片段数较少。
```go
func partitionLabels(s string) []int {
    var res []int;
    var marks [26]int;
    size, left, right := len(s), 0, 0;
    for i := 0; i < size; i++ {
        marks[s[i] - 'a'] = i;
    }
    for i := 0; i < size; i++ {
        right = max(right, marks[s[i] - 'a']);
        if i == right {
            res = append(res, right - left + 1);
            left = i + 1;
        }
    }
    return res;
}

func max(a, b int) int {
    if a < b {
        a = b;
    }
    return a;
}
```
### 合并区间
给出一个区间的集合，请合并所有重叠的区间。
```go
func merge(intervals [][]int) [][]int {
    //先从小到大排序
    sort.Slice(intervals,func(i,j int)bool{
        return intervals[i][0]<intervals[j][0]
    })
    //再弄重复的
    for i:=0;i<len(intervals)-1;i++{
        if intervals[i][1]>=intervals[i+1][0]{
            intervals[i][1]=max(intervals[i][1],intervals[i+1][1])//赋值最大值
            intervals=append(intervals[:i+1],intervals[i+2:]...)
            i--
        }
    }
    return intervals
}
func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```
### 单调递增的数字
给定一个非负整数 N，找出小于或等于 N 的最大的整数，同时这个整数需要满足其各个位数上的数字是单调递增

示例：
- 输入: N = 332
- 输出: 299
```go
func monotoneIncreasingDigits(N int) int {
    s := strconv.Itoa(N)//将数字转为字符串，方便使用下标
    ss := []byte(s)//将字符串转为byte数组，方便更改。
    n := len(ss)
    if n <= 1 {
        return N
    }
    for i:=n-1 ; i>0; i-- {
        if ss[i-1] > ss[i] {//前一个大于后一位,前一位减1，后面的全部置为9
            ss[i-1] -= 1
            for j := i ; j < n; j++ {//后面的全部置为9
                ss[j] = '9'
            }
        } 
    }
    res, _ := strconv.Atoi(string(ss))
    return res 
}
```
### 买卖股票的最佳时机含手续费
给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

**注意**：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

示例 1: 输入: prices = [1, 3, 2, 8, 4, 9], fee = 2 输出: 8

解释: 能够达到的最大利润: 在此处买入 prices[0] = 1 在此处卖出 prices[3] = 8 在此处买入 prices[4] = 4 在此处卖出 prices[5] = 9 总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

贪心或者dp

```go
func maxProfit(prices []int, fee int) int {
    var minBuy int = prices[0] //第一天买入
    var res int
    for i:=0;i<len(prices);i++{
        //如果当前价格小于最低价，则在此处买入
        if prices[i]<minBuy{
            minBuy=prices[i]
        }
        //如果以当前价格卖出亏本，则不卖，继续找下一个可卖点
        if prices[i]>=minBuy&&prices[i]-fee-minBuy<=0{
            continue
        }
        //可以售卖了
        if prices[i]>minBuy+fee{
            //累加每天的收益
            res+=prices[i]-minBuy-fee
            //更新最小值（如果还在收获利润的区间里，表示并不是真正的卖出，而计算利润每次都要减去手续费，所以要让minBuy = prices[i] - fee;，这样在明天收获利润的时候，才不会多减一次手续费！）
            minBuy=prices[i]-fee
        }
    }
    return res
}
```
### 监控二叉树
给定一个二叉树，我们在树的节点上安装摄像头。

节点上的每个摄影头都可以监视其父对象、自身及其直接子对象。

计算监控树的所有节点所需的最小摄像头数量。
```go
const inf = math.MaxInt64 / 2

func minCameraCover(root *TreeNode) int {
    var dfs func(*TreeNode) (a, b, c int)
    dfs = func(node *TreeNode) (a, b, c int) {
        if node == nil {
            return inf, 0, 0
        }
        lefta, leftb, leftc := dfs(node.Left)
        righta, rightb, rightc := dfs(node.Right)
        a = leftc + rightc + 1
        b = min(a, min(lefta+rightb, righta+leftb))
        c = min(a, leftb+rightb)
        return
    }
    _, ans, _ := dfs(root)
    return ans
}

func min(a, b int) int {
    if a <= b {
        return a
    }
    return b
}

```

## 动态规划
有很多重叠子问题，优先考虑使用动态规划。

与贪心的区别：贪心不会考虑之前的状态而只考虑局部最优。
### 理论基础
**dp步骤：**
- 确定dp数组（dp table）以及下标的含义
- 确定递推公式
- dp数组如何初始化
- 确定遍历顺序
- 举例推导dp数组

debug:把dp数组打印出来
### 斐波那契数
```go
func fib(n int) int {
    if n < 2 {
        return n
    }
    a, b, c := 0, 1, 0
    for i := 1; i < n; i++ {
        c = a + b
        a, b = b, c
    }
        return c
}
```
### 爬楼梯
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意**：给定 n 是一个正整数。

示例 1：
```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1 阶 + 1 阶
2 阶
```
```go
func climbStairs(n int) int {
    if n==1{
        return 1
    }
    dp:=make([]int,n+1)
    dp[1]=1
    dp[2]=2
    for i:=3;i<=n;i++{
        dp[i]=dp[i-1]+dp[i-2]
    }
    return dp[n]
}
```
#### 使用最小花费爬楼梯
数组的每个下标作为一个阶梯，第 i 个阶梯对应着一个非负数的体力花费值 cost[i]（下标从 0 开始）。

每当你爬上一个阶梯你都要花费对应的体力值，一旦支付了相应的体力值，你就可以选择向上爬一个阶梯或者爬两个阶梯。

请你找出达到楼层顶部的最低花费。在开始时，你可以选择从下标为 0 或 1 的元素作为初始阶梯。

示例 1：

输入：cost = [10, 15, 20] 输出：15 解释：最低花费是从 cost[1] 开始，然后走两步即可到阶梯顶，一共花费 15 
```go
func minCostClimbingStairs(cost []int) int {
	dp := make([]int, len(cost))
	dp[0], dp[1] = cost[0], cost[1]
	for i := 2; i < len(cost); i++ {
		dp[i] = min(dp[i-1], dp[i-2]) + cost[i]
	}
	return min(dp[len(cost)-1], dp[len(cost)-2])
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```
### 不同路径
#### I
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？
```go
func uniquePaths(m int, n int) int {
	dp := make([][]int, m)
	for i := range dp {
		dp[i] = make([]int, n)
		dp[i][0] = 1
	}
	for j := 0; j < n; j++ {
		dp[0][j] = 1
	}
	for i := 1; i < m; i++ {
		for j := 1; j < n; j++ {
			dp[i][j] = dp[i-1][j] + dp[i][j-1]
		}
	}
	return dp[m-1][n-1]
}
```
#### II
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
```go
func uniquePathsWithObstacles(obstacleGrid [][]int) int {
	m,n:= len(obstacleGrid),len(obstacleGrid[0])
	// 定义一个dp数组
	dp := make([][]int,m)
	for i,_ := range dp {
		dp[i] = make([]int,n)
	}
	// 初始化
	for i:=0;i<m;i++ {
		// 如果是障碍物, 后面的就都是0, 不用循环了
		if obstacleGrid[i][0] == 1 {
			break
		}
		dp[i][0]=1
	}
	for i:=0;i<n;i++ {
		if obstacleGrid[0][i] == 1 {
			break
		}
		dp[0][i]=1
	}
	// dp数组推导过程
	for i:=1;i<m;i++ {
		for j:=1;j<n;j++ {
			// 如果obstacleGrid[i][j]这个点是障碍物, 那么我们的dp[i][j]保持为0
           	 	if obstacleGrid[i][j] != 1 {
				// 否则我们需要计算当前点可以到达的路径数
				dp[i][j] = dp[i-1][j]+dp[i][j-1]
			}
		}
	}
	// debug遍历dp
	//for i,_ := range dp {
	//	for j,_ := range dp[i] {
	//		fmt.Printf("%.2v,",dp[i][j])
	//	}
	//	fmt.Println()
	//}
	return dp[m-1][n-1]
}
```
### 整数拆分
给定一个正整数 n，将其拆分为至少两个正整数的和，并使这些整数的乘积最大化。 返回你可以获得的最大乘积。
```go
func integerBreak(n int) int {
    /**
    动态五部曲
    1.确定dp下标及其含义
    2.确定递推公式
    3.确定dp初始化
    4.确定遍历顺序
    5.打印dp
    **/
    dp:=make([]int,n+1)
    dp[1]=1
    dp[2]=1
    for i:=3;i<n+1;i++{
        for j:=1;j<i-1;j++{
// i可以差分为i-j和j。由于需要最大值，故需要通过j遍历所有存在的值，取其中最大的值作为当前i的最大值，在求最大值的时候，一个是j与i-j相乘，一个是j与dp[i-j].
            dp[i]=max(dp[i],max(j*(i-j),j*dp[i-j]))
        }
    }
    return dp[n]
}
func max(a,b int) int{
    if a>b{
        return a
    }
    return b
}
```
### 不同的二叉搜索树
给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？
```go
func numTrees(n int)int{
	dp:=make([]int,n+1)
	dp[0]=1
	for i:=1;i<=n;i++{
		for j:=1;j<=i;j++{
			dp[i]+=dp[j-1]*dp[i-j]
		}
	}
	return dp[n]
}
```
### 0-1背包理论基础
#### I
暴力的解法是指数级别的时间复杂度。进而才需要动态规划的解法来进行优化
[代码随想录详解](https://programmercarl.com/%E8%83%8C%E5%8C%85%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%8001%E8%83%8C%E5%8C%85-1.html#_01-%E8%83%8C%E5%8C%85)

```go

func test_2_wei_bag_problem1(weight, value []int, bagweight int) int {
	// 定义dp数组
	dp := make([][]int, len(weight))
	for i, _ := range dp {
		dp[i] = make([]int, bagweight+1)
	}
	// 初始化
	for j := bagweight; j >= weight[0]; j-- {
		dp[0][j] = dp[0][j-weight[0]] + value[0]
	}
	// 递推公式
	for i := 1; i < len(weight); i++ {
		//正序,也可以倒序
		for  j := weight[i];j<= bagweight ; j++ {
			dp[i][j] = max(dp[i-1][j], dp[i-1][j-weight[i]]+value[i])
		}		
	}
	return dp[len(weight)-1][bagweight]
}

func max(a,b int) int {
	if a > b {
		return a
	}
	return b
}

func main() {
	weight := []int{1,3,4}
	value := []int{15,20,30}
	test_2_wei_bag_problem1(weight,value,4)
}
```
#### II
[代码随想录详解](https://programmercarl.com/%E8%83%8C%E5%8C%85%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%8001%E8%83%8C%E5%8C%85-2.html#%E4%B8%80%E7%BB%B4dp%E6%95%B0%E7%BB%84-%E6%BB%9A%E5%8A%A8%E6%95%B0%E7%BB%84)

```go

func test_1_wei_bag_problem(weight, value []int, bagWeight int) int {
	// 定义 and 初始化
	dp := make([]int,bagWeight+1)
	// 递推顺序
	for i := 0 ;i < len(weight) ; i++ {
		// 这里必须倒序,区别二维,因为二维dp保存了i的状态
		for j:= bagWeight; j >= weight[i] ; j-- {
			// 递推公式
			dp[j] = max(dp[j], dp[j-weight[i]]+value[i])
		}
	}
	//fmt.Println(dp)
	return dp[bagWeight]
}

func max(a,b int) int {
	if a > b {
		return a
	}
	return b
}


func main() {
	weight := []int{1,3,4}
	value := []int{15,20,30}
	test_1_wei_bag_problem(weight,value,4)
}
```
### 分割等和子集
给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

**注意**: 每个数组中的元素不会超过 100 数组的大小不会超过 200

示例 1: 输入: [1, 5, 11, 5] 输出: true 解释: 数组可以分割成 [1, 5, 5] 和 [11].

示例 2: 输入: [1, 2, 3, 5] 输出: false 解释: 数组不能分割成两个元素和相等的子集.
```go
// 分割等和子集 动态规划
// 时间复杂度O(n^2) 空间复杂度O(n)
func canPartition(nums []int) bool {
    sum := 0
    for _, num := range nums {
        sum += num
    }
    // 如果 nums 的总和为奇数则不可能平分成两个子集
    if sum % 2 == 1 {
        return false
    }
    
    target := sum / 2
    dp := make([]int, target + 1)

    for _, num := range nums {
        for j := target; j >= num; j-- {
            if dp[j] < dp[j - num] + num {
                dp[j] = dp[j - num] + num
            }
        }
    }
    return dp[target] == target
}
```
### 最后一块石头的重量II
有一堆石头，每块石头的重量都是正整数。

每一回合，从中选出任意两块石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：

如果 x == y，那么两块石头都会被完全粉碎； 如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。 最后，最多只会剩下一块石头。返回此石头最小的可能重量。如果没有石头剩下，就返回 0。

示例： 输入：[2,7,4,1,8,1] 输出：1 解释： 组合 2 和 4，得到 2，所以数组转化为 [2,7,1,8,1]， 组合 7 和 8，得到 1，所以数组转化为 [2,1,1,1]， 组合 2 和 1，得到 1，所以数组转化为 [1,1,1]， 组合 1 和 1，得到 0，所以数组转化为 [1]，这就是最优值。

```go
func lastStoneWeightII(stones []int) int {
	// 15001 = 30 * 1000 /2 +1
	dp := make([]int, 15001)
	// 求target
	sum := 0
	for _, v := range stones {
		sum += v
	}
	target := sum / 2
	// 遍历顺序
	for i := 0; i < len(stones); i++ {
		for j := target; j >= stones[i]; j-- {
			// 推导公式
			dp[j] = max(dp[j], dp[j-stones[i]]+stones[i])
		}
	}
	return sum - 2 * dp[target]
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```
### 目标和
给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。

返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

示例：

输入：nums: [1, 1, 1, 1, 1], S: 3
输出：5
```
解释：
-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3
```
一共有5种方法让最终目标和为3。

可回溯可dp

```go
func findTargetSumWays(nums []int, target int) int {
	sum := 0
	for _, v := range nums {
		sum += v
	}
	if target > sum {
		return 0
	}
	if (sum+target)%2 == 1 {
		return 0
	}
	// 计算背包大小
	bag := (sum + target) / 2
	// 定义dp数组
	dp := make([]int, bag+1)
	// 初始化
	dp[0] = 1
	// 遍历顺序
	for i := 0; i < len(nums); i++ {
		for j := bag; j >= nums[i]; j-- {
			//推导公式
			dp[j] += dp[j-nums[i]]
			//fmt.Println(dp)
		}
	}
	return dp[bag]
}
```
### 一和零
给你一个二进制字符串数组 strs 和两个整数 m 和 n 。

请你找出并返回 strs 的最大子集的大小，该子集中 最多 有 m 个 0 和 n 个 1 。

如果 x 的所有元素也是 y 的元素，集合 x 是集合 y 的 子集 。

示例 1：

输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3 输出：4

解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。 其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。

```go
func findMaxForm(strs []string, m int, n int) int {
	// 定义数组
	dp := make([][]int, m+1)
	for i,_ := range dp {
		dp[i] = make([]int, n+1 )
	}
	// 遍历
	for i:=0;i<len(strs);i++ {
		zeroNum,oneNum := 0 , 0
		//计算0,1 个数
		//或者直接strings.Count(strs[i],"0")
		for _,v := range strs[i] {
			if v == '0' {
				zeroNum++
			}
		}
		oneNum = len(strs[i])-zeroNum
		// 从后往前 遍历背包容量
		for j:= m ; j >= zeroNum;j-- {
			for k:=n ; k >= oneNum;k-- {
				// 推导公式
				dp[j][k] = max(dp[j][k],dp[j-zeroNum][k-oneNum]+1)
			}
		}
		//fmt.Println(dp)
	}
	return dp[m][n]
}

func max(a,b int) int {
	if a > b {
		return a
	}
	return b
}
```
### 完全背包理论基础
每件物品都有无限个（也就是可以放入背包多次）
```go


// test_CompletePack1 先遍历物品, 在遍历背包
func test_CompletePack1(weight, value []int, bagWeight int) int {
	// 定义dp数组 和初始化
	dp := make([]int, bagWeight+1)
	// 遍历顺序
	for i := 0; i < len(weight); i++ {
		// 正序会多次添加 value[i]
		for j := weight[i]; j <= bagWeight; j++ {
			// 推导公式
			dp[j] = max(dp[j], dp[j-weight[i]]+value[i])
			// debug
			//fmt.Println(dp)
		}
	}
	return dp[bagWeight]
}

// test_CompletePack2 先遍历背包, 在遍历物品
func test_CompletePack2(weight, value []int, bagWeight int) int {
	// 定义dp数组 和初始化
	dp := make([]int, bagWeight+1)
	// 遍历顺序
	// j从0 开始
	for j := 0; j <= bagWeight; j++ {
		for i := 0; i < len(weight); i++ {
			if j >= weight[i] {
				// 推导公式
				dp[j] = max(dp[j], dp[j-weight[i]]+value[i])
			}
			// debug
			//fmt.Println(dp)
		}
	}
	return dp[bagWeight]
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

func main() {
	weight := []int{1, 3, 4}
	price := []int{15, 20, 30}
	fmt.Println(test_CompletePack1(weight, price, 4))
	fmt.Println(test_CompletePack2(weight, price, 4))
}
```
### 零钱兑换II
给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。

示例 1:

输入: amount = 5, coins = [1, 2, 5] 输出: 4 解释: 有四种方式可以凑成总金额: 5=5 5=2+2+1 5=2+1+1+1 5=1+1+1+1+1

```go
func change(amount int, coins []int) int {
	// 定义dp数组
	dp := make([]int, amount+1)
	// 初始化,0大小的背包, 当然是不装任何东西了, 就是1种方法
	dp[0]  = 1
	// 遍历顺序
	// 遍历物品
	for i := 0 ;i < len(coins);i++ {
		// 遍历背包
		for j:= coins[i] ; j <= amount ;j++ {
			// 推导公式
			dp[j] += dp[j-coins[i]]
		}
	}
	return dp[amount]
}
```
### 组合总和（IV）
给定一个由正整数组成且不存在重复数字的数组，找出和为给定目标正整数的组合的个数。

示例:

nums = [1, 2, 3] target = 4

所有可能的组合为： (1, 1, 1, 1) (1, 1, 2) (1, 2, 1) (1, 3) (2, 1, 1) (2, 2) (3, 1)

请**注意**，顺序不同的序列被视作不同的组合。

因此输出为 7

```go
func combinationSum4(nums []int, target int) int {
	//定义dp数组
	dp := make([]int, target+1)
	// 初始化
	dp[0] = 1
	// 遍历顺序, 先遍历背包,再循环遍历物品
	for j:=0;j<=target;j++ {
		for i:=0 ;i < len(nums);i++ {
			if j >= nums[i] {
				dp[j] += dp[j-nums[i]]
			}
		}
	}
	return dp[target]
}
```
### 爬楼梯（进阶）
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意**：给定 n 是一个正整数。
```
示例 1： 输入： 2 输出： 2 解释： 有两种方法可以爬到楼顶。
1 阶 + 1 阶
2 阶
```
```go
func climbStairs(n int) int {
	//定义
	dp := make([]int, n+1)
	//初始化
	dp[0] = 1
	// 本题物品只有两个1,2
	m := 2
	// 遍历顺序
	for j := 1; j <= n; j++ {	//先遍历背包
		for i := 1; i <= m; i++ {	//再遍历物品
			if j >= i {
				dp[j] += dp[j-i]
			}
			//fmt.Println(dp)
		}
	}
	return dp[n]
}
```
### 零钱兑换
给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。

示例 1： 输入：coins = [1, 2, 5], amount = 11 输出：3 解释：11 = 5 + 5 + 1
```go

// 版本一, 先遍历物品,再遍历背包
func coinChange1(coins []int, amount int) int {
	dp := make([]int, amount+1)
	// 初始化dp[0]
	dp[0] = 0
	// 初始化为math.MaxInt32
	for j := 1; j <= amount; j++ {
		dp[j] = math.MaxInt32
	}

	// 遍历物品
	for i := 0; i < len(coins); i++ {
		// 遍历背包
		for j := coins[i]; j <= amount; j++ {
			if dp[j-coins[i]] != math.MaxInt32 {
				// 推导公式
				dp[j] = min(dp[j], dp[j-coins[i]]+1)
				//fmt.Println(dp,j,i)
			}
		}
	}
	// 没找到能装满背包的, 就返回-1
	if dp[amount] == math.MaxInt32 {
		return -1
	}
	return dp[amount]
}

// 版本二,先遍历背包,再遍历物品
func coinChange2(coins []int, amount int) int {
	dp := make([]int, amount+1)
	// 初始化dp[0]
	dp[0] = 0
	// 遍历背包,从1开始
	for j := 1; j <= amount; j++ {
		// 初始化为math.MaxInt32
		dp[j] = math.MaxInt32
		// 遍历物品
		for i := 0; i < len(coins); i++ {
			if j >= coins[i] && dp[j-coins[i]] != math.MaxInt32 {
				// 推导公式
				dp[j] = min(dp[j], dp[j-coins[i]]+1)
				//fmt.Println(dp)
			}
		}
	}
	// 没找到能装满背包的, 就返回-1
	if dp[amount] == math.MaxInt32 {
		return -1
	}
	return dp[amount]
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```
### 完全平方数
给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

给你一个整数 n ，返回和为 n 的完全平方数的 最少数量 。

完全平方数 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，1、4、9 和 16 都是完全平方数，而 3 和 11 不是。

示例 1： 输入：n = 12 输出：3 解释：12 = 4 + 4 + 4

```go

// 版本一,先遍历物品, 再遍历背包
func numSquares1(n int) int {
	//定义
	dp := make([]int, n+1)
	// 初始化
	dp[0] = 0
	for i := 1; i <= n; i++ {
		dp[i] = math.MaxInt32
	}
	// 遍历物品
	for i := 1; i <= n; i++ {
		// 遍历背包
		for j := i*i; j <= n; j++ {
			dp[j] = min(dp[j], dp[j-i*i]+1)
		}
	}

	return dp[n]
}

// 版本二,先遍历背包, 再遍历物品
func numSquares2(n int) int {
	//定义
	dp := make([]int, n+1)
	// 初始化
	dp[0] = 0
	// 遍历背包
	for j := 1; j <= n; j++ {
		//初始化
		dp[j] = math.MaxInt32
		// 遍历物品
		for i := 1; i <= n; i++ {
			if j >= i*i {
				dp[j] = min(dp[j], dp[j-i*i]+1)
			}
		}
	}

	return dp[n]
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```
### 单词拆分
给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

拆分时可以重复使用字典中的单词。

你可以假设字典中没有重复的单词。

示例 1： 输入: s = "leetcode", wordDict = ["leet", "code"] 输出: true 解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。

```go
func wordBreak(s string,wordDict []string) bool  {
	wordDictSet:=make(map[string]bool)
	for _,w:=range wordDict{
		wordDictSet[w]=true
	}
	dp:=make([]bool,len(s)+1)
	dp[0]=true
	for i:=1;i<=len(s);i++{
		for j:=0;j<i;j++{
			if dp[j]&& wordDictSet[s[j:i]]{
				dp[i]=true
				break
			}
		}
	}
	return dp[len(s)]
}
```
### 打家劫舍
#### I
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

示例 1： 输入：[1,2,3,1] 输出：4 解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。   偷窃到的最高金额 = 1 + 3 = 4 。

```go

func rob(nums []int) int {
	if len(nums)<1{
		return 0
	}
	if len(nums)==1{
		return nums[0]
	}
	if len(nums)==2{
		return max(nums[0],nums[1])
	}
	dp :=make([]int,len(nums))
	dp[0]=nums[0]
	dp[1]=max(nums[0],nums[1])
	for i:=2;i<len(nums);i++{
		dp[i]=max(dp[i-2]+nums[i],dp[i-1])
	}
	return dp[len(dp)-1]
}

func max(a, b int) int {
	if a>b{
		return a
	}
	return b
}
```
#### II
你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，能够偷窃到的最高金额。

示例 1：

输入：nums = [2,3,2] 输出：3 解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```go

// 打家劫舍Ⅱ 动态规划
// 时间复杂度O(n) 空间复杂度O(n)
func rob(nums []int) int {
    if len(nums) == 1 {
        return nums[0]
    }
    if len(nums) == 2 {
        return max(nums[0], nums[1])
    }
    
    result1 := robRange(nums, 0)
    result2 := robRange(nums, 1)
    return max(result1, result2)
}

// 偷盗指定的范围
func robRange(nums []int, start int) int {
    dp := make([]int, len(nums))
    dp[1] = nums[start]
    
    for i := 2; i < len(nums); i++ {
        dp[i] = max(dp[i - 2] + nums[i - 1 + start], dp[i - 1])
    }
    
    return dp[len(nums) - 1]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
### III
在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。
动态规划
```go
func rob(root *TreeNode) int {
	res := robTree(root)
	return max(res[0], res[1])
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

func robTree(cur *TreeNode) []int {
	if cur == nil {
		return []int{0, 0}
	}
	// 后序遍历
	left := robTree(cur.Left)
	right := robTree(cur.Right)

    // 考虑去偷当前的屋子
	robCur := cur.Val + left[0] + right[0]
    // 考虑不去偷当前的屋子
	notRobCur := max(left[0], left[1]) + max(right[0], right[1])

    // **注意**顺序：0:不偷，1:去偷
	return []int{notRobCur, robCur}
}
```
### 买卖股票的最佳时机
#### I
给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。
```
示例 1：
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。**注意**利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```
```go
func maxProfit(prices []int) int {
	length:=len(prices)
	if length==0{return 0}
	dp:=make([][]int,length)
	for i:=0;i<length;i++{
		dp[i]=make([]int,2)
	}
	
	dp[0][0]=-prices[0]
	dp[0][1]=0
	for i:=1;i<length;i++{
		dp[i][0]=max(dp[i-1][0],-prices[i])
		dp[i][1]=max(dp[i-1][1],dp[i-1][0]+prices[i])
	}
	return dp[length-1][1]
}

func max(a,b int)int {
    if a>b{
        return a 
    }
    return b 
}
```
#### II
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
```
示例 1:
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4。随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```
```go

// 买卖股票的最佳时机Ⅱ 动态规划
// 时间复杂度：O(n) 空间复杂度：O(n)
func maxProfit(prices []int) int {
    dp := make([][]int, len(prices))
    status := make([]int, len(prices) * 2)
    for i := range dp {
        dp[i] = status[:2]
        status = status[2:]
    }
    dp[0][0] = -prices[0]
    
    for i := 1; i < len(prices); i++ {
        dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i])
        dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i])
    }
    
    return dp[len(prices) - 1][1]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
```go
func maxProfit(prices []int) int {
    //创建数组
    dp:=make([][]int,len(prices))
    for i:=0;i<len(prices);i++{
        dp[i]=make([]int,2)
    }
    dp[0][0]=-prices[0]
    dp[0][1]=0
    for i:=1;i<len(prices);i++{
        dp[i][0]=max(dp[i-1][0],dp[i-1][1]-prices[i])
        dp[i][1]=max(dp[i-1][1],dp[i-1][0]+prices[i])
    }
    return dp[len(prices)-1][1]
}
func max(a,b int)int{
    if a<b{
        return b
    }
    return a
}
```
#### III
给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

示例 1: 输入：prices = [3,3,5,0,0,3,1,4] 输出：6 解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3。

```go
func maxProfit(prices []int) int {
    dp:=make([][]int,len(prices))
    for i:=0;i<len(prices);i++{
        dp[i]=make([]int,5)
    }
    dp[0][0]=0
    dp[0][1]=-prices[0]
    dp[0][2]=0
    dp[0][3]=-prices[0]
    dp[0][4]=0
    for i:=1;i<len(prices);i++{
        dp[i][0]=dp[i-1][0]
        dp[i][1]=max(dp[i-1][1],dp[i-1][0]-prices[i])
        dp[i][2]=max(dp[i-1][2],dp[i-1][1]+prices[i])
        dp[i][3]=max(dp[i-1][3],dp[i-1][2]-prices[i])
        dp[i][4]=max(dp[i-1][4],dp[i-1][3]+prices[i])
    }
    return dp[len(prices)-1][4]
}
func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```
#### IV
给定一个整数数组 prices ，它的第 i 个元素 prices[i] 是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。

**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

示例 1： 输入：k = 2, prices = [2,4,1] 输出：2 解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2。
版本一：
```go
// 买卖股票的最佳时机IV 动态规划
// 时间复杂度O(kn) 空间复杂度O(kn)
func maxProfit(k int, prices []int) int {
    if k == 0 || len(prices) == 0 {
        return 0
    }
    
    dp := make([][]int, len(prices))
    status := make([]int, (2 * k + 1) * len(prices))
    for i := range dp {
        dp[i] = status[:2 * k + 1]
        status = status[2 * k + 1:]
    }
    for j := 1; j < 2 * k; j += 2 {
        dp[0][j] = -prices[0]
    }
    
    for i := 1; i < len(prices); i++ {
        for j := 0; j < 2 * k; j += 2 {
            dp[i][j + 1] = max(dp[i - 1][j + 1], dp[i - 1][j] - prices[i])
            dp[i][j + 2] = max(dp[i - 1][j + 2], dp[i - 1][j + 1] + prices[i])
        }
    }
    return dp[len(prices) - 1][2 * k]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
```go
func maxProfit(k int, prices []int) int {
    if len(prices)==0{
        return 0
    }
    dp:=make([][]int,len(prices))
    for i:=0;i<len(prices);i++{
        dp[i]=make([]int,2*k+1)
    }
    for i:=1;i<len(dp[0]);i++{
        if i%2!=0{
            dp[0][i]=-prices[0]
        }
    }
    for i:=1;i<len(prices);i++{
        dp[i][0]=dp[i-1][0]
        for j:=1;j<len(dp[0]);j++{
            if j%2!=0{
                 dp[i][j]=max(dp[i-1][j],dp[i-1][j-1]-prices[i])
            }else {
                dp[i][j]=max(dp[i-1][j],dp[i-1][j-1]+prices[i])
            }
        }
    }
    return dp[len(prices)-1][2*k]
}
func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```
#### 最佳买卖股票时机含冷冻期
给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:
- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

示例: 输入: [1,2,3,0,2] 输出: 3 解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]

```go
// 最佳买卖股票时机含冷冻期 动态规划
// 时间复杂度O(n) 空间复杂度O(n)
func maxProfit(prices []int) int {
    n := len(prices)
    if n < 2 {
        return 0
    }

    dp := make([][]int, n)
    status := make([]int, n * 4)
    for i := range dp {
        dp[i] = status[:4]
        status = status[4:]
    }
    dp[0][0] = -prices[0]
    
    for i := 1; i < n; i++ {
        dp[i][0] = max(dp[i - 1][0], max(dp[i - 1][1] - prices[i], dp[i - 1][3] - prices[i]))
        dp[i][1] = max(dp[i - 1][1], dp[i - 1][3])
        dp[i][2] = dp[i - 1][0] + prices[i]
        dp[i][3] = dp[i - 1][2]
    }
    
    return max(dp[n - 1][1], max(dp[n - 1][2], dp[n - 1][3]))
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
#### 最佳买卖股票时机含手续费
给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

**注意**：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

示例 1: 输入: prices = [1, 3, 2, 8, 4, 9], fee = 2 输出: 8

解释: 能够达到的最大利润: 在此处买入 prices[0] = 1 在此处卖出 prices[3] = 8 在此处买入 prices[4] = 4 在此处卖出 prices[5] = 9 总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

```go
// 买卖股票的最佳时机含手续费 动态规划
// 时间复杂度O(n) 空间复杂度O(n)
func maxProfit(prices []int, fee int) int {
    n := len(prices)
    dp := make([][2]int, n)
    dp[0][0] = -prices[0]
    for i := 1; i < n; i++ {
        dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i] - fee)
        dp[i][0] = max(dp[i-1][0], dp[i-1][1] - prices[i])
    }
    return dp[n-1][1]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
### 股票问题总结
[代码随想录](https://programmercarl.com/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92-%E8%82%A1%E7%A5%A8%E9%97%AE%E9%A2%98%E6%80%BB%E7%BB%93%E7%AF%87.html#%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BA)
### 最长上升子序列
给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

示例 1： 输入：nums = [10,9,2,5,3,7,101,18] 输出：4 解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。

```go
func lengthOfLIS(nums []int ) int {
  dp := []int{}
  for _, num := range nums {
      if len(dp) ==0 || dp[len(dp) - 1] < num {
      dp = append(dp, num)
    } else {
          l, r := 0, len(dp) - 1
          pos := r
          for l <= r {
              mid := (l + r) >> 1
              if dp[mid] >= num {
                  pos = mid;
                  r = mid - 1
              } else {
                  l = mid + 1
              }
          }
          dp[pos] = num
      }//二分查找
  }
    return len(dp)
}
```
### 最长连续递增序列
给定一个未经排序的整数数组，找到最长且 连续递增的子序列，并返回该序列的长度。

连续递增的子序列 可以由两个下标 l 和 r（l < r）确定，如果对于每个 l <= i < r，都有 nums[i] < nums[i + 1] ，那么子序列 [nums[l], nums[l + 1], ..., nums[r - 1], nums[r]] 就是连续递增子序列。

示例 1： 输入：nums = [1,3,5,4,7] 输出：3 解释：最长连续递增序列是 [1,3,5], 长度为3。 尽管 [1,3,5,7] 也是升序的子序列, 但它不是连续的，因为 5 和 7 在原数组里被 4 隔开。

动态规划：
```
py
class Solution:
    def findLengthOfLCIS(self, nums: List[int]) -> int:
        if len(nums) == 0:
            return 0
        result = 1
        dp = [1] * len(nums)
        for i in range(len(nums)-1):
            if nums[i+1] > nums[i]: #连续记录
                dp[i+1] = dp[i] + 1
            result = max(result, dp[i+1])
        return result
```
贪心法：
```
py
class Solution:
    def findLengthOfLCIS(self, nums: List[int]) -> int:
        if len(nums) == 0:
            return 0
        result = 1 #连续子序列最少也是1
        count = 1
        for i in range(len(nums)-1):
            if nums[i+1] > nums[i]: #连续记录
                count += 1
            else: #不连续，count从头开始
                count = 1
            result = max(result, count)
        return result
```
### 最长重复子数组
给两个整数数组 A 和 B ，返回两个数组中公共的、长度最长的子数组的长度。

示例：

输入： A: [1,2,3,2,1] B: [3,2,1,4,7] 输出：3 解释： 长度最长的公共子数组是 [3, 2, 1] 。

```go
func findLength(A []int, B []int) int {
	m, n := len(A), len(B)
	res := 0
	dp := make([][]int, m+1)
	for i := 0; i <= m; i++ { 
		dp[i] = make([]int, n+1)
	}

	for i := 1; i <= m; i++ {
		for j := 1; j <= n; j++ {
			if A[i-1] == B[j-1] {
				dp[i][j] = dp[i-1][j-1] + 1
			}
			if dp[i][j] > res {
				res = dp[i][j]
			}
		}
	}
	return res
}
```
### 最长公共子序列
给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列的长度。

一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

若这两个字符串没有公共子序列，则返回 0。

示例 1:
```
输入：text1 = "abcde", text2 = "ace"
输出：3
解释：最长公共子序列是 "ace"，它的长度为 3。
```
```go
func longestCommonSubsequence(text1 string, text2 string) int {
	t1 := len(text1)
	t2 := len(text2)
	dp:=make([][]int,t1+1)
	for i:=range dp{
		dp[i]=make([]int,t2+1)
	}

	for i := 1; i <= t1; i++ {
		for j := 1; j <=t2; j++ {
			if text1[i-1]==text2[j-1]{
				dp[i][j]=dp[i-1][j-1]+1
			}else{
				dp[i][j]=max(dp[i-1][j],dp[i][j-1])
			}
		}
	}
	return dp[t1][t2]
}

func max(a,b int)int  {
	if a>b{
		return a 
	}
	return b
}
```
### 不相交的线
我们在两条独立的水平线上按给定的顺序写下 A 和 B 中的整数。

现在，我们可以绘制一些连接两个数字 A[i] 和 B[j] 的直线，只要 A[i] == B[j]，且我们绘制的直线不与任何其他连线（非水平线）相交。

以这种方法绘制线条，并返回我们可以绘制的最大连线数。
```go
func maxUncrossedLines(A []int, B []int) int {
	m, n := len(A), len(B)
	dp := make([][]int, m+1)
    for i := range dp {
        dp[i] = make([]int, n+1)
    }

	for i := 1; i <= len(A); i++ {
		for j := 1; j <= len(B); j++ {
			if (A[i - 1] == B[j - 1]) {
				dp[i][j] = dp[i - 1][j - 1] + 1
			} else {
				dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
			}
		}
	}
	return dp[m][n]

}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
### 最大子序和
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例: 输入: [-2,1,-3,4,-1,2,1,-5,4] 输出: 6 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6

```go
// solution
// 1, dp
// 2, 贪心

func maxSubArray(nums []int) int {
    n := len(nums)
    // 这里的dp[i] 表示，最大的连续子数组和，包含num[i] 元素
    dp := make([]int,n)
    // 初始化，由于dp 状态转移方程依赖dp[0]
    dp[0] = nums[0]
    // 初始化最大的和
    mx := nums[0]
    for i:=1;i<n;i++ {
        // 这里的状态转移方程就是：求最大和
        // 会面临2种情况，一个是带前面的和，一个是不带前面的和
        dp[i] = max(dp[i-1]+nums[i],nums[i])
        mx = max(mx,dp[i])
    }
    return mx
}

func max(a,b int) int{
    if a>b {
        return a 
    }
    return b
}
```
### 判断子序列
给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

示例 1： 输入：s = "abc", t = "ahbgdc" 输出：true

示例 2： 输入：s = "axc", t = "ahbgdc" 输出：false

```go
func isSubsequence(s string, t string) bool {
    dp := make([][]int,len(s)+1)
    for i:=0;i<len(dp);i++{
        dp[i] = make([]int,len(t)+1)
    }
    for i:=1;i<len(dp);i++{
        for j:=1;j<len(dp[i]);j++{
            if s[i-1] == t[j-1]{
                dp[i][j] = dp[i-1][j-1] +1
            }else{
                dp[i][j] = dp[i][j-1]
            }
        }
    }
    return dp[len(s)][len(t)]==len(s)
}
```
### 不同的子序列
给定一个字符串 s 和一个字符串 t ，计算在 s 的子序列中 t 出现的个数。

字符串的一个 子序列 是指，通过删除一些（也可以不删除）字符且不干扰剩余字符相对位置所组成的新字符串。（例如，"ACE" 是 "ABCDE" 的一个子序列，而 "AEC" 不是）

题目数据保证答案符合 32 位带符号整数范围。

```go
func numDistinct(s string, t string) int {
    dp:= make([][]int,len(s)+1)
    for i:=0;i<len(dp);i++{
        dp[i] = make([]int,len(t)+1)
    }
    // 初始化
    for i:=0;i<len(dp);i++{
        dp[i][0] = 1
    }
    // dp[0][j] 为 0，默认值，因此不需要初始化
    for i:=1;i<len(dp);i++{
        for j:=1;j<len(dp[i]);j++{
            if s[i-1] == t[j-1]{
                dp[i][j] = dp[i-1][j-1] + dp[i-1][j]
            }else{
                dp[i][j] = dp[i-1][j]
            }
        }
    }
    return dp[len(dp)-1][len(dp[0])-1]
}
```
### 两个字符串的删除操作
给定两个单词 word1 和 word2，找到使得 word1 和 word2 相同所需的最小步数，每步可以删除任意一个字符串中的一个字符。

示例：
```
输入: "sea", "eat"
输出: 2 解释: 第一步将"sea"变为"ea"，第二步将"eat"变为"ea"
```
### 编辑距离
给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：
- 插入一个字符
- 删除一个字符
- 替换一个字符

示例 1： 输入：word1 = "horse", word2 = "ros" 输出：3 解释： horse -> rorse (将 'h' 替换为 'r') rorse -> rose (删除 'r') rose -> ros (删除 'e')
```go

func minDistance(word1 string, word2 string) int {
	m, n := len(word1), len(word2)
	dp := make([][]int, m+1)
	for i := range dp {
		dp[i] = make([]int, n+1)
	}
	for i := 0; i < m+1; i++ {
		dp[i][0] = i // word1[i] 变成 word2[0], 删掉 word1[i], 需要 i 部操作
	}
	for j := 0; j < n+1; j++ {
		dp[0][j] = j // word1[0] 变成 word2[j], 插入 word1[j]，需要 j 部操作
	}
	for i := 1; i < m+1; i++ {
		for j := 1; j < n+1; j++ {
			if word1[i-1] == word2[j-1] {
				dp[i][j] = dp[i-1][j-1]
			} else { // Min(插入，删除，替换)
				dp[i][j] = Min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1]) + 1
			}
		}
	}
	return dp[m][n]
}
func Min(args ...int) int {
	min := args[0]
	for _, item := range args {
		if item < min {
			min = item
		}
	}
	return min
}
```
### 编辑距离总结
[代码随想录](https://programmercarl.com/%E4%B8%BA%E4%BA%86%E7%BB%9D%E6%9D%80%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB%EF%BC%8C%E5%8D%A1%E5%B0%94%E5%81%9A%E4%BA%86%E4%B8%89%E6%AD%A5%E9%93%BA%E5%9E%AB.html#%E5%88%A4%E6%96%AD%E5%AD%90%E5%BA%8F%E5%88%97)
### 回文子串
给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

示例 1：

输入："abc" 输出：3 解释：三个回文子串: "a", "b", "c"

```go
func countSubstrings(s string) int {
    res:=0
    dp:=make([][]bool,len(s))
    for i:=0;i<len(s);i++{
        dp[i]=make([]bool,len(s))
    }

    for i:=len(s)-1;i>=0;i--{
        for j:=i;j<len(s);j++{
            if s[i]==s[j]{
                if j-i<=1{
                    res++
                    dp[i][j]=true
                }else if dp[i+1][j-1]{
                    res++
                    dp[i][j]=true
                }
            }
        }
    }
    return res
}
```
### 最长回文子序列
给定一个字符串 s ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 s 的最大长度为 1000 。

示例 1: 输入: "bbbab" 输出: 4 一个可能的最长回文子序列为 "bbbb"。

示例 2: 输入:"cbbd" 输出: 2 一个可能的最长回文子序列为 "bb"。

```go
func longestPalindromeSubseq(s string) int {
	lenth:=len(s)
	dp:=make([][]int,lenth)
	for i:=0;i<lenth;i++{
		for j:=0;j<lenth;j++{
			if dp[i]==nil{
				dp[i]=make([]int,lenth)
			}
			if i==j{
				dp[i][j]=1
			}
		}
	}
	for i:=lenth-1;i>=0;i--{
		for j:=i+1;j<lenth;j++{
			if s[i]==s[j]{
				dp[i][j]=dp[i+1][j-1]+2
			}else {
				dp[i][j]=max(dp[i+1][j],dp[i][j-1])
			}
		}
	}

	return dp[0][lenth-1]
}
```
### DP总结
[代码随想录](https://programmercarl.com/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E6%80%BB%E7%BB%93%E7%AF%87.html#%E5%8A%A8%E5%88%92%E5%9F%BA%E7%A1%80)

### 开篇词｜为什么大厂都爱考动态规划？

你好，我是卢誉声，很高兴能在这个专栏与你见面，和你一起搞定动态规划。开门见山，我先做一个自我介绍。最开始，我在思科系统（Cisco Systems）工作，曾参与设计和开发了下一代视频会议系统的核心数据交换服务。我的工作涵盖了协议栈开发、微服务设计、分布式系统编配以及弹性算法设计。这段经历让我形成了一个认知：算法对设计关键服务来说十分重要，它决定了系统的稳定性、弹性以及可扩展性。后来，我加入了 Autodesk，成为了一款三维设计旗舰软件的框架和平台软件工程师。负责开发了基于大规模结构化数据的高性能搜索引擎，首次将灵活的多线程和异步框架带入产品框架层面，在原有的底层内存模型上采用了改进后的检索引擎，相较于原有的搜索功能，实现了超过 300 倍的性能提升。除此之外，我还改进并维护了用于改进用户体验的数据处理系统，在平台框架层面的工作，让我积累了大量的工程实践经验。现在，我在  Autodesk 数据平台就职，负责设计和开发大规模数据的分析、丰富化以及流化分布式服务。我发现自己的职业发展一直围绕着数据在不断前进。基于此，我常说的一句话是：“数据即是正义”。那直到今天，我的态度依然没有变。数据为媒，算法为介，而在极其重要的算法中，动态规划其实占了很大的比重。

事实上，如果你平常关注大厂面试的话，你会发现，但凡是研发岗位，无论是招聘初级还是高级工程师，大厂都倾向于安排一轮或多轮专门的算法面试环节，而且在面试环节提出动态规划相关问题的这种趋势已经愈发明显。这是为什么呢？我来谈谈我的看法。

先说算法这件事吧。我想请你回想一下，当处理数据结构相关的问题时，你有没有这样的经历？你本能地到工具函数或者库函数中寻找有没有现成的工具。如果问题得到快速解决，它是不是迅速就成了过眼云烟？如果这个问题看起来比较棘手，它不是一个典型的算法问题，那么就寻求搜索引擎的帮助，或者干脆访问 Stack Overflow 这样的“智库”寻找前人留下的解决方案？虽然平时工作中表现优异，但当你想换工作参加大厂面试时，又发现自己难以解决面试官提出的算法问题，无从下手，面对白板“望洋兴叹”？

相信我，你不是一个人！这种现象很普遍。其实，对于开发人员来说，算法和数据结构就是我们的基本功。我们常常自嘲软件研发人员的工作就是复制粘贴，搬砖就是日常工作的全部。但当公司或部门要求你去研究一个全新的技术，或者快速阅读一份开发多年且成熟的开源项目代码，并对其改造来服务于自己的产品功能时，你的压力会让你明白基本功到底有多重要！关于基本功这事儿，我要插个故事进来，再多说几句。我曾有幸与 C++ 之父 Bjarne Stroustrup 先生进行过面对面的交流。我问了他一个问题：“如今新生代技术人员倾向于学习 Java、Go 或 Python 这些更容易上手的编程语言，您是如何看待这个现象的？”Stroustrup 先生的回答大概是这样的：“如果一个人只了解一种编程语言，那么他不能称自己是专业人士，而从我的角度上看，将 C++ 作为基础，能让你深入洞察各种各样编程语言背后的思想和设计思路。”


我作为面试官曾接触过许多优秀的候选人，他们有着各种各样的背景，既有潜力又非常努力，但在面对算法问题和解决问题时没有太多思路，始终无法更上一层楼，十分遗憾。而动态规划恰恰是解决问题的重要方法论，面对很多数据处理的应用场景，它在降低时间复杂度上极具优势，因此成为了考察重点。不仅如此，动态规划问题还能很好地考察面试者的数学模型抽象能力和逻辑思维能力，可以反应个人在算法上的综合能力。所以我觉得，大厂之所以如此看中一个面试者的算法基础，特别是动态规划问题的解决能力，是因为他们更加看中一位面试者解决问题的思路与逻辑思维能力，而不只是工具与技能的熟练程度。

不同于普通算法，如排序或递归，动态规划从名字上看就显得很特别，有些“高端、大气、上档次”的味道在里面。但其实它离我们很近。**我举个例子你就明白了，在云计算平台上一个解决方案的计算能力（容量）肯定是有限的，那么为了高效服务那些重要程度或优先级最高的客户，同时又不想浪费计算资源（说白了为了省钱），我们该怎么办？这个问题其实可以通过队列这样的分发方式来进行一个简单的编配。但是这不够好，如果我们能够事先知道一个计算任务的重要程度和所需的计算时长，就可以通过动态规划算法来进行预演算，从数学角度推导出一个严谨的编排结果，实现有限资源的最大化利用。**

你看，似乎遥不可及的动态规划问题，其实就是求最优解问题，它无时无刻都在我们身边，总是戏剧般地提高了最优化问题的性能！这再一次凸显出大厂为何青睐于动态规划问题，而且成为了区别面试者的一个隐形门槛。甚至可以说，掌握动态规划思想，在工作面试、技术等级晋升上都扮演了核心角色。总之一句话，动归必学。

模块一：初识动态规划我会为你讲解复杂面试题的思考和解决方式。从贪心算法开始，一步步阐述动态规划的由来，并通过一个贯穿全篇的例子来展现动态规划的强大之处。学习和掌握这些经典的处理方法，能够为你后续掌握动态规划打下一个坚实基础。通过这部分内容，你会系统了解到动态规划问题的特点和解题经验。模块二：动态规划的套路我会为你讲解动态规划问题的解题框架和套路，你可以把这个套路理解成是解决动归问题的模板。在此模板的基础上，我会向你讲解面试真题，有针对性地套用解题框架。而应对面试题的纷繁复杂，我会为你进行有效的分类，并针对每一种动态规划问题进行深入而全面的讲解。通过这部分内容，你会快速掌握常见面试题的解题套路。模块三：举一反三，突破套路我会针对几种特别易考的动态规划面试题进行总结，帮助你攻破套路。并在这些高级话题的基础上，提出设计动态规划算法的关键问题。另外，还有刷题指南，所谓孰能生巧，必要的练习我们还是要的。通过这部分内容，你会快速掌握动态规划面试题的进阶法门。


### 什么样的问题应该使用动态规划？

![img](https://static001.geekbang.org/resource/image/56/0f/5605585d75b8ef128285bea9a7c86d0f.jpg?wh=1796*1120)


动态规划问题的典型特点：求“最”优解问题（最大值和最小值）、求可行性（True 或 False）、求方案总数、数据结构不可排序（Unsortable）、算法不可使用交换（Non-swappable）。


数据不可排序（Unsortable）
假设我们有一个无序数列，希望求出这个数列中最大的两个数字之和。很多初学者刚刚学完动态规划会走火入魔到看到最优化问题就想用动态规划来求解，嗯，那么这样应该也是可以的吧……不，等等，这个问题不是简单做一个排序或者做一个遍历就可以求解出来了吗？


数据不可交换（Non-swapable）
还有一类问题，可以归类到我们总结的几类问题里去，但是不存在动态规划要求的重叠子问题（比如经典的八皇后问题），那么这类问题就无法通过动态规划求解。这种情况需要避免被套进去。

### 常见的动态规划面试题串烧

简单的路径规划、带障碍的路径规划、跳跃游戏（题目：给出一个非负整数数组 A，你最初定位在数组的第一个位置。数组中的每个元素代表你在那个位置可以跳跃的最大长度。判断你是否能到达数组的最后一个位置。）








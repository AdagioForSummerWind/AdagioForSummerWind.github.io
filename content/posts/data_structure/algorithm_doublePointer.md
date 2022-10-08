---
title: "Algorithm_doublePointer"
date: 2022-01-06T08:19:19+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: double pointer method
draft: true
---
> 学习[代码随想录](https://programmercarl.com/)笔记
# 双指针法
双指针法（快慢指针法）在数组和链表的操作中是非常常见的，很多考察数组、链表、字符串等操作的面试题，都使用双指针法。
## 移除元素

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
## 反转字符串
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
## 翻转链表
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
## 删除链表的倒数第N个节点
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
## 链表相交
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
## 环形链表II
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
## 三数之和
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
## 四数之和
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
## 双指针总结
[总结](https://programmercarl.com/%E5%8F%8C%E6%8C%87%E9%92%88%E6%80%BB%E7%BB%93.html)
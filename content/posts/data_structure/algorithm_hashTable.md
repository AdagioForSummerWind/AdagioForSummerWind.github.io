---
title: "Algorithm_hashTable"
date: 2022-01-06T08:18:30+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: hashTable
draft: true
---
> 学习[代码随想录](https://programmercarl.com/)笔记
# 哈希表
## 哈希表理论基础
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

## 有效的字母异位词
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
## 两个数组的交集
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
## 快乐数
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
## 两数之和
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
## 四数相加II
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
## 赎金信
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
## 三数之和
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

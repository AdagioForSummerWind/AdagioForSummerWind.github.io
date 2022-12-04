---
title: "Algorithm_backTracking"
date: 2022-01-06T08:22:27+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: back tracking algorithm 
draft: false
---
> 学习[代码随想录](https://programmercarl.com/)笔记
# 回溯算法
## 理论基础
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
## *组合问题及其优化
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
## 组合总和III
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
## 电话号码的字母组合
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
## 组合总和
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
## 组合总和II
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
## 分割回文串
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
## 复原IP地址
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
## 子集问题
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
## 子集II
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
## 递增子序列
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
## 全排列
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
## 全排列II
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
## 回溯算法去重问题的另一种写法
https://programmercarl.com/%E5%9B%9E%E6%BA%AF%E7%AE%97%E6%B3%95%E5%8E%BB%E9%87%8D%E9%97%AE%E9%A2%98%E7%9A%84%E5%8F%A6%E4%B8%80%E7%A7%8D%E5%86%99%E6%B3%95.html#_90-%E5%AD%90%E9%9B%86ii

看c++版的
## 重新安排行程
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
## N皇后
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
## 解数独
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

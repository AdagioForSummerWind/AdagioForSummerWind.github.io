---
title: "Algorithm_greedy"
date: 2022-01-06T08:23:51+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: data structure greedy
draft: false
---
> 学习[代码随想录](https://programmercarl.com/)笔记
# 贪心算法
## 理论基础
贪心算法一般分为如下四步：
- 将问题分解为若干个子问题
- 找出适合的贪心策略
- 求解每一个子问题的最优解
- 将局部最优解堆叠成全局最优解
## 分发饼干
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
## 摆动序列
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
## 最大子序和
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
## 买卖股票的最佳时机II
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
## 跳跃游戏
### I
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
### II
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
## K次取反后最大化的数组和
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
## 加油站
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
## 分发糖果
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
## 柠檬水找零
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
## 根据身高重建队列
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
## 用最少数量的箭引爆气球
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
## 无重叠区间
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
## 划分字母区间
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
## 合并区间
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
## 单调递增的数字
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
## 买卖股票的最佳时机含手续费
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
## 监控二叉树
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

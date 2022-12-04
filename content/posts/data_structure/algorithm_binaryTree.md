---
title: "Algorithm_binaryTree"
date: 2022-01-06T08:21:45+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: binary tree
draft: false
---
> 学习[代码随想录](https://programmercarl.com/)笔记
# 二叉树
## 理论基础
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
## 递归遍历
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
## 迭代遍历
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
## 统一迭代
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
## 层序遍历
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
## 翻转二叉树
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
## 对称二叉树
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
## 最大深度
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
## 最小深度
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
## 完全二叉树的节点个数
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
## 平衡二叉树
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
## 二叉树的所有路径

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
## 二叉树的递归+回溯
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
## 左叶子之和
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
## 找树左下角的值
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
## 路径总和
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
## 从中序、后序遍历序列构造二叉树
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
## 最大二叉树
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
## 合并二叉树
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
## 二叉搜索树
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

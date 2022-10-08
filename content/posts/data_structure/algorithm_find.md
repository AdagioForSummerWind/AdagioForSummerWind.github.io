---
title: "Algorithm_find"
date: 2022-02-20T19:52:33+08:00
lastmod: 2022-02-20
tags: [data structure]
categories: [Coding]
slug: algo_find
draft: false
---
# 查找算法
散列查找：也称哈希查找，有拉链法查找，也有线性探测法查找，拉链法使用数组链表结构，线性探测法使用数组。

树查找：有搜索二叉树，平衡查找树如：红黑树，B树，AVL树，B+等，使用链表树结构
## 哈希表：散列查找
线性查找：在链表上线性查找键更新值。

散列查找：
- 空间换时间的查找算法
- 依赖数据结构HashTable（Hash: 指压缩映射，它将一个比较大的域空间映射到一个比较小的域空间）

关于线性探测法与拉链法在go语法里map实现原理时有讲解。（go_base_01）

哈希算法非常多，随机分布性不同，当然，让得到的哈希值越均匀随机分布越好，拉链法形成的链表不会很长。

最好时间复杂度能达到： O(1) ，最坏情况下退化到查找链表： O(n) 。均匀性很好的哈希算法以及合适空间大小的数组，在很大概率避免了最坏情况。
## 二叉查找树
又称为二叉搜索树，二叉排序树。

二叉查找树不保证是一个平衡的二叉树，最坏情况下二叉查找树会退化成一个链表。

通用形式的二叉查找树实现甚少使用，大部分程序都使用了AVL树或红黑树。

二叉查找树中序遍历即可实现排序。

查找，添加，删除元素的时间复杂度取决于树的高度。查找，添加和删除的时间复杂度范围为 log(n)~n 。

AVL树和红黑树都是相对平衡的二叉查找树，因为特殊的**旋转平衡操作**，树的高度被大大压低。它们查找效率较高，添加，删除，查找操作的平均时间复杂度都为 log(n) 。

## AVL树（平衡二叉搜索树）
Adelson-Velsky and Landis。

添加和删除元素时的调整操作比较关键且重要。

```go
// AVL树
type AVLTree struct {
    Root *AVLTreeNode // 树根节点
}
// AVL节点
type AVLTreeNode struct {
    Value  int64                 // 值
    Times  int64                 // 值出现的次数
    Height int64                 // 该节点作为树根节点，树的高度，方便计算平衡因子
    Left   *AVLTreeNode // 左子树
    Right  *AVLTreeNode // 右字树
}
// 初始化一个AVL树
func NewAVLTree() *AVLTree {
    return new(AVLTree)
}
```
**AVL树添加元素**
- 插入节点后，需要满足所有节点的平衡因子在 [-1，0，1] 范围内，如果不在，需要进行旋转调整。旋转有四种情况：
    - 在右子树上插上右儿子导致失衡，左旋，转一次。
    - 在左子树上插上左儿子导致失衡，右旋，转一次。
    - 在左子树上插上右儿子导致失衡，先左后右旋，转两次。
    - 在右子树上插上左儿子导致失衡，先右后左旋，转两次。

右图来自维基百科：![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220221220504.png)
```go
// 单右旋操作，看图说话
func RightRotation(Root *AVLTreeNode) *AVLTreeNode {
    // 只有Pivot和B，Root位置变了
    Pivot := Root.Left
    B := Pivot.Right
    Pivot.Right = Root
    Root.Left = B
    // 只有Root和Pivot变化了高度
    Root.UpdateHeight()
    Pivot.UpdateHeight()
    return Pivot
}
```
```go
// 单左旋操作，看图说话
func LeftRotation(Root *AVLTreeNode) *AVLTreeNode {
    // 只有Pivot和B，Root位置变了
    Pivot := Root.Right
    B := Pivot.Left
    Pivot.Left = Root
    Root.Right = B
    // 只有Root和Pivot变化了高度
    Root.UpdateHeight()
    Pivot.UpdateHeight()
    return Pivot
}
```
复用上面代码
```go
// 先左后右旋操作，看图说话
func LeftRightRotation(node *AVLTreeNode) *AVLTreeNode {
    node.Left = LeftRotation(node.Left)
    return RightRotation(node)
}
```
```go
// 先右后左旋操作，看图说话
func RightLeftRotation(node *AVLTreeNode) *AVLTreeNode {
    node.Right = RightRotation(node.Right)
    return LeftRotation(node)
}
```
完成旋转操作，可以添加元素了~
```go
// 添加元素
func (tree *AVLTree) Add(value int64) {
    // 往树根添加元素，会返回新的树根
    tree.Root = tree.Root.Add(value)
}
func (node *AVLTreeNode) Add(value int64) *AVLTreeNode {
    // 添加值到根节点node，如果node为空，那么让值成为新的根节点，树的高度为1
    if node == nil {
        return &AVLTreeNode{Value: value, Height: 1}
    }
    // 如果值重复，什么都不用做，直接更新次数
    if node.Value == value {
        node.Times = node.Times + 1
        return node
    }
    // 辅助变量
    var newTreeNode *AVLTreeNode
    if value > node.Value {
        // 插入的值大于节点值，要从右子树继续插入
        node.Right = node.Right.Add(value)
        // 平衡因子，插入右子树后，要确保树根左子树的高度不能比右子树低一层。
        factor := node.BalanceFactor()
        // 右子树的高度变高了，导致左子树-右子树的高度从-1变成了-2。
        if factor == -2 {
            if value > node.Right.Value {
                // 表示在右子树上插上右儿子导致失衡，需要单左旋：
                newTreeNode = LeftRotation(node)
            } else {
                //表示在右子树上插上左儿子导致失衡，先右后左旋：
                newTreeNode = RightLeftRotation(node)
            }
        }
    } else {
        // 插入的值小于节点值，要从左子树继续插入
        node.Left = node.Left.Add(value)
        // 平衡因子，插入左子树后，要确保树根左子树的高度不能比右子树高一层。
        factor := node.BalanceFactor()
        // 左子树的高度变高了，导致左子树-右子树的高度从1变成了2。
        if factor == 2 {
            if value < node.Left.Value {
                // 表示在左子树上插上左儿子导致失衡，需要单右旋：
                newTreeNode = RightRotation(node)
            } else {
                //表示在左子树上插上右儿子导致失衡，先左后右旋：
                newTreeNode = LeftRightRotation(node)
            }
        }
    }
    if newTreeNode == nil {
        // 表示什么旋转都没有，根节点没变，直接刷新树高度
        node.UpdateHeight()
        return node
    } else {
        // 旋转了，树根节点变了，需要刷新新的树根高度
        newTreeNode.UpdateHeight()
        return newTreeNode
    }
}
```
由于树的高度最高为 1.44log(n)，查找元素插入位置，最坏次数为 1.44log(n) 次。逐层更新子树高度并判断平衡是否被破坏，最坏需要 1.44log(n) 次，因此可以得知添加元素最坏时间复杂度为：2.88log(n)

当插入节点后，某子树不平衡时最多旋转 2次，也就是双旋该子树即可恢复平衡，该调整为局部特征，调整完后其父层不再需要旋转。也就是说，插入操作最坏旋转两次即可

AVL树查找元素与普通二叉查找树一致。

**删除元素**有四种情况：
- 删除的节点是叶子节点，没有儿子，直接删除后看离它最近的父亲节点是否失衡，做调整操作。
- 删除的节点下有两个子树，选择高度更高的子树下的节点来替换被删除的节点，如果左子树更高，选择左子树中最大的节点，也就是左子树最右边的叶子节点，如果右子树更高，选择右子树中最小的节点，也就是右子树最左边的叶子节点。最后，删除这个叶子节点，也就是变成情况1。
- 删除的节点只有左子树，可以知道左子树其实就只有一个节点，被删除节点本身（假设左子树多于2个节点，那么高度差就等于2了，不符合AVL树定义），将左节点替换被删除的节点，最后删除这个左节点，变成情况1。
- 删除的节点只有右子树，可以知道右子树其实就只有一个节点，被删除节点本身（假设右子树多于2个节点，那么高度差就等于2了，不符合AVL树定义），将右节点替换被删除的节点，最后删除这个右节点，变成情况1。

后面三种情况最后都变成情况1，就是将删除的节点变成叶子节点，然后可以直接删除该叶子节点，然后看其最近的父亲节点是否失衡，失衡时对树进行平衡。

实现代码：
```go
func (node *AVLTreeNode) Delete(value int64) *AVLTreeNode {
    if node == nil {
        // 如果是空树，直接返回
        return nil
    }
    if value < node.Value {
        // 从左子树开始删除
        node.Left = node.Left.Delete(value)
        // 删除后要更新该子树高度
        node.Left.UpdateHeight()
    } else if value > node.Value {
        // 从右子树开始删除
        node.Right = node.Right.Delete(value)
        // 删除后要更新该子树高度
        node.Right.UpdateHeight()
    } else {
        // 找到该值对应的节点
        // 该节点没有左右子树
        // 第一种情况，删除的节点没有儿子，直接删除即可。
        if node.Left == nil && node.Right == nil {
            return nil // 直接返回nil，表示直接该值删除
        }
        // 该节点有两棵子树，选择更高的哪个来替换
        // 第二种情况，删除的节点下有两个子树，选择高度更高的子树下的节点来替换被删除的节点，如果左子树更高，选择左子树中最大的节点，也就是左子树最右边的叶子节点，如果右子树更高，选择右子树中最小的节点，也就是右子树最左边的叶子节点。最后，删除这个叶子节点。
        if node.Left != nil && node.Right != nil {
            // 左子树更高，拿左子树中最大值的节点替换
            if node.Left.Height > node.Right.Height {
                maxNode := node.Left
                for maxNode.Right != nil {
                    maxNode = maxNode.Right
                }
                // 最大值的节点替换被删除节点
                node.Value = maxNode.Value
                node.Times = maxNode.Times
                // 把最大的节点删掉
                node.Left = node.Left.Delete(maxNode.Value)
                // 删除后要更新该子树高度
                node.Left.UpdateHeight()
            } else {
                // 右子树更高，拿右子树中最小值的节点替换
                minNode := node.Right
                for minNode.Left != nil {
                    minNode = minNode.Left
                }
                // 最小值的节点替换被删除节点
                node.Value = minNode.Value
                node.Times = minNode.Times
                // 把最小的节点删掉
                node.Right = node.Right.Delete(minNode.Value)
                // 删除后要更新该子树高度
                node.Right.UpdateHeight()
            }
        } else {
            // 只有左子树或只有右子树
            // 只有一个子树，该子树也只是一个节点，将该节点替换被删除的节点，然后置子树为空
            if node.Left != nil {
                //第三种情况，删除的节点只有左子树，因为树的特征，可以知道左子树其实就只有一个节点，它本身，否则高度差就等于2了。
                node.Value = node.Left.Value
                node.Times = node.Left.Times
                node.Height = 1
                node.Left = nil
            } else if node.Right != nil {
                //第四种情况，删除的节点只有右子树，因为树的特征，可以知道右子树其实就只有一个节点，它本身，否则高度差就等于2了。
                node.Value = node.Right.Value
                node.Times = node.Right.Times
                node.Height = 1
                node.Right = nil
            }
        }
        // 找到值后，进行替换删除后，直接返回该节点
        return node
    }
    // 左右子树递归删除节点后需要平衡
    var newNode *AVLTreeNode
    // 相当删除了右子树的节点，左边比右边高了，不平衡
    if node.BalanceFactor() == 2 {
        if node.Left.BalanceFactor() >= 0 {
            newNode = RightRotation(node)
        } else {
            newNode = LeftRightRotation(node)
        }
        //  相当删除了左子树的节点，右边比左边高了，不平衡
    } else if node.BalanceFactor() == -2 {
        if node.Right.BalanceFactor() <= 0 {
            newNode = LeftRotation(node)
        } else {
            newNode = RightLeftRotation(node)
        }
    }
    if newNode == nil {
        node.UpdateHeight()
        return node
    } else {
        newNode.UpdateHeight()
        return newNode
    }
}
```
验证AVL树：
```go
// 验证是不是棵AVL树
func (tree *AVLTree) IsAVLTree() bool {
    if tree == nil || tree.Root == nil {
        return true
    }
    // 判断节点是否符合 AVL 树的定义
    if tree.Root.IsRight() {
        return true
    }
    return false
}
// 判断节点是否符合 AVL 树的定义
func (node *AVLTreeNode) IsRight() bool {
    if node == nil {
        return true
    }
    // 左右子树都为空，那么是叶子节点
    if node.Left == nil && node.Right == nil {
        // 叶子节点高度应该为1
        if node.Height == 1 {
            return true
        } else {
            fmt.Println("leaf node height is ", node.Height)
            return false
        }
    } else if node.Left != nil && node.Right != nil {
        // 左右子树都是满的
        // 左儿子必须比父亲小，右儿子必须比父亲大
        if node.Left.Value < node.Value && node.Right.Value > node.Value {
        } else {
            // 不符合 AVL 树定义
            fmt.Printf("father is %v lchild is %v, rchild is %v\n", node.Value, node.Left.Value, node.Right.Value)
            return false
        }
        bal := node.Left.Height - node.Right.Height
        if bal < 0 {
            bal = -bal
        }
        // 子树高度差不能大于1
        if bal > 1 {
            fmt.Println("sub tree height bal is ", bal)
            return false
        }
        // 如果左子树比右子树高，那么父亲的高度等于左子树+1
        if node.Left.Height > node.Right.Height {
            if node.Height == node.Left.Height+1 {
            } else {
                fmt.Printf("%#v height:%v,left sub tree height: %v,right sub tree height:%v\n", node, node.Height, node.Left.Height, node.Right.Height)
                return false
            }
        } else {
            // 如果右子树比左子树高，那么父亲的高度等于右子树+1
            if node.Height == node.Right.Height+1 {
            } else {
                fmt.Printf("%#v height:%v,left sub tree height: %v,right sub tree height:%v\n", node, node.Height, node.Left.Height, node.Right.Height)
                return false
            }
        }
        // 递归判断子树
        if !node.Left.IsRight() {
            return false
        }
        // 递归判断子树
        if !node.Right.IsRight() {
            return false
        }
    } else {
        // 只存在一棵子树
        if node.Right != nil {
            // 子树高度只能是1
            if node.Right.Height == 1 && node.Right.Left == nil && node.Right.Right == nil {
                if node.Right.Value > node.Value {
                    // 右节点必须比父亲大
                } else {
                    fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                    return false
                }
            } else {
                fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                return false
            }
        } else {
            if node.Left.Height == 1 && node.Left.Left == nil && node.Left.Right == nil {
                if node.Left.Value < node.Value {
                    // 左节点必须比父亲小
                } else {
                    fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                    return false
                }
            } else {
                fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                return false
            }
        }
    }
    return true
}
```
AVL树完整代码：
```go
package main
import (
    "fmt"
)
// AVL树
type AVLTree struct {
    Root *AVLTreeNode // 树根节点
}
// AVL节点
type AVLTreeNode struct {
    Value  int64        // 值
    Times  int64        // 值出现的次数
    Height int64        // 该节点作为树根节点，树的高度，方便计算平衡因子
    Left   *AVLTreeNode // 左子树
    Right  *AVLTreeNode // 右字树
}
// 初始化一个AVL树
func NewAVLTree() *AVLTree {
    return new(AVLTree)
}
// 更新节点的树高度
func (node *AVLTreeNode) UpdateHeight() {
    if node == nil {
        return
    }
    var leftHeight, rightHeight int64 = 0, 0
    if node.Left != nil {
        leftHeight = node.Left.Height
    }
    if node.Right != nil {
        rightHeight = node.Right.Height
    }
    // 哪个子树高算哪棵的
    maxHeight := leftHeight
    if rightHeight > maxHeight {
        maxHeight = rightHeight
    }
    // 高度加上自己那一层
    node.Height = maxHeight + 1
}
// 计算平衡因子
func (node *AVLTreeNode) BalanceFactor() int64 {
    var leftHeight, rightHeight int64 = 0, 0
    if node.Left != nil {
        leftHeight = node.Left.Height
    }
    if node.Right != nil {
        rightHeight = node.Right.Height
    }
    return leftHeight - rightHeight
}
// 单右旋操作，看图说话
func RightRotation(Root *AVLTreeNode) *AVLTreeNode {
    // 只有Pivot和B，Root位置变了
    Pivot := Root.Left
    B := Pivot.Right
    Pivot.Right = Root
    Root.Left = B
    // 只有Root和Pivot变化了高度
    Root.UpdateHeight()
    Pivot.UpdateHeight()
    return Pivot
}
// 单左旋操作，看图说话
func LeftRotation(Root *AVLTreeNode) *AVLTreeNode {
    // 只有Pivot和B，Root位置变了
    Pivot := Root.Right
    B := Pivot.Left
    Pivot.Left = Root
    Root.Right = B
    // 只有Root和Pivot变化了高度
    Root.UpdateHeight()
    Pivot.UpdateHeight()
    return Pivot
}
// 先左后右旋操作，看图说话
func LeftRightRotation(node *AVLTreeNode) *AVLTreeNode {
    node.Left = LeftRotation(node.Left)
    return RightRotation(node)
}
// 先右后左旋操作，看图说话
func RightLeftRotation(node *AVLTreeNode) *AVLTreeNode {
    node.Right = RightRotation(node.Right)
    return LeftRotation(node)
}
// 添加元素
func (tree *AVLTree) Add(value int64) {
    // 往树根添加元素，会返回新的树根
    tree.Root = tree.Root.Add(value)
}
func (node *AVLTreeNode) Add(value int64) *AVLTreeNode {
    // 添加值到根节点node，如果node为空，那么让值成为新的根节点，树的高度为1
    if node == nil {
        return &AVLTreeNode{Value: value, Height: 1}
    }
    // 如果值重复，什么都不用做，直接更新次数
    if node.Value == value {
        node.Times = node.Times + 1
        return node
    }
    // 辅助变量
    var newTreeNode *AVLTreeNode
    if value > node.Value {
        // 插入的值大于节点值，要从右子树继续插入
        node.Right = node.Right.Add(value)
        // 平衡因子，插入右子树后，要确保树根左子树的高度不能比右子树低一层。
        factor := node.BalanceFactor()
        // 右子树的高度变高了，导致左子树-右子树的高度从-1变成了-2。
        if factor == -2 {
            if value > node.Right.Value {
                // 表示在右子树上插上右儿子导致失衡，需要单左旋：
                newTreeNode = LeftRotation(node)
            } else {
                //表示在右子树上插上左儿子导致失衡，先右后左旋：
                newTreeNode = RightLeftRotation(node)
            }
        }
    } else {
        // 插入的值小于节点值，要从左子树继续插入
        node.Left = node.Left.Add(value)
        // 平衡因子，插入左子树后，要确保树根左子树的高度不能比右子树高一层。
        factor := node.BalanceFactor()
        // 左子树的高度变高了，导致左子树-右子树的高度从1变成了2。
        if factor == 2 {
            if value < node.Left.Value {
                // 表示在左子树上插上左儿子导致失衡，需要单右旋：
                newTreeNode = RightRotation(node)
            } else {
                //表示在左子树上插上右儿子导致失衡，先左后右旋：
                newTreeNode = LeftRightRotation(node)
            }
        }
    }
    if newTreeNode == nil {
        // 表示什么旋转都没有，根节点没变，直接刷新树高度
        node.UpdateHeight()
        return node
    } else {
        // 旋转了，树根节点变了，需要刷新新的树根高度
        newTreeNode.UpdateHeight()
        return newTreeNode
    }
}
// 找出最小值的节点
func (tree *AVLTree) FindMinValue() *AVLTreeNode {
    if tree.Root == nil {
        // 如果是空树，返回空
        return nil
    }
    return tree.Root.FindMinValue()
}
func (node *AVLTreeNode) FindMinValue() *AVLTreeNode {
    // 左子树为空，表面已经是最左的节点了，该值就是最小值
    if node.Left == nil {
        return node
    }
    // 一直左子树递归
    return node.Left.FindMinValue()
}
// 找出最大值的节点
func (tree *AVLTree) FindMaxValue() *AVLTreeNode {
    if tree.Root == nil {
        // 如果是空树，返回空
        return nil
    }
    return tree.Root.FindMaxValue()
}
func (node *AVLTreeNode) FindMaxValue() *AVLTreeNode {
    // 右子树为空，表面已经是最右的节点了，该值就是最大值
    if node.Right == nil {
        return node
    }
    // 一直右子树递归
    return node.Right.FindMaxValue()
}
// 查找指定节点
func (tree *AVLTree) Find(value int64) *AVLTreeNode {
    if tree.Root == nil {
        // 如果是空树，返回空
        return nil
    }
    return tree.Root.Find(value)
}
func (node *AVLTreeNode) Find(value int64) *AVLTreeNode {
    if value == node.Value {
        // 如果该节点刚刚等于该值，那么返回该节点
        return node
    } else if value < node.Value {
        // 如果查找的值小于节点值，从节点的左子树开始找
        if node.Left == nil {
            // 左子树为空，表示找不到该值了，返回nil
            return nil
        }
        return node.Left.Find(value)
    } else {
        // 如果查找的值大于节点值，从节点的右子树开始找
        if node.Right == nil {
            // 右子树为空，表示找不到该值了，返回nil
            return nil
        }
        return node.Right.Find(value)
    }
}
// 删除指定的元素
func (tree *AVLTree) Delete(value int64) {
    if tree.Root == nil {
        // 如果是空树，直接返回
        return
    }
    tree.Root = tree.Root.Delete(value)
}
func (node *AVLTreeNode) Delete(value int64) *AVLTreeNode {
    if node == nil {
        // 如果是空树，直接返回
        return nil
    }
    if value < node.Value {
        // 从左子树开始删除
        node.Left = node.Left.Delete(value)
        // 删除后要更新该子树高度
        node.Left.UpdateHeight()
    } else if value > node.Value {
        // 从右子树开始删除
        node.Right = node.Right.Delete(value)
        // 删除后要更新该子树高度
        node.Right.UpdateHeight()
    } else {
        // 找到该值对应的节点
        // 该节点没有左右子树
        // 第一种情况，删除的节点没有儿子，直接删除即可。
        if node.Left == nil && node.Right == nil {
            return nil // 直接返回nil，表示直接该值删除
        }
        // 该节点有两棵子树，选择更高的哪个来替换
        // 第二种情况，删除的节点下有两个子树，选择高度更高的子树下的节点来替换被删除的节点，如果左子树更高，选择左子树中最大的节点，也就是左子树最右边的叶子节点，如果右子树更高，选择右子树中最小的节点，也就是右子树最左边的叶子节点。最后，删除这个叶子节点。
        if node.Left != nil && node.Right != nil {
            // 左子树更高，拿左子树中最大值的节点替换
            if node.Left.Height > node.Right.Height {
                maxNode := node.Left
                for maxNode.Right != nil {
                    maxNode = maxNode.Right
                }
                // 最大值的节点替换被删除节点
                node.Value = maxNode.Value
                node.Times = maxNode.Times
                // 把最大的节点删掉
                node.Left = node.Left.Delete(maxNode.Value)
                // 删除后要更新该子树高度
                node.Left.UpdateHeight()
            } else {
                // 右子树更高，拿右子树中最小值的节点替换
                minNode := node.Right
                for minNode.Left != nil {
                    minNode = minNode.Left
                }
                // 最小值的节点替换被删除节点
                node.Value = minNode.Value
                node.Times = minNode.Times
                // 把最小的节点删掉
                node.Right = node.Right.Delete(minNode.Value)
                // 删除后要更新该子树高度
                node.Right.UpdateHeight()
            }
        } else {
            // 只有左子树或只有右子树
            // 只有一个子树，该子树也只是一个节点，将该节点替换被删除的节点，然后置子树为空
            if node.Left != nil {
                //第三种情况，删除的节点只有左子树，因为树的特征，可以知道左子树其实就只有一个节点，它本身，否则高度差就等于2了。
                node.Value = node.Left.Value
                node.Times = node.Left.Times
                node.Height = 1
                node.Left = nil
            } else if node.Right != nil {
                //第四种情况，删除的节点只有右子树，因为树的特征，可以知道右子树其实就只有一个节点，它本身，否则高度差就等于2了。
                node.Value = node.Right.Value
                node.Times = node.Right.Times
                node.Height = 1
                node.Right = nil
            }
        }
        // 找到值后，进行替换删除后，直接返回该节点
        return node
    }
    // 左右子树递归删除节点后需要平衡
    var newNode *AVLTreeNode
    // 相当删除了右子树的节点，左边比右边高了，不平衡
    if node.BalanceFactor() == 2 {
        if node.Left.BalanceFactor() >= 0 {
            newNode = RightRotation(node)
        } else {
            newNode = LeftRightRotation(node)
        }
        //  相当删除了左子树的节点，右边比左边高了，不平衡
    } else if node.BalanceFactor() == -2 {
        if node.Right.BalanceFactor() <= 0 {
            newNode = LeftRotation(node)
        } else {
            newNode = RightLeftRotation(node)
        }
    }
    if newNode == nil {
        node.UpdateHeight()
        return node
    } else {
        newNode.UpdateHeight()
        return newNode
    }
}
// 中序遍历
func (tree *AVLTree) MidOrder() {
    tree.Root.MidOrder()
}
func (node *AVLTreeNode) MidOrder() {
    if node == nil {
        return
    }
    // 先打印左子树
    node.Left.MidOrder()
    // 按照次数打印根节点
    for i := 0; i <= int(node.Times); i++ {
        fmt.Println("value:", node.Value, " tree height:", node.BalanceFactor())
    }
    // 打印右子树
    node.Right.MidOrder()
}
// 验证是不是棵AVL树
func (tree *AVLTree) IsAVLTree() bool {
    if tree == nil || tree.Root == nil {
        return true
    }
    // 判断节点是否符合 AVL 树的定义
    if tree.Root.IsRight() {
        return true
    }
    return false
}
// 判断节点是否符合 AVL 树的定义
func (node *AVLTreeNode) IsRight() bool {
    if node == nil {
        return true
    }
    // 左右子树都为空，那么是叶子节点
    if node.Left == nil && node.Right == nil {
        // 叶子节点高度应该为1
        if node.Height == 1 {
            return true
        } else {
            fmt.Println("leaf node height is ", node.Height)
            return false
        }
    } else if node.Left != nil && node.Right != nil {
        // 左右子树都是满的
        // 左儿子必须比父亲小，右儿子必须比父亲大
        if node.Left.Value < node.Value && node.Right.Value > node.Value {
        } else {
            // 不符合 AVL 树定义
            fmt.Printf("father is %v lchild is %v, rchild is %v\n", node.Value, node.Left.Value, node.Right.Value)
            return false
        }
        bal := node.Left.Height - node.Right.Height
        if bal < 0 {
            bal = -bal
        }
        // 子树高度差不能大于1
        if bal > 1 {
            fmt.Println("sub tree height bal is ", bal)
            return false
        }
        // 如果左子树比右子树高，那么父亲的高度等于左子树+1
        if node.Left.Height > node.Right.Height {
            if node.Height == node.Left.Height+1 {
            } else {
                fmt.Printf("%#v height:%v,left sub tree height: %v,right sub tree height:%v\n", node, node.Height, node.Left.Height, node.Right.Height)
                return false
            }
        } else {
            // 如果右子树比左子树高，那么父亲的高度等于右子树+1
            if node.Height == node.Right.Height+1 {
            } else {
                fmt.Printf("%#v height:%v,left sub tree height: %v,right sub tree height:%v\n", node, node.Height, node.Left.Height, node.Right.Height)
                return false
            }
        }
        // 递归判断子树
        if !node.Left.IsRight() {
            return false
        }
        // 递归判断子树
        if !node.Right.IsRight() {
            return false
        }
    } else {
        // 只存在一棵子树
        if node.Right != nil {
            // 子树高度只能是1
            if node.Right.Height == 1 && node.Right.Left == nil && node.Right.Right == nil {
                if node.Right.Value > node.Value {
                    // 右节点必须比父亲大
                } else {
                    fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                    return false
                }
            } else {
                fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                return false
            }
        } else {
            if node.Left.Height == 1 && node.Left.Left == nil && node.Left.Right == nil {
                if node.Left.Value < node.Value {
                    // 左节点必须比父亲小
                } else {
                    fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                    return false
                }
            } else {
                fmt.Printf("%v,(%#v,%#v) child", node.Value, node.Right, node.Left)
                return false
            }
        }
    }
    return true
}
func main() {
    values := []int64{2, 3, 7, 10, 10, 10, 10, 23, 9, 102, 109, 111, 112, 113}
    // 初始化二叉查找树并添加元素
    tree := NewAVLTree()
    for _, v := range values {
        tree.Add(v)
    }
    // 找到最大值或最小值的节点
    fmt.Println("find min value:", tree.FindMinValue())
    fmt.Println("find max value:", tree.FindMaxValue())
    // 查找不存在的99
    node := tree.Find(99)
    if node != nil {
        fmt.Println("find it 99!")
    } else {
        fmt.Println("not find it 99!")
    }
    // 查找存在的9
    node = tree.Find(9)
    if node != nil {
        fmt.Println("find it 9!")
    } else {
        fmt.Println("not find it 9!")
    }
    // 删除存在的9后，再查找9
    tree.Delete(9)
    tree.Delete(10)
    tree.Delete(2)
    tree.Delete(3)
    tree.Add(4)
    tree.Add(3)
    tree.Add(10)
    tree.Delete(111)
    node = tree.Find(9)
    if node != nil {
        fmt.Println("find it 9!")
    } else {
        fmt.Println("not find it 9!")
    }
    // 中序遍历，实现排序
    tree.MidOrder()
    if tree.IsAVLTree() {
        fmt.Println("is a avl tree")
    } else {
        fmt.Println("is not avl tree")
    }
}
```
## 2-3树和左倾红黑树
左倾红黑树比普通红黑树实现更简单。

红黑树是一种**近似平衡**的二叉查找树，从 2-3 树或 2-3-4 树衍生而来。通过对二叉树节点进行染色，染色为红或黑节点，来模仿 2-3 树或 2-3-4 树的3节点和4节点，从而让树的高度减小。2-3-4 树对照实现的红黑树是普通的红黑树，而 2-3 树对照实现的红黑树是一种变种，称为左倾红黑树。

**2-3树**：它不是一棵二叉树，是一棵三叉树。具有以下特征：
- 内部节点要么有1个数据元素和2个孩子，要么有2个数据元素和3个孩子，叶子节点没有孩子，但有1或2个数据元素。
- 所有叶子节点到根节点的长度一致。这个特征保证了完全平衡，非常完美的平衡。
- 每个节点的数据元素保持从小到大排序，两个数据元素之间的子树的所有值大小介于两个数据元素之间。

2-3树插入元素：
- 先二分查找到要插入的位置
    - 插入元素到一个2节点，直接插入即可，这样节点变成3节点。
    - 插入元素到一个3节点，该3节点的父亲是一个2节点，先将节点变成临时的4节点，然后向上分裂调整一次。
    - 插入元素到一个3节点，该3节点的父亲是一个3节点，先将节点变成临时的4节点，然后向上分裂调整，此时父亲节点变为临时4节点，继续向上分裂调整。
- 核心在于插入3节点后，该节点变为临时4节点，然后进行分裂恢复树的特征。最坏情况为插入节点后，每一次分裂后都导致上一层变为临时4节点，直到树根节点，这样需要不断向上分裂。
- 临时4节点的分裂，细分有六种情况：![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220221233253.png)

**2-3树删除元素**：
- 情况1：删除中间节点
    - 删除的是非叶子节点，该节点一定是有两棵或者三棵子树的，那么从子树中找到其最小后继节点，该节点是叶子节点，用该节点替换被删除的非叶子节点，然后再删除这个叶子节点，进入情况2。
    - 如何找到最小后继节点，当有两棵子树时，那么从右子树一直往左下方找，如果有三棵子树，被删除节点在左边，那么从中子树一直往左下方找，否则从右子树一直往左下方找。
- 情况2：删除叶子节点
    - 删除的是叶子节点，这时如果叶子节点是3节点，那么直接变为2节点即可，不影响平衡。但是，如果叶子节点是2节点，那么删除后，其父节点将会缺失一个儿子，破坏了满孩子的 2-3 树特征，需要进行调整后才能删除。
- 针对情况2，删除一个2节点的叶子节点，会导致父节点缺失一个儿子，破坏了 2-3 树的特征，我们可以进行调整变换，主要有两种调整：
    - 重新分布：尝试从兄弟节点那里借值，然后重新调整节点。
    - 合并：如果兄弟借不到值，合并节点（与父亲的元素），再向上递归处理。


左倾红黑树：
- 可以由2-3树实现
- 根节点的链接是黑色的。
- 红链接均为左链接。
- 没有任何一个结点同时和两条红链接相连
- 任意一个节点到达叶子节点的所有路径，经过的黑链接数量相同，也就是该树是完美黑色平衡的。比如，某一个节点，它可以到达5个叶子节点，那么这5条路径上的黑链接数量一样。
```go
// 定义颜色
const (
    RED   = true
    BLACK = false
)
// 左倾红黑树
type LLRBTree struct {
    Root *LLRBTNode // 树根节点
}
// 左倾红黑树节点
type LLRBTNode struct {
    Value       int64     // 值
    Times       int64      // 值出现的次数
    Left        *LLRBTNode // 左子树
    Right       *LLRBTNode // 右子树
    Color       bool       // 父亲指向该节点的链接颜色
}
// 新建一棵空树
func NewLLRBTree() *LLRBTree {
    return &LLRBTree{}
}
// 节点的颜色
func IsRed(node *LLRBTNode) bool {
    if node == nil {
        return false
    }
    return node.Color == RED
}
```
在元素添加和实现的过程中，需要做调整操作，有两种旋转操作，对某节点的右链接进行左旋转，或者左链接进行右旋转。
```go
// 左旋转
func RotateLeft(h *LLRBTNode) *LLRBTNode {
    if h == nil {
        return nil
    }
    x := h.Right
    h.Right = x.Left
    x.Left = h
    x.Color = h.Color
    h.Color = RED
    return x
}
```
```go
// 右旋转
func RotateRight(h *LLRBTNode) *LLRBTNode {
    if h == nil {
        return nil
    }
    x := h.Left
    h.Left = x.Right
    x.Right = h
    x.Color = h.Color
    h.Color = RED
    return x
}
```
```go
// 颜色转换
func ColorChange(h *LLRBTNode) {
    if h == nil {
        return
    }
    h.Color = !h.Color
    h.Left.Color = !h.Left.Color
    h.Right.Color = !h.Right.Color
}
```
添加元素实现：
- 每次添加元素节点时，都将该节点 Color 字段，也就是父亲指向它的链接设置为 RED 红色。接着判断其父亲是否有两个红链接（如连续的两个左红链接或者左右红色链接），或者有右红色链接，进行颜色变换或旋转操作。
- 几种情况：
    - 插入元素到2节点，直接让节点变为3节点，不过当右插入时需要左旋使得红色链接在左边
    - 插入元素到3节点，需要做旋转和颜色转换操作

```go
// 左倾红黑树添加元素
func (tree *LLRBTree) Add(value int64) {
    // 跟节点开始添加元素，因为可能调整，所以需要将返回的节点赋值回根节点
    tree.Root = tree.Root.Add(value)
    // 根节点的链接永远都是黑色的
    tree.Root.Color = BLACK
}
// 往节点添加元素
func (node *LLRBTNode) Add(value int64) *LLRBTNode {
    // 插入的节点为空，将其链接颜色设置为红色，并返回
    if node == nil {
        return &LLRBTNode{
            Value: value,
            Color: RED,
        }
    }
    // 插入的元素重复
    if value == node.Value {
        node.Times = node.Times + 1
    } else if value > node.Value {
        // 插入的元素比节点值大，往右子树插入
        node.Right = node.Right.Add(value)
    } else {
        // 插入的元素比节点值小，往左子树插入
        node.Left = node.Left.Add(value)
    }
    // 辅助变量
    nowNode := node
    // 右链接为红色，那么进行左旋，确保树是左倾的
    // 这里做完操作后就可以结束了，因为插入操作，新插入的右红链接左旋后，nowNode节点不会出现连续两个红左链接，因为它只有一个左红链接
    if IsRed(nowNode.Right) && !IsRed(nowNode.Left) {
        nowNode = RotateLeft(nowNode)
    } else {
        // 连续两个左链接为红色，那么进行右旋
        if IsRed(nowNode.Left) && IsRed(nowNode.Left.Left) {
            nowNode = RotateRight(nowNode)
        }
        // 旋转后，可能左右链接都为红色，需要变色
        if IsRed(nowNode.Left) && IsRed(nowNode.Right) {
            ColorChange(nowNode)
        }
    }
    return nowNode
}
```
算法分析：

左倾红黑树的最坏树高度为 2log(n)，其中 n 为树的节点数量。为什么呢，我们先把左倾红黑树当作 2-3 树，也就是说最坏情况下沿着 2-3 树左边的节点都是3节点，其他节点都是2节点，这时树高近似 log(n)，再从 2-3 树转成左倾红黑树，当3节点不画平时，可以知道树高变成原来 2-3 树树高的两倍。虽然如此，构造一棵最坏的左倾红黑树很难。

AVL 树的最坏树高度为 1.44log(n)。由于左倾红黑树是近似平衡的二叉树，没有 AVL 树的严格平衡，树的高度会更高一点，因此查找操作效率比 AVL 树低，但时间复杂度只在于常数项的差别，去掉常数项，时间复杂度仍然是 log(n)。

我们的代码实现中，左倾红黑树的插入，需要逐层判断是否需要旋转和变色，复杂度为 log(n)，当旋转变色后导致上层存在连续的红左链接或者红色左右链接，那么需要继续旋转和变色，可能有多次这种调整操作，如图在箭头处添加新节点，出现了右红链接，要一直向上变色到根节点（实际上穿投到根节点的情况极少发生）：![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220221234511.png)

我们可以优化代码，使得在某一层旋转变色后，如果其父层没有连续的左红链接或者不需要变色，那么可以直接退出，不需要逐层判断是否需要旋转和变色。

对于 AVL 树来说，插入最多旋转两次，但其需要逐层更新树高度，复杂度也是为 log(n)。

按照插入效率来说，很多教程都说左倾红黑树会比 AVL 树好一点，因为其不要求严格的平衡，会插入得更快点，但根据我们实际上的递归代码，两者都需要逐层向上判断是否需要调整，只不过 AVL 树多了更新树高度的操作，此操作影响了一点点效率，但我觉得两种树的插入效率都差不多。

在此，我们不再纠结两种平衡树哪种更好，因为代码实现中，两种平衡树都需要自底向上的递归操作，效率差别不大

**删除元素实现**：

删除操作就复杂得多了。对照一下 2-3 树。
1. 情况1：如果删除的是非叶子节点，找到其最小后驱节点，也就是在其右子树中一直向左找，找到的该叶子节点替换被删除的节点，然后删除该叶子节点，变成情况2。
2. 情况2：如果删除的是叶子节点，如果它是红节点，也就是父亲指向它的链接为红色，那么直接删除即可。否则，我们需要进行调整，使它变为红节点，再删除。

在这里，为了使得删除叶子节点时可以直接删除，叶子节点必须变为红节点。（在 2-3 树中，也就是2节点要变成3节点，我们知道要不和父亲合并再递归向上，要不向兄弟借值然后重新分布）

我们创造两种操作，如果删除的节点在左子树中，可能需要进行红色左移，如果删除的节点在右子树中，可能需要进行红色右移。

我们介绍红色左移的步骤：

要在树 h 的的左子树中删除元素，这时树 h 根节点是红节点，其儿子 b，d 节点都为黑
色节点，且两个黑色节点都是2节点，都没有左红孩子，那么直接对 h 树根节点变色即可（相当于2-3 树：把父亲的一个值拉下来合并）
## 2-3-4树和普通红黑树

## 参考
https://www.cs.princeton.edu/~rs/talks/LLRB/LLRB.pdf






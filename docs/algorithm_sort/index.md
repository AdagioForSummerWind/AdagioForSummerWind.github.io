# Algorithm_sort


- [排序算法](#排序算法)
  - [冒泡](#冒泡)
  - [选择](#选择)
  - [插入](#插入)
  - [希尔](#希尔)
  - [归并](#归并)
  - [优先队列及堆](#优先队列及堆)
  - [快速](#快速)
  - [内置库使用快排的原因](#内置库使用快排的原因)
  - [堆排序](#堆排序)


> https://segmentfault.com/a/1190000039668324
> https://www.cnblogs.com/onepixel/p/7674659.html

# 排序算法
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220220204617.png)

稳定性概念
- 定义：能保证两个相等的数，经过排序之后，其在序列的前后位置顺序不变。（A1=A2，排序前A1在A2前面，排序后A1还在A2前面）
- 意义：稳定性本质是维持具有相同属性的数据的插入顺序，如果后面需要使用该插入顺序排序，则稳定性排序可以避免这次排序。

冒泡排序，直接选择排序，直接插入排序被认为是初级的排序算法。中等规模用希尔排序，大规模排序使用快排、归并、堆排序这些高级排序算法。快排综合性能最好，甚至成为了很多编程库内置的排序算法。
## 冒泡
时间复杂度一般是$O(n^2)$。冒泡排序是效率较低的排序算法，可以说是最慢的排序算法了，我们只需知道它是什么，在实际编程中一定不能使用如此之慢的排序算法!
```go
// 冒泡排序 (bubble sort)
package sorts

func bubbleSort(arr []int) []int {
    swapped := true
    for swapped {
        swapped = false
        for i := 0; i < len(arr)-1; i++ {
            if arr[i+1] < arr[i] {
                arr[i+1], arr[i] = arr[i], arr[i+1]
                swapped = true
            }
        }
    }
    return arr
}
```
## 选择
效率同样低下的排序算法。可以在每次循环选择时既选择最小的数，也选择最大的数，以减少循环次数达到优化的目的。工程上同样避免使用。
```go
// 选择排序 (selection sort)
package sorts

func SelectionSort(arr []int) []int {

    for i := 0; i < len(arr); i++ {
        min := i
        for j := i + 1; j < len(arr); j++ {
            if arr[j] < arr[min] {
                min = j
            }
        }

        tmp := arr[i]
        arr[i] = arr[min]
        arr[min] = tmp
    }
    return arr
}
```
## 插入
时间复杂度：$O(n)-O(n^2)$

数组规模 n 较小的大多数情况下，我们可以使用插入排序，它比冒泡排序，选择排序都快，甚至比任何的排序算法都快。

数列中的有序性越高，插入排序的性能越高，因为待排序数组有序性越高，插入排序比较的次数越少。

数据规模较大时，效率也低。
```go
func insertionSortList(head *ListNode) *ListNode {
    if head == nil {
        return nil
    }
    dummyHead := &ListNode{Next: head}
    lastSorted, curr := head, head.Next
    for curr != nil {
        if lastSorted.Val <= curr.Val {
            lastSorted = lastSorted.Next
        } else {
            prev := dummyHead
            for prev.Next.Val <= curr.Val {
                prev = prev.Next
            }
            lastSorted.Next = curr.Next
            curr.Next = prev.Next
            prev.Next = curr
        }
        curr = lastSorted.Next
    }
    return dummyHead.Next
}
```
```go
// 插入排序 (insertion sort)

func insertSort(a []int)[]int{
    for i:=1;i<len(a)&&a[i]>0;i++{
        j,temp:=i-1,a[i]
        for a[i]>a[j] {
            if j==0 {
                j--
                break
            }
            j--
        }
        for x:=i-1;x>j;x--{
            a[x+1]=a[x]
        }
        a[j+1]=temp
    }
    return a
}

```
## 希尔
一个美国人1959年发明的，希尔排序是直接插入排序的改进版本。因为直接插入排序对那些几乎已经排好序的数列来说，排序效率极高，达到了 O(n) 的线性复杂度，但是每次只能将数据移动一位。**希尔排序创造性的可以将数据移动 n 位，然后将 n 一直缩小，缩到与直接插入排序一样为 1**

先取一个小于 N 的整数 d1 ，将位置是 d1 整数倍的数们分成一组，对这些数进行直接
插入排序。接着取一个小于 d1 的整数 d2 ，将位置是 d2 整数倍的数们分成一组，对这些数进行直接插入排序。接着取一个小于 d2 的整数 d3 ，将位置是 d3 整数倍的数们分成一组，对这些数进行直接插入排序。…直到取到的整数 d=1 ，接着使用直接插入排序。

这是一种分组插入方法，最后一次迭代就相当于是直接插入排序，其他迭代相当于每次移动 n 个距离的直接插入排序，这些整数是两个数之间的距离，我们称它们为**增量**。

我们取数列长度的一半为增量，以后每次减半，直到增量为1。

希尔排序通过分组使用直接插入排序，因为步长比 1大，在一开始可以很快将无序的数列变得不那么无序，比较和交换的次数也减少，直到最后使用步长为 1 的直接插入排序，数列已经是相对有序了，所以时间复杂度会稍好一点。

在最好情况下，也就是数列是有序时，希尔排序需要进行 logn 次增量的直接插入排序，因为每次直接插入排序最佳时间复杂度都为： O(n) ，因此希尔排序的最佳时间复杂度为： O(nlogn) 。

在最坏情况下，每一次迭代都是最坏的，假设增量序列为： d8 d7 d6 ... d3 d2 1 ，那么每一轮直接插入排序的元素数量为： n/d8 n/d7 n/d6 .... n/d3 n/d2 n ，那么时间复杂度按照直接插入的最坏复杂度来计算为：$O( (n/d8)^2 + (n/d7)^2 + (n/d6)^2 + ... + (n/d2)^2 + n^2) = O( < 2 ) * O(n^2)$

不同的分组增量序列，有不同的时间复杂度。**Hibbard 增量序列： 1，3，7，···，2n−1 是被证明可广泛应用的分组序列，时间复杂度为： Θ(n^1.5) 。**

希尔排序的时间复杂度大约在这个范围： O(n^1.3)~O(n^2) ，具体还无法用数学来严格证明它。
```go
// 希尔排序 (shell sort)
package sorts

func ShellSort(arr []int) []int {
    for d := int(len(arr) / 2); d > 0; d /= 2 { 
        for i := d; i < len(arr); i++ {
            for j := i; j >= d && arr[j-d] > arr[j]; j -= d {
                arr[j], arr[j-d] = arr[j-d], arr[j]
            }
        }
    }
    return arr
}
```

## 归并
分治法

将两个有序数组进行合并，最多进行 N 次比较就可以生成一个新的有序数组， N 是两个数组长度之和。

归并操作最坏的时间复杂度为： O(n) ，其中 n 是较长数组的长度（因为$N<2n$）。归并操作最好的时间复杂度为： O(n) ，其中 n 是较短数组的长度。正是利用这个特点，归并排序先排序较小的数组，再将有序的小数组合并形成更大有序的数组。

归并排序有两种递归做法，一种是自顶向下，一种是自底向上。
- 自顶向下：不断二分大数组直到无法切分，排序，不断两两合并，得到排序后数组。
    - 每次都是一分为二，特别均匀，所以最差和最坏时间复杂度都一样。归并操作的时间复杂度为： O(n) ，因此总的时间复杂度为： T(n)=2T(n/2)+O(n) ，根据主定理公式可以知道时间复杂度为： O(nlogn) 
    - 因为不断地递归，程序栈层数会有 logn 层，所以递归栈的空间复杂度为： O(logn) ，对于排序十亿个整数，也只要： log(100 0000 0000)=29.897 ，占用的堆栈层数最多 30 层
- 自底向上：小数组排序合并成大数组。
    - 时间复杂度同上
    - 因不需要递归，没有程序栈占用，空间复杂度为O(1)

归并排序归并操作占用了额外的辅助数组，且归并操作是从一个元素的数组开始。

改进：
- 对于小规模数组，使用直接插入排序。
- 原地排序，节约掉辅助数组空间的占用。

建议使用自底向上非递归排序，不会有程序栈空间损耗

手摇算法（翻转算法）：
- 主要用来对数组两部分进行位置互换
- eg:将字符串 abcde1234567 的前 5 个字符与后面的字符交换位置，那么手摇后变成： 1234567abcde 。
- 如何翻转：
    - 将前部分逆序
    - 将后部分逆序
    - 对整体逆序

归并原地排序利用了手摇算法的特征，不需要额外的辅助数组。

我们自底开始，将元素按照数量为 blockSize 进行小数组排序，使用直接插入排序，然后我们对这些有序的数组向上进行原地归并操作。

**归并排序是唯一一个有稳定性保证的高级排序算法，某些时候，为了寻求大规模数据下排序前后，相同元素位置不变，可以使用归并排序。**

```go
// 归并排序 (merge sort)
package sorts

func merge(a []int, b []int) []int {

    var r = make([]int, len(a)+len(b))
    var i = 0
    var j = 0

    for i < len(a) && j < len(b) {

        if a[i] <= b[j] {
            r[i+j] = a[i]
            i++
        } else {
            r[i+j] = b[j]
            j++
        }

    }

    for i < len(a) {
        r[i+j] = a[i]
        i++
    }
    for j < len(b) {
        r[i+j] = b[j]
        j++
    }

    return r

}

// Mergesort 合并两个数组
func Mergesort(items []int) []int {

    if len(items) < 2 {
        return items

    }

    var middle = len(items) / 2
    var a = Mergesort(items[:middle])
    var b = Mergesort(items[middle:])
    return merge(a, b)

}
```
```go
func MergeSort(nums []int) []int {
    return mergeSort(nums)
}
func mergeSort(nums []int) []int {
    if len(nums) <= 1 {
        return nums
    }
    // 分治法：divide 分为两段
    mid := len(nums) / 2
    left := mergeSort(nums[:mid])
    right := mergeSort(nums[mid:])
    // 合并两段数据
    result := merge(left, right)
    return result
}
func merge(left, right []int) (result []int) {
    // 两边数组合并游标
    l := 0
    r := 0
    // 注意不能越界
    for l < len(left) && r < len(right) {
        // 谁小合并谁
        if left[l] > right[r] {
            result = append(result, right[r])
            r++
        } else {
            result = append(result, left[l])
            l++
        }
    }
    // 剩余部分合并
    result = append(result, left[l:]...)
    result = append(result, right[r:]...)
    return
}
```
## 优先队列及堆
堆排序属于选择类排序算法。

优先队列是一种能完成以下任务的队列：插入一个数值，取出最小或最大的数值（获取数值，并且删除）。

优先队列可以用二叉树来实现，我们称这种结构为二叉堆。

最小堆和最大堆是二叉堆的一种，是一棵**完全二叉树**（一种平衡树）。

最小堆的性质：
1. 父节点的值都小于左右儿子节点。
2. 这是一个递归的性质。

最大堆的性质：
1. 父节点的值都大于左右儿子节点。
2. 这是一个递归的性质。

最大堆和最小堆实现方式一样，只不过根节点一个是最大的，一个是最小的

最大堆特征：
- 最大堆实现细节(两个操作)：
    1. push：向堆中插入数据时，首先在堆的末尾插入数据，如果该数据比父亲节点还大，那么交换，然后不断向上提升，直到没有大小颠倒为止。
    2. pop：从堆中删除最大值时，首先把最后一个值复制到根节点上，并且删除最后一个数值，然后和儿子节点比较，如果值小于儿子，与儿子节点交换，然后不断向下交换， 直到没有大小颠倒为止。在向下交换过程中，如果有两个子儿子都大于自己，就选择较大的。
- 最大堆有两个核心操作，一个是上浮，一个是下沉，分别对应 push 和 pop 。
- 上浮操作
    - 操作一次 push 的最好时间复杂度为： O(1) ，因为第一次上浮时如果不大于父亲，那么就结束了。最坏的时间复杂度为： O(logn) ，相当于每次都大于父亲，会一直往上浮到根节点，翻转次数等于树的高度，而树的高度等于元素个数的对数： log(n) 。
    - 构建一个最大堆，从空堆开始，每次添加元素到尾部后，需要向上翻转，最坏翻转次数是：近似 = log(1)+log(2)+log(3)+...+log(n) = log(n!)
    - log(n!) 和 nlog(n) 是同阶的，故最坏时间复杂度便得到了。元素不全相同的情况下最好时间复杂度也是这个。
- 下沉操作
    - 操作一次 pop 最好的时间复杂度也是： O(1) ，因为第一次比较时根节点就是最大的。最坏时间复杂度仍然是树的高度： O(logn) 。
    - 从一个最大堆，逐一移除堆顶元素，然后将堆尾元素置于堆顶后，向下翻转恢复堆特征，最坏翻转次数是:近似 = log(1)+log(2)+log(3)+...+log(n) = log(n!)，同上浮。元素不全相同的情况下最好时间复杂度也是O(nlog(n))
- 如果所有的元素都一样的情况下，建堆和移除堆的每一步都不需要翻转，最好时间复杂度
为： O(n) ，复杂度主要在于遍历元素。

**根据最大堆，可以实现堆排序。**

普通堆排序：先构建一个最小堆，然后依次把根节点元素 pop 出即可：
- 因为一开始会认为堆是空的，每次添加元素都需要添加到尾部，然后向上翻转，需要用 Heap.Size来记录堆的大小增长，这种堆构建，可以认为是非原地的构建，影响了效率

改进的原地自底向上的堆排序，不会从空堆开始，而是把待排序的数列当成一个混乱的最大堆，从底层逐层开始，对元素进行下沉操作，一直恢复最大堆的特征，直到根节点。
- 将构建堆的时间复杂度从 O(nlogn) 降为 O(n) ，总的堆排序时间复杂度从 O(2nlogn) 改进到 O(n+nlogn) 。

自底向上堆排序：
- 构建最大堆步骤：
    - 先对最底部的所有非叶子节点进行下沉，即这些非叶子节点与它们的儿子节点比较，较大的儿子和父亲交换位置。
    - 接着从次二层开始的非叶子节点重复这个操作，直到到达根节点最大堆就构建好了


```go
// 堆排序 (heap sort)
package sorts

type maxHeap struct {
    slice    []int
    heapSize int
}

func buildMaxHeap(slice []int) maxHeap {
    h := maxHeap{slice: slice, heapSize: len(slice)}
    for i := len(slice) / 2; i >= 0; i-- {
        h.MaxHeapify(i)
    }
    return h
}

func (h maxHeap) MaxHeapify(i int) {
    l, r := 2*i+1, 2*i+2
    max := i

    if l < h.size() && h.slice[l] > h.slice[max] {
        max = l
    }
    if r < h.size() && h.slice[r] > h.slice[max] {
        max = r
    }
    if max != i {
        h.slice[i], h.slice[max] = h.slice[max], h.slice[i]
        h.MaxHeapify(max)
    }
}

func (h maxHeap) size() int { return h.heapSize } 

func HeapSort(slice []int) []int {
    h := buildMaxHeap(slice)
    for i := len(h.slice) - 1; i >= 1; i-- {
        h.slice[0], h.slice[i] = h.slice[i], h.slice[0]
        h.heapSize--
        h.MaxHeapify(0)
    }
    return h.slice
}
```

## 快速
亦使用了分治法。

对冒泡排序的改进。

快速排序通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

步骤：
- 先从数列中取出一个数作为基准数。一般取第一个数。
- 分区过程，将比这个数大的数全放到它的右边，小于或等于它的数全放到它的左边。
- 再对左右区间重复第二步，直到各区间只有一个数。

在最好情况下，每一轮都能平均切分，这样遍历元素只要 n/2 次就可以把数列分成两部分，每一轮的时间复杂度都是： O(n) 。因为问题规模每次被折半，折半的数列继续递归进行切分，也就是总的时间复杂度计算公式为： T(n) = 2*T(n/2) + O(n) 。按照主定理公式计算，我们可以知道时间复杂度为： O(nlogn)

最差的情况下，每次都不能平均地切分，每次切分都因为基准数是最大的或者最小的，不能分成两个数列，这样时间复杂度变为了 T(n) = T(n-1) + O(n) ，按照主定理计算可以知道时间复杂度为： O(n^2) 

数据规模越大越难以出现最差的情况，在综合情况下，快速排序的平均时间复杂度为： O(nlogn) 。对比之前介绍的排序算法，快速排序比那些动不动就是平方级别的初级排序算法更佳。

为了避免切分不均匀情况的发生，有几种方法改进：
1. 每次进行快速排序切分时，先将数列随机打乱，再进行切分，这样随机加了个震荡，减少不均匀的情况。当然，也可以随机选择一个基准数，而不是选第一个数。
2. 每次取数列头部，中部，尾部三个数，取三个数的中位数为基准数进行切分。

快速排序使用原地排序，存储空间复杂度为： O(1) 。而因为递归栈的影响，递归的程序栈开辟的层数范围在 $logn-n$ ，所以递归栈的空间复杂度为： $O(logn)-log(n)$ ，最坏为： log(n) ，当元素较多时，程序栈可能溢出。通过改进算法，使用伪尾递归进行优化，递归栈的空间复杂度可以减小到 O(logn) 

**改进**：
1. 在小规模数组的情况下，直接插入排序的效率最好，当快速排序递归部分进入小数组范围，可以
切换成直接插入排序。
2. 排序数列可能存在大量重复值，使用三向切分快速排序，将数组分成三部分，大于基准数，等于
基准数，小于基准数，这个时候需要维护三个下标。
3. 使用伪尾递归减少程序栈空间占用，使得栈空间复杂度从 O(logn)~log(n) 变
为： O(logn) 。

伪尾递归优化：
```go
// 伪尾递归快速排序
 func QuickSort3(array []int, begin, end int) {
    for begin < end {
 // 进行切分
        loc := partition(array, begin, end)

 // 那边元素少先排哪边
        if loc-begin < end-loc {
 // 先排左边
            QuickSort3(array, begin, loc-1)
            begin = loc + 1
        } else {
 // 先排右边
            QuickSort3(array, loc+1, end)
            end = loc - 1
        }
    }
 }
```
解析：很多人以为这样子是尾递归。其实这样的快排写法是伪装的尾递归，不是真正的尾递归，因为有for 循环，不是直接 return QuickSort ，递归还是不断地压栈，栈的层次仍然不断地增长。但是，因为先让规模小的部分排序，栈的深度大大减少，程序栈最深不会超过 logn 层，这样堆栈最坏空间复杂度从 O(n) 降为 O(logn) 。这种优化也是一种很好的优化，因为栈的层数减少了，对于排序十亿个整数，也只要： log(100 00000000)=29.897 ，占用的堆栈层数最多 30 层，比不进行优化，可能出现的 O(n) 常数层好很多。

非递归写法仅仅是将之前的递归栈转化为自己维持的手工栈。

```go
package main

import "fmt"

var swapcnt int

func main() {
	arr := []int{2, 3, 4, 5, 1}
	//idx := Swap(arr, 0, len(arr))
	//fmt.Println(idx, arr)
	myquickSort(arr, 0, len(arr)-1)
	fmt.Println(arr)
	fmt.Println(swapcnt)
}

func Swap(arr []int, l, r int) int {
	idx := l
	pivot := arr[r]
	for i := l; i <= r; i++ {
		if arr[i] <= pivot {
			if idx != i {
				arr[idx], arr[i] = arr[i], arr[idx]
				swapcnt += 1
			}
			idx += 1
		}
	}
	return idx - 1
}

func myquickSort(nums []int, l, r int) {
	if l < r {
		m := Swap(nums, l, r)
		myquickSort(nums, l, m-1)
		myquickSort(nums, m+1, r)
	}
}

func quickSort(nums []int, l, r int) { //[l,r]
	if l < r {
		m := partition(nums, l, r)
		quickSort(nums, l, m-1)
		quickSort(nums, m+1, r)
	}
}

func partition(nums []int, l int, r int) int {
	key := nums[r]
	i, j := l, l
	for j < r {
		if nums[j] < key {
			nums[i], nums[j] = nums[j], nums[i]
			i++
		}
		j++
	}
	nums[i], nums[r] = nums[r], nums[i]
	return i
}


// 解决栈溢问题
func qsort(arr []int, left, right int) {
	if left > right {
		return
	}
	pivot := arr[right]
	i, j := left, right
	for i < j {
		for i < j && pivot < arr[j] {
			j--
		}
		for i < j && pivot >= arr[i] {
			i++
		}
		arr[i], arr[j] = arr[j], arr[i]
	}
	qsort(arr, left, i-1)
	qsort(arr, i+1, right)
}


```


```go
// 三向切分快速排序 (quick sort)
package sorts

import (
    "math/rand"
)

func QuickSort(arr []int) []int {

    if len(arr) <= 1 {
        return arr
    }

    pivot := arr[rand.Intn(len(arr))]

    lowPart := make([]int, 0, len(arr))
    highPart := make([]int, 0, len(arr))
    middlePart := make([]int, 0, len(arr))

    for _, item := range arr {
        switch {
        case item < pivot:
            lowPart = append(lowPart, item)
        case item == pivot:
            middlePart = append(middlePart, item)
        case item > pivot:
            highPart = append(highPart, item)
        }
    }

    lowPart = QuickSort(lowPart)
    highPart = QuickSort(highPart)

    lowPart = append(lowPart, middlePart...)
    lowPart = append(lowPart, highPart...)

    return lowPart
}
```

```go
func QuickSort(nums []int) []int {
    // 思路：把一个数组分为左右两段，左段小于右段
    quickSort(nums, 0, len(nums)-1)
    return nums

}
// 原地交换，所以传入交换索引
func quickSort(nums []int, start, end int) {
    if start < end {
        // 分治法：divide
        pivot := partition(nums, start, end)
        quickSort(nums, 0, pivot-1)
        quickSort(nums, pivot+1, end)
    }
}
// 分区
func partition(nums []int, start, end int) int {
    // 选取最后一个元素作为基准pivot
    p := nums[end]
    i := start
    // 最后一个值就是基准所以不用比较
    for j := start; j < end; j++ {
        if nums[j] < p {
            swap(nums, i, j)
            i++
        }
    }
    // 把基准值换到中间
    swap(nums, i, end)
    return i
}
// 交换两个元素
func swap(nums []int, i, j int) {
    t := nums[i]
    nums[i] = nums[j]
    nums[j] = t
}
```
## 内置库使用快排的原因
首先堆排序，归并排序最好最坏时间复杂度都是： O(nlogn) ，而快速排序最坏的时间复杂度是： O(n^2) ，但是很多编程语言内置的排序算法使用的仍然是快速排序，这是为什么？
1. 这个问题有偏颇，选择排序算法要看具体的场景， Linux 内核用的排序算法就是堆排序，而Java 对于数量比较多的复杂对象排序，内置排序使用的是归并排序，只是一般情况下，快速排序更快。
2. 归并排序有两个稳定，第一个稳定是排序前后相同的元素位置不变，第二个稳定是，每次都是很平均地进行排序，读取数据也是顺序读取，能够利用存储器缓存的特征，比如从磁盘读取数据进行排序。因为排序过程需要占用额外的辅助数组空间，所以这部分有代价损耗，但是原地手摇的归并排序克服了这个缺陷。
3. 复杂度中，大 O 有一个常数项被省略了，堆排序每次取最大的值之后，都需要进行节点翻转，重新恢复堆的特征，做了大量无用功，常数项比快速排序大，大部分情况下比快速排序慢很多。但是堆排序时间较稳定，不会出现快排最坏 O(n^2) 的情况，且省空间，不需要额外的存储空间和栈空间。
4. 当待排序数量大于16000个元素时，使用自底向上的堆排序比快速排序还快，可见此：https://core.ac.uk/download/pdf/82350265.pdf。
5. 快速排序最坏情况下复杂度高，主要在于切分不像归并排序一样平均，而是很依赖基准数的现在，我们通过改进，比如随机数，三切分等，这种最坏情况的概率极大的降低。大多数情况下，它并不会那么地坏，大多数快才是真的块。
6. 归并排序和快速排序都是分治法，排序的数据都是相邻的，而堆排序比较的数可能跨越很大的范围，导致局部性命中率降低，不能利用现代存储器缓存的特征，加载数据过程会损失性能。

对稳定性有要求的，要求排序前后相同元素位置不变，可以使用归并排序， Java 中的复杂对象类型，要求排序前后位置不能发生变化，所以小规模数据下使用了直接插入排序，大规模数据下使用了归并排序。

对栈，存储空间有要求的可以使用堆排序，比如 Linux 内核栈小，快速排序占用程序栈太大了，使用快速排序可能栈溢出，所以使用了堆排序。

在 Golang 中，标准库 sort 中对切片进行稳定排序：会先按照 20 个元素的范围，对整个切片分段进行插入排序，因为小数组插入排序效率高，然后再对这些已排好序的小数组进行归并排序。其中归并排序还使用了原地排序，节约了辅助空间。

快速排序限制程序栈的层数为： 2*ceil(log(n+1)) ，当递归超过该层时表示程序栈过深，那么转为堆排序。













## 堆排序
```go
package main

func HeapSort(a []int) []int {
    // 1、无序数组a
    // 2、将无序数组a构建为一个大根堆
    for i := len(a)/2 - 1; i >= 0; i-- {
        sink(a, i, len(a))
    }
    // 3、交换a[0]和a[len(a)-1]
    // 4、然后把前面这段数组继续下沉保持堆结构，如此循环即可
    for i := len(a) - 1; i >= 1; i-- {
        // 从后往前填充值
        swap(a, 0, i)
        // 前面的长度也减一
        sink(a, 0, i)
    }
    return a
}
func sink(a []int, i int, length int) {
    for {
        // 左节点索引(从0开始，所以左节点为i*2+1)
        l := i*2 + 1
        // 右节点索引
        r := i*2 + 2
        // idx保存根、左、右三者之间较大值的索引
        idx := i
        // 存在左节点，左节点值较大，则取左节点
        if l < length && a[l] > a[idx] {
            idx = l
        }
        // 存在右节点，且值较大，取右节点
        if r < length && a[r] > a[idx] {
            idx = r
        }
        // 如果根节点较大，则不用下沉
        if idx == i {
            break
        }
        // 如果根节点较小，则交换值，并继续下沉
        swap(a, i, idx)
        // 继续下沉idx节点
        i = idx
    }
}
func swap(a []int, i, j int) {
    a[i], a[j] = a[j], a[i]
}
```

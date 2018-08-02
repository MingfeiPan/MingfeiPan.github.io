---
layout: post
title: BFPRT algorithm
category: algorithm
tag : algorithm
---

### top-k  

top-k是一个总被提到的问题, 通常被描述为在某个数量求其前k小或者前k大的数, 最基本的方法可以用排序, 有序后截取所需要的top-k即可, 比如利用快排就可以在O(nlogn)内处理完。 但是显然这不是最佳方案, 因为很容易看到我们对top-k之外的其他数据的排序工作毫无意义, 考虑可以精简掉这些时间。    

另一个方法是构造一个大顶堆或者小顶堆, 这种方法在处理大量数据时有奇效, 我们可以先取k个数构造堆, 其他数据依次入堆, 这样数据也不必要一次加载到内存, 可以放在其他存储介质中依次读取。 假设我们每次入堆都需要重新heapify, 最坏也就是每次执行O(logk)次, n个数其时间复杂度为O(nlogk), k是我们top-k的取值, 往往不会是一个很大的数, 所以综合来看这个方法已经可以被视作是O(n)时间复杂度的方法了。 

第三种办法, 接着关注快排, 一次快排的partition就是寻找一个数值, 将数组中所有小于这个数的值放在其左侧, 所有大于其数值的数放在其右侧, 如果我们选择的数值恰好是top-k的那个点, 那一次快排O(n)就可以解决这个问题了, 这就是bfprt做的事情。 

### BFPRT  

[BFPRT](https://en.wikipedia.org/wiki/Median_of_medians), 像我们所熟知的AVL树一样, 来自其发明人的名字组合Blum、Floyd、Pratt、Rivest、Tarjan, 也可以被称作中文数的中位数算法, 其核心是挑选在快速排序中每次使用的所谓轴点(pivot)的特殊方法。  

在选取top-k时, 我们很容易想到利用快速排序一次遍历的方法, 就可以讲序列左右氛围k与n-k两部分, 这时如果我们能定位到一个好的轴点, 那么确实可以快速的定位出我们需要的k个数, 这里列一下BFPRT的伪代码  

```
function bfprt(list, left, right, k)
    loop
        if left = right
             return left
        pivotIndex := pivot(list, left, right)
        pivotIndex := partition(list, left, right, pivotIndex)
        if k == pivotIndex
            return k
        else if k < pivotIndex
            right := pivotIndex - 1
        else
            left := pivotIndex + 1
```  

其实非常直白, 找到一个轴点, 做一次快排分区, 查看分出来的数的数量与k的关系然后再继续递归  

单次快排partition我们都很熟悉:  

```
int partition(int* arr, int left, int right, int pivot)
{
	swap(&arr[pivot], &arr[right]);
	int index = left;
	
	for(int i = left; i < right; i++)
	{
		if(arr[i] > arr[right]){
			swap(&arr[index++], &arr[i]);
		}
	}
	swap(&arr[index], &arr[right]);
	return index;
}
```  

这里最核心的是如何高效的找到pivot  

```
int getpivot(int* arr, int left, int right)
{
	if((right - left) < 5)
		return insertsort(arr, left, right);
	int flag = left - 1;
	int index;
	for(int i = left; i + 4 <= right; i+= 5)
	{
		index = insertsort(arr, i, i+4);
		swap(&arr[++flag], &arr[index]);
	}
	return bfprt(arr, left, flag, ((flag - left + 1) / 2) + 1);
}
```

选取轴点的过程, 我们以每组5个一组, 分别划分原数组的数, 对每个组排序, 这里可以任选一种排序方法, 因为每组只有5个数, 时间复杂度可以视作常数, 我这里用插入排序, 排序后找到他们的中位数, 之后再从这些中位数组成的数组中找到中位数, 这就是所选的pivot, 注意在中位数数组的中位数选取时, 可以递归的调用BFPRT  

之后的流程就是一次快排然后自身递归的过程了, 时间复杂度请参考[Median of medians](https://en.wikipedia.org/wiki/Median_of_medians)。  

[一份c的实现](https://github.com/MingfeiPan/leetcode/blob/master/heap/215.c)。 

通常我们在使用时, top-k往往面对的数据量很大, 一次读取到内存中不现实, 此时选择大/小顶堆是最合适的方法。 








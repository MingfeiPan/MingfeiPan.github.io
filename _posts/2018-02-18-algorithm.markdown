---
layout: post
title: BFPRT algorithm
category: algorithm
tag : algorithm
---

### top-k  

很多面试都喜欢问top-k的问题, 通常被描述为在某个数量求其前k小或者前k大的数, 可选的方法有通过高效的排序, 比如快排或者堆排序来找出top k的数, 或者直接构造一个大顶堆或者小顶堆做k次的gettop&heapify来达成, 但是这些方法只能做到O(nlogn), 其实存在一种O(n)的方法, 也就是我们要说的BFPRT algorithm  

### BFPRT  

[BFPRT](https://en.wikipedia.org/wiki/Median_of_medians), 像我们所熟知的AVL树一样, 来自其发明人的名字组合Blum、Floyd、Pratt、Rivest、Tarjan, 也可以被称作中文数的中位数算法, 其核心是挑选在快速排序中每次使用的所谓轴点(pivot)的特殊方法  

在选取top-k时, 我们很容易想到利用快速排序一次遍历的方法, 就可以讲序列左右氛围k与n-k两部分, 这时如果我们能定位到一个好的轴点, 那么确实可以快速的定位出我们需要的k个数, 这里列一下BFPRT的伪代码  

```
function select(list, left, right, n)
    loop
        if left = right
             return left
        pivotIndex := pivot(list, left, right)
        pivotIndex := partition(list, left, right, pivotIndex)
        if n = pivotIndex
            return n
        else if n < pivotIndex
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

这里最核心的是如何高效的找到pivot, 我们可以看出在退化情况下, 比如我们每次把第一个数当作轴点, 其实这就是一个快排的过程, 然而经过中位数的中位数算法, 确实可以挑选到高效的轴点从而把时间复杂度从O(nlogn)进化到O(n)  

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

选取轴点的过程, 我们以每组5个一组, 分别划分原数组的数, 对每个组排序, 这里可以任选一种排序方法, 因为每组只有5个数, 可以视作常数, 我这里用插入, 排序后找到他们的中位数, 之后再从这些中位数组成的数组中找到中位数, 这就是所选的pivot, 注意在中位数数组的中位数选取时, 可以递归的调用BFPRT  

之后的流程就是一次快排然后自身递归的过程了, 这里时间复杂度请参考[百科](https://en.wikipedia.org/wiki/Median_of_medians),  这里还清楚的解释了为什么选择每组5个而不是其他数字  

这里我没有列出完整的代码, 如果有需求可以参考我这里有一份[c的实现](https://github.com/MingfeiPan/leetcode/blob/master/heap/215.c)  






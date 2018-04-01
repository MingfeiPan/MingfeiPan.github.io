---
layout: post
title: 动态规划的更多一些笔记 
category: algorithm
tag : algorithm
---

上一篇长篇大论的写了很多, 实际上结合了很多别人的笔记, 自己理解的同时也结合了一些题目, 所以再补充一些题目的细节吧, 做题时自己对一般动归问题的解法往往是, 以递归的思路拆分问题, 再以递推的思路写出代码, 个人感觉好处是, 从小到大的去推解, 能更好的处理遇到重叠子问题的情况, 思路符合我们一般性思考的过程, 虽然递归的去写代码也可以做到(利用所谓备忘录法)。  

#### 最大连续子数组之和  

这个题是在面试中遇到过的, 当时直觉知道是动归, 但是最后代码还是没写好, 题目可以描述为, 有一个无序的整型数组, 其中数据可正可负, 求找出数组中连续一个或多个元素组成的子数组, 使这个子数组的和为最大。  
比如给一个数组[1,5,-3,4,-8], 应该返回7  
我们先定义这个问题, 数组arr的长度为n, 求其长度为k的连续子数组, 使得子数组之和最大, 如果是朴素方法, 也就是暴力破解, 我们要遍历一遍arr, 把分别包含每个元素的子数组组都找出来, 再分别计算他们的和, 所以要用到两次遍历, 一次求和, 时间复杂度在O(n^3)。 显然这是不可选的, 那么动归的思路, 我还是习惯性的从后向前分析, 在本问题中去找子问题的定义, 分析最后一个元素arr[n-1]与整个结果的关系:  

>1.arr[n-1]自己就是结果  
>2.以arr[n-1]结尾的连续子数组是结果  
>3.结果跟arr[n-1]无关, 答案在f(n-1)中  

用ret[n-1]表示我们的结果, end[n-1]表示以n-1结束的结果, 其状态转移方程可以写为:  
ret[n-1] = max(ret[n-2], arr[n-1], arr[n-1] + end[n-1]

```
def findm(l):
    if len(l) == 0:
        return 0
    ret = l[0]
    cur = l[0]
    for i in range(1, len(l)):
        cur += l[i]
        cur = cur if cur > l[i] else l[i]
        ret = ret if ret > cur else cur

    return ret
```

可以看到这里也用到了类似滚动变量的结构, 所以空间复杂度是O(1)的, 另外在查看这个题时候, 看到了一个另一个规划的思路, 更加清晰, "从头遍历数组, 累加求和, 每当当前的和小于0时, 就重置这个累加为当前元素重新开始累加, 返回这些累加和的最大值"  

```
def findm1(l):
    if len(l) == 0:
        return 0
    ret = -sys.maxsize 
    cur = 0
    for i in range(0, len(l)):
        cur = cur + l[i] if cur >= 0 else l[i]
        ret = ret if ret > cur else cur

    return ret
```

利用这个更加明晰的定义, 我们可以更方便的找出这个连续子数组在原数组的位置, 每当重置时->起点更新, 每当更新ret时->全局起点更新, 终点更新

```
def findm1(l):
    start = 0
    end = 0
    curstart = 0
    if len(l) == 0:
        return 0
    ret = -sys.maxsize 
    cur = 0
    for i in range(0, len(l)):
        if cur < 0:
            cur = l[i]
            curstart = i
        else:
            cur = cur + l[i]

        if ret < cur:
            ret = cur
            start = curstart
            end = i

    print('start : %d  end : %d', (start, end))

    return ret
```

### lis

lis(Longest Increasing Subsequence), 最长递增子序列, 跟lcs蛮像的, 也是一个非常经典的dp题, 指找到一个给定序列的最长子序列的长度, 使得子序列中的所有元素单调递增  

这个问题有很多解法, 我们这里只说dp的思路, 还是老习惯, 从后向前分析问题, 从前向后写代码, 对于一个数列, 长度为n, 可以先拆分问题为求出以每个元素为结尾的所有数列的最大子序列, 再找出最大的那个, 这样拆分一次, 可以用尾元素来定位, 更加容易定义出子问题跟转移方程  

当确定结尾之后, 问题进一步成为了: 以数列中第k项结尾的最长递增子序列的长度, 所以可以找到状态转移方程:  

>1. f(k) = f(i) + 1 for i in 1..k of arr[i] < arr[k]

对于在1..k之间的每个数i, 如果arr[i]小于arr[k]那问题f(k)就等于f(i) + 1, 其实这里就可以感觉到, 应该会出现很多的重叠子, 所以在处理时, 用一个数组来保存有的f(k) for k in 1..n  

```
    def lengthOfLIS(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        size = len(nums)
        arr = []
        arr.insert(0, 1)
                
        for i in range(1, size):
            maxn = 1
            for j in range(0, i):
                if nums[j] < nums[i]:
                    maxn = maxn if maxn > arr[j] + 1 else arr[j] + 1

            arr.insert(i, maxn)
            
        ret = 1
        for i in range(0, size):
            ret = ret if ret > arr[i] else arr[i]
        return ret
```

可以看到我们从小到大的代码思路, 总之做了一些练习, 感觉真正定义子问题时, 是要靠实验的, 起码目前我无法在第一时间找到精确的定义, 这应该需要一个过程  


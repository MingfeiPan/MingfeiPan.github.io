---
layout: post
title: 回溯算法的一些笔记 
category: algorithm
tag : algorithm
---

回溯算是一种很直观的搜索方法, 在搜索尝试过程中寻找问题的解, 当发现已不满足求解条件时就回溯返回, 再尝试别的路径。 

如果我们把包含所有解的解空间看作一棵树, 回溯其实是按照深度优先搜索的策略, 从root出发按深搜搜索解, 到某结点时, 符合条件就继续搜索, 不符合就返回上一个结点回溯。 

很自然地, 递归的思路正好合得上, 用一个类似level的字段标志搜索的深度:

```
def dfs(level, target, current):
	if current == target:
		return
	else:
		for i in range(low, high):
			if ok():
				dfs(level+1, target, ...)
			else:
				clear()
				
```

基本步骤:  
* 每一步要先处理return case, 如果匹配则return  
* 如果不匹配, 再使用一个循环, 循环内检测这一步是否正确, 正确则递归  
* 理下一步, 处理完成后, 回到初始状态, 供循环下一层使用  

单个解时, 可以找到return case立即返回, 多个解时通常需要遍历整个搜索树来找到所有符合要求的解

生成power set就是一个可以利用回溯来解决的[问题](https://leetcode.com/problems/subsets/)

```
class Solution:
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        if not nums:
            return [[]]
        ret = []
        self.dfs(ret, [], nums, 0)
        return ret
    def dfs(self, ret, temp, nums, start):
        ret.append(temp)
        for i in range(start, len(nums)):
            self.dfs(ret, temp+[nums[i]], nums, i+1)
```

典型的一段递归写法, 类似的问题均可以采用这种结构来处理, 不同处往往体现在条件分支的选择上面  


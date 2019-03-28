---
layout: post
title: 卡尔技能与排列组合
category: algorithm
tag : algorithm
---

### 卡尔

逛社区看到一帖子聊面试的时候问[卡尔技能组合](https://www.v2ex.com/t/546059?p=1), 跟dota相关必须进去瞅瞅, 题干:"如果你玩过dota2的话, 很简单一句话, n个球的卡尔有多少个技能"。在下世界前三卡尔(maybe, miracle, mingfei), 对卡尔的技能已经熟悉到不用过脑子就切的出来(尤其是qqwrv)。 所以3个球10个技能自然不说了, 下面讨论下n个球的情况。  

其实也没什么讨论的, 这个问题就是重复组合(combination with repetiton)的问题。 排列, 组合, 重复组合都是上学时学过的数学概念, 纯数学计算的话是很简单的, 这里讨论程序怎样实现。 

### dfs  

其实不管是排列还是组合都是直观的感觉就是一个递归的过程, back tracking跟dfs的思路都很直接:  

* 遍历数据源iterable, 加入当前的结果集, 继续向下层遍历, 当结果集符合要求时, 返回结果集。 

组合(combinations):  

```
def combinations_dfs(iterable, r):

    pool = tuple(iterable)
    n = len(pool)

    ret = []

    def dfs(curr, start, n, r):
        if r == 0:
            ret.append(curr)
            return
        for i in range(start, n):
            dfs(curr+[pool[i]], i+1, n, r-1)

    dfs([], 0, n, r)
    return ret
    
[['a', 'b'], ['a', 'c'], ['a', 'd'], ['b', 'c'], ['b', 'd'], ['c', 'd']]

```

排列(permutations):

```
def permutations_dfs(iterable, r):
    pool = tuple(iterable)
    n = len(iterable)

    ret = []
    def dfs(curr=None):
        if curr is None:
            curr = []

        if len(curr) == r:
            ret.append(curr)

        for i in range(0, n):
            if pool[i] in curr:
                continue
            else:
                dfs(curr+[pool[i]])

    dfs()
    return ret
   
[['a', 'b'], ['a', 'c'], ['a', 'd'], ['b', 'a'], ['b', 'c'], ['b', 'd'], ['c', 'a'], ['c', 'b'], ['c', 'd'], ['d', 'a'], ['d', 'b'], ['d', 'c']]

```

重复组合(combinations with replacement)  

```
def combinations_with_replacement_dfs(iterable, r):
    pool = tuple(iterable)
    n = len(pool)

    ret = []

    def dfs(curr, index, start):
        if index == r:
            ret.append(curr[:])
            return

        for i in range(start, n):
            curr[index] = pool[i]
            dfs(curr, index+1, i)

    dfs([[0] for i in range(r)], 0, 0)
    return ret
    
[['q', 'q', 'q'], ['q', 'q', 'w'], ['q', 'q', 'e'], ['q', 'w', 'w'], ['q', 'w', 'e'], ['q', 'e', 'e'], ['w', 'w', 'w'], ['w', 'w', 'e'], ['w', 'e', 'e'], ['e', 'e', 'e']]

```

`注意重复组合因为出现了重复元素, 所以单纯对list进行append已经不合适了, 需要按照index位置添加元素`




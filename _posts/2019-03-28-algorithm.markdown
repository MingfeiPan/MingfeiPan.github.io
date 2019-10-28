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

排列比组合的情况更多一些, 因为位置因素会有区别, 可以基于递归的思路想一下, 会容易理解: 

* 对于一个n个元素的序列(无重复)而言, 求他的全排列的问题, 可以分解为n个子问题: 依次让每个元素作为首元素, 剩余n-1个元素进行全排列, 直到结束
* 考察一个三个元素的例子: [a, b, c], 可以分解为三个字问题
	* a开头, 接上[b, c]的全排列
	* b开头, 接上[a, c]的全排列
	* c开头, 接上[a, b]的全排列
	* 然后依次递归

```
func permute(nums []int) [][]int {
    if len(nums) == 0 {
        return [][]int{}
    }
    ret := make([][]int, 0)
    bt(&ret, nums, []int{})
    return ret
}

func bt(ret *[][]int, nums []int, cur []int) {
    if len(nums) == 0 {
        // temp := append([]int{}, cur...)
        *ret = append(*ret, append([]int{}, cur...))
    }else {
        for i := 0; i < len(nums); i++ {
            temp := append([]int{}, nums[:i]...)
            tempcur := append([]int{}, cur...)
            
            // nums剔除当前位置元素, 递归剩下元素的permute,  cur加上当前i位置元素, go的复制操作有点日狗
            bt(ret, append(temp, nums[i+1:]...), append(tempcur, nums[i]))
        }
    }
}
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




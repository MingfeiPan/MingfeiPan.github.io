---
layout: post
title: Python set
category: python
tag : python
---


### set

list_unique = set(list_duplicate)  
set 可以去重, 也可以作用于tuple  

>set是一种集合,可以使用 in/not in, len   

##### set 可用操作  
`s.add(a)` 添加  
`s.remove(a)` 删除,如果不存在则引发 KeyError   
`s.discard(a)` 如果存在则删除  
`s.pop()` 删除并且返回 s 中的一个不确定的元素, 如果为空则引发 KeyError  
`s.clear()` 删除 s 中的所有元素

##### set 集合操作
###### 子集操作  
given two sets:  
`s = set([1,2,3,4,5])`  
`t = set([1,2,3])`

`s.issubset(t)` s是否为t的子集，相当于s<=t    
False  
`t.issubset(s)` #t是否为s的子集，相当于s>=t  
True  
`s<t`  
False  
`s>t`  
True  
`s==t`  
False  

###### 合集操作  
given two sets:  
`s = set([1,2])`  
`t = set([2,3])`  

并集  
`s.union(t)` or `s | t`  
交集  
`s.intersection(t)` or `s & t`  
差集  
`s.difference(t)` or `s - t`  
补集  
`s.symmetric_difference(t)` or `s ^ t`  

###### 附加操作
返回增加了 s 中元素后的 t
`s.update(t)` or `s |= t`  
返回只保留含有 t 中元素的 s  
`s.intersection_update(t)` or `s &= t`  
返回删除了 t 中含有的元素后的 s  
`s.difference_update(t)` or `s -= t`  
返回含有 t 或者 s 中有而不是两者都有的元素的 s  
`s.symmetric_difference_update(t)` or `s ^= t` 

返回 s 的一个浅复制  
`s.copy()`  
返回 s 的 hash 值  
`hash(s)`  







  




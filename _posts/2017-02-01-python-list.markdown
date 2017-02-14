---
layout: post
title: Python list
category: python
tag : python
---

需要做笔记的一些语法

### list

`li = [1,2,3,4,5]`

可以通过  
`del li[2]`  
来删除,删除后应该是  
`li = [1,2,4,5]`  
***
可用的语法:    
长度 len(x)  
连接 `[1,2,3]+[4,5,6] = [1,2,3,4,5,6]`   
重复 `['x']*4 = ['x','x','x','x']`  
exist `3 in [1,2,3]` True  
iteration `for i in [1,2,3]: print x`  
比较 `cmp(list1,list2)`  
`max(list)`  
`min(list)`  
tuple to list `list(tu)`
***
可操作方法:  
list.append(obj) 末尾添加新的元素  
list.count(obj) 统计obj出现次数  
list.extend(obj) 追加另一个list  
list.index(obj) 返回obj的第一个索引位置  
list.insert(index, obj) 插入到列表  
list.pop(obj=list[-1]) 移除某个元素,默认最后一个,返回此值  
list.remove(obj) 删除obj的第一个匹配项  
list.reverse() reverse list  
list.sort(key=None, reverse=False) 排序 key作为一个用于比较的关键字, reverse=True表示倒序排
sorted(list, key, reverse) 同排序

> a.sort() 会改变a b=sorted(a) 是安全的, 不会改变a的结构  

另外
  
> enumerate 可以用来遍历 list 
for i,v in enumerate(['tic', 'tac', 'toe']):  
	print (i,v)  
...  
...  
0 tic  
1 tac  
2 toe  
可以拿到index






  








 



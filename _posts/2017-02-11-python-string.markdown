---
layout: post
title: Python string
category: python
tag : python
---

### string  
`字符串（即不能修改的字符list`
 
`str = "Hello My friend"`     字符串是一个整体。如果你想直接修改字符串的某一部分，是不可能的。但我们能够读出字符串的某一部分。  
子字符串的提取  
`str[:6]`  
字符串包含判断操作符：in，not in  
`"He" in str`  
`"she" not in str`  
  
### 可用方法
`S.find(substring, [start [,end]])` 可指范围查找子串，返回索引值，否则返回-1  
`S.rfind(substring,[start [,end]])` 反向查找  
`S.index(substring,[start [,end]])` 同find，只是找不到产生ValueError异常  
`S.rindex(substring,[start [,end]])` 同上反向查找  
`S.count(substring,[start [,end]])`  返回找到子串的个数  
  
`S.lowercase()`  
`S.capitalize()`      首字母大写  
`S.lower()`           转小写  
`S.upper()`           转大写  
`S.swapcase()`        大小写互换  
  
`S.split(str, ' ')`   将string转list，以空格切分  
`S.join(list, ' ')`     将list转string，以空格连接  
  
`len(str)`               串长度  
`cmp("my friend", str)`  字符串比较。第一个大，返回1  
`max('abcxyz')`          寻找字符串中最大的字符  
`min('abcxyz')`            寻找字符串中最小的字符  
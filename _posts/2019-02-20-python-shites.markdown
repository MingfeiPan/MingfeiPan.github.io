---
layout: post
title:  python shites
category: python
tag : python
--- 

一个[python之坑](https://github.com/satwikkansal/wtfpython/)的项目的个人记录

### mutable对象作为默认参数

```
def foo(l=[]):
		l.append(1)
		print(l)

foo()
#[1]
foo()
#[1,1]
```

默认参数会在函数定义时求值, 另一个例子:  

```
import time

def foo(now=time.time()):
	print(now)


foo()
#1550650341.422179
time.sleep(1)
foo()
#1550650341.422179

```

解决办法:使用None作为默认参数, 在函数内验证  

```
import time

def foo(now=None):
	if now is None:
		now = time.time()
	print(now)


foo()
time.sleep(1)
foo()
	
```

### 生成一个元素是可变对象的序列

```
a = [[]] * 10

print(a)
#[[], [], [], [], [], [], [], [], [], []]

a[0].append(1)
print(a)
#[[1], [1], [1], [1], [1], [1], [1], [1], [1], [1]]

```

```
a = [[] for _ in range(10)]

print(a)
#[[], [], [], [], [], [], [], [], [], []]

a[0].append(1)
#[[1], [], [], [], [], [], [], [], [], []]
print(a)
```

原因:python中list是可变对象, 前一种写法每一个list都指向同一个对象, 所以同时发生了改变  


### 访问列表时修改列表 

```
l = [1,2,3,6,5,4]

def foo(l):
	for i, v in enumerate(l):
		if v % 3 == 0:
			del l[i]

foo(l)
print(l)
#[1, 2, 6, 5, 4]
```

3被删除之后i还是会递增, 于是错过了6这个元素, 这个问题目前看如果是用枚举迭代这种形式去删除是会出问题的  

```
def bar(l):
	return [i for i in l if i % 3 is not 0]

print(bar(l))
#[1, 2, 5, 4]
```

重新生成list是个可以考虑的选择   

### is or == 

==运算符比较两个对象的值(_\_\eq__方法), is比较对象的标识(对象的引用	), 在cpython中, id()返回对象的内存地址, 可以视作标识的整数表示  	

### tuple的不可变性  

tuple的不可变指的是tuple数据结构的物理内容, 即保存的引用不可变, 元组的值会随着内部保存的引用的可变对象变化而变化, 元组中不可变的是元素的标识(cpython中的id())

### 由字符串驻留(String interning, In computer science, string interning is a method of storing only one copy of each distinct string value, which must be immutable)带来的一些逗逼影响(虽然完全不会影响到使用)

```
>>> s1 = 'abc'
>>> s2 = 'abc'
>>> s1 is s2
True
>>> id(s1)
4380131032
>>> id(s2)
4380131032

```

cpython会在某些字符串或者小的整形上使用这个优化措施来防止重复创业一些热门内容, 比如0,1,42,'abc'  


###  由窥孔优化, (peephole optimization)[https://en.wikipedia.org/wiki/Peephole_optimization] 带来的影响  

```
>>> 'a' * 20 is 'aaaaaaaaaaaaaaaaaaaa'
True
>>> 'a' * 21 is 'aaaaaaaaaaaaaaaaaaaaa'
False

简单说就是python编译时对过长的常量进行了折叠以节省时间, 要求string长度不能大于20, (相关代码)[https://github.com/python/cpython/blob/4508bc37dd80c71adfaa0925a67c438389817076/Python/peephole.c#L288]

```



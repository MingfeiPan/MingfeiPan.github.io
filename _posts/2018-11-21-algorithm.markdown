---
layout: post
title:  外部排序的一些笔记
category: algorithm
tag : algorithm
--- 

逛社区的时候看到一个[帖子](https://www.v2ex.com/t/508900)里在讨论k-way merge的问题, 给出n个节点, 每个节点有长度为m的数组, 对m\*n个数进行排序。 最直接的方法就是拼成一个数组然后对单个数组排序就好了, 前提是m\*n个可以直接放入内存中, 当然这类问题讨论时往往是作为[外部排序](https://en.wikipedia.org/wiki/External_sorting)讨论的, 也就是所谓m*n个数已经没有办法直接放入内存中而需要借助外存(硬盘)来处理排序。  

#### k-way merge

这类问题基本是通过先各自排序各自数组, 再通过merge sort来处理, 上面那个题也是一样的。 这类问题也叫做[k-way merge](https://en.wikipedia.org/wiki/K-way_merge_algorithm)。 当key为2时, 简化为我们日常接触的2路归并排序。 wiki上总结了几种k-way merge的方法, 其中Direct k-way merge跟 Heap 这两种比较常用的方法都可以做到时间复杂度O(nlogk)。 考虑帖子里不让用归并, 所以我写了一份heap的实现。 

#### heap

基本需要注意的是:  

* 每个数组分别排序  
* 维护一个最小堆, 其中每个元素是一个tuple(数组编号, 当前元素下标, 元素值), 堆由元素值排序  
* 每次取出堆顶元素, 然后把这个元素数组编号中下一位的元素入堆, 直到排完  

需要注意的是, 很多语言都有内置的堆实现, 但是所用的元素就是一个数, 我们这里需要改进为一个数组(tuple), 下面是实现的代码:  

```
# a heap structure designed for external sort

import random
import sys
import collections

item = collections.namedtuple('item', ['listname', 'index', 'value'])

class Heap:
	def __init__(self):
	    self.h = []
	    self.currsize = 0

	def leftChild(self,i):
		if 2*i+1 < self.currsize:
			return 2*i+1
		return None

	def rightChild(self,i):
		if 2*i+2 < self.currsize:
			return 2*i+2
		return None

	def maxHeapify(self,node):
		if node < self.currsize:
			m = node
			lc = self.leftChild(node)
			rc = self.rightChild(node)
			if lc is not None and self.h[lc].value < self.h[m].value:
				m = lc
			if rc is not None and self.h[rc].value < self.h[m].value:
				m = rc
			if m!=node:
				temp = self.h[node]
				self.h[node] = self.h[m]
				self.h[m] = temp
				self.maxHeapify(m)


	def getTop(self):
		if self.currsize >= 1:
			me = self.h[0]
			temp = self.h[0]
			self.h[0] = self.h[self.currsize-1]
			# self.h[self.currsize-1] = temp
			del self.h[self.currsize-1]
			self.currsize -= 1
			self.maxHeapify(0)
			return me
		return None

	def insert(self,data):
		self.h.append(data)
		curr = self.currsize
		self.currsize+=1
		while self.h[curr].value < self.h[curr//2].value:
			temp = self.h[curr//2]
			self.h[curr//2] = self.h[curr]
			self.h[curr] = temp
			curr = curr//2

	def display(self):
		print(self.h)

def main():

	listsize = 1000
	listcount = 1000
	l = [[random.randrange(1, 10000) for i in range(listsize)] for index in range(listcount)]
	list(map(lambda a : a.sort(), l))

	h = Heap()
	for i in range(len(l)):
		h.insert(item(i, 0, l[i][0]))
	output = []
	flag = 0
	while flag < listcount * listsize:
		temp = h.getTop()
		output.append(temp.value)
		if temp.index < listsize - 1:
			h.insert(item(temp.listname, temp.index+1, l[temp.listname][temp.index+1]))
		flag += 1
	print(len(output))


if __name__=='__main__':
	main()


```

ps:这里并没有写入文件读取文件的步骤, 还是在内存中模拟的   
---
layout: post
title: 快慢指针的一些笔记
category: algorithm
tag : linkedlist
---

### 快慢指针  
快慢指针在解决一些链表问题时, 非常好用, 通常是用两个移动速度不同的指针来执行, 例如一个步长2, 一个步长1  

经典场景包括判断链表是否有环, 环的入口位置, 找出链表中位数, 还有很多需要遍历求长度求位置的场景下, 快慢指针都取巧省掉便利的环节, 直接找到位置  

首先来看判断有环, 下面是一段核心代码    

```
struct ListNode *fast, *slow;
fast = head;
slow = head;
while(fast && fast != slow)
{
	slow = slow->next;
	fast = fast->next ? fast->next->next : NULL;
}

return fast ? true : false;
```  

思考一下过程, 有环时, 两指针在环上运动时, fast速度为1, slow相对静止, 所以是一定会相遇, 反之则链表无环  

然后下一步, 如果有环之后, 如何判断入口, 如图我们假设a点为起点, b点为环入口点, c点为第一次相遇点, 这是slow跑了n, 而fast跑了2n  

<img src="/img/in-post/l1.jpg">

>这时a到c的距离为n, fast的2n减去a到c也就是n的距离, 实际上在环中从c出发走一圈的距离就是n, 也就是l3+l2=n=l1+l2, 所以l3=l1, 我们只需要让一个点从起点a出发, 另一个点从第一次相遇点c出发, 同步移动, 等第一次相遇时就是环入口点b  


最后关于上面另一个场景, 可以参考leetcode no.19(Remove Nth Node From End of List), 借助快慢指针, 可以省去遍历, 实现one pass的方案, 这里就不详细描述了  






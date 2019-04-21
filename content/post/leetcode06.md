---
title: "Leetcode06"
date: 2019-03-06T23:58:06+08:00
archives: "2019"
tags: [leetcode]
author: Mr King
---

### Merge K sorted link list

还是昨天的合并　K 个有序链表，今天去社区看了下，还有更简单的解决方案，时间复杂度和空间复杂度更好，大概的思路是用 Python 的最小堆来实现，每次从堆里弹出最小元素，然后最近一次哪个链表出了最小元素就把下一个塞进堆里，很高效，很简洁，元祖（tuple）的运用是这个实现的点睛之笔，下面贴出代码

```
def mergeKLists(self, lists):
	from heap import heappop, heapify, heapreplace
	dummy = node = ListNode(0)
	
	# 下面这一步很赞
	h = [(n.val, n) for n in lists if n]
	
	# n 转　minheap
	heapify(h)
	while(h):
		# 取　堆里最小的值
		v, n = h[0]
		if n.next is None:
			heappop(h)
		else:
			＃　弹出最小值，并把同一链表的下一个最小值放进堆中
			heapreplace(h, (n.next.val, n.next))

		node.next = n
		node = node.next

	return dummy.next
```

### Conclusion

想起了大佬 Linus 的那句话 

> "Bad programmers worry about the code. Good programmers worry about data structures and their relationships."
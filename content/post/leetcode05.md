---
title: "Leetcode05"
date: 2019-03-05T23:37:00+08:00
archives: "2019"
tags: []
author: Mr King
---

### Merge k Sorted Lists

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

#### Example

```
Input:
[
  1->4->5,
  1->3->4,
  2->6
]
Output: 1->1->2->3->4->4->5->6
```

合并n个有序链表，第一眼看到觉的很简单，想怎么实现的时候头大，大体思路是全部都放到一个list里，然后排序list，再转链表
```
class Solution(object):
    def mergeKLists(self, lists):
        """
        :type lists: List[ListNode]
        :rtype: ListNode
        """
        merged_list = []
        for head in lists:
        	while head:
        		merged_list.append(head.val)
        		head = head.next

        sorted_list = sorted(merged_list)
        root = n = ListNode(0)
        for i in sorted_list:
        	n.next = ListNode(i)
        	n = n.next

        return root.next

```
发现当思路清晰并且语法熟悉的情况下，实现时间缩短了很多，加油！
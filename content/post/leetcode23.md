---
title: "Leetcode23"
date: 2019-04-30T00:28:19+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

#### Remove Duplicates from Sorted List

Given a sorted linked list, delete all duplicates such that each element appear only *once*.

**Example 1:**

```
Input: 1->1->2
Output: 1->2
```

**Example 2:**

```
Input: 1->1->2->3->3
Output: 1->2->3
```

第一想法是转 list 再转链表

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def deleteDuplicates(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        s = []
        while head:
            if head.val not in s:
                s.append(head.val)
            head = head.next
        
        dummy = current = ListNode(None)
        for i in s:
            current.next = ListNode(i)
            current = current.next
        
        return dummy.next
```

回头再审下题，给了一个排好序的链表，这题考的操作链表的能力

```
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def deleteDuplicates(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        current = head
        
        while current is not None and current.next is not None:
            if current.val == current.next.val:
                current.next = current.next.next
            else:
                current = current.next
            
        return head
```
考虑清楚后再动手，比较快，着急只会起反作用

![duplicate](https://hurryking.github.io/img/duplicate.png)

难度降低后快了很多，可是成就感低了很多

>慢就是快
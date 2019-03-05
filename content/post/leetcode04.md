---
title: "Leetcode04"
date: 2019-03-05T01:18:38+08:00
archives: "2019"
tags: []
author: Mr King
---

### Add Two Numbers
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

#### Example:

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
```

Explanation: 342 + 465 = 807.


两数相加，以链表形式表示(主要考察链表的熟悉度)，我还是用了栈，大体思路就是先转成栈，运算完成再转成链表，对链表还是不熟悉，得多加练习了
有时候思路很清晰，但是实现起来就是特别慢，还是基本功没打好，得加油了

```
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        
        q1 = []
        while l1:
            q1.append(l1.val)
            l1 = l1.next

        q2 = []
        while l2:
            q2.append(l2.val)
            l2 = l2.next
        
        larger = q2 if len(q2) >= len(q1) else q1
        tinyer = q1 if larger == q2 else q2
        larger.reverse()
        tinyer.reverse()
        
        res = []
        iscarry = False
        for i in range(len(larger)):
            d1 = larger.pop() if larger else 0
            d2 = tinyer.pop() if tinyer else 0

            count = d1 + d2
            if iscarry:
                count += 1

            if (count < 10):
                res.append(count)
                iscarry = False
            else:
                res.append(count-10)
                iscarry = True
        
        if iscarry:
            res.append(1)
        
        head = sum_list = ListNode(None)
        for i in range(len(res)):
            sum_list.val = res[i]
            
            if i < len(res)-1:
                sum_list.next = ListNode(None)
                sum_list = sum_list.next

        return head
```
#### 20190305更新
今天去讨论区看了下，有个比较清爽的实现，自己写一边，吸收下，还有刚才看了下排版，昨天太过着急睡觉没检查，乱套了，下次需要注意了
```
class Solution(object):
    def addTwoNumbers(self, l1, l2):
        carry = 0
        # 头节点为哑节点
        root = n = ListNode(0)
        while l1 or l2 or carry:
            \# 如果最后一次进位了还需要再往后插入一个１(这个实现是实在是棒！)
            d1 = d2 = 0
            if l1:
                d1 = l1.val
                l1 = l1.next
            
            if l2:
                d2 = l2.val
                l2 = l2.next
                
            carry, val = divmod(d1+d2+carry, 10)
            
            n.next = ListNode(val)
            n = n.next
            
        return root.next

```
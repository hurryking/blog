---
title: "Leetcode07"
date: 2019-03-07T22:48:41+08:00
archives: "2019"
tags: [leetcode]
author: Mr King
---

### Reverse Nodes in k-Group

Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.

k is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of k then left-out nodes in the end should remain as it is.

#### Example:

Given this linked list: ``1->2->3->4->5``

For k = 2, you should return: ``2->1->4->3->5``

For k = 3, you should return: ``3->2->1->4->5``

#### Note:

* Only constant extra memory is allowed.
* You may not alter the values in the list's nodes, only nodes itself may be changed.

题目意思是　反转 k　个一组的节点(暂时想不到好的翻译)，给 5 个节点要求 2　个节点分一组来进行反转，最后再拼接成链表返回，要求只能使用固定内存(不允许动态申请内存)，不能修改节点的值，也就是只能靠改变指向来解决问题。

憋了半个小时，还是没想出办法，去评论区看了下别人的思路，下面是实现

```　　
class Solution(object):
    def reverseKGroup(self, head, k):
        dummy = jump = ListNode(0)
        dummy.next = l = r = head

        while True:
            count = 0
            while r and count < k:   # 使用　r 界定链表的范围
                r = r.next
                count += 1
            if count == k:  # 计数到　k 就反转切分完成的链表
                pre, cur = r, l
                for _ in range(k):
                    cur.next, cur, pre = pre, cur.next, cur  # 反转链表标准操作
                jump.next, jump, l = pre, l, r  # 链接两个链表组
            else:
                return dummy.next
```
研究了一个小时，笔在本上画了 N 遍，还是没理解链接两个链表组那块的代码``jump.next, jump, l = pre, l, r``，看着好迷炫，今天是膨胀了选了个难度　hard，明天接着消化吧。

感觉代码就是抽象和逻辑的艺术，逻辑能力差不行，抽象能力差也不行。
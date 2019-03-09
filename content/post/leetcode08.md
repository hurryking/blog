---
title: "Leetcode08"
date: 2019-03-09T14:55:08+08:00
archives: "2019"
tags: []
author: John SMITH
---

### Reverse K-group Node

Let's continue the last problem.
Here is the question I don't understanind last time.

1. How does the dummy.next change? Accurately When did the dummy.next change?
	
	Here is the answer.(How does the dummy.next change?)[https://leetcode.com/problems/reverse-nodes-in-k-group/discuss/11491/Succinct-iterative-Python-O(n)-time-O(1)-space/168139]
	This is related about how the python assign the variable. It's pretty hard to understand for people first time.

2. How the program connect two K-group?
	
	Let's tear ``jump.next, jump, l = pre, l, r`` apart.
	```
	jump.next = pre # this line just do the dummy.next work in the first iteration, second iteartion this line run in vain, this is the part someone really confused about.
	jump = l # connect the prev K-group an next K-group
	l = r # move the K-group boundary
	```

Let's take a look again.
```
class Solution(object):
    def reverseKGroup(self, head, k):
        dummy = jump = ListNode(0)
        dummy.next = l = r = head

        while True:
            count = 0
            while r and count < k:
                r = r.next
                count += 1
            if count == k:
                pre, cur = r, l
                for _ in range(k):
                    cur.next, cur, pre = pre, cur.next, cur
                jump.next, jump, l = pre, l, r
            else:
                return dummy.next
```
Follwing is my draft about this problem Haha.
<img src="https://user-gold-cdn.xitu.io/2019/3/9/169626eddc4b3a16?w=1080&h=1920&f=jpeg&s=223910" style="height:250px;">

Hope you guys can understand this one.

### Conclusion

这道题看了一天，最终还是看懂了，有些感悟写在下面

> Someone born with a silver spoon in the month, someone not, the only thing you can do is accept that, and moveing on your fuking way, there must be something great waiting for you, maybe a fuking gold mine.
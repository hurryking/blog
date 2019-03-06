---
title: "Leetcode01"
date: 2019-02-28T02:28:38+08:00
archives: "2019"
tags: []
author: Mr King
---

### Valid Square

Given the coordinates of four points in 2D space, return whether the four points could construct a square.

The coordinate (x,y) of a point is represented by an integer array with two integers.

##### Example:
```
Input: p1 = [0,0], p2 = [1,1], p3 = [1,0], p4 = [0,1]
Output: True
```
Note:
All the input integers are in the range [-10000, 10000].
A valid square has four equal sides with positive length and four equal angles (90-degree angles).
Input points have no order.

看完题目，回家想了一路，大概思路是求两点之间的距离，如果是正方形两点之前的距离只有个值，暴力破解两层循环，代码不堪入目

看评论区有个两行搞定的
```
class Solution(object):
    def validSquare(self, p1, p2, p3, p4):
        """
        :type p1: List[int]
        :type p2: List[int]
        :type p3: List[int]
        :type p4: List[int]
        :rtype: bool
        """
        points = [p1, p2, p3, p4]
        return len({(a[0]-b[0])**2 + (a[1]-b[1])**2 for a in points for b in points}) == 3 and \
               len(set(map(tuple, points))) == 4
```
看完惊了个呆，这是什么骚操作，集合推导式套两层for循环，查了下资料发现
```
{(a[0]-b[0])**2 + (a[1]-b[1])**2 for a in points for b in points}
＃相当与
for a in points:
    for b in points:
        a[0]-b[0])**2 + (a[1]-b[1])**2
#然后集合去重
len(set(map(tuple, points)))
＃这句的意思是看有没有重复的点
```
代码的大概意思也是求两点间的距离，不过包含自己到自己，所以有三个值

涨姿势了，不过话说回来这也是暴力破解，回头再找找时间复杂度好点的


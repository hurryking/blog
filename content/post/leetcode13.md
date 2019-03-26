---
title: "Leetcode13"
date: 2019-03-27T00:46:43+08:00
archives: "2019"
tags: ['leetcode13']
author: John SMITH
---

### Jump Game

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**Example 1:**

```
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
Example 2:
```

```
Input: [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum
             jump length is 0, which makes it impossible to reach the last index.
```

给出一组非负整数数组，初始化位置在数组的第一个位置，数组中的每个元素代表你所在位置能跳跃的最大长度，判断你是否能到达最后一个数组。

rest 代表剩余步数，遍历如果当前位置的最大布数比剩余布数大就保留当前位置的布数，剩余布数为 0 并且没有到达尾部的时候返回 False

```
class Solution(object):
    def canJump(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        rest = 0
        for i in xrange(len(nums)):
            rest = max(rest-1, nums[i])
            if rest == 0 and i < len(nums)-1:
                return False
            
        return True
```

### 结束语

> There is no't a best way, only a better way.
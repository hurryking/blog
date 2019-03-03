---
title: "Leetcode03"
date: 2019-03-03T18:23:20+08:00
archives: "2019"
tags: []
author: Mr n
---

### Palindrome Number
Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

#### Example 1:
```
Input: 121
Output: true
```
#### Example 2:
```
Input: -121
Output: false
```
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
Example 3:
```
Input: 10
Output: false
```
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.

如何判断是否是回文数字，取取对应位置的数字对比如果遇到不相等就不是回文，真的是脑子不够用了，废了老半天劲才写出来，这么简单的问题，下面是代码(比较糙)
```
#!/usr/bin/env python
class Solution(object):
    def isPalindrome(self, x):
        """
        :type x: int
        :rtype: bool
        """
        x = str(x)
        length = len(x)
        size = int(length/2) + 1

        for i in range(0, size):
        	if x[i:i+1] != x[length-(i+1):length-i]:
        		return False

        return True

x=1221
s = Solution()
print(s.isPalindrome(x))
```

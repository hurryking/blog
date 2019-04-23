---
title: "Leetcode03"
date: 2019-03-03T18:23:20+08:00
archives: "2019"
tags: [leetcode]
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

```python
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
然后去评论区看了下，又出现了一行解决的，思路是把数字转字符串然后反转，和没反转前的对比一样就是回文，这个思路比我的好，并且发现了一个骚操作，一行反转字符串
```python
class Solution(object):
    def isPalindrome(self, x):
        """
        :type x: int
        :rtype: bool
        """

        return str(x) == str(x)[::-1]

```
还有除法算倒过来的数然后进行整数对比的，如果要研究时间复杂度，个人觉的上面的一行时间复杂度不好，还得再深入研究下。
---
title: "Leetcode18"
date: 2019-04-19T23:05:01+08:00
archives: "2019"
tags: []
author: John SMITH
---



#### ZigZag Conversion

The string `"PAYPALISHIRING"` is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

```
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: `"PAHNAPLSIIGYIR"`

Write the code that will take a string and make this conversion given a number of rows:

```
string convert(string s, int numRows);
```

**Example 1:**

```
Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"
```

**Example 2:**

```
Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:

P     I    N
A   L S  I G
Y A   H R
P     I
```



Z 字形字符串转换，要求把给定字符串按照指定的行数进行转换，比如说 "PAYPALISHIRING" 要求分 3 行进行 Z 字形转换

属于找规律的题，把字符串先按照行数分 n 个list，然后按照规律模拟步进，最后再把三行合并到一起。

```python
class Solution(object):
    def convert(self, s, numRows):
        """
        :type s: str
        :type numRows: int
        :rtype: str
        """
        if numRows == 1 or numRows > len(s):
            return s;
        
        rows = [""]*numRows;
        step = 1
        index = 0
        for letter in s:
            rows[index] += letter
            
            if index == 0:
                step = 1
            elif index == numRows - 1:
                step = -1
            
            index += step
        
        return ''.join(rows)
```



抽象能力是区分工程师能力的分水岭，好的工程师在创造概念，基于概念进行编程，坏的工程师在堆叠逻辑，写出来一堆又臭又长的东西。

>今天看了左耳朵耗子关于 996 的文章，作为员工能为企业提供的最大价值就是以最小的代价解决问题，任何行业都一样，你能解决的问题别人解决不了这就是你的价值，努力只是众多要条件中的一种，不是必要条件 996 是个双败的制度，并不能提高工作效率。
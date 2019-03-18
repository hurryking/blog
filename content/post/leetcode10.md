---
title: "Leetcode10"
date: 2019-03-19T00:26:56+08:00
archives: "2019"
tags: [leetcode]
author: Mr King
---

### Generate Parentheses

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given n = 3, a solution set is:

```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```

给出数字n，列出所有有效的括号组合
此题和上次的排列组合都可以用回溯算法(DFS)解决
回溯算法３要点

1. 选择
选择'('或者‘)’
2.　限制
必须是有效的括号 括号闭合之前必须先开放 括号数有限(n)

3.　目标
排列好所有括号

下面是代码
```
class Solution(object):
    def generateParenthesis(self, n):
        """
        :type n: int
        :rtype: List[str]
        """
        res = []
        self.dfs(n, n, '', res)
        
        return res
    
    def dfs(self, left, right, path, res):
    	# 限制
        if left > right or left < 0 or right < 0:
            return;
        # 符合条件的组合
        if left == 0 and right == 0:
            res.append(path)
            return;
        
        # 深度优先遍历
        self.dfs(left-1, right, path+'(', res)
        self.dfs(left, right-1, path+')', res)
```

附图一张，仅供参考^_^

![DFS](https://hurryking.github.io/img/Parentheses.png)


### Conclution

养了半年的花今天开花了
![DFS](https://hurryking.github.io/img/myflower.jpeg)
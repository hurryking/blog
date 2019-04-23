---
title: "Leetcode16"
date: 2019-04-09T22:10:48+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

### N-Queens

The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.

![n-queue](https://assets.leetcode.com/uploads/2018/10/12/8-queens.png)

Given an integer n, return all distinct solutions to the n-queens puzzle.

Each solution contains a distinct board configuration of the n-queens' placement, where 'Q' and '.' both indicate a queen and an empty space respectively.

**Example:**

```
Input: 4
Output: [
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above.
```

#### 历史渊源(wikipedia)

八皇后问题最早是由西洋棋棋手马克斯·贝瑟尔（Max Bezzel）于1848年提出。第一个解在1850年由弗朗兹·诺克（Franz Nauck）给出。并且将其推广为更一般的n皇后摆放问题。诺克也是首先将问题推广到更一般的n皇后摆放问题的人之一。

在此之后，陆续有数学家对其进行研究，其中包括高斯和康托，1874年，S.冈德尔提出了一个通过行列式来求解的方法，这个方法后来又被J.W.L.格莱舍加以改进。

1972年，艾兹格·迪杰斯特拉用这个问题为例来说明他所谓结构化编程的能力。他对深度优先搜索回溯算法有着非常详尽的描述。

#### 解题


```python
def solveNQueens(self, n):
    res = []
    self.dfs([-1]*n, 0, [], res)
    return res
 
# nums 是一个一维度数组，[1, 3, 0, 2]　意思是第一个 queen 放在第一列，第二个 queen 放在第三列，以此类推
def dfs(self, nums, index, path, res):
    if index == len(nums):
        res.append(path)
        return  # 回溯
    for i in xrange(len(nums)):
        nums[index] = i
        if self.valid(nums, index):  # pruning
            tmp = "."*len(nums)
            self.dfs(nums, index+1, path+[tmp[:i]+"Q"+tmp[i+1:]], res)

# 第 n 个 quuen 是否能被放在当前列
def valid(self, nums, n):
    for i in xrange(n):
        if abs(nums[i]-nums[n]) == n -i or nums[i] == nums[n]:
            return False
    return True
```

还是有些地方不能理解，需要回头再仔细琢磨下。

### conlusion

最近状态有些低迷，又开始了懒惰，还有些迷茫，不知道坚持下去的意义是什么，明天继续努力吧。

> 自律是自由的前提，这世上没有绝对的自由。
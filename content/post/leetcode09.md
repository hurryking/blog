---
title: "Leetcode09"
date: 2019-03-14T00:06:45+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

### 46. Permutations

Given a collection of distinct integers, return all possible permutations.

#### Example:
```
**Input**: [1,2,3]
**Output**:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

给出一组不重复的整数，返回所有可能的排列组合

深度优先遍历解法DFS
```
class Solution(object):
    def permute(self, nums):
        res = []
        self.dfs(nums, [], res)
        return res

    def dfs(self, nums, path, res):
        if not nums:
            res.append(path)
            # return # backtracking
        for i in xrange(len(nums)):
            self.dfs(nums[:i]+nums[i+1:], path+[nums[i]], res)

```
![dfs](https://hurryking.github.io/img/permutations.png)
相当于一棵树分了n叉,　每个子树都是除了自己以外的节点的排列组合，然后再往下分(n-1)叉，分(n-2)叉
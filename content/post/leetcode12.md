---
title: "Leetcode12"
date: 2019-03-25T22:26:57+08:00
archives: "2019"
tags: ['leetcode12']
author: Mr King
---

### Subsets

Given a set of **distinct** integers, nums, return all possible subsets (the power set).

**Note**: The solution set must not contain duplicate subsets.

**Example:**

```
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

给出一组不重复的整数，返回所有子集

[what is power set](https://www.mathsisfun.com/sets/power-set.html) 

回溯解法(DFS)

回溯算法三要素

1. 选择

可以作出哪些选择

2. 约束

不能做哪些选择

3. 目标

最终要达成的目标

```
class Solution:
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        res = []
        self.dfs(0, [], nums, res)
        
        return res
        
    def dfs(self, index, path, nums, res):
        res.append(path)
        for i in xrange(index, len(nums)):
            self.dfs(i+1, path+[nums[i]], nums, res)
```

![subset](https://hurryking.github.io/static/img/SubSet.png)

还有一种位操作的解法，在[what is power set](https://www.mathsisfun.com/sets/power-set.html)里有提到位表示的部分，下篇补充下。

### Conclusion

> 有时候是坚持了才能看见希望，不是看见希望了才坚持
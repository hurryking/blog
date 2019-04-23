---
title: "Leetcode19"
date: 2019-04-21T16:42:02+08:00
archives: "2019"
tags: [leetcode]
author: Mr King
---


#### Permutation II

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

*Example:*

```
Input: [1,1,2]
Output:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

排列组合，给出一组可能重复的数字，求所有可能的排列组合。

之前有刷过一道，给出一组不可能重复的数字求排列组合的问题，[Permutation](https://hurryking.github.io/2019/03/leetcode09/), 第一反应就是套用之前的解法加上判断重复的逻辑。

```python
class Solution(object):
    def permuteUnique(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        res = []
        self.dfs(nums, [], res)
        
        return res
    
    def dfs(self, choices, path, res):
        if not choices:
            res.append(path)
            return;
        
        for i in range(len(choices)):
        	# 此处 i > 0 可以反思下为什么，我在此处踩坑了
        	if i > 0 and choices[i] == choices[i-1]:
                continue
            
            self.dfs(choices[:i]+choices[i+1:], path+[choices[i]], res)

```

如何判重是这里最重要的逻辑，当选中的选项和上一个选项一样就重复了，跳过此步选择

从这道题中看出递归是个比较难理解的概念，当递归的时候程序做了什么，这是理解递归很重要的一点，很多人以电影 <<盗梦空间>>　为例，我觉得确实很合适，递归的时候程序保留了上个执行环境(包括函数参数)，所以在递归返回的时候程序才能知道现在在哪一层，不至于迷失，当然递归终结条件也是很重要的，不可缺失。

汉诺塔问题是一个比较经典的递归算法的问题。

>
人生会面临很多个选择，最终这些选择决定了你是谁，所以做好生活中的每个选择，成为那个你想成为的人。
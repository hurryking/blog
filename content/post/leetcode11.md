---
title: "Leetcode11"
date: 2019-03-22T01:18:08+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

### 3Sum

Given an array ``nums`` of n integers, are there elements a, b, c in ``nums`` such that a + b + c = 0? Find alleft unique triplets in the array which gives the sum of zero.

#### Note

The solution set must not contain duplicate triplets.

#### Example

```
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

给一个数组，找出其中 3 数相加和为 0 的全部组合

本题没有思路，最终从讨论区得到的思路，下面是代码

```
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        res = []
        nums.sort()
        for i in xrange(len(nums)-2):
            if i > 0 and nums[i] == nums[i-1]:
                continue
            left, right = i+1, len(nums)-1
            
            while left < right:
                s = nums[i] + nums[left] + nums[right]
                if s < 0:
                    left +=1 
                elif s > 0:
                    right -= 1
                else:
                    res.append((nums[i], nums[left], nums[right]))
                    while left < right and nums[left] == nums[left+1]:
                        left += 1
                    while left < right and nums[right] == nums[right-1]:
                        right -= 1
                    left += 1 
                    right -= 1
        return res
```

先从小到大排序，然后从头开始遍历，有　left 和　right 两个指针，把当前值与 left right 相加，小于　0 就把 left 右移，大于　0 就把　right 左移，等于　0 就把组合存起来，去下重然后继续

来人，上草图！

![草图](https://hurryking.github.io/img/3Sum.png)

### Conclusion

花又开了几朵，屋里闻起来真香
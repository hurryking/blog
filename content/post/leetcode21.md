---
title: "Leetcode21"
date: 2019-04-24T22:24:36+08:00
archives: "2019"
tags: []
author: John SMITH
---

#### 3Sum Closest

Given an array `nums` of *n* integers and an integer `target`, find three integers in `nums` such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution.

**Example:**

```
Given array nums = [-1, 2, 1, -4], and target = 1.

The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

3数之和，给出一组整数和一个目标数，找出3个整数，它们的和最接近目标数，返回三数之和，你可以假设给出的数组中只有一个解。

这道题是 [3Sum](<https://hurryking.github.io/2019/03/leetcode11/>) 的扩展，思路是一样的用双指针

```
class Solution(object):
    def threeSumClosest(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        nums.sort()
        res = sum(nums[:3])
        
        for current in range(len(nums)-2):
        	left, right = current+1, len(nums)-1

        	while left < right:
        		s = nums[left] + nums[right] + nums[current]
        		if abs(s - target) < abs(res - target):
        			res = s

        		if s < target:
        			left += 1
        		elif s > target:
        			right -= 1
        		else:
        			return s

        return res
```

res 存着目前最符合条件的结果

每次迭代会和本次迭代的结果对比如果差距更小则更新，这里 abs 来求差距，如果 迭代中遇到和 target 一致的结果直接返回，因为题目中说到了只有一个解，无疑 0 差距最接近答案。

发现最近的执行力开始变强了，希望保持这种状态。

有时候选择大于努力，不要怨天尤人，打好自己手里的牌。

>遇到质疑的时候，不要着急反驳，用实力来证明自己。
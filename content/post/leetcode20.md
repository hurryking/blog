---
title: "Leetcode20"
date: 2019-04-23T23:36:09+08:00
archives: "2019"
tags: [leetcode]
author: Mr King
---

#### Two Sum

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution, and you may not use the *same* element twice.

**Example:**

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```



给出一组整数，返回两数之和相加为指定数的索引。

假设输入中只有一个解，最好不要重复使用一个元素两次

第一种解法 brute force

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        for i in range(len(nums)):
            for j in range(len(nums[i+1:])):
                if nums[j+i+1] + nums[i]== target:
                    return [i, j+i+1]
        
        return [1, -1]
```

第二种解法 Two-pass Hash Table

简单来说，遍历的时候检查下当前元素有没有互补元素，没有就把和当前的元素互补的元素放在一个字典里，用空间换时间

比如说 输入是 [1, 3, 5, 2]  3，遍历第一个元素的时候，把 [3-1] : 0 放到字典里备用，遍历第二遍把 [3-3] : 1 放进字典里备用，第三遍把 [3-5] : 2放进字典里备用，第四遍 2在字典里说明有匹配的元素，直接返回匹配元素的索引和自己的索引

```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        res = {}
        for i, v in enumerate(nums):
            if v not in res:
                res[target-v] = i
            else:
                return [res[v], i]
        
        return [1, -1]
```

今天参与了第一个开源项目 ext-zookeeper (一个给 swoole 写的zookeeper客户端)，做一些文档的整理工作，也在尝试阅读大佬写的代码，要学的地方还有很多，希望有一天能参与扩展研发。

>聪明人都在下笨功夫，集中精力突破单点
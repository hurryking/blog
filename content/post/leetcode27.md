---
title: "Leetcode27"
date: 2019-05-23T00:52:55+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

#### Search in rotated sorted array

买卖股票系列告一段落，主要是[第三个](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)　实在是难消化，鄙人实力不济，改日再战。

今天的题目是[search in rotated sorted array](https://leetcode.com/problems/search-in-rotated-sorted-array/)，题目的意思是给出一个反转后的不重复有序数组(从大到小)，大意如下，[0,1,2,3,4,5] 有可能被翻转为[4,5,0,1,2,3]　或者 [3,4,5,0,1,2]，然后让你找出数组中指定的值。

脑子里第一个蹦出来的是，鼎鼎有名的二分算法(binary search)，有进步，哈哈哈，后面一想，这该怎么二分呢，如果不翻转，这题 so easy
，翻转过后怎么办，讨论区逛了一圈，找了一个能理解的实现，如果low小于等于mid，左半边有序，否则右半边有序，然后我们判断目标数是否在有序范围内就ok了。

```
class Solution:
    # @param {integer[]} numss
    # @param {integer} target
    # @return {integer}
    def search(self, nums, target):
        if not nums:
            return -1

        low, high = 0, len(nums) - 1

        while low <= high:
            mid = int((low + high) / 2)
            # print(int(mid))
            if target == nums[mid]:
                return mid

            if nums[low] <= nums[mid]:

                if nums[low] <= target <= nums[mid]:
                    high = mid - 1
                else:
                    low = mid + 1
            else:
                if nums[mid] <= target <= nums[high]:
                    low = mid + 1
                else:
                    high = mid - 1

        return -1
```

参考资料
1. [Grandyang](https://www.cnblogs.com/grandyang/p/4325648.html)
2. [python](https://leetcode.com/problems/search-in-rotated-sorted-array/discuss/14437/Python-binary-search-solution-O(logn)-48ms)

<iframe width="560" height="315" src="https://www.youtube.com/embed/HqmpIQ9l-uA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 保持愤怒，保持热爱，守住底线。
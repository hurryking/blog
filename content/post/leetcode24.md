---
title: "Leetcode24"
date: 2019-04-30T19:59:14+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

#### Sort Colors

Given an array with *n* objects colored red, white or blue, sort them **in-place** so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

**Note:** You are not suppose to use the library's sort function for this problem.

**Example:**

```
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Follow up:**

- A rather straight forward solution is a two-pass algorithm using counting sort.
  First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
- Could you come up with a one-pass algorithm using only constant space?

经典的荷兰旗问题，给出一个包含三种颜色元素的数组，原地对它们进行排序，使其按照固定顺序(红，白，蓝)进行排列，我们用 0 代表红色 1 代表白色 2 代表蓝色。

注意: 请不要使用自带的 sort 函数去解决问题。

有一种用额外空间存储的算法，比如存下 {0 : 2, 1 : 2, 2: 2} 然后再去按顺序遍历，如果这样做就失去了此题的初衷。

下面给出快速排序的解法，以白色为基准，把红色放在白色的左边，蓝色放在白色的右边。与之前的 [3SUM](<https://hurryking.github.io/2019/03/leetcode11/>) 双指针法十分相似。

```python
class Solution(object):
    def sortColors(self, nums):
        """
        :type nums: List[int]
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        head = middle = 0
        tail = len(nums)-1
        while middle <= tail:
            if nums[middle] == 0:
                tmp = nums[middle]
                nums[middle] = nums[head]
                nums[head] = tmp
                middle += 1
                head += 1
            elif nums[middle] == 1:
                middle += 1
            else:
                temp = nums[middle]
                nums[middle] = nums[tail]
                nums[tail] = temp
                tail -= 1
        
        return nums
```

![Dutch Flag](https://hurryking.github.io/img/DuthFlag.png)
[快速排序](https://zh.wikipedia.org/wiki/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F#%E5%8E%9F%E5%9C%B0%EF%BC%88in-place%EF%BC%89%E5%88%86%E5%89%B2%E7%9A%84%E7%89%88%E6%9C%AC)

> 世人笑你太疯癫，你笑世人看不传。
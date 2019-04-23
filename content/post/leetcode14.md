---
title: "Leetcode14"
date: 2019-04-01T23:24:39+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

### Sqrt(x)

Implement `int sqrt(int x).`

Compute and return the square root of x, where x is guaranteed to be a non-negative integer.

Since the return type is an integer, the decimal digits are truncated and only the integer part of the result is returned.

**Example 1:**

```
Input: 4
Output: 2
```

**Example 2:**

```
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since 
             the decimal part is truncated, 2 is returned.
```

求平方根，不用系统函数

牛顿迭代法

```python
class Solution(object):
    def mySqrt(self, x):
        """
        :type x: int
        :rtype: int
        """
        r = x
        while r*r > x:
            r = (r+x/r)/2
        
        return r
```

查了许多资料，对牛顿迭代法还是理解很模糊，只知道牛顿迭代法收敛比较快，多用来求近似值。

二分查找法，这个比较熟悉

```
class Solution(object):
    def mySqrt(self, x):
        """
        :type x: int
        :rtype: int
        """
        l, r = 0, x
        while l <= r:
            mid = l + (r-l)//2
            if mid * mid <= x < (mid+1)*(mid+1):
                return mid
            elif x < mid * mid:
                r = mid
            else:
                l = mid + 1
```

最近的状态有些懈怠，又开始循环之前的情况，正在尝试控制自己不要向懒惰屈服，得定些奖惩规矩来刺激正向循环，不然又成为３分钟热度了。

### Conclusion

下班的时候读了曹大最新的文章，里面讲到了事实与逻辑，其中有一句印象很深，程序员该抬头看看了，现在市场上已经不缺程序员了，读完心中一阵发凉，如果意识不到自己在市场上的位置，还停留在以为自己处在很稀缺的状态，就很危险了，应该思考如何提升自身的稀缺性，不能一味麻木的去钻研，要多深度思考，多看些有深度的文章，不能被垃圾信息淹没了自己的脑袋。

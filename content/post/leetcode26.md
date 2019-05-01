---
title: "Leetcode26"
date: 2019-05-01T23:06:51+08:00
archives: "2019"
tags: []
author: John SMITH
---

#### Best Time to Buy and Sell Stock II

Say you have an array for which the *i*th element is the price of a given stock on day *i*.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).

**Note:** You may not engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).

**Example 1:**

```
Input: [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
             Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
```

**Example 2:**

```
Input: [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
             Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are
             engaging multiple transactions at the same time. You must sell before buying again.
```

**Example 3:**

```
Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```

这是昨天刷的 [Best Time to Buy and Sell Stock](<https://hurryking.github.io/2019/05/leetcode25/>) 进阶，允许多次买入卖出，求最大利润

如果允许多次交易，如何保证最大利益，肯定是每个低点买下个高点卖，所以第一种解法就是求出每个谷峰和谷底

如何求谷底，向前比较只要比前一个大就说明还在降低，只要停止降低，下一个就是最低价，谷峰相反

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        i = 0
        vallley = prices[0] if prices else 0
        peak = prices[0] if prices else 0
        profit = 0
        
        # 这里的 -1 很重要 因为最后以为再向前走会超出范围，每次都会向前比较一位 所以可以跳过 很重要的细节
        while i < (len(prices) - 1):
            # 找出谷底
            while i < (len(prices) - 1) and prices[i] >= prices[i+1]:
                i += 1
            valley = prices[i]
            # 找出谷峰
            while i < (len(prices) - 1) and prices[i] <= prices[i+1]:
                i += 1
            peak = prices[i]
            profit += peak - valley
        
        return profit
```

还有一种思路，可以只找增长的部分，不用找谷峰谷底，每次向后比较，只要比上一次高就算入利润中

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        profit = 0
        for i in range(len(prices)):
            if i > 0 and prices[i] > prices[i-1]:
                profit += prices[i] - prices[i-1]
        
        return profit
```

这种思路只用关系增长就好了，代码很简约直白。

发现自己脑中的想法总是很浅，没有形成一种体系，还是深度思考比较少，对一些问题缺少体系化的认知，缺少体系化的阅读，可能是碎片化阅读带来的结果，以后需要注意了，遇到事情多问几个为什么，不能一味接受。



>叠加式的进步，或者原地踏步。
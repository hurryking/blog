---
title: "Leetcode25"
date: 2019-05-01T00:41:05+08:00
archives: "2019"
tags: []
author: John SMITH
---

#### Best Time to Buy and Sell Stock

Say you have an array for which the *i*th element is the price of a given stock on day *i*.

If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.

Note that you cannot sell a stock before you buy one.

**Example 1:**

```
Input: [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
             Not 7-1 = 6, as selling price needs to be larger than buying price.
```

**Example 2:**

```python
Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```

最好的卖出股票的时间，给出一组正整数，每个代表第 i 天的价格，只允许交易一次，求出最大化的利润。

必须先买后卖。

第一想法是 brute force，但是超时了，第二想法是每次记下当前价格和最低价格的差，然后求出最大利润，下面是代码

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """        
        minprice = prices[0] if prices else 0
        maxprofit = 0
        for i in prices:
            minprice = min(i, minprice)
            maxprofit = max(maxprofit, i-minprice)
        return maxprofit
```

每次计算当前价格与最小价格的差，然后求最大利润，这道题比较简单。

难度降下来后，确实速度变快了，要开始升难度了。

回头把 牛顿迭代求平方根 和 N 皇后问题再写下思路。

>人生就是一个接一个困难的解决过程，把它当作游戏，很快就过去了，希望你能享受游戏的乐趣。


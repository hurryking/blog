---
title: "Leetcode15"
date: 2019-04-03T00:27:16+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

### Subset Power set

接着上回提到的求[子集合](https://hurryking.github.io/2019/03/leetcode12/)的问题，继续说位操作解法。

```python
class Solution:
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        res = []
        for i in range(1<<len(nums)):
            tmp = []
            for j in range(len(nums)):
                # 判断对应位是否为1
                if i & 1 << j:
                    tmp.append(nums[j])
                    
            res.append(tmp)
        
        return res
```

可以把每一个结果转换为二进制，然后遍历　000 到　111 ，把对应位置为１的元素保留到临时list，最终结果就是全部子集合

比如说 ['a', 'b', 'c']  a 代表 第一位为 １ 的情况 100，b 代表　第二位为 １ 的情况　010，c 代表第三位为 １ 的情况，

100 =>　['a']

101 =>　['a','c']　

111 => ['a', 'b', 'c']
这样应该足够清晰了。

![bitmask](https://hurryking.github.io/img/bitmask.png)

>
清醒的活在当下，不为往事懊悔。
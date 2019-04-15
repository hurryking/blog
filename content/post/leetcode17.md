---
title: "Leetcode17"
date: 2019-04-15T22:11:16+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

### Longest Common Prefix

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

**Example 1:**

```
Input: ["flower","flow","flight"]
Output: "fl"
``` 

**Example 2:**

```
Input: ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

**Note:**

All given inputs are in lowercase letters a-z.

最长公共子前缀，给出一个字符串数组，求最长公共子前缀，所给出的字串都在 a-z 中。

第一反应是给　brute force　哈哈哈哈，后来想了下，用 [字典树](https://zh.wikipedia.org/zh-hans/Trie) 再取出没有子节点的前 n 个元素

接下来的问题就是如何用 python 实现字典树了，用 py 的 dict

```
{
	'f': {
		'l': {
			'o': {
				'w': {
					'e': {
						'r': {
							'end': 'end'
						}
					},
					'end': 'end'
				}
			}
		}
	}
}
```

下面是代码
```
class Solution(object):
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """

        common_prefix = ''
        root = {}
        for word in strs:
            current = root
            for letter in word:
                current = current.setdefault(letter, {})

            current['end'] = 'end'
        
        while 'end' not in root.keys():
            if len(root.items()) == 1:
                for k,v in root.items():
                    common_prefix += k
                    root = v
            else:
                break

        return common_prefix
```
[Trie](https://hurryking.github.io/img/Trie.png)

写了将近一个小时，知道用 trie 树，但是中间的逻辑部分总是不知道怎么实现，比如说 trie 的实现，如何向下遍历　dict，如何判断公共子序列是否到头了，最终还是看别人的实现

最近想到了职业规划的问题，目前的状况是在做大数据可视化，之前是 PHP，研究的都不是很深，之前的想法是做自由职业，自己开发产品，然后靠维护产品生存，目前不知道做什么比较好，正在想做小游戏，目前前端能力欠缺正在补充。个人觉得一项技术研究到 90%　就　ok　了，再往下走就会得不偿失。还有就目前而言，会多项能熟练使用的技术优势大于会一项很精深的技术。

> 
个人品牌是最好的护城河
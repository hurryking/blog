---
title: "Leetcode22"
date: 2019-04-27T00:08:41+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

####  Length of Last Word

Given a string *s* consists of upper/lower-case alphabets and empty space characters `' '`, return the length of last word in the string.

If the last word does not exist, return 0.

**Note:** A word is defined as a character sequence consists of non-space characters only.

**Example:**

```
Input: "Hello World"
Output: 5
```

给出一组由大小写单词和空格组成的字符串，求字符串中最后一个单词的长度

第一反应是用空格分离然后拿最后一个元素，结果有一个情况没考虑到，比如说如果最后一个字符是空格，结果 WA 了七八次，想问题还是不够全面，最终做出来了，可以先把前后的空格 strip 掉，然后再走上面的思路

```
class Solution(object):
    def lengthOfLastWord(self, s):
        """
        :type s: str
        :rtype: int
        """
        return len(s.strip().split(" ")[-1])
        
```

不过这种方法时间复杂度很高，如果字符串很长就会死的很惨，去讨论区看了下，得出如下的方法

```
class Solution(object):
    def lengthOfLastWord(self, s):
        """
        :type s: str
        :rtype: int
        """
        trailler_pointer = -1
        lenofs = len(s)
        
        # find the position of last trailer space
        while trailler_pointer >= -lenofs and s[trailler_pointer] == ' ':
            trailler_pointer -= 1
        
        word_pointer = trailler_pointer
        while word_pointer >= -lenofs and s[word_pointer] != ' ':
            word_pointer -= 1
        
        return trailler_pointer - word_pointer
```

答主称之为快慢指针法，下面我来描述下思路，就是先找出最后一个空字符串的位置，然后基于空字串的位置再找最后一个单词的位置，最后相减就是结果，清晰明了

今天看了下极客时间的程序员的数学课专栏试读部分，其中提到程序是数学的扩展，想学好数学要以实用为准，我觉得对算法也是一样，可是刷 leetcode 是不是算实用的一种，但总归算是一种对逻辑能力的锻炼，不是坏事

希望以后可以在实际工作场景中用到学过的算法

>
当局者迷，旁观者清，当你能自己做自己的旁观者的时候就厉害了，没到这种境界的时候还是多听听长者的建议




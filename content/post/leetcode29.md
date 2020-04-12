---
title: "Leetcode29"
date: 2020-02-25T22:26:57+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---

### Longest Common Sequence

用动态规划的思想，解决最长公共子序列问题。

先解释概念，什么是公共子序列？

最长公共子序列（LCS）是一个在一个序列集合中（通常为两个序列）用来查找所有序列中最长子序列的問題。这与查找最長公共子串的问题不同的地方是：子序列不需要在原序列中占用连续的位置。可能比较难懂，贴上一个维基百科的[定义链接](https://zh.wikipedia.org/wiki/%E6%9C%80%E9%95%BF%E5%85%AC%E5%85%B1%E5%AD%90%E5%BA%8F%E5%88%97)

举例如下，假设有两个字符串 A="acda" B="adac"

那么 A 和 B 的公共子串有 ac ad ada，其中最长的公共子序列就是 ada

动态规划是什么，简单来说就是把事情按照阶段来进行处理，每个阶段的运算依赖上个阶段的结果。

```shell
                LCS("abc", "dab")
                        |
                        |
                        |
                        |
    -----------------------------------------
    |　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
    |　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
    |　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
 LCS("abc", "ab")                  LCS("bc", "dab")
    |　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
    |　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
    |　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
  1 + LCS("bc", "b")                    ---------------
		    |　　　　　　　　　　　　　　　　　　　　　　　　　　　|　　　　　　　　　　　　　|
		    |　　　　　　　　　　　　　　　　　　　　　　　　　　　|　　　　　　　　　　　　　|
		    |　　　　　　　　　　　　　　　　　　　　　　　　　　　|　　　　　　　　　　　　　|
		2 + LCS("c", "")        LCS("c", "dab") LCS("bc", "ab")
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　|
                                                    -------------
                                                    |           |
                                                    |           |
                                                    |           |
											LCS("c", "ab") LCS("bc", "b")
                                                                |
                                                        1 + LCS("c", "")	
```

给出一段伪代码
```
function LCSLength(X[1..m], Y[1..n])
    C = array(0..m, 0..n)
    for i := 0..m
       C[i,0] = 0
    for j := 0..n
       C[0,j] = 0
    for i := 1..m
        for j := 1..n
            if X[i] = Y[j]
                C[i,j] := C[i-1,j-1] + 1
            else
                C[i,j] := max(C[i,j-1], C[i-1,j])
    return C[m,n]

```

下面贴出 PHP 的实现
```php
<?php
Class Solution
{
    public function lcs($str1, $str2)
    {
        $matrix = [];
        $max = 0;

        for ($i=0; $i < strlen($str1); $i++) {
            for ($j=0; $j < strlen($str2); $j++) {
                if ($str1[$i] == $str2[$j]) {
                    $matrix[$i][$j] = isset($matrix[$i-1][$j-1]) ? $matrix[$i-1][$j-1]+1 : 1;
                } else {
                    $matrix[$i-1][$j] = isset($matrix[$i-1][$j]) ? $matrix[$i-1][$j] : 0;
                    $matrix[$i][$j-1] = isset($matrix[$i][$j-1]) ? $matrix[$i][$j-1] : 0;

                    $matrix[$i][$j] = $matrix[$i-1][$j] > $matrix[$i][$j-1] ? $matrix[$i-1][$j] : $matrix[$i][$j-1];
                }

                $max = $matrix[$i][$j] > $max ? $matrix[$i][$j] : 0;
            }
        }

        return $max;
    }
}

$solution = new Solution();
$res = $solution->lcs("abcde", "acss");

```
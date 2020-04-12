---
title: "Leetcode29"
date: 2020-04-12T22:26:57+08:00
archives: "2020"
tags: ['leetcode']
author: Mr King
---

### 寻找到达某个节点的路径

这是今天面试遇到的一道算法题，看起来比较简单，思路很清楚，但是有些细节没有想清楚，所以没有一下子，答出来。现在整理下思路继续写出来。

题目是:  
有如下数组[1, 2, [5,6,8,[21,22]],[12,15]]，返回到达给定元素的路径。

思路是深度有限遍历，然后把路径放进一个栈里面，进入节点压栈，出节点退栈。

```php
function deepin($arr, $needle, $path)
{
	if (is_array($arr)) {
		foreach ($arr as $key => $value) {
			$path[] = $key;

			if ($res = deepin($value, $needle, $path)) {
				return $res;
			} else {
				array_pop($path);
			}
		}
	}

	if ($arr == $needle) {
		return $path;
	} else {
		array_pop($path);
		return false;
	}
}

$array = [3, 5, [9, 10, 11], [12, 13, 29]];
$path = [];
$path = deepin($array, 5, $path);
```

因为用到了递归，所以数据规模比较大的话会爆栈，还需要优化下，用非递归的方式去遍历
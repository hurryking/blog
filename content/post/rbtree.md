---
title: "Rbtree"
date: 2019-02-28T02:28:49+08:00
archives: "2019"
tags: []
author: John SMITH
---

---
title: "红黑树Python版本"
date: 2019-02-23T17:41:55+08:00
archives: "2019"
tags: []
author: lanren
---

### 红黑树的基本属性

1. 节点颜色必须为红色或黑色
2. 根节点只能为黑色
3. 所有叶节点必须为黑色
4. 每个红色节点必须有两个黑色节点
5. 从任意节点到其下的叶节点的路径上的黑色节点数量必须相同

### 属性解读

树的高度增长过快会导致各项操作所需的时间复杂度变坏, 属性4和5确保了树的高度增长不会过快,  确保了从根到叶的最短路径和最长路径相差不会相差超过2倍(最短路径全为黑节点,最长路径为红黑相间)

### Python 实现

#### 数据结构定义
```
Black = 'Black'
Red = 'Red'
Nil = 'Nil'

class Node(object):
	"""docstring for Node"""
	def __init__(self, value=None, color=Black):
	    self.value = value
	    self.color = color
}

class RBTree(object):
	"""docstring for RBTree"""
	def __init__(self, node=None):
	    self.root = node
}
```
牢记需要遵守上面的5个基本属性
#### 插入
默认插入红色(因为插入黑色节点对树影响大，路径上直接多了个黑色节点，需要调整的地方增多), 先插入后修复至平衡
```
	def insert(self, value=None):
		if not value:
			raise EmptyValueErr('空值异常')

		new_node = Node(value=value, color=RED)
		new_node.left = new_node.right = Node(value=None, color=Nil)
		if not self.root:
			self.root = new_node

			return new_node

		parent = self._findParent(value)

		if parent.value > value:
			parent.left = new_node
		else:
			parent.right = new_node

		self._insertFixUp(value)

```
##### 找父节点
```
def _findParent(self, value):
		current = self.root
		while current.color != Nil:
			if current.value > value:
				if current.left.color != Nil:
					current = current.left
				else:
					return current
			elif current.value < value:
				if current.right.color != Nil:
					current = current.right
				else:
					return current
			else:
				raise SameNodeErr('重复节点')

		return current
```
##### 插入修正
```

```
##### 各种旋转
```

```
#### 删除
201902280210　未完待续


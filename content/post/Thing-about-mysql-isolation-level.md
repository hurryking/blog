---
title: "Mysql InnoDB　锁和事务模型系列(一)"
date: 2019-03-10T14:37:25+08:00
archives: "2019"
tags: [mysql]
author: Mr king
---

### Mysql InnoDB 锁

**以下的所有操作都是基于 mysql8**

Mysql innodb 包含的锁类型如下:

* 共享锁和排他锁
* 意向锁
* 记录锁
* 区间锁
* Next-key 锁
* 插入意向锁
* 自增锁
* 断定锁(仅空间索引使用)

#### 共享锁和排他锁

InnoDB 实现了标准的行级锁定。S(共享)和 X (排他)锁

* S 允许事务读取一行的时候持有锁
* X 允许事务更新或者删除一行的时候持有锁

如果事务　T1 对行　r 持有 S，事务　T2 对行　r 进行加锁的操作会有如下两种情况:
* T2 对　r 加 S 可以立刻得到授权，T1 和　T2 都对　r 持有　S
* T2 对　r 加 X 不能立马得到授权，得等　r 上的 S 都释放了以后才可以　

如果事务　T２ 对行　r 持有 X，事务　T2 对行　r 进行加任何类型的锁都不会立马得到相应，必须得等到　T1 释放掉对 r 加的 X。


#### 意向锁

InnoDB 支持多粒度锁定，意思就是允许行级锁和表级锁共同存在。
意向锁 是表级锁。
分为　IS(意向分享锁) 与　IX(意向排他锁) 

意向锁的工作方式(协议)如下:
1. 事务在获得表中一行的 S 以前，必须先获得这个表上的　IS 或者更高级别的锁
2. 事务在获得表中一行的 X 以前，必须先获得这个表上的　IX

表级别的锁兼容性表现如下

**-**(冲突) **+**(兼容)

|     |  S  |  X  | IS  | IX  |
| :--: | --: | --: | --: | --: |
| X  |  -   |   -  | - | - |
| IX  |  -   |  -   | +    | + |
| S |   +  | - | + | - |
| IS |  +  |  -  | + | + |

如果事务要申请的锁与已经存在的锁冲突，就必须等已经存在的锁释放后才能申请成功。不然会导致死锁。
意向锁的主要目的就是知道有人在锁定或者即将锁定表中的某一行。**这块理解还是有些模糊需要多查下资料**

#### 记录锁

记录锁是加在一行带有索引的记录上的锁。
如果此表没有索引，那么 mysql 会加一个隐式的聚簇索引，用这个索引去给指定的行加记录锁。

#### 区间锁

#### Next-key 锁

#### 插入意向锁

#### 自增锁

#### 断定锁(仅空间索引使用)

### Conclusion

很多东西还是不了解，得花时间去实际操作下，完善下知识体系。20190310未完待续 ^_^
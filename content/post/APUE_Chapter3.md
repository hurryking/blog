---
title: "APUE Cahpter3"
date: 2019-04-28T01:07:02+08:00
archives: "2019"
tags: ['APUE']
author: Mr King
---


#### Unix 环境高级编程　第三章

> 这是一篇没有经过思考整理，单纯搬书的内容，不值得浪费时间去看

![APUE思维导图](http://hurryking.github.io/img/APUE_Chapter3.svg)
文件I/O

讲到一些 Uninx 下的文件操作函数，open creat read close fcntl
其中文件共享一节讲到了内核使用３种数据结构来表示打开的文件
1. 进程表项(每个进程会在进程表中有一个记录项，记录项中有一张此进程打开的文件描述符表)

2. 文件表项(记录文件的文件状态标识，当前的文件偏移量和 v 节点指针)

3. v节点表(记录文件类型和操作文件的函数指针还有 i 节点)

这里给出 APUE 中的图
![fd](http://hurryking.github.io/img/fd.png)
这张图还是比较详细的

讲到多进程模式下的文件操作，以及系统提供的对于文件的原子操作

read 中提到的**预读**(read ahead)，读出多余应用需要的，预备下次使用，减少I/O
write 中提到文件系统的**延迟写**(delayed write)的流程，先写高速缓冲区然后进队列，最后进磁盘，减少I/O
找到一个质量比较高的关于文件系统的[资源](http://www.cs.cornell.edu/courses/cs415/1999fa/slides-fs/siframes.htm)，有兴趣的可以看看

lseek 文件中的空洞不占用磁盘块(已经验证)，但是文件结尾的空洞会占磁盘块(未验证)
这里给出一个在 linux 上快速生成指定大小文件的命令 dd

```
dd if=/dev/zero of=file.txt count=1024 bs=1024
```

if 是要读取的文件
of 是要写入的文件
count 是要 copy 的次数
bs 是每次写入的字节数
dd 大多数时候用来测试操作系统的写入速度

fcntl O_SYNC 开启后我的实验结果和课本中提到的不一样(有空可以研究下为啥)

遇到了 git large file 的问题，对 git 原理还是不熟悉，要有耐心去读官方手册，然后实验，一味靠搜索解决不了根本的认知问题

习题3.5 比较有意思

* ./command > outfile 2>&1

* ./command 2>&1 >outfile

第一条会把 STDOUT_FILENO 重定向到 outfile，然后 dup 将 STDOUT_FILENO 复制到 STDERR_FILENO，即把 STDIN_FILENO 和 STDERR_FILENO 指向同一个文件表项。

第二条先把 STDERR_FILENO 复制到终端的 fd，STDERR_FILENO　和　终端fd 共享一个文件表项，然后把 STDOUT_FILENO 重定向到 outfile，也就是最终会把错误输出在终端，标准输出到 outfile。

这样解释还是不够详细，有时间还是得看 linux 内核关于 shell　部分的具体实现。

看到了一篇文章中提到程序员的剩余价值问题，处于金字塔最低端，要想办法成全自己。
利用自身优势去服务更多认可自己价值的人，才是正道，做自己的产品，提供基于自身的价值才是唯一的出路。

>
要多加思考不要别人喂什么就吃什么，脑袋会塞满垃圾的。
---
title: "APUE_Chapter5"
date: 2019-05-13T17:53:49+08:00
archives: "2019"
tags: ['APUE']
author: Mr King
---

### 第五章

> 这是一篇没有经过思考整理，单纯搬书的内容，不值得浪费时间去看

标准IO(带缓冲的IO)

流的定向 宽定向 字节定向 

缓冲的类型 全缓冲(读写磁盘) 行缓冲 不缓冲(输出到终端)

打开流 fopen freopen fdopen

getc() getchar() putchar() fgetc() putc() fputc() fgets() fputs()

设置流的流类型 setbuf setvbuf

定位流 ftell fseek ftello fseeko fgetpos fsetpos

格式化IO printf sprintf fprintf dprintf snprintf

二进制IO fread fwrite

内存流 fmemopen

5-13　中的字符串数组和字符串指针的问题还是没有理解，涉及到 C 内存布局问题，还需要阅读更多资料并实践，这里是[资料地址](https://www.usna.edu/Users/cs/aviv/classes/ic221/s16/lec/08/lec.html)。
5-14　内存流　fseek 后的结果不理解，fseek后hello,world为什么会出现在12个b后面，这12个b是什么鬼,这个地方很疑惑。

C 语言内存布局 size 详解
text data bss heap stack high_add

setjmp getjmp 使用 [try catch](http://www.di.unipi.it/~nids/docs/longjump_try_trow_catch.html)
里面的 switch + while 看的头晕目眩
[Duff's device](http://www.catb.org/jargon/html/D/Duffs-device.html) 一个很厉害的字节拷贝算法 看起来很玄学 哈哈哈

第七章中(7.10-2)自动变量的潜在问题没有看懂需要再找资料理解下

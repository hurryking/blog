---
title: "APUE_Chapter5"
date: 2019-05-13T17:53:49+08:00
archives: "2019"
tags: ['apue']
author: Mr King
---



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

C 语言内存布局 size 详解
text data bss heap stack high_add

![Duff's device](http://www.catb.org/jargon/html/D/Duffs-device.html) 一个很厉害的字节拷贝算法 看起来很玄学 哈哈哈
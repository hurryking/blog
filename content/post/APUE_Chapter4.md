---
title: "APUE_Chapter4"
date: 2019-04-30T14:21:30+08:00
archives: "2019"
tags: ['APUE']
author: Mr King
---


第四章
文件 unlink 在 centos6.7 的实验过程中，发现和书中提到的结果不一样

```
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <string.h>
#include <unistd.h>

int
main(void)
{
    if (creat("tempfile", O_RDWR) < 0) {
        perror("open error");
    }

    if (unlink("tempfile") < 0) {
        perror("unlink error");
    }

    printf("file unlinked\n");
    sleep(10);
    printf("done\n");
    exit(0);
}

```

运行环境 centos 6.7
还有预期用 open("tempfile", O_RDWR) 可以创建文件实践发现不行
预期是在运行期间是可以删除 tempfile 的，但是实践发现并不可以
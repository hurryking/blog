---
title: "APUE_Chapter4"
date: 2019-04-30T14:21:30+08:00
archives: "2019"
tags: ['APUE']
author: Mr King
---

### APUE 第四章

> 这是一篇没有经过思考整理，单纯搬书的内容，不值得浪费时间去看

本章主要讲述了 stat 结构体中的每个属性的含义，以及使用。

```
struct stat {
    mode_t          st_mode;    /* 文件类型以及模式(权限) */
    ino_t           st_ino;	    /* i节点编号 */
    dev_t           st_dev;	    /* 设备号(文件系统) */
    dev_t           st_rdev;    /* 设备号(特殊文件) 字符特殊设备和块特殊设备 */
    nlink_t         st_nlink;   /* 链接数 */
    uid_t           st_uid;	    /* 所属者的用户ID */
    gid_t           st_gid;	    /* 所属组的组ID(有歧义所属者的组ID) */
    off_t           st_size;    /* 普通文件的字节数 */
    struct timespec	st_atime;   /* 最后一次访问时间 */
    struct timespec st_mtime;   /* 最后一次修改时间 */
    struct timespec st_ctime;   /* 最后一次文件状态修改时间 */
    blksize_t       st_blksize; /* I/O 块的字节数 */
    blkcnt_t        st_blocks;  /* 分配的磁盘块数 */
}
```



#### st_mode

1. 文件类型

   Unix 下文件类型有如下 7 种

   * 普通文件
   * 目录文件
   * 块特殊文件 比如硬盘(提供对设备带缓冲的访问，以块为单位读取) 硬盘挂载在系统文件树上 挂载点的文件类型就是块特殊文件
   * 字符特殊文件 比如鼠标键盘(提供对设备不带缓冲的访问，输入多少读取多少不缓冲，以字符为单位进行读取) 
   * FIFO 命名管道，进程间通信
   * Socket 进程间的网络通信
   * 符号链接(软连接)

2. 文件权限和进程权限

   ##### 文件权限有如下九种

   ​	用户读写执行 组读写执行 其他读写执行

   ##### 进程权限

   ​	实际用户 ID 实际组 ID 

   ​	有效用户 ID 有效组 ID 附属组 ID 

   ​	保存的设置用户 ID 保存的设置组 ID 

   ​	实际用户 ID 和 组 ID 在登陆时就决定了，以 test 登陆的，执行的所有进程的 实际用户 ID 就是 test

   ​	有效用户 ID 和 组 ID 决定我们能访问哪些文件，在访问文件的时候会校验进程的有效用户 ID 和 组 ID ，确保进程有权限访问文件

   ​	保存的设置用户 ID 和 组 ID 可以改变用户的有效用户 ID 和 组 ID

   ##### umask

   ​	可以用来设置新文件的权限屏蔽位，如果不想让其他人读，可以设置 0002，新建的文件就是其他人不可读

   ##### S_ISVTX (粘着位)

   ​	早期程序执行完后，会被保存在交换区，方便下次运行时更快的载入内存 (通常在 Unix 文件系统中，文件的各数据块随机存放，交换区是被作为一个连续文件处理的)。现在大多数 Unix 系统都配置了[虚拟存储系统](<https://www.tldp.org/LDP/tlk/mm/memory.html>)以及[快速文件系统](<https://en.wikipedia.org/wiki/Unix_File_System>)，已经不再这样使用了。后来粘着位的使用又扩展到了目录，对目录设置粘着位后，只有对该目录有写权限，并且满足以下三个条件之一才能删除或者重命名该目录下的文件:

* 拥有此文件
* 拥有此目录
* 是超级用户

   /tmp 就是目录粘着位的使用典范



#### st_uid st_gid

1. chown fchown fchownat lchown

   改变文件的属主和组

#### st_size

1. 文件长度

   st_size 表示以字节为单位的文件的长度，只对普通文件 目录和符号链接有效。

2. 文件空洞

   lseek 超出文件长度后再往文件尾写内容，会导致文件中有空洞。

#### st_nlink

1. 文件系统

   UFS 文件系统与磁盘的关系

   此处应该附图一张

   ![ufs-4-14](https://hurryking.github.io/img/APUE_4_14.png)

   ![ufs-4-15](https://hurryking.github.io/img/APUE_4_15.png)

2. link linkat unlink unlinkat

   link linkat 用来建立硬链接，任何一个文件可以有多个目录项指向其 i 节点，这里的硬链接就是新建一个目录项指向已经存在的某个目录项的 i 节点，与软连接不同，后面会讲软连接。

   unlink unlinkat 删除目录项，并将 pathname 所引用文件的连接数减1。当链接数到 0 时，该文件才可以被删除，还有一种情况，当有进程打开了这个文件，不能删除。

3. 符号链接

   符号链接是指向一个文件的间接指针，与硬链接不同。

   软连接解决了硬链接存在的两个问题：

   * 硬链接通常要求链接和文件位于统一文件系统
   * 只有超级用户才能创建指向目录的硬链接

   给目录创建符号链接有可能导致循环，给一个目录下的文件创建一个指向该目录的软连接就会造成循环。

   ![link_loop](https://hurryking.github.io/img/link_loop.png)

#### st_atime st_mtime st_ctime

1. 文件时间

   下面列出哪些操作会对文件时间造成影响

   ![filetime](https://hurryking.github.io/img/AUPE_filetime.png)

#### st_dev st_rdev

​	设备特殊文件

​	系统中每个文件名关联的 st_dev 值是文件系统的设备号，统一磁盘驱动上的各文件系统主设备号(major)相同，次设备号(minor)不同。

​	只有字符特殊文件和块特殊文件才有 st_rdev 值。次值包含实际设备的设备编号。

### 遇到的问题

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

进行了一番搜索后找到了原因，mode 设置为 O_CREATE 就可以了。

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

之前觉得第二章是讲标准的，以后可以随查随用就跳过了，后面发现很多地方都提到了第二章的 POSIX 标准，所以还得回头粗看一边。

[比较好的资料](http://www.cnblogs.com/0xcafebabe/p/4430632.html)

> 当你没有得到你想要的东西的时候，只能证明你想得到那件东西的欲望还不够强烈
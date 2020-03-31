---
title: "Hugo 安装"
date: 2020-03-31T17:02:57+08:00
archives: "2020"
tags: ['go']
author: John SMITH

---
### hugo 安装
由于最近换了一台新的服务器，想把博客迁移过来所以决定把hugo 在新服务器上再安装一遍  
环境: CentOS Linux release 7.3.1611 x86_64  

阅读 Hugo 安装文档[Hugo 的文档](https://github.com/gohugoio/hugo) 发现新版本需要 go1.11 所以先安装 go

找到下载地址[下载地址](https://golang.org/dl/ ) 我们选择最新版本 1.14.1 选中x86_64 对应的二进制包
切换到临时目录
```bashshell
cd /tmp/
```
下载
```bashshell
wget https://dl.google.com/go/go1.14.1.linux-amd64.tar.gz
```
我想把 go 安装到 /usr/local/， 于是把安装包解压到 /usr/local/
```bashshell
tar -C /usr/local -xzf go1.14.1.linux-amd64.tar.gz
```
然后把 go 的 bin 目录添加到系统 PATH 路径中
```bashshell
echo "export PATH=\$PATH:/usr/local/go/bins" >> ~/.profile
```
然后引用一下 ~/.profile
```bashshell
source ~/.profile
```
然后 go 就安装成功了，查看下 go 的版本信息
```bashshell
go --version
```

接下来安装 hugo

由于我的系统内存太小编译安装报错，内存不够，所以选择了直接下载二进制包

https://github.com/gohugoio/hugo/releases

选择最新版本 0.68.3
```bashshell
cd /tmp/
wget https://github.com/gohugoio/hugo/releases/download/v0.68.3/hugo_0.68.3_Linux-64bit.tar.gz
tar -C /usr/local/bin -zxvf https://github.com/gohugoio/hugo/releases/download/v0.68.3/hugo_0.68.3_Linux-64bit.tar.gz 
```

安装完成，查看版本
```bashshell
hugo version
```
一气呵成，毫无营养含量的一篇博客就这样诞生了。

> 焦虑的时候最好做点什么事情，不然会更焦虑
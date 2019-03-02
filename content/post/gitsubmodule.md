---
title: "Git 子模块使用"
date: 2019-03-02T00:27:05+08:00
archives: "2019"
tags: []
author: John SMITH
---

## Submodule解决了什么问题
当你的项目里依赖与一个第三方模块，或者一个子项目，而你需要保持这个模块的更新，确保使用这个子模块的所有项目都能同步到，如果拷贝的话就达不到这个目的，总之一句话就是在一个库里可以包含多个子库，并且主库和子库是隔离的，互不影响．

## 如何使用
我们现在新建一个库叫 parent
```
git clone git@github.com:hurryking/parent.git
```

然后在 parent 下新建一个目录 children
```
cd parent
mkdir children
```
进入 children 执行
```
cd children
git submodule add git@github.com:hurryking/let-s-go.git
```
然后在 parent 下就能看到刚才加的子库，还有一个 **.gitmodules** 文件，里面记录着子库的在这个项目的路径和远程仓库的地址

![gitsubmodlue01](https://hurryking.github.io/img/gitsubmodle01.png)

然后提交这次的改动
```
git commit -am "added submoudle let's go"
```
推送到远程仓库
```
git push -u origin master(由于是第一次入库需要设置下默认上游)
```
然后我们修改下子库，提交
```
git commit -am "修改子模块但是并不提交子模块的修改"
```
会发现 git 提示我们有修改

![gitsubmodule02](https://hurryking.github.io/img/gitsubmodule02.png)

如果我们尝试推送到远程库，会发现并没有在子模块的修改并未在主模块生效，因为我们没有在子模块推送这次修改

然后我们新建一个目录，clone 下我们刚才的 parent
```
git clone git@github.com:hurryking/parent.git
```
会发现 clone 下来的项目中子库中并没有东西，git 并没有自动替我们拉取子库的代码

我们需要执行下面的命令初始化子库
```
git submodule init
```
拉取子库最新代码
```
git submodule update
```
或者有个更简单的办法在 clone 带recursive参数，会自动拉取所有子模块的代码
```
git clone --recursive git@github.com:hurryking/parent.git
```

以上只是子模块最简单的一些使用，想要了解的更新，请移步[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)

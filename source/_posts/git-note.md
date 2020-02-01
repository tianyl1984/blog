---
layout: post
title: git学习笔记
description: git学习笔记
category: git
---

## 常用git命令

### 分支

删除分支，未合并到 master 时需要使用 -D 强制删除

```
git branch -d branch-name
```

### 提交

-am 不需要加入暂存区直接提交

--amend 修改最近提交的 message

```
git commit -m "fix bug"
```

### fetch

```
git fetch origin master:master
```

### stash

把暂存区的隐藏，方便处理其他临时插入的需求

```
git stash
```

### 配置

#### 配置作用范围

范围: --local --global --system

```
git config --global --list
```

### 回滚

重置 HEAD 到指定 commitid

```
git reset --hard commitid
```

--hard 丢失未 commit 的修改，重置working copy指向指定的提交记录

--soft 仅仅重置 HEAD 到指定 commitid

#### 未 push 到远程

直接通过`git reset --hard commitid`回滚到指定提交记录，通过`git reflog`可以查看之前的提交，再通过`reset`恢复之前的操作。reflog只存在本地的记录中，重新 clone 会丢失。

#### 已 push 到远程

1. `git reset --hard commitid` 回滚到指定提交，并修改本地代码

2. `git reset --soft origin/master` 本地代码不变，HEAD 指向远程分支，此时需要创建一个提交，因为代码没有修改。

3. 正常 commit 并 push 及可。


### 其他


#### 配置网页查看

在网页中查看帮助命令

```
git help --web commit
```

下载 git help 文档

```
git clone git://git.kernel.org/pub/scm/git/git-htmldocs.git git-doc
```

配置客户端，mac 配置使用`git config --global web.browser google-chrome`命令设置客户端时不清楚什么原因无法识别安装的 chrome，只好使用自定义浏览器的方式，通过命令行打开 chrome

```
git config --global web.browser mychrome
```

配置 mychrome 打开路径

```
git config --global browser.mychrome.cmd '/path/to/openchrome.sh'
```

openchrome.sh 内容如下：

```
#!/bin/bash
open -a /Applications/Google\ Chrome.app $1
```

使用`git help clone --web`通过浏览器查看`clone`命令的帮助文档

#### 图形化查看分支

```
gitk
```

#### 使用图形化操作仓库

```
git gui
```

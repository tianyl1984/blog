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

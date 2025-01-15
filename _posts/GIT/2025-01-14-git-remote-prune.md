---
title: "git同步远程已删除的分支"
description: 
date: 2025-01-14 10:00:00 +0800
categories: [GIT]
tags: [GIT]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## git remote prune
Git 的一大优点是它在删除数据时非常小心。这使得 Git 中的提交或其他有价值的数据很难丢失！

这样做的一个小缺点是，有时您可能会看到实际上不再需要的陈旧数据。最好的例子之一是对已删除的远程分支的引用：假设您的一位队友删除了您共享的远程存储库上的一个分支；除非您明确指示 Git 进行清理，否则该分支仍会显示给您。

在Git中，`git remote prune`命令用于清理远程仓库中已删除的分支引用。通常，当团队成员删除共享远程仓库中的分支时，本地仓库仍会显示这些已删除的分支。使用`git remote prune`可以删除这些陈旧的远程分支引用。

## 使用方法

`prune` 是`git fetch`和`git remote`命令的一个选项。（不要将其与独立`git prune`命令混淆 - 这是在垃圾收集期间使用的，不是我们在这里讨论的。）

- 使用`git fetch --prune`

最简单的方法是将`prune`作为选项与`git fetch`一起使用：
```bash
$ git fetch --prune origin
```

- 使用`git remote prune`

如果只想执行prune而不获取远程数据，可以使用`git remote prune`命令：
```bash
$ git remote prune origin
```

这两个命令的结果是相同的：删除指定远程仓库中不再存在的远程分支引用。

## 配置自动执行prune

如果希望在每次执行fetch操作时自动执行prune，可以进行如下配置：
```bash
$ git config --global fetch.prune true
```

## 注意事项

本地分支安全：`prune`命令不会影响本地分支，只会删除远程分支的引用。

与`git prune`的区别：`git prune`是一个垃圾回收命令，用于清理本地仓库中无法访问的对象，而`git remote prune`和`git fetch --prune`用于清理远程仓库中已删除的分支引用。

通过使用`git remote prune`，可以保持本地仓库的整洁，避免显示不再存在的远程分支引用。

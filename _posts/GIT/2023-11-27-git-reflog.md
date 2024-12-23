---
title: "Git进阶使用：git reflog处理代码丢失 & 协作开发保证git信息简洁"
description: 
date: 2023-11-27 10:00:00 +0800
categories: [GIT]
tags: [GIT]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

`reflog` 是 Git 操作的一道安全保障，它能够记录几乎所有本地仓库的改变。包括所有分支 commit 提交，已经删除（其实并未被实际删除）commit 都会被记录。总结而言，只要 HEAD 发生变化，就可以通过 `reflog` 查看到。

日常开发中，切换分支误操作，造成本地代码修改丢失。此时，可以借助 `git reflog` 找回丢失的代码修改。

## Reference
- [# Git进阶使用——git reflog处理代码丢失 & 协作开发保证git信息简洁](https://juejin.cn/post/6844904055232282632)  

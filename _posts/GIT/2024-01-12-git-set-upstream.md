---
title: "Git: set upstream"
description: 
date: 2024-01-12 10:00:00 +0800
categories: [GIT]
tags: [GIT, upstream]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

upstream 分支可以定义为我们的本地分支（也称为远程跟踪分支）在远程仓库上遵循的分支。当我们需要将默认远程分支设置为我们当前的本地分支时，我们必须运行 git upstream 命令。

如果我们添加一个远程分支，并在命令中使用 --set-upstream，这意味着 Git 知道我们将来在 Git fetch、Git pull 或 Git push 时要做什么。

在本地，此命令用于设置 Upstream：
```bash
git branch --set-upstream <remote-branch>
```

推送分支时设置跟踪
```bash
git push --set-upstream origin <remote-branch>
```

避免必须精确输入 --set-upstream 的一种方法是使用其简短版本 -u，如下所示：
```bash
git push -u origin local-branch
```

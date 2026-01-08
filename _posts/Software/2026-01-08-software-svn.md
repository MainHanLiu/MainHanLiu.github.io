---
title: "SVN检出相关操作"
description: 
date: 2026-01-08 10:00:00 +0800
categories: [Software, SVN]
tags: [Software, SVN]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## checkout 和 export
svn export相比于checkout不带`.svn`文件夹，因此是纯文件，无法进行版本控制。

## Checkout Depth 选项

| Depth 选项               | 含义                    |
| ---------------------- | --------------------- |
| Fully recursive        | 所有文件 + 所有子目录（完整）      |
| Immediate children,including folders     | 当前目录文件 + 子目录（但子目录是空的） |
| Only file children | 仅当前目录文件           |
| Only this item         | 只有当前目录本身（连文件都没有）      |
| Exclude                | 从工作副本中移除              |

## 稀疏检出
SVN待检出的文件夹中可能有部分文件夹用作历史存档用，这些文件夹基本不会用上，若检出到本地又十分占用存储空间，此时需要进行稀疏检出。

### 方式一 直接检出子目录
1. SVN Checkout
2. URL 直接填子目录
3. Checkout Depth：Fully recursive

### 方式二 逐级检出
>优点：可以选择让空文件夹占位，目录层级仍然保持显示

1. 使用SVN Checkout 根目录
2. Checkout Depth 选择 Immediate children(当前目录文件 + 空的子目录)
3. 选中所有需要检出的文件夹，选择update to revision
4. Update Depth 选择 Fully recursive

>临时查看未检出文件夹，可以使用repo broswer远程浏览

### 方式三 custom Depth
1. 使用SVN Checkout 根目录
2. 选择Choose items，选中所有需要检出的文件夹
3. 此时Checkout Depth自动变为custom Depth


## exclude
文件检出后又不需要了，右键选择update to revision，Update Depth选择Exclude

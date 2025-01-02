---
title: "VSCODE 软件相关"
description: 
date: 2023-10-23 10:00:00 +0800
categories: [Software, Config]
tags: [Software, Config]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## vscode 多行快捷键

| 快捷键 | 作用 |
|-- |--|
| ctrl+alt+上下键 | 进行 行选择  |
| shift+左右键 | 进行 列选择  |
| 推荐: Ctrl + Alt + up/down | 上下插入光标 - 支持多个 |
|  |  |
| 选中一段文字，按shift+alt+i | 在每行末尾都会出现光标 |
|  |  |
| ctrl+左右键 | 自动识别一个整体 |
| ctrl+shift+左右键 | 自动识别并选择 |
| ctrl+shift+L | 选中编辑代码中相同的内容 |
|  |  |
| alt+鼠标点选 | 多处选择编辑 |
|  |  |
| ctrl+alt+左右键 | 分页 |
|  |  |
| 选中文本后,Ctrl + [ 和 Ctrl + ] | 可实现文本的向左移动 和 向右移动 |


## 软件快捷键

| 快捷键 | 作用 |
|-- |--|
| Ctrl + P | 搜索文件  |
| Ctrl+Shift+O | 搜索符号 |
| Ctrl + T | 搜索使用（更广泛？） |
| Alt + LeftArrow/RightArrow| 设置视图切换  |

## Tips
- 调出大纲：在资源管理器界面的一级目录点击右键，勾选大纲  
![输入图片说明](/imgs/software-vscode/2023-11-09/p4369nFIPeXqhCwk.png)

- 左侧的搜索栏（放大镜）可进行全局的任意文字和英文搜索

- 创建函数的定义和声明  
[# VS Code C++ 扩展：自动创建函数的定义和声明](https://www.topomel.com/archives/5101.html)  
[# 重构 C++ 代码](https://vscode.js.cn/docs/cpp/cpp-refactoring)  

- 删除注释  
[# VScode使用正则表达式快速删除注释](https://blog.csdn.net/yjjjjz/article/details/89521414)

| 正则表达式 | 含义 |
|-- |--|
| `//[\s\S]*?\n` | 单行注释  |
| `/\*(.|\r\n|\n)*?\*/` | 块注释（多行注释）|
| `\/\*[\s\S]*\*\/|\/\/.*` | 所有注释 |
| Alt + LeftArrow/RightArrow| 设置视图切换 |


## 插件
- Git Graph

- GitLens
>[# vscode隐藏内联git信息(gitlens)](https://blog.csdn.net/braised_fish/article/details/126364531) 

- vscode-icons

- Format Files

- Select Line Status Bar
>[# Select Line Status Bar （计算所选中的行数）](https://marketplace.visualstudio.com/items?itemName=tomoki1207.selectline-statusbar)

## Reference
- [# VScode 常用快捷键汇总](https://learnku.com/articles/34744)

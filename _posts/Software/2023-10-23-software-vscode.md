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

## settings.json
```c
{
    // Doxygen documentation generator set
    "doxdocgen.c.triggerSequence": "/*", // 触发自动注释的生成
    "doxdocgen.c.commentPrefix": " * ", // 注释行的前缀
    "doxdocgen.c.firstLine": "/**", // 注释行的首行
    "doxdocgen.c.lastLine": "*/", // 注释行的尾行
    // Smart text snippet for factory methods/functions.
    "doxdocgen.c.factoryMethodText": "Create a {name} object",
    // Smart text snippet for getters.
    "doxdocgen.c.getterText": "Get the {name} object",
    // Smart text snippet for setters.
    "doxdocgen.c.setterText": "Set the {name} object",
    // Smart text snippet for constructors.
    "doxdocgen.cpp.ctorText": "Construct a new {name} object",
    // Smart text snippet for destructors.
    "doxdocgen.cpp.dtorText": "Destroy the {name} object",
    // The template of the template parameter Doxygen line(s) that are generated. If empty it won't get generated at all.
    "doxdocgen.cpp.tparamTemplate": "@tparam {param} ",
    // 文件注释的组成及其排序
    "doxdocgen.file.fileOrder": [
        "custom",
        "brief", // @brief 简介
        "file", // @file
        "author", // 作者
        "version", // 版本
        "date", // 日期
        "empty", // 空行
        "copyright", // 版权
        "empty",
        "custom" // 自定义
    ],
    // 下面时设置上面标签tag的具体信息
    "doxdocgen.file.fileTemplate": "@file {name}",
    "doxdocgen.file.versionTag": "@version 0.0.1",
    "doxdocgen.generic.authorEmail": "authorName@xx.com",
    "doxdocgen.generic.authorName": "authorName",
    "doxdocgen.generic.authorTag": "@authorName      {author} ({email})",
    // // 日期格式与模板
    "doxdocgen.generic.dateFormat": "YYYY-MM", // "YYYY-MM-DD"
    "doxdocgen.generic.dateTemplate": "@date {date}",
    // 文件注释：版权信息模板
    "doxdocgen.file.copyrightTag": [
        "@copyright Copyright (c) {2023}  ****** Company"
    ],
    "doxdocgen.file.customTag": [

        "************************************************************************",
    ],
    // 根据自动生成的注释模板（目前主要体现在函数注释上）
    "doxdocgen.generic.order": [

        "brief",
        "tparam",
        "param",
        "return",
        "custom", // 自定义
        // "author",
        // "date"
    ],
    "doxdocgen.generic.briefTemplate": "@brief       {text}",
    "doxdocgen.generic.paramTemplate": "@param  [in] {indent:8}{param}{indent:8}",
    "doxdocgen.generic.returnTemplate": "@return {indent:8}{type} ",
    "doxdocgen.generic.customTags": [
        "@retval      0，正常",
        "@retval      < 0，异常，详情见errors.h",
        "@warning     无",
    ],
    "doxdocgen.generic.splitCasingSmartText": true,
    "doxdocgen.generic.includeTypeAtReturn": true, // return 中包含类型信息
    "doxdocgen.generic.boolReturnsTrueFalse": false, // bool 返回值拆分成 true 和 false 两种情况
    "doxdocgen.generic.linesToGet": 20, // 回车后最多向下多少行去找函数声明
    "doxdocgen.generic.useGitUserName": false, // {author} 是都根据 git config --get user.name 替换
    "doxdocgen.generic.useGitUserEmail": false,
    "editor.fontSize": 18,
    "files.autoGuessEncoding": true,
    "files.associations": {
        "*.h": "c"
    },
    "security.workspace.trust.untrustedFiles": "open",
    "editor.tabCompletion": "on",
    "editor.bracketPairColorization.independentColorPoolPerBracketType": true,
    "C_Cpp.autocompleteAddParentheses": true,
    "editor.defaultFormatter": "ms-vscode.cpptools",
    "C_Cpp.vcFormat.newLine.beforeOpenBrace.block": "newLine",
    "C_Cpp.vcFormat.indent.accessSpecifiers": true,
    "C_Cpp.vcFormat.indent.braces": true,
    "C_Cpp.vcFormat.indent.caseContentsWhenBlock": true,
    "C_Cpp.vcFormat.indent.caseLabels": true,
    "C_Cpp.vcFormat.newLine.beforeOpenBrace.function": "newLine",
    "C_Cpp.vcFormat.newLine.closeBraceSameLine.emptyFunction": true,
    "C_Cpp.vcFormat.newLine.scopeBracesOnSeparateLines": true,
    "C_Cpp.vcFormat.space.betweenEmptyBraces": true,
    "C_Cpp.vcFormat.space.betweenEmptyLambdaBrackets": true,
    "C_Cpp.vcFormat.space.pointerReferenceAlignment": "right",
    "files.autoSave": "onFocusChange",
    "C_Cpp.clang_format_style": "file:D:\\Learn/.clang-format.log",
    "C_Cpp.formatting": "clangFormat",
    "diffEditor.codeLens": true,
    "editor.mouseWheelZoom": true,
    "editor.unicodeHighlight.invisibleCharacters": false,
    "editor.detectIndentation": false,
    "workbench.iconTheme": "vscode-icons",
    "markdown-preview-github-styles.colorTheme": "light",
    "markdown-preview-github-styles.darkTheme": "light",
    "gitlens.views.worktrees.files.layout": "list",
    "editor.formatOnPaste": true,
    "gitlens.hovers.currentLine.over": "line",
    "gitlens.codeLens.enabled": false,
    "explorer.confirmDelete": false,
    "gitlens.views.commits.files.layout": "list",
    "diffEditor.ignoreTrimWhitespace": false,
    "diffEditor.renderSideBySide": true,
    "gitlens.views.commitDetails.files.layout": "tree",
    "gitlens.views.searchAndCompare.files.layout": "tree",
    "workbench.colorCustomizations": {},
    "git.suggestSmartCommit": false,
    "vsicons.dontShowNewVersionMessage": true,
    "cpp-check-lint.cppcheck.--inline-suppr": false,
    "cpp-check-lint.cppcheck.--language=": "c",
    "window.zoomLevel": 0,
    "gitlens.home.preview.enabled": false,
}
```

## Reference
- [# VScode 常用快捷键汇总](https://learnku.com/articles/34744)

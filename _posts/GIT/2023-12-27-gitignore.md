---
title: "gitignore文件: 如何在Git中忽略文件和文件夹"
description: 
date: 2023-12-27 10:00:00 +0800
categories: [GIT]
tags: [GIT, gitignore]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

在 .gitignore 中，你可以通过提及特定文件或文件夹的名称或模式来告诉 Git 只忽略一个文件或一个文件夹。你也可以用同样的方法告诉 Git 忽略多个文件或文件夹。

## 如何在 Git 中忽略一个文件和文件夹

如果你想只忽略一个特定的文件，你需要提供该文件在项目根目录下的完整路径。

例如，如果你想忽略位于根目录下的 `text.txt` 文件，你可以做如下操作：
```bash
/text.txt
```

而如果你想忽略一个位于根目录下的 `test` 目录中的 `text.txt` 文件，你要做的是：
```bash
/test/text.txt
```

你也可以这样写上述内容：
```bash
test/text.txt
```

如果你想忽略所有具有特定名称的文件，你需要写出该文件的字面名称。

例如，如果你想忽略任何 `text.txt` 文件，你可以在 `.gitignore` 中添加以下内容：
```bash
text.txt
```

在这种情况下，你不需要提供特定文件的完整路径。这种模式将忽略位于项目中任何地方的具有该特定名称的所有文件。

要忽略整个目录及其所有内容，你需要包括目录的名称，并在最后加上斜线 `/`：
```bash
test/
```

这个命令将忽略位于你的项目中任何地方的名为 `test` 的目录（包括目录中的其他文件和其他子目录）。

需要注意的是，如果你只写一个文件的名字或者只写目录的名字而不写斜线 `/`，那么这个模式将同时匹配任何带有这个名字的文件或目录：

```bash
# 匹配任何名字带有 test 的文件和目录
test
```

如果你想忽略任何以特定单词开头的文件或目录怎么办？

例如，你想忽略所有名称以 `img` 开头的文件和目录。要做到这一点，你需要指定你想忽略的名称，后面跟着 `*` 通配符选择器，像这样：
```bash
img*
```

这个命令将忽略所有名字以 `img` 开头的文件和目录。

但是，如果你想忽略任何以特定单词结尾的文件或目录呢？

如果你想忽略所有以特定文件扩展名结尾的文件，你需要使用 `*` 通配符选择器，后面跟你想忽略的文件扩展名。

例如，如果你想忽略所有以 `.md` 文件扩展名结尾的 markdown 文件，你可以在你的 `.gitignore` 文件中添加以下内容：
```bash
*.md
```
这个模式将匹配位于项目中任何地方的以 `.md` 为扩展名的任何文件。

前面，你看到了如何忽略所有以特定后缀结尾的文件。当你想做一个例外，而有一个后缀的文件你不想忽略的时候，会发生什么？

假设你在你的 `.gitignore` 文件中添加了以下内容：
```bash
.md
```

这个模式会忽略所有以 `.md` 结尾的文件，但你不希望 Git 忽略一个 `README.md` 文件。

要做到这一点，你需要使用带有感叹号的否定模式，即 `!`，来排除一个本来会被忽略的文件：
```bash
# 忽略所有 .md 文件
.md
# 不忽略 README.md 文件
!README.md
```
在 `.gitignore` 文件中使用这两种模式，所有以 `.md` 结尾的文件都会被忽略，除了 `README.md` 文件。

需要记住的是，如果你忽略了整个目录，这个模式就不起作用。

例如，你忽略了所有的 `test` 目录：
```bash
test/
```
假设在一个 `test` 文件夹内，你有一个文件，`example.md`，你不想忽略它。
你不能像这样在一个被忽略的目录内排除一个文件：
```bash
# 忽略所有名字带有 test 的目录
test/
# 试图在一个被忽略的目录内排除一个文件是行不通的
!test/example.md
```

## 如何忽略以前提交的文件
当你创建一个新的仓库时，最好的做法是创建一个 `.gitignore` 文件，包含所有你想忽略的文件和不同的文件模式--在提交之前。

Git 只能忽略尚未提交到仓库的未被追踪的文件。

如果你过去已经提交了一个文件，但希望没有提交，会发生什么？

比如你不小心提交了一个存储环境变量的 `.env` 文件。

你首先需要更新 `.gitignore` 文件以包括 `.env` 文件：
```bash
# 给 .gitignore 添加 .env 文件
echo ".env" >> .gitignore
```

现在，你需要告诉 Git 不要追踪这个文件，把它从索引中删除：
```bash
git rm --cached .env
```

`git rm` 命令，连同 `--cached` 选项，从版本库中删除文件，但不删除实际的文件。这意味着该文件仍然在你的本地系统和工作目录中作为一个被忽略的文件。

`git status` 会显示该文件已不在版本库中，而输入 `ls` 命令会显示该文件存在于你的本地文件系统中。

如果你想从版本库和你的本地系统中删除该文件，省略 `--cached` 选项。
接下来，用 `git add` 命令将 `.gitignore` 添加到暂存区：
```bash
git add .gitignore
```

最后，用 `git commit` 命令提交 `.gitignore` 文件：
```bash
git commit -m "update ignored files"
```

## 忽略已经被追踪的文件夹
有时候，我们已经将文件夹添加到了Git版本控制中，但后来又决定将其忽略。这时我们需要做以下步骤来忽略该文件夹。

首先，从Git中删除该文件夹，但保留在本地文件系统中：
```bash
git rm --cached -r [folder-name]
```

接下来，在.gitignore中添加该文件夹的规则：
```bash
/[folder-name]/
```

最后，将.gitignore文件和删除的文件夹重新添加到Git版本控制中：
```bash
git add .gitignore [folder-name]
git status
```

现在该文件夹将被忽略，并且不会在Git的状态中显示出来。

## Reference
- [# .gitignore 文件——如何在 Git 中忽略文件和文件夹](https://www.freecodecamp.org/chinese/news/gitignore-file-how-to-ignore-files-and-folders-in-git/)

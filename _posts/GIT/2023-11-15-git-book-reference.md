---
title: "Git ProGit Note"
description: ProGit 阅读笔记
date: 2023-11-15 10:00:00 +0800
categories: [GIT]
tags: [GIT, ProGit, Note]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

![输入图片说明](/imgs/git-book-reference/2023-11-17/kOAL2OWj7bWgq7iW.png)

# Git 基础
许多人习惯用复制整个项目目录的方式来保存不同的版本，或许还会改名加上备份时间以示区别。 这么做唯一的好处就是简单，但是特别容易犯错。 有时候会混淆所在的工作目录，一不小心会写错文件或者覆盖意想外的文件。

你执行的 Git 操作，几乎只往 Git 数据库中 **添加** 数据。 你很难使用 Git 从数据库中删除数据，也就是说 Git 几乎不会执行任何可能导致文件不可恢复的操作。未提交更新时有可能丢失或弄乱修改的内容。但是一旦你提交快照到 Git 中， 就难以再丢失数据，特别是如果你定期的推送数据库到其它仓库的话。

Git 有三种状态，你的文件可能处于其中之一：**已修改（modified）** 、**已暂存（staged）** 和 **已提交（committed）**。  
![输入图片说明](/imgs/git-book-reference/2023-11-15/Liup5HCmrO5gtqfT.png)

**工作区**：就是你在电脑里能看到的目录。  
**暂存区**：英文叫 stage 或 index。一般存放在 .git 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。  
**版本库**：工作区有一个隐藏目录 .git，这个不算工作区，而是 Git 的版本库。  

## 获取 Git 仓库
### 在已存在目录中初始化仓库
```console
$ git init
```
该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。千万不可随意手动更改，否则会破坏仓库
### 克隆现有的仓库
```console
$ git clone https://github.com/libgit2/libgit2
```
从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。

## 记录每次更新到仓库
### 检查当前文件状态
```console
$ git status
```
查看目录下的件处于什么状态。未跟踪、跟踪（修改/删除/新增）  
>Untracked files 未跟踪  
>Changes not staged for commit 未暂存  
>Changes to be committed 已暂存未提交  

`git status` 命令的输出十分详细，但其用语有些繁琐。 Git 有一个选项可以帮你缩短状态命令的输出，这样可以以简洁的方式查看更改，使用 `git status -s` 命令，你将得到一种格式更为紧凑的输出。
```console
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```
新添加的未跟踪文件前面有 `??` 标记，新添加到暂存区中的文件前面有 `A` 标记，修改过的文件前面有 `M` 标记。 输出中有两栏，左栏指明了暂存区的状态，右栏指明了工作区的状态。例如，上面的状态报告显示： `README` 文件在工作区已修改但尚未暂存，而 `lib/simplegit.rb` 文件已修改且已暂存。 `Rakefile` 文件已修改，暂存后又作了修改，因此该文件的修改中既有已暂存的部分，又有未暂存的部分。

### 跟踪新文件
```console
$ git add <file>
$ git add .
```

### 暂存已修改的文件
添加至暂存区  
`git add test.h`暂存指定文件   
`git add dir/`递归暂存指定目录下所有文件  
`git add .`暂存目录下的所有文件  
>`git add` 命令是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。 将这个命令理解为“精确地将内容添加到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。

如果在 git add 提交test.h至暂存区后，又对文件进行了修改，那么该文件会同时出现在暂存区和非暂存区（工作区）。 实际上 Git 只不过暂存了你运行 git add 命令时的版本。 如果你现在提交，test.h 的版本是你最后一次运行 git add 命令时的那个版本，而不是你运行 git commit 时，在工作目录中的当前版本。 所以，运行了 git add 之后又作了修订的文件，需要重新运行 git add 把最新版本重新暂存起来。

### 忽略文件
创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。要养成一开始就为你的新仓库设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。  
星号（`*`）匹配零个或多个任意字符；`[abc]` 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（）表示匹配任意中间目录，比如 `a//z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等。
```
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

### 查看已暂存和未暂存的修改
```console
$ git diff
```
查看修改之后还没有暂存起来的变化内容。 此命令比较的是工作目录中当前文件和暂存区域快照之间的差异。  
>请注意，git diff 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 

```console
$ git diff --staged / git diff --cached
```
查看已暂存文件与最后一次提交的文件差异。
> `--staged` 和 `--cached` 是同义词

 ```console
$ git diff test.h
 ```
 查看指定文件修改内容  

### 提交更新
```console
$ git commit
```
提交之前，请务必确认还有什么已修改或新建的文件还没有 `git add` 过， 否则提交的时候不会记录这些尚未暂存的变化。 这些已修改但未暂存的文件只会保留在本地磁盘。 所以，每次准备提交前，先用 `git status` 看下，你所需要的文件是不是都已暂存起来了， 然后再运行提交命令 `git commit`。

`git commit`会启动你选择的文本编辑器来输入提交说明。
启动的编辑器中，默认的提交消息包含最后一次运行 `git status` 的输出，放在注释行里，另外开头还有一个空行，供你输入提交说明。退出编辑器时，Git 会丢弃注释行，用你输入的提交说明生成一次提交。 

另外，你也可以在 `commit` 命令后添加 `-m` 选项，将提交信息与命令放在同一行：`git commit -m "test.h修改"`

### 跳过使用暂存区域
```console
$ git commit -a -m 'added new benchmarks'
```
尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。 Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤。  
>这很方便，但是要小心，有时这个选项会将不需要的文件添加到提交中。

### 移除文件
```console
$ git rm test.h
```
删除文件并暂存，相当于手动删除文件并`git add test.h`添加到暂存区。  
如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项 `-f`

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从**暂存区域**移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用 `--cached` 选项：
```console
$ git rm --cached test.h
```
>注意：如果一个文件已经被commit，那么它将无法被彻底从仓库中移除。

`git rm` 命令后面可以列出文件或者目录的名字，也可以使用 `glob` 模式。比如：`git rm log/\*.log`，注意到星号 `*` 之前的反斜杠 `\`， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开。 此命令删除 `log/` 目录下扩展名为 `.log` 的所有文件。 类似的比如：`git rm \*~`，该命令会删除所有名字以 `~` 结尾的文件。

### 移动文件 ， 重命名文件
```console
$ git mv file_from file_to
```
其实，运行 `git mv` 就相当于运行了下面三条命令：
```console
$ mv README.md README
$ git rm README.md
$ git add README
```
如此分开操作，Git 也会意识到这是一次重命名，所以不管何种方式结果都一样。 两者唯一的区别在于，`git mv` 是一条命令而非三条命令，直接使用 `git mv` 方便得多。 不过在使用其他工具重命名文件时，记得在提交前 `git rm` 删除旧文件名，再 `git add` 添加新文件名。

## 查看提交历史
```console
$ git log
```
不传入任何参数的默认情况下，`git log` 会按时间先后顺序列出所有的提交，最近的更新排在最上面。
>`git log` 从当前分支下的当前版本查看之前的版本记录     
>`git log -g` 查看全局的仓库版本变化历史记录（包含回退记录）

`git log` 有许多选项可以帮助你搜寻你所要找的提交， 下面我们会介绍几个最常用的选项。

其中一个比较有用的选项是 `-p` 或 `--patch` ，它会显示每次提交所引入的差异（按 **补丁** 的格式输出）。 你也可以限制显示的日志条目数量，例如使用 `-2` 选项来只显示最近的两次提交：
```console
$ git log -p -2
```
 `-p` 选项除了显示基本信息之外，还附带了每次提交的变化。 当进行代码审查，或者快速浏览某个搭档的提交所带来的变化的时候，这个参数就非常有用了。

你也可以为 `git log` 附带一系列的总结性选项。 比如你想看到每次提交的简略统计信息，可以使用 `--stat` 选项：
```console
$ git log --stat
```
`--stat` 选项在每次提交的下面列出所有被修改过的文件、有多少文件被修改了以及被修改过的文件的哪些行被移除或是添加了。 在每次提交的最后还有一个总结。

另一个非常有用的选项是 `--pretty`。 这个选项可以使用不同于默认格式的方式展示提交历史。 这个选项有一些内建的子选项供你使用。 比如 `oneline` 会将每个提交放在一行显示，在浏览大量的提交时非常有用。 另外还有 `short`，`full` 和 `fuller` 选项，它们展示信息的格式基本一致，但是详尽程度不一：
```console
$ git log --pretty=oneline
```

最有意思的是 `format` ，可以定制记录的显示格式，这样的输出对后期提取分析格外有用——因为你知道输出的格式不会随着 Git 的更新而发生改变。  
```console
$ git log --pretty=format:"%h - %an, %ar : %s"
```

当 `oneline` 或 `format` 与另一个 `log` 选项 `--graph` 结合使用时尤其有用。 这个选项添加了一些 ASCII 字符串来形象地展示你的分支、合并历史。
```console
$ git log --pretty=format:"%h %s" --graph
```

#### 限制输出长度
除了定制输出格式的选项之外，`git log` 还有许多非常实用的限制输出长度的选项，也就是只输出一部分的提交。

>`-<n>`仅显示最近的 n 条提交。  
>`--since`, `--after`仅显示指定时间之后的提交。  
>`--until`, `--before`仅显示指定时间之前的提交。  
>`--author`仅显示作者匹配指定字符串的提交。  
>`--committer`仅显示提交者匹配指定字符串的提交。  
>`--grep`仅显示提交说明中包含指定字符串的提交。  
>`-S`仅显示添加或删除内容匹配指定字符串的提交。  

一个非常有用的过滤器是 `-S`（俗称“pickaxe”选项，取“用鹤嘴锄在土里捡石头”之意）， 它接受一个字符串参数，并且只会显示那些添加或删除了该字符串的提交。 假设你想找出添加或删除了对某一个特定函数的引用的提交，可以调用：
```console
$ git log -S function_name
```

一个很实用的 `git log` 选项是路径（path）， 如果只关心某些文件或者目录的历史提交，可以在 git log 选项的最后指定它们的路径。 因为是放在最后位置上的选项，所以用两个短划线（--）隔开之前的选项和后面限定的路径名。

## 撤消操作
有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 `--amend` 选项的提交命令来重新提交：
```console
$ git commit --amend
```
这个命令会将暂存区中的文件提交。 如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令）， 那么快照会保持不变，而你所修改的只是提交信息。文本编辑器启动后，可以看到之前的提交信息。 编辑后保存会覆盖原来的提交信息。

例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：
```console
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```
最终你只会有一个提交——第二次提交将代替第一次提交的结果。

当你在修补最后的提交时，与其说是修复旧提交，倒不如说是完全用一个 **新的提交** 替换旧的提交， 理解这一点非常重要。从效果上来说，就像是旧有的提交从未存在过一样，它并不会出现在仓库的历史中。修补提交最明显的价值是可以稍微改进你最后的提交，而不会让“啊，忘了添加一个文件”或者 “小修补，修正笔误”这种提交信息弄乱你的仓库历史。

### 撤销对文件的暂存（撤销git add）
例如，你已经修改了两个文件并且想要将它们作为两次独立的修改提交， 但是却意外地输入 `git add *` 暂存了它们两个。如何只取消暂存两个中的一个呢？ `git status` 告诉了你应该如何取消暂存：
```console
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   test.h
```
所以，我们可以用下列命令来取消暂存 `test.h` 文件，`test.h` 文件将会变为已修改未暂存的状态。  （撤销git add）
```console
$ git restore --staged test.h
```

### 撤消对文件的修改
如果你并不想保留工作区对 `test.h` 文件的修改怎么办？ 你该如何方便地撤消修改——将它还原成上次提交时的样子？ 幸运的是，`git status` 告诉了你应该如何做。  
```console
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   test.h
```
使用`git restore test.h`命令，将撤销在工作区的任何修改  
>修改后未提交到暂存区：回到最近的版本库状态  
>提交到暂存区后在工作区再次修改：回到添加至暂存区后的状态  
>总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。


>当使用`git restore --staged test.h`取消暂存时，只是将add这个操作撤销了，之前的修改仍保留在工作区，如果需要撤销工作区的修改，需要再次运行`git restore test.h`来撤销工作区的修改。

记住，在 Git 中任何 **已提交** 的东西几乎总是可以恢复的。 甚至那些被删除的分支中的提交或使用 `--amend` 选项覆盖的提交也可以恢复 。 然而，任何你未提交的东西丢失后很可能再也找不到了。

## 远程仓库的使用

### 查看远程仓库

如果想查看你已经配置的远程仓库服务器，可以运行 `git remote` 命令。 它会列出你指定的每一个远程服务器的简写。 如果你已经克隆了自己的仓库，那么至少应该能看到 origin ——这是 Git 给你克隆的仓库服务器的默认名字：
```console
$ git remote
origin
```

你也可以指定选项 `-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。
```console
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
```

### 添加远程仓库
使用`git clone` 命令克隆添加远程仓库的方式不再进行介绍。

添加一个新的远程 Git 仓库，同时指定一个方便使用的简写：
```console
$ git remote add <shortname> <url>
```

```console
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
pb	    https://github.com/paulboone/ticgit (fetch)
pb	    https://github.com/paulboone/ticgit (push)
```

现在你可以在命令行中使用字符串 `pb` 来代替整个 URL。 例如，如果你想拉取 Paul 的仓库中有但你没有的信息，可以运行 `git fetch pb`：
```console
$ git fetch pb
remote: Counting objects: 43, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 43 (delta 10), reused 31 (delta 5)
Unpacking objects: 100% (43/43), done.
From https://github.com/paulboone/ticgit
 * [new branch]      master     -> pb/master
 * [new branch]      ticgit     -> pb/ticgit
```
现在 Paul 的 master 分支可以在本地通过 `pb/master` 访问到——你可以将它合并到自己的某个分支中， 或者如果你想要查看它的话，可以检出一个指向该点的本地分支。

### 从远程仓库中抓取与拉取
如果你想拉取仓库中有但你没有的信息，可以运行：
```console
$ git fetch <remote>
```
如果你使用 `clone` 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。所以，`git fetch origin` 会抓取克隆（或上一次抓取）后新推送的所有工作。 必须注意 `git fetch` 命令只会将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。

如果你的当前分支设置了跟踪远程分支， 那么可以用 `git pull` 命令来自动抓取后合并该远程分支到当前分支：
```console
$ git pull <remote>
```
 这或许是个更加简单舒服的工作流程。默认情况下，`git clone` 命令会自动设置本地 master 分支跟踪克隆的远程仓库的 `master` 分支（或其它名字的默认分支）。 运行 `git pull` 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。

### 推送到远程仓库
当你想分享你的项目时，必须将其推送到上游。 这个命令很简单： 
```console
$ git push <remote> <branch>
```
当你想要将 `master` 分支推送到 `origin` 服务器时（再次说明，克隆时通常会自动帮你设置好那两个名字）， 那么运行这个命令就可以将你所做的备份到服务器：
```console
$ git push origin master
```
只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。 当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 你必须先抓取他们的工作并将其合并进你的工作后才能推送。

### 远程仓库的重命名与移除
你可以运行 `git remote rename` 来修改一个远程仓库的简写名。 例如，想要将 `pb` 重命名为 `paul`，可以用 `git remote rename` 这样做：
```console
$ git remote rename pb paul
$ git remote
origin
paul
```
值得注意的是这同样也会修改你所有远程跟踪的分支名字。 那些过去引用 `pb/master` 的现在会引用 `paul/master`。

如果因为一些原因想要移除一个远程仓库——你已经从服务器上搬走了或不再想使用某一个特定的镜像了， 又或者某一个贡献者不再贡献了——可以使用 `git remote remove` 或 `git remote rm` ：
```console
$ git remote remove paul
$ git remote
origin
```
一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起被删除。

## 打标签 tag
Git 可以给仓库历史中的某一个提交打上标签，以示重要。 比较有代表性的是人们会使用这个功能来标记发布结点（ `v1.0` 、 `v2.0` 等等）。

在 Git 中列出已有的标签非常简单，只需要输入 `git tag` （可带上可选的 `-l` 选项 `--list`）：
```console
$ git tag
```

### 创建标签
Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）。

轻量标签很像一个不会改变的分支——它只是某个特定提交的引用。

而附注标签是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。 通常会建议创建附注标签，这样你可以拥有以上所有信息。

### 附注标签
在 Git 中创建附注标签十分简单。 最简单的方式是当你在运行 `tag` 命令时指定 `-a` 选项：
```console
$ git tag -a v1.4 -m "my version 1.4"
```
通过使用 `git show` 命令可以看到标签信息和与之对应的提交信息：
```console
$ git show v1.4
```
输出显示了打标签者的信息、打标签的日期时间、附注信息，然后显示具体的提交信息。

### 轻量标签
另一种给提交打标签的方式是使用轻量标签。 轻量标签本质上是将提交校验和存储到一个文件中——没有保存任何其他信息。 创建轻量标签，不需要使用 `-a`、`-s` 或 `-m` 选项，只需要提供标签名字：
```console
$ git tag v1.5
```

### 后期打标签
你也可以对过去的提交打标签。
```console
$ git tag -a v1.2 9fceb02
```

### 共享标签
默认情况下，`git push` 命令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到共享服务器上。 这个过程就像共享远程分支一样——你可以运行 `git push origin <tagname>`。
```console
$ git push origin v1.5
```

如果想要一次性推送很多标签，也可以使用带有 `--tags` 选项的 `git push` 命令。 这将会把所有不在远程仓库服务器上的标签全部传送到那里。
```console
$ git push origin --tags
```

### 删除标签
要删除掉你本地仓库上的标签，可以使用命令 ：
```console
$ git tag -d <tagname>
```
注意上述命令并不会从任何远程仓库中移除这个标签，删除远程标签：
```console
$ git push origin --delete <tagname>
```

### 检出标签
如果你想查看某个标签所指向的文件版本，可以使用 `git checkout` 命令， 虽然这会使你的仓库处于“分离头指针（detached HEAD）”的状态——这个状态有些不好的副作用
```console
$ git checkout v2.0.0
```
在“分离头指针”状态下，如果你做了某些更改然后提交它们，标签不会发生变化， 但你的新提交将不属于任何分支，并且将无法访问，除非通过确切的提交哈希才能访问。 因此，如果你需要进行更改，比如你要修复旧版本中的错误，那么通常需要创建一个新分支：
```console
$ git checkout -b version2 v2.0.0
```

## Git 别名
Git 并不会在你输入部分命令时自动推断出你想要的命令。 如果不想每次都输入完整的 Git 命令，可以通过 `git config` 文件来轻松地为每一个命令设置一个别名。 这里有一些例子你可以试试：
```console
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.cm commit
$ git config --global alias.st status
```
这意味着，当要输入 `git commit` 时，只需要输入 `git cm`。 随着你继续不断地使用 Git，可能也会经常使用其他命令，所以创建别名时不要犹豫。

格式化log输出别名：
```console
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
创建该别名后，使用`git lg`就可以高效地输出格式化高的log内容了。

# Git 分支
使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线。有人把 Git 的分支模型称为它的“必杀技特性”，也正因为这一特性，使得 Git 从众多版本控制系统中脱颖而出。 为何 Git 的分支模型如此出众呢？ Git 处理分支的方式可谓是难以置信的轻量，创建新分支这一操作几乎能在瞬间完成，并且在不同分支之间的切换操作也是一样便捷。 与许多其它版本控制系统不同，Git 鼓励在工作流程中频繁地使用分支与合并，哪怕一天之内进行许多次。

## 分支简介
为了真正理解 Git 处理分支的方式，我们需要回顾一下 Git 是如何保存数据的。Git 保存的不是文件的变化或者差异，而是一系列不同时刻的 **快照** 。在进行提交操作时，Git 会保存一个提交对象（commit object）。 知道了 Git 保存数据的方式，我们可以很自然的想到——该提交对象会包含一个指向暂存内容快照的指针。 但不仅仅是这样，该提交对象还包含了作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针。 首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象， 而由多个分支合并产生的提交对象有多个父对象。

Git 的分支，其实本质上仅仅是指向提交对象的可变指针。 Git 的默认分支名字是 `master`。Git 的 `master` 分支并不是一个特殊分支。 它就跟其它分支完全没有区别。 之所以几乎每一个仓库都有 master 分支，是因为 `git init` 命令默认创建它，并且大多数人都懒得去改动它。

![分支及其提交历史](/imgs/git-book-reference/2023-11-15/gZrhY8swAxlYXitY.png)

### 分支创建
Git 是怎么创建新分支的呢？ 很简单，它只是为你创建了一个可以移动的新的指针。 比如，创建一个 testing 分支， 你需要使用 `git branch` 命令：
```
$ git branch testing
```
这会在当前所在的提交对象上创建一个指针。  
![输入图片说明](/imgs/git-book-reference/2023-11-15/VXfaQpYkCTELIlft.png)  
那么，Git 又是怎么知道当前在哪一个分支上呢？ 也很简单，它有一个名为 `HEAD` 的特殊指针，指向当前所在的本地分支（译注：将 `HEAD` 想象为当前分支的别名）。 在本例中，你仍然在 `master` 分支上。 因为 `git branch` 命令仅仅 **创建** 一个新分支，并不会自动切换到新分支中去。

**创建新分支的同时切换过去**：通常我们会在创建一个新分支后立即切换过去，这可以用 `git checkout -b <newbranchname>` 一条命令搞定。  (或`git switch -c <newbranchname>`)

### 分支切换
要切换到一个已存在的分支，你需要使用 `git checkout` 命令。 我们现在切换到新创建的 `testing` 分支去：
```console
$ git checkout testing
```
这样 `HEAD` 就指向 `testing` 分支了。  
![输入图片说明](/imgs/git-book-reference/2023-11-15/GquosgG2SFvhIpbY.png)  
那么，这样的实现方式会给我们带来什么好处呢？不妨进行一次修改并commit。  
![输入图片说明](/imgs/git-book-reference/2023-11-15/AEHJtNUpX7EIMIQP.png)  
如图所示，你的 `testing` 分支向前移动了，但是 `master` 分支却没有，它仍然指向运行 `git checkout` 时所指的对象。 这就有意思了，现在我们切换回 `master` 分支看看：  
```console
$ git checkout master
```
![输入图片说明](/imgs/git-book-reference/2023-11-15/nXZ9BWJNquDh37B2.png)  
这条命令做了两件事。 一是使 HEAD 指回 `master` 分支，二是将工作目录恢复成 `master` 分支所指向的快照内容。 也就是说，你现在做修改的话，项目将始于一个较旧的版本。 本质上来讲，这就是忽略 `testing` 分支所做的修改，以便于向另一个方向进行开发。

你可以在不同分支间不断地来回切换和工作，并在时机成熟时将它们合并起来。 而所有这些工作，你需要的命令只有 `branch`、`checkout` 和 `commit`。

你可以简单地使用 `git log` 命令查看分叉历史。 运行 `git log --oneline --decorate --graph --all` ，它会输出你的提交历史、各个分支的指向以及项目的分支分叉情况。

由于 Git 的分支实质上仅是包含所指对象校验和（长度为 40 的 SHA-1 值字符串）的文件，所以它的创建和销毁都异常高效。 创建一个新分支就相当于往一个文件中写入 41 个字节（40 个字符和 1 个换行符），如此的简单能不快吗？这与过去大多数版本控制系统形成了鲜明的对比，它们在创建分支时，将所有的项目文件都复制一遍，并保存到一个特定的目录。 完成这样繁琐的过程通常需要好几秒钟，有时甚至需要好几分钟。所需时间的长短，完全取决于项目的规模。 而在 Git 中，任何规模的项目都能在瞬间创建新分支。 同时，由于每次提交都会记录父对象，所以寻找恰当的合并基础（译注：即共同祖先）也是同样的简单和高效。 这些高效的特性使得 Git 鼓励开发人员频繁地创建和使用分支。

## 分支的新建与合并
想要新建一个分支并同时切换到那个分支上，你可以运行一个带有 `-b` 参数的 `git checkout` 命令：
```console
$ git checkout -b iss53
```
它是下面两条命令的简写：
```console
$ git branch iss53
$ git checkout iss53
```

假设：你准备解决问题追踪系统中的 #53 问题，你通过`git checkout -b iss53`新建了一个`iss53`分支并切换到该分支，你在 #53 问题上工作，并且做了一些提交。在此过程中，`iss53` 分支在不断的向前推进，因为你已经检出到该分支 （也就是说，你的 `HEAD` 指针指向了 `iss53` 分支）。

现在突然有个紧急问题等待你来解决。 有了 Git 的帮助，你不必把这个紧急问题和 `iss53` 的修改混在一起， 你也不需要花大力气来还原关于 53# 问题的修改，然后再添加关于这个紧急问题的修改，最后将这个修改提交到线上分支。 你所要做的仅仅是切换回 `master` 分支。

但是，在你这么做之前，要留意你的工作目录和暂存区里那些还没有被提交的修改， 它可能会和你即将检出的分支产生冲突从而阻止 Git 切换到该分支。 最好的方法是，在你切换分支之前，保持好一个干净的状态。 有一些方法可以绕过这个问题（即，暂存（stashing） 和 修补提交（commit amending））

现在，我们假设你已经把你的修改全部提交了，这时你可以切换回 `master` 分支了：
```console
$ git checkout master
```
这个时候，你的工作目录和你在开始 #53 问题之前一模一样，现在你可以专心修复紧急问题了。 请牢记：当你切换分支的时候，Git 会重置你的工作目录，使其看起来像回到了你在那个分支上最后一次提交的样子。 Git 会自动添加、删除、修改文件以确保此时你的工作目录和这个分支最后一次提交时的样子一模一样。

接下来，你要修复这个紧急问题。 我们来建立一个 `hotfix` 分支，在该分支上工作直到问题解决：
```console
$ git checkout -b hotfix
$ ...
$ git commit -a -m 'fixed the broken email address'
```
![输入图片说明](/imgs/git-book-reference/2023-11-16/cFGAvEbzriNrJkJm.png)  
你可以运行你的测试，确保你的修改是正确的，然后将 `hotfix` 分支合并回你的 `master` 分支来部署到线上。 你可以使用 `git merge` 命令来达到上述目的：
```console
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
...
```
在合并的时候，你应该注意到了“快进（fast-forward）”这个词。 由于你想要合并的分支 `hotfix` 所指向的提交 `C4` 是你所在的提交 `C2` 的直接后继， 因此 Git 会直接将指针向前移动。换句话说，当你试图合并两个分支时， 如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候， 只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”。  
>合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

>通过fast-forward方式合并会将dev分支的所有commit记录合并至master分支，如果想要将dev分支的多个提交过程合并成一个提交记录，使用`git merge --no-ff -m "merge with no-ff" dev`进行普通模式合并。

现在，最新的修改已经在 `master` 分支所指向的提交快照中，你可以着手发布该修复了。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/JQDDFGnXcGWhSMo5.png)  

这个紧急问题的解决方案发布之后，你准备回到被打断之前时的工作中。 然而，你应该先删除 `hotfix` 分支，因为你已经不再需要它了 —— `master` 分支已经指向了同一个位置。 你可以使用带 `-d` 选项的 `git branch` 命令来删除分支：
```console
$ git branch -d hotfix
```
现在你可以切换回你正在工作的分支继续你的工作，也就是针对 #53 问题的那个分支（iss53 分支）。
```console
$ git checkout iss53
...
$ git commit -a -m 'finished the new footer [issue 53]'
```
![输入图片说明](/imgs/git-book-reference/2023-11-16/aqHpKHWAeS8UqyV3.png)  
你在 `hotfix` 分支上所做的工作并没有包含到 `iss53` 分支中。 如果你需要拉取 `hotfix` 所做的修改，你可以使用 `git merge master` 命令将 `master` 分支合并入 `iss53` 分支，或者你也可以等到 `iss53` 分支完成其使命，再将其合并回 `master` 分支。

### 分支的合并
假设你已经修正了 #53 问题，并且打算将你的工作合并入 `master` 分支。 为此，你需要合并 `iss53` 分支到 `master` 分支，这和之前你合并 `hotfix` 分支所做的工作差不多。 你只需要检出到你想合并入的分支，然后运行 `git merge` 命令：
```console
$ git checkout master
$ git merge iss53
Merge made by the 'recursive' strategy.
index.html |    1 +
1 file changed, 1 insertion(+)
```
这和你之前合并 `hotfix` 分支的时候看起来有一点不一样。 在这种情况下，你的开发历史从一个更早的地方开始分叉开来（diverged）。 因为，`master` 分支所在提交并不是 `iss53` 分支所在提交的直接祖先，Git 不得不做一些额外的工作。 出现这种情况的时候，Git 会使用两个分支的末端所指的快照（`C4` 和 `C5`）以及这两个分支的公共祖先（`C2`），做一个简单的三方合并。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/M530LmRdW5YKECFM.png)  
和之前将分支指针向前推进所不同的是，Git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它。 这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/EjSJo4XeegbHrmsy.png)  
既然你的修改已经合并进来了，就不再需要 `iss53` 分支了。 现在你可以在任务追踪系统中关闭此项任务，并删除这个分支。  
```console
$ git branch -d iss53
```
### 遇到冲突时的分支合并
有时候合并操作不会如此顺利。 如果你在两个不同的分支中，对**同一个文件**的**同一个部分**进行了不同的修改，Git 就没法干净的合并它们（对于同一个文件不同部分的修改可以直接被合并工具合并，且并不会产生提示）。 如果你对 #53 问题的修改和有关 `hotfix` 分支的修改都涉及到同一个文件的同一处，在合并它们的时候就会产生合并冲突：
```console
$ git merge iss53
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```
此时 Git 做了合并，但是没有自动地创建一个新的合并提交。 Git 会暂停下来，等待你去解决合并产生的冲突。 你可以在合并冲突后的任意时刻使用 `git status` 命令来查看那些因包含合并冲突而处于未合并（unmerged）状态的文件：
```console
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

    both modified:      index.html

no changes added to commit (use "git add" and/or "git commit -a")
```
任何因包含合并冲突而有待解决的文件，都会以未合并状态标识出来。 Git 会在有冲突的文件中加入标准的冲突解决标记，这样你可以打开这些包含冲突的文件然后手动解决冲突。 出现冲突的文件会包含一些特殊区段，看起来像下面这个样子：
```
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```
这表示 `HEAD` 所指示的版本（也就是你的 `master` 分支所在的位置，因为你在运行 merge 命令的时候已经检出到了这个分支）在这个区段的上半部分（`=======` 的上半部分），而 `iss53` 分支所指示的版本在 `=======` 的下半部分。 为了解决冲突，你必须选择使用由 `=======` 分割的两部分中的一个，或者你也可以自行合并这些内容。 例如，你可以通过把这段内容换成下面的样子来解决冲突：

```html
<div id="footer">
please contact us at email.support@github.com
</div>
```
上述的冲突解决方案仅保留了其中一个分支的修改，并且 `<<<<<<<` , `=======` , 和 `>>>>>>>` 这些行被完全删除了。 在你解决了所有文件里的冲突之后，对每个文件使用 `git add` 命令来将其标记为冲突已解决。 一旦暂存这些原本有冲突的文件，Git 就会将它们标记为冲突已解决。

如果你想使用图形化工具来解决冲突，你可以运行 `git mergetool`，该命令会为你启动一个合适的可视化合并工具，并带领你一步一步解决这些冲突。

如果你想使用除默认工具（在这里 Git 使用 `opendiff` 做为默认的合并工具） 外的其他合并工具，你可以在 “下列工具中（one of the following tools）” 这句后面看到所有支持的合并工具。 然后输入你喜欢的工具名字就可以了。

等你退出合并工具之后，Git 会询问刚才的合并是否成功。 如果你回答是，Git 会暂存那些文件以表明冲突已解决： 你可以再次运行 `git status` 来确认所有的合并冲突都已被解决。
```console
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:

    modified:   index.html
```

如果你想撤销这次合并，可以使用下列命令来撤销此次合并记录：
```console
$ git merge --abort  
```

如果你对结果感到满意，并且确定之前有冲突的的文件都已经暂存了，这时你可以输入 `git commit` 来完成合并提交。  

## 分支管理
`git branch` 命令不只是可以创建与删除分支。 如果不加任何参数运行它，会得到当前所有分支的一个列表
```console
$ git branch
  iss53
* master
  testing
```
注意 `master` 分支前的 `*` 字符：它代表现在检出的那一个分支（也就是说，当前 `HEAD` 指针所指向的分支）。 这意味着如果在这时候提交，`master` 分支将会随着新的工作向前移动。 如果需要查看每一个分支的最后一次提交，可以运行 `git branch -v` 命令。

`--merged` 与 `--no-merged` 这两个有用的选项可以过滤这个列表中已经合并或尚未合并到当前分支的分支。 如果要查看哪些分支已经合并到当前分支，可以运行 `git branch --merged`：
```console
$ git branch --merged
  iss53
* master
```
因为之前已经合并了 `iss53` 分支，所以现在看到它在列表中。 在这个列表中分支名字前没有 `*` 号的分支通常可以使用 `git branch -d` 删除掉；你已经将它们的工作整合到了另一个分支，所以并不会失去任何东西。

查看所有包含未合并工作的分支，可以运行 `git branch --no-merged`。这里显示了其他分支。 因为它包含了还未合并的工作，尝试使用 `git branch -d` 命令删除它时会失败：
```console
$ git branch -d testing
error: The branch 'testing' is not fully merged.
If you are sure you want to delete it, run 'git branch -D testing'.
```
如果真的想要删除分支并丢掉那些工作，如同帮助信息里所指出的，可以使用 `-D` 选项强制删除它。

你总是可以提供一个附加的参数来查看其它分支的合并状态而不必检出它们。 例如，在testing分支查看尚未合并到 `master` 分支：
```console
$ git checkout testing
$ git branch --no-merged master
  topicA
  featureB
```

## 分支开发工作流
### 长期分支
许多使用 Git 的开发者都喜欢使用这种方式来工作，比如只在 `master` 分支上保留完全稳定的代码——有可能仅仅是已经发布或即将发布的代码。 他们还有一些名为 `develop` 或者 `next` 的平行分支，被用来做后续开发或者测试稳定性——这些分支不必保持绝对稳定，但是一旦达到稳定状态，它们就可以被合并入 `master` 分支了。 这样，在确保这些已完成的主题分支（短期分支，比如之前的 `iss53` 分支）能够通过所有测试，并且不会引入更多 bug 之后，就可以合并入主干分支中，等待下一次的发布。

通常把他们想象成流水线（work silos）可能更好理解一点，那些经过测试考验的提交会被遴选到更加稳定的流水线上去。

再次强调一下，使用多个长期分支的方法并非必要，但是这么做通常很有帮助，尤其是当你在一个非常庞大或者复杂的项目中工作时。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/HzI5o4oY8ymvFRd9.png)

### 主题分支
主题分支对任何规模的项目都适用。 主题分支是一种短期分支，它被用来实现单一特性或其相关工作。

你已经在上一节中你创建的 `iss53` 和 `hotfix` 主题分支中看到过这种用法。 你在上一节用到的主题分支（`iss53` 和 `hotfix` 分支）中提交了一些更新，并且在它们合并入主干分支之后，你又删除了它们。 这项技术能使你快速并且完整地进行上下文切换（context-switch）——因为你的工作被分散到不同的流水线中，在不同的流水线中每个分支都仅与其目标特性相关，因此，在做代码审查之类的工作的时候就能更加容易地看出你做了哪些改动。 你可以把做出的改动在主题分支中保留几分钟、几天甚至几个月，等它们成熟之后再合并，而不用在乎它们建立的顺序或工作进度。

考虑这样一个例子，你在 `master` 分支上工作到 `C1`，这时为了解决一个问题而新建 `iss91` 分支，在 `iss91` 分支上工作到 `C4`，然而对于那个问题你又有了新的想法，于是你再新建一个 `iss91v2` 分支试图用另一种方法解决那个问题，接着你回到 `master` 分支工作了一会儿，你又冒出了一个不太确定的想法，你便在 `C10` 的时候新建一个 `dumbidea` 分支，并在上面做些实验。 你的提交历史看起来像下面这个样子：  
![输入图片说明](/imgs/git-book-reference/2023-11-16/7MOMjfgfqAfkWU29.png)  
现在，我们假设两件事情：你决定使用第二个方案来解决那个问题，即，使用在 `iss91v2` 分支中方案。 另外，你将 `dumbidea` 分支拿给你的同事看过之后，结果发现这是个惊人之举。 这时你可以抛弃 `iss91` 分支（即丢弃 `C5` 和 `C6` 提交），然后把另外两个分支合并入主干分支。 最终你的提交历史看起来像下面这个样子：
![输入图片说明](/imgs/git-book-reference/2023-11-16/FjHgfcpuS0ZhIANT.png)  

请牢记，当你做这么多操作的时候，这些分支全部都存于本地。 当你新建和合并分支的时候，所有这一切都只发生在你本地的 Git 版本库中 —— 没有与服务器发生交互。

## 远程分支
Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。怎么分布呢？最早，肯定只有一台机器有一个原始版本库，此后，别的机器可以“克隆”这个原始版本库，而且每台机器的版本库其实都是一样的，并没有主次之分。

实际情况往往是这样，找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。

**“origin” 并无特殊含义**
远程仓库名字 “origin” 与分支名字 “master” 一样，在 Git 中并没有任何特别的含义一样。 同时 “master” 是当你运行 `git init` 时默认的起始分支名字，原因仅仅是它的广泛使用， “origin” 是当你运行 `git clone` 时默认的远程仓库名字。 如果你运行 `git clone -o booyah`，那么你默认的远程分支名字将会是 `booyah/master`。origin只是远程仓库在本地的一个别名，在远程仓库中，主分支仍为master。

远程分支以 `<remote>/<branch>` 的形式命名。 例如，如果你想要看你最后一次与远程仓库 `origin` 通信时 `master` 分支的状态，你可以查看 `origin/master` 分支。 你与同事合作解决一个问题并且他们推送了一个 `iss53` 分支，你可能有自己的本地 `iss53` 分支， 然而在服务器上的分支会以 `origin/iss53` 来表示。

这可能有一点儿难以理解，让我们来看一个例子。 假设你的网络里有一个在 `git.ourcompany.com` 的 Git 服务器。 如果你从这里克隆，Git 的 `clone` 命令会为你自动将其命名为 `origin`，拉取它的所有数据， 创建一个指向它的 `master` 分支的指针，并且在本地将其命名为 `origin/master`。 Git 也会给你一个与 origin 的 `master` 分支在指向同一个地方的本地 `master` 分支，这样你就有工作的基础。

![输入图片说明](/imgs/git-book-reference/2023-11-16/w1YE7ySISNTGhzCZ.png)  
如果你在本地的 `master` 分支做了一些工作，在同一段时间内有其他人推送提交到 `git.ourcompany.com` 并且更新了它的 `master` 分支，这就是说你们的提交历史已走向不同的方向。 即便这样，只要你保持不与 `origin` 服务器连接（并拉取数据），你的 `origin/master` 指针就不会移动。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/0Za1JcMvclOgWmID.png)  
如果要与给定的远程仓库同步数据，运行 `git fetch <remote>` 命令（在本例中为 `git fetch origin`）。 这个命令查找 `` `origin'' 是哪一个服务器（在本例中，它是 `git.ourcompany.com ``）， 从中抓取本地没有的数据，并且更新本地数据库，移动 `origin/master` 指针到更新之后的位置。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/Y7RsJa901wObdPBX.png)  

为了演示有多个远程仓库与远程分支的情况，我们假定你有另一个内部 Git 服务器，仅服务于你的某个敏捷开发团队。 这个服务器位于 `git.team1.ourcompany.com`。 你可以运行 `git remote add` 命令添加一个新的远程仓库引用到当前的项目。 将这个远程仓库命名为 `teamone`，将其作为完整 URL 的缩写。（此时只是将该远程仓库引用加入到了该项目中，使其可见）  
![输入图片说明](/imgs/git-book-reference/2023-11-16/E7cmkVrAXuB0NdFF.png)  
现在，可以运行 `git fetch teamone` 来抓取远程仓库 `teamone` 有而本地没有的数据。 因为那台服务器上现有的数据是 `origin` 服务器上的一个子集， 所以 Git 并不会抓取数据而是会设置远程跟踪分支 `teamone/master` 指向 `teamone` 的 `master` 分支。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/91LIUVuY6HijLnzQ.png)  

### 推送
当你想要公开分享一个分支时，需要将其推送到有写入权限的远程仓库上。 本地的分支并不会自动与远程仓库同步——你必须显式地推送想要分享的分支。 这样，你就可以把不愿意分享的内容放到私人分支上，而将需要和别人协作的内容推送到公开分支。  
如果希望和别人一起在名为 `serverfix` 的分支上工作，你可以像推送第一个分支那样推送它。 运行 `git push <remote> <branch>`:  
```console
$ git push origin serverfix
Counting objects: 24, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (24/24), 1.91 KiB | 0 bytes/s, done.
Total 24 (delta 2), reused 0 (delta 0)
To https://github.com/schacon/simplegit
 * [new branch]      serverfix -> serverfix
```
你也可以运行 `git push origin serverfix:serverfix`， 它会做同样的事——也就是说“推送本地的 `serverfix` 分支，将其作为远程仓库的 `serverfix` 分支” ，可以通过这种格式来推送本地分支到一个命名不相同的远程分支。 

如果并不想让远程仓库上的分支叫做 `serverfix`，可以运行 `git push origin serverfix:awesomebranch` 来将本地的 `serverfix` 分支推送到远程仓库上的 `awesomebranch` 分支。

下一次其他协作者从服务器上抓取数据时，他们会在本地生成一个远程分支 `origin/serverfix`，指向服务器的 `serverfix` 分支的引用：
```console
$ git fetch origin
remote: Counting objects: 7, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0)
Unpacking objects: 100% (3/3), done.
From https://github.com/schacon/simplegit
 * [new branch]      serverfix    -> origin/serverfix
```
要特别注意的一点是当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本（拷贝）。 换一句话说，这种情况下，不会有一个新的 `serverfix` 分支——只有一个不可以修改的 `origin/serverfix` 指针。

如果想要在自己的 `serverfix` 分支上工作，可以将其建立在远程跟踪分支之上：
```console
$ git checkout -b serverfix origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```
这会给你一个用于工作的本地分支，并且起点位于 `origin/serverfix`。

### 跟踪分支
从一个远程跟踪分支`origin/serverfix`检出一个本地分支会自动创建所谓的“跟踪分支 `serverfix` ”（它跟踪的分支叫做“上游分支”）。 跟踪分支`serverfix` 是与远程分支`origin/serverfix`有直接关系的本地分支 `serverfix` 。 如果在一个跟踪分支上输入 `git pull`，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。

当克隆一个仓库时，它通常会自动地创建一个跟踪 `origin/master` 的 `master` 分支。

像之前看到的那样，运行 `git checkout -b <branch> <remote>/<branch>`将会从一个远程跟踪分支检出一个本地分支会自动创建所谓的“跟踪分支”。 这是一个十分常用的操作，所以 Git 提供了 `--track` 快捷方式：
```console
$ git checkout --track origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

由于这个操作太常用了，该捷径本身还有一个捷径。 如果你尝试检出的分支 (a) 不存在且 (b) 刚好只有一个名字与之匹配的远程分支，那么 Git 就会为你创建一个跟踪分支：
```console
$ git checkout serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

如果想要将本地分支与远程分支设置为不同的名字，你可以轻松地使用上一个命令增加一个不同名字的本地分支：
```console
$ git checkout -b sf origin/serverfix
Branch sf set up to track remote branch serverfix from origin.
Switched to a new branch 'sf'
```
现在，本地分支 `sf` 会自动从 `origin/serverfix` 拉取。
如果想要查看设置的所有跟踪分支，可以使用 `git branch` 的 `-vv` 选项。 这会将所有的本地分支列出来并且包含更多的信息，如每一个分支正在跟踪哪个远程分支与本地分支是否是领先、落后或是都有。

```console
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```
这里可以看到 `iss53` 分支正在跟踪 `origin/iss53` 并且 “ahead” 是 2，意味着本地有两个提交还没有推送到服务器上。 也能看到 `master` 分支正在跟踪 `origin/master` 分支并且是最新的。 接下来可以看到 `serverfix` 分支正在跟踪 `teamone` 服务器上的 `server-fix-good` 分支并且领先 3 落后 1， 意味着服务器上有一次提交还没有合并入同时本地有三次提交还没有推送。 最后看到 `testing` 分支并没有跟踪任何远程分支。

需要重点注意的一点是这些数字的值来自于你从每个服务器上最后一次抓取的数据。 这个命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据。 如果想要统计最新的领先与落后数字，需要在运行此命令前抓取所有的远程仓库。 可以像这样做：
```console
$ git fetch --all; git branch -vv
```
### 拉取
当 `git fetch` 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。 它只会获取数据然后让你自己合并。 然而，有一个命令叫作 `git pull` 在大多数情况下它的含义是一个 `git fetch` 紧接着一个 `git merge` 命令。 如果有一个像之前章节中演示的设置好的跟踪分支，不管它是显式地设置还是通过 `clone` 或 `checkout` 命令为你创建的，`git pull` 都会查找当前分支所跟踪的服务器与分支， 从服务器上抓取数据然后尝试合并入那个远程分支。

由于 `git pull` 的魔法经常令人困惑所以通常单独显式地使用 `fetch` 与 `merge` 命令会更好一些。

### 删除远程分支
假设你已经通过远程分支做完所有的工作了——也就是说你和你的协作者已经完成了一个特性， 并且将其合并到了远程仓库的 `master` 分支（或任何其他稳定代码分支）。 可以运行带有 `--delete` 选项的 `git push` 命令来删除一个远程分支。 如果想要从服务器上删除 `serverfix` 分支，运行下面的命令：
```console
$ git push origin --delete serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```
基本上这个命令做的只是从服务器上移除这个指针。 Git 服务器通常会保留数据一段时间直到垃圾回收运行，所以如果不小心删除掉了，通常是很容易恢复的。

## 变基
在 Git 中整合来自不同分支的修改主要有两种方法：`merge` 以及 `rebase`。这两种整合方法的最终结果没有任何区别，但是变基使得提交历史更加整洁。 你在查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的， 但它们看上去就像是串行的一样，提交历史是一条直线没有分叉。

请注意，无论是通过变基，还是通过三方合并，整合的最终结果所指向的快照始终是一样的，只不过提交历史不同罢了。 变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。

### 有趣的变基例子
你创建了一个主题分支 `server`，为服务端添加了一些功能，提交了 `C3` 和 `C4`。 然后从 `C3` 上创建了主题分支 `client`，为客户端添加了一些功能，提交了 `C8` 和 `C9`。 最后，你回到 `server` 分支，又提交了 `C10`。
![输入图片说明](/imgs/git-book-reference/2023-11-16/VaLVmRglbwpDrcX8.png)

假设你希望将 `client` 中的修改合并到主分支并发布，但暂时并不想合并 `server` 中的修改， 因为它们还需要经过更全面的测试。这时，你就可以使用 `git rebase` 命令的 `--onto` 选项， 选中在 `client` 分支里但不在 `server` 分支里的修改（即 `C8` 和 `C9`），将它们在 `master` 分支上重放：
```console
$ git rebase --onto master server client
```
以上命令的意思是：“取出 `client` 分支，找出它从 `server` 分支分歧之后的补丁， 然后把这些补丁在 `master` 分支上重放一遍，让 `client` 看起来像直接基于 `master` 修改一样”。这理解起来有一点复杂，不过效果非常酷。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/IF2pLd91fYiDVoHN.png)  
现在可以快进合并 `master` 分支了。（如图 快进合并 `master` 分支，使之包含来自 `client` 分支的修改）：  
```console
$ git checkout master
$ git merge client
```
![输入图片说明](/imgs/git-book-reference/2023-11-16/3gIiOE6yA9p4EWJS.png)

接下来你决定将 `server` 分支中的修改也整合进来。 使用 `git rebase <basebranch> <topicbranch>` 命令可以直接将主题分支 （即本例中的 `server`）变基到目标分支（即 `master`）上。 这样做能省去你先切换到 `server` 分支，再对其执行变基命令的多个步骤。
```console
$ git rebase master server
```
如图 将 `server` 中的修改变基到 `master` 上所示，`server` 中的代码被“续”到了 `master` 后面。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/m9Z2CXH9ftpAPrs0.png)  
然后就可以快进合并主分支 `master` 了：  
```console
$ git checkout master
$ git merge server
```
至此，`client` 和 `server` 分支中的修改都已经整合到主分支里了， 你可以删除这两个分支：
```console
$ git branch -d client
$ git branch -d server
```
最终提交历史会变成图中的样子：  
![输入图片说明](/imgs/git-book-reference/2023-11-16/HGgI3pmKuwW5uW3V.png)

### 变基的风险
**如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基。**
变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。 如果你已经将提交推送至某个仓库，而其他人也已经从该仓库拉取提交并进行了后续工作，此时，如果你用 `git rebase` 命令重新整理了提交并再次推送，你的同伴因此将不得不再次将他们手头的工作与你的提交进行整合，如果接下来你还要拉取并整合他们修改过的提交，事情就会变得一团糟。

### 变基 vs. 合并
至此，你已在实战中学习了变基和合并的用法，你一定会想问，到底哪种方式更好。 在回答这个问题之前，让我们退后一步，想讨论一下提交历史到底意味着什么。

有一种观点认为，仓库的提交历史即是 **记录实际发生过什么**。 它是针对历史的文档，本身就有价值，不能乱改。 从这个角度看来，改变提交历史是一种亵渎，你使用 谎言掩盖了实际发生过的事情。 如果由合并产生的提交历史是一团糟怎么办？ 既然事实就是如此，那么这些痕迹就应该被保留下来，让后人能够查阅。

另一种观点则正好相反，他们认为提交历史是 **项目过程中发生的事**。 没人会出版一本书的第一版草稿，软件维护手册也是需要反复修订才能方便使用。 持这一观点的人会使用 `rebase` 及 `filter-branch` 等工具来编写故事，怎么方便后来的读者就怎么写。

现在，让我们回到之前的问题上来，到底合并还是变基好？希望你能明白，这并没有一个简单的答案。 Git 是一个非常强大的工具，它允许你对提交历史做许多事情，但每个团队、每个项目对此的需求并不相同。 既然你已经分别学习了两者的用法，相信你能够根据实际情况作出明智的选择。

**总的原则是，只对尚未推送或分享给别人的本地修改执行变基操作清理历史， 从不对已推送至别处的提交执行变基操作，这样，你才能享受到两种方式带来的便利。**

## 一个简单的多人 Git 工作流程的通常事件顺序
![输入图片说明](/imgs/git-book-reference/2023-11-16/rl5BmEtPNrZ2C0Oc.png)


# Git 工具
## 选择修订版本
### 引用日志

当你在工作时， Git 会在后台保存一个引用日志（reflog）， 引用日志记录了最近几个月你的 HEAD 和分支引用所指向的历史。  
你可以使用 `git reflog` 来查看引用日志
```console
$ git reflog
734713b HEAD@{0}: commit: fixed refs handling, added gc auto, updated
d921970 HEAD@{1}: merge phedders/rdocs: Merge made by the 'recursive' strategy.
1c002dd HEAD@{2}: commit: added some blame and merge stuff
1c36188 HEAD@{3}: rebase -i (squash): updating HEAD
95df984 HEAD@{4}: commit: # This is a combination of two commits.
1c36188 HEAD@{5}: rebase -i (squash): updating HEAD
7e05da5 HEAD@{6}: rebase -i (pick): updating HEAD
```
每当你的 HEAD 所指向的位置发生了变化，Git 就会将这个信息存储到引用日志这个历史记录里。 你也可以通过 reflog 数据来获取之前的提交历史。 如果你想查看仓库中 HEAD 在五次前的所指向的提交，你可以使用 `@{n}` 来引用 reflog 中输出的提交记录。
```console
$ git show HEAD@{5}
```
可以运行 `git log -g` 来查看类似于 `git log` 输出格式的引用日志信息：
```console
$ git reflog
734713b HEAD@{0}: commit: fixed refs handling, added gc auto, updated
d921970 HEAD@{1}: merge phedders/rdocs: Merge made by the 'recursive' strategy.
1c002dd HEAD@{2}: commit: added some blame and merge stuff
1c36188 HEAD@{3}: rebase -i (squash): updating HEAD
95df984 HEAD@{4}: commit: # This is a combination of two commits.
1c36188 HEAD@{5}: rebase -i (squash): updating HEAD
7e05da5 HEAD@{6}: rebase -i (pick): updating HEAD
```
值得注意的是，引用日志只存在于本地仓库，它只是一个记录你在 **自己** 的仓库里做过什么的日志。 其他人拷贝的仓库里的引用日志不会和你的相同，而你新克隆一个仓库的时候，引用日志是空的，因为你在仓库里还没有操作。

### 提交区间
#### 双点
最常用的指明提交区间语法是双点。 这种语法可以让 Git 选出在一个分支中而不在另一个分支中的提交。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/LlkmihGrGSVK7vcW.png)  

你想要查看 experiment 分支中还有哪些提交尚未被合并入 master 分支。 你可以使用 `master..experiment` 来让 Git 显示这些提交。也就是“在 experiment 分支中而不在 master 分支中的提交”。 为了使例子简单明了，我使用了示意图中提交对象的字母来代替真实日志的输出，所以会显示：
```console
$ git log master..experiment
D
C
```
反过来，如果你想查看在 `master` 分支中而不在 `experiment` 分支中的提交，你只要交换分支名即可。 `experiment..master` 会显示在 `master` 分支中而不在 `experiment` 分支中的提交：
```console
$ git log experiment..master
F
E
```
这可以让你保持 `experiment` 分支跟随最新的进度以及查看你即将合并的内容。 另一个常用的场景是查看你即将推送到远端的内容：
```console
$ git log origin/master..HEAD
```
这个命令会输出在你当前分支中而不在远程 `origin` 中的提交。 如果你执行 `git push` 并且你的当前分支正在跟踪 `origin/master`，由 `git log origin/master..HEAD` 所输出的提交就是会被传输到远端服务器的提交。如果你留空了其中的一边， Git 会默认为 `HEAD`。 例如， `git log origin/master..` 将会输出与之前例子相同的结果 —— Git 使用 HEAD 来代替留空的一边。

#### 多点
双点语法很好用，但有时候你可能需要两个以上的分支才能确定你所需要的修订， 比如查看哪些提交是被包含在某些分支中的一个，但是不在你当前的分支上。 Git 允许你在任意引用前加上 `^` 字符或者 `--not` 来指明你不希望提交被包含其中的分支。 因此下列三个命令是等价的：
```console
$ git log refA..refB
$ git log ^refA refB
$ git log refB --not refA
```
这个语法很好用，因为你可以在查询中指定超过两个的引用，这是双点语法无法实现的。 比如，你想查看所有被 `refA` 或 `refB` 包含的但是不被 `refC` 包含的提交，你可以使用以下任意一个命令：
```console
$ git log refA refB ^refC
$ git log refA refB --not refC
```
这就构成了一个十分强大的修订查询系统，你可以通过它来查看你的分支里包含了哪些东西。

#### 三点
最后一种主要的区间选择语法是三点，这个语法可以选择出被两个引用 **之一** 包含但又不被两者同时包含的提交。 再看看之前双点例子中的提交历史。 如果你想看 `master` 或者 `experiment` 中包含的但不是两者共有的提交，你可以执行：
```console
$ git log master...experiment
F
E
D
C
```
这和通常 `log` 按日期排序的输出一样，仅仅给出了4个提交的信息。
这种情形下，`log` 命令的一个常用参数是 `--left-right`，它会显示每个提交到底处于哪一侧的分支。 这会让输出数据更加清晰。
```console
$ git log --left-right master...experiment
< F
< E
> D
> C
```

有了这些工具，你就可以十分方便地查看你 Git 仓库中的提交。

## 贮藏与清理
有时，当你在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态， 而这时你想要切换到另一个分支做一点别的事情。 问题是，你不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。 针对这个问题的答案是 `git stash` 命令。

贮藏（stash）会处理工作目录的脏的状态——即跟踪文件的修改与暂存的改动——然后将未完成的修改保存到一个栈上， 而你可以在任何时候重新应用这些改动（甚至在不同的分支上）。

当想要切换分支，但是还不想要提交之前的工作，执行贮藏修改命令，运行 `git stash` 或 `git stash push`， 将新的贮藏推送到栈上。stash后可以看到工作目录是干净的了，此时可以顺利切换分支。

此时，你可以切换分支并在其他地方工作；你的修改被存储在栈上。 要查看贮藏的东西，可以使用 `git stash list`
```console
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
```
在本例中，有两个之前的贮藏，所以你接触到了三个不同的贮藏工作。 可以通过原来 stash 命令的帮助提示中的命令将你刚刚贮藏的工作重新应用：`git stash apply`。 如果想要应用其中一个更旧的贮藏，可以通过名字指定它，像这样：`git stash apply stash@{2}`。 如果不指定一个贮藏，Git 认为指定的是最近的贮藏。

并不是必须要有一个干净的工作目录，或者要应用到同一分支才能成功应用贮藏。 可以在一个分支上保存一个贮藏，切换到另一个分支，然后尝试重新应用这些修改。 当应用贮藏时工作目录中也可以有修改与未提交的文件——如果有任何东西不能干净地应用，Git 会产生合并冲突。


文件的改动被重新应用了（暂存后未恢复但是又进行了修改？新修改被舍弃），但是之前暂存的文件却没有重新暂存。 想要那样的话，必须使用 `--index` 选项来运行 `git stash apply` 命令，来尝试重新应用暂存的修改。 如果已经那样做了，那么你将回到原来的位置：
```console
$ git stash apply --index
```

应用选项只会尝试应用贮藏的工作——在堆栈上还有它。 可以运行 `git stash drop` 加上将要移除的贮藏的名字来移除它，也可以运行 `git stash pop` 来应用贮藏然后立即从栈上扔掉它。

如果指定了 `--patch` 标记，Git 不会贮藏所有修改过的任何东西， 但是会交互式地提示哪些改动想要贮藏、哪些改动需要保存在工作目录中。
```console
$ git stash --patch
```
### 从贮藏创建一个分支
如果贮藏了一些工作，将它留在那儿了一会儿，然后继续在贮藏的分支上工作，在重新应用工作时可能会有问题。 如果应用尝试修改刚刚修改的文件，你会得到一个合并冲突并不得不解决它。 如果想要一个轻松的方式来再次测试贮藏的改动，可以运行 `git stash branch <new branchname>` 以你指定的分支名创建一个新分支，检出贮藏工作时所在的提交，重新在那应用工作，然后在应用成功后丢弃贮藏：
```console
$ git stash branch testchanges
```
这是在新分支轻松恢复贮藏工作并继续工作的一个很不错的途径。

## 搜索
无论仓库里的代码量有多少，你经常需要查找一个函数是在哪里调用或者定义的，或者显示一个方法的变更历史。 Git 提供了两个有用的工具来快速地从它的数据库中浏览代码和提交。
### Git Grep
Git 提供了一个 `grep` 命令，你可以很方便地从提交历史、工作目录、甚至索引中查找一个字符串或者正则表达式。  
相比于一些常用的搜索命令比如 `grep` 和 `ack`，`git grep` 命令有一些的优点。 第一就是速度非常快，第二是你不仅仅可以可以搜索工作目录，还可以搜索任意的 Git 树。 在上一个例子中，我们在一个旧版本的 Git 源代码中查找，而不是当前检出的版本。
### Git 日志搜索
或许你不想知道某一项在 **哪里** ，而是想知道是什么 **时候** 存在或者引入的。 `git log` 命令有许多强大的工具可以通过提交信息甚至是 diff 的内容来找到某个特定的提交。

例如，如果我们想找到 `ZLIB_BUF_MAX` 常量是什么时候引入的，我们可以使用 `-S` 选项 （在 Git 中俗称“鹤嘴锄（pickaxe）”选项）来显示新增和删除该字符串的提交。
```console
$ git log -S ZLIB_BUF_MAX --oneline
e01503b zlib: allow feeding more than 4GB in one go
ef49a7a zlib: zlib can only process 4GB at a time
```
如果我们查看这些提交的 diff，我们可以看到在 `ef49a7a` 这个提交引入了常量，并且在 `e01503b` 这个提交中被修改了。

如果你希望得到更精确的结果，你可以使用 `-G` 选项来使用正则表达式搜索。

#### 行日志搜索
行日志搜索是另一个相当高级并且有用的日志搜索功能。 在 `git log` 后加上 `-L` 选项即可调用，它可以展示代码中一行或者一个函数的历史。

例如，假设我们想查看 `zlib.c` 文件中 `git_deflate_bound` 函数的每一次变更， 我们可以执行 `git log -L :git_deflate_bound:zlib.c`。 Git 会尝试找出这个函数的范围，然后查找历史记录，并且显示从函数创建之后一系列变更对应的补丁。
```console
$ git log -L :git_deflate_bound:zlib.c
```

## 重写历史
**在满意之前不要推送你的工作**：
Git 的基本原则之一是，由于克隆中有很多工作是本地的，因此你可以 **在本地** 随便重写历史记录。 然而一旦推送了你的工作，那就完全是另一回事了，除非你有充分的理由进行更改，否则应该将推送的工作视为最终结果。 简而言之，在对它感到满意并准备与他人分享之前，应当避免推送你的工作。

### 修改最后一次提交
对于你的最近一次提交，你往往想做两件事情：简单地修改提交信息， 或者通过添加、移除或修改文件来更改提交实际的内容。

如果，你只是想修改最近一次提交的提交信息，那么很简单：
```console
$ git commit --amend
```
上面这条命令会将最后一次的提交信息载入到编辑器中供你修改。 当保存并关闭编辑器后，编辑器会将更新后的提交信息写入新提交中，它会成为新的最后一次提交。

另一方面，如果你想要修改最后一次提交的实际内容，那么流程很相似：首先作出你想要补上的修改， 暂存它们，然后用 `git commit --amend` 以新的改进后的提交来 **替换** 掉旧有的最后一次提交，

使用这个技巧的时候需要小心，因为修正会改变提交的 SHA-1 校验和。 它类似于一个小的变基——如果已经推送了最后一次提交就不要修正它。

当你在修补一次提交时，可以同时修改提交信息和提交内容。 如果你修补了提交的内容，那么几乎肯定要更新提交消息以反映修改后的内容。另一方面，如果你的修补是琐碎的（如修改了一个笔误或添加了一个忘记暂存的文件）， 那么之前的提交信息不必修改，你只需作出更改，暂存它们，然后通过以下命令避免不必要的编辑器环节即可：
```console
$ git commit --amend --no-edit
```

## 重置揭密
| 树 | 用途 |
|--|--|
| HEAD | 上一次提交的快照，下一次提交的父结点 |
| Index | 预期的下一次提交的快照 |
| Working Directory | 沙盒 |

#### HEAD
HEAD 是当前分支引用的指针，它总是指向该分支上的最后一次提交。 这表示 HEAD 将是下一次提交的父结点。

#### 索引
索引是你的 **预期的下一次提交**。 我们也会将这个概念引用为 Git 的“暂存区”，这就是当你运行 `git commit` 时 Git 看起来的样子。
Git 将上一次检出到工作目录中的所有文件填充到索引区，它们看起来就像最初被检出时的样子。 之后你会将其中一些文件替换为新版本，接着通过 `git commit` 将它们转换为树来用作新的提交。

#### 工作目录
最后，你就有了自己的 **工作目录**（通常也叫 **工作区**）。 另外两棵树以一种高效但并不直观的方式，将它们的内容存储在 `.git` 文件夹中。 工作目录会将它们解包为实际的文件以便编辑。 你可以把工作目录当做 **沙盒**。在你将修改提交到暂存区并记录到历史之前，可以随意更改。

### 工作流程

![输入图片说明](/imgs/git-book-reference/2023-11-16/AdA2cS9UtkW4TSJL.png)  
`git add` 暂存到索引，该命令获取工作目录中的内容，并将其复制到索引中  

`git commit`提交到版本库，它会取得索引中的内容并将它保存为一个永久的快照， 然后创建一个指向该快照的提交对象，最后更新 `master` 来指向本次提交。  
切换分支或克隆的过程也类似。 当检出一个分支时，它会修改 **HEAD** 指向新的分支引用，将 **索引** 填充为该次提交的快照， 然后将 **索引** 的内容复制到 **工作目录** 中。  

### reset
让我们跟着 `reset` 看看它都做了什么。 它以一种简单可预见的方式直接操纵这三棵树。 它做了三个基本操作。

#### 第 1 步：移动 HEAD

`reset` 做的第一件事是移动 HEAD 的指向（回退分支）。 这与`checkout` 改变 HEAD 自身不同（切换分支）；`reset` 移动 HEAD 指向的分支。 这意味着如果 HEAD 设置为 `master` 分支（例如，你正在 `master` 分支上）， 运行 `git reset 9e5e6a4` 将会使 `master` 指向 `9e5e6a4`。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/GrWD7ygOwiUHYtxA.png)  

无论你调用了何种形式的带有一个提交的 `reset`，它首先都会尝试这样做。 使用 `reset --soft`，它将仅仅停在那儿。

现在看一眼上图，理解一下发生的事情：它本质上是撤销了上一次 `git commit` 命令。 当你在运行 `git commit` 时，Git 会创建一个新的提交，并移动 HEAD 所指向的分支来使其指向该提交。 当你将它 `reset` 回 `HEAD~`（HEAD 的父结点）时，其实就是把该分支移动回原来的位置，而不会改变索引和工作目录。 现在你可以更新索引并再次运行 `git commit` 来完成 `git commit --amend` 所要做的事情了。

#### 第 2 步：更新索引（--mixed）

注意，如果你现在运行 `git status` 的话，就会看到新的 HEAD 和以绿色标出的它和索引之间的区别。

接下来，`reset` 会用 HEAD 指向的当前快照的内容来更新索引。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/qYleYnxsmhFMPLgK.png)

如果指定 `--mixed` 选项，`reset` 将会在这时停止。 这也是默认行为，所以如果没有指定任何选项（在本例中只是 `git reset HEAD~`），这就是命令将会停止的地方。

现在再看一眼上图，理解一下发生的事情：它依然会撤销一上次 `提交`，但还会 _取消暂存_ 所有的东西。 于是，我们回滚到了所有 `git add` 和 `git commit` 的命令执行之前。

#### 第 3 步：更新工作目录（--hard）

`reset` 要做的的第三件事情就是让工作目录看起来像索引。 如果使用 `--hard` 选项，它将会继续这一步。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/iXzMUcPeTPZFPeg8.png)

现在让我们回想一下刚才发生的事情。 你撤销了最后的提交、`git add` 和 `git commit` 命令以及工作目录中的所有工作。

必须注意，`--hard` 标记是 `reset` 命令唯一的**危险**用法，它也是 Git 会真正地销毁数据的仅有的几个操作之一。 其他任何形式的 `reset` 调用都可以轻松撤消，但是 `--hard` 选项不能，因为它**强制覆盖**了工作目录中的文件。 在这种特殊情况下，我们的 Git 数据库中的一个提交内还留有该文件的 **v3** 版本， 我们可以通过 `reflog` 来找回它。但是若该文件还未提交，Git 仍会覆盖它从而导致无法恢复。

#### 回顾
`reset` 命令会以特定的顺序重写这三棵树，在你指定以下选项时停止：  
1.  移动 HEAD 分支的指向 （若指定了 `--soft`，则到此停止）
2.  使索引看起来像 HEAD  （若未指定 `--hard`，则到此停止）
3.  使工作目录看起来像索引

>`git reset --hard HEAD^` 回退到上一版本  
`git reset --hard HEAD^^` 回退到上上版本  
`git reset --hard HEAD~10` 回退到前10个版本  
`git reflog` 查看所有的commit记录，含有版本号和提交记录  
`git reset --hard fec402d` 回退到指定的版本  


### 通过路径来重置
前面讲述了 `reset` 基本形式的行为，不过你还可以给它提供一个作用路径。 若指定了一个路径，`reset` 将会跳过第 1 步，并且将它的作用范围限定为指定的文件或文件集合。 这样做自然有它的道理，因为 HEAD 只是一个指针，你无法让它同时指向两个提交中各自的一部分。 不过**索引和工作目录**可以部分更新，所以重置会继续进行第 2、3 步。

现在，假如我们运行 `git reset file.txt` （这其实是 `git reset --mixed HEAD file.txt` 的简写形式，因为你既没有指定一个提交的 SHA-1 或分支，也没有指定 `--soft` 或 `--hard`），它会将`file.txt` 从 HEAD 复制到索引中。如果指定`git reset --hard file.txt` ，它会将`file.txt` 从 HEAD 复制到索引中，并**强制覆盖**工作目录中的文件（危险）。

我们可以不让 Git 从 HEAD 拉取数据，而是通过具体指定一个提交来拉取该文件的对应版本。 我们只需运行类似于 `git reset eb43bf file.txt` 的命令即可。它其实做了同样的事情，也就是把工作目录中的文件恢复到 **v1** 版本，运行 `git add` 添加它， 然后再将它恢复到 **v3** 版本（只是不用真的过一遍这些步骤）。 如果我们现在运行 `git commit`，它就会记录一条“将该文件恢复到 **v1** 版本”的更改， 尽管我们并未在工作目录中真正地再次拥有它。如果使用hard关键字，那么它会从指定的提交版本中将指定文件恢复至索引和工作目录中。

### 压缩
我们来看看如何利用这种新的功能来做一些有趣的事情——压缩提交。

假设你有一个项目，第一次提交中有一个文件，第二次提交增加了一个新的文件并修改了第一个文件，第三次提交再次修改了第一个文件。 由于第二次提交是一个未完成的工作，因此你想要压缩它。

![输入图片说明](/imgs/git-book-reference/2023-11-16/e3HcngsE38lv11jV.png)

那么可以运行 `git reset --soft HEAD~2` 来将 HEAD 分支移动到一个旧一点的提交上（即你想要保留的最近的提交）  
![输入图片说明](/imgs/git-book-reference/2023-11-16/WfZ3G38an1kadjte.png)  
然后只需再次运行 `git commit`  
![输入图片说明](/imgs/git-book-reference/2023-11-16/zXZCmURr1yC5ngR0.png)  
现在你可以查看可到达的历史，即将会推送的历史，现在看起来有个 v1 版 `file-a.txt` 的提交， 接着第二个提交将 `file-a.txt` 修改成了 v3 版并增加了 `file-b.txt`。 包含 v2 版本的文件已经不在历史中了。  
>reflog应该仍能记录  

### checkout检出
最后，你大概还想知道 `checkout` 和 `reset` 之间的区别。 和 `reset` 一样，`checkout` 也操纵三棵树，不过它有一点不同，这取决于你是否传给该命令一个文件路径。

#### 不带路径

运行 `git checkout [branch]` (切换分支)与运行 `git reset --hard [branch]`（回退分支） 非常相似，它会更新所有三棵树使其看起来像 `[branch]`，不过有两点重要的区别。

首先不同于 `reset --hard`，`checkout` 对工作目录是安全的，它会通过检查来确保不会将已更改的文件弄丢。 其实它还更聪明一些。它会在工作目录中先试着简单合并一下，这样所有 _还未修改过的_ 文件都会被更新。 而 `reset --hard` 则会不做检查就全面地替换所有东西。

第二个重要的区别是 `checkout` 如何更新 HEAD。 `reset` 会移动 HEAD 分支的指向，而 `checkout` 只会移动 HEAD 自身来指向另一个分支。

例如，假设我们有 `master` 和 `develop` 分支，它们分别指向不同的提交；我们现在在 `develop` 上（所以 HEAD 指向它）。 如果我们运行 `git reset master`，那么 `develop` 自身现在会和 `master` 指向同一个提交，HEAD仍然指向`develop`。 而如果我们运行 `git checkout master` 的话，`develop` 不会移动，HEAD 自身会移动。 现在 HEAD 将会指向 `master`。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/MqKi1w5KTpWakduW.png)


#### 带路径
运行 `checkout` 的另一种方式就是指定一个文件路径，这会像 `reset` 一样不会移动 HEAD。 它就像 `git reset [branch] file` 那样用该次提交中的那个文件来更新索引，但是它也会覆盖工作目录中对应的文件。 它就像是 `git reset --hard [branch] file`（如果 `reset` 允许你这样运行的话）， 这样对工作目录并不安全，它也不会移动 HEAD。

### 总结

希望你现在熟悉并理解了 `reset` 命令，不过关于它和 `checkout` 之间的区别，你可能还是会有点困惑，毕竟不太可能记住不同调用的所有规则。

下面的速查表列出了命令对树的影响。 “HEAD” 一列中的 “REF” 表示该命令移动了 HEAD 指向的分支引用，而 “HEAD” 则表示只移动了 HEAD 自身。 特别注意 'WD Safe?' 一列——如果它标记为 **NO**，那么运行该命令之前请考虑一下。  
![输入图片说明](/imgs/git-book-reference/2023-11-16/aNfLawajF99j7D6u.png)

## 高级合并
经常解决小的冲突，比在一系列提交后解决一个巨大的冲突要好。

然而，有时也会有棘手的冲突。 不像其他的版本控制系统，Git 并不会尝试过于聪明的合并冲突解决方案。 Git 的哲学是聪明地决定无歧义的合并方案，但是如果有冲突，它不会尝试智能地自动解决它。 因此，如果很久之后才合并两个分叉的分支，你可能会撞上一些问题。

首先，在做一次可能有冲突的合并前尽可能保证工作目录是干净的。

#### 中断一次合并
首先，让我们介绍如何摆脱这个情况。 你可能不想处理冲突这种情况，完全可以通过 `git merge --abort` 来简单地退出合并。

`git merge --abort` 选项会尝试恢复到你运行合并前的状态。 但当运行命令前，在工作目录中有未储藏、未提交的修改时它不能完美处理，除此之外它都工作地很好。

如果出于某些原因你想要重来一次，也可以运行 `git reset --hard HEAD` 回到上一次提交的状态。 请牢记此时任何未提交的工作都会丢失，所以请确认你不需要保留任何改动。

#### 忽略空白

在这个特定的例子中，冲突与空白有关。 我们知道这点是因为这个例子很简单，但是在实际的例子中发现这样的冲突也很容易， 因为每一行都被移除而在另一边每一行又被加回来了。 默认情况下，Git 认为所有这些行都改动了，所以它不会合并文件。

默认合并策略可以带有参数，其中的几个正好是关于忽略空白改动的。 如果你看到在一次合并中有大量关于空白的问题，你可以直接中止它并重做一次， 这次使用 `-Xignore-all-space` 或 `-Xignore-space-change` 选项。 第一个选项在比较行时 **完全忽略** 空白修改，第二个选项将一个空白符与多个连续的空白字符视作等价的。
```console
$ git merge -Xignore-space-change dev
Auto-merging hello.rb
Merge made by the 'recursive' strategy.
 hello.rb | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```
如果你的团队中的某个人可能不小心重新格式化空格为制表符或者相反的操作，这会是一个救命稻草。

#### 手动文件再合并
虽然 Git 对空白的预处理做得很好，还有很多其他类型的修改，Git 也许无法自动处理，但是脚本可以处理它们。

我们真正想要做的是对将要合并入的文件在真正合并前运行 `dos2unix` 程序。 所以如果那样的话，我们该如何做？

首先，我们进入到了合并冲突状态。 然后我们想要我们（ours）的版本的文件，他们（theirs）的版本的文件（从我们将要合并入的分支）和共同（common）的版本的文件（从分支叉开时的位置）的拷贝。 然后我们想要修复任何一边的文件，并且为这个单独的文件重试一次合并。

获得这三个文件版本实际上相当容易。 Git 在索引中存储了所有这些版本，在 “stages” 下每一个都有一个数字与它们关联。 Stage 1 是它们共同的祖先版本，stage 2 是你的版本，stage 3 来自于 `MERGE_HEAD`，即你将要合并入的版本（“theirs”）。

通过 `git show` 命令与一个特别的语法，你可以将冲突文件的这些版本释放出一份拷贝。
```console
$ git show :1:hello.rb > hello.common.rb
$ git show :2:hello.rb > hello.ours.rb
$ git show :3:hello.rb > hello.theirs.rb
```
`:1:hello.rb` 只是查找那个 blob 对象 SHA-1 值的简写。

既然在我们的工作目录中已经有这所有三个阶段的内容，我们可以手工修复它们来修复空白问题，然后使用鲜为人知的 `git merge-file` 命令来重新合并那个文件。

```console
$ dos2unix hello.theirs.rb
dos2unix: converting file hello.theirs.rb to Unix format ...

$ git merge-file -p \
    hello.ours.rb hello.common.rb hello.theirs.rb > hello.rb
```

在这时我们已经漂亮地合并了那个文件。 实际上，这比使用 `ignore-space-change` 选项要更好，因为在合并前真正地修复了空白修改而不是简单地忽略它们。 在使用 `ignore-space-change` 进行合并操作后，我们最终得到了有几行是 DOS 行尾的文件，从而使提交内容混乱了。

如果你想要在最终提交前看一下我们这边与另一边之间实际的修改， 你可以使用 `git diff` 来比较将要提交作为合并结果的工作目录与其中任意一个阶段的文件差异。 让我们看看它们。

要在合并前比较结果与在你的分支上的内容，换一句话说，看看合并引入了什么，可以运行 `git diff --ours`
```console
$ git diff --ours
```
这里我们可以很容易地看到在我们的分支上发生了什么，在这次合并中我们实际引入到这个文件的改动，是修改了其中一行。

如果我们想要查看合并的结果与他们那边有什么不同，可以运行 `git diff --theirs`。 在本例及后续的例子中，我们会使用 `-b` 来去除空白，因为我们将它与 Git 中的， 而不是我们清理过的`hello.theirs.rb` 文件比较。
```console
$ git diff --theirs -b
```

最终，你可以通过 `git diff --base` 来查看文件在两边是如何改动的。
```console
$ git diff --base -b
```

在这时我们可以使用 `git clean` 命令来清理我们为手动合并而创建但不再有用的额外文件。
```console
$ git clean -f
Removing hello.common.rb
Removing hello.ours.rb
Removing hello.theirs.rb
```

#### 检出冲突
一个很有用的工具是带 `--conflict` 选项的 `git checkout`。 这会重新检出文件并替换合并冲突标记。 如果想要重置标记并尝试再次解决它们的话这会很有用。

可以传递给 `--conflict` 参数 `diff3` 或 `merge`（默认选项）。 如果传给它 `diff3`，Git 会使用一个略微不同版本的冲突标记： 不仅仅只给你 “ours” 和 “theirs” 版本，同时也会有 “base” 版本在中间来给你更多的上下文。
```console
$ git checkout --conflict=diff3 hello.rb
```

一旦我们运行它，文件看起来会像下面这样：
```ruby
#! /usr/bin/env ruby

def hello
<<<<<<< ours
  puts 'hola world'
||||||| base
  puts 'hello world'
=======
  puts 'hello mundo'
>>>>>>> theirs
end

hello()
```
如果你喜欢这种格式，可以通过设置 `merge.conflictstyle` 选项为 `diff3` 来做为以后合并冲突的默认选项。

```console
$ git config --global merge.conflictstyle diff3
```
`git checkout` 命令也可以使用 `--ours` 和 `--theirs` 选项，这是一种无需合并的快速方式，你可以选择留下一边的修改而丢弃掉另一边修改。
当有二进制文件冲突时这可能会特别有用，因为可以简单地选择一边，或者可以只合并另一个分支的特定文件——可以做一次合并然后在提交前检出一边或另一边的特定文件。

#### 合并日志
另一个解决合并冲突有用的工具是 `git log`。 这可以帮助你得到那些对冲突有影响的上下文。 回顾一点历史来记起为什么两条线上的开发会触碰同一片代码有时会很有用。

为了得到此次合并中包含的每一个分支的所有独立提交的列表， 我们可以使用之前在 三点 学习的“三点”语法。
```console
$ git log --oneline --left-right HEAD...MERGE_HEAD
< f1270f7 update README
< 9af9d3b add a README
< 694971d update phrase to hola world
> e3eb223 add more tests
> 7cff591 add testing script
> c3ffff1 changed text to hello mundo
```
这个漂亮的列表包含 6 个提交和每一个提交所在的不同开发路径。

我们可以通过更加特定的上下文来进一步简化这个列表。 如果我们添加 `--merge` 选项到 `git log` 中，它会只显示任何一边接触了合并冲突文件的提交。
```console
$ git log --oneline --left-right --merge
< 694971d update phrase to hola world
> c3ffff1 changed text to hello mundo
```

如果你运行命令时用 `-p` 选项代替，你会得到所有冲突文件的区别。 快速获得你需要帮助理解为什么发生冲突的上下文，以及如何聪明地解决它，这会非常有用。

#### 组合式差异格式
因为 Git 暂存合并成功的结果，当你在合并冲突状态下运行 `git diff` 时，只会得到现在还在冲突状态的区别。 当需要查看你还需要解决哪些冲突时这很有用。

在合并冲突后直接运行的 `git diff` 会给你一个相当独特的输出格式。
```console
$ git diff
diff --cc hello.rb
index 0399cd5,59727f0..0000000
--- a/hello.rb
+++ b/hello.rb
@@@ -1,7 -1,7 +1,11 @@@
  #! /usr/bin/env ruby

  def hello
++<<<<<<< HEAD
 +  puts 'hola world'
++=======
+   puts 'hello mundo'
++>>>>>>> mundo
  end

  hello()
```
这种叫作“组合式差异”的格式会在每一行给你两列数据。 第一列为你显示 “ours” 分支与工作目录的文件区别（添加或删除）， 第二列显示 “theirs” 分支与工作目录的拷贝区别。

所以在上面的例子中可以看到 `<<<<<<<` 与 `>>>>>>>` 行在工作拷贝中但是并不在合并的任意一边中。 这很有意义，合并工具因为我们的上下文被困住了，它期望我们去移除它们。

如果我们解决冲突再次运行 `git diff`，我们将会看到同样的事情，但是它有一点帮助。
```console
$ vim hello.rb
$ git diff
diff --cc hello.rb
index 0399cd5,59727f0..0000000
--- a/hello.rb
+++ b/hello.rb
@@@ -1,7 -1,7 +1,7 @@@
  #! /usr/bin/env ruby

  def hello
-   puts 'hola world'
 -  puts 'hello mundo'
++  puts 'hola mundo'
  end

  hello()
```
这里显示出 “hola world” 在我们这边但不在工作拷贝中，那个 “hello mundo” 在他们那边但不在工作拷贝中， 最终 “hola mundo” 不在任何一边但是现在在工作拷贝中。在提交解决方案前这对审核很有用。

也可以在合并后通过 `git log` 来获取相同信息，查看冲突是如何解决的。 如果你对一个合并提交运行 `git show` 命令 Git 将会输出这种格式， 或者你也可以在 `git log -p`（默认情况下该命令只会展示还没有合并的补丁）命令之后加上 `--cc` 选项。

```console
$ git log --cc -p -1
commit 14f41939956d80b9e17bb8721354c33f8d5b5a79
Merge: f1270f7 e3eb223
Author: Scott Chacon <schacon@gmail.com>
Date:   Fri Sep 19 18:14:49 2014 +0200

    Merge branch 'mundo'

    Conflicts:
        hello.rb

diff --cc hello.rb
index 0399cd5,59727f0..e1d0799
--- a/hello.rb
+++ b/hello.rb
@@@ -1,7 -1,7 +1,7 @@@
  #! /usr/bin/env ruby

  def hello
-   puts 'hola world'
 -  puts 'hello mundo'
++  puts 'hola mundo'
  end

  hello()
```

### 撤消合并
假设现在在一个主题分支上工作，不小心将其合并到 `master` 中，有两种方法来解决这个问题，这取决于你想要的结果是什么。

#### 修复引用
如果这个不想要的合并提交只存在于你的本地仓库中，最简单且最好的解决方案是移动分支到你想要它指向的地方。 大多数情况下，如果你在错误的 `git merge` 后运行 `git reset --hard HEAD~`，这会重置分支指向。

这个方法的缺点是它会重写历史，在一个共享的仓库中这会造成问题的。如果其他人已经有你将要重写的提交，你应当避免使用 `reset`。 如果有任何其他提交在合并之后创建了，那么这个方法也会无效；移动引用实际上会丢失那些改动。

#### 还原提交
如果移动分支指针并不适合你，Git 给你一个生成一个新提交的选项，提交将会撤消一个已存在提交的所有修改。 Git 称这个操作为“还原”，在这个特定的场景下，你可以像这样调用它：

```console
$ git revert -m 1 HEAD
[master b1d8379] Revert "Merge branch 'topic'"
```

`-m 1` 标记指出 “mainline” 需要被保留下来的父结点。 当你引入一个合并到 `HEAD`（`git merge topic`），新提交有两个父结点：第一个是 `HEAD`（`C6`），第二个是将要合并入分支的最新提交（`C4`）。 在本例中，我们想要撤消所有由父结点 #2（`C4`）合并引入的修改，同时保留从父结点 #1（`C6`）开始的所有内容。  
![输入图片说明](/imgs/git-book-reference/2023-11-17/3dA4PmwkDWYGsvft.png)

新的提交 `^M` 与 `C6` 有完全一样的内容，所以从这儿开始就像合并从未发生过，除了“现在还没合并”的提交依然在 `HEAD` 的历史中。 如果你尝试再次合并 `topic` 到 `master` Git 会感到困惑：
```console
$ git merge topic
Already up-to-date.
```
`topic` 中并没有东西不能从 `master` 中追踪到达。 更糟的是，如果你在 `topic` 中增加工作然后再次合并，Git 只会引入被还原的**合并之后**的修改（即，C3/C4修改被丢弃，只合并C7）。  
![输入图片说明](/imgs/git-book-reference/2023-11-17/cjK14FLGKWAvYarB.png)

解决这个最好的方式是撤消还原原始的合并，因为现在你想要引入被还原出去的修改，**然后** 创建一个新的合并提交：
```console
$ git revert ^M
[master 09f0126] Revert "Revert "Merge branch 'topic'""
$ git merge topic
```
![输入图片说明](/imgs/git-book-reference/2023-11-17/6LNtvdnHYgjC1DfJ.png)
在本例中，`M` 与 `^M` 抵消了。 `^^M` 事实上合并入了 `C3` 与 `C4` 的修改，`C8` 合并了 `C7` 的修改，所以现在 `topic` 已经完全被合并了。

### 其他类型的合并
默认情况下，当 Git 看到两个分支合并中的冲突时，它会将合并冲突标记添加到你的代码中并标记文件为冲突状态来让你解决。 如果你希望 Git 简单地选择特定的一边并忽略另外一边而不是让你手动解决冲突，你可以传递给 `merge` 命令一个 `-Xours` 或 `-Xtheirs` 参数。

如果 Git 看到这个，它并不会增加冲突标记。 任何可以合并的区别，它会直接合并。 任何有冲突的区别，它会简单地选择你全局指定的一边，包括二进制文件。

这个选项也可以传递给我们之前看到的 `git merge-file` 命令， 通过运行类似 `git merge-file --ours` 的命令来合并单个文件。

## 使用 Git 调试
### 文件标注
如果你在追踪代码中的一个 bug，并且想知道是什么时候以及为何会引入，文件标注通常是最好用的工具。 它能显示任何文件中每行最后一次修改的提交记录。 所以，如果你在代码中看到一个有 bug 的方法，你可以使用 `git blame` 标注这个文件，查看哪一次提交引入了这行。

以下示例用 `git blame` 确定了 Linux 内核源码顶层的 `Makefile` 中每一行分别来自哪个提交和提交者， 此外用 `-L` 选项还可以将标注的输出限制为该文件中的第 69 行到第 82 行。
```console
$ git blame -L 69,82 Makefile
b8b0618cf6fab (Cheng Renquan  2009-05-26 16:03:07 +0800 69) ifeq ("$(origin V)", "command line")
b8b0618cf6fab (Cheng Renquan  2009-05-26 16:03:07 +0800 70)   KBUILD_VERBOSE = $(V)
^1da177e4c3f4 (Linus Torvalds 2005-04-16 15:20:36 -0700 71) endif
^1da177e4c3f4 (Linus Torvalds 2005-04-16 15:20:36 -0700 72) ifndef KBUILD_VERBOSE
^1da177e4c3f4 (Linus Torvalds 2005-04-16 15:20:36 -0700 73)   KBUILD_VERBOSE = 0
^1da177e4c3f4 (Linus Torvalds 2005-04-16 15:20:36 -0700 74) endif
^1da177e4c3f4 (Linus Torvalds 2005-04-16 15:20:36 -0700 75)
066b7ed955808 (Michal Marek   2014-07-04 14:29:30 +0200 76) ifeq ($(KBUILD_VERBOSE),1)
066b7ed955808 (Michal Marek   2014-07-04 14:29:30 +0200 77)   quiet =
066b7ed955808 (Michal Marek   2014-07-04 14:29:30 +0200 78)   Q =
066b7ed955808 (Michal Marek   2014-07-04 14:29:30 +0200 79) else
066b7ed955808 (Michal Marek   2014-07-04 14:29:30 +0200 80)   quiet=quiet_
066b7ed955808 (Michal Marek   2014-07-04 14:29:30 +0200 81)   Q = @
066b7ed955808 (Michal Marek   2014-07-04 14:29:30 +0200 82) endif
```
注意一下 `^1da177e4c3f4` 这个提交的几行，其中的前缀 `^` 指出了该文件自第一次提交后从未修改的那些行。 这会带来小小的困惑，因为目前你至少已经看到三种 Git 使用 `^` 来修饰一个提交的 SHA-1 值的不同含义，但这里确实就是这个意思。

一个很有意思的特性就是你可以让 Git 找出所有的代码移动。 如果你在 `git blame` 后面加上一个 `-C`，Git 会分析你正在标注的文件， 并且尝试找出文件中从别的地方复制过来的代码片段的原始出处。 比如，你将 `GITServerHandler.m` 这个文件拆分为数个文件，其中一个文件是 `GITPackUpload.m`。 对 `GITPackUpload.m` 执行带 `-C` 参数的 blame 命令，你就可以看到代码块的原始出处：
```console
$ git blame -C -L 141,153 GITPackUpload.m
f344f58d GITServerHandler.m (Scott 2009-01-04 141)
f344f58d GITServerHandler.m (Scott 2009-01-04 142) - (void) gatherObjectShasFromC
f344f58d GITServerHandler.m (Scott 2009-01-04 143) {
70befddd GITServerHandler.m (Scott 2009-03-22 144)         //NSLog(@"GATHER COMMI
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 145)
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 146)         NSString *parentSha;
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 147)         GITCommit *commit = [g
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 148)
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 149)         //NSLog(@"GATHER COMMI
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 150)
56ef2caf GITServerHandler.m (Scott 2009-01-05 151)         if(commit) {
56ef2caf GITServerHandler.m (Scott 2009-01-05 152)                 [refDict setOb
56ef2caf GITServerHandler.m (Scott 2009-01-05 153)
```

这个功能很有用。 通常来说，你会认为复制代码过来的那个提交是最原始的提交，因为那是你第一次在这个文件中修改了这几行。 但 Git 会告诉你，你第一次写这几行代码的那个提交才是原始提交，即使这是在另外一个文件里写的。

### 二分查找

当你知道问题是在哪里引入的情况下文件标注可以帮助你查找问题。 如果你不知道哪里出了问题，并且自从上次可以正常运行到现在已经有数十个或者上百个提交， 这个时候你可以使用 `git bisect` 来帮助查找。 `bisect` 命令会对你的提交历史进行二分查找来帮助你尽快找到是哪一个提交引入了问题。

这是一个可以帮助你在几分钟内从数百个提交中找到 bug 的强大工具。 事实上，如果你有一个脚本在项目是正常的情况下返回 0，在不正常的情况下返回非 0，你可以使 `git bisect` 自动化这些操作。

# 自定义 Git

## 配置 Git

#### `core.editor`
默认情况下，Git 会调用你通过环境变量 `$VISUAL` 或 `$EDITOR` 设置的文本编辑器， 如果没有设置，默认则会调用 `vi` 来创建和编辑你的提交以及标签信息。 你可以使用 `core.editor` 选项来修改默认的编辑器：
```console
$ git config --global core.editor emacs
```
现在，无论你定义了什么终端编辑器，Git 都会调用 Emacs 编辑信息。
#### `commit.template`

如果把此项指定为你的系统上某个文件的路径，当你提交的时候， Git 会使用该文件的内容作为提交的默认初始化信息。 创建的自定义提交模版中的值可以用来提示自己或他人适当的提交格式和风格。
例如：考虑以下 `~/.gitmessage.txt` 模板文件：
```text
Subject line (try to keep under 50 characters)

Multi-line description of commit,
feel free to be detailed.

[Ticket: X]
```
要想让 Git 把它作为运行 `git commit` 时显示在你的编辑器中的默认信息，如下设置 `commit.template`：
```console
$ git config --global commit.template ~/.gitmessage.txt
$ git commit
```
如果你的团队对提交信息有格式要求，可以在系统上创建一个文件，并配置 Git 把它作为默认的模板，这样可以更加容易地使提交信息遵循格式。如果你的团队对提交信息有格式要求，可以在系统上创建一个文件，并配置 Git 把它作为默认的模板，这样可以更加容易地使提交信息遵循格式。

#### `core.autocrlf`
假如你正在 Windows 上写程序，而你的同伴用的是其他系统（或相反），你可能会遇到 CRLF 问题。 这是因为 Windows 使用回车（CR）和换行（LF）两个字符来结束一行，而 macOS 和 Linux 只使用换行（LF）一个字符。 虽然这是小问题，但它会极大地扰乱跨平台协作。许多 Windows 上的编辑器会悄悄把行尾的换行字符转换成回车和换行， 或在用户按下 Enter 键时，插入回车和换行两个字符。

Git 可以在你提交时自动地把回车和换行转换成换行，而在检出代码时把换行转换成回车和换行。 你可以用 `core.autocrlf` 来打开此项功能。 如果是在 Windows 系统上，把它设置成 `true`，这样在检出代码时，换行会被转换成回车和换行：
```console
$ git config --global core.autocrlf true
```

如果使用以换行作为行结束符的 Linux 或 macOS，你不需要 Git 在检出文件时进行自动的转换； 然而当一个以回车加换行作为行结束符的文件不小心被引入时，你肯定想让 Git 修正。 你可以把 `core.autocrlf` 设置成 input 来告诉 Git 在提交时把回车和换行转换成换行，检出时不转换：
```console
$ git config --global core.autocrlf input
```
这样在 Windows 上的检出文件中会保留回车和换行，而在 macOS 和 Linux 上，以及版本库中会保留换行。

如果你是 Windows 程序员，且正在开发仅运行在 Windows 上的项目，可以设置 `false` 取消此功能，把回车保留在版本库中：
```console
$ git config --global core.autocrlf false
```

# 附录 A: 在其它环境中使用 Git

## 图形界面

### `gitk` 和 `git-gui`
`gitk` 是一个历史记录的图形化查看器。黄点表示 HEAD，红点表示尚未提交的本地变动。 下方的窗口用来显示当前选中的提交的具体信息；评论和补丁显示在左侧，摘要显示在右侧。 中间则是一组用来搜索历史的控件。

`git-gui` 则主要是一个用来制作提交的工具。

左侧是索引区，未暂存的修改显示在上方，已暂存的修改显示在下方。 你可以通过点击文件名左侧的图标来将该文件在暂存状态与未暂存状态之间切换，你也可以通过选中一个文件名来查看它的详情。

右侧窗口的上方以 diff 格式来显示当前选中文件发生了变动的地方。 你可以通过右击某一区块或行从而将这一区块或行放入暂存区。

右侧窗口的下方是写日志和执行操作的地方。 在文本框中键入日志然后点击“提交”就和执行 `git commit` 的效果差不多。 如果你想要修订上一次提交, 可以选中“修订”按钮，上次一提交的内容就会显示在“暂存区”。 然后你就可以简单的对修改进行暂存和取消暂存操作，更新提交日志，然后再次点击“提交”用这个新的提交来覆盖上一次提交。

`gitk` 和 `git-gui` 就是针对某种任务设计的工具的两个例子。 它们分别为了不同的目的（即查看历史和制作提交）而进行了精简，略去了用不到的功能。

# 附录 C: Git 命令

## 设置与配置
有两个十分常用的命令：`config` 和 `help`。 从第一次调用 Git 到日常微调及阅读参考，它们一直陪伴着你。

### git config
Git 做的很多工作都有一种默认方式。 对于绝大多数工作而言，你可以改变 Git 的默认方式，或者根据你的偏好来设置。 这些设置涵盖了所有的事，从告诉 Git 你的名字，到指定偏好的终端颜色，以及你使用的编辑器。 此命令会从几个特定的配置文件中读取和写入配置值，以便你可以从全局或者针对特定的仓库来进行设置。

本书几乎所有的章节都用到了 `git config` 命令。

在 [初次运行 Git 前的配置](https://git-scm.com/book/zh/v2/ch00/_first_time) 一节中，在开始使用 Git 之前，我们用它来指定我们的名字，邮箱地址和编辑器偏好。

在 [Git 别名](https://git-scm.com/book/zh/v2/ch00/_git_aliases) 一节中我们展示了如何创建可以展开为长选项序列的短命令，以便你不用每次都输入它们。

在 [变基](https://git-scm.com/book/zh/v2/ch00/_rebasing) 一节中，执行 `git pull` 命令时，使用此命令来将 `--rebase` 作为默认选项。

在 [凭证存储](https://git-scm.com/book/zh/v2/ch00/_credential_caching) 一节中，我们使用它来为你的 HTTP 密码设置一个默认的存储区域。

在 [关键字展开](https://git-scm.com/book/zh/v2/ch00/_keyword_expansion) 一节中我们展示了如何设置在 Git 的内容添加和减少时使用的 smudge 过滤器 和 clean 过滤器。

最后，基本上 [配置 Git](https://git-scm.com/book/zh/v2/ch00/_git_config) 整个章节都是针对此命令的。

#### git config core.editor 命令

Vim
`git config --global core.editor "vim"`

Notepad (Windows 64-bit)
`git config --global core.editor notepad`

VS Code
`git config --global core.editor "code --wait"`

### git help

`git help` 命令用来显示任何命令的 Git 自带文档。 但是我们仅会在此附录中提到大部分最常用的命令，对于每一个命令的完整的可选项及标志列表，你可以随时运行 `git help <command>` 命令来了解。

## 获取与创建项目

有几种方式获取一个 Git 仓库。 一种是从网络上或者其他地方拷贝一个现有的仓库，另一种就是在一个目录中创建一个新的仓库。

### git init

你只需要简单地运行 `git init` 就可以将一个目录转变成一个 Git 仓库，这样你就可以开始对它进行版本管理了。

我们一开始在 [获取 Git 仓库](https://git-scm.com/book/zh/v2/ch00/_getting_a_repo) 一节中介绍了如何创建一个新的仓库来开始工作。

在 [远程分支](https://git-scm.com/book/zh/v2/ch00/_remote_branches) 一节中我们简单的讨论了如何改变默认分支。

在 [把裸仓库放到服务器上](https://git-scm.com/book/zh/v2/ch00/_bare_repo) 一节中我们使用此命令来为一个服务器创建一个空的祼仓库。

最后，我们在 [底层命令与上层命令](https://git-scm.com/book/zh/v2/ch00/_plumbing_porcelain) 一节中介绍了此命令背后工作的原理的一些细节。

### git clone

`git clone` 实际上是一个封装了其他几个命令的命令。 它创建了一个新目录，切换到新的目录，然后 `git init` 来初始化一个空的 Git 仓库， 然后为你指定的 URL 添加一个（默认名称为 `origin` 的）远程仓库（`git remote add`），再针对远程仓库执行 `git fetch`，最后通过 `git checkout` 将远程仓库的最新提交检出到本地的工作目录。

`git clone` 命令在本书中多次用到，这里只列举几个有意思的地方。

在 [克隆现有的仓库](https://git-scm.com/book/zh/v2/ch00/_git_cloning) 一节中我们通过几个示例详细介绍了此命令。

在 [在服务器上搭建 Git](https://git-scm.com/book/zh/v2/ch00/_getting_git_on_a_server) 一节中，我们使用了 `--bare` 选项来创建一个没有任何工作目录的 Git 仓库副本。

在 [打包](https://git-scm.com/book/zh/v2/ch00/_bundling) 一节中我们使用它来解包一个打包好的 Git 仓库。

最后，在 [克隆含有子模块的项目](https://git-scm.com/book/zh/v2/ch00/_cloning_submodules) 一节中我们学习了使用 `--recursive` 选项来让克隆一个带有子模块的仓库变得简单。

## 快照基础

对于基本的暂存内容及提交到你的历史记录中的工作流，只有少数基本的命令。

### git add

`git add` 命令将内容从工作目录添加到暂存区（或称为索引（index）区），以备下次提交。 当 `git commit` 命令执行时，默认情况下它只会检查暂存区域，因此 `git add` 是用来确定下一次提交时快照的样子的。

这个命令对于 Git 来说特别重要，所以在本书中被无数次地提及和使用。 我们将快速地过一遍一些可以看到的独特的用法。

我们在 [跟踪新文件](https://git-scm.com/book/zh/v2/ch00/_tracking_files) 一节中介绍并详细解释了 `git add` 命令。

然后，我们在 [遇到冲突时的分支合并](https://git-scm.com/book/zh/v2/ch00/_basic_merge_conflicts) 一节中提到了如何使用它来解决合并冲突。

接下来，我们在 [交互式暂存](https://git-scm.com/book/zh/v2/ch00/_interactive_staging) 一章中使用它来交互式的暂存一个已修改文件的特定部分。

最后，在 [树对象](https://git-scm.com/book/zh/v2/ch00/_tree_objects) 一节中我们在底层模拟了它的用法，以便你了解幕后发生了什么。

### git status

`git status` 命令会显示工作区及暂存区域中不同状态的文件。 其中包含了已修改但未暂存，或已经暂存但没有提交的文件。 在一般的显示形式中，它会给你一些如何在这些暂存区之间移动文件的提示。

首先，我们在 [检查当前文件状态](https://git-scm.com/book/zh/v2/ch00/_checking_status) 一节中介绍了 `status` 的基本及简单的形式。 虽然我们在全书中都有用到它，但是绝大部分的你能用 `git status` 做的事情都在这一章讲到了。

### git diff

当需要查看任意两棵树的差异时，可以使用 `git diff` 命令。 此命令可以查看你工作环境与你的暂存区的差异（`git diff` 默认的做法），你暂存区域与你最后提交之间的差异（`git diff --staged`），或者比较两个提交记录的差异（`git diff master branchB`）。

首先，我们在 [查看已暂存和未暂存的修改](https://git-scm.com/book/zh/v2/ch00/_git_diff_staged) 一章中研究了 `git diff` 的基本用法，在此节中我们展示了如何查看哪些变化已经暂存了，哪些没有。

在 [提交准则](https://git-scm.com/book/zh/v2/ch00/_commit_guidelines) 一节中,我们在提交前使用 `--check` 选项来检查可能存在的空白字符问题。

在 [确定引入了哪些东西](https://git-scm.com/book/zh/v2/ch00/_what_is_introduced) 一节中,了解了使用 `git diff A…​B` 语法来更有效地比较不同分支之间的差异。

在 [高级合并](https://git-scm.com/book/zh/v2/ch00/_advanced_merging) 一节中我们使用 `-b` 选项来过滤掉空白字符的差异，及通过 `--theirs`、`--ours` 和 `--base` 选项来比较不同暂存区冲突文件的差异。

最后，在 [开始使用子模块](https://git-scm.com/book/zh/v2/ch00/_starting_submodules) 一节中,我们使用此命令合 `--submodule` 选项来有效地比较子模块的变化。

### git difftool

当你不想使用内置的 `git diff` 命令时。`git difftool` 可以用来简单地启动一个外部工具来为你展示两棵树之间的差异。

我们只在 [查看已暂存和未暂存的修改](https://git-scm.com/book/zh/v2/ch00/_git_diff_staged) 一节中简单的提到了此命令。

### git commit

`git commit` 命令将所有通过 `git add` 暂存的文件内容在数据库中创建一个持久的快照，然后将当前分支上的分支指针移到其之上。

首先，我们在 [提交更新](https://git-scm.com/book/zh/v2/ch00/_committing_changes) 一节中涉及了此命令的基本用法。 我们演示了如何在日常的工作流程中通过使用 `-a` 标志来跳过 `git add` 这一步，及如何使用 `-m` 标志通过命令行而不启动一个编辑器来传递提交信息。

在 [撤消操作](https://git-scm.com/book/zh/v2/ch00/_undoing) 一节中我们介绍了使用 `--amend` 选项来重做最后的提交。

在 [分支简介](https://git-scm.com/book/zh/v2/ch00/_git_branches_overview)，我们探讨了 `git commit` 的更多细节，及工作原理。

在 [签署提交](https://git-scm.com/book/zh/v2/ch00/_signing_commits) 一节中我们探讨了如何使用 `-S` 标志来为提交签名加密。

最后，在 [提交对象](https://git-scm.com/book/zh/v2/ch00/_git_commit_objects) 一节中，我们了解了 `git commit` 在背后做了什么，及它是如何实现的。

### git reset

`git reset` 命令主要用来根据你传递给动作的参数来执行撤销操作。 它可以移动 `HEAD` 指针并且可选的改变 `index` 或者暂存区，如果你使用 `--hard` 参数的话你甚至可以改变工作区。 如果错误地为这个命令附加后面的参数，你可能会丢失你的工作，所以在使用前你要确定你已经完全理解了它。

首先，我们在 [取消暂存的文件](https://git-scm.com/book/zh/v2/ch00/_unstaging) 一节中介绍了 `git reset` 简单高效的用法，用来对执行过 `git add` 命令的文件取消暂存。

在 [重置揭密](https://git-scm.com/book/zh/v2/ch00/_git_reset) 一节中我们详细介绍了此命令，几乎整节都在解释此命令。

在 [中断一次合并](https://git-scm.com/book/zh/v2/ch00/_abort_merge) 一节中，我们使用 `git reset --hard` 来取消一个合并，同时我们也使用了 `git merge --abort` 命令，它是 `git reset` 的一个简单的封装。

### git rm

`git rm` 是 Git 用来从工作区，或者暂存区移除文件的命令。 在为下一次提交暂存一个移除操作上，它与 `git add` 有一点类似。

我们在 [移除文件](https://git-scm.com/book/zh/v2/ch00/_removing_files) 一节中提到了 `git rm` 的一些细节，包括递归地移除文件，和使用 `--cached` 选项来只移除暂存区域的文件但是保留工作区的文件。

在本书的 [移除对象](https://git-scm.com/book/zh/v2/ch00/_removing_objects) 一节中，介绍了 `git rm` 仅有的几种不同用法，如在执行 `git filter-branch` 中使用和解释了 `--ignore-unmatch` 选项。 这对脚本来说很有用。

### git mv

`git mv` 命令是一个便利命令，用于移到一个文件并且在新文件上执行`git add`命令及在老文件上执行`git rm`命令。

我们只是在 [移动文件](https://git-scm.com/book/zh/v2/ch00/_git_mv) 一节中简单地提到了此命令。

### git clean

`git clean` 是一个用来从工作区中移除不想要的文件的命令。 可以是编译的临时文件或者合并冲突的文件。

在 [清理工作目录](https://git-scm.com/book/zh/v2/ch00/_git_clean) 一节中我们介绍了你可能会使用 `clean` 命令的大量选项及场景。

## 分支与合并

Git 有几个实现大部的分支及合并功能的实用命令。

### git branch

`git branch` 命令实际上是某种程度上的分支管理工具。 它可以列出你所有的分支、创建新分支、删除分支及重命名分支。

[Git 分支](https://git-scm.com/book/zh/v2/ch00/ch03-git-branching) 一节主要是为 `branch` 命令来设计的，它贯穿了整个章节。 首先，我们在 [分支创建](https://git-scm.com/book/zh/v2/ch00/_create_new_branch) 一节中介绍了它，然后我们在 [分支管理](https://git-scm.com/book/zh/v2/ch00/_branch_management) 一节中介绍了它的其它大部分特性（列举及删除）。

在 [跟踪分支](https://git-scm.com/book/zh/v2/ch00/_tracking_branches) 一节中，我们使用 `git branch -u` 选项来设置一个跟踪分支。

最后，我们在 [Git 引用](https://git-scm.com/book/zh/v2/ch00/_git_refs) 一节中讲到了它在背后做一什么。

### git checkout

`git checkout` 命令用来切换分支，或者检出内容到工作目录。

我们是在 [分支切换](https://git-scm.com/book/zh/v2/ch00/_switching_branches) 一节中第一次认识了命令及 `git branch` 命令。

在 [跟踪分支](https://git-scm.com/book/zh/v2/ch00/_tracking_branches) 一节中我们了解了如何使用 `--track` 标志来开始跟踪分支。

在 [检出冲突](https://git-scm.com/book/zh/v2/ch00/_checking_out_conflicts) 一节中，我们用此命令和 `--conflict=diff3` 来重新介绍文件冲突。

在 [重置揭密](https://git-scm.com/book/zh/v2/ch00/_git_reset) 一节中，我们进一步了解了其细节及与 `git reset` 的关系。

最后，我们在 [HEAD 引用](https://git-scm.com/book/zh/v2/ch00/ref_the_ref) 一节中介绍了此命令的一些实现细节。

### git merge

`git merge` 工具用来合并一个或者多个分支到你已经检出的分支中。 然后它将当前分支指针移动到合并结果上。

我们首先在 [新建分支](https://git-scm.com/book/zh/v2/ch00/_basic_branching) 一节中介绍了 `git merge` 命令。 虽然它在本书的各种地方都有用到，但是 `merge` 命令只有几个变种，一般只是 `git merge <branch>` 带上一个你想合并进来的一个分支名称。

我们在 [派生的公开项目](https://git-scm.com/book/zh/v2/ch00/_public_project) 的后面介绍了如何做一个 `squashed merge` （指 Git 合并时将其当作一个新的提交而不是记录你合并时的分支的历史记录。）

在 [高级合并](https://git-scm.com/book/zh/v2/ch00/_advanced_merging) 一节中，我们介绍了合并的过程及命令，包含 `-Xignore-space-change` 命令及 `--abort` 选项来中止一个有问题的提交。

在 [签署提交](https://git-scm.com/book/zh/v2/ch00/_signing_commits) 一节中我们学习了如何在合并前验证签名，如果你项目正在使用 GPG 签名的话。

最后，我们在 [子树合并](https://git-scm.com/book/zh/v2/ch00/_subtree_merge) 一节中学习了子树合并。

### git mergetool

当你在 Git 的合并中遇到问题时，可以使用 `git mergetool` 来启动一个外部的合并帮助工具。

我们在 [遇到冲突时的分支合并](https://git-scm.com/book/zh/v2/ch00/_basic_merge_conflicts) 中快速介绍了一下它，然后在 [外部的合并与比较工具](https://git-scm.com/book/zh/v2/ch00/_external_merge_tools) 一节中介绍了如何实现你自己的外部合并工具的细节。

### git log

`git log` 命令用来展示一个项目的可达历史记录，从最近的提交快照起。 默认情况下，它只显示你当前所在分支的历史记录，但是可以显示不同的甚至多个头记录或分支以供遍历。 此命令通常也用来在提交记录级别显示两个或多个分支之间的差异。

在本书的每一章几乎都有用到此命令来描述一个项目的历史。

在 [查看提交历史](https://git-scm.com/book/zh/v2/ch00/_viewing_history) 一节中我们介绍了此命令，并深入做了研究。 研究了包括 `-p` 和 `--stat` 选项来了解每一个提交引入的变更，及使用`--pretty` 和 `--online` 选项来查看简洁的历史记录。

在 [分支创建](https://git-scm.com/book/zh/v2/ch00/_create_new_branch) 一节中我们使用它加 `--decorate` 选项来简单的可视化我们分支的指针所在，同时我们使用 `--graph` 选项来查看分叉的历史记录是怎么样的。

在 [私有小型团队](https://git-scm.com/book/zh/v2/ch00/_private_team) 和 [提交区间](https://git-scm.com/book/zh/v2/ch00/_commit_ranges) 章节中，我们介绍了在使用 `git log` 命令时用 `branchA..branchB` 的语法来查看一个分支相对于另一个分支, 哪一些提交是唯一的。 在 [提交区间](https://git-scm.com/book/zh/v2/ch00/_commit_ranges) 一节中我们作了更多介绍。

在 <_merge_log>> 和 [三点](https://git-scm.com/book/zh/v2/ch00/_triple_dot) 章节中，我们介绍了 `branchA…​branchB` 格式和 `--left-right` 语法来查看哪些仅其中一个分支。 在 [合并日志](https://git-scm.com/book/zh/v2/ch00/_merge_log) 一节中我们还研究了如何使用 `--merge` 选项来帮助合并冲突调试，同样也使用 `--cc` 选项来查看在你历史记录中的合并提交的冲突。

在 [引用日志](https://git-scm.com/book/zh/v2/ch00/_git_reflog) 一节中我们使用此工具和 `-g` 选项 而不是遍历分支来查看 Git 的 `reflog`。

在 [搜索](https://git-scm.com/book/zh/v2/ch00/_searching) 一节中我们研究了`-S` 及 `-L` 选项来进行来在代码的历史变更中进行相当优雅地搜索，如一个函数的历史。

在 [签署提交](https://git-scm.com/book/zh/v2/ch00/_signing_commits) 一节中，我们了解了如何使用 `--show-signature` 来为每一个提交的 `git log` 输出中，添加一个判断是否已经合法的签名的一个验证。

### git stash

`git stash` 命令用来临时地保存一些还没有提交的工作，以便在分支上不需要提交未完成工作就可以清理工作目录。

[贮藏与清理](https://git-scm.com/book/zh/v2/ch00/_git_stashing) 一整个章节基本就是在讲这个命令。

### git tag

`git tag` 命令用来为代码历史记录中的某一个点指定一个永久的书签。 一般来说它用于发布相关事项。

我们在 [打标签](https://git-scm.com/book/zh/v2/ch00/_git_tagging) 一节中介绍了此命令及相关细节，并在 [为发布打标签](https://git-scm.com/book/zh/v2/ch00/_tagging_releases) 一节实践了此命令。

我也在 [签署工作](https://git-scm.com/book/zh/v2/ch00/_signing) 一节中介绍了如何使用 `-s` 标志创建一个 GPG 签名的标签，然后使用 `-v` 选项来验证。

## 项目分享与更新

在 Git 中没有多少访问网络的命令，几乎所以的命令都是在操作本地的数据库。 当你想要分享你的工作，或者从其他地方拉取变更时，这有几个处理远程仓库的命令。

### git fetch

`git fetch` 命令与一个远程的仓库交互，并且将远程仓库中有但是在当前仓库的没有的所有信息拉取下来然后存储在你本地数据库中。

我们开始在 [从远程仓库中抓取与拉取](https://git-scm.com/book/zh/v2/ch00/_fetching_and_pulling) 一节中介绍了此命令，然后我们在 [远程分支](https://git-scm.com/book/zh/v2/ch00/_remote_branches) 中看到了几个使用示例。

我们在 [向一个项目贡献](https://git-scm.com/book/zh/v2/ch00/_contributing_project) 一节中有几个示例中也都有使用此命令。

在 [合并请求引用](https://git-scm.com/book/zh/v2/ch00/_pr_refs) 我们用它来抓取一个在默认空间之外指定的引用，在 [打包](https://git-scm.com/book/zh/v2/ch00/_bundling) 中，我们了解了怎么从一个包中获取内容。

在 [引用规范](https://git-scm.com/book/zh/v2/ch00/_refspec) 章节中我们设置了高度自定义的 `refspec` 以便 `git fetch` 可以做一些跟默认不同的事情。

### git pull

`git pull` 命令基本上就是 `git fetch` 和 `git merge` 命令的组合体，Git 从你指定的远程仓库中抓取内容，然后马上尝试将其合并进你所在的分支中。

我们在 [从远程仓库中抓取与拉取](https://git-scm.com/book/zh/v2/ch00/_fetching_and_pulling) 一节中快速介绍了此命令，然后在 [查看某个远程仓库](https://git-scm.com/book/zh/v2/ch00/_inspecting_remote) 一节中了解了如果你运行此命令的话，什么将会合并。

我们也在 [用变基解决变基](https://git-scm.com/book/zh/v2/ch00/_rebase_rebase) 一节中了解了如何使用此命令来来处理变基的难题。

在 [检出冲突](https://git-scm.com/book/zh/v2/ch00/_checking_out_conflicts) 一节中我们展示了使用此命令如何通过一个 URL 来一次性的拉取变更。

最后，我们在 [签署提交](https://git-scm.com/book/zh/v2/ch00/_signing_commits) 一节中我们快速的介绍了你可以使用 `--verify-signatures` 选项来验证你正在拉取下来的经过 GPG 签名的提交。

### git push

`git push` 命令用来与另一个仓库通信，计算你本地数据库与远程仓库的差异，然后将差异推送到另一个仓库中。 它需要有另一个仓库的写权限，因此这通常是需要验证的。

我们开始在 [推送到远程仓库](https://git-scm.com/book/zh/v2/ch00/_pushing_remotes) 一节中介绍了 `git push` 命令。 在这一节中主要介绍了推送一个分支到远程仓库的基本用法。 在 [推送](https://git-scm.com/book/zh/v2/ch00/_pushing_branches) 一节中，我们深入了解了如何推送指定分支，在 [跟踪分支](https://git-scm.com/book/zh/v2/ch00/_tracking_branches) 一节中我们了解了如何设置一个默认的推送的跟踪分支。 在 [删除远程分支](https://git-scm.com/book/zh/v2/ch00/_delete_branches) 一节中我们使用 `--delete` 标志和 `git push` 命令来在删除一个在服务器上的分支。

在 [向一个项目贡献](https://git-scm.com/book/zh/v2/ch00/_contributing_project) 一整节中，我们看到了几个使用 `git push` 在多个远程仓库分享分支中的工作的示例。

在 [共享标签](https://git-scm.com/book/zh/v2/ch00/_sharing_tags) 一节中，我们知道了如何使用此命令加 `--tags` 选项来分享你打的标签。

在 [发布子模块改动](https://git-scm.com/book/zh/v2/ch00/_publishing_submodules) 一节中，我们使用 `--recurse-submodules` 选项来检查是否我们所有的子模块的工作都已经在推送子项目之前已经推送出去了，当使用子模块时这真的很有帮助。

在 [其它客户端钩子](https://git-scm.com/book/zh/v2/ch00/_other_client_hooks) 中我们简单的提到了 `pre-push` 挂钩（hook），它是一个可以用来设置成在一个推送完成之前运行的脚本，以检查推送是否被允许。

最后，在 [引用规范推送](https://git-scm.com/book/zh/v2/ch00/_pushing_refspecs) 一节中，我们知道了使用完整的 refspec 来推送，而不是通常使用的简写形式。 这对我们精确的指定要分享出去的工作很有帮助。

### git remote

`git remote` 命令是一个是你远程仓库记录的管理工具。 它允许你将一个长的 URL 保存成一个简写的句柄，例如 `origin` ，这样你就可以不用每次都输入他们了。 你可以有多个这样的句柄，`git remote` 可以用来添加，修改，及删除它们。

此命令在 [远程仓库的使用](https://git-scm.com/book/zh/v2/ch00/_remote_repos) 一节中做了详细的介绍，包括列举、添加、移除、重命名功能。

几乎在此书的后续章节中都有使用此命令，但是一般是以 `git remote add <name> <url>` 这样的标准格式。

### git archive

`git archive` 命令用来创建项目一个指定快照的归档文件。

我们在 [准备一次发布](https://git-scm.com/book/zh/v2/ch00/_preparing_release) 一节中，使用 `git archive` 命令来创建一个项目的归档文件用于分享。

### git submodule

`git submodule` 命令用来管理一个仓库的其他外部仓库。 它可以被用在库或者其他类型的共享资源上。 `submodule` 命令有几个子命令, 如（`add`、`update`、`sync` 等等）用来管理这些资源。

只在 [子模块](https://git-scm.com/book/zh/v2/ch00/_git_submodules) 章节中提到和详细介绍了此命令。

## 检查与比较

### git show

`git show` 命令可以以一种简单的人类可读的方式来显示一个 Git 对象。 你一般使用此命令来显示一个标签或一个提交的信息。

我们在 [附注标签](https://git-scm.com/book/zh/v2/ch00/_annotated_tags) 一节中使用此命令来显示带注解标签的信息。

然后，我们在 [选择修订版本](https://git-scm.com/book/zh/v2/ch00/_revision_selection) 一节中，用了很多次来显示不同的版本选择将解析出来的提交。

我们使用 `git show` 做的最有意思的事情是在 [手动文件再合并](https://git-scm.com/book/zh/v2/ch00/_manual_remerge) 一节中用来在合并冲突的多个暂存区域中提取指定文件的内容。

### git shortlog

`git shortlog` 是一个用来归纳 `git log` 的输出的命令。 它可以接受很多与 `git log` 相同的选项，但是此命令并不会列出所有的提交，而是展示一个根据作者分组的提交记录的概括性信息

我们在 [制作提交简报](https://git-scm.com/book/zh/v2/ch00/_the_shortlog) 一节中展示了如何使用此命令来创建一个漂亮的 changelog 文件。

### git describe

`git describe` 命令用来接受任何可以解析成一个提交的东西，然后生成一个人类可读的字符串且不可变。 这是一种获得一个提交的描述的方式，它跟一个提交的 SHA-1 值一样是无歧义，但是更具可读性。

我们在 [生成一个构建号](https://git-scm.com/book/zh/v2/ch00/_build_number) 及 [准备一次发布](https://git-scm.com/book/zh/v2/ch00/_preparing_release) 章节中使用 `git describe` 命令来获得一个字符串来命名我们发布的文件。

## 调试

Git 有一些命令可以用来帮你调试你代码中的问题。 包括找出是什么时候，是谁引入的变更。

### git bisect

`git bisect` 工具是一个非常有用的调试工具，它通过自动进行一个二分查找来找到哪一个特定的提交是导致 bug 或者问题的第一个提交。

仅在 [二分查找](https://git-scm.com/book/zh/v2/ch00/_binary_search) 一节中完整的介绍了此命令。

### git blame

`git blame` 命令标注任何文件的行，指出文件的每一行的最后的变更的提交及谁是那一个提交的作者。 当你要找那个人去询问关于这块特殊代码的信息时这会很有用。

只有 [文件标注](https://git-scm.com/book/zh/v2/ch00/_file_annotation) 一节中提到此命令。

### git grep

`git grep` 命令可以帮助在源代码中，甚至是你项目的老版本中的任意文件中查找任何字符串或者正则表达式。

只有 [Git Grep](https://git-scm.com/book/zh/v2/ch00/_git_grep) 的章节中提到此命令。

## 补丁

Git 中的一些命令是以引入的变更即提交这样的概念为中心的，这样一系列的提交，就是一系列的补丁。 这些命令以这样的方式来管理你的分支。

### git cherry-pick

`git cherry-pick` 命令用来获得在单个提交中引入的变更，然后尝试将作为一个新的提交引入到你当前分支上。 从一个分支单独一个或者两个提交而不是合并整个分支的所有变更是非常有用的。

在 [变基与拣选工作流](https://git-scm.com/book/zh/v2/ch00/_rebase_cherry_pick) 一节中描述和演示了 `Cherry picking`

### git rebase

`git rebase` 命令基本是是一个自动化的 `cherry-pick` 命令。 它计算出一系列的提交，然后再以它们在其他地方以同样的顺序一个一个的 `cherry-picks` 出它们。

在 [变基](https://git-scm.com/book/zh/v2/ch00/_rebasing) 一章中详细提到了此命令，包括与已经公开的分支的变基所涉及的协作问题。

在 [替换](https://git-scm.com/book/zh/v2/ch00/_replace) 中我们在一个分离历史记录到两个单独的仓库的示例中实践了此命令，同时使用了 `--onto` 选项。

在 [Rerere](https://git-scm.com/book/zh/v2/ch00/ef_rerere) 一节中，我们研究了在变基时遇到的合并冲突的问题。

在 [修改多个提交信息](https://git-scm.com/book/zh/v2/ch00/_changing_multiple) 一节中，我们也结合 `-i` 选项将其用于交互式的脚本模式。

### git revert

`git revert` 命令本质上就是一个逆向的 `git cherry-pick` 操作。 它将你提交中的变更的以完全相反的方式的应用到一个新创建的提交中，本质上就是撤销或者倒转。

我们在 [还原提交](https://git-scm.com/book/zh/v2/ch00/_reverse_commit) 一节中使用此命令来撤销一个合并提交。

## 管理

如果你正在管理一个 Git 仓库，或者需要通过一个复杂的方法来修复某些东西，Git 提供了一些管理命令来帮助你。

### git gc

`git gc` 命令在你的仓库中执行 “garbage collection”，删除数据库中不需要的文件和将其他文件打包成一种更有效的格式。

此命令一般在背后为你工作，虽然你可以手动执行它-如果你想的话。 我们在[维护](https://git-scm.com/book/zh/v2/ch00/_git_gc) 一节中研究此命令的几个示例。

### git fsck

`git fsck` 命令用来检查内部数据库的问题或者不一致性。

我们只在 [数据恢复](https://git-scm.com/book/zh/v2/ch00/_data_recovery) 这一节中快速使用了一次此命令来搜索所有的悬空对象（dangling object）。

### git reflog

`git reflog` 命令分析你所有分支的头指针的日志来查找出你在重写历史上可能丢失的提交。

我们主要在 [引用日志](https://git-scm.com/book/zh/v2/ch00/_git_reflog) 一节中提到了此命令，并在展示了一般用法，及如何使用 `git log -g` 来通过 `git log` 的输出来查看同样的信息。

我们同样在 [数据恢复](https://git-scm.com/book/zh/v2/ch00/_data_recovery) 一节中研究了一个恢复丢失的分支的实例。

### git filter-branch

`git filter-branch` 命令用来根据某些规则来重写大量的提交记录，例如从任何地方删除文件，或者通过过滤一个仓库中的一个单独的子目录以提取出一个项目。

在 [从每一个提交中移除一个文件](https://git-scm.com/book/zh/v2/ch00/_removing_file_every_commit) 一节中，我们解释了此命令，并探究了其他几个选项，例如 `--commit-filter`，`--subdirectory-filter` 及 `--tree-filter` 。

在 [Git-p4](https://git-scm.com/book/zh/v2/ch00/_git_p4) 和 [TFS](https://git-scm.com/book/zh/v2/ch00/_git_tfs) 的章节中我们使用它来修复已经导入的外部仓库。

## Reference
- [# Reference](https://git-scm.com/docs)
- [# Book: ProGit](https://git-scm.com/book/zh/v2) 

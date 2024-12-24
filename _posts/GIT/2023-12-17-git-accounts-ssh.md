---
title: "SSH登录多个 Gitee & Github 账户"
description: 
date: 2023-12-17 10:00:00 +0800
categories: [GIT]
tags: [GIT, Gitee, Github, ssh]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## 生成ssh公钥私钥并将公钥添加至账户中
```
$ ssh-keygen -t rsa -C "xxx@xxxxxx.com"
```
![输入图片说明](/imgs/git-accounts-ssh/2023-12-17/GepTMQXvrGfEvGSl.png)  
在生成SSH密钥过程中，直接回车生成的文件名默认为id_rsa，在此可键入自定义文件名，如`id_rsa_gitee_test`，生成文件可在`~/.ssh/`文件夹下找到。(此步也可以全部回车默认生成后再到ssh文件夹下对公私钥文件重命名)

登录Gitee/Github账户，添加公钥信息。

如果你同时拥有Gitee和Github账户，又更甚者有多个Gitee账户和多个Github账户，你只需要根据上述步骤，为每个账户生成其专门的SSH密钥（通过文件名区分）。

当出现**报错**：  
【SSH】git@gitee.com: Permission denied (publickey)  
**原因**：  
新版本的 Git for Windows 使用的 OpenSSH 版本是 8.8，默认使用的是 RSA-SHA2 算法，而 Gitee 使用的 golang ssh 库使用的是 RSA-SHA1 算法，造成公钥验证不通过。  
**解决**：  
在 ~/.ssh/ 下增加 config 文件，config 文件增加如下内容：  
```
Host gitee.com   
HostkeyAlgorithms +ssh-rsa   
PubkeyAcceptedAlgorithms +ssh-rsa  
```
**这三行文本的意思是：让请求 Gitee 继续使用 RSA-SHA1 算法。**  
>参考：[# git@gitee.com: Permission denied (publickey)](https://juejin.cn/post/7026552940257083399)

配置完成后.ssh文件夹下的内容：  
![输入图片说明](/imgs/git-accounts-ssh/2023-12-17/b3bHWNMowOj7kVb5.png)  

## 配置 config
多账号必须配置 config 文件，若无 config 文件，则需创建 config 文件
```bash
$ touch ~/.ssh/config   
```
其内容如下：
```
#请求 Gitee 继续使用 RSA-SHA1 算法
Host gitee.com 
HostkeyAlgorithms +ssh-rsa 
PubkeyAcceptedAlgorithms +ssh-rsa

#github syx
Host github_syx.com
Hostname ssh.github.com
IdentityFile ~/.ssh/id_rsa_syx

#github hanliu
Host github_hanliu.com
Hostname ssh.github.com
IdentityFile ~/.ssh/id_rsa_github

#gitee hanliu
Host gitee_hanliu.com
HostName gitee.com
IdentityFile ~/.ssh/id_rsa_gitee
PreferredAuthentications publickey
```
本文件配置了两个Github账户和一个Gitee账户。

配置文件中使用到的配置字段信息解释：  
-   Host  
    它涵盖了下面一个段的配置，我们可以通过他来替代将要连接的服务器地址。  
    这里可以使用任意字段或通配符。  
    当ssh的时候如果服务器地址能匹配上这里Host指定的值，则Host下面指定的HostName将被作为最终的服务器地址使用，并且将使用该Host字段下面配置的所有自定义配置来覆盖默认的/etc/ssh/ssh_config配置信息。  
-   HostName  
    真正连接的服务器地址 
-   IdentityFile  
    指定本次连接使用的密钥文件
-   PreferredAuthentications  
    指定优先使用哪种方式验证，支持密码和秘钥验证方式，可不配置
-   Port  
    自定义的端口。默认为22，可不配置
-   User  
    自定义的用户名，默认为git，可不配置   

配置完成后，可使用`ssh -T git@Host`进行连接测试，Host需要根据配置文件定义进行替换，如下即为连接成功。  
```
$ ssh -T git@github_hanliu.com
Hi MainHanLiu! You've successfully authenticated, but GitHub does not provide shell access.
```

## ssh使用
在克隆仓库时，不能直接使用
```
git clone git@github.com:xxx/xxx.git
```
而需要根据config文件中设置的Host进行对应和修改，例如
```
git clone git@github_hanliu.com:xxx/xxx.git
```

## SourceTree设置多个ssh私钥
工具-选项-SSH客户端配置  
ssh路径之间使用英文分号分割  
![输入图片说明](/imgs/git-accounts-ssh/2023-12-17/jVWUspcJglZODe38.png)

回到SourceTree修改SSH远程仓库路径  
![输入图片说明](/imgs/git-accounts-ssh/2023-12-17/3guQSVb8O4pSCKQa.png)  
这里拉取的是Gitee上的项目,框起来的地方默认是gitee.com，根据上面的描述我们修改成配置文件中的gitee_hanliu，这样在拉取项目的时候会自动使用配置文件中定义的私钥。  

## Reference
- [# 配置同时使用 Gitlab、Github、Gitee(码云) 共存的开发环境](https://www.jianshu.com/p/68578d52470c)
- [# 一台电脑双GitHub账户配置](https://zhuanlan.zhihu.com/p/107341502)
- [# 如何在一台电脑上同时使用Gitee（码云）和Github？](https://zhuanlan.zhihu.com/p/78978851)
- [# 解决SourceTree本地多ssh key的问题(Window系统)](https://blog.csdn.net/qq_26343241/article/details/103489413)

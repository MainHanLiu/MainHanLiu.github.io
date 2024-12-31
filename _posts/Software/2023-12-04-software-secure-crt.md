---
title: "SecureCRT Config"
description: 
date: 2023-12-04 10:00:00 +0800
categories: [Software, Config]
tags: [Software, Config]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---
## 配置  
终端-仿真-Linux   
终端-仿真-模式-换行 新行模式  
终端-外观-Dark Pastel  
日志文件-连接时启动日志  
日志文件-日志文件名  
```
D:\admin\Documents\%S (%Y-%M-%D %h%m%s).log
```
日志文件-在每一行
```
[%Y-%M-%D %h:%m:%s:%t]  
```
![输入图片说明](/imgs/software-secure-crt/2023-12-26/OvY63iFUgQg0uUOO.png)
![输入图片说明](/imgs/software-secure-crt/2023-12-26/go9l3pMzylHQo4jK.png)

## SecureCRT 脚本
- [# SecureCRT 定时发送一个字符串](https://blog.csdn.net/scanf_linux/article/details/109025307)
- [# 利用SecureCrt实现自动化脚本](https://blog.csdn.net/wanxuexiang/article/details/83445326)

##  使用SecureCRT工具设置快捷自动输入指令
- 选择"**View**"->"**Button Bar**"菜单
- 在Button Bar上右键，在弹出菜单选择"**New Button**"。
- 构造要发送的命令按钮的指令的字符串: `uname -r \r` 命令按钮的标签名称: **uname**，在命令按钮添加成功后，在Button Bar可以查看相应的标签按钮。
- 点击uname名称的按钮，则会自动发送`uname -r`的指令, 并执行指令返回结果。

## Reference
- [# SecureCRT安装、汉化、上传、美化](https://blog.csdn.net/qq_52089863/article/details/132722671)  

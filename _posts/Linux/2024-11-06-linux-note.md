---
title: "Linux Note"
description: 
date: 2024-11-06 10:00:00 +0800
categories: [Linux]
tags: [Linux, Note]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## Linux cmd manual
[# Linux命令大全(手册)  ](https://www.linuxcool.com/)

[# Linux 常用 C 函数参考手册](https://getiot.tech/manual/linux-c-functions/index.html# )

[# Linux C function() 参考手册](https://angela0.github.io/linux_c/)

[# C 标准库 - 参考手册](https://www.runoob.com/cprogramming/c-standard-library.html)

[# C 标准库 - 搜索](https://www.runoob.com/?s=snprintf)

## C/C++中文参考手册
[# C/C++中文参考手册(C++23标准) 离线chm最新版](https://blog.csdn.net/gitblog_09778/article/details/141944006)

[# C/C++中文参考手册-项目地址](https://gitcode.com/open-source-toolkit/851eb )

## ZLG资料
https://manual.zlg.cn/web/#/30/1095

## vim键位

https://www.cnblogs.com/RioTian/p/17947391

https://blog.csdn.net/BjarneCpp/article/details/110923086


## 在文本模式与桌面GUI模式之间切换，以减少内存消耗  
[# 如何在 Ubuntu 或 Debian 上启动进入命令行](https://cn.linux-console.net/?p=9029)  

### 关闭图形界面
```bash
sudo systemctl set-default multi-user.target
```
### 打开图形界面
```bash
sudo systemctl set-default graphical.target
```

## Question
MCU1的tick上电不是从0开始的？从何开始？如何计算tick的时间？
>MCU2的tick上电为0,1tick=1ms，u32溢出需要四年

CAN的读写在两个线程不需要互斥吗？因为总线仲裁机制？在何处实现的？

## SIGCHLD
子进程结束时, 父进程会收到这个信号。
如果父进程没有处理这个信号，也没有等待(wait)子进程，子进程虽然终止，但是还会在内核进程表中占有表项，这时的子进程称为僵尸进程。这种情 况我们应该避免(父进程或者忽略SIGCHILD信号，或者捕捉它，或者wait它派生的子进程，或者父进程先终止，这时子进程的终止自动由init进程 来接管)。
### 使用signal(SIGCHLD, SIG_IGN)处理僵尸进程
通过signal(SIGCHLD, SIG_IGN)通知内核对子进程的结束不关心，由内核回收。如果不想让父进程挂起，可以在父进程中加入一条语句：signal(SIGCHLD,SIG_IGN);表示父进程忽略SIGCHLD信号，该信号是子进程退出的时候向父进程发送的。

## SIG_ING
忽略的意思

## kill(pid, 0)
这里要用到的信号为0，并不表示要关闭某个程序，而表示对进程运行状态进行监控，如果发现进程关闭或其他异常，将返回状态码1，反之，如果发现进程运行正常，将返回状态码0


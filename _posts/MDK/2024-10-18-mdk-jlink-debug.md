---
title: "局域网内远程调试JLINK"
description: 
date: 2024-10-18 10:00:00 +0800
categories: [MDK]
tags: [MDK]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## 局域网内远程调试JLINK

- 服务器PC  

1. JLINK连接MCU（USB——JLINK——MCU）

2. 打开Jlink Remote sever  
![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/BQdGom7oePPmgwYw.png)

3. 使用LAN方式连接  
![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/fZ9u4jNK0OwI7gTG.png)

-  客户端PC

在客户端PC（局域网内）有两种方式连接  

第一种，直接使用KEIL，选择TCP/IP连接并填写服务器PC上的IP地址  
![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/t3LdYNtZ97Cy51P1.png)

连接成功时，服务器PC会如下显示  
![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/9YDJGm6AY02DvOkx.png)

第二种，使用Jlink Commander ，输入指令IP 192.168.2.12  
![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/uPvG8bEAFflIIxpM.png)

![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/v65i0qid92vLtjxY.png)

>
1. 输入connect（或控制指令 如halt暂停cpu）建立连接；
2. 输入芯片型号，直接回车可以选择默认选项，或输入"?"将会弹窗选择；
3. 选择连接方式，根据实际情况选择JTAG或者SWD；
4. 设置下载速度，直接回车可以选择默认选项；
5. 连接成功，使用Jlink指令进行控制

![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/JnP9t731HfiBeTBd.png)

## Jlink-commander

[# Jlink-commander指令 语法wiki](https://wiki.segger.com/J-Link_Commander#Commands)

为什么要使用到J-LinkCommander呢？

大部分情况下，我们使用J-link都是在IDE中debug使用的，出现问题，直接debug复现然后解决。这是最常见的开发方式。

但是有些情况我们不能使用IDE直接debug，比如发布版本（JTAG或SWD接口可以正常使用），大批量生产时发现几片异常的板子，重新debug问题就消失了。这个时候使用IDE直接debug已经不能解决问题，我们需要使用J-LinkCommander读取关键寄存器的值来协助我们定位排查问题。

当MCU里面的程序跑飞的时候，不要断电，将MCU连接到J-Link，打开J-Link Commander，并输入命令“halt”。这样，就能暂停内核，获取内核通用寄存器信息。有了PC指针和SP指针的内容，就能很方便的定位到程序在哪里死掉了，或者程序在进入HardFault之前执行了什么指令  

![输入图片说明](/imgs/mdk-jlink-debug/2024-10-18/dYUr3N8fCP9IwazH.png)

```
常用指令
Mem 读内存
mem8 读8比特内存
mem16 读16比特内存
mem32 读32比特内存
w1 写8比特内存
w2 写16比特内存
w4 写32比特内存
h 停止cpu运行的程序
setbp 设置断点
g 跳到代码段地址执行
s 单步执行（调试用）
r 复位
q 退出
```

## Reference
- [# jlink远程下载笔记](https://blog.csdn.net/u012850592/article/details/87866309)
- [# 如何使用J-Link远程调试？](https://shequ.stmicroelectronics.cn/thread-627759-1-1.html)

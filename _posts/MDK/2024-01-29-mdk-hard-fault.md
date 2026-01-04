---
title: "Hard Fault 问题定位方法"
description: 
date: 2024-01-29 10:00:00 +0800
categories: [MDK]
tags: [MDK]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## 常见原因
1. 内存溢出或者访问越界。
2. 堆栈溢出。

## MCU处理过程
1. 有一个压栈的过程，若产生异常时使用 的是PSP（进程栈指针），就压入到 PSP 中，若产生异常时使用MSP（主栈指针），就压入到MSP 中。
2. 根据处理器的模式和使用的堆栈，设置 LR（链接寄存器） 的值（设置完LR 的值再压栈）
3. 异常保存，硬件自动把 8 个寄存器的值压入堆栈（8 个寄存器依次为 xPSR、PC、LR、R12以及 R3~R0）。如果异常发生时，当前的代码正在使用PSP，则上面8个寄存器压入PSP; 否则就压入MSP。

## Hard Fault 定位方法
- **方法一  通过LR回溯**  
>LR连接寄存器里面保存的是调用子程序之前的PC的值。  
>因为CM3内部使用了指令流水线，读PC时返回的值是当前指令的地址+4，所以进入异常之前自动加载到LR的值也为调用子程序前的指令地址+4

1. debug模式下在HardFault_Handler处理函数设置断点，当出现hardfault时，debug停止；
2. 此时打开VIEW->Register Window查看LR的值，判断出现hardfault时是压栈入MSP还是PSP；
3. 打开VIEW->Memory Window，输入MSP或者PSP的值，将窗口调整为4 Bytes，从上往下依次存放的是R0/R1/R2/R3/R12/LR，第6个即为LR的值；
4. 打开VIEW->Disassembly Window，右键单击选择Show disassembly at address...,在弹出窗口输入LR的值，即可跳转到出现hardfault后运行的代码；
5. 在这段代码前就是出现hardfault的位置，仔细分析查找原因。
![输入图片说明](/imgs/mdk-hard-fault/2024-04-28/qBRLCf0n3B852uMV.png)

- **方法二   通过Call Stack Window定位**  
1. debug模式下在HardFault_Handler处理函数设置断点，当出现hardfault时，debug停止；
2. 此时打开VIEW->Call Stack Window，第一行即为目前执行的函数HardFault_Handler
3. 在HardFault_Handler上右键单击选择Show Caller Code，即可定位到出现hardfault被调用的函数
4. 找到函数，仔细分析，查找原因
![输入图片说明](/imgs/mdk-hard-fault/2024-04-28/3MiGvZub8bQZc0dr.png)

**说明**  
hardfault定位方法不唯一，可以多个方法结合进行定位。  
遇到hardfault尽量排查出来，一般情况都是内存越界，堆栈溢出导致的。

>举例：  
>telnet通过网口输出太快时，曾引发hard fault： bus fault SCB_CFSR_BFSR:0x04 IMPRECISERR

## Reference
- [# 技术分享 / HardFault问题分析](https://developers.goodix.com/zh/bbs/blog_detail/b0f9329799b04397b384214a8953efa3)
- [# [STM32] HardFault问题详细分析及调试笔记](https://blog.csdn.net/m0_54916619/article/details/129979222)
- [# Cortex-M 处理器 hardfault 定位方法和步骤（基于Keil mdk）](https://blog.csdn.net/supermuscleman/article/details/103929606)
- [# Cortex-M内核硬件故障问题的分析方法](https://blog.csdn.net/fhqlongteng/article/details/112756689)
- [# [RTT] RT-Thread Hard Fault 死机问题定位方法](https://blog.csdn.net/weixin_43854928/article/details/123997001)
- [# 如何分析Hard Fault错误的原因？](https://zhuanlan.zhihu.com/p/425428779)

## 引申学习
- [# [RTT] RT-Thread RT_ASSERT 断言死机问题定位方法](https://blog.csdn.net/weixin_43854928/article/details/123884381?spm=1001.2014.3001.5502)
- [# [RTT] RT-Thread线程调度机制、线程切换时机分析](https://blog.csdn.net/weixin_43854928/article/details/119985822)

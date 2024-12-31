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
#   alt: Here is the text upder the image
---

## RT-Thread Hard Fault 死机问题定位方法
telnet通过网口输出太快时，引发hard fault：  
bus fault SCB_CFSR_BFSR:0x04 IMPRECISERR

LR连接寄存器里面保存的是调用子程序之前的PC的值。 因为CM3内部使用了指令流水线，读PC时返回的值是当前指令的地址+4，所以进入异常之前自动加载到LR的值也为调用子程序前的指令地址+4。
 
## Reference
- [# [STM32] HardFault问题详细分析及调试笔记](https://blog.csdn.net/m0_54916619/article/details/129979222)
- [# Cortex-M 处理器 hardfault 定位方法和步骤（基于Keil mdk）](https://blog.csdn.net/supermuscleman/article/details/103929606)
- [# Cortex-M内核硬件故障问题的分析方法](https://blog.csdn.net/fhqlongteng/article/details/112756689)
- [# [RTT] RT-Thread Hard Fault 死机问题定位方法](https://blog.csdn.net/weixin_43854928/article/details/123997001)
- [# 如何分析Hard Fault错误的原因？](https://zhuanlan.zhihu.com/p/425428779)

## 引申学习
- [# [RTT] RT-Thread RT_ASSERT 断言死机问题定位方法](https://blog.csdn.net/weixin_43854928/article/details/123884381?spm=1001.2014.3001.5502)
- [# [RTT] RT-Thread线程调度机制、线程切换时机分析](https://blog.csdn.net/weixin_43854928/article/details/119985822)

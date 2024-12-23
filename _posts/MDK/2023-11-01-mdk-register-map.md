---
title: "STM32寄存器地址名称映射关系"
description: 
date: 2023-11-01 10:00:00 +0800
categories: [MDK]
tags: [MDK]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

![输入图片说明](/imgs/mdk-register-map/2023-11-01/fJBmoLGaXXExi0K2.png)  
首先外设基地址是个常量地址，APB2的地址就比外设基地址偏移了0x10000，所以APB2的地址是0x40010000；

因为 GPIO 都是挂载在 APB2 总线 之上，所以它的基地址是由 APB2 总线的基地址+GPIOA 在 APB2 总线上的偏移地址决定 的。同理依次类推，我们便可以算出 GPIOA 基地址了。GPIOA 基地址为 0x40010000 + 0x0800 = 0x40010800

最后GPIOA_BASE地址强制转换为了GPIO_TypeDef结构体类型，所以GPIOA便指向了该结构体第一个寄存器CRL的首地址上，因为结构体存储的成员的地址是连续的，所以知道首地址，再根据每个成员的偏移量，便能推算出每个寄存器的地址了。  
![输入图片说明](/imgs/mdk-register-map/2023-11-01/vofDFejkcbsl0lWd.png)  

## Reference
- [# STM32寄存器地址名称映射关系](https://blog.csdn.net/weixin_46251230/article/details/126584406) 

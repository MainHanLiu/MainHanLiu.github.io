---
title: "为什么STM32从Flash地址0x08000000的启动重映射"
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

在初写STM32程序时，遇到一个困惑，STM32的Flash在MDK里被设置为起始地址0x0800 0000，而CM3手册规定芯片复位时要从0x0000 0000地址开始取出中断向量 ，那STM32怎么样执行代码呢？地址重映射？或者在0x0000 0000里有对应有实际存储器？

仔细阅读手册，发现这件事是因为STM32设计的Flash起始地址是在0x0800 0000位置开始的。全部代码都只能从这里开始存储（物理上）。详见STM32 referenc manual手册第54页。

那既然从这里才能存储代码，就必须在MDK里设置Flash地址为0x0800 0000。

这样就还有一个问题，理论上，CM3中规定上电后CPU是从0地址开始执行，但是这里中断向量表却被烧写在0x0800 0000地址里，那启动时不就找不到中断向量表了？既然CM3定下的规矩是从0地址启动，SMT32当然不能破坏ARM定下的“规矩”，所以它做了一个启动映射的过程，就是和芯片上总能见到的BOOT0和BOOT1有关了，当选择从主Flash启动模式后，芯片一上电，Flash的0x0800 0000地址被映射到0地址处，不影响CM3内核的读取，所以这时的CM3既可以在0地址处访问中断向量表，也可以在0x0800 0000地址处访问中断向量表，而代码还是在0x0800 0000地址处存储的。这就是最难理解的地方，其实，这是基本上所有ARM芯片采用的启动映射方法。ARM7，ARM9没有内部Flash的通常都是这样做的。这个过程出自STM32 referenc manual手册，里面是有说明的。

还要注意，这个中断向量表是可以在程序中再次被映射的。控制它的就是CM3已经规定的NVIC寄存器SCB->VTOR。在STM32库中给出的启动代码里，startup_stm32f10x_hd.s文件里，第146行，是上电后读取中断向量表中的复位中断位置，并执行复位中断处理代码
```
; Reset handler
Reset_Handler   PROC
                EXPORT  Reset_Handler             [WEAK]
                IMPORT  __main
                IMPORT  SystemInit
                LDR     R0, =SystemInit
                BLX     R0               
                LDR     R0, =__main
                BX      R0
                ENDP
```
注意复位后第一个被执行的是SystemInit代码，这个代码在库目录下的system_stm32f10x.c文件里，它初始化了时钟，NVIC等一系列操作，这里摘要与中断向量有关的代码
```c
void SystemInit (void)
{
......
#ifdef VECT_TAB_SRAM
  SCB->VTOR = SRAM_BASE | VECT_TAB_OFFSET; /* Vector Table Relocation in Internal SRAM. */
#else
  SCB->VTOR = FLASH_BASE | VECT_TAB_OFFSET; /* Vector Table Relocation in Internal FLASH. */
#endif 
}
```
可以看出中断向量重映射是一个选择性编译，通常宏定义VECT_TAB_SRAM都没有被定义，所以这里执行结束后，SCB->VTOR就是FLASH_BASE了，值为0x0800 0000。以后CM3再取中断向量，就会根据SCB->VTOR的设置，从这里取向量执行了。中断向量自此终于转正。

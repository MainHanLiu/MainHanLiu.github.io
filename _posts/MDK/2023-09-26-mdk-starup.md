---
title: "MDK 启动文件详解"
description: 
date: 2023-09-26 10:00:00 +0800
categories: [MDK]
tags: [MDK, 启动文件]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## 启动文件
　　无论是是何种MCU，从简单的51，MSP430，到ARM9，ARM11，A7 都必须有启动文件，因为对于嵌入式开发，绝大部分情况都是使用C语言，而C语言一般都是从main 函数开始，但是对于MCU来说，他是如何找到并执行main函数的，就需要用到“启动文件”，就是各种 startup_xxxx.s 文件。启动文件是使用机器认识的汇编语言，经过一些必要的配置，最终能够调用 main 函数，使得用户程序能够在 MCU上正常运行起来的必备文件。  
　　在嵌入式系统中，启动文件是整个系统非常关键的部分，它会进行一些底层的初始化，构建程序运行必要的环境，比如堆栈初始化，变量初始化等。如果启动文件出现错误，则整个系统就跑不起来，因此研究启动文件非常必要。  
　　在keil中，启动文件由汇编代码编写，一般命名为startup_xxx.s，xxx为支持的某种芯片，比如可以是lpc15xx（NXP的LPC15xx系列）、MK60D10（飞思卡尔）、stm32f10x（意法半导体stm32f10x系列）等Cortext-M0/M3/M4内核芯片。它们的代码格式非常相近，根据启动文件代码由上到下的编写顺序。

将启动文件理解为一种描述性的代码，不要拘泥于它的实现机制，我之前一直在想启动代码中汇编代码的执行顺序，后来没明白，估计和keil工具有关系,因此只需要记住，程序上电执行的第一条代码（我们可以控制的代码），就是启动文件中Reset Handler函数，Reset Handler函数的地址存放在flash的0x00000004处。

在启动文件中，会设置MSP（主堆栈指针）和PC（程序计数器）的值，MSP的地址默认是0x00000000，PC的地址默认是0x00000004，这两个地址可以通过CORTEX-M中的VTOR寄存器来进行重映射，修改。

启动文件内容分析：
1.  定义堆栈空间；
2.  定义中断向量表
3.  定义复位中断函数（Reset_Handler）；
4.  定义弱[WEAK]声明下的其它中断异常服务函数；
5.  定义堆栈初始化

程序上电后，启动流程
1.  从中断向量表地址开始处初始化堆栈指针SP=_initial_sp和PC指针=Reset_Handler
2.  跳转到复位程序入口处，初始化向量表，配置系统时钟，将堆栈地址传递给库函数，调用C库函数_main初始化用户堆栈，搬运data段和bss段到指定的RAM区域中，最后调用main函数，进入用户程序。

### 汇编指令

| 指令名称 | 作用 |
|--|--|
| EQU | 给数字常量取一个符号名，相当于C语言中的define |
| AREA | 汇编一个新的代码段或者数据段 |
| SPACE | 分配内存空间 |
| PRESERVE8 | 当前文件堆栈需按照8字节对齐 |
| EXPORT | 声明一个标号具有全局属性，可被外部的文件使用 |
| DCD | 以字为单位分配内存，要求4字节对齐，并要求初始化这些内存 |
| PROC | 定义子程序，与ENDP成对使用，表示子程序结束 |
| WEAK | 弱定义，如果外部文件声明了一个标号，则优先使用外部文件定义的标号， 如果外部文件没有定义也不出错。要注意的是：这个不是ARM的指令，是编译器的，这里放在一起只是为了方便。 |
| IMPORT | 声明标号来自外部文件，跟C语言中的EXTERN关键字类似 |
| B | 跳转到一个标号 |
| ALIGN | 编译器对指令或者数据的存放地址进行对齐，一般需要跟一个立即数，缺省表示4字节对齐 要注意的是：这个不是ARM的指令，是编译器的，这里放在一起只是为了方便。 |
| END | 到达文件的末尾，文件结束 |
| IF,ELSE,ENDIF | 汇编条件分支语句，跟C语言的if else类似 |
| LDR | 从存储器中加载字到一个寄存器中 |
| BL | 跳转到由寄存器/标号给出的地址，并把跳转前的下条指令地址保存到LR |
| BLX | 跳转到由寄存器给出的地址，并根据寄存器的LSE确定处理器的状态，还要把跳转前的下条指令地址保存到LR |
| BX | 跳转到由寄存器/标号给出的地址，不用返回 |

### 启动文件代码

#### stack栈
```c
Stack_Size      EQU     0x00000400

                AREA    STACK, NOINIT, READWRITE, ALIGN=3
Stack_Mem       SPACE   Stack_Size
__initial_sp
```
开辟栈的大小为0X00000400（1KB），名字为STACK，NOINIT表示不初始化，可读可写，8字节对齐（2^3）。
栈的作用是用于局部变量，函数调用，函数形参等的开销，栈的大小不能超过内部SRAM的大小。

`__initial_sp`只是一个标号，标号主要用于表示一片内存空间的某个位置，等价于C语言中的“地址”概念。地址仅仅表示存储空间的位置。
标号 `__initial_sp` 紧挨着SPACE语句放置，表示了栈顶地址，栈是由高向低生长的。栈顶处于RAM最大地址处。

如果编写的程序比较大，定义的局部变量很多，那么就需要修改栈的大小。如果某一天，你写的程序出现了莫名奇怪的错误，并进入了硬fault的时候，这时你就要考虑下是不是栈不够大，溢出了。

#### heap堆
```c
Heap_Size       EQU     0x00000400

                AREA    HEAP, NOINIT, READWRITE, ALIGN=3
__heap_base
Heap_Mem        SPACE   Heap_Size
__heap_limit
```
开辟堆的大小为0X00000200（512字节），名字为HEAP，NOINIT表示不初始化，可读可写，8字节对齐（2^3）。
`__heap_base`表示堆的起始地址，`__heap_limit`表示堆的结束地址。堆是由低向高生长的，跟栈的生长方向相反。动态分配内存用到堆。

堆主要用来动态内存的分配，像malloc()函数申请的内存就在堆上面。这个在STM32里面用的比较少。

堆和栈的属性都是 READWRITE 可读写，可读写段保存于 SRAM区，即地址0x2000 0000 地址后。

### PRESERVE8
指定当前文件的堆栈按照8字节对齐。

### THUMB
表示后面指令兼容THUMB指令。THUBM是ARM以前的指令集，16bit， 现在Cortex-M系列的都使用THUMB-2指令集，THUMB-2是32位的，兼容16位和32位的指令，是THUMB的超集。

### 建立中断向量表
```
		AREA    RESET, DATA, READONLY
		EXPORT  __Vectors
		EXPORT  __Vectors_End
		EXPORT  __Vectors_Size
```
AREA 定义了一段名为 RESET 的 READONLY 只读数据段，只读属性保存在 Flash 区。（如果STM32从Flash启动，则此中断向量表的地址为0x0800 0000）
声明 __Vectors、__Vectors_End和__Vectors_Size这三个标号具有全局属性，可供外部的文件调用。
```
__Vectors      DCD     __initial_sp               ; Top of Stack 首地址一定是栈顶地址
               DCD     Reset_Handler              ; Reset Handler 复位程序地址
               DCD     NMI_Handler                ; NMI Handler
               DCD     HardFault_Handler          ; Hard Fault Handler
               DCD     MemManage_Handler          ; MPU Fault Handler
               DCD     BusFault_Handler           ; Bus Fault Handler
               DCD     UsageFault_Handler         ; Usage Fault Handler
               DCD     0                          ; Reserved 表示保留
               DCD     0                          ; Reserved
               DCD     0                          ; Reserved
               DCD     0                          ; Reserved
               DCD     SVC_Handler                ; SVCall Handler
               DCD     DebugMon_Handler           ; Debug Monitor Handler
               DCD     0                          ; Reserved
               DCD     PendSV_Handler             ; PendSV Handler
               DCD     SysTick_Handler            ; SysTick Handler
 
               ; External Interrupts              ; 外设的中断
               DCD     WWDG_IRQHandler            ; Window Watchdog
               DCD     PVD_IRQHandler             ; PVD through EXTI Line detect
               DCD     TAMPER_IRQHandler          ; Tamper
               DCD     RTC_IRQHandler             ; RTC
               DCD     FLASH_IRQHandler           ; Flash
               DCD     RCC_IRQHandler             ; RCC
               DCD     EXTI0_IRQHandler           ; EXTI Line 0
               DCD     EXTI1_IRQHandler           ; EXTI Line 1
               DCD     EXTI2_IRQHandler           ; EXTI Line 2
               DCD     EXTI3_IRQHandler           ; EXTI Line 3
               DCD     EXTI4_IRQHandler           ; EXTI Line 4
               DCD     DMA1_Channel1_IRQHandler   ; DMA1Channel 1
               DCD     DMA1_Channel2_IRQHandler   ; DMA1Channel 2
               DCD     DMA1_Channel3_IRQHandler   ; DMA1Channel 3
               DCD     DMA1_Channel4_IRQHandler   ; DMA1Channel 4
               DCD     DMA1_Channel5_IRQHandler   ; DMA1Channel 5
               DCD     DMA1_Channel6_IRQHandler   ; DMA1Channel 6
               DCD     DMA1_Channel7_IRQHandler   ; DMA1 Channel 7
               DCD     ADC1_2_IRQHandler          ; ADC1 & ADC2
               DCD     USB_HP_CAN1_TX_IRQHandler  ; USBHigh Priority or CAN1 TX
               DCD     USB_LP_CAN1_RX0_IRQHandler; USB Low  Priority or CAN1 RX0
               DCD     CAN1_RX1_IRQHandler        ; CAN1 RX1
               DCD     CAN1_SCE_IRQHandler        ; CAN1 SCE
               DCD     EXTI9_5_IRQHandler         ; EXTI Line 9..5
               DCD     TIM1_BRK_IRQHandler        ; TIM1 Break
               DCD     TIM1_UP_IRQHandler         ; TIM1 Update
               DCD     TIM1_TRG_COM_IRQHandler    ; TIM1Trigger and Commutation
               DCD     TIM1_CC_IRQHandler         ; TIM1 Capture Compare
               DCD     TIM2_IRQHandler            ; TIM2
               DCD     TIM3_IRQHandler            ; TIM3
               DCD     TIM4_IRQHandler            ; TIM4
               DCD     I2C1_EV_IRQHandler         ; I2C1 Event
               DCD     I2C1_ER_IRQHandler         ; I2C1 Error
               DCD     I2C2_EV_IRQHandler         ; I2C2 Event
               DCD     I2C2_ER_IRQHandler         ; I2C2 Error
               DCD     SPI1_IRQHandler            ; SPI1
               DCD     SPI2_IRQHandler            ; SPI2
               DCD     USART1_IRQHandler          ; USART1
               DCD     USART2_IRQHandler          ; USART2
               DCD     USART3_IRQHandler          ; USART3
               DCD     EXTI15_10_IRQHandler       ; EXTI Line 15..10
               DCD     RTCAlarm_IRQHandler        ; RTC Alarm through EXTI Line
               DCD     USBWakeUp_IRQHandler       ; USB Wakeup from suspend
               DCD     TIM8_BRK_IRQHandler        ; TIM8 Break
               DCD     TIM8_UP_IRQHandler         ; TIM8 Update
               DCD     TIM8_TRG_COM_IRQHandler    ; TIM8Trigger and Commutation
               DCD     TIM8_CC_IRQHandler         ; TIM8 Capture Compare
               DCD     ADC3_IRQHandler            ; ADC3
               DCD     FSMC_IRQHandler            ; FSMC
               DCD     SDIO_IRQHandler            ; SDIO
               DCD     TIM5_IRQHandler            ; TIM5
               DCD     SPI3_IRQHandler            ; SPI3
               DCD     UART4_IRQHandler           ; UART4
               DCD     UART5_IRQHandler           ; UART5
               DCD     TIM6_IRQHandler            ; TIM6
               DCD     TIM7_IRQHandler            ; TIM7
               DCD     DMA2_Channel1_IRQHandler   ; DMA2Channel1
               DCD     DMA2_Channel2_IRQHandler   ; DMA2Channel2
               DCD     DMA2_Channel3_IRQHandler   ; DMA2Channel3
               DCD     DMA2_Channel4_5_IRQHandler; DMA2 Channel4 & Channel5
__Vectors_End
__Vectors_Size EQU	   __Vectors_End - __Vectors
```
`__Vectors` 为向量表起始地址，_`_Vectors_End` 为向量表结束地址，两个相减即可算出向量表大小。

关键字`DCD`表示分配一个4字节的空间，后面的函数名即为中断服务函数入口地址。另外中断向量表一般存放在Flash 0地址。

Cortex-M3 内核规定，起始地址必须存放栈顶指针，而第二个地址则必须存放复位中断入口向量地址，这样在 Cortex-M3 内核复位后，会自动从起始地址的下一个 32 位空间取出复位中断入口向量，跳转执行复位中断服务程序。Cortex-M3 内核固定了中断向量表的位置, 但是起始地址是可变化的。

向量表在地址空间中的位置默认存放在0x00000000处（映射到0x8000000，两个是一个东西），但是还可以设置，通过 NVIC 中的一个重定位寄存器来指出向量表的地址。



### 定义复位中断服务函数/复位程序（Reset_Handler）
复位程序是系统上电后执行的第一个程序，复位程序也是中断程序，只是这个程序比较特殊，因此单独提出来讲解。
```
		AREA |.text|, CODE, READONLY
```
定义一个名称为.text的代码段，可读。
```
Reset_Handler PROC                          ;PROC表示程序的开始
              EXPORT Reset_Handler [WEAK]   ;使用EXPORT将Reset_Handler申明为可被外部引用，后面WEAK表示弱定义
              IMPORT SystemInit             ;表示该标号来自外部文件
              IMPORT __main                 ;表示该标号来自外部文件
              LDR R0, =SystemInit           ;从存储器中加载SystemInit到一个寄存器R0的地址中
              BLX R0                        ;跳转到寄存器R0的地址，并根据寄存器的 LSE 确定处理器的状态，还要把跳转前的下条指令地址保存到 LR
              LDR R0, =__main               ;从存储器中加载__main到一个寄存器R0的地址中
              BX R0                         ;这里跳转到至指定寄存器的地址后，不会返回
              ENDP                          ;和PROC是对应的，表示程序的结束
```

我们知道0地址（通常为0x0800 0000）存放`__initial_sp`，偏移为4的地址（通常为0x0800 0004）存放`Reset_Handler`。

MCU上电后，首先加载SP和PC，从0x0800 0000处读取栈顶地址并保存，然后从0x0800 0004读取中断向量表的起始地址加载到PC，这是复位程序的入口地址，加载PC后，程序跳转到`Reset_Handler`开始运行。

复位子程序是系统上电后第一个执行的程序，调用`SystemInit`函数初始化系统时钟，然后调用C库函数`_mian`，最终调用`main`函数，进入用户程序。

`SystemInit`是一个标准的库函数，在system_stm32f103xe.c这个库文件中定义。主要作用是配置系统时钟，这里调用这个函数之后，单片机的系统时钟配被配置指定频率。

`__main`是一个标准的C库函数，主要作用是初始化用户堆栈，这个是由编译器完成的，该函数最终会调用main函数去到C的世界。这就是为什么我们写的程序都有一个main函数的原因。
> 在`__main`函数中：
> .data段数据的初始化->
> .bss段变量清零->
> 设置堆栈指针->
> 库函数初始化（比如常用的malloc函数）->
> 如果必要会设置main函数的argc和argv两个参数->
> 调用用户main函数->
> 退出


### 其它中断异常服务函数，以及弱[WEAK]声明
```c
NMI_Handler    PROC
               EXPORT  NMI_Handler                [WEAK]
               B       .
               ENDP
HardFault_Handler\
               PROC
               EXPORT  HardFault_Handler          [WEAK]
               B       .
               ENDP
MemManage_Handler\
               PROC
               EXPORT  MemManage_Handler          [WEAK]
               B       .
               ENDP
// 其余省略...
```

`B . `为死循环。

启动文件里面已经帮我们写好所有中断的中断服务函数，跟我们平时写的中断服务函数不一样的就是这些函数都是空的，真正的中断复服务程序需要我们在外部的 C 文件里面重新实现，这里只是提前占了一个位置而已。

其他中断服务函数是弱声明的（由[WEAK]关键字标注）。所谓弱声明，即：如果用户定义了相同的函数则此处函数失效而使用用户定义的中断服务函数。这样是为了防止用户使能了中断而没有中断服务函数，从而造成程序崩溃。假设使能了中断，而用户又没有定义中断服务函数，当中断来临时，程序就会跳转到启动文件预先写好的空的中断服务程序中，并且在这个空函数中无限循环。


### 堆栈初始化
```
		;用户栈和堆初始化,由C库函数_main来完成
        IF      :DEF:__MICROLIB ;这个宏在KEIL里面开启

        EXPORT  __initial_sp
        EXPORT  __heap_base
        EXPORT  __heap_limit

        ELSE

        IMPORT  __use_two_region_memory ;这个函数由用户自己实现
        EXPORT  __user_initial_stackheap

__user_initial_stackheap PROC ;标号
        LDR     R0, =  Heap_Mem
        LDR     R1, =(Stack_Mem + Stack_Size)
        LDR     R2, = (Heap_Mem +  Heap_Size)
        LDR     R3, = Stack_Mem
        BX      LR
        ENDP

        ALIGN

        ENDIF

        END
```
`IF :DEF:__MICROLIB`是条件编译选项，如果勾选【Use MicroLIB】，即使用微库，则`__MICROLIB`会被定义，赋予标号`__initial_sp`（栈顶地址）、 `__heap_base`（堆起始地址）、`__heap_limit`（堆结束地址）全局属性， 可供外部文件调用；否则插入标号`__use_two_region_memory`，这个函数需要用户自己实现，具体要实现成什么样， 可在KEIL的帮助文档里面查询到，然后声明标号`__user_initial_stackheap`具有全局属性，可供外部文件调用，并实现这个标号的内容，KEIL C库函数会调用`__user_initial_stackheap`，堆栈参数通过R0~R3传递给KEIL C库。

## Reference
- [野火--启动文件详解](https://doc.embedfire.com/motor/f407jiaoyang/zh/latest/doc/chapter14/chapter14.html)  
- [KEIL中启动文件详解（汇编语言](https://www.cnblogs.com/mddblog/p/4920063.html)   
- [STM32的启动过程 — startup_xxxx.s文件解析（MDK和GCC双环境）](https://blog.csdn.net/weixin_42328389/article/details/120656722)

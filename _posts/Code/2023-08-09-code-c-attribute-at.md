---
title: "__attribute__((at(...))) 绝对定位"
description: 
date: 2023-08-09 10:00:00 +0800
categories: [Code, C]
tags: [Code, C, attribute]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---


## **`__attribute__((at(...)))` 绝对定位**  
`__attribute__` ：用来指定变量或结构位域的特殊属性，关键字后的双括弧中的内容是属性说明；  
`at`：用来设置变量的绝对地址，指定某个变量处于内存或 FLASH 里面的某个给定的地址。  
`__attribute__((at(绝对地址)))`的作用：  
（1）绝对定位到 Flash；  
定位到 flash 中，常用于固化信息，例如：设备的出厂信息，FLASH 标记等；  
```c
const uint8_t usFlashInitVal[] __attribute__((at(0x00030000))) = {0x11,0x22,0x33,0x44,0x55,0x66};
//定位在flash中，0x00030000开始的6个字节信息固定
```
（2）绝对定位到 RAM。
常用于数据量较大的缓存，如：串口接收数据。也用于某个位置的特定变量。
```c
uint8_t ucUsartRecvBuffer[USART_RECV_LEN] __attribute__ ((at(0x00025000)));	
//接收缓冲,最大USART_RECV_LEN个字节,起始地址为 0x00025000
```
注意：
1. 如果不加 const 修饰，则定位到了 RAM；加 const 修饰，则定位到了Flash  
2. 绝对定位不能在函数中定义，局部变量是定义在栈区，栈区是自动分配、释放，不能定义为绝对地址，只能于函数外定义；  
3.  定义的长度不能超过栈或 Flash 的大小，否则导致栈、Flash 溢出。 

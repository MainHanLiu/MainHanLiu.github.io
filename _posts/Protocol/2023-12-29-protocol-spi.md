---
title: "Protocol: SPI"
description: 
date: 2023-12-28 10:00:00 +0800
categories: [Protocol, SPI]
tags: [Protocol, SPI]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

**SPI**（Serial Peripheral Interface，串行外设接口）是一种高速、全双工、同步、串行、主从结构通信总线，并且在芯片的管脚上只占用四根线，节约了芯片的管脚，同时为PCB的布局上节省空间，提供方便，正是出于这种简单易用的特性，现在越来越多的芯片集成了这种通信协议。  
SPI协议主要作为主控芯片去配置外围芯片的接口协议，常用于短距离通讯，主要应用于 EEPROM、FLASH、实时时钟、AD 转换器、还有数字信号处理器和数字信号解码器之间。  
SPI 以主从方式工作，通常有一个主设备和一个或多个从设备。通信由主设备发起，主设备通过 CS 选择要通信的从设备，然后通过 SCLK 给从设备提供时钟信号，数据通过 MOSI 输出给从设备，同时通过 MISO 接收从设备发送的数据。  

需要说明的是，SPI只有主模式和从模式之分，**没有读和写的说法，因为实质上每次SPI是主从设备在交换数据。也就是说，你发一个数据必然会收到一个数据；你要收一个数据必须也要先发一个数据。**

CPOL是用来决定SCK时钟信号空闲时的电平，CPOL＝0，空闲电平为低电平，CPOL＝1时，空闲电平为高电平。CPHA是用来决定采样时刻的，CPHA=0，在每个周期的第一个时钟沿采样，CPHA＝1，在每个周期的第二个时钟沿采样。

SPI总线的传输速率**需要自定义，没有具体的规定**，一般为：400KHz~10MHz（AHB：100MHz，APB：50MHz）

### SPI 的通讯模式
SPI 有四种模式：
- CPOL=0，表示当SCLK=0时处于空闲态，空闲低电平，所以有效状态就是SCLK处于高电平时  
- CPOL=1，表示当SCLK=1时处于空闲态，空闲高电平，所以有效状态就是SCLK处于低电平时  
- CPHA=0，表示数据采样是在第1个边沿  
- CPHA=1，表示数据采样是在第2个边沿
![输入图片说明](/imgs/protocol-spi/2023-08-29/gvnxDwER2v0MrBTW.png)
![输入图片说明](/imgs/protocol-spi/2023-08-29/kKS750HZnbMu2dRi.png)

SPI的时钟极性和相位的配置通常称为 **SPI模式**，所有可能的模式都遵循以下约定；具体如下表所示；

| SPI Mode | CPOL | CPHA |
|--|--|--|
| 0 [00] | 0 | 0 |
| 1 [01] | 0 | 1 |
| 2 [10] | 1 | 0 |
| 3 [11] | 1 | 1 |

![输入图片说明](/imgs/protocol-spi/2023-12-29/RZr8O6hWyKNPeo7S.png)

![输入图片说明](/imgs/protocol-spi/2023-12-29/ryW9EZuJKW6VQKYh.png)

## Reference
- [# SPI协议详解（工作特点、时序图）](https://blog.csdn.net/weixin_46022434/article/details/105624672)
- [# SPI协议详解（图文并茂+超详细）](https://zhuanlan.zhihu.com/p/290620901)

## 待进一步总结
- [# SPI、 I2C、USART等协议共同点区别、使用场景](https://blog.csdn.net/qq_36226810/article/details/80311644)

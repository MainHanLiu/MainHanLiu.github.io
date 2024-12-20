---
title: "EEPROM / Flash"
description: 
date: 2023-08-08 10:00:00 +0800
categories: [Hardware, Flash]
tags: [Hardware, Flash, EEPROM]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## 存储器类型分类
![输入图片说明](/imgs/hardware-flash/2023-08-08/K02M2ASELYqxdTaO.png)

## Definition
- EEPROM的全称是“电可擦除可编程只读存储器  
- 狭义的EEPROM：特点是可以随机访问和修改任何一个字节，可以往每个bit中写入0或者1。这是最传统的一种EEPROM，掉电后数据不丢失，可以保存100年，可以擦写100w次。具有较高的可靠性，但是电路复杂/成本也高。因此目前的EEPROM都是几十KB到几百KB的，绝少有超过512K的。  
- flash属于广义的EEPROM，因为它也是电擦除的rom。但是为了区别于一般的按字节为单位的擦写的EEPROM，我们都叫它flash。  
flash做的改进就是擦除时不再以字节为单位，而是以块为单位，一次简化了电路，数据密度更高，降低了成本。上M的rom一般都是flash。  
flash分为nor flash和nand flash。nor flash数据线和地址线分开，可以实现ram一样的随机寻址功能，可以读取任何一个字节。但是擦除仍要按块来擦。nand flash同样是按块擦除，但是数据线和地址线复用，不能利用地址线随机寻址。读取只能按页来读取。（nandflash按块来擦除，按页来读，norflash没有页）  
由于nandflash引脚上复用，因此读取速度比nor flash慢一点，但是擦除和写入速度比nor flash快很多。nand flash内部电路更简单，因此数据密度大，体积小，成本也低。因此大容量的flash都是nand型的。小容量的2～12M的flash多是nor型的。  
使用寿命上，nand flash的擦除次数是nor的数倍。而且nand flash可以标记坏块，从而使软件跳过坏块。nor flash 一旦损坏便无法再用。  
因为nor flash可以进行字节寻址，所以程序可以在nor flash中运行。嵌入式系统多用一个小容量的nor flash存储引导代码，用一个大容量的nand flash存放文件系统和内核。  
通常，单片机里的Flash都用于存放运行代码，在运行过程中不能改；EEPROM是用来保存用户数据，运行过程中可以改变，比如一个时钟的闹铃时间初始化设定为12：00，后来在运行中改为6：00，这是保存在EEPROM里，不怕掉电，就算重新上电也不需要重新调整到6：00。  

Flash闪存本身是一种非易失性存储，在没有电流供应的条件下也能够长久地保持数据，其存储特性相当于硬盘，这项特性正是闪存得以成为各类便携型数字设备的存储介质的基础。Flash按照内部存储结构的不同，可以分为两种：Nor Flash和Nand Flash    
## Nor Flash和Nand Flash的区别
　　Nor Flash更像内存，有独立的地址线和数据线，但价格比较贵，容量比较小；而NAND型更像硬盘，地址线和数据线是共用的I/O线，类似硬盘的所有信息都通过一条硬盘线传送一般，而且NAND的成本较NOR来说很低，而容量却大很多。    
　　因此，NOR型闪存比较适合频繁随机读写的场合，通常用于存储程序代码并直接在闪存内运行，手机就是使用NOR型闪存的大户，所以手机的“内存”容量通常不大；NAND型闪存主要用来存储资料，我们常用的闪存产品，如闪存盘、数码存储卡都是用NAND型闪存。  
　　flash可以对称为块的存储器单元块进行擦写和再编程。任何flash器件的写入操作只能在空或已擦除的单元内进行，所以大多数情况下，在进行写入操作之前必须先执行擦除。NAND器件执行擦除操作是十分简单的，而NOR则要求在进行擦除前先要将目标块内所有的位都写为1。  
　　由于擦除NOR器件时是以64～128KB的块进行的，执行一个写入/擦除操作的时间为5s，与此相反，擦除NAND器件是以8～32KB的块进行的，执行相同的操作最多只需要4ms。  
**SPI NAND Flash 和SPI NOR Flash的区别**  
　　首先，SPI是指一种通信接口。那么严格的来说SPI Flash是一种使用SPI通信的Flash，即，可能指NOR也可能是NAND。但现在大部分情况默认下人们说的SPI Flash指的是SPI NOR Flash。  
## SPI Flash
SPI flash是一种片外扩展存储的方法。主机与flash芯片采用SPI(Serial Peripheral Interface 串行外设接口)总线进行通信。因为nor flash是数据串行通信，而nand flash是采用数据并行通信。所以SPI总线外接的flash无一例外是nor flash。  
SPI总线采用4线通信方式：串行时钟线(SPCK)、主机输入/从机输出数据线(MISO)、主机输出/从机输入数据线(MOSI)、从机选择线(NPCS)。SPI总线通信速度要比IIC总线通信速度快一些，因为它的时序更加简单。SPI直接通过片选线选择从机，而不是通过时序进行设备选址。因此SPI总线挂靠的外设不如IIC总线多。  


### SPI Flash 型号: w25q128
128Mbit = 16MB  
W25Q128将16M的容量分为256个块(Block),每个块大小为64K字节，每个块又分为16个扇区(Sector),每个扇区4K个字节,共4096个扇区。W25Q128的最小擦除单位为一个扇区，也就是每次必须擦除4K个字节。这样我们需要给W25Q128开辟一个至少4K的缓存区，这样对SRAM要求比较高，要求芯片必须有4K以上SRAM才能很好的操作。  
W25Q128的擦写周期多达10W次，具有20年的数据保存期限，支持电压为2.7~3.6V,W25Q128支持标准的SPI,还支持双输出/四输出的SPI,最大SPI时钟可以到80Mhz(双输出时相当于160Mhz,四输出时相当于320M)  

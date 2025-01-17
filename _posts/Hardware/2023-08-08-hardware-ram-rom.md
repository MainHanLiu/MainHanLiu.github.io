---
title: "RAM与ROM(EEPROM/Flash)"
description: 
date: 2023-08-08 10:00:00 +0800
categories: [Hardware, ROM]
tags: [Hardware, Flash, EEPROM]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## 存储器类型分类
![输入图片说明](/imgs/hardware-ram-rom/2023-08-08/K02M2ASELYqxdTaO.png)

RAM与ROM是计算机中常见的存储器类型，它们在数据存储和访问方面扮演着重要的角色。

RAM(Random Access Memory)是一种临时存储器，用于存储计算机正在运行的程序和数据。它具有快速的读写速度和随机访问的特点。

相比之下，ROM（Read-Only Memory）是一种只读存储器，用于存储固定的程序指令和数据。ROM中的数据在计算机断电时不会丢失，因此被称为非易失性存储器。

## RAM
RAM（Random Access Memory）：掉电丢失。

RAM是随机存储器，是与CPU直接交换数据的内部存储器，也叫主存(内存)，用于临时存储数据和程序。存储单元的内容可随机地存取和读取，而不需要按顺序查找，且存取的速度与存储单元的位置无关的存储器。

- DRAM（动态随机存取存储器）：  
    DRAM是计算机系统中最常见的RAM类型之一。  
    它使用电容和电流来存储数据，并且需要刷新操作以保持数据的稳定性。  
    DRAM容量较大，成本相对较低，因此广泛用于主内存（RAM）。  
    >动态RAM（Dynamic RAM），DRAM保留数据的时间很短，速度也比SRAM慢，不过它还是比任何的ROM都要快，但从价格上来说DRAM相比SRAM要便宜很多，计算机内存就是DRAM的。DRAM中每个存储单元由配对出现的晶体管和电容器构成，每隔一段时间，固定要对DRAM刷新充电一次，否则内部的数据即会消失。

- SRAM（静态随机存取存储器）：  
    SRAM是另一种常见的RAM类型，相对于DRAM来说，更快、更稳定。  
    它使用存储触发器（通常是互补金属氧化物半导体CMOS电路）来存储数据。  
    SRAM速度快，但比DRAM成本更高，一般用于高性能缓存和高速缓冲存储器。  
    >静态RAM（Static RAM），SRAM速度非常快，是目前读写最快的存储设备了，但是它也非常昂贵，所以只在要求很苛刻的地方使用，譬如CPU的一级缓冲，二级缓冲。SRAM采取多重晶体管设计，不需要刷新电路即能保存它内部存储的数据，特点为高性能、低集成度、速度快、体积较大。

- SDRAM（同步动态随机存取存储器）：  
    SDRAM是一种同步的DRAM类型，与系统主时钟同步工作。  
    它通过分时多通道的方式提供高速访问和数据传输。  
    SDRAM广泛用于计算机系统和其他需要高性能存储的设备。  

- DDR SDRAM（双倍数据率同步动态随机存取存储器）：  
    DDR SDRAM是SDRAM的一种进化形式，提供了更高的传输速率。  
    它通过在每个时钟周期内传送两次数据，实现更高的带宽和更快的数据访问速度。  
    DDR SDRAM用于大多数现代计算机系统中的主内存。  

## ROM
ROM（Read-Only Memory）：掉电保持。

ROM是只读存储器，其中的数据不能被常规方式修改或写入。它包含了固定的数据，例如启动程序、固件等。

ROM最初不能编程，出厂什么内容就永远什么内容，不灵活；后来出现了PROM，PROM是一次性的，可以自己写入一次，写入后就无法修改了，要是写错了，只能再换一片；后来又出现了可多次擦除写入的EPROM（可擦除可编程ROM），EPROM每次擦除程序都需要使用紫外线进行照射。另外一种EEPROM是通过电子擦除，价格很高，写入很慢。FLASH属于广义的EEPROM（也算ROM的一种），因为它也是电擦除的ROM，但是为了区别于一般的按字节为单位的擦写的EEPROM，我们都叫它FLASH。FLASH做的改进就是擦除时不再以字节为单位，而是以块为单位。

通常，单片机里的Flash都用于存放运行代码，在运行过程中不能改；EEPROM是用来保存用户数据，运行过程中可以改变，比如一个时钟的闹铃时间初始化设定为12：00，后来在运行中改为6：00，这是保存在EEPROM里，不怕掉电，就算重新上电也不需要重新调整到6：00。  

- MASK ROM（掩模只读存储器）  
    MASK ROM是在制造过程中被预先编程的ROM类型，无法在常规操作下进行修改。  
    它的数据是通过光刻等工艺制作在芯片上的，被“硬编码”进入芯片的电路结构中。  
    MASK ROM适用于存储固定的启动程序、固件等代码。  

- PROM（可编程只读存储器）  
    PROM是一种可编程的只读存储器，用户可以通过编程操作一次性地将数据写入其中。  
    编程操作通常由专用的编程设备或编程器完成，一旦写入数据后，数据将无法修改，要是写错了，只能再换一片。  
    PROM适用于需要存储不可更改数据的应用，如设备固件等。  

- EPROM（可擦除可编程只读存储器）  
    EPROM是一种可擦除的可编程ROM，允许用户多次编程和擦除操作。  
    EPROM使用紫外线擦除器件来擦除数据，然后通过编程器进行重新编程。  
    EPROM通常用于存储需要经常更新的程序和数据的应用。  

- EEPROM（电可擦除可编程只读存储器）
    EEPROM是一种电可擦除的可编程ROM，相对于EPROM，它不需要紫外线擦除器件。  
    EEPROM可以通过电压脉冲来擦除和编程数据，使得操作更加方便和可靠。  
    EEPROM广泛应用于存储配置数据、校准数据、设备序列号等的场景。  
    >狭义的EEPROM  
    特点是可以随机访问和修改任何一个字节，可以往每个bit中写入0或者1。这是最传统的一种EEPROM，掉电后数据不丢失，可以保存100年，可以擦写100w次。具有较高的可靠性，但是电路复杂/成本也高。因此目前的EEPROM都是几十KB到几百KB的，绝少有超过512K的。  

    >型号举例：  
    AT24C256C-SSHL-T  
    eeprom总容量 256Kbit = 32KB = 64B * 512page  
    Atmel AT24C256C, 256Kbit(32K * 8bit) Serial EEPROM: The 256K is   internally organized as 512 pages of 64-bytes each  

- Flash存储器  
    Flash存储器是一种基于EEPROM技术的存储器，具有非易失性和高密度的特点。  
    >FLASH属于广义的EEPROM（也算ROM的一种），因为它也是电擦除的ROM，但是为了区别于一般的按字节为单位的擦写的EEPROM，我们都叫它FLASH。FLASH做的改进就是擦除时不再以字节为单位，而是以块为单位，简化了电路，数据密度更高，降低了成本，相比EEPROM更具灵活性和可编程性。

    >Flash的编程原理是只能将1写为0，而不能将0写为1，所以在进行Flash编程前，必须将对应的块擦除，即将该块的每一位都变为1，块内所有字节变为0xFF。  

    Flash存储器广泛应用于各种存储需求，如固件存储、系统存储、可移动存储介质等。
    >上M的ROM一般都是Flash

## Flash
### Flash的种类
Flash闪存本身是一种非易失性存储，在没有电流供应的条件下也能够长久地保持数据，其存储特性相当于硬盘，这项特性正是闪存得以成为各类便携型数字设备的存储介质的基础。Flash按照内部存储结构的不同，可以分为两种：Nor Flash和Nand Flash。 

- NOR Flash  
    NOR Flash数据线和地址线分开，可以实现ram一样的随机寻址功能，可以读取任何一个字节，用户可以直接运行装载在NOR FLASH里面的代码，所以NOR Flash可以直接用来做boot，这样可以减少SRAM的容量，从而节约成本。但是擦除仍要按块来擦。  

- NAND Flash  
    NAND Flash同样是按块擦除，但是数据线和地址线复用，不能利用地址线随机寻址。读取只能按页来读取。（NAND Flash按块来擦除，按页来读，NOR Flash没有页）  
 
    NAND Flash没有采取内存的随机读取技术，它的读取是以一次读取一块的形式来进行的，通常是一次读取512个字节，采用这种技术的Flash比较廉价。  
    > 单片机基本都是NOR Flash，而手机我们说的64G 128G应该是NAND Flash。  

因为NOR Flash可以进行字节寻址，所以程序可以在NOR Flash中运行。用户不能直接运行NAND Flash上的代码，因此好多使用NAND Flash的开发板除了使用NAND Flah以外，还有一块小的NOR Flash来运行启动代码。  
>嵌入式系统多用一个小容量的NOR Flash存储引导代码，用一个大容量的NAND Flash存放文件系统和内核。  
 
### Nor Flash和Nand Flash的区别
　　Nor Flash更像内存，有独立的地址线和数据线，但价格比较贵，容量比较小；而NAND型更像硬盘，地址线和数据线是共用的I/O线，类似硬盘的所有信息都通过一条硬盘线传送一般，而且NAND的成本较NOR来说很低，而容量却大很多。    

　　因此，NOR型闪存比较适合频繁随机读写的场合，通常用于存储程序代码并直接在闪存内运行，手机就是使用NOR型闪存的大户，所以手机的“内存”容量通常不大；NAND型闪存主要用来存储资料，我们常用的闪存产品，如闪存盘、数码存储卡都是用NAND型闪存。  

　　Flash可以对称为块的存储器单元块进行擦写和再编程。任何Flash器件的写入操作只能在空或已擦除的单元内进行，所以大多数情况下，在进行写入操作之前必须先执行擦除。NAND器件执行擦除操作是十分简单的，而NOR则要求在进行擦除前先要将目标块内所有的位都写为1。  

　　由于擦除NOR器件时是以64～128KB的块进行的，执行一个写入/擦除操作的时间为5s，与此相反，擦除NAND器件是以8～32KB的块进行的，执行相同的操作最多只需要4ms。  
>由于NAND Flash引脚上复用，因此读取速度比NOR Flash慢一点，但是擦除和写入速度比NOR Flash快很多。

NAND Flash内部电路更简单，因此数据密度大，体积小，成本也低。因此大容量的Flash都是NAND型的。小容量的2～12M的Flash多是NOR型的。  

使用寿命上，NAND Flash的擦除次数是NOR的数倍。而且NAND Flash可以标记坏块，从而使软件跳过坏块。NOR Flash 一旦损坏便无法再用。  
 
### SPI Flash
**SPI NAND Flash 和SPI NOR Flash的区别**  
　　首先，SPI是指一种通信接口。那么严格的来说SPI Flash是一种使用SPI通信的Flash，即，可能指NOR也可能是NAND。但现在大部分情况默认下人们说的SPI Flash指的是SPI NOR Flash。 

SPI Flash是一种片外扩展存储的方法。主机与Flash芯片采用SPI(Serial Peripheral Interface 串行外设接口)总线进行通信。因为NOR Flash是数据串行通信，而NAND Flash是采用数据并行通信。所以SPI总线外接的Flash无一例外是NOR Flash。  

SPI总线采用4线通信方式：串行时钟线(SPCK)、主机输入/从机输出数据线(MISO)、主机输出/从机输入数据线(MOSI)、从机选择线(NPCS)。SPI总线通信速度要比IIC总线通信速度快一些，因为它的时序更加简单。SPI直接通过片选线选择从机，而不是通过时序进行设备选址。因此SPI总线挂靠的外设不如IIC总线多。  

## SPI Flash 型号
- W25Q16（SPI，2MB）

- W25Q64JVSSIQ  
W25Q64 将 **8M** 的容量分为 **128 个块（Block）**，每个块大小为 **64K 字节**，每个块又分为 **16个扇区（Sector）**，每个扇区 **4K 个字节**

- W25Q128（SPI，16MB）
>128Mbit = 16MB  
W25Q128将16M的容量分为256个块(Block),每个块大小为64K字节，每个块又分为16个扇区(Sector),每个扇区4K个字节,共4096个扇区。W25Q128的最小擦除单位为一个扇区，也就是每次必须擦除4K个字节。这样我们需要给W25Q128开辟一个至少4K的缓存区，这样对SRAM要求比较高，要求芯片必须有4K以上SRAM才能很好的操作。  
W25Q128的擦写周期多达10W次，具有20年的数据保存期限，支持电压为2.7~3.6V,W25Q128支持标准的SPI,还支持双输出/四输出的SPI,最大SPI时钟可以到80Mhz(双输出时相当于160Mhz,四输出时相当于320M)  

- W25Q256（SPI，32MB）  

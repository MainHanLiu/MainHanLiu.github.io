---
title: "ARM与DSP"
description: 
date: 2023-08-03 10:00:00 +0800
categories: [Module, ARM]
tags: [Module, ARM, DSP]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---
## DSP
DSP工作原理是接收模拟信号，转换为0或1的数字信号，再对数字信号进行修改、删除、强化，并在其他系统芯片中把数字数据解译回模拟数据或实际环境格式。它不仅具有可编程性，而且其实时运行速度可达每秒数以千万条复杂指令程序，源源超过通用微处理器，是数字化电子世界中日益重要的电脑芯片。它的强大数据处理能力和高运行速度，是最值得称道的两大特色。  

DSP芯片，由于它运算能力很强，速度很快，体积很小，而且采用软件编程具有高度的灵活性，因此为从事各种复杂的应用提供了一条有效途径。其主要应用是实时快速地实现各种数字信号处理算法。根据数字信号处理的要求，DSP芯片一般具有如下主要特点：  
（1）在一个指令周期内可完成一次乘法和一次加法；  
（2）程序和数据空间分开，可以同时访问指令和数据；  
（3）片内具有快速RAM，通常可通过独立的数据总线在两块中同时访问；  
（4）具有低开销或无开销循环及跳转的硬件支持；  
（5）快速的中断处理和硬件I/O支持；  
（6）具有在单周期内操作的多个硬件地址产生器；  
（7）可以并行执行多个操作；  
（8）支持流水线操作，使取指、译码和执行等操作可以重叠执行  
当然，与通用微处理器相比，DSP芯片的其他通用功能相对较弱些。  
ARM最大的优势在于速度快、低功耗、芯片集成度高，多数ARM芯片都可以算作SOC，基本上外围加上电源和驱动接口就可以做成一个小系统了。  

## ARM DSP 区别
总的来说主要区别有：  
ARM具有比较强的事务管理功能，可以用来跑界面以及应用程序等，其优势主要体现在控制方面，它的速度和数据处理能力一般，但是外围接口比较丰富，标准化和通用性做的很好，而且在功耗等方面做得也比较好，所以适合用在一些消费电子品方面；  
而DSP主要是用来计算的，比如进行加密解密、调制解调等，优势是强大的数据处理能力和较高的运行速度。由于其在控制算法等方面很擅长，所以适合用在对控制要求比较高的场合，比如军用导航、电机伺服驱动等方面。  
如果只是着眼于嵌入式应用的话，嵌入式CPU和DSP的区别应该只在于一个偏重控制一个偏重运算了。  

单片机为了存储器管理的方便（便于支持操作系统），一般采用指令、数据空间统一编码的冯·诺依曼结构。 DSP为了提高数据吞吐的速度，基本上都是指令、数据空间独立的哈佛结构。  

单片机对于数字计算方面的指令少得多，DSP为了进行快速的数字计算，提高常用的信号处理算法的效率，加入了很多指令，比如单周期乘加指令、逆序加减指令（FFT时特别有用，不是ARM的那种逆序），块重复指令（减少跳转延时）等等，甚至将很多常用的由几个操作组成的一个序列专门设计一个指令可以一周期完成（比如一指令作一个乘法，把结果累加，同时将操作数地址逆序加1），极大的提高了信号处理的速度。由于数字处理的读数、回写量非常大，为了提高速度，采用指令、数据空间分开的方式，以两条总线来分别访问两个空间，同时，一般在DSP内部有高速RAM，数据和程序要先加载到高速片内ram中才能运行。DSP为提高数字计算效率，牺牲了存储器管理的方便性，对多任务的支持要差的多，所以DSP不适合于作多任务控制作用。  
	
## 发展趋势：  
DSP是否将作为手机的心脏生存下去，目前的争论非常激烈。今天的手机生产采用的是双核方式：DSP芯片处理通信，如调制解调器功能和语音处理等；一块通用处理器（通常是ARM设计的RISC处理器）负责处理手机上运行的各种程序，如用户界面和控制协议堆栈等。随这两种处理器的功能日益强大，或许它们中的一方将会接管另一方目前执行的功能。但问题在于：是ARM取代DSP，还是DSP挤掉ARM？  
如果将这三者结合起来，即由DSP结合采样电路采集并处理信号，由ARM处理器作为平台，运行Linux操作系统，将经过DSP运算的结果发送给用户程序进行进一步处理，然后提供给图形化友好的人机交互环境完成数据分析和网络传输等功能，就会最大限度的发挥三者所长。  

## Reference
- [# 深入了解DSP和ARM的关系（相同与区别）](https://blog.csdn.net/godloveyuxu/article/details/80027501)  

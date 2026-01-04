---
title: "MDK的编译过程及文件类型全解"
description: 深入分析MDK编译过程，程序的组成、存储与运行，程序的地址分配，深入探究elf格式，hex文件，bin文件，axf文件，map文件
date: 2023-09-15 10:00:00 +0800
categories: [MDK]
tags: [MDK, Compile]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## 编译过程
了解编译及下载过程有助于理解芯片的工作原理，这些知识对制作IAP(bootloader)以及读写控制器内部FLASH的应用时非常重要。

![MDK编译过程](/imgs/mdk-compile/2023-09-15/y9cxrpTonQbIvujb.png)  
1. 编译，MDK软件使用的编译器是armcc和armasm，它们根据每个c/c++和汇编源文件编译成对应的以“.o”为后缀名的对象文件(Object Code，也称目标文件)，其内容主要是从源文件编译得到的机器码，包含了代码、数据以及调试使用的信息；
2. 链接，链接器armlink把各个.o文件及库文件链接成一个映像文件“.axf”或“.elf”；
3. 格式转换，一般来说Windows或Linux系统使用链接器直接生成可执行映像文件elf后，内核根据该文件的信息加载后， 就可以运行程序了，但在单片机平台上，需要把该文件的内容加载到芯片上，所以还需要对链接器生成的elf映像文件利用格式转换器fromelf转换成“.bin”或“.hex”文件，交给下载器下载到芯片的FLASH或ROM中。

### 具体工程中的编译过程说明  
![编译工程时的编译提示](/imgs/mdk-compile/2023-09-15/QgWULGSC7FWlE71K.png)  
构建工程的提示输出主要分6个部分，说明如下：
1. 提示信息的第一部分说明构建过程调用的编译器。图中的编译器名字是“V5.05(build 169)”，后面附带了该编译器所在的文件夹。在电脑上打开该路径，可看到该编译器包含各个编译工具，如armar、armasm、armcc、armlink及fromelf。
>armcc用于把c/c++文件编译成ARM指令代码，编译后会输出ELF格式的O文件(对象、目标文件)
> 
>armasm是汇编器，它把汇编文件编译成O文件
>  
>armlink是链接器，它把各个O文件链接组合在一起生成ELF格式的AXF文件，AXF文件是可执行的，下载器把该文件中的指令代码下载到芯片后，该芯片就能运行程序了；利用armlink还可以控制程序存储到指定的ROM或RAM地址。在MDK中可在“Option for Target->Linker”页面配置armlink选项。链接器默认是根据芯片类型的存储器分布来生成程序的，该存储器分布被记录在工程里的sct后缀的文件中，有特殊需要的话可自行编辑该文件，改变链接器的链接方式。
>   
>armar工具用于把.o打包成库文件lib。fromelf可根据axf文件生成hex、bin文件，hex和bin文件是大多数下载器支持的下载文件格式。在MDK中，针对armar和fromelf工具的选项几乎没有，仅集成了生成HEX或Lib的选项。如果我们想利用fromelf生成bin文件，可以在MDK的“Option for Target->User”页中添加调用fromelf的指令。
>
>在前面编译过程中，MDK调用了各种编译工具，平时我们直接配置MDK，不需要学习如何使用它们，但了解它们是非常有好处的。例如，若希望使用MDK编译生成bin文件的，需要在MDK中输入指令控制fromelf工具；在本章后面讲解AXF及O文件的时候，需要利用fromelf工具查看其文件信息，这都是无法直接通过MDK做到的。关于这些工具链的说明，在MDK的帮助手册《ARM Development Tools》都有详细讲解，点击MDK界面的“help->uVision Help”菜单可打开该文件。  
>![编译工具](/imgs/mdk-compile/2023-09-15/wiMXNOD4HjUOfJm2.png)  
2. 使用armasm编译汇编文件。图中列出了编译startup启动文件时的提示，编译后每个汇编源文件都对应有一个独立的.o文件。
3. 使用armcc编译c/c++文件。图中列出了工程中所有的c/c++文件的提示，同样地，编译后每个c/c++源文件都对应有一个独立的.o文件。
4. 使用armlink链接对象文件，根据程序的调用把各个.o文件的内容链接起来，最后生成程序的axf映像文件，并 附带程序各个域大小的说明，包括Code、RO-data、RW-data及ZI-data的大小。
5. 使用fromelf生成下载格式文件，它根据axf映像文件转化成hex文件，并列出编译过程出现的错误(Error)和警告(Warning)数量。  
6. 最后一段提示给出了整个构建过程消耗的时间。构建完成后，可在工程的“Output”及“Listing”目录下找到由以上过程生成的各种文件。可以看到，每个C源文件都对应生成了.o、.d及.crf后缀的文件，还有一些额外的.dep、.hex、.axf、.htm、.lnp、.sct、.lst及.map文件。  
![编译后Output及Listing文件夹中的内容](/imgs/mdk-compile/2023-09-15/Ltdjm5oWKGecqBLZ.png)

## 程序的组成、存储与运行
### CODE、RO、RW、ZI Data域及堆栈空间
在工程的编译提示输出信息中有一个语句`Program Size：Code=xx RO-data=xx RW-data=xx ZI-data=xx`，它说明了程序各个域的大小，编译后，应用程序中所有具有同一性质的数据(包括代码)被归到一个域，程序在存储或运行的时候，不同的域会呈现不同的状态，这些域的意义如下：
-   `Code`：即代码域，它指的是编译器生成的机器指令，这些内容被存储到ROM区。
-   `RO-data`：Read Only data，即只读数据域，它指程序中用到的只读数据，这些数据被存储在ROM区，因而程序不能修改其内容。例如C语言中const关键字定义的变量就是典型的RO-data。
-   `RW-data`：Read Write data，即可读写数据域，它指初始化为“非0值”的可读写数据，程序刚运行时，这些数据具有非0的初始值，且运行的时候它们会常驻在RAM区，因而应用程序可以修改其内容。例如C语言中使用定义的全局变量，且定义时赋予“非0值”给该变量进行初始化。
-   `ZI-data`：Zero Initialie data，即0初始化数据，它指初始化为“0值”的可读写数据域，它与RW-data的区别是程序刚运行时这些数据初始值全都为0，而后续运行过程与RW-data的性质一样，它们也常驻在RAM区，因而应用程序可以更改其内容。例如C语言中使用定义的全局变量，且定义时赋予“0值”给该变量进行初始化(若定义该变量时没有赋予初始值，编译器会把它当ZI-data来对待，初始化为0)；
-   ZI-data的栈空间(Stack)及堆空间(Heap)：在C语言中，函数内部定义的局部变量属于栈空间，进入函数的时候从向栈空间申请内存给局部变量，退出时释放局部变量，归还内存空间。而使用malloc动态分配的变量属于堆空间。在程序中的栈空间和堆空间都是属于ZI-data区域的，这些空间都会被初始值化为0值。编译器给出的ZI-data占用的空间值中包含了堆栈的大小(经实际测试，若程序中完全没有使用malloc动态申请堆空间，编译器会优化，不把堆空间计算在内)。

> 综上所述，以程序的组成构件为例，它们所属的区域类别如下  
> ![输入图片说明](/imgs/mdk-compile/2023-09-15/650Vlrq7Of23Mc0W.png)  

### 程序的存储与运行
`RW-data`和`ZI-data`它们仅仅是初始值不一样而已，为什么编译器非要把它们区分开？这就涉及到程序的存储状态了，应用程序具有静止状态和运行状态。静止态的程序被存储在非易失存储器中，如STM32的内部FLASH，因而系统掉电后也能正常保存。但是当程序在运行状态的时候，程序常常需要修改一些暂存数据，由于运行速度的要求，这些数据往往存放在内存中(RAM)，掉电后这些数据会丢失。因此，程序在静止与运行的时候它在存储器中的表现是不一样的。  
![应用程序的加载视图与执行视图](/imgs/mdk-compile/2023-09-15/tKqcvwUeXhLs5ZOq.png)  

图中的左侧是应用程序的存储状态，右侧是运行状态，而上方是RAM存储器区域，下方是ROM存储器区域。

程序在存储状态时，RO节(RO section)及RW节都被保存在ROM区。当程序开始运行时，内核直接从ROM中读取代码，并且在执行主体代码前，会先执行一段加载代码，它把RW节数据从ROM复制到RAM， 并且在RAM加入ZI节，ZI节的数据都被初始化为0。加载完后RAM区准备完毕，正式开始执行主体程序。

编译生成的RW-data的数据属于图中的RW节，ZI-data的数据属于图中的ZI节。是否需要掉电保存，这就是把RW-data与ZI-data区别开来的原因，因为在RAM创建数据的时候，默认值为0，但如果有的数据要求初值非0，那就需要使用ROM记录该初始值，运行时再复制到RAM。

STM32的RO区域不需要加载到SRAM，内核直接从FLASH读取指令运行。计算机系统的应用程序运行过程很类似，不过计算机系统的程序在存储状态时位于硬盘，执行的时候甚至会把上述的RO区域(代码、只读数据)加载到内存，加快运行速度，还有虚拟内存管理单元(MMU)辅助加载数据，使得可以运行比物理内存还大的应用程序。而STM32没有MMU，所以无法支持Linux和Windows系统。

当程序存储到STM32芯片的内部FLASH时(即ROM区)，它占用的空间是Code、RO-data及RW-data的总和，所以如果这些内容比STM32芯片的FLASH空间大，程序就无法被正常保存了。当程序在执行的时候，需要占用内部SRAM空间(即RAM区)，占用的空间包括RW-data和ZI-data。应用程序在各个状态时各区域的组成如下。  
![输入图片说明](/imgs/mdk-compile/2023-09-15/Hpg0YhzGtTKxQJDL.png)  
在MDK中，我们建立的工程一般会选择芯片型号，选择后就有确定的FLASH及SRAM大小，若代码超出了芯片的存储器的极限，编译器会提示错误，这时就需要裁剪程序了，裁剪时可针对超出的区域来优化。

## MDK工程的文件类型
- uvprojx文件就是我们平时双击打开的工程文件，它记录了整个工程的结构，如芯片类型、工程包含了哪些源文件等内容
- uvoptx文件记录了工程的配置选项，如下载器的类型、变量跟踪配置、断点位置以及当前已打开的文件等等
- uvguix文件记录了MDK软件的GUI布局，如代码编辑区窗口的大小、编译输出提示窗口的位置等等。
> 根据这几个文件的记录类型，可以知道uvprojx文件是最重要的，删掉它我们就无法再正常打开工程了，而uvoptx及uvguix文件并不是必须的，可以删除，重新使用MDK打开uvprojx工程文件后，会以默认参数重新创建uvoptx及uvguix文件。(所以当使用Git/SVN等代码管理的时候，往往只保留uvprojx文件) 
- MDK支持c、cpp、h、s、inc类型的源代码文件，其中c、cpp分别是c/c++语言的源代码，h是它们的头文件，s是汇编文件，inc是汇编文件的头文件，可使用“$include”语法包含。编译器根据工程中的源文件最终生成机器码。
- .lib 库文件。工程中生成可执行文件或库文件只能二选一，默认编译是生成可执行文件的，可执行文件即我们下载到芯片上直接运行的机器码。得到生成的`*.lib`文件后，可把它像C文件一样添加到其它工程中，并在该工程调用lib提供的函数接口，除了不能看到`*.lib`文件的源码，在应用方面它跟C源文件没有区别。
- .dep和.d文件(Dependency file)记录的是工程或其它文件的依赖，主要记录了引用的头文件路径，其中.dep是整个工程的依赖，它以工程名命名，而.d是单个源文件的依赖， 它们以对应的源文件名命名。
- `*.crf`是交叉引用文件(Cross-Reference file)，它主要包含了浏览信息(browse information)，即源代码中的宏定义、变量及函数的定义和声明的位置。
- .map，链接器生成的列表文件，包含存储器映像分布
- .o、.elf、.axf、.bin及.hex文件都存储了编译器根据源代码生成的机器码，根据应用场合的不同，它们又有所区别。
- .bin，二进制格式的映像文件，是纯粹的FLASH映像，不含任何额外信息  
- .hex，Intel Hex格式的映像文件，可理解为带存储地址描述格式的bin文件

###  目标文件
`*.o`、`_.elf`、`_.axf`以及前面提到的lib文件都是属于目标文件，它们都是使用ELF格式来存储的。

目标文件主要有如下三种类型：
1.  可重定位的文件(Relocatable File)，包含基础代码和数据，但它的代码及数据都没有指定绝对地址，因此它适合于与其他目标文件链接来创建可执行文件或者共享目标文件。 这种文件一般由编译器根据源代码生成。
> 例如MDK的armcc和armasm生成的\*.o文件就是这一类，另外还有Linux的\*.o 文件，Windows的 \*.obj文件。
2.  可执行文件(Executable File) ，它包含适合于执行的程序，它内部组织的代码数据都有固定的地址(或相对于基地址的偏移)，系统可根据这些地址信息把程序加载到内存执行。这种文件一般由链接器根据可重定位文件链接而成，它主要是组织各个可重定位文件，给它们的代码及数据一一打上地址标号，固定其在程序内部的位置，链接后，程序内部各种代码及数据段不可再重定位(即不能再参与链接器的链接)。
> 例如MDK的armlink生成的\*.elf及\*.axf文件，(使用gcc编译工具可生成\*.elf文件，用armlink生成的是\*.axf文件，_.axf文件在_.elf之外，增加了调试使用的信息，其余区别不大，后面我们仅讲解\*.axf文件)，另外还有Linux的/bin/bash文件，Windows的\*.exe文件。
3.  共享目标文件(Shared Object File)， 它的定义比较难理解，我们直接举例，MDK生成的\*.lib文件就属于共享目标文件，它可以继续参与链接，加入到可执行文件之中。另外，Linux的.so类型文件，如/lib/glibc-2.5.so，Windows的DLL都属于这一类。

.axf文件是由多个.o文件链接而成的，而\*.o文件由相应的源文件编译而成，一个源文件对应一个\*.o文件。  

总的来说，链接器把各个\*.o文件的节区归类、排列，根据目标器件的情况编排地址生成输出，汇总到\*.axf文件。

如图为具体的链接过程示例，“多彩流水灯”工程中在`“bsp_led.c”`文件中有一个`LED_GPIO_Config`函数，而它内部调用了`“stm32f4xx_hal_gpio.c”`的`GPIO_Init`函数，经过armcc编译后，`LED_GPIO_Config`及`GPIO_Iint`函数都成了指令代码，分别存储在`bsp_led.o`及`stm32f4xx_hal_gpio.o`文件中，这些指令在\*.o文件都没有指定地址，仅包含了内容、大小以及调用的链接信息，而经过链接器后，链接器给它们都分配了特定的地址，并且把地址根据调用指向链接起来。

![具体的链接过程](/imgs/mdk-compile/2023-09-15/5mxeDZH5GZbZ8N20.png)  

### 分散加载代码
学习至此，还有一个疑问，前面提到程序有存储态及运行态，它们之间应有一个转化过程，把存储在FLASH中的RW-data数据拷贝至SRAM。然而我们的工程中并没有编写这样的代码，在汇编文件中也查不到该过程，芯片是如何知道FLASH的哪些数据应拷贝到SRAM的哪些区域呢？

程序中具有一段名为“__scatterload”的分散加载代码，它是由armlink链接器自动生成的。分散加载代码包含了拷贝过程(主要使用LDM复制指令)，而LDM指令的操作数中包含了加载的源地址，这些地址中包含了内部FLASH存储的RW-data数据，执行这些指令后数据就会从FLASH地址加载到内部SRAM的地址。而 “__scatterload ”的代码会被“__main”函数调用，见 代码清单40_8，__main在启动文件中的“Reset_Handler”会被调用，因而，在主体程序执行前，已经完成了分散加载过程。

### hex文件及bin文件
若编译过程无误，即可把工程生成前面对应的\*.axf文件，而在MDK中使用下载器(DAP/JLINK/ULINK等)下载程序或仿真的时候，MDK调用的就是\*.axf文件，它解释该文件，然后控制下载器把\*.axf中的代码内容下载到STM32芯片对应的存储空间，然后复位后芯片就开始执行代码了。

然而，脱离了MDK或IAR等工具，下载器就无法直接使用\*.axf文件下载代码了，它们一般仅支持hex和bin格式的代码数据文件。默认情况下MDK都不会生成hex及bin文件，需要配置工程选项或使用fromelf命令。
>生成hex文件的配置比较简单，在“Options for Target->Output->Create Hex File”中勾选该选项，然后编译工程即可
>
>使用MDK生成bin文件需要使用fromelf命令，在MDK的“Options For Target->Users”中加入个性化的命令，如`“fromelf --bin --output ..\..\Output\流水灯.bin ..\..\Output\流水灯.axf”`，该指令是根据本机及工程的配置而写的，在不同的系统环境或不同的工程中，指令内容都不一样，我们需要理解它， 才能为自己的工程定制指令。在MDK命令输入栏中，我们把fromelf指令放置在“After Build/Rebuild”(工程构建完成后执行)一栏也是基于这个考虑，这样设置后，工程构建完成生成了最新的\*.axf文件，MDK再执行fromelf指令，从而得到最新的bin文件。
>
>![ fromelf命令格式分解](/imgs/mdk-compile/2023-09-15/zZpZxpH3WL137umK.png)

#### hex文件
hex是Intel公司制定的一种使用ASCII文本记录机器码或常量数据的文件格式，这种文件常常用来记录将要存储到ROM中的数据，绝大多数下载器支持该格式。一个hex文件由多条记录组成，而每条记录由五个部分组成， 格式形如`:llaaaatt[dd…]cc`  
>记录的各个部分介绍如下：
-   “:” ：每条记录的开头都使用冒号来表示一条记录的开始；
-   ll ：以16进制数表示这条记录的主体数据区的长度(即后面[dd…]的长度)；
-   aaaa:表示这条记录中的内容应存放到FLASH中的起始地址；
-   tt：表示这条记录的类型，它包含中的各种类型；
>![输入图片说明](/imgs/mdk-compile/2023-09-15/6YUtPtDib74sr8RD.png)
-   dd ：表示一个字节的数据，一条记录中可以有多个字节数据，ll区表示了它有多少个字节的数据；
-   cc ：表示本条记录的校验和，它是前面所有16进制数据 (除冒号外，两个为一组)的和对256取模运算的结果的补码。

>![输入图片说明](/imgs/mdk-compile/2023-09-15/GK48sGF5R1rDMcfV.png)  
>上图的Hex文件总共6行的内容。每行都是以':'开始，之后是数据域长度、地址域、记录类型、数据域和校验和。  
>最后一行的数据类型是0x01,代表文件结束了。	

```
一个常见hex文件（注：实际数据之间无空格）：
:02 0000 04 0800 F2
:10 0000 00 00040020B1010008FD020008BD020008 44
:10 0010 00 F9020008010200083504000800000000 91
:10 0020 00 00000000000000000000000021030008 A4
···省略数行
:10 0470 00 00000240840400080000002000040000 86
:04 0480 00 48040008 24
:04 0000 05 08000199 55
:00 0000 01 FF
```

```
:02 0000 04 0800 F2
```
>04类型：扩展线性地址
>- 02：表示这条记录数据区的长度为2字节；
>- 0000：表示这条记录要存储到的地址；
>- 04：表示这是一条扩展线性地址记录，数据区的是扩展线性地址；
>- 0800：由于这是一条扩展线性地址记录，所以这部分表示地址的高16位，与前面的“0000”结合在一起，表示要扩展的线性地址为“0x0800 0000”，这正好是STM32内部FLASH的首地址；
>- F2：表示校验和，它的值为(0x02+0x00+0x00+0x04+0x08+0x00)%256的值再取补码。

```
:10 0000 00 000400209D010008DB0200087F020008 B8
```
> 00类型：数据行
>-  10：表示这条记录数据区的长度为16字节；
>-  0000：偏移地址，与前面的扩展线性记录结合，表示这条记录要存储的FLASH首地址为(0x0800 0000+0x0000)；数据第一行偏移0000地址，第二行就是偏移0010，第二行就是偏移0020，依次偏移到FFF0；如果偏移到FFF0，则会重新下一个起始地址，一段程序你就明白了：
>-  00：表示这是一条数据记录，数据区的是程序数据；
>-  000400204501000829030008BF020008：这是要按地址存储的数据；
>-  B8:校验和

```
:02 0000 04 0800 F2
...省略
:10 FFD0 00 D0C5CFA20D0A00003052010810B50A48 62
:10 FFE0 00 02F0FEFC09A002F0FBFC14A002F0F8FC F9
:10 FFF0 00 1EA221A123A002F0F3FC2CA002F0F0FC 31
:02 0000 04 0801 F1
:10 0000 00 394802F0EDFC10BD3C5301080D0A2A20 CE
:10 0010 00 20202020202020202020202020202020 E0
:10 0020 00 2020202020202020202020414756D6C7 F5
```
>**从0x 0800 0000地址 到 0x 0801 0000的转变，数据记录每行有16字节**  
>:02 **0000** **04** **0800** F2  
> /* 省略... */  
>:10 **FFF0** **00** 1EA221A123A002F0F3FC2CA002F0F0FC 31  
>:02 **0000** **04** **0801** F1  
>:10 **0000** **00** 394802F0EDFC10BD3C5301080D0A2A20 CE  

bin、hex及axf文件都包含了指令代码，但它们的信息丰富程度是不一样的。
-   bin文件是最直接的代码映像，它记录的内容就是要存储到FLASH的二进制数据(机器码本质上就是二进制数据)， 在FLASH中是什么形式它就是什么形式，没有任何辅助信息，包括大小端格式也没有，因此下载器需要有针对芯片FLASH平台的辅助文件才能正常下载(一般下载器程序会有匹配的这些信息)；烧写BIN文件的时候，用户是一定需要指定地址信息的。
-   hex文件是一种使用十六进制符号表示的代码记录，记录了代码应该存储到FLASH的哪个地址，下载器可以根据这些信息辅助下载；在烧写或下载HEX文件的时候，一般都不需要用户指定地址，因为HEX文件内部的信息已经包括了地址。
-   axf文件在前文已经解释，它不仅包含代码数据，还包含了工程的各种信息，因此它也是三个文件中最大的。

>hex文件的两个优点：
>1. 使用ASCII文本保存固件信息，方便查看一些固件内容；  
>2. 通过文件每行的校验和与最后一行的文件结束标志，在文件的传输与保存过程中能够发现固件是否完整。

Hex文件有更好的可读性，最重要的是hex文件能够保证固件在保存与传输时的完整性。因此hex文件更适用于保存与传输。而Bin文件是纯二进制文件，内部只包含程序编译后的机器码和变量数据。当文件损坏时，我们也无法知道文件已损坏。不过Bin文件作为固件的最终形式，在使用串口下载程序或者远程升级时，是不可替代的。

经验丰富的人是有可能从bin或hex文件中恢复出汇编代码的，只是成本较高，但不是不可能。如果芯片没有做任何加密措施，使用下载器可以直接从芯片读回它存储在FLASH中的数据，从而得到bin映像文件，根据芯片型号还原出部分代码即可进行修改，甚至不用修改代码，直接根据目标产品的硬件PCB，抄出一样的板子，再把bin映像下载芯片，直接山寨出目标产品，所以在实际的生产中，一定要注意做好加密措施。由于axf文件中含有大量的信息，且直接使用fromelf即可反汇编代码，所以更不要随便泄露axf文件。lib文件也能反使用fromelf文件反汇编代码，不过它不能还原出C代码，由于lib文件的主要目的是为了保护C源代码，也算是达到了它的要求。

### map文件
#### 第一部分——节区的跨文件引用(Section Cross References)
```c
=============================================================
Section Cross References

   startup_stm32f40xx.o(RESET) refers to startup_stm32f40xx.o(STACK) for __initial_sp
   startup_stm32f40xx.o(RESET) refers to startup_stm32f40xx.o(.text) for Reset_Handler
   startup_stm32f40xx.o(RESET) refers to stm32f4xx_it.o(i.NMI_Handler) for NMI_Handler
   startup_stm32f40xx.o(RESET) refers to stm32f4xx_it.o(i.HardFault_Handler) for HardFault_Handler
   /**...以下部分省略****/
   main.o(i.main) refers to bsp_led.o(i.LED_GPIO_Config) for LED_GPIO_Config
   main.o(i.main) refers to stm32f4xx_gpio.o(i.GPIO_ResetBits) for GPIO_ResetBits
   main.o(i.main) refers to main.o(i.Delay) for Delay
   main.o(i.main) refers to stm32f4xx_gpio.o(i.GPIO_SetBits) for GPIO_SetBits
   bsp_led.o(i.LED_GPIO_Config) refers to stm32f4xx_rcc.o(i.RCC_AHB1PeriphClockCmd) for RCC_AHB1PeriphClockCmd
   bsp_led.o(i.LED_GPIO_Config) refers to stm32f4xx_gpio.o(i.GPIO_Init) for GPIO_Init
   /**...以下部分省略****/
=============================================================
```
在这部分中，详细列出了各个\*.o文件之间的符号引用。由于\*.o文件是由asm或c/c++源文件编译后生成的，各个文件及文件内的节区间互相独立，链接器根据它们之间的互相引用链接起来，链接的详细信息在这个“Section Cross References”一一列出。

例如，开头部分说明的是startup_stm32f10x.o文件中的“RESET”节区分为它使用的“__initial_sp” 符号引用了同文件“STACK”节区。

也许我们对启动文件不熟悉，不清楚这究竟是什么，那我们继续浏览，可看到main.o文件的引用说明，如说明main.o文件的i.main节区为它使用的LED_GPIO_Config符号引用了bsp_led.o文件的i.LED_GPIO_Config节区。

同样地，下面还有bsp_led.o文件的引用说明，如说明了bsp_led.o文件的i.LED_GPIO_Config节区为它使用的GPIO_Init符号引用了stm32f10x_gpio.o文件的i.GPIO_Init节区。

可以了解到，这些跨文件引用的符号其实就是源文件中的函数名、变量名。
#### 第二部分——删除无用节区的说明(Removing Unused input sections from the image)
```c
==============================================================================

Removing Unused input sections from the image.

Removing startup_stm32f40xx.o(HEAP), (512 bytes).
Removing system_stm32f4xx.o(.rev16_text), (4 bytes).
Removing system_stm32f4xx.o(.revsh_text), (4 bytes).
Removing system_stm32f4xx.o(.rrx_text), (6 bytes).
Removing system_stm32f4xx.o(i.SystemCoreClockUpdate), (136 bytes).
Removing system_stm32f4xx.o(.data), (20 bytes).
Removing misc.o(.rev16_text), (4 bytes).
Removing misc.o(.revsh_text), (4 bytes).
Removing misc.o(.rrx_text), (6 bytes).
Removing misc.o(i.NVIC_Init), (104 bytes).
Removing misc.o(i.NVIC_PriorityGroupConfig), (20 bytes).
Removing misc.o(i.NVIC_SetVectorTable), (20 bytes).
Removing misc.o(i.NVIC_SystemLPConfig), (28 bytes).
Removing misc.o(i.SysTick_CLKSourceConfig), (28 bytes).
Removing stm32f4xx_adc.o(.rev16_text), (4 bytes).
Removing stm32f4xx_adc.o(.revsh_text), (4 bytes).
Removing stm32f4xx_adc.o(.rrx_text), (6 bytes).
Removing stm32f4xx_adc.o(i.ADC_AnalogWatchdogCmd), (16 bytes).
Removing stm32f4xx_adc.o(i.ADC_AnalogWatchdogSingleChannelConfig), (12 bytes).
Removing stm32f4xx_adc.o(i.ADC_AnalogWatchdogThresholdsConfig), (6 bytes).
Removing stm32f4xx_adc.o(i.ADC_AutoInjectedConvCmd), (24 bytes).
Removing stm32f4xx_adc.o(i.ADC_ClearFlag), (6 bytes).
Removing stm32f4xx_adc.o(i.ADC_ClearITPendingBit), (8 bytes).
Removing stm32f4xx_adc.o(i.ADC_Cmd), (24 bytes).
/**...以下部分省略****/
=========================================================================
```
这部分列出了在链接过程它发现工程中未被引用的节区，这些未被引用的节区将会被删除(指不加入到`*.axf`文件，不是指在`*.o`文件删除)，这样可以防止这些无用数据占用程序空间。

例如，上面的信息中说明startup_stm32f407xx.o中的HEAP(在启动文件中定义的用于动态分配的“堆” 区)以及 stm32f4xx_adc.o的各个节区都被删除了， 因为在我们这个工程中没有使用动态内存分配，也没有引用任何stm32f4xx_adc.c中的内容。 由此也可以知道，虽然我们把STM32HAL库的各个外设对应的c库文件都添加到了工程， 但不必担心这会使工程变得臃肿，因为未被引用的节区内容不会被加入到最终的机器码文件中。

#### 第三部分——符号映像表(Image Symbol Table)
```c
=============================================================

Image Symbol Table

Local Symbols

Symbol Name                              Value     Ov Type        Size  Object(Section)

../clib/microlib/init/entry.s            0x00000000   Number         0  entry8a.o ABSOLUTE
../clib/microlib/init/entry.s            0x00000000   Number         0  entry7b.o ABSOLUTE
../clib/microlib/init/entry.s            0x00000000   Number         0  entry7a.o ABSOLUTE
/*...省略部分*/
LED_GPIO_Config                          0x08000281   Thumb Code    84  bsp_led.o(i.LED_GPIO_Config)
MemManage_Handler                        0x080002d9   Thumb Code     2  stm32f4xx_it.o(i.MemManage_Handler)
NMI_Handler                              0x080002db   Thumb Code     2  stm32f4xx_it.o(i.NMI_Handler)
PendSV_Handler                           0x080002dd   Thumb Code     2  stm32f4xx_it.o(i.PendSV_Handler)
RCC_AHB1PeriphClockCmd                   0x080002e1   Thumb Code    22  stm32f4xx_rcc.o(i.RCC_AHB1PeriphClockCmd)
SVC_Handler                              0x080002fd   Thumb Code     2  stm32f4xx_it.o(i.SVC_Handler)
SysTick_Handler                          0x080003b9   Thumb Code     2  stm32f4xx_it.o(i.SysTick_Handler)
SystemInit                               0x080003bd   Thumb Code    74  system_stm32f4xx.o(i.SystemInit)
UsageFault_Handler                       0x08000419   Thumb Code     2  stm32f4xx_it.o(i.UsageFault_Handler)
__scatterload_copy                       0x0800041b   Thumb Code    14  handlers.o(i.__scatterload_copy)
__scatterload_null                       0x08000429   Thumb Code     2  handlers.o(i.__scatterload_null)
__scatterload_zeroinit                   0x0800042b   Thumb Code    14  handlers.o(i.__scatterload_zeroinit)
main                                     0x08000439   Thumb Code   226  main.o(i.main)
Region$$Table$$Base                      0x08000520   Number         0  anon$$obj.o(Region$$Table)
Region$$Table$$Limit                     0x08000530   Number         0  anon$$obj.o(Region$$Table)
__initial_sp                             0x20000400   Data           0  startup_stm32f40xx.o(STACK)

/*...省略部分*/

=============================================================
```
这个表列出了被引用的各个符号在存储器中的具体地址、占据的空间大小等信息。如我们可以查到LED_GPIO_Config符号存储在0x08000281地址，它属于Thumb Code类型，大小为84字节，它所在的节区为bsp_led.o文件的i.LED_GPIO_Config节区。

#### 第四部分——存储器映像索引(Memory Map of the image)
```c
=============================================================

Memory Map of the image

Image Entry point : 0x08000189

Load Region LR_IROM1 (Base: 0x08000000, Size: 0x00000530, Max: 0x00100000, ABSOLUTE)

Execution Region ER_IROM1 (Base: 0x08000000, Size: 0x00000530, Max: 0x00100000, ABSOLUTE)

Base Addr    Size         Type   Attr      Idx    E Section Name        Object

0x08000000   0x00000188   Data   RO            3    RESET               startup_stm32f40xx.o
/*..省略部分*/
0x080001e8   0x00000012   Code   RO         4765    i.Delay             main.o
0x080001fa   0x0000007c   Code   RO         1857    i.GPIO_Init         stm32f4xx_gpio.o
0x08000276   0x00000004   Code   RO         1864    i.GPIO_ResetBits    stm32f4xx_gpio.o
0x0800027a   0x00000004   Code   RO         1865    i.GPIO_SetBits      stm32f4xx_gpio.o
0x0800027e   0x00000002   Code   RO         4800    i.HardFault_Handler  stm32f4xx_it.o
0x08000280   0x00000058   Code   RO         4873    i.LED_GPIO_Config   bsp_led.o
0x080002d8   0x00000002   Code   RO         4801    i.MemManage_Handler  stm32f4xx_it.o
/*..省略部分*/
0x08000438   0x000000e8   Code   RO         4766    i.main              main.o
0x08000520   0x00000010   Data   RO         4913    Region$$Table       anon$$obj.o

Execution Region RW_IRAM1 (Base: 0x20000000, Size: 0x00000400, Max: 0x00020000, ABSOLUTE)

Base Addr    Size         Type   Attr      Idx    E Section Name        Object

0x20000000   0x00000400   Zero   RW            1    STACK               startup_stm32f40xx.o

=============================================================
```
本工程的存储器映像索引分为ER_IROM1及RW_IRAM1部分，它们分别对应STM32内部FLASH及SRAM的空间。相对于符号映像表，这个索引表描述的单位是节区，而且它描述的主要信息中包含了节区的类型及属性，由此可以区分Code、RO-data、RW-data及ZI-data。

例如，从上面的表中我们可以看到i.LED_GPIO_Config节区存储在内部FLASH的0x08000280地址，大小为0x00000058，类型为Code，属性为RO。而程序的STACK节区(栈空间)存储在SRAM的0x20000000地址，大小为0x00000400，类型为Zero，属性为RW（即RW-data）。

#### 第五部分——映像组件大小(Image component sizes)
映像组件大小是最常查询的内容
```c
==============================================================================

Image component sizes

Code (inc. data)   RO Data    RW Data    ZI Data      Debug   Object Name

88          4          0          0          0        702   bsp_led.o
250          6          0          0          0       1787   main.o
36          8        392          0       1024       1048   startup_stm32f40xx.o
132          0          0          0          0       2940   stm32f4xx_gpio.o
18          0          0          0          0       5198   stm32f4xx_it.o
28          6          0          0          0        785   stm32f4xx_rcc.o
276         34          0          0          0     250921   system_stm32f4xx.o

----------------------------------------------------------------------
834         58        408          0       1024     263381   Object Totals
0          0         16          0          0          0   (incl. Generated)
6          0          0          0          0          0   (incl. Padding)

/*...省略部分*/
==============================================================================

Code (inc. data)   RO Data    RW Data    ZI Data      Debug

920         74        408          0       1024     262453   Grand Totals
920         74        408          0       1024     262453   ELF Image Totals
920         74        408          0          0          0   ROM Totals

==============================================================================

Total RO  Size (Code + RO Data)                 1328 (   1.30kB)
Total RW  Size (RW Data + ZI Data)              1024 (   1.00kB)
Total ROM Size (Code + RO Data + RW Data)       1328 (   1.30kB)

==============================================================================
```
这部分包含了各个使用到的\*.o文件的空间汇总信息、整个工程的空间汇总信息以及占用不同类型存储器的空间汇总信息，它们分类描述了具体占据的Code、RO-data、RW-data及ZI-data的大小，并根据这些大小统计出占据的ROM总空间。

我们仅分析最后两部分信息，如Grand Totals一项，它表示整个代码占据的所有空间信息， 其中Code类型的数据大小为920字节，这部分包含了74字节的指令数据(inc .data)已算在内， 另外RO-data占408字节，RW-data占0字节，ZI-data占1024字节。 在它的下面两行有一项ROM Totals信息，它列出了各个段所占据的ROM空间， 除了ZI-data不占ROM空间外，其余项都与Grand Totals中相等(RW-data也占据ROM空间， 只是本工程中没有RW-data类型的数据而已)。

最后一部分列出了只读数据(RO)、可读写数据(RW)及占据的ROM大小。 其中只读数据大小为1328字节， 它包含Code段及RO-data段; 可读写数据大小为1024字节， 它包含RW-data及ZI-data段；占据的ROM大小为1328字节， 它除了Code段和RO-data段，还包含了运行时需要从ROM加载到RAM的RW-data数据。

综合整个map文件的信息，可以分析出，当程序下载到STM32的内部FLASH时， 需要使用的内部FLASH是从0x0800 0000地址开始的大小为1328字节的空间； 当程序运行时，需要使用的内部SRAM是从0x20000000地址开始的大小为1024字节的空间。

粗略一看，发现这个小程序竟然需要1024字节的SRAM，实在说不过去，但仔细分析map文件后，可了解到这1024字节都是STACK节区的空间(即栈空间)，栈空间大小是在启动文件中定义的，这1024字节是默认值(0x00000400)。它是提供给C语言程序局部变量申请使用的空间，若我们确认自己的应用程序不需要这么大的栈，完全可以修改启动文件，把它改小一点，查看htm静态调用图文件可了解静态的栈调用情况，可以用它作为参考。

###  sct分散加载文件
当工程按默认配置构建时，MDK会根据我们选择的芯片型号，获知芯片的内部FLASH及内部SRAM存储器概况，生成一个以工程名命名的后缀为\*.sct的分散加载文件(Linker Control File，scatter loading)，链接器根据该文件的配置分配各个节区地址，生成分散加载代码，因此我们通过修改该文件可以定制具体节区的存储位置。

例如可以设置源文件中定义的所有变量自动按地址分配到外部SRAM，这样就不需要再使用关键字`__attribute__`按具体地址来指定了；利用它还可以控制代码的加载区与执行区的位置，例如可以把程序代码存储到单位容量价格便宜的NAND-FLASH中，但在NAND-FLASH中的代码是不能像内部FLASH的代码那样直接提供给内核运行的，这时可通过修改分散加载文件，把代码加载区设定为NAND-FLASH的程序位置，而程序的执行区设定为SRAM中的位置，这样链接器就会生成一个配套的分散加载代码，该代码会把NAND-FLASH中的代码加载到SRAM中，内核再从SRAM中运行主体代码，大部分运行Linux系统的代码都是这样加载的。

## 创建项目&编译过程

启动文件顾名知义，也就是当STM32上电启动后第一个执行的汇编程序 。
启动文件主要功能：  
- 初始化堆栈指针 SP =_initial_sp  
- 初始化程序计数器指针 PC = Reset_Handler  
- 设置堆、栈的大小  
- 初始化中断向量表  
- 配置外部 SRAM 作为数据存储器（此项有用户根据自己用的开发板的实际情况做配置）  
- 调用 SystemIni() 函数配置 STM32 的系统时钟  （SystemInit 函数需要在外部实现）
- 调用 C 库函数_main 初始化用户堆栈，从而最终调用 main 函数去到 C 的世界 。

由于官方提供的启动文件分了不同型号，如果你不知道你用的芯片该选择哪一种型号的启动文件，不要慌张，Keil-MDK IDE 提供有这个功能，如图，在导入芯片的 package后，如图所示，选择对应芯片型号的启动文件后，可自动添加入工程中。  
![输入图片说明](/imgs/mdk-compile/2023-09-15/TCJ3HGUcrQZproLg.png)

## Reference
- [# MDK的编译过程及文件类型全解](https://doc.embedfire.com/motor/f407jiaoyang/zh/latest/doc/chapter40/chapter40.html)
- [# 浅谈Keil-MDK创建项目&编译过程](https://blog.csdn.net/wct3344142/article/details/105342920)

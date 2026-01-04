---
title: "MDK-KEIL guide/config"
description: 
date: 2023-09-04 10:00:00 +0800
categories: [MDK]
tags: [MDK, Config]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

[# keil系列教程1-15](https://www.strongerhuang.com/Keil/Keil%E7%B3%BB%E5%88%97%E6%95%99%E7%A8%8B.html)  
- Keil系列教程01_Keil介绍、下载、安装与注册  
- Keil系列教程02_新建基础软件工程  
- Keil系列教程03_主窗口和工具栏详细说明  
- Keil系列教程04_菜单概述  
- **Keil系列教程05_工程目标选项配置（一）**  
- **Keil系列教程06_工程目标选项配置（二）**  
- Keil系列教程07_Configuration（一）  
- Keil系列教程08_Configuration（二）  
- **Keil系列教程09_调试仿真**  
- Keil系列教程10_文件类型及相关描述  
- Keil系列教程11_工程窗口图标说明  
- **Keil系列教程12_map文件全面解析**  
- Keil系列教程13_创建多目标工程  
- Keil系列教程14_创建多工程工作空间  
- Keil系列教程15_编辑窗口右键菜单选项说明  

目前Keil有四种产品（软件）：MDK-ARM、C51、C251、C166。四种软件都是基于µVision，在用法上极为相似。

与Keil MDK4及之前版本不同，Keil MDK5分成MDK Core和Software Packs两部分，也就是安装包和支持包。MDK Core主要包含uVision5 IDE集成开发环境和ARM Compiler5。Software Packs则可以在不更换MDK Core的情况下，单独管理（下载、更新、移除）设备支持包和中间件更新包。

MDK-ARM V4 和 V5版本在安装的时候最大的区别在于：V4版本安装包里面集成器件的支持包，而V5版本是独立出来，自己下载安装的，因此需要下载自己芯片型号对应的器件支持包。

MDK-ARM中用于支持MCU芯片的软件包叫“器件支持包”，简称“支持包”。支持包可以通过在线安装，或者下载离线包自己离线安装，这两种安装方式。

在线安装，打开MDK-ARM软件，点击“软件支持包”按钮（Pack Installer）   
![输入图片说明](/imgs/mdk-keil-software/2023-09-04/G3drUjk2ijibwxQD.png)  
![输入图片说明](/imgs/mdk-keil-software/2023-09-04/LWTu8LZAL7cduUkX.png)  

离线安装支持包就如安装软件一样：下载支持包（上面提供下载），双击下载好的离线包，此时会识别安装路径（前提是已经安装MDK），点击“Next”，一会儿就安装完成。  
![输入图片说明](/imgs/mdk-keil-software/2023-09-04/svxix0gU8gGUK7f9.png)  

**STM32下载“标准外设库”，准备源代码（库）**  
根据芯片型号下载对应的标准外设库（如芯片为STM32F103ZE，则下载对应的STM32F10x_StdPeriph_Lib）  
1. 提取ST标准外设库文件  
    ST标准外设库里面源代码文件我们只需要使用部分文件。因此，提取需要使用的文件及文件夹到我们的工程中。  

    解压“STM32F10x_StdPeriph_Lib”，可以看到在主目录下面有四个文件夹：  
    _htmresc：图片文件夹（不提取）  
    Libraries：库文件夹（提取大部分）  
    Project： 示例工程（提取少部分）  
    Utilities： 公共代码、评估板代码（不提取）  

    我们需要使用Libraries和Project目录下的文件，但也有部分不用。因此，我们需要删除不用的文件。  

2. 新建文件和文件夹  
    除了上面提取的文件之外，我们需要建立存放自己代码的文件和文件夹。我这里主要新建三个文件夹：  
    App: 应用部分代码  
    Bsp: 底层部分代码  
    Doc: 说明文档  

    在新建的文件夹下面还需要新建自己的源文件，源文件里面添加源代码。

    上面两个步骤完成之后，我们就可以看到在“STM32F103ZE（Keil）_Demo”下面整理的文件夹。

    新建一个简单、基础的软件工程大概需要有三个步骤：建立工程、添加文件和配置工程。

**工具栏**  
Keil的Toolbars工具栏共有三种：File Toolbar文件工具栏、 Build Toolbar编译工具栏、 Debug Toolbar调试工具栏。

在两种状态（模式）下呈现不同工具栏。在编辑状态时，呈现文件、编译两种工具栏；在调试状态时，呈现文件、调试两种工具栏。

如下图，将编辑状态和调试状态的工具栏按钮分为12类  
![输入图片说明](/imgs/mdk-keil-software/2023-09-04/F50Ijhi7BgZ9JJXT.png)  

**工程目标选项配置**    
C/C++选项页  
Optimization：优化选择项，有Level0 - Level3四个选项  
【设置编译器命令行：-Onum】  
初学者、在线调试建议使用Level0，也就是不优化，这样执行的效果才和代码一样。如果配置成Level3，在线调试可能有些地方优化而不能打断点。  

Optimize for Time：优化时间，即优化代码中费时的地方  
【设置编译器命令行：-Otime】  
比如有些算法，本身代码量就比较大，运行需要很长时间（假如需要2秒），这个时候勾选上该功能，会发现运行时间有比较明显的减少（或许不到1秒时间）。  

**菜单概述**  
[# Keil系列教程04 菜单概述](https://www.strongerhuang.com/Keil/Keil%E7%B3%BB%E5%88%97%E6%95%99%E7%A8%8B04_%E8%8F%9C%E5%8D%95%E6%A6%82%E8%BF%B0.html)  

**map文件**  
[# Keil系列教程12 map文件全面解析](https://www.strongerhuang.com/Keil/Keil%E7%B3%BB%E5%88%97%E6%95%99%E7%A8%8B12_map%E6%96%87%E4%BB%B6%E5%85%A8%E9%9D%A2%E8%A7%A3%E6%9E%90.html)  

**keil优化等级**  
C/C++选项页  
Optimize：优化选择项，有Level0 - Level3四个选项  
【设置编译器命令行：-Onum】    
初学者、在线调试建议使用Level0，也就是不优化，这样执行的效果才和代码一样。如果配置成Level3，在线调试可能有些地方优化而不能打断点。  
使用编译器的最高告警级别（一般有0、1、2、3四个等级），理解所有的告警，通过修改代码而不是降低告警级别来消除所有告警；所有软件代码优化等级发布版本使用代码优化等级 1；  

**cannot open source input file “core_cm4.h”解决方法**  
`error: #5: cannot open source input file “core_cm4.h”: No such file or directory`  
装了比MDK5.11A更高版本的MDK后,可能出现编译标准例程报该错误，这是因为最新版本MDK不支持自动寻找include产生的错误，解决方法是点击魔术棒–>C/C++选项卡–>Include Paths ,选择我们MDK安装目录,找到`\ARM\Pack\ARM\CMSIS\5.5.1\CMSIS\Include和\ARM\Pack\ARM\CMSIS\5.3.0\CMSIS\Include`。每一个人安装路径显示名称不同，但大致路径都是如此。最重要的是找到包含core_cm4.h所在的文件夹，将文件夹添加进来即可，以后遇到相似问题，解决方法和这一样。

**keil4工程转keil5**  
1. Project–>Manage–>Migrate to Version 5 Format…
2. 重新选择添加flash容量，编译。 

**Keil 工程文件uVision Project显示成“礦ision Project“的解决方法**  
https://blog.csdn.net/happybasic/article/details/118308359

**keil查找符号信息（快速定位函数和变量的定义）**  
在Options for Target 的 Output选项卡里面勾选 Browse Information 选项，再编译，再定位，否则无法定位

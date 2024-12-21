---
title: "RO/RW/ZI & 内存（RAM）分配"
description: 
date: 2023-09-05 11:00:00 +0800
categories: [MDK]
tags: [MDK, 内存分配]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## **RO RW ZI**  

要知道代码、常量、全局、局部变量存储在哪儿，就需要了解FLASH和RAM的一些特性，在工程中要具体分析其大小及对应存储地址，可以从map文件中分析。  

网上有资料说的有歧义，可能会误导大家，如：RW-data为已初始化的全局变量。加了static修饰的变量叫静态变量，和全局变量还是存在差别，但已初始化的静态变量也会归为RW-data中。

1.Keil编译窗口信息

`Program Size: Code=2596 RO-data=268 RW-data=44 ZI-data=1028`

`Code`：指代码的大小；  
`RO-data`：指除了内联数据(inline data)之外的常量数据；  
`RW-data`：指可读写（RW）、已初始化的变量数据；  
`ZI-data`：指未初始化（ZI）的变量数据；  

`Code`、`Ro-data`：位于FLASH中；  
`RW-data`、`ZI-data`：位于RAM中；  
提醒：`RW-data`已初始化的数据会存储在Flash中，上电会从FLASH搬移至RAM中。  

关系如下：  
RO  Size = Code + RO Data  
RW  Size = RW Data + ZI Data  
ROM Size = Code + RO Data + RW Data  

## **内存（RAM）分配**  
本节扩展一下RAM的一些知识，有助于大家对程序运行时RAM的情况。

内存分为5类：  
1. 栈区（stack）： 由编译器自动分配和释放 ，存放函数的参数值，局部变量的值等。  
2. 堆区（heap） ： 一般由程序员（使用malloc）分配和（使用free）释放， 若程序员不释放，则一直被占用。一般来说，单片机类的程序一般没有使用堆。  
3. 全局区（静态区）（static）：全局变量和静态变量的存储是放在一块的，初始化的全局变量和静态变量在一块区域， 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。  
4. 文字常量区（const） ：常量字符串就是放在这里的。   
5. 程序代码区 （code）： 存放函数体的二进制代码。  

## **经典例子**
```c
int a = 0; //全局初始化区
char *p1; //全局未初始化区
int main(void)
{
  int b;// 栈
  char s[] = "abc"; //栈
  char *p2; //栈
  char *p3 = "123456"; // 常量字符串"123456\0"在常量区，p3在栈上。
  static int c =0; // 全局（静态）初始化区
  p1 = (char *)malloc(10);
  p2 = (char *)malloc(20);
  // 分配得来的10和20字节的区域就在堆区。
  strcpy(p1, "123456"); // 123456\0放在常量区，编译器可能会将它与p3所指向的"123456"优化成一个地方。
}
```

## Reference
- [# RO RW ZI](https://mp.weixin.qq.com/s/Ey9ZqHt05sXdwescFl2vZA)

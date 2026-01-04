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
#   alt: Here is the text under the image
---
## .map文件
要知道代码、常量、全局、局部变量存储在哪儿，就需要了解FLASH和RAM的一些特性，在工程中要具体分析其大小及对应存储地址，可以从map文件中分析。  

编译后的.map文件中包含了很多有用的信息，关于涉及内存调试必看。  
1. Section Cross References：模块、段(入口)交叉引用  
2. Removing Unused input sections from the image：移除未调用模块  
3. Image Symbol Table：映射符号表  
4. Memory Map of the image：内存（映射）分布  
5. Image component sizes：存储组成大小

网上有资料说的有歧义，可能会误导大家，如：RW-data为已初始化的全局变量。加了static修饰的变量叫静态变量，和全局变量还是存在差别，但已初始化的静态变量也会归为RW-data中。

## Code RO RW ZI
keil编译后，在输出栏可以看到:  
`Program Size: Code=102566 RO-data=10050 RW-data=4504 ZI-data=43256`

其中  
`Code`： 指代码的大小  
`RO-data` ：指除了内联数据(inline data)之外的const变量或字符串常量  
`RW-data` ：指可读写（RW）、已初始化的变量数据  
`ZI-data`：指未初始化（ZI）的变量数据  
其实在工程目录下的`.map`文件中有更详细的描述，动手查看一下。
>  
  `.map`文件中：  
  Code：代码段  
  RO ：const变量或字符串常量  
  RW ：指可读写（RW）的数据  
  data：赋值了的全局变量或static变量、全局数组  
  bss：未赋值的全局变量或static变量  

>注意:  
  `Code`、`RO-data`：位于FLASH中；  
  `RW-data`、`ZI-data`：位于RAM中；  

RW-data已初始化的数据会存储在Flash中，上电会从FLASH搬移至RAM。  
所以程序占用的Flash大小为Code + RO Data + RW Data(中已初始化的数据)。
>
    Total RO  Size (Code + RO Data)               112616 ( 109.98kB)
    Total RW  Size (RW Data + ZI Data)             47760 (  46.64kB)
    Total ROM Size (Code + RO Data + RW Data)     114500 ( 111.82kB)
程序占用RAM= RW-data + ZI-data

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

C语言中使用定义的全局变量，且定义时赋予`0值`给该变量进行初始化，则其为RW变量。(若定义该变量时没有赋予初始值，编译器会把它当ZI-data来对待，初始化为 0)；

ZI-data 的栈空间(Stack)及堆空间(Heap)：在C语言中，函数内部定义的局部变量属于栈空间，进入函数的时候会向栈空间申请内存给局部变量，退出时释放局部变量，归还内存空间。而使用malloc动态分配的变量属于堆空间。在程序中的栈空间和堆空间都是属于ZI-data区域的，这些空间都会被初始值化为0值。编译器给出的ZI-data占用的空间值中包含了堆栈的大小(经实际测试，若程序中完全没有使用malloc动态申请堆空间，编译器会优化，不把堆空间计算在内)。

## Reference
- [# RO RW ZI](https://mp.weixin.qq.com/s/Ey9ZqHt05sXdwescFl2vZA)

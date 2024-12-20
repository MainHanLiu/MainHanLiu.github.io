---
title: "extern机制和使用规范"
description: 
date: 2023-07-21 10:00:00 +0800
categories: [Code, C]
tags: [Code, C, extern]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## **使用extern和包含头文件来引用函数有什么区别呢？**  
extern的引用方式比包含头文件要简洁得多，但推荐使用头文件包含方式来包含开放的外部变量和接口。

## **extern机制和使用规范**

> (1) 
> extern 应用于全局变量，函数或模板声明说明该符号具有外部链接 (external linkage)属性（这个符号在别处定义）。  
> (2) 
> 凡是没有带extern的变量声明同时也都是定义。而对函数而言，带有{}是定义，否则是声明。  
> 如果想声明一个变量而非定义它，extern int a;在变量名前添加关键字 extern ，且不要显式的初始化变量，这时候 extern 不能省略，因为省略了，就变成 int a;虽然没有初始化，但这是一个定义，不是声明。  
> int fun(); 和 extern int fun(); 都是声明(定义要有实现体)。 用 extern int fun() 只是更明确指明是声明而已。而 int a; 是定义 extern int a; 是声明。   
> (3)
> 只能通过包含头文件的方式使用其他.c提供的接口，禁止在.c中通过extern的方式使用外部函数接口、变量；若a.c使用了b.c定义的foo()函数，则应当在b.h中声明extern int foo(int input)；并在 a.c 中通过 #include 来使用 foo （函数声明可以省略extern，不会与函数定义混淆）。禁止通过在 a.c中直接写 extern int foo(int input);来使用 foo，后面这种写法容易在 foo 改变时（改变入参、返回类型等）可能导致声明和定义不一致。  
> (4)
> 对于一个文件中调用另一个文件的全局变量，因为全局变量一般定义在原文件.c中，我们不能用#include包含源文件而只能包含头文件，所以常用的方法是用extern int a来声明外部变量。 另外一种方法是可以是在a.c文件中定义了全局变量int global_num，可以在对应的a.h头文件中写extern int global_num ，这样其他源文件可以通过include a.h来声明她是外部变量就可以了。  
> (5)
> 此外，extern修饰符可用于C＋＋程序中调用c函数的规范问题。

---
title: "do while语句在宏中的运用"
description: 
date: 2023-08-01 10:00:00 +0800
categories: [Code, C]
tags: [Code, C]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---


```c
#define  DEBUG   1  

#if DEBUG
  #define DBG_PRINTF(fmt, args...)  \
  {\
    printf("<<File:%s  Line:%d  Function:%s>> ", __FILE__, __LINE__, __FUNCTION__);\
    printf(fmt, ##args);\
  }
#else
  #define DBG_PRINTF(fmt, args...)   
#endif
```
此种`{ }`写法可能导致错误的原因：在宏定义中，若整体使用`{ }`，在开发者编写的语句中可能会写出
```
if (c)
  宏；
else 
  宏；
```
的语法，在替换后为
```c
if (c)
  { /* ....... */ };
else
  { /* ....... */ };
```
显然，出现了语法错误。if之后的大括号之后不能加分号。这里的分号可以看做一条空语句（单独使用时该语法不报错），这个空语句会把if与else给分隔开来，导致else不能正确匹配到if，导致语法错误。  
**为了解决这个问题，有几种方法**  
第一种方法是：把分号去掉  
```
if (c)
  宏
else 
  宏
```
第二种方法是：在if之后使用DBG_PRINTF打印调试时总是加{ }。  
```
if (c)
  {宏；}
else 
  {宏；}
```
以上两种方法都可以正常编译、运行。但是，我们C语言中，每条语句往往以分号结尾；并且，总有些人习惯在if判断之后只有一条语句的情况下不加大括号；而且我们创建的DBG_PRINTF宏函数的目的就是为了对标printf函数，printf函数的使用加分号在任何地方的使用都是没有问题的。  
基于这几个原因，我们有必要再对我们的DBG_PRINTF宏函数进行一个改造。  
下面引入`do{}while(0)`来对我们的DBG_PRINTF进行一个简单的改造。改造后的DBG_PRINTF宏函数如下  
```c
#define DBG_PRINTF(fmt, args...)  \
do\
{\
    printf("<<File:%s  Line:%d  Function:%s>> ", __FILE__, __LINE__, __FUNCTION__);\
    printf(fmt, ##args);\
}while(0)
```
这里的`do...while`循环的循环体只执行一次，与不加循环是效果一样。我们的宏函数实体中，`while(0)`后面不加分号，在实际调用时补上分号，既符合了C语言语句分号结尾的习惯，也符合了`do...while`的语法规则。  

## Reference
- [# 宏打印 do while{0}](https://zhuanlan.zhihu.com/p/141542463)

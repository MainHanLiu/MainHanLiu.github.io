---
title: "C陷阱篇之enum默认长度"
description: 
date: 2023-09-04 10:00:00 +0800
categories: [Code, C]
tags: [Code, C, enum]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## **C陷阱篇之enum默认长度**  
enum型用于定义常量集合，相比#define有一些优势，如：enum是一种数据类型，使用时会检查类型匹配；enum增加了范围约束，避免变量赋值和使用时超出定义范围。但enum也有一个隐含问题：enum变量占用的空间与编译器相关。

多数编译器默认enum型长度等于int型，很多人也把enum型变量等同于int，但C标准在这里留下了尾巴：“枚举型尺寸是能够容纳最大枚举子值的整数尺寸”，“枚举类型中枚举子的值必须要能用一个int型表述”。也就是说，枚举型的尺寸不能超过int型，但不必等于int型，只要能容纳最大枚举子就行，下例：
```c
enum EType1 { e1 = CHAR_MAX };
enum EType2 { e2 = SHORT_MAX };
```
这两个枚举类型最小可用char、short的内存空间表示，即有可能：
```c
sizeof( EType1 ) == sizeof( char );
sizeof( EType2 ) == sizeof( short );
```
一些编译器为节约内存可以设置这种“量体裁衣”的策略。如ADS就有(enum container always int)选项，选定后enum变量长度为int，否则就等于能容纳最大枚举子的最短长度。gcc也有类似选项-fshort-enums，默认不设定，一旦设置就选用节省内存的enum长度。

enum长度不确定会带来可移植性问题，如果第三方库API接口使用enum类型，编译和调用库时一旦有关enum长度的编译器设置不一致，API接口层对数值的解析就不匹配。比如上层应用编译时没有用-fshort-enums，默认用4字节空间来存储使用enum变量，而编译库时设置了fshort-enums，则库内部此enum size可能为1。当把enum变量地址传进API时，内部只修改变量最低字节，高3字节值无变化（内容随机），API返回时，上层使用的4字节enum变量值就可能随机。（潜规则篇之API接口）

## 结论
因此内部代码使用enum类型优于define，但对外API接口尽量避免用enum型。
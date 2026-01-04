---
title: "宏定义define与typedef"
description: 
date: 2023-10-26 10:00:00 +0800
categories: [Code, Common]
tags: [Code, Common, define, typedef]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## 宏定义
宏定义的一般形式为：`#define 宏名 字符串`  

宏名是标识符的一种，命名规则和标识符相同。  

字符串可以是数字、表达式、if 语句、函数等。这里所说的字符串是一般意义上的字符序列，不要和C语言中的字符串等同，它不需要双引号，当需要替换成C语言的字符串类型时，需要加双引号。  

在预处理阶段，对程序中所有出现的“宏名”，预处理器都会用宏定义中的字符串去代换，这称为“宏替换”或“宏展开”。  

### 对宏定义的几点说明
1. 宏定义是用宏名来表示一个字符串，在宏展开时又以该字符串取代宏名，这只是一种简单粗暴的替换。字符串中可以含任何字符，它可以是常数、表达式、if 语句、函数等，预处理程序对它不作任何检查，如有错误，只能在编译已被宏展开后的源程序时发现。  
2. 宏定义不是说明或语句，在行末不必加分号，如加上分号则连分号也一起替换。
3. 宏定义必须写在函数之外，其作用域为宏定义命令起到源程序结束。如要终止其作用域可使用#undef命令。
4. 代码中的宏名如果被引号包围，那么预处理程序不对其作宏代替。
```c
#define OK 100
printf("OK\n");
// 此处OK不会被替换
```
5. 宏定义允许嵌套，在宏定义的字符串中可以使用已经定义的宏名，在宏展开时由预处理程序层层代换。
6. 习惯上宏名用大写字母表示，以便于与变量区别。但也允许用小写字母。（不推荐）
7. 可用宏定义表示数据类型，使书写方便。（不推荐）  
>例：`#define UINT unsigned int`  

### define宏定义中的特殊操作符 
在单一的宏定义中，最多可以出现一次`#`或`##`预处理操作符。如果没有指定与`#`或`##`预处理操作符相关的计算次序，则会产生问题。为避免该问题，在单一的宏定义中只能使用其中一种操作符(即，一个`#`或一个`##`，或都不用)。除非非常有必要，否则尽量不要使用`#`和`##`。

#### （1）`#@` 字符化操作符
`#@x`只能用于有传入参数的宏定义中，且必须置于宏定义体中的参数名前。作用是将传的单字符参数名转换成字符，以一对单引用括起来其实就是给x加上单引号，结果返回是一个`const char`
```c
#define ToChar(x)    #@x

char a = ToChar(1);  
// 等效于 char a = '1';  

char a = ToChar(123);  
// 等效于 char a = '3';  
// 即，若传入多个字符，取最后一个字符化，但最多不能超过四个字符
```

#### （2）`#` 字符串化操作符
`#x`表示字符串化操作符（stringification），用来把参数转换成字符串。其作用是：将宏定义中的传入参数名转换成用一对双引号括起来参数名字符串。其只能用于有传入参数的宏定义中，且必须置于宏定义体中的参数名前。说白了，就是给x加双引号。
```c
#define ToString(x)    #x

char* str = ToString(123132);  
// 等效于 char* str = "123132";
```
#### （3）`##` 连接操作符
(token pasting, or token concatenation，merge two tokens into one while expanding macros)
```c
#define Conn(x,y)     x##y

int n = Conn(123,456);
// 等效于 int n = 123456;

char* str = Conn("asdf", "adf");
// 等效于 char* str = "asdfadf";
```

#### （4）`...` and `__VA_ARGS__`
`__VA_ARGS__` 是一个可变参数的宏，用来接受不定数量的参数。实现思想就是宏定义中参数列表的最后一个参数为省略号（也就是三个点），这样预定义宏`__VA_ARGS__`就可以被用在替换部分中，替换省略号所代表的字符串。

```c
#define eprintf(...)  fprintf(stderr, __VA_ARGS__)

eprintf("%s:%d: ", input_file, lineno);  
// 等效于 fprintf(stderr, "%s:%d: ", input_file, lineno);  
```

当`__VA_ARGS__`宏前面加上`##`时，可以省略参数输入

```c
#define eprintf(format, ...)  fprintf(stderr, format, ##__VA_ARGS__)

eprintf("success!\n")  
// 等效于 fprintf(stderr, "success!\n");
```
`##__VA_ARGS__ `宏前面加上`##`的作用在于，当可变参数的个数为0时，这里的##起到把前面多余的","去掉的作用,否则会编译出错。

省略号只能代替最后面的宏参数。
`#define W(x,…,y)`错误
但是支持`#define W(x, …)`，此时传入的参数个数必须能够匹配。

## typedef
宏定义只是简单的字符串替换，由预处理器来处理。  

推荐使用**typedef**：`typedef 类型原名 类型别名`  

`typedef`是在编译阶段由编译器处理的，它并不是简单的字符串替换，而给原有的数据类型起一个新的名字，将它作为一种新的数据类型。  

**typedef** 仅限于为类型定义符号名称。  

>例：
```c
#define PIN1 int *
typedef int *PIN2;  //也可以写作typedef int (*PIN2);
```
从形式上看这两者相似， 但在实际使用中却不相同。  
`PIN1 a, b;` 在宏代换后变成：`int * a, b;`，表示 a 是指向整型的指针变量，而 b 是整型变量。  
`PIN2 a, b;`表示 a、b 都是指向整型的指针变量。因为 PIN2 是一个新的、完整的数据类型。由这个例子可见，宏定义虽然也可表示数据类型， 但毕竟只是简单的字符串替换。在使用时要格外小心，以避出错。  　

### typedef定义函数指针别名
`typedef char (*p)(int);`，定义的类型是`char (*)(int)`，即传入一个int，返回一个char的函数指针，定义的别名是p。  
```c
// 定义函数指针别名
typedef char (*p)(int);   
p pFun;
char foo(int a){...}
pFun = foo;   
(*pFun)(2);    
```

## Reference
- [#、##、__VA_ARGS__和##__VA_ARGS__的作用](https://blog.csdn.net/q2519008/article/details/80934815)

---
title: "字节数-字节序-字节对齐"
description: 
date: 2023-08-02 10:00:00 +0800
categories: [Code, Common]
tags: [Code, pragma pack]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## **`__packed`关键字**

> `__packed`关键字，可以得到非对齐字的紧凑型结构体，会强制编译器将结构体成员按1字节对齐。使用`__packed`一般会以降低运行性能为代价，由于大多数cpu处理数据在合适的字节边界数的情况下会更有效，`packed`的使用会破坏这种自然的边界数  
> 如果编译器不支持`__packed`关键字，将其定义为空宏即可`#define  __packed`  

## 字节数
不同类型数据在32/64位系统占据字节:

| 数据类型 | 32位系统 | 64位系统 |
|--|--|--|
| char | 1 | 1 |
| short | 2 | 2 |
| int | 4 | 4 |
| long | 4 | **8** |
| long long | 8 | 8 |
| float | 4 | 4 |
| double | 8 | 8 |
| ptr | 4 | **8** |

>64位系统中，仅long和ptr不同，为8



## 字节序-字节对齐

### 一、 更改c编译器的缺省字节对齐方式  
在缺省情况下，c编译器为每一个变量或数据单元按其自然对界条件分配空间；一般地可以通过下面的两种方法来改变缺省的对界条件：  
1. 方法一  
    使用#pragma pack(n)，指定c编译器按照n个字节对齐；  
    使用#pragma pack()，取消自定义字节对齐方式。  
2. 方法二  
    ```
    __attribute__(aligned(n))

    当attribute__((aligned(n)))修饰结构体定义时，影响结构体整体的补齐大小和对齐地址，当修饰声明后的变量时，不影响sizeof大小只影响对齐。

    __attribute__((aligned(n)))作用在结构体总体大小上，不影响结构体内部成员的排序。  
    让所作用的数据成员对齐在n字节的自然边界上；如果结构中有成员的长度大于n，则按照最大成员的长度来对齐；  
    编译器会让n与默认的对齐字节数进行比较，取较大值为对齐字节数，与#pragma pack(n)恰好相反。  

    __attribute__((packed))
    取消结构在编译过程中的优化对齐，按照实际占用字节数进行对齐。
    ```

**#pragma pack**  
语法：`#pragma pack( [show] | [push | pop] [, identifier], n )`  
`#pragma pack` 的主要作用就是改变编译器的内存对齐方式，这个指令在网络报文的处理中有着重要的作用，一旦改变数据类型的alignment，直接效果就是占用memory的减少，但是performance会下降；如果编写的是基于协议，如串口通讯的程序，那么必须严格按照一定的规则进行接收数据包。那么，只要`#pragma pack(1)`，让数据在内存中是连续的，才好处理的  
**#pragma pack(n)**  
`#pragma pack(n)`是他最基本的用法，其作用是改变编译器的对齐方式， 不使用这条指令的情况下，编译器默认采取`#pragma pack(8)`也就是8字节的默认对齐方式，n值可以取（`1`， `2`， `4`， `8`， `16`） 中任意一值。  
**#pragma pack()**  
`#pragma pack()` 能够取消自定义的对齐方式，恢复默认对齐。  
**#pragma pack(show)**  
`#pragma pack(show)`显示当前内存对齐的字节数。在程序中`#pragma pack(show)`会在编译阶段提出一个警告，说明当前对齐字节数。  
**#pragma pack(push)**  
**#pragma pack(push, n)**  
`#pragma pack(push)`会将当前的对齐字节数压入栈顶，并设置这个值为新的对齐字节数， 就是说不会改变这个值。    
`#pragma pack(push, n)` 会将当前的对齐字节数压入栈顶，并设置n为新的对齐字节数。  
**#pragma pack(pop)**  
**#pragma pack(pop, n)**  
`#pragma pack(pop)`会弹出栈顶对齐字节数，并设置其为新的内存对齐字节数  
`#pragma pack(pop, n)`情况就不同了， 他会弹出栈顶并直接丢弃，设置`n`为其新的内存对齐字节数  
**#pragma pack(push [, identifier] [, n])**  
**#pragma pack(pop [, identifier] [, n])**  
`#pragma pack(push, identifier [, n])`会在上面的操作基础上为这个对齐字节数附上一个标识符， 这里注意这个标识符只能以（`$`、`_`、`字母`）开始， 标识符中可以有（`$`、`_`、`字母`、`数字`），并且标识符不能是关键字(`push， pop可以作为标识符`)。  
`#pragma pack(pop, identifier [, n])`较为复杂，编译器执行这条执行时会从栈顶向下顺序查找匹配的`identifier`，找到`identifier`相同的这个数之后将从栈顶到`identifier`，包括找到`identifier`全部`pop`弹出， 若没有找到则不进行任何操作。

![输入图片说明](/imgs/code-c-pragma-pack/2023-08-02/snejv2sRypGD34Hp.png)
  
![输入图片说明](/imgs/code-c-pragma-pack/2023-08-02/5Uzj4Og6mnsJY7jQ.png)


**对齐原则**  
1、数据成员对齐规则：结构(struct)(或联合(union))的数据成员，第一个数据成员放在offset为0的地方，以后每个数据成员的对齐按照#pragma pack指定的数值和这个数据成员自身长度中，比较小的那个进行。  
2、结构(或联合)的整体对齐规则：在数据成员完成各自对齐之后，结构(或联合)本身也要进行对齐，对齐将按照#pragma pack指定的数值和结构(或联合)最大数据成员长度中，比较小的那个进行。  
**附注：**  
```c
typedef struct test
 {
	 char c;
	 double b;
 }test_t;

test_t b;

printf("%d", sizeof(b));
```
若`#pragma pack(4)`，结果为12  
若`#pragma pack(8)`，结果为16  
在32位机中，double默认对齐8，但`pragma pack(4)`，在取最小值对齐时总会取到4，因此double在32位机可以视作4字节对齐  
（注意：在vs中即使选x86运行时，默认`#pragma pack()`为8而不是4）  
**重要规则**   
1，复杂类型中各个成员按照它们被声明的顺序在内存中顺序存储，第一个成员的地址和整个类型的地址相同；  
2，每个成员分别对齐，即每个成员按自己的方式对齐，并最小化长度；规则就是每个成员按其类型的对齐参数（通常是这个类型的大小）和指定对齐参数中较小的一个对齐；  
3，结构、联合或者类的数据成员，第一个放在偏移为0的地方（**即起始地址以0结尾，int类型的偏移以4的倍数结尾，shortl类型偏移以2的倍数结尾，char类型任意**）；以后每个数据成员的对齐，按照#pragma pack指定的数值和这个数据成员自身长度两个中比较小的那个进行；也就是说，当#pragma pack指定的值等于或者超过所有数据成员长度的时候，这个指定值的大小将不产生任何效果；  
4，复杂类型（如结构）整体的对齐是按照结构体中长度最大的数据成员和#pragma pack指定值之间较小的那个值进行；这样在成员是复杂类型时，可以最小化长度；  
5，结构整体长度的计算必须取所用过的所有对齐参数的整数倍，不够补空字节；也就是取所用过的所有对齐参数中最大的那个值的整数倍，因为对齐参数都是2的n次方；这样在处理**数组**时可以保证每一项都边界对齐；  
**下面给出例子并详细分析**  
**例子一**  
```c
#pragma pack(4)  
class TestB  
{  
public:  
    int aa; //第一个成员，放在[0,3]偏移的位置，  
    char a; //第二个成员，自身长为1，#pragma pack(4),取小值，也就是1，所以这个成员按一字节对齐，放在偏移[4]的位置。  
    short b; //第三个成员，自身长2，#pragma pack(4)，取2，按2字节对齐，所以放在偏移[6,7]的位置。  
    char c; //第四个，自身长为1，放在[8]的位置。  
};  
```
可见，此类实际占用的内存空间是9个字节。根据规则5，结构整体的对齐是`min( sizeof( int ), pack_value ) = 4，所以sizeof( TestB ) = 12;`  

**例子二**  
```c
#pragma pack(2)  
class TestB  
{  
public:  
    int aa; //第一个成员，放在[0,3]偏移的位置，  
    char a; //第二个成员，自身长为1，#pragma pack(2),取小值，也就是1，所以这个成员按一字节对齐，放在偏移[4]的位置。  
    short b; //第三个成员，自身长2，#pragma pack(2)，取2，按2字节对齐，所以放在偏移[6,7]的位置。  
    char c; //第四个，自身长为1，放在[8]的位置。  
};  
```
可见结果与例子一相同，可实际占用的内存空间是9个字节。各个成员的位置没有改变，但是此时结构整体的对齐是`min( sizeof( int ), pack_value ) = 2，所以sizeof( TestB ) = 10；`  

**例子三**  
```c
#pragma pack(4)  
class TestC  
{  
public:  
    char a; //第一个成员，放在[0]偏移的位置，  
    short b; //第二个成员，自身长2，#pragma pack(4)，取2，按2字节对齐，所以放在偏移[2,3]的位置。  
    char c; //第三个，自身长为1，放在[4]的位置。  
};  
```
整个类的实际内存消耗是5个字节，整体按照`min( sizeof( short ), 4 ) = 2对齐，所以结果是sizeof( TestC ) = 6；`  

**例子四**  
```c
struct Test  
{  
    char x1; //第一个成员，放在[0]位置，  
    short x2; //第二个成员，自身长度为2，按2字节对齐，所以放在偏移[2,3]的位置，  
    float x3; //第三个成员，自身长度为4，按4字节对齐，所以放在偏移[4,7]的位置，  
    char x4; //第四个陈冠，自身长度为1，按1字节对齐，所以放在偏移[8]的位置，  
};  
```
所以整个结构体的实际内存消耗是9个字节，但考虑到结构整体的对齐是4个字节，所以整个结构占用的空间是12个字节。  

**例子五**  
```c
#pragma pack(8)

struct s1  
{  
    short a; //第一个，放在[0,1]位置，  
    long b; //第二个，自身长度为4，按min(4, 8) = 4对齐，所以放在[4,7]位置  
};  
```
所以结构体的实际内存消耗是8个字节，结构体的对齐是min( sizeof( long ), pack_value ) = 4字节，所以整个结构占用的空间是8个字节。

```c
struct s2  
{  
    char c; //第一个，放在[0]位置，  
    s1 d; //第二个，根据规则四，对齐是min( 4, pack_value ) = 4字节，所以放在[4,11]位置,  
    long long e; //第三个，自身长度为8字节，所以按8字节对齐，所以放在[16,23]位置，  
};  
```
所以实际内存消耗是24自己，整体对齐方式是8字节，所以整个结构占用的空间是24字节，sizeof(s2) = 24, s2的c后面是空了3个字节接着是d。  

### 二、机制__attribute__  
[#pragma(pack(n))与__attribute__((aligned(n))) 的区别](https://blog.csdn.net/qq_31985307/article/details/119548722)  
### **a、正确理解__attribute__((aligned(n)))**  
attribute((aligned(n)))定义的是**最小对齐边界**，它可以用来修饰structure也可以用来修饰变量或者structure成员变量，它只能**增加** structure、变量或者structure成员变量的对齐值（GCC手册说在修饰 typedef 类型时可以增加或减少对齐值）。  
### **b、正确理解 #pragma(pack(n))**  
#pragma(pack(n))定义的是**最大对齐边界**，它可以用来修饰structure也可以用来修饰structure成员变量，它**可以增加/减少** structure或者structure成员变量的对齐值。  
### **c、详解例题**  
>首先我们要明确在code顺序上，上述例题的 #pragma(pack(n)) 先生效，然后才是__attribute__((aligned(n)))生效。  

**例题1**
```c
#pragma pack(push,2)
typedef struct test{
    char a;
    double b;
    char c;
    int d;
}  __attribute__ ((aligned (32))) test ;
#pragma pack(pop)
```

> ① #pragma pack(push,2) 将编译器的**最大对齐边界设置为 2**  
② 以 2 为最大对齐边界后，test 的内存对齐情况(先不考虑 aligned 属性)：
```c
typedef struct test{
    char a;   // 1B
    double b; //因为最大对齐是2，所以只需要满足2的倍数，不需要满足8的倍数
    		  //填充 1B ，本身占据 8B	
    char c;   //对齐时我们要选择最小的对齐值，对于c最小的对齐值是1（不是2）
              //无填充，自身占据 1B
    int d;    //对齐值为2，故填充 1B，自身占据 4B
} test ;      //整个结构体对齐值为 2（不是8，因为已经设置了最大对齐值是2）
// (1B) + (1B）+（8B) + (1B) + (1B）+（4B) = 16B + 0B(已经满足2的倍数，无需对结构体进行填充)
```
> 也就是说当前（不考虑 aligned 属性）时，test 类型占据 16B，最大对齐值 2 。  
③ 最后来看 aligned 属性，我们前面说了，aligned 属性设置的是最小对齐值，也就是说当前类型或变量的对齐值要大于或等于aligned 属性设置的对齐值，所以编译器重新设置 test类型的对齐值为32，则test struct需要填充16B。  
?结果就是32B 。

**例题2**
```c
#pragma pack(push,4)
typedef struct  test{
    char a;
    double b;
    char c;
    int d;
}  __attribute__ ((aligned (2))) test ;
#pragma pack(pop)
```
> 1B + 3B + 8B + 1B + 3B + 4B = 20B + 0B  
> 对应于 test类型的自身对齐值（min(8,4)=4），大于 aligned 属性设置的最小对齐值的情况，这种情况下**可以忽略aligned 属性**。  
?结果就是20B 。

**例题3**
```c
#pragma pack(push,8)
typedef struct  subtest{
    char a;
    double b;
    char c;
    int d;
}subtest;

#pragma pack(push,2)
typedef struct  test{
    char a;
    subtest b;
}  __attribute__ ((aligned (4))) test ;
#pragma pack(pop)

```
> ① test 的内存对齐情况(先不考虑 aligned 属性)  
```c
#pragma pack(push,8)
typedef struct  subtest{
    char a;
    double b;
    char c;
    int d;
}subtest;

#pragma pack(push,2)
typedef struct  test{
    char a;    // 1B
    subtest b; // 对于结构体subtest，原本以8B做为对齐值，但这里设置了最大对齐值2
               // 所以char a之后填充1B，自身占据 24B
} test ;       // (1B) + (1B) + (24B)  = 26B + 0B(已经是2的倍数，不需要再填充)
#pragma pack(pop)
```

> ② 最后来看 aligned 属性，设置的最小对齐值4 > 2,所以 test 结构体需要填充 2B，以构成4的倍数。  
?结果就是28B 。

### **d、注意__attribute__ ((aligned (n)))的位置**  
```c
// 代码一：
#pragma pack(push,8)
typedef struct  test{
    char a;
    double b;
    char c;
    int d;
} __attribute__ ((aligned (16))) test ;
test a ;
```
> 1 + 7 + 8 + 1 + 3 + 4 = 24 （ 24 % 8 = 0）+ 0   
> 24 + 8 = 32 （ 32 % 16 = 0）  
> sizeof(test) = **32**   
> &a % 16 = 0   

```c
// 代码二：
#pragma pack(push,8)
typedef struct  test{
    char a;
    double b;
    char c;
    int d;
} test __attribute__ ((aligned (16)));
test a ;
```
> 1 + 7 + 8 + 1 + 3 + 4 = 24 （ 24 % 8 = 0）+ 0   
> sizeof(test) = **24**  
> &a % 16 = 0   

代码一修饰的是 **struct test类型** ，影响结构体大小和对齐地址  
代码二修饰的是 **test类型**，只影响对齐地址  

### **e、补充说明**  
#pragma pack(n) 的作用范围是整个文件，也就是说结构体本身和结构体成员都需要遵守这个对齐规则；  
而__attribute__((aligned(x)))的作用范围如果是用在结构体上，则对齐规则只限于结构体本身，其内部的成员变量不遵循此规则。如果是修饰结构体中的成员变量，则此成员变量需要遵守对齐规则，最后结构体进行对齐时需要和成员变量的最大对齐值成倍数即可（不一定受此规则限制）。如果是修饰变量，则只影响该变量的对齐地址。  

[# C语言字节对齐及__attribute__((aligned(n))) 与 #pragma(pack(n))的作用](https://blog.csdn.net/super_demo/article/details/50032553)  
```
GNU C 的一大特色就是__attribute__机制。__attribute__可以设置函数属性（Function Attribute ）、变量属性（Variable Attribute ）和类型属性（Type Attribute ）

__attribute__ 语法格式为：__attribute__((attribute-list))
attribute的前面和后面都有两个下划线，后面紧跟两对元括弧， attribute-list是一个用逗号分隔开的属性列表。__attribute__((attribute-list))放于声明的尾部“；”之前。

在使用__attribute__参数时，你也可以在参数的前后都加上“__” （两个下划线），例如，使用__aligned__而不是aligned ，这样，你就可以在相应的头文件里使用它而不用关心头文件里是否有重名的宏定义。
```
```
__attribute__((__aligned__(n)))定义的是最小对齐边界，它可以用来修饰structure也可以用来修饰变量或者structure成员变量，它只能增加 structure、变量或者structure成员变量的对齐值（GCC手册说在修饰 typedef 类型时可以增加或减少对齐值）

当attribute__((aligned(n)))修饰结构体定义时，影响结构体整体的补齐大小和对齐地址，当修饰声明后的变量时，不影响sizeof大小只影响对齐。
attribute__((aligned(n)))作用在结构体总体大小上，不影响结构体内部成员的排序。
__attribute__((aligned(n)))让所作用的数据成员对齐在n字节的自然边界上；如果结构中有成员的长度大于n，则按照最大成员的长度来对齐；  
编译器会让n与默认的对齐字节数进行比较，取较大值为对齐字节数，与#pragma pack(n)恰好相反。

int a __attribute__((__aligned__(8))) = 10;
这个修饰的影响主要是对齐，所谓对齐是存储为值的起始地址。变量a的地址&a,本来是4字节对齐，变成了8字节对齐（有的环境对最大对齐数值有限制）。8字节对齐就是&a的地址是8的整数倍。
sizeof（a） = 4; //a 占用的字节数还是4个字节

下面看看在使用了typedef之后对对齐和变量大小的影响：
typedef int int32_t __attribute__((__aligned__(8))) ；
sizeof(int32_t ) = 4; //占用的字节数还是4个字节
这样说明int32_t 声明的变量按照8字节对齐，大小是4字节，这样就会有一个问题，这个变量不能定义数组：
int32_t array[2]; //这样定义编译器会报err
报错的原因是数组的存储在内存中是连续的，而int32_t 只有4字节确要8字节对齐，这样对齐和连续就不能同时保证，就会报错。
```
```
typedef struct flag {
	int a;
	char b;
}__attribute__((__aligned__(1))) FLG;
FLG test;
 
sizeof(test) = 8; 
__attribute__((__aligned__(1)))修饰struct flag，结构体最小对齐值为1，系统默认4字节对齐了结构体成员，1 < 4,忽略1字节对齐。
```
```
修饰结构体构造
typedef struct flag {
	int a;
	char b;
}__attribute__((__aligned__(32))) FLG;
FLG test;
 
sizeof(test) = 32; 
__attribute__((__aligned__(32)))修饰struct flag，结构体按照32字节对齐

****************************************************

修饰构造出的结构体变量
typedef struct flag {
	int a;
	char b;
}FLG __attribute__((__aligned__(32)));
FLG test;
 
sizeof(test) = 8; 
__attribute__((__aligned__(32)))修饰FLG变量，只影响对齐，不影响结构的大小，FLG的地址是32字节对齐。
```
```
struct my_packed_struct 
{ 
	char c; 
	int i; 
}__attribute__((__packed__));

__attribute__((packed)) 的作用就是告诉编译器取消结构在编译过程中的优化对齐,按照实际占用字节数进行对齐
```

 
## 内存中变量分布  
- Question:  
如果代码中紧邻定义的前一个变量在分配空间时是紧凑类型，下一个变量是优化对齐，那么下一个变量从哪里开始？变量，结构体的顺序和开始地址如何组织？  
- Answer:  
经实验和代码中的定义顺序无关系，可以观察到起始地址严格符合对齐要求。  
C语言规范中没有定义的行为：这些行为包括但不限于：变量分布，++/--混用的时候的优先级，函数调用时参数的计算次序等等。  
因此变量分布的方式与编译器的行为有关，无绝对规则。  

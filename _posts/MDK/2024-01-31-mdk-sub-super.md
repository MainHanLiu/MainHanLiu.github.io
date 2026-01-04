---
title: "函数扩展之$Sub$$与$Super$$"
description: 用于给某个函数打补丁而不破坏原来的程序结构 
date: 2024-01-31 10:00:00 +0800
categories: [MDK]
tags: [MDK]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## `$Sub$$`与`$Super$$`

为了在进入 main 程序之前，完成系统功能初始化，可以使用 `$sub$$` 和 `$super$$` 函数标识符在进入主程序之前调用另外一个例程，这样可以让用户不用去管 main() 之前的系统初始化操作。

![输入图片说明](/imgs/mdk-sub-super/2024-01-31/AYzBGuPeRJM02WAH.png)

`$Sub$$`和`$Super$$`不只可以用于main函数，也可以用于其他任意函数

在MDK开发环境下，对于某些无法被更改的函数，我们需要更改这些函数执行前后的逻辑，偏偏又无法更改到调用这些函数并已经被封装的代码，这真是让人一筹莫展。幸好MDK给我们留了一个后门，让我们充分使用`$Sub$$`和`$Super$$`来完成这个目标。

比如某个函数`extern void foo(void);`，我们要在它执行的前后进行一段特定的处理，那么参考下面的代码：
```c
/*定义 foo 函数*/
void foo(void)
{
    printf("foo()\n");
}
 
/*定义foo的补丁函数*/
void $Sub$$foo(void)
{
    extern int $Super$$foo(void);

    /*需要在 foo() 之前执行的代码块*/
    printf("before_foo()\n");
   
    /*调用 foo()，要注意使用前缀"$Super$$"*/
    $Super$$foo();
 
    /*需要在 foo() 之后执行的代码块*/
    printf("execute_after_foo()\n");
}
```

输出内容：
```
before_foo()
foo()
after_foo()
```
相当于在`$Super$$Fun()`的位置整体把原先的Fun函数替换进来，并在`$Super$$Fun()`的前后增加代码补丁，`$Sub$$Fun()`变成了真正的`Fun()`


## RTT 举例
RT-Thread在main函数执行之前就执行了初始化硬件、时钟的工作
```c
/* re-define main function */
int $Sub$$main(void)
{
    rtthread_startup();
    return 0;
}

/* the system main thread */
void main_thread_entry(void *parameter)
{
    extern int main(void);
    extern int $Super$$main(void);

    /* RT-Thread components initialization */
    rt_components_init();

    /* invoke system main function */
#if defined(__CC_ARM) || defined(__CLANG_ARM)
    $Super$$main(); /* for ARMCC. */
#elif defined(__ICCARM__) || defined(__GNUC__)
    main();
#endif
}
```

>该特性仅在ARM CC编译器内被支持

## Reference
- [# RT-Thread代码启动过程——以及 `$Sub$$main` 与 `$Super$$main`](https://blog.csdn.net/yang1111111112/article/details/80913001)
- [# 官方手册介绍](https://developer.arm.com/documentation/dui0377/g/Accessing-and-Managing-Symbols-with-armlink/Use-of--Super---and--Sub---to-patch-symbol-definitions)
- [# 关于`$Sub$$main` 和 `$Super$$main`的用法](https://blog.csdn.net/weixin_44788542/article/details/115867642)

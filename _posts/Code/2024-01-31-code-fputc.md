---
title: "fputc串口重定向"
description: 
date: 2024-01-31 10:00:00 +0800
categories: [Code, Common]
tags: [Code, Common]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

printf 是一个库函数，用户不知道源代码，因此当需要输出到 LCD、串口1、串口2等不同的地方时，我们不可能去修改标准库的源码。

printf函数是在stdio.h文件中定义的，它又会调用更底层的fputc这个函数，去一个个地打印出字符；所以，我们只要将fputc函数实现，就可以使用printf函数方便地输出了。

>上述的使用方法虽然很简单、很方便，但是有个致命的问题，效率不高，因为它重写fputc函数的时候，使用的是轮询的方法，执行这个函数的时候，几乎需要一直等到串口数据完全发完才能向后执行。
>- 由于这个fputc函数每调用一次是发送一个字符，直接用`HAL_UART_Transmit_IT`中断或DMA发送的函数也是不行的；因为cubemx生成的中断或DMA发送的实现方式，是一次性只能发送一串数据，再次调用时，要等到上次的一串数都发完，才能正确发送下一次。
>- 对于fputc这种每发送一个字符要调用一次的用法，中断发送、DMA发送和轮询发送的效率是一样的，几乎都是要一直等到所有数据都发完。
>
>一种提高效率的办法是使用发送fifo缓冲区，例如我们之前讲到的方法：将要发送的数据都放入fifo，由发送完中断自动从fifo中取数发送，这样就不用一直等到数据发完。下次有新数据来时，也是填入fifo中，不需要等待。

fputc()具有`_attribute__((weak))`属性，因此可以重定向。

如果你要实现多个不同的串口打印输出，不如使用 vsprintf（建议vsnprintf），好处就是这个函数的输出位置不是fputc，而是你给定的缓存空间，这样你就可以实现自己的printf函数了。

## Reference
- [# 关于串口发送的重定向](https://www.cnblogs.com/xiaobaibai2021/p/15716910.html)

---
title: "Peripheral Note"
description: 
date: 2023-09-05 10:00:00 +0800
categories: [Peripheral]
tags: [Peripheral, Note]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## adc
一个ADC的位数是12位，参考电压为3.3V，那么其数字值变化1对应的模拟信号电压变化为3.3V/(2^12)≈0.8mV。想要获得更高的精度，可以选择位数跟高的ADC或者降低参考电压。  

## DMA
直接访问存储器（DMA），即从一个地方把东西送到另一个地方。这个地方可以是外设，也可以是内存的一个地址。这样在最初配置好后，完全不需要CPU介入，只需要在需要的时候读取对应的内容就行，大大减轻了CPU的负荷。这是32位单片机真正比8位机强大的最为关键所在。所以在用32位做开发时，都尽可能的使用DMA去减轻CPU的负荷。 

## GPIO
对于IO口的使用，重要的一点必须牢记的是：对于输入口，必须有明确的电平信号，确保不能浮空(可以通过增加上拉或下拉电阻来实现)；而对于输出口，其输出的状态的电平必须考虑其外部的连接情况，比如说驱动能力。
---
title: "KEIL 生成带有时间戳的hex文件"
description: 
date: 2024-04-25 10:00:00 +0800
categories: [MDK]
tags: [MDK]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

1. Keil —— Options —— User选项卡
```
After build:
	build_date_time.bat @L
```
2. build_date_time.bat
```bash
echo off
::echo %date%_%time%
set date_num=%date:~0,10%
set date_num=%date_num:-=%
set date_num=%date_num:/=%
set time_num=%time:~0,8%
set time_num=%time_num::=%
if "%time_num:~0,1%"==" " set "time_num=0%time_num:~1%"
echo on
copy ..\..\output\%1.hex ..\..\output\%1_%date_num%%time_num%.hex
```
>注意 window下复制命令为copy 非cp

## Reference
- [# keil生成带有时间戳的hex文件](https://blog.csdn.net/CWgneD/article/details/110929789)
- [# %date~0,4%和 %time~0,2%等用法详解](https://blog.csdn.net/icanlove/article/details/42640329)

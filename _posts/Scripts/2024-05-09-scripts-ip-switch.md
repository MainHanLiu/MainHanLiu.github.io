---
title: "IP切换脚本"
description: 
date: 2024-05-09 10:00:00 +0800
categories: [Scripts]
tags: [Scripts, bat]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

- 静态ip

```bash
@echo off
rem //设置变量 
set NAME="以太网 2"

rem //以下属性值可以根据需要更改
set ADDR=192.168.1.12
set MASK=255.255.255.0
rem //以上属性依次为IP地址、子网掩码

echo IP地址 = %ADDR%
echo 掩码 = %MASK%

netsh interface ipv4 set address %NAME% static %ADDR% %MASK% %GATEWAY% 

echo **********已设置为静态IP：%ADDR%***********

exit
```

- 动态ip

```bash
@echo off

rem //设置变量 
set NAME="以太网 2"

netsh interface ip set address %NAME% dhcp
netsh interface ip set dns %NAME% dhcp 
echo **********已设置为动态IP地址***********

exit
```

**注：**  
以上脚本需要在管理员权限下运行

>设置默认以管理员权限运行脚本，不弹窗
1. 右键点击该程序的桌面快捷方式，选择“属性”。
2. 在属性设置界面中，切换到“快捷方式”选项卡，然后点击“高级”按钮。
3. 在高级属性对话框中，勾选“以管理员身份运行”复选框，然后点击“确定”按钮。
4. 点击“应用”和“确定”按钮，保存设置。
这样设置后，每次双击运行该程序时都会默认以管理员身份运行。

---
title: "KEIL 不破坏现场环境的情况下进入debug模式"
description: 
date: 2024-10-17 10:00:00 +0800
categories: [MDK]
tags: [MDK]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## 在调试选项中，取消以下选择项

1. **Options For Target - Debug - Load Application at startup**
![输入图片说明](/imgs/mdk-debug-reset/2024-10-17/oF82m2vaCh8rYHmO.png)  

2. **Options For Target - Debug - Settings - Reset after connect** (with **Options For Target - Debug - Settings - Connect** selected as **NORMAL**)
![输入图片说明](/imgs/mdk-debug-reset/2024-10-17/FOMTJyfCuebQRNsZ.png)  

3. **Options For Target - Utilities - Update Target before Debugging**
![输入图片说明](/imgs/mdk-debug-reset/2024-10-17/fBd5vrlgE3gjTTdp.png)  

如果上述所有选项都被禁用，调试器将在不执行硬件重置的情况下启动。  

## 新建调试模式初始化命令脚本loadaxf

loadaxf文件内容如下：  
```bash
LOAD %L INCREMENTAL
```

## Reference
- [# UVISION DEBUGGER: Connect to a running Target without Hardware Reset](https://developer.arm.com/documentation/ka003020/latest)
- [# 关于如何使用keil程序运行时，不破坏现场环境的情况下进入debug模式](https://www.cnblogs.com/rongjiangwei/p/16466495.html)

---
title: "JFlash/HexView合并hex文件"
description: 
date: 2023-12-28 10:00:00 +0800
categories: [MDK]
tags: [JFlash, HexView]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## JFlash合并bin文件及hex文件
- 合并bin格式文件：  
1. file-> open data file， 选择boot文件， 地址从8000000开始。
2. file-> merge data file， 选择app文件， 地址从程序开始执行地址开始，根据keil mdk工程里设置的地址来填写， 比如8004000。
3. file-> save data file as... ，保存合并后文件（可选相应的文件格式）。

- 合并hex格式文件：  
hex格式文件自带地址管理，不需要选择地址。  
1. file-> open data file， 选择boot文件。
2. file-> merge data file， 选择app文件。
3. file-> save data file as... ，保存合并后文件（可选相应的文件格式）。
总体来说，建议选择合并hex格式的文件，省去地址输入的烦恼。但是HEX文件占内存大一点。

## HexView
下载：一般安装了Canape自动就会有，并且也不需要license文件  

>问题：在Windows 10上无法使用旧版本的HexView。程序不再打开。一旦你试图打开HexView，它被阻塞在启动阶段，不出现或关闭。  
>重命名HexView目录中的pbuild.dll。该文件防止启动HexView，会导致崩溃，它不是运行程序所必需的。  
>——引用自[support.vector.com](https://support.vector.com/kb?id=kb_article_view&sysparm_article=KB0011961&sys_kb_id=a29f7c10dbd470544896115e689619f0&spa=1)   

### .bat调用
```bat
Rem Path to the executable exe of the Vector HexView tool on your PC
set HEXVIEW_TOOL_PATH=..\..\tools\HexView\hexview.exe
 
Rem Boot_file indicates one of the merged source files and its path
set BOOT_FILE_PATH=..\..\tools\Input\boot.hex
 
Rem Core_file indicates one of the merged source files and its path
set CORE_FILE_PATH=..\..\tools\Input\core.hex

Rem App_file indicates one of the merged source files and its path
set APP_FILE_PATH=..\..\tools\Input\app.hex

Rem App_src_file indicates one of the merged source files and its path
set APP_SRC_FILE_PATH=..\..\prj\*\Objects\app.hex
 
Rem Output_hex_File indicates the output hex file, and its path, after the merge
set OUTPUT_FILE_PATH=..\..\tools\Output\output.hex

:: 生成烧录文件···
if exist %~dp0%APP_FILE_PATH% (
del %APP_FILE_PATH%
)

copy %APP_SRC_FILE_PATH% %APP_FILE_PATH%


Rem 判断要合成的hex文件是否存在
if not exist %~dp0%BOOT_FILE_PATH% (
echo boot.hex不存在...
exit
)
if not exist %~dp0%CORE_FILE_PATH% (
echo core.hex不存在···
exit
)
if not exist %~dp0%APP_FILE_PATH% (
echo app.hex不存在···
exit
)

Rem 删除旧的烧录文件
if exist %~dp0%OUTPUT_FILE_PATH% (
del %OUTPUT_FILE_PATH%
)

echo 生成烧录文件output.hex···
Rem Call the command line provided by the HexView tool and merge Input_hex_File1 Input_hex_File2 Input_hex_File3 to output a new Output_hex_File
%HEXVIEW_TOOL_PATH% /S /MT:%~dp0%BOOT_FILE_PATH%;0x0:0x60000000-0x6001FFFF+%~dp0%CORE_FILE_PATH%;0x0:0x60020000-0x6011FFFF+%~dp0%APP_FILE_PATH%;0x0:0x60120000-0x6019FFFD /XI:32 -o %~dp0%OUTPUT_FILE_PATH%

Rem For a new Output_hex_File, fill in the characters
%HEXVIEW_TOOL_PATH% %OUTPUT_FILE_PATH% /S /FA: /AF:0xFF /FR:0x6019FFFE-0x6019FFFF /FP:FF /XI:32 -o %OUTPUT_FILE_PATH%

::pause
```

### 为什么要“Fill填充”
**S19**/**Hex**文件是一种广泛使用的嵌入式系统文件格式,用于存储程序代码和数据。**S19**/**Hex**文件中的未使用地址需要进行填充的主要原因有:  
1. **确保二进制映像的连续性**。S19/Hex文件存放的其实是目标处理器上程序和数据的二进制映像,如果有地址段未填充,会导致此地址段的二进制映像 discontinuity,无法正确下载到目标处理器。
2. **满足一些处理器的对齐要求**。有些处理器要求程序代码和数据必须按一定的地址边界对齐,如果未填充,可能导致映像无法正确运行。
3. **防止未初始化数据被错误执行**。未使用的地址段的数据初始值是不确定的,如果不进行填充,这些未初始化的数据可能被处理器错误执行,导致未定义行为。
4. **便于程序调试**。填充后的S19/Hex文件,各地址段的二进制映像是连续完整的,这有利于调试工具读取和解析,实现更准确的调试。
5. **防止其他意外影响**。未填充的地址段的数据虽然不会被正常执行,但是可能会影响执行环境,导致意料之外的影响。填充可以避免这些未知影响。

## Reference
- [# 使用Hexview处理MCU hex文件](https://zhuanlan.zhihu.com/p/605509950)
- [# hexview合成hex，命令行模式](https://zhuanlan.zhihu.com/p/603159794)
- [# 采用hexview的HEX文件合成](https://blog.csdn.net/kxajd001/article/details/132531632)

- [# 《嵌入式硬件/软件开发刷写/烧录文件》专栏](https://blog.csdn.net/qfmzhu/category_12260357.html)
- [# 【嵌入式烧录/刷写文件】-2.2-合并两个Intel Hex文件](https://blog.csdn.net/qfmzhu/article/details/129840035)
- [# 【嵌入式烧录/刷写文件】-2.5-Fill填充Intel Hex文件](https://blog.csdn.net/qfmzhu/article/details/130809765)

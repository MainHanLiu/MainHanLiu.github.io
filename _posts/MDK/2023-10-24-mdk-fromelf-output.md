---
title: "KEIL - After Build使用fromelf或运行脚本"
description: 
date: 2023-10-24 10:00:00 +0800
categories: [MDK]
tags: [MDK, fromelf]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## keil 将o文件和bin文件分开，bin文件输出至指定文件夹  

设置路径： keil-User-After Build
```
[] fromelf --bin !L --output ../../output/output.bin

[] ..\..\tools\pcs_cmu\****.bat  
// 附上签名信息脚本
```

```
fromelf --i32 !L --output ../../output/output.hex
```

`$L`、`@L`、`L`用来指定对应的路径或名称。  
1. `L`是指axf文件路径，加文件名。  
2. `$L`是指axf的文件路径，不含文件名。  
3. `@L`是指axf的文件名，不含axf的后缀。  
>比如：生成`D:\1\out\aa.axf`文件，那么  
`L`的内容为：`D:\1\out\aa.axf`  
`$L`的内容为：`D:\1\out\` （包含最后的“\”）  
`@L`的内容为：`aa`  

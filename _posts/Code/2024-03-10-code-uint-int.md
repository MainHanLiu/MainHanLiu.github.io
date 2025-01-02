---
title: "int uint的存储形式与解读方式"
description: 
date: 2024-03-10 10:00:00 +0800
categories: [Code, Common]
tags: [Code, Common]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## 存储形式与解读方式

`int` `uint` 不论赋值正负，其在内存中的十六进制补码表达是一样的  
赋值为正时，其十进制解读相同  
赋值为负时，int正确解读，`uint`被解读为强制转换后的正值  
```
int32_t  in50  = 50;
uint32_t uin50 = 50;

十六进制：
in50  : 0x00000032
uin50 : 0x00000032

十进制：
in50  : 50
uin50 : 50
```

```
例：
int32_t  in50  = -50;
uint32_t uin50 = -50;

十六进制：
in50  : 0xFFFFFFCE
uin50 : 0xFFFFFFCE

十进制：
in50  : -50
uin50 : 4294967246
```

## uint16在0~32767范围内可以强制转换成int16进行比较

| 十六进制 | uint16 | int16 |
|--|--|--|
| 0x0000 | 0 | 0 |
| 0x0001 | 1 | 1 |
| ... | ... | ... |
| 0x7FFE | 32766 | 32766 |
| 0x7FFF | 32767 | 32767 |
|  |  |  |
| 0x8000 | 32768 | -32768 |
| 0x8001 | 32769 | -32767 |
| 0x8002 | 32770 | -32766 |
| ... | ... | ... |
| 0xFFFD | 65533 | -3 |
| 0xFFFE | 65534 | -2 |
| 0xFFFF | 65535 | -1 |
| 0x0000 | 65536 | 0 |

```c
/**
 * @brief       参数有效性校验
 * @param       [in] p_data 待校验参数
 * @param       [in] size   待校验参数的'字'数
 * @param       [in] p_min  最小值变量
 * @param       [in] p_max  最大值变量
 * @return      int32_t
 * @retval      0，正常
 * @retval      >0, 超出范围的参数个数
 * @warning     uint16在0~32767范围内可以强制转换成int16进行比较,因此本接口比较的uint类型变量不能超过32767
 */
static int32_t _param_valid_check(const int16_t *p_data, const int16_t size, const int16_t *p_min, const int16_t *p_max)
{
    int32_t ret = 0;
    int16_t i   = 0;

    for (i = 0; i < size; ++i)
    {
        if (*p_data < *p_min || *p_data > *p_max)
        {
            ret += 1;
        }
        p_data++; 
        p_min++; 
        p_max++; 
    }

    return ret;
}
```

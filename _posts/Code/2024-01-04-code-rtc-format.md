---
title: "RTC系统时间格式(BCD/BIN)"
description: 
date: 2024-01-04 10:00:00 +0800
categories: [Code, Common]
tags: [Code, Common, RTC]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## 函数原型
```c
/**
 * @brief        设置RTC时间 
 * @param        [in] rtc_format 时间格式
 * -# RTC_BIN_FORMAT ((uint32_t)0x000000000)
 * -# RTC_BCD_FORMAT ((uint32_t)0x000000001)
 * @param        [in] p_time rtc时间结构体  
 * @return        执行结果
 * @retval        SF_OK 成功  
 * @retval        <0 失败，详情见 sofar_errors.h
 */
int32_t sdk_rtc_set(uint32_t rtc_format, sdk_rtc_t *p_time);


/**
 * @brief        读取RTC时间 
 * @param        [in] rtc_format 时间格式
 * -# RTC_BIN_FORMAT ((uint32_t)0x000000000)
 * -# RTC_BCD_FORMAT ((uint32_t)0x000000001)
 * @param        [out] p_time rtc时间结构体
 * @return        执行结果
 * @retval        SF_OK 成功  
 * @retval        <0 失败，详情见 sofar_errors.h   
 */
int32_t sdk_rtc_get(uint32_t rtc_format, sdk_rtc_t *p_time);
```

## RTC系统时间设置

set的时间格式决定传进去的初始值，get可以按照BIN或者BCD来取，但是需要按照指定的解释方式  
- **BIN模式 （58分表示为0x3A分） 需要按照`"%02d"`解释显示**

| 实际时间 |`.tm_sec` （BIN模式）| 十进制`"%02d"`解释显示 |
|--|--|--|
| 57s | 0x39 | 57 |
| 58s | 0x3A | 58 |
| 59s | 0x3B | 59 |
| 00s | 0x00 | 00 |

- **BCD模式 （58分表示为0x58分） 需要按照"%02x"解释显示**

| 实际时间 |`.tm_sec` （BCD模式）| 十六进制`"%02x"`解释显示 |
|--|--|--|--|
| 57s | 0x57 | 57 |
| 58s | 0x58 | 58 |
| 59s | 0x59 | 59 |
| 00s | 0x00 | 00 |

## 获取时间并加载进字符串
```c
char time_str[20] = {0};
sdk_rtc_get(RTC_BIN_FORMAT, (sdk_rtc_t *)&g_rtc_time);
sprintf(time_str ,"20%02d-%02d-%02d %02d:%02d:%02d\0",
        g_rtc_time.tm_year,
        g_rtc_time.tm_mon,
        g_rtc_time.tm_day,
        g_rtc_time.tm_hour,
        g_rtc_time.tm_min,
        g_rtc_time.tm_sec);
```

## RTC系统时间 64 65 异常值显示问题分析
**现象**：MCU1不断下发3070指令，分或者秒出现64 65等异常值但大部分显示正常。  

**原因**：  
BIN模式 （58分表示为0x3A分）  
BCD模式（58分表示为0x58分）  

SCI下发的时间是BIN格式，而set和get使用了BCD模式，但是打印时使用"%02d"。  

SCI接收到的year为0x18，设置进去也为0x18，读回来也为0x18，但是按照"%02d"解析，就变成了24，因此看似正确，实际上BCD模式下应该按照"02x"解析。

设置BCD模式却用"%02d"解析时，实际时间为57s时，.tm_sec实际数值为0x39，按照BCD模式设置进去也为0x39，因为BCD模式下会从0x39跳到0x40 0x41，但是0x40 0x41按照"%02d"解析为64 65，因此会出现64 65的显示（BIN模式下0x39跳到0x3A，0x3B，0x3B，0x00）。  

因为SCI不断进行时间下发，因此时间很快又被矫正回去，若在进行一次下发后，持续打印RTC时间，会打印出64 65 66 ... 至 89。

按照BCD模式设置时:

| 实际时间 |.tm_sec在BCD模式下实际数值变化| 十六进制"%02x”解释显示 | 十进制"%02d”解释显示 |
|--|--|--|--|
| 57s | 0x39 | 39 | 57 |
| 58s | 0x40 | 40 | 64 |
| 59s | 0x41 | 41 | 65 |

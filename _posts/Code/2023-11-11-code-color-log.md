---
title: "串口调试日志封装：日志开关+定位信息+颜色显示"
description: 
date: 2023-11-11 10:00:00 +0800
categories: [Code, Common]
tags: [Code, Common]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---


## 串口输出附加定位信息
```c
sdk_log_i("["__FILE__"][Line: %d][%s]: enter!\n", __LINE__, __func__);
```

其中，`__FILE__`, `__LINE__`, `__func__`都是C语言的预定义符号，他们都在头文件`stdio.h`中。  
`__FILE__`: 当前源文件名，表示为字符串常量；  
`__LINE__`: 当前行号，表示为十进制整型常量；  
`__func__`: 当前函数名，表示为字符串常量。  

```c
// sdk_log_i 附加定位信息打印封装
#define ESS_LOG__DEBUG
#ifdef ESS_LOG__DEBUG
  #define filename(x)             (strrchr(x, '\\') ? strrchr(x, '\\') + 1 : x)
  #define normal_log(format, ...) sdk_log_i("\033[32m [%s-Line:%d][%s]: " format "\033[32;0m\r\n", filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
  #define warn_log(format, ...)   sdk_log_w("\033[33m warn-[%s-Line:%d][%s]: " format "\033[32;0m\r\n", filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
  #define err_log(format, ...)    sdk_log_e("\033[31m error-[%s-Line:%d][%s]: " format "\033[32;0m\r\n", filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
#else
  #define normal_log(format, ...)
  #define warn_log(format, ...)
  #define err_log(format, ...)
#endif
```

## Sample 
```c
#ifndef __COLOR_LOG_H__
#define __COLOR_LOG_H__

#include "app_cfg.h"
#include "app_rtc_time.h"

#define COLOR_START          "\033["
#define COLOR_STOP           "\033[0m"    // 关闭所有属性，一般放在后面，这样只会影响局部字符串颜色格式

// 格式控制
#define COLOR_TYPE_BOLD      "1;"    // 粗体
#define COLOR_TYPE_UNDERLINE "4;"    // 下划线
#define COLOR_TYPE_BLINK     "5;"    // 闪烁
#define COLOR_TYPE_INVERSE   "7;"    // 反显
#define COLOR_TYPE_INVISIBLE "8;"    // 消隐

// 背景颜色
#define COLOR_BG_BLACK       "40;"    // 黑色
#define COLOR_BG_RED         "41;"    // 红色
#define COLOR_BG_GREEN       "42;"    // 绿色
#define COLOR_BG_BROWN       "43;"    // 黄色
#define COLOR_BG_BLUE        "44;"    // 蓝色
#define COLOR_BG_PURPLE      "45;"    // 洋红
#define COLOR_BG_BLUE2       "46;"    // 青色
#define COLOR_BG_GREY        "47;"    // 白色

// 字体颜色
// 自定义在宏定义中放入结束符m，因此必须将该字体颜色控制码将放至最后
#define COLOR_FG_BLACK       "30m"    // 黑色
#define COLOR_FG_RED         "31m"    // 红色
#define COLOR_FG_GREEN       "32m"    // 绿色
#define COLOR_FG_YELLOW      "33m"    // 黄色
#define COLOR_FG_BLUE        "34m"    // 蓝色
#define COLOR_FG_PURPLE      "35m"    // 洋红
#define COLOR_FG_BLUE2       "36m"    // 青色
#define COLOR_FG_WHITE       "37m"    // 白色

#if LOG_WITH_TIME
    #define TIME_PREFIX "[%02x-%02x %02x:%02x:%02x] "
    #define TIME_DATA   , g_rtc_time.tm_mon, g_rtc_time.tm_day, g_rtc_time.tm_hour, g_rtc_time.tm_min, g_rtc_time.tm_sec
#else
    #define TIME_PREFIX
    #define TIME_DATA
#endif


// sdk_log_i 附加定位信息打印封装
// 格式： \033[显示方式;字体色;背景色m
// 举例： \033[1;40;31m 表示粗体-黑底-红字
// 如缺省默认，表示结束颜色设置：\033[0m
#if  (ESS_LOG_SWITCH == 2)
    #define filename(x)             (strrchr(x, '\\') ? strrchr(x, '\\') + 1 : x)
    #define assert_log(format, ...) sdk_log_a(COLOR_START COLOR_FG_PURPLE TIME_PREFIX "ASSERT-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #define error_log(format, ...)  sdk_log_e(COLOR_START COLOR_FG_RED TIME_PREFIX "ERROR-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #define warn_log(format, ...)   sdk_log_w(COLOR_START COLOR_FG_YELLOW TIME_PREFIX "WARN-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #define info_log(format, ...)   sdk_log_i(COLOR_START COLOR_FG_GREEN TIME_PREFIX "INFO-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #define debug_log(format, ...)  sdk_log_d(COLOR_START COLOR_FG_WHITE TIME_PREFIX "DEBUG-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #define set_log(format, ...)    sdk_log_i(COLOR_START COLOR_FG_WHITE TIME_PREFIX "SET-[%d]\t" format COLOR_STOP "\r\n" TIME_DATA, __LINE__, ##__VA_ARGS__)
    #define expect_log(format, ...) sdk_log_i(COLOR_START COLOR_FG_GREEN TIME_PREFIX "EXP-[%d]\t" format COLOR_STOP "\r\n" TIME_DATA, __LINE__, ##__VA_ARGS__)
    #define status_log(format, ...) sdk_log_i(COLOR_START COLOR_FG_BLUE2 TIME_PREFIX "LOG-[%d]\t" format COLOR_STOP "\r\n" TIME_DATA, __LINE__, ##__VA_ARGS__)
    // 用于控制单板条件下未连接从设备警告信息过多的日志输出
    // 单板调试下设置输出级别为DEBUG,整机系统下设置输出级别为WARN,因此在单板条件下设置系统整体日志级别为INFO时即可屏蔽从设备失联警告信息
    #if SINGLE_BOARD
        #define weak_warn_log(format, ...) sdk_log_d(COLOR_START COLOR_FG_YELLOW TIME_PREFIX "WARN-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #else
        #define weak_warn_log(format, ...) sdk_log_w(COLOR_START COLOR_FG_YELLOW TIME_PREFIX "WARN-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #endif
#elif  (ESS_LOG_SWITCH == 1)
    #define filename(x)             (strrchr(x, '\\') ? strrchr(x, '\\') + 1 : x)
    #define assert_log(format, ...) sdk_log_a(COLOR_START COLOR_FG_PURPLE TIME_PREFIX "ASSERT-[%s:%d][%s]: " format COLOR_STOP "\r\n" TIME_DATA, filename(__FILE__), __LINE__, __func__, ##__VA_ARGS__)
    #define error_log(format, ...)
    #define warn_log(format, ...)
    #define info_log(format, ...)
    #define debug_log(format, ...)
    #define set_log(format, ...)
    #define expect_log(format, ...)
    #define status_log(format, ...)
    #define weak_warn_log(format, ...)
#else
    #define assert_log(format, ...)
    #define error_log(format, ...)
    #define warn_log(format, ...)
    #define info_log(format, ...)
    #define debug_log(format, ...)
    #define set_log(format, ...)
    #define expect_log(format, ...)
    #define status_log(format, ...)
    #define weak_warn_log(format, ...)
#endif

#endif

```

## Reference
- [# 串口输出，使用printf打出ANSI color的字体](https://blog.csdn.net/hustwf/article/details/89188213)  
- [# 串口调试日志打印颜色输出](https://blog.csdn.net/weixin_43940932/article/details/120651732)

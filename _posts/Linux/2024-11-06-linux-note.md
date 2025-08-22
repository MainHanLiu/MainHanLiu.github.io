---
title: "Linux Note"
description: 
date: 2024-11-06 10:00:00 +0800
categories: [Linux]
tags: [Linux, Note]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## Linux cmd manual
[# Linux命令大全(手册)  ](https://www.linuxcool.com/)

[# Linux 常用 C 函数参考手册](https://getiot.tech/manual/linux-c-functions/index.html# )

[# Linux C function() 参考手册](https://angela0.github.io/linux_c/)

[# C 标准库 - 参考手册](https://www.runoob.com/cprogramming/c-standard-library.html)

[# C 标准库 - 搜索](https://www.runoob.com/?s=snprintf)

## C/C++中文参考手册
[# C/C++中文参考手册(C++23标准) 离线chm最新版](https://blog.csdn.net/gitblog_09778/article/details/141944006)

[# C/C++中文参考手册-项目地址](https://gitcode.com/open-source-toolkit/851eb )

## ZLG资料
https://manual.zlg.cn/web/#/30/1095

## vim键位

https://www.cnblogs.com/RioTian/p/17947391

[vim速查图-适合打印](https://blog.csdn.net/BjarneCpp/article/details/110923086)

## 在文本模式与桌面GUI模式之间切换，以减少内存消耗  
[# 如何在 Ubuntu 或 Debian 上启动进入命令行](https://cn.linux-console.net/?p=9029)  

### 关闭图形界面
```bash
sudo systemctl set-default multi-user.target
```
### 打开图形界面
```bash
sudo systemctl set-default graphical.target
```

## Question
MCU1的tick上电不是从0开始的？从何开始？如何计算tick的时间？
>MCU2的tick上电为0,1tick=1ms，u32溢出需要四年

CAN的读写在两个线程不需要互斥吗？因为总线仲裁机制？在何处实现的？

## SIGCHLD
子进程结束时, 父进程会收到这个信号。
如果父进程没有处理这个信号，也没有等待(wait)子进程，子进程虽然终止，但是还会在内核进程表中占有表项，这时的子进程称为僵尸进程。这种情 况我们应该避免(父进程或者忽略SIGCHILD信号，或者捕捉它，或者wait它派生的子进程，或者父进程先终止，这时子进程的终止自动由init进程 来接管)。
### 使用signal(SIGCHLD, SIG_IGN)处理僵尸进程
通过signal(SIGCHLD, SIG_IGN)通知内核对子进程的结束不关心，由内核回收。如果不想让父进程挂起，可以在父进程中加入一条语句：signal(SIGCHLD,SIG_IGN);表示父进程忽略SIGCHLD信号，该信号是子进程退出的时候向父进程发送的。

## SIG_ING
忽略的意思

## kill(pid, 0)
这里要用到的信号为0，并不表示要关闭某个程序，而表示对进程运行状态进行监控，如果发现进程关闭或其他异常，将返回状态码1，反之，如果发现进程运行正常，将返回状态码0

## 共享内存

| | |
|--|--|
| 创建键值    | `key_t shmkey = ftok("/",'a');` |
| 创建共享内存 | `int32_t shmid = shmget(shmkey, sizeof(common_data_t), 0666|IPC_CREAT);` |
| 映射共享内存 | `common_data_t * shmaddr = (common_data_t*)shmat(shmid,0,0);` |


## 文件权限

| r | 只读 | 
| r+ | 可读写 |
| w | 只写 | 
| w+ | 可读写 |
| a | 只写 | 
| a+ | 可读写 |

| r r+ | 文件必须存在 |
| w w+ | 若文件存在则文件长度清为零，即该文件内容会消失；若文件不存在则建立该文件 |
| a a+ | 若文件存在，写入的数据会被加到文件尾，即文件原先的内容会被保留。若文件不存在则会建立该文件 |

上述的形态字符串都可以再加一个 b 字符，如 rb、w+b 或 ab+ 等组合，加入 b 字符用来告诉函数库打开的文件为二进制文件，而非纯文字文件。

## 查看系统架构（CPU 类型）
```bash
uname -m 
```

|输出|架构|
|--|--|
|i686|x86_32 版本|
|x86_64|x86_64 版本|
|armv7l|ARM 32 位|
|aarch64|ARM 64 位|

## 从文件中读取配置并设置网关
```C
void set_gw_eth0() {
    FILE *file;
    char line[100];
    char gateway[20] = "";

    file = fopen("/etc/network/interfaces", "r");
    if (file == NULL) 
    {
        return;
    }

    while (fgets(line, sizeof(line), file)) 
    {
        // 逐行读取文件内容

        if (strstr(line, "iface eth0") != NULL) 
        {
            // 检查当前行是否包含字符串"iface eth0"，即是否定义了eth0接口

            while (fgets(line, sizeof(line), file)) 
            {
                if (strstr(line, "gateway") != NULL) 
                {
                    if (sscanf(line, "%*s %19s", gateway) == 1) {
                        // 使用sscanf函数从当前行中提取网关地址:
                        // 格式字符串中的"%*s"表示跳过第一个单词gateway
                        // "%19s"表示将接下来的最多19个字符存储在gateway数组中。
                        break;
                    }
                }
            }
            break;
        }
    }
    
    fclose(file);

    if (gateway[0] != '\0') {
        // 判断是否成功获取到了有效的网关地址

        char command[100];

        // 构造一条系统命令字符串
        snprintf(command, sizeof(command), "route add default gw %s eth0", gateway);

        if (system(command) == -1) {
            //  调用system()函数执行该命令来添加新的默认路由项
            return;
        }
        printf("eth0 Default route set to gateway: %s\n", gateway);
    }
}
```

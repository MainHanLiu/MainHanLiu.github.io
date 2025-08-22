---
title: "关闭并重定向标准输入输出"
description: 
date: 2025-02-07 10:00:00 +0800
categories: [Linux]
tags: [Linux]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

/dev/null 是 Linux/Unix 系统的“黑洞”文件，向其写入的任何数据都会被丢弃，从中读取的数据总是 EOF（文件结束）。

close_standard_io() 主要用于关闭标准输入、标准输出和标准错误，并将它们重定向到 /dev/null，以避免守护进程（Daemon）或子进程干扰终端。

```C
void close_standard_io()
{
    int fd = -1;
    if ((fd = open("/dev/null", O_RDWR)) == -1) 
    {
        return;
    }

    // 将 /dev/null 复制到标准输入（STDIN_FILENO = 0），使得进程从标准输入读取数据时，只会读取到 /dev/null，即不会有输入。
    dup2(fd, STDIN_FILENO); 
    // 将 /dev/null 复制到标准输出（STDOUT_FILENO = 1），进程的标准输出会被丢弃，不会显示在终端上。
    dup2(fd, STDOUT_FILENO);
    // 将 /dev/null 复制到标准错误（STDERR_FILENO = 2），进程的错误信息也会被丢弃，不会显示在终端上。
    dup2(fd, STDERR_FILENO);

    close(fd);
    return;
}
```

### 为什么要这样做？
1. 守护进程（Daemon）必须与终端脱离
- 守护进程通常不应直接与终端交互，避免影响用户操作。
- 通过将标准输入、标准输出、标准错误重定向到 /dev/null，可以防止进程从终端获取输入或向终端输出数据。
2. 避免后台进程输出信息
- 当程序以后台模式运行（如 nohup 或 &），如果不重定向，输出可能仍然会打印到终端。
- 通过 dup2() 将 STDOUT 和 STDERR 重定向到 /dev/null，可以避免后台进程在终端输出信息。
3. 提高进程的稳定性
- 如果 stdout 或 stderr 仍然连接到终端，且终端关闭，进程可能会收到 SIGHUP 信号而被终止。
- 通过重定向，它不会受到终端状态的影响。

### 应用场景
- 守护进程（Daemon）初始化
- 子进程创建后避免影响主进程
- 后台服务运行时屏蔽日志
- 防止无意义的标准输入输出

### 改进建议
虽然 /dev/null 能够有效丢弃数据，但如果日志需要被记录，建议将 STDOUT_FILENO 和 STDERR_FILENO 重定向到日志文件，如：

```c
int fd = open("/var/log/mydaemon.log", O_WRONLY | O_CREAT | O_APPEND, 0644);
if (fd != -1) {
    dup2(fd, STDOUT_FILENO);
    dup2(fd, STDERR_FILENO);
    close(fd);
}
```
这样，日志仍然可以被存储，便于调试和问题排查。

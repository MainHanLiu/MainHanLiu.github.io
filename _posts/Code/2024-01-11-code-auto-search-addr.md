---
title: "485设备自动寻址代码"
description: 
date: 2024-01-11 10:00:00 +0800
categories: [Code, Common]
tags: [Code, Common]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

对某一485通道挂载的设备地址不确定/修改了但不记得，可使用此方式遍历寻址

```c
static int32_t search_addr = 0;
for ( ; search_addr <= 0xff; ++search_addr)
{
    ret = data_read(&recv_buf, search_addr);
    if (ret >= 0)
    {
        assert_log("searched! addr is 0x%02x", search_addr);
        break;
    }
    info_log("%02x, %d", search_addr, ret);

    sdk_os_delay(sdk_os_tick_from_millisecond(20));
    sys_send_heartbeat_to_sys_manage(TASK_ID);
}
if (search_addr++ == 256)
{
    warn_log("all addr is wrong");
}
```

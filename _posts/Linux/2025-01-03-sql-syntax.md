---
title: "SQLite Syntax"
description: 
date: 2025-01-03 10:00:00 +0800
categories: [Linux]
tags: [Linux]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## Syntax
SQLite 是不区分大小写的，但也有一些命令是大小写敏感的，比如 GLOB 和 glob 在 SQLite 的语句中有不同的含义。

SQL 注释以两个连续的 `"-"` 字符（ASCII 0x2d）开始，并扩展至下一个换行符（ASCII 0x0a）或直到输入结束，以先到者为准。
您也可以使用 C 风格的注释，以`/*`开始，并扩展至下一个`*/`字符对或直到输入结束，以先到者为准。SQLite的注释可以跨越多行。

所有的 SQLite 语句可以以任何关键字开始，如 SELECT、INSERT、UPDATE、DELETE、ALTER、DROP 等，所有的语句以分号 `;` 结束。

SQLite支持列的亲和类型概念。任何列仍然可以存储任何类型的数据，当数据插入时，该字段的数据将会优先采用亲缘类型作为该值的存储方式。

|存储类  |  描述|
|--|--|
|NULL   | 值是一个 NULL 值。|
|INTEGER|值是一个带符号的整数，根据值的大小存储在 1、2、3、4、6 或 8 字节中。|
|REAL   | 值是一个浮点值，存储为 8 字节的 IEEE 浮点数字。|
|TEXT   | 值是一个文本字符串，使用数据库编码（UTF-8、UTF-16BE 或 UTF-16LE）存储。|
|BLOB   | 值是一个 blob 数据，完全根据它的输入存储。|

SQLite 没有一个单独的用于存储日期和/或时间的存储类，但 SQLite 能够把日期和时间存储为 TEXT、REAL 或 INTEGER 值。

|存储类	| 日期格式|
|--|--|
|TEXT	|格式为 "YYYY-MM-DD HH:MM:SS.SSS" 的日期。|
|REAL	|从公元前 4714 年 11 月 24 日格林尼治时间的正午开始算起的天数。|
|INTEGER	|从 1970-01-01 00:00:00 UTC 算起的秒数。|

您可以以任何上述格式来存储日期和时间，并且可以使用内置的日期和时间函数来自由转换不同格式。

WHERE 子句是用来设置 SELECT 语句的条件语句


触发器是一段在特定数据库操作（如 INSERT、UPDATE、DELETE）发生时自动执行的代码。常用于实现数据一致性检查、日志记录、级联操作等
```sql
CREATE TRIGGER [IF NOT EXISTS] trigger_name 
   [BEFORE | AFTER | INSTEAD OF] [INSERT | UPDATE | DELETE] 
   ON table_name
   [FOR EACH ROW] 
   [WHEN condition]
BEGIN
   -- 触发器逻辑（SQL 语句）
END;
```

## SQL
```c
sdk_rtc_get(RTC_BIN_FORMAT, &now);  //获取当前时间
snprintf(date_format, sizeof(date_format), "%04d-%02d-%02d", now.tm_year + 2000, now.tm_mon, now.tm_day);
snprintf(sql, sizeof(sql), "SELECT strftime('%%H', date), charge, discharge FROM charge_data WHERE date LIKE '%s%%';",date_format);
```
> %% 是 %的转义写法

在 SQLite 中，**结果集的列顺序由 `SELECT` 子句中的字段顺序决定**。


### 1. **SQL 查询决定了列顺序**
代码中构建的 SQL 语句为：
```sql
SELECT strftime('%H', date), charge, discharge 
FROM charge_data 
WHERE date LIKE '2024-06-09%';
```

`strftime('%H', date)` 的作用  
- `strftime` 是 SQLite 的日期时间格式化函数，`%H` 表示提取 **24 小时制的小时**。
  - 例如，若 `date` 字段值为 `2024-06-09 14:30:00`，`strftime('%H', date)` 返回字符串 `"14"`。
- 代码中通过 `atoi(sqlite3_column_text(stmt, 0))` 将其转换为整数 `14`。

`date LIKE '2025-02-20%%'` 的作用  
- 对date通配前缀为2025-02-20的列


- `SELECT` 子句的字段顺序为：
  1. `strftime('%H', date)`：提取 `date` 字段的小时（如 `14`）。
  2. `charge`：充电量。
  3. `discharge`：放电量。

- **结果集的列索引**：
  - **第 0 列**：`strftime('%H', date)`（小时）。
  - **第 1 列**：`charge`。
  - **第 2 列**：`discharge`。

因此，当调用 `sqlite3_column_text(stmt, 0)` 时，获取的是第一列（小时值）。


假设 `charge_data` 表中有以下数据：

| date                | charge | discharge |
|---------------------|--------|-----------|
| 2024-06-09 14:00:00 | 100    | 50        |
| 2024-06-09 15:00:00 | 120    | 60        |

执行查询后，结果集为：

| strftime('%H', date) | charge | discharge |
|----------------------|--------|-----------|
| 14                   | 100    | 50        |
| 15                   | 120    | 60        |

- 第一列是 `strftime('%H', date)` 生成的小时值。

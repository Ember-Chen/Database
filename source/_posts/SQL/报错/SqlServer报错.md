---
title: SqlServer报错
date: 2024-05-08 19:40:23
tags: 报错
categories: [SQL,报错]
---

# SqlServer报错
- 无法删除/调整字段
    ```sql
    -- 语句
    ALTER TABLE student
	    ALTER COLUMN sex CHAR(5);
    /* 报错
    对象'DF__student__sex__09946309' 依赖于 列'sex'。
    由于一个或多个对象访问此 列，ALTER TABLE ALTER COLUMN sex 失败。
    */
    ```
    - 原因分析：该字段原先有`DEFAULT`约束
    - 解决方式：删除该约束，约束名见报错(`DF__...`)
    <!-- more -->
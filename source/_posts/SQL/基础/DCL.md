---
title: DCL
date: 2024-05-08 20:32:22
tags: DCL
categories: [SQL,基础]
---

# 授权
```sql
GRANT <权限>[,<权限>] ...[ON <对象类型> <对象名>]
TO <用户>[,<用户>] ...[WITH GRANT OPTION]
```
<!-- more -->
- [e.g.]
    ```sql
    GRANT SELECT ON TABLE Student TO usr1;

    GRANT ALL PRIVILIGES ON TABLE student,course TO usr2,usr3;

    GRANT SELECT ON TABLE SC TO PUBLIC;
    ```
- 允许授权传递 `WITH GRANT OPTION`
    ```sql
    GRANT INSERT ON TABLE sc TO usr5 WITH GRANT OPTION
    ```


# 收回授权
```sql
REVOKE <权限>[,<权限>]…[ON <对象类型> <对象名>]
    FROM <用户>[,<用户>]
```


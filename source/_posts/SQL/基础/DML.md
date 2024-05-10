---
title: DML
date: 2024-05-08 20:30:02
tags: DML
categories: [SQL,基础]
---

# DML

## 插入数据

### 常规插入元组

```sql
INSERT INTO <表名> (<属性列1>,<属性列2 >,...)
VALUES 
(...),
...,
(...);
```
<!-- more -->
- [e.g.] 将(95020,陈冬,男,IS,18岁) 插入到Student表中

  ```sql
  INSERT INTO student
  VALUES (‘21020’,'陈冬','男','IS',18);
  ```



### 对表插入子查询结果

- 只能插入**现有表**

- [e.g.] 在student表中，求各个系的学生的平均年龄，并把结果存入`stu_info`表

  ```sql
  INSERT INTO stu_info(dept, avg_age)
  	SELECT dept,AVG(age) FROM student GROUP BY dept;
  ```



### 将子查询结果插入表

```sql
SELECT <...>
	INTO <表名>
```

- 可以插入现有表，局部临时表(#)，全局临时表(##)





## 更新数据

```sql
UPDATE <表名> SET <字段>=<值> WHERE <条件>;
```

- [e.g.] 将学生21001的年龄改为22岁

  ```sql
  UPDATE student SET sage=22 WHERE Sno='21001';
  ```

- 使用`EXISTS`

  - [e.g.] 将计算机系的全体学生的成绩置零

  ```sql
  UPDATE sc SET grade=0 WHERE EXISTS(
  	SELECT 1 FROM stu WHERE sc.sno=stu.sno AND stu.dept='CS'
  )
  ```





## 删除数据

```sql
DELETE FROM <表名> [WHERE <条件>];
```





## 易错点总结

- DML中的动词关键字
  - `INSERT INTO...VALUES`,`UPDATE <表名> SET`,`DELETE FROM`
  - DDL中删除操作: `DROP`; DML中删除操作: `DELETE`
  - DDL中修改操作: `ALTER`; DML中修改操作: `UPDATE <表名> SET`




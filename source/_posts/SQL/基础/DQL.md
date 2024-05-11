---
title: DQL
date: 2024-05-08 20:37:48
tags: DQL
categories: [SQL,基础]
---

# 普通语法

- 含表达式的查询

  - [e.g.] 查全体学生的姓名及其出生年份(表中只有Sage字段)

    ```sql
    SELECT Sname, year(getadate)-Sage FROM students;
    ```

<!-- more -->

- 自定义查询结果列名

  ```sql
  SELECT sname name FROM student;
  ```

- `DISTINCT`-去重

  - `DISTINCT`关键字在一句查询中只能出现1次

    ```sql
    SELECT DISTINCT(sno) FROM sc;
    ```

- `ORDER BY`排序

  - [e.g.] 按成绩降序排序

    ```sql
    SELECT * FROM sc ORDER BY grade DESC;
    ```

- `TOP`限制显示条数

  - 格式

    ```sql
    TOP n [PERSENT] [WITH TIES];
    ```

  - `TOP n`:			表示取查询结果的前n行

  - `TOP n PERSENT`:	表示取查询结果的前n%行

  - `WITH TIES`		显示并例的结果

  - `TOP` 一定要搭配 `ORDER BY` 使用才有意义

  - [e.g.] 查询年龄最大的三个学生的姓名

    ```sql
    SELECT TOP 3 sname FROM student ORDER BY sage DESC;
    ```

  - [e.g.] 查询成绩前3的学生姓名，包含并列

    ```sql
    SELECT TOP 3 WITH TIES sname FROM student ORDER BY sgrade DESC;
    ```

- `CASE`对结果进行处理

  - [e.g.] 查询001号课程的学号和成绩，并进行如下处理成绩>90 显示优。80-89:良

    ```sql
    SELECT sno,
    CASE
        WHEN grade>=90 THEN ‘优’
        WHEN grade BETWEEN 80 AND 89 THEN ‘良’
        WHEN grade BETWEEN 70 AND 79 THEN ‘中’
        WHEN grade BETWEEN 60 AND 69 THEN ‘及格’
        WHEN grade<60 THEN ‘不及格’
    End AS degree
    FROM sc
    WHERE cno=’001’ AND grade IS NOT NULL;
    ```

- `UNION`查询结果进行**并**操作

  ```sql
  <查询块1>
  UNION
  <查询块2>
  ```

  - [e.g.] 查询所有老师和同学的 姓名、性别和生日

    ```sql
    SELECT tname AS name,tsex AS sex,birth FROM teacher
    UNION
    SELECT sname,sex,birth FROM student;
    // 注意这个`AS`
    ```


# 聚合查询
-  只有count是对行的累加计数，其他聚合函数都是对某一列的计算
-  **只有count不忽略null**，其他都会忽略



# 字符串匹配

- `LIKE` 等价于 `=`; `NOT LIKE` 等价于 `!=`  // 用LIKE 更好

- 通配符`%`: 任意长度的**字符串**

- 通配符`_`: 任意的**字符**

- 转义字符`\`

- `[xyz]`: 符合x,y,z任意一个即可

- [e.g.] 找出所有姓张/陈/李的

  ```sql
  SELECT * FROM student WHERE sname LIKE '[张陈李]%';
  ```





# 表连接查询

- 等值连接

  - 等值连接中，会存在重复的列

  - [e.g.]

    ```sql
    SELECT Student.*, SC.* FROM Student,SC WHERE Student.Sno=Sc.Sno;
    ```

- 自然连接

  - 自然连接会去重

  - [e.g.]

    ```sql
    SELECT SStudent.Sno,Sname,Ssex,Sage,Sdept,Cno,Grade
    	FROM SStudent,SC WHERE SStudent.Sno = SC.Sno;
    ```

- 自身连接

  - 给自身多个不同的**别名**，以示区别

  - [e.g.]

    ```sql
    SELECT FIRST.Cno, SECOND.Cpno FROM SCourse FIRST, SCourse SECOND
    WHERE FIRST.Cpno=Scond.Cno;
    ```

- 综合例题

  - [e.g.] 查询每个学生的学号、姓名、选修的课程名及成绩

    ```sql
    /*
    共有3张表，Student, SC, Course
    Student的属性: Sno, Sname
    SC的属性: Cno, Sno, grade
    Course的属性: Cno, Cname
    */
    SELECT Student.Sno,Sname,Cname,grade
    FROM Student,SC,Course
    WHERE Student.Sno=SC.Sno AND SC.Cno=Course.Cno;
    ```



# 嵌套查询

- 不相关子查询

  - 从里至外，先一次性全部完成内部的查询，然后进行外部的查询

- 相关子查询

- [e.g.] 查询选择课程2的学生的姓名

  ```sql
  SELECT Sname FROM Student
  WHERE Sno IN (SELECT Sno FROM SC WHERE Cno=’2’);
  ```

- [e.g.] 查询与刘晨在同一个系学习的学生

  ```sql
  SELECT Sname FROM Student
  WHERE Sdept=(SELECT Sdept FROM Student WHERE Sname=‘刘晨’);
  ```

- [e.g.] 和学号为“108”的同学同年出生的所有学生的sno、sname和 birthday列

  ```sql
  SELECT sno,sname,birth FROM student WHERE YEAR(birth)=(
  	SELECT YEAR(birth) FROM student WHERE sno='108'
  );
  ```



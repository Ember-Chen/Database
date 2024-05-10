---
title: DDL
date: 2024-05-08 19:27:53
tags: DDL
categories: [SQL,基础]
---

# 表

## 创建表

```sql
CREATE TABLE table(
    字段名  数据类型  约束,
    ...
);
```
<!-- more -->
- 数据类型

  - `CHAR(fix_len), VARCHAR(max_len), DECIMAL(precision, float)`

- 常用约束

  - `NOT NULL, PRIMARY KEY, CHECK(expression), DEFAULT xxx, ...`

- 直接定义外键约束

  ```sql
  CREATE TABLE <表名>(
      ...
      <字段名> <类型> REFERENCES <外表名>(<外表字段>),
      ...
  );
  ```

  ```sql
  CREATE TABLE order(
      ...
      sid INT REFERENCES customer(sid), 
      ...
  );
  ```

- 整体添加外键约束

  ```sql
  FOREIGN KEY (此表字段名) REFERENCES 被参照表表名(被参照表中的字段名);
  ```

  ```sql
  CREATE TABLE emp (
      ...
      FOREIGN KEY (emp_dept_id) REFERENCES departments(dept_id)
      ...
  );
  // 任一employee实体的emp_dept_id 必须存在于departments表中
  ```


- 整体添加普通约束

  ```sql
  CREATE TABLE cnst_example (
      salary MONEY NOT NULL,
      age INT,
      ...
      CONSTRAINT my_check CHECK (salary < 100000 AND age>18)
  );
  ```

- 综合示例

  ```sql
  CREATE TABLE my_tb(
      sno CHAR(5) PRIMARY KEY,
      phone CHAR(11) UNIQUE,
      cno CHAR(5) REFERENCES FROM class(cno),
      ono CHAR(5),
      sex CHAR(2),
      CONSTRAINT bi_sex CHECK(sex IN (‘男’,’女’))
      FOREIGN KEY (ono) REFERENCES org(ono)
  );
  ```

  


## 修改表

- 添加新字段

  ```sql
  ALTER TABLE <表名>
  	ADD <新列名> <数据类型> [完整性约束];
  ```

  ```sql
  ALTER TABLE orders
  	ADD buyer_id INT CHECK(buyer_id>0) --列名+数据类型+完整性约束
  	FOREIGN KEY(buyer_id) REFERENCES buyers(id);
  ```

- 删除已有字段

  ```sql
  ALTER TABLE <表名>
  	DROP COLUMN <字段名>;
  ```

- 修改已有字段

  ```sql
  ALTER TABLE <表名>
  	ALTER COLUMN <字段名> <新数据类型> <约束>
  ```

  ```sql
  ALTER TABLE student
    ALTER COLUMN sage SMALLINT NOT NULL;
  ```

- 为字段添加默认值

  ```sql
  ALTER TABLE <表名>
    ADD DEFAULT(<默认值>) FOR <字段名>;
  ```

  ```sql
  ALTER TABLE em
    ADD DEFAULT('1970-1-1') FOR birth;
  ```



## 删除表

```sql
DROP TABLE <表名>;
```





# 约束

- 创建约束

  ```sql
  ALTER TABLE <表名>
  	ADD CONSTRAINT <约束名> CHECK (...);
  ```

- 删除已有的完整性约束

  ```sql
  ALTER TABLE <表名>
  	DROP CONSTRAINT <约束名>;
  ```

- 添加未验证的约束(`WITH NO CHECK`)

  ```sql
  INSERT INTO doc_exd VALUES (-1);
  ALTER TABLE doc_exd WITH NOCHECK
  	ADD CONSTRAINT exd_check CHECK (column_a > 1);
  // 如果不加NOCHECK关键字，新添加的验证会对原有数据报错
  ```





# 规则

- 约束 vs. 规则

  - 约束只能针对**单张表**建立
  - 规则可以运用于**多张表**上
  - 即: 可以将多张表的**共同约束**提炼成为**规则**

- 创建规则

  ```sql
  CREATE RULE <rule_name> AS
  <规则约束>
  ```

  ```sql
  CREATE RULE binary_sex AS
  @sex IN ('男','女'); 
  ```





# 索引

## 创建索引

```sql
CREATE [UNIQUE] INDEX <索引名> ON <表名>(列名1 次序, 列名2 次序, ...)
// 升序ASC,降序DESC;	缺省ASC
```

[e.g.] 为students表按”学号sno升序,课程号cno降序”创建唯一索引

```sql
CREATE UNIQUE INDEX my_index ON students(sno ASC, cno DESC);
```

 

## 重建索引

```sql
ALTER INDEX <索引名> ON <表名> REORGANIZE;
```



## 删除索引

```sql
DROP INDEX <索引名> ON <表名>;
```





# 视图

- 创建视图

  - 创建视图时，SELECT语句并不执行；在查询视图时，该语句才会进行

  - 视图的列名要么不指定，要么全部指定；特定情况下必须指定（有集函数，有冲突）

  - 包含WITH CHECK OPTION时，对视图的更新操作，只会作用于视图内包含的数据

    ```sql
    CREATE  VIEW  <视图名>  [(<列名> [,<列名>]…)] AS
    <子查询>  [WITH CHECK OPTION];
    ```

- 查询/更新视图

  - 大体和表类似

- `WITH CHECK OPTION`

  - [e.g.] 建立信息系学生的视图，并要求透过该视图进行的更新操作只涉及信息系学生

    ```sql
    CREATE VIEW IS_Student AS
    	SELECT Sno, Sname, Sage FROM Student WHERE Sdept='IS' WITH CHECK OPTION
    // 对视图的更新，删除操作，都会加上Sdept=’IS’的条件限定
    // 对视图的插入操作，若Sdept为空，则自动将Sdept设为’IS’；若不满足Sdept=’IS’，则拒绝插入操作
    ```

- 多表视图

  - [e.g.] 建立信息系选修了1号课程的学生视图

    ```sql
    CREATE VIEW IS_1_Stu (Sno,Sname,Grade) AS
    
    SELECT Student.Sno,Sname,Grade FROM Student,SC
    
    WHERE Student.Sno=SC.Sno AND Sdept=’IS’ AND Cno=’1’;
    ```

- 基于视图的视图

  - [e.g.] 建立信息系选修了1号课程且成绩在90分以上的学生视图(已有信息系选修了1号课程的学生视图)

    ```sql
    CREATE VIEW is_s2 AS
    	SELECT sno,sname,grade FROM is_s1 WHERE grade>=90; 
    ```

- 含表达式的视图

  - [e.g.] 定义一个反映学生出生年份的视图

    ```sql
    CREATE VIEW BT_S(Sno,Sname,Sbirth)
    
    AS SELECT Sno,Sname,2021-Sage FROM Student;
    ```

- 含分组的视图

  ```sql
  CREAT VIEW S_G(Sno,Gavg) AS
  	SELECT Sno，AVG(Grade)  FROM SC GROUP BY Sno;
  ```

- 基本概念

  - 虚表，是从一个或几个基本表(或视图)导出的表
  - 视图只是对原有数据的一种显示方式，并不会复制一份多余的数据
  - 基表中的数据发生变化，从视图中查询出的数据也随之改变
  - 以 SELECT * 方式创建的视图，其可扩充性差，应尽可能避免

- 视图的作用

  - 在保持外模式不变的情况下，重构数据库

    - 将学生关系Student(Sno,Sname,Ssex,Sage,Sdept)垂直地分成SX(Sno,Sname,Sage)和SY(Sno,Ssex,Sdept)两个关系，这时原表Student为SX表和SY表自然连接的结果。可以建立一个视图Student：

      ```sql
      CREATE VIEW  Student(Sno,Sname,Ssex,Sage,Sdept) AS
      	SELECT SX.Sno,SX.Sname,SY.Ssex,SX.Sage,SY.Sdept
      	FROM SX,SY WHERE SX.Sno=SY.Sno
      ```

    - 尽管数据库的逻辑结构改变了，但应用程序不必修改，因为新建立的视图定义了用户原来的关系，使用户的**外模式保持不变**，用户的应用程序通过视图仍然能够查找数据

  - 能够对机密数据提供安全保护

    - 对不同用户定义不同视图，使每个用户只能看到他有权看到的数据。通过WITH CHECK OPTION可以对关键数据定义操作时间限制


# 易错点总结
- `INDEX`相关操作，很容易遗忘`ON`；索引一定要依附于具体的表
    ```sql
    CREATE INDEX myidx ON student(sno ASC);

    ALTER INDEX myidx ON student REORGANIZE;

    DROP INDEX myidx ON studen;
    ```
- `VIEW`相关操作，容易遗忘`AS`，而且不用`ON`表
    ```sql
    CREATE VIEW myview (sno,birth) AS
        SELECT sno,birth FROM student WHERE sex='女';
    ```


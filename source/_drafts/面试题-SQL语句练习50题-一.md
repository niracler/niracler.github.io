---
title: 面试题|SQL语句练习50题(一)
tags:
    - SQL
---

我这里的练习题源于 https://blog.csdn.net/fashion2014/article/details/78826299/

<!-- more -->

## 表名和字段

- 学生表：Student(s_id, s_name, s_birth, s_sex) # 学生编号， 学生姓名， 出生年月， 学生性别
- 课程表：Course(c_id, c_name, t_id) # 课程编号，课程名称，教师编号
- 教师表：Teacher(t_id, t_name) # 教师编号， 教师姓名
- 成绩表：Score(s_id, c_id, s_score) # 学生编号， 课程编号， 成绩

## 建表

### 学生表

```sql
CREATE TABLE Student(
    s_id VARCHAR(20),
    s_name VARCHAR(20) NOT NULL DEFAULT '',
    s_birth VARCHAR(20) NOT NULL DEFAULT '',
    s_sex VARCHAR(10) NOT NULL DEFAULT '',
    PRIMARY KEY(s_id)
);
```

### 课程表

```sql
CREATE TABLE Course(
    c_id VARCHAR(20),
    c_name VARCHAR(20) NOT NULL DEFAULT '',
    t_id VARCHAR(20) NOT NULL,
    PRIMARY KEY(c_id)
)
```

### 教师表

```sql
CREATE TABLE Teacher(
    t_id VARCHAR(20),
    t_name VARCHAR(20) NOT NULL DEFAULT '',
    PRIMARY KEY(t_id)
);
```

### 成绩表

```sql
CREATE TABLE Score(
    s_id VARCHAR(20),
    c_id VARCHAR(20),
    s_score INT,
    PRIMARY KEY(s_id, c_id)
);
```

## 测试数据

### 插入学生表测试数据

```sql
INSERT INTO Student VALUES('01' , '赵雷' , '1990-01-01' , '男');
INSERT INTO Student VALUES('02' , '钱电' , '1990-12-21' , '男');
INSERT INTO Student VALUES('03' , '孙风' , '1990-05-20' , '男');
INSERT INTO Student VALUES('04' , '李云' , '1990-08-06' , '男');
INSERT INTO Student VALUES('05' , '周梅' , '1991-12-01' , '女');
INSERT INTO Student VALUES('06' , '吴兰' , '1992-03-01' , '女');
INSERT INTO Student VALUES('07' , '郑竹' , '1989-07-01' , '女');
INSERT INTO Student VALUES('08' , '王菊' , '1990-01-20' , '女');
```

### 插入课程表测试数据

```sql
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
```

### 插入教师表测试数据

```sql
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
```

### 插入成绩表测试数据

```sql
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```

## 练习题和SQL语句

### 1 查询"01"课程成绩比"02"课程成绩高的学生的信息以及课程分数

```sql
SELECT stu.* , sa.s_score as score1, sb.s_score as score2
FROM score sa, score sb , student stu
WHERE sa.s_id=sb.s_id AND stu.s_id=sa.s_id
AND sa.c_id='01'
AND sb.c_id='02'
AND sa.s_score > sb.s_score;
```

### 2 查询"01"课程成绩比"02"课程成绩低的学生的信息以及课程分数

```sql
SELECT stu.* , sa.s_score as score1, sb.s_score as score2
FROM score sa, score sb , student stu
WHERE sa.s_id=sb.s_id AND stu.s_id=sa.s_id
AND sa.c_id='01'
AND sb.c_id='02'
AND sa.s_score < sb.s_score;
```

### 3 查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```sql
SELECT stu.s_name,  sc.s_id, AVG(s_score)
FROM score sc, student stu
WHERE stu.s_id=sc.s_id
GROUP BY sc.s_id, stu.s_name
HAVING AVG(s_score) >= 60;
```

### 4 查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩 -- (包括有成绩的和无成绩的)

```sql
SELECT stu.s_id, stu.s_name,  AVG(sc.s_score)
FROM student stu
LEFT JOIN score sc
ON stu.s_id=sc.s_id
GROUP BY stu.s_id
HAVING AVG(sc.s_score) < 60
UNION
SELECT a.s_id,a.s_name,0
FROM student a
WHERE a.s_id NOT IN (
SELECT DISTINCT s_id FROM score);
```

### 5 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

```sql
SELECT stu.s_id , stu.s_name, COUNT(sc.s_id), SUM(sc.s_score)
FROM student stu
LEFT JOIN score sc ON sc.s_id=stu.s_id
GROUP BY stu.s_id
ORDER BY stu.s_id;
```

### 6 查找姓李的老师的数量

```sql
SELECT COUNT(t_id) FROM teacher
WHERE t_name like '李%';
```

### 7 查询学过”张三“老师授课的同学的信息

```sql
SELECT student.s_id, s_name, s_birth, s_sex
FROM student JOIN score
ON score.s_id=student.s_id
WHERE c_id in (
    SELECT c_id FROM teacher
    INNER JOIN course
    ON course.t_id = teacher.t_id
    WHERE  t_name = '张三'
);
```

### 8 查询没有学过张三老师授课的同学的信息

```sql
SELECT * FROM student WHERE s_id not in (
    SELECT s_id FROM score
    WHERE c_id in (
        SELECT c_id FROM teacher
        INNER JOIN course
        ON course.t_id = teacher.t_id
        WHERE  t_name = '张三'
    )
);
```

### 9 查询学过编号为‘01’并且也学过编号为‘02’的课程的同学的信息

```sql
SELECT * FROM student
WHERE s_id in (SELECT s_id FROM score WHERE c_id='01')
AND s_id in (SELECT s_id FROM score WHERE c_id='02');
```

### 10 查询学过编号为‘01’但是没有学过编号为‘02’的课程的同学信息

```sql
SELECT * FROM student
WHERE s_id in (SELECT s_id FROM score WHERE c_id='01')
AND s_id not in (SELECT s_id FROM score WHERE c_id='02');
```

### 11

```sql

```

## 参考文章

1. [sql语句练习50题(Mysql版)](https://blog.csdn.net/fashion2014/article/details/78826299/)
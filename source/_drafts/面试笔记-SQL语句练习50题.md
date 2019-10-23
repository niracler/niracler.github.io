---
title: 面试题|SQL语句练习50题
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
CREATE TABLE `Student`(
    `s_id` VARCHAR(20),
    `s_name` VARCHAR(20) NOT NULL DEFAULT '',
    `s_birth` VARCHAR(20) NOT NULL DEFAULT '',
    `s_sex` VARCHAR(10) NOT NULL DEFAULT '',
    PRIMARY KEY(`s_id`)
);
```

### 课程表

```sql
CREATE TABLE `Course`(
    `c_id` VARCHAR(20),
    `c_name` VARCHAR(20) NOT NULL DEFAULT '',
    `t_id` VARCHAR(20) NOT NULL,
    PRIMARY KEY(`c_id`)
)
```

### 教师表

```sql
CREATE TABLE `Teacher`(
    `t_id` VARCHAR(20),
    `t_name` VARCHAR(20) NOT NULL DEFAULT '',
    PRIMARY KEY(`t_id`)
);
```

### 成绩表

```sql
CREATE TABLE `Score`(
    `s_id` VARCHAR(20),
    `c_id` VARCHAR(20),
    `s_score` INT(3),
    PRIMARY KEY(`s_id`, `c_id`)
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

### 1 查询"01"课程成绩高的学生的信息以及课程分数

## 参考文章

1. [sql语句练习50题(Mysql版)](https://blog.csdn.net/fashion2014/article/details/78826299/)
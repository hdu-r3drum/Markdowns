# SQL通用语法

- SQL语句可以单行或者多行书写，以分号结尾。
- SQL语句可以使用空格/缩进来增强语句的可读性
- MySQL数据库的SQL语句不区分大小写，关键字建议用大写

# SQL分类

- DDL Data Definition Language：数据定义
- DML Data Manipulation Language：数据操作
- DQL Data Query Language：数据查询
- DCL Data Control Language：数据控制，用来创建数据库用户，控制数据库访问权限

# DDL

- 查询数据库

```mysql
SHOW DATABASES
//查询所有数据库

SELECT DATABASE();
//查询当前数据库

CREATE DATABASE [IF NOT EXISTS] name [DEFAULT CHARSET] charset [COLLATE];
//创建

DROP DATABASE [IF EXISTS] name;
//删除

USE name;
//使用
```



- 表操作-查询

```mysql
SHOW TABLES;
//查询当前数据库所有表

DESC tablename;
//查询表结构

SHOW CREATE TABLE tablename;
//查询指定表的建表语句
```

- 表操作-创建

```mysql
CREATE TABLE tablename{
	字段1 字段1类型[COMMENT 字段1注释],
	字段2 字段2类型[COMMENT 字段2注释],
	字段3 字段3类型[COMMENT 字段3注释],
}[COMMENT 表注释];
```

Example code：根据需求创建一张员工信息表

```mysql
\\1 编号 纯数字
\\2 员工工号 字符串类型 长度不超过10位
\\3 员工姓名 字符串类型 长度不超过10位
\\4 性别 男/女
\\5 年龄
\\6 身份证号
\\7 入职时间

create table staff_table(
    -> id int comment "编号",
    -> work_number varchar(10) comment "员工工号",
    -> name varchar(10) comment "姓名",
    -> gender char(1) comment "性别",
    -> age tinyint unsigned comment "年龄",
    -> identity char(18) comment "身份证号",
    -> join_time date comment "入职时间"
    -> ) comment "练习：员工表";
```

- 表操作-表结构修改

```mysql
//添加字段
ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释][约束];

//修改数据类型
ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);

//修改字段名和字段类型
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释][约束];

//删除字段
ALTER TABLE 表名 DROP 字段名;

//修改表名
ALTER TABLE 表名 RENAME TO 新表名;

//删除表
DROP TABLE [IF EXISTS] 表名;
```

*关于约束的内容在基础.md中

# DML

- 添加数据

```mysql
//给指定的字段添加数据
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUSE (值1, 值2,...);

//给全部字段添加数据
INSERT INTO 表名 VALUES (值1, 值2,...);

//批量添加 结合上面两句
```

- 修改数据

```mysql
UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2,...[WHERE 条件];

DELETE FROM 表名 [WHERE 条件];
```



# DQL

- 语法

```mysql
SELECT 
	字段列表
FROM
	表名列表
WHERE
	条件列表
GROUP BY
	分组字段列表
HAVING
	分组后条件列表
ORDER BY
	排序字段列表
LIMIT
	分页参数
```

- 执行顺序

```mysql
FROM
WHERE
GROUP BY
HAVING
SELECT
ORDER BY
LIMIT
```



- 去重

```mysql
SELECT DISTINCT 字段列表 FROM 表名列表;
```

- 聚合函数

```mysql
COUNT
max
min
avg
sum
//	作用于表中的某一列
```





# DCL

- 用户管理

```mysql
// 查询用户
USE mysql;
SELECT * FROM user;

// 创建用户
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';

// 修改用户密码
ALTER USER '用户名'@'主机名' IDENTIFIED WITH 旧密码 BY '新密码';

// 删除用户
DROP USER '用户名'@'主机名';
```

- 权限控制

```mysql
//查询权限
SHOW GRANTS FOR '用户名'@'主机名';

//授予权限
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';

//撤销权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```


# MySQL

## RDBMS(Relational Database Management System)

术语

+ 数据库: 数据库是一些关联表的集合。
+ 数据表: 表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
+ 列: 一列(数据元素) 包含了相同的数据, 例如邮政编码的数据。
+ 行：一行（=元组，或记录）是一组相关的数据，例如一条用户订阅的数据。
+ 冗余：存储两倍数据，冗余降低了性能，但提高了数据的安全性。
+ 主键：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。
+ 外键：外键用于关联两个表。
+ 复合键：复合键（组合键）将多个列作为一个索引键，一般用于复合索引。
+ 索引：使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构。类似于书籍的目录。
+ 参照完整性: 参照的完整性要求关系中不允许引用不存在的实体。与实体完整性是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。

## MySQL管理

退出mysql使用以下三个命令:

+ exit
+ quit
+ \q

1.`USE 数据库名`

选择要操作的Mysql数据库，使用该命令后所有Mysql命令都只针对该数据库。

```bash
mysql> use RUNOOB;
Database changed
```

2.`SHOW DATABASES`

列出 MySQL 数据库管理系统的数据库列表。

```bash
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| RUNOOB             |
| cdcol              |
| mysql              |
| onethink           |
| performance_schema |
| phpmyadmin         |
| test               |
| wecenter           |
| wordpress          |
+--------------------+
10 rows in set (0.02 sec)
```

3.`SHOW TABLES`

显示指定数据库的所有表，使用该命令前需要使用 use 命令来选择要操作的数据库。

```bash
mysql> use RUNOOB;
Database changed
mysql> SHOW TABLES;
+------------------+
| Tables_in_runoob |
+------------------+
| employee_tbl     |
| runoob_tbl       |
| tcount_tbl       |
+------------------+
3 rows in set (0.00 sec)
```

4.`SHOW COLUMNS FROM 数据表`

显示数据表的属性，属性类型，主键信息 ，是否为 NULL，默认值等其他信息。

```bash
mysql> SHOW COLUMNS FROM runoob_tbl;
+-----------------+--------------+------+-----+---------+-------+
| Field           | Type         | Null | Key | Default | Extra |
+-----------------+--------------+------+-----+---------+-------+
| runoob_id       | int(11)      | NO   | PRI | NULL    |       |
| runoob_title    | varchar(255) | YES  |     | NULL    |       |
| runoob_author   | varchar(255) | YES  |     | NULL    |       |
| submission_date | date         | YES  |     | NULL    |       |
+-----------------+--------------+------+-----+---------+-------+
4 rows in set (0.01 sec)
```

5.`SHOW INDEX FROM 数据表`

显示数据表的详细索引信息，包括`PRIMARY KEY(主键)`

```bash
mysql> SHOW INDEX FROM runoob_tbl;
+------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table      | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| runoob_tbl |          0 | PRIMARY  |            1 | runoob_id   | A         |           2 |     NULL | NULL   |      | BTREE      |         |               |
+------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
1 row in set (0.00 sec)
```

6.`SHOW TABLE STATUS LIKE [FROM db_name] [LIKE 'pattern'] \G:`

该命令将输出Mysql数据库管理系统的性能及统计信息。

## MySQL 数据库的创建、删除、切换

```bash
$SHOW DATABASES; // 列出MySQL数据库列表
$CREATE DATABASE <数据库名>;
$DROP DATABASE <数据库名>;
$USE <数据库名>;
```

## 数据类型

首先来看两个概念

+ 位(bit) 二进制位，计算机内部存储数据的最小单位
+ 字节(byte) 一个字节等于8个bit,通常一个字节可以存入一个ASCII码，两个字节可以存放一个汉字国标码

数值类型

类型|大小
TINYINT|1字节
SMALLINT|2字节
MEDIUMINT|3字节
INT或INTEGER|4字节
BIGINT|8字节
FLOAT|4字节
DOUBLE|8字节
DECIMAL|

字符串类型

类型|大小|用途
CHAR|0-255字节|定长字符串
VARCHAR|0-65535字节|变长字符串
TINYBLOB|0-255字节|不超过255个字符的二进制字符串
TINYTEXT|0-255字节|短文本字符串
BLOB|0-65535字节|二进制形式的长文本数据
TEXT|0-65535字节|长文本数据
MEDIUMBLOB|0-16777215字节|二进制形式的中等长度文本数据
MEDIUMTEXT|0-16777215字节|中等长度文本数据
LONGBLOB|0-4294967295字节|二进制形式的极大文本数据
LONGTEXT|0-4294967295字节|极大文本数据

## 数据表操作

+ SELECT - 从数据库中提取数据
+ UPDATE - 更新数据库中的数据
+ DELETE - 从数据库中删除数据
+ INSERT INTO - 向数据库中插入新数据
+ CREATE DATABASE - 创建新数据库
+ ALTER DATABASE - 修改数据库
+ CREATE TABLE - 创建新表
+ ALTER TABLE - 变更（改变）数据库表
+ DROP TABLE - 删除表
+ CREATE INDEX - 创建索引（搜索键）
+ DROP INDEX - 删除索引

创建数据表

```sql
CREATE TABLE table_name (column_name column_type);

CREATE TABLE IF NOT EXISTS `runoob_tbl`(
   `runoob_id` INT UNSIGNED AUTO_INCREMENT,
   `runoob_title` VARCHAR(100) NOT NULL,
   `runoob_author` VARCHAR(40) NOT NULL,
   `submission_date` DATE,
   PRIMARY KEY ( `runoob_id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

删除数据表

```sql
DROP TABLE table_name;
```

插入数据/增

```sql
INSERT INTO table_name ( field1, field2,...fieldN )
                       VALUES
                       ( value1, value2,...valueN );
```

查询数据/查

```sql
SELECT column_name,column_name
FROM table_name
[WHERE Clause]
[LIMIT N][ OFFSET M]
```

+ 查询语句中可以使用一个或者多个表，表之间使用逗号(,)分割，并使用WHERE语句来设定查询条件
+ SELECT 命令可以读取一条或者多条记录
+ 可以使用星号（*）来代替其他字段，SELECT语句会返回表的所有字段数据
+ 可以使用 WHERE 语句来包含任何条件
+ 可以使用 LIMIT 属性来设定返回的记录数
+ 可以通过OFFSET指定SELECT语句开始查询的数据偏移量。默认情况下偏移量为0

WHERE 字句

```sql
SELECT field1, field2,...fieldN FROM table_name1, table_name2...
[WHERE condition1 [AND [OR]] condition2.....
```

UPDATA查询/改

```sql
UPDATE table_name SET field1=new-value1, field2=new-value2
[WHERE Clause]
```

DELETE语句

```sql
DELETE FROM table_name [WHERE Clause]
```

+ 如果没有指定WHERE字句，MySQL表中的所有记录将被删除
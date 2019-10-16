---
title: MySQL的表索引定义
date: 2019-10-15 15:38:24
categories:
- MySQL
tags:
- Database definition syntax
---

## 概述

索引是 DBMS 根据表中的一列或若干列按一定的顺序建立的列值与记录行之间的对应关系表，方便 DBA 管理

- 索引是以文件的形式存储，DBMS 将一张表的所有所有保存在同一个索引文件中，需要占用磁盘空间，如果有大量索引，可能比数据文件更快达到最大的文件尺寸。
- 索引提高查询速度的同时，会降低更新表的速度。更新表中索引列上的数据时，索引会被自动更新，确保索引树与表中的内容完全保持一致，因此索引越多，则更新时长更长。

根据用途，索引在逻辑上大体分为三类

1. 普通索引（INDEX）: 最基本的索引类型，没有任何限制。通常使用关键字 INDEX 或 KEY

2. 唯一性索引（UNIQUE）：索引中所有的值只能出现一次，必须唯一 通常使用关键字 UNIQUE

3. 主键（PRIMARY KEY）：主键是一种唯一性索引。创建主键时，必须指定关键字 PRIMARY KEY，且不能有空值，一般在创建表的时候指定，也可通过修改表的方式添加，每张表只能有一个主键

## 创建索引

分别有三种方式创建索引

### `CREATE INDEX`
```SQL
CREATE [UNIQUE] INDEX index_name
    NO tbl_name(index_col_name,...)
```
1. `UNIQUE`：指定创建唯一性索引，一张表可以创建多个索引，每个索引在该表中名称唯一
2. `tabl_name`: 数据库的表名
3. `index_col_name` ：关于索引的描述。格式为`col_name[(length)][ASC|DESC]`

#### 索引的描述三个语法要素
- `col_name`：指定要创建索引的列名，
- `lenght`:指定使用列的前length 个字符创建索引，有利于减小索引文件的大小
- `ASC|DESC`指定按升序（ASC）或降序（DESC）排列，默认 ASC


```SQL
mysql>CREATE INDEX index_customers
    ->  NO mysql_test.customers (cust_name(3)ASC)
Query OK, 0 rows affected (0.20 sec)
Records:0 Duplicates:0 Warning:0
```

#### `CREATE TABLE`

- `[CONSTRAINT [symbol]] PRIMARY KEY (index_col_name,...)`：创建新表的同时创建该表主键
- `{INDEX|KEY}[index_name](index_col_name,...)` ：创建表的同时创建该表索引
- `[CONSTRAINT [symbol]] UNIQUE [INDEX|KEY] [index_name] (index_col_name,...)`:用于创建表时创建唯一性索引
- `[CONSTRATIN [symbol]] FOREIGN KEY[index_name] (index_col_name,...)`:创建表的同时创建外键

- `KEY`: 关键字 INDEX的同义词
- `CONSTRAINT`：为主键、UNIQUE键、外键定义一个名字，使用 CREATE TABLE 定义列选项时，可以通过直接在某个列定义后面添加`PRIMARY KEY` 添加主键，主键由多列组成的多列索引时，不能使用此方法。

```SQL
mysql> USE mysql_test
Database changed
mysql> CREATE TABLE seller
    ->(
    -> seller_id int NOT NULL AUTO_INCREMENT
    -> seller_name char(50) NOT NLULL,
    -> seller_address char(50) null,
    -> product_type int(5) NULL
    -> sales int NULL
    -> PRIMARY KEY (seller_id,product_type)
    -> INDEX index_seller(salse)
   ->)
Query OK, 0 rows affected (0.20 sec)

```

#### `ALTER TABLE`

- `ADD {INDEX|KEY} [index_name] (index_col_name,...)`: 新增表列的同时添加索引
- `ADD [CONTRAIN [smbol]] PRIMARY KEY [index_name] (index_col_name,...)`：新增列的同时添加主键
- `ADD [CONTRAIN [smbol]] UNIQUE [INDEX|KEY] [index_name] (index_col_name,...)`: 新增列的同时添加唯一性索引
- `ADD [CONTRAIN [smbol]] FOREIGN KEY [index_name] (index_col_name,...)`: 新增列的同时添加外键
```SQL
mysql> ALTER TABLE mysql_test.seller
    -> ADD INDEX index_seller_name(seller_name)
Query OK, 0 rows affected (0.20 sec)
 Records: 0 Duplicates: 0 Warning: 0
```

### 查看索引

使用 SHOW INDEX 查看索引
语法：
```SQL
SHOW {INDEX|INDEXES|KEYS}
 {FROM|IN} tbl_name
 [{FROM|IN}db_name]
 [WHERE expr]
```

### 删除索引

使用 DROP INDEX 或 ALTER TABLE 删除索引

- DROP INDEX

```SQL
DROP INDEX index_name ON tbl_name
```

- ALTER TABLE

1. DROP PRIMARY KEY 删除表中主键

2. DROP INDEX 删除对应索引

3. DROP FOREIGN KEY 删除外键

```SQL
mysql> ALTER TABLE mysql_test.customers
    -> DROP PRIMARY KEY
    -> DROP INDEX index_custormes
Query OK, 0 rows affected (0.20 sec)
 Records: 0 Duplicates: 0 Warning: 0
```

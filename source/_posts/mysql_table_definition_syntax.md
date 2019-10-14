---
title: database_table_syntax
date: 2019-10-14 20:15:35
catagories:
- MySQL
tags:
- Table definiton syntax
---
## 表定义

只有成功创建数据库后，才能创建数据表，数据表是字段的集合，在表中数据按行和列的格式存储

### 创建表

MySQL 使用 CREATE TABLE 创建表。其中有多个选择，主要由表创建定义（create definition）、表选项定义（table options） 和区分选项（partition options）等内容构成。

表创建定义：由表列的名字、列的定义集可能的一个空值声明、一个完整性约束或表索引项组成，表索引项主要定义表的索引、主键、外键等。

语法结构：
```SQL
CREATE[TEMPORARY]TABLE tbl_name
(
    字段名|数据类型[列级完整性约束条件][默认值]
    [,字段名2 数据类型[列级完整性约束条件][默认值]]
    [,....]
    [,表级完整性约束条件]
)[ENGINE=引擎类型]
```

**Example:**
新建一个客户信息
```SQL
mysql> USE mysql_test
Database changed
mysql> CRATE TABLE customers
    ->(
    -> cust_id INT NOT NULL AUTO_INCREMENT,
    -> cust_name CHAR(50) NOT NULL,
    -> cust_sex CHAR(1) NOT NULL DEFAULT 0,
    -> cust_address CHAR(50) NULL
    -> cust_contact CHAR(50) NULL
    -> PRIMARY KEY(CUST_ID)
    ->)
Query OK, 0 rows affected(0.11 sec)
```

#### 临时表与持久表

TEMPORARY：表示临时表，如果不选用则位持久表。
持久表一直存在，多个用户或应用程序可同时使用持久表，如果只需临时存放数据可添加 TEMPORARY 关键字
临时表只能对创建它的用户可见，断开数据库连接时，表会自动清除

#### 数据类型

数据类型指系统中所允许的数据的类型。每列都应有适当的数据类型，来限制或允许该列的数据。 建表时必须为每列指定正确的数据类型及数据长度 （CHAR(50)）

MySQL 主要数据类型:
- 数值类型：整型 int、浮点 double、布尔 bool
- 日期和时间类型：日期型、时间戳 timestamp、时间型 time
- 字符串类型：定长字符类型char、可变长字符类型varchrar
- 空间数据类型:单个几何类型 GEOMETRY等

#### 关键字 AUTO_INCREMENT

AUTO_INCREMENT: 表中数据类型为整型的列设置自增属性 （++i）,从当前指或 1 开始，表中只能有一个 AUTO_INCREMENT。

当一个表列被指定为 AUTO_INCREMENT 后，其值可被覆盖，即可在表数据插入语句中为该列指定一个值（必须唯一），则该值将替换系统自动生成的值，后续增量基于该插入的值

#### 指定默认值

DEFAULT:用于指定MySQL在未给值的情况下默认的值（DEFAULT 0）

如果未指定默认值，则自动为其分配一个值，如若该列可取值NULL，则默认NULL，若定义 NOT NULL，则默认取决于该列的类型：
- 一个没有声明 AUTO_INCREMENT 列 为数字类型，默认 0
- 一个 AUTO_INCREMENT 列 默认为顺序中的下一个值
- 对于除 TIMESTAMP 以外的日期和时间类型，默认为该类型适当的'零'值、
- 对于表中第一个 TIMESTAMP 列，默认值为当前日期和时间

#### NULL值

NULL：没有值或缺值，允许NULL的列，插入行时可以不给该列的值；不允许NULL值的列，则该列必须有数据
`NULL` 和 `''`是不对等的 NOT NULL 列中允许`''` 不允许 NULL

#### 主键

PRIMARY KEY :指定主键，主键必须唯一且不能为NULL， 如果是单列，值必须唯一，如果是组合列，则其组合的值必须唯一


### 更新表

通过使用 ALTER TABLE 来修改数据库

- `ADD[COLUMN]`：新增表列，可增多列使用逗号分隔即可
**Example:**
```SQL
mysql> ALTER TABLE mysqle_test.customers
    -> ADD COLUMN cust_city char(10) NOT NULL DEFAULT'ShenZhen' AFTER cust_sex;
Query OK,0 rows affected(0.61 sec)
Records:0 Duplicates:0 Warning:0
```
AFTER：将新增的列添加到cut_sexl 列之后
FIRST：将新增的列添加到表的第一列

若使用上述关键字则将新增的列添加至表最后

类似的 可以使用 ADDPRIMARY KEY 、ADDFOREIGN KEY 、ADD INDEX 添加对应的 主键、外键、索引

- `CHANGE[COLUMN]`: 修改表中列的名称或数据类型，可修改多列使用逗号分隔即可

```SQL
mysql> ALTER TABLE mysqle_test.customers
    -> CHANGE COLUMN cust_sex sex char(1) NULL DEFAULT 'M'
Query OK,0 rows affected(0.66 sec)
Records:0 Duplicates:0 Warning:0
```
如果将数据类型更换，可能会丢失该列原有的数据，如果视图改变的数据类型于原有的数据类型不兼容，则SQL命令不会执行，且抛出错误。
再兼容的情况下，该列的数据可能会被截断，如：一列的数据类型为 varchart(10),改为char(1),则该列中的数据'ShenZhen'会变为'S'

- `ALTER [COLUMN]`: 修改或删除指定列的默认值
```SQL
mysql> ALTER TABLE mysqle_test.customers
    -> ALTER COLUMN cust_city SET  DEFAULT 'ShangHai'
Query OK,0 rows affected(0.36 sec)
Records:0 Duplicates:0 Warning:0
```

- `MODIFY [COLUMN]`: 修改指定列的数据类型，通过 'FIRST' 或 'AFTER' 修改列的位置
```SQL
mysql> ALTER TABLE mysqle_test.customers
    -> MODIFY COLUMN cust_name char(30)  FIRST
Query OK,0 rows affected(0.20 sec)
Records:0 Duplicates:0 Warning:0
```

- `DROP [COLUMN]`: 删除列，该列所有数据一并删除
```SQL
mysql> ALTER TABLE mysqle_test.customers
    -> DROP COLUMN cust_city
Query OK,0 rows affected(0.42 sec)
Records:0 Duplicates:0 Warning:0
```
同样 可使用 DROP PRIMARY KEY 、DROP FOREIGN KEY、DROP INDEX 删除对应的主键、外键、索引

- `RENAME[TO]`:表重命名
```SQL
mysql> ALTER TABLE mysqle_test.customers
    -> RENAME TO
Query OK,0 rows affected(0.42 sec)
```

### 重命名表

除了 ALTER TABLE 中的 RENAME TO 修改表名，还可通过 RENAME TABLE 来修改单张和多张表（以逗号分隔）
```SQL
mysql> RENAME TABLE mysql_test.back.customers TO mysqle_test.customers
```

### 删除表
`DROP[TEMPORARY]TABLE[IF EXISTS]`删除一个已存在的表,可以删除多张表，前提操作人必须有权限，但是操作人在该张表上的权限不会被删除

### 查看表






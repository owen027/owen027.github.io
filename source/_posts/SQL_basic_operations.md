---
title: SQL和MySQL 基础
date: 2019-10-09 18:18:47
categories:
-  MySQL
tags:
- SQL basis
---
# SQL

## 概述

Struture Query Langue （结构化查询语言，SQL），于1974年 Boyce 和 Chamberlin 提出。其已成为关系数据库的标准语言，是一种数据库查询和程序设计语言，用于存取数据及查询、更新和管理关系数据库系统，具有数据库定义、数据操作和数据控制等功能

### 特点

- SQL不是特点的数据库专有语言，几乎所有重要的关系数据库管理系统都支持 SQL。
- SQL简单易学，SQL语句不区分大小写。

### 组成

SQL集数据查询（Data Query）、数据定义（Data Definition）、数据操纵（Data Manipulation）和数据控制（Data Control）四大功能于一体

#### 数据定义语言（DDL）

主要用于对数据库及数据库中的各种对象进行创建、修改、删除等操作。数据库对象主要为 表、默认约束、规则、视图、触发器、存储过程等

**关键字：**
1. CREATE: 创建数据库或数据库对象
2. ALTER：修改数据库或数据库对象
3. DORP： 删除数据库或数据库对象

#### 数据操作语言（DML）

主要用于操作数据库中的各种对象，主要是检索和修改数据

**关键字：**

1. SELECT：从表或视图中检索数据，使用最为频繁
2. INSERT：将数据插入到表或视图中
3. UPDATE：修改表或视图中的数据，可修改单行，也可修改多行
4. DELETE：删除表或视图中的数据，可跟据条件删除指定数据

#### 数据控制语言（DCL）

用于安全管理（权限）

1. GRANT：授予权限
2. REVOKE：回收权限


---


# MySQL

## 概述

MySQL是一个关系数据库管理系统（RDBMS),具有客户/服务器体系结构，由瑞典MySQL AB公司开发。 具有体积小、速度快、开源、遵循GPL（GNU通用公共许可证）等特点

MySQL在SQL标准的基础上增加了部分扩展的语言要素包括常量、变量、运算符、表达式、函数、流程控制语句和注解等。

### 运算符

<!-- 1. 算术运算符: +、-、*、/、%
2. 位运算符：&（位与）、|（位或）、^（位异或）、~（位取反）、>>（位右移）、<<（位左移）
3. 比较运算符：=、>、<、>=、<=、<>（不等于）、!=（不等于）、<=>（相等或都等于空）
4. 逻辑运算符：NOT 或 ! （逻辑非）、AND 或 &&（逻辑与）、OR 或 ||（逻辑或）、XOR（逻辑异或）
 -->
|算术运算符|位运算符|比较运算符|逻辑运算符
|:-:|:-:|:-:|:-:|
|+|&（位与）|=|NOT 或 ! （逻辑非）|
|-|l（位或）|>|AND 或 &&（逻辑与）|
|*|^（位异或）|<|OR 或 ll（逻辑或）|
|/|~（位取反）|>=|XOR（逻辑异或）|
|%|>>（位右移）|<=||
| |<<（位左移）|<>（不等于）或 !=（不等于）||
|||<=>（相等或都等于空）|||

### 内置函数

- 数学函数，如ABS()、SORT()
- 聚合函数，如COUNT()
- 字符串函数，如ASCII()、CHAR()
- 日期和时间函数，NOW()、YEAR()
- 加密函数，ENCODE()、ENCRYPT()
- 控制流程函数，IF()、IFNULL()
- 格式化函数，FORMAT()
- 类型转换函数，CAST()
- 系统信息函数，USER(),VERSION()

## 数据定义

SQL 的数据定义功能包括数据库模式、表、索引和视图的定义
SQL 标准不提供修改数库模式定义和修改视图定义的操作，如需修改对象，可先删除再创建，或通过RDBMS提供的扩展语句实现
SQL 标准不提供索引相关的语句

### 数据库模式定义

对数据库的增、删、改、查、选择等操作

#### 创建数据库

在MySQL中，使用 CREATE DATABASE 或 CREATE SCHEMA 语句创建数据库
语法结构：
```SQL
CREATE {DATABASE|SCHEMA}[IF NOT EXISTS] db_name
[DEFAULT]CHARACTER SET [=]charest_name
|[DEFAULT]COLLATE [=]collation_name

```
`[]`: 表示为可选
`|`: 用于分隔花括号中的选项，表示任选一项语法
`db_name`: 标识具体的数据库命名，必须符合操作系统文件夹命名规则，在MySQL中不区分大小写
`DEFAULT`: 默认值
`CHAREST SET`: 指定数据库字符集
`COLLATE`: 指定字符集校对规则
`IF NOT EXISTS`：创建数据库前进行判断，只有该数据库不存在时才能创建数据库

**Example：**
创建名称位 mysql_test 的数据库
```SQL
CREATE DATABASE mysql_test;
```
在次输入同样的命令 将会报错 可添加 `IF NOT EXISTS` 避免报错

#### 选择数据库

 MySQL 中创建数据库之后，不会自动使用新创建的数据库，可使用 `USE` 语句从当前环境切换换至新创建的数据库或其它数据库
**Example：**
```SQL
USE mysql_test;
```

#### 修改数据库

MySQL 中使用 ALTERDATABASE 或使用 ALTER SCHEMA 语句，修改数据库的相关参数
语法结构：
```SQL
ALTER {DATABASE|SCHEMA}[db_name]
```
**Example：**
修改已有数据库默认字符集和校对规则
```SQL
ALTER DATABASE mysqle_test
...
```

#### 删除数据库

MySQL 中使用 DROP DATABASE 或 DROP SCHEMA 来删除数据库,其数据永久删除
语法结构：
```SQL
DROP {DATABASE|SCHEMA}[IF EXISITS]db_name
```
**Example：**
```SQL
DROP DATABASE IF EXISITS  mysqle_test
...
```

#### 查看数据库

MySQL 中使用 SHOW DATABASE 或 SHOW SCHEMA 来看可用数据库列表，只会列从出权限范围内的数据库
语法结构：
```SQL
SHOW {DATABASE|SCHEMA}[LIKE'pattern'|WHERE expr]

```
`LIKE`: 匹配指定数据库名称
`WHERE`: 指定数据库名称查询范围

**Example：**
```SQL
SHOW DATABASE
```

### 表定义

只有成功创建数据库后，才能创建数据表，数据表是字段的集合，在表中数据按行和列的格式存储

#### 创建表

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

##### 临时表与持久表

TEMPORARY：表示临时表，如果不选用则位持久表。
持久表一直存在，多个用户或应用程序可同时使用持久表，如果只需临时存放数据可添加 TEMPORARY 关键字
临时表只能对创建它的用户可见，断开数据库连接时，表会自动清除

##### 数据类型

数据类型指系统中所允许的数据的类型。每列都应有适当的数据类型，来限制或允许该列的数据。 建表时必须为每列指定正确的数据类型及数据长度 （CHAR(50)）

MySQL 主要数据类型:
- 数值类型：整型 int、浮点 double、布尔 bool
- 日期和时间类型：日期型、时间戳 timestamp、时间型 time
- 字符串类型：定长字符类型char、可变长字符类型varchrar
- 空间数据类型:单个几何类型 GEOMETRY等

##### 关键字 AUTO_INCREMENT

AUTO_INCREMENT: 表中数据类型为整型的列设置自增属性 （++i）,从当前指或 1 开始，表中只能有一个 AUTO_INCREMENT。

当一个表列被指定为 AUTO_INCREMENT 后，其值可被覆盖，即可在表数据插入语句中为该列指定一个值（必须唯一），则该值将替换系统自动生成的值，后续增量基于该插入的值

##### 指定默认值

DEFAULT:用于指定MySQL在未给值的情况下默认的值（DEFAULT 0）

如果未指定默认值，则自动为其分配一个值，如若该列可取值NULL，则默认NULL，若定义 NOT NULL，则默认取决于该列的类型：
- 一个没有声明 AUTO_INCREMENT 列 为数字类型，默认 0
- 一个 AUTO_INCREMENT 列 默认为顺序中的下一个值
- 对于除 TIMESTAMP 以外的日期和时间类型，默认为该类型适当的'零'值、
- 对于表中第一个 TIMESTAMP 列，默认值为当前日期和时间

##### NULL值

NULL：没有值或缺值，允许NULL的列，插入行时可以不给该列的值；不允许NULL值的列，则该列必须有数据
`NULL` 和 `''`是不对等的 NOT NULL 列中允许`''` 不允许 NULL

##### 主键

PRIMARY KEY :指定主键，主键必须唯一且不能为NULL， 如果是单列，值必须唯一，如果是组合列，则其组合的值必须唯一


#### 更新表

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

- `ALTER [COLUMN]`:

---
title: mysql_database_definition_syntax
date: 2019-10-14 20:19:31
catagories:
- MySQL
tags:
- Database definition syntax
---

### 创建数据库

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

### 选择数据库

 MySQL 中创建数据库之后，不会自动使用新创建的数据库，可使用 `USE` 语句从当前环境切换换至新创建的数据库或其它数据库
**Example：**
```SQL
USE mysql_test;
```

### 修改数据库

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

### 删除数据库

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

### 查看数据库

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


---
title: MySQL 的更新和查询
date: 2019-10-16 11:18:24
categories:
- MySQL
tags:
- Database definition syntax
---

## 更新

数据更新有三种操作：向表中添加若干行数据（INSERT）、修改表中的数据(UPDATE)、删除数据(DELETE)

### 插入数据

#### INSERT ... VALUES 插入多行或单行元组数据

```SQL
INSERT [INTO] tbl_name [col_name,...]
    {VALUES|VALUE}({expr|DEFAULT},...),(...),...

```

col_name :指定需要插入数据的列名列表。 如果向表中所有列插入数据，则全部列名可省略，这样做是不安全的，如果表结构发生变化，数据将会错乱。
向表的部分列插入数据，对于没被指定的列，其规则如下：

1. 对于具有标识（IDENTITY）属性的列，系统会自动生成序号值类唯一标识该列
2. 具有默认值的列，通过 INSERT 中指定关键字 DEFALUT 将其设为默认值
3. 没默认值的列，若允许空值，则通过 INSERT 指定 NULL 设为空，否则执行语句报错
4. 对于类型为 TIMESTAMP 的列，系统自动赋值
5. AUTO_INCREMENT 属性列的值再表中其它列被赋值后生成，因此对表中其他列做赋值操作时，该属性列的引用只会返回数字 0

`VALUES 或 VALUE`：包含各列需要插入数据的清单。 数据的顺序必须与列的顺序相对应，该子句的值可以是：
- 'expr': 一个常量、变量或表达式，也可以是NULL，其值的数据裂隙与列的数据类型一致，否则报错
- 'DEFAULT'：指定默认该列值为该列的默认值，前提该列已经明确指定了默认值，否则报错

```SQL
mysql> INSERT INTO mysql_test.customers
    -> Values(901,'张三','F','北京市')
Query OK, 1 row affected(0.09 sec)
```

```SQL
mysql> INSERT INTO mysql_test.customers
    -> Values(0,'李四',DEFAULT,'北京市')
Query OK, 1 row affected(0.09 sec)
```


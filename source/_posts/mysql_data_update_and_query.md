---
title: MySQL 的更新和查询
date: 2019-10-16 11:18:24
categories:
- MySQL
tags:
- Database definition syntax
---

## 更新

数据更新有三种操作：插入数据（INSERT）、修改表中的数据(UPDATE)、删除数据(DELETE)

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
5. AUTO_INCREMENT 属性列的值在表中其它列被赋值之 后生成，因此对表中其他列做赋值操作时，该属性列的引用只会返回数字 0

`VALUES 或 VALUE`：包含各列需要插入数据的清单。 数据的顺序必须与列的顺序相对应，该子句的值可以是：
- 'expr': 一个常量、变量或表达式，也可以是NULL，其值的数据类型须与列的数据类型一致，否则报错
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

上述中未给出待插入表的列清单，这样的操作是不安全的须改成：
```SQL
mysql> INSERT INTO mysql_test.customers(cust_Id,cust_name,cust_sex,cust_address)
    -> Values(0,'李四',DEFAULT,'北京市')
Query OK, 1 row affected(0.09 sec)
```
当表的结构发生改变, INSERT 语句任能正确执行。

#### INSERT...SET

`INSERT...SET` 可以直接给表中的某些列指定对应的列值
```SQL
INSERT [INTO] tbl_name
    SET col_name1={expr|DEFAULT},col_name2={expr|DEFAULT},....
```
对于未指定的列为默认值
```SQL
mysql> INSERT INTO mysql_test.customers
    -> SET cust_address='武汉',cust_name="李四"，cust_sex=DEFAULT
Query OK, 1 row affected(0.09 sec)
```


### 删除数据

使用`DELETE FROM tbl_name` 可删除行数据

```SQL
DELETE FROM tbl_name
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]
```

-` WHERE`：限定删除条件，从而删除特定的行，如不指定条件则删除指定表的所有行数据
- `ORDER BY`：各行按指定顺序进行删除
- `LIMIT`：告知服务器在控制命令被返回到客户端前被删除行的最大值

```SQL
DELETE FROM mysql_test.customers
   WHERE cust_name='王五'
Query OK, 1 row affected(0.09 sec)
```

### 修改数据

使用`UPDATE` 更新表中的数据

```SQL
UPDATE  tbl_name
    SET col_name={expr|DEFAULT}[,col_name={expr|DEFAULT}]...
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]
```
-` WHERE`：限定修改条件，从而修改特定的行，如不指定条件则修改指定表的所有行数据
- `ORDER BY`：各行按指定顺序进行修改
- `LIMIT`：限定被修改的行数

```SQL
UPDATE  mysql_test.customers
   SET cust_address = '北京',cust_sex = NULL
   WHERE cust_name='王五'
Query OK, 1 row affected(0.09 sec)
```

---

## 查询数据
数据查询是SQL 语言的核心功能，是使用最多的操作

### SELECT

SELECT 可以从数据库中检索、统计或输出数据
执行过程是从数据库中选取匹配的特定行和列，并组成一个结果集放到临时表中返回。
```SQL
SELECT
 [ALL|DISTINCT|DISTINCTROW]
 select_expr[,select_expr ...]
 FROM table_references
 [WHERE where_condition]
 [GROUP BY {col_name|expr|position}
    [ASC|DESC],...[WITH ROLLUP]]
 [HAVING where_condition]
 [ORDER BY {col_name|expr|position}
    [ASC|DESC],...]
 [LIMIT {[offset,]row_count|row_count OFFSET offset}]
```
SELECT 子句用于指定输出的字段； （必选）
FROM 子句用于指定数据的来源；   （从表选择数据时使用）
WHERE 用于指数据的选择条件；
GROUP BY 用于对检索到的记录进行分组；（在按组计算聚合时使用）
HAVING 用于指定组的选择条件；
ORDER BY 用于对查询结果的排序；

<label>必须按照SELECT 语句的语法格式所罗列的顺序格式查找数据。</label>

如 HAVING 语句必须位于BROUP BY之后 OREDR BY 之前。




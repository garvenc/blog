本文更新于2019-06-30，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

# 分区类型

可以使用`SHOW PLUGINS`查看是否安装了分区插件。

MySQL创建分区表支持使用大部分存储引擎，但不支持使用MERGE或CSV存储引擎。同一个分区表的所有分区必须使用同一种存储引擎。

MySQL分区作用于整个表的所有数据和索引，其索引一定是本地LOCAL索引。

无论那种分区类型，要么分区表上没有主键/唯一键，要么分区表的主键/唯一键包含分区键。

分区表中的分区名不区分大小写。

可以通过查询information_schema.PARTITIONS得知分区中的行数，来间接确定记录是插入到那一个分区中的：

```sql
SELECT PARTITION_NAME, PARTITION_EXPRESSION, PARTITION_DESCRIPTION, TABLE_ROWS
FROM information_schema.PARTITIONS
WHERE TABLE_SCHEMA = dbname AND TABLE_NAME = tablename
```

## Range分区

```sql
CREATE TABLE tablename (...) [[STORAGE] ENGINE=engine]
PARTITION BY RANGE [COLUMNS] (partitionkey[, ...]) (
PARTITION partitionname VALUES LESS THAN (value)|MAXVALUE
[, ...]
)
```

如不使用`COLUMNS`，则Range分区的分区键必需为`INT`类型的某一列，或某一返回`INT`类型的表达式。如使用`COLUMNS`，则为Range Columns分区，分区键可为非`INT`类型，可为多列分区，不能为表达式。

Range分区的区间必须连续且不能互相重叠，区间为左闭右开区间。每个分区都是按顺序进行定义的，从最低到最高。

分区键的值如果是`NULL`则会被当作最小值来处理。

## List分区

```sql
CREATE TABLE tablename (...) [[STORAGE] ENGINE=engine]
PARTITION BY LIST [COLUMNS] (partitionkey[, ...]) (
PARTITION partitionname VALUES IN (value[, ...])
[, ...]
)
```

如不使用`COLUMNS`，List分区的分区键必需为`INT`类型的某一列，或某一返回`INT`类型的表达式。如使用`COLUMNS`，则为List Columns分区，分区键可为非`INT`类型，可为多列分区，不能为表达式。

List分区的声明不必按照特定的顺序。

分区键的值如果是`NULL`则必须出现在分区定义的枚举列表中。

## Columns分区

Columns分区可分为Range Columns分区和List Columns分区，其分区键都支持整数、日期时间、字符串数据类型。

* 所有整数类型，即`TINYINT`、`SMALLINT`、`MEDIUMINT`、`INT`、`BIGINT`，不支持其他数值类型。
* 日期时间类型`DATE`和`DATETIME`。分区日期处理支持的函数有`YEAR()`、`TO_DAYS()`、`TO_SECONDS()`。
* 字符串类型`CHAR`、`VARCHAR`、`BINARY`、`VARBINARY`，不支持`[*]TEXT`和`[*]BLOB`。

Columns分区可使用一列或多列作为分区键，即支持多列分区，不支持表达式作为分区键。其分区键是基于元组的比较，即多列排序。

## Hash分区

```sql
CREATE TABLE tablename (...) [[STORAGE] ENGINE=engine]
PARTITION BY [LINEAR] HASH(partitionkey)
PARTITIONS count
```

MySQL支持两种Hash分区，常规Hash分区和线性Hash分区。常规Hash分区使用的是取模算法，线性Hash分区使用的是一个线性的2的幂的运算法则。常规Hash分区在分区管理（增加、删除、合并、拆分分区）时代价较大，线程Hash分区在分区管理时能处理得更迅速，但各个分区之间数据分布不太均匀。常规Hash分区将记录保存到分区编号为`MOD(partitionkey, count)`的分区中，线性Hash分区保存的分区编号按照如下规则计算。当线性Hash分区的个数为2的幂的时候，其和常规Hash分区的分区结果是一致的。

1. 找到下一个大于等于`count`的2的幂`V = Power(2, Ceiling(Log(2, count)))`，`V`有可能大于`count`。
1. 设置`N = partitionkey & (V - 1)`。由上一步得，`V - 1`为一个所有位都为1的整数，运算结果`N`共有`V`种可能（`0`至`V - 1`）。
1. 当`N > count`时，设置`V = Ceiling(V / 2)`，由第一步得知该值肯定小于`count`且为一个2的幂。使用新的`V`设置`N = N & (V - 1)`即为分区编号。

Hash分区的分区键必需为`INT`类型的某一列，或某一返回`INT`类型的表达式。

分区键的值如果是`NULL`则会将其当作零值处理。

## Key分区

```sql
CREATE TABLE tablename (...) [[STORAGE] ENGINE=engine]
PARTITION BY [LINEAR] KEY ([partitionkey[, ...]])
PARTITIONS count
```

Key分区的分区键可为除`[*]TEXT`和`[*]BLOB`类型以外的一列或多列，不能为表达式。如未指定分区键，则使用主键作为分区键；若无主键，则选择非空唯一键作为分区键。MySQL使用服务器的HASH函数计算列的散列值。

分区键的值如果是`NULL`则会将其当作零值处理。

Key分区表不能执行`ALTER TABLE DROP PRIMARY KEY`来删除主键，否则会返回错误：Field in list of fields for partition function not found in table。

Key分区使用`LINEAR`和Hash分区有相同的作用。

## 子分区

Range分区和List分区可再进行子分区（复合分区），子分区可以使用Hash分区或Key分区。

```sql
CREATE TABLE tablename (...) [[STORAGE] ENGINE=engine]
PARTITION BY RANGE|LIST [COLUMNS] (partitionkey1[, ...])
SUBPARTITION BY HASH|KEY (partitionkey2[, ...])
SUBPARTITIONS count
(
PARTITION partitionname VALUES {LESS THAN (value)|MAXVALUE}|{IN (value[, ...])}
[, ...]
)
```

`PARTITION`子句的`VALUES`根据分区是Range分区还是List分区填写（下同）。

# 分区管理

## Range分区和List分区的分区管理

删除分区，会同时删除分区中的数据：

```sql
ALTER TABLE tablename
DROP PARTITION partitionname
```

增加分区，Range分区只能添加分区至分区列表最大一端：

```sql
ALTER TABLE tablename
ADD PARTITION (
PARTITION partitionname VALUES {LESS THAN (value)|MAXVALUE}|{IN (value[, ...])}
)
```

重定义分区，不会丢失原有数据，可以用来拆分一个分区为多个分区，也可以用来合并多个相邻分区（指定义语句相邻）为一个分区或多个分区，同时重定义的分区范围必需与原分区相同：

```sql
ALTER TABLE tablename
REORGANIZE PARTITION partitionname1[, ...] INTO (
PARTITION partitionname2 VALUES {LESS THAN (value)|MAXVALUE}|{IN (value[, ...])}
[, ...]
)
```

如不使用相邻分区重定义，则会返回错误：When reorganizing a set of partitions they must be in consecutive order。

## Hash分区和Key分区的分区管理

合并分区：

```sql
ALTER TABLE tablename
COALESCE PARTITION count
```

不能通过加大count值来增加分区的数量，否则会返回错误：Cannot remove all partitions, use DROP TABLE instead!。

增加分区，为新增count个分区，而不是增加到count个分区：

```sql
ALTER TABLE tablename
ADD PARTITION PARTITIONS count
```

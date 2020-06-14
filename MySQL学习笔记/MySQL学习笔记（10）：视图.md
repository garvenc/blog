本文更新于2019-06-22，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

为了便于描述，此处将创建视图的DDL复述一次，其已于“SQL”章节描述。

```sql
CREATE [OR REPLACE] [ALGORITHM={UNDEFINED|MERGE|TEMPTABLE}] VIEW viewname [(colname[, ...])]
AS select_statement [WITH [CASCADE|LOCAL] CHECK OPTION]
```

视图的行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。

MySQL视图的定义有一些限制，如在`FROM`关键字后面不能包含子查询，这和其他数据库是不同的。通过将子查询的内容定义成另一个视图，然后对该视图再创建视图即可实现类似的功能。

视图分可更新视图和不可更新视图，以下类型的视图是不可更新的：

* 常量视图。
* 包含以下关键字的SQL语句：聚合函数（`SUM`、`MIN`、`MAX`、`COUNT`等）、`DISTINCT`、`GROUP BY`、`HAVING`、`UNION`或`UNION ALL`。
* `SELECT`中包含子查询，如`SELECT (SELECT colname FROM tablename LIMIT 1)。
* `JOIN`。
* `FROM`一个不能更新的视图。
* `WHERE`子句中的子查询引用了`FROM`子句中的表。

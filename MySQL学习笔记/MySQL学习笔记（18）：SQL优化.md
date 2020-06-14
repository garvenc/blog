本文更新于2019-08-18，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

# 优化SQL语句的步骤

## 通过`SHOW STATUS`了解SQL语句的执行情况

操作的计数，是对执行次数进行计数，不论提交还是回滚都会累加。

Com_xxx形式的参数表示每个xxx语句执行的次数，对所有的存储引擎都会进行累计，如：

* Com_select：执行`SELECT`的次数。
* Com_insert：执行`INSERT`的次数，对批量插入的操作只累加一次。
* Com_update：执行`UPDATE`的次数。
* Com_delete：执行`DELETE`的次数。
* Com_commit：事务提交的次数。
* Com_rollback：事务回滚的次数。

Innodb_rows_xxx形式的参数只对InnoDB存储引擎进行累计，其累计的方式也与Com_xxx不同：

* Innodb_rows_read：执行`SELECT`返回的行数。
* Innodb_rows_inserted：执行`INSERT`插入的行数。
* Innodb_rows_updated：执行`UPDATE`更新的行数。
* Innodb_rows_deleted：执行`DELETE`删除的行数。

Handler_read_xxx形式的参数可表示索引的使用情况：

* Handler_read_key：一个行被索引值读的次数。高表示索引被经常使用。
* Handler_read_rnd_next：在数据文件中读下一个行的次数。高表示索引不经常使用，进行大量的表扫描。

以下参数便于了解数据库的基本情况：

* Connections：试图连接服务器的次数。
* Uptime：服务器工作时间。
* Slow_queries：慢查询次数。

## 定位执行效率低下的SQL语句

* 通过慢查询日志定位执行效率低下的SQL语句。
* 通过`SHOW PROCESSLIST`查看服务器当前的线程，包括线程的状态、是否锁表等，可以实时查看SQL的执行情况。

## 通过`EXPLAIN`或`DESC`分析SQL的执行计划

`DESC`和`EXPLAIN`分析SQL执行计划的使用和作用是一样的。

执行`EXPLAIN statement`后再执行`SHOW WARNINGS`，可以看到被优化器改写后真正执行的SQL。

一个执行计划包括若干行，每行包括如下的列：

* id:值越大越先执行（值越大越位于下方），一样大从上至下执行。
* select_type：查询类型，可取如下值：
	* DERIVED：派生表的查询。
	* SIMPLE：简单查询，即不使用子查询和`UNION`的查询。
	* SUBQUERY：子查询。
	* PRIMARY：主查询，即包含子查询的最外层查询，或`UNION`中的第一个查询。
	* UNION：`UNION`中的第二个或之后的查询。
* table：输出结果集的表。
* partitions：访问的分区。
* type：访问类型，即在表中查找所需行的方式。

	以下取值性能由最差至最好：
	1. ALL：全表扫描，遍历所有行。
	1. index：索引全扫描，遍历整个索引。
	1. range：索引范围扫描，常见于`<`、`<=`、`>`、`>=`、`BETWEEN`等操作符。
	1. ref：使用非唯一索引扫描或唯一索引的前缀扫描，返回匹配某个值的所有记录行。其经常出现在`JOIN`操作中。
	1. eq_ref：类似ref，区别在于使用唯一索引。其出现在使用`PRIMARY KEY`或`UNIQUE INDEX`作为关联条件的表连接中。
	1. const/system：单表中最多有一个匹配行，因此这个匹配行中的其他列能被优化器当做常量来使用。如根据`PRIMARY KEY`或`UNIQUE INDEX`进行过滤的查询。system是const的特例，当表中只有一条记录时的const就为system。
	1. NULL：不用访问表或索引，就能直接得到结果。

	还可取其他的值，如：
	* ref_or_null：与ref类似，区别在于条件中包含对`NULL`的查询。
	* index_merge：索引合并。
	* unique_subquery：`IN`后面是一个查询唯一索引字段的子查询。
	* index_subquery：与unique_subquery类型，区别在于`IN`后面是一个查询非唯一索引字段的子查询。
* possible_keys：查询时可能使用的索引。
* key：实际使用的索引。
* key_len：实际使用到的索引的字节长度。
* ref：实际使用的索引在其他表的关联字段。如果是常数等值查询，则为const。
* rows：扫描的行数。
* filtered：存储引擎返回的数据过滤后，满足查询条件的记录的比例。
* Extra：执行情况的说明，包括不适合在其他列中显示但是对执行计划非常重要的额外信息。
	* Using filesort：filesort排序，而不是通过索引直接返回排序结果。
	* Using index：覆盖索引扫描，直接访问索引就能获取所需的数据，不需要通过索引回表。
	* Using index condition：使用ICP（Index Condition Pushdown，参看“索引”章节）优化查询，将某些情况下的条件过滤操作下放到存储引擎层完成，降低不必要的IO访问。
	* Using where：优化器除了利用索引加速访问外，还需根据索引回表查询数据。
	* Using union：多次查询后对结果集合并，如使用`OR`查询。

## 通过`SHOW PROFILES`和`SHOW PROFILE`分析SQL

profiling默认是关闭的，可通过设置变量`@@profiling`进行打开或关闭。

`SHOW PROFILES`结果包括以下字段：

* Query_ID：查询ID。
* Duration：查询耗时。
* Query：查询语句。

`SHOW PROFILE [ALL|CPU|{BLOCK IO}|{PAGE FAULTS}|SOURCE][, ...] FOR QUERY query_id`（query_id为`SHOW PROFILES`结果的Query_ID字段）结果包括以下字段：

* Status：查询执行过程中的状态。各状态含义如下：
	* starting
	* Waiting for query cache lock
	* checking query cache for query
	* checking permissions
	* Opening tables
	* inti
	* System lock
	* optimizing
	* statistics
	* preparing
	* executing
	* Sending data：开始访问数据行并把结果返回客户端，包含大量的磁盘操作。
	* end
	* query end
	* closing tables
	* removing tmp table
	* freeing items
	* storing result in query cache
	* logging slow query
	* cleaning up
* Duration：耗时。
* CPU_user
* CPU_system
* Context_voluntary
* Context_involuntary
* Block_ops_in
* Block_ops_out
* Messages_sent
* Messages_received
* Page_faults_major
* Page_faults_minor
* Swaps
* Source_function
* Source_file
* Source_line

## 通过trace分析优化器如何选择执行计划

需打开trace，设置格式为JSON，设置trace最大能使用的内存大小。如：

```sql
SET @@optimizer_trace="enabled=on";
SET @@end_markers_in_json=on;
SET @@optimizer_trace_max_size=1000000;
```

执行`SELECT * FROM information_schema.OPTIMIZER_TRACE`即可得到结果。

# 分析、检查、优化、修复表

`ANALYZE`、`CHECK`、`OPTIMIZE`、`REPAIR`执行期间都会对表进行锁定。

分析表，使得SQL能够生成正确的执行计划。如果感觉实际的执行计划并不符合预期，执行一次分析表可能会解决问题：

```sql
ANALYZE [LOCAL|NO_WRITE_TO_BINLOG] TABLE tablename[, ...]
```

检查表，用于检查表或视图是否有错误。如视图定义中被引用的表不存在：

```sql
CHECK TABLE tablename[, ...] [{QUICK|FAST|MEDIUM|EXTENDED|CHANGED}[ ...]]
```

优化表，可以将表中的空间碎片进行合并。如果已经删除表的很大一部分数据，或已经对含有可变长度行（含有`VARCHAR`、`*BLOB`或`*TEXT`的列）的表进行很多更改，则应该进行优化表：

```sql
OPTIMIZE [LOCAL|NO_WRITE_TO_BINLOG] TABLE tablename[, ...]
```

修复表，对坏表进行修复：

```sql
REPAIR [LOCAL|NO_WRITE_TO_BINLOG] TABLE tablename[, ...] [{QUICK|EXTENDED|USE_FRM}[ ...]]
```

# 常用SQL优化

## 大批量导入数据

对MyISAM存储引擎，可通过关闭和打开非唯一索引的更新提高导入效率：

```sql
ALTER TABLE tablename DISABLE KEYS;
# import data
ALTER TABLE tablename ENALBE KEYS;
```

对InnoDB存储引擎：

* 因为InnoDB表是按照主键的顺序保存的，所以将导入的数据按照主键的顺序排列，可以提高导入效率。
* 在导入数据前执行`SET unique_checks=0`关闭唯一性校验，在导入结束后执行`SET unique_checks=1`恢复唯一性校验，可提高导入效率。
* 如果应用使用自动提交的方式，建议导入前执行`SET autocommit=0`关闭自动提交，导入结束后执行`SET autocommit=1`恢复自动提交，可提高导入效率。

## 优化`INSERT`语句

* 当从同一客户端插入很多行时，应尽量使用多个值列表的`INSERT`语句。
* 如果从不同客户端插入很多行，可以使用INSERT DELAYED`，让`INSERT`马上返回（实际上数据被放在MySQL服务器内存队列中）。
* 将索引文件和数据文件在不同的磁盘存放（利用建表中的选项）。
* 如果进行批量`INSERT`，可以增加bulk_insert_buffer_size变量值来提高速度（只对MyISAM表使用）。
* 当从一个文件装载一个表时，使用`LOAD DATA INFILE`。

## 优化`ORDER BY`语句

MySQL有两种排序方式：

* 通过有序索引扫描直接返回有序数据。在使用`EXPLAIN`分析时显示为Using index。
* filesort排序。将返回的数据在sort_buffer_size设置的内存排序区进行排序，至于是否使用磁盘文件和临时表等，取决于MySQL服务器对排序参数的设置和需要排序数据的大小。

对于filesort，MySQL比较查询取出的字段总大小和max_length_for_sort_data，判断使用哪种排序算法：

* 两次扫描算法（Tow passes）：第一次根据条件获取排序字段和行指针信息，并在排序区中排序。第二次根据行指针回表读取记录，可能导致大量随机IO操作。
* 一次扫描算法（Single Pass）：一次性取出满足条件的行的所有字段，然后在排序区排序后直接输出结果。这会导致内存开销比较大。

优化`ORDER BY`语句应该：尽量减少额外的排序，通过索引直接返回有序数据。`WHERE`条件和`ORDER BY`使用相同的索引，并且`ORDER BY`的顺序和索引顺序相同，并且`ORDER BY`的字段都是升序或都是降序。否则肯定需要额外的排序操作，这样就会出现filesort排序。

尽量`SELECT`必要的字段名，而不是`SELECT *`所有字段，这样可以减少排序区的使用，提高性能。

## 优化`GROUP BY`语句

MySQL会对`GROUP BY`的所有字段进行排序。如果想避免排序的消耗，可以使用`ORDER BY NULL`禁止排序。

## 优化子查询

有些情况下，子查询可以被更有效率的表连接代替。因为表连接不需要在内存中创建临时表。

## 优化`OR`条件

对于含有`OR`的查询，如果要利用索引，则`OR`之间的每个字段都必需能利用索引。此时，实际是对`OR`的各个字段分别查询的结果进行`UNION`操作。

## 优化分页查询

执行`LIMIT offset_start, row_count`时，MySQL排序出offset_start+row_count条记录后仅仅返回最后row_count条记录，前面的offset_start条记录都会被丢弃，查询和排序的代价非常高。有两种优化思路：

* 在索引上完成排序分页的操作，最后根据主键关联回表查询所需的其他列内容。
* 把查询转换成基于某个位置的查询，使用`LIMIT row_count`代替`LIMIT offset_start, row_count`。但这种方式对数据集有特定的要求。

## 使用SQL提示

SQL提示（SQL HINT）就是在SQL语句中加入一些人为提示来达到优化的目的。

```sql
SELECT SQL_BUFFER_RESULT * FROM ...
```

这个语句强制MySQL生成一个临时结果集。生成后所有表上的锁均被释放，这能在遇到表锁问题或要花很长时间将结果传给客户端时有帮助。

```sql
SELECT * FROM tablename USE|IGNORE|FORCE INDEX (indexname[, ...]) WHERE ...
```

`USE INDEX`提供希望（实际执行时不一定会被选择）查询时使用的索引，`IGNORE INDEX`忽略指定的索引，`FORCE INDEX`强制使用指定的索引。

# 常用SQL技巧

* 利用`ORDER BY RAND()`提取随机行。
* 利用`GROUP BY ... WITH ROLLUP`获取更多的分组聚合信息。

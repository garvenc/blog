本文章更新于2022-01-04，使用MySQL 5.7，操作系统为Deepin 15.9。

[TOC]

**说明：下文中，小写为自定义变量，根据实际情况填写（个别小写的参数除外）。使用`[]`引起表示内容可选，使用`{}`引起表示内容为一个整体，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

实际上，所有语句和命令的关键字（文中大写）都不区分大小写。

以下部分语句是MySQL扩展，非标准SQL。

# DDL语句

DDL语句，即数据定义语句（Data Definition Languages）。其关键字包括：`CREATE`、`DROP`、`ALTER`。

在MySQL中，数据库和表对应于数据目录中的目录和文件，所以操作系统的大小写敏感性决定数据库名和表名的大小写敏感性。这意味着二者在Windows中是大小写不敏感的，在Linux中是大小写敏感的。

类似于表名，表别名在Windows中是大小写不敏感的，在Linux中是大小写敏感的。

列名、索引名、存储过程名、触发器名等在任何平台都是大小写不敏感的。

## 创建数据库

```sql
CREATE DATABASE dbname
[[DEFAULT] [CHARSET [=] charset] [COLLATE [=] collation]]
```

## 删除数据库

```sql
DROP DATABASE dbname
```

## 修改数据库

```sql
ALTER DATABASE dbname
[[DEFAULT] [CHARSET [=] charset] [COLLATE [=] collation]]
```

## 创建表

```sql
CREATE TABLE tablename (
{colname type [[CHARSET [=] charset] [COLLATE [=] collation]] [[NOT] NULL] [DEFAULT value]}[, ...]
[PRIMARY KEY (colname[, ...]),]
[{KEY keyname (colname[, ...])}[, ...]]
[CONSTRAINT fkname FOREIGN KEY (colname[, ...])
	REFERENCES tablename2(colname2[, ...]) [ON DELETE reference_option] [ON UPDATE reference_option]]
)
[[STORAGE] ENGINE=engine [UNION=(tablename[, ...]) [INSERT_METHOD=insert_method]]]
[[DEFAULT] [CHARSET [=] charset] [COLLATE [=] collation]]
[partition_declaration]
```

如不指定`NOT NULL`和`DEFAULT value`，字段默认是可空的。

reference_option可取如下值，表示本表有关联记录时，关联表更新或删除采取何种操作：

* `RESTRICT`和`NO ACTION`：禁止关联表更新或删除。
* `CASCADE`：更新或删除本表对应记录。
* `SET NULL`：本表的对应字段设置为`NULL`。
* `SET DEFAULT`：本表的对应字段设置为默认值。

如使用MERGE存储引擎，则可使用`UNION=(tablename[, ...]) INSERT_METHOD=insert_method`，`INSERT_METHOD`可取以下值：

* FIRST：插入操作被作用在第一个表上。
* LAST：插入操作被作用在最后一个表上。
* NO：默认值，不能执行插入操作。

partition_declaration表示分区语句，会在“分区”文章中细述。

根据已存在的表创建新的表：

```sql
CREATE TABLE tablename1
LIKE tablename2
```

## 删除表

```sql
DROP TABLE tablename
```

## 修改表

添加字段：

```sql
ALTER TABLE tablename
ADD [COLUMN] colname1 type [CHARSET [=] charset] [COLLATE [=] collation] [[NOT] NULL] [DEFAULT value]
[FIRST|{AFTER colname2}]
```

修改字段定义：

```sql
ALTER TABLE tablename
MODIFY [COLUMN] colname1 type [CHARSET [=] charset] [COLLATE [=] collation] [[NOT] NULL] [DEFAULT value]
[FIRST|{AFTER colname2}]
```

修改字段定义，也可修改字段名：

```sql
ALTER TABLE tablename
CHANGE [COLUMN] oldcolname newcolname type [CHARSET [=] charset] [COLLATE [=] collation] [[NOT] NULL] [DEFAULT value]
[FIRST|{AFTER colname}]
```

删除字段：

```sql
ALTER TABLE tablename
DROP [COLUMN] colname
```

添加外键：

```sql
ALTER TABLE tablename
ADD CONSTRAINT fkname FOREIGN KEY (colname[, ...])
REFERENCES tablename2(colname2[, ...]) [ON DELETE reference_option] [ON UPDATE reference_option]
```

删除外键：

```sql
ALTER TABLE tablename
DROP FOREIGN KEY fkname;
```

修改表名：

```sql
ALTER TABLE tablename
RENAME [TO] newtablename
```

修改表的自动增长列初始值：

```sql
ALTER TABLE tablename
AUTO_INCREMENT = n
```

修改表的存储引擎：

```sql
ALTER TABLE tablename
ENGINE=engine
```

修改表的字符集：

```sql
ALTER TABLE tablename
[[DEFAULT] [CHARSET [=] charset] [COLLATE [=] collation]]
```

## 创建索引

```sql
CREATE [UNIQUE|FULLTEXT|SPATIAL] INDEX indexname
[USING HASH|BTREE]
ON tablename({colname[(len)] [ASC|DESC]} [, ...])
```

仅当创建前缀索引的时候才使用len，len为字节数。

或：

```sql
ALTER TABLE tablename
ADD INDEX indexname(column[, ...])
```

## 删除索引

```sql
DROP INDEX indexname ON tablename
```

或：

```sql
ALTER TABLE tablename DROP INDEX indexname
```

## 创建视图

```sql
CREATE [OR REPLACE] [ALGORITHM={UNDEFINED|MERGE|TEMPTABLE}] VIEW viewname [(colname[, ...])]
AS select_statement [WITH [CASCADE|LOCAL] CHECK OPTION]
```

`WITH [CASCADE|LOCAL] CHECK OPTION`表示允许更新记录的条件，默认是`CASCADE`：

* `LOCAL`表示更新后的记录只要满足本视图的条件就可以更新。
* `CASCADE`表示更新后的记录必须满足本视图关联的所有视图（包括创建本视图使用的视图）的条件才可以更新。

## 修改视图

```sql
ALTER [ALGORITHM={UNDEFINED|MERGE|TEMPTABLE}] VIEW viewname [(colname[, ...])]
AS select_statement [WITH [CASCADE|LOCAL] CHECK OPTION]
```

## 删除视图

```sql
DROP VIRW [IF EXISTS] viewname[, ...] [RESTRICT|CASCADE]
```
## 存储过程和函数

存储过程和函数相关DDL语句于“存储过程和函数”文章中描述。

## 创建事件

```sql
CREATE EVENT eventname
ON SCHEDULE {AT timestamp [+ INTERVAL n interval]}|{EVERY n interval}
DO
statement_list
```

interval可为：`YEAR`、`QUARTER`、`MONTH`、`DAY`、`HOUR`、`MINUTE`、`WEEK`、`SECOND`、`YEAR_MONTH`、`DAY_HOUR`、`DAY_MINUTE`、`DAY_SECOND`、`HOUR_MINUTE`、`HOUR_SECOND`、`MINUTE_SECOND`，与函数`DATE_ADD`类似。

如果事件调度器状态是关闭的，需使用`SET GLOBAL event_scheduler = 1`打开，事件才能生效。

## 修改事件

禁用事件：

```sql
ALTER EVENT eventname DISABLE
```

## 删除事件

```sql
DROP EVENT eventname
```

## 创建触发器

```sql
CREATE TRIGGER triggername BEFORE|AFTER INSERT|UPDATE|DELETE
ON tablename FOR EACH ROW
statement_list
```

触发时间如下：

* `BEFORE`指在检查约束前触发。
* `AFTER`指在检查约束后触发。

触发器只能创建在永久表上，不能对临时表创建触发器。对同一个表相同触发时间的相同触发事件，只能定义一个触发器。

## 删除触发器

```sql
DROP TRIGGER [dbname.]triggername
```

# DML语句

DML语句，即数据操纵语句（Data Manipulation Languages）。其关键字包括：`INSERT`、`UPDATE`、`DELETE`、`SELECT`。

## 插入记录

```sql
INSERT [LOW_PRIORITY|DELAYED|HIGH_PRIORITY] INTO tablename [(colname1[, ...])] VALUES
(record1_value1[, ...])[, ...]
[ON DUPLICATE KEY UPDATE colname2=value2[, ...]]
```

当不指定字段名时，值的顺序需与表定义的字段排列顺序一致。

也可以使用如下的方式指定字段值：

```sql
INSERT INTO tablename SET {colname = value}[, ...]
```

使用查询的结果插入记录：

```sql
INSERT INTO tablename select_statement
```

## 更新记录

```sql
UPDATE tablename SET {colname = value}[, ...] [WHERE condition1 [AND|OR condition2][...]]
```

或：

```sql
UPDATE tablename[, ...] SET {[tablename.]colname = value}[, ...] [WHERE condition1 [AND|OR condition2][...]]
```

## 删除记录

```sql
DELETE FROM tablename [WHERE condition1 [AND|OR condition2][...]]
```

或：

```sql
DELETE tablename1[, ...] FROM tablename1[, ...] [WHERE condition1 [AND|OR condition2][...]]
```

## 查询记录

```sql
SELECT *|{[DISTINCT] {constant|[@|@@]variable|colname1 [AS alias1]}[, ...]}
FROM tablename[, ...]
[WHERE condition1 [AND|OR condition2][...]]
[GROUP BY colname2[, ...]] [WITH ROLLUP]
[HAVING having_condition1 [AND|OR having_condition2][...]]
[ORDER BY NULL|{{colname3 [DESC|ASC]}[, ...]}]
[LIMIT [offset_start,] row_count]
[INTO OUTFILE 'filename'
	[FIEDLS [TERMINATED BY 'string'] [[OPTIONALLY] ENCLOSED BY 'char'] [ESCAPED BY 'char']]
	[LINES [STARTING BY 'string'] [TERMINATED BY 'string']]
]
[{LOCK IN SHARE MODE}|{FOR UPDATE}]
```

`WITH ROLLUP`表示对分类聚合后的结果进行再汇总，不能和`ORDER BY`同时使用。

`ORDER BY`子句中，`NULL`为禁止排序，`DESC`为降序，`ASC`为升序，默认为`ASC`。

`LIMIT`子句中，，offset_start默认为0，即第一条记录。

表别名：

```sql
SELECT t.colname FROM table t
```

表连接:

```sql
SELECT *
FROM tablename1
{[INNER]|LEFT|RIGHT JOIN tablename2 ON condition}[ ...]
```

左连接包含左表中所有记录，右表可能出现空记录；右连接包含右表所有记录，左表可能出现空记录。默认为内连接，内连接也可以如下形式：

```sql
SELECT *
FROM tablename1, tablename2 WHERE condition
```

常见的子查询:

```sql
SELECT * FROM tablename1 WHERE colname1 [NOT] IN (SELECT colname2 FROM tablename2)
```

```sql
SELECT * FROM tablename1 WHERE colname1 =|!= (SELECT colname2 FROM tablename2)
```

```sql
SELECT * FROM tablename1 WHERE [NOT] EXISTS (SELECT * FROM tablename2 WHERE tablename1.colname1 = tablename2.colname2)
```

```sql
SELECT * FROM tablename1, (SELECT * FROM tablename) tablename2 WHERE condition
```

第二种用法仅适用于子查询只返回一行记录的情况。

联合:

```sql
select_statement1
{UNION [ALL] select_statement2}
[...]
```

伪表名：

```sql
SELECT something [FROM DUAL]
```

不使用`ALL`会对记录进行排重。

`SELECT INTO OUTFILE`使用的格式选项含义如下：

* `FIELDS TERMINATED BY 'string'`：字段分隔字符串，默认为'\t'。
* `FIELDS [OPTIONALLY] ENCLOSED BY 'char'`：字段引用符，默认为空。如加上`OPTIONALLY`则只用在字符串类型字段上。
* `FIELDS ESCAPED BY 'char'`：转义字符，默认为'\'。
* `LINES STARTING BY 'string'`：行首字符串，默认为空。
* `LINES TERMINATED BY 'string'`：行结束字符串，默认为'\n'。

相应的，`LOAD DATA INFILE`的语句如下：

```sql
LOAD DATA [LOCAL] INFILE 'filename' INTO TABLE tablename
[FIEDLS [TERMINATED BY 'string'] [[OPTIONALLY] ENCLOSED BY 'char'] [ESCAPED BY 'char']]
[LINES [STARTING BY 'string'] [TERMINATED BY 'string']]
[IGNORE n LINES]
[(colname[, ...])]
[SET {colname=expr}[, ...]]
```

`LOCAL`表示从客户端本地读取文件，默认从服务器读取文件。

大多数选项都与`SELECT INTO OUTFILE`相同，新增的选项含义如下：

* `IGNORE n LINES`：忽略输入文件中的前n行数据。
* (colname[, ...])：按照列出的字段顺序和字段数量加载数据。
* SET {colname=expr}[, ...]：将列做一定的值转换后再加载。

# DCL语句

DCL语句，即数据控制语句（Data Control Languages）。其关键字包括：`GRANT`、`REVOKE`。

## 授予权限

可同时创建用户。

```sql
GRANT {USAGE|ALL|{privtype[, ...]} [(column[, ...]])}[, ...]
ON [TABLE|FUNCTION|PROCEDURE] *|*.*|dbname.*|tablename
TO {{user|'user'}@{host|'host'} [IDENTIFIED BY [PASSWORD] 'password'] [REQUIRE SSL]}[, ...]
[WITH [GRANT OPTION] [resource_option[...]]]
```

`USAGE`只用于数据库登录，不能执行任何操作。

priv_type为`SELECT|INSERT|UPDATE|DELETE|INDEX|ALTER|CREAATE|DROP|GRANT|CREATE VIEW|SHOW VIEW|CREATE ROUTINE|ALTER ROUTINE|REFERENCES|RELOAD|SHUTDOWN|PROCESS|FILE|SHOW DB|SUPER|CREATE TMP TABLE|LOCK TABLES|EXECUTE|REPL SLAVE|REPL CLIENT`，大多与mysql数据库user表的*_priv对应。

管理权限不能指定数据库，`ON`后必须跟`*.*`。

使用`PASSWORD`表示password是经过`PASSWORD`函数加密的。

resource_option包括，设置为0表示删除限制：

* `MAX_QUERIES_PER_HOUR count`
* `MAX_UPDATES_PER_HOU count`
* `MAX_CONNECTIONS_PER_HOUR count`
* `MAX_USER_CONNECTIONS count`

## 回收权限

```sql
REVOKE {ALL|{priv_type|GRANT OPTION[,...]} [colname[, ...]]}[, ...]
ON [TABLE|FUNCTION|PROCEDURE] *|*.*|dbname.*|tablename
FROM {user|'user'}@{host|'host'}[, ...]
```

`USAGE`权限不能被回收，亦即，`REVOKE`不能删除用户。

**此外，还有以下用于权限控制的语句：**

## 创建用户

```sql
CREATE USER {user|'user'}@{host|'host'}
IDENTIFIED [WITH auth_plugin] BY [PASSWORD] 'password'
```

user为''表示任何用户。host为''、*、或%（含义与`LIKE`相同）表示任何外部主机，不包括localhost。

auth_plugin可使用：

* mysql_native_password
* caching_sha2_password

使用`PASSWORD`表示password是经过`PASSWORD`函数加密的。

## 修改用户

```sql
ALTER USER {user|'user'}@{host|'host'}
IDENTIFIED [WITH auth_plugin] BY [PASSWORD] 'password'
```

## 删除用户

```sql
DROP USER {user|'user'}@{host|'host'}[, ...]
```

## 修改密码

```sql
SET PASSWORD [FOR {user|'user'}@{host|'host'}] = PASSWORD('password')
```

## 查看权限

```sql
SHOW GRANTS [FOR {user|'user'}@{host|'host'}]
```

# 其他语句

## 分析、检查、优化、修复表

`ANALYZE`、`CHECK`、`OPTIMIZE`、`REPAIR`执行期间都会对表进行锁定。

分析表，使得SQL能够生成正确的执行计划。如果感觉实际的执行计划并不符合预期，进行分析表可能会解决问题：

```sql
ANALYZE [LOCAL|NO_WRITE_TO_BINLOG] TABLE tablename[, ...]
```

检查表，用于检查表或视图是否有错误（如视图定义中被引用的表不存在）：

```sql
CHECK TABLE tablename[, ...] [{QUICK|FAST|MEDIUM|EXTENDED|CHANGED}[...]]
```

优化表，可以将表中的碎片空间进行合并。如果已经删除表的很大一部分数据，或已经对含有可变长度行（含有`VARCHAR`、`BLOB`或`TEXT`的列）的表进行很多更改，则应该进行优化表：

```sql
OPTIMIZE [LOCAL|NO_WRITE_TO_BINLOG] TABLE tablename[, ...]
```

修复表，对坏表进行修复：

```sql
REPAIR [LOCAL|NO_WRITE_TO_BINLOG] TABLE tablename[, ...] [{QUICK|EXTENDED|USE_FRM}[...]]
```

## SHOW

关于`SHOW`语句的详情参阅官方文档：[https://dev.mysql.com/doc/refman/5.7/en/show.html](https://dev.mysql.com/doc/refman/5.7/en/show.html)。

`SHOW`语句返回元数据信息，元数据信息大多保存在`information_schema`库中。

| 语句                                                          | 作用                                    |
| ------------------------------------------------------------ | --------------------------------------- |
| SHOW BINLOG EVENTS [IN 'filename'] [FROM position]           | 列出二进制日志事件                         |
| SHOW CHARACTER SET [LIKE 'pattern']                          | 列出服务器支持的字符集                      |
| SHOW COLLATION [LIKE 'pattern']                              | 列出服务器支持的字符集校对规则               |
| SHOW [FULL] COLUMNS FROM [dbname.]tablename [LIKE 'pattern'] | 列出列                                   |
| SHOW CREATE DATABASE dbname                                  | 列出创建数据库语句                         |
| SHOW CREATE EVENT eventname                                  | 列出创建事件语句                           |
| SHOW CREATE FUNCTION funcname                                | 列出创建函数语句                           |
| SHOW CREATE PROCEDURE procname                               | 列出创建存储过程语句                        |
| SHOW CREATE TABLE [dbname.]tablename                         | 列出创建表语句                             |
| SHOW CREATE TRIGGER triggername                              | 列出创建触发器语句                         |
| SHOW CREATE VIEW [dbname.]viewname                           | 列出创建视图语句                           |
| SHOW DATABASES [LIKE 'pattern']                              | 列出数据库                                |
| SHOW ENGINE engine STATUS                                    | 列出存储引擎状态（如：INNODB）                 |
| SHOW ENGINES                                                 | 列出服务器支持的存储引擎                     |
| SHOW ERRORS                                                  | 列出服务器出现的错误                        |
| SHOW EVENTS                                                  | 列出事件                                  |
| SHOW FUNCTION CODE funcname                                  | 列出函数代码                               |
| SHOW FUNCTION STATUS [LIKE 'pattern']                        | 列出函数状态                               |
| SHOW GRANTS [FOR {user&#124;'user'}@{host&#124;'host'}]      | 列出账号权限                               |
| SHOW INDEX FROM [dbname.]tablename                           | 列出索引                                  |
| SHOW [BINARY/MASTER] LOGS                                    | 列出二进制日志文件                          |
| SHOW MASTER STATUS                                           | 列出主库状态                               |
| SHOW OPEN TABLES [FROM dbname] [LIKE 'pattern']              | 列出打开并缓存的表                          |
| SHOW PLUGINS                                                 | 列出已安装的插件                           |
| SHOW PROCEDURE CODE procname                                 | 列出存储过程代码                           |
| SHOW PROCEDURE STATUS [LIKE 'pattern']                       | 列出存储过程状态                           |
| SHOW PRIVILEGES                                              | 列出权限                                  |
| SHOW PROCESSLIST                                             | 列出服务器线程                             |
| SHOW PROFILE [ALL&#124;CPU&#124;{BLOCK IO}&#124;{PAGE FAULTS}&#124;SOURCE][, ...] FOR QUERY query_id | 列出查询的分析信息 |
| SHOW PROFILES                                                | 列出可分析的查询                            |
| SHOW RELAYLOG EVENTS [IN 'filename'] [FROM position]         | 列出中继日志事件                            |
| SHOW SLAVE HOSTS                                             | 列出从库信息                               |
| SHOW SLAVE STATUS                                            | 列出从库状态                               |
| SHOW [SESSION&#124;GLOBAL] STATUS [LIKE 'pattern']           | 列出状态变量，默认为SESSION                  |
| SHOW TABLE STATUS [FROM dbname] [LIKE 'pattern']             | 列出表和视图状态                            |
| SHOW [FULL] TABLES [FROM dbname] [LIKE 'pattern']            | 列出表和视图                               |
| SHOW TRIGGERS [FROM dbname] [LIKE 'pattern']                 | 列出触发器                                 |
| SHOW [SESSION&#124;GLOBAL] VARIABLES [LIKE 'pattern']        | 列出系统变量                               |
| SHOW WARNINGS                                                | 列出服务器警告                             |

## FLUSH

关于`FLUSH`语句的详情参阅官方文档：[https://dev.mysql.com/doc/refman/5.7/en/flush.html](https://dev.mysql.com/doc/refman/5.7/en/flush.html)。

| 语句                                                          | 作用                                    |
| ------------------------------------------------------------ | --------------------------------------- |
| FLUSH PRIVILEGES                                             | 刷新权限，会重新读取权限表                   |
| FLUSH TABLES                                                 | 刷新表，会关闭所有打开的表，并刷新查询缓存       |
| FLUSH USER_RESOURCES                                         | 刷新用户资源限制，会重置为0                  |

## 其他

| 语句                                       | 作用                                    |
| ----------------------------------------- | --------------------------------------- |
| DESC tablename|statement                  | 同EXPLAIN。指定tablename等价于SHOW COLUMNS FROM tablename，指定statement分析SQL语句的执行计划 |
| EXPLAIN tablename|statement               | 指定tablename等价于SHOW COLUMNS FROM tablename，指定statement分析SQL语句的执行计划 |
| KILL processid                            | 杀死线程                                 |
| SET [SESSION&#124;GLOBAL] [@&#124;@@]variable=value | 设置变量值，缺省为SESSION                  |
| SET NAMES charset                         | 同时修改当前会话的客户端、连接、返回结果字符集   |
| START SLAVE                               | 启动从库复制线程                          |
| STOP SLAVE                                | 停止从库复制线程                          |

# 命令

上述语句使用`mysql`工具交互执行时都需要使用语句分隔符（通常为“;”）结尾，但命令不需要。

| 命令                                       | 简写 | 作用                                              |
| ----------------------------------------- | ---- | ------------------------------------------------ |
| ? [topic]                                 | \?   | 同HELP。查看帮助，最顶层主题为contents                 |
| CLEAR                                     | \c   | 清除当前语句，不会执行                                |
| CONNECT [dbname [host]]                   | \r   | 重新连接服务器                                      |
| DELIMITER delimiter                       | \d   | 设置语句分隔符                                      |
| EDIT                                      | \e   | 使用$EDITOR指定的编辑器编辑语句                       |
| EGO                                       | \G   | 执行语句，将结果字段纵向排列                            |
| EXIT                                      | \q   | 同QUIT。退出                                         |
| GO                                        | \g   | 执行语句，将结果字段横向排列                            |
| HELP [topic]                              | \h   | 查看帮助                                            |
| NOPAGER                                   | \n   | 禁用pager，将结果打印至标准输出                         |
| NOTEE                                     | \t   | 禁用tee，不同时将结果追加至文件                         |
| PAGER [systemcommand]                     | \P   | 设置pager，将结果使用指定的系统命令打印，而不是打印至标准输出 |
| PRINT                                     | \p   | 打印当前命令                                         |
| PROMPT [prompt]                           | \R   | 设置交互提示语                                       |
| QUIT                                      | \q   | 退出                                                |
| REHASH                                    | \\#  | 重建自动完成哈希                                      |
| SOURCE filename                           | \\.  | 执行SQL文件                                         |
| STATUS                                    | \s   | 查看服务器状态                                       |
| SYSTEM [systemcommand]                    | \\!  | 执行系统命令                                         |
| TEE filename                              | \T   | 设置tee，同时将结果追加至文件                           |
| USE dbname                                | \u   | 切换数据库                                          |
| CHARSET charset                           | \C   | 设置字符集                                          |
| WARNINGS                                  | \W   | 显示警告信息                                         |
| NOWARNING                                 | \w   | 不显示警告信息                                       |
| RESETCONNECTION                           | \x   | 重置连接回话上下文                                    |

# 变量

`@`开头的变量为用户变量，`@@`开头的变量为系统变量，缺省为系统变量。

# 注释

从`#`、`--`起至行末的内容都视为注释。`/*`与`*/`之间所有行的内容也视为注释。

本文更新于2020-05-03，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

MySQL有4种日志：错误日志、二进制日志（BINLOG）、查询日志、慢查询日志。

# 错误日志

错误日志记录了mysqld的启动和停止，以及运行过程中发生的严重错误，其格式为纯文本，默认开启。

`SHOW VARIABLES`相关变量：

* log_error：当前的错误日志文件名。

mysqld命令行选项：

* --log-error[=filename]：错误日志文件名，默认为“主机名.err”，默认保存在datadir指定的目录中。

# 二进制日志

二进制日志记录了所有的DDL语句和DML语句，其格式为二进制，默认不开启。

二进制日志的格式分3种：基于语句（STATEMENT）的日志格式SBL、基于行（ROW）的日志格式RBL、混合（MIXED）格式。

`SHOW VARIABLES`相关变量：

* binlog_format：二进制日志格式。
* expire_logs_days：二进制日志过期天数。
* log_bin：是否开启二进制日志。
* log_bin_basename：二进制日志基于的文件名（实际的文件名会追加形如.000001的编号）。
* log_bin_index：二进制索引文件名。
* sql_log_bin：是否写入二进制日志。具有SUPER权限可禁止将自己的语句写入二进制日志。
* sync_binlog：0表示由文件系统控制二进制日志缓存的刷新，大于0表示每若干条二进制日志刷新文件系统缓存。

mysqld命令行选项：

* --binlog-do-db=dbname：没显式指定的数据库不记录二进制日志。可指定多次。
* --binlog-ignore-db=dbname：没显式忽略的数据库都记录二进制日志。可指定多次。
* --binlog-format=format：设置二进制日志格式，可为STATEMENT、ROW、MIXED。
* --innodb-safe-binlog：经常和--sync-binlog一起使用，令事务在日志中的记录更安全。
* --expire-logs-days=n：设置二进制日志的过期天数。
* --log-bin[=filename]：启用二进制日志并指定日志文件名，默认为“主机名-bin”，默认保存在datadir指定的目录中。

my.cnf配置[mysqld]：

* expire_logs_days=n：设置二进制日志过期天数。
* log-bin[=basename]：打开二进制日志并指定日志文件名，默认为“主机名-bin”，默认保存在datadir指定的目录中。需同时配置server-id，否则服务器会无法启动。
* server-id=id

删除日志有如下几种方法：

* 执行`RESET MASTER`，删除所有日志，新的日志编号从000001开始。
* 执行`PURGE MASTER LOGS TO 'hostname-bin.xxxxxx'`，将编号xxxxxx之前（不含）的日志删除。
* 执行`PURGE MASTER LOGS BEFORE 'yyyy-mm-dd HH:MM:ss'`，将指定时间之前的日志删除。

可使用`FLUSH LOGS`刷新日志文件。

# 查询日志

查询日志记录了所有语句，其格式为纯文本，默认不开启。

`SHOW VARIABLES`相关变量：

* sql_log_off：是否写入查询日志。

mysqld命令行选项：

* --general-log[=0|1]：是否启用查询日志。
* --general-log-file=filename：指定查询日志文件名，默认为“主机名.log”，默认保存在datadir指定的目录中。
* --log-output[={NONE|TABLE|FILE}[,...]]：设置查询日志和慢查询日志的保存方式。NONE为不保存，其优先级最高；TABLE为保存至表中，查询日志保存至mysql.general_log表，慢查询日志保存至msql.slow_logy表；FILE为保存至文件中，为默认值。

# 慢查询日志

慢查询日志记录所有执行时间（获得锁的时间不算执行时间）超过long_query_time（秒）并且扫描记录数不少于min_examined_row_limit的语句，其格式为纯文本，默认不开启。

默认情况下，有两种语句不会记录到慢查询日志：管理语句和不使用索引进行查询的语句。管理语句包括`ALTER TABLE`、`ANALYZE TABLE`、`CHECK TABLE`、`OPTIMIZE TABLE`、`REPAIR TABLE`、`CREATE INDEX`、`DROP INDEX`。

`SHOW VARIABLES`相关变量：

* slow_query_log：是否启用慢查询日志。
* slow_query_log_file：慢查询日志文件名。

mysqld命令行选项：

* --log-output[={NONE|TABLE|FILE}[,...]]：见查询日志相关选项说明。
* --log-queries-not-using-indexes：监控不使用索引进行查询的语句。
* --log-slow-admin-statements：监控管理语句。
* --slow-query-log[=0|1]：是否启用慢查询日志。
* --slow-query-log-file=filename：指定慢查询日志文件名，默认为“主机名-slow.log”，默认保存在datadir指定的目录中。

my.cnf配置[mysqld]：

* slow_query_log：是否启用慢查询日志。

# 其他日志分析工具

还可以安装使用mysqlsla、myprofi、mysql-explain-slow-log、mysqllogfilter等工具。

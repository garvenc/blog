本文更新于2021-05-09，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

在指定参数选项时有两种方式：

* “-”+单个缩写字符+值，值前可加上空白符（“-p”选项例外，若加上则认为稍后输入密码，空白后面的值并不被认作密码）。
* “--”+完整单词+“=”+值

所有工具会使用一些通用选项，通用选项可查看“mysql”小节。

# myisamchk——MyISAM表维护工具

```shell
myisamchk [options] tablename
```

options选项如下：

* -r, --repair：修复表。
* -o, --safe-recover：更安全的修复表。

# myisampack——MyISAM表压缩工具

使用很高的压缩率对MyISAM存储引擎的表进行压缩，但压缩后的表成为只读表。

```shell
myisampack [options] filename
```

# mysql——客户端连接工具

```shell
mysql [options] [database]
```

如不指定database，则连接成功后需使用`USE database` 指定要操作的数据库。

最后加上输入重定向`< filename`可以执行文件内容。

options选项如下：

* 帮助选项（所有工具的通用选项，不再复述）
	* -?, --help：查看帮助。
* 连接选项（所有工具的通用选项，不再复述）
	* -u, --user=user：指定用户名。
	* -p, --password[=password]：指定密码。
	* -h, --host=host：指定服务器IP或域名，默认为localhost。
	* -P, --port=port：指定服务器端口，默认为3306。
	* --protocol=protocol：指定连接协议，可为tcp、socket、pipe、memory。如使用localhost作为主机名，默认使用socket。
* 客户端字符集选项（所有工具的通用选项，不再复述）
	* --default-character-set=charset：设置客户端字符集。相当于执行`SET NAMES charset`。
* 执行选项
	* -e, --execute=sql：执行SQL语句。可连续执行多个SQL语句，之间用“;”隔开。
* 格式化选项
	* -E, --vertical：将记录的字段竖向排列，类似SQL语句以`\G`结束。
	* -s, --silent：去掉线条框显示，字段之间用tab分隔，每条记录一行。
* 错误处理选项
	* -f, --force：在批量执行SQL时，如执行出错，跳过并强制执行后续的SQL。如不加该选项，则会停止并退出。
	* -v, --verbose：显示更多信息。
	* --show-warnings：显示警告信息。
* 其他选项
	* --local-infile[=0|1]：是否使用客户端本地文件导入。

# mysqladmin——MySQL管理工具

```shell
mysqladmin [options] {command [command_options]}[ ...]
```

options选项和mysql的类似，不再赘述。

可以执行的命令command如下：

* create databasename
* debug
* drop databasename
* extended-status：查看服务器状态，同`SHOW STATUS`。
* flush-hosts
* flush-logs
* flush-status
* flush-tables
* flush-threads
* flush-privileges
* kill id,id,...
* password [new-password]
* ping
* processlist
* reload
* refresh
* shutdown
* status
* start-slave
* stop-slave
* variables
* version

# mysqlbinlog——日志管理工具

每一条日志都带有位置和时间，下面的示例中，位置为603，时间为2018-03-13 18:05:15。因日志格式为ROW且加上了-v选项，最后还显示了可读的日志内容。

```
# at 603
#180313 18:05:15 server id 1  end_log_pos 649 CRC32 0x5bba1b83 	Write_rows: table id 245 flags: STMT_END_F

BINLOG '
W6KnWhMBAAAAMQAAAFsCAAAAAPUAAAAAAAEABHRlc3QAA2VtcAACAw8CZAAAzYe0ig==
W6KnWh4BAAAALgAAAIkCAAAAAPUAAAAAAAEAAgAC//wFAAAABXdvcmxkgxu6Ww==
'/*!*/;
### INSERT INTO `test`.`emp`
### SET
###   @1=5
###   @2='world'
```

```shell
mysqlbinlog [options] log_file[, ...]
```

options选项如下：

* -d, --database=database：只列出指定数据库的日志。
* -o, --offset=n：忽略日志中前n条命令。
* -r, --result-file=filename：输出至指定文件。
* -s, --short-form：显示简单格式，省略掉一些信息。
* -v：如果日志是ROW格式，加上-v或-vv选项进行查看。否则会显示base64字符串。
* --base64-output=value：若为decode-rows且使用-v，则ROW格式的日志只显示对应的SQL语句，不显示base64字符串。
* --set-charset=charset：在输出的第一行加上`SET NAMES charset`。
* --start-datetime=datetime：指定开始时间（含）。
* --start-position=position：指定开始位置（含）。
* --stop-datetime=datetime：指定结束时间（不含）。
* --stop-position=position：指定结束位置（不含）。

# mysqlcheck——表维护工具

mysqlcheck实际上集成了`ANALYZE`、`CHECK`、`OPTIMIZE`、`REPAIR`功能。

```shell
mysqlcheck [options] dbname [tablename[ ...]]
mysqlcheck [options] --database dbname[ ...]
mysqlcheck [options] --all-database
```

options选项如下：

* -a, --analyze：分析表。
* -c, --check：检查表，默认选项。
* -o, --optimize：优化表。
* -r, --repair：修复表。
* --all-databases：所有数据库。
* --databases dbname[ ...]：指定数据库，可指定多个。

# mysqldump——数据导出工具

```shell
mysqldump [options] dbname [tablename[ ...]]
mysqldump [options] --databases dbname[ ...]
mysqldump [options] --all-databases
```

通常会在最后加上`> filename`将导出结果重定向至文件。

options选项如下：

* 数据源选项
	* -A, --all-databases：导出所有数据库。
	* -B, --databases dbname[ ...]：指定数据库，可指定多个。会包含`CREATE DATABASE`。
	* --ignore-table=dbname.tablename：不导出指定的表，可使用多次。
* 输出内容选项
	* -d, --no-data：不包含表数据。
	* -n, --no-create-db：不包含`CREATE DATABASE`。
	* -R, --routines：包含存储过程和函数。
	* -t, --no-create-info：不包含`CREATE TABLE`。
	* --add-drop-database：在每个`CREATE DATABASE`前加上`DROP DATABASE`。默认加上此选项。
	* --add-drop-table：在每个`CREATE TABLE`前加上`DROP TABLE`。默认加上此选项。
	* --triggers：包含触发器。默认加上此选项。
* 输出格式选项
	* -c, --complete-insert：使输出的`INSERT`语句中包含字段名。
	* -T, --tab=dirname：备份为建表文件（.sql）和以数据文件（.txt），实际上是`SELECT INTO OUTFILE`的命令行接口，经常和下面几个选项配合使用：
	* --compact：使输出结果简洁，不包含注释。
	* --extended-insert：使用包括多条记录的多行`INSERT`语法。
	* --fields-enclosed-by=name：字段引用符。与-T一起使用。
	* --fields-escaped-by=name：转义字符。与-T一起使用。
	* --fields-optionally-enclosed-by=name：字段可选引用符，只用在字符串类型字段上。与-T一起使用。
	* --fields-terminated-by=name：字段分隔符。与-T一起使用。
	* --lines-terminated-by=name：行结束符。与-T一起使用。
* 其他选项
	* -F, --flush-logs：备份前刷新日志。即关闭旧日志，生成新日志，使恢复时直接从新日志开始重做。
	* -l, --lock-tables：给所有表加读锁。
	* --quick：用于导出大表。会一次一行检索表中的行而不是检索所有行，并在输出前将其缓存至内存中。
	* --single-transaction：使事务型存储引擎得到一个快照，令备份数据保证一致性。事务型存储引擎使用此选项比使用-l更好。

# mysqldumpslow——慢查询日志汇总工具

```shell
mysqldumpslow [options] log_file[, ...]
```

# mysqlhotcopy——MyISAM热备份工具

mysqlhotcopy是一个Perl脚本，需要安装Perl的MySQL数据库接口包。其只适用于MyISAM存储引擎，且需运行在Linux环境中。

```shell
mysqlhotcopy [options] dbname[, ...] [dirname]
```

options选项如下：

* --allowold：如果备份目录下有同名备份（也为一目录），则将旧的改名为“备份名_old”。
* --addtodest：如果备份目录下有同名备份（也为一目录），则将文件加入其中。
* --noindices：不备份索引文件。
* --flushlog：表被锁定后刷新日志。

# mysqlimport——数据导入工具

mysqlimport用来导入`mysqldump -T`导出的数据文件（.txt），实际上是`LOAD DATA INFILE`的命令行接口。

```shell
mysqlimport [options] [--local] dbname txtfile[, ...]
```

options选项如下：

* -L, --local：使用客户端本地文件导入。
* --fields-enclosed-by=name：字段引用符。与-T一起使用。
* --fields-escaped-by=name：转义字符。与-T一起使用。
* --fields-optionally-enclosed-by=name：字段可选引用符，只用在字符串类型字段上。与-T一起使用。
* --fields-terminated-by=name：字段分隔符。与-T一起使用。
* --lines-terminated-by=name：行结束符。与-T一起使用。
* --ignore-lines=n：忽略前n行。

数据导入至txtfile文件名指定的表。

# mysqlshow——数据库对象查看工具

```sell
mysqlshow [options] [dbname [tablename [colname]]]
```

options选项如下：

如果不加任何选项，会显示所有数据库。

* --count：显示数据库或表的统计信息。可以不指定数据库，指定数据库，或指定表。
* -k, --keys：显示指定表的表结构和索引信息，和`SHOW FULL COLUMNS`与`SHOW INDEX`的结果一致。
* -i, --status：显示表的状态信息，和`SHOW TABLE STATUS`的结果一致。

# mysqlslap——压力测试工具

```shell
mysqlslap [options]
```

options选项如下：

* -a：使用自动生成的SQL。
* -c N[,...]：各基准测试的并发连接数（默认为1）。
* -i N：每个基准测试的迭代次数。
* --auto-generate-sql-load-type=TYPE：自动生成的SQL的类型。读为read，写为write，混合读写为mixed（默认）。
* --engine=ENGINE[,...]：各基准测试的存储引擎。MyISAM为myisam，InnoDB为innodb。
* --number-of-queries=N：单次迭代中所有连接的总请求数。会平均分配到-c指定的连接。如不指定此选项，则只进行连接不进行请求。

# perror——错误代码查看工具

```shell
perror [options] errorcode[, ...]
```

errorcode为错误码，位于错误信息的“ERROR”后面。

# replace——文本替换工具

```shell
replace {from to}[...] -- file[ ...]
replace {from to}[...] < fromfile [> tofile]
```

使用“--”表示字符串对结束，文件名开始，替换完后会覆盖原文件。

使用重定向不会覆盖原文件。

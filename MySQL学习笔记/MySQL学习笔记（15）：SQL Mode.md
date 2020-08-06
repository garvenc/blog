本文更新于2019-06-29，使用MySQL 5.7，操作系统为Deepin 15.4。

与其他数据库不同，MySQL可以运行在不同的SQL Mode（SQL模式）下。

可通过变量`@@sql_mode`查看或设置当前的SQL Mode。

下面是一些常用的SQL Mode：

* `ANSI`：使语法和行为更符合标准SQL。为非严格模式，等同于`REAL_AS_FLOAT`、`PIPES_AS_CONCAT`、`ANSI_QUOTES`、`IGNORE_SPACE`的组合。
* `NO_BACKSLASH_ESCAPES`：使反斜杠成为普通字符。
* `NO_TABLE_OPTION`：去掉`SHOW CREATE TABLE`中的`ENGINE`相关的内容。
* `PIPES_AS_CONCAT`：将'||'视为字符串连接符，就像在Oracle中一样。
* `STRICT_TRANS_TABLES`：严格模式。当插入非法日期、字段长度超长等不正确的值时，给出错误而不是警告。
* `TADITIONAL`：等同于`STRICT_TRANS_TABLES`、`STRICT_ALL_TABLES`、`NO_ZERO_IN_DATE`、`NO_ZERO_DATE`、`ERROR_FOR_DIVISION_BY_ZERO`、`TRADITIONAL`、`NO_AUTO_CREATE_USER`的组合。

MySQL还提供若干数据库组合模式，以便在其他异构数据库之间迁移数据：`DB2`、`MAXDB`、`MSSQL`、`ORACLE`、`POSTGRESQL`。

本文更新于2020-05-03，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

MySQL的备份分为逻辑备份和物理备份，物理备份分为冷备份和热备份。恢复分为完全恢复和不完全恢复，不完全恢复又分为基于时间点的恢复和基于位置的恢复。

# 逻辑备份和恢复

使用mysqldump进行逻辑备份，如`mysqldump dbname > filename`。

为了保证数据备份的一致性，MyISAM存储引擎需加上-l参数，如InnoDB等事务存储引擎应加上--single-transaction。

恢复包括两个步骤：

1. 使用备份数据恢复，如`mysql dbname < backupfilename`。
1. 将备份后至恢复前的日志重做（不要把上一步执行时的二进制日志也重做了），如`mysqlbinlog logfilename | mysql dbname`。可进行不完全恢复，但要注意是否包含完整的事务，如`mysqlbinlog --start-date=start logfilename | mysql dbname`或`mysqlbinlog --start-position=position logfilename | mysql dbname`。

# 物理备份和恢复

## 冷备份和恢复

冷备份就是停掉数据库服务，拷贝数据文件。

## 热备份和恢复

对MyISAM存储引擎，热备份可使用`mysqlhotcopy`，本质就是将要备份的表加读锁，然后拷贝数据文件。

对InnoDB存储引擎，可安装使用ibbackup、Xtrabackup（包括xtrabackup和innobackupex两个主要工具）等工具。

# 表数据导入导出

表数据导出有两种方法：

* 使用`SELECT ... INTO OUTFILE filename [options]`。
* 使用`mysqldump -T`。

表数据导入有两种方法：

* `LOAD DATA [LOCAL] INFILE filename INTO TABLE tablename [options]`。
* `mysqlimport dbname filename`。

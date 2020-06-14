本文更新于2020-05-04，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

# 忘记roo密码

1. 使用`mysqld_safe --skip-grant-tables`跳过权限系统启动服务。
1. 不使用账号密码，直接使用`mysql`登录。
1. 使用`UPDATE mysql.user SET authentication_string = PASSWORD('password') WHERE user = 'root' [AND host = 'host']`，不能使用`SET PASSWORD`，因为--skip-grant-tables会导致其失败。
1. 使用`FLUSH PRIVILEGES`或重启服务后，新密码生效。

# 修改密码

修改密码有以下几种办法：

* `mysqladmin password 'password'
* `SET PASSWORD [FOR user@host] = PASSWORD('password')`
* `GRANT USAGE ON *.* TO user@host IDENTIFIED BY [PASSWORD] 'password'`
* `UPDATE mysql.user SET authentication_string = PASSWORD('password') WHERE Host = 'host' AND User = 'user'; FLUSH PRIVILEGES;`

# MyISAM存储引擎的表损坏

一张损坏的表的症状通常是查询意外中断并且能看到下述错误：

* “tbl_name.frm”被锁定不能更改。
* 不能找到文件“tbl_name.MYI”（Errcode: nnn）。
* 文件意外结束。
* 记录文件被损坏。
* 从表处理器得到错误nnn。

解决方法有：

* 使用工具：`myisamchk -r tablename`或`myisamchk -o tablename`。
* 使用语句：`CHECK TABLE tablename`和`REPAIR TABLE tablename`。

# 数据目录磁盘空间不足的问题

对于MyISAM存储引擎的表，可将表的数据文件和索引文件mv到磁盘空间充足的分区上，然后在原文件处创建符号链接。mv前必须停机或将表锁定。

对于InnoDB存储引擎的表，可以增加一个新的数据文件，具体实现方法是在参数innodb_data_file_path中增加此文件的绝对路径，如：innodb_data_file_path=/home/ibdata1:2000M;/home1/ibdata2:2000M:autoextend。

# mysql.sock丢失后如何连接数据库

如果指定localhost作为主机名，则会默认使用UNIX套接字文件连接，而不是TCP/IP。通过--protocol=tcp|socket|pipe|memory选项，可以显示地指定连接协议。

# 访问内网数据库

通过中转服务器，使用ssh管道，或MySQL Proxy工具。

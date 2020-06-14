本文更新于2020-04-05，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

# 安装

MySQL Utilities需单独安装，如使用apt-get命令：

```shell
apt-get install mysql-utilities
```

# 分类

MySQL工具根据用途可以分为如下几类：

* 审计日志管理：mysqlauditadmin、mysqlauditgrep。
* 数据库检查比较：mysqldbcompare、mysqldiff、mysqlindexcheck。
* 数据库导入导出：mysqldbexport、mysqldbimport。
* 数据库克隆：mysqldbcopy、mysqlserverclone、mysqluserclone。
* 数据库复制：mysqlfailover、mysqlreplicate、mysqlrpladmin、mysqlrplcheck、mysqlrplshow。
* 数据库过滤：mysqlmetagrep、mysqlprocgrep。
* 数据空间查询：mysqldiskusage。
* MySQL Utilities客户端：mysqluc。

# mysqldbcompare——数据库比较工具

比较对象的定义和数据记录。

```shell
mysqldbcompare --server1=USER1:PWD1@HOST1:PORT1 --server2=USER2:PWD2@HOST2:PORT2 DB1:DB2
```

# mysqldbcopy——数据库复制工具

```shell
mysqldbcopy --source=USER1:PWD1@HOST1:PORT1 --destination=USER2:PWD2@HOST2:PORT2 SRC_DB:DST_DB
```

# mysqldiff——数据库对象定义比较工具

比较对象的定义。

```shell
mysqldiff --server1=USER1:PWD1@HOST1:PORT1 --server2=USER2:PWD2@HOST2:PORT2 {DB1[.OBJECT1]:DB2[.OBJECT2]}[...]
```

输出结果中，“-”表示从server1中删除的定义，“+”表示server2中增加的定义。

# mysqldiskusage——磁盘使用情况查看工具

```shell
mysqldiskusage --server=USER:PWD@HOST:PORT DB --all
```

# mysqlindexcheck——索引检查工具

可以识别冗余的索引。

```shell
mysqlindexcheck --server=USER:PWD@HOST:PORT {DB[.TABLE]}[...]
```

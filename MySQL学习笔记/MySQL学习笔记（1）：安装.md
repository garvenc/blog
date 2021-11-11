本文更新于2021-08-04。

[TOC]

# 安装

在Windows下可以采用noinstall包或图形化安装，在Linux下可以采用apt-get/dpkg、yum/rpm、二进制包、源码包安装。

## 使用apt-get安装

以Deepin 15.9下安装MySQL 5.7为例。

```shell
sudo apt-get install mysql-server
```

安装过程无初始化MySQL的root密码提示。登录可采用如下方法之一：

* 使用操作系统root用户直接使用`mysql`免密登录。
* 在`/etc/mysql/debian.cnf`中查看组`[client]`的`user`（如：debian-sys-maint）和`password`，使用其登录。

登录后修改密码：

```sql
ALTER USER root@localhost
IDENTIFIED WITH mysql_native_password BY 'password'
```

注意，如此修改root@localhost的密码后，`mysql.user`表中的`plugin`将从`auth_socket`变为`mysql_native_password`，此后能使用密码登录，但不能再用操作系统root用户直接使用`mysql`免密登录。修改debian-sys-maint@localhost的密码同理。

## 在Debian 8.6下安装

在安装mysql-community-server时，会提示初始化MySQL的root密码。

```shell
wget http://ftp.br.debian.org/debian/pool/main/liba/libaio/libaio1_0.3.110-1_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/m/mecab/libmecab2_0.996-1.1_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/n/numactl/libnuma1_2.0.10-1_amd64.deb
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-server_5.7.19-1debian8_amd64.deb-bundle.tar

sudo dpkg -i libaio1_0.3.110-1_amd64.deb
sudo dpkg -i libmecab2_0.996-1.1_amd64.deb
sudo dpkg -i libnuma1_2.0.10-1_amd64.deb

mkdir mysql
tar -xv -C mysql -f mysql-server_5.7.19-1debian8_amd64.deb-bundle.tar
cd mysql
sudo dpkg -i mysql-common_5.7.19-1debian8_amd64.deb
sudo dpkg -i mysql-community-client_5.7.19-1debian8_amd64.deb
sudo dpkg -i mysql-client_5.7.19-1debian8_amd64.deb
sudo dpkg -i mysql-community-server_5.7.19-1debian8_amd64.deb
```

## 在Debian 10下安装

在安装mysql-community-server时，会提示初始化MySQL的root密码。

```shell
wget http://ftp.br.debian.org/debian/pool/main/p/psmisc/psmisc_23.2-1_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/m/mecab/libmecab2_0.996-1.1_amd64.deb
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-server_5.7.35-1debian10_amd64.deb-bundle.tar

sudo dpkg -i psmisc_23.2-1_amd64.deb
sudo dpkg -i libmecab2_0.996-1.1_amd64.deb

mkdir mysql
tar -xv -C mysql -f mysql-server_5.7.35-1debian10_amd64.deb-bundle.tar
cd mysql
sudo dpkg -i mysql-common_5.7.35-1debian10_amd64.deb
sudo dpkg -i mysql-community-client_5.7.35-1debian10_amd64.deb
sudo dpkg -i mysql-client_5.7.35-1debian10_amd64.deb
sudo dpkg -i mysql-community-server_5.7.35-1debian10_amd64.deb
```

## 在CentOS 7.5下安装

以CentOS 7.5下安装MySQL 8.0为例。

### 卸载MariaDB

yum服务器上移除了MySQL相关的软件包。CentOS7默认安装MariaDB相关的库，可能会与MySQL冲突，先将其卸载。

1. 查看已安装的MariaDB相关rpm包。

	```shell
	rpm -qa | grep mariadb
	```
1. 查看已安装的MariaDB相关yum包，包名需根据`rpm`命令的结果判断。

	```shell
	yum list mariadb-libs
	```
1. 移除已安装的MariaDB相关yum包，包名需根据`yum list`命令的结果判断。

	```shell
	sudo yum remove mariadb-libs
	```

### 安装MySQL

下面安装时如缺少Perl相关的包，执行`yum install -y perl-Module-Install`。

因包之间的依赖关系，各`rpm`命令必须按序执行。

```shell
wget https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.11-1.el7.x86_64.rpm-bundle.tar

mkdir mysql
tar -xv -C mysql -f mysql-8.0.11-1.el7.x86_64.rpm-bundle.tar
cd mysql
sudo rpm -ivh mysql-community-common-8.0.11-1.el7.x86_64.rpm
sudo rpm -ivh mysql-community-libs-8.0.11-1.el7.x86_64.rpm
sudo rpm -ivh mysql-community-client-8.0.11-1.el7.x86_64.rpm
sudo rpm -ivh mysql-community-server-8.0.11-1.el7.x86_64.rpm
```

### 初始化密码

1. 启动MySQL服务，第一次启动时会初始化数据目录`/var/lib/mysql`。

	```shell
	sudo service mysqld start
	```
1. 使用初始化后root@localhost的临时密码登录，临时密码记录在`/var/log/mysql.log`，其中有一行：“A temporary password is generated for root@localhost:”。
1. 修改root@localhost的密码，密码必须足够复杂，如包含大写、小写、数字、符号。

	```sql
	ALTER USER root@localhost
	IDENTIFIED WITH mysql_native_password BY 'password'
	```

### 修改密码认证插件

MySQL 8.0.4开始使用caching_sha2_password密码认证插件，很多旧版的客户端都不支持，为了后向兼容可修改为mysql_native_password。如不需修改，跳过此步骤。

在MySQL配置文件`/etc/my.cnf`的`[mysqld]`组中增加一行（配置文件本身带有这一行，只需把注释符去掉）：`default_authentication_plugin=mysql_native_password`。然后重启MySQL服务。

同时，需修改用户使用的密码认证插件（如该用户不会在旧版客户端登录，则可忽略此步骤）：

```sql
ALTER USER user@host
IDENTIFIED WITH mysql_native_password BY 'password'
```

或在创建用户时：

```sql
CREATE USER user@host
IDENTIFIED WITH mysql_native_password BY 'password'
```

# 服务启动、停止、重启、状态查看

Linux下的MySQL服务名为`mysql`或`mysqld`，以`mysql`为例。

* 启动：`service mysql start`或`systemctl start mysql`。
* 停止：`service mysql stop`或`systemctl stop mysql`。
* 重启：`service mysql restart`或`systemctl restart mysql`。
* 状态查看：`service mysql status`或`systemctl status mysql`。

# Linux下的文件布局

安装后默认的文件布局如下：

* /etc/mysql/：配置文件目录。
* /usr/bin/：客户端程序和脚本目录。
* /usr/sbin/：mysqld服务器目录。
* /usr/share/doc/：文档目录。
* /usr/include/mysql/：包含（头）文件目录。
* /usr/lib/mysql/：库文件目录。
* /usr/share/mysql/：错误消息和字符集文件目录。
* /usr/share/sql-bench/：基准程序目录。
* /var/lib/mysql/：日志文件（错误日志除外）和数据库文件目录。
* /var/log/mysql/：错误日志文件目录。

# 默认数据库

* information_schema：数据库元数据信息。这是一个虚拟数据库，并不存在于磁盘中。
	* CHARACTER_SETS：所有字符集。
	* COLLATIONS：所有字符集校对规则。
	* COLUMNS：所有列的信息。
	* INNODB_LOCKS：所有的InnoDB锁。
	* INNODB_LOCK_WAITS：所有InnoDB锁的等待。
	* OPTIMIZER_TRACE：优化器trace结果。
	* PARTITIONS：所有分区的信息。
	* PROFILING：所有profiling的信息。
	* ROUTINES：所有存储过程和函数的信息。
	* SCHEMATA：所有数据库的信息。
	* STATISTICS：所有索引的信息。
	* TABLES：所有表的信息。
	* TRIGGERS：所有触发器的信息。
	* VIEWS：所有视图的信息。
* mysql：用户权限和MySQL运行所需的信息。
	* columns_priv：列权限。
	* db：数据库权限。
	* tables_priv：表权限。
	* user：用户权限。

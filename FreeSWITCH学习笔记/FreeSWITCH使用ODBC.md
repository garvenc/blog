本文更新于2022-04-10，使用Debian 10、FreeSWITCH 1.10.7、MySQL 5.7.35。

1. 安装ODBC：
	```shell
	sudo apt install unixodbc unixodbc-dev
	```
1. 安装ODBC的MySQL驱动程序：
	```shell
	wget https://downloads.mysql.com/archives/get/p/10/file/mysql-connector-odbc-8.0.19-linux-debian10-x86-64bit.tar.gz
	tar -xv -f mysql-connector-odbc-8.0.19-linux-debian10-x86-64bit.tar.gz
	sudo mkdir /usr/local/mysql-connector-odbc
	sudo cp -r mysql-connector-odbc-8.0.19-linux-debian10-x86-64bit/* /usr/local/mysql-connector-odbc 
	```
1. 修改ODBC的配置，配置文件为/etc/odbc.ini，配置内容示例如下：
	```ini
	[freeswitch]
	Description = Test FreeSWITCH
	Driver = /usr/local/mysql-connector-odbc/lib/libmyodbc8a.so
	SERVER = localhost
	PORT = 3306
	DATABASE = test
	USER = root
	PASSWORD = 1234
	OPTION = 67108864
	SocketBufferSize = 4096
	FetchBufferSize = 100
	CHARSET = UTF8
	```
	说明如下，需根据实际情况填写：
	* []：DSN名字，用于在其它地方引用
	* Description：描述，不影响使用。
	* Driver：数据库驱动，位于上一步安装的路径中。
	* SERVER：数据库服务器主机。
	* PORT：数据库服务器端口。
	* DATABASE：数据库服务器的数据库名。
	* USER：数据库服务器用户名。
	* PASSWORD：数据库服务器密码。
	* OPTION：67108864表示将多条SQL语句放到一起执行以提高效率，如果没有这一项会出错。
1. 测试ODBC连接：
	```shell
	isql freeswitch -v
	```
	需使用上一步配置中实际的DSN名字。需先创建上一步配置中指定数据库名的数据库，否则会连接失败。如连接成功则会提示：
	```
	+---------------------------------------+
	| Connected!                            |
	|                                       |
	| sql-statement                         |
	| help [tablename]                      |
	| quit                                  |
	|                                       |
	+---------------------------------------+
	SQL> 
	```
1. 重新编译安装FreeSWITCH（笔者的源代码目录为/usr/src/freeswitch，根据实际情况修改）：
	```shell
	cd /usr/src/freeswitch
	./bootstrap.sh -j
	./configure
	make
	make install
	```
	执行`make install`前需将freeswitch进程停止。
1. 修改FreeSWITCH配置，使用ODBC连接数据库。各模块的配置有所不同（笔者的安装目录为/usr/local/freeswitch，根据实际情况修改），如：
	* 将core.db替换为ODBC：
		
		配置/usr/local/freeswitch/conf/autoload_configs/switch.conf.xml：
		```xml
		<param name="core-db-dsn" value="odbc://freeswitch::" />
		```
		需使用/etc/odbc.ini配置中实际的DSN名字，并需先创建/etc/odbc.ini配置中指定数据库名的数据库。
	* 将internal Profile替换为ODBC：

		配置/usr/local/freeswitch/conf/sip_profiles/internal.xml：
		```xml
		<param name="odbc-dsn" value="odbc://freeswitch::"/>
		```
		需使用/etc/odbc.ini配置中实际的DSN名字，且/etc/odbc.ini中的数据库名DATABASE必需为internal，并需先创建internal数据库。
1. 重启freeswitch进程。

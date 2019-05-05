# 说明

本文写于2017-05-20，使用MySQL-5.7.18。操作系统为64位CentOS Linux release 7.2.1511 (Core)，以桌面形式安装。

# 卸载MariaDB

CentOS7默认安装MariaDB而不是MySQL，而且yum服务器上也移除了MySQL相关的软件包。因为MariaDB和MySQL可能会冲突，故先卸载MariaDB。

1. 查看已安装的MariaDB相关rpm包。

	```shell
	rpm -qa | grep mariadb
	```
1. 查看已安装的MariaDB相关yum包，包名需根据`rpm`命令的结果判断。

	```shell
	yum list mariadb-libs
	```
1. 移除已安装的MariaDB相关yum包，包名需根据`yum list`命令的结果判断。此步骤需要root权限。

	```shell
	yum remove mariadb-libs
	```

# 下载MySQL rpm包

由于软件包很大，可以先用其他方式（如迅雷）下载。使用rpm方式，还可在无法联网的条件下安装——这是yum无法做到的。如需安装其他版本的MySQL，请自行到官网搜索相应的rpm下载链接。

```shell
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.18-1.el7.x86_64.rpm-bundle.tar
```

# 使用rpm包安装MySQL

以下步骤需要root权限。且因包之间的依赖关系，各`rpm`命令必须按序执行。

```shell
mkdir mysql-5.7.18
tar -xv -f mysql-5.7.18-1.el7.x86_64.rpm-bundle.tar -C mysql-5.7.18
cd mysql-5.7.18/
rpm -ivh mysql-community-common-5.7.18-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.18-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.18-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-5.7.18-1.el7.x86_64.rpm
```

安装成功后，也可把安装文件和临时文件删除。

```shell
cd ..
rm -rf mysql-5.7.18
rm mysql-5.7.18-1.el7.x86_64.rpm-bundle.tar
```

# 修改MySQL初始密码

以下步骤需要root权限。

1. 由于一开始并不知道密码，先修改配置文件`/etc/my.cnf`令MySQL跳过登录时的权限检验。加入一行：

	```
	skip-grant-tables
	```
1. 重启MySQL。

	```shell
	service mysqld restart
	```
1. 免密码登录MySQL。

	```shell
	mysql
	```
1. 在mysql客户端执行如下命令，修改root密码。

	```
	use mysql;
	UPDATE user SET authentication_string = password('your-password') WHERE host = 'localhost' AND user = 'root';
	quit;
	```
1. 修改配置文件`/etc/my.cnf`删除此前新增那一行`skip-grant-tables`，并重启MySQL。这一步非常重要，不执行可能导致严重的安全问题。
1. 使用刚刚设置的密码登录。

	```shell
	mysql -u root -p
	```
1. MySQL会强制要求重新修改密码，且不能为简单规则密码。

	```
	ALTER USER root@localhost IDENTIFIED BY 'your-new-password';
	```

步骤可能略显麻烦，还未想到其他办法，先这样用着。

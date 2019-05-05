# 说明

本文写于2017-10-03，使用MySQL 5.7，操作系统为64位 Debian GNU/Linux 8.6 (jessie)。

# 安装

因apt仓库将mysql相关的包移除，需要自己去官网下载之，以及相关的依赖包。
在安装mysql-community-server时，会提示输入MySQL的root密码。

以下部分步骤需要root权限。

```shell
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-server_5.7.19-1debian8_amd64.deb-bundle.tar
wget http://ftp.br.debian.org/debian/pool/main/liba/libaio/libaio1_0.3.110-1_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/m/mecab/libmecab2_0.996-1.1_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/n/numactl/libnuma1_2.0.10-1_amd64.deb

dpkg -i libaio1_0.3.110-1_amd64.deb
dpkg -i libmecab2_0.996-1.1_amd64.deb
dpkg -i libnuma1_2.0.10-1_amd64.deb

mkdir mysql_5.7
tar -xv -C mysql_5.7 -f mysql-server_5.7.19-1debian8_amd64.deb-bundle.tar
cd mysql_5.7
dpkg -i mysql-common_5.7.19-1debian8_amd64.deb
dpkg -i mysql-community-client_5.7.19-1debian8_amd64.deb
dpkg -i mysql-client_5.7.19-1debian8_amd64.deb
dpkg -i mysql-community-server_5.7.19-1debian8_amd64.deb
```

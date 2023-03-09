本文更新于2023-02-22。

[TOC]

# 安装社区版（MongoDB Community Server）

各模块可按需下载。如需安装其他版本，请到官网[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)搜索相应的链接。

## 安装4.4.5

操作系统为Debian 10.5。

### 安装mongod

```shell
wget https://repo.mongodb.org/apt/debian/dists/buster/mongodb-org/4.4/main/binary-amd64/mongodb-org-server_4.4.5_amd64.deb
sudo dpkg -i mongodb-org-server_4.4.5_amd64.deb
```

### 安装mongo

```shell
wget https://repo.mongodb.org/apt/debian/dists/buster/mongodb-org/4.4/main/binary-amd64/mongodb-org-shell_4.4.5_amd64.deb
sudo dpkg -i mongodb-org-shell_4.4.5_amd64.deb
```

### 安装mongos

```shell
wget https://repo.mongodb.org/apt/debian/dists/buster/mongodb-org/4.4/main/binary-amd64/mongodb-org-mongos_4.4.5_amd64.deb
sudo dpkg -i mongodb-org-mongos_4.4.5_amd64.deb
```

### 使用tgz包安装

安装mongod、mongo、mongos。

```shell
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-debian10-4.4.5.tgz
tar -xzv -f mongodb-linux-x86_64-debian10-4.4.5.tgz
cd mongodb-linux-x86_64-debian10-4.4.5/bin
./install_compass
```

# 安装Shell（MongoDB Shell）

安装mongosh。（MongoDB 6.0使用mongosh替换mongo）

各模块可按需下载。如需安装其他版本，请到官网[https://www.mongodb.com/try/download/shell](https://www.mongodb.com/try/download/shell)搜索相应的链接。

```shell
wget https://downloads.mongodb.com/compass/mongodb-mongosh-shared-openssl11_1.7.1_amd64.deb
dpkg -i mongodb-mongosh-shared-openssl11_1.7.1_amd64.deb
```

## 安装数据库工具（MongoDB Database Tools）

安装mongodump、mongoexport、mongofiles、mongoimport、mongorestore、mongostat、mongotop。

如需安装其他版本，请到官网[https://www.mongodb.com/try/download/database-tools](https://www.mongodb.com/try/download/database-tools)搜索相应的链接。

```shell
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-debian10-x86_64-100.3.1.deb
sudo dpkg -i mongodb-database-tools-debian10-x86_64-100.3.1.deb
```

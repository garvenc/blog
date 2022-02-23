本文更新于2021-04-10，使用MongoDB 4.4.5，操作系统为Debian 10.5。

[TOC]

# 安装社区版（MongoDB Community Server）

各模块可按需下载。如需安装其他版本，请到官网[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)搜索相应的链接。

## 安装server

安装mongod命令：

```shell
wget https://repo.mongodb.org/apt/debian/dists/buster/mongodb-org/4.4/main/binary-amd64/mongodb-org-server_4.4.5_amd64.deb
sudo dpkg -i mongodb-org-server_4.4.5_amd64.deb
```

## 安装shell

安装mongo命令：

```shell
wget https://repo.mongodb.org/apt/debian/dists/buster/mongodb-org/4.4/main/binary-amd64/mongodb-org-shell_4.4.5_amd64.deb
sudo dpkg -i mongodb-org-shell_4.4.5_amd64.deb
```

## 安装mongos

安装mongos命令：

```shell
wget https://repo.mongodb.org/apt/debian/dists/buster/mongodb-org/4.4/main/binary-amd64/mongodb-org-mongos_4.4.5_amd64.deb
sudo dpkg -i mongodb-org-mongos_4.4.5_amd64.deb
```

## 使用tgz包安装

安装mongod、mongo、mongos：

```shell
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-debian10-4.4.5.tgz
tar -xzv -f mongodb-linux-x86_64-debian10-4.4.5.tgz
cd mongodb-linux-x86_64-debian10-4.4.5/bin
./install_compass
```

# 安装工具（MongoDB Tools）

各模块可按需下载。如需安装其他版本，请到官网[https://docs.mongodb.com/tools/](https://docs.mongodb.com/tools/)搜索相应的链接。

## 安装数据库工具（MongoDB Database Tools）

安装mongodump、mongoexport、mongofiles、mongoimport、mongorestore、mongostat、mongotop：

```shell
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-debian10-x86_64-100.3.1.deb
sudo dpkg -i mongodb-database-tools-debian10-x86_64-100.3.1.deb
```

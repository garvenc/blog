本文更新于2024-08-17。

[TOC]

官网下载地址：[https://www.mongodb.com/try](https://www.mongodb.com/try)

# 安装社区版服务器（MongoDB Community Server）

## 安装server

使用7.0.12，操作系统为Debian 12.6 (bookworm)。

包含mongod命令。

安装后的服务名为mongod。

```shell
wget https://repo.mongodb.org/apt/debian/dists/bookworm/mongodb-org/7.0/main/binary-amd64/mongodb-org-server_7.0.12_amd64.deb
sudo dpkg -i mongodb-org-server_7.0.12_amd64.deb
```

## 安装mongos

使用4.4.5，操作系统为Debian 10.5 (buster)。

包含mongos命令。

```shell
wget https://repo.mongodb.org/apt/debian/dists/buster/mongodb-org/4.4/main/binary-amd64/mongodb-org-mongos_4.4.5_amd64.deb
sudo dpkg -i mongodb-org-mongos_4.4.5_amd64.deb
```

## 使用tgz包安装

使用4.4.5，操作系统为Debian 10.5 (buster)。

包含mongod、mongo、mongos命令。

```shell
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-debian10-4.4.5.tgz
tar -xzv -f mongodb-linux-x86_64-debian10-4.4.5.tgz
cd mongodb-linux-x86_64-debian10-4.4.5/bin
./install_compass
```

# 安装Shell（MongoDB Shell）

使用7.0.12，操作系统为Debian 12.6 (bookworm)。

包含mongosh命令。（MongoDB 6.0使用mongosh替换mongo）

```shell
wget https://downloads.mongodb.com/compass/mongodb-mongosh_2.2.15_amd64.deb
sudo dpkg -i mongodb-mongosh_2.2.15_amd64.deb
```

# 安装数据库工具（MongoDB Database Tools）

使用7.0.12，操作系统为Debian 12.6 (bookworm)。

包含mongodump、mongoexport、mongofiles、mongoimport、mongorestore、mongostat、mongotop命令。

```shell
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-debian12-x86_64-100.10.0.deb
sudo dpkg -i mongodb-database-tools-debian12-x86_64-100.10.0.deb
```

本文更新于2021-11-02，使用Redis 6.0.8，操作系统为Deepin 15.11。

[TOC]

# 安装

官方文档参看：[https://redis.io/download](https://redis.io/download)。

```shell
wget http://download.redis.io/releases/redis-6.0.8.tar.gz
tar -xzf redis-6.0.8.tar.gz
cd redis-6.0.8
make
sudo make install
```

执行`make`后，可执行文件编译于redis-6.0.8/src中。官方文档无`sudo make install`这一步，其执行后会将可执行文件拷贝至/usr/local/bin目录下，用户为root，用户组为staff。可执行文件包括：

* redis-benchmark：性能测试。
* redis-check-aof：检查AOF文件。
* redis-check-rdb：检查快照文件。
* redis-cli：客户端。
* redis-sentinel：哨兵。
* redis-server：服务器。

另外，redis-6.0.8/src下还有以下可执行文件，不会被`sudo make install`拷贝至/usr/local/bin目录下：

* redis-trib.rb：Cluster集群管理。

# 配置

**说明：本节中，大写为自定义变量，根据实际情况填写。使用`|`表示使用左侧或右侧内容。**

* appendfsync always|everysec|no：AOF将写操作同步至磁盘的频率，always为每次写操作，everysec为最多每秒一次，no为不做限制而由操作系统决定。
* appendonly yes|no：是否开启AOF，不使用快照。
* auto-aof-rewrite-min-size SIZE：AOF文件超过此大小，且比上一次重写后增长超过auto-aof-rewrite-percentage指定的百分比时，进行重写。如：auto-aof-rewrite-min-size 64mb。
* auto-aof-rewrite-percentage PERCENTAGE：AOF文件比上一次重写后增长超过此百分比，且超过auto-aof-rewrite-min-size指定的大小时，进行重写。如：auto-aof-rewrite-percentage 100。
* bind IP [...]：监听的IP地址。
* client-output-buffer-limit pubsub HARDSIZE SOFTSIZE SOFTSECONDS：发布/订阅连接的输出缓冲区限制，不超过HARDSIZE且在SOFTSECONDS秒内不超过SOFTSIZE，否则关闭连接。全0为无限制。如：client-output-buffer-limit pubsub 32mb 8mb 60。
* dbfilename FILENAME：快照文件名。如：dbfilename dump.rdb。
* dir DIR：快照文件和AOF文件保存的目录。如：dir ./。
* hash-max-ziplist-entries AMOUNT：无序散列被编码为压缩列表时允许的最大元素数量。
* hash-max-ziplist-value SIZE：无序散列被编码为压缩列表时允许的最大字节数。
* list-max-ziplist-entries AMOUNT：列表被编码为压缩列表时允许的最大元素数量。
* list-max-ziplist-value SIZE：列表被编码为压缩列表时允许的最大字节数。
* lua-time-limit MILLSECONDS：Lua脚本执行的最长毫秒数，超时则使用`SCRIPT KILL`将脚本杀死。
* no-appendfsync-on-rewrite yes|no：当重写AOF文件时是否停止将写操作刷新至磁盘。
* rdbcompression yes|no：保存快照时是否进行压缩。
* save SECONDS WRITETIMES：距离上一次创建快照超过SECONDS秒且达到WRITETIMES次写操作时触发`BGSAVE`命令保存快照，可设置多个。如：save 60 1000。
* slave-read-only yes|no：从服务器是否只读不允许写入。默认为yes。
* set-max-intset-entries AMOUNT：集合被编码为整数集合时允许的最大元素数量。
* slaveof HOST PORT：作为从服务器，指定主服务器。
* stop-writes-on-bgsave-error yes|no：当执行`BGSAVE`命令保存快照时出错是否停止后续新的写操作。
* zset-max-ziplist-entries AMOUNT：有序集合被编码为压缩列表时允许的最大元素数量。
* zset-max-ziplist-value SIZE：有序集合被编码为压缩列表时允许的最大字节数。

# 命令行工具

**说明：本节中，大写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选。**

## redis-benchmark

性能测试。

```shell
redis-benchmark OPTIONS
```

OPTIONS可为：

* -c N：指定并发客户端的数量。缺省为50。
* -q：简化输出。

## redis-check-aof

检查AOF文件。

```shell
redis-check-aof [--fix] FILENAME
```

* --fix：修复AOF文件。

## redis-check-rdb

检查快照文件。

```shell
redis-check-dump FILENAME
```

## redis-cli

客户端。

```shell
redis-cli OPTIONS
```

OPTIONS可为：

* -h HOST：指定服务器主机。默认为localhost。
* -p PORT：指定服务器端口。默认为6379。
* --help：打印帮助信息。

## redis-sentinel

哨兵。用于对主从服务进行自动故障转移，实际上是运行在特殊模式下的redis-server。

## redis-server

服务器。

```shell
redis-server
```

## redis-trib.rb

Cluster集群管理。

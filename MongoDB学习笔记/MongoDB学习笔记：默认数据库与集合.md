本文更新于2022-01-15，使用MongoDB 4.4.5。

[TOC]

# admin

管理员的数据库。

管理员命令只能在此数据库执行。此数据库中的用户为超级用户，可对所有数据库进行操作，并能执行管理员命令。

## system.users

用户身份验证信息的集合。

# config

分片集群配置的数据库。

## changelog

分片集群变更记录信息的集合。

每个文档包含以下字段：

* _id：操作的唯一标识符。
* clientAddr：触发操作的客户端地址。可能为`""`。
* details：操作详情。

	如果为拆分操作，则包含以下字段：
	* before：拆分前的块。
	* left：块拆分后的左侧块。
	* right：拆分后的右侧块。

		上述各字段都包含以下字段：
		* lastmod：块的版本。`Timstamp`类型。
		* lastmodEpoch：块的版本。`ObjectId`类型。
		* max：块范围的最大值（不含）。
		* min：块范围的最小值（含）。

	如果为迁移操作（每次迁移会创建4个文档：迁移前、from分片、to分片、迁移后），则包含以下字段：
	* from：迁移的源分片。
	* max：块范围的最大值（不含）。
	* min：块范围的最小值（含）。
	* step 1 of 6：第1步的耗时。单位为毫秒。
	* step 2 of 6：第2步的耗时。单位为毫秒。
	* step 3 of 6：第3步的耗时。单位为毫秒。
	* step 4 of 6：第4步的耗时。单位为毫秒。
	* step 5 of 6：第5步的耗时。单位为毫秒。
	* step 6 of 6：第6步的耗时。单位为毫秒。
	* to：迁移的目的分片。
* ns：集合的命名空间。
* server：操作发生的服务器地址。
* time：时间。
* what：操作。拆分为split，迁移为moveChunk.to。

## chunks

分片集群块信息的集合。

每个文档包含以下字段：

* _id：块的唯一标识符。
* lastmod：块的版本。`Timstamp`类型，如：`Timestamp(MAJOR, MINOR)`。MAJOR会在块被迁移至新的分片时改变，MINOR会在块被拆分时改变。
* lastmodEpoch：块的版本。`ObjectId`类型。
* max：块范围的最大值（不含）。
* min：块范围的最小值（含）。
* ns：块所属集合的命名空间。
* shard：块所属的分片。

## collections

分片集合信息的集合。

每个文档包含以下字段：

* _id：集合的命名空。不包括非分片集合。
* distributionMode：
* dropped：
* lastmod：
* lastmodEpoch：
* key：片键。
* unique：片键是否是唯一索引。只当为true才有此字段。
* uuid：

## databases

数据库信息的集合。

每个文档包含以下字段：

* _id：数据库名。不管数据库有没有被分片。
* lastMod：
* partitioned：是否被分片。
* primary：主分片。数据库的所有新集合默认创建在主分片上。
* version：

## locks

分片集群锁信息的集合。

每个文档包含以下字段：

*_id：锁名称。balancer为均衡器。
* state：状态。非活跃为0，等待锁为1，均衡中为2。
* who：代表哪一个mongos。

## settings

设置信息的集合。

每个文档包含以下字段：

* _id：设置名称。均衡器为balancer。块大小为chunksize。
* enabled：若`_id`为balancer，则为是否启动均衡器。
* activeWindow：均衡器的时间窗口。
	* start：开始时间。
	* stop：结束时间。
* value：若`_id`为chunksize，则为块大小，单位为MB。

## shards

分片信息的集合。

每个文档包含以下字段：

* _id：分片名称。也为副本集名称。
* host：主机地址。格式为SHARD_NAME/HOST_PORT[,...]。
* state：状态。
* tags：分片标签数组。

## tags

分片标签信息的集合。

每个文档包含以下字段：

* _id：标签ID。
* max：片键的最大值（不含）。
* min：片键的最小值（含）。
* ns：对应集合的命名空间。
* tag：标签名。

# local

本地的数据库。

此数据库不会被复制。此数据库中的用户为超级用户，可对所有数据库进行操作，并能执行管理员命令。

## me

服务器自身信息集合。

## oplog.rs

oplog操作日志的集合。是一个固定集合。

## slaves

以当前节点作为同步源的节点信息的集合。包括影同步的节点信息。

## startup_log

启动日志的集合。

## system.replset

副本集配置的集合。

包含一个文档，其字段同`rs.config`的返回。副本集中所有节点的此文档都是相同的，不要自行修改此文档。

# 当前数据库

所有数据库都可以拥有以下集合。

## fs.chunks

GridFS文件块的集合。

每个文档包含以下字段：

* _id：块ID。
* data：文件数据。
* files_id：文件ID。
* n：块在文件中的相对位置。

## fs.files

GridFS文件元信息的集合。

每个文档包含以下字段：

* _id：文件ID。
* chunkSize：块字节大小。默认是256KB。
* filename：文件名。
* length：文件字节大小。
* metadata
* uploadDate：上传时间。

## system.profile

系统分析器记录信息的集合。是一个固定集合。

每个文档包含以下字段：

* client：客户端地址。
* ns：集合的命名空间。
* op：操作类型。

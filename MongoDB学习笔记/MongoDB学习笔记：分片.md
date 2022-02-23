本文更新于2022-01-08，使用MongoDB 4.4.5。

[TOC]

# 单台服务器下创建分片

1. 确保/data/db目录存在且当前系统用户有读写权限。例如（需根据实际情况设置权限）：

	```shell
	su root
	mkdir -p /data/db
	chmod 0777 /data /data/db
	```
1. 启动shell但不连接到任何mongod。

	```shell
	mongo --nodb
	```
1. 创建分片集群（笔者的分片集群有3个分片，块大小为1MB）。会先删除原有数据库目录，再创建数据库目录和文件，启动mongos进程、配置副本集若干mongod进程（默认3个进程）、若干分片副本集的若干mongod进程（笔者指定为3个分片副本集，每个副本集默认1个进程），同时会不断输出所有节点的日志。

	```js
	var sharding = new ShardingTest({shards: 3, chunksize: 1});
	```

1. 启动shell连接到mongos（笔者使是localhost::20006），可执行查询操作。

	```shell
	mongo mongodb://localhost:20006
	```
	shell提示是“mongos”。
1. 对数据库（笔者使用test数据库）启用分片。

	```js
	sh.enableSharding("test");
	```
1. 为片键（笔者使用users集合的username字段）建立索引。

	```js
	db.users.createIndex({username: 1});
	```
1. 根据片键（笔者使用username字段）对集合（笔者使用的users集合为数据库test的集合）分片。之后的查询就可以使用分片。

	```js
	sh.shardCollection("test.users", {username: 1});
	```
1. 在第一个shell中停止分片集群。会停止mongos进程、配置副本集所有mongod进程、所有分片副本集所有mongod进程。

	```js
	sharding.stop();
	```

# 多台服务器下创建分片

此种方法也可在单台服务器下创建分片（笔者就以此方式使用）。

1. **启动配置服务器**
	1. 确保数据目录（笔者的副本集有3个节点，使用/data/db/config0、/data/db/config1、/data/db/config2）存在且当前系统用户有读写权限。例如（需根据实际情况设置权限）：
	
		```shell
		su root
		mkdir -p /data/db/config0
		mkdir -p /data/db/config1
		mkdir -p /data/db/config2
		chmod 0777 /data /data/db /data/db/config0 /data/db/config1 /data/db/config2
		```
	1. 使用不同的shell启动副本集节点（笔者的副本集名字为config），必需指定绑定的IP（笔者使用0.0.0.0），如果在单台服务器下需指定不同的数据目录（如笔者在上一步所述）和端口（笔者使用40000、40001、40002）。会创建数据库文件。

		```shell
		mongod --configsvr --replSet config --dbpath /data/db/config0 --port 40000 --bind_ip 0.0.0.0
		mongod --configsvr --replSet config --dbpath /data/db/config1 --port 40001 --bind_ip 0.0.0.0
		mongod --configsvr --replSet config --dbpath /data/db/config2 --port 40002 --bind_ip 0.0.0.0
		```
	1. 启动shell连接到任意一个节点（笔者使用端口40000的节点）。
	
		```shell
		mongo mongodb://localhost:40000
		```

	1. 初始化副本集，会从备份节点中选举出主节点（笔者的副本集名字为config，各节点为localhost:40000、localhost:40001、localhost:40002）。如果副本集曾经初始化，则不用执行此步骤。
	
		```js
		rs.initiate({
			_id: "config",
			members: [
				{
					"_id": 0,
					"host": "localhost:40000"
				},
				{
					"_id": 1,
					"host": "localhost:40001"
				},
				{
					"_id": 2,
					"host": "localhost:40002"
				}
			]
		});
		```
1. **启动mongos**

	需指定配置服务器（笔者使用的副本集名字为config，各节点为localhost:40000、localhost:40001、localhost:40002。另外，笔者的mongos使用端口60000）。
	```shell
	mongos --configdb config/localhost:40000,localhost:40001,localhost:40002 --port 60000
	```
1. **启动若干个副本集**
	1. 确保数据目录（笔者使用3个副本集，每个副本集有1个节点，使用/data/db/shard0-0、/data/db/shard1-0、/data/db/shard2-0）存在且当前系统用户有读写权限。例如（需根据实际情况设置权限）：
	
		```shell
		su root
		mkdir -p /data/db/shard0-0
		mkdir -p /data/db/shard1-0
		mkdir -p /data/db/shard2-0
		chmod 0777 /data /data/db /data/db/shard0-0 /data/db/shard1-0 /data/db/shard2-0
		```
	1. 使用不同的shell启动副本集节点（笔者的副本集名字为shard0、shard1、shard2），必需指定绑定的IP（笔者使用0.0.0.0），如果在单台服务器下需指定不同的数据目录（如笔者在上一步所述）和端口（笔者使用50000、50001、50002）。会创建数据库文件。

		```shell
		mongod --shardsvr --replSet shard0 --dbpath /data/db/shard0-0 --port 50000 --bind_ip 0.0.0.0
		mongod --shardsvr --replSet shard1 --dbpath /data/db/shard1-0 --port 50001 --bind_ip 0.0.0.0
		mongod --shardsvr --replSet shard2 --dbpath /data/db/shard2-0 --port 50002 --bind_ip 0.0.0.0
		```
	1. 初始化所有副本集（笔者只列出名字为shard0的副本集的步骤。名字为shard1和shard2的副本集，同理）。如果副本集曾经初始化，则不用执行此步骤。
		1. *启动shell连接到副本集节点（笔者示例使用名字为shard0的副本集，其节点的端口为50000。端口为50001和50002的节点，同理）。*
		
			```shell
			mongo mongodb://localhost:50000
			```
	
		1. *初始化副本集，会从备份节点中选举出主节点（笔者示例使用名字为shard0的副本集，其节点为localhost:50000。名字为shard1的副本集的节点为localhost:50001，名字为shard2的副本集的节点为localhost:50002，同理）。*
		
			```js
			rs.initiate({
				_id: "shard0",
				members: [
					{
						"_id": 0,
						"host": "localhost:50000"
					}
				]
			});
			```
1. **将所有副本集转换为分片**
	1. 启动shell连接到mongos（笔者为localhost:60000）。

		```shell
		mongo mongodb://localhost:60000
		```
	1. 将所有副本集（即笔者使用的shard0/localhost:50000、shard1/localhost:50001、shard2/localhost:50002）添加为分片。

		```shell
		sh.addShard("shard0/localhost:50000");
		sh.addShard("shard1/localhost:50001");
		sh.addShard("shard2/localhost:50002");
		```
1. **数据分片**
	1. 启动shell连接到mongos（笔者为localhost:60000）。

		```shell
		mongo mongodb://localhost:60000
		```
	1. 对数据库（笔者使用test数据库）启用分片。
	
		```js
		sh.enableSharding("test");
		```
	1. 为片键（笔者使用users集合的username字段）建立索引。
	
		```js
		db.users.createIndex({username: 1});
		```
	1. 根据片键（笔者使用username字段）对集合（笔者使用的users集合为数据库test的集合）分片。之后的查询就可以使用分片。
	
		```js
		sh.shardCollection("test.users", {username: 1});
		```

## 重新启动并连接到分片

对于使用上述方式创建的分片，如需重新启动并连接到分片，只需启动不同的shell执行其中若干个步骤，如下：

```shell
mongod --configsvr --replSet config --dbpath /data/db/config0 --port 40000 --bind_ip 0.0.0.0
mongod --configsvr --replSet config --dbpath /data/db/config1 --port 40001 --bind_ip 0.0.0.0
mongod --configsvr --replSet config --dbpath /data/db/config2 --port 40002 --bind_ip 0.0.0.0
mongos --configdb config/localhost:40000,localhost:40001,localhost:40002 --port 60000
mongod --shardsvr --replSet shard0 --dbpath /data/db/shard0-0 --port 50000 --bind_ip 0.0.0.0
mongod --shardsvr --replSet shard1 --dbpath /data/db/shard1-0 --port 50001 --bind_ip 0.0.0.0
mongod --shardsvr --replSet shard2 --dbpath /data/db/shard2-0 --port 50002 --bind_ip 0.0.0.0
mongo mongodb://localhost:60000
```

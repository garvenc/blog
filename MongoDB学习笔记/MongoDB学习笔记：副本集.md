本文更新于2022-01-08，使用MongoDB 4.4.5。

[TOC]

# 单台服务器下创建副本集

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
1. 创建副本集（笔者的副本集有3个节点，名字为repl）。

	```js
	var replset = new ReplSetTest({nodes: 3, name: "repl"});
	```
1. 启动副本集。会先删除原有数据库目录，再创建数据库目录和文件，启动所有副本集进程，同时会不断输出所有节点的日志。

	```js
	replset.startSet();
	```
	返回值值的各个元素包含节点的地址（笔者是localhost::20000、localhost::20001、localhost::20002）。
1. 初始化副本集。会从备份节点中选举出主节点。

	```js
	replset.initiate();
	```
1. 启动shell连接到副本集的节点（笔者使用localhost::20000），可执行查询操作。

	```shell
	mongo mongodb://localhost:20000
	```
	shell提示是“repl:PRIMARY”或“repl:SECONDARY”，前面是副本集名字，后面表示主节点或备份节点。
1. 在第一个shell中停止副本集。会停止所有副本集进程。

	```js
	replset.stopSet();
	```

# 多台服务器下创建副本集

此种方法也可在单台服务器下创建副本集（笔者就以此方式使用）。

1. 确保数据目录（笔者的副本集有3个节点，使用/data/db/rs0、/data/db/rs1、/data/db/rs2）存在且当前系统用户有读写权限。例如（需根据实际情况设置权限）：

	```shell
	su root
	mkdir -p /data/db/rs0
	mkdir -p /data/db/rs1
	mkdir -p /data/db/rs2
	chmod 0777 /data /data/db /data/db/rs0 /data/db/rs1 /data/db/rs2
	```
1. 使用不同的shell启动副本集节点（笔者的副本集名字为replica），如果在单台服务器下需指定不同的数据目录（如笔者在上一步所述）和端口（笔者使用30000、30001、30002）。会创建数据库文件。

	```shell
	mongod --replSet replica --dbpath /data/db/rs0 --port 30000
	mongod --replSet replica --dbpath /data/db/rs1 --port 30001
	mongod --replSet replica --dbpath /data/db/rs2 --port 30002
	```
1. 启动shell连接到任意一个节点（笔者使用端口30000的节点）。

	```shell
	mongo mongodb://localhost:30000
	```
1. 初始化副本集，会从备份节点中选举出主节点（笔者的副本集名字为replica，各节点为localhost:30000、localhost:30001、localhost:30002）。如果副本集曾经初始化，则不用执行此步骤。

	```js
	rs.initiate({
		_id: "replica",
		members: [
			{
				"_id": 0,
				"host": "localhost:30000"
			},
			{
				"_id": 1,
				"host": "localhost:30001"
			},
			{
				"_id": 2,
				"host": "localhost:30002"
			}
		]
	});
	```
1. 启动shell连接到副本集（笔者的副本集名字为replica，各节点为localhost:30000、localhost:30001、localhost:30002），可执行查询操作。

	```shell
	mongo mongodb://localhost:30000,localhost:30001,localhost:30002/?replicaSet=replica
	```
1. 也可以启动shell连接到任意节点（笔者使用localhost:30000）。

	```shell
	mongo mongodb://localhost:30000
	```

## 重新启动并连接到副本集

对于使用上述方式创建的副本集，如需重新启动并连接到副本集，只需启动不同的shell执行其中若干个步骤，如下：

```shell
mongod --replSet replica --dbpath /data/db/rs0 --port 30000
mongod --replSet replica --dbpath /data/db/rs1 --port 30001
mongod --replSet replica --dbpath /data/db/rs2 --port 30002
mongo mongodb://localhost:30000,localhost:30001,localhost:30002/?replicaSet=replica
```

# 维护副本集节点

1. 将待维护节点的mongod服务停止。
1. 以单机模式启动节点（笔者使用上例中的localhost:30000，并将端口修改为10000）。

	```shell
	mongod --dbpath /data/db/rs0 --port 10000
	```
1. 启动shell连接到此节点（如上所述，笔者使用localhost:10000），进行维护。

	```shell
	mongo mongodb://localhost:10000
	```
1. 维护完成后，使用原来的副本集参数启动此节点（如上所述，笔者使用原来的副本集名字replica和原来的端口30000）。

	```shell
	mongod --replSet replica --dbpath /data/db/rs0 --port 30000
	```

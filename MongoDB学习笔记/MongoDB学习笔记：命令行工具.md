本文更新于2023-05-08，使用MongoDB 4.4.5。

[TOC]

# bsondump

BSON查看工具。

```shell
bsondump [OPTION] BSONFILENAME
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --version：查看版本。

# mongo

同mongosh。MongoDB 6.0使用mongosh替换mongo。

# mongod

MongoDB服务器。

```shell
mongod [OPTION]
mongod --replSet REPLSET_NAME
mongod --repair [--repairpath DST_DIR] [--dbpath DIR]
```

OPTION可为：

* 通用选项：
	* --auth：启用身份验证。
	* --bind_ip：监听的IP。
	* --config|-f FILENAME：配置文件。命令行指定的选项可覆盖配置文件中的值。
	* --fork：创建后台运行的子进程。需同时使用--logpath。
	* --help|-h：查看帮助。
	* --logappend：以追加方式写入日志文件。需同时使用--logpath。
	* --logpath FILENAME：日志输出文件。如不指定则输出至标准输出。
	* --noscripting：禁止执行JavaScript脚本。注意，某些shell辅助函数依赖于JavaScript脚本。
	* --nounixsocket：不监听UNIX套接字。
	* --port PORT：监听的端口。默认为27017。
	* --profile LEVEL：设置系统分析器等级。0为关闭系统分析器，1为只记录慢查询，2为记录所有操作。
	* --quiet：不输出日志。
	* --slowms MICROSECONDS：设置系统分析器的慢查询阈值。单位为毫秒。
	* --verbose|-v：日志级别。不使用该选项则为最低值0；-vvvvv为最高值5（记录几乎所有操作，包括每一个请求处理的内容）。
	* --version：查看版本。
* 副本集选项：
	* --replSet REPLSET_NAME：指定副本集名称，作为副本集节点启动。不使用此选项，则以单机模式启动。
* 分片选项：
	* --configsvr：作为配置服务器启动。默认端口为27019，默认数据目录为/data/configdb。
	* --shardsvr：作为分片启动。默认端口为27018，
* 存储选项：
	* --dbpath DIR：数据目录。默认为/data/db（Windows为当前驱动器）。
	* --directoryperdb：每个数据库存放在单独的目录中。
	* --journalCommitInterval MICROSECONDS：日记系统进行提交的时间间隔。单位为毫秒，最小为2，最大为500。
	* --noprealloc：禁止预分配数据文件。
	* --repair：修复数据。不会监听端口，但会输出日志。
	* --repairpath DST_DIR：指定修复时复制文件的目标目录。
	* --syncdelay SECONDS：将脏页刷新到磁盘的时间间隔。单位为秒。

# mongodump

数据备份工具。备份过程中会继续执行写入操作。

```shell
mongodump [OPTION]
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --version：查看版本。
* 连接选项：
	* --host HOST：主机名。
	* --port PORT：端口。
* 身份验证选项：
	* --authenticationDatabase AUTH_DBNAME：身份校验使用的数据库。
	* --password|-p [PASSWORD]：指定密码。如不指定PASSWORD，则于交互界面提示输入密码。
	* --username|-u USERNAME：指定用户名。
* 命名空间选项：
	* --db|-d DBNAME：只备份指定的数据库。
* 输出选项：
	* --excludeCollection COLLECTIONNAME：不进行备份的集合。可指定多次。
	* --gzip：将.bson数据文件和.metadata.json元数据文件使用gzip压缩。
	* --oplog：同时转储备份过程中的操作日志oplog，以获得备份时间点的快照。如备份副本集，必需使用此选项。
	* --out|-o DUMPDIR：输出目录。默认为dump。输出目录中包含若干个名字为数据库名的目录，每个目录中包含若干个集合名开头的.bson数据文件和.metadata.json元数据文件。

如连接至副本集，则会自动选择一个备份节点执行备份操作。

如连接至mongos，则会备份所有分片。

# mongoexport

数据导出工具。

# mongofiles

GridFS工具。

```shell
mongofiles [OPTION] COMMAND [ARG]
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --version：查看版本。
* 连接选项：
	* --host HOST：主机名。
	* --port PORT：端口。
* 身份验证选项：
	* --authenticationDatabase AUTH_DBNAME：身份校验使用的数据库。
	* --password|-p [PASSWORD]：指定密码。如不指定PASSWORD，则于交互界面提示输入密码。
	* --username|-u USERNAME：指定用户名。
* 存储选项：
	* --db|-d DBNAME：指定数据库。
	* --local|-l LOCALFILENAME：本地文件名，只用于`put`和`get`子命令。

COMMAND [ARG]可为（FILENAME可为文件路径）：

* delete FILENAME：从GridFS中删除文件。
* get FILENAME：将GridFS中的文件下载到文件系统中。
* list [PREFIX]：列出GridFS中的文件。以前缀匹配查找。如不指定前缀，则列出所有文件。
* put FILENAME：将文件系统中的文件上传到GridFS。
* search SUBSTRING：在GridFS中搜索文件。搜索子串。

# mongoimport

数据导入工具。

# mongorestore

数据恢复工具。

```shell
mongorestore [OPTION] [DUMPDIR|BSONFILENAME]
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --version：查看版本。
* 连接选项：
	* --host HOST：主机名。
	* --port PORT：端口。
* 身份验证选项：
	* --authenticationDatabase AUTH_DBNAME：身份校验使用的数据库。
	* --password|-p [PASSWORD]：指定密码。如不指定PASSWORD，则于交互界面提示输入密码。
	* --username|-u USERNAME：指定用户名。
* 命名空间选项：
	* --collection|-c COLLECTIONNAME：只恢复指定的集合。此选项已废弃。
	* --db|-d DBNAME：只恢复指定的数据库。此选项已废弃。
	* --nsFrom DBNAME.COLLECTIONNAME：重命名时的源命名空间。必需指定对应的--nsTo。DBNAME和COLLECTIONNAME均可使用*匹配所有。
	* --nsInclude DBNAME.COLLECTIONNAME：只恢复指定的命名空间。DBNAME和COLLECTIONNAME均可使用*匹配所有。
	* --nsTo DBNAME.COLLECTIONNAME。重命名时的目标命名空间。必需指定对应的--nsFrom。DBNAME和COLLECTIONNAME均可使用*匹配所有。
* 输入选项：
	* --dir DUMPDIR：需恢复的备份数据目录。默认为dump。根据不同情况，此目录需包含若干个名字为数据库名的目录，或需包含若干个集合名开头的.bson数据文件和.metadata.json元数据文件。
	* --gzip：从使用gzip压缩方式备份的数据中恢复。
	* --oplogReplay：重放操作日志oplog，以获得备份时间点的快照。
* 恢复选项：
	* --drop：进行数据替换，在恢复集合前先删除之。

如连接至mongos，则会恢复所有分片。

# mongos

分片路由。

```shell
mongos [OPTION]
mongos --configdb CONFIG
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --maxConns N：指定最大连接数量。
	* --port PORT：监听的端口。默认为27017。
	* --version：查看版本。
* 分片选项：
	* --configdb CONFIG：指定配置服务器，格式为REPLSET_NAME/HSOT_PORT<,...,HSOT_PORT>。

# mongosh

JavaScript shell。

```shell
mongo [OPTION] [DBNAME|HOST[:PORT][/DBNAME]|MONGODB_URI] [JS_FILENAME[ ...]]
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --host HOST：主机名。
	* --nodb：启动shell时不连接到任何mongod。
	* --norc：启动shell时不加载用户主目录下的.mongorc.js。默认会加载。
	* --port PORT：端口。
	* --quiet：不打印连接成功后的提示信息。
	* --version：查看版本。
* 身份验证选项：
	* --authenticationDatabase AUTH_DBNAME：身份校验使用的数据库。
	* --password|-p [PASSWORD]：指定密码。如不指定PASSWORD，则于交互界面提示输入密码。
	* --username|-u USERNAME：指定用户名。

如不指定DBNAME或HOST[:PORT][/DBNAME或MONGODB_URI，默认连接到localhost:27017/test。

MONGODB_URI为连接字符串。官方文档为：[https://www.mongodb.com/docs/v6.0/reference/connection-string/](https://www.mongodb.com/docs/v6.0/reference/connection-string/)。格式为：`mongodb://[USERNAME[:PASSWORD]@]HOST[:PORT][,...,HOST1[:PORT1]][/[DBNAME]][?NAME=VALUE[&...&NAME1=VALUE1]]`。NAME=VALUE可为：

* authSource=AUTH_DBNAME：身份校验使用的数据库。
* replicaSet=REPLSET_NAME：副本集名字。如使用此参数，可指定任意个（并不需要所有）HOST[:PORT]，会自动选择主节点连接。

如指定JS_FILENAME，则依次执行脚本文件然后退出（相对路径根据shell运行目录）。否则，以交互方式使用shell。

# mongostat

状态统计工具。每隔一段时间输出一次，统计自上一次输出以来的信息。可连接至副本集和分片集群。

```shell
mongostat [OPTION] [INTERVAL_SECONDS]
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --version：查看版本。
* 连接选项：
	* --host HOST：主机名。
	* --port PORT：端口。
* 身份验证选项：
	* --authenticationDatabase AUTH_DBNAME：身份校验使用的数据库。
	* --password|-p [PASSWORD]：指定密码。如不指定PASSWORD，则于交互界面提示输入密码。
	* --username|-u USERNAME：指定用户名。
* 状态选项：
	* --discover：寻找副本集或分片集群的所有节点，针对每个节点也输出一行信息。

输出包含以下字段：

* host：主机地址。
* insert：插入的次数。
* query：查询的次数。
* update：更新的次数。
* delete：删除的次数。
* getmore：查询游标时获取更多数据的次数。
* command：执行命令的次数。
* dirty：
* used：
* flushes：数据刷新至磁盘的次数。
* mapped：映射的内存大小。通常约等于数据目录的大小。
* vsize：正在使用的虚拟内存大小。通常为数据目录的2倍（一次用于映射文件，一次用于日记系统）。
* res：正在使用的内存大小。通常应尽量接近机器的内存大小。
* faults：
* qrw：读写操作的队列大小。即被阻塞的读写操作的数量。读和写以“|”分隔。
* arw：读写的活跃客户端数量。即正在进行读写的客户端的数量。读和写以“|”分隔。
* net_in：通过网络传输进来的字节数。不必和操作系统的统计相等。
* net_out：通过网络传输出去的字节数。不必和操作系统的统计相等。
* conn：打开的连接数。包括输入和输出连接。
* set：副本集名称。
* repl：副本集的节点状态。
* time：统计的时间点。

# mongotop

集合实时监控工具。每隔一段时间输出一次。

```shell
mongotop [OPTION] [INTERVAL_SECONDS]
```

OPTION可为：

* 通用选项：
	* --help|-h：查看帮助。
	* --version：查看版本。
* 连接选项：
	* --host HOST：主机名。
	* --port PORT：端口。
* 身份验证选项：
	* --authenticationDatabase AUTH_DBNAME：身份校验使用的数据库。
	* --password|-p [PASSWORD]：指定密码。如不指定PASSWORD，则于交互界面提示输入密码。
	* --username|-u USERNAME：指定用户名。
* 输出选项：
	* --locks：查看每个数据库的锁状态。

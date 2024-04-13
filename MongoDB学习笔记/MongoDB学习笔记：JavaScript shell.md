本文更新于2024-03-02，使用MongoDB 6.0.4。

[TOC]

可于官网搜索相关文档：[https://www.mongodb.com/search](https://www.mongodb.com/search)

**说明：下文中，大写为自定义变量（个别大写的类型除外），根据实际情况填写。使用`<>`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

支持所有JavaScript内置的类型。

# 辅助扩展

辅助扩展不可在脚本文件中使用。

## edit

编辑变量。使用`EDITOR`变量或环境变量指定的编辑器编辑。

```
edit VAR_NAME
```

## help

查看帮助。

```
help
```

## it

对查询结果进行下一次遍历。对`DBCollection.prototype.find`使用。

```
it
```

## exit

退出。

```
exit
```

## show collections

查看当前数据库的所有集合。

```
show collections
```

## show databases

查看所有数据库。建议使用`show dbs`。

```
show databases
```

## show dbs

查看所有数据库。

```
show dbs
```

## show tables

查看当前数据库的所有集合。建议使用`show collections`。

```
show tables
```

## show users

查看当前数据库的所有用户。

```
show users
```

## use

切换数据库。

```
use DB_NAME
```

# BulkWriteResult

多个写入结果类型。包括以下字段：

* nMatched：匹配查询条件的文档数量。
* nModified：修改的文档数量。
* nUpserted：执行upsert的文档数量。
* nInserted：插入的文档数量。
* nRemoved：删除的文档数量。
* upserted
* writeErrors
* writeConcernErrors

# db

当前数据库。为`DB`类型。

# DB

数据库类型。

## DATABASE.COLLECTION_NAME

数据库指定的集合，等同于`DATABASE[COLLECTION_NAME]`。为`DBCollection`类型。

## DB.prototype.adminCommand

以管理员身份执行命令。可执行管理员命令，会在admin数据库上执行，会在$cmd集合上执行。大多数命令都有方法对其进行封装。

```js
var OBJ = DATABASE.adminCommand(COMMAND_DOC);
```

COMMAND_DOC第一个字段为命令名。

返回包括以下字段：

* errmsg：当ok为0时，描述失败的原因。
* ok：1为成功，0为失败。

### flushRouterConfig

刷新分片集群的路由配置元信息。

```js
var OBJ = DATABASE.adminCommand({flushRouterConfig: 1});
```

### getParameter

获取参数。

```js
var OBJ = DATABASE.adminCommand({getParameter: 1, PARAM: 1});
```

PARAM见`setParameter`命令。

### logRotate

日志文件轮替。

```js
var OBJ = DATABASE.adminCommand({logRotate: 1});
```

### movePrimary

将主分片上的数据库迁移至其他分片。

```js
var OBJ = DATABASE.adminCommand({movePrimary: DB_NAME, to: SHARD_NAME});
```

### removeShard

从分片集群中删除分片。

```js
var OBJ = DATABASE.adminCommand({removeShard: SHARD_NAME});
```

返回包括以下字段：

* dbsToMove：当迁移主分片时需要迁移的数据库名数组。
* msg：执行信息。
* note：下一步操作的提示。
* state：执行状态。完成为completed。

删除分片可能需要很长时间，可执行多次来查看执行状态。

### replSetMaintenance

强制副本集备份节点进入/退出维护模式，不能在主节点上执行。

```js
var OBJ = DATABASE.adminCommand({replSetMaintenance: BOOL});
```

### setParameter

设置参数。

```js
var OBJ = DATABASE.adminCommand({setParameter: 1, PARAM: VALUE});
```

PARAM可为：

* logLevel：日志等级。

## DB.prototype.auth

身份验证。

```shell
var RESULT = db.auth(USERNAME, PASSWORD);
```

验证通过返回1，不通过返回0。

## DB.prototype.changeUserPassword

修改用户密码。

```shell
var OBJ = DATABASE.changeUserPassword(USERNAME, PASSWORD);
```

## DB.prototype.createCollection

创建集合。

```js
var OBJ = DATABASE.createCollection(COLLECTION_NAME <, COLLECTION_OPTION_DOC>);
```

COLLECTION_OPTION_DOC可使用以下字段：

* autoIndexId：是否在`_id`上创建索引。缺省为true。
* capped：是否创建为固定集合。缺省为false，如为true则必需同时指定`size`。
* max：固定集合的最大文档数量。
* size：固定集合的字节大小。

## DB.prototype.createUser

在当前数据库添加用户。

```shell
var OBJ = db.createUser({
	user: USERNAME,
	pwd: PASSWORD,
	roles: [ROLE|{role: ROLE, db: DBNAME} <, ...>]
});
```

参数可使用以下字段：

* pwd：密码。
* roles：身份角色数组。可使用两种形式，其中一种形式可使用以下字段：
	* db：将身份角色限制在此数据库上。
	* role：身份角色。
* user：用户名。

ROLE可为：

* 数据库用户角色：
	* read：读数据库的文档。
	* readWrite：读写数据库的文档。
* 数据库管理角色：
	* dbAdmin：数据库管理者。如创建索引、查看统计，不包括数据库用户管理。
	* dbOwner：数据库所有者。拥有数据库的所有权限。
	* userAdmin：数据库用户管理。
* 集群管理角色，用于副本集和分片集群：
	* clusterAdmin：集群管理者。拥有集群的所有管理权限。只在admin数据库可用。
	* clusterManager：集群管理员。拥有集群的管理和监控权限。只在admin数据库可用。
	* clusterMonitor：集群监控员。拥有集群的监控权限。只在admin数据库可用。
	* hostManager：主机管理员。管理主机节点。只在admin数据库可用。
* 备份恢复角色：
	* backup：数据库备份。
	* restore：数据库恢复。
* 于所有数据库上的角色：
	* dbAdminAnyDatabase：所有数据库管理者。如创建索引、查看统计，不包括数据库用户管理。只在admin数据库可用。
	* readAnyDatabase：读所有数据库的文档。只在admin数据库可用。
	* readWriteAnyDatabase：读写所有数据库的文档。只在admin数据库可用。
	* userAdminAnyDatabase：所有数据库用户管理。只在admin数据库可用。
* 超级用户角色：
	* root：超级权限。只在admin数据库可用。
* 内部角色：
	* __system：表示副本集或分片集群节点的内部角色。

无返回，会打印结果信息。

## DB.prototype.currentOp

获取当前的所有操作。

```js
var OBJ = DATABASE.currentOp(<QUERY_DOC>);
```

QUERY_DOC见`DBCollection.prototype.find`，为返回的inprog数组元素指定匹配条件。

返回包括以下字段：

* inprog：正在执行的操作的数组。
	* active：操作是否正在运行。如为false，则此操作已交出或正等待其他操作交出锁。
	* client：操作的客户端地址。
	* desc：操作的客户端描述。可能为“connN”的形式，N为连接ID。
	* locks：操作使用的锁。全局锁为`^`。
	* lockStatus：操作使用的锁的状态。
		* timeAcquiringMicros：操作获得锁必需等待的时间。单位为毫秒。
	* ns：操作执行的命名空间。
	* numYields：操作交出锁使其他操作得以运行的次数。
	* op：操作的类型。可为：command、getmore、insert、killcursors、none、query、remove、update。
	* opid：操作的ID。
	* secs_running：操作执行的时间。单位为秒。
	* waitingForLock：操作是否正在等待锁。

## DB.prototype.dropUser

在当前数据库删除用户。

```js
var BOOL = DATABASE.dropUser(USER);
```

## DB.prototype.fsyncLock

锁定所有数据库。禁止写入操作，都会被加入队列中等待，并进行同步，将所有脏页刷新至磁盘。

```js
var OBJ = DATABASE.fsyncLock();
```

## DB.prototype.fsyncUnlock

解锁所有数据库。

```js
var OBJ = DATABASE.fsyncUnlock();
```

## DB.prototype.getCollection

获取集合。类似`DATABASE.COLLECTION_NAME`。

```js
var DBCOLLECTION = DATABASE.getCollection(COLLECTION_NAME);
```

返回`DBCollection`类型。

## DB.prototype.getCollectionNames

获取所有集合名。类似`show collections`。

```js
var NAME_ARR = DATABASE.getCollectionNames();
```

## DB.prototype.getLastError

获取最后一次操作的错误等信息。

```js
var OBJ = DATABASE.getLastError();
```

## DB.prototype.getMongo

获取MongoDB连接。

```js
var MONGO = DATABASE.getMongo();
```

返回`Mongo`类型。

## DB.prototype.getProfilingLevel

获取数据库的系统分析器等级。

```js
var LEVEL = DATABASE.getProfilingLevel();
```

## DB.prototype.getSisterDB

获取同级数据库。类似`use DB_NAME`。

```
var DATABASE2 = DATABASE.getSisterDB(DB_NAME);
```

返回`DB`类型。

## DB.prototype.help

查看数据库的帮助。

```js
DATABASE.help();
```

## DB.prototype.isMaster

是否是副本集的主节点。

```js
var OBJ = DATABASE.isMaster();
```

返回的字段包括：

* hosts：所有节点的地址。不可见隐藏节点。
* ismaster：是否是主节点。单机模式下为true。
* me：当前节点的地址。
* primary：主节点的地址。
* secondary：是否是备份节点。
* setName：副本集名称。

## DB.prototype.killOp

终止操作。只有交出了锁的操作（如更新、查找、删除）才能被终止，正在占用锁或正在等待锁的操作无法被终止。

```js
var OBJ = DATABASE.killOp(OPID);
```

当操作从`DB.prototype.currentOp`的返回数组中消失后才真正被终止。

## DB.prototype.listCommands

列出所有命令。

```js
DATABASE.listCommands();
```

## DB.prototype.printReplicationInfo

查看副本集的oplog信息。可在主节点和备份节点上执行。

```js
DATABASE.printReplicationInfo();
```

无返回，会打印几行信息：

* configured oplog size：配置的oplog大小。
* log length start to end：oplog中记录的事件的开始时间到结束时间的时长。
* oplog first event time：oplog中记录的事件的开始时间。
* oplog last event time：oplog中记录的事件的结束时间。
* now：当前时间。

## DB.prototype.printSlaveReplicationInfo

查看副本集备份节点的oplog信息。可在主节点和备份节点上执行。

```js
DATABASE.printSlaveReplicationInfo();
```

无返回，会打印几个小节的备份节点信息，每个小节包括几行信息：

* source：备份节点的主机和端口。
	* syncedTo：备份节点数据同步至的时间。
	* 落后于主节点的时长。

## DB.prototype.runCommand

执行命令。不可执行管理员命令，会在$cmd集合上执行。大多数命令都有方法对其进行封装。

```js
var OBJ = DATABASE.runCommand(COMMAND_DOC);
```

COMMAND_DOC第一个字段为命令名。

返回包括以下字段：

* errmsg：当ok为0时，描述失败的原因。
* ok：1为成功，0为失败。

### compact

压缩集合。

```shell
var OBJ = DATABASE.runCommand({compact: COLLECTION_NAME});
```

返回包括以下字段：

* bytesFreed：释放的字节数。

会消耗大量资源。

### connPoolStats

查看分片集群的连接池状态。

```js
var OBJ = DATABASE.runCommand({connPoolStats: 1});
```

只在分片集群的mongos和mongod上运行才有意义。

### dataSize

查看数据总大小。

```js
var OBJ = DATABASE.runCommand({
	dataSize: COLLECTION_NAMESPACE
	<, keyPattern: {KEY: 1|-1 <, ...>}
	 <, min: {KEY: VALUE <, ...>}>
	 <, max: {KEY: VALUE <, ...>}>
	>
});
```

COLLECTION_NAMESPACE即"DB_NAME.COLLECTION_NAME"。

返回包括以下字段：

* numObjects：文档数。
* millis：执行的毫秒数。
* size：总大小，单位为字节。

需要扫描所有文档。

### dbHash

获取数据库的散列值。

```js
var OBJ = DATABASE.runCommand({dbHash: 1});
```

## DB.prototype.serverCmdLineOpts

查看启动服务器的命令行参数。

```js
var OBJ = DATABASE.serverCmdLineOpts();
```

返回包括以下字段：

* argv：启动服务器的命令行参数数组。
* parsed：解析后的命令行参数。
	* net：网络参数。
		* port：端口。
	* replication：副本集参数。
		* replSet：副本集名字。
	* storage：存储参数。
		* dbPath：数据目录。

## DB.prototype.serverStatus

获取服务器状态。

```js
var OBJ = DATABASE.serverStatus();
```

## DB.prototype.setProfilingLevel

设置数据库的系统分析器等级。

```js
var OBJ = DATABASE.setProfilingLevel(LEVEL <, SLOW_MS>);
```

LEVEL可为：0为关闭系统分析器，1为只记录慢查询，2为记录所有操作。

SLOW_MS为慢查询阈值，单位为毫秒。不要设置得过小，即使系统分析器关闭，慢查询也可能被记录下来。

返回包括以下字段：

* sampleRate
* slowms：此前的慢查询阈值。
* was：此前的等级。

此方法的设置会在服务器重启后被清除。

## DB.prototype.shutdownServer

停止服务器。必需运行在admin数据库上。

```js
var RETURN = DATABASE.shutdownServer(<OPTION>);
```

OPTION可使用以下字段：

* force：是否强制停止。
* timeoutSecs：停止主节点时等待备份节点追赶的超时秒数。

只当停止失败时，才有返回值，其为一个字符串。

## DB.prototype.stats

查看数据库的状态。在一个繁忙的系统上列出数据库的状态会非常慢，而且会阻碍其他操作。

```js
var OBJ = DATABASE.stats(<SCALE_FACTOR>);
```

SCALE_FACTOR为比例因子（scale factor），返回文档的字段数值会先除以SCALE_FACTOR。默认为1，即以1字节为单位。

* avgObjSize：
* collections：集合数量。
* dataSize：数据占用的空间大小，包括文档间的间隔，不包括空闲列表（free list）的空间。该值与storageSize的差为被删除文档的大小。
* db：数据库名称。
* fileSize：为数据库文件分配的总空间大小，包括预分配文件的大小。该值应该是最大的，等于数据目录中所有以数据库名开头的文件大小合计。
* indexes：
* indexSize：
* nsSizeMB：.ns文件的大小。
* objects：所有集合的文档数量合计。
* storageSize：数据库正在使用的总空间大小。该值应该是次大的，与fileSize的差即为预分配文件及前一个文件未使用部分大小之和。

## DB.prototype.version

获取服务器版本。

```js
var VERSION = DATABASE.version();
```

# DBCollection

集合类型。

## DBCollection.prototype.aggregate

聚合。

```js
var DOCS = DBCOLLECTION.aggregate(PIPELINE_DOC <, ...>);
```

PIPELINE_DOC可使用以下管道操作符：

* $count：计数。`$count: "INPUT_KEY"`。输出只包含一个文档，此文档只有INPUT_KEY字段。
* $group：分组。`$group: {_id: "$INPUT_KEY"|{KEY: "$INPUT_KEY" <, ...>} <, OTHER_KEY: EXPR_DOC <, ...>>}`。`_id`指定分组键，也是输出文档的`_id`值。"$INPUT_KEY"使用输入文档指定的键进行分组。EXPR_DOC可以使用以下分组表达式，其EXPR可以直接指定值，可以使用"$INPUT_KEY"来指定键的值：

	算数操作符：

	* $avg：返回分组中EXPR平均值。`$avg: EXPR`。
	* $sum：返回分组中EXPR总和。`$sum: EXPR`。

	极值操作符：

	* $first：返回分组中第一个EXPR。`$first: EXPR`。
	* $last：返回分组中最后一个EXPR。`$last: EXPR`。
	* $max：返回分组中最大的EXPR。`$max: EXPR`。
	* $min：返回分组中最小的EXPR。`$min: EXPR`。

	数组操作符：

	* $addToSet：若数组不包含EXPR，则将其添加到数组中。`$addToSet: EXPR`。数组中元素的顺序是不确定的。
	* $push：无论EXPR为何值，都将其添加到数组中。`$push: EXPR`。
* $limit：限制。`$limit: N`。结果集只取前N个文档。
* $match：匹配。`$match: QUERY_DOC`。QUERY_DOC见`DBCollection.prototype.find`，但不可以使用地理空间操作符。
* $project：投射。`$project: PROJECTION_DOC`。PROJECTION_DOC见`DBCollection.prototype.find`。
* $skip：跳过。`$skip: N`。跳过结果集的前N个文档。
* $sort：排序。`$sort: SORT_DOC`。SORT_DOC见`DBQuery.prototype.sort`。
* $unwind: 拆分。`$unwind: "$ARRAY_KEY"`。根据数组键中的每一个值拆分为单独的文档。如果数组有N个元素，则拆分成N个文档，此键依次替换成各个数组元素，并包含原文档中的其他键。

返回的DOCS实现了`forEach`方法。

示例：

```js
var DOCS = db.EIData.aggregate(
	{$match: {"user.firstname": "Tom"}},
	{$group: {_id: "$user.lastname", amount: {$sum: 1}}},
	{$sort: {amount: 1}}
);
```

## DBCollection.prototype.convertToCapped

将集合转换成固定集合。

```js
var OBJ = DBCOLLECTION.convertToCapped({size: N_BYTES});
```

## DBCollection.prototype.count

返回文档的数量。

```js
var N = DBCOLLECTION.count(<QUERY_DOC>);
```

QUERY_DOC见`DBCollection.prototype.find`。当不指定QUERY_DOC时，即查询文档的总数，无论集合有多大，都可以很快返回。

## DBCollection.prototype.createIndex

创建索引。

```js
var OBJ = DBCOLLECTION.createIndex(INDEX_DOC <, INDEX_OPTION_DOC>);
```

INDEX_DOC为`{KEY: 1|-1|"hashed"|"text"|"2d"|"2dsphere" <, ...>}`。1为升序，-1为降序，hashed为散列索引，text为全文本索引，2d或2dsphere为地理空间索引。如指定多个键，则为按指定键顺序的复合索引。

2d索引需建立在值为`[X, Y]`形式的字段上。

2dsphere索引需建立在值为GeoJSON的字段上。GeoJSON的语法如下：

* 点：`{type: "Point", coordinates: [X, Y]}`。
* 线：`{type: "Line", coordinates: [[X1, Y1], [X2, Y2] <, ...>]}`。
* 多边形：`{type: "Polygon", coordinates: [[X1, Y1], [X2, Y2] <, ...>]}`。

INDEX_OPTION_DOC中可使用以下字段：

* background：为true或1，则在后台创建索引。
* default_language：为全文本索引指定分词语言。默认为`english`。如插入的文档有`language`字段，则分词语言会被其值覆盖。
* expireAfterSeconds：指定超时秒数，创建为TTL索引。
* max：指定索引边界的最大值。2d索引默认为180。
* min：指定索引边界的最小值。2d索引默认为-180。
* name：指定索引名。
* sparse：为true或1，则创建为稀疏索引。
* unique：为true或1，则创建为唯一索引。
* weights：为全文本索引不同的字段指定权重。如：`weights: {KEY|$**: WEIGHT <, ...>}`。`$**`表示所有字符串字段，包括内嵌字段。权重范围为1至1000000000，默认为1。

## DBCollection.prototype.deleteMany

删除多个文档。

```js
var OBJ = DBCOLLECTION.deleteMany(QUERY_DOC);
```

QUERY_DOC见`DBCollection.prototype.find`。

返回包括以下字段：

* acknowledged：是否已确认写入。
* deletedCount：被删除文档的数量。

## DBCollection.prototype.deleteOne

删除单个文档。

```js
var OBJ = DBCOLLECTION.deleteOne(QUERY_DOC);
```

QUERY_DOC见`DBCollection.prototype.find`。

返回包括以下字段：

* acknowledged：是否已确认写入。
* deletedCount：被删除文档的数量。

## DBCollection.prototype.distinct

字段排重。如果字段是数组，则会使用数组元素进行排重。

```js
var VALUE_ARR = DBCOLLECTION.distinct(KEY);
```

## DBCollection.prototype.drop

删除集合。过一段时间后会释放磁盘空间。

```js
var BOOL = DBCOLLECTION.drop();
```

## DBCollection.prototype.dropIndex

删除索引。

```js
var OBJ = DBCOLLECTION.dropIndex(INDEX_NAME);
```

INDEX_NAME为`DBCollection.prototype.getIndexes`返回的name字段的值。

## DBCollection.prototype.find

查找文档。

```js
var DBQUERY = DBCOLLECTION.find(<QUERY_DOC <, PROJECTION_DOC>>);
DBCOLLECTION.find(<QUERY_DOC <, PROJECTION_DOC>>);
```

QUERY_DOC为查找的匹配条件，各键值对的关系是与。可使用：

* KEY：键的匹配条件。可以使用a.b的形式指定内嵌文档（可为数组）的字段；可以使用a.INDEX的形式通过下标指定数组元素。`KEY: VALUE|CONDITION_DOC`。VALUE为`null`还可匹配键不存在的情况；VALUE为子文档或数组则进行精确匹配，与字段的顺序和数量有关。CONDITION_DOC可使用以下操作符：
	* $all：全包含。`$all: [VALUE <, ...>]`。
	* $elemMatch：数组元素匹配。`$elemMatch: CONDITION_DOC`。只匹配数组，存在元素与CONDITION_DOC匹配则数组匹配。CONDITION_DOC中的键为数组元素子文档的键。
	* $eq：等于。`$eq: VALUE`。
	* $exists：存在。`$exists: BOOL`。实际上，0和false表示false，其他值都表示true。注意，字段值为`null`也为存在。
	* $gt：大于。`$gt: VALUE`。
	* $gte：大于等于。`$gte: VALUE`。
	* $in：在其中。`$in: [VALUE <, ...>]`。返回文档的顺序与VALUE的顺序无关。
	* $lt：小于。`$lt: VALUE`。
	* $lte：小于等于。`$lte: VALUE`。
	* $ne：不等于。`$ne: VALUE`。
	* $nin：不在其中。`$nin: [VALUE <, ...>]`。
	* $not：取反。`$not: CONDITION_DOC`。
	* $mod：取余。`$mod: [DIVISOR, REMAINDER]`。
	* $size：数组长度。`$size: N`。

	也可以使用以下地理空间操作符（GEOJSON使用GeoJSON语法，见`DBCollection.prototype.createIndex`）：
	* $geoIntersects：查找与区域相交的文档。
		* 使用2dsphere索引：`$geoIntersects: {$geometry: GEOJSON}`。
	* $near：查找在区域附近的文档，由近至远排序。
		* 使用2dsphere索引：`$near: {$geometry: GEOJSON}`。
		* 使用2d索引：`$near: [X, Y]`。
	* $within：查找在区域之内的文档。
		* 使用2dsphere索引：`$within: {$geometry: GEOJSON}`。
		* 使用2d索引，只要某个点在区域之内就匹配：`$within: 2D_SHAPE`。2D_SHAPE可使用以下操作符表示形状：
			* $box：矩形。`$box: [[X1, Y1], [X2, Y2]]`。
			* $center：圆形。`$center: [[X, Y], R]`。
			* $polygon：多边形。`$polygon: [[X, Y] <, ...>]`。
* $and：与。`$and: [CONDITION_DOC <, ...>]`。
* $nor：都不。`$nor: [CONDITION_DOC <, ...>]`。
* $or：或。`$or: [CONDITION_DOC <, ...>]`。
* $text：使用全文本索引查询。`$text: SEARCH_DOC`。SEARCH_DOC可使用以下操作符：
	* $search：指定搜素条件。`$search: SEARCH`。SEARCH以空格分隔成若干个搜索词，各搜素词是或的关系。如搜索词以`""`引起表示必需匹配该词，如搜索词以`-`开头表示排除该词。
* $where：执行任意查询。`$where: FUNCTION`。FUNCTION中的this为当前遍历的文档，返回true表示匹配，否则表示不匹配。

PROJECTION_DOC为查找结果字段的投射方式。`{KEY: VALUE, <, ...>}`。对于KEY，若QUERY_DOC使用a.b进行查询，则可以通过定位操作符$以a.$的形式指定已匹配的数组元素，但只能指定第一个匹配的数组元素。VALUE可为：

* 0：不投射该键。`KEY: 0`。未指定的键都投射。如有一个非`_id`键指定为0，则所有键不能再指定为非0数值。除非使用`_id: 0`，否则`_id`会被自动投射。
* 非0数值：投射该键。`KEY: N`。未指定的键不投射。
* $slice：投射数组的切片子集。`KEY: {$slice: N|[OFFSET, COUNT]}`。N为正数则保留开头元素，为负数则保留末尾元素，0则为空数组。未指定的键都投射。
* "$INPUT_KEY"：使用文档指定键的值进行投射，类似键重命名。`KEY: "$INPUT_KEY"，INPUT_KEY的形式与QUERY_DOC中KEY的形式一样。
* 表达式：`KEY: EXPR_DOC`。EXPR_DOC可以使用以下表达式，其EXPR可以直接指定值，可以使用"$INPUT_KEY"来指定键的值，可以使用任意深度嵌套的表达式：

	数学表达式，接收数值类型，返回数值类型：

	* $add：相加。`$add: [EXPR <, ...>]`。
	* $subtract：相减。`$subtract: [EXPR1, EXPR2]`。返回EXPR1-EXPR2。
	* $multiply：相乘。`$multiply: [EXPR <, ...>]`。
	* $divide`：相除。$divide: [EXPR1, EXPR2]`。返回EXPR1/EXPR2。
	* $mod`：取余。$mod: [EXPR1, EXPR2]`。返回EXPR1%EXPR2。

	日期表达式，接收日期类型，返回数值类型：

	* $dayOfMonth：返回在当前月的第几天。`$dayOfMonth: EXPR`。
	* $dayOfWeek：返回在当前星期的第几天，星期天为第一天。`$dayOfWeek: EXPR`。
	* $dayOfYear：返回在当前年的第几天。`$dayOfYear: EXPR`。
	* $hour：返回小时，二十四小时制。`$hour: EXPR`。
	* $minute：返回分钟。`$minute: EXPR`。
	* $month：返回月份。`$month: EXPR`。
	* $second：返回秒。`$second: EXPR`。
	* $week：返回在当前年的第几个星期，第一个星期是从当前年的第一个星期天开始。`$week: EXPR`。
	* $year：返回年。`$year: EXPR`。

	字符串表达式，接收字符串类型，返回字符串类型：

	* $concat：连接字符串。`$concat: [EXPR <, ...>]`。
	* $substr：截取子串。`$substr: [EXPR, START_OFFSET, LENGTH]`。从START_OFFSET字节（含）开始截取LENGTH字节
	* $toLower：返回字符串小写。`$toLower: EXPR`。
	* $toUpper：返回字符串大写。`$toUpper: EXPR`。

	逻辑表达式，包括：

	1. 比较表达式：

		* $cmp：比较。`$cmp: [EXPR1, EXPR2]`。若EXPR1<EXPR2，返回负数；若EXPR1==EXPR2，返回0；若EXPR1>EXPR2，返回正数。
		* $eq：相等。`$eq: [EXPR1, EXPR2]`。返回EXPR1==EXPR2。
		* $gt：大于。`$gt: [EXPR1, EXPR2]`。返回EXPR1>EXPR2。
		* $gte：大于等于。`$gte: [EXPR1, EXPR2]`。返回EXPR1>=EXPR2。
		* $lt：小于。`$lt: [EXPR1, EXPR2]`。返回EXPR1<EXPR2。
		* $lte：小于等于。`$lte: [EXPR1, EXPR2]`。返回EXPR1<=EXPR2。
		* $ne：不等于。`$ne: [EXPR1, EXPR2]`。返回EXPR1!=EXPR2。
		* $strcasecmp：字符串比较，区分大小写。`$strcasecmp: [EXPR1, EXPR2]`。接收字符串类型，返回结果类似`$cmp`。

	1. 布尔表达式，接收布尔类型，返回布尔类型：

		* $and：且。`$and: [EXPR <, ...>]`。
		* $not：非。`$not: EXPR`。
		* $or：或。`$or: [EXPR <, ...>]`。
	1. 控制语句：

		* $cond：判断条件。`$cond: [BOOL_EXPR, EXPR_WHILE_TRUE, EXPR_WHILE_FALE]`：若BOOL_EXPR为`true`，则返回EXPR_WHILE_TRUE，否则返回EXPR_WHILE_FALE。
		* $ifNull：判断空值。`$ifNull: [EXPR, REPLACEMENT_EXPR]`：若EXPR为`null`，则返回REPLACEMENT_EXPR，否则返回EXPR。
* 其他：投射该键为VALUE。`KEY: VALUE`。

返回`DBQuery`类型。如不接收返回，则默认打印前20个文档，若需继续遍历则使用辅助扩展`it`。

示例：

```js
DBCOLLECTION.find({firstname: "Tom"}, {_id: 0});
```

## DBCollection.prototype.findAndModify

查找并更新文档。只会更新一个文档。

```js
var RESULT_DOC = DBCOLLECTION.findAndModify({
	query: QUERY_DOC
	<, sort: SORT_DOC>
	<, remove: BOOL>
	<, update: DOC|MODIFIER_DOC>
	<, upsert: BOOL>
	<, new: BOOL>
	<, fields: PROJECTION_DOC>
});
```

* fields：返回結果包含的字段。PROJECTION_DOC见`DBCollection.prototype.find`。
* new：true返回更新后的文档，false返回更新前的文档。缺省为false。
* query：查找规则。QUERY_DOC见`DBCollection.prototype.find`。
* remove：true为删除文档，false为更新文档。默认为false。`remove`和`update`必须使用其一。
* sort：排序规则。SORT_DOC见`DBQuery.prototype.sort`。
* update：更新规则。DOC和MODIFIER_DOC见`DBCollection.prototype.update`。`remove`和`update`必须使用其一。
* upsert：是否执行upsert。

## DBCollection.prototype.findOne

查找单个文档。

```js
var DOC = DBCOLLECTION.findOne(<QUERY_DOC <, PROJECTION_DOC <, OPTION>>>);
```

QUERY_DOC见`DBCollection.prototype.find`。

PROJECTION_DOC见`DBCollection.prototype.find`。

OPTION可使用以下字段：

* sort：排序方式。`sort: SORT_DOC`。SORT_DOC见`DBQuery.prototype.sort`。

返回的文档经过格式化。如找不到，则返回`null`。

## DBCollection.prototype.findOneAndUpdate

查找单个文档并更新。

```js
var DOC = DBCOLLECTION.findOneAndUpdate(
	QUERY_DOC,
	MODIFIER_DOC,
	<OPTION>
);
```

QUERY_DOC见`DBCollection.prototype.find`。

MODIFIER_DOC见`DBCollection.prototype.update`。

OPTION可使用以下字段：

* projection：投射方式。`projection: PROJECTION_DOC`。PROJECTION_DOC见`DBCollection.prototype.find`。
* returnDocument："before"为返回更新前的文档，"after"为返回更新后的文档。
* sort：排序方式。`sort: SORT_DOC`。SORT_DOC见`DBQuery.prototype.sort`。
* upsert：是否执行upsert。即，查询条件QUERY_DOC匹配不到文档时，先使用QUERY_DOC创建文档，再使用MODIFIER_DOC修改文档。

## DBCollection.prototype.getIndexes

查看集合的所有索引信息。

```js
var ARR = DBCOLLECTION.getIndexes();
```

返回的数组元素包括以下字段：

* key：索引的键，即`DBCollection.prototype.createIndex`中创建索引的INDEX_DOC。
* name：索引名，默认为根据`DBCollection.prototype.createIndex`中INDEX_DOC生成的KEY_DIRECTION[_...]的形式。
* v：索引版本。

## DBCollection.prototype.help

查看集合的帮助。

```js
DBCOLLECTION.help();
```

## DBCollection.prototype.insert

插入文档。

```js
var WRITERESULT = DBCOLLECTION.insert(DOC <, OPTION>);
var BULKWRITERESULT = DBCOLLECTION.insert([DOC <, ...>] <, OPTION>);
```

OPTION可使用以下字段：

* writeConcern：写入安全配置。`writeConcern: WRITE_CONCERN_DOC`。WRITE_CONCERN_DOC可使用以下字段：
	* j：
	* w：等待数据库写入成功才返回。整数N为写入至少N个节点（包括主节点），`"majority"`为写入大多数节点，或为自定义复制保证规则名字（见`rs.initiate`的GET_LAST_ERROR_MODES_NAME）。
	* wtimeout：写入超时，单位为毫秒。超时后返回失败，此时并不意味着写操作失败，只是表明写操作未复制到足够多的节点。

插入单个文档时，类似`DBCollection.prototype.insertOne`，返回`WriteResult`类型。

插入多个文档时，类似`DBCollection.prototype.insertMany`，返回`BulkWriteResult`类型。

## DBCollection.prototype.insertMany

插入多个文档。如其中某个文档插入失败，则之前的都插入成功，之后的都插入失败，且会抛出异常。

```js
var OBJ = DBCOLLECTION.insertMany([DOC <, ...>]);
```

返回包括以下字段：

* acknowledged：是否已确认写入。
* insertedIds：被插入文档的`_id`值的数组。如DOC未指定则自动添加。

## DBCollection.prototype.insertOne

插入单个文档。

```js
var OBJ = DBCOLLECTION.insertOne(DOC);
```

返回包括以下字段：

* acknowledged：是否已确认写入。
* insertedId：被插入文档的`_id`值。如DOC未指定则自动添加。

## DBCollection.prototype.mapreduce

执行MapReduce。

```js
var RESULT = DBCOLLECTION.mapreduce(MAP, REDUCE, OUTPUT_COLLECTION_NAME|OPTION_DOC);
```

MAP是一个函数，`this`表示当前正在处理的文档：

```js
function () {
	emit(KEY, VALUE);
}
```

REDUCE是一个函数，一定要能够在map阶段的结果或前一个reduce阶段的结果上反复执行，因此返回的文档必须能作为VALUE_ARR的一个元素：

```js
function(KEY, VALUE_ARR) {
	return VALUE;
}
```

OPTION_DOC可使用以下字段：

* finalize：将reduce的结果进行最后处理。`finalize: FINALIZE`。FINALIZE是一个函数：

	```js
	function(KEY, VALUE) {
		return FINALIZE_VALUE;
	}
	```
* limit：执行map之前限制文档的最多数量（含）。1limit: N`。
* out：结果集合名。`out: OUTPUT_COLLECTION_NAME`。必需此字段。
* query: 执行map之前的查找规则。`query: QUERY_DOC`。QUERY_DOC见`DBCollection.prototype.find`。
* scope：变量作用域。`scope: {PARAM_NAME: PARAM_VALUE <, ...>}`。其他函数可以直接使用PARAM_NAME来使用作用域中的变量。
* sort：执行map之前的排序规则。`sort: SORT_DOC`。SORT_DOC见`DBQuery.prototype.sort`。

会先清空结果集，结果集合中的每个文档包括以下字段：

* _id：键。即MAP或REDUCE的KEY。
* value：值。即REDUCE的最终VALUE，如果指定了FINALIZE则为其返回的FINALIZE_VALUE。

可以在MAP、REDUCE、FINALIZE中使用`print`将信息输出至服务器日志。

## DBCollection.prototype.remove

删除文档。

```js
var WRITERESULT = DBCOLLECTION.remove(QUERY_DOC <, BOOL_ONE>);
```

QUERY_DOC见`DBCollection.prototype.find`。

返回`WriteResult`类型。

## DBCollection.prototype.renameCollection

重命名集合。无论集合有多大都在瞬间完成。

```js
var OBJ = DBCOLLECTION.renameCollection(NEW_COLLECTION_NAME <, REMOVE_EXISTING>);
```

若集合NEW_COLLECTION_NAME已存在，REMOVE_EXISTING为true则先删除该集合；为false则返回失败。默认为false。

## DBCollection.prototype.save

保存文档。如果DOC没有`_id`或集合中无该`_id`值的文档，则插入文档；否则更新该`_id`值的文档。

```js
var WRITERESULT = DBCOLLECTION.save(DOC);
```

返回`WriteResult`类型。

## DBCollection.prototype.stats

查看集合的状态。

```js
var OBJ = DBCOLLECTION.stats(<SCALE_FACTOR>);
```

SCALE_FACTOR为比例因子（scale factor），返回文档的字段数值会先除以SCALE_FACTOR。默认为1，即以1字节为单位。

返回文档的字段如下：

* avgObjSize：文档的平均大小。相当于size/count。
* count：文档的数量。
* indexSizes：各索引的大小。其键为各索引名，其值为各索引的大小。
* nindexes：索引的数量。索引在建立完成后才会被算入其中。
* ns：集合的命名空间。
* paddingFactor：填充因子。
* size：所有文档的大小合计。相当于所有文档执行`Object.bsonsize`的总和。
* storageSize：集合占用的存储空间大小。包括文档占用的空间、文档间的间隔、索引占用的空间、集合两端预留的未使用空间。
* totalIndexSize：所有索引的大小合计。相当于`indexSizes`各值的总和。

## DBCollection.prototype.update

更新文档。

```js
var WRITERESULT = DBCOLLECTION.update(QUERY_DOC, DOC <, BOOL_UPSERT <, BOOL_MANY>>);
var WRITERESULT = DBCOLLECTION.update(QUERY_DOC, MODIFIER_DOC <, BOOL_UPSERT <, BOOL_MANY>>);
```

不可更新`_id`字段。

QUERY_DOC见`DBCollection.prototype.find`。

可以将文档替换成DOC。

也可以在MODIFIER_DOC使用更新修改器修改部分字段。更新修改器可使用：

* $inc：增加或减少键的值，键不存在则先创建为0。`$inc: {KEY: NUMBER <, ...>}`。只能用于数值类型的键。
* $rename：重命名键。`$rename: {KEY: NEWKEY}`。
* $set：设置键的值，键不存在则创建。`$set: {KEY: VALUE <, ...>}`。
* $setOnInsert：当更新需插入文档时，同时设置键的值；只更新不插入文档时，不进行设置。`$setOnInsert: {KEY: VALUE <, ...>}`。
* $unset：删除键。`$unset: {KEY: 1 <, ...>}`。值是无关的。

以及数组修改器：

* $addToSet：将数组作为集合，保证元素不重复，向末尾推入元素，键不存在则先创建为空数组。`$addToSet: {ARR_KEY: ELEMENT|EACH_DOC <, ...>}`。只能用于数组类型的键。EACH_DOC可使用以下子操作符：
	* $each：推入多个元素。`$each: [ELEMENT <, ...>]`。
* $pop：从数组弹出单个元素。`$pop: {ARR_KEY: 1|-1}`。1为从开头弹出，-1为从结尾弹出。只能用于数组类型的键。
* $pull：从数组彻底删除元素，即使元素出现多次。键可不存在。`$pull: {ARR_KEY: ELEMENT}`。
* $push: 向数组末尾推入元素，键不存在则先创建为空数组。`$push: {ARR_KEY: ELEMENT|PUSH_EACH_DOC <, ...>}`。只能用于数组类型的键。PUSH_EACH_DOC可使用以下子操作符：
	* $each: 推入多个元素。`$each: [ELEMENT <, ...>]`。
	* $slice：推入元素按需排序后，保留多少个元素。`$slice: N`。正数则保留开头元素，负数则保留末尾元素，0则清空。需与`$each`一起使用。
	* $sort：推入元素后，排序。`$sort: {ELEMENT_KEY: 1|-1}`。ELEMENT_KEY为数组元素的键，1为升序，-1为降序。需与`$each`一起使用。

上述KEY等键可以使用a.b的形式指定内嵌文档（可为数组）的字段；可以使用a.INDEX的形式通过下标指定数组元素；若QUERY_DOC使用a.b进行查询，则可以通过定位操作符$以a.$的形式指定已匹配的数组元素，但只能更新第一个匹配的数组元素。

如BOOL_UPSERT为true（缺省为false），则执行upsert。即，查询条件QUERY_DOC匹配不到文档时，先使用QUERY_DOC创建文档，再使用DOC或MODIFIER_DOC修改文档。

如BOOL_MANY为true（缺省为false），则更新所有匹配查询条件QUERY_DOC的文档，否则只更新第一个文档。

返回`WriteResult`类型。

示例：

```js
DBCOLLECTION.update({firstname: "Tom"}, {$inc: {age: 1}});
```

## DBCollection.prototype.updateMany

更新多个文档。

```js
var OBJ = DBCOLLECTION.updateMany(QUERY_DOC, MODIFIER_DOC);
```

QUERY_DOC见`DBCollection.prototype.find`。

MODIFIER_DOC见`DBCollection.prototype.update`。

返回包括以下字段：

* acknowledged：是否已确认写入。
* matchedCount：匹配的文档数量。
* modifiedCount：修改的文档数量。

## DBCollection.prototype.updateOne

更新单个文档。

```js
var OBJ = DBCOLLECTION.updateOne(QUERY_DOC, MODIFIER_DOC);
```

QUERY_DOC见`DBCollection.prototype.find`。

MODIFIER_DOC见`DBCollection.prototype.update`。

返回包括以下字段：

* acknowledged：是否已确认写入。
* matchedCount：匹配的文档数量。
* modifiedCount：修改的文档数量。

## DBCollection.prototype.validate

检验集合数据是否有效，即是否没有损坏。

```js
var OBJ = DBCOLLECTION.validate(<{full: true}>);
```

如指定了`{full: true}`，则进行更完整的检验。

返回包括以下字段：

* valid：数据是否有效。

# DBQuery

查询游标类型。

## DBQuery.prototype.explain

获取执行计划。

```js
var OBJ = DBQUERY.explain();
```

返回包括以下字段：

* ok：是否成功得出执行计划。
* queryPlanner：执行计划。
	* parsedQuery：被解析过的查询条件。
	* rejectedPlans：拒绝的执行计划。
	* winningPlan：胜出的执行计划。
		* inputStage
			* stage
			* keyPattern
			* indexName
			* isMultiKey：是否是多键索引。
			* multiKeyPaths
			* isUnique
			* isSparse
			* isPartial
			* indexVersion
			* direction
			* indexBounds
		* shards：使用到的分片的执行计划组成的数组。
		* stage
* serverInfo：服务器信息。

## DBQuery.prototype.forEach

遍历所有文档。

```js
DBQUERY.forEach(DOC => {});
```

## DBQuery.prototype.hasNext

是否还有下一个文档。

```js
var BOOL = DBQUERY.hasNext();
```

## DBQuery.prototype.hint

使用指定的索引。

```js
var DBQUERY2 = DBQUERY.hint(HINT_DOC);
```

HINT_DOC为`{KEY: 1|-1 <, ...>}`。1为升序，-1为降序。如指定多个键，则为按指定键顺序的复合索引。或为`{$natural: 1|-1}`使用自然排序，进行全表扫描。

## DBQuery.prototype.limit

限制文档的最多数量（含）。

```js
var DBQUERY2 = DBQUERY.limit(N);
```

## DBQuery.prototype.min

指定索引的最小值（含）。必须与`DBQuery.prototype.hint`一起使用。

```js
var DBQUERY2 = DBQUERY.min({KEY: VALUE <, ...>});
```

## DBQuery.prototype.max

指定索引的最大值（含）。必须与`DBQuery.prototype.hint`一起使用。

```js
var DBQUERY2 = DBQUERY.max({KEY: VALUE <, ...>});
```

## DBQuery.prototype.next

返回下一个文档。

```js
var DOC = DBQUERY.next();
```

返回的文档经过格式化。

## DBQuery.prototype.pretty

以优美的格式显示。

```js
var DBQUERY2 = DBQUERY.pretty()
```

## DBQuery.prototype.skip

跳过若干数量的文档。

```js
var DBQUERY2 = DBQUERY.skip(N);
```

## DBQuery.prototype.sort

对文档进行排序。

```js
var DBQUERY2 = DBQUERY.sort(SORT_DOC);
```

SORT_DOC可使用`{KEY: 1|-1 <, ...>}`。1为升序，-1为降序。按照各KEY指定的次序逐个排序。或为`{$natural: 1|-1}`使用自然排序。

## DBQuery.prototype.toArray

遍历所有文档并以数组返回。

```js
var ARR = DBQUERY.toArray();
```

# DriverSession

会话类型。

示例：

```js
var mongo = db.getMongo();
var session = mongo.startSession();
session.startTransaction();
var test = session.getDatabase("test");
test.foo.insertOne({});
session.commitTransaction();
session.endSession();
```

## DRIVERSESSION.abortTransaction

回滚事务。

```js
DRIVERSESSION.abortTransaction();
```

## DRIVERSESSION.commitTransaction

提交事务。

```js
DRIVERSESSION.commitTransaction();
```

## DRIVERSESSION.endSession

结束会话。

```js
DRIVERSESSION.endSession();
```

## DRIVERSESSION.getDatabase

获取数据库。

```js
var DATABASE = DRIVERSESSION.getDatabase(DB_NAME);
```

返回`DB`类型。

## DRIVERSESSION.startTransaction

开始事务。

```js
DRIVERSESSION.startTransaction();
```

# EDITOR

`edit`辅助扩展使用的编辑器路径。也可在环境变量中设置。

```js
var EDITOR = "EDITOR_PATH";
```

# ISODate

创建UTC时间。

```js
var DATE = new ISODate();
var DATE = new ISODate(STR);
```

STR最完整的格式为：2006-01-02T15:04:05.000Z。

返回`Date`类型。

# load

加载并执行脚本。

```js
load(JS_FILENAME);
```

相对路径根据shell运行目录，且无法识别主目录“~”。

# MaxKey

最大值。值为`{ "$maxKey" : 1 }`。

# MinKey

最小值。值为`{ "$minKey" : 1 }`。

# Mongo

MongoDB连接类型。

## new Mongo

创建连接。

```js
var MONGO = new Mongo(HOST_PORT);
```

## Mongo.prototype.getDB

获取数据库。

```js
var DATABASE = MONGO.getDB(DB_NAME);
```

返回`DB`类型。

## Mongo.prototype.getDBs

获取所有数据库信息。类似`show dbs`。

```js
var OBJ = MONGO.getDBs();
```

## Mongo.prototype.setSlaveOk

允许连接从备份节点读取数据。

```js
MONGO.setSlaveOk()
```

默认不允许连接从备份节点读取数据，否则会出现“not master and slaveOk=false”的错误。

## Mongo.prototype.startSession

开启会话。需要在副本集中执行。

```js
var DRIVERSESSION = MONGO.startSession();
```

返回`DriverSession`类型。

# NumberInt

32位有符号整数类型。

```js
var NUMBERINT = new NumberInt(N);
```

# NumberLong

64位有符号整数类型。

```js
var NUMBERLONG = new NumberLong(N);
```

# Object

对象类型。

额外添加以下接口：

## Object.bsonsize

获取对象的BSON字节大小。

```js
var N = Object.bsonsize(DOC);
```

# ObjectId

对象ID类型。由12字节组成，依次为：4字节秒级时间戳、3字节主机唯一标识符（通常是主机名的散列值）、2字节进程ID、3字节自增计数器。

```js
var OBJECTID = new ObjectId();
var OBJECTID = new ObjectId(HEX_STR);
```

## OBJECTID.str

对象ID的16进制字符串。

## ObjectId.prototype.getTimestamp

返回对象ID的时间戳对应的时间。

```js
var DATE = OBJECTID.getTimestamp();
```

# print

打印输出。

```js
print(<ARG <, ...>>);
```

# printjson

以JSON格式打印输出。

```js
printjson(ARG);
```

# prompt

shell提示。

```js
var prompt = "STR";
var prompt = function() {return "STR";};
```

为字符串或返回字符串的函数。如为函数，建议处理异常。

# quit

退出。

```js
quit();
```

# run

执行外部命令行程序。

```js
run(COMMAND <, ARG <, ...>>);
```

# ReplSetTest

副本集类型。

## new ReplSetTest

创建副本集。并不会创建数据库文件和启动进程，也不会从备份节点中选举出主节点，需依次执行`REPLSETTEST.startSet`和`REPLSETTEST.initiate`。

```js
var REPLSETTEST = new ReplSetTest(OPTION);
```

OPTION包括以下字段：

* name：副本集名字。缺省则使用“__unknown_name__”。
* nodes：节点的个数。

## REPLSETTEST.initiate

初始化副本集。会从备份节点中选举出主节点。类似`rs.initiate`。

```js
REPLSETTEST.initiate(<REPL_CONFIG>);
```

REPL_CONFIG见`rs.initiate`。

## REPLSETTEST.startSet

启动副本集。会先删除原有数据库目录，再创建数据库目录和文件，启动所有副本集进程，同时会不断输出所有节点的日志。

```js
var ARR = REPLSETTEST.startSet();
```

ARR的各个元素包含节点的地址。

各节点的数据库目录名为“副本集名-节点序号”的形式。

## REPLSETTEST.stopSet

停止副本集。会停止所有副本集进程。

```js
REPLSETTEST.stopSet();
```

# rs

副本集辅助函数。

## rs.add

为副本集添加节点。

```js
var OBJ = rs.add(HOST_PORT|REPL_MEMBER);
```

REPL_MEMBER见`rs.initiate`。

此节点的数据目录要么是空的，要么拥有一份其他节点的数据副本。

## rs.addArb

为副本集添加仲裁者。

```js
var OBJ = rs.addArb(HOST_PORT);
```

## rs.config

查看副本集配置。可见隐藏节点。

```js
var REPL_CONFIG = rs.config();
```

REPL_CONFIG见`rs.initiate`。此外还包括以下字段：

* version：版本号。每次修改副本集配置都会自增1，初始值为1。

## rs.freeze

冻结备份节点。只能在备份节点上执行。

```js
var OBJ = rs.freeze(<SECONDS>);
```

SECONDS为持续时间。如大于0，则强制其在指定时间内始终为备份节点，不能被选举为主节点。如为0，则解除冻结。如不指定，默认为0。

## rs.help

查看副本集辅助函数的帮助。

```js
rs.help();
```

## rs.initiate

初始化副本集。会从备份节点中选举出主节点。

```js
var OBJ = rs.initiate(<REPL_CONFIG>);
```

REPL_CONFIG包括以下字段：

* _id：副本集名字。必需和服务器启动时设置的副本集名字一致。
* members：副本集成员节点数组。每个元素REPL_MEMBER包括以下字段：
	* _id：节点ID。数值类型，可选，必需唯一。
	* arbiterOnly：是否为仲裁者。可选。
	* buildIndexes：是否创建索引。可选，`priority`必需为0。
	* hidden：是否为隐藏节点。可选，`priority`必需为0。
	* host：节点主机和端口地址。必需唯一。
	* priority：优先级。可选，0至100优先级递增。
	* slaveDelay：是否为延迟备份节点。可选，`priority`必需为0。
	* tags：分类标签。`tags: {REPL_TAG_KEY: VALUE <, ...>}`。可选。
	* votes：投票数量。为0，则阻止节点在选举中投赞成票，但仍可投否决票。可选，应该只在创建包含7个（不含）以上节点的副本集或者希望阻止自动故障转移时使用。
* protocolVersion：副本集协议版本。自MongoDB 4.0起只支持1。
* settings：包括以下字段：
	* allowChaining：是否允许复制链。如为false，则强制每个备份节点都从主节点进行复制。可选，默认为true。
	* getLastErrorModes：自定义复制保证规则。`getLastErrorModes: {GET_LAST_ERROR_MODES_NAME: {REPL_TAG_KEY: N} <, ...>}`。可选，表示保证复制至分类标签REPL_TAG_KEY中至少N个不同的分类，每个分类的至少一个节点中。

如不使用REPL_CONFIG，则会自动生成单节点副本集的配置，配置中自动生成的`members.host`字段可能并不适用。

如果创建一个全新的副本集，可以在任意一个节点上执行；如果副本集中已经有一个有数据的节点，则只能在此节点上执行；如果副本集中拥有数据的节点不止一个，则无法初始化副本集。

## rs.reconfig

重新配置副本集。

```js
var OBJ = rs.reconfig(REPL_CONFIG <, OPTION>);
```

REPL_CONFIG见`rs.initiate`。不能修改`members._id`。

OPTION可使用以下字段：

* force：是否强制重新配置副本集。若为true，则只能在新配置下可能成为备份节点的节点上执行；否则，只能在新配置下可能成为主节点的节点上执行。默认为false。若为`true`，执行成功后，`rs.config`返回的`version`字段会大幅增加。

## rs.remove

为副本集删除节点。

```js
var OBJ = rs.remove(HOST_PORT);
```

## rs.status

以当前节点的角度查看副本集所有节点的当前状态。可见隐藏节点。

```js
var OBJ = rs.status();
```

返回包括以下字段：

* members：副本集成员节点状态数组。每个元素包括以下字段：
	* _id：节点ID。
	* errmsg：节点在心跳请求中返回当前节点的状态信息。通常不是错误信息。
	* health：当前节点是否可达此节点。可达为1，不可达为0。
	* lastHeartbeat：当前节点最后收到此节点心跳的时间。
	* name：节点主机和端口地址。
	* optime：节点oplog中最后一个操作发生的时间（也就是操作被同步的时间）。与optimeDate一致。这是其他节点通过心跳报告过来的，和实际会有偏差。
	* optimeDate：节点oplog中最后一个操作发生的时间（也就是操作被同步的时间）。与optime一致。这是其他节点通过心跳报告过来的，和实际会有偏差。
	* pingMs：心跳从当前节点到达此节点所花费的平均时间。单位为毫秒。
	* self：是否是当前节点自身。只会出现在当前节点的元素中。
	* state：节点状态的内部表示。与stateStr一致。
	* stateStr：节点状态字符串。与state一致，如：PRIMARY、SECONDARY。
	* uptime：当前节点可达此节点时起直到现在的时长，单位是秒。
* set：副本集名字。
* syncSourceHost：当前节点同步源的主机和端口地址。只当当前节点为备份节点时有值。

## rs.stepDown

将主节点退位为备份节点。只能在主节点上执行。

```js
var OBJ = rs.stepDown(<SECONDS>);
```

SECONDS为持续时间。如不指定，默认为60。

## rs.syncFrom

修改当前节点的同步源。只能在备份节点上执行。

```js
var OBJ = rs.syncFrom(HOST_PORT);
```

如果指定一个并不比当前节点领先的节点作为同步源，会给出警告，但仍然被允许。

# sh

分片集群辅助函数。

## sh.addShard

将副本集或单机增加为分片。

```js
var OBJ = sh.addShard(REPLSET|HOST_PORT);
```

REPLSET的格式为：`REPLSET_NAME/HOST_PORT<,...,HOST_PORT>`。REPLSET_NAME会被用作分片名，不能和现有分片相同。如果副本集已有数据，会将其所有数据库添加进分片集群中，但不能和现有分片有同名的数据库。可指定任意个（并不需要所有）HOST_PORT，会自动检测副本集所有节点。

如使用HOST_PORT，分片名的格式为shardNNNN，NNNN为4位整数。

## sh.addShardTag

为分片添加标签。

```js
var OBJ = sh.addShardTag(SHARD_NAME, SHARD_TAG);
```

单个分片可添加的标签数量没有限制。

## sh.addTagRange

为分片标签添加范围规则。

```js
var OBJ = sh.addTagRange(COLLECTION_NAMESPACE, {SHARD_KEY: MIN <, ...>}, {SHARD_KEY: MAX <, ...>}, SHARD_TAG);
```

范围包含MIN，不包含MAX。并不会立即生效，其只是一个对于均衡器的指令。

## sh.enableSharding

对数据库启用分片。

```js
var OBJ = sh.enableSharding(DB_NAME);
```

## sh.getBalancerState

查看均衡器开启状态。

```js
var BOOL = sh.getBalancerState();
```

## sh.help

查看分片集群辅助函数的帮助。

```js
sh.help();
```

## sh.moveChunk

将包括指定片键值的块移动到指定分片。移动完成后才返回。

```shell
var OBJ = sh.moveChunk(COLLECTION_NAMESPACE, {SHARD_KEY: VALUE <, ...>}, SHARD_NAME);
```

除非遇到特殊情况，否则都应使用自动分片，而非手动分片。在均衡器开启时尤其要注意。

## sh.splitAt

在指定片键值处拆分块。

```js
var OBJ = sh.splitAt(COLLECTION_NAMESPACE, {SHARD_KEY: VALUE <, ...>});
```

## sh.removeShardTag

删除分片标签。

```js
var OBJ = sh.removeShardTag(SHARD_NAME, SHARD_TAG);
```

## sh.setBalancerState

设置均衡器开启状态。

```js
var OBJ = sh.setBalancerState(BOOL);
```

不能立即终止进行中的均衡过程。

## sh.shardCollection

对集合分片。

```js
var OBJ = sh.shardCollection(COLLECTION_NAMESPACE, {SHARD_KEY: 1|"hashed" <, ...>});
```

如集合DB_NAME.COLLECTION_NAME已存在，则SHARD_KEY上必需有索引；否则，会自动在SHARD_KEY上创建索引。

如SHARD_KEY为hashed且集合不存在，会立即创建一些空块，并将这些块分发到分片集群中。如SHARD_KEY不为"hashed"，则可能插入大量的文档后才进行分片。

## sh.status

查看分片集群的状态。

```js
sh.status(<true>);
```

如指定`true`则打印出详细信息，包括每个块的信息。

无返回，会打印几部分信息：

* sharding version
* shards：分片信息。每行一个文档，每个文档包括以下字段：
	* _id：分片名。
	* host：主机地址。格式为SHARD_NAME/HOST_PORT。
	* state：状态。
	* tags：分片标签数组。
* active mongoses
* autosplit
* balancer:
* databases：数据库信息。可能有若干份数据库信息。
	
	每份数据库信息以一个文档开始，文档包括以下字段：
	* _id：数据库名。
	* primary：主分片名。
	* partitioned：是否分片。

		此后是若干个集合的分片信息，以集合名为起始行。
		
		集合的分片信息几部分：
		* shard key：片键。
		* unique：是否唯一。
		* balancing：是否启用均衡器。
		* chunks：块信息。包括两部分：

			第一部分是此集合使用的分片。每行一个，包括分片名、分片中使用的块数量。

			第二部分是各个块信息。每行一个，包括“-->>”表示的块范围、所在的分片名、`Timestamp(MAJOR, MINOR)`的块版本（MAJOR会在块被迁移至新的分片时改变，MINOR会在块被拆分时改变）、特大块还有“jumbo”。
		* tag：分片标签信息。每行一个，包括标签名，“-->>”表示的标签范围。

如mongod使用`--noscripting`选项启动，则无法执行此方法。

# ShardingTest

分片集群类型。

## new ShardingTest

创建分片集群。会先删除原有数据库目录，再创建数据库目录和文件，启动mongos进程、配置副本集若干mongod进程、若干分片副本集的若干mongod进程，同时会不断输出所有节点的日志。

```js
var SHARDINGTEST = new ShardingTest(OPTION);
```

OPTION包括以下字段：

* chunkSize：块大小。单位为MB。
* shards：分片数量。

各配置副本集节点的目录名为“__unknown_name__-configRS-节点序号”的形式，各分片副本集节点的目录名为“__unknown_name__-rs分片序号-节点序号”的形式。

## SHARDINGTEST.stop

停止分片集群。会停止mongos进程、配置副本集所有mongod进程、所有分片副本集所有mongod进程。

```js
SHARDINGTEST.stop();
```

# startParallelShell

启动新的并行shell并执行脚本。

```js
var FUNC = startParallelShell(JS_CODE <, PORT>);
```

会打印脚本的执行结果。

# Timestamp

时间戳类型。

## new Timestamp

创建时间戳。

```js
var TIMESTAMP = new Timestamp(<SECONDS, COUNTER>);
```

SECONDS为从1970-01-01 00:00:00起的秒数。

COUNTER为自定义的计数器。

# WriteResult

单个写入结果类型。包括以下字段：

* _id：执行upsert时插入文档的`_id`。
* nMatched：匹配查询条件的文档数量。
* nModified：修改的文档数量。
* nUpserted：执行upsert的文档数量。
* nInserted：插入的文档数量。
* nRemoved：删除的文档数量。

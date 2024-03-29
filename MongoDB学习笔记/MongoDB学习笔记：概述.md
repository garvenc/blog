本文更新于2023-08-27，使用MongoDB 4.4.5。

[TOC]

# 与关系数据库比较

* 文档数据库，而不是关系数据库。不采用关系模型主要是为了获得更好的扩展性，以及还有其他的好处。
* 使用更灵活的文档，而不是关系数据库的行。文档中可再嵌入文档，用一条数据来表现复杂的层次关系。
* 没有预定义的模式。键值不再是固定的类型和大小，添加、删除、修改字段更容易。
* 采用横向扩展。面向文档的数据模型更容易在多台服务器之间进行数据分割。
* 不具备一些关系数据库中普遍的功能。如连接和复杂的多行事务，因为在分布式系统中这两个功能难以高效地实现。

# 组成

## 数据库

* 由多个集合组成。
* 每个数据库都有独立的权限，保存在不同的文件中（通常也在单独的文件夹中），数据库名就是文件名。
* 数据库名只能使用ASCII字母和数字。
* 数据库名区分大小写，即使在不区分大小写的文件系统中。为简单起见，数据库名应全部小写。
* 有一些保留的数据库名。如：admin、local、config。
* 将数据库名使用“.”连接集合名，得到集合的完全限定名，即命名空间。如z数据库中的a.b集合命名空间为z.a.b。

## 集合

* 类似拥有动态模式的关系数据库的表，由多个文档组成。
* 集合名不能含有“\0”，其表示集合名的结尾；不能含有“$”，因某些系统生成的集合包含此字符。
* 不能以“system.”开头，其为保留的系统集合前缀。
* 惯例使用“.”分隔不同的子集合，集合可跟其子集合无任何关系，甚至其不需存在。如a集合与a.b集合。

## 文档

* 类似但不同于关系数据库的行，数据的基本单元。
* 键值对是一个有序集，虽然通常顺序并不重要。
* 键不能含有“\0”，其表示键的结尾；不能含有“.”和“$”，其有特殊意义。
* 每个文档都必须有_id键且在集合中唯一。

# 数据类型

* `null`：空值或不存在的字段。
* 布尔值。
* 数值：64位浮点数（默认）、32位有符号整数（`NumberInt`）、64位有符号整数（`NumberLong`）。
* 字符串。
* 时间：UTC时间。
* 正则表达式。
* 数组。
* 文档。
* 对象ID（`ObjectId`）。
* 二进制数据：不能直接在JavaScript shell中使用。
* 代码：JavaScript代码。

不会自动进行数据类型转换。

排序时各类型比较从小到大依次为：

1. 最小值`MinKey`
1. `null`
1. 数值
1. 字符串
1. 文档
1. 数组
1. 二进制数据
1. 对象ID
1. 布尔值
1. 时间
1. 正则表达式
1. 最大值`MaxKey`

# 填充因子

（注：因不再使用MMAPv1存储引擎，此部分表述可能不再准确。）

**空间间隔**（padding）。

更新文档时，若文档变大导致原先的位置空间不够，该文档会被移动到另一个位置，此时会修改集合的**填充因子**（padding factor），之后插入的文档都拥有填充因子指定的空间。

# 写入安全

**写入安全**（Write Concern）用于控制写入的安全级别，是否等待数据库响应（写入是否成功）才继续执行。两种最基本的写入安全机制是应答式写入（acknowledged write）和非应答式写入（unacknowledged write）。

# 自然排序

**自然排序**（natural sort）即文档在磁盘中的顺序。

# 固定集合

**固定集合**（capped collection）的大小是固定的，其行为类似循环队列，如果没有空间，最老的文档会被删除，新插入的文档会占据这块空间。固定集合中的文档不能被删除，也不能通过更新改变其大小。固定集合必须在使用之前显式创建，且不能被分片。

# 循环游标

循环游标是一种特殊的游标，当其结果集被取光后游标不会被马上关闭，若有新文档插入时，循环游标会继续取得新文档。如果超过10分钟没有新文档，循环游标就会被释放。由于普通集合不维护文档的插入顺序，循环游标只能用在固定集合上。不能在Mongo Shell中使用循环游标。

# 索引

索引**桶**（bucket）大小为8KB。

索引的本质是一棵树，通常索引比存储的数据量大得多，有很多空闲空间，以便在增加新**入口**（entry）时进行优化。**右平衡索引**（right-balanced index）可将空闲空间减至最少，且只需保留树最右侧的分支在内存中，`_id`索引就是一个典型的右平衡索引。随机分布的索引通常有50%左右的空闲空间。**升序索引**（ascending-order index）有10%的空闲空间。

索引中，不存在的字段和`null`值的字段的存储方式是一样的。

可以对嵌套文档本身建立索引，也可以对嵌套文档的字段建立索引。

对数组建立索引，实际上是对数组的每个元素建立一个索引条目。无法将数组作为一个整体建立索引。一个索引中的数组字段最多只能有一个，这是为了避免在复合索引中索引条目的爆炸性增长。

使用索引需要进行两次查找：一次是查找索引条目，一次是根据索引指针查找相应文档。

可能有几个索引匹配查询，查询优化器从这些索引中选择一个查询计划，所有查询计划是并行执行的，最早返回一定数量结果的是胜者，其他查询计划就会被终止。这个查询计划会被缓存，此查询接下来都使用此查询计划，直到集合数据发生较大变动。建立索引，或执行一定次数的查询后，查询优化器会重新评估查询计划。

默认在前台创建索引，会阻塞所有读写请求直到索引创建完成。在后台创建索引会定期释放写锁，创建索引的过程暂停，因而能同时处理读写请求，但仍会对应用程序性能有较大影响。后台创建索引比前台创建索引慢得多。

索引**基数**（cardinality），即**势**，是集合中字段拥有的不同值的数量。索引基数越高，该字段上的索引就越有用。

建立在多个字段上的索引叫**复合索引**。相互反转（每个字段的方向都乘以-1）的索引是等价的。如果有一个N个字段的复合索引，则同时得到N个由最左前缀的字段组成的**隐式索引**。

当一个索引包含查询需要的所有字段，则没必要获取实际的文档，这个索引覆盖了本次查询，即使用**覆盖索引**（covered index）。如果在一个含有数组的字段上做索引，这个索引永远无法覆盖查询，即使将数组字段从返回的字段中剔除。

如果索引的字段在某个文档中是一个数组，则这个索引被标记为**多键索引**（multikey index）。索引被标记为多键索引后，无法再变成非多键索引，即使这个字段为数组的所有文档都从集合中删除。多键索引可能有多个索引条目指向同一个文档，因此在返回结果集时需要先去除重复的文档。

**唯一索引**可确保集合中每个文档的指定字段都有不同的值。对复合索引，单个字段的值可以相同，但所有字段的组合值必须不同。

**稀疏索引**只对字段存在的文档创建索引条目，字段不存在的文档不创建索引条目。同一个查询，使用稀疏索引与不使用稀疏索引的返回结果可能会不同。

**TTL索引**（time-to-live index），即具有生命周期的索引。如文档此字段为时间类型，则超时后文档会被删除。TTL索引不能是复合索引。TTL索引每隔一分钟定期清理。

**全文本索引**只会对字符串数据进行索引，匹配到的文档按照相关性降序排列。一个集合最多只能有一个全文本索引，但是可以包含多个字段，字段的顺序不重要，会被同等对待。全文本索引可以和非全文本索引组成复合索引。

**地理空间索引**包括2dsphere索引（用于球面地图）和2d索引（用于平面地图和时间连续的地图）。地理空间索引可以和非地理空间索引组成复合索引。2d索引只能对点进行索引。

## 典型场景

* 因为索引中不存在的字段和`null`值的字段的存储方式是一样的，因此：对于非稀疏索引，`{KEY: {$exists: true}}`无法使用索引，`{KEY: {$exists: false}}`可以使用索引但还需遍历对应的所有文档；对于稀疏索引，`{KEY: {$exists: true}}`和`{KEY: {$exists: false}}`都无法使用索引。（注，本人看法：对于稀疏索引，`{KEY: {$exists: false}}`无法使用索引；其他情况下，`$exists`可能使用索引。）
* `{KEY: {$ne: VALUE}}`可以使用索引，但需扫描整个索引，但并不是很有效。
* `{KEY: {$nin: [VALUE <, ...>]}}`无法使用索引。（注：本人看法，可以使用索引，但需扫描整个索引。）
* `{KEY: {$not: CONDITION_DOC}}`大多数都无法使用索引，但能够对`{KEY: {$not: {$lt: VALUE}}}`等范围和正则表达式进行反转来使用索引。
* `{$or: [CONDITION_DOC <, ...>]}`可以使用索引，实际是执行多次查询后将结果集合并并移除重复的文档。
* `{$where: FUNCTION}`无法使用索引。
* 对数组建立的索引不包含下标信息，`{"KEY.INDEX": VALUE}`无法使用索引（注，本人看法：不确定是否可以使用索引。）；但若创建了`{"KEY.INDEX": 1}`的索引，则使用`{"KEY.INDEX": VALUE}`查询相同下标的元素时可使用此索引。

# GridFS

GridFS将大文件分割为多个比较大的块，每个块作为独立的文档进行存储，此外还有一个文档用于将这些块组织在一起并存储文件的元信息。如果要修改GridFS上的文件，只能先将文件删除再重新保存。无法在同一时间对文件的所有块加锁。

# 聚合

**聚合**使用多个管道操作符创建一个管道（pipeline），每个管道操作符接受一连串的文档，对这些文档做一些转换，然后将转换后的文档传递给下一个操作符（对最后一个管道操作符，就是返回结果）。这些管道操作符包括匹配（match）、投射（project）、分组（group）、拆分（unwind）、排序（sort）、限制（limit）、跳过（skip）。

管道如果不是直接从原先的集合中使用数据（投射、分组、拆分可能产生这种情况），就无法在匹配和排序中使用索引。

应尽可能将匹配放在管道的前面，一是可以将不需要的文档过滤掉来减少管道的工作量，二是可以使用索引。

聚合不会记录字段的历史名称，应在使用投射来重命名字段前使用索引。

分组不能使用流式工作方式对文档进行处理，必需要等收到所有文档后，才能对文档分组，然后才能将分组结果发送给管道下一个操作符。这意味着，在分片的情况下，分组会先在每个分片上执行，然后各个分片上的分组结果会发送到mongos再进行最后的统一分组，剩余的管道工作也是在mongos而不是分片上执行。

排序不能使用流式工作方式对文档进行处理，必需要等收到所有文档后，才能对文档排序，然后才能将排序结果发送给管道下一个操作符。这意味着，在分片的情况下，排序会先在每个分片上执行，然后各个分片上的排序结果会发送到mongos再进行最后的统一排序，剩余的管道工作也是在mongos而不是分片上执行。建议在管道的前面进行排序，这时可使用索引，否则可能比较慢并占用大量内存。

如果需要跳过大量的文档，效率会很低，因为必需要先匹配到所有需要跳过的文档，然后才能将其丢弃。

MongoDB不允许单一聚合操作占用过多的系统内存，如果占用了20%以上的内存，就会直接输出错误。

# MapReduce

**MapReduce**需要几个步骤：最开始是映射（map），将操作映射到集合中的每个文档，这个操作要么无作为，要么产生一些键和值；然后洗牌（shuffle），按照键分组，将产生的值组成列表放到对应的键中。化简（reduce）则把列表中的值化简为一个值。这个值被返回，然后接着进行洗牌，直到每个键的列表只有一个值为止，这也就是最终结果。

MapReduce非常慢，不应该用在实时数据分析中。每个传递给map的文档都要先反序列化，从BSON对象转换为JavaScript对象，这个过程非常耗时。

# 副本集

**副本集**（replica set）是一组服务器，其中有一个**主节点**（primary），还有多个**备份节点**（secondary）。副本集最多只能有12个节点，其中只有7个节点有投票权，这是为了减少心跳请求的网络流量和选举花费的时间。即使只有一个服务器，最好也将其设置为只有一个节点的副本集。

备份节点只通过复制功能写入数据，不接受客户端的写入请求。备份节点可能落后于主节点，默认拒绝读请求，防止拿到过期的数据。

许多维护工作不能在备份节点上进行（因为要执行写操作），也不能在主节点上进行。要对该节点进行维护，可以**单机模式**（standalone mode）启动服务。

修改副本集配置时，只能在新配置下可能成为主节点的节点上执行。副本集当前主节点需要先退化为备份节点，关闭所有连接，也即副本集中会暂时没有主节点，配置修改成功后恢复正常。但也可以在新配置下可能成为备份节点的节点上**强制重新配置**（force reconfigure）副本集。

**自动故障转移**（automatic failover）：如果主节点崩溃，备份节点会自动将其中一个节点选举为新的主节点。

**大多数**（majority）即副本集中一半以上的节点。选举主节点时需要由大多数决定；主节点无法得到大多数支持时就会退位成备份节点；写操作被复制到大多数时就是安全的。

**选举**机制：当备份节点无法与主节点连通时，就会请求其他副本集节点将自己选举为主节点。其他节点会做几项检查：其他节点自身能否与主节点连通；候选节点的数据是否最新；有没有其他更高优先级的节点可以被选举为主节点。如果其他节点发现任何原因表明候选节点不应该成为主节点，就会否决此次选举，1张否决票相当于10000张赞成票。如果候选节点得到大多数赞成票，就会成为主节点。如果选举打成平局，每个节点都需要等待30秒才能开始下一次选举。

**仲裁者**（arbiter）的唯一作用是参与选举，其并不保存数据，也不会为客户端提供服务。这是一个永久选项，无法将仲裁者重新配置为非仲裁者，反之亦然。如果可能，尽量使用奇数个数据节点，不要使用仲裁者。

拥有高**优先级**的节点会优先选举为主节点。优先级范围是0至100，默认为1。优先级为0的节点永远不能成为主节点，这样的节点成为**被动成员**（passive member）。

客户端不会向**隐藏成员**发送请求，隐藏成员也不会作为同步源（尽管当其他同步源不可用时隐藏成员也会被使用）。只有优先级为0的节点才能被隐藏。

**延迟备份节点**的数据会比主节点延迟指定的时间。要求优先级是0。如果应用会将读请求路由到备份节点，应该将延迟备份节点设为隐藏成员，以免读请求被路由到延迟备份节点。

备份节点并不需要与主机点拥有相同的索引（甚至可以没有索引）。这是一个永久选项，要求优先级为0。

## 同步

**复制**用于在多台服务器间备份数据。复制使用**操作日志**（operation log）oplog实现，每个节点都可以作为**同步源**（sync source）提供给其他节点使用。oplog是一个固定集合，如果单个动作会影响多个文档，那么每个受影响的文档都会对应oplog中的一条操作。由于复制的过程是先复制数据再写入oplog，所以备份节点可能会在已经同步过的数据上再次执行操作，MongoDB在设计之处就考虑到这种情况：将oplog中的同一个操作执行多次，与只执行一次的效果是一样的。

**复制图谱**（replication graph）。

当一个备份节点从另一个备份节点而不是主节点处复制数据时，就会形成**复制链**。

如果形成复制链，每个备份节点都会将收到的复制请求转发给其同步源，这些转发的请求并不会要求进行数据同步，但主节点就能知道每个备份节点的同步源，这成为**影同步**（ghost syncs）。

**自动复制链**（automatic replication chaining）的缺点：复制链越长，将写操作复制到所有节点所花费的时间就越长。

如果复制链中出现了环，则称为发生**复制循环**。MongoDB根据ping时间选择同步源，会选择离自己比较近而且数据比自己新的节点，因此如果每个节点都自动选取同步源，就不可能出现复制循环。

备份节点从副本集的另一个节点那进行完整的数据复制，这个过程就是**初始化同步**（initial sync）。包括以下步骤：

1. 先做一些准备工作：选择一个节点作为同步源，在local数据库的me集合中为自己创建一个标识符，删除所有已存在的数据库，以全新的状态开始同步。
1. 然后是克隆，将同步源的所有数据复制到本地。这通常是整个过程最耗时的部分。
1. 然后进入oplog同步的第一步，克隆过程中所有操作都会被记录到oplog中。
1. 接下来是oplog同步的第二步，将oplog同步的第一步中的操作应用下来。
1. 到目前为止，本地数据应该与同步源某个时间点的数据完全一致，可以开始创建索引。这个过程可能也会很耗时。
1. 如果此时的数据仍然远远落后于同步源，那么oplog同步的第三步就是将创建索引期间的所有操作全部同步过来。
1. 现在，此节点完成初始化同步，切换到普通同步，可以成为备份节点。

初始化同步过程中，如果第2步（克隆）或第5步（创建索引）耗费了太长时间，导致此节点远远落后于同步源，那么这个节点就是**陈旧的**（stale）。当一个节点陈旧之后，它会查看副本集中其他的节点，如果某个节点的oplog足够详尽，可以用于处理那些落下的操作，就从这个节点进行同步。如果任何一个节点的oplog都没有参考价值，那么此节点的复制就会终止，需要重新进行完全同步或者从备份中恢复。

执行初始化同步时，会强制将当前成员的所有数据分页加载到内存中，这回导致需频繁放文的热数据不能常驻内存，会导致请求变慢。

节点从同步源进行同步，但发现无法找到自己的最后一次操作，这时它就会进行**回滚**（rollback），在二者oplog中找到共同点，然后将本地共同点之后的oplog操作撤销，再进行正常同步。如果要回滚的数据量大于300MB或者要回滚30分钟以上的操作，回滚就会失败。对于回滚失败的节点，必需重新同步。

**滞后**（lag）是指备份节点相对于主节点的落后程度，是主节点最后一次操作的时间戳与备份节点最后一次操作的时间戳的差。

## 心跳

每个节点每隔2秒会向其他所有节点发送一个**心跳请求**（heartbeat request）。心跳会在最多20秒之后超时。

节点的状态包括：

* STARTUP：节点刚启动时处于这个状态。在这个状态下，会尝试加载副本集配置，加载成功后就进入STARTUP2状态。
* STARTUP2：整个初始化同步过程都处于这个状态。在这个状态下，会创建几个线程，用于处理复制和选举，然后切换到RECOVERING状态。
* RECOVERING：**维护模式**（maintenance mode）。这个状态表明节点运转正常，但暂时还不能处理请求。在启动过程中成为备份节点之前，每个节点都要经历此状态。在处理非常耗时的操作时，也可能进入此状态。当一个节点与其他节点脱节时，也会进入此状态（通常来说，这个节点需要重新同步，但节点这时并没有进入错误状态，因为它期望发现一个拥有足够详尽oplog的节点，然后继续同步oplog并回到正常状态）。
* SECONDARY：备份节点。
* PRIMARY：主节点。
* ARBITER：在正常的操作中，仲裁者始终处于此状态。
* DOWN：如果节点变得不可达，就处于此状态。节点可能仍处于正常运行状态，不可达的原因可能是网络问题。
* UNKNOWN：如果一个节点无法到达任何其他节点，其他节点也就无法知道它处于什么状态，会将其报告为此状态。通常表明这个节点挂掉了，或者存在网络访问问题。
* REMOVED：当节点被移出副本集时，就处于此状态。
* ROLLBACK：如果节点正在进行数据回滚，就处于此状态。回滚过程结束时，节点会转换为RECOVERING状态，然后成为备份节点。
* FATAL：如果节点发生了不可换回的错误，也不再尝试恢复正常，它就处于此状态。

## 驱动程序

连接到副本集时，驱动程序使用希望连接到的副本集**种子**（seed）列表。种子是副本集节点。并不需要将所有节点都列出来（虽然可以这样做），驱动程序连接到某个种子服务器后，就能够得到其他节点的地址。

可以设置驱动程序的**读取首选项**（read preferences）：

* Nearest：将读请求路由到延迟最低的节点（根据驱动程序到副本集节点的ping时间）。
* Primary：始终将读请求发送到主节点。如果没有主节点，请求会出错。默认设置。
* Primary preferred：主节点优先。失去主节点时，应用程序进入只读状态，将读请求发送至备份节点。
* Secondary：始终将读请求发送至备份节点。如果没有可用的备份节点，请求会出错。
* Secondary preferred：优先将读请求路由到可用的备份节点。如果备份节点都不可用，请求会被发送到主节点。

将读请求发送到备份节点通常不是一个好主意：对一致性要求非常高的应用程序不应该从备份节点读取数据；更好的选择是使用分片做分布式负载。

# 分片

**分片**（sharding）是指将数据拆分，分散存储在不同机器上的过程。有时也用**分区**（partitioning）来表示。

**手动分片**（manual sharding）：应用程序需要维护与若干不同数据库服务器的连接，每个连接是完全独立的，应用程序管理不同服务器上不同数据的存储，还管理在合适的数据库上查询数据的工作，非常难以维护。

MongoDB支持**自动分片**（auto sharding），可以使数据库架构对应用程序不可见，应用程序只需要连接到路由服务器（mongos），就可以像使用单机服务器一样进行请求。每个分片对请求的响应都会发送给路由服务器，路由服务器将所有响应合并在一起，返回给应用程序。

**主分片**（primary shard）保存数据库中没有被分片的集合的数据。

**片键**（shard key）是集合的一个键，根据这个键拆分数据。只有被索引过的键才能作为片键。

使用**复合片键**（compound shard key）可实现多热点技术。

包含片键的查询能直接被发送到目标分片或者是集群分片的一个子集，这样的查询叫做**定向查询**（targeted query）。如果查询必需被发送到所有分片，这样的查询叫做**分散-聚集查询**（scatter-gather query）。

拆分数据常用的数据分发方式有三种：

* **升序片键**（ascending key）：片键会随着时间稳定增长。会导致所有的写入、拆分、均衡都是在同一个分片。如果没有高性能服务器处理插入流水，活没有使用标签，那么不要使用升序片键。
* **随机分发的片键**（hashed shard key）：片键没有固定的规律。随机访问数据时效率不高，无法使用片键做范围查询，不能使用unique选项，浮点型会先取整然后再散列。使用随机片键在集群上分配负载是非常好的。
* **基于位置的片键**（location-based key）：位置不必与实际的物理位置相关，数据会根据位置分组，位置比较接近的文档会被保存在同一个块中。

片键不可以是数组。文档一旦插入，其片键的值就无法修改。大多数特殊类型的索引都不能用作片键，特别是不能在地理空间索引上进行分片。

**配置服务器**保存集群的配置信息：集群中有哪些分片、分片的是哪些集合、数据块的分布。配置服务器的1KB空间约可表示200MB数据。

MongoDB将文档分组为**块**（chunk），每个块由片键特定范围内的文档组成，具有相同片键的文档保存在相同的块中。

**块范围**是一个左闭右开的区间。含有`$maxKey`的块叫**最大块**（max chunk）。块大小的最大值默认为64MB。

块与块之间叫**拆分点**（split point）。当块大小增长到某个阈值，就会检查是否需要对块进行拆分，如需拆分，则在配置服务器更新块的元信息，不用进行数据移动。即使块较大，有时也可能找不到可用的拆分点。

mongos不断重复发起拆分请求却无法拆分的过程叫做**拆分风暴**（split storm）。防止拆分风暴的唯一方法是保证配置服务器可用。

每个mongos各自维护写入计数器计算自身收到的写请求是否到达拆分阈值，尽可能保证mongos可用，而不是在需要时开启不需要时停止。

每个mongos都有可能周期性地变身为**均衡器**（balancer），检查分块表查看是否有分片到达了**均衡阈值**（balancing threshold），即一个分片明显比其他分片有更多的块（只使用块数量，而非数据大小）。如有则对整个集群加锁，防止配置服务器对集群进行修改，然后进行块**迁移**（migration）；如无则不再充当均衡器。均衡并不会影响路由。

当from分片收到mongos发来的迁移命令时，会执行以下步骤：

1. 检查命令的参数。
1. 向配置服务器申请获得一个分布式锁，以便进入迁移过程。
1. 尝试连接到to分片。
1. 数据复制，这是整个过程的临界区。from分片与to分片是直接通信的。
1. 与to分片和配置服务器一起确认迁移是否成功完成。

当to分片收到from分片发来的迁移命令时，会执行以下步骤：

1. 迁移索引。如to分片已有迁移集合的块，则忽略此步骤。
1. to分片删除块范围内已存在的所有文档。
1. 将from分片的块中所有文档复制到to分片。
1. 复制期间，在to分片上重新执行曾在这些文档上执行过的操作。
1. 等待to分片将迁移过来的数据复制到集群的大多数节点上。
1. 修改块的元数据以完成迁移过程，表明数据已被成功迁移至to分片。

在执行几乎所有数据库管理操作之前，都应先关闭均衡器。

不可拆分（因所有文档的片键值一样）和不可迁移（因块大小超出最大值）的块叫**特大块**（jumbo chunk）。

必需明确指定数据库和集合，不会自动对数据进行拆分。通常不必太早分片，因为分片不仅会增加部署复杂度，还要求做出设计决策，而该决策以后很难再改变。另外，最好也不要在系统运行太久之后再分片，因为在一个过载的系统上不停机进行分片是非常困难的。

至少应该创建3个或以上的分片。随着分片数量不断增加，系统性能大致会呈线性增长。但是如果从一个未分片的系统转换为只有几个分片的系统，性能通常会有所下降。

通常来说，不应从集群中删除分片。删除分片时，在**排出数据**（draining）的过程中，均衡器会负责将待删除分片的数据迁移至其他分片，块在移动前可能被拆分。

分片时，admin数据库会被保存在配置服务器，因此分片中的节点并不知道admin数据库的存在。

当使用副本集作为分片时，要修改副本集，需直接连接到副本集的主节点而不是通过mongos，然后对副本集进行重新配置，配置服务器会自动检测更改。

可以启动任意数量的mongos进程。通常是每个应用服务器使用一个mongos进程，可与应用服务器运行在同一台机器上。

# 连接

分片创建的连接数量有：

* mongos进程数量*3：每个mongos会为每个mongod创建3个连接。一个用于转发客户端请求；一个用于追踪错误信息，即**写回监听器**（writeback listener）；一个用于监控副本集状态。
* 每个副本集的节点数量*3：主节点会与每个备份节点创建1个连接，每个备份节点会与主节点创建2个连接。
* 其他进程数量：指其他连接到mongod的进程数量，包括MMS代理、shell连接、迁移时连接到其他分片的连接。

网络的连接情况如下：

* 单机服务器：
	* 客户端需与mongod连接。
* 副本集：
	* 客户端需与所有可见的非仲裁者节点连接。
	* 副本集节点需与其他所有节点连接。
* 分片：
	* 客户端需与mongos连接。
	* mongos需与所有分片及配置服务器连接。
	* 分片需与其他所有分片和配置服务器连接。

# 身份验证

每个数据库都可以拥有任意个用户。admin和local是两个特殊的数据库，当中的用户为超级用户，可对所有数据库进行操作，并能执行管理员命令。

驱动程序如使用连接池或需切换到另一个节点，则需在新连接重新进行身份验证。这一操作应由驱动程序自动处理。

# 系统分析器

**系统分析器**（system profiler）会导致mongod的整体性能有所下降。系统分析器默认处于关闭状态。

# 诊断日志

**诊断日志**（diaglog，diagnostic log）用来记录和回放操作流水。启用诊断日志会造成性能损失。

# 日记系统

**日记系统**（journaling）：MongoDB会在进行写入时建立一条**日记**（journal），日记中包含此次写入操作更改的磁盘地址和字节。因此，一旦服务器突然停机，可在启动时对日记进行重放，重新执行那些停机前没能刷新到磁盘的写入操作。

脏页默认每60秒刷新到磁盘一次，因此日记文件只需记录约60秒的写入数据。

默认每隔100毫秒，或写入数据到达若干字节时，便会将这些操作写入日记文件。

日记系统会影响写入速度。对于所有生产环境都推荐使用日记系统。

# BSON

**BSON**（Binary JSON），即二进制JSON，是一种轻量的二进制格式，用一串字节来描述任何MongoDB文档，是文档存放于磁盘中的格式，也是服务器与客户端间传输的格式。

BSON的优点有：

* 高效：无需占用过多额外空间。最坏的情况下效率比JSON低一点，最好的情况下（如存储二进制信息）效率比JSON高很多。
* 可遍历性：BSON以空间效率为代价（如添加前缀表示字符串长度而不是依赖末尾的终止符），使自身更容易被遍历。这一特性在对文档进行**内省**（introspect）时很实用。
* 高性能：可快速进行编码和解码。

# 线路协议

驱动程序使用轻量的TCP/IP**线路协议**（wire protocol）来访问服务器，其基本上就是对BSON数据进行简单的包装。

# 数据文件

**预分配**（preallocated）文件：每个数据库内应一致存在一个填充为0的空文件，该文件被写入数据后，下一个空文件会被预分配。

在数据文件中，数据库被按照**命名空间**（namespace）进行组织，每个命名空间中存放有特定集合的数据。每个命名空间中的数据在磁盘上被分为几组数据，即**区段**（extent）。每个命名空间可拥有几个不同的区段，这几个区段在磁盘上不一定是连续的。

修复实质上是删除所有受损数据。修复过程会将所有输出都写入新文件中，不会对原有文件进行修改。

# 内存映射存储引擎

MongoDB使用内存映射存储引擎，服务器启动时其内存对所有数据文件进行映射，接下来就由操作系统负责将数据刷新到磁盘，以及管理内存中的数据页交换。因此在32位系统最多只能存储2GB数据。

**工作集**（working set）是能覆盖大多数请求的核心数据集。

MongoDB使用的内存类型有：

* **常驻内存**（resident memory）：MongoDB在物理内存中明确拥有的内存部分。
* **映射内存**（mapped memory）：MongoDB赋予了地址的数据页面，通常约等于整个工作集的大小。MongoDB赋予页面一个地址，此地址并非物理内存中的真实地址，而是一个虚拟地址。MongoDB向操作系统内核请求内存，内核会在它的**页缓存**（page cache）中进行查找，查找失败会产生一个**缺页中断**（page fault），将此页数据从磁盘复制至内存。
* **虚拟内存**：MongoDB为映射内存的每个页面，都额外维护一个虚拟地址，以供日记系统使用（这不意味内存中有两份同样的数据，有的只是两个地址）。所以MongoDB使用的虚拟内存总量，约等于映射内存的两倍。

访问不在内存中的索引条目通常造成两次缺页中断，分别发生在将索引条目和文档加载如内存时。查询索引造成缺页中断称为**索引树脱靶**（btree miss），无需访问磁盘即可查询索引成为**索引树中靶**（btree hits）。

**IO延迟**指CPU闲置等待磁盘响应的时间，通常于缺页中断密切相关。

**后台刷新平均时间**（background flush average）是指将**脏页**（dirty page）写入磁盘所花费的时间。通常反映写入负载的大小，因此如果写入负载很低，则可能无法表现出磁盘的压力大小。

**锁比例**（lock percdntage）指MongoDB处于锁定中的时间。锁比例过高的最普遍原因之一是缺少合适的索引。

# MMS

**MMS**（Mongo Monitoring Service）即Mongo监控服务。

# 操作系统

标准的部署方案是使用较少的内存空间和较大的机械磁盘空间，但需注意内存空间应大于工作集大小。

如副本集中存在机械硬盘，通常不能添加固态硬盘。如使用固态硬盘的节点成为主节点，并接管它能处理的一切工作，其他节点受速度所限无法及时复制数据，从而被落后。因此如要引入固态硬盘，向集群中添加一个新的分片是更好的方法。

用机械硬盘记录日记，用固态硬盘记录数据，这样既能节省固态硬盘的空间，也不会影响性能。

不要使用RAID5，MongoDB进行的是多次少量的写入工作，会非常慢。

如需在内存和CPU间选择一个进行硬件投资，一定要选择内存。如需在CPU速度和核数间做出选择，应选择速度。

64位Linux是运行MongoDB的最好选择，64位Windows也能很好地运行。

MongoDB只支持小端。

应分配一小块交换空间，以防系统内存使用过多而导致内核的**内存溢出杀手**（OOM killer，out-of-memory killer）终止MongoDB的运行。MongoDB通常不会使用交换空间，但在需要对数据进行排序，即建立索引或进行排序操作时，会使用交换空间。

在Linux上，推荐使用ext4或XFS文件系统。具有一个能在备份时进行**文件系统快照**（filesystem snapshot）的文件系统是不错的，但是会影响到性能。不推荐使用ext3文件系统，因为它在预分配数据文件时耗时过长。在Windows上使用NTFS和FAT文件系统都是可以的。

**虚拟化**（virtualization）存在缺点，尤其是无法预知的网络和磁盘IO状况。

**NUMA**（Non-uniform Memory Architecture），即**非一致内存结构**，面对数据库尤其是MongoDB时，表现非常糟糕。

**预读**（readahead）即操作系统从磁盘中读取笔实际请求更多的数据。推荐预读16至256个扇区。

操作系统以**页面**为单位在磁盘和内存间转移数据（x86架构默认为4096字节）。启用**大内存页面**（hugepage）导致的问题和预读过多内容导致的问题类似，一般不要启用这一特性。

磁盘控制器从操作系统收到请求后，会使用一种**磁盘调度算法**来决定处理这些请求的顺序。截止时间（deadline）调度算法和完全公平队列（CFQ，completely fair queueing）是不错的选择。有时noop（这是最简单的调度算法）调度算法是最好的选择，如在虚拟化环境中、在固态硬盘上、使用RAID控制器进行缓存时。

由于MongoDB访问数据文件十分频繁，如禁止记录最后访问时间（atime）则会得到性能的提升。

MongoDB可能会受到两个限制的影响，通常应设为无限制（包括软限制和硬限制）：

* 进程可建立线程的数量。
* 进程能够打开**文件描述符**（file descriptor）的数量

一般各节点间的时钟误差不超过1秒是最安全的。副本集能处理几乎所有的**时钟偏移**（clock skew），分片则能处理一部分时钟偏移。

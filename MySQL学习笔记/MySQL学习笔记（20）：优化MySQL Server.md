本文更新于2020-04-05，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

# MySQL体系结构

MySQL实例由一组后台线程、一些内存块和若干服务线程组成。

后台线程包括：

* 主线程：主要负责将脏缓存页刷新到数据文件，执行purge操作，触发检查点，合并插入缓冲区等。
* IO线程：
	* insert buffer线程：主要负责插入缓冲区的合并操作。
	* read线程：负责数据库读操作，可配置多个读线程。
	* write线程：负责数据库写操作，可配置多个写线程。
	* log线程：将重做日志刷新到logfile中。
* 锁线程：负责锁控制和死锁检测。
* 错误监控线程：主要负责错误监控和错误处理。
* purge线程：MySQL5.5之后用单独的线程执行purge操作。

可通过`SHOW ENGINE INNODB STATUS`查看线程的状态。

# MySQL内存优化

内存优化原则：

* 将尽量多的内存分配给MySQL做缓存，但要给操作系统和其他应用程序的运行预留足够的内存，否则如果产生SWAP页交换，将严重影响系统性能。
* MyISAM的数据文件读取依赖于操作系统自身的IO缓存，因此，如果有MyISAM表，就要预留更多的内存给操作系统做IO缓存。
* 排序区、连接区等缓存是分配给每个数据库会话专用的，其默认值的设置要根据最大连接数合理分配，如果设置太大，不但浪费内存资源，而且在并发连接较高时会导致物理内存耗尽。

## MyISAM内存优化

MyISAM存储引擎使用索引缓存（key buffer）缓存索引块，对于数据块没有特别的缓存机制，完全依赖于操作系统的IO缓存。

### key_buffer_size设置

key_buffer_size决定MyISAM索引缓存区的大小，它直接影响MyISAM表的存取效率。建议至少分配1/4可用物理内存。

通过检查系统状态变量可评估MyISAM缓存的效率：

* 读比率：key_reads/key_read_requests，一般应小于0.01。
* 写比率：key_writes/key_write_requests，对于更新和删除特别多的应用可能接近1，对于每次更新很多行的应用就会比较小。
* 使用率：1-(key_blocks_unused*key_cache_block_size/key_buffer_size)，一般在0.8左右比较合适。

### 使用多索引缓存

多索引缓存的机制，可以将不同表的索引缓存放到不同的key buffer中，减少session间对key buffer的竞争导致热数据被淘汰。

创建新的索引缓存（keybuffername为新建的缓存名，下同）：

```sql
SET GLOBAL keybuffername.key_buffer_size = n
```

删除索引缓存：

```sql
SET GLOBAL keybuffername.key_buffer_size = 0
```

指定表的索引缓存（不指定则使用默认索引缓存）：

```sql
CACHE INDEX tablename[, ...] IN keybuffername
```

索引预加载：

```sql
LOAD INDEX INTO CACHE tablename[, ...]
```

### 调整中点插入策略

MySQL默认使用LRU（Last Recently Used）策略来选择要淘汰的索引数据块，可使用中点插入策略（Midpoint Insertion Strategy）来优化索引块淘汰算法。其将LRU链被分为hot子表和warm子表两部分，能避免偶尔被访问的索引块将访问频繁的热块淘汰。

可以通过设置key_cache_division_limit来控制多大比例的缓存用做warm子表，默认值为100，也就是不启用中点插入策略。
还可以通过key_cache_age_threshold设置数据块由hot子表向warm子表降级的时间。对于有N个块的索引缓存来说，如果一个在hot子表头部的索引块在最后N*key_cache_age_threshold/100次缓存命中未被访问过，就会被降级到warm子表。

### 调整read_buffer_size和read_rnd_buffer_size

如果需要经常顺序扫描MyISAM表，可以增大read_buffer_size。如果需要做排序的查询（如`ORDER BY`），可以增大read_rnd_buffer_size。但需注意，二者都是按session分配的。

## InnoDB内存优化

InnoDB缓存池（buffer pool）不仅用来缓存索引块，也用来缓存数据块。

InnoDB缓存池逻辑上由空闲缓存块列表（free list）、需要刷新到磁盘的缓存块列表（flush list）和正在使用的缓存块列表（LRU list）组成。

InnoDB使用的LRU算法与MyISAM的中点插入策略LRU算法类似，其将LRU list分为young sublist和old sublist。页的刷新存在于flush list和LRU list中，从LRU list淘汰的数据页会立刻放到free list中。

### innodb_buffer_pool_size设置

innodb_buffer_pool_size决定InnoDB存储引擎表数据和索引数据的最大缓存池大小。在专用数据库服务器上，可分配80%的物理内存。

可通过`SHOW STATUS LIKE 'innodb_buffer_pool%'`查看缓存池的使用情况。

InnoDB缓存池命中率：1-innodb_buffer_pool_reads/innodb_buffer_pool_read_request。

### 调整old sublist大小

old sublist的比例由innodb_old_blocks_pct决定，其取值范围为5-95，默认为37。

### innodb_old_blocks_time设置

innodb_old_blocks_time决定了缓存数据块从old sublist转移到young sublist的快慢，单位为毫秒。

### 调整缓存池数量，减少内部对缓存池数据结构的争用

InnoDB会将innodb_buffer_pool_size指定大小的缓存平分给innodb_buffer_pool_instances个buffer pool。

### 控制缓存池刷新，延长数据缓存时间，减少磁盘IO

在InnoDB找不到干净的可用缓存页或检查点被触发等情况下，后台线程就会开始把脏的缓存页回写到磁盘文件中，这个过程叫缓存刷新。

缓存刷新的快慢主要取决于两个参数：

* innodb_max_dirty_pages_pct：控制缓存池中脏页的最大比例，默认为75%。
* innodb_io_capacity：代表磁盘的IO能力，决定一批刷新脏页的数量，默认为200。

### InnoDB双写策略

在进行脏页刷新时，InnoDB采用双写（doublewrite）策略，首先将脏页的副本写到系统表空间的doublewrite buffer，原因是：MySQL的数据页大小（一般是16K）与操作系统的IO数据页大小（一般是4K）不一致，无法保证缓存页被完整、一致地刷新到磁盘。由于同步到doublewrite buffer是对连续磁盘空间的顺序写，因此开启双写对性能的影响并不太大。

可通过`SHOW VARIABLES LIKE '%doublewrite%'`查看双写是否开启。

## 调整用户服务线程排序缓存区

如果通过`SHOW GLOBAL STATUS`看到sort_merge_passes的值很大，可以增大sort_buffer_size来增大排序缓存区。
对于无法通过索引进行连接操作的查询，可以增大join_buffer_size。
不过需注意，二者的缓存区都是面向服务线程分配的。如果是多表关联的复杂查询，还可能会分配多个表连接缓存区。

## 使用查询缓存

查询缓存存储`SELECT`查询的文本及相应结果，如果随后收到一个相同的查询，直接从查询缓存中得到结果，而不再需要解析和查询。

可以通过`SHOW VARIABLES LIKE '%query_cache%'`可以查看缓存相关的参数，通过`SHOW STATUS LIKE '%Qcache%'`可以查看查询缓存的使用情况。

# InnoDB日志优化

当更新数据时，InnoDB内部的操作流程大致是：

1. 将数据读入缓存池，并对相关记录加独占锁。
1. 将UNDO信息写入undo表空间的回滚段中。
1. 更新缓存页中的数据，并将更新记录写入重做日志缓存池（另一个缓存池redo buffer）中。
1. 提交时，根据innodb_flush_log_at_trx_commit的设置，用不同的方式将重做日志缓存池中的更新记录刷新到重做日志文件中，然后释放独占锁。
1. 后台IO线程根据需要择机将缓存中更新过的数据刷新到磁盘文件中。

LSN（Log Sequence Number）称为日志序列号，实际上对应日志文件的偏移量。生成公式为：新的LSN=旧的LSN+写入的日志大小。

## innodb_flush_log_at_trx_commit设置

控制将redo buffer中的更新记录写入到日志文件以及将日志文件数据刷新到磁盘的操作时机。

* 值为0：在事务提交时，不会立即将缓存中的redo日志写到磁盘文件，而是每秒触发一次，并调用操作系统fsync刷新IO缓存。如果数据库崩溃，数据就会丢失。
* 值为1（默认值）：事务提交时，立即将缓存中的redo日志回写到磁盘文件，并调用操作系统fsync刷新IO缓存。
* 值为2：事务提交时，立即将缓存中的redo日志回写到磁盘文件，但并不马上调用fsync刷新IO缓存，而是每秒触发一次。如果数据库崩溃，只要操作系统没有崩溃，数据就不会丢失。

## 设置innodb_log_file_size，控制检查点

当一个日志文件写满后，InnoDB会自动切换到另一个日志文件，但切换时会触发数据库检查点（checkpoint），这将导致InnoDB缓存脏页的小批量刷新，会明显降低InnoDB的性能。

一般来说，平均每半小时写满一个日志文件比较合适。

## innodb_log_buffer_size设置

innodb_log_buffer_size决定InnoDB重做日志缓存池的大小，默认是8MB。

# 调整MySQL并发相关的参数

## max_connections设置

max_connections控制允许连接到MySQL数据库的最大数量，默认是151。

如果状态变量connection_errors_max_connections不为0且一直增长，说明不断有连接请求因数据库连接数已达到最大允许值而失败。

## open_files_limit设置

因数据库连接也要占用文件描述符，也需注意open_files_limit是否足够。

## back_log设置

back_log控制MySQL监听TCP端口时的积压请求栈大小。MySQL 5.6.6以后默认为50+(max_connections/5)，但最大不超过900。

## table_open_cache设置

table_open_cache控制所有SQL执行线程可打开的表缓存数量。该值应设置为：max_connections*N，N为每个连接执行关联查询时所涉及到的表的最大个数。

## thread_cache_size设置

thread_cache_size控制MySQL缓存可供重用的客户服务线程的数量。

可以通过线程cache的失效率threads_created/connections来衡量tread_cache_size的设置是否合适。

## innodb_lock_wait_timeout设置

innodb_lock_wait_timeout可以控制InnoDB事务等待行锁的时间，默认为50ms。

本文更新于2020-05-30，使用MySQL 5.7，操作系统为Deepin 15.4。

# 配置文件

Windows下配置文件的读取顺序：

1. %WINDIR%/my.ini
1. C:/my.cnf
1. INSTALL_DIR/my.ini，INSTALL_DIR为实际的安装目录
1. --defaults-extra-file=xxx指定的文件

Linux下配置文件的读取顺序：

1. /etc/my.cnf
1. INSTALL_DIR/my.cnf，INSTALL_DIR为实际的安装目录
1. --defaults-extra-file=xxx指定的文件
1. ~/.my.cnf

配置文件内容分为多个组：

* [mysql]：配置mysql客户端工具。如default-character-set可配置默认客户端、连接、返回结果字符集。
* [mysqld]：配置mysqld服务器。
* [mysqldump]：配置mysqldump工具。

# 系统变量

关于系统变量的详情参阅官方文档：[https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)。

关于InnoDB系统变量的详情参阅官方文档：[https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html)。

MySQL服务器可使用以下方式配置，各种方式的参数名相似：

* 系统变量：可使用`SHOW VARIABLES [LIKE 'name']`或`SELECT @@name`查看，使用`SET [GLOBAL|SESSION] [@@]name = value`设置。参数名使用“_”分隔单词。
* 命令行参数：即mysqld的命令行参数。参数名加上“--”前缀，使用“-”分隔单词。可通过`mysqld --help --verbose`可查看命令行参数的使用及从当前配置读取的系统变量值。
* 配置文件：配置文件的[mysqld]组，参数名使用“-”或“_”分隔单词。

参数值如为布尔类型，设置为0、OFF为假，1、ON为真。命令行参数和配置文件，不指定值时表示真。

| 系统变量             | 命令行参数          | 配置文件            | 说明                                                 |
| ------------------ | ------------------ | ------------------ | ---------------------------------------------------- |
| autocommit         |                    |                    | 是否自动提交事务                                        |
| back_log           |                    |                    | TCP监听端口新连接的等待队列大小                           |
|                    | --binlog-do-db=    |                    | 只有指定的库才记录二进制日志，可指定多次                     |
| binlog_format      | --binlog-format=   |                    | 二进制日志格式，，可为STATEMENT、ROW、MIXED                  |
|                    | --binlog-ignore-db= |                   | 只有指定的库才不记录二进制日志，可指定多次                   |
| bulk_insert_buffer_size |               |                    | 每个线程MyISAM批量插入的缓存区大小                        |
| character_set_client     |              |                    | 客户端字符集                                           |
| character_set_connection |              |                    | 连接字符集，字符串常量的字符集也由此指定                     |
| character_set_database   |              |                    | 默认库的字符集                                          |
| character_set_results    |              |                    | 返回给客户端的结果字符集                                 |
| character_set_server     |              |                    | 服务器字符集                                           |
| collation_database |                    |                    | 默认库的字符集校对规则                                   |
| collation_server   |                    |                    | 服务器字符集校对规则                                     |
| concurrent_insert  |                    |                    | MyISAM表并发插入规则。NEVER或0不允许并发插入；AUTO或1当MyISAM表没有空洞时，允许一个线程读表的同时另一个线程从表尾插入；ALWAYS或2无论表有无空洞，都允许在表尾并发插入 |
| datadir            |                    |                    | 数据目录。默认为/var/lib/mysql/                          |
| end_markers_in_json |                   |                    | 设置trace分析优化器以JSON格式输出                         |
| event_scheduler    |                    |                    | 是否打开事件调度器                                       |
| expire_logs_days   | --expire-logs-days= |                   | 二进制日志过期天数                                       |
| foreign_key_checks |                    |                    | 是否进行外键检查                                        |
|                    | --general-log      |                    | 是否启用查询日志                                        |
|                    | --general-log-file= |                   | 查询日志文件名，默认为“主机名.log”，默认保存在datadir指定的目录中 |
| have_query_cache   |                    |                    | 服务器在安装时是否已经配置高速缓存                         |
| init_file          |                    |                    | 服务器启动后执行的SQL文件                                |
| innodb_buffer_pool_instances |          |                    | InnoDB缓存池实例数，每个实例平分缓存池的大小                 |
| innodb_buffer_pool_size    |            |                    | InnoDB表数据和索引数据的最大缓存池大小                     |
| innodb_data_file_path      |            |                    | InnoDB共享表空间文件，格式为file_name:file_size[:autoextend[:max:max_file_size]]，可以“;”分隔 |
| innodb_data_home_dir       |            |                    | InnoDB共享表空间目录                                    |
| innodb_doublewrite         |            |                    | InnoDB是否开启双写策略                                   |
| innodb_file_per_table      |            |                    | InnoDB是否使用多表空间的存储方式                          |
| innodb_flush_log_at_trx_commit |        |                    | InnoDB事务提交时刷新日志的规则。0在事务提交时，不会立即将缓存中的redo日志写到磁盘文件，而是每秒触发一次，并调用操作系统fsync刷新IO缓存，若数据库崩溃，数据就会丢失；1在事务提交时，立即将缓存中的redo日志回写到磁盘文件，并调用操作系统fsync刷新IO缓存；2在事务提交时，立即将缓存中的redo日志回写到磁盘文件，但并不马上调用fsync刷新IO缓存，而是每秒触发一次，若数据库崩溃，只要操作系统没有崩溃，数据就不会丢失 |
| innodb_io_capacity         |            |                    | InnoDB后台任务每秒的IO次数，包括刷新缓存池脏页等              |
| innodb_lock_wait_timeout   |            |                    | InnoDB事务等待行锁的超时时间                              |
| innodb_locks_unsafe_for_binlog |        |                    | InnoDB是否在执行`INSERT INTO ... SELECT ...`和`CREATE TABLE ... SELECT ...`时对源表记录加锁，如否则可能无法使用binlog正确恢复或复制数据 |
| innodb_log_buffer_size     |            |                    | InnoDB日志缓存池大小                                     |
| innodb_log_file_size       |            |                    | InnoDB日志文件大小                                       |
| innodb_lru_scan_depth      |            |                    | InnoDB缓存池无可用数据页时扫描LRU list尾部数据页的个数。其后进行相关磁盘刷新操作，淘汰的页放入free list中 |
| innodb_max_dirty_pages_pct |            |                    | InnoDB缓存池中脏页的最大比例                              |
| innodb_old_blocks_pct      |            |                    | InnoDB缓存池LRU list中old sublist的比例                  |
| innodb_old_blocks_time     |            |                    | InnoDB缓存池LRU list中数据块从old sublist转移到new sublist的毫秒数 |
| innodb_table_locks         |            |                    | InnoDB能否感知MySQL Server加的表级锁，同时MySQL Server能否感知InnoDB加的行级锁。能感知才能自动识别涉及表级锁的死锁 |
| join_buffer_size   |                    |                    | 每个线程的表连接缓存区大小                                 |
| key_cache_age_threshold  |              |                    | MyISAM索引缓存块由hot子表向warm子表降级的时间。对于有N个块的索引缓存来说，如果一个在hot子表头部的索引块在最后N*key_cache_age_threshold/100次缓存命中未被访问过，就会被降级到warm子表 |
| key_cache_block_size     |              |                    | MyISAM索引缓存块大小                                     |
| key_cache_division_limit |              |                    | MyISAM索引缓存用做warm子表的比例。默认值为100，亦即不启用中点插入策略 |
| key_buffer_size          |              |                    | MyISAM索引缓存区的大小。可使用<bufferName>.key_buffer_size使用多索引缓存机制 |
|                   | --local-infile      |                    | 是否允许`LOAD DATA LOCAL INFILE`从客户端本地读取文件       |
| log_bin           | --log-bin[=]        |                    | 是否开启二进制日志。命令行参数可指定文件名，，默认为“主机名-bin”，默认保存在datadir指定的目录中 |
| log_bin_basename  |                     |                    | 二进制日志文件名（不带目录，实际文件名会追加形如.000001的编号）    |
| log_bin_index     |                     |                    | 二进制日志文件的索引文件                                   |
| log_error         | --log-error[=]      |                    | 错误日志文件名，默认为“主机名.err”，默认保存在datadir指定的目录中 |
|                   | --log-output=       |                    | 查询日志和慢查询日志的保存方式。NONE不保存，其优先级最高；TABLE保存至表，查询日志保存至mysql库general_log表，慢查询日志保存至msql库slow_log表；FILE保存至文件，为默认值。可使用“,”分隔 |
|                   | --log-queries-not-using-indexes |        | 是否将不使用索引的查询写入慢查询日志                         |
|                   | --log-slave-updates             |        | 从库是否写二进制日志。如果从库同时也作为主库，则需打开这个选项，还需指定--log-bin |
|                   | --log-slow-admin-statements     |        | 是否将执行慢的管理语句写入慢查询日志                         |
| long_query_time   |                     |                    | 执行时间（不计入获得锁的时间）超过该值的查询将写入慢查询日志       |
| low_priority_updates   |                |                    | 是否使MyISAM存储引擎的写请求比读请求优先级更低                |
| lower_case_table_names |                |                    | 表名大小写规则。0磁盘存储的表名区分大小写，比较也区分大小写，Linux的默认值；1磁盘存储的表名为小写，比较不区分大小写，Windows的默认值；2磁盘存储的表名区分大小写，比较不区分大小写，Mac的默认值 |
|                        | --master-connect-retry= |           | 从库在与主库的连接丢失时重试的时间间隔                        |
| max_connections        |                |                    | MySQL数据库的最大连接数                                    |
| max_heap_table_size    |                |                    | Memory存储引擎最大的表大小                                 |
| max_length_for_sort_data |              |                    | 对filesort，如查询到的字段总大小小于该值，则使用一次扫描算法，否则使用两次扫描算法 |
| max_write_lock_count   |                |                    | 表级锁获取写锁的最大次数，此后会允许获取一次读锁                 |
| min_examined_row_limit |                |                    | 扫描记录数不少于该值的查询将写入慢查询日志                     |
| open_files_limit       |                |                    | 打开的文件描述符最大数量                                    |
| optimizer_trace        |                |                    | 优化器trace设置                                           |
| optimizer_trace_max_size |              |                    | 化器trace使用的最大内存大小                                 |
| profiling              |                |                    | 是否打开profiling                                         |
| query_cahce_size       |                |                    | 查询缓存大小                                               |
| query_cache_type       |                |                    | 查询缓存使用规则。0或OFF关闭查询缓存；1或ON打开查询缓存（使用SQL_NO_CACHE除外）；2或DEMAND只缓存使用SQL_CACHE的查询 |
| read_buffer_size       |                |                    | MyISAM表顺序读取的缓存大小，为每个线程独占                      |
|                        | --read-only    |                    | 从库是否只接受root的更新操作                                 |
| read_rnd_buffer_size   |                |                    | MyISAM表随机读取的缓存大小，为每个线程独占                      |
|                        | --replicate-do-db=             |    | 从库复制的库                                               |
|                        | --replicate-do-table=          |    | 从库复制的表                                               |
|                        | --replicate-ignore-db=         |    | 从库忽略复制的库                                            |
|                        | --replicate-ignore-table=      |    | 从库忽略复制的表                                            |
|                        | --replicate-wild-go-table=     |    | 以通配符方式指定从库复制的表                                  |
|                        | --replicate-wild-ignore-table= |    | 以通配符方式指定从库忽略复制的表                               |
|                        | --safe-user-create   |              | 是否不能用`GRANT`创建新用户，除非用户有mysql库user表的`INSERT`权限 |
|                        | --skip-grant-tables  |              | 是否令服务器跳过权限系统的使用                                |
|                        | --skip-networking    |              | 是否不允许TCP/IP连接，而必须使用命名管道、共享内存、UNIX套接字      |
|                        | --skip-show-database |              | 是否有`SHOW DATABASES`权限的用户才能执行且显示所有库名；否则所有用户都能执行，但只显示有权限的库名 |
|                        | --slave-skip-errors= |              | 复制过程中从库可以自动跳过的错误号                            |
| slow_query_log         | --slow-query-log     |              | 是否启用慢查询日志                                          |
| slow_query_log_file    | --slow-query-log-file[=] |          | 慢查询日志文件名，默认为“主机名-slow.log”，默认保存在datadir指定的目录中 |
| sort_buffer_size       |                |                    | 内存排序缓存区大小，为每个线程独占                              |
| sql_log_bin            |                |                    | 是否写入二进制日志。具有SUPER权限可禁止将自己的语句写入二进制日志   |
| sql_log_off            |                |                    | 是否关闭查询日志，只针对当前连接                               |
| sql_mode               |                |                    | SQL Mode                                                 |
|                        | --ssl          |                    | 是否支持SSL连接。如支持SSL，还需指定--ssl-ca、--ssl-cert、--ssl-key |
| sync_binlog            | --sync-binlog= |                    | 二进制日志缓存刷新规则。0由文件系统控制二进制日志缓存的刷新，大于0表示每若干条二进制日志刷新文件系统缓存 |
| table_open_cache       |                |                    | 打开并缓存的所有表数量                                      |
| thread_cache_size      |                |                    | 缓存可重用的线程数量                                        |
| tx_isolation           |                |                    | 事务隔离级别                                               |
| unique_checks          |                |                    | 是否开启唯一性检查                                          |


# 状态变量

关于状态变量的概要参阅官方文档：[https://dev.mysql.com/doc/refman/5.7/en/server-status-variable-reference.html](https://dev.mysql.com/doc/refman/5.7/en/server-status-variable-reference.html)。

关于状态变量的详情参阅官方文档：[https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html](https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html)。

| 状态变量                           | 说明                                   |
| --------------------------------- | ------------------------------------- |
| Com_commit                        | 事务提交的次数                           |
| Com_delete                        | 执行DELETE的次数，不论提交还是回滚都会累加   |
| Com_insert                        | 执行INSERT的次数，不论提交还是回滚都会累加，对批量插入的操作只累加一   |
| Com_rollback                      | 事务回滚的次数                           |
| Com_select                        | 执行SELECT的次数，不论提交还是回滚都会累加   |
| Com_update                        | 执行UPDATE的次数，不论提交还是回滚都会累加   |
| Connection_errors_max_connections | 因为超过数据库最大连接数而被拒绝的连接数     |
| Connections                       | 连接服务器的历史总次数                    |
| Handler_read_key                  | 使用索引读取的行数，越高表示索引越经常使用    |
| Handler_read_rnd_next             | 在数据文件读下一行的次数，越高表示进行越多表扫描 |
| Innodb_buffer_pool_read_requests  | 从InnoDB缓存池读取的次数                 |
| Innodb_buffer_pool_reads          | 从磁盘读取到InnoDB缓存池的次数            |
| Innodb_buffer_pool_wait_free      | Innodb的IO线程从数据文件中读取了数据要写入缓存池的时候，需要等待空闲页的次数 |
| InnoDB_row_lock_time_avg          | InnoDB行锁平均等待时间                   |
| InnoDB_row_lock_waits             | InnoDB航所等待次数                      |
| Innodb_rows_deleted               | InnoDB执行DELETE删除的行数               |
| Innodb_rows_inserted              | InnoDB执行INSERT插入的行数               |
| Innodb_rows_read                  | InnoDB执行SELECT返回的行数               |
| Innodb_rows_updated               | InnoDB执行UPDATE更新的行数               |
| Key_blocks_unused                 | MyISAM未使用的索引缓存块数                |
| Key_read_requests                 | 从MyISAM索引缓存区读取的次数              |
| Key_reads                         | 从磁盘读取至MyISAM索引缓存区的次数         |
| Key_write_requests                | 写入至MyISAM索引缓存区的次数              |
| Key_writes                        | 从MyISAM索引缓存区写入至磁盘的次数         |
| Open_tables                       | 打开并缓存的表数量                       |
| Qcache_free_blocks                | 查询缓存的空闲内存块数                    |
| Qcache_free_memory                | 查询缓存的空闲内存总数                    |
| Qcache_hits                       | 查询缓存命中数                           |
| Qcache_inserts                    | 被加入到查询缓存中的查询数目               |
| Qcache_lowmem_prunes              | 因缺少内存而被从查询缓存中删除的查询数       |
| Qcache_not_cached                 | 没有被查询缓存缓存的查询数                 |
| Qcache_queries_in_cache           | 查询缓存中已注册的查询数目                 |
| Qcache_total_blocks               | 查询缓存的总内存块数                      |
| Slow_queries                      | 慢查询次数                              |
| Sort_merge_passes                 | 排序算法进行合并的次数                    |
| Table_locks_immediate             | 能够立即获得表级锁的次数                  |
| Table_locks_waited                | 不能立即获取表级锁而需要等待的次数，越高表示表级锁争用越严重 |
| Threads_created                   | 为连接创建的线程数量                      |
| Uptime                            | 服务器工作时长                           |

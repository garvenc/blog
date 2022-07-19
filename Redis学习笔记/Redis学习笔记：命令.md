本文更新于2022-07-16，使用Redis 6.0.8，操作系统为Deepin 15.11。

[TOC]

**说明：本文中，小写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

官方文档参看：[https://redis.io/commands](https://redis.io/commands)

# 字符串

即字节序列。也可视作整数、浮点数。

## APPEND

字符串追加内容，如键不存在则创建。

```
APPEND str value
```

返回追加后的字节长度：

> (integer) 5

## BITCOUNT

统计二进制位中为1的数量。可指定字节区间[start, end]。

```
BITCOUNT str [start, end]
```

返回二进制位为1的数量：

> (integer) 4

## BITOP

执行位运算后将结果存储于deststr。

```
BITOP AND|OR|XOR deststr str [...]
BITOP NOT deststr str
```

返回结果字符串deststr的字节长度：

> (integer) 1

## DECR

自减1，只可用于视为整数的字符串类型。如键不存在，则先以0值创建。

```
DECR str
```

返回自减后的值：

> "-1"

## DECRBY

减少指定整数值，只可用于视为整数的字符串类型。如键不存在，则先以0值创建。

```
DECRBY str decrement
```

返回减少后的值：

> "-1"

## GET

获取字符串。

```
GET str
```

返回字符串：

> "value"

或无此键时返回：

> (nil)

## GETBIT

获取第offset的二进制位。字节的高位在前，第一位为0，不可使用负数。

```
GETBIT str offset
```

只会返回0或1，如位不存在则返回0：

> (integer) 1

## GETRANGE

获取字节区间[start, end]内的子串。第一个字节下标为0，可使用负数，最后一个字节下标为-1。

```
GETRANGE str start end
```

返回子串：

> "val"

或如不存在返回：

> ""

## INCR

自增1，只可用于视为整数的字符串类型。如键不存在，则先以0值创建。

```
INCR str
```

返回自增后的值：

> (integer) 1

## INCRBY

增加整数值，只可用于视为整数的字符串类型。如键不存在，则先以0值创建。

```
INCRBY str increment
```

返回增加后的值：

> (integer) 1

## INCRBYFLOAT

增加浮点数值，只可用于视为整数或浮点数的字符串类型。如键不存在，则先以0值创建。

```
INCRBYFLOAT str increment
```

返回增加后的值：

> "0.1"

## SET

设置字符串。

```
SET str value [EX seconds|PX milliseconds] [NX|XX]
```

`NX`为只当键不存在时设置，`XX`为只当键存在时设置。

成功返回：

> OK

当使用`NX`时键不存在，或当使用`XX`时键已存在，则返回：

> OK

当使用`NX`时键已存在，或当使用`XX`时键不存在，则返回：

> (nil)

## SETBIT

设置第offset的二进制位，只能设置为0或1，如键不存在则创建。字节的高位在前，第一位为0，不可使用负数。如字符串长度不足，则使用0值字节填充。

```
SETBIT str offset bit
```

返回二进制位原来的值：

> (integer) 1

## SETNX

只当键不存在时设置字符串。

```
SETNX str value
```

成功设置返回1，未成功设置返回0：

> (integer) 1

## SETRANGE

设置从offset（含）字节起的内容，如键不存在则创建。第一个字节下标为0，不可使用负数。如字符串长度不足，则使用0值字节填充。

```
SETRANGE str offset value
```

返回设置后的字符串字节长度：

> (integer) 5

# 列表

元素为字符串。左侧为开头，右侧为结尾。

## BLPOP

阻塞地从第一个非空列表弹出最左侧元素。

```
BLPOP list [...] timeoutseconds
```

如无阻塞弹出，则返回弹出的列表、元素：

> 1\) "list"
> 2\) "element"

如有阻塞弹出，则返回弹出的列表、元素和阻塞时间：

> 1\) "list"
> 2\) "element"
> (1.01s)

如阻塞超时未弹出，则返回nil和阻塞时间：

> (nil)
> (3.01s)

## BRPOP

阻塞地从第一个非空列表弹出最右侧元素。

```
BRPOP list [...] timeoutseconds
```

如无阻塞，则返回弹出的列表、元素：

> 1\) "list"
> 2\) "element"

如有阻塞，则返回弹出的列表、元素和阻塞时间：

> 1\) "list"
> 2\) "element"
> (1.01s)

如阻塞超时，则返回nil和阻塞时间：

> (nil)
> (3.01s)

## BRPOPLPUSH

阻塞地从srclist弹出最右侧元素，并把元素推入destlist最左侧。如destlist不存在则创建。

```
BRPOPLPUSH srclist destlist timeoutseconds
```

如无阻塞，则返回该元素：

> "element"

如有阻塞，则返回该元素和阻塞时间：

> "element"
> (1.01s)

如阻塞超时，则返回nil和阻塞时间：

> (nil)
> (3.01s)

## LINDEX

返回左侧指定下标的元素。第一个元素下标为0，可使用负数，最后一个元素下标为-1。

```
LINDEX list n
```

返回该下标处的元素：

> "element"

或无此元素时返回：

> (nil)

## LPOP

弹出最左侧元素。

```
LPOP list
```

返回弹出的元素：

> "element"

或列表为空时返回：

> (nil)

## LPUSH

将元素依次推入列表最左侧，如列表不存在则创建。参数中最后的元素位于列表最左侧。

```
LPUSH list element [...]
```

返回列表的长度：

> (integer) 1

## LRANGE

从左侧起查找区间[start, end]内的元素。第一个元素下标为0，可使用负数，最后一个元素下标为-1。

```
LRANGE list start end
```

返回元素列表：

> 1\) "element0"
> 2\) "element1"

或列表为空时返回：

> (empty array)

## LTRIM

从左侧起裁剪列表，只保留区间[start, end]内的元素。第一个元素下标为0，可使用负数，最后一个元素下标为-1。

```
LTRIM list start end
```

成功返回：

> OK

## RPOP

弹出最右侧元素。

```
RPOP list
```

返回弹出的元素：

> "element"

或列表为空时返回：

> (nil)

## RPOPLPUSH

从srclist弹出最右侧元素，并把元素推入destlist最左侧。如destlist不存在则创建。

```
RPOPLPUSH srclist destlist
```

返回该元素：

> "element"

或当srclist为空时返回：

> (nil)

## RPUSH

将元素依次推入列表右侧，如列表不存在则创建。参数中最后的元素位于列表最右侧。

```
RPUSH list element [...]
```

返回列表的长度：

> (integer) 1

# 无序集

元素为字符串。

## SADD

添加元素至集合中，如集合不存在则创建。

```
SADD set element [...]
```

返回实际添加的元素个数，如元素已存在则不计入个数：

> (integer) 1

## SCARD

返回集合的元素数量。

```
SCARD set
```

返回元素数量：

> (integer) 1

## SDIFF

差集运算。

```
SDIFF set excludeset [...]
```

返回结果集合的元素：

> 1\) "element0"
> 2\) "element1"

或当结果集合为空时返回：

> (empty array)

## SDIFFSTORE

差集运算，并将结果存储至destset。如destset不存在则创建，如已存在则清空。

```
SDIFFSTORE destset set excludeset [...]
```

返回结果集合的元素个数：

> (integer) 2

## SINTER

交集运算。

```
SINTER set [...]
```

返回结果集合的元素：

> 1\) "element0"
> 2\) "element1"

或当结果集合为空时返回：

> (empty array)

## SINTERSTORE

交集运算，并将结果存储至destset。如destset不存在则创建，如已存在则清空。

```
SINTERSTORE destset set [...]
```

返回结果集合的元素个数：

> (integer) 2

## SISMEMBER

检查是否包含元素。

```
SISMEMBER set element
```

包含返回1，不包含返回0：

> (integer) 1

## SMEMBERS

返回集合的所有元素。

```
SMEMBERS set
```

返回所有元素：

> 1\) "element0"
> 2\) "element1"

或集合为空时返回：

> (empty array)

## SMOVE

如元素在srcset中，则将其删除并移入destset。如destset不存在则创建。

```
SMOVE srcset destset element
```

如移动则返回1，如未移动则返回0：

> (integer) 1

## SPOP

随机弹出元素。

```
SPOP set [count]
```

返回弹出的元素列表：

> "element0"
> "element1"

或无元素弹出时返回：

> (empty array)

## SRANDMEMBER

随机返回元素。当count为正数时，元素不会重复，因此个数可能不足；当count为负数时，元素可能重复。

```
SRANDMEMBER set [count]
```

返回元素列表：

> "element0"
> "element1"

或count为0时返回：

> (empty array)

## SREM

删除集合中的元素。

```
SREM set element [...]
```

返回实际删除的元素个数，如元素不存在则不计入个数：

> (integer) 1

## SUNION

并集运算。

```
SUNION set [...]
```

返回结果集合的元素：

> 1\) "element0"
> 2\) "element1"

或当结果集合为空时返回：

> (empty array)

## SUNIONSTORE

并集运算，并将结果存储至destset。如destset不存在则创建，如已存在则清空。

```
SUNIONSTORE destset set [...]
```

返回结果集合的元素个数：

> (integer) 2

# 有序集

字符串与浮点数分值映射。

## ZADD

添加元素及分值至有序集合中，如有序集合不存在则创建。

```
ZADD zset score element [score element ...]
```

返回实际添加的元素个数，如元素已存在且分值相同则不计入个数：

> (integer) 1

## ZCARD

返回有序集合的元素数量。

```
ZCARD zset
```

返回元素数量：

> (integer) 1

## ZCOUNT

返回有序集合分值在区间[min, max]的元素数量。

```
ZCOUNT zset min max
```

返回元素数量：

> (integer) 1

## ZINCRBY

增加元素的分值。如元素不存在，则先以0分值添加。

```
ZINCRBY zset increment element
```

返回增加后的分值：

> "0.1"

## ZINTERSTORE

类似交集运算，并将结果存储至destzset。如destset不存在则创建，如已存在则清空。

```
ZINTERSTORE destzset nzset zset [...] [WEIGHTS weight [...]] [AGGREGATE SUM|MIN|MAX]
```

zset参数可使用集合，被当作分值为1的有序集合。AGGREGATE默认为SUM。

返回结果有序集合的元素个数：

> (integer) 2

## ZRANGE

分值从小到大查找排名在区间[start, end]内的元素。第一个元素下标为0，可使用负数，最后一个元素下标为-1。

```
ZRANGE zset start end [WITHSCORES]
```

返回元素列表：

> 1\) "element0"
> 2\) "element1"

如指定`WITHSCORES`则同时返回分值：

> 1\) "element0"
> 2\) "1"
> 3\) "element1"
> 4\) "2"

或列表为空时返回：

> (empty array)

## ZRANK

查找元素在有序集合中从小到大的排名。第一为0。

```
ZRANK zset element
```

返回元素的排名：

> (integer) 0

或当元素不存在时返回：

> (nil)

## ZRANGEBYSCORE

分值从小到大查找分值在区间[min, max]的元素。

```
ZRANGEBYSCORE zset min max [WITHSCORES] [LIMIT offset count]
```

返回元素列表：

> 1\) "element0"
> 2\) "element1"

如指定`WITHSCORES`则同时返回分值：

> 1\) "element0"
> 2\) "1"
> 3\) "element1"
> 4\) "2"

或列表为空时返回：

> (empty array)

## ZREM

删除有序集合中的元素。

```
ZREM zset elment [...]
```

返回实际删除的元素个数，如元素不存在则不计入个数：

> (integer) 1

## ZREMRANGEBYRANK

删除有序集合中分值从小到大排名在区间[start, end]内的元素。第一个元素下标为0，可使用负数，最后一个元素下标为-1。

```
ZREMRANGEBYRANK zset start end
```

返回被删除的元素个数：

> (integer) 1

## ZREMRANGEBYSCORE

删除有序集合中分值在区间[min, max]内的元素。

```
ZREMRANGEBYSCORE zset min max
```

返回被删除的元素个数：

> (integer) 1

## ZREVRANGE

分值从大到小查找排名在区间[start, end]内的元素。第一个元素下标为0，可使用负数，最后一个元素下标为-1。

```
ZREVRANGE zset start end [WITHSCORES]
```

返回元素列表：

> 1\) "element0"
> 2\) "element1"

如指定`WITHSCORES`则同时返回分值：

> 1\) "element0"
> 2\) "2"
> 3\) "element1"
> 4\) "1"

或列表为空时返回：

> (empty array)

## ZREVRANGEBYSCORE

分值从大到小查找分值在区间[min, max]的元素。

```
ZRANGEBYSCORE zset max min [WITHSCORES] [LIMIT offset count]
```

返回元素列表：

> 1\) "element0"
> 2\) "element1"

如指定`WITHSCORES`则同时返回分值：

> 1\) "element0"
> 2\) "2"
> 3\) "element1"
> 4\) "1"

或列表为空时返回：

> (empty array)


## ZREVRANK

查找元素在有序集合中从大到小的排名。第一为0。

```
ZREVRANK zset element
```

返回元素的排名：

> (integer) 0

或当元素不存在时返回：

> (nil)

## ZSCORE

返回元素的分值。

```
ZSCORE zset element
```

返回分值：

> "1"

或当元素不存在时返回：

> (nil)

## ZUNIONSTORE

类似并集运算，并将结果存储至destzset。如destset不存在则创建，如已存在则清空。

```
ZUNIONSTORE destzset nzset zset [...] [WEIGHTS weight [...]] [AGGREGATE SUM|MIN|MAX]
```

zset参数可使用集合，被当作分值为1的有序集合。AGGREGATE默认为SUM。

返回结果有序集合的元素个数：

> (integer) 2

# 无序散列

键值均为字符串，值也可视作整数、浮点数。

## HDEL

删除散列的键，如散列所有键都被删除则散列也会被删除。

```
HDEL hash field [...]
```

返回实际删除的键个数，如键不存在则不计入个数：

> (integer) 1

## HEXISTS

检查键是否存在。

```
HEXISTS hash field
```

存在返回1，不存在返回0：

> (integer) 1

## HGET

获取单个键对应的值。

```
HGET hash field
```

返回键对应的值：

> "value"

或当无此键时返回：

> (nil)

## HGETALL

返回所有键值对。

```
HGETALL hash
```

键值间隔依次返回：

> 1\) "field1"
> 2\) "value1"
> 3\) "field2"
> 4\) "value2"

或散列为空时返回：

> (empty array)

## HINCRBY

增加键的值，只可用于视为整数的字符串类型。如散列键不存在则创建，如键不存在则先以0值创建。

```
HINCRBY hash field increment
```

返回增加后的值：

> (integer) 1

## HINCRBYFLOAT

增加键的浮点数值，只可用于视为整数或浮点数的字符串类型。如散列键不存在则创建，如键不存在则先以0值创建。

```
HINCRBYFLOAT hash field increment
```

返回增加后的值：

> "0.1"

## HKEYS

返回所有键。

```
HKEYS hash
```

返回键的列表：

> 1\) "field1"
> 2\) "field2"

或散列为空时返回：

> (empty array)

## HLEN

返回键值对数量。

```
HLEN hash
```

返回数量：

> (integer) 1

## HMGET

获取多个键对应的值。

```
HMGET hash field [...]
```

依次返回键对应的值，如键不存在则值为nil：

> 1\) "value1"
> 2\) (nil)

## HMSET

设置散列的键值对，如散列不存在则创建。

```
HMSET hash field value [field value ...]
```

返回实际添加的键个数，如键已存在则不计入个数：

> (integer) 1

## HSET

设置散列的键值对，如散列不存在则创建。

```
HSET hash field value [field value ...]
```

返回实际添加的键个数，如键已存在则不计入个数：

> (integer) 1

## HVALS

返回所有值。值可能重复。

```
HVALS hash
```

返回值的列表：

> 1\) "value1"
> 2\) "value2"

或散列为空时返回：

> (empty array)

# 键

## DEL

删除键，可用于所有类型。

```
DEL key [...]
```

返回删除的键数量，如键不存在则不计入个数：

> (integer) 1

## EXISTS

检查键是否存在，可用于所有类型。

```
EXISTS key [...]
```

返回存在的键数量：

> (integer) 1

## FLUSHALL

删除所有键。

```
FLUSHALL [ASYNC]
```

返回：

> OK

## KEYS

查看匹配glob模式的键。

```
KEYS glob
```

返回键的列表：

> 1\) "key1"
> 2\) "key2"

或键的列表为空时返回：

> (empty list or set)

## TYPE

查看键的类型。

```
TYPE key
```

返回键的类型：

> zset

或键不存在时返回：

> none

# 排序

## SORT

排序。可对列表、集合、有序集（忽略分值）进行排序。

```
SORT key [BY by_pattern] [LIMIT offset count] [GET get_pattern [GET get_pattern ...]] [ASC|DESC] [ALPHA] [STORE destlist]
```

`BY`指定使用外部键进行排序。以key中的元素替换by_pattern中的*号，以此作为键查找字符串，再使用此字符串作为排序的依据（如：user*）。或以key中的元素替换by_pattern中的*号，以此作为键查找无序散列，再使用->取无序散列中键的值作为排序的依据（如：user*->name）。如by_pattern不存在，则跳过排序。

`LIMIT`限制返回的结果跳过的数量和返回的总数量。

`GET`指定外部键作为返回结果。以key中的元素替换get_pattern中的*号，以此作为键查找字符串，再使用此字符串作为返回结果（如：user*）。或以key中的元素替换by_pattern中的*号，以此作为键查找无序散列，再使用->取无序散列中键的值作为返回结果（如：user*->name）。get_pattern为#表示key中的元素。

`ASC`为升序，`DESC`为降序，默认为升序。

`ALPHA`为作为字符串排序，默认为作为数值排序。

`STORE`将结果保存至列表中，如destlist不存在则创建，如已存在则清空。

返回排序后的元素：

> "1"
> "2"

或如返回多个外部键：

> "1"
> "a"
> "2"
> "b"

或无元素时返回：

> (empty array)

如使用了`STORE`则返回排序后的元素数量：

> (integer) 1

# 过期

## EXPIRE

设置键于若干秒后过期。

```
EXPIRE key seconds
```

键存在返回1，不存在返回0：

> (integer) 1

## EXPIREAT

设置键于秒级Unix时间戳过期：

```
EXPIREAT key unix_timestamp_second
```

键存在返回1，不存在返回0：

> (integer) 1

## PERSIST

将键设为永久，会移除过期时间。

```
PERSIST key
```

键原有过期时间返回1，原是永久返回0：

> (integer) 1

## PEXPIRE

设置键于若干毫秒后过期。

```
PEXPIRE key millseconds
```

键存在返回1，不存在返回0：

> (integer) 1

## PEXPIREAT

设置键于毫秒级Unix时间戳过期：

```
PEXPIREAT key unix_timestamp_millsecond
```

键存在返回1，不存在返回0：

> (integer) 1

## PTTL

查看键距离过期的毫秒数。

```
PTTL key
```

返回毫秒数，永久返回-1，不存在返回-2：

> (integer) 1

## TTL

查看键距离过期的秒数。

```
TTL key
```

返回秒数，永久返回-1，不存在返回-2：

> (integer) 1

# 事务

命令的执行顺序为：`WATCH`、`UNWATCH`（不再继续执行后面的命令）、`MULTI`、`DISCARD`（不再继续执行后面的命令）、`EXEC`。

## DISCARD

取消所有对键进行的监视并清空使用`MULTI`入队的命令。在`MULTI`之后`EXEC`之前执行。

```
DISCARD
```

返回：

> OK

## EXEC

执行事务。

```
EXEC
```

执行`MULTI`至`EXEC`的所有命令，嵌套返回各命令的执行结果：

> 1\) "value"
> 2\) 1) "element0"
> &nbsp;&nbsp;&nbsp;2) "element1"

执行失败返回：

> (nil)

## MULTI

开始事务。

```
MULTI
```

返回：

> OK

`MULTI`至`EXEC`的所有命令会入队并在`EXEC`后一起执行，期间所有命令均返回：

> QUEUED

## UNWATCH

取消所有对键进行的监视。在`WATCH`之后`MULTI`之前执行。

```
UNWATCH
```

返回：

> OK

## WATCH

对键进行监视。执行`WATCH`至`EXEC`这段时间内，如其他连接对键进行修改，`EXEC`会执行失败。

```
WATCH key [...]
```

返回：

> OK

# 发布/订阅

消息为字符串。

## PSUBSCRIBE

订阅匹配glob模式的频道。

```
PSUBSCRIBE glob [...]
```

依次返回各频道的事件名（psubscribe）、glob模式、当前订阅的频道数量：

> 1\) "psubscribe"
> 2\) "glob1"
> 3\) (integer) 1
> 1\) "psubscribe"
> 2\) "glob2"
> 3\) (integer) 2

此后，当前连接被阻塞直至断开。期间依次输出消息的事件名（pmessage）、glob模式、频道名、消息值：

> 1\) "pmessage"
> 2\) "glob1"
> 3\) "channel"
> 4\) "msg"

## PUNSUBSCRIBE

在redis-cli，因订阅后连接被阻塞，不能使用此命令。

退订匹配glob模式的频道。如不指定glob则退订所有频道。

```
PUNSUBSCRIBE [glob [...]]
```

依次返回各glob模式的事件名（punsubscribe）、glob模式、当前订阅的频道数量。如当前订阅的频道数量已为0，则不继续返回后续事件，但事件仍存在连接的队列中：

> 1\) "punsubscribe"
> 2\) "glob1"
> 3\) (integer) 1
> 1\) "punsubscribe"
> 2\) "glob2"
> 3\) (integer) 0

当不指定channel且没有订阅频道时返回：

> 1\) "punsubscribe"
> 2\) (nil)
> 3\) (integer) 0

## PUBLISH

向频道发送消息。

```
PUBLISH channel msg
```

返回接收到消息的连接数：

> (integer) 1

## SUBSCRIBE

订阅频道。

```
SUBSCRIBE channel [...]
```

依次返回各频道的事件名（subscribe）、频道名、当前订阅的频道数量：

> 1\) "subscribe"
> 2\) "channel1"
> 3\) (integer) 1
> 1\) "subscribe"
> 2\) "channel2"
> 3\) (integer) 2

此后，当前连接被阻塞直至断开。期间依次输出消息的事件名（message）、频道名、消息值：

> 1\) "message"
> 2\) "channel1"
> 3\) "msg"

## UNSUBSCRIBE

在redis-cli，因订阅后连接被阻塞，不能使用此命令。

退订频道。如不指定channel则退订所有频道。

```
UNSUBSCRIBE [channel [...]]
```

依次返回各频道的事件名（unsubscribe）、频道名、当前订阅的频道数量。如当前订阅的频道数量已为0，则不继续返回后续事件，但事件仍存在连接的队列中：

> 1\) "unsubscribe"
> 2\) "channel1"
> 3\) (integer) 1
> 1\) "unsubscribe"
> 2\) "channel2"
> 3\) (integer) 0

当不指定channel且没有订阅频道时返回：

> 1\) "unsubscribe"
> 2\) (nil)
> 3\) (integer) 0

# 持久化

## BGREWRITEAOF

重写AOF文件。子进程负责将AOF文件写入磁盘，父进程继续处理命令请求。


```
BGREWRITEAOF
```

返回：

> Background append only file rewriting started

## BGSAVE

后台创建快照文件。子进程负责将快照文件写入磁盘，父进程继续处理命令请求。

```
BGSAVE
```

返回：

> Background saving started

## SAVE

前台创建快照文件。快照文件创建完毕之前都不再响应任何其他命令。

```
SAVE
```

返回：

> OK

# 复制

## SLAVEOF

作为从服务器，指定主服务器，并丢弃已有数据，复制数据:

```
SLAVEOF host port
```

停止复制，使用已有数据，由从服务器切换为主服务器：

```
SLAVEOF NO ONE
```

返回：

> OK

# 管理

## DEBUG OBJECT

返回对象的调试信息。

```
DEBUG OBJECT key
```

返回调试信息：

> Value at:0x7f4500a77c80 refcount:1 encoding:ziplist serializedlength:17 lru:11272693 lru_seconds_idle:6559102

如键不存在返回：

> (error) ERR no such key

## INFO

查看服务器状态信息。

```
INFO section
```

返回的信息包括几个小节，每个小节由一行以“#”开头的小节名开头及一行空行结尾，小节中每条信息一行并以“:”分隔名字和值：

> \# Modules
>
> \# Cluster
> cluster_enabled:0

各名字的含义如下：

* aof_pending_bio_fsync：AOF等待同步到磁盘的命令数。

## SELECT

选择切换至第n号库。最大为15，默认为0。如非0，命令行提示符带有“[n]”字样。

```
SELECT n
```

返回：

> OK

## SHUTDOWN

停止服务。

```
SHUTDOWN [NOSAVE|SAVE]
```

无返回，连接断开。

# 脚本

## EVAL

执行Lua脚本代码。

```
EVAL lua_code key_amount [key [...]] [arg [...]]
```

key为lua_code中使用到的键，在Lua脚本中依次以KEYS[1]等形式使用。arg为lua_code中使用到的附加参数，在Lua脚本中依次以ARGV[1]等形式使用。

返回Lua脚本的返回值，如有表格（table）则嵌套返回：

> 1\) "lua"
> 2\) 1) (integer) 1
> &nbsp;&nbsp;&nbsp;2) (integer) 2

或当失败返回错误：

> (error) ERR wrong number of arguments for 'eval' command

## EVALSHA

执行已加载的Lua脚本代码。

```
EVALSHA sha1 key_amount [key [...]] [arg [...]]
```

key为lua_code中使用到的键，在Lua脚本中依次以KEYS[1]等形式使用。arg为lua_code中使用到的附加参数，在Lua脚本中依次以ARGV[1]等形式使用。

返回Lua脚本的返回值，如有表格（table）则嵌套返回：

> 1\) "lua"
> 2\) 1) (integer) 1
> &nbsp;&nbsp;&nbsp;2) (integer) 2

或当失败返回错误：

> (error) ERR wrong number of arguments for 'evalsha' command

## SCRIPT FLUSH

清除所有已加载的Lua脚本代码。

```
SCRIPT FLUSH
```

返回：

> OK

## SCRIPT KILL

杀死正在执行的Lua脚本。

```
SCRIPT KILL
```

返回：

> OK

如没有正在执行的Lua脚本则返回：

> (error) NOTBUSY No scripts in execution right now.

## SCRIPT LOAD

加载但不执行Lua脚本代码：

```
SCRIPT LOAD lua_code
```

返回脚本代码的SHA1校验和：

> "e0e1f9fabfc9d4800c877a703b823ac0578ff8db"

或当失败返回错误：

> (error) ERR Unknown subcommand or wrong number of arguments for 'load'. Try SCRIPT HELP.

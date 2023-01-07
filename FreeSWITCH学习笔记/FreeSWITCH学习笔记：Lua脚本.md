本文更新于2022-06-03，使用FreeSWITCH 1.10.7。

[TOC]

# argv

命令行参数。`argv[0]`为脚本文件名。

# freeswitch.API

```lua
API = freeswitch.API()
```

## API:execute

执行API。

```lua
REPLY = API:execute(APINAME [, ARG [, ...]])
```

# freeswitch.bridge

会阻塞。

# freeswitch.consoleLog

输出日志。

```lua
freeswitch.consoleLog(LEVEL, MSG)
```

# freeswitch.Dbh

创建数据库句柄。可连接SQLite或任何支持ODBC的数据库。

```lua
DBH = freeswitch.Dbh(SQLITEFILEPATH|"odbc://DSN USERNAME PASSWORD")
```

## DBH:affected_rows

返回上一个`DBH:query`影响的行数。

```lua
N = DBH:affected_rows()
```

## DBH:connected

测试数据库是否连接成功。

```lua
OK = DBH:connected()
```

## DBH:test_reactive

确认数据库表存在。如果SELECTSQL执行不行功，则先试图运行DROPSQL删除数据库表，再运行CREATESQL重新创建数据库表。

```lua
DBH:test_reactive(SELECTSQL, DROPSQL, CREATESQL)
```

## DBH:query

执行查询语句。

```lua
DBH:query(SQL, [function (row) end])
```

回调函数中可以`row.COLUMN`的形式，用数据库表的列名来引用值。

## DBH:release

释放数据库句柄。

```lua
DBH:release()
```

# freeswitch.email

# freeswitch.Event

初始化一个事件。

```lua
EVENT = freeswitch.Event(EVENTTYPE [, SUBCLASS])
```

事件类型TYPE需要在switch_event_types_t枚举类型中有定义，它是在switch_types.h中定义的。如果使用未定义的类型，则统一为"MESSAGE"。

当TYPE为"CUSTOM"时，SUBCLASS可为任意字符串，它将作为事件中的Event-Subclass。

## EVENT:addBody

给事件添加正文。

```lua
EVENT:addBody(BODY)
```

## EVENT:addHeader

给事件增加一个事件头。

```lua
EVENT:addHeader(HEADER, VALUE)
```

HEADER为"Content-Type"用于表示正文的类型。

## EVENT:delHeadder

从事件中删除一个事件头。

```lua
EVENT:delHeader(HEADER)
```

## EVENT:fire

产生事件。

```lua
EVENT:fire()
```

## EVENT:getBody

从事件中获取正文。

```lua
BODY = EVENT:getBody()
```

## EVENT:getHeader

从事件中获取事件头的值。

```lua
VALUE = EVENT:getHeader(HEADER)
```

## EVENT:getType

获取事件的类型（名字），即`freeswitch.Event`的TYPE参数的值。等价于`EVENT:getHeader("Event-Name")`。

```lua
EVENTTYPE = EVENT:getType()
```

## EVENT:serialize

将事件序列化成字符串。

```lua
STR = EVENT:serialize([FORMAT])
```

FORMAT省略时为简单文本格式，"json"为JSON格式，"xml"为XML格式。

# freeswitch.EventConsumer

创建事件消费者。

```lua
EVENTCONSUMER = freeswitch.EventConsumer(EVENTTYPE)
```

## EVENTCONSUMER.pop

取出事件。

```lua
EVENTCONSUMER.pop(N)
```

N为0是非阻塞的，为1是阻塞的。

# freeswitch.msleep

```lua
freeswitch.msleep(MS)
```

# freeswitch.Session

发起一个呼叫，并一直等待对方应答。

```lua
SESSION = freeswitch.Session(CALLURL)
```

## SESSION:answer

给主叫回送应答消息（200）。相当于answer APP。

```lua
SESSION:answer()
```

## SESSION:execute

执行APP。大部分session函数与APP是一一对应的。不会触发`SESSION:setInputCallback`的回调。

```lua
SESSION:execute(APPNAME [, ARG [, ...]])
```

## SESSION:get_uuid

获取当前Session的UUID。相当于`SESSION:getVariable("uuid")`。

```lua
UUID = SESSION:get_uuid()
```

## SESSION:getVariable

获取通道变量的值。

```lua
VALUE = SESSION:getVariable(VAR)
```

## SESSION:hangup

挂断通话。相当于hangup APP。默认执行完脚本后会自动挂断通话，可不显式调用。

```lua
SESSION:hangup([REASON])
```

## SESSION:hangupCause

返回挂机原因。

```lua
REASON = SESSION:hangupCause()
```

## SESSION:playAndGetDigits

播放声音并等待接收DTMF按键。相当于play_and_get_digits APP。

```lua
DIGITS = SESSION:playAndGetDigits(MINDIGITS, MAXDIGITS, MAXATTEMPTS, TIMEOUTMS, TERMINATORS, MUSIC, INVALIDMUSIC, REGEX [, INTERDIGITTIMEOUTMS [, "FAILEXTENSION [FAILDIALPLAN [FAILCONTEXT]]"]])
```

## SESSION:preAnswer

## SESSION:read

播放声音并等待接收DTMF按键。相当于read APP。

```lua
DIGITS = SESSION:read(MINDIGITS, MAXDIGITS, MUSIC, INTERDIGITTIMEOUTMS, TERMINATORS)
```

## SESSION:ready

检查Session是否可正常使用，如已挂机则返回false。

```lua
OK = SESSION:ready()
```

在脚本中，如有循环，一定要检测`SESSION.ready()`是否返回true，否则Session挂机后脚本仍可能死循环运行。

## SESSION:recordFile

录音。相当于record APP。

```lua
SESSION:recordFile(FILENAME [, MAXSECONDS, SILENCETHRESHOLD, SILENCESECONDS])
```

FILENAME为录音文件名。

MAXSECONDS为录音最长秒数。

SILENCETHRESHOLD为静音阈值，低于此值的声音认为是静音。

SILENCESECONDS为静音时长大于此秒数则停止录音。

## SESSION:setAutoHangup

设置执行完脚本后是否自动挂断通话。

```lua
SESSION:setAutoHangup(BOOL)
```

## SESSION:setInputCallback

设置收到外部输入时的回调函数。

```lua
SESSION:setInputCallback(STRFUNCTIONNAME, "")
```

回调函数共有4个参数：

* s：即session。
* type：如：dtmf，event。
* obj：输入对象。当type为dtmf时，有两个键：digit为按键，duration为时长。
* arg：

回调函数如返回"break"则停止当前正在执行的APP，后面没有其它脚本语句的话Lua APP就会退出；如果返回空值（如空字符串）则什么也不做。

## SESSION:set_tts_params

设置TTS参数。

```lua
SESSION:set_tts_params(TTSENGINE, TTSVOICE)
```

## SESSION:setVariable

设置通道变量的值。相当于set APP。

```lua
SESSION:setVariable(VAR, VALUE)
```

## SESSION:sleep

暂停若干毫秒。相当于sleep APP。

```lua
SESSION:sleep(MS)
```

## SESSION:speak

使用TTS播放声音。相当于speak APP。

```lua
SESSION:speak(TEXT)
```

## SESSION:streamFile

播放声音。相当于playback APP。

```lua
SESSION:streamFile(MUSIC)
```

## SESSION:unsetInputCallback

取消收到外部输入时的回调函数。

# message

在Chatplan中自动获得此对象。其与`freeswitch.Event`创建的对象是一样的。此外还有以下函数。

## message:chat_execute

执行文本消息支持的动作。

```lua
message:chat_execute(ACTION)
```

ACTION可为：

* fire：产生一个MESSAGE事件。
* info：显示信息。
* reply：回复消息。
* send：发送消息。
* set：设置变量。
* stop：停止消息路由。
* system：调用system函数执行系统调用。

# session

在Dialplan中会自动获得此对象。其与`freeswitch.Session`创建的对象是一样的。

# XML_REQUEST

XML绑定请求中，会自动获得此对象。

# XML_STRING

XML绑定请求中，脚本执行完成后，FreeSWITCH从此变量得到XML文本，进行解析以执行下一步的动作。

本文更新于2022-12-20，使用FreeSWITCH 1.10.7。

[TOC]

**Event Socket命令最后需带有两个换行符。**

# api

执行API命令。阻塞。

```esl
api API [ARG [ ...]]
```

# auth

内连模式下身份验证。需要第一个发送。

```esl
auth PASSWORD
```

# bgapi

后台执行API命令。不阻塞。

```esl
bgapi API [ARG [ ...]]
[Job-UUID: UUID]
```

返回带有Job-UUID。当API命令执行完成后会产生一个BACKGROUND_JOB事件。

# connect

外连模式下接收FreeSWITCH的连接。需要第一个发送。

```esl
connect
```

# divert_events

开启/关闭将InputCallback产生的事件转发到Event Socket。

```esl
divert_events on|off
```

# event

订阅事件。

```esl
event [plain|json|xml] all|EVENTTYPE [ ...] [SUBCLASS [ ...]]
```

默认为plain。

# exit

告诉FreeSWITCH令其关闭TCP连接。

```esl
exit
```

# filter

添加事件过滤器。可使用多次，匹配其中之一即可接收。

```esl
filter EVENTHEADER VALUE
```

# filter delete

删除事件过滤器。

```esl
filter delete [EVENTHEADER VALUE]
```

不指定EVENTHEADER则删除所有接收事件的过滤。

# linger

外连模式下，设置FreeSWITCH在Channel挂断后至断开TCP连接之间的逗留时间，以等待所有事件发送完毕。

```esl
linger SECONDS
```

# log

订阅日志。

```esl
log LEVEL
```

# myevents

订阅Channel的所有事件。`event`的特殊情况。

```esl
myevents [plain|json|xml] [CHANNELUUID]
```

在外连模式下不需要CHANNELUUID，在内连模式下则需要。

默认为plain。

# nixevent

取消订阅事件。`event`的相反动作。

```esl
nixevent all|EVENTTYPE [ ...] [SUBCLASS [ ...]]
```

# noevent

取消所有订阅事件。相当于`nixevent all`。

```esl
noevent
```

# nolinger

外连模式下，撤销FreeSWITCH在Channel挂断后至断开TCP连接之间的逗留时间。`linger`的相反动作。

```esl
nolinger
```

# nolog

关闭订阅日志。`log`的相反动作。

```esl
nolog
```

# sendevent

发送事件。

```esl
sendevent EVENTTYPE
HEADER: VALUE
[...]

[BODY]
```

# sendmsg

发送APP指令。

```esl
sendmsg
call-command: execute
execute-app-name: APP
execute-app-arg: [ARG [ ...]]
```

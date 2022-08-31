本文更新于2022-02-27。

[TOC]

# RTP

RTP（Real-time Transport Protocol），即实时传输协议。

文档见RFC 3550：[https://datatracker.ietf.org/doc/rfc3550](https://datatracker.ietf.org/doc/rfc3550)。

RTP包的头部格式如下：

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|V=2|P|X|  CC   |M|     PT      |       sequence number         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           timestamp                           |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           synchronization source (SSRC) identifier            |
+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
|            contributing source (CSRC) identifiers             |
|                             ....                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

* V（version）：2 bit。标明RTP版本号。RFC 3550中规定版本号为2。
* P（Padding）：1 bit。如果该位被置为1，则将在该数据包末尾包含额外的附加信息，附加信息的最后一个字节表示额外附加信息的长度（包含该字节本身）。该字段之所以存在是因为一些加密机制需要固定长度的数据块，或者为了在一个底层协议数据单元中传输多个RTP数据包。
* X（extension）：1 bit。如果该位被置为1，则在固定的头部后存在一个扩展头部，格式定义在RFC 3550中。
* CC（CSRC count）：4 bit。在固定头部后存在多少个CSRC标记。
* M（Marker）：1 bit。该位的功能依赖于RTP中实际传输媒体类型的Profile的定义。Profile可以改变该位的长度，但是要保持M和PT总长度不变（一共8 bit）。一般来说，音频跟视频中该位的定义是不同的。在视频中，M值为1通常表示一帧图像的结束。
* PT（payload type）：7 bit。载荷类型，标记RTP包所携带信息的类型，标准类型列在RFC 3551中。如果接收方不能识别该类型，必需忽略该包。
* sequence number：16 bit。序列号，每个RTP包发送后该序列号加1，接收方可以根据该序列号检测丢包或重新排列数据包顺序等。
* timestamp：32 bit。时间戳。反映RTP包中所携带信息中第一个字节的采样时间。
* synchronization source (SSRC) identifier：32 bit。同步源标识。在一个RTP会话期间，每个数据流都应该有一个不同的SSRC。如果数据源发生变化，则应该改变SSRC，以让接收端感知到该变化。
* contributing source (CSRC) identifiers：0到15项，每个项32 bit。参与数据源标识。只有存在多路混发的时候才有效。多路混发的情况，如一个将多声道的语音流合并到一个RTP流中发送，在这里就可以列出原来每个声道。

# RTCP

RTCP（Real-time Transport Control Protocol），即实时传输控制协议，是RTP的一个姊妹协议。

文档见RFC 3551：[https://datatracker.ietf.org/doc/rfc3551](https://datatracker.ietf.org/doc/rfc3551)。

RTP使用一个偶数UDP端口，RTCP则使用RTP的下一个相邻的奇数端口。

定义的RTP载荷类型有：

* 0：PCMU
* 8：PCMA

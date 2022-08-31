本文更新于2022-05-02。

SDP（Session Description Protocol），即会话描述协议。

文档见RFC 4566：[https://datatracker.ietf.org/doc/rfc4566](https://datatracker.ietf.org/doc/rfc4566)。

* a（Attributes）：属性。用于描述上一个非a字段。
	```sdp
	a=inactive
	a=ptime:PACKETTIME
	a=recvonly
	a=sendonly
	a=sendrecv
	a=rtpmap:PAYLOADTYPE ENCODINGNAME/CLOCKRATE [/ENCODINGPARAMETERS]
	```
	* inactive:不收不发。
	* ptime：RTP的打包间隔。
	* recvonly：只收。
	* sendonly：只发。
	* sendrecv：双向收发。
	* rtpmap：m的RTP载荷类型与编解码名的映射。各字段依次为：载荷类型、编解码名/采样频率（Hz）、参数。编解码名telephone-event为DTMF。

	示例：
	> a=rtpmap:101 telephone-event/8000
	> a=fmtp:101 0-15
	> a=sendrecv
* b（Bandwidth Type）：带宽类型。
	```sdp
	b=BANDWIDTHTYPE:BANDWIDTH
	```
	示例：
	> b=AS:2064
* c（Connection Data）：连接数据。
	```sdp
	c=NETTYPE ADDRTYPE CONNECTIONADDRESS
	```
	各字段依次为：网络类型、地址类型、网络地址（RTP流会发到该地址上）。示例：
	> c=IN IP4 192.168.1.102
* m（Media Type）：媒体类型。
	```sdp
	m=MEDIA PORT PROTO FMT[ ...]
	```
	各字段依次为：媒体类型（audio为音频，video为视频）、端口号、传输协议（RTP/AVP表示使用RTP承载的Audio/Video Profile）、RTP载荷类型支持的若干个格式（分为静态编码和动态编码，大于95的为动态编码，需在后面使用a=rtpmap进行说明）。示例：
	> m=audio 59820 RTP/AVP 8 18 101
* o（Origin）：源。
	```sdp
	o=USERNAME SESSIONID SESSIONVERSION NETTYPE ADDRTYPE UNICASTADDRESS
	```
	各字段依次为：用户名、会话ID、会话版本号、网络类型、地址类型、单播地址。示例：
	> o=- 1364745810658806 1 IN IP4 192.168.1.102
* s（Session Name）：会话名称。
	```sdp
	s=SESSIONNAME
	```
	示例：
	> s=Bria 3 release 3.5.0b stamp 69410
* t（Timing）：起止时间。
	```sdp
	t=STARTTIME STOPTIME
	```
	0表示无限。示例：
	> t=0 0
* v（Version）：协议的版本号。
	```sdp
	v=VERSION
	```
	示例：
	> v=0

完整示例：
> v=0
> o=- 1364745810658806 1 IN IP4 192.168.1.102
> s=Bria 3 release 3.5.0b stamp 69410
> c=IN IP4 192.168.1.102
> b=AS:2064
> t=0 0
> m=audio 59820 RTP/AVP 8 18 101
> a=rtpmap:101 telephone-event/8000
> a=fmtp:101 0-15
> a=sendrecv

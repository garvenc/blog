本文更新于2022-12-19，使用FreeSWITCH 1.10.7。

[TOC]

官方帮助文档：[https://freeswitch.org/confluence/](https://freeswitch.org/confluence/)

**说明：下文中，部分大写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

# 目录结构

在Linux下的默认安装路径为/usr/local/freeswitch，在Windows下的默认安装路径为C:\Program Files\FreeSWITCH。

* bin：可执行程序目录。
	* freeswitch：服务器。
	* fs_cli：客户端。
	* fs_encode：将声音文件从一种编码转换到另一种编码。
	* fsxs：编译模块的辅助工具。不用Makefile也可编译，意味着不用源代码环境也可编译模块。
	* tone2wav：从一个TGML标记语言描述文件生成声音文件。
* conf：配置文件目录。
	* autoload_configs：自动加载的模块的配置文件的目录。
		* *.conf.xml：一般每个模块一个配置文件，文件名格式为“不包含mod_的模块名.conf.xml”。
	* chatplan：聊天计划的配置文件的目录。
	* dialplan：拨号计划的配置文件的目录。
		* default.xml：默认Context，一般用于内部注册用户路由。
		* public.xml：一般用于外部来话路由。
	* directory：用户目录。支持多个域（Domain），每个域可以写到一个XML文件中。
		* default：默认域的SIP用户的配置目录。
			* *.xml：默认域的SIP用户，每个用户一个文件，文件名为用户名，依次为1000~1019。
		* default.xml：默认域。
	* freeswitch.xml：主配置文件，载入其他配置文件。
	* ivr_menus：IVR菜单配置目录。
	* jingle_profiles：连接Google Talk的相关配置的目录。
	* lang：多语言支持的配置目录。
		* en：英语。
		* fr：法语。
	* mrcp_profiles：MRCP的相关配置的目录，用于跟第三方语音合成和语音识别系统对接。
		* vestec-mrcp-v1.xml：Vestec支持MRCP的V1版本协议（RTSP）的配置。
	* sip_profiles：SIP配置文件目录。一般每个文件描述一个Profile。
		* external.xml：一般用于外部网关。
		* internal.xml：一般用于本地用户。
	* skinny_profiles：思科SCCP协议话机的配置文件目录。
	* vars.xml：定义一些全局变量。
* db：数据库（sqlite）目录。
	* core.db：核心数据库。记录系统的接口（interfaces）、任务（tasks）以及当前的通道（channels）、通话（calls）等实时数据。
	* sofia_reg_*.db：SIP数据库。每个Profile一个文件。
* grammar：语法文件目录，用于ASR。
* htdocs：HTTP Server根目录。
* include：头文件目录。
* lib：库文件目录。
	* libfreeswitch.so
* log：日志文件及CDR话单目录。
	* cdr-csv：CSV话单目录。
		* Master.csv：话单汇总文件。轮替的文件加上.2006-01-02-15-04-05格式的后缀。
		* *.csv：每个独立分机一个话单文件。轮替的文件加上.2006-01-02-15-04-05格式的后缀。
	* cdr-pg-csv：话单写入ProstgreSQL失败时保存的本地文件话单目录。
	* freeswitch.log：日志文件。
	* freeswitch.xml.fsxml：完整的XML文档的镜像。
	* xml_cdr：XML话单目录。当话单POST请求失败时也会将话单保存至此。
* mod：可加载的模块目录。
* recordings：录音文件目录。record应用程序默认的存放路径。
* run：运行目录。
	* freeswitch.pid：存放FreeSWITCH运行时的PID。
* scripts：嵌入式语言写的脚本的目录。lua、luarun、jsrun等应用程序默认的查找路径。
* sounds：声音文件目录。playback应用程序默认的查找路径。
	* en：英语提示音文件目录。
		* us：美式英语提示音文件目录。
			* callie：Callie（人名）录制的嗓音文件目录。每个子目录为某一类声音文件的目录。
				* ascii：ASCII字符提示音目录。每个子目录为某一采样率（Hz）的声音文件目录。
					* 8000：8000Hz语音的目录。
				* base256
				* conference
				* currency
				* digits：数字语音的目录。
				* directory
				* ivr：IVR提示音目录。
				* misc
				* phonetic-ascii
				* time
				* voicemail
				* zrtp
	* music：MOH保持音乐目录。
* storage：语音信箱的录音文件及从其他HTTP服务器下载的语音文件缓存目录。

# 命令行

## freeswitch

freeswitch可使用以下命令行选项：

* -c：启动到控制台，默认。
* -core：出错时进行内核转储。
* -g GROUP：启动进程的系统用户组。
* -h：查看帮助。
* -heavy-timer：更精确的时钟。可能会更精确，但对系统要求更高。
* -help：查看帮助。
* -lp：开启低优先级设置。
* -nc：启动到后台模式，没有控制台。
* -ncwait：后台模式，等待系统完全初始化完毕之后再退出父进程，隐含-nc选项。
* -nf：不允许fork子进程。
* -nocal：关闭时钟核准。FreeSWITCH理想的运行环境是1000Hz的内核时钟。如果你的内核时钟小于1000Hz或在虚拟机上，可以尝试关闭该选项。
* -nonat：如果路由器支持uPnP或NAT-PMP，则FreeSWITCH可以自动解决NAT穿透问题。如果路由器不支持，则该选项可以使启动更快。
* -nort：关闭实时时钟。
* -nosql：不使用SQL，show channels类的命令将不能显示结果。
* -np：开启普通优先级设置。
* -rp：开启高优先级（实时）设置。
* -stop：关闭FreeSWITCH，它会在run目录中查找PID文件。
* -u USER：启动进程的系统用户。
* -vg：在valgrind下运行，调试内存泄露时使用。
* -version：查看版本信息。

以及：

* -base DIR：指定其他的基准目录，在配置文件中使用$${base}。
* -certs DIR：指定其他SSL证书目录。
* -conf DIR：指定其他的配置文件所在目录，需与-log、-db合用。
* -db DIR：指定其他数据库目录。
* -grammar DIR：指定其他语法目录。
* -htdocs DIR：指定其他HTTP根目录。
* -log DIR：指定其他的日志目录。
* -mod DIR：指定其他模块目录。
* -recordings DIR：指定其他录音目录。
* -run DIR：指定其他存放PID文件的运行目录。
* -scripts DIR：指定其他脚本目录。
* -sounds DIR：指定其他声音文件目录。
* -storage DIR：指定其他存储目录（语音信箱等）。
* -temp DIR：指定其他临时文件目录。

## fs_cli

配置文件为~/.fs_cli_conf。

* -P PORT：连接的端口。
* -x API [ARG[ ...]]：执行API后退出。

进入控制台可执行以下命令：

* /bye：退出。等同于Ctrl+D。
* /event plain|json|xml EVENTTYPE [SUBCLASS]：订阅事件。
* /exit：退出。等同于Ctrl+D。
* /filter：过滤事件。
* /help：查看帮助。
* /log LEVEL：设置日志级别。
* /nixevent：除了特定一种外，开启所有事件。
* /noevents：关闭事件接收。
* /nolog：关闭日志。
* /quit：退出。等同于Ctrl+D。

# 端口

* TCP 5060：SIP internal默认端口。
* TCP 5080：SIP external默认端口。
* TCP 8021：Event Socket默认端口。
* UDP 5060：SIP internal默认端口。
* UDP 5080：SIP external默认端口。
* TCP 8080：FreeSWITCH Portal默认端口。

# 日志

日志级别可使用数值或字符串表示。数值越大显示越详细，字符串不区分大小写：

* 0 - EMERG/CONSOLE
* 1 - ALERT
* 2 - CRIT
* 3 - ERR
* 4 - WARNING
* 5 - NOTICE
* 6 - INFO
* 7 - DEBUG

# 信号

* SIGHUP：重新解析全局变量，并执行日志轮转等其它事情。

# 数据库

## core.db

`show`命令的大部分内容基于这些表。

包含的表有：

* aliases：别名表，用于存储命令行别名。
* basic_calls：一个视图，基于channels和calls表，提供基本的呼叫信息。
* calls：呼叫表，在bridge的呼叫中，用于关联Channel表中的两条腿。
* channels：存储所有当前的Channel。
* complete：存储所有Tab Complete数据。
* detailed_calls：一个视图，基于channels和calls，提供详细的呼叫信息。
* interfaces：存储所有的Interface。
* nat：存储当前的NAT映射关系。
* recovery：在使用系统恢复功能时，该表存储所有呼叫的详细信息。
* registrations：存储注册用户的信息。与sofia_reg_PROFILE.db的sip_registrations表中有些数据是重复的。由于不止SIP用户需要注册，其它模块的用户也可能需要注册，此处存储的是统一的注册信息。
* tasks：当前的任务表，如heartbeat（心跳）、检测IP地址变化等。

## sofia_reg_PROFILE.db

每个Profile都使用一个单独的数据库（PROFILE替换为实际的Profile名字）。

包含的表有：

* sip_authentication：SIP认证相关。
* sip_dialogs：SIP对话相关。
* sip_presence：SIP呈现相关。
* sip_registrations：SIP用户的注册信息。
* sip_shared_appearance_dialogs：SIP SLA相关，记录当前的Dialog。
* sip_shared_appearance_subscriptions：SIP SLA相关，记录订阅信息。
* sip_subscriptions：SIP订阅相关。

# 接口

FreeSWITCH在核心（Core）中提供了很多抽象的接口，这些接口对同类型的逻辑或功能实体进行了抽象，但没有具体实现，具体的实现一般由外围的模块负责，核心层通过回调（钩子）方式调用具体的实现代码。

* 应用程序（Application，APP）：mod_dptools模块提供了系统中大部分的APP。
* 语音识别及语音合成（ASR/TTS）：支持语音自动识别（ASR）及文本/语音转换（TTS）。
* 聊天计划（Chatplan）：类似Dialplan，不同的是Chatplan主要对文本消息进行路由。它是在mod_sms中实现的。
* 编解码器（Codec）：G711（即PCM）编解码是在核心中实现的，其他的编解码一般都由独立的模块实现。
* 数据库（DB）：在核心中实现。
* 拨号计划（Dialplan）：Dialplan主要提供查找电话路由的功能，默认的Dialplan由mod_dialplan_xml提供。
* 嵌入式语言（Embeded Language）：通过swig可支持多种嵌入式语言进而控制呼叫流程。
* 终点（Endpoint）：Endpoint是终结FreeSWITCH的地方，也就是说在往外走就超出FreeSWITCH的控制。它主要为实现一些跟通话相关的互联协议的接口，如SIP、H323、Google Talk等。
* 事件消费者（Event Consumer）：通过Event Socket可以使用任何其他语音（只要支持Socket），通过TCP Socket可控制呼叫流程、扩展FreeSWITCH的功能。
* 格式、文件接口（Format，File Interface）：支持不同格式的声音文件回放、录音。
* 命令接口（FSAPI）：即FreeSWITCH API，简称API，它是一种对外的命令接口，它的原理非常简单——输入一个简单的字符串（以空格分隔），该字符串由模块的内部函数处理，然后得到一个输出。系统中大部分的API都是由mod_commands模块提供。
* 日志（Logger）：日志可以写到控制台、日志文件、系统日志（syslog）以及远程的日志服务器。实现日志功能的模块有mod_console、mod_logfile、mod_syslog等。
* 分词短语（Say）
* 定时器（Timer）：实时的话音通话需要非常准确的定时器，FreeSWITCH最理想的工作时钟频率是1000Hz。
* XML接口（XML Interface）：对XML的解析和访问是在核心中实现的，但对XML的应用和扩展都是在外部模块中完成的。

# 拨号计划

系统支持的拨号计划（Dialplan）及对应的模块：

* asterisk：mod_dialplan_asterisk。
* enum：mod_enum。
* inline：mod_dptools。
* LUA：mod_lua。
* XML：mod_dialplan_xml。

# 呼叫字符串

呼叫字符串格式为（此处使用`<>`引起表示内容可选，`/`表示使用左侧或右侧内容，`...`表示重复之前内容。）：

```
<{GLOBALVAR=VALUE<,...>}...><[LOVALVAR=VALUE<,...>]>DIALSTR<,/|...>
```

`{}`括起的通道变量是全局的，作用于呼叫字符串中每一条腿；`[]`括起的通道变量为局部通道变量，只作用于呼叫字符串中靠近它的某一条腿。通道变量VALUE中的逗号需要使用`\`转义，或在VALUE的开头使用`^^C`来将分隔符替换为指定的字符C。DIALSTR使用`,`隔开表示同振，使用`|`隔开表示顺振。

DIALSTR可为（一般来说，每种Endpoint都会提供相应的呼叫字符串，每一种呼叫字符串的类型都属于一个Endpoint Interface）：

* freetdm/SPAN/CHANNEL/USERNAME：mod_freetdm呼叫字符串。CHANNEL为a表示从小到大选择一个空闲时隙，为A表示从大到小选择一个空闲时隙。
* h323/USERNAME@IP：mod_h323的h323呼叫字符串。
* loopback/USERNAME：loopback呼叫字符串。
* opal/h323:USERNAME@IP：mod_opal的h323呼叫字符串。
* rtmp/UUID/USERNAME@IP[:PORT]：rtmp呼叫字符串。
* sofia/PROFILE/[sip:]USERNAME[@IP[:PORT]]：指定PROFILE的用户。
* sofia/gateway/GATEWAY/USERNAME：网关用户。
* user/USERNAME[@DOMAIN]：本地用户。

示例：

```
{origination_caller_id_name=test}{effective_caller_id_name=haha}user/1000
{origination_caller_id_name=test}{effective_caller_id_name=haha}user/1000|sofia/internal/1001
{origination_caller_id_name=test}[effective_caller_id_name=haha]user/1000,[effective_caller_id_name=hehe]sofia/internal/1001
```

# 挂机原因

官方文档见：[https://freeswitch.org/confluence/display/FREESWITCH/Hangup+Cause+Code+Table](https://freeswitch.org/confluence/display/FREESWITCH/Hangup+Cause+Code+Table)

挂机原因有：

* CALL_REJECTED：拒接。
* NO_ANSWER：无应答。
* NO_ROUTE_DESTINATION：空号或无法路由。
* NORMAL_CLEARING：呼叫正常释放。
* NORMAL_TEMPORARY_FAILURE：普通临时失败。
* USER_BUSY：用户忙。
* USER_NOT_REGISTERED：用户未注册。

# Channel状态机

```
                          Hunting
NEW --> INIT --> ROUTING ---------> EXECUTE --> HANAGUP --> REPORTIG --> DESTROY
                    ^                  |
                    |     Transfer     |
                    +------------------+
```

再Hunting时，只解析Dialplan，并不执行任何Action，而是将所有满足条件的Action都放到一个列表中，待呼叫流程进行到EXECUTE阶段时，再依次执行列表中的Action。

# 事件

* API：执行API。
* BACKGROUND_JOB：使用bgapi后台API执行完成。
* CHANNEL_ANSWER：Channel被应答。两条腿均会产生该事件。
* CHANNEL_BRIDGE：Channel与另一个Channel桥接成功。只在主动发起bridge的那条腿上产生该事件。
* CHANNEL_CALLSTATE
* CHANNEL_CREATE：来话或去话产生。
* CHANNEL_DESTROY：Channel完全释放。
* CHANNEL_EXECUTE：一个APP开始执行。
* CHANNEL_EXECUTE_COMPLETE：一个APP执行完成。
* CHANNEL_HANGUP：挂机。
* CHANNEL_HANGUP_COMPLETE：挂机完成。信息更详细。
* CHANNEL_PARK：通话挂起。
* CHANNEL_PROGRESS：收到100或180消息。
* CHANNEL_PROGRESS_MEDIA：在应答之前收到早期媒体，如收到183消息。
* CUSTOM：自定义。已约定的Subclass有：
	* conference::maintenance
	* fifo:info
	* sofia::register
	* sofia::unregister
* DTMF：双音多频按键信息。
* HEARTBEAT：心跳。每20秒产生一次。
* MODULE_LOAD：模块加载。
* MODULE_UNLOAD：模块卸载。
* PLAYBACK_START：放音开始。
* PLAYBACK_STOP：放音结束。
* RECORD_START：录音开始。
* RECORD_STOP：录音结束。
* SHUTDOWN：系统关闭。
* STARTUP：系统启动。

Channel事件发生顺序：

1. CHANNEL_CREATE
1. CHANNEL_PROGRESS
1. CHANNEL_PROGRESS_MEDIA
1. CHANNEL_ANSWER
1. CHANNEL_BRIDGE
1. CHANNEL_EXECUTE
1. CHANNEL_EXECUTE_COMPLETE
1. CHANNEL_HANGUP
1. CHANNEL_HANGUP_COMPLETE
1. CHANNEL_DESTROY

# 媒体协商

在FreeSWITCH中，编码名称的格式为`CODEC@HZh@PTIMEi`。h代表Hz，即采样频率；i代表ptime，即打包间隔。CODEC、HZ、PTIME为实际的值。

FreeSWITCH支持的协商策略有：

* generous（普通的）：优先考虑客户端的编码，默认的协商策略。
* greedy（贪婪的）：优先考虑服务器的编码。
* scorooge（吝啬的）：优先考虑服务器的编码，且强制使用服务器的采样率。

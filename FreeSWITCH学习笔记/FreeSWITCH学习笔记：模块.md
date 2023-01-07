本文更新于2022-12-12，使用FreeSWITCH 1.10.7。

[TOC]

官方文档见：[https://freeswitch.org/confluence/display/FREESWITCH/Modules](https://freeswitch.org/confluence/display/FREESWITCH/Modules)

# applications

该目录下的模块提供了大部分应用的功能，有的模块因实现了多种Interface而不好归类，这些模块也会存在该目录中。

* mod_abstraction：用于创建新的API命令。新的命令可以基于原有的API创建，相当于创建原有命令的别名或快捷方式。
* mod_avmd：avmd是Advanced Voice Mail Detection的缩写，即高级语音邮件检测。它是mod_vmd的高级版。详见mod_vmd。
* mod_blacklist：黑名单功能。它提供了一些通过Dialplan来添加、删除以及检查黑名单的方法。
* mod_callcenter：一个比较强大的呼叫中心类应用。
* mod_cidlookup：用于主叫号码查询，即可根据主叫号码从本地数据库或网络上查询到主叫的名字。
* mod_cluechoo：该模块是一个例子模块，主要是教大家怎么写模块。另外，它还带了一个好玩的例子，如在命令行上执行cluechoo命令，在屏幕上将会看到开过一个小火车。
* mod_commands：提供了大部分系统的命令API。
* mod_conference：多人语音即视频会议。
* mod_curl：使用libcurl作为一个HTTP客户端向Web服务器发送请求，也可以得到返回的结果。
* mod_db：该模块提供一组接口，用于使用API或APP对数据库表进行增删改查等操作。
* mod_directory：该模块用于按姓名呼叫用户。如果不知道用户的分机号，但知道用户的名字，则可以通过输入该用户名字的前几位进行拨叫。
* mod_distributor：如果需要通过多个网关出局，则该模块可以帮助将呼叫根据一定的比例分发到不同的网关。
* mod_dptools：提供了大部分系统的APP。
* mod_easyroute：用于根据号码路由，对大规模的DID比较有用。
* mod_enum：通过ENUM查询可以根据E164号码找到用户的SIP地址。该模块也提供了一个enum Dialplan。
* mod_esf：esf是Extended SIP Functionality的缩写，即扩展的SIP功能。它提供通过Multicast方法实现组拨的功能。
* mod_esl：该模块用于实现两个FreeSWITCH间的ESL对接，即一个FreeSWITCH可以作为另一个FreeSWITCH的ESL客户端访问它。
* mod_expr：提供expr表达式计算。
* mod_fifo：一个先进先出队列，可以用于简单的呼叫中心排队。
* mod_fsk：收发FSK（Frequency-shift keying，频移键控）信息。
* mod_fsv：fsv是FreeSWITCH Video的缩写，它使用了一种私有的格式来进行视频录制，可以支持任何编码的视频格式。它只存储原始的RTP包，对视频流不进行任何处理。音频轨道用L16编码保存。
* mod_hash：用于操作系统内部的哈希表。可以存储一些简单的数据。
* mod_httpapi：一种HTTP格式的API接口，可以通过HTTP方式写IVR。
* mod_http_cache：通过HTTP方式上传和下载文件，并可以进行本地缓存。
* mod_ladspa：使用ladspa库对声音进行处理，可以让声音更好听。
* mod_lcr：LCR是Least Cost Routing的缩写，即最省钱的路由。它会根据数据库配置的路由信息和费率找到最省钱的路由。
* mod_limit：用于系统资源限制。
* mod_memcache：与Memcache交互，类似于把mod_hash的数据存到远程的Memcache中。
* mod_mongo：与MongoDB交互，类似mod_memcache。
* mod_mp4：提供MP4文件的播放支持。
* mod_nibblebill：一些简单的计费功能，可用于预付费和电话卡类的应用。
* mod_oreka：使用oreka进行录音。oreka是一款开源的录音软件。
* mod_osp：通过Open Settlement Protocol查找路由或上报CDR。
* mod_rad_auth：使用radius服务器进行鉴权。
* mod_random：通过访问/dev/hwrandom设备影响随机数的熵。
* mod_redis：与redis服务器交互，类似于mod_memcache。
* mod_rss：访问RSS（Really Simple Syndication，简易信息聚合）数据。
* mod_skel：一个模块的例子框架。
* mod_sms：处理文本消息，如收发SIP MESSAGE消息等。它实现了消息路由（Chatplan），类似于Dialplan。
* mod_snapshot：可以截取一段声音的快照。
* mod_snipe_hunt：一个简单的例子模块。
* mod_snom：用于snom话机的一些特性。
* mod_sonar：该模块类似于一个真正的声呐。首先你可以在远端启动一个服务器，其能对来话执行echo APP。然后在本地的FreeSWITCH上产生一些铃声，发送到远端的服务器上再反射回来，然后使用VAD检测功能可以检测这些铃声，从而可以在某种程度上确定网络的质量。
* mod_soundtouch：使用soundtouch库对声音进行处理，可以增加音效。
* mod_spandsp：使用spandsp支持一些语音编码及传真功能。
* mod_spy：用于监视某个话机，当该话机有通话时，本机振铃同时可以监听。
* mod_stress：使用快速傅里叶变换（Fast Fourier Transform，FFT）检测重音。
* mod_translate：通过既定的规则对号码进行翻译。
* mod_valet_parking：电话停靠。类似于泊车，有来电时可以将来电停靠在某个泊位上，然后通知某人拨打指定的号码将来话接走。
* mod_vmd：提供Voicemail声音检测。在国外，好多电话都有自动应答功能，如“您好，主人不在家，请留言”。使用该模块可检测到这种声音，应用程序在自动外呼时就可以根据它的结果判断是人工接听的还是机器接听的。
* mod_voicemail：语音邮箱。
* mod_voicemail_ivr：带IVR导航的语音邮箱。

# ars_tts

该目录下的模块提供自动语音识别及语音合成的功能。

* mod_cepstral：使用Cepstral语音库支持TTS。
* mod_flite：使用Festival Lite库支持TTS。只支持英文。
* mod_pocketsphinx：使用pocketsphinx库支持语音识别。
* mod_tts_commandline：通过命令行程序使用TTS。
* mod_unimrcp：通过uniMRCP协议与其他ASR/TTS产品对接。uniMRCP是一个标准的协议，很多语音产品都支持它。

# codecs

该目录下的模块处理各种类型的音频、视频编码。

* mod_amr
* mod_amrwb
* mod_b64：Base64编码，可以传输任意数据。
* mod_bv：BroadVoice编码。
* mod_celt
* mod_codec2
* mod_com_g729：商业的G.729编码，可转码，需要许可证。
* mod_dahdi_codec：通过DAHDI库提供的编码。
* mod_g723_1
* mod_g729：只支持透传。
* mod_h26x：提供H261、H263、H264等视频编码。
* mod_ilbc
* mod_isac
* mod_mp4v
* mod_opus
* mod_sangoma_codec：通过硬件板卡支持包括G.729、iLBC等多种编码。
* mod_silk
* mod_siren
* mod_skel_codec：例子模块。
* mod_speex
* mod_throra
* mod_voipcodecs
* mod_vp8

# dialplans

该目录下的模块提供拨号计划。

* mod_dialplan_asterisk：类似于Asterisk格式的拨号计划。
* mod_dialplan_directory：通过LDAP查询拨号计划。
* mod_dialplan_xml：XML拨号计划。

# directories

该目录下的模块提供目录服务。

* mod_ldap：通过LDAP提供目录服务。

# endpoints

该目录下的模块提供各种Endpoint的实现。

* mod_alsa：使用ALSA声卡。
* mod_dingaling：连接Google Talk。
* mod_freetdm：支持E1接口的硬件板块。
* mod_gsmopen：使用无线上网卡上的GSM接口或使用手机上的GSM接口给外界发短信或与外界通话。
* mod_h323：连接H.323设备，使用OpenH323库实现。依赖ptlib。
* mod_khomp：使用KHOMP板卡。
* mod_loopback：提供loopback回环接口。
* mod_opal：连接H.323设备，使用OPAL库实现。依赖ptlib。
* mod_portaudio：通过Portaudio库支持本地声卡。
* mod_rtmp：通过Adobe的RTMP协议与浏览器中的Flash电话进行通话。
* mod_skinny：支持思科的SCCP协议话机。
* mod_skypopen：与Skype互通。
* mod_sofia：SIP模块。为对Sofia-SIP库的黏合和封装。

在mod_dptools模块中，实现了一些常用的“假”的Endpoint Interface。之所以说是“假”的，是因为它们并没有像mod_sofia那样既有底层的协议驱动，又有媒体收发处理，而是为了简化某些操作，或者为了在某些特殊的情况下使用一致的命令或接口而实现的。比如，我们常用的user就是一个Endpoint。一般来说，一个Endpoint都会提供一个用于外呼的呼叫字符串。

# event_handlers

该目录下的模块提供事件处理功能。

* mod_cdr_csv：CSV格式的话单。也可以在话单文件中嵌入SQL语句。
* mod_cdr_mongodb：将话单写入MongoDB。
* mod_cdr_pg_csv：将话单写入PostgreSQL数据库。
* mod_cdr_sqlite：将话单写入SQLite数据库。
* mod_erlang_event：对接Erlang节点，提供事件、日志、命令接口等。作为一个隐藏的Erlang节点，也实现了类似ESL中的Inbound和Outbound通信模式。
* mod_event_multicast：将事件通过组播方式发出去。
* mod_event_socket：通过ESL库与第三方的接口。
* mod_event_test：测试。
* mod_event_zmq：使用ZeroMQ协议与第三方对接。
* mod_format_cdr：XML或JSON格式的话单。
* mod_json_cdr：JSON格式的CDR。
* mod_radius_cdr：将CDR写入Radius服务器。
* mod_rayo：Rayo支持。
* mod_snmp：SNMP网管接口。

# formats

该目录下的模块提供各种格式的文件。

* mod_local_stream：从本地文件生成媒体流。
* mod_native_file：支持原生文件读写，如直接读写.PCMU或.G729格式的文件。
* mod_portaudio_stream：使用portaudio库从本地声卡生成媒体流。
* mod_shell_stream：从Shell命令中生成媒体流。
* mod_shout：提供MP3文件格式支持和远程Shoutcast服务器支持。
* mod_sndfile：使用libsndfile支持大多数的声音文件。
* mod_ssml：SSML格式的文件支持。
* mod_tone_stream：生成铃流音。
* mod_vlc：使用libvlc提供媒体文件格式的支持。

# languages

该目录下的模块提供各种嵌入式编码语言接口。

* mod_java：Java的接口。
* mod_lua：Lua的接口。
* mod_managed：微软平台的语言接口，如C#、VB.NET等。
* mod_perl：Perl的接口。
* mod_python：Python的接口。
* mod_spidermonkey：JavaScript的接口。
* mod_v8：使用V8 JavaScript引擎实现的JavaScript模块，可替换mod_spidermonkey。
* mod_yaml：Yaml的接口。

# loggers

该目录下的模块提供与日志相关的功能。

* mod_console：控制台日志。
* mod_logfile：日志文件。
* mod_syslog：将日志写到Syslog。

# Say

该目录下的模块提供多语种接口。

* mod_say_de：德语。
* mod_say_en：英语。
* mod_say_es：西班牙语。
* mod_say_fa：波斯语。
* mod_say_fr：法语。
* mod_say_he：希伯来语。
* mod_say_hr：克罗地亚语。
* mod_say_hu：匈牙利语。
* mod_say_it：意大利语。
* mod_say_ja：日语。
* mod_say_nl：荷兰语。
* mod_say_pl：波兰语。
* mod_say_pt：葡萄牙语。
* mod_say_ru：俄语。
* mod_say_th：泰语。
* mod_say_zh：汉语。

# timers

该目录下的模块提供与定时器相关的功能。

* mod_posix_timer：Posix定时器。
* mod_timerfd：使用Linux内核中的timerfd定时器。

# xml_int

该目录下的模块提供XML接口。

* mod_xml_cdr：使用XML格式写CDR。
* mod_xml_curl：从远程HTTP服务器获取XML配置。
* mod_xml_ldap：从远程LDAP服务器获取XML配置。
* mod_xml_radius：从远程Radius服务器获取XML配置。
* mod_xml_rpc：使用XML RPC接口与第三方交互，提供命令、日志及事件接口等。其本身也是一个简单的Web服务器，并提供一个简单的Web管理界面。
* mod_xml_scgi：使用SCGI协议获取XML配置。

本文更新于2022-12-15，使用FreeSWITCH 1.10.7。

官方文档见：[https://freeswitch.org/confluence/display/FREESWITCH/mod_dptools](https://freeswitch.org/confluence/display/FREESWITCH/mod_dptools)

**说明：下文中，部分大写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

APP又称拨号计划工具（Dialplan Tools）。可使用以下APP（括号中为参数列表，参数如有空格需用`''`引起）：

* answer([is_conference])：给主叫回送应答消息（200）。FreeSWITCH做被叫时，如果想给主叫放音，必需应答后才可以。`is_conference`表示支持RFC4579视频会议控制标准。
* att_xfer(CALLURL)：协商转。
* bind_meta_app(NUM a|b s APP::ARG[ ...])：绑定DTMF，按键后执行APP。NUM为按键（需先按*键激活功能），可指定a腿或b腿，s表示在收到按键的相同的腿上执行。
* bridge(CALLURL)：桥接至另一条腿（b-leg）。其是阻塞的，直到b-leg释放后才继续往下走。
* callcenter(QUEUE)：将呼叫转到呼叫中心。
* conference(CONFERENCE[@PROFILE][+PIN][+flags{FLAG1|FLAG2}])：将来话转入会议。`@`之前为会议的名字，之后为会议的参数。竖线为字面值，不表示“或”的意思。
* curl(URL)：与HTTP服务器交互。
* delay_echo(MS)：延迟若干毫秒回声。
* detect_speech(ASRENGINE GRAMMERFILENAME GRAMMERFILENAME|pause|resume|params MODELFILENAME DICTIONARYFILENAME)：语音识别开启/暂停/重启/设置参数。文件名可没有扩展名。
* directory(default DOMAINNAME default)：按姓名呼叫用户。当系统提示输入名字时，通过输入用户名字的前几位进行拨叫。
* echo：回音。
* endless_playback(FILEPATH)：无限循环播放声音文件。
* erlang(ERLANGCALL NODE)：连接外部Erlang节点。
* execute_extension(EXTENSION DIALPLAN CONTEXT)：临时执行其他Dialplan下的context的extension，不会重新进入ROUTING阶段。
* export([nolocal:]VAR=VALUE)：设置变量，并会使用VAR设置特殊的变量export_vars。如使用nolocal，则只设置到b-leg上；否则，设置到两条腿上。
* fifo(LIST in|LIST out [wait|nowait])：从一个先进先出队列中进行呼叫停泊/取回。使用out时可指定wait（offhook坐席）或nowait（onhook坐席），默认为wait。
* hangup：挂断通话。
* hash(insert/HASH/KEY/VALUE|delete/HASH/KEY|select/HASH/KEY)：操作内存中哈希表数据结构的键值对。
* hold：将通话挂起，并播放MOH保持音乐。
* info：在日志中打印所有通道变量。
* intercept([-bleg] CHANNELUUID)：代接。
* ivr(IVR)：进入IVR菜单。
* javascript(JSFILEPATH)：执行JavaScript脚本。
* log(LEVEL MESSAGE)：打印日志。（originate命令执行此APP时，不知道怎么设置LEVEL，括号里面都当成MESSAGE。）
* loop_playback(+N FILEPATH)：循环播放声音文件若干次。
* lua(LUAFILEPATH [ARG [ ...]])：执行Lua脚本。如为相对路径，则在安装目录的scripts目录下。
* park：将通话挂起，对端听不到任何声音。
* phrase(PHRASE[,INPUT])：播放Phrase宏。
* play_and_detect_speect(MUSIC detect:ASRENGINE GRAMMARFILENAME)：播放一段声音并进行语音识别。文件名可没有扩展名。
* play_and_get_digits MINDIGITS MAXDIGITS MAXATTEMPTS TIMEOUTMS TERMINATORS MUSIC INVALIDMUSIC VAR REGEX [INTERDIGITTIMEOUTMS ['FAILEXTENSION [FAILDIALPLAN [FAILCONTEXT]]']]：播放声音并等待接收DTMF按键，与`read`类似，但更强大。MINDIGITS为最少收号位数，MAXDIGITS为最大收号位数，MAXATTEMPTS为重试次数，TIMEOUTMS为收齐全部位数的超时毫秒数，TERMINATORS为收号小于MINDIGITS位时的提前结束按键（可多个，通常是#），MUSIC为播放的提示音（用法见`playback`的MUSIC），INVALIDMUSIC为输入错误时播放的提示音（用法见`playback`的MUSIC），VAR为收到用户按键后存放的变量名，REGEX为收号后进行匹配的正则表达式，INTERDIGITTIMEOUTMS为位间间隔毫秒数，FAILEXTENSION为若最后输入错误时转到的extension，FAILDIALPLAN为输入错误时转到的Dialplan，FAILCONTEXT为输入错误时转到的Dialplan Context。
* play_fsv(FILEPATH)：播放录像。
* playback(MUSIC)：播放声音。MUSIC可为：
	* FILEPATH，文件名。不带扩展名会查找与本Channel编码一致的原生文件。
	* file_string://MUSIC[!...]：串联多个声音。
	* http://PATH：HTTP接口。可将远程文件缓存到本地。
	* local_stream://LOCALSTREAM，本地文件流，每个流在系统中只有一个实例。LOCALSTREAM为conf/autoload_configs/local_stream.cof.xml中定义的名字。
	* phrase:PHRASE[:INPUT]：Phrase宏。
	* say:[TTSENGINE:TTSVOICE:]TEXT：使用TTS根据文本内容放音。TTSENGINE和对应TTSVOICE用法见`speak`。
	* shout://SERVER/PATH：SHOUTcast协议，播放服务器上的文件（可为MP3文件）。
	* silence_stream://MS[,CN]，静音流，MS等于0为无限长，CN为舒适噪音，范围为1至10000，通常使用600至1400，值越小噪声越大，-1则产生绝对静音。
	* tone_stream://%(ONMS,OFFMS,HZ1[,HZ2])[;%(ONMS,OFFMS,HZ1[,HZ2])];loops=N]：铃流，使用TGML语音生成信号音。ONMS为通毫秒数，OFFMS为断毫秒数，HZ1、HZ2为交替的频率，N为循环次数，-1为无限循环。
	* unimrcp:SERVER：MRCP协议。
	* vlc://FILEPATH|HTTPURL：使用libvlc播放。
* pre_answer：给对方回复消息（183），后续的`playback`之类的action将作为早期媒体给对方发送过去。
* python(PYFILEPATH)：执行Python脚本。文件路径为相对于Python的查找目录（如/usr/lib/python2.7/dist-packages/）。
* read MINDIGITS MAXDIGITS MUSIC VAR INTERDIGITTIMEOUTMS TERMINATORS：播放声音并等待接收DTMF按键。MINDIGITS为最少收号位数，MAXDIGITS为最大收号位数，MUSIC为播放的提示音（用法见`playback`的MUSIC），VAR为收到用户按键后存放的变量名，INTERDIGITTIMEOUTMS为等待每一位的输入超时毫秒数，TERMINATORS为收号小于MINDIGITS位时的提前结束按键（可多个，通常是#）。
* record(FILEPATH)：对当前Channel进行录音。阻塞，只用于单腿的呼叫。如不指定扩展名，则以原生格式录音。
* record_fsv(FILEPATH)：录像，并将视频回显。只用于单腿的呼叫。
* record_session(FILEPATH)：对当前Channel进行录音。非阻塞，可用于单腿的呼叫也可用于桥接的呼叫。如不指定扩展名，则以原生格式录音。
* ring_ready：给对方回复消息（180），通知对方可以振铃。
* rxfax(FILENAME)：收传真。文件格式为TIFF。
* say(LANGUAGE SAYTYPE SAYMETHOD TEXT)：使用Say接口将预先录好的声音对特定的内容放音，支持多语言。LANGUAGE为语言，SAYTYPE为播放的数据类型，SAYMETHOD为播放的读法，TEXT为播放的内容。

	SAYTYPE可为：
	* ACCOUNT_NUMBER：账号。
	* CURRENCY：货币。
	* CURRENT_DATE：当前日期。
	* CURRENT_DATE_TIME：当前日期时间。
	* CURRENT_TIME：当前时间。
	* EMAIL_ADDRESS：Email地址。
	* IP_ADDRESS：IP地址。
	* ITEMS：项目。
	* MESSAGES：消息。
	* NAME_PHONETIC：姓名拼写。
	* NAME_SPELLED：姓名。
	* NUMBER：数字。
	* PERSONS：人名。
	* POSTAL_ADDRESS：邮寄地址。
	* SHORT_DATE_TIME：短日期时间。
	* TELEPHONE_EXTENSION：分机号。
	* TELEPHONE_NUMBER：手机号码。
	* TIME_MEASUREMENT：时间。
	* URL：网址。

	SAYMETHOD可为（并不是所有语言都实现了全部读法）：
	* COUNTED：以序数词方式读出。
	* ITERATED：逐个数字读出。
	* PRONOUNCED：以基数词方式读出。
* sched_hangup(+SECOND)：若干秒后挂断通话。
* set(VAR=VALUE)：设置通道变量到当前Channel（a-leg）上。如VALUE为`_undef_`则取消VAR的赋值。
* sleep(MS)：暂停若干毫秒。
* socket(IP:PORT [async] [full])：使用Event Socket的外连模式连接外部服务器。async表示异步执行，默认是同步执行。full表示外部应用可以使用全部的API，默认只有少量API是有效的。
* speak([TTSENGINE|TTSVOICE|]TEXT)：使用TTS播放声音。此参数使用竖线隔开各字段。可用的TTSENGINE和对应TTSVOICE有：
	* flite：awb、kal、rms、slt。
	* tts_commandline
* start_dtmf：打开带内DTMF检测。
* transfer([-bleg] USERNAME DIALPLAN CONTEXT)：盲转。将当前通话重新转接到ROUTING阶段，重新去Dialplan中进行路由，呼叫USERNAME。
* txfax(FILENAME)：发传真。文件格式为TIFF。
* unset(VAR)：取消变量的赋值。
* verbose_events：令事件包含所有variable_开头的字段。
* voicemail(CONTEXT DOMAINNAME EXTENSION)：语音信箱。

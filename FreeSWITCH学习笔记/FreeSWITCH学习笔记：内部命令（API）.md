本文更新于2022-12-14，使用FreeSWITCH 1.10.7。

官方文档见：[https://freeswitch.org/confluence/display/FREESWITCH/mod_commands](https://freeswitch.org/confluence/display/FREESWITCH/mod_commands)

**说明：下文中，部分大写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

在freeswitch或fs_cli可执行以下内部命令：

（输入制表符可补全命令，输入空格再输入制表符可补全子命令。只输入命令不输入参数，或输入错误的参数，都会提示用法。）

* bgapi API [ARG [ ...]]：将API命令放到后台执行。
* callcenter_config agent add AGENT callback|uuid_standby：向呼叫中心添加坐席。
* callcenter_config agent list：列出呼叫中心所有坐席。
* callcenter_config agent set ATTRIBUTE AGENT VALUE：设置呼叫中心坐席的属性。
* callcenter_config queue list：列出所有呼叫中心所有队列。
* callcenter_config tire list：列出呼叫中心所有梯队。
* cdr_csv rotate：令CSV话单文件轮替。
* conference：查看会议命令的帮助。
* conference CONFERENCE agc MEMBERID|all|last|non_moderator VALUE：启用自动增益控制（Auto Gain Control）。
* conference CONFERENCE bgdial CALLURL [CALLERIDNUMBER] [CALLERIDNAME]：从会议呼出，令对方加入会议。其是非阻塞的。
* conference CONFERENCE chkrecord [FILEPATH]：检查会议录音情况。
* conference CONFERENCE clear-vid-floor：取消视频会议的floor。
* conference CONFERENCE deaf MEMBERID|all|last|non_moderator：将会议成员禁听。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE dial CALLURL [CALLERIDNUMBER] [CALLERIDNAME]：从会议呼出，令对方加入会议。其是阻塞的。
* conference CONFERENCE dtmf MEMBERID|all|last|non_moderator DIGITS：向会议成员发送DTMF。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE energy MEMBERID|all|last|non_moderator VALUE：设置会议成员的能量值（音量超过此值才能混音到会议桥中）。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE enter_sound file FILEPATH：设置会议成员进入时向其它成员播放声音的文件。
* conference CONFERENCE enter_sound none：设置会议成员进入时向其它成员播放声音为静音。
* conference CONFERENCE enter_sound off：设置会议成员进入时向其它成员播放声音为禁用。
* conference CONFERENCE enter_sound on：设置会议成员进入时向其它成员播放声音为启用。
* conference CONFERENCE exit_sound file FILEPATH：设置会议成员退出时向其它成员播放声音的文件。
* conference CONFERENCE exit_sound none：设置会议成员退出时向其它成员播放声音为静音。
* conference CONFERENCE exit_sound off：设置会议成员退出时向其它成员播放声音为禁用。
* conference CONFERENCE exit_sound on：设置会议成员退出时向其它成员播放声音为启用。
* conference CONFERENCE file_seek [+|-]VALUE MEMBERID：会议中播放声音文件的快进、快退。
* conference CONFERENCE floor MEMBERID|last：将会议成员设置为floor。没什么用。
* conference CONFERENCE get PARAM：获取会议参数的值。参数有max_members、sound_prefix、caller_id_name、caller_id_number、endconference_grace_time。
* conference CONFERENCE hup MEMBERID|all|last|non_moderator：挂断会议成员。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE kick MEMBERID|all|last|non_moderator：从会议中踢出成员。可指定所有成员、最后加入的成员、非主席成员。
* conference [CONFERENCE] list：列出会议的成员。
* conference CONFERENCE lock：锁定会议，别人无法加入。
* conference CONFERENCE mute MEMBERID|all|last|non_moderator：将会议成员禁言。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE nopin：取消会议的密码。
* conference CONFERENCE norecord [FILEPATH|all]：停止会议录音。
* conference CONFERENCE pause [FILEPATH]：暂停会议中声音文件的播放，或暂停会议录音。
* conference CONFERENCE pin PIN：设置会议的密码。
* conference CONFERENCE play FILEPATH [MEMBERID]：向会议中播放声音文件，可指定成员。
* conference CONFERENCE record FILEPATH：对会议录音。
* conference CONFERENCE recording check|pause|resume|start|stop FILEPATH|all：如在会议的Profile中设置了录音摸板，则可如此检查/暂停/恢复/开始/停止录音。
* conference CONFERENCE relate MEMBERID1[,...] MEMBERID2[,...] clear：清除会议成员前者和后者之间可否听到的关系。
* conference CONFERENCE relate MEMBERID1[,...] MEMBERID2[,...] nohear：令会议成员后者听不到前者。
* conference CONFERENCE relate MEMBERID1[,...] MEMBERID2[,...] nospeak：令会议成员后者说话前者听不到。
* conference CONFERENCE resume [FILEPATH]：恢复会议录音。
* conference CONFERENCE say TEXT：在会议中使用TTS播放文本。
* conference CONFERENCE saymember MEMBERID TEXT：在会议中仅向某个成员使用TTS播放文本。
* conference CONFERENCE set PARAM VALUE：设置会议参数的值。参数有max_members、sound_prefix、caller_id_name、caller_id_number、endconference_grace_time。
* conference CONFERENCE stop all|current|last [MEMBERID]：停止会议中声音文件的播放。可指定所有正在播放、当前正在播放、最后播放。
* conference CONFERENCE tmute MEMBERID|all|last|non_moderator：切换会议成员的禁言/不禁言状态。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE transfer OTHERCONFERENCE MEMBERID[ ...]：将会议成员转到另一个会议。
* conference CONFERENCE undeaf MEMBERID|all|last|non_moderator：取消对会议成员的禁听。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE unlock：解锁会议，允许加入。
* conference CONFERENCE unmute MEMBERID|all|last|non_moderator：取消对会议成员的禁言。可指定所有成员、最后加入的成员、非主席成员。
* conference CONFERENCE vid-floor MEMBERID|last [force]：将视频会议成员设置为持有floor，在视频会议中所有成员都看到持有floor成员的画面。如指定force则固定成员，不再根据声音大小自动切换画面。
* conference CONFERENCE volume_in MEMBERID VALUE：设置会议成员的输入音量。
* conference CONFERENCE volume_out MEMBERID VALUE：设置会议成员的输出音量。
* conference [CONFERENCE] xml_list：列出会议的成员，XML格式。
* console loglevel console|alert|crit|err|warning|notice|info|debug：设置控制台日志等级。
* curl URL：与HTTP服务器交互。
* db delete|insert|select：持久化数据库存储。
* distributor LIST：从号码连选的列表中选择一个节点名称。
* echo STR[ ...]：原样输出字符串。
* esf_page_group [MULTICASTIP MULTICASTPORT CONTROLPORT] ：发送组播RTP包。三个参数的默认值依次为：224.168.168.168、34567、6061。
* expand API [ARG [ ...]]：将`${}`和`$${}`引起的变量替换为实际值后再执行API命令。
* expr EXPR[;...]：计算表达式结果。EXPR可为：
	* 数学表达式：如1+1。
	* 函数：
		* ceil(NUMBER)：向上取整。
		* random(BEGIN,END,&SEED)：获取区间之间的随机数。
		* randomize(&SEED)：设置随机数种子。
* fifo list [LIST]：查看呼叫队列的状态。
* fifo reparse：重新解析呼叫队列的配置。
* fifo_member add LIST CALLURL：动态增加呼叫队列坐席。
* fifo_member del LIST CALLURL：动态删除呼叫队列坐席。
* fsctl crash：令FreeSWITCH崩溃。
* fsctl max_sessions [VALUE]：查看或设置呼叫最大并发数。
* fsctl sps [VALUE]：查看或设置每秒最大呼叫数。
* global_getvar VAR：查看全局变量的值。
* hash delete/HASH/KEY：删除内存中哈希表数据结构的键值对。
* hash insert/HASH/KEY/VALUE：插入内存中哈希表数据结构的键值对。
* hash select/HASH/KEY：获取内存中哈希表数据结构的键值对的值。
* help：查看帮助。
* hupall：挂断所有通话。
* jsrun JSFILEPATH：执行JavaScript脚本。
* load MOD：加载模块。
* lua LUAFILEPATH [ARG [ ...]]：执行Lua脚本。在当前线程执行，会阻塞当前线程。
* luarun LUAFILEPATH [ARG [ ...]]：执行Lua脚本。在新线程执行，不会阻塞当前线程。
* nat_map status：查看NAT端口映射状态。
* originate CALLURL EXTEN|&APP(ARG[ ...]) [DIALPLAN] [CONTEXT] [CALLERIDNAME] [CALLERIDNUMBER] [TIMEOUTSEC]：发起对CALLURL的呼叫，接听后将另一端转入Dialplan路由至EXTEN或执行APP。当使用XML Dialplan时EXTEN为另一个号码；当使用inline Dialplan时EXTEN为[m:C:]APP[:ARG][,...,APPN[:ARGN]]，如果ARG有空格则需使用单引号括起，如ARG有逗号则将分隔符替换为字符C。DIALPLAN默认为XML。inline Dialplan会忽略CONTEXT，Lua Dialplan的CONTEXT为Lua文件路径。CALLERIDNAME为来电显示的名字，CALLERIDNUMBERBER为来电显示的号码。TIMEOUTSEC为对方收到INVITE消息后不回复100 Trying的超时秒数。会阻塞并在收到媒体指示后返回（如183或200消息）。
* pa answer：接听电话。
* pa call USERNAME：呼叫用户。
* pa devlist：列出portaudio模块发现的设备。
* pa hangup：挂机。
* pa indev #N：使用第N个设备作为输入设备。
* pa looptest：echo回路测试。
* pa outdev #N：使用第N个设备作为输出设备。
* python PYFILEPATH：执行Python脚本。文件路径为相对于Python的查找目录（如/usr/lib/python2.7/dist-packages/）。
* regex STR | REGEX [| REPLACEMENT]：测试字符串STR是否匹配正则表达式REGEX，如有REPLACEMENT则使用其替换匹配的内容。REPLACEMENT可以使用`$N`或`%N`的形式引用匹配的分组。
* reload MOD：重新加载模块。
* reloadxml：重新加载XML配置文件。
* rtmp status：查看RTMP状态。
* rtmp_contact [PROFILE/]USERNAME[@DOMAIN]：返回使用RTMP的已注册用户的联系地址。
* show channels：查看正在通话的通道。
* show codec：查看可用的编解码类型及其支持模块。
* show dialplan：查看可用的Dialplan及其支持模块。
* show file：查看可用的文件类型及其支持模块。
* show nat_map：查看NAT端口映射关系。
* shutdown：停止FreeSWITCH。
* sofia global capture on|off：开启/关闭所有Profile的Homer方式抓包。
* sofia global siptrace on|off：开启/关闭所有Profile的SIP消息打印。
* sofia help：查看帮助。
* sofia loglevel all|default|tport|iptsec|nea|nta|nth_client|nth_server|nua|soa|sresolv|stun 0|1|2|3|4|5|6|7|8|9：设置Sofia底层协议栈中指定模块的日志级别。越大信息越详细。
* sofia profile PROFILE capture on|off：开启/关闭指定的Profile的Homer方式抓包。
* sofia profile PROFILE flush_inbound_reg USERNAME@DOMAIN|CALLID：将指定的Profile中指定的已注册用户清除。
* sofia profile PROFILE killgw GATEWAY：删除指定的Profile中指定的网关。
* sofia profile PROFILE rescan：刷新指定的Profile。隐含reloadxml，并不是所有配置参数都能生效。
* sofia profile PROFILE register GATEWAY：令指定的Profile中指定的网关立即向外注册。
* sofia profile PROFILE restart：重启指定的Profile。隐含reloadxml。
* sofia profile PROFILE siptrace on|off：开启/关闭指定的Profile的SIP消息打印。
* sofia profile PROFILE start：启动指定的Profile。隐含reloadxml。
* sofia profile PROFILE stop：停止指定的Profile。隐含reloadxml。
* sofia profile PROFILE unregister GATEWAY：令指定的Profile中指定的网关立即向外注销。
* sofia recover：从数据库中读出FreeSWITCH崩溃前的通话信息并恢复。
* sofia status|xmlstatus：查看Sofia状态。
* sofia status|xmlstatus gateway GATEWAY：查看指定网关的状态。
* sofia status|xmlstatus profile PROFILE：查看指定的Profile的状态。
* sofia status|xmlstatus profile PROFILE reg：查看指定的Profile所有已注册用户。
* sofia status|xmlstatus profile PROFILE reg USERNAME：查看指定的Profile中指定的已注册用户。通过SIP的Contact头过滤。
* sofia status|xmlstatus profile PROFILE user USERNAME@DOMAIN：查看指定的Profile中指定的已注册用户。
* sofia tracelevel console|alert|crit|err|warning|notice|info|debug：console只会打印到控制台，不会写入日志文件。
* sofia_contact [PROFILE/]USERNAME[@DOMAIN]：返回已注册用户的联系地址。
* sofia_count_reg USERNAME@DOMAIN：查看该用户使用多少个客户端注册。在允许多点注册的情况下会有多个。
* sofia_dig IP：返回其他服务器的地址和端口。类似DNS的dig。
* sofia_presence_data list USERNAME@DOMAIN：列出指定用户的Presence信息。
* sofia_presence_data status USERNAME@DOMAIN：列出指定用户的Presence状态。
* sofia_presence_data user_agent USERNAME@DOMAIN：列出指定用户的Presence的user agent信息。
* sofia_username_of USERNAME@DOMAIN：返回已注册用户的用户名。
* status：查看服务器状态。
* strepoch：显示当前的Unix时间戳。
* strftime [FORMAT]：将当前时间格式化显示。
* stun STUNSERVER：使用STUN服务器检测公网IP和端口。
* system CLI [ARG [...]]：调用系统命令。
* uptime：查看服务启动的秒数。
* uuid_bridge CHANNELUUID1 CHANNELUUID2：使用UUID桥接两个通话Channel。
* uuid_debug_media CHANNELUUID read|write|both|vread|vwrite|vboth on|off：打开/关闭指定通话Channel的媒体流调试信息。read为收，write为发，both为收发，v开头为可打印视频媒体流。每行输出包括以下信息：R或W表示收或发，呼叫字符串，b=表示RTP包大小（含包头），本地IP和端口，远端IP和端口，pt=表示载荷类型，ts=表示时间戳，m=表示RTP的Marker。
* uuid_kill CHANNELUUID：释放通话Channel。
* uuid_record CHANNELUUID start FILENAME：对指定的通话Channel开始录音。如不指定扩展名，则以原生格式录音。
* uuid_record CHANNELUUID stop FILENAME|all：对指定的通话Channel停止录音。如使用all则停止通话Channel的所有录音。如不指定扩展名，则以原生格式录音。
* uuid_setvar CHANNELUUID VAR VALUE：对指定的通话Channel设置通道变量。
* uuid_transfer(CHANNELUUID CALLURL|lua:LUAFILENAME [DIALPLAN [CONTEXT]])：将通话重新转移到ROUTING阶段，重新去Dialplan中进行路由。
* version：查看服务器版本。
* xml_curl debug_on：打开mod_xml_curl模块的调试。会将每次请求得到的XML文件存放到系统临时目录中。

有些命令可以使用`COMMAND help`查看帮助。

示例：

```
originate user/1000 &echo
originate user/1000 1001
originate user/1000 1001 XML
originate user/1000 echo
originate user/1000 echo inline
originate {origination_caller_id_name=test}{effective_caller_id_name=haha}user/1000 &bridge(user/1001)
```

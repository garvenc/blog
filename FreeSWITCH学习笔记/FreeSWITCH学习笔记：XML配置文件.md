本文更新于2023-11-17，使用FreeSWITCH 1.10.7。

[TOC]

FreeSWITCH默认的配置是一个SOHO PBX（家用电话小交换机）。

`X-PRE-PROCESS`标签是FreeSWITCH特有的，称为预处理指令，用于根据`data`参数设置（`set`）一些全局变量，以及将`data`参数指定的文件内容包含（`include`）到当前文件中。FreeSwitch在加载阶段只对其进行简单替换，并不进行语法分析，因此对它进行注释是没有效果的，解决办法是破坏`X-PRE-PROCESS`的定义（如将其替换为XPRE-PROCESS）。

可以`$${VAR[:OFFSET[:LENGTH]]}`形式引用全局变量，以`${VAR[:OFFSET[:LENGTH]]}`形式引用通道变量（局部变量）。OFFSET从0开始，如为负数表示从尾部开始。

# 加载顺序

配置文件的加载顺序如下：

```
freeswitch.xml
  |- vars.xml
  |- autoload_configs/*.xml
  | [autoload_configs/acl.conf.xml]
  | [autoload_configs/callcenter.conf.xml]
  | [autoload_configs/cdr_csv_conf.xml]
  | [autoload_configs/cdr_pg_csv.conf.xml]
  | [autoload_configs/conference.conf.xml]
  | [autoload_configs/distributor.conf.xml]
  | [autoload_configs/event_socket.conf.xml]
  | [autoload_configs/fifo.conf.xml]
  | [autoload_configs/ivr.conf.xml]
  |    `- ivr_menus/*.xml
  |      [ivr_menus/demo_ivr.xml]
  | [autoload_configs/local_stream.conf.xml]
  | [autoload_configs/lua.conf.xml]
  | [autoload_configs/modules.conf.xml]
  | [autoload_configs/nibblebill.conf.xml]
  | [autoload_configs/post_load_modules.conf.xml]
  | [autoload_configs/sofia.conf.xml]
  |    `- sip_profiles/*.xml
  |      [sip_profiles/external.xml]
  |         `- sip_profiles/external/*.xml
  |           [sip_profiles/external/example.xml]
  |      [sip_profiles/internal.xml]
  | [autoload_configs/switch.conf.xml]
  | [autoload_configs/tts_commandline.conf.xml]
  | [autoload_configs/xml_cdr.conf.xml]
  | [autoload_configs/xml_curl.conf.xml]
  | [autoload_configs/xml_rpc.conf.xml]
  |- dialplan/*.xml
  | [dialplan/default.xml]
  |    `- dialplan/default/*.xml
  | [dialplan/public.xml]
  |    `- dialplan/public/*.xml
  |- directory/*.xml
  | [directory/default.xml]
  |    `- directory/default/*.xml
  |      [directory/default/1000.xml]
  `- lang/en/*.xml
    [lang/en/en.xml]
       `- lang/en/demo/*.xml
         [lang/en/demo/demo-ivr.xml]
```

**重要的配置文件：**

* autoload_configs/modules.conf.xml
* autoload_configs/sofia.conf.xml
* autoload_configs/switch.conf.xml
* dialplan/default.xml
* dialplan/public.xml
* directory/default.xml
* directory/default/1000.xml
* freeswitch.xml
* sip_profiles/external.xml
* sip_profiles/external/example.xml
* sip_profiles/internal.xml
* vars.xml

# autoload_configs/

自动加载的模块的配置文件的目录。

一般来说每个模块有一个配置文件，文件名格式为“不包含mod_的模块名.conf.xml”，但文件名并不重要，可以改成其他的名字，只要扩展名为.xml。模块在启动时会向XML注册表中查找name为指定值的configuration，进而访问其下面的配置参数。

## autoload_configs/acl.conf.xml

ACL权限控制配置。文件内容如下：

```xml
<configuration name="acl.conf" description="Network Lists">
  <network-lists>
    <!--
         These ACL's are automatically created on startup.

         rfc1918.auto  - RFC1918 Space
         nat.auto      - RFC1918 Excluding your local lan.
         localnet.auto - ACL for your local lan.
         loopback.auto - ACL for your local lan.
    -->

    <list name="lan" default="allow">
      <node type="deny" cidr="192.168.42.0/24"/>
      <node type="allow" cidr="192.168.42.42/32"/>
    </list>

    <!--
        This will traverse the directory adding all users
        with the cidr= tag to this ACL, when this ACL matches
        the users variables and params apply as if they
        digest authenticated.
    -->
    <list name="domains" default="deny">
      <!-- domain= is special it scans the domain from the directory to build the ACL -->
      <node type="allow" domain="$${domain}"/>
      <!-- use cidr= if you wish to allow ip ranges to this domains acl. -->
      <!-- <node type="allow" cidr="192.168.0.0/24"/> -->
    </list>

  </network-lists>
</configuration>
```

`list`的`name`属性为名字，用于在其它地方引用。

## autoload_configs/callcenter.conf.xml

呼叫中心模块配置。这是文件内容的一部分：

```xml
  <queues>

    <queue name="support@default">
      <param name="strategy" value="longest-idle-agent"/>
      <param name="moh-sound" value="$${hold_music}"/>
      <!--<param name="record-template" value="$${recordings_dir}/${strftime(%Y-%m-%d-%H-%M-%S)}.${destination_number}.${caller_id_number}.${uuid}.wav"/>-->
      <param name="time-base-score" value="system"/>
      <param name="max-wait-time" value="0"/>
      <param name="max-wait-time-with-no-agent" value="0"/>
      <param name="max-wait-time-with-no-agent-time-reached" value="5"/>
      <param name="tier-rules-apply" value="false"/>
      <param name="tier-rule-wait-second" value="300"/>
      <param name="tier-rule-wait-multiply-level" value="true"/>
      <param name="tier-rule-no-agent-no-wait" value="false"/>
      <param name="discard-abandoned-after" value="60"/>
      <param name="abandoned-resume-allowed" value="false"/>
    </queue>

  </queues>

  <agents>
    <!--<agent name="1000@default" type="callback" contact="[leg_timeout=10]user/1000@default" status="Available" max-no-answer="3" wrap-up-time="10" reject-delay-time="10" busy-delay-time="60" />-->
  </agents>
  <tiers>
    <!-- If no level or position is provided, they will default to 1.  You should do this to keep db value on restart. -->
    <!-- <tier agent="1000@default" queue="support@default" level="1" position="1"/> -->
  </tiers>

```

`queue`为队列，`name`为队列的名字，用于在其它地方引用。

`queue`的`param`的`name`可为：

* stragy：策略。其值可为：
	* agent-with-fewest-calls：总时选择接电话次数最少的坐席。
	* agent-with-least-talk-time：总是选择通话时间最短的坐席。
	* long-idel-agent：选择空闲时间最长的坐席。
	* random：随机选择。
	* ring-all：所有坐席振铃，哪个先接就选哪个。
	* round-robin：轮询。
	* sequentially-by-agent-order：根据梯队和顺序选择。
	* top-down：按固定的顺序选择。

`agent`为坐席，可使用以下属性：

* busy-delay-time：如果坐席忙，再次选到该坐席的最短时长。
* contact：坐席的呼叫字符串，用于在有来话时呼叫该坐席。
* max-no-answer：坐席呼叫失败超过此次数，则将坐席状态设置为On Break。
* name：坐席的名字，用于在其它地方引用。
* reject-delay-time：坐席拒接后再次选到该坐席的最短时长。
* status：坐席的初始状态。可为：
	* Available：可用状态，可以接电话。
	* Available (On Demand)：一种特殊的可用状态。
	* Logged Out：退出服务状态。
	* On Break：坐席已登录，但不可以接电话。
* type：坐席的类型。可为：
	* callback：onhook坐席。
	* uuid-standby：offhook坐席。

`tire`为梯队，将队列与坐席关联起来。

## autoload_configs/cdr_csv_conf.xml

使用CSV文件存储话单的配置。文件内容如下：

```xml
<configuration name="cdr_csv.conf" description="CDR CSV Format">
  <settings>
    <!-- 'cdr-csv' will always be appended to log-base -->
    <!--<param name="log-base" value="/var/log"/>-->
    <param name="default-template" value="example"/>
    <!-- This is like the info app but after the call is hung up -->
    <!--<param name="debug" value="true"/>-->
    <param name="rotate-on-hup" value="true"/>
    <!-- may be a b or ab -->
    <param name="legs" value="a"/>
        <!-- Only log in Master.csv -->
        <!-- <param name="master-file-only" value="true"/> -->
  </settings>
  <templates>
    <template name="sql">INSERT INTO cdr VALUES ("${caller_id_name}","${caller_id_number}","${destination_number}","${context}","${start_stamp}","${answer_stamp}","${end_stamp}","${duration}","${billsec}","${hangup_cause}","${uuid}","${bleg_uuid}", "${accountcode}");</template>
    <template name="example">"${caller_id_name}","${caller_id_number}","${destination_number}","${context}","${start_stamp}","${answer_stamp}","${end_stamp}","${duration}","${billsec}","${hangup_cause}","${uuid}","${bleg_uuid}","${accountcode}","${read_codec}","${write_codec}"</template>
    <template name="snom">"${caller_id_name}","${caller_id_number}","${destination_number}","${context}","${start_stamp}","${answer_stamp}","${end_stamp}","${duration}","${billsec}","${hangup_cause}","${uuid}","${bleg_uuid}", "${accountcode}","${read_codec}","${write_codec}","${sip_user_agent}","${call_clientcode}","${sip_rtp_rxstat}","${sip_rtp_txstat}","${sofia_record_file}"</template>
    <template name="linksys">"${caller_id_name}","${caller_id_number}","${destination_number}","${context}","${start_stamp}","${answer_stamp}","${end_stamp}","${duration}","${billsec}","${hangup_cause}","${uuid}","${bleg_uuid}","${accountcode}","${read_codec}","${write_codec}","${sip_user_agent}","${sip_p_rtp_stat}"</template>
    <template name="asterisk">"${accountcode}","${caller_id_number}","${destination_number}","${context}","${caller_id}","${channel_name}","${bridge_channel}","${last_app}","${last_arg}","${start_stamp}","${answer_stamp}","${end_stamp}","${duration}","${billsec}","${hangup_cause}","${amaflags}","${uuid}","${userfield}"</template>
    <template name="opencdrrate">"${uuid}","${signal_bond}","${direction}","${ani}","${destination_number}","${answer_stamp}","${end_stamp}","${billsec}","${accountcode}","${userfield}","${network_addr}","${regex('${original_caller_id_name}'|^.)}","${sip_gateway_name}"</template>
  </templates>
</configuration>
```

`param`的`name`可为：

* default-template：默认模板。
* legs：话单记录的腿（a腿/b腿）。
* master-file-only：是否只记录话单汇总文件，不对每个分机单独记录话单文件。
* rotate-on-hup：当FreeSWITCH收到SIGHUP信号时，是否将话单轮替。

## autoload_configs/cdr_pg_csv.conf.xml

使用PostgreSQL存储话单的配置。文件内容如下：

```xml
<configuration name="cdr_pg_csv.conf" description="CDR PG CSV Format">
  <settings>
    <!-- See parameters for PQconnectdb() at http://www.postgresql.org/docs/8.4/static/libpq-connect.html -->
    <param name="db-info" value="host=localhost dbname=cdr connect_timeout=10" />
    <!-- CDR table name -->
    <!--<param name="db-table" value="cdr"/>-->

    <!-- Log a-leg (a), b-leg (b) or both (ab) -->
    <param name="legs" value="a"/>

    <!-- Directory in which to spool failed SQL inserts -->
    <!-- <param name="spool-dir" value="$${log_dir}/cdr-pg-csv"/> -->
    <!-- Disk spool format if DB connection/insert fails - csv (default) or sql -->
    <param name="spool-format" value="csv"/>
    <param name="rotate-on-hup" value="true"/>

    <!-- This is like the info app but after the call is hung up -->
    <!--<param name="debug" value="true"/>-->
  </settings>
  <schema>
    <field var="local_ip_v4"/>
    <field var="caller_id_name"/>
    <field var="caller_id_number"/>
    <field var="destination_number"/>
    <field var="context"/>
    <field var="start_stamp"/>
    <field var="answer_stamp"/>
    <field var="end_stamp"/>
    <field var="duration" quote="false"/>
    <field var="billsec" quote="false"/>
    <field var="hangup_cause"/>
    <field var="uuid"/>
    <field var="bleg_uuid"/>
    <field var="accountcode"/>
    <field var="read_codec"/>
    <field var="write_codec"/>
    <!-- <field var="sip_hangup_disposition"/> -->
    <!-- <field var="ani"/> -->
  </schema>
</configuration>
```

`param`的`name`可为：

* db-info：PostgreSQL标准的连接字符串。
* db-table：数据库表名。

`schema`配置通道变量与数据库中字段的对应关系。

`field`的属性有：

* column：数据库表的字段名。缺省则与var相同。
* quote：SQL语句中的值是否使用单引号括起（如当值为整数时为false）。
* var：通道变量。

## autoload_configs/conference.conf.xml

多人电话会议配置。这是文件内容的一部分：

```xml
  <caller-controls>
    <group name="default">
      <control action="mute" digits="0"/>
      <control action="deaf mute" digits="*"/>
      <control action="energy up" digits="9"/>
      <control action="energy equ" digits="8"/>
      <control action="energy dn" digits="7"/>
      <control action="vol talk up" digits="3"/>
      <control action="vol talk zero" digits="2"/>
      <control action="vol talk dn" digits="1"/>
      <control action="vol listen up" digits="6"/>
      <control action="vol listen zero" digits="5"/>
      <control action="vol listen dn" digits="4"/>
      <control action="hangup" digits="#"/>
    </group>
  </caller-controls>

  <profiles>
    <profile name="default">
      <param name="rate" value="8000"/>
    </profile>

    <profile name="wideband">
    </profile>

    <profile name="ultrawideband">
    </profile>

    <profile name="cdquality">
    </profile>
  </profiles>
```

`control`的`action`属性可为：

* execute_application：执行APP。`control`的`data`属性为执行APP的语句。

`param`的`name`可为：

* alone-sound：会议只剩一个用户时向其播放的声音文件。
* auto-record：录音模板。当会议中有至少两个人时就会自动开始录音。
* comfort-noise：是否产生舒适噪声。
* conference-flags：会议标志。rfc-4579为RFC4579定义的视频会议控制标准。
* muted-sound：用户被禁言时向其播放的声音文件。
* rate：采样率。
* unmuted-sound：用户被取消禁言时向其播放的声音文件。

## autoload_configs/distributor.conf.xml

号码连选模块配置。文件内容如下：

```xml
<configuration name="distributor.conf" description="Distributor Configuration">
  <lists>
    <!-- every 10 calls to test you will get foo1 once and foo2 9 times...yes NINE TIMES! -->
    <!-- this is not the same as 100 with 10 and 90 that would do foo1 10 times in a row then foo2 90 times in a row -->
    <list name="test">
      <node name="foo1" weight="1"/>
      <node name="foo2" weight="9"/>
    </list>
  </lists>
</configuration>
```

## autoload_configs/event_socket.conf.xml

Event Socket配置。文件内容如下：

```xml
<configuration name="event_socket.conf" description="Socket Client">
  <settings>
    <param name="nat-map" value="false"/>
    <param name="listen-ip" value="::"/>
    <param name="listen-port" value="8021"/>
    <param name="password" value="ClueCon"/>
    <!--<param name="apply-inbound-acl" value="loopback.auto"/>-->
    <!--<param name="stop-on-bind-error" value="true"/>-->
  </settings>
</configuration>
```

## autoload_configs/fifo.conf.xml

呼叫队列模块配置。文件内容如下：

```xml
<configuration name="fifo.conf" description="FIFO Configuration">
  <settings>
    <param name="delete-all-outbound-member-on-startup" value="false"/>
  </settings>
  <fifos>
    <fifo name="cool_fifo@$${domain}" importance="0">
      <!--<member timeout="60" simo="1" lag="20">{member_wait=nowait}user/1005@$${domain}</member>-->
    </fifo>
  </fifos>
</configuration>
```

`param`的`name`可为：

* odbc-dsn：数据库ODBC的DSN。格式为：[odbc://]NAME:[USERNAME]:[PASSWORD]。

`fifos`用于配置静态坐席。

`fifo`的`name`属性为队列的名字，用于在其它地方引用。

每个`member`相当于一个坐席，其值为该坐席的呼叫字符串。坐席会轮流振铃（拒接其中一个，另一个就会振铃）。

`timeout`属性为呼叫超时的秒数。

`simo`属性为最大能服务的呼叫的数量。

`lag`属性为该坐席接听一个呼叫后再接听下一个呼叫的间隔秒数。

## autoload_configs/ivr.conf.xml

IVR模块配置。文件内容如下：

```xml
<configuration name="ivr.conf" description="IVR menus">
  <menus>
    <X-PRE-PROCESS cmd="include" data="../ivr_menus/*.xml"/>
  </menus>
</configuration>
```

## autoload_configs/local_stream.conf.xml

本地文件流的配置。这是文件内容的一部分：

```xml
<configuration name="local_stream.conf" description="stream files from local dir">
  <!-- fallback to default if requested moh class isn't found -->
  <directory name="default" path="$${sounds_dir}/music/8000">
    <param name="rate" value="8000"/>
    <param name="shuffle" value="true"/>
    <param name="channels" value="1"/>
    <param name="interval" value="20"/>
    <param name="timer-name" value="soft"/>
    <!-- list of short files to break in with every so often -->
    <!--<param name="chime-list" value="file1.wav,file2.wav"/>-->
    <!-- frequency of break-in (seconds)-->
    <!--<param name="chime-freq" value="30"/>-->
    <!-- limit to how many seconds the file will play -->
    <!--<param name="chime-max" value="500"/>-->
  </directory>

  <directory name="moh/8000" path="$${sounds_dir}/music/8000">
    <param name="rate" value="8000"/>
    <param name="shuffle" value="true"/>
    <param name="channels" value="1"/>
    <param name="interval" value="20"/>
    <param name="timer-name" value="soft"/>
  </directory>
</configuration>
```

`name`为名字，斜杠前面用于在其它地方引用，斜杠后面用于自动匹配采样率。如果找不到指定的流则会使用`default`流，如果`default`流也不存在则会提示错误。

`path`为声音文件的目录。

## autoload_configs/lua.conf.xml

Lua语言配置。文件内容如下：

```xml
<configuration name="lua.conf" description="LUA Configuration">
  <settings>

    <!-- 
    Specify local directories that will be searched for LUA modules
    These entries will be pre-pended to the LUA_CPATH environment variable
    -->
    <!-- <param name="module-directory" value="/usr/lib/lua/5.1/?.so"/> -->
    <!-- <param name="module-directory" value="/usr/local/lib/lua/5.1/?.so"/> -->

    <!-- 
    Specify local directories that will be searched for LUA scripts
    These entries will be pre-pended to the LUA_PATH environment variable
    -->
    <!-- <param name="script-directory" value="/usr/local/lua/?.lua"/> -->
    <!-- <param name="script-directory" value="$${script_dir}/?.lua"/> -->

    <!--<param name="xml-handler-script" value="/dp.lua"/>-->
    <!--<param name="xml-handler-bindings" value="dialplan"/>-->

    <!--
        The following options identifies a lua script that is launched
        at startup and may live forever in the background.
        You can define multiple lines, one for each script you 
        need to run.
    -->
    <!--<param name="startup-script" value="startup_script_1.lua"/>-->
    <!--<param name="startup-script" value="startup_script_2.lua"/>-->
    
    <!--<hook event="CUSTOM" subclass="conference::maintenance" script="catch-event.lua"/>-->
  </settings>
</configuration>
```

`param`的`name`可为：

* startup-script：FreeSWITCH启动时执行的脚本文件。
* xml-handler-bindings：使用XML绑定的Section。Section见freeswitch.xml。
* xml-handler-script：XML绑定执行的脚本文件。

## autoload_configs/modules.conf.xml

配置FreeSWITCH启动时自动加载哪些模块。这是文件内容的一部分：

```xml
<configuration name="modules.conf" description="Modules">
  <modules>
    <!-- Loggers (I'd load these first) -->
    <load module="mod_console"/>
    <!-- <load module="mod_graylog2"/> -->
    <load module="mod_logfile"/>
    <!-- <load module="mod_syslog"/> -->
  </modules>
</configuration>
```

## autoload_configs/nibblebill.conf.xml

预付费计费模块配置。这是文件内容的一部分：

```xml
  <settings>
    <param name="odbc-dsn" value="bandwidth.com"/>
  </settings>
```

`param`的`name`可为：

* db_column_account：计费账号对应的数据库表字段名。
* global_heartbeat：通话过程中定时扣款的时间间隔。
* odbc-dsn：数据库ODBC的DSN。格式为：[odbc://]NAME:[USERNAME]:[PASSWORD]。

## autoload_configs/post_load_modules.conf.xml

其格式和用法与autoload_configs/modules.conf.xml差不多，不同的是其中定义的模块加载时间较晚。

## autoload_configs/sofia.conf.xml

SIP模块配置。文件内容如下：

```xml
<configuration name="sofia.conf" description="sofia Endpoint">

  <global_settings>
    <param name="log-level" value="0"/>
    <!-- <param name="abort-on-empty-external-ip" value="true"/> -->
    <!-- <param name="auto-restart" value="false"/> -->
    <param name="debug-presence" value="0"/>
    <!-- <param name="capture-server" value="udp:homer.domain.com:5060"/> -->
    
    <!-- 
        the new format for HEPv2/v3 and capture ID    
        
        protocol:host:port;hep=2;capture_id=200;

    -->
    
    <!-- <param name="capture-server" value="udp:homer.domain.com:5060;hep=3;capture_id=100"/> -->
  </global_settings>

  <!--
      The rabbit hole goes deep.  This includes all the
      profiles in the sip_profiles directory that is up
      one level from this directory.
  -->
  <profiles>
    <X-PRE-PROCESS cmd="include" data="../sip_profiles/*.xml"/>
  </profiles>

</configuration>
```

各参数的含义如下：

* capture-server：Homer方式抓包的Capture Node（又称Capture Server）的地址。

## autoload_configs/switch.conf.xml

SIP核心配置。这是文件内容的一部分：

```xml
<configuration name="switch.conf" description="Core Configuration">

  <settings>
    <param name="max-sessions" value="1000"/>
  </settings>

</configuration>
```

`param`的`name`可为：

* core-db-dsn：core数据库的DSN。格式为：[odbc://]NAME:[USERNAME]:[PASSWORD]。
* max-sessions：呼叫最大并发数。
* rtp-end-port：RTP结束端口（含）。
* rtp-start-port：RTP起始端口（含）。
* sessions-per-second：每秒最大呼叫数。

## autoload_configs/tts_commandline.conf.xml

调用其他TTS软件命令行的配置。文件内容如下：

```xml
<configuration name="tts_commandline.conf" description="TextToSpeech Commandline configuration">
    <settings>
        <!--
        Some variables will be replaced :
        ${text}: input text (quoted)
        ${rate}: sample rate (example: 8000)
        ${voice}: voice_name passed to TTS(quoted)
        ${file}: output file (quoted, including .wav extension)
    
    Example commands can be found at:
    https://freeswitch.org/confluence/display/FREESWITCH/mod_tts_commandline#mod_tts_commandline-Examplecommands
        -->
        <param name="command" value="echo ${text} | text2wave -f ${rate} > ${file}"/>
    </settings>
</configuration>
```

在调用该模块时会自动设置以下变量：

* file：声音文件名。默认扩展名为.wav，使用完毕会自动删除.wav文件。
* rate：Channel的采样率。
* text：TTS文本。
* voicd：嗓音的名字。

## autoload_configs/xml_cdr.conf.xml

生成XML格式的话单发送至外部HTTP服务器的配置。这是文件内容的一部分：

```xml
  <settings>
    <!-- <param name="url" value="http://localhost/cdr_curl/post.php"/> -->
  </settings>
```

`param`的`name`可为：

* url：话单的HTTP POST请求的URL地址。

## autoload_configs/xml_curl.conf.xml

请求外部HTTP服务器生成XML的配置。这是文件内容的一部分：

```xml
<configuration name="xml_curl.conf" description="cURL XML Gateway">
  <bindings>
    <binding name="example">
      <!-- <param name="gateway-url" value="http://www.freeswitch.org/gateway.xml" bindings="dialplan"/> -->
    </binding>
  </bindings>
</configuration>
```

`param`的`name`可为：

* gateway-url：HTTP网关服务器的URL。`bindings`表示应用的Section。

## autoload_configs/xml_rpc.conf.xml

FreeSWITCH Portal配置。文件内容如下：

```xml
<configuration name="xml_rpc.conf" description="XML RPC">
  <settings>
    <!-- The port where you want to run the http service (default 8080) -->
    <param name="http-port" value="8080"/>
    <!-- if all 3 of the following params exist all http traffic will require auth -->
    <param name="auth-realm" value="freeswitch"/>
    <param name="auth-user" value="freeswitch"/>
    <param name="auth-pass" value="works"/>


    <!-- regex pattern to match against commands called against this service.
         If a command with arguments matches, it will be logged at INFO level -->
    <!--<param name="commands-to-log" value=""/> -->

  </settings>
</configuration>
```

# dialplan/

拨号计划的配置目录。支持多个Context，每个Context可以写到一个XML文件中。

由于在处理Dialplan时要对每一项进行正则表达式匹配，这是非常影响效率的，所以在生产环境中往往要删除默认的Dialplan。

## dialplan/default.xml

默认Context，一般用于内部注册用户路由。这是文件内容的一部分：

```xml
  <context name="default">

    <extension name="unloop">
      <condition field="${unroll_loops}" expression="^true$"/>
      <condition field="${sip_looped_call}" expression="^true$">
        <action application="deflect" data="${destination_number}"/>
      </condition>
    </extension>

    <extension name="global" continue="true">
      <condition field="${call_debug}" expression="^true$" break="never">
        <action application="info"/>
      </condition>

      <condition field="${default_password}" expression="^1234$" break="never">
        <action application="log" data="CRIT WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING "/>
        <action application="log" data="CRIT Open $${conf_dir}/vars.xml and change the default_password."/>
        <action application="log" data="CRIT Once changed type 'reloadxml' at the console."/>
        <action application="log" data="CRIT WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING "/>
        <action application="sleep" data="10000"/>
      </condition>
    </extension>

    <extension name="Local_Extension">
      <condition field="destination_number" expression="^(10[01][0-9])$">
        <action application="export" data="dialed_extension=$1"/>
        <!-- bind_meta_app can have these args <key> [a|b|ab] [a|b|o|s] <app> -->
        <action application="bind_meta_app" data="1 b s execute_extension::dx XML features"/>
        <action application="bind_meta_app" data="2 b s record_session::$${recordings_dir}/${caller_id_number}.${strftime(%Y-%m-%d-%H-%M-%S)}.wav"/>
        <action application="bind_meta_app" data="3 b s execute_extension::cf XML features"/>
        <action application="bind_meta_app" data="4 b s execute_extension::att_xfer XML features"/>
        <action application="set" data="ringback=${us-ring}"/>
        <action application="set" data="transfer_ringback=$${hold_music}"/>
        <action application="set" data="call_timeout=30"/>
        <!-- <action application="set" data="sip_exclude_contact=${network_addr}"/> -->
        <action application="set" data="hangup_after_bridge=true"/>
        <!--<action application="set" data="continue_on_fail=NORMAL_TEMPORARY_FAILURE,USER_BUSY,NO_ANSWER,TIMEOUT,NO_ROUTE_DESTINATION"/> -->
        <action application="set" data="continue_on_fail=true"/>
        <action application="hash" data="insert/${domain_name}-call_return/${dialed_extension}/${caller_id_number}"/>
        <action application="hash" data="insert/${domain_name}-last_dial_ext/${dialed_extension}/${uuid}"/>
        <action application="set" data="called_party_callgroup=${user_data(${dialed_extension}@${domain_name} var callgroup)}"/>
        <action application="hash" data="insert/${domain_name}-last_dial_ext/${called_party_callgroup}/${uuid}"/>
        <action application="hash" data="insert/${domain_name}-last_dial_ext/global/${uuid}"/>
        <!--<action application="export" data="nolocal:rtp_secure_media=${user_data(${dialed_extension}@${domain_name} var rtp_secure_media)}"/>-->
        <action application="hash" data="insert/${domain_name}-last_dial/${called_party_callgroup}/${uuid}"/>
        <action application="bridge" data="user/${dialed_extension}@${domain_name}"/>
        <action application="answer"/>
        <action application="sleep" data="1000"/>
        <action application="bridge" data="loopback/app=voicemail:default ${domain_name} ${dialed_extension}"/>
      </condition>
    </extension>

    <extension name="echo">
      <condition field="destination_number" expression="^9196$">
        <action application="answer"/>
        <action application="echo"/>
      </condition>
    </extension>

    <extension name="hold_music">
      <condition field="destination_number" expression="^9664$"/>
      <condition field="${rtp_has_crypto}" expression="^(AES_CM_128_HMAC_SHA1_32|AES_CM_128_HMAC_SHA1_80)$">
        <action application="answer"/>
        <action application="execute_extension" data="is_secure XML features"/>
        <action application="playback" data="$${hold_music}"/>
        <anti-action application="set" data="zrtp_secure_media=true"/>
        <anti-action application="answer"/>
        <anti-action application="playback" data="silence_stream://2000"/>
        <anti-action application="execute_extension" data="is_zrtp_secure XML features"/>
        <anti-action application="playback" data="$${hold_music}"/>
      </condition>
    </extension>

    <X-PRE-PROCESS cmd="include" data="default/*.xml"/>

  </context>
```

每个`context`中有多个`extension`，一个`context`中的`extension`与其他`context`中的`extension`在逻辑上是隔离的。

`context`的`name`用于在其它地方引用。

`extension`的`name`对呼叫流程没有任何影响。

`extension`的属性`continue`表示执行完`action`是否继续匹配其他`extension`，缺省为`false`。

`extension`中可以对一些`condition`（测试条件）进行判断，如果匹配测试条件所指定的表达式则执行对应的`action`（动作），如不匹配则执行对应的`anti-action`（反动作）。

`condition`可使用以下属性（可同时使用多个属性）：

* field：通道变量。自定义变量需使用`${}`引起：
* hour：小时，0~23。
* mday：日，1~31。
* minute：分，0~59。
* minute-of-day：一天中的第几分钟，1~1440（1点等于60，中午等于720）。
* mon：月，1~12。
* mweek：本月中的第几周，1~6。
* wday：一周中的第几天，1~7（周日为1）。可使用“-”连接两个值表示范围。
* week：一年中的第几周，1~53。
* year：当前的年，0~9999。
* yday：一年中的第几天，1~366。

`condition`的`break`可为：

* always；不管是否匹配，都停止。
* never：不管是否匹配，都继续。
* on-false：在匹配失败时停止（但继续处理其他`extension`），这是默认配置。
* on-true：在匹配成功时停止（但会先完成对应`action`，然后继续处理其他的`extension`），不成功则继续匹配其他`condition`。

`expression`为`field`匹配的正则表达式。

没有`field`和`expression`的空`condition`被认为匹配所有规则，称其为绝对条件（Absolute Condition）。

`action`和`anti-action`有`application`属性，表示要执行的APP。

`action`和`anti-action`可有`data`属性，表示APP的参数，可用`$N`（从1开始）表示`expression`匹配的内容，用`${API([ARG[ ...]])}`表示API调用。如没有参数，也可省略。

`action`和`anti-action`将`inline`设置为`true`表示内联执行。

`action`可设置`loop`属性，如失败则重复执行直至指定次数。

可以使用`sip_h_`开头的通道变量添加扩展的SIP消息头。

默认号码如下（官方文档：[https://freeswitch.org/confluence/display/FREESWITCH/Default+Dialplan+QRF](https://freeswitch.org/confluence/display/FREESWITCH/Default+Dialplan+QRF)）：

| 号码       | 说明                                 |
| --------- | ------------------------------------ |
| 1000-1019 | 呼叫默认分机号（默认密码为1234）           |
| 2000-2002 | 呼叫组                                |
| 30xx      | 电话会议，8kHz（其中xx可为00~99）        |
| 31xx      | 电话会议，宽带，16kHz（其中xx可为00~99）   |
| 32xx      | 电话会议，超宽带，32kHz（其中xx可为00~99） |
| 33xx      | 电话会议，CD音质，48kHz（其中xx可为00~99） |
| 4000      | 听取语音信箱                            |
| 5000      | 示例IVR                               |
| 5900      | 呼叫停泊                               |
| 5901      | 呼叫取回                               |
| 7243      | 组播                                  |
| 9178      | 收传真                                 |
| 9179      | 发传真                                 |
| 9180      | 铃音测试，使用远端生成的回铃音             |
| 9181      | 铃音测试，产生英式铃音                    |
| 9182      | 铃音测试，使用音乐当铃音，彩铃             |
| 9183      | 先应答，然后发送英式铃音                  |
| 9184      | 先应答，然后发送音乐铃音                  |
| 9191      | ClueCon                               |
| 9192      | 在log中显示Channel信息                  |
| 9193      | 录像                                  |
| 9194      | 播放录像                               |
| 9195      | echo，回音测试，延迟5秒                  |
| 9196      | echo，回音测试                         |
| 9197      | milliwatte extension，铃音生成         |
| 9198      | TGML铃音生成示例                        |
| 9664      | 保持音乐                               |

## dialplan/public.xml

公共Context，一般用于网关路由。这是文件内容的一部分：

```xml
  <context name="public">

    <extension name="unloop">
      <condition field="${unroll_loops}" expression="^true$"/>
      <condition field="${sip_looped_call}" expression="^true$">
        <action application="deflect" data="${destination_number}"/>
      </condition>
    </extension>

    <extension name="public_extensions">
      <condition field="destination_number" expression="^(10[01][0-9])$">
        <action application="transfer" data="$1 XML default"/>
      </condition>
    </extension>

    <X-PRE-PROCESS cmd="include" data="public/*.xml"/>

  </context>
```

大部分参数与dialplan/default.xml相同。

# directory/

用户目录。支持多个域（Domain），每个域可以写到一个XML文件中。

## directory/default.xml

用户目录默认域。这是文件内容的一部分：

```xml
  <domain name="$${domain}">
    <params>
      <param name="dial-string" value="{^^:sip_invite_domain=${dialed_domain}:presence_id=${dialed_user}@${dialed_domain}}${sofia_contact(*/${dialed_user}@${dialed_domain})},${verto_contact(${dialed_user}@${dialed_domain})}"/>
    </params>

    <variables>
      <variable name="record_stereo" value="true"/>
      <variable name="default_gateway" value="$${default_provider}"/>
      <variable name="default_areacode" value="$${default_areacode}"/>
      <variable name="transfer_fallback_extension" value="operator"/>
    </variables>

    <groups>
      <group name="default">
        <users>
          <X-PRE-PROCESS cmd="include" data="default/*.xml"/>
        </users>
      </group>

      <group name="sales">
        <users>
          <!--
              type="pointer" is a pointer so you can have the
              same user in multiple groups.  It basically means
              to keep searching for the user in the directory.
          -->
          <user id="1000" type="pointer"/>
          <user id="1001" type="pointer"/>
          <user id="1002" type="pointer"/>
          <user id="1003" type="pointer"/>
          <user id="1004" type="pointer"/>
        </users>
      </group>

    </groups>
  </domain>
```

`domain`的`name`属性为租户域的名字。

`params`定义了该域中所有用户的公共参数。`dial-string`参数为呼叫字符串。

`variables`定义了一些公共变量，在用户主叫或被叫时，这些变量会绑定到相应的Channel上形成Channel Variable。

还定义了很多组（`group`），组名并无特殊意义。组里面包含很多用户（`user`），可以是指向已存在用户的指针。使用组并不是必需的，可以将`users`直接放到`domain`下一级。

`params`和`variables`可以出现在`user`、`group`、`domain`中，当有重复时按此优先级从高到低。

## directory/default/

本地用户目录。

### directory/default/1000.xml

一个本地用户。文件内容如下：

```xml
<include>
  <user id="1000">
    <params>
      <param name="password" value="$${default_password}"/>
      <param name="vm-password" value="1000"/>
    </params>
    <variables>
      <variable name="toll_allow" value="domestic,international,local"/>
      <variable name="accountcode" value="1000"/>
      <variable name="user_context" value="default"/>
      <variable name="effective_caller_id_name" value="Extension 1000"/>
      <variable name="effective_caller_id_number" value="1000"/>
      <variable name="outbound_caller_id_name" value="$${outbound_caller_name}"/>
      <variable name="outbound_caller_id_number" value="$${outbound_caller_id}"/>
      <variable name="callgroup" value="techsupport"/>
    </variables>
  </user>
</include>
```

`user_context`的值为此用户发起的呼叫的拨号计划的`context`。

# freeswitch.xml

主配置文件，使用`X-PRE-PROCESS`指令的`include`语句载入其他文件。这是文件内容的一部分：

```xml
<?xml version="1.0"?>
<document type="freeswitch/xml">
  <X-PRE-PROCESS cmd="include" data="vars.xml"/>

  <section name="configuration" description="Various Configuration">
    <X-PRE-PROCESS cmd="include" data="autoload_configs/*.xml"/>
  </section>

  <section name="dialplan" description="Regex/XML Dialplan">
    <X-PRE-PROCESS cmd="include" data="dialplan/*.xml"/>
  </section>

  <section name="chatplan" description="Regex/XML Chatplan">
    <X-PRE-PROCESS cmd="include" data="chatplan/*.xml"/>
  </section>

  <section name="directory" description="User Directory">
    <X-PRE-PROCESS cmd="include" data="directory/*.xml"/>
  </section>

  <section name="languages" description="Language Management">
    <X-PRE-PROCESS cmd="include" data="lang/de/*.xml"/>
    <X-PRE-PROCESS cmd="include" data="lang/en/*.xml"/>
    <X-PRE-PROCESS cmd="include" data="lang/fr/*.xml"/>
    <X-PRE-PROCESS cmd="include" data="lang/ru/*.xml"/>
    <X-PRE-PROCESS cmd="include" data="lang/he/*.xml"/>
    <X-PRE-PROCESS cmd="include" data="lang/es/es_ES.xml"/>
    <X-NO-PRE-PROCESS cmd="include" data="lang/es/es_MX.xml"/>
    <X-PRE-PROCESS cmd="include" data="lang/pt/pt_BR.xml"/>
    <X-NO-PRE-PROCESS cmd="include" data="lang/pt/pt_PT.xml"/>
    <X-NO-PRE-PROCESS cmd="include" data="lang/sv/*.xml"/>
  </section>
</document>
```

# ivr_menus/

IVR菜单配置。

## ivr_menus/demo_ivr.xml

IVR菜单配置示例。文件内容如下：

```xml
<include>
  <!-- demo IVR setup -->
  <!-- demo IVR, Main Menu -->
  <menu name="demo_ivr"
      greet-long="phrase:demo_ivr_main_menu"
      greet-short="phrase:demo_ivr_main_menu_short"
      invalid-sound="ivr/ivr-that_was_an_invalid_entry.wav"
      exit-sound="voicemail/vm-goodbye.wav"
      confirm-macro=""
      confirm-key=""
      tts-engine="flite"
      tts-voice="rms"
      confirm-attempts="3"
      timeout="10000"
      inter-digit-timeout="2000"
      max-failures="3"
      max-timeouts="3"
      digit-len="4">

    <!-- The following are the definitions for the digits the user dials -->
    <!-- Digit 1 transfer caller to the public FreeSWITCH conference -->
    <entry action="menu-exec-app" digits="1" param="bridge sofia/$${domain}/888@conference.freeswitch.org"/>
    <entry action="menu-exec-app" digits="2" param="transfer 9196 XML default"/>    <!-- FS echo -->
    <entry action="menu-exec-app" digits="3" param="transfer 9664 XML default"/>    <!-- MOH -->
    <entry action="menu-exec-app" digits="4" param="transfer 9191 XML default"/>    <!-- ClueCon -->
    <entry action="menu-exec-app" digits="5" param="transfer 1234*256 enum"/>       <!-- Screaming monkeys -->
    <entry action="menu-sub" digits="6" param="demo_ivr_submenu"/>                  <!-- demo sub menu -->
    <!-- Using a regex in the digits tag lets you define a dial pattern for the caller
         You may define multiple regexes if you need a different pattern for some reason -->
    <entry action="menu-exec-app" digits="/^(10[01][0-9])$/" param="transfer $1 XML features"/>
    <entry action="menu-top" digits="9"/>          <!-- Repeat this menu -->
  </menu>

  <!-- Demo IVR, Sub Menu -->
  <menu name="demo_ivr_submenu"
      greet-long="phrase:demo_ivr_sub_menu"
      greet-short="phrase:demo_ivr_sub_menu_short"
      invalid-sound="ivr/ivr-that_was_an_invalid_entry.wav"
      exit-sound="voicemail/vm-goodbye.wav"
      timeout="15000"
      max-failures="3"
      max-timeouts="3">

    <!-- The demo IVR sub menu prompt basically just says, "press star to return to previous menu..." -->
    <entry action="menu-top" digits="*"/>
   </menu>


<!-- TTS sample; non-functional but it demonstrates say: and TTS -->
<!--
  <menu name="demo3"
      greet-long="say:Press 1 to join the conference, Press 2 to join the other conference"
      greet-short="say:Press 1 to join the conference, Press 2 to join the other conference"
      invalid-sound="say:invalid extension"
      exit-sound="say:exit sound"
      timeout ="15000"
      max-failures="3">
    <entry action="menu-exit" digits="*"/>
    <entry action="menu-play-sound" digits="1" param="say:You pressed 1"/>
    <entry action="menu-exec-app" digits="2" param="transfert 1000 XML default"/>
    <entry action="menu-exec-app" digits="3" param="transfert 1001 XML default"/>
  </menu>
-->
</include>
```

每个`menu`描述一个菜单，有以下属性（如文件为相对路径，则相对于/usr/local/freeswitch/sounds）：

* digit-len：最大收号位数。
* exit-sound：最后菜单退出时（一般是超时没有按键）的提示音文件。
* greet-long：最开始的欢迎音文件。
* greet-short：用户长时间没有按键的简短提示音文件。
* inter-digit-timeout：两次按键的最大间隔（毫秒）。
* invalid-sound：用户按键错误的提示音文件。
* max-failures：用户按键错误的最多次数。
* max-timeouts：最大超时次数。
* name：菜单名。必需唯一，用于在其它地方引用。
* timeout：超时时间（毫秒）。即多长时间没有收到按键就超时，播放其他提示音。

菜单有若干个菜单项`entry`，其`action`可为：

* menu-exec-app：执行一个APP。
* menu-sub：执行下级菜单。
* menu-top：返回上级菜单。

# lang

语言配置目录。

## lang/en

英语配置目录。

### lang/en/demo

英语配置示例。

#### lang/en/demo/demo-ivr.xml

英语IVR的Phrase宏示例。这是文件内容的一部分：

```xml
  <macro name="demo_ivr_main_menu" pause="100">
    <input pattern="(.*)">
      <match>
        <!-- string together several existing sound files to create one long greeting -->
        <action function="play-file" data="ivr/ivr-welcome_to_freeswitch.wav"/>
        <action function="play-file" data="ivr/ivr-this_ivr_will_let_you_test_features.wav"/>
        <action function="play-file" data="ivr/ivr-you_may_exit_by_hanging_up.wav"/>
        <!-- note that you can do more than just play files, e.g. have pauses and do TTS -->

        <!-- Menu option 1: Call FreeSWITCH conference-->
        <action function="play-file" data="ivr/ivr-enter_ext_pound.wav"/>
        <action function="play-file" data="silence_stream://1500"/>
        <action function="play-file" data="ivr/ivr-to_call_the_freeswitch_conference.wav"/>
        <action function="play-file" data="ivr/ivr-please.wav"/>
        <action function="play-file" data="voicemail/vm-press.wav"/>
        <action function="play-file" data="digits/1.wav"/>

        <!-- Menu option 2: Do FreeSWITCH echo test -->
        <action function="play-file" data="ivr/ivr-to_do_a_freeswitch_echo_test.wav"/>
        <action function="play-file" data="ivr/ivr-please.wav"/>
        <action function="play-file" data="voicemail/vm-press.wav"/>
        <action function="play-file" data="digits/2.wav"/>

        <!-- Menu option 3: Listen to Music on Hold -->
        <action function="play-file" data="ivr/ivr-to_listen_to_moh.wav"/>
        <action function="play-file" data="ivr/ivr-please.wav"/>
        <action function="play-file" data="voicemail/vm-press.wav"/>
        <action function="play-file" data="digits/3.wav"/>

        <!-- Menu option 4: Register for ClueCon -->
        <action function="play-file" data="ivr/ivr-register_for_cluecon.wav"/>
        <action function="play-file" data="digits/4.wav"/>

        <!-- Menu option 5: Listen to screaming monkeys -->
        <action function="play-file" data="ivr/ivr-to_hear_screaming_monkeys.wav"/>
        <action function="play-file" data="ivr/ivr-please.wav"/>
        <action function="play-file" data="voicemail/vm-press.wav"/>
        <action function="play-file" data="digits/5.wav"/>

        <!-- Menu option 6: Hear a sample submenu -->
        <action function="play-file" data="ivr/ivr-to_hear_sample_submenu.wav"/>
        <action function="play-file" data="ivr/ivr-please.wav"/>
        <action function="play-file" data="voicemail/vm-press.wav"/>
        <action function="play-file" data="digits/6.wav"/>

        <!-- Menu option 9: Repeat these options -->
        <action function="play-file" data="ivr/ivr-to_repeat_these_options.wav"/>
        <action function="play-file" data="ivr/ivr-please.wav"/>
        <action function="play-file" data="voicemail/vm-press.wav"/>
        <action function="play-file" data="digits/9.wav"/>
        <action function="play-file" data="silence_stream://2000"/>
      </match>
    </input>
  </macro>
```

`name`为宏名称，用于其他地方引用。

`pause`表示每个`action`之间暂停多长时间（毫秒）。

可指定多个`input`，根据`pattern`正则表达式匹配不同的输入。

`function`可为：

* play-file：播放文件。
* say：使用Say接口放音。除了`data`属性，还需要`type`和`method`属性。
* speak-text：使用TTS将文本内容放音。

`data`可使用`$N`引用`pattern`匹配的内容。

### lang/en/en.xml

英语配置。这是文件内容的一部分：

```xml
<include>
  <language name="en" say-module="en" sound-prefix="$${sound_prefix}" tts-engine="cepstral" tts-voice="callie">
    <phrases>
      <macros>
        <X-PRE-PROCESS cmd="include" data="demo/*.xml"/> <!-- Note: this now grabs whole subdir, previously grabbed only demo.xml -->
        <!-- voicemail_en_tts is purely implemented with tts, we have the files based one that is the default. -->
        <X-PRE-PROCESS cmd="include" data="vm/sounds.xml"/> <!-- vm/tts.xml if you want to use tts and have cepstral -->
        <X-PRE-PROCESS cmd="include" data="dir/sounds.xml"/> <!-- dir/tts.xml if you want to use tts and have cepstral -->
        <X-PRE-PROCESS cmd="include" data="ivr/*.xml"/>  <!-- IVR and custom phrases go here -->
      </macros>
      <X-PRE-PROCESS cmd="include" data="vm/voicemail_ivr.xml"/>
    </phrases>
  </language>
</include>
```

`name`为语言的名字，用于在其它地方引用。

`say-module`表示该语言用来支持Say接口的模块，本例中为mod_say_en。

`sound-prefix`为该语言Say接口的声音文件的路径前缀。

`tts-engine`为TTS引擎。

`tts-voice`为TTS嗓音。

# sip_profiles

SIP Profile配置。

## sip_profiles/external.xml

SIP external Profile配置。这是文件内容的一部分：

```xml
<profile name="external">
  <gateways>
    <X-PRE-PROCESS cmd="include" data="external/*.xml"/>
  </gateways>

  <domains>
    <domain name="all" alias="false" parse="true"/>
  </domains>

  <settings>
    <param name="context" value="public"/>
    <param name="auth-calls" value="false"/>
  </settings>
</profile>
```

大部分参数与sip_profiles/internal.xml相同。

## sip_profiles/external/

SIP external Profile配置目录。

### sip_profiles/external/example.xml

SIP external Profile网关配置示例。文件内容如下：

```xml
<include>
  <!--<gateway name="asterlink.com">-->
  <!--/// account username *required* ///-->
  <!--<param name="username" value="cluecon"/>-->
  <!--/// auth realm: *optional* same as gateway name, if blank ///-->
  <!--<param name="realm" value="asterlink.com"/>-->
  <!--/// username to use in from: *optional* same as  username, if blank ///-->
  <!--<param name="from-user" value="cluecon"/>-->
  <!--/// domain to use in from: *optional* same as  realm, if blank ///-->
  <!--<param name="from-domain" value="asterlink.com"/>-->
  <!--/// account password *required* ///-->
  <!--<param name="password" value="2007"/>-->
  <!--/// extension for inbound calls: *optional* same as username, if blank ///-->
  <!--<param name="extension" value="cluecon"/>-->
  <!--/// proxy host: *optional* same as realm, if blank ///-->
  <!--<param name="proxy" value="asterlink.com"/>-->
  <!--/// send register to this proxy: *optional* same as proxy, if blank ///-->
  <!--<param name="register-proxy" value="mysbc.com"/>-->
  <!--/// expire in seconds: *optional* 3600, if blank ///-->
  <!--<param name="expire-seconds" value="60"/>-->
  <!--/// do not register ///-->
  <!--<param name="register" value="false"/>-->
  <!-- which transport to use for register -->
  <!--<param name="register-transport" value="udp"/>-->
  <!--How many seconds before a retry when a failure or timeout occurs -->
  <!--<param name="retry-seconds" value="30"/>-->
  <!--Use the callerid of an inbound call in the from field on outbound calls via this gateway -->
  <!--<param name="caller-id-in-from" value="false"/>-->
  <!--extra sip params to send in the contact-->
  <!--<param name="contact-params" value=""/>-->
  <!-- Put the extension in the contact -->
  <!--<param name="extension-in-contact" value="true"/>-->
  <!--send an options ping every x seconds, failure will unregister and/or mark it down-->
  <!--<param name="ping" value="25"/>-->
  <!--<param name="cid-type" value="rpid"/>-->
  <!--rfc5626 : Abilitazione rfc5626 ///-->
  <!--<param name="rfc-5626" value="true"/>-->
  <!--rfc5626 : extra sip params to send in the contact-->
  <!--<param name="reg-id" value="1"/>-->
  <!--</gateway>-->
</include>
```

各参数的含义如下：

* caller-id-in-from：是否将主叫号码放到SIP的From首部。省略则为false，即放到Remote-Party-ID首部。
* contact-params：SIP的Contact首部的额外参数。具体参数需根据实际情况而定。
* expire-seconds：SIP的REGISTER消息的Expires首部。省略则为3600。单位为秒。
* extension：来话中的分机号，即被叫号码。省略则取username。
* from-domain：SIP的From首部的domain值。省略则取realm。
* from-user：SIP的From首部的用户信息。省略则取username。
* outbound-proxy：呼叫使用的代理服务器，即SIP的INVITE消息发送的地址。可与register-proxy不同。
* password：密码。必需。
* ping：发送SIP的OPTIONS消息的时间间隔。如果失败，则从该网关注销，并将其设置为down状态。
* proxy：代理服务器。省略则取realm。
* realm：SIP网关服务器地址。可为域名或IP，如果端口不是5060则需加上“:端口”。省略则取网关名，即gateway的name值。必需。
* register：是否注册到网关。省略则为true。如果是中继，则不需要注册。
* register-proxy：注册使用的代理服务器。省略则取realm。
* register-transport：SIP消息的承载。可为：tcp、udp。
* retry-seconds：注册失败或超时后，重新注册的等待秒数。
* username：用户名，会出现在SIP消息的Authorization头中。必需。

## sip_profiles/internal.xml

SIP internal Profile配置。这是文件内容的一部分：

```xml
<profile name="internal">
  <gateways>
  </gateways>

  <domains>
    <domain name="all" alias="true" parse="false"/>
  </domains>

  <settings>
    <param name="context" value="public"/>
    <param name="rfc2833-pt" value="101"/>
    <!-- port to bind to for sip traffic -->
    <param name="sip-port" value="$${internal_sip_port}"/>
    <param name="dialplan" value="XML"/>
    <param name="dtmf-duration" value="2000"/>
    <param name="inbound-codec-prefs" value="$${global_codec_prefs}"/>
    <param name="outbound-codec-prefs" value="$${global_codec_prefs}"/>
    <param name="rtp-timer-name" value="soft"/>
    <!-- ip address to use for rtp, DO NOT USE HOSTNAMES ONLY IP ADDRESSES -->
    <param name="rtp-ip" value="$${local_ip_v4}"/>
    <!-- ip address to bind to, DO NOT USE HOSTNAMES ONLY IP ADDRESSES -->
    <param name="sip-ip" value="$${local_ip_v4}"/>
    <param name="hold-music" value="$${hold_music}"/>
    <param name="apply-nat-acl" value="nat.auto"/>
  </settings>
</profile>
```

`profile`的`name`用于在其它地方引用。

`param`的`name`可为：

* aggressive-nat-detection：对SIP包进行深度NAT检测，以决定使用那个IP地址。
* apply-inbound-acl：SIP来话的ACL鉴权方式。
* apply-nat-acl：处理SIP包NAT的ACL鉴权方式。nat.auto为使用RFC1918规定的私网地址并去掉本地网络的地址来替换Contact信息。
* auth-calls：是否对来话进行鉴权。即所有从该Profile进来的INVITE请求都需要经过Digest验证。
* codec-prefs：来话和去话支持的媒体编码列表。
* context：来话进入Dialplan中哪个Context进行路由。本地用户设置的user_context的优先级更高。
* dialplan：Dialplan类型。
* disable-rtp-auto-adjust：是否禁用RTP自动调整。
* dtmf-type：DTMF类型。inband为使用带内DTMF，info为使用SIP的INFO消息。
* enable-100rel：是否	在收到183消息时发送PRACK证实消息。
* ext-rtp-ip：NAT环境中公网的RTP IP。该设置会影响SDP中的IP。可为：IP地址（直接指定公网IP）、autonat:IP地址（直接指定公网IP，更智能）、stun:STUN服务器地址（使用STUN服务器获取公网IP）、host:域名（通过DNS域名解析获取公网IP）、auto（自动检测公网IP）、auto-nat（如路由器支持NAT-PMP或uPnP，则自动使用这些协议获取公网IP）。
* ext-sip-ip：NAT环境中公网的SIP IP。该设置会影响SDP中的IP。可为：IP地址（直接指定公网IP）、autonat:IP地址（直接指定公网IP，更智能）、stun:STUN服务器地址（使用STUN服务器获取公网IP）、host:域名（通过DNS域名解析获取公网IP）、auto（自动检测公网IP）、auto-nat（如路由器支持NAT-PMP或uPnP，则自动使用这些协议获取公网IP）。
* force-register-db-domain：强制注册使用此域存储在数据库中。可删除。
* force-register-domain：强制注册使用此域。可删除。
* force-subscription-domain：强制订阅使用此域。可删除。
* inbound-codec-prefs：来话支持的媒体编码列表。
* inbound-bypass-media：来话是否启用媒体绕过模式。
* inbound-late-negotiation：是否使用晚协商。
* inbound-zrtp-passthru
* manage-presence：是否启用列席。
* manage-shared-appearence：是否启用共享线路呈现。
* media-option：媒体选项。为resume-media-on-hold表示，如果FreeSWITCH处于媒体绕过状态，当话机按下Hold键时，FreeSWITCH将回到有媒体的状态。为bypass-media-after-att-xfer表示，如在执行出席转移前处于媒体绕过状态，转接执行时通过re-INVITE要回媒体，等到转接结束后再回到媒体绕过状态。
* NDLB-force-rport：是否使用NDLB配置启用rport参数支持。
* odbc-dsn：数据库ODBC的DSN。格式为：[odbc://]NAME:[USERNAME]:[PASSWORD]。
* outbound-codec-prefs：去话支持的媒体编码列表。
* rtp-ip：RTP的IP地址。
* sip-capture：是否使用Homer方式进行抓包。
* sip-ip：SIP的IP地址。
* sip-port：该Profile监听的SIP端口。
* track-calls：是否将通话的当前状态实时写到数据库中。

# vars.xml

使用`X-PRE-PROCESS`指令的`set`语句定义一些全局变量。这是文件内容的一部分：

```xml
  <X-PRE-PROCESS cmd="set" data="domain=$${local_ip_v4}"/>
  <X-PRE-PROCESS cmd="set" data="domain_name=$${domain}"/>
  <X-PRE-PROCESS cmd="set" data="hold_music=local_stream://moh"/>
  <X-PRE-PROCESS cmd="set" data="use_profile=external"/>
```

`data`各变量的含义如下：

* api_on_startup：FreeSWITCH启动时执行的API及参数。
* default_password：默认用户密码。
* domain：租户域。默认是主机的IP地址。
* domain_name：租户域的名字。用于Dialplan中。
* global_codec_prefs：全局支持的媒体编码列表。
* internal_sip_port：internal Profile监听的SIP端口。
* outbound_codec_prefs：去话支持的媒体编码列表。
* sound_prefix：声音文件的路径前缀。

另外，系统自动设置的变量有（系统自动设置的变量是在vars.xml加载前设置的，因而可以在vars.xml中覆盖它们）：

* hostname：主机名。
* local_ip_v4：本地IP（IPv4）。
* local_mask_v4：本地掩码（IPv4）。
* local_ip_v6：本地IP（IPv6）。
* switch_serial：随机产生的序列号。
* base_dir：FreeSWITCH的安装路径。
* recordings_dir：录音路径，可以在启动时用-recordings选项指定。
* sound_prefix：声音文件的路径前缀，默认是/usr/local/freeswitch/sounds/en/us/callie。
* sounds_dir：声音文件的目录，默认是/usr/local/freeswitch/sounds。
* core_uuid：自动生成的UUID字符串，用于标志本FreeSWITCH实例，每次启动都会改变。
* zrtp_enabled：是否启用zrtp。
* nat_public_addr：检测到的NAT公网地址。
* nat_private_addr：NAT私网地址。
* nat_type：NAT类型。

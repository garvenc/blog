本文更新于2022-04-23，使用FreeSWITCH 1.10.7。

假设已经在/usr/local/freeswitch安装FreeSWITCH，并在此目录启动一个实例。现需在另一个目录（笔者使用/usr/local/freeswitch2）启动另一个实例。

1. 复制文件。
	```shell
	mkdir /usr/local/freeswitch2
	mkdir /usr/local/freeswitch2/log
	mkdir /usr/local/freeswitch2/db
	cp -r /usr/local/freeswitch/conf /usr/local/freeswitch2/
	cp -r /usr/local/freeswitch/sounds /usr/local/freeswitch2/
	cp -r /usr/local/freeswitch/bin /usr/local/freeswitch2/
	```
1. 修改配置，防止冲突。
	* 修改Event Socket端口，配置文件为/usr/local/freeswitch2/conf/autoload_configs/event_socket.conf.xml，需修改如下的行：
		```xml
		<param name="listen-port" value="8021"/>
		```
		笔者将8021修改为18021（根据实际情况修改）。
	* 修改SIP端口，配置文件为/usr/local/freeswitch2/conf/vars.xml，需修改如下的行：
		```xml
		<X-PRE-PROCESS cmd="set" data="internal_sip_port=5060"/>
		<X-PRE-PROCESS cmd="set" data="internal_tls_port=5061"/>
		<X-PRE-PROCESS cmd="set" data="external_sip_port=5080"/>
		<X-PRE-PROCESS cmd="set" data="external_tls_port=5081"/>
		```
		笔者将5060、5061、5080、5081依次修改为15060、15061、15080、15081（根据实际情况修改）。
	* 修改RTP端口范围，配置文件为/usr/local/freeswitch2/autoload_configs/switch.conf.xml，需修改如下的行：
		```xml
		<param name="rtp-start-port" value="16384"/>
		<param name="rtp-end-port" value="32768"/>
		```
		笔者将16384、32768依次修改为46384、52768（根据实际情况修改）。
	* 修改其它可能冲突的模块配置。
1. 启动服务。
	```shell
	/usr/local/freeswitch2/bin/freeswitch -conf /usr/local/freeswitch2/conf -log /usr/local/freeswitch2/log -db /usr/local/freeswitch2/db
	```
	可加上其它启动选项。
1. 使用服务。
	* 使用fs_cli连接（根据实际情况指定端口）。
		```shell
		/usr/local/freeswitch2/bin/fs_cli -P 18021
		```
	* SIP客户端可连接至15060端口（根据实际情况指定端口）。

本文更新于2022-04-04，使用FreeSWITCH 1.10.7。

以下示例，假定源代码目录为/usr/src/freeswitch，安装目录为/usr/local/freeswitch。

1. 安装中文Say模块（包括zh和zh_CN的Say接口）：
	1. 进入源代码目录：
		```shell
		cd /usr/src/freeswitch
		```
	1. 修改modules.conf，将模块所在行的注释符去掉，即将`#say/mod_say_zh`修改为：
		```
		say/mod_say_zh
		```
	1. 编译安装：
		```shell
		./bootstrap.sh -j
		./configure
		make mod_say_zh
		make mod_say_zh-install
		```
	1. 查看模块是否已安装，即安装目录下模块文件是否存在：
		```shell
		ls -l /usr/local/freeswitch/mod/ | grep mod_say_zh
		```
	1. 将模块设置为服务器启动时自动加载：

		修改/usr/local/freeswitch/conf/autoload_configs/modules.conf.xml，将模块所在行的`<!--`和`-->`注释符去掉，即将`<!-- <load module="mod_say_zh"/> -->`修改为：
		```xml
		<load module="mod_say_zh"/>
		```
	1. 重启FreeSWITCH或如下手动加载模块：
		```shell
		/usr/local/freeswitch/bin/fs_cli
		```
		执行：
		```
		load mod_say_zh
		```
1. 安装中文语音提示音文件：
	```shell
	cd /usr/src/freeswitch
	make sounds-zh-cn-sinmei-8000
	make sounds-zh-cn-sinmei-8000-install
	```
	可以使用查看提示音文件是否存在：
	```shell
	ls -l /usr/local/freeswitch/sounds/zh/cn/sinmei
	```
1. 创建中文语言配置：
	```shell
	cd /usr/local/freeswitch/conf/lang
	mkdir zh
	cd zh
	touch zh.xml
	```
	修改zh.xml的内容为：
	```xml
	<include>
	  <language name="zh" say-module="zh" sound-prefix="$${sounds_dir}/zh/cn/sinmei" tts-engine="" tts-voice="">
	    <phrases>
	      <macros>
	      </macros>
	    </phrases>
	  </language>
	</include>
	```
1. 设置FreeSWITCH加载中文语言配置：
	
	在/usr/local/freeswitch/conf/freeswitch.xml中`<section name="languages" description="Language Management">`这一节点内增加：
	```xml
	<X-PRE-PROCESS cmd="include" data="lang/zh/*.xml"/>
	```
1. 重新加载配置文件：
	```shell
	/usr/local/freeswitch/bin/fs_cli
	```
	执行：
	```
	reloadxml
	```

---

可以使用以下方式验证：

1. 修改Dialplan：

	在/usr/local/freeswitch/conf/dialplan/default.xml的`<context name="default">`这一节点内增加：
	```xml
	    <extension name="testing">
	        <condition field="destination_number" expression="^1234$">
	            <action application="answer"/>
	            <action application="say" data="zh NUMBER ITERATED 1234567"/>
	        </condition>
	    </extension>
	```
1. 重新加载配置文件：
	```shell
	/usr/local/freeswitch/bin/fs_cli
	```
	执行：
	```
	reloadxml
	```
1. 拨打1234，试验是否能听到“一二三四五六七”。

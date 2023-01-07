本文更新于2022-11-08。

[TOC]

# 版本

FreeSWITCH版本号由3部分构成，以点隔开。第1部分为主版本号，第2部分为次版本号（偶数为稳定版，奇数为开发版），第3部分为补丁及更新的标志。

# 源代码

在Debian 10下按照官方文档安装的源代码目录为/usr/src/freeswitch。

* clients/：
	* flex：Flash软电话客户端。
* freeswitch-sounds-*.tar.gz：声音文件压缩包。
* libs/：第三方库和模块的目录。
	* esl/：ESL库目录。
		* fs_cli.c：fs_cli代码。
		* java/：ESL的Java库目录。
			* Makefile：Java的ESL库的安装文件。
			* org/
				* freeswitch/
					* esl/：Java库实现。包含若干个.java文件。
		* src/：ESL库源代码目录。
			* include：ESL库头文件目录。
			* switch_apr.c：对所有用到的APR函数进行封装。
		* testclient.c：C语音ESL内连模式客户端示例。
		* testserver.c：C语音ESL外连模式服务端示例。
* modules.conf：指定安装哪些模块。
* scripts/：脚本目录。
	* javascript/：JavaScript脚本目录。
	* lua/：Lua脚本目录。
	* perl/：Perl脚本目录。
		* add_user：在安装目录创建用户配置文件的工具。
	* yes_no.gram：语音识别语法文件示例。
* src/：源代码目录。
	* fs_encode.c：fs_encode的源代码。
	* include/：头文件目录。
		* private/：除了核心外，其它所有模块不应该使用的头文件目录。
			* switch_core_pvt.h
		* switch_event.h：事件头文件。
		* switch_frame.h：帧定义头文件。
		* switch_types.h：大部分的宏、常量、枚举的定义。
	* mod/：模块的源代码目录。
		* applications/：各种应用功能相关模块的目录。
			* mod_commands/：
				* mod_commands.c
			* mod_dptools/：
				* mod_dptools.c
		* asr_tts/：语音识别及语音合成相关模块的目录。
		* codecs/：音视频编解码相关模块的目录。
		* dialplans/：Dialplan相关模块的目录。
		* directories/：LDAP相关模块的目录。
		* endpoints/：Endpoint相关模块的目录。
			* mod_sofia/：
				* mod_sofia.c
				* sofia.c
		* event_handlers/：事件处理相关模块的目录。
		* formats/：文件格式相关模块的目录。
			* mod_sndfile/
		* languages/：嵌入式语言相关模块的目录。
			* mod_python/：Python模块目录。
				* python_example.py：Python脚本示例。
		* logger/：日志相关模块的目录。
		* say/：不同语种的语言相关模块的目录。
		* sdk/：包含一些例子和宏的目录。
			* autotools
				* src
					* mod_example.c：最精简的模块示例。
		* timers/：时钟和定时器相关模块的目录。
		* xml_int/：XML相关模块的目录。
	* switch.c：main函数。
	* switch_apr.c：APR库。
	* switch_channel.c：核心封装的Channel操作。
	* switch_console.c；控制台。
	* switch_core.c：核心代码。
	* switch_core_io.c：核心IO。
	* switch_core_media.c：核心媒体协商和媒体处理。
	* switch_core_state_machine.c：核心Channel状态机。
	* switch_dso.c：动态链接库。
	* switch_event.c：事件系统。
	* switch_ivr.c：媒体处理逻辑。
	* switch_ivr_async.c：媒体处理逻辑异步处理。
	* switch_ivr_bridge.c：媒体处理逻辑话路桥接。
	* switch_ivr_originate.c：媒体处理逻辑发起呼叫。
	* switch_loadable_module.c：可加载模块。
	* switch_rtp.c：RTP媒体收发。
	* switch_xml.c：XML解析。
	* tone2wav.c：tone2wav的源代码。

# 在Debian 10下安装

官方文档参见：[https://freeswitch.org/confluence/display/FREESWITCH/Debian+10+Buster](https://freeswitch.org/confluence/display/FREESWITCH/Debian+10+Buster)。

```shell
apt-get update && apt-get install -yq gnupg2 wget lsb-release
wget -O - https://files.freeswitch.org/repo/deb/debian-release/fsstretch-archive-keyring.asc | apt-key add -
 
echo "deb http://files.freeswitch.org/repo/deb/debian-release/ `lsb_release -sc` main" > /etc/apt/sources.list.d/freeswitch.list
echo "deb-src http://files.freeswitch.org/repo/deb/debian-release/ `lsb_release -sc` main" >> /etc/apt/sources.list.d/freeswitch.list
 
apt-get update
  
# Install dependencies required for the build
apt-get build-dep freeswitch
  
# then let's get the source. Use the -b flag to get a specific branch
cd /usr/src/
git clone https://github.com/signalwire/freeswitch.git -bv1.10 freeswitch
cd freeswitch
  
# Because we're in a branch that will go through many rebases, it's
# better to set this one, or you'll get CONFLICTS when pulling (update).
git config pull.rebase true
  
# ... and do the build
./bootstrap.sh -j
./configure
make
make install
```

安装过程中可能出现以下错误（也可在执行上述安装脚本前先行处理）：

1. 运行`./bootstrap.sh -j`可能出现以下错误：
	1. `apt-get install autoconf`可处理以下错误：
		> build-requirements: autoconf not found.
		>                     You need autoconf version 2.59 or newer installed
		>                     to build FreeSWITCH from source.
	1. `apt-get install libtool-bin`可处理以下错误：
		> build-requirements: libtool not found.
		>                     You need libtool version 1.5.14 or newer to build FreeSWITCH from source.
1. 运行`./configure`可能出现以下错误：
	1. `apt-get install pkg-config`可处理以下错误：
		> *** The pkg-config script could not be found. Make sure it is
		> *** in your path, or set the PKG_CONFIG environment variable
		> *** to the full path to pkg-config.
		> *** Or see http://www.freedesktop.org/software/pkgconfig to get pkg-config.
	1. `apt-get install libspandsp3-dev`可处理以下错误：
		> checking for spandsp >= 3.0... configure: error: no usable spandsp; please install spandsp3 devel package or equivalent
	1. `apt-get install libsofia-sip-ua-dev`可处理以下错误：
		> checking for sofia-sip-ua >= 1.13.6... configure: error: no usable sofia-sip; please install sofia-sip-ua devel package or equivalent
	1. `apt-get install libsqlite3-dev`可处理以下错误：
		> checking for sqlite3 >= 3.6.20... Package sqlite3 was not found in the pkg-config search path. Perhaps you should add the directory containing `sqlite3.pc' to the PKG_CONFIG_PATH environment variable No package 'sqlite3' found
		> configure: error: Library requirements (sqlite3 >= 3.6.20) not met; consider adjusting the PKG_CONFIG_PATH environment variable if your libraries are in a nonstandard prefix so pkg-config can find them.
	1. `apt-get install libcurl4-openssl-dev`可处理以下错误：
		> checking for libcurl >= 7.19... Package libcurl was not found in the pkg-config search path. Perhaps you should add the directory containing `libcurl.pc' to the PKG_CONFIG_PATH environment variable No package 'libcurl' found
		> configure: error: Library requirements (libcurl >= 7.19) not met; consider adjusting the PKG_CONFIG_PATH environment variable if your libraries are in a nonstandard prefix so pkg-config can find them.
	1. `apt-get install libpcre3-dev`可处理以下错误：
		> checking for libpcre >= 7.8... Package libpcre was not found in the pkg-config search path. Perhaps you should add the directory containing `libpcre.pc' to the PKG_CONFIG_PATH environment variable No package 'libpcre' found
		> configure: error: Library requirements (libpcre >= 7.8) not met; consider adjusting the PKG_CONFIG_PATH environment variable if your libraries are in a nonstandard prefix so pkg-config can find them.
	1. `apt-get install libspeexdsp-dev`可处理以下错误：
		> checking for speex >= 1.2rc1 speexdsp >= 1.2rc1... Package speex was not found in the pkg-config search path. Perhaps you should add the directory containing `speex.pc' to the PKG_CONFIG_PATH environment variable No package 'speex' found Package speexdsp was not found in the pkg-config search path. Perhaps you should add the directory containing `speexdsp.pc' to the PKG_CONFIG_PATH environment variable No package 'speexdsp' found
		> configure: error: Library requirements (speex >= 1.2rc1 speexdsp >= 1.2rc1) not met; consider adjusting the PKG_CONFIG_PATH environment variable if your libraries are in a nonstandard prefix so pkg-config can find them.
	1. `apt-get install libldns-dev`可处理以下错误：
		> checking for libldns-fs >= 1.6.6... checking for libldns >= 1.6.6... checking for ldns_str2rdf_a in -lldns... no
		> configure: error: You need to either install libldns-dev or disable mod_enum in modules.conf
	1. `apt-get install libedit-dev`可处理以下错误：
		> configure: error: You need to either install libedit-dev (>= 2.11) or configure with --disable-core-libedit-support
1. 运行`make`可能出现以下错误（**大多数错误处理后都需要重新执行`./configure`甚至`./bootstrap.sh -j`**）：
	1. `apt-get install yasm`可处理以下错误：
		> Neither yasm nor nasm have been found. See the prerequisites section in the README for more info.
	1. `apt-get install libavformat-dev libswscale-dev`可处理以下错误：
		> Makefile:1477: *** You must install libavformat-dev and libswscale-dev to build mod_av.  Stop.
	1. `apt-get install liblua5.2-dev`可处理以下错误：
		> mod_lua.cpp:37:10: fatal error: lua.h: No such file or directory
		>  \#include "lua.h"
		>           ^~~~~~~
		> compilation terminated.
		> make[4]: *** [Makefile:1041: libluamod_la-mod_lua.lo] Error 1
	1. `apt-get install libopus-dev `可处理以下错误：
		> Makefile:968: *** You must install libopus-dev to build mod_opus.  Stop
	1. `apt-get install libpq-dev`可处理以下错误：
		> Makefile:966: *** You must install libpq-dev to build mod_pgsql.  Stop.
	1. `apt-get install libks`可处理以下错误：
		> akefile:977: *** You must install libks to build mod_signalwire.  Stop.
	1. `apt-get install signalwire-client-c`可处理以下错误：
		> Makefile:972: *** You must install signalwire-client-c to build mod_signalwire.  Stop
	1. `apt-get install uuid-dev`可处理以下错误：
		> usr/include/libks/ks_types.h:172:11: fatal error: uuid/uuid.h: No such file or directory
		>   \#include <uuid/uuid.h>
		>            ^~~~~~~~~~~~~
		> compilation terminated.
		> make[4]: *** [Makefile:733: mod_signalwire_la-mod_signalwire.lo] Error 1
	1. `apt-get install libsndfile-dev`可处理以下错误：
		> Makefile:1452: *** You must install libsndfile-dev to build mod_sndfile.  Stop.

默认安装至/usr/local/freeswitch。安装完成后，可以选择创建软连接：

```shell
ln -s /usr/local/freeswitch/bin/freeswitch /usr/local/bin
ln -s /usr/local/freeswitch/bin/fs_cli /usr/local/bin
```

## 指定安装目录

除了下面这一步，其它与上面相同：

```shell
./configure --prefix=DIR
```

## 安装PostgresSQL原生支持

除了下面这一步，其它与上面相同：

```shell
./configure --enable-core-pgsql-support
```

## 安装Java的ESL库

除了下面这一步，其它与上面相同：

```shell
./configure --with-java=JDKPATH
```

## 其他安装操作

* 安装声音文件：
	* make sounds-install：安装标准提示音文件。
	* make moh-install：安装标准MOH音乐文件。
	* make cd-sounds-install：安装CD音质提示音文件。
	* make cd-moh-install：安装CD音质MOH音乐文件。
	* make hd-sounds-install：安装高清提示音文件。
	* make hd-moh-install：安装高清MOH音乐文件。
	* make uhd-sounds-install：安装超高清提示音文件。
	* make uhd-moh-install：安装超高清MOH音乐文件。
	* make sounds-XX：下载非英语提示音文件。使用对应的语言替换XX，如简体中文使用zh-cn-sinmei-8000。
	* make sounds-XX-install：安装非英语提示音文件。使用对应的语言替换XX，如简体中文使用zh-cn-sinmei-8000。
* 安装模块：
	* make MOD：编译模块（**需先修改modules.conf，再重新执行`./configure`甚至`./bootstrap.sh -j`**）。MOD一般以mod_开头，core为核心。
	* make MOD-install：安装模块（***需先make MOD*）。
* 其他：
	* make current：升级到最新版本。
	* make sure：重新编译。
	* make samples：安装或重新安装配置文件。

## 直接进入模块目录安装模块

```shell
cd /usr/src/freeswitch
cd src/mod/KIND/MOD
make
make install
```

# 使用Makefile安装

```shell
wget http://www.freeswitch.org.cn/Makefile && make install
```

# 在Windows下安装

安装包下载地址为：[https://files.freeswitch.org/windows/installer/](https://files.freeswitch.org/windows/installer/)。

根据实际情况选择，笔者使用：[https://files.freeswitch.org/windows/installer/x64/FreeSWITCH-1.10.7-Release-x64.msi](https://files.freeswitch.org/windows/installer/x64/FreeSWITCH-1.10.7-Release-x64.msi)。

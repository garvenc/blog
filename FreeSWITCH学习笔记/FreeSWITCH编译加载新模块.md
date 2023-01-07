本文更新于2022-05-03，使用FreeSWITCH 1.10.7。

以下示例，假定源代码目录为/usr/src/freeswitch，安装目录为/usr/local/freeswitch，安装的新模块为mod_say_zh。

1. 如模块未编译，需先编译模块。有两种方式：
	* 第一种：
		1. 进入模块的源代码目录：
			```shell
			cd /usr/src/freeswitch
			cd src/mod/say/mod_say_zh
			```
		1. 编译安装：
			```shell
			make install
			```
	* 第二种：
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
			如提示缺少依赖库，则先使用`apt install`安装依赖库，再重新执行此处的命令。
1. 查看模块是否已安装，即安装目录下模块文件是否存在：
	```shell
	ls -l /usr/local/freeswitch/mod/ | grep mod_say_zh
	```
1. 如有需要，可将模块设置为服务器启动时自动加载：
	
	修改/usr/local/freeswitch/conf/autoload_configs/modules.conf.xml，将模块所在行的`<!--`和`-->`注释符去掉，即将`<!-- <load module="mod_say_zh"/> -->`修改为：
		```xml
		<load module="mod_say_zh"/>
		```
1. 如未设置启动时自动加载，也可手动加载模块：
	```shell
	/usr/local/freeswitch/bin/fs_cli
	```
	执行：
	```
	load mod_say_zh
	```

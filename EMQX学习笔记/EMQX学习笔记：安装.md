本文更新于2023-03-01。

[TOC]

下载网址：[https://www.emqx.com/zh/try?product=broker](https://www.emqx.com/zh/try?product=broker)。更多版本可访问：[https://www.emqx.com/zh/downloads/broker](https://www.emqx.com/zh/downloads/broker)。

# Docker下运行

使用EMQX 4.4.14。

安装文档：[https://www.emqx.io/docs/zh/v4.4/getting-started/install.html](https://www.emqx.io/docs/zh/v4.4/getting-started/install.html)

```shell
docker pull emqx/emqx:4.4.14
docker create --name emqx -p 1883:1883 -p 8081:8081 -p 8083:8083 -p 8883:8883 -p 8084:8084 -p 18083:18083 emqx/emqx:4.4.14
docker start emqx
```

容器中的目录结构：

* /opt/emqx/：安装目录。
	* etc/：配置文件目录。
		* certs/：证书目录。
	* data/：数据目录。
		* loaded_plugins：已加载插件的描述文件。

# CentOS 7.5下安装

使用EMQX 4.4.3。

安装文档：[https://www.emqx.io/docs/zh/v4.4/getting-started/install.html](https://www.emqx.io/docs/zh/v4.4/getting-started/install.html)。

1. 安装。
	```shell
	wget https://www.emqx.com/zh/downloads/broker/4.4.3/emqx-4.4.3-otp24.1.5-3-el7-amd64.rpm
	sudo yum install emqx-4.4.3-otp24.1.5-3-el7-amd64.rpm
	```
1. 启动。
	```shell
	sudo emqx start
	```
	如果启动失败，出现以下提示：
	```
	{"init terminating in do_boot",{undef,[{crypto,start,[],[]},{init,start_em,1,[]},{init,do_boot,3,[]}]}}
	init terminating in do_boot ({undef,[{crypto,start,[],[]},{init,start_em,1,[]},{init,do_boot,3,[]}]})
	
	Crash dump is being written to: erl_crash.dump...done
	FATAL: Unable to start Erlang.
	Please make sure openssl-1.1.1 (libcrypto) and libncurses are installed.
	Also ensure it's running on the correct platform,
	this EMQX release is built for 24.1.5-3-x86_64-pc-linux-gnu-64-el7
	```
	则需要先安装openssl-1.1.1再重新执行`sudo emqx start`启动：
	```shell
	wget https://www.openssl.org/source/openssl-1.1.1d.tar.gz
	tar xf openssl-1.1.1d.tar.gz
	cd openssl-1.1.1d
	./config
	make
	sudo make install
	```
1. 使用系统服务启动。
	```shell
	sudo systemctl start emqx
	```
	如果启动失败，查看`systemctl status emqx`出现以下提示：
	```
	[/usr/lib/systemd/system/emqx.service:14] Executable path is not absolute, ignoring: bash /usr/bin/emqx start
	[/usr/lib/systemd/system/emqx.service:17] Executable path is not absolute, ignoring: bash /usr/bin/emqx stop
	emqx.service lacks both ExecStart= and ExecStop= setting. Refusing.
	```
	则需要修改服务文件/usr/lib/systemd/system/emqx.service，将`bash`命令改为绝对路径`/usr/bin/bash`，再重新加载服务配置并启动：
	```shell
	sudo systemctl daemon-reload
	sudo systemctl start emqx
	```
1. 如果防火墙端口未开启，则按需开启以下端口。
	* 1883：Broker TCP端口（mqtt://）。
	* 8883：Broker SSL端口（mqtts://）。
	* 18083：Dashboard端口（Web仪表盘）。
1. 如发现emqx不停重启，酌情取消服务的自动重启。

	在/usr/lib/systemd/system/emqx.service中对应行的前面加上`#`注释符：
	```
	# Restart=on-failure
	```

# 在Windows下安装

1. 下载安装包。下载地址：[https://www.emqx.com/zh/downloads/broker/4.4.3/emqx-4.4.3-otp24.2.1-windows-amd64.zip](https://www.emqx.com/zh/downloads/broker/4.4.3/emqx-4.4.3-otp24.2.1-windows-amd64.zip)
1. 解压。
1. 进入解压目录，运行`./emqx/bin/emqx.cmd start`。

# 端口

* 1883：External监听器的MQTT端口。
* 8081：HTTP API端口。
* 8883：External监听器的MQTTS端口。
* 11883：Internal监听器的MQTT端口。
* 18083：Dashboard端口。

# Dashboard

* 默认账号：admin
* 默认密码：public

# MQTTX

MQTTX是一个mqtt客户端。

官网：[https://mqttx.app/zh](https://mqttx.app/zh)。

Windows安装包下载地址：[https://www.emqx.com/zh/downloads/MQTTX/v1.7.3/MQTTX-Setup-1.7.3-x64.exe](https://www.emqx.com/zh/downloads/MQTTX/v1.7.3/MQTTX-Setup-1.7.3-x64.exe)。

说明文档：[https://mqttx.app/zh/docs](https://mqttx.app/zh/docs)。

如未安装emqx，可以使用EMQX官方的公共Broker调试：broker.emqx.io。

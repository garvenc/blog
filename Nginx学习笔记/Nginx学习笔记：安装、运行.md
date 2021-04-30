本文更新于2021-04-29，使用nginx 1.16。

[TOC]

# 安装

各系统的安装包可于官网[http://nginx.org/en/download.html](http://nginx.org/en/download.html)下载。

## Windows下安装

从[http://nginx.org/download/nginx-1.16.1.zip](http://nginx.org/download/nginx-1.16.1.zip)下载安装包，并解压，解压目录即为安装目录。

## Linux下安装

1. 安装依赖库（按需选择版本），用于编译时指定依赖库的源代码目录：

	* CentOS（7.5）下安装依赖库：

		```shell
		yum install pcre-devel
		yum install zlib-devel
		yum install openssl-devel
		```
	* Debian（8.9）下安装依赖库（CentOS下也可使用）：

		```shell
		cd /usr/local/src
		wget https://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz
		wget http://www.zlib.net/zlib-1.2.11.tar.gz
		wget https://www.openssl.org/source/openssl-1.1.1f.tar.gz
		tar -xvz -f pcre-8.44.tar.gz
		tar -xvz -f zlib-1.2.11.tar.gz
		tar -xvz -f openssl-1.1.1f.tar.gz
		```
		可删除.tar.gz文件。
1. 创建用户：

	```shell
	groupadd nginxd
	useradd -g nginxd nginxd
	```
1. 创建安装目录：

	```shell
	cd /usr/local
	mkdir nginx
	chown nginxd:nginxd nginx
	```
1. 安装nginx：

	* CentOS下安装：

		```shell
		wget http://nginx.org/download/nginx-1.16.1.tar.gz
		tar -xvz -f nginx-1.16.1.tar.gz
		cd nginx-1.16.1
		./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre
		make
		make install
		```
	* Debian下安装（CentOS下也可使用）：
	
		```shell
		wget http://nginx.org/download/nginx-1.16.1.tar.gz
		tar -xvz -f nginx-1.16.1.tar.gz
		cd nginx-1.16.1
		./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module \
			--with-openssl=/usr/local/src/openssl-1.1.1f --with-zlib=/usr/local/src/zlib-1.2.11 --with-pcre=/usr/local/src/pcre-8.44
		make
		make install
		```
	
	`./configure`参数含义如下：
	
	* --prefix：安装目录。
	* --with-http_ssl_module：SSL模块，用于支持SSL，需要安装依赖openssl。
	* --with-http_stub_status_module：监控模块，用于监控nginx状态。
	* --with-pcre：PCRE模块，用于支持正则表达式，需要安装依赖pcre。
	* --with-zlib：ZLIB模块，用于支持压缩算法，需安装依赖zlib。
	
	注意`make install`会覆盖此前安装的所有文件，包括配置文件。
1. 权限配置

	```shell
	chown -R nginxd:nginxd /usr/local/nginx
	vi /usr/local/nginx/nginx.conf
	```
	
	修改/usr/local/nginx/confi/nginx.conf的user为nginxd。

# 运行

需先进入安装目录。

运行nginx时不使用参数，会启动nginx服务，包含主进程和工作进程两个nginx进程。

运行nginx时也可使用以下参数：

* -?, -h：查看帮助。
* -c filename：设置配置文件名（默认为conf/nginx.conf）。
* -g directives：设置配置文件之外的全局指令。
* -p prefix：设置路径前缀（默认无）。
* -q：检查配置期间禁止打印非错误消息。
* -s signal：发送信号至主进程，signal可为：
	* stop：强制停止服务。
	* quit：优雅退出服务。
	* reopen：打开新的日志文件。需要先重命名原来的日志文件。
	* reload：重新加载配置文件，服务不会中止。
* -t：检查配置后退出。
* -T：检查配置，转储后退出。
* -v：打印版本后退出。
* -V：打印版本和configure编译配置参数后退出。

# 日志

请求日志为安装目录下的logs/access.log。

请求日志使用组合日志格式，各字段依次为：

1. 客户端主机名或IP。
1. ident查找的用户名。
1. 已认证的用户名。
1. 日期时间，以“[]”扩起。
1. 请求行，以“""”引起。
1. 响应状态码。
1. 响应Content-Length，如无则为0。
1. 请求Referer。
1. 请求User-Agent。

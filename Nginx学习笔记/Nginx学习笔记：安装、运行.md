本文更新于2024-08-15。

[TOC]

# 安装

各系统的安装包可于官网[https://nginx.org/en/download.html](https://nginx.org/en/download.html)下载。

## Windows下安装

从[http://nginx.org/download/nginx-1.16.1.zip](http://nginx.org/download/nginx-1.16.1.zip)下载安装包，并解压，解压目录即为安装目录。

## Linux下安装

本文的安装目录为/usr/local/nginx，可自行定义。

1. 创建用户：
	```shell
	sudo groupadd nginxd
	sudo useradd -g nginxd nginxd
	```
1. 创建安装目录：
	```shell
	cd /usr/local
	sudo mkdir nginx
	sudo chown nginxd:nginxd nginx
	```
1. 安装依赖库及nginx：
	* CentOS 7.5下安装nginx 1.16：
		```shell
		sudo yum install openssl-devel
		sudo yum install pcre-devel
		sudo yum install zlib-devel

		wget http://nginx.org/download/nginx-1.16.1.tar.gz
		tar -xvz -f nginx-1.16.1.tar.gz
		cd nginx-1.16.1
		./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre
		make
		sudo make install
		```
	* Debian 8.9下安装nginx 1.16（CentOS 7.5下安装nginx 1.16也适用）：
		```shell
		cd /usr/local/src
		sudo wget https://www.openssl.org/source/openssl-1.1.1f.tar.gz
		sudo wget https://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz
		sudo wget http://www.zlib.net/zlib-1.2.11.tar.gz
		sudo tar -xvz -f openssl-1.1.1f.tar.gz
		sudo tar -xvz -f pcre-8.44.tar.gz
		sudo tar -xvz -f zlib-1.2.11.tar.gz

		wget http://nginx.org/download/nginx-1.16.1.tar.gz
		tar -xvz -f nginx-1.16.1.tar.gz
		cd nginx-1.16.1
		./configure --prefix=/usr/local/nginx --with-http_stub_status_module \
			--with-http_ssl_module --with-openssl=/usr/local/src/openssl-1.1.1f \
			--with-pcre=/usr/local/src/pcre-8.44 \
			--with-zlib=/usr/local/src/zlib-1.2.11
		make
		sudo make install
		```
	* Debian 12.6 (bookworm)下安装nginx 1.26.1：
		```shell
		sudo apt install libssl-dev
		sudo apt install libpcre3-dev
		
		wget https://nginx.org/download/nginx-1.26.1.tar.gz
		tar -xvz -f nginx-1.26.1.tar.gz
		cd nginx-1.26.1
		./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre
		make
		sudo make install
		```

	`./configure`参数含义如下：
	
	* --prefix=DIR：安装目录。
	* --with-http_ssl_module：启用ngx_http_ssl_module模块，用于支持SSL。需要安装openssl依赖。
	* --with-http_stub_status_module：启用ngx_http_stub_status_module模块，用于监控nginx状态。
	* --with-openssl=DIR：OpenSSL库的目录。
	* --with-pcre[=DIR]：使用PCRE库支持正则表达式。需要安装pcre依赖。
	* --with-zlib=DIR：使用zlib库支持压缩算法。需安装zlib依赖。
	
	**注意`make install`会覆盖此前安装的所有文件，包括配置文件。**
1. 权限配置：
	```shell
	sudo chown -R nginxd:nginxd /usr/local/nginx
	```
	在/usr/local/nginx/conf/nginx.conf开头增加一行：
	```
	user nginxd;
	```

# 运行

官方文档：[https://nginx.org/en/docs/switches.html](https://nginx.org/en/docs/switches.html)。

可执行程序为安装目录下的sbin/nginx（本文为/usr/local/nginx/sbin/nginx）。

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
	* reload：重新加载配置文件。会启动新的工作线程，并优雅关闭旧的工作线程。
* -t：检查配置后退出。
* -T：检查配置后退出，会将配置打印至标准输出。
* -v：打印版本号后退出。
* -V：打印版本号、编译器版本号、编译配置参数后退出。

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

# systemd配置

如需使用systemd启动nginx，则修改配置文件/lib/systemd/system/nginxd.service：

```
[Unit]
Description=Nginx
After=network.target
 
[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
```

# Docker下运行

```shell
docker container create --network host --name nginx nginx
docker start nginx
```

容器中的目录结构：

* /etc/nginx/：配置文件、证书文件目录。
	* nginx.conf：配置文件。
	* cert/：证书文件目录。
* /usr/sbin/nginx：可执行文件。
* /usr/share/nginx/html：静态文件目录。

在容器中执行nginx命令，如：

```shell
docker container exec -it nginx nginx -t
```

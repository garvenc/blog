本文更新于2020-06-20，使用Go 1.18.3，操作系统为Ubuntu 18.04。

官方文档参看[https://golang.google.cn/doc/install](https://golang.google.cn/doc/install)。

1. 下载安装包。版本按需选择，官网下载地址为：[https://golang.google.cn/dl/](https://golang.google.cn/dl/)，如：
	```shell
	wget https://golang.google.cn/dl/go1.18.3.linux-amd64.tar.gz
	```
1. 删除旧版本，解压新版本。
	```shell
	test -e /usr/local/go && sudo rm -r /usr/local/go
	sudo tar -C /usr/local -xzf go1.18.3.linux-amd64.tar.gz
	```
1. 设置PATH环境变量。在`$HOME/.profile`（有些系统是`$HOME/.bash_profile`）或`/etc/profile`中增加一行：
	```shell
	export PATH=$PATH:/usr/local/go/bin
	```
1. 重新读取环境变量。执行`source $HOME/.profile`（有些系统是`sorce $HOME/.bash_profile`）或`source /etc/profile`。
1. 查看Go版本。
	```shell
	go version
	```

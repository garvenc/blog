本文更新于2025-05-26，使用go1.24.3，操作系统为Ubuntu 18.04。

# 安装Go

官方文档参看：Download and install（[https://golang.google.cn/doc/install](https://golang.google.cn/doc/install)）。

1. 下载安装包。版本按需选择，官网下载地址为：[https://golang.google.cn/dl/](https://golang.google.cn/dl/)，如：
	```shell
	wget -v https://dl.google.com/go/go1.23.4.linux-amd64.tar.gz
	```
1. 使用root权限，删除旧版本，解压新版本。
	```shell
	rm -rf /usr/local/go && tar -C /usr/local -xzf go1.23.4.linux-amd64.tar.gz
	```
1. 设置PATH环境变量（如此前已设置环境变量，则跳过）。在`$HOME/.profile`（有些系统是`$HOME/.bash_profile`）或`/etc/profile`中增加一行：
	```shell
	export PATH=$PATH:/usr/local/go/bin
	```
1. 重新读取环境变量（如此前已设置环境变量，则跳过）。执行`source $HOME/.profile`（有些系统是`sorce $HOME/.bash_profile`）或`source /etc/profile`。
1. 查看Go版本。
	```shell
	go version
	```

# 安装多个Go版本

官方文档参看：Managing Go installations（[https://go.dev/doc/manage-install](https://go.dev/doc/manage-install)）。

1. 需已安装git命令。
1. 安装Go指定版本（示例为go1.20.14）。
	```shell
	go install golang.org/dl/go1.20.14@latest
	go1.20.14 download
	```
1. 使用指定版本运行go，如：
	```shell
	go1.20.14 version
	```

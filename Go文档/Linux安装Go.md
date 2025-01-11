本文更新于2024-12-21，使用go1.23.4，操作系统为Ubuntu 18.04。

官方文档参看[https://golang.google.cn/doc/install](https://golang.google.cn/doc/install)。

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

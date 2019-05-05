# 说明

本文更新于2018-08-17，使用Go 1.10.3，操作系统为Deepin 15.4。

官方文档参看[https://golang.google.cn/doc/install](https://golang.google.cn/doc/install)。

# 安装

```shell
wget https://dl.google.com/go/go1.10.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.10.3.linux-amd64.tar.gz
sudo vi /etc/profile
source /etc/profile
```

上述步骤在`/etc/profile`中增加一行：

```
export PATH=$PATH:/usr/local/go/bin
```

# 环境变量

Go默认使用`GOROOT=/usr/local/go`和`GOPATH=$HOME/go`，如需自定义，需自行在`$HOME/.profile`（有些系统是`$HOME/.bash_profile`）中设置。

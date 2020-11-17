本文更新于2020-11-10，使用Go 1.15.4，操作系统为Ubuntu 16.04。

官方文档参看[https://golang.google.cn/doc/install](https://golang.google.cn/doc/install)。

```shell
wget https://dl.google.com/go/go1.15.4.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.15.4.linux-amd64.tar.gz
```

在`/etc/profile`或`$HOME/.profile`（有些系统是`$HOME/.bash_profile`）中增加一行：

```
export PATH=$PATH:/usr/local/go/bin
```

执行`source /etc/profile`或`source $HOME/.profile`（有些系统是`$HOME/.bash_profile`）。

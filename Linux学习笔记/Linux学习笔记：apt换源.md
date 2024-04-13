本文更新于2024-03-19，使用debian 11。

Debian官方源更换为阿里云源：

```shell
sudo sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list
```

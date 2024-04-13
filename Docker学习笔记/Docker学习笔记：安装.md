本文更新于2024-02-03。

[TOC]

# 安装

官方文档参看：[https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)。可从[https://download.docker.com/](https://download.docker.com/)选择相应版本的安装包。

安装后的服务名为docker。

## 在Debian安装

安装文档：[https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/)。

### 在Debian 11安装

使用Docker 25.0.2。

```shell
wget https://download.docker.com/linux/debian/dists/bullseye/pool/stable/amd64/containerd.io_1.6.28-1_amd64.deb
wget https://download.docker.com/linux/debian/dists/bullseye/pool/stable/amd64/docker-ce-cli_25.0.2-1~debian.11~bullseye_amd64.deb
wget https://download.docker.com/linux/debian/dists/bullseye/pool/stable/amd64/docker-ce_25.0.2-1~debian.11~bullseye_amd64.deb
wget https://download.docker.com/linux/debian/dists/bullseye/pool/stable/amd64/docker-buildx-plugin_0.12.1-1~debian.11~bullseye_amd64.deb
wget https://download.docker.com/linux/debian/dists/bullseye/pool/stable/amd64/docker-compose-plugin_2.24.5-1~debian.11~bullseye_amd64.deb

sudo dpkg -i containerd.io_1.6.28-1_amd64.deb
sudo dpkg -i docker-ce-cli_25.0.2-1~debian.11~bullseye_amd64.deb
sudo dpkg -i docker-ce_25.0.2-1~debian.11~bullseye_amd64.deb
sudo dpkg -i docker-buildx-plugin_0.12.1-1~debian.11~bullseye_amd64.deb
sudo dpkg -i docker-compose-plugin_2.24.5-1~debian.11~bullseye_amd64.deb
```

如出错，可能需要安装依赖包：

```
apt install iptables
```

## 使用脚本安装

使用Docker 19.03.12，操作系统为Debian 10。

不建议在生产环境使用。

```shell
wget -O get-docker.sh https://get.docker.com
sudo sh get-docker.sh
```

# 用户组

可将用户（如：$USER）加入docker用户组，并重新登录，来获得执行命令的权限。否则，需要使用root权限才能运行。

```shell
sudo gpasswd -a $USER docker
```

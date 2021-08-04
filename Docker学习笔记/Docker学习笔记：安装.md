本文更新于2021-06-29。

[TOC]

# 安装

官方文档参看：[https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)。

可从[https://download.docker.com/](https://download.docker.com/)选择相应版本的安装包。

安装后的服务名为docker。

## 使用dpkg包安装

### 在Debian 8.9安装

使用Docker 18.06.3。

```shell
sudo apt install libltdl7

wget https://download.docker.com/linux/debian/dists/jessie/pool/stable/amd64/docker-ce_18.06.3~ce~3-0~debian_amd64.deb
sudo dpkg -i docker-ce_18.06.3~ce~3-0~debian_amd64.deb
```

### 在Debian 10.5安装

使用Docker 20.10.7。

```shell
wget https://download.docker.com/linux/debian/dists/buster/pool/stable/amd64/containerd.io_1.4.6-1_amd64.deb
wget https://download.docker.com/linux/debian/dists/buster/pool/stable/amd64/docker-ce-cli_20.10.7~3-0~debian-buster_amd64.deb
wget https://download.docker.com/linux/debian/dists/buster/pool/stable/amd64/docker-ce_20.10.7~3-0~debian-buster_amd64.deb

sudo dpkg -i containerd.io_1.4.6-1_amd64.deb
sudo dpkg -i docker-ce-cli_20.10.7~3-0~debian-buster_amd64.deb
sudo dpkg -i docker-ce_20.10.7~3-0~debian-buster_amd64.deb
```

## 使用脚本安装

使用Docker 19.03.12，操作系统为Debian 10。

不建议在生产环境使用。

```shell
wget -O get-docker.sh https://get.docker.com
sudo sh get-docker.sh
```

# 用户组

可将用户（如:$USER）加入docker用户组，来获得执行命令的权限。否则，需要使用root权限才能运行。

```shell
sudo gpasswd -a $USER docker
```

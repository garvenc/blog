本文更新于2024-08-14。

[TOC]

# 安装

官方安装文档：[https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)

可从[https://download.docker.com/](https://download.docker.com/)选择相应版本的安装包，或使用阿里云镜像站[https://mirrors.aliyun.com/docker-ce/](https://mirrors.aliyun.com/docker-ce/)。

安装后的服务名为docker。

## 在Debian安装

安装文档：[https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/)。

使用Docker 27.1.2，操作系统为Debian 12.6 (bookworm)。

```shell
wget https://mirrors.aliyun.com/docker-ce/linux/debian/dists/bookworm/pool/stable/amd64/containerd.io_1.7.20-1_amd64.deb
wget https://mirrors.aliyun.com/docker-ce/linux/debian/dists/bookworm/pool/stable/amd64/docker-ce-cli_27.1.2-1~debian.12~bookworm_amd64.deb
wget https://mirrors.aliyun.com/docker-ce/linux/debian/dists/bookworm/pool/stable/amd64/docker-ce_27.1.2-1~debian.12~bookworm_amd64.deb
wget https://mirrors.aliyun.com/docker-ce/linux/debian/dists/bookworm/pool/stable/amd64/docker-buildx-plugin_0.16.2-1~debian.12~bookworm_amd64.deb
wget https://mirrors.aliyun.com/docker-ce/linux/debian/dists/bookworm/pool/stable/amd64/docker-compose-plugin_2.29.1-1~debian.12~bookworm_amd64.deb

sudo dpkg -i containerd.io_1.7.20-1_amd64.deb
sudo dpkg -i docker-ce-cli_27.1.2-1~debian.12~bookworm_amd64.deb
sudo dpkg -i docker-ce_27.1.2-1~debian.12~bookworm_amd64.deb
sudo dpkg -i docker-buildx-plugin_0.16.2-1~debian.12~bookworm_amd64.deb
sudo dpkg -i docker-compose-plugin_2.29.1-1~debian.12~bookworm_amd64.deb
```

以上使用阿里云镜像站，如使用官方地址，则将https://mirrors.aliyun.com/docker-ce替换为https://download.docker.com。

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

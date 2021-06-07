本文更新于2021-06-07。

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

# 环境变量

* DOCKER_HOST：指定docker客户端连接的守护进程地址，类似-H选项，如：tcp://HOST:PORT。

# 目录和文件

* /var/lib/docker/：保存Docker镜像、容器、容器配置。
* /var/lib/docker/containers/：保存容器。
* /var/lib/docker/volumes/：保存卷。
* /var/run/docker.sock：绑定的Unix套接字。

配置文件可能为：

* 对于Ubuntu或Debian系统：/etc/default/docker
* 对于Read Hat或Fedora系统：/etc/sysconfig/docker
* 对于使用Upstart的系统：/etc/init/docker.conf
* 对于使用Systemd的系统：/usr/lib/systemd/system/docker.service

# 基本概念

* Registry：存储仓库。如：Docker Hub。
* 仓库：存储镜像。
* 镜像：创建容器。
* 容器：可以启动。

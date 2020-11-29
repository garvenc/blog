本文更新于2020-11-25，使用Docker Compose 1.27.4，操作系统为Debian 10。

[TOC]

**说明：本文中，大写为自定义变量，根据实际情况填写。使用`<>`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

Docker Compose是一个简单的容器编配工具。Docker Compose中的服务也即容器。

# 安装

官方文档参阅：[https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)

本文使用Docker Compose 1.27.4，其它版本需自行指定。

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

# docker-compose命令

官方文档参阅：[https://docs.docker.com/compose/reference/overview/](https://docs.docker.com/compose/reference/overview/)

可使用以下选项：

* --help：查看帮助。等同于`docker-compose help`。
* --version：查看版本。类似于`docker-compose version`。

默认会连接到本地的Docker守护进程，也可使用DOCKER_HOST环境变量指定。

需在docker-compose.yml文件所在的目录执行大多数子命令。

## docker-compose help

查看帮助。

```shell
docker-compose help [SUBCMD]
```

SUBCMD即本文描述的子命令。`docker-compose help SUBCMD`等同于`docker-compose SUBCMD --help`。

## docker-compose kill

向服务发送信号，可用来杀死服务。

```shell
docker-compose kill [SERVICE [...]]
```

## docker-compose logs

查看服务的日志。

```shell
docker-compose logs [SERVICE [...]]
```

## docker-compose ps

查看服务的运行状态。

```shell
docker-compose ps [SERVICE [...]]
```

返回包括以下列：

* Name：服务名。
* Command：启动服务的命令。
* State：服务的状态。
* Ports：映射的端口。

## docker-compose rm

删除服务。

```shell
docker-compose rm [SERVICE [...]]
```

会提示进行确认。

## docker-compose start

启动服务。

```shell
docker-compose start [SERVICE [...]]
```

## docker-compose stop

停止服务。

```shell
docker-compose stop [SERVICE [...]]
```

## docker-compose up

启动docker-compose.yml指定的服务，并将日志输出合并到一起。

```shell
docker-compose up [-d] [SERVICE [...]]
```

* -d：以守护进程的模式来运行服务。

服务名使用docker-compose.yml所在的目录名和文件中指定的服务的名字作为前缀，使用数字作为后缀。如：dir_name_1。

# docker-compose.yml文件

官方文档参阅：[https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/)

示例：

```
web:
  image: ubuntu
  command: python app.py
  ports:
   - "5000:5000"
  volumes:
   - .:/app
  links:
   - redis
redis:
  image: redis
```

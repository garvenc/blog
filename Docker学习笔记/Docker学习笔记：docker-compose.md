本文更新于2021-07-22，使用Docker Compose 1.27.4，操作系统为Debian 10。

[TOC]

**说明：本文中，大写为自定义变量，根据实际情况填写。使用`<>`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

Docker Compose是一个简单的容器编配工具，可管理单台主机上的Docker。

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

默认会连接到本地的Docker引擎，也可使用DOCKER_HOST环境变量指定。

可执行以下子命令，大多数子命令需在docker-compose.yml文件所在的目录下执行。各子命令可使用以下公共选项：

* --help：查看子命令的帮助。

**下文中自定义变量的说明：**

* SERVICE：服务名，即docker-compose.yml文件中指定的服务的名字。

## docker-compose build

编译构建服务。

```shell
docker-compose build [OPTIONS] [SERVICE[ ...]]
```

OPTIONS可为：

* --build-arg KEY=VALUE：为服务设置编译时的变量。
* --force-rm：总是删除中间过程产生的容器。
* --no-cache：编译构建时不使用缓存。
* --pull：总是拉取最新的镜像版本。

## docker-compose down

下线服务，同时删除容器、网络、磁盘卷、镜像。

```shell
docker-compose down [OPTIONS]
```

OPTIONS可为：

* --remove-orphans：删除未在docker-compose.yml文件中定义的服务的容器。
* --rmi all|local：删除镜像。必需为其一：all为删除所有服务的镜像。local为删除未在docker-compose.yml文件中自定义标签的镜像。
* -v|--volumes：删除docker-compose.yml中定义的磁盘卷和匿名磁盘卷。

默认只会移除：docker-compose.yml定义的容器和网络（如使用默认网络，也会被移除）。

## docker-compose help

查看帮助。

```shell
docker-compose help [SUBCMD]
```

SUBCMD即本文描述的子命令。`docker-compose help SUBCMD`等同于`docker-compose SUBCMD --help`。

## docker-compose kill

向服务的容器发送信号，可用来杀死服务的容器。

```shell
docker-compose kill [SERVICE[ ...]]
```

## docker-compose logs

查看服务的容器的日志。

```shell
docker-compose logs [SERVICE[ ...]]
```

每一行带有缩短的服务名字（使用docker-compose.yml文件中指定的服务名作为前缀，使用数字作为后缀，如：name_1），并将各服务的日志合并输出。

## docker-compose ps

列出服务的容器。

```shell
docker-compose ps [SERVICE[ ...]]
```

返回包括以下列：

* Name：服务名字。使用docker-compose.yml所在的目录名和文件中指定的服务的名字作为前缀，使用数字作为后缀，如：dir_name_1。
* Command：启动服务的命令。
* State：服务的状态。
* Ports：映射的端口。

## docker-compose rm

删除已停止的服务的容器。

```shell
docker-compose rm [OPTIONS] [SERVICE[ ...]]
```

OPTIONS可为：

* -f|--force：强制删除，不进行确认。默认会提示进行确认。
* -s|--stop：在删除容器前先停止容器。
* -v：删除匿名磁盘卷。默认不会删除。

## docker-compose run

```shell
docker-compose run [OPTIONS] SERVICE [COMMAND [ARG[ ...]]
```

OPTIONS可为：

* -d|--detach：在后台运行容器。
* --entrypoint COMMAND [ARG[ ...]]：覆盖构建镜像时的ENTRYPOINT指令。
* -e KEY=VALUE：设置环境变量。可使用多次。
* --name NAME：容器的名字。
* --no-deps：不编译构建、创建（或重新创建）、启动其关联的服务。
* --rm：容器运行退出后自动删除容器，忽略后台运行的模式。
* -p|--publish：映射容器中的端口至宿主机端口。
* --service-ports：使用服务可用的端口执行命令，并映射到宿主机。
* -T：禁用伪tty终端。默认会分配一个伪tty终端。
* -u|--user USER|UID[:GROUP|GID]：以容器内部哪个用户运行，覆盖构建镜像时的USER指令。
* -v|--volume：挂载磁盘卷。
* -w|--workdir DIR：设置容器启动时的工作目录，覆盖构建镜像时的WORKDIR指令。

## docker-compose start

启动服务已经停止的容器。

```shell
docker-compose start [SERVICE[ ...]]
```

## docker-compose stop

停止服务正在运行的容器，但不删除容器。

```shell
docker-compose stop [OPTIONS] [SERVICE[ ...]]
```

## docker-compose up

为服务编译构建、创建（或重新创建）、启动容器，并连接到容器将日志输出合并到一起。如果服务的容器正在运行，则会挑选出改变的内容停止并重新创建容器。该命令退出时，所有容器也会停止。

```shell
docker-compose up [OPTIONS] [SERVICE[ ...]]
```

OPTIONS可为：

* --abort-on-container-exit：如果有一个容器停止，则停止所有容器。与-d作用相反。
* --build：在启动容器之前编译构建镜像。
* -d|--detach：在后台运行容器。与--abort-on-container-exit作用相反。
* --exit-code-from SERVICE：返回指定服务的容器退出码。可与--abort-on-container-exit配合使用。
* --force-recreate：强制重新创建容器，即使没有改变。与--no-recreate作用相反。
* --no-build：不重新编译构建镜像，即使镜像已不存在。
* --no-color：使用黑白输出。
* --no-deps：只编译构建、创建（或重新创建）、启动改变了的服务，不编译构建、创建（或重新创建）、启动其关联的服务。
* --no-recreate：不重新创建容器，即使有改变。与--force-recreate作用相反。
* --remove-orphans：删除未在docker-compose.yml文件中定义的服务的容器。
* --scale SERVICE=NUM：伸缩服务的实例至指定的数量，会覆盖docker-compose.yml文件中定义的scale值。
* -t|--timeout DURATION：当容器在运行时停止容器的超时时间，单位为秒。默认为10。

# docker-compose.yml文件

官方文档参阅：[https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/)

示例：

```yml
version: "3.9"  # optional since v1.27.0
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

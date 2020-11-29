本文更新于2020-11-25，使用Docker 19.03.12，操作系统为Debian 10。

[TOC]

# 安装

```shell
wget -O install.sh https://get.docker.com
sudo sh install.sh
```

# 基本概念

* Registry：存储仓库。如：Docker Hub。
* 仓库：存储镜像。
* 镜像：创建容器。
* 容器：可以启动。

# 用户组

可将用户（如:USER）加入docker用户组，来获得执行命令的权限。否则，需要使用root权限才能运行。

```shell
sudo gpasswd -a USER docker
```

# 环境变量

* DOCKER_HOST：指定docker客户端连接的守护进程地址，类似-H选项，如：tcp://HOST:PORT。

# dockerd服务

官方文档参阅：[https://docs.docker.com/engine/reference/commandline/dockerd/](https://docs.docker.com/engine/reference/commandline/dockerd/)

Docker守护进程，又称Docker服务器，或Docker引擎。

可使用以下选项：

* -H tcp://HOST:PORT|unix://SOCKFILE：指定监听的主机地址。
* --icc：只允许内部容器通讯，禁止没有链接的容器间通讯。
* --tls：启动TLS但不进行客户端认证。
* --tlscacert FILENAME：CA证书文件名。
* --tlscert FILENAME：服务器证书文件名。
* --tlskey FILENAME：服务器私钥文件名。
* --tlsverify：启用TLS并进行客户端认证。

## 目录和文件

* /var/lib/docker/：保存Docker镜像、容器、容器配置。
* /var/lib/docker/containers/：保存容器。
* /var/lib/docker/volumes/：保存卷。
* /var/run/docker.sock：绑定的Unix套接字。

配置文件可能为：

* 对于Ubuntu或Debian系统：/etc/default/docker
* 对于Read Hat或Fedora系统：/etc/sysconfig/docker
* 对于使用Upstart的系统：/etc/init/docker.conf
* 对于使用Systemd的系统：/usr/lib/systemd/system/docker.service

# docker命令

官方文档参阅：[https://docs.docker.com/engine/reference/commandline/cli/](https://docs.docker.com/engine/reference/commandline/cli/)

**说明：本节的小节中，大写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

可使用以下选项：

* --help：查看帮助。等同于`docker help`。
* --version：查看版本。等同于`docker version`。
* --tls：启动TLS但不进行服务端认证。
* --tlscacert FILENAME：CA证书文件名。
* --tlscert FILENAME：客户端证书文件名。
* --tlskey FILENAME：客户端私钥文件名。
* --tlsverify：启用TLS并进行服务端认证。

可执行以下子命令，各子命令可使用以下公共选项：

* -H [HOST:]port：指定守护进程地址。默认读取环境变量DOCKER_HOST。
* --help：查看子命令的帮助。

## docker attach

附着到运行中的容器的会话上。会将宿主机的标准输入、标准输出、标准错误输出附着到容器所运行的进程上。

```shell
docker attach CONTAINER
```

CONTAINER可以使用容器名称或容器ID。

可能需要按下回车键才能进入会话。

## docker build

使用Dockerfile构建镜像。

```shell
docker build [OPTIONS] -t IMAGE DOCKERFILE_DIR
```

* -t IMAGE：指定镜像名称。
* --build-arg NAME=VALUE：指定构建时使用的变量。只能指定在ARG指令中定义过的变量。
* --no-cache：不使用构建缓存。

IMAGE的格式为REPOSITORY[:TAG]。

## docker commit

创建并提交镜像。不推荐使用此方法创建镜像，推荐使用Dockerfile定义文件和`docker build`。

```shell
docker commit [-m MESSAGE] [-a AUTHOR] CONTAINER IMAGE
```

* -a：指定作者。
* -m：指定提交信息。

CONTAINER可以使用容器名称或容器ID。IMAGE的格式为REPOSITORY[:TAG]。

## docker cp

从容器复制文件或复制文件至容器。

## docker create

创建容器，不会启动容器。

```shell
docker create -i -t [--name NAME] [OPTIONS] IMAGE [COMMAND]
docker create -d [--name NAME] [--restart WHEN] [OPTIONS] IMAGE [COMMAND]
```

OPTIONS用法参看`docker run`。

IMAGE的格式为[HOST:PORT/]REPOSITORY[:TAG]，如没有指定TAG，则默认使用latest。

COMMAND为要在容器中运行的命令，可使用参数。只当容器有命令处于运行状态时，容器才处于运行状态。

## docker exec

在容器内部运行新进程。

```shell
docker exec -i -t [-u USER] CONTAINER COMMAND
docker exec -d [-u USER] CONTAINER COMMAND
```

* -d：创建守护式后台进程。
* -i：开启STDIN。创建交互式前台进程必需该选项。
* -t：分配伪tty终端。创建交互式前台进程必需该选项。
* -u USER：执行进程的系统用户名或用户ID，格式为：USERNAME|UID[:GROUPNAME|GID]。

CONTAINER可以使用容器名称或容器ID。COMMAND为要在容器中运行的命令，可使用参数。

## docker help

查看帮助。

```shell
docker help [SUBCMD]
```

SUBCMD即本文描述的子命令。`docker help SUBCMD`等同于`docker SUBCMD --help`，也可以使用`man docker-SUBCMD`查看子命令的帮助。

## docker history

查看镜像的历史信息，可得知构建镜像的每一层。

```shell
docker history CONTAINER
```

返回依次为从顶部到底部的各镜像层，包括以下列：

* IMAGE：镜像ID。
* CREATED：镜像层创建时间。
* CREATED BY：创建镜像层的命令或指令。
* SIZE：镜像层大小。
* COMMENT：注释。

## docker images

列出镜像。

```shell
docker images [-a] [-q] [IMAGE]
```

* -a：列出所有镜像，不指定默认不列出中间层的镜像。
* -q：安静模式，只列出镜像ID。

IMAGE的格式为[HOST:PORT/]REPOSITORY[:TAG]。

返回包括以下列：

* REPOSITORY：仓库名。
* TAG：镜像标签。
* IMAGE ID：镜像ID。
* CREATED：镜像的创建时间。
* SIZE：镜像大小。

## docker info

查看Docker信息，包括容器和镜像的数量、执行和存储驱动、基本配置等。

```shell
docker info
```

## docker inspect

查看容器的底层信息。

```shell
docker inspect [-f FORMAT] CONTAINER [...]
```

* -f FROMAT：指定输出的格式，使用Go语言的模板（text/template）语法，如：`'{{.Name}} {{.State.Running}}'`。

CONTAINER可以使用容器名称或容器ID。

返回的JSON各字段含义如下：

* State：进程状态。
	* Pid：进程ID。
* Mounts：卷的挂载信息。
* NetworkSettings：网络配置信息。
	* Networks：各网络的信息。键为网络名。
		* 网络名：
			* IPAddress：容器在网络中的IP。

## docker login

登录到Registry，并将认证信息保存下来，保存于$HOME/.docker/config.json。

```shell
docker login
```

## docker logout

退出到Registry的登录。

```shell
docker logout
```

## docker kill

向容器发送信号，可用来杀死容器。

```shell
docker kill [-s SIGNAL] CONTAINER [...]
```

* -s SIGNAL：指定信号，默认为SIGKILL信号。

CONTAINER可以使用容器名称或容器ID。

## docker logs

获取容器标准输出和标准错误输出的日志。

```shell
docker logs [-f] [-t] [--tail N] CONTAINER
```

* -f：持续跟踪日志输出，类似`tail -f`。通过Ctrl+C退出日志跟踪。
* -t：为每条日志加上时间戳。
* --tail N：只获取最后的N条日志，N可为0。

CONTAINER可以使用容器名称或容器ID。

## docker network

Docker Networking。

```shell
docker network connect NETWORK CONTAINER
docker network create NETWORK
docker disconnect NETWORK CONTAINER
docker network inspect NETWORK
docker network ls
docker network rm NETWORK
```

各子命令如下：

* connect：将容器添加到网络中。
* create：创建网络。
* disconnect：将容器从网络中移除。
* inspect：查看网络的底层信息。
* ls：列出所有网络。
* rm：删除网络。

CONTAINER可以使用容器名称或容器ID。

## docker node

管理Swarm节点。

### docker node ls

列出Swarm所有节点。

```shell
docker node ls
```

返回包括以下列：

* ID：节点ID。后带“*”的ID为客户端当前连接的节点。
* HOSTNAME：节点主机名。
* STATUS：
* AVAILABILITY：
* MANAGER STATUS：
* ENGINE VERSION：Docker版本。

### docker node ps

列出Swarm节点上运行的任务。

```shell
docker node ps
```

返回包括以下列：

* ID：任务ID。
* NAME：任务名称。
* IMAGE：启动容器所使用的镜像。
* NODE：
* DESIRED STATE：
* CURRENT STATE：当前状态。
* ERROR：
* PORTS：

## docker port

查看容器和宿主机间的端口映射。

```shell
docker port CONTAINER [CONTAINER_PORT[/PROTOCOL]]
```

## docker ps

列出容器。

```shell
docker ps [-a] [-q]
docker ps -l [-q]
docker ps -n N [-q]
```

* -a：列出所有容器，不指定默认只列出运行中的容器。
* -l：列出最后创建的一个容器，无论其是否运行中。
* -n N：列出最后创建的N个容器，无论其是否运行中。
* -q：安静模式，只列出容器ID。
* --no-trunc：不对输出进行截断。

返回包括以下列：

* CONTAINER ID：容器ID。
* IMAGE：启动容器所使用的镜像。格式为[HOST:PORT/]REPOSITORY[:TAG]。
* COMMAND：容器中运行的命令。
* CREATED：容器的创建时间。
* STATUS：容器状态。运行中或已停止，包括相关的时间。
* PORTS：宿主机和容器间的端口映射。
* NAMES：容器名。

## docker pull

从仓库拉取镜像。

```shell
docker pull IMAGE
```

IMAGE的格式为[HOST:PORT/]REPOSITORY[:TAG]，如没有指定TAG，则默认使用latest。REPOSITORY部分，Docker Hub中的用户仓库格式为USERNAME/REPOSITORYNAME，顶层仓库格式为REPOSITORYNAME。

## docker push

推送镜像至Registry。

```shell
docker push IMAGE
```

IMAGE的格式为[HOST:PORT/]REPOSITORY[:TAG]，如没有指定TAG，则默认使用latest。。REPOSITORY部分，Docker Hub中的用户仓库格式为USERNAME/REPOSITORYNAME，顶层仓库格式为REPOSITORYNAME。

需先使用`docker login`登录。Docker Hub只能推送至自己的非自动构建的用户仓库。

如推送至非Docker Hub的Registry，需先使用`docker tag`。

## docker restart

重启容器。

```shell
docker restart CONTAINER [...]
```

CONTAINER可以使用容器名称或容器ID。

## docker rm

删除容器。

```shell
docker rm [-f] CONTAINER [...]
```

* -f：强制删除容器，即使容器还在运行中。

CONTAINER可以使用容器名称或容器ID。

## docker rmi

删除镜像。

```shell
docker rmi IMAGE [...]
```

IMAGE的格式为[HOST:PORT/]REPOSITORY[:TAG]，如没有指定TAG，则默认使用latest。

显示的每一行表示删除一个镜像层。

## docker run

创建并启动容器。

```shell
docker run -i -t [--name NAME] [OPTIONS] IMAGE [COMMAND]
docker run -d [--name NAME] [--restart WHEN] [OPTIONS] IMAGE [COMMAND]
```

OPTIONS可为：

* **-d**：创建守护式容器。
* -e NAME=VALUE：设置环境变量。
* **-h HOSTNAME：指定容器的主机名。默认使用容器ID作为主机名**
* **-i**：为容器开启STDIN。交互式容器必需该选项。
* **-p [[HOST_IP:][HOST_PORT]:]CONTAINER_PORT[/PROTOCOL]**：指定开放给宿主机的端口。如不指定HOST_PORT，则宿主机使用32768~61000间的随机端口。可使用多次。
* -P：开放在Dockerfile中使用EXPOSE指令公开的所有端口，使用32768~61000间的随机端口。
* **-t**：为容器分配伪tty终端。交互式容器必需该选项。
* -u USER|UID[:GROUP|GID]：指定启动时以系统的那个用户运行，覆盖构建镜像时的USER指令。
* **-v HOST_DIR:CONTAINER_DIR[:rw|ro]**：将宿主机的目录作为卷挂载到容器中。如目录不存在，都会自动创建。
* -w DIR：设置容器启动时的工作目录，覆盖构建镜像时的WORKDIR指令。
* --add-host HOSTHOSTNAME:IP：添加主机解析至容器/etc/hosts文件中。
* --cidfile FILENAME：将容器ID保存至文件。
* --dns SERVER：设置DNS服务器。
* --dns-search DOMAIN：设置DNS搜索域。
* --entrypoint COMMAND:设置入口点，覆盖构建镜像时的ENTRYPOINT指令。
* --expose PORTS：指定容器需公开的端口。
* --link SERVICE_CONTAINER:LINK_ALIAS：创建客户服务链接。
* --log-driver DRIVER：指定日志驱动。默认为json-file，其为`docker logs`提供基础；syslog将禁用`docker logs`并将日志输出到syslogd；none禁用日志，并禁用`docker logs`。
* **--name NAME**：指定容器名称，不指定默认自动生成名称。只能使用大小写字母、数字、圆点、横线、下划线（即匹配正则表达式：^[a-zA-Z0-9.-_]+$），且必需唯一。
* **--net NETWORK：指定容器使用的网络。**
* --privileged：使用特权模式运行容器。容器对宿主机拥有root权限，有一定的安全风险，需在确保可信时使用。
* --restart WHEN：指定当容器停止运行时，何时自动重启容器。默认为no，不自动重启；on-failure[:N]为当退出码不为0时才自动重启，并可指定最多重启的次数。
* --rm：容器运行退出后自动删除容器。
* --volumes-from CONTAINER：将指定的容器中的所有卷都加入新创建的容器中。CONTAINER可以使用容器名称或容器ID。可使用多次。

IMAGE的格式为[HOST:PORT/]REPOSITORY[:TAG]，如没有指定TAG，则默认使用latest。

COMMAND为要在容器中运行的命令，可使用参数。只当容器有命令处于运行状态时，容器才处于运行状态。

交互式容器会使用root用户登录至容器中。

## docker search

从仓库查找镜像。

```shell
docker search IMAGE_KEYWORD
```

## docker service

管理Swarm服务。

### docker service create

创建Swarm服务。会选择某个Swarm节点创建并启动容器。

```shell
docker service create [--name NAME] [OPTIONS] IMAGE [COMMAND]
```

OPTIONS可为：

* **-p [[HOST_IP:][HOST_PORT]:]CONTAINER_PORT[/PROTOCOL]**：指定开放给宿主机的端口。如不指定HOST_PORT，则宿主机使用32768~61000间的随机端口。可使用多次。
* **--name NAME**：指定服务名称，不指定默认自动生成名称。只能使用大小写字母、数字、圆点、横线、下划线（即匹配正则表达式：^[a-zA-Z0-9.-_]+$），且必需唯一。

### docker service ls

列出所有Swarm服务。

```shell
docker service ls
```

返回包括以下列：

* ID：服务ID。
* NAME：服务名称。
* MODE：
* REPLICAS：
* IMAGE：启动容器所使用的镜像。
* PORTS：宿主机和容器间的端口映射。

## docker start

启动容器。

```shell
docker start CONTAINER [...]
```

CONTAINER可以使用容器名称或容器ID。

## docker stats

持续显示容器的状态统计。

```shell
docker stats CONTAINER [...]
```

CONTAINER可以使用容器名称或容器ID。

## docker stop

停止容器。该命令会向Docker容器进程发送SIGTERM信号。如果想强制杀死容器，需使用`docker kill`。

```shell
docker stop CONTAINER [...]
```

CONTAINER可以使用容器名称或容器ID。

## docker swarm

管理Swarm。

### docker swarm init

当前节点作为管理节点，初始化Swarm集群。返回节点ID、集群TOKEN、管理节点监听的IP和端口。

```shell
docker swarm init
```

### docker swarm join

当前节点加入Swarm集群。

```shell
docker swarm join --token TOKEN MANAGER_HOST_PORT
```

### docker swarm leave

当前节点退出Swarm集群。

```shell
docker swarm leave [-f]
```

* -f：强制退出，即使当前节点是集群的最后一个管理节点。

## docker tag

为镜像打上Registry的标签。

```shell
docker tag IMAGE REGISTRY_IMAGE
```

IMAGE的格式为REPOSITORY[:TAG]，如没有指定TAG，则默认使用latest。

REGISTRY_IMAGE的格式为HOST:PORT/REPOSITORY[:TAG]。

## docker top

查看容器内运行的进程。

```shell
docker top CONTAINER
```

CONTAINER可以使用容器名称或容器ID。

## docker version

查看版本。

```shell
docker version
```

## docker volume

管理卷。

各子命令如下：

* create
* inspect
* ls
* prune
* rm

## docker wait

等待容器退出，并返回退出码。

```shell
docker wait CONTAINER [...]
```

CONTAINER可以使用容器名称或容器ID。

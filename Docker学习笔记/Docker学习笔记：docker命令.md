本文更新于2023-03-02，使用Docker 23.0.1，操作系统为Debian 11。

[TOC]

**说明：下文中，大写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

# 基本概念

* 注册服务器（registry）：存储仓库。如：Docker Hub（[https://hub.docker.com/](https://hub.docker.com/)）。
* 仓库（repository）：存储镜像。
* 镜像（image）：创建容器。
* 容器（container）：应用运行的实例。只当容器有命令处于运行状态时，容器才处于运行状态。
* 磁盘卷（volume）
* 网络（network）
* 引擎（engine）：又称Docker服务器，或Docker守护进程。
* 节点（node）：加入集群的引擎。
* 集群（swarm）：由节点构成，包括管理节点（manager）和工作节点（worker）。
* 任务（task）：服务在集群的节点中执行任务。
* 服务（service）：在集群中的节点执行任务。

# docker命令

官方文档参阅：[https://docs.docker.com/engine/reference/commandline/cli/](https://docs.docker.com/engine/reference/commandline/cli/)

环境变量：

* DOCKER_HOST：引擎地址，即-H选项。
* DOCKER_TLS_VERIFY：启用TLS并进行服务端认证，即--tlsverify选项。使用任意非空值。
* HTTP_PROXY
* HTTPS_PROXY
* NO_PROXY

可使用以下选项：

* --help：查看帮助。等同于`docker help`。
* --version：查看版本。
* --tls：启动TLS但不进行服务端认证。
* --tlscacert FILENAME：CA证书文件名。
* --tlscert FILENAME：客户端证书文件名。
* --tlskey FILENAME：客户端私钥文件名。
* --tlsverify：启用TLS并进行服务端认证。

可执行以下子命令，各子命令可使用以下公共选项：

* -H unix://SOCKFILE|tcp://HOST[:PORT]|fd://[FDNUMBER]：指定引擎地址。默认为unix:///var/run/docker.sock（windows默认为tcp://127.0.0.1:2376）。
* --help：查看子命令的帮助。

**下文中自定义变量的说明：**

* CONTAINER：容器名称或容器ID。
* IMAGE：镜像名称或镜像ID。镜像名称格式为[HOST[:PORT]/][USERNAME/]REPOSITORY[:TAG|@DIGEST]，Docker Hub的用户仓库为USERNAME/REPOSITORY，顶层仓库为REPOSITORY，如没有指定TAG，则默认使用latest。
* NETWORK：网络名称或网络ID。
* NODE：集群节点ID。
* SERVICE：服务名或服务ID。
* VOLUME：磁盘卷名称或磁盘卷ID。

## docker attach

将宿主机的标准输入、标准输出、标准错误输出附着到运行中的容器，容器转为前台运行。等同于`docker container attach`。

```shell
docker attach [OPTIONS] CONTAINER
```

## docker build

使用Dockerfile构建镜像。等同于`docker image build`。

```shell
docker build [OPTIONS] DOCKERFILE_DIR|URL|-
```

## docker commit

使用容器创建镜像，不会包含挂载的磁盘卷中的数据。等同于`docker container commit`。

```shell
docker commit [OPTIONS] CONTAINER IMAGE
```

## docker container

管理容器。

### docker container attach

将宿主机的标准输入、标准输出、标准错误输出附着到运行中的容器，容器转为前台运行。等同于`docker attach`。

```shell
docker container attach [OPTIONS] CONTAINER
```

OPTIONS可为：

* --detach-keys：（覆盖卸载容器的key序列号。）
* --no-stdin：不附着标准输入。默认为false。
* --sig-proxy：代理进程接收的所有信号。默认为true。

可能需要按下回车键才能进入会话。

### docker container commit

使用容器创建镜像，不会包含挂载的磁盘卷中的数据。等同于`docker commit`。

```shell
docker container commit [OPTIONS] CONTAINER IMAGE
```

OPTIONS可为：

* -a|--author AUTHOR：指定作者。
* -c|--change DOCKERFILE_INSTRUCTION：创建时先应用Dockerfile指令。可使用多次。
* -m|--message MESSAGE：指定提交信息。
* -p|--pause：提交时暂停容器。默认为true。

不推荐使用此方法创建镜像，推荐使用Dockerfile定义文件和`docker build`。

### docker container cp

在容器和宿主机之间复制文件，容器停止运行时也可复制。等同于`docker cp`。

```shell
docker container cp [OPTIONS] CONTAINER:CONTAINER_PATH HOST_PATH
docker container cp [OPTIONS] HOST_PATH CONTAINER:CONTAINER_PATH
```

在容器创建的文件的所有者为容器中启动进程的用户，在宿主机创建的文件的所有者为宿主机的当前用户。

### docker container create

创建容器，不会启动容器。等同于`docker create`。

```shell
docker container create [OPTIONS] IMAGE [COMMAND [ARG[ ...]]
```

OPTIONS可为：

* --add-host HOSTNAME:IP：添加主机至IP的解析至容器中。
* -a|--attach STREAM：附着标准流至容器，可为stdin、stdout、stderr，可使用多次。
* --cidfile FILENAME：将容器ID保存至文件。
* --cpus N：容器的CPU个数。
* --dns SERVER：设置DNS服务器。
* --dns-option OPTION：设置DNS选项。
* --dns-search DOMAIN：设置DNS搜索域。
* --entrypoint COMMAND [ARG[ ...]]：设置入口点，覆盖构建镜像时的ENTRYPOINT指令。
* -e|--env VAR[=VALUE]：设置环境变量，如不使用=VALUE则取$VAR的值。该选项优先级比--env-file高。可使用多次。
* --env-file FILENAME：从文件中读取环境变量。每行的格式为VAR=VALUE。该选项优先级比--env低。
* --expose PORT：容器需公开的端口。可使用多次。
* --health-cmd：（检测容器健康的命令。）
* --health-interval：（检测容器健康的时间间隔。可使用的单位有ns、us、ms、s、m、h。）
* --health-retries：（连接失败指定的次数则汇报为不健康状态。）
* --health-start-period：（容器初始化之前的启动时间，超时后计入健康检测的重试次数。可使用的单位有ns、us、ms、s、m、h。）
* --health-timeout：（执行健康检测命令的超时时间。可使用的单位有ns、us、ms、s、m、h。）
* -h|--hostname HOSTNAME：容器的主机名。默认使用容器ID作为主机名。
* -i|--interactive：始终开启标准输入。创建交互式容器必需该选项。
* --ip IP：容器的IP。
* --keynel-memory N：内核内存字节限制。
* -l|--label KEY[=VALUE]：设置容器的标签，如不使用=VALUE则为空字符串。
* --link SERVICE_CONTAINER:SERVICE_CONTAINER_ALIAS：连接至另一个容器，SERVICE_CONTAINER_ALIAS为服务容器对当前容器在连接中的别名。可使用多次。
* --log-driver DRIVER：容器的日志驱动。json-file为`docker logs`提供基础，syslog为将日志输出到syslogd并禁用`docker logs`，none为禁用日志并禁用`docker logs`。默认为json-file。
* --log-opt KEY=VALUE：日志驱动选项。可使用多次。max-size为每份日志的最大大小，可使用的单位有k、m、g，默认为-1，即无限制。max-file为最大的日志份数，与max-size同时使用，默认为1。
* --mount MOUNT：关联文件系统并挂载到服务中。可为type=volume[,source=VOLUME_NAME],destination=CONTAINER_PATH[,volume-lobel="KEY=VALUE"[,...]]或type=bind,source=HOST_PATH,destination=CONTAINER_PATH[,volume-lobel="KEY=VALUE"[,...]]。
* --name NAME：容器名称，不指定自动生成名称。只能使用大小写字母、数字、圆点、横线、下划线（即匹配正则表达式：`^[a-zA-Z0-9.-_]+$`），且必需唯一。
* --network NETWORK：容器使用的网络模式。bridge为NAT桥接宿主机网络，host为共享宿主机网络，container:CONTAINER为共享其他容器网络，none为未配置网络，overlay为隧道技术。
* --no-healthcheck：禁用指定了HEALTHCHECK的容器。默认为false。
* --privileged：使用特权模式运行容器。容器对宿主机拥有root权限，有一定的安全风险，需在确保可信时使用。默认为false。
* -p|--publish [[HOST_IP:][HOST_PORT]:]CONTAINER_PORT[/PROTOCOL]：映射容器中的端口至宿主机端口。如不指定HOST_PORT，则宿主机使用32768~61000间的随机端口。可使用多次。
* -P|--publish-all：映射在Dockerfile中使用EXPOSE指令指定的所有端口至宿主机端口，使用32768~61000间的随机端口。默认为false。
* --read-only：挂载容器的root文件系统为只读模式。默认为false。
* --restart WHEN：当容器停止运行时，何时自动重启容器。no为不自动重启；on-failure[:N]为当退出码不为0时才自动重启，并可指定最多重启的次数；always为总是重启，无论退出码为何值，且总是在引擎启动时启动容器，不管容器是否被主动停止；unless-stopped为总是重启，无论退出码为何值，且总是在引擎启动时启动容器，除非容器被主动停止。默认为no。
* --rm：容器运行退出后自动删除容器。
* --stop-signal SIGNAL：停止容器的信号。默认为SIGTERM。
* --storage-opt KEY=VALUE：存储驱动选项。仅对devicemapper、btrfs、overlay2、windowsfilter、zfs文件系统有效。
* --tmpfs CONTAINER_DIR[:ro|rw[,noexec][,nosuid][,size=SIZE]]：挂载空的虚拟内存文件系统。
* -t|--tty：分配伪tty终端。创建交互式容器必需该选项。默认为false。
* --ulimit TYPE=SOFT_LIMIT[:HARD_LIMIT]：设置ulimit限制，如未设置HARD_LIMIT，则其与SOFT_LIMIT相同。
* -u|--user USER|UID[:GROUP|GID]：以容器内部哪个用户运行，覆盖构建镜像时的USER指令。默认为root。
* -v|--volume [VOLUME|HOST_DIR:]CONTAINER_DIR[:ro|rw]：将宿主机的目录作为卷挂载到容器中。宿主机和容器的目录不存在都会自动创建，容器目录必需为绝对路径。
* --volumes-from CONTAINER[:ro|rw]：将指定的容器中所有磁盘卷都加入新创建的容器。可使用多次。
* -w|--workdir DIR：设置容器启动时的工作目录，覆盖构建镜像时的WORKDIR指令。

### docker container exec

在运行中的容器内运行新进程。等同于`docker exec`。

```shell
docker container exec [OPTIONS] CONTAINER COMMAND [ARG[ ...]]
docker container exec -d CONTAINER COMMAND [ARG[ ...]]
docker container exec -i -t CONTAINER COMMAND [ARG[ ...]]
```

OPTIONS可为：

* -d|--detach：创建守护式后台进程。默认为false。
* --detach-keys：（覆盖卸载容器的key序列号。）
* -e|--env VAR[=VALUE]：设置环境变量，如不使用=VALUE则取$VAR的值。
* -i|--interactive：始终开启标准输入。创建交互式前台进程必需该选项。默认为false。
* --privileged：使用特权模式运行。对宿主机拥有root权限，有一定的安全风险，需在确保可信时使用。默认为false。
* -t|--tty：分配伪tty终端。创建交互式前台进程必需该选项。默认为false。
* -u|--user USERNAME|UID[:GROUPNAME|GID]：执行进程的容器系统用户名或用户ID。

### docker container kill

向容器发送信号，可用来杀死容器。等同于`docker kill`。

```shell
docker container kill [OPTIONS] CONTAINER[ ...]
```

OPTIONS可为：

* -s SIGNAL：指定信号，默认为SIGKILL信号。

### docker container logs

获取容器标准输出和标准错误输出的日志。等同于`docker logs`。

```shell
docker container logs [OPTIONS] CONTAINER
```

OPTIONS可为：

* --details：显示详细信息。默认为false。
* -f|--follow：持续跟踪日志输出，类似`tail -f`。通过Ctrl+C退出跟踪。默认为false。
* --since TIMESTAMP|DURATION：显示最近时间的日志。TIMESTAMP格式为2006-01-02T15:04:05，DURATION格式为15h|4m|5s。
* -n|--tail N|all：只获取最后的若干条日志，N可为0。默认为all。
* -t|--timestamp：为每条日志加上时间戳。

### docker container ls

列出容器。等同于`docker container ps`、`docker ps`。

```shell
docker container ls [OPTIONS]
```

OPTIONS可为：

* -a：列出所有容器，不指定默认只列出运行中的容器。
* -f|--filter KEY=VALUE：根据条件过滤。
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

### docker container pause

暂停容器。等同于`docker pause`。

```shell
docker container pause CONTAINER[ ...]
```

### docker container port

查看容器和宿主机间的端口映射。等同于`docker port`。

```shell
docker container port CONTAINER [CONTAINER_PORT[/PROTOCOL]]
```

### docker container ps

列出容器。等同于`docker container ls`、`docker ps`。

```shell
docker container ps [OPTIONS]
```

### docker container restart

重启容器。等同于`docker restart`。

```shell
docker container restart CONTAINER[ ...]
```

### docker container rm

删除容器。等同于`docker rm`。

```shell
docker container rm [OPTIONS] CONTAINER[ ...]
docker container rm -l /CONTAINER1/CONTAINER2
```

OPTIONS可为：

* -f|--force：强制删除，即使容器正在运行中。如删除运行中的容器则先发送SIGKILL。默认为false。
* -l|--link /CONTAINER1/CONTAINER2：删除两容器之间的所有网络桥接。
* -v|--volumes：同时删除容器的所有磁盘卷，但不会删除主机映射的文件。默认为false。

### docker container run

创建并启动容器。等同于`docker run`。

```shell
docker container run [OPTIONS] IMAGE [COMMAND [ARG[ ...]]
docker container run -d IMAGE [COMMAND [ARG[ ...]]
docker container run -i -t IMAGE [COMMAND [ARG[ ...]]
```

OPTIONS可使用`docker container create`的所有OPTIONS，还可为：

* -d|--detach：创建守护式容器，只会打印容器ID。默认为false。

### docker container start

启动容器。等同于`docker start`。

```shell
docker container start [OPTIONS] CONTAINER[ ...]
```

OPTIONS可为：

* -a|--attach：将宿主机的标准输出和标准错误输出附着至容器并转发信号至容器。默认为false。
* --detach-keys：（覆盖卸载容器的key序列号。）
* -i|--interactive：将宿主机的标准输入附着至容器。默认为false。

### docker container stats

持续显示容器的状态统计。等同于`docker stats`。

```shell
docker container stats [OPTIONS] CONTAINER[ ...]
```

### docker container stop

停止容器。会向Docker容器进程发送SIGTERM信号，如果想强制杀死容器，需使用`docker kill`。等同于`docker stop`。

```shell
docker container stop [OPTIONS] CONTAINER[ ...]
```

OPTIONS可为：

* -t|--time SECONDS：等待秒数，超时则强制停止。

### docker container wait

等待容器退出，并返回退出码。等同于`docker wait`。

```shell
docker container wait CONTAINER[ ...]
```

## docker cp

在容器和宿主机之间复制文件。等同于`docker container cp`。

```shell
docker cp [OPTIONS] CONTAINER:CONTAINER_PATH HOST_PATH
docker cp [OPTIONS] HOST_PATH CONTAINER:CONTAINER_PATH
```

在容器创建的文件的所有者为容器中启动进程的用户，在宿主机创建的文件的所有者为宿主机的当前用户。

## docker create

创建容器，不会启动容器。等同于`docker container create`。

```shell
docker create [OPTIONS] IMAGE [COMMAND [ARG[ ...]]
docker create -i -t IMAGE [COMMAND [ARG[ ...]]
```

## docker exec

在运行中的容器内运行新进程。等同于`docker container exec`。

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG[ ...]]
docker exec -d CONTAINER COMMAND [ARG[ ...]]
docker exec -i -t CONTAINER COMMAND [ARG[ ...]]
```

## docker help

查看帮助。

```shell
docker help [SUBCMD]
```

SUBCMD即本文描述的子命令。`docker help SUBCMD`等同于`docker SUBCMD --help`，也可以使用`man docker-SUBCMD`查看子命令的帮助。

## docker history

查看镜像的历史信息，可得知构建镜像的每一层。等同于`docker image history`。

```shell
docker history [OPTIONS] IMAGE
```

## docker image

管理镜像。

### docker image build

使用Dockerfile构建镜像。等同于`docker build`。

```shell
docker image build [OPTIONS] DOCKERFILE_DIR|URL|-
```

OPTIONS可为：

* --add-host HOSTNAME:IP：添加主机至IP的解析至容器中。
* --build-arg NAME=VALUE：指定构建时使用的变量。只能指定在ARG指令中定义过的变量。
* --cache-from：（镜像被作为缓存的资源。）
* --cgroup-parent：（容器父级的cgroup。）
* --compress：（使用gzip压缩构建容。默认为false。）
* --cpu-period：（从来指定容器对CPU的使用要在多长时间内重新分配CFS（Completely Fair Scheduler）。）
* --cpu-quota：（指定在--cpu-period指定高度周期内，最多可以有多少时间用来运行容器。）
* -c|--cpu-shares：（容器对CPU资源的使用绝对不会超过此值。）
* --cpuset-cpus N[,...]：容器使用的CPU序号。0为第一个。
* --cpuset-mems N[,...]|A-B：容器使用的内存节点序号。0为第一个。
* --disable-content-trust：跳过镜像校验。默认为false。
* -f|--file FILENAME：指定Dockerfile文件。默认是当前路径下的“Dockerfile”。
* --force-rm：总是移除中间过程产生的容器。默认为false。
* --isolation：（容器隔离技术。）
* --label KEY[=VALUE]：设置容器的标签，如不使用=VALUE则为空字符串。
* -m|--memory N：内存字节限制。
* --memory-swap N：虚拟内存字节限制，即物理内存和交换内存的总和。
* --network NETWORK：构建时为`RUN`指令设置的网络模式。默认为default。
* --no-cache：不使用构建缓存。默认为false。
* --pull：尝试拉取最新的镜像版本。默认为false。
* -q|--quiet：安静模式，只在构建成功后打印镜像ID。默认为false。
* --rm：构建成功后移除中间过程产生的容器。默认为true。
* --security-opt：（安全选项。）
* --shm-size N：/dev/shm的大小。
* --squash：新构建的所有层合并到一个层中。默认为false。
* -t|--tag NAME[:TAG]：镜像名称和标记。
* --target：（设置目标阶段构建。）
* --ulimit TYPE=SOFT_LIMIT[:HARD_LIMIT]：设置ulimit限制，如未设置HARD_LIMIT，则其与SOFT_LIMIT相同。

### docker image history

查看镜像的历史信息，可得知构建镜像的每一层。等同于`docker history`。

```shell
docker image history [OPTIONS] IMAGE
```

返回依次为从顶部到底部的各镜像层，包括以下列：

* IMAGE：镜像ID。
* CREATED：镜像层创建时间。
* CREATED BY：创建镜像层的命令或指令。
* SIZE：镜像层大小。
* COMMENT：注释。

### docker image ls

列出镜像。等同于`docker images`。

```shell
docker image ls [OPTIONS] [IMAGE]
```

OPTIONS可为：

* -a|--all：列出所有镜像，包括中间层的镜像。默认为false。
* --digests：列出摘要。默认为false。
* -f|--filter KEY=VALUE：根据条件过滤。
* --format GOTEMPLATE。使用Go语言模板格式化输出，如：`'{{.Name}} {{.State.Running}}'`。
* --no-trunc：不对输出进行截断。默认为false。
* -q|--quiet：安静模式，只列出镜像ID。默认为false。

返回包括以下列：

* REPOSITORY：仓库名。
* TAG：镜像标记。
* DIGEST：摘要。
* IMAGE ID：镜像ID。
* CREATED：镜像的创建时间。
* SIZE：镜像大小。

### docker image pull

从仓库拉取镜像。等同于`docker pull`。

```shell
docker image pull [OPTIONS] IMAGE
```

OPTIONS可为：

* -a|--all-tages：下载仓库中所有带标记的镜像。默认为false。
* --disable-content-trust：跳过镜像校验。默认为true。

### docker image push

推送镜像至注册服务器。等同于`docker push`。

```shell
docker image push IMAGE
```

OPTIONS可为：

* --disable-content-trust：跳过镜像签名。默认为true。

需先使用`docker login`登录。Docker Hub只能推送至自己的非自动构建的用户仓库。如推送至非Docker Hub的注册服务器，需先使用`docker image tag`。

### docker image rm

删除镜像。等同于`docker rmi`。

```shell
docker image rm [OPTIONS] IMAGE[ ...]
```

显示的每一行表示删除一个镜像层。

### docker image tag

为镜像打上标记。等同于`docker tag`。

```shell
docker image tag IMAGE TARGET_IMAGE
```

如推送至非Docker Hub的注册服务器，需先使用`docker image tag`。

### docker container top

查看容器内运行的进程。等同于`docker top`。

```shell
docker container top CONTAINER
```

## docker images

列出镜像。等同于`docker image ls`。

```shell
docker images [OPTIONS] [IMAGE]
```

## docker info

查看Docker引擎信息，包括容器和镜像的数量、执行和存储驱动、基本配置等。

```shell
docker info [OPTIONS]
```

## docker inspect

查看Docker对象的底层信息。

```shell
docker inspect [OPTIONS] OBJECT[ ...]
```

OPTIONS可为：

* -f|--format GOTEMPLATE。使用Go语言模板格式化输出，如：`'{{.Name}} {{.State.Running}}'`。

返回的JSON各字段含义如下：

* State：进程状态。
	* Pid：进程ID。
* Mounts：卷的挂载信息。
* NetworkSettings：网络配置信息。
	* Networks：各网络的信息。键为网络名。
		* 网络名：
			* IPAddress：容器在网络中的IP。

## docker login

登录注册服务器，并将认证信息保存下来，保存于$HOME/.docker/config.json。

```shell
docker login [OPTIONS] [REGISTRY_SERVER]
```

OPTIONS可为：

* -p|--password [PASSWORD]：密码。如不使用此选项，或不指定PASSWORD，则会提示于命令行交互输入。
* -u|--username USERNAME：账号。如不使用此选项，则会提示于命令行交互输入。

## docker logout

退出注册服务器的登录。

```shell
docker logout
```

## docker kill

向容器发送信号，可用来杀死容器。等同于`docker container kill`。

```shell
docker kill [OPTIONS] CONTAINER[ ...]
```

## docker logs

获取容器标准输出和标准错误输出的日志。等同于`docker container logs`。

```shell
docker logs [OPTIONS] CONTAINER
```

## docker network

管理网络。

### docker network connect

将容器连接到网络中。

```shell
docker network connect [OPTIONS] NETWORK CONTAINER
```

OPTIONS可为：

* --alias ALIAS：容器在网络中的别名。
* --ip IPV4：容器在网络中的IPv4地址。
* --ip6 IPV6：容器在网络中的IPv6地址。
* --link：（添加连接到其他容器。）
* --link-local-ip：（为容器添加一个本地的连接地址。）

### docker network create

创建网络。

```shell
docker network create [OPTIONS] NETWORK
```

OPTIONS可为：

* --attachable：（允许手动向容器添加网络。默认为false。）
* --aux-address：（备用的网络驱动IPv4或IPv6地址。）
* -d|--driver DRIVER：网络驱动。bridge为NAT桥接宿主机网络，overlay为隧道技术。默认为bridge。
* --gateway：（主子网的IPv4或IPv6网关。）
* --ingress：（创建集群的路由网网络。默认为false。）
* --internal：内部网络，禁止外部访问。默认为false。
* --ip-range：（为容器分配IP的范围。）
* --ipam-driver：（IP地址管理驱动。默认为default。）
* --ipam-opt：（IP地址管理驱动的参数。）
* --ipv6：启用IPv6网络。默认为false。
* --label KEY[=VALUE]：设置网络的标签，如不使用=VALUE则为空字符串。
* -o|--opt：（网络驱动的参数。）
* --subnet：（CIDR格式的子网。）

### docker network disconnect

将容器从网络中断开。

```shell
docker network disconnect [OPTIONS] NETWORK CONTAINER
```

OPTIONS可为：

* -f|--force：强制断开。默认为false。

### docker network inspect

查看网络的底层信息。

```shell
docker network inspect [OPTIONS] NETWORK[ ...]
```

OPTIONS可为：

* -f|--formet GOTEMPLATE。使用Go语言模板格式化输出。

### docker network ls

列出网络。

```shell
docker network ls [OPTIONS]
```

OPTIONS可为：

* -f|--filter KEY=VALUE：根据条件过滤。
* --format GOTEMPLATE。使用Go语言模板格式化输出，如：`'{{.Name}} {{.State.Running}}'`。
* --no-trunc：不对输出进行截断。默认为false。
* -q|--quiet：安静模式，只列出网络ID。默认为false。

返回包括以下列：

* NETWORK ID：网络ID。
* NAME：网络名称。
* DRIVER：网络驱动。
* SCOPE：网络作用域。

### docker network prune

删除未使用的网络。

```shell
docker network prune [OPTIONS]
```

* --filter KEY=VALUE：根据条件过滤。
* -f|--force：强制删除，不需交互式提示。默认为false。

### docker network rm

删除网络。

```shell
docker network rm NETWORK[ ...]
```

## docker node

管理集群节点。此命令必需运行于管理节点上。

### docker node demote

将管理节点降级为工作节点。

```shell
docker node demote NODE[ ...]
```

### docker node ls

列出节点。

```shell
docker node ls [OPTIONS]
```

返回包括以下列：

* ID：节点ID。后带“*”的当前节点。
* HOSTNAME：节点主机名。
* STATUS：
* AVAILABILITY：可用状态。
* MANAGER STATUS：
* ENGINE VERSION：Docker引擎版本。

### docker node ps

列出节点上运行的任务。

```shell
docker node ps [OPTIONS] [NODE[ ...]]
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

## docker pause

暂停容器。等同于`docker container pause`。

```shell
docker pause CONTAINER[ ...]
```

## docker port

查看容器和宿主机间的端口映射。等同于`docker container port`。

```shell
docker port CONTAINER [CONTAINER_PORT[/PROTOCOL]]
```

## docker ps

列出容器。等同于`docker container ls`、`docker container ps`。

```shell
docker ps [OPTIONS]
```

## docker pull

从仓库拉取镜像。等同于`docker image pull`。

```shell
docker pull [OPTIONS] IMAGE
```

## docker push

推送镜像至注册服务器。等同于`docker push`。

```shell
docker image push IMAGE
```

## docker restart

重启容器。等同于`docker container restart`。

```shell
docker restart CONTAINER[ ...]
```

## docker rm

删除容器。等同于`docker container rm`。

```shell
docker rm [OPTIONS] CONTAINER[ ...]
docker rm -l /CONTAINER1/CONTAINER2
```

## docker rmi

删除镜像。等同于`docker image rm`。

```shell
docker rmi [OPTIONS] IMAGE[ ...]
```

## docker run


创建并启动容器。等同于`docker container run`。

```shell
docker run [OPTIONS] IMAGE [COMMAND [ARG[ ...]]
docker run -d IMAGE [COMMAND [ARG[ ...]]
docker run -i -t IMAGE [COMMAND [ARG[ ...]]
```

## docker search

从注册服务器查找镜像。

```shell
docker search [OPTIONS] IMAGE_KEYWORD
```

## docker service

管理集群服务。此命令必需运行于管理节点上。

### docker service create

创建服务。会选择若干节点创建并启动容器。

```shell
docker service create [OPTIONS] IMAGE [COMMAND [ARG[ ...]]
```

OPTIONS可为：

* --constraint 'CONSTRAINT ==|!= VALUE'：服务运行节点的约束。CONSTRAINT可取以下值：node.id为节点ID，node.hostname为节点的主机名，node.role为节点的角色（manager或worker），node.labels.KEY为节点的标签，engine.labels.KEY为引擎的标签。
* --container-label：（容器的标签。）
* --credential-spec：（管理服务账号的证书，仅在Windows下有效。）
* -d|--detach：立即退出不等待服务完全启动。默认为true。
* --dns SERVER：见`docker container run`。
* --dns-option OPTION：见`docker container run`。
* --dns-search DOMAIN：见`docker container run`。
* --endpoint-mode MODE：终端模式。可为vip、dnsrr。默认为vip。
* --entrypoint COMMAND [ARG[ ...]]：见`docker container run`。
* -e|--env VAR[=VALUE]：见`docker container run`。
* --env-file FILENAME：见`docker container run`。
* --group：（为容器设置额外的用户组。可使用多次。）
* --health-cmd：见`docker container run`。
* --health-interval：见`docker container run`。
* --health-retries：见`docker container run`。
* --health-start-period：见`docker container run`。
* --health-timeout：见`docker container run`。
* --host：（添加主机至IP的解析至容器中。）
* --hostname HOSTNAME：见`docker container run`。
* -l|--label KEY[=VALUE]：设置服务的标签，如不使用=VALUE则为空字符串。
* --limit-cpu：（CPU限制。）
* --limit-memory：（内存限制。）
* --log-driver DRIVER：服务的日志驱动。json-file为`docker logs`提供基础，syslog为将日志输出到syslogd并禁用`docker logs`，none为禁用日志并禁用`docker logs`。默认为json-file。
* --log-opt KEY=VALUE：见`docker container run`。
* --mode MODE：服务的模式。replicated为副本型（在指定个数的节点上运行），global为全局型（在所有节点上运行）。默认为replicated。
* --mount MOUNT：关联文件系统并挂载到服务中。可为type=volume[,source=VOLUME_NAME],destination=CONTAINER_PATH[,volume-lobel="KEY=VALUE"[,...]]或type=bind,source=HOST_PATH,destination=CONTAINER_PATH[,volume-lobel="KEY=VALUE"[,...]]。
* --name NAME：服务名称。
* --network NETWORK：（网络设备。）
* --no-healthcheck：见`docker container run`。
* --placement-pref：（偏好存放位置。）
* -p|--publish：（见`docker container run`。）
* -q|--quiet：安静模式。默认为false。
* --read-only：见`docker container run`。
* --replicas N：节点的数量。
* --reserve-cpu：（保留的CPU数。）
* --reserve-memory：（保留的内存大小。默认为0。）
* --restart-condition CONDITION：重启条件。可为none、on-failure、any。默认为any。
* --restart-delay：（重启的延迟时间。可使用的单位有ns、us、ms、s、m、h。默认为5s。）
* --restart-max-attempts N：重启的尝试次数，超过则失败。
* --rollback-delay：（回滚的延迟时间。可使用的单位有ns、us、ms、s、m、h。默认为0s。）
* --rollback-failure-action：（回滚失败后的操作。可为pause、continue。默认为pause。）
* --rollback-max-failure-ratio：（回滚时允许的最大失败率。默认为0。）
* --rollback-monitor：（监控回滚的持续时间。可使用的单位有ns、us、ms、s、m、h。默认为5s。）
* --rollback-order：（回滚顺序。可为start-first、stop-first。默认为stop-first。）
* --rollback-parallelism：（同时回滚的最大节点数。0为同时回滚所有节点。默认为1。）
* --secret：（为服务设置安全机制。）
* --stop-grace-period：（强制退出容器之前的等待时间。可使用的单位有ns、us、ms、s、m、h。默认为10s。）
* --stop-signal SIGNAL：见`docker container run`。
* -t|--tty：见`docker container run`。
* --update-delay DURATION：更新时各批次之间的延时间隔。可使用的单位有ns、us、ms、s、m、h。默认为0s。
* --update-failure-action：（更新失败后的操作。可为pause、continue、rollback。默认为pause。）
* --update-max-failure-ratio：（更新时允许的最大失败率。默认为0。）
* --update-monitor：（监控更新的持续时间。可使用的单位有ns、us、ms、s、m、h。默认为5s。默认为5s。）
* --update-order ORDER：更新顺序。可为start-first、stop-first。默认为stop-first。
* --update-parrallelism N：更新时每批次同时更新的最大节点数量。0为同时更新所有节点。默认为1。
* -u|--user USER|UID[:GROUP|GID]：见`docker container run`。
* --with-registry-auth：（发送仓库的验证信息到集群的客户端。默认为false。）
* -w|--workdir DIR：见`docker container run`。

### docker service ls

列出服务。

```shell
docker service ls [OPTIONS]
```

OPTIONS可为：

* -f|--filter KEY=VALUE：根据条件过滤。KEY可为id、label、name。
* -q|--quiet：安静模式，只列出服务ID。默认为false。

返回包括以下列：

* ID：服务ID。
* NAME：服务名称。
* MODE：
* REPLICAS：
* IMAGE：启动容器所使用的镜像。
* PORTS：宿主机和容器间的端口映射。

### docker service ps

列出服务上运行的任务。

```shell
docker service ps [OPTIONS] SERVICE
```

OPTIONS可为：

* -f|--filter KEY=VALUE：根据条件过滤。
* --format GOTEMPLATE。使用Go语言模板格式化输出，如：`'{{.Name}} {{.State.Running}}'`。
* --no-resolve：不将服务ID解析成服务名。默认为false。
* --no-trunc：不对输出进行截断。默认为false。
* -q|--quiet：安静模式，只列出网络ID。默认为false。

返回包括以下列：

* ID：任务ID
* NAME：服务名或服务ID。
* IMAGE：镜像。
* NODE：节点ID。
* DESIRED STATE：期望状态。
* CURRENT STATE：当前状态。
* ERROR： 错误信息。
* PORTS

### docker service rm

删除服务。

```shell
docker service rm SERVICE[ ...]
```

### docker service scale

伸缩服务。只能在副本型服务上执行。

```shell
docker service scale SERVICE=N[ ...]
```

### docker service update

更新服务。

```shell
docker service update [OPTIONS] SERVICE
```

OPTIONS可为：

* --args：（服务命令参数。）
* --constraint-add：（添加或更新服务运行节点的约束。）
* --constraint-rm：（删除服务运行节点的约束。）
* --container-label-add：（添加或更新容器的标签。）
* --container-label-rm：（删除容器的标签。）
* --credential-spec：（见`docker service create`。）
* -d|--detach：立即退出不等待服务完全更新。默认为true。
* --dns-add：（添加或更新DNS服务器。）
* --dns-option-add：（添加或更新DNS选项。）
* --dns-option-rm：（删除DNS选项。）
* --dns-rm：（删除DNS服务器。）
* --dns-search-add：（添加或更新DNS搜索域。）
* --endpoint-mode MODE：见`docker service create`。
* --entrypoint COMMAND [ARG[ ...]]：见`docker service create`。
* --env-add：（添加或更新环境变量。）
* --env-rm：（删除环境变量。）
* --force：强制更新，即使没有改变，会重新创建任务。默认为false。
* --group-add：（为容器添加额外的用户组。）
* --group-rm：（删除为容器添加的额外的用户组。）
* --health-cmd：（见`docker service create`。）
* --health-interval：（见`docker service create`。）
* --health-retries：（见`docker service create`。）
* --health-start-period：（见`docker service create`。）
* --health-timeout：（见`docker service create`。）
* --host-add：（添加或更新主机至IP的解析至容器中。）
* --host-rm：（删除容器中主机至IP的解析。）
* --hostname HOSTNAME：见`docker service create`。
* --image：（服务的镜像。）
* --label-add：（添加或更新服务的标签。）
* --label-rm：（删除服务的标签。）
* --limit-cpu：（见`docker service create`。）
* --limit-memory：（见`docker service create`。）
* --log-driver DRIVER：见`docker service create`。
* --log-opt KEY=VALUE：见`docker service create`。
* --mount-add：（添加或更新服务的磁盘卷挂载。）
* --mount-rm：（通过路径删除服务的磁盘卷挂载。）
* --network-add：（添加网络。）
* --network-rm：（删除网络。）
* --no-healthcheck：见`docker service create`。
* --placement-pref-add：（添加偏好存放位置。）
* --placement-pref-rm：（删除偏好存放位置。）
* --publish-add：（添加公开发布的端口。）
* --publish-rm：（通过制定端口删除公开发布的端口。）
* -q|--quiet：见`docker service create`。
* --read-only：见`docker service create`。
* --replicas N：见`docker service create`。
* --reserve-cpu：（见`docker service create`。）
* --reserve-memory：（见`docker service create`。）
* --restart-condition CONDITION：见`docker service create`。
* --restart-delay：见`docker service create`。
* --restart-max-attempts N：见`docker service create`。
* --rollback：回滚到服务的前一个版本。默认为false。
* --rollback-delay：（见`docker service create`。）
* --rollback-failure-action：（见`docker service create`。）
* --rollback-max-failure-ratio：（见`docker service create`。）
* --rollback-monitor：（见`docker service create`。）
* --rollback-order：（见`docker service create`。）
* --rollback-parallelism：（见`docker service create`。）
* --secret-add：（添加或更新服务的安全机制。）
* --secret-rm：（删除服务的安全机制。）
* --stop-grace-period：（见`docker service create`。）
* --stop-signal SIGNAL：见`docker service create`。
* -t|--tty：见`docker service create`。
* --update-delay DURATION：见`docker service create`。
* --update-failure-action：（见`docker service create`。）
* --update-max-failure-ratio：（见`docker service create`。）
* --update-monitor：（见`docker service create`。）
* --update-order ORDER：见`docker service create`。
* --update-parrallelism N：见`docker service create`。
* -u|--user USER|UID[:GROUP|GID]：见`docker service create`。
* --with-registry-auth：（见`docker service create`。）
* -w|--workdir DIR：见`docker service create`。

## docker stack

服务栈管理。

## docker stack deploy

部属服务栈。

## docker start

启动容器。等同于`docker container start`。

```shell
docker start [OPTIONS] CONTAINER[ ...]
```

## docker stats

持续显示容器的状态统计。等同于`docker container stats`。

```shell
docker stats [OPTIONS] CONTAINER[ ...]
```

## docker stop

停止容器。会向Docker容器进程发送SIGTERM信号，如果想强制杀死容器，需使用`docker kill`。等同于`docker container stop`。

```shell
docker stop [OPTIONS] CONTAINER[ ...]
```

## docker swarm

管理集群。

### docker swarm init

初始化集群，当前节点为管理节点，刚初始化的集群只有此单个节点。

```shell
docker swarm init [OPTIONS]
```

OPTIONS可为：

* --advertise-addr IP|INTERFACE[:PORT]：此节点用于服务发现的监听地址。
* --autolock：管理节点自动锁，即启动一个停止的目的管理节点需要解锁密码。默认为false。
* --availability AVAILABILITY：节点的可用状态。可为active、pause、drain。默认为active。
* --cert-expiry DURATION：证书的有效时间。可使用的单位有ns、us、ms、s、m、h。默认为2160h0m0s。
* --dispatcher-heartbeat DURATION：心跳调度时间。可使用的单位有ns、us、ms、s、m、h。默认为5s。
* --external-ca：（证书。可使用多次。）
* --forct-new-cluster：从当前状态强制创建集群。默认为false。
* --listen-addr IP|INTERFACE[:PORT]：此节点用于集群内的监听地址。默认为0.0.0.0:2377。
* --max-snapshots N：保留的最多快照数。
* --snapshot-interval N：（快照的间隔时间。）
* --task-history-limit N：任务的最大历史记录。

返回当前节点ID和加入集群需运行的命令。

### docker swarm join

当前节点加入集群。

```shell
docker swarm join [OPTIONS] MANAGER_HOST:MANAGER_PORT
```

OPTIONS可为：

* --advertise-addr IP|INTERFACE[:PORT]：管理节点用于服务发现的地址。
* --availability AVAILABILITY：节点的可用状态。可为active、pause、drain。默认为active。
* --listen-addr IP|INTERFACE[:PORT]：此节点用于集群内的监听地址。默认为0.0.0.0:2377。
* --token TOKEN：加入集群的token。

MANAGER_HOST:MANAGER_PORT为管理节点在集群内的地址。

### docker swarm join-token

查看及刷新token。

```shell
docker swarm join-token [OPTIONS] worker|manager
```

* -q|--quiet：安静模式，只列出token，否则列出加入集群的完整命令（包括token和管理节点在集群内的地址）。默认为false。
* --rotate：刷新token。

### docker swarm leave

当前节点退出集群。

```shell
docker swarm leave [OPTIONS]
```

OPTIONS可为：

* -f|--force：强制退出，即使当前节点是管理节点。如最后一个管理节点退出，则集群解散。默认为false。

### docker swarm update

更新集群。

```shell
docker swarm update [OPTIONS]
```

OPTIONS可为：

* --autolock：管理节点自动锁，即启动一个停止的目的管理节点需要解锁密码。默认为false。
* --cert-expiry DURATION：证书的有效时间。可使用的单位有ns、us、ms、s、m、h。默认为2160h0m0s。
* --dispatcher-heartbeat DURATION：心跳调度时间。可使用的单位有ns、us、ms、s、m、h。默认为5s。
* --external-ca：（证书。可使用多次。）
* --max-snapshots N：保留的最多快照数。
* --snapshot-interval N：（快照的间隔时间。）
* --task-history-limit N：任务的最大历史记录。

## docker system

系统管理。

### docker system df

查看磁盘使用。

```shell
docker system df
```

### docker system prune

删除未使用的数据。

```shell
docker system prune [OPTIONS]
```

OPTIONS可为：

* -a：删除所有未使用的镜像。
* -f：强制删除。

## docker tag

为镜像打上标记。等同于`docker image tag`。

```shell
docker tag IMAGE TARGET_IMAGE
```

## docker top

查看容器内运行的进程。等同于`docker container top`。

```shell
docker top CONTAINER
```

## docker version

查看版本。

```shell
docker version
```

## docker volume

管理磁盘卷。

### docker volume create

创建磁盘卷。

```shell
docker volume create [OPTIONS] [VOLUME]
```

OPTIONS可为：

* -d|--driver DRIVER：驱动名。默认为local。
* --label：KEY[=VALUE]：设置磁盘卷的标签，如不使用=VALUE则为空字符串。
* -o|--opt KEY=VALUE：为驱动设置选项，不同的驱动参数也不相同。可使用多次。

如未指定VOLUME则自动生成名称，如VOLUME已存在则重用之。

### docker volume inspect

查看磁盘卷的底层信息。

```shell
docker volume inspect [OPTIONS] VOLUME[ ...]
```

OPTIONS可为：

* -f|--formet GOTEMPLATE。使用Go语言模板格式化输出。

### docker volume ls

列出磁盘卷。

```shell
docker volume ls [OPTIONS]
```

OPTIONS可为：

* -f|--filter KEY=VALUE：根据条件过滤。KEY=VALUE可为dangling=true|false|0|1、driver=DRIVER、label=KEY[=VALUE]、name=VOLUME_REGEXP。
* --format GOTEMPLATE：使用Go语言模板格式化输出，如：`'{{.Name}} {{.State.Running}}'`。
* -q|--quiet：安静模式，只列出磁盘卷名称。默认为false。

返回包括以下列：

* DRIVER：驱动。
* VOLUME NAME：磁盘卷名称

### docker volume prune

删除未使用的磁盘卷，会同时删除宿主机的文件。

```shell
docker volume prune [OPTIONS]
```

* --filter KEY=VALUE：根据条件过滤。参看`docker volume ls --filter`。
* -f|--force：强制删除，不需交互式提示。默认为false。

### docker volume rm

删除磁盘卷，会同时删除宿主机的文件。

```shell
docker volume rm [OPTIONS] VOLUME[ ...]
```

OPTIONS可为：

* -f|--force：强制删除，不需交互式提示。默认为false。

## docker wait

等待容器退出，并返回退出码。等同于`docker container wait`。

```shell
docker wait CONTAINER[ ...]
```
